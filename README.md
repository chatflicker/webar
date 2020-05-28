# Zappar for A-Frame

This library allows you use Zappar's best-in-class AR technology with content built using the 3D platform A-Frame.

You may also be interested in:
 - [Zappar for ThreeJS](https://www.npmjs.com/package/@zappar/zappar-threejs) (@zappar/zappar-threejs)
 - Zappar's library for Unity
 - [Zappar for JavaScript](https://www.npmjs.com/package/@zappar/zappar) (@zappar/zappar), if you'd like to build content with a different 3D rendering platform
 - ZapWorks Studio, a full 3D development environment built for AR, VR and MR


## Getting Started

### Bootstrap Projects

Check out these repositories that contain `index.html` files to get you started:
https://github.com/zappar-xr/zappar-aframe-face-tracking-standalone-bootstrap
https://github.com/zappar-xr/zappar-aframe-instant-tracking-standalone-bootstrap
https://github.com/zappar-xr/zappar-aframe-image-tracking-standalone-bootstrap

Or these repositories that have `webpack` setups optimized for development and deployment:
https://github.com/zappar-xr/zappar-aframe-face-tracking-webpack-bootstrap
https://github.com/zappar-xr/zappar-aframe-instant-tracking-webpack-bootstrap
https://github.com/zappar-xr/zappar-aframe-image-tracking-webpack-bootstrap

### Example Projects

There's a repository of example projects for your delectation over here:

https://github.com/zappar-xr/zappar-aframe-examples

## Starting Development

You can use this library by downloading a standalone zip containing the necessary files, by linking to our CDN, or by installing from NPM for use in a webpack project.

#### Standalone Download

Download the bundle from this link:
https://libs.zappar.com/zappar-aframe/0.2.1/zappar-aframe.zip

Unzip into your web project and reference from your HTML like this:
```
<script src="zappar-aframe.js"></script>
```

#### CDN

Reference the zappar.js library from your HTML like this:
```
<script src="https://libs.zappar.com/zappar-aframe/0.2.1/zappar-aframe.js"></script>
```

#### NPM Webpack Module

Run the following NPM command inside your project directory:
```
npm install --save @zappar/zappar-aframe
```

Then import the library into your JavaScript or TypeScript files:
```
import * as ZapparAFrame from "@zappar/zappar-aframe";
```

The final step is to add this necessary entry to your webpack `rules`:
```
module.exports = {
  //...
  module: {
    rules: [
      //...
      {
        test: /zcv\.wasm$/,
        type: "javascript/auto",
        loader: "file-loader"
      }
      //...
    ]
  }
};
```

## Overview

You can integrate the Zappar library with an existing A-Frame scene. A typical project may look like this. The remainder of this document goes into more detail about each of the component elements of this example.
```
<body>
      <a-scene>
          <a-assets>
              <a-asset-item id="target-file" src="myTarget.zpt"/>
          </a-assets>

          <a-entity camera zappar-camera></a-entity>
          
          <a-entity zappar-image="#target-file">
              <!-- YOUR 3D CONTENT GOES HERE -->
          </a-entity>
      </a-scene>
  </body>
```

## Local Preview and Testing

Due to browser restrictions surrounding use of the camera, you must use HTTPS to view or preview your site, even if doing so locally from your computer. If you're using `webpack`, consider using `webpack-dev-server` which has an `https` option to enable this.

Alternatively you can use the [ZapWorks command-line tool](https://www.npmjs.com/package/@zappar/zapworks-cli) to serve a folder over HTTPS for access on your local computer, like this:
```
zapworks serve .
```

The command also lets you serve the folder for access by other devices on your local network, like this:
```
zapworks serve . --lan
```

## Hosting and Publishing Content

Once you've built your site, you have a number of options for hosting your site:
 - Using ZapWork's integrated hosting
 - Self-hosting on servers and a domain that you manage

### ZapWorks Hosting

TODO

### Self-hosting

If you'd like to self-host your content, there are a number of recommendations we make to ensure the best experience for end-users:
 - You need to register your domain name with ZapWorks so that it passes the license check. For more information contact support@zappar.com
 - You must serve the content over HTTPS (due to browser restrictions surrounding the camera)
 - Files in the Zappar library ending with the `.wasm` file extension should be served with the `application/wasm` mime-type
 - Several files in this library (and likely others in your project too) compress well using `Content-Encoding: gzip`. In particular you should serve files with the following extensions with gzip content-encoding: `.wasm`, `.js`, `.zbin`, `.zpt`

## Setting up the Camera

The first step is to add or replace any existing camera you have in your scene with one using the `zappar-camera` component, like this:
```
<a-entity camera zappar-camera></a-entity>
```

Don't change the position or rotation of the camera yourself - the Zappar library will do this automatically.

### User Facing Camera

Some experiences, e.g. face tracked experiences, require the use of the user-facing camera on the device. To activate the user-facing, camera, provide the `user-facing: true` parameter to the `zappar-camera` component:
```
<a-entity camera zappar-camera="user-facing: true;"></a-entity>
```

### Mirroring the Camera

Users expect user-facing cameras to be shown mirrored, so by default the `zappar-camera` will mirror the camera view for the user-facing camera.

Configure this behavior with the following option:
```
<a-entity camera zappar-camera="user-facing: true; user-camera-mirror-mode: poses"></a-entity>
```

The values you can pass to `user-camera-mirror-mode` are:
 - `poses`: this option mirrors the camera view and makes sure your content aligns correctly with what you're tracking on screen. Your content itself is not mirrored - so text, for example, is readable. This option is the default.
 - `css`: this option mirrors the entire canvas. With this mode selected, both the camera and your content appear mirrored.
 - `no-mirror`: no mirroring of content or camera view is performed

There's also a `rear-camera-mirror-mode` parameter that takes the same values should you want to mirror the rear-facing camera. The default `rear-camera-mirror-mode` is `no-mirror`.

### Camera Pose

The Zappar library provides multiple modes for the camera to move around in the A-Frame scene. You can set this mode with the `pose-mode` parameter of the `zappar-camera` component. There are the following options:
 - `default`: in this mode the camera stays at the origin of the scene, pointing down the negative Z axis. Any tracked groups will move around in your scene as the user moves the physical camera and real-world tracked objects.
 - `attitude`: the camera stays at the origin of the scene, but rotates as the user rotates the physical device. When the Zappar library initializes, the negative Z axis of world space points forward in front of the user.
 - `anchor-origin`: the origin of the scene is the center of the group specified by the camera's `pose-anchor-origin` parameter. In this case the camera moves and rotates in world space around the group at the origin.

 The correct choice of camera pose with depend on your given use case and content. Here are some examples you might like to consider when choosing which is best for you:
  - To have a light that always shines down from above the user, regardless of the angle of the device or anchors, use `attitude` and place a light shining down the negative Y axis is world space.
  - In an application with a physics simulation of stacked blocks, and with gravity pointing down the negative Y axis of world space, using `anchor-origin` would allow the blocks to rest on a tracked image regardless of how the image is held by the user, while using `attitude` would allow the user to tip the blocks off the image by tilting it.


## Tracking

The Zappar library offers three types of tracking for you to use to build augmented reality experiences:
 - *Image Tracking* can detect and track a flat image in 3D space. This is great for building content that's augmented onto business cards, posters, magazine pages, etc.
 - *Face Tracking* detects and tracks the user's face. You can attach 3D objects to the face itself, or render a 3D mesh that's fit to (and deforms with) the face as the user moves and changes their expression. You could build face-filter experiences to allow users to try on different virtual sunglasses, for example, or to simulate face paint.
 - *Instant World Tracking* lets you tracking 3D content to a point chosen by the user in the room or immediate environment around them. With this tracking type you could build a 3D model viewer that lets users walk around to view the model from different angles, or an experience that places an animated character in their room.

### Image Tracking

To track content from a flat image in the camera view, use the `zappar-image` component:
```
<a-entity zappar-image="#target-file">
  <!-- PLACE CONTENT TO APPEAR ON THE IMAGE HERE -->
</a-entity>
```

The group provides a coordinate system that has its origin at the center of the image, with positive X axis to the right, the positive Y axis towards the top and the positive Z axis coming up out of the plane of the image. The scale of the coordinate system is such that a Y value of +1 corresponds to the top of the image, and a Y value of -1 corresponds to the bottom of the image. The X axis positions of the left and right edges of the target image therefore depend on the aspect ratio of the image.

#### Target File

`ImageTracker`s use a special 'target file' that's been generated from the source image you'd like to track. You can generate them using the ZapWorks command-line utility like this:
```
zapworks train myImage.png
```

The resulting file can be loaded into an A-Frame as an asset and its ID passed as the parameter of the `zappar-image` component:
```
<a-assets>
    <a-asset-item id="target-file" src="myTarget.zpt"/>
</a-assets>
<a-entity zappar-image="#target-file">
  <!-- PLACE CONTENT TO APPEAR ON THE IMAGE HERE -->
</a-entity>
```

#### Events

The `zappar-image` component will emit the following events on the element it's attached to:
 - `zappar-visible` - emitted when the image appears in the camera view
 - `zappar-notvisible` - emitted when the image is no longer visible in the camera view

Here's an example of using these events:
```
<a-assets>
    <a-asset-item id="target-file" src="myTarget.zpt"/>
</a-assets>
<a-entity zappar-image="#target-file" id="my-image-tracker">
  <!-- PLACE CONTENT TO APPEAR ON THE IMAGE HERE -->
</a-entity>

<script>

let myImageTracker = document.getElementById("my-image-tracker");

myImageTracker.addEventListener("zappar-visible", () => {
  console.log("Image has become visible");
});

myImageTracker.addEventListener("zappar-notvisible", () => {
  console.log("Image is no longer visible");
});

</script>
```


### Face Tracking

To place content on or around a user's face, create a new `zapapr-face` component:
```
<a-entity zappar-face>
  <!-- PLACE CONTENT TO APPEAR ON THE FACE HERE -->
</a-entity>
```

The group provides a coordinate system that has its origin at the center of the head, with positive X axis to the right, the positive Y axis towards the top and the positive Z axis coming forward out of the user's head.

Note that users typically expect to see a mirrored view of any user-facing camera feed. Please see the section on mirroring the camera view earlier in this document.

#### Events

The `zappar-face` component will emit the following events on the element it's attached to:
 - `zappar-visible` - emitted when the face appears in the camera view
 - `zappar-notvisible` - emitted when the face is no longer visible in the camera view

Here's an example of using these events:
```
<a-entity zappar-face id="my-face-tracker">
  <!-- PLACE CONTENT TO APPEAR ON THE IMAGE HERE -->
</a-entity>

<script>

let myFaceTracker = document.getElementById("my-image-tracker");

myFaceTracker.addEventListener("zappar-visible", () => {
  console.log("Face has become visible");
});

myFaceTracker.addEventListener("zappar-notvisible", () => {
  console.log("Face is no longer visible");
});

</script>
```


### Face Mesh

In addition to tracking the center of the face using `zappar-face`, the Zappar library provides a face mesh that will fit to the face and deform as the user's expression changes. This can be used to apply a texture to the user's skin, much like face paint.

To use the face mesh, create an entity within your `zappar-face` entity and use the `face-mesh` primitive type, like this:
```
<a-entity zappar-face id="my-face-tracker">
    <a-entity geometry="primitive: face-mesh; face: #my-face-tracker" material="src:#my-texture; transparent: true;"></a-entity>
</a-entity>
```

### Instant World Tracking

To track content from a point on a surface in front of the user, use the `zappar-instant` component:
```
<a-entity zappar-instant="placement-mode: true;" id="my-instant-tracker">
    <!-- PLACE CONTENT TO APPEAR IN THE WORLD HERE -->
</a-entity>
```

With the `placement-mode: true` parameter set, the instant tracker will let the user choose a location for the content by pointing their camera around the room. When the user indicates that they're happy with the placement, e.g. by tapping a button on-screen, remove that parameter to fix the content in that location:
```
<a-entity zappar-instant="placement-mode: true;" id="my-instant-tracker">
    <!-- PLACE CONTENT TO APPEAR IN THE WORLD HERE -->
</a-entity>
<script>
    let myInstantTracker = document.getElementById("my-instant-tracker");
    document.body.addEventListener("click", () => {
        myInstantTracker.setAttribute("zappar-instant", "placement-mode: false;");
    })
</script>
```

The group provides a coordinate system that has its origin at the point that's been set, with the positive Y coordinate pointing up out of the surface, and the X and Z coordinates in the plane of the surface.
