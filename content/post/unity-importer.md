+++
date = "2017-10-12T18:43:08+02:00"
image = "unity-importer/timeline.png"
math = false
tags = ["unity", "animvr", "game dev"]
title = "Unity ScriptedImporters and the Timeline API"
+++

## Overview

[Unity](https://unity3d.com)'s editor scripting is part of what makes the engine so attractive. Writing tools that directly interact with the rest of your game code is powerful and can make a large difference in the productivity of teams of all sizes. It also leads to a healthy landscape of third party integrations. In this post I will describe how two new Unity APIs allowed us to create a well integrated Unity toolkit for AnimVR and go through issues you might run into. Below is a video showcasing our integration:

{{< youtube CATU3e8qAv0 >}}

> The Unity package is available over here: [unity.nvrmind.io](http://unity.nvrmind.io/)

## A little Background
We have been working on the VR drawing and animation tool [AnimVR](https://nvrmind.io/#animvr) for about a year. We are building it in Unity for a variety of reasons that I don't want to get into now, but one of our main goals is to support import and export from and to a variety of interchange formats. So far the main way to export content from AnimVR is to use the [Alembic Cache](http://www.alembic.io/) format which is very popular in feature film pipelines but barely used in the game industry.

We've also been teasing a Unity toolkit for ages. A prototype existed when we released the first beta version and at the time we thought it wouldn't take us long to release it to the public. Unfortunately it turned out that back then we would have needed to include a lot of custom runtime code to make playback of AnimVR stages work in Unity. Getting any kind of consistent timing outside of playmode is cumbersome as most who've worked with Unity editor code will know. Additionally this would have been code we would have had to maintain and update alongside the main AnimVR application, slowing us down in a phase where we wanted to be able to react to feedback quickly.

A second concern of mine was that I believe third party importers should adapt to the concepts of the *target* application (Unity, in this case) rather than the *source* (AnimVR). This way imported assets can be used in all the ways natively supported assets can be. I want to enable people to build Unity projects where the content happens to be made in AnimVR, rather than "view" AnimVR projects in Unity. 

We decided to not release that version of the Unity toolkit and wait until we had a better idea on how to reach that goal. So, what made it possible for us to release the current toolkit?

### [ScriptedImporters](https://docs.unity3d.com/ScriptReference/Experimental.AssetImporters.ScriptedImporter.html)
Previously the only thing we had to work with regarding asset import were [AssetPostprocessors](https://docs.unity3d.com/ScriptReference/AssetPostprocessor.html), but those have major drawbacks that prevent a tight integration. With an AssetPostprocessor you can run code whenever Unity detects a file with a certain extension. This allows you to create new assets based on your custom files. Unfortunately there is no way to associate those newly created assets with the source file, meaning that you double the number of assets you have to manage. As far as Unity was concerned .stage files from AnimVR where random binary files with nothing meaningful in them. It also leads to worse UX, since there is a difference in how you work with natively support file types (like meshes, textures and audio) and custom file types.

Since Unity 2017.1 there is a way to handle all of that better and actually build importers that have the same UX as the native ones. Creating a custom ScriptedImporter allows you to associate assets with a file on disk. Whenever the file is updated the old assets are automatically discarded and recreated and deleting the file also deletes all associated assets. You can even add a custom editor for your file type that allows users to edit import settings in a familiar way. This makes support for custom files a lot cleaner.

The whole API is working well, but still marked as experimental. I'll describe a few pitfalls that you need to watch out for in the implementation section of this post.

{{< figure src="/img/unity-importer/scripted_importer.png" title="The inspector of our ScriptedImporter" >}}

### [The Timeline API](https://forum.unity.com/threads/timeline-available-in-unity-2017-1.455265/)
For a while now Unity has been trying to break into the "realtime cinematics" market. The central building block of those efforts is the Unity Timeline API that was added in Unity 2017.1 and is still actively being developed. It provides a way to lay out tracks and clips in time and play them back, in a way that should be familiar to anyone who's used video or sound editing software. It's also very extensible, allowing you to define custom "Playables" that get updated whenever the playback time changes. 

This maps very nicely to our AnimVR stages. They consist of a number of layers (e.g. paint, mesh, audio, camera, etc.) that are laid out in time. Initially we actually considered using the Timeline API at runtime in AnimVR, but after some experimentation we concluded that our use case didn't need many of the features that the API provides and that this would uneccesarily complicate our daily work. Now we're using Timeline to drive the animation in the imported assets. It allows us to make use of a rich API without complicating our runtime code and lets users use AnimVR stages as part of their exisiting timeline setup.

{{< figure src="/img/unity-importer/timeline.png" title="The resulting Unity Timeline" >}}

## Implementation
### Loading the Data from Disk
When an asset with the right extension is imported Unity calls `OnImportAsset(AssetImportContext ctx)` on your `ScriptedImporter`. The `AssetImportContext` contains all the necessary information to find the relevant file on disk and read it. In our case we use the same deserialization method as in standalone AnimVR which should make it easy to keep compatibility between the Unity toolkit and the main application.

### Creating Assets from Script
While working with the Timeline API you will notice that it is fairly unintuitive to use from code. This is partly due to the fact that there is little documentation and few "real world" examples and partly because many settings are private variables that only get exposed in the UI. So far I've managed to keep afloat by decompiling the Timeline related code that comes with Unity and then using reflection to access the variables I need, which is par of the course for any Unity editor scripting.

#### A PlayableDirector
Any object that wants to play back a Timeline needs a `PlayableDirector` component. The `PlayableDirector` provides the adapter between the `TimelineAsset` that can't reference any objects in the scene and the scene objects that are controlled with the Timeline. It's a regular component and you can simply created it like this:

{{< highlight csharp >}}
director = stageObj.AddComponent<PlayableDirector>();
{{< /highlight >}}

#### The Timeline
Creating the timeline asset itself is very straightforward. Just remember to use `CreateInstance` like with any other `ScriptableObject`. Here is how we do it in the toolkit:

{{< highlight csharp >}}
// Create the asset
var timelineAsset = TimelineAsset.CreateInstance<TimelineAsset>();
// Set the properties you need.
timelineAsset.name = stage.name + "_Timeline";
timelineAsset.editorSettings.fps = stage.fps;
timelineAsset.durationMode = TimelineAsset.DurationMode.FixedLength;
timelineAsset.fixedDuration = stage.timelineLength * 1.0 / stage.fps;
// Add the asset to the AssetImportContext. This makes sure that it gets retained after the import is done.
ctx.AddSubAsset(timelineAsset.name, timelineAsset);
{{< /highlight >}}

Note the call to `ctx.AddSubAsset`. This is essential, because by default any assets that are created during the import process are destroyed automatically by Unity. Only assets that are registered via `ctx.AddSubAsset` will be kept. If you forget this for an asset it'll most likely manifest in missing references. What needs to be added is usually fairly clear, but there are some tricky cases that I'll discuss later.

> **Quick Tip:** The "name" of the sub asset needs to be unique and the same across imports. I use `AnimationUtility.CalculateTransformPath` to quickly create legible, and likely unique names for assets associated with an imported sub object.

#### Tracks

In its default state the `TimelineAsset` doesn't have any tracks. You can add a new track to the Timeline as follows (in this case it's an "Activation Track").

{{< highlight csharp >}}
var activationTrack = timelineAsset.CreateTrack<ActivationTrack>( null /*Parent GroupTrack*/, "Track Name");
// Tracks need to be saved as sub assets!
ctx.AddSubAsset("Track Name", activationTrack);
{{< /highlight >}}

As pointed out in the code, any tracks you add to a `TimelineAsset` need to be saved as sub assets too. If you don't do that the tracks will be missing after the import is done and you won't even get an error message!

#### Clips

Now, adding a clip to a track is easy:

{{< highlight csharp >}}
var activationClip = activationTrack.CreateDefaultClip();
activationClip.displayName = "Activation Clip";
activationClip.start = startTimeInSeconds;
activationClip.duration = durationInSecond;
{{< /highlight >}}

Again, you need to watch out to save the right asset. In this case it turns out that the clip itself is actually stored as part of the track, so you don't need to do anything there. Still, every clip has an associated `IPlayableAsset` which needs to be saved seperately and can be accessed via `TimelineClip.asset`.

{{< highlight csharp >}}
var activationAsset = activationClip.asset as ActivationPlayableAsset;
ctx.AddSubAsset(path + "_activationAsset", activationAsset);
{{< /highlight >}}

> **Quick Tip:** With clips you get to think about three "names". `clip.asset.name` is the name of the `UnityEngine.Object` and the string shown when you look at the asset in the editor, `clip.displayName` is what is shown in the Timeline window on the clip and finally you need to pass a **unique** identifier to `AddSubAsset` (the other two can be chosen freely). 

`TimelineClip.asset` is not type safe, you need to cast it to the correct asset type which is not clearly indicated. What I usually do is to jump to the definition of the associated track and check its attributes.

{{< highlight csharp >}}
[TrackBindingType(typeof(GameObject))]
[TrackClipType(typeof(ActivationPlayableAsset))]
[TrackMediaType(TimelineAsset.MediaType.Script)]
public class ActivationTrack : TrackAsset
{
    ...
}
{{< /highlight >}}

The `TrackClipType` attribute tells you what the type of `TimelineClip.asset` will be when you create the clip with `CreateDefaultClip()`.

### Referencing Prefabs

If the tracks and clips you are creating are self contained you are done now. Unfortunately that's usually not the case, because in the end you want to control objects and scripts via your timeline. This is also the case with our `ActivationTrack`. If you create that type of track in the editor you'll see something like this:

{{< figure src="/img/unity-importer/activation_track.png" title="ActivationTrack in the Timeline window." >}}

The field on the left lets you drag in the `GameObject` you want to control via that track. But how do you assign that value from script? Unfortunately there is no public `controlledObject` field in the ActivationTrack class or anything similar. This is because you are supposed to be able to use the same `TimelineAsset` with different `PlayabaleDirectors`. Hence there needs to be a way for us to define "references" in the `TimelineAsset` and then use the `PlayableDirector` to fill them. And there are two (of course) very different ways to do that!

#### [ExposedReferences](https://docs.unity3d.com/ScriptReference/ExposedReference_1.html)

The first way is to use a member variable of type `ExposedReference<ReferencedType>` and it's used in the default `ControlTrack`. Each `ExposedReference` instance needs to have a unique value for `exposedName`. On the `PlayableDirector` you can then call `SetReferenceValue` with the name of the `ExposedReference` you want to assign and the value it's supposed to have. 

Unfortunately, as far as my testing goes, the mapping set via `SetReferenceValue` is not updated correctly when instatiating prefabs. Usually, when you reference `GameObjects` in a prefab and the prefab is instantiated, those references are updated to point to the corresponding instantiated objects. This is not the case with `ExposedReference` values. When you instatiating your imported object the references will still point to the prefabs instead to objects in the scene.

#### Generic Bindings
The other way to reference objects seems a lot more powerful, but is also barely documented. This section is speculation based on what I was able to figure out in my experiments.

The way ActivationTracks decides which GameObject to control is done via `PlayableDirector.GetGenericBinding`. The `Set/GetGenericBinding` methods allow you to map an asset (e.g. a `TimelineTrack`) to any object (including `GameObjects`) and retrieve that mapping later. So, when we create the ActivationTrack we do the following: 

{{< highlight csharp >}}
director.SetGenericBinding(activationTrack, controlledObject);
{{< /highlight >}}

The track will then later use this binding by calling `director.GetGenericBinding(this);`. The interesting points here are that 

1. Generic Bindings are serialized with the `PlayableDirector`.
2. When instantiating a prefab with a `PlayableDirector` attached all the references will be automatically updated the corresponding instantiated objects.

This is exactly what we want! With the last step complete, we can now create a fully functional Unity Timeline in a ScriptedImporter.

## Conclusion

ScriptedImporters are a very powerful new feature in Unity and let you import complex custom files. The new Timeline API is great if you need to lay out things in time, but can be tricky to work with from code unless you know exactly what's going on.
There are a few things you need to do to make ScriptedImporters work with the Unity Timeline API.

- Remember to call AddSubAsset on the right things.
    - The `TimelineAsset`
    - Any `TrackAsset` you create
    - The `TimelineClip.asset` of any clip you create
- Use `SetGenericBinding` to pre-set references on the `PlayableDirector`.

I left out the whole inspector/import settings side of things since that should be fairly straight forward for anyone familiar with regular Unity editor coding, but I'm happy to answer questions regarding that as well.