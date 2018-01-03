# Migration notes Godot 2 -> Godot 3

Notes for migrating Godot 2 code to Godot 3.

## Contributing

If you have found something yourself, please fork this repository, edit this file and place your notes at a fitting chapter. Then send a pull request. Thanks for contributing.

# engine.cfg -> project.godot

engine.cfg was renamed to project.godot (@cbscribe)

## _process, _input

_process, _input etc will be called by default if they are present in the script

## is_action_just_pressed

In some cases, particularly in _physics_process or _process, you may need is_action_just_pressed rather than is_action_pressed (and likewise for is_action_just_released). note: this is unverified/unexplored

### Enums

You can [export enums](https://github.com/godotengine/godot/pull/12952) now.

### Dictionaries

Dictionaries will be sorted by key insertion order when using a for loop to iterate over them. So if you load some JSON you will get keys in the same order as in the JSON file. (ref https://godotengine.org/qa/11570/will-gdscript-change-in-godot-3-0)

### Loops

for i in 10: print(i)   # like Cobra, yay!

### match statement
http://docs.godotengine.org/en/latest/learning/scripting/gdscript/gdscript_basics.html#match

### Feature tags
Platform-specific [project settings via feature tags](https://twitter.com/reduzio/status/887337781642616832?lang=en).

### Named Physics layers

Physics layers, both 2D and 3D, can now be named in the settings

### Signals

Some signal names have been changed:

* mouse_enter => mouse_entered
* mouse_exit => mouse_exited
* input_event => gui_input
* _on_ScaleTween_tween_complete => _on_ScaleTween_tween_completed

### Audio Engine

[Big changes in the audio engine](https://godotengine.org/article/godot-30-new-internals-progress-report-4)

* audio engine has named buses
* built-in effects can be assigned to each bus. to-do: example
* 3 audio nodes:
  * AudioPlayer, which is a generic player.
	* AudioSource, which works for 3D positional audio.
	* AudioSource2D, which is the same but for 2D.

### Import changes

[Overhauled import process](https://godotengine.org/article/godot-30-new-internals-progress-report-4)

* v3 can automatically import many types of assets, placed directly in the project, resulting in fewer steps for developers
* new import dock to change options, located next to "Scene" tab

### Images as StreamTextures

Images are now available as StreamTextures. ImageTextures only exist for compatibility reasons and scripting.

## API changes

### Forbidden variable names (@puppetmaster-)

Most commonly used variable designations that are now internal used in Godot 3
* Node2D
* material
* scale
* position
* transform
* z (open pull request)
* texture
* offset
* frame
* color
* energy
* mode
* Spatial
* scale
* rotation
* transform
* size
* text

### Direct accessors

Getters and Setters are used directly now using property modification, so i.e. get_position/set_position should be used as position now. (@NullConstant)

Additionally, subproperties can be directly accessed and modified now. So position.x is directly changeable and doesn't need to be set using a helper variable. (@mrcdk)


### _fixed_process -> _physics_process:

* v2: func _fixed_process(delta):
* v3: func _physics_process(delta):

### Position

* v2: set_pos(get_pos().x + 10, get_pos().y)
* v3: position.x += 10

### Node syntax

* v2: get_node('fooBar')  # still works in v3, but I'll only use with a variable arg rather than literal
* v3:
  * $fooBar
  * $/root/baz/buzz (use quotes around the expression, if path contains a whitespace) (@cbscribe)
  * $SomeLabel.text = '...'

### position, rotation

* v2: pos, rot, etc.
* v3: position, rotation, etc.

### Globals => ProjectSettings

* v2: Globals.get('foo')
* v3: ProjectSettings.get('foo')

### set_network_mode => set_network_master

* v2: node.set_network_mode(MASTER|SLAVE)
* v3: node.set_network_master(PEER_ID)

### viewport.get_rect not available anymore

Use get_visible_rect instead

## KinematicBody2D (@gururise)

* v2: move(Vector2)
* v3: move_and_slide(Vector2)

## RawArray -> PoolByteArray (@mhilbrunner)

RawArray is PoolByteArray now

## New Array types

* Vector2Array is now PoolVector2Array
* Vector3Array is now PoolVector3Array
* ColorArray is now PoolColorArray

## z => z_index

* [Parameter "z" is renamed to "z_index" on Node2D](https://github.com/godotengine/godot/pull/15191) (@Jerome67000)
