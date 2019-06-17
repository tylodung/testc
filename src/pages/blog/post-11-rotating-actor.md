---
templateKey: blog-post
path: /rotating-actor
title: Rotating Actor
image: https://res.cloudinary.com/several-levels/image/upload/v1512221876/rotating-actor_kuncdl.jpg
video: ku61sRkcQHQ
tags: ["rotation"]
uev: 4.18.3
date: 2017-12-01T05:25:44.226Z
description: Learn how to rotate an actor every frame.
---
**Github Link: [https://github.com/Harrison1/unrealcpp/tree/master/RotatingActor](https://github.com/Harrison1/unrealcpp/tree/master/RotatingActor)**

*For this tutorial we are using the standard first person C++ template with starter content. If you don't know how to add a new actor class to your project, please visit the [Add C++ Actor Class](/add-actor-class) post.*

In this tutorial we are going to change an actor's rotation every frame. Create a new `C++` actor class and call it **RotatingActor**. In the header file we will create 3 float variables. We will set their `UPROPERTY` to `EditAnywhere` so we can change the values in the editor and we'll put all of the variables inside the **Movement** category to keep them together and seperate from other attributes. Below is the final header script.

### RotatingActor.h
```cpp
#pragma once

#include "CoreMinimal.h"
#include "GameFramework/Actor.h"
#include "RotatingActor.generated.h"

UCLASS()
class UNREALCPP_API ARotatingActor : public AActor
{
	GENERATED_BODY()
	
public:	
	// Sets default values for this actor's properties
	ARotatingActor();

protected:
	// Called when the game starts or when spawned
	virtual void BeginPlay() override;

public:	
	// Called every frame
	virtual void Tick(float DeltaTime) override;

	// declare our float variables 	
	UPROPERTY(EditAnywhere, Category = Movement)
	float PitchValue;

	UPROPERTY(EditAnywhere, Category = Movement)
	float YawValue;

	UPROPERTY(EditAnywhere, Category = Movement)
	float RollValue;
	
};
```

In the `.cpp` file set the default values to 0 for all the float variables.

#### set default values
```cpp
// Sets default values
ARotatingActor::ARotatingActor()
{
 	// Set this actor to call Tick() every frame.  You can turn this off to improve performance if you don't need it.
	PrimaryActorTick.bCanEverTick = true;

	PitchValue = 0.f;
	YawValue = 0.f;
	RollValue = 0.f;

}
```

In the `Tick` function create a new `FRotator` variable titled `NewRotation` and set it equal to our float variables we created earlier. Next, create an `FQuat` variable and set it equal to `NewRotatoin`. Then, all we have to do is run `AddActorLocalRotation` to have our actor spin in place. Below is the final `.cpp` code.

#### Tick function
```cpp
/ Called every frame
void ARotatingActor::Tick(float DeltaTime)
{
	Super::Tick(DeltaTime);

	// on every frame change rotationg for a smooth rotating actor
	FRotator NewRotation = FRotator(PitchValue, YawValue, RollValue);
	
	FQuat QuatRotation = FQuat(NewRotation);
	
	AddActorLocalRotation(QuatRotation, false, 0, ETeleportType::None);


	// The below method causes a bug for the pitch value. The pitch value stops updating at 90 degrees
	// this is a known bug in the Unreal Engine. 
	// solution found by ue4 user nutellis https://answers.unrealengine.com/questions/591752/pitch-rotation-stucks-at-90-90-c.html

	// FRotator NewRotation = GetActorRotation();

	// NewRotation.Pitch += PitchValue;
	// NewRotation.Yaw += YawValue;
	// NewRotation.Roll += RollValue;
	
	// OR add values using the FRotator Add function
	// FRotator NewRotation = GetActorRotation().Add(PitchValue, YawValue, RollValue);


	// SetActorRotation(NewRotation);

}
```

Compile the code. Drag and drop your new actor into your game. Add a staic mesh component to the actor. Set a value for the `PitchValue` and then when you push play the actor will spin in place. 