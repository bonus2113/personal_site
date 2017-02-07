{
  "draft": true,
  "date": "2016-10-05T21:53:00",
  "image": "/img/sdf_post/super_high_res.png",
  "math": false,
  "title": "The pitfalls of non-local optimizationin HLSL shaders"
}

##The Setup

I'm currently working on the computer graphics side of things of a little tool that allows [constructive solid geometry (CSG)](https://en.wikipedia.org/wiki/Constructive_solid_geometry) sculpting in [Unity3D](https://unity3d.com/). In particular, I take a set of [signed distance field](https://en.wikipedia.org/wiki/Signed_distance_function) brushes and use sphere tracing to render them. The current in progress version produces results like this:

{{< figure src="/img/sdf_post/super_high_res.png" caption="WIP Results" >}}

If you want to know more about these techniques you should check out [Inigo Quilez' site](http://www.iquilezles.org/www/index.htm), he's written lots of great articles on the subject. For a great overview on how to fix many of the common issues with sphere tracing I recommend the paper ["Enhanced Sphere Tracing"](http://lgdv.cs.fau.de/publications/publication/Pub.2014.tech.IMMD.IMMD9.enhanc/).

##The Problem

One major issue of sphere tracing is performance. To get acceptable results many iterations per pixel are needed. Per iteration you need to sample the distance field at least once. This can get very expensive when you have many different edits. I get around this by storing the static brushes in a 3D texture and only evaluating a few (typically one or two) interactive ones at rendering time.  
This means that evaluating the distance field is equivalent to sampling the 3D texture and a few extra oprations for the interactive brushes. The texture access is very coherent (I get about 97.5% L2 cache hit rate) and hence fast. When I was implementing this I expected that evaluating at most two brushes per iteration should not be a major issue either. Interestingly I discovered, that, as soon as an interactive brush was in the scene (not even on screen), performance suffered greatly.
