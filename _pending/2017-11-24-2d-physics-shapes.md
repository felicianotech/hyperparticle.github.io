---
layout: post
title: Draw 2D Physics Shapes in Unity3D
---

In this tutorial series I will show you how to draw and manipulate basic 2D physics shapes with your cursor using Unity, with no additional external dependencies (tested with 2017.2, but should also work with 5.x).

- [GitHub Repository](https://github.com/Hyperparticle/draw-shapes-unity)
- [Unity Asset Store Bundle]()

Below is a basic prototype you can play in your browser.

<div class="iframe-rounded">
  <iframe class="iframe-responsive" src="https://c.simmer.io/static/unityFrame/index.html?url=https%3A%2F%2Fsimmercdn.com%2Funity%2Fgql7LhArHUV8ur4C8k5G0Fnr4nk1%2Fcontent%2F641ca5c6-362a-9d05-ef54-ed5931176753&imagePath=screens/4.png" scrolling="no" frameborder="0"></iframe>
</div>

Back in 2008, I had loads of fun playing [IncrediBots](http://incredibots.com/if/game.php). For the uninitiated, the game is very similar to [Phun (now Algodoo)](http://www.algodoo.com/); it involved drawing basic physics shapes and combining them with fixed, rotating, and sliding joints with motors attached to do interesting things via keyboard input. The game featured several puzzles and a rating system for published contraptions.

Here's a few examples of things that you could make:

<div class="iframe-rounded" style="width:96%">
  <iframe class="iframe-responsive" src="https://gfycat.com/ifr/OpenLimpingKoodoo" scrolling="no" frameborder="0"></iframe>
</div>

So let's do the same thing in Unity! Conveniently, Unity has excellent support for 2D physics, so implementing a basic prototype is very simple to do.

There are 4 parts:

1. [Drawing 2D polygon meshes](#drawing-2d-polygon-meshes)
1. [Specifying Points with the Cursor](#specifying-points-with-the-cursor)
1. [Adding 2D Physics](#adding-2d-physics)
1. [Drawing Multiple Shapes](#drawing-multiple-shapes)

This tutorial will implement rectangles, circles, and triangles.

<div class="iframe-rounded">
  <iframe class="iframe-responsive" src="https://gfycat.com/ifr/ElderlyEducatedFairyfly" scrolling="no" frameborder="0"></iframe>
</div>

# Drawing 2D Polygon Meshes

[![I-shaped polygon](/public/img/I-shape.png)](https://github.com/Hyperparticle/draw-shapes-unity/tree/d3b94561d464721a4607f030b6ec2d1c721f131b)

To draw a mesh programmatically in Unity, we need to specify its vertices and triangles. Unity allows each vertex to be colored differently, so we will supply random colors. From these, the mesh can be created and applied to the GameObject's `MeshFilter` component to display the mesh.

<script src="https://gist.github.com/Hyperparticle/68586a8834ed6cafd4a1b2ba32ccf6ed.js"></script>

Specifying vertices is easy, as we can list them in an array of `Vector2`s. The `vertices2D` array defines an I-shape. A small complication is that Unity wants an array of `Vector3`s, so we need to convert them. `System.Array.ConvertAll` is handy for this.

To find the triangles in a 2D polygon's list of points, we can use a [triangulation algorithm](https://en.wikipedia.org/wiki/riangulation_(geometry)). I copied the well-suited [Triangulator.cs class](http://wiki.unity3d.com/index.php?title=Triangulator) specified in the Unity3D wiki and dropped it in the project. There's nothing too fancy here; just give the object a list of points and it will give back the indices of each triangle.

The next step involves creating an array of colors, one for each vertex. Here they are initialized to random colors with `Random.ColorHSV()` using Linq syntax.

The last step is to create the mesh from the vertices, triangles, and color data and apply the mesh to a `MeshFilter` to draw it. Note that it is a good practice to call `mesh.RecalculateBounds()` and `mesh.RecalculateNormals()` to ensure the mesh behaves as it should.

If we attach the script to an empty `GameObject`, we should see an I-shaped polygon with lots of colors ([see above](#drawing-2d-polygon-meshes)). Check out commit [1 - draw simple polygon mesh](https://github.com/Hyperparticle/draw-shapes-unity/tree/d3b94561d464721a4607f030b6ec2d1c721f131b) to view the results in Unity.

# Specifying Points with the Cursor

<div class="iframe-rounded">
  <iframe class="iframe-responsive" src="https://gfycat.com/ifr/ChubbyHastyElectriceel" scrolling="no" frameborder="0"></iframe>
</div>

To capture input from the mouse and create shapes, we will create a controller that handles this. Its job is to create new shapes if no shapes are being drawn, update shapes if they require additional input, and set shapes loose if they are complete. To keep things simple, a script will collect new vertices whenever the left mouse button is released (click + drag is left as an exercise to the reader).

Let's start with rectangles. To draw one, we can specify its two opposite corner vertices with mouse input.

## Drawing Rectangles

To aid with simple operations on lists of vectors, we will create a `Util.cs` script defining a few operations.

<script src="https://gist.github.com/Hyperparticle/0629b17b6a7ceecec4e40764890058be.js"></script>

Next we create an empty `Rectangle` prefab and attach a script called `DrawRectangle.cs`. The rectangle prefab is expected have a `MeshFilter` to draw its mesh (make sure to add a material to properly display the mesh), and `LineRenderer` to draw a rectangle outline. We expose a public `FillColor` for the mesh.

Here we define two public functions:

1. `AddVertex(Vector2 vertex)` adds a new vertex to its internal list of two vertices and calls `UpdateShape`.
1. If two vertices have been added, `UpdateShape(Vector2 newVertex)` replaces its second vertex with a new one. The four corners are extrapolated from the two vertices to update the mesh and line renderer. The shape is automatically redrawn by Unity every time this function is called.

The third function, `RectangleMesh(Vector2 v0, Vector2 v1, Color fillColor)`, serves to generate the rectangle mesh similar to the I-shaped polygon in the previous section.

<script src="https://gist.github.com/Hyperparticle/e3bb8681cbe88d02fb8af9bc91ac804b.js"></script>

One thing to note is that the rectangle's transform set at the midpoint (centroid) of the mesh. The mesh's vertices are relative to this center point. This will make some future calculations nicer, e.g., attaching a box collider.

## Draw Algorithm

The basic algorithm for drawing a rectangle is as follows:

1. If the left mouse button is released and no rectangle is being updated, create a new rectangle with its two opposite corner vertices at the current mouse position. Indicate that the rectangle is being updated.
1. If a rectangle is being updated, update the rectangle's last corner vertex to be at the current mouse position.
1. If the left mouse button is released and a rectangle is being updated, stop updating it.

Below we define new script called `DrawController.cs`. Its job is to collect mouse input and send mouse coordinates to the currently active rectangle `GameObject`. The property `CurrentShapeToDraw` holds the current rectangle.

<script src="https://gist.github.com/Hyperparticle/e54433fb470af12bae282e57e7063ab3.js"></script>

Check out commit [2 - draw rectangle](https://github.com/Hyperparticle/draw-shapes-unity/tree/82ea8fbdd5e0450ec342132e81de990fe9d658b0) to view the results in Unity.

# Adding 2D Physics

<div class="iframe-rounded">
  <iframe class="iframe-responsive" src="https://gfycat.com/ifr/FlawlessDelightfulHog" scrolling="no" frameborder="0"></iframe>
</div>

From what we have, adding physics to these rectangles is simple. We attach a `Rigidbody2D` and a `BoxCollider2D` to the rectangle prefab. The biggest change is in the `DrawRectangle.cs` script:

1. Add a public `SimulatingPhysics` property that when set, simulates physics with the `Rigidbody2D`. Otherwise, the shape is set to static so that other shapes may interact with it.
1. Update the `BoxCollider2D`'s bounding box after we update the mesh.

<script src="https://gist.github.com/Hyperparticle/20c0b0208565fd5faec031b8ab43d8cc.js"></script>

Lastly, the `DrawController.cs` script adds `CurrentShapeToDraw.SimulatingPhysics = true;` right before `CurrentShapeToDraw = null;` in `AddShapeVertex()` to simulate the shape when it is finished.

And voila, we have physics! Check out commit [3 - add rectangle physics](https://github.com/Hyperparticle/draw-shapes-unity/tree/e029010e71a2fa550b5ce2dfab72ea4ccd2092e3) to view the results in Unity.

# Drawing Multiple Shapes

## Drawing Circles

## Drawing Triangles

## Bonus: Explosions

# Conclusion

I hope you enjoyed the tutorial series and have a new found appreciation for Unity's simple physics interface. And if anyone wants to collaborate with me on creating a full featured game out of this, [contact me](https://twitter.com/hyperparticle).
