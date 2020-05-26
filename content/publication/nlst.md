{
  "abstract": "Signed distance fields (SDFs) are a powerful implicit representation for modeling solids, volumes and surfaces. Their infinite resolution, controllable continuity and robust constructive solid geometry operations, coupled with smooth blending, enable powerful and intuitive sculpting tools for creating complex SDF models. SDF metric properties also admit efficient surface rendering with sphere tracing. Unfortunately, SDFs remain incompatible with many popular direct deformation techniques which re-position a surface via its explicit representation. Linear blend skinning used in character articulation, for example, directly displaces each vertex of a triangle mesh. To overcome this limitation, we propose a variant of sphere tracing for directly rendering deformed SDFs. We show that this problem reduces to integrating a non-linear ordinary differential equation. We propose an efficient numerical solution, with controllable error, which first automatically computes an initial value along each cast ray before walking conservatively along a curved ray in the undeformed space according to the signed distance. Importantly, our approach does not require knowledge, computation or even global existence of the inverse deformation, which allows us to readily apply many existing forward deformations. We demonstrate our method's effectiveness for interactive rendering of a variety of popular deformation techniques that were, to date, limited to explicit surfaces.",
  "abstract_short": "We tackle the problem of rendering deformed signed distance fields (a), by phrasing sphere tracing in object space (b) as an initial value problem. Under non-linear deformation the straight deformed space ray becomes a curve, which we follow via numerical integration (c). We go to great lengths to avoid computing the inverse deformation. This enables us to easily apply many modern deformation techniques to signed distance fields (d).",
  "authors": [
    "Dario Seyb",
    "Alec Jacobson",
    "Derek Nowrouzezahrai",
    "Wojciech Jarosz"
  ],
  "date": "2019-11-24T00:00:00",
  "draft": false,
  "image": "seyb19nonlinear-teaser.jpg",
  "image_preview": "seyb19nonlinear-teaser.jpg",
  "math": true,
  "publication": "ACM Transactions on Graphics (Proceedings of SIGGRAPH Asia)",
  "publication_short": "ACM TOG",
  "selected": false,
  "subtitle": "Dartmouth Rendering Competition 2019: Winning Image",
  "title": "Non-linear sphere tracing for rendering deformed signed distance fields",
  "url_code": "",
  "url_dataset": "",
  "url_pdf": "https://cs.dartmouth.edu/~wjarosz/publications/seyb19nonlinear.pdf",
  "url_project": "https://cs.dartmouth.edu/~wjarosz/publications/seyb19nonlinear.html",
  "url_slides": "",
  "url_video": "https://cs.dartmouth.edu/~wjarosz/publications/seyb19nonlinear.mp4",
  "video": ""
}


