# unreal_engine_5_c++_note  

**IN UNREAL EVERYTHING IS INHERITED UObject** 
  
**IN UNREAL EVERYTHING IS A ACTOR(all mesh ,visible object etc),AND ACTOR IS INHERITED UObject too**  
  
**Pawn is a ACTOR(with base movement etc...),Character is a ACTOR also is a Pawn:Character IS INHERITED PAWN Object**   
  

## Example : Using unreal Api(C++)  
##### CREATEING A FUNCTION AND VARIABLE IN A CLASS AND EXPOSE TO UE EDITOR  
```
DEF:  
 UPROPERTY(Property type)   
 //type eg:  
 //EditAnywhere/EditDefaultOnly/EditInstanceOnly        
 //BlueprintReadOnly//BlueprintReadWrite/BlueprintReadWrite/  
 Function/Variable  
```  
  
### EXAMPLE 1:
```
UPROPERTY(VisibleAnywhere)  
UStaticMeshComponent* VisualMesh; //a component that will act as our mesh / visual repersentation object (eg:door/rock etc)  
```  
  
##### TO INIT THE OBJECT
```
//CREATING A SubObj with Mesh Componet and name it Mesh  
VisualMesh = CreateDefaultSubobject<UStaticMeshComponent>(TEXT("Mesh"))  
  
//Set current mesh componet and add it to RootComponent  
//SetupAttahment ,add it to it parnent  
VisualMesh->SetupAttahment(RootComponent)  
  
  
//This Line mean :find a cube in our content folder : /Game/StarterContent/Shapes/Shape_Cube.Shape_Cube  
static ConstructorHelpers::FObjectFinder<UStaticMesh> CubeVisualAsset(TEXT("/Game/StarterContent/Shapes/Shape_Cube.Shape_Cube"));    
//check is that cube has been found   
//call Successed method to check  
if (CubeVisualAsset.Succeeded())  
{  
    //use that mesh in UStaticMeshComponent  
    //Using `setStaticMesh(obj)`  
    VisualMesh->SetStaticMesh(CubeVisualAsset.Object);  
    
    //SetRealativeLocation => set current compoment location and relative to its paranent
    VisualMesh->SetRelativeLocation(FVector(0.0f, 0.0f, 0.0f));  
}
```

