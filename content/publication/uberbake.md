{
  "abstract": "We describe the design and evolution of UberBake, a global illumination system developed by Activision, which supports limited lighting changes in response to certain player interactions. Instead of relying on a fully dynamic solution, we use a traditional static light baking pipeline and extend it with a small set of features that allow us to dynamically update the precomputed lighting at run-time with minimal performance and memory overhead. This means that our system works on the complete set of target hardware, ranging from high-end PCs to previous generation gaming consoles, allowing the use of lighting changes for gameplay purposes. In particular, we show how to efficiently precompute lighting changes due to individual lights being enabled and disabled and doors opening and closing. Finally, we provide a detailed performance evaluation of our system using a set of production levels and discuss how to extend its dynamic capabilities in the future.",
  "abstract_short": " Our system allows for player-driven lighting changes at run-time. Above we show a scene where a door is opened during gameplay. The image on the left shows the final lighting produced by our system as seen in the game. In the middle, we show the scene without the methods described here (top). Our system enables us to efficiently precompute the associated lighting change (bottom). This functionality is built on top of a dynamic light set system which allows for levels with hundreds of lights who’s contribution to global illumination can be controlled individually at run-time (right). ©Activision Publishing, Inc.",
  "authors": [
    "[Dario Seyb](https://darioseyb.com/)",
    "Peter-Pike Sloan",
    "Ari Silvennoinen",
    "Michał Iwanicki",
    "Wojciech Jarosz."
  ],
  "date": "2020-07-15T00:00:00",
  "draft": false,
  "image": "uberbake-teaser.png",
  "image_preview": "uberbake-teaser.png",
  "math": true,
  "publication": "ACM Transactions on Graphics (Proceedings of SIGGRAPH)",
  "publication_short": "ACM TOG",
  "selected": false,
  "subtitle": "",
  "title": "The design and evolution of the UberBake light baking system",
  "url_code": "",
  "url_dataset": "",
  "url_pdf": "",
  "url_project": "",
  "url_slides": "",
  "url_video": "",
  "video": ""
}


