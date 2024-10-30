+++
date = '2024-10-27T22:30:56-07:00'
draft = false 
title = 'Pong Tutorial in Godot; Part 1'
+++
(v0.1)

<!-- markdown-toc start - Don't edit this section. Run M-x markdown-toc-refresh-toc -->
**Table of Contents**

- [Introduction](#introduction)
- [The Godot Graphics Pipeline](#the-godot-graphics-pipeline)
- [Install the Godot Engine](#install-the-godot-engine)
- [Design ](#design)
- [Create the Pong Project](#create-the-pong-project)
- [Viewport](#viewport)
    - [Classes and Class Inheritance](#classes-and-class-inheritance)
- [Text Label](#text-label)
    - [Object Instantiation and Variables](#object-instantiation-and-variables)
    - [Dynamic vs Static Typing](#dynamic-vs-static-typing)
- [Frame Rate](#frame-rate)
    - [Singletons](#singletons)
- [Game Loop](#game-loop)
    - [The SceneTree ](#the-scenetree)
    - [Variable Scope](#variable-scope)
    - [Static and Instance Methods](#static-and-instance-methods)
    - [Conditionals](#conditionals)
    - [Constants](#constants)
- [Handling Player Input](#handling-player-input)
    - [Handling Input](#handling-input)
- [Game Exit](#game-exit)
    - [Object Constructors and Destructors, Finalizers ](#object-constructors-and-destructors-finalizers)
- [Conclusion](#conclusion)
    - [The Final Code](#the-final-code)
- [Advanced Concepts](#advanced-concepts)

<!-- markdown-toc end -->

# Introduction
This is the first in a series of tutorials building up to a basic Pong clone implemented using the [Godot engine](https://godotengine.org/). We will try to accomplish as much as we can in [GDScript](https://docs.godotengine.org/en/stable/tutorials/scripting/gdscript/gdscript_basics.html), which is the native scripting language of the Godot engine, relying less on Godot's SceneTree editor and Property Inspector. These tools, while useful for experimentation, can make refactoring code, debugging, and writing unit tests more difficult; [grepping](https://en.wikipedia.org/wiki/Grep) over files in your project folder is much faster than having to manually inspect properties of the Nodes in the SceneTree, for example.

[OOP (Object-Oriented Programming)](https://en.wikipedia.org/wiki/Object-oriented_programming) is the driving paradigm in the design of [GDScript and the Godot engine](https://docs.godotengine.org/en/stable/tutorials/best_practices/what_are_godot_classes.html). You don't need to become an expert in Object Orientated programming to use the Godot engine or GDScript, but you should take the time to become somewhat proficient. This tutorial introduces some principles of OOP but won't go into much detail as this isn't a general programming tutorial.

The amount of effort and time required to complete any software task is often underestimated. In other words, your game will take longer than you think. Here's a method to estimate any software task; round your first estimate up to the unit of estimation, then double that. For example, if your initial estimate to complete your game is three and a half months, then round up to four months and double that, giving eight months. Double that again if you are providing this estimate to a Producer, Product Owner or Project Manager; so sixteen months.

So instead of jumping in and creating the Pong clone, we will implement something seemingly straightforward, displaying the [frame rate](https://en.wikipedia.org/wiki/Frame_rate) (fps -- frames-per-second).

![Pong FPS Tutorial 1](/posts/pong-tutorial-1/pong-execute.png "Displaying the frame rate in Pong FPS Tutorial 1")

We begin with a high level description of how the Godot engine [displays graphics to the screen](#the-godot-graphics-pipeline).

# The Godot Graphics Pipeline
An object (Node) must be added to the Godot graphics pipeline to be rendered to the display. Nodes are visible in the Godot editor in the SceneTree hierarchy.

[![An example of a SceneTree, from the Godot documentation on Nodes and Scenes](./nodes-and-scenes.webp)](https://docs.godotengine.org/en/stable/getting_started/step_by_step/nodes_and_scenes.html)

Pipeline components are described at a high level below;

- The [RenderingServer](https://docs.godotengine.org/en/stable/classes/class_renderingserver.html) projects game objects (Nodes) onto a Viewport.

- A [Viewport](https://docs.godotengine.org/en/stable/tutorials/rendering/viewports.html) owns the surface that the RenderingServer draws to. The Viewport is itself a [Node](https://docs.godotengine.org/en/stable/classes/class_node.html) in the SceneTree. The Viewport surface is what is displayed on your screen unless otherwise modified by a Camera Node in the SceneTree.

- The [SceneTree](https://docs.godotengine.org/en/stable/classes/class_scenetree.html) is a [Scene Graph](https://en.wikipedia.org/wiki/Scene_graph) containing a hierarchy of Nodes, one of which is the Viewport. The SceneTree also manages the [MainLoop](https://docs.godotengine.org/en/stable/classes/class_mainloop.html) (the game loop). As we will learn later, the MainLoop defines the virtual methods `_initialize()`, `_finalize()`, and `_process()` that we [override](https://docs.godotengine.org/en/stable/tutorials/scripting/overridable_functions.html) with our game logic.

- A [Node](https://docs.godotengine.org/en/stable/classes/class_node.html) is the basic building block of the Godot engine. Only nodes that are added to the SceneTree either in the Godot Editor or using the [`"add_child"` method](https://docs.godotengine.org/en/stable/tutorials/scripting/nodes_and_scene_instances.html#creating-nodes) can be displayed. A Node can have up to one parent Node and zero or more child Nodes. The Node at the head of the SceneTree is the "Root Node". Note that "parent" and "child" here refer only to the Node hierarchy in the SceneGraph and not [class inheritance](https://en.wikipedia.org/wiki/Inheritance_(object-oriented_programming)).

- A Viewport is associated with a [DisplayServer](https://docs.godotengine.org/en/stable/classes/class_displayserver.html) which is responsible for window management.
  
- A [Camera Node](https://docs.godotengine.org/en/stable/classes/class_camera2d.html) can be 2D or 3D and provides customization beyond what the Viewport provides. For example, a Camera3D Node is required to render a scene in 3D. A Camera also allows for only a subset of the Viewport to be rendered to the screen. Most Godot projects use a Camera Node. We do not as this tutorial does not require one.
  
- [Input](https://docs.godotengine.org/en/stable/classes/class_input.html) and [Engine](https://docs.godotengine.org/en/stable/classes/class_engine.html#class-engine-method-get-frames-per-second) are [Singletons](https://en.wikipedia.org/wiki/Singleton_pattern) providing access to the underlying capabilities of the Godot engine. Input provides access to keyboard, mouse, and game pad events. The Engine provides access to engine internals such as version, the current frame count, the frame rate (that we need), etc.

- The RenderingServer renders Nodes in the SceneTree that appear in the hierarchy below the Viewport Node to that Viewport surface using transform parameters in the Camera Node if one is present. A SceneTree may contain multiple Viewport Nodes but we will only make use of the default "Root Viewport" Node at the head of the SceneTree.

# Install the Godot Engine

![Download and install](https://godotengine.org/) the Godot engine if you have not already.

# Design 
The tasks below outline the overall design. We go into detail in the following sections as to why.

Tasks 

0. [ ] [Create the Project](#the-project),
1. Initialization:
    1. [ ] [Create a Viewport](#viewport),
    2. [ ] Add the Viewport to the SceneTree,
    3. [ ] [Create a Text Label](#text-label),
    4. [ ] Add the Label as a child Node of Viewport, 
    5. [ ] Assign the Label a position in x/y coordinates,
2. Process each frame;
    1. [ ] [Retrieve the current frame rate](#frame-rate),
    2. [ ] [Print the frame rate to the Label Node](#game-loop).
    3. [ ] [Process keyboard events](#handling-player-input),
    4. [ ] Exit when `Esc` is depressed.
3. [ ] [Game Exit](#game-exit)

# Create the Pong Project
Create a default project with SceneTree, Root Node and the Viewport.

* Start Godot and create a project named "Pong" in a new folder,

![Godot Editor - Create a new project](/posts/pong-tutorial-1/create-project.png "New Project")

Note: The "new Folder" icon is all the way to the right.

![Godot Editor - Create a new folder](/posts/pong-tutorial-1/create-folder.png "New Folder")

* Then choose "Other Node",

![Godot Editor - Create "Other Node"](/posts/pong-tutorial-1/new-other-node.png "New Other Node")

* And create the base "Node" (Class "Node", Base class for all scene objects).

![Godot Editor - Create a basic Node](/posts/pong-tutorial-1/create-new-node.png "Create a basic Node")

* Rename the root Node to "Pong",

![Godot Editor - Rename the Root Node](/posts/pong-tutorial-1/rename-node.png "Rename Root node")

![Godot Editor - Rename the Root Node](/posts/pong-tutorial-1/node-rename.png "Rename Root node")

* Create a folder `res://src` where we will place our project source files so as to declutter the project root directory.

![Godot Editor - Create a src subdirectory](/posts/pong-tutorial-1/res-src.png "Create a src subdirectory")

* Attach a new script to the filename root "Pong" node.

![Godot Editor - ](/posts/pong-tutorial-1/attach-script.png "Attach a new script to the root Node")

* And rename to `res://src/Pong.gd`

![Godot Editor - ](/posts/pong-tutorial-1/new-script-rename.png "Create a src subdirectory")

![Godot Editor - ](/posts/pong-tutorial-1/script-name.png "res://src/Pong.gd script")

* Open the `Pong.gd` file. Godot should have generated the following boilerplate code.

``` gdscript
extends Node


# Called when the node enters the scene tree for the first time.
func _ready() -> void:
	pass # Replace with function body.


# Called every frame. 'delta' is the elapsed time since the previous frame.
func _process(delta: float) -> void:
	pass

```

OK, that's about all the work we need to do in the Scene editor in this tutorial.

# Viewport
The RenderingServer renders the child Nodes of the Viewport to the Viewport surface. The Viewport surface is displayed to the screen.

We don't need to create a RenderingServer or DisplayServer as these are managed by the Godot Engine. We also don't need to create a Viewport as the "Root Node" by default is also the "Root Viewport". We can also do without creating a [Camera Node](https://docs.godotengine.org/en/stable/classes/class_camera2d.html) as we will be rendering the entire Viewport surface to our display without any customization.

See [Classes and Class Inheritance](#classes-and-class-inheritance) for the implementation.

## Classes and Class Inheritance
Think of a class as the blueprints for a house and an object as the house once created (instantiated).

Open the script file `Pong.gd`. This file defines the `Pong` class and will contain the game logic we defined in the [Design](#design) section. Change the first line as follows;

``` gdscript
class_name Pong extends Node
```

The `extends` keyword means we are extending `Node` [with a class](https://docs.godotengine.org/en/stable/tutorials/best_practices/what_are_godot_classes.html) `Pong`. Pong will subclass and [inherit the methods and attributes](https://en.wikipedia.org/wiki/Inheritance_(object-oriented_programming)) from [`Node`](https://docs.godotengine.org/en/stable/classes/class_node.html) (inheritance being one of the principles of [OOP](https://en.wikipedia.org/wiki/Object-oriented_programming)). Almost all Godot components inherit directly or indirectly from Node. As we need to hook into the SceneTree, `Pong` must inherit from Node as well. `Pong.gd` will also contain the code we write in this tutorial.

We don't explicitly create an instance of `Pong` [using `new()`](#object-instantiation-and-variables) because we added it as a Node to the SceneTree. The SceneTree handles the creation and deletion of Nodes in its hierarchy. See the following section [Object Instantiation and Variables](#object-instantiation-and-variables).

# Text Label
We cannot render text directly to a Viewport surface as it does not support this capability, so we cannot print "Hello World" as we would to a terminal. Instead we need to add a [Label](https://docs.godotengine.org/en/stable/classes/class_label.html) Node to the SceneTree and write the current frame rate to this control using its [`set_text()` method](https://docs.godotengine.org/en/stable/classes/class_label.html#class-label-property-text).

See [Object Instantiation](#object-instantiation-and-variables), and [Dynamic vs Static Typing](#dynamic-vs-static-typing) for the implementation.

## Object Instantiation and Variables
We need to create an instance of [`Label`](https://docs.godotengine.org/en/stable/classes/class_label.html) to display the frame rate -- in effect we are [instantiating](https://en.wikipedia.org/wiki/Instance_(computer_science)) a new `Label` object. We do this by calling the [constructor](https://en.wikipedia.org/wiki/Constructor_(object-oriented_programming)) method
[`new()`](https://docs.godotengine.org/en/stable/tutorials/scripting/nodes_and_scene_instances.html#creating-nodes) on the `Label` class which returns a new object of type `Label`.

``` gdscript
var fps: Label = Label.new() # Create a new Label object
```

The `=` assigns the object returned by `Label.new()` to a new
 [variable](https://en.wikipedia.org/wiki/Variable_(computer_science)) created by [`var`](https://docs.godotengine.org/en/stable/tutorials/scripting/gdscript/gdscript_basics.html#data) having the name`fps` and type `Label`.

We are now able to call the [`set_text()` method](https://docs.godotengine.org/en/stable/classes/class_label.html#class-label-property-text) on the instance of `Label` stored in `fps`, passing in the current frames per second as a `String` [argument](https://en.wikipedia.org/wiki/Parameter_(computer_programming)#Parameters_and_arguments).

``` gdscript
var fps: Label = Label.new() # Create a new Label object
fps.set_text("frames per second") 
```
## Dynamic vs Static Typing
[GDScript is a dynamically](https://docs.godotengine.org/en/stable/tutorials/scripting/gdscript/gdscript_advanced.html) [typed language](https://en.wikipedia.org/wiki/Type_system#Static_type_checking) that supports [explicit type definitions](https://docs.godotengine.org/en/stable/tutorials/scripting/gdscript/static_typing.html).

In the example below, we assign a `Label` object to the _untyped_ variable `fps_untyped` and a `Label` object to the _typed_ variable `fps_typed`. The typed variable `fps_typed` can only hold objects of type `Label` or objects of classes that inherit from `Label`. An attempt to assign any other type will cause a compile time error. The untyped variable `fps_untyped` can hold objects of any type.

``` gdscript
var fps_untyped = Label.new() # Untyped

var fps_typed: Label = Label.new() # Typed 
```

It is good practice to specify types as much as possible, even though doing so makes code more verbose, as this eliminates an entire class of errors that can be caught by a developer at compile time versus having players experience these bugs at runtime. Languages like Python are dynamically typed while a language like Go is statically typed.

# Frame Rate
Now we need to calculate or retrieve the current frame rate. Handily, Godot provides the [get_frame_per_second() method](https://docs.godotengine.org/en/stable/classes/class_engine.html#class-engine-method-get-frames-per-second) for this purpose. 

See [Singletons](#singletons) for the implementation.

## Singletons
Godot provides the [Singleton](https://en.wikipedia.org/wiki/Singleton_pattern) object [Engine](https://docs.godotengine.org/en/stable/classes/class_engine.html) that implements the method [`get_frames_per_second()`](https://docs.godotengine.org/en/stable/classes/class_engine.html#class-engine-method-get-frames-per-second) that returns the current frames-per-second. In general terms, a Singleton can be thought of as a global variable containing an instance of Engine. As the term implies, there can only be one instance of Engine and it is managed by Godot. As such, we don't need to create an instance of [`Engine`](https://docs.godotengine.org/en/stable/classes/class_engine.html#class-engine-method-get-frames-per-second) using `new()`.

``` gdscript
var fps: Label = Label.new() # Create a new Label object
fps.set_text(str(Engine.get_frames_per_second())) # Set fps.text to the current fps
```

The `str()` function converts the `float` returned by `Engine.get_frames_per_second()` to a `String` value required by `set_text`. Not performing this conversion will result in a runtime error.

# Game Loop
Still not done; 

0. [X] [Create the Project](#the-project),
1. Initialization:
    1. [X] [Create a Viewport](#viewport),
    2. [X] Add the Viewport to the SceneTree,
    3. [X] [Create a Text Label](#text-label),
    4. [ ] Add the Label as a child Node of Viewport, 
    5. [ ] Assign the Label a position in x/y coordinates,
2. Process each frame;
    1. [X] [Retrieve the current frame rate](#frame-rate),
    2. [ ] [Print the frame rate to the Label Node](#game-loop).
    3. [ ] [Process keyboard events](#handling-player-input),
    4. [ ] Exit when `Esc` is depressed.
3. [ ] [Game Exit](#game-exit)

Steps (1.1) - (1.5) should be performed once, while steps (2.1) - (2.4) are performed each frame. We can accomplish this by [overriding](https://docs.godotengine.org/en/stable/tutorials/scripting/overridable_functions.html) the `_initialize()` and `_process()` methods defined in the [MainLoop](https://docs.godotengine.org/en/stable/classes/class_mainloop.html).

See [The SceneTree](#the-scenetree), [Variable Scope](#variable-scope), [Static and Instance Methods](#static-and-instance-methods), [Conditionals](#conditionals), and [Constants](#constants) for the implementation.

## The SceneTree 
The [SceneTree](https://docs.godotengine.org/en/stable/classes/class_scenetree.html) is what is called a [scene graph](https://en.wikipedia.org/wiki/Scene_graph), a hierarchy of Nodes in an inverted tree structure.

The `Label` must be added to a Node in the SceneTree before the frame rate can be displayed. One of [our first steps](#create-the-pong-project) was to add our `Pong` Node to the SceneTree by [subclassing the Root Node](#classes-and-class-inheritance). We are now able to add `Label` as a child Node to the `Pong` Node using the [`add_child`](https://docs.godotengine.org/en/stable/classes/class_node.html#class-node-method-add-child) method. Note that [`Label`](https://docs.godotengine.org/en/stable/classes/class_label.html) is also a subclass of Node and therefore inherits all of Node's attributes and methods.

``` gdscript
var fps: Label = Label.new() # Create a new Label object
self.call_deferred("add_child", fps) # Explicitly add the label to the scene
fps.set_position(Vector2(0,0)) # Set position
fps.set_text(str(Engine.get_frames_per_second())) # Set fps.text to the current fps
```

Calls to `add_child()` (and `free()`) are generally passed into a [`call_deferred()`](https://docs.godotengine.org/en/stable/classes/class_object.html#class-object-method-call-deferred) method that postpones execution of `"add_child"` until idle time.

In the code, we call `self.call_deferred`, where `self` refers to "the current Node", in this case the `Pong` Node. GDScript does allow `self` to be omitted but I prefer to use `self` as it makes explicit that a method is being invoked on the current Node.

The SceneTree manages the [MainLoop](https://docs.godotengine.org/en/stable/classes/class_mainloop.html) which defines the virtual methods `_initialize()`, `_finalize()`, and `_process()`.

To recap;

1. Initialization:
    0. Create the project,
    1. Create a Viewport,
    2. Add the Viewport to the SceneTree,
    3. Create a Label Node,
    4. Add the Label as a child Node of Viewport, 
    5. Assign the Label a position in x/y coordinates,
2. Process each frame;
    1. Retrieve the current frame rate,
    2. Print the frame rate to the Label Node.
    3. Process keyboard events,
    4. Exit when `Esc` is depressed.

To implement "Initialization" and "Process each frame", we [override the virtual methods](https://docs.godotengine.org/en/stable/tutorials/scripting/overridable_functions.html), `_ready()_` and `_process()`. Unlike Instance methods, Virtual methods must be implemented by the subclass.

* `_ready()` is called after a node is added to the scene graph and is ready for use, and
* `_process()` is called each frame.

``` gdscript
func _ready():
    var fps: Label = Label.new() # Create a new Label object
    self.call_deferred("add_child", fps) # Explicitly add the label to the scene
    fps.set_position(Vector2(0,0)) # Set position

func _process(_delta: float):
    fps.set_text(str(Engine.get_frames_per_second())) # Set fps.text to the current fps
```

But how does Godot know to call our methods? When the game starts, Godot will instantiate the root node (`Pong`). `Pong` instantiates the `Label` Node and adds this as a child node to itself (using `"add_child"`). The SceneTree calls [`_ready()`](https://docs.godotengine.org/en/stable/classes/class_node.html#class-node-private-method-ready) after Pong the Pong Node is added to the SceneTree, and calls [_process()](https://docs.godotengine.org/en/stable/classes/class_node.html#class-node-private-method-process) once per frame. The SceneTree will call `_ready()` and `_process()` on the `Label` object if we had overridden those methods, which we do not.

## Variable Scope
The previous code exhibits a bug in that `fps` is defined as a local variable meaning it is visible only within [the scope](https://en.wikipedia.org/wiki/Scope_%28computer_science%29#Function_scope) of `_ready()`. But `fps` [needs to be visible](https://en.wikipedia.org/wiki/Scope_%28computer_science%29#Lexical_scope) to both `_ready()` and `_process()`. We must therefore define `fps` as an instance variable, placing `fps` in the class scope and [visible to all methods of the class](https://docs.godotengine.org/en/stable/tutorials/scripting/gdscript/gdscript_basics.html#functions). We create an instance variable by moving `fps:` to the top level.

``` gdscript
var fps: Label # Crete a variable of type Label

func _ready():
    fps = Label.new() # Create a new Label object
	  self.call_deferred("add_child", fps) # Explicitly add the label to the scene

func _process(_delta: float):
    fps.set_text(str(Engine.get_frames_per_second())) # Set fps.text to the current fps
```

_instance_ and _local_ variables differ to _static_ variables in that the value of a _static_ variable is shared across all instances of a class, whereas _instance_ and _local_ variables are specific to an instance.

## Static and Instance Methods
Internally, Godot uses _static_, _instance_, and _virtual_ methods (the engine is written in C++), however GDScript only supports _static_ and _instance_ methods. A developer [can override the _virtual_ methods](https://docs.godotengine.org/en/stable/tutorials/scripting/overridable_functions.html) in predefined classes (e.g. `SceneTree`), but GDScript does not provide the ability for a developer to create their own _virtual_ methods.

## Conditionals
We don't need to update the frame rate on each frame (e.g. 60 times a second), so we make use of an [if conditional](https://docs.godotengine.org/en/stable/tutorials/scripting/gdscript/gdscript_basics.html#if-else-elif) to update the frame rate every tenth frame.

``` gdscript
func _process(_delta: float) -> void:
	if Engine.get_process_frames() % 10 == 0:
		fps.set_text(FPS_TEXT + str (Engine.get_frames_per_second()))
```

The `%` is the modulo or remainder operator. It returns the remainder when dividing the number of frames since game start (`Engine.get_process_frames()`) by `10`. If the remainder is `0` then the conditional is `true` and we update the frame rate.

## Constants
We define several [constants](https://docs.godotengine.org/en/stable/tutorials/scripting/gdscript/gdscript_basics.html#constants) with values for maximum frame rate, fps update interval, and the FPS text. Constants are used instead of variables when we don't want the value assigned to a variable to change during program execution. Some other advantages of constants include improving code readability, and changing the value of a constant will be reflected everywhere that constant is used.

``` gdscript
class_name Pong extends Node2D

const MAX_FPS = 0 # Zero means use the maximum frame rate
const UPDATE_INTERVAL = 10 # Update the frames per second every ten frames.
const FPS_TEXT = "FPS: " # Constant prefix displayed with the current fps, e.g. "FPS: 60"
const ACTION_UI_CANCEL = "ui_cancel"

var fps: Label # Crete a variable of type Label

func _ready():
	Engine.set_max_fps(MAX_FPS) # Set the "do not exceed" frame rate.
  fps = Label.new() # Create a new Label object
	fps.set_position(Vector2(0,0)) # Set position
	self.call_deferred("add_child", fps) # Add the label to the scene

func _process(_delta: float) -> void:
	if Engine.get_process_frames() % UPDATE_INTERVAL == 0:
		fps.set_text(FPS_TEXT + str(Engine.get_frames_per_second()))
```

# Handling Player Input
We can exit the game by closing the Godot window. But as the plan is to use the keyboard to control the paddles, we may as well explore how to handle keyboard input and exit the game when the `Esc` key is depressed.

The methods to retrieve player input are provided by the [Input Singleton](https://docs.godotengine.org/en/stable/classes/class_input.html). We use the [is_action_pressed()](https://docs.godotengine.org/en/stable/classes/class_input.html#class-input-method-is-action-pressed) method and filter events to the `"ui_cancel"` signal which is by default mapped to the `Esc` key.

See [Handling Input](#handling-input) for the implementation.

## Handling Input
Checking if the player depressed the `Esc` key is straightforward. [`Input.is_Action_Pressed("ui_cancel")`](https://docs.godotengine.org/en/stable/classes/class_input.html#class-input-method-is-action-pressed) returns `true` when an event action is received that is mapped to the `Esc` key.

``` gdscript
func _process(_delta: float) -> void:
	if Input.is_action_pressed(ACTION_UI_CANCEL): exitGame()
	if Engine.get_process_frames() % UPDATE_INTERVAL == 0:
		fps.set_text(FPS_TEXT + str (Engine.get_frames_per_second()))

func exitGame() -> void:
	self.get_tree().root.propagate_notification(NOTIFICATION_WM_CLOSE_REQUEST)
	self.get_tree().quit()
```

We define a method `exitGame()` to send the "window close" request to the DisplayServer (attached to the root node) and tell the SceneTree to exit.

# Game Exit
We need to cleanup any resources we allocate during execution (the `Label`) as Godot only [supports limited garbage collection](https://docs.godotengine.org/en/stable/tutorials/scripting/gdscript/gdscript_basics.html#memory-management).

See [Object Constructors and Destructors, Finalizers](#object-constructors-and-destructors-finalizers) for the implementation.

## Object Constructors and Destructors, Finalizers 
The virtual methods `_init()`, `_ready()`, and `_finalize()` [can be overridden](https://docs.godotengine.org/en/stable/tutorials/scripting/overridable_functions.html) to manage the object life cycle.

* `_init()` is a [_constructor_](https://en.wikipedia.org/wiki/Constructor_(object-oriented_programming)).
  This method is called when an object is instantiated in, for example, a call to `new()`. `_init()` is called before `_ready()`.
* `_ready()` is called after the object is added to the SceneTree and is ready to be used. This means that `_ready()` is only called on objects added to the SceneTree.
* `finalize()` is a [_destructor_](https://en.wikipedia.org/wiki/Destructor_(computer_programming)), called when the object is deleted following, for example, a call to `call_deferred("free")`.

Godot [supports limited garbage collection](https://docs.godotengine.org/en/stable/tutorials/scripting/gdscript/gdscript_basics.html#memory-management), for example resources explicitly allocated (e.g. using `new()`) [must be explicitly deleted using `free()`](https://docs.godotengine.org/en/stable/classes/class_object.html#class-object-method-free). Not doing so will result in [memory leaks](https://en.wikipedia.org/wiki/Memory_leak).

Our tutorial code shows this; we explicitly instantiated `Label` using `new()` requiring that we explicitly delete `Label` by calling `fps.call_deferred("free")` ([`call_deferred()`](https://docs.godotengine.org/en/stable/classes/class_object.html#class-object-method-call-deferred)) when we are done with it. We do this in the `_finalize()` method of our `Pong` class.

``` gdscript
func _finalize() -> void:
	fps.call_deferred("free") # Becasue we added the label, we also need to delete it
```

The `Pong` Node was added to the SceneTree in section [Create the Pong Project](#create-the-pong-project), so the creation and deletion of the `Pong` instance is managed by the SceneTree.

# Conclusion
All that to display the current frame rate. If we had jumped right into the implementation we would also have had to consider loading assets, collision detection, implementing physics for ball strikes and bounces, sound effects, and player point scores for game start and game over. Never mind some rudimentary AI if we want to support single player mode against a computer opponent.

We will need to do all this anyway, but trying to implement all of that, while learning GDScript, while learning how the Godot engine works is a lot to bite off.

See [The Final Code](#the-final-code) for the implementation.

## The Final Code

`res://src/Pong.gd`
``` gdscript
class_name Pong extends Node

const MAX_FPS = 0 # Zero means use the maximum frame rate
const UPDATE_INTERVAL = 10 # Update the frames per second every ten frames.
const FPS_TEXT = "FPS: " # Constant prefix displayed with the current fps, e.g. "FPS: 60"

const ACTION_UI_CANCEL = "ui_cancel"

var fps: Label = Label.new() # Create a new Label object

func _ready() -> void:
	Engine.set_max_fps(MAX_FPS)
	fps.set_position(Vector2(0,0)) # Set position
	self.call_deferred("add_child", fps) # Explicitly add the label to the scene

func _process(_delta: float) -> void:
	if Input.is_action_pressed(ACTION_UI_CANCEL): exitGame()
	if Engine.get_process_frames() % UPDATE_INTERVAL == 0:
		fps.set_text(FPS_TEXT + str (Engine.get_frames_per_second()))

func _finalize() -> void:
	fps.call_deferred("free") # Becasue we added the label, we also need to delete it

func exitGame() -> void:
	self.get_tree().root.propagate_notification(NOTIFICATION_WM_CLOSE_REQUEST)
	self.get_tree().quit()
```
# Advanced Concepts

* It is possible to [define your own lightweight Nodes](https://docs.godotengine.org/en/stable/tutorials/best_practices/node_alternatives.html). We mention this but an example is too advanced for this tutorial.

* The [Godot class Object](https://docs.godotengine.org/en/stable/classes/class_object.html#class-object)


<!-- -------- -->

<!-- I use Emacs as my preferred code editor and try to make minimal use of the Godot -->
<!-- Scene editor and Inspector. If you are unfamiliar with Emacs, then I recommend -->
<!-- using the Godot editor. -->

<!-- Learning how to engineer for reuse, readability, extensibility, -->
<!-- reliability, and performance is as important when developing your game as it is -->
<!-- when developing your back-end services that will need to scale to handle the -->
<!-- inrush of millions of players when your MMORPG suddenly goes viral. -->

<!-- As far as the Virtual methods such as `_ready()` and `_process()` are useful in situations -->
<!-- where the method of the subclass must be executed, not the method of the base -->
<!-- class. In the contrived example below, both Pong and Node2D implement -->
<!-- `_process()` and `SomeNodeMethod()`, where `_process()` is a virtual method and -->
<!-- `SomeNodeMethod()` is a non-virtual method. Because `Pong` is assigned to  -->

<!-- ``` gdscript -->
<!-- for n in Scene.nodes(): -->
<!--     var node: Node2D = n # The Pong object is assigned to a variable of type Node -->
<!--     node.SomeNodeMethod() # The SomeNodeMethod() of the base Node2D class is called  -->
<!--     node._process() -->
<!-- ``` -->