##### WORKING WITH THAT COMMPONENT
```
//Run it for every tick
//get current actor /component location and store in FVector(x,y,z)
FVector NewLoaction = GetActorLocation() ;

//get current actor /component rotation and store in FRotation(x,y,z)
FRotator NewRotation = GetActotrRotation();

//get current time when is this component has been created
float runningTime = GetGameTimeSinceCreation() //get time with second 

//calculate its moving height using math and current tick
//DeltaTime is coming from tick function
//all math method is provided by FMath object
float DeltaHeight = (FMath::Sin(RunningTime + DeltaTime) - FMath::Sin(RunningTime));

//set the new location of Z axis
NewLocation.Z += DeltaHeight * 20.0f; // set the height by fator 20.0f

//set the rotation degree by time
float DeltaRotation = DeltaTime * 20.0f; //every second 20 eg:1s *20 degree = 20 2s*20 = 40degree

//set the new rotation of Yaw
Yaw : Rotate Z axis ，Pitch :rotate X Axis ,Roll:rotate Y Axis
NewRotation.Yaw += DeltaRotation;

//after setting localtion and rotation ,apply to component
//setActorLocationAndRotation(FVector,FRotator) ,there are  setActorLocation(FVector) and setActor(FRotator)
SetActorLocationAndRotation(NewLocation, NewRotation);
```
##### AFTER BUILD AND USE
** RESULT:  
![alt example1](https://upload.cc/i1/2021/05/28/vstqeY.jpg "example 1")  

#### ADDITIONAL FEATRUE  
**Add 2 more property**  
```
  //Edit any where and blurprint can read and edit,property group as FloatingActor
  UPROPERTY(EditAnywhere,BlurprintReadWrite,Catgory="FloatingActor")
  float FloatSpeed = 20.0f; //by default 20.0f
  
  UPROPERTY(EditAnywhere ,BlueprintReadWrite,Catgory="FloatingActor")
  float RotationSpeed = 20.0f; //by default 20.0f

```
***change the fixed speed to variable***   
```
  NewLocation.Z += DeltaHeight * FloatSpeed; //contorl it by FloatSpeed
  float DeleteRotation = DeltaTime * RotationSpeed; // control it by RotationSpeed
```
*those value can edit via UE Editor*  
**WE CAN CREATE ANY BLUEPRINT BASE ON THIS COMPONENT LIKE INHERITED**

##UNREAL Source code(example)
  
*FloatingActor.h*  
```
#pragma once

#include "CoreMinimal.h"
#include "GameFramework/Actor.h"
#include "FloatingActor.generated.h"

UCLASS()
class QUICKSTART_API AFloatingActor : public AActor
{
    GENERATED_BODY()

public: 
    // Sets default values for this actor's properties
    AFloatingActor();

    UPROPERTY(VisibleAnywhere)
    UStaticMeshComponent* VisualMesh;

protected:
    // Called when the game starts or when spawned
    virtual void BeginPlay() override;

public: 
    // Called every frame
    virtual void Tick(float DeltaTime) override;

};
```

*FloatingActor.cpp*  
  
```
#include "FloatingActor.h"

// Sets default values
AFloatingActor::AFloatingActor()
{
    // Set this actor to call Tick() every frame.  You can turn this off to improve performance if you don't need it.
    PrimaryActorTick.bCanEverTick = true;

    VisualMesh = CreateDefaultSubobject<UStaticMeshComponent>(TEXT("Mesh"));
    VisualMesh->SetupAttachment(RootComponent);

    static ConstructorHelpers::FObjectFinder<UStaticMesh> CubeVisualAsset(TEXT("/Game/StarterContent/Shapes/Shape_Cube.Shape_Cube"));

    if (CubeVisualAsset.Succeeded())
    {
        VisualMesh->SetStaticMesh(CubeVisualAsset.Object);
        VisualMesh->SetRelativeLocation(FVector(0.0f, 0.0f, 0.0f));
    }
}

// Called when the game starts or when spawned
void AFloatingActor::BeginPlay()
{
    Super::BeginPlay();

}

// Called every frame
void AFloatingActor::Tick(float DeltaTime)
{
    Super::Tick(DeltaTime);

    FVector NewLocation = GetActorLocation();
    FRotator NewRotation = GetActorRotation();
    float RunningTime = GetGameTimeSinceCreation();
    float DeltaHeight = (FMath::Sin(RunningTime + DeltaTime) - FMath::Sin(RunningTime));
    NewLocation.Z += DeltaHeight * 20.0f;       //Scale our height by a factor of 20
    float DeltaRotation = DeltaTime * 20.0f;    //Rotate by 20 degrees per second
    NewRotation.Yaw += DeltaRotation;
    SetActorLocationAndRotation(NewLocation, NewRotation);
}
```
  
//----------------------------------------------------------------------------------------------------------------//
//-------------------------------------##EXAMPLE 2##-----------------------------------------------------//
//----------------------------------------------------------------------------------------------------------------//

**IN THIS EXAMPLE WE WILL WORKING WITH C++ -> BluePrint**  
**ALLOW AS TO EXTEND OUR C++ CLASS BY Blueprint**  
  
*Every time creating a c++ class,will generate the header bollow*
  ```
  #include "GameFramework/Actor.h"
#include "MyActor.generated.h"

UCLASS()
class AMyActor : public AActor
{
    GENERATED_BODY()

public:
    // Sets default values for this actor's properties
    AMyActor();

    // Called every frame
    virtual void Tick( float DeltaSeconds ) override;

protected:
    // Called when the game starts or when spawned
    virtual void BeginPlay() override;
};
```
### WHAT iS Beigin Play And Tick?? ###  
**Begin Play : lets us know the actor has enter the game in a playable state**
*IS A GOOD PLACE TO INITATE GAMEPLAY LOGIN!*
  
 **Tick: It will call one per frame with amount of elapsed time(time tick)**
 *WE CAN DO SOME RECURRING LOGIN HERE,BUT IT WILl LOSE SOME PERFORMANCE**
 #### **Make sure disable tick in constructor** ####
 ```
  PrimaryActorTick.bCanEverTick = true; //set to false
 ```
 
 **After we are created a class,we can create a blueprint class base on this class and it will allow use to use its method or variable**
 
#### IF EDITOR CHANGE THE VALUE,HOW CAN WE CHANGE IN C++ TOO(a value that will calculate by a function,not change directly by editor)? ####

##### When editor change the value,engine will notifies target object that something is changed in editor ,so that we need to add a hook(like a method) let engine to call/trigger it when editor in change something(eg:exposing value etc),need to re-calculate the value when trigger.

*A Hook is a function that call by engine when current target is changed by editor*
```
#if WITH_EDITOR //needd this for editor only
Structure for passing pre and post edit change events ：FPropertyChangedEvent
void AMyActor::PostEditChangeProperty(FPropertyChangedEvent& PropertyChangedEvent)
{
    CalculateValues();

    Super::PostEditChangeProperty(PropertyChangedEvent);
}
#endif
```

### We can also make our function expose to blueprint ###
*BlueprintCallable*
```
UFUNCTION(BlueprintCallable, Category="Damage")
void CalculateValues();
```

#### Exposing Variable and function ###
**They are using different Marco**
  
`Variable:`
```
UPROPERTY(Property)
exposingVariable;
```
  
`fucntion:`
```
UFNNCTION(Property,Category) //Property is always using BlueprintCallable and Category is required!!
exposingFunction;
```

*BEST APPROACH IS TO USE C++ FOR BUILDING BASE GAMEPLAY SYSTEM AND PERFORMANCE CRITICAL CODE WITH BLUEPRINT USED TO CUSTOMIZE BEHAVIOUR.*

### TO ALLOW Blueprint implememnt C++ function ###
**There are 2 property**

1.UFUNCTION(BlueprintImplementableEvent,Category="some event")
2.UFUNCTION(BlueprintNativeEvent,Categoty="some event")

```
BlueprintImplememnttableEvent => c++ decalare the function ,but not provide the function body,like c++ but do nothing,this function is designed to be override by a blueprint,let blueprint to implement and override that function

BlueprintNativeEvent =>c++ decalare the function and provide the body,the the body name of function use {ourFunctionName}_Implement to instead of {ourFunctionName},is designed to be override by a blueprint
```
**BlueprintImplememnttableEvent can't be implement**  
**BlueprintNativeEvent body function must be followed by this format => name_Implement(){}**  

# dive Deep to UE C++
## Unreal Gameplay Classes:Object,Actors and Components  
**4 Main class types dervie from major gameplay classes**  
* UObject
* AActor
* UActorComponet
* UStruct

### Unreal Object(UObject)  
**UObject is base building block in Unreal**  
**UObject is couple with UClass**  
**UObject and UClass provide some important services**  
* Reflection of properties and methods
  * UPROPERTY AND UFUNCTION (UClass)
* Serializetoin of properties => what is that think??????????
* Garbage Collection (a Rubbish collection)
* Finding Obj by name 
* Configurable value for porperty
* Newworking support
  
#### Each class in Unreal is derives from UObject*
#### Each class has a singleton(single/once) UClass instance for providing mata services {What UObject will look like, what property method etc are available ...}
### *Each gameplay calss(object) lifectime, the root is UObject and UClass*  
*Not involve directly deriving from UObject but instead from AActor and UActorComponent(working with that)*
  
### AACtor
**AACtor is a UOject,a part of the gameplay experence**  
**AACtor can either placed by us or gamepley system and destory through gameplay code(c++ or blueprint) by grabage collection**  
**Everything in our level is AACtor.Any Object extended from this class can place in the level**  
**Actor can have their own behaviour and it can have a component hierarchy of Actor components(through composition)**
**Actor components composition is done by Actor RootComponent，and RootComponent is contain a single USceneCompoent**
*USceneceComponent allow actor to translation , rotation and scale*  
  
#####AActor event call
* Begin Play : Called it when Actor is first come into UEWorld(spaning) during gameplay
* Tick : Called it once per frame
* EndPlay : Called it when Actor is leaving the UEWorld(Destory)

**Actor lifecycle :Actor loaded into existence(BeginPlay),the level is unloaded and the actor is destoryed**
**Spawning Actor is more complicated than creating a actor：need to be registered with variety of runtime system(when to spawn, location ,rotation and so on)**
**UWorld Provide a member to spawn actor**
```
The process of creating a new instance of an Actor
AActor* UWorld::SpawnActor
(
    UClass*         Class,
    FName           InName,
    FVector const*  Location,
    FRotator const* Rotation,
    AActor*         Template,
    bool            bNoCollisionFail,
    bool            bRemoteOwned,
    AActor*         Owner,
    APawn*          Instigator,
    bool            bNoFail,
    ULevel*         OverrideLevel,
    bool            bDeferConstruction
)

```
*WHEN ACTOR IS DESTOPRY, GRABAGE COLLECTION WILL COLLECT THE DESTORY OBJECT*

**Other than spawing, we can call destory to destory the actor**
```
//Allow use to perform some logic before actor go into garbage collection
AACtor::Destory ->calling EndPlay method
```
** We can also control actor life(how long does actor exist) **
```
//Set the lifeSpan,set a time to control
AActor::SetLifeSpanSet 
//When time has expired, it will automatically Destory the actor
//the lifespan of this actor.
```

