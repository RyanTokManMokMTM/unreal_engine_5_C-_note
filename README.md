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
```c++
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
```c++
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
```c++
  //Edit any where and blurprint can read and edit,property group as FloatingActor
  UPROPERTY(EditAnywhere,BlurprintReadWrite,Catgory="FloatingActor")
  float FloatSpeed = 20.0f; //by default 20.0f
  
  UPROPERTY(EditAnywhere ,BlueprintReadWrite,Catgory="FloatingActor")
  float RotationSpeed = 20.0f; //by default 20.0f

```
***change the fixed speed to variable***   
```c++
  NewLocation.Z += DeltaHeight * FloatSpeed; //contorl it by FloatSpeed
  float DeleteRotation = DeltaTime * RotationSpeed; // control it by RotationSpeed
```
*those value can edit via UE Editor*  
**WE CAN CREATE ANY BLUEPRINT BASE ON THIS COMPONENT LIKE INHERITED**

##UNREAL Source code(example)
  
*FloatingActor.h*  
```c++
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
  
```c++
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
  ```c++
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
 ```c++
  PrimaryActorTick.bCanEverTick = true; //set to false
 ```
 
 **After we are created a class,we can create a blueprint class base on this class and it will allow use to use its method or variable**
 
#### IF EDITOR CHANGE THE VALUE,HOW CAN WE CHANGE IN C++ TOO(a value that will calculate by a function,not change directly by editor)? ####

##### When editor change the value,engine will notifies target object that something is changed in editor ,so that we need to add a hook(like a method) let engine to call/trigger it when editor in change something(eg:exposing value etc),need to re-calculate the value when trigger.

*A Hook is a function that call by engine when current target is changed by editor*
```c++
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
```c++
UPROPERTY(Property)
exposingVariable;
```
  
`fucntion:`
```c++
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
  
##### AActor event call
* Begin Play : Called it when Actor is first come into UEWorld(spaning) during gameplay
* Tick : Called it once per frame
* EndPlay : Called it when Actor is leaving the UEWorld(Destory)

**Actor lifecycle :Actor loaded into existence(BeginPlay),the level is unloaded and the actor is destoryed**
**Spawning Actor is more complicated than creating a actor：need to be registered with variety of runtime system(when to spawn, location ,rotation and so on)**
**UWorld Provide a member to spawn actor**
``` c++
//The process of creating a new instance of an Actor
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
**We can also control actor life(how long does actor exist)**
```c++
//Set the lifeSpan,set a time to control
AActor::SetLifeSpanSet 
//When time has expired, it will automatically Destory the actor
//the lifespan of this actor.
```
  
### UActorComponent
**Actor Components have their own behaviors**
**Actor Components provide responsible for functionality that across different type of Actor**  
**An Actor can have many Actor Components and Actor performance a individual tasks for this actor overall the roles**  
**Actor Component can be attached to other components**  
**Actor Component can only attached to one parnent ,but it may have many child Components attached to itself**  
*A tree hierarchy of actor components*  
  
#### **A relationship between actor and actor component** ####
*Actor ask question and Actor component to answer the question(actor tell component to do the task,component might do the task)*
  
**RootComponent is the top-level component of tree hieranrchy**  
**Ticking is a component are tigger the tick function of the owning Actor Tick Function**  

