# ECS7016P: Interactive Agents and Procedural Generation 
## Coursework 3: Tech Demo(Maze Runner)
### Download project from given link:

### Introduction
In this project we create an interactive tech demonstration of some game AI techniques in Unity, it consists
of a level generator that outputs a level which contains an autonomous interactive agent. Users can also interact
with agents by manipulating simple third person player characters to navigate levels. The tech demo aims to show how to 
use the appropriate game AI technology for procedural content generation(PCG) and interactive agents(Navigation and Behaviour).

### Prerequisites
This Tech Demo requires Unity game engine. You can access it via the Unity Hub, if pre-installed or download it to your
own computer: https://unity3d.com/get-unity/download

<b>Scripting Language:</b> C#<br />
Visual Studio is the default code editor for Unity, used to edit C# scripts in this project. If you prefer another editor,
you can configure this in Preferences > External Tools.  Visual Studio Code and Jetbrains Rider are common alternatives.

<b>Unity Hub Version</b><br />
* Login with a free Unity personal account.
* Under Installs, check that you have a recent version of the Unity editor (2020.3 or higher).

### Assets Used
Visit the Unity asset store and log in using the same account that you used to log in to the Unity Hub.  We have used
Standard Assets (for Unity 2018.4) found on this link: 
https://assetstore.unity.com/packages/essentials/asset-packs/standard-assets-for-unity-2018-4-32351#description

### How to Execute
This project contains both versions of the Maze Runner game i.e. Autonomous AI Agent(using NavMesh & Path-Finding) 
and Agent Controlled by user with Procedural Content Generation. You must work with the complete version:
Open the Unity Hub Editor, go to "SampleScene" scene(Assets/Scenes/SampleScene.unity) and press play.  

### Select Agent
Now in the editor's Hierarchy view, select the ThirdPersonController object.  In the Inspector, the ThirdPersonCharacter script
component shows list of different attributes you can set for that player, for instance, moving turn speed, Jump Power etc.
Similarly, you can select AIThirdPersonController object which is autonomous AI controlled with similar attributes exposed.
Also note that the game can be executed including both the agents at same time, just by enabling it from the Inspector of that particular game object.

Player will start from center of arena, play the game a few times, the controls for player are "WASD" or "UP,LEFT,DOWN,RIGHT" 
keys, "Space" to jump and "C" to crouch

AI Agent will start at the center of arena and will find its way to input destination location using A-Star Path Finding
algorithm and sensory inputs to Turn, Jump and Crouch, for this setting go to the Hierarchy select the "Destination" game object
and set X and Z coordinate location of your choice and hit play.

The main hierarchy and initial screen should look like this:
* Hierarchy<br /><img height="150" src="/Users/bhavya/Documents/Interactive Agents And Procedural Generation/TechDemo_190004273/Images/Hierarchy.png" width="200"/><br /><br />
* Initial Game Screen<br /><img height="300" src="/Users/bhavya/Documents/Interactive Agents And Procedural Generation/TechDemo_190004273/Images/GameScreenInitial.png" width="500"/>

## Agent

### Third Person User Control Character
The ThirdPersonCharacter(in ThirdPersonController)script exposes a number of properties which determine the jump power, the amount of control while
in air, and various other speed and behaviour modifiers. For more detail about each setting, see the comments in the script.
The ThirdPersonUserControl script takes input from the "CrossPlatformInput" class included in given location
(Assets/Agent/CrossPlatformInput/Scripts).
* Script Properties<br /><img height="300" src="/Users/bhavya/Documents/Interactive Agents And Procedural Generation/TechDemo_190004273/Images/TPController.png" width="300"/>

### Third Person AI Control Character
The AICharacterControl(in AIThirdPersonController)script exposes Target property, where we pass in the Destination 
object(set X and Z coordinates) in Hierarchy view, this allows AI agent to navigate autonomously to the set destination. This agent also 
has ThirdPersonCharacter Script similar to User controlled Agent, exposing same properties as defined above to manipulate
similar behaviour.
* Script Properties<br /><img height="300" src="/Users/bhavya/Documents/Interactive Agents And Procedural Generation/TechDemo_190004273/Images/AIController.png" width="300"/>

### Agent Navigation & Behaviour

**A-Star Path Finding, How Does It Work?**<br />
There are 3 classes: Node2D, Grid2D, and Pathfinding2D found in Assets/Agent/Character/Scripts

**Node2D:** A non-physical object that acts as a marker for where each tile is within the Grid and stores the cost of that
tile and whether it is an obstacle or passable.<br /><br />
**Grid2D:** Creates a 2D array of nodes within a certain bound, gridWorldSize, and facilitates Pathfinding2D to iterate
through the Grid.<br /><br />
**Pathfinding2D:** Where the calculation of the appropriate path occurs. Given 2 transforms, seeker and target, and an instance
of Grid2D, will find the shortest possible path between them. The FindPath() function, using the copy of the grid that
it made in Start() creates the Node2D List that contains all the Nodes forming the path from startPos to targetPos,
starting at the Node closest to startPos and ending with the Node at targetPos's location. As long as startPos and targetPos
do not occupy the same Node, the list will be at least one Node large. This List is passed to the "path" variable in your
gridowner object. Then we call

`"object_name".GetComponent<Pathfinding2D>()."gridowner_name".GetComponent<Grid2D>().path[0].worldPosition;`<br /><br />
to get the position vector of the first Node in the List.For my game, I simply set the position to the first Node in
the List during that characters turn:<br /><br />
`seeker.transform.position = seeker.GetComponent<Pathfinding2D>().GridOwner.GetComponent<Grid2D>().path[0].worldPosition;`

**Agent Navigation**<br />
To designing navigation system for this tech demonstration we employed the Unity NavMesh system which consists of the following pieces:

* NavMesh (short for Navigation Mesh) is a data structure which describes the walkable surfaces of the game world and 
allows finding path from one walkable location to another in the game world. The data structure is built, or baked, 
automatically from your level geometry.
* NavMesh Agent component helps to create characters which avoid each other while moving towards their goal. Agents 
reason about the game world using the NavMesh, and they know how to avoid each other as well as moving obstacles.
* Off-Mesh Link component allows to incorporate navigation shortcuts which cannot be represented using a walkable surface.
For example, jumping over a wall, can be all described as Off-mesh links.
* NavMesh Obstacle component allows to describe moving obstacles the agents should avoid while navigating the world. 
While the obstacle is moving the agents do their best to avoid it, but once the obstacle becomes stationary it will carve 
a hole in the navmesh so that the agents can change their paths to steer around it, or if the stationary obstacle is 
blocking the pathway, the agents can find a different route.(Future Enhancement)

In the game Hierarchy, we created NavMesh game object, it has PathFinding2d Script attached to it, which exposed certain 
properties for our autonomous AI agent to navigate through the levels. To find path between two locations in the scene, 
we first map the start(seeker position) and destination(destination position) locations to their nearest polygons. Then we start searching from the start 
location, visiting all the neighbours until we reach the destination polygon. Tracing the visited polygons allows us to find
the sequence of polygons which will lead from the start to the destination. Algorithm we used to find the path is A* (pronounced
“A star”), which is what Unity uses. For more info please refer code comments.
* Script Properties<br /><img height="300" src="/Users/bhavya/Documents/Interactive Agents And Procedural Generation/TechDemo_190004273/Images/NavMesh.png" width="300"/><br /><br />

**Agent Behaviour**<br />
The class ThirdPersonCharacter found in Assets/Agent/Character/Scripts

The Start method gets all the required components of an agent and starts the execution.
Move method allows the agent to move in given direction, where we pass in the direction vector and boolean flags if agent
wants to crouch or jump. Now, the control and velocity handling is different when grounded and airborne: interfacing with 
the underlying player controls. We control ground movement using HandleGroundedMovement() method and air movements using 
HandleAirborneMovement() method, for ground movements we pass in the booleans crouch and jump. The agent perception code
(implemented in AICharacterControl script)updates the action choice with some useful information: distance to the target (the destination), whether the target is 
in front and/or on the right, and how far off-centre it is, position of obstacles/walls. The Behaviour methods define four
simple behaviours: TurnSlowly, TurnRapidly, MoveBehaviour, JumpBehaviour, and CrouchBehaviour which depends on the parameter we pass in 
for variables exposed in script. Now if the agent crouches, we handle the scaling part using ScaleCapsuleForCrouching() method
and also check whether we have required space to be standing using PreventStandingInLowHeadroom() method.
Finally, we update the animator as per the action taken by the agent using UpdateAnimator() method passing in the move vector.

### Code Files
1. <b>Autonomous AI Agent:</b><br />
Assets/Agent/Character/Scripts/ThirdPersonCharacter.cs<br />
Assets/Agent/Character/Scripts/AICharacterControl.cs<br /><br />

2. <b>Third Person Agent:</b><br />
Assets/Agent/Character/Scripts/ThirdPersonCharacter.cs<br />
Assets/Agent/Character/Scripts/ThirdPersonUserControl.cs<br />

## Generator

### Generator implementation
We use a recursive backtracking algorithm, it selects a random current node to add its position to the stack, selects one
of the unvisited neighbors to break the wall between them, and adds the current node to the new position. It works by moving
to stack and repeat the process until you reach a dead end. When it reaches a dead end, it returns to the  visited node
by removing the element from the stack, finding a node with an unvisited adjacency and trying to find a non-zero node.
Repeat the process until there are no more elements on the stack, at which point the maze is complete.

Procedural Maze Generation using Recursive Backtracking
Logic Video Explanation: https://youtu.be/e5zDG4JIsyg

### Generator design
We have created a MazeRenderer game object, available in Hierarchy View, where we attached MazeRenderer Script, which exposes 
few properties i.e. height, width and size of the wall(refer image below). Diving into the execution logic, Start() method in MazeRenderer script 
will first generate the Maze and then finally render it in the game environment. The main logic for maze generation is in 
MazeGenerator Script, where we call the Generator() method, which eventually calls ApplyRecursiveBacktracker() method 
to generate maze using graph traversal algorithm. Nodes already visited during the generation process are tracked by
GetUnvisitedNeighbours() method. We use a utility function GetOppositeWall() to get the type of wall during the whole process.
As shown in the below given section Generator output 3 different types of maze generated procedurally using this algorithm.
Refer to comments in code for more info.

* Script Properties<br /><img height="300" src="/Users/bhavya/Documents/Interactive Agents And Procedural Generation/TechDemo_190004273/Images/MazeRenderer.png" width="300"/>

### Generator output
<img height="200" src="/Users/bhavya/Documents/Interactive Agents And Procedural Generation/TechDemo_190004273/Images/Map1.png" width="300"/>
<img height="200" src="/Users/bhavya/Documents/Interactive Agents And Procedural Generation/TechDemo_190004273/Images/Map2.png" width="300"/>
<img height="200" src="/Users/bhavya/Documents/Interactive Agents And Procedural Generation/TechDemo_190004273/Images/Map3.png" width="300"/>

### Code Files
1. <b>Maze Generator</b><br />
Assets/Generator/MazeGenerator.cs<br /><br />

2. <b>Maze Renderer</b><br />
Assets/Generator/MazeRenderer.cs<br />

## Video Documentation
* URL: 

## Future Enhancements

The minimal viable product of tech demo shows the Agent Navigation using NavMesh, implementing agent behaviours like JUMP, CROUCH
RUN, TURN(360 Degree). The AI Agent has capability of finding the shortest path to given destination using A-Star Path Finding 
Algorithm and autonomously navigating to it using sensory inputs from the game environment. Future enhancements of this demo might 
include enemies where agent have to find the path to destination hiding from enemies and collecting gold and power-ups(like jump 
strength, running speed) along the way where we can use the behaviours efficiently. Other than this we might include more 
complex behaviour implementation like planning and navigation to NCP enemies using waypoint navigation for patrolling. 

Procedural generation used at this stage is by using graph traversal techniques, but in future enhancements we can employ more complex 
evolutionary algorithms to generate various stages with different types of obstacles and difficulty levels from easy to hard.

#### Submitted by Bhavya Rajesh Makwana, Student ID: 190004273