# unreal_engine_5_c++_note  

** IN UNREAL EVERYTHING IS INHERITED UObject  
** IN UNREAL EVERYTHING IS A ACTOR(all mesh ,visible object etc),AND ACTOR IS INHERITED UObject too  
** Pawn is a ACTOR(with base movement etc...),Character is a ACTOR also is a Pawn:Character IS INHERITED PAWN Object    

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