![Alt text](https://upload.cc/i1/2021/05/30/dychkL.jpg "3 personal component hierarchy")  
*Actor with those components:RootComponnent:CapsuleComponent ->3 children(ArrowComponent,Mesh,FirstPersonCameraComponent[1 child: Mesh1p])*
![Alt text](https://upload.cc/i1/2021/05/30/Capzm4.jpg "3 personal component hierarchy")  
*DEMO For those components*

### UStruct
**UStruct do not extends from any class and used to make our struct**
```c++
//To make our struct and UE will do a base work for us
UStrcut()
```
  
**UStruct do not have grabage collection, we need to manage UStrcut lifecycle ourself**
**In UStrcut the type must be UObject reflection type to support for editing in UE Editor,Blueprint...**

### Unreal Reflection System  
##### What is Reflection System?
**Reflection System is a implementation of reflection that enable dynamic feature in Unreal**
* Garbage collection
* serialization
* networking replication
* BP/C++ Communication
**We need to use a correct markup，otherwise Unreal will ignore the data and not generate the reflection data**

*Reflection markup*
* *UCLASS***Tell unreal to generate a reflection data for a class, the class must derive from UObject(top level obj in unreal)**
* *USTRCUT*:**Tell unreal to generate a reflection data for a struct(UStrcut type must be the sported type in unreal）** 
* *GENERATE BODY*: **Unreal Relaces this with all necessary boilerplate code that get generated for the type(會生成type必要的boilerplate code)** 
* *UPROPERTY*:**Enable a member variable of a UClass or UStruct to be used as a UPROPERTY. UPROPERTY has many uses, not only expose variable to editor and blueprint**
* *UFUNTION*: **Enable a class mehod of a UClass or UStruct to be used as a UFUNCTION. A UFUNCTION can allow the class method to be called form a Blurprints.**

```c++
#include "MyObject.generated.h" //this header file=> unreal will put all the reflection data into this file, we must include it at the end of the header file

UClass(Blueprintable)
class UMyObject : public UObject{
   GENERATED_BODY() 
   
 public:
  MyUObject();

  UPROPERTY(BlueprintReadOnly, EditAnywhere)
  float ExampleProperty;

  UFUNCTION(BlueprintCallable)
  void ExampleFunction();

}
```
*All marco of reflection can put some additional specifiers*  
```c++
UCLASS(Blueprintable) // UClass have many specifiers -> check document
  class();
```
```c++
UPROPERTY(BlueprintReadOnly,EditAnywhere) // UPROPERTY have many specifiers -> check document****
  int x;
```

```c++
UFUNCTION(BlueprintCallable) // UPROPERTY have many specifiers -> check document
  void x();
```
*Some basic property*
* Blueprintable : the class can be extened by Blueprint
* BlurprintReadOnly : this property can be read only from Blueprint
* EditAnywhere : this property can be edited bu property windown，on prototype and instance
* Category: a Section of property in editor， is a helpful for organize the property
  
[UCLASS PROPERTY DOC](https://docs.unrealengine.com/4.26/en-US/ProgrammingAndScripting/GameplayArchitecture/Classes/Specifiers/)  
[UPROPERTY PROPERTY DOC](https://docs.unrealengine.com/4.26/en-US/ProgrammingAndScripting/GameplayArchitecture/Properties/Specifiers/)  
[UFUNCTION PROPERTY DOC](https://docs.unrealengine.com/4.26/en-US/ProgrammingAndScripting/GameplayArchitecture/Functions/Specifiers/)  
[USRUCT PROPERTY DOC](https://docs.unrealengine.com/4.26/en-US/ProgrammingAndScripting/GameplayArchitecture/Structs/Specifiers/) 
   
### Oject/Actor Iterators: Loop over all object or custom object/actor in gameplay world
*just like a for loop*
```c++
//Using a contaniner will a object type
//Object iterators 
for(TObjectIterator<Type> it;it;++it){
  Type* CurrentObj = *it; //all it is a Type object
}
```
  
*Example of TOjectIterator to iterate over all instance of UObject*
```c++
for (TObjectIterator<UObject> It; It; ++It)
{
    UObject* CurrentObject = *It;
    UE_LOG(LogTemp, Log, TEXT("Found UObject named: %s"), *CurrentObject->GetName());
}
```
*We can only iterate our custom object which is derive from UObject*
```c++
for(TObjectIterator<MyOwnClass> it;it;++it){
  MyOwnClass* currentObj = *it;
  UE_LOG(LogTemp,Log,TEXT("object name: %s"),*currentObj->GetName());
}
```
**Warnning: Using Iterator**
##### this will lead to unexpedted result: it may return all UObject instaces created for game world instance
```
Using object iterators in PIE (Play In Editor) can lead to unexpected results. Since the Editor is loaded, the object iterator will return all UObject instances created for your game world instance, in addition to those that are just being used by the Editor.
```

**AACtor interate is the same way as UObject interate ,but class must deride from AActor**  
*Loop over all actor ，we can get the actor from some world*
```c++
APlayerController* MyPC = GetMyPlayerControllerFromSomewhere();
UWorld* World = MyPC->GetWorld();

//loop over the actor from current player contoller world
//we can using GetWorld() to get  UWorld object
for (TActorIterator<AEnemy> It(World); It; ++It)
{
    // ...
}
```
*AActor is derive from UOject , we can only use TObjectiterator to instead*  
*But we need to take care about PIE issue (play in editor,editor is loaded,everything is UObject)*





 
