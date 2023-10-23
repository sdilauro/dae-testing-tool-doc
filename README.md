# Decentraland Alternative Explorers Testing Tool 🔧

## INDEX:
### Component description:
- [Transform](#transform)
- [AvatarAttach](#avataratach)
- WIP...

## Introduction

The entities that make up the Decentraland scenes can be modified with different components. The purpose of this tool is to check the correct behavior of the components in the different alternative clients.
To do so, it is necessary to prepare a scene where we can visualize individually (or combining if necessary) the behavior of the different components and compare it by means of screenshots to the correct behavior (which will be obtained in the foundation client).

## Components:

<a id="transform"></a>
### Transform:

This component, as its name indicates, transforms the entity, giving it position, rotation, scale and a parent. 
- Position: Vector3
- Scale: Vector3
- Rotation: Quaternion
- Parent: Entity

<a id="avataratach"></a>
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

### VideoPlayer:

This component stores statuses containing information about videos such as the source, which can be a file uploaded to the scene, or from an external source, or live via Decentraland Cast.

### VideoEvent:

This component has information about the state of the **VideoPlayer**, it can be queried to, for example, synchronize player events with scene events such as starting an animation.

### PointerEvents:

This component has the ability to make the entities interact, there are different ways to handle an interaction:
- Register a callback
- System-based
- Advanced

The inputEvent can be of different type (DOWN, UP, HOVER_ENTER or HOVER_LEAVE)

## Relationships:

### Transform and spatial components:

Transform relates to most of the spatial components that need to be spatially defined in the scene.

### Visibility and rendered components:

All entities with rendered components, such as primitive shapes, 3d models, text shapes, nft shapes, etc. can be made visible or invisible with this component.

### VideoPlayer and MeshRenderer, Material, VideoEvents:

To play a video we need an entity (in this example: ```screen```) visible in the scene (create MeshRenderer) to which we must set a material (create Material) with a video texture:

```Typescript
const screen = engine.addEntity()

MeshRenderer.createOrReplace(screen, { mesh: { $case: 'plane', plane: { uvs: [] } } })
Transform.create(screen, 
    { position: { x: 0, y: 0, z: -0.55 },
     scale: Vector3.create(0.9, 0.9, 1),
     parent: frame 
})

VideoPlayer.create(screen, {
    src: 'assets/videos/starwarstrailer.mp4',
    playing: playing,
    volume: 0.2,
    loop: true
})

const videoTexture = Material.Texture.Video({ videoPlayerEntity: screen })

Material.setPbrMaterial(screen, {
    texture: videoTexture,
    roughness: 1.0,
    specularIntensity: 0,
    metallic: 0,
})
```
In addition, VideoPlayer can be combined with VideoEvent, a component that we can consult and read all its information to use in the scene. The information is as follows:

```Typescript
videoEvent: {
    currentOffset: number
    state: number
    tickNumber: number
    timestamp: number
    videoLength: number
}
```

### PointerEvents and MeshCollider, PointerEventsResult:

To interact with the entities by means of the player pointer it is necessary that the entities have a collision mesh set in the collision layer **CL_POINTER**. When the entity receives an interaction, a **PointerEventResult** is generated. It has data about the event, including things like the button that was activated, and where the pointer was pointing at at the time.


## How to test components?

### Transform:

To test Transform we will use a series of snapshots that we will compare with snapshots automatically generated by the test. In this case we will take a cube and we will position it in different positions to validate the ```position``` property, then we will rotate the figure 45 degrees in each axis to test ```rotation```. Next, we will scale the entity twice, once with values lower than 1 and once with values higher than 1.
Finally we must test ```parent``` getting a change of the absolute position to buy with the snapshot.

### Animator:
Animator will be tested in a visual way through snapshots, we will have to animate a simple model that contains at least 2 animations to be able to build a cycle of states in which each one is reproduced separately and in the future test with the weights of the same ones to reproduce them as a whole.

### AudioSource and AudioStream:
To test AudioSource and AudioStream we will have to prepare a simple audio with intermittent tones and compare it with previously recorded audios, the audio to be played must allow verification:
- if it is playing
- the volume
- if it is looping
- the pitch

In the case of AudioSource, we should modify the position of the entity and test that the sound is affected in 3D.

### AvatarAttach and AvatarBase:
To test this component we will generate an NPC Avatar in the scene, seting up differents properties of AvatarBase and attach a sphere to the different anchor points, then verify the snapshots.

### AvatarEmotesCommand: WIP
### AvatarEquippedData: WIP

###