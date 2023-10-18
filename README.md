# Decentraland Alternative Explorers Testing Tool 🔧

## Introduction

The entities that make up the Decentraland scenes can be modified with different components. The purpose of this tool is to check the correct behavior of the components in the different alternative clients.
To do so, it is necessary to prepare a scene where we can visualize individually (or combining if necessary) the behavior of the different components and compare it by means of screenshots to the correct behavior (which will be obtained in the foundation client).

## Components:

### Transform:

This component, as its name indicates, transforms the entity, giving it position, rotation, scale and a parent. 
- Position: Vector3
- Scale: Vector3
- Rotation: Quaternion
- Parent: Entity

### AvatarAttach:

Again, the name is quite descriptive. This component gives us the possibility of attaching an entity to the Player entity, but not simply as a child, it gives us a series of options to attach it in a osition, for example right hand, left hand, etc. It should be noted that this component verwrites the **Transform**, so if we want to change for example the position, we must assign a parent entity and then attach that parent to the avatar.

correct way:

```Typescript

const childEntity = engine.addEntity()
const parentEntity = engine.AddEntity()

Transform.create(childEntity, {parent:parentEntity, position:Vector3.create(0,1,0)})

// Attach to loacl player
AvatarAttach.create(parentEntity, {
    anchorPointId: AvatarAnchorPointType.AAPT_NAME_TAG,
})

// Attach to another player, by ID
AvatarAttach.create(parentEntity, {
avatarId: '0xAAAAAAAAAAAAAAAAA',
    anchorPointId: AvatarAnchorPointType.AAPT_NAME_TAG,
})

```

wrong way:

```Typescript

const myEntity = engine.addEntity()

Transform.create(myEntity, {position:Vector3.create(0,1,0)})

// Attach to loacl player
AvatarAttach.create(myEntity, {
    anchorPointId: AvatarAnchorPointType.AAPT_NAME_TAG,
})

// Attach to another player, by ID
AvatarAttach.create(myEntity, {
avatarId: '0xAAAAAAAAAAAAAAAAA',
    anchorPointId: AvatarAnchorPointType.AAPT_NAME_TAG,
})

```

### Billboard:

This component also overwrites a property of **Transform**. Billboard makes sure that the entity it is affecting always faces the player's camera. You can choose the rotation axis or a combination of two (only with the x and y axes).

### MeshRenderer:

MeshRenderer gives a primitive shape to the entity that will be rendered in the scene. We can give it basic shapes such as cube, sphere, plane or cylinder (the latter is given two diameters, so it can be considered a truncated cone). 
To modify this geometry we must use the **Transform** component.

### Material:

If the entity has the **MeshRenderer** component, it is possible to add the Material component to it, which will provide material to the figure rendered by the MeshRenderer component. The Material component supports different properties such as texture, color, emission color and intensity, transparencies, etc.

### GltfContainer:

Component that allows us to load a 3D model in **glTF** format together with its textures, skeleton, animations, etc. It is very useful when rendering primitive figures with MeshRenderer is not enough.

### MeshCollider:

MeshCollider works similarly to MeshRenderer, this time instead of rendering a primitive shape it will create it as a collider, and you can choose its behavior by selecting the collider layer.

### Visibility:

Gives visibility or invisibility to an entity. Note that even if the entity has **MeshCollider** it will still collide even if it is invisible.

### CameraMode and CameraModeArea:

These components can be explained together. CameraMode gives us information about the state of the player's camera, this can be in first or third person. CameraModeArea will force this property in a volume determined by a vector of three dimensions. When the player enters the zone delimited in the CameraModeArea it doesn't matter his current CameraMode, it will force the one set in the properties of the component.
It should be noted that this component will only be affected by the position and rotation properties of the Transform component, not by the scale property. You can only determine its size by modifying its **area** property.

```Typescript
const modArea = new Entity()

CameraModeArea.create(modArea, {
     //This is only one way to size to the component
    area: Vector3.create(16, 1, 14),
    cameraMode: CameraMode.FirstPerson,
  })
```

### NftShape:

This component renders a 2D NFT in a frame that can be chosen from many styles. The frame adjusts to the size of the NFT automatically.

```
urn:decentraland:<CHAIN>:<CONTRACT_STANDARD>:<CONTRACT_ADDRESS>:<TOKEN_ID>
```

This string includes:

- The network (currently only Ethereum is supported)
- The contract standard that this token is based on, for example erc721
- The contract of the token (for example, the CryptoKitties contract)
- The id of the specific token to display

### AudioStream and AudioSource:

Both components serve to output sound. AudioSource for sounds emitted directly from the entity, i.e. from a certain position in the scene, while AudioStream emits sound in the entire scene no matter where you are positioned in the scene.
In addition AudioSource uses sound clips from the project, preferably from the separate ```/sounds``` folder, while AudioStream allows to use audio from external resources, as long as they are https urls, are allowed hosts in the scene and do not violate [CORS Policies](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing)🔗

### Animator:

This component manipulates the behavior of animations in a 3D model. It can store different states, each of these is an object that defines each animation. For example which clip of the model it is, if it is playing, if the loop is true or false, the playback speed or the weight of the animation, which is used to give different intensity to the movement of the affected bones.

### TextShape:

This component renders floating text which can be positioned with **Transform** and its size can be set from the font size or scale of the Transform. It has several properties that can be defined such as color, line spacing, alignment, etc.