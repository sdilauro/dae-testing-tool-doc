# Decentraland Alternative Explorers Testing Tool

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