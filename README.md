# Migration notes Godot 2 -> Godot 3

Notes for migrating Godot 2 code to Godot 3.

## Contributing

If you have found something yourself, please fork this repository, edit this file and place your notes at a fitting chapter. Then send a pull request. You can also use Github's own "Edit file"-feature. Thanks for contributing.

# engine.cfg -> project.godot

engine.cfg was renamed to project.godot (@cbscribe)

## _process, _input

_process, _input etc will be called by default if they are present in the script

## is_action_just_pressed

In some cases, particularly in _physics_process or _process, you may need is_action_just_pressed rather than is_action_pressed (and likewise for is_action_just_released). note: this is unverified/unexplored

## Enums

You can [export enums](https://github.com/godotengine/godot/pull/12952) now.

## Dictionaries

Dictionaries will be sorted by key insertion order when using a for loop to iterate over them. So if you load some JSON you will get keys in the same order as in the JSON file. (ref https://godotengine.org/qa/11570/will-gdscript-change-in-godot-3-0)

## Loops

for i in 10: print(i)   # like Cobra, yay!

## match statement
http://docs.godotengine.org/en/latest/learning/scripting/gdscript/gdscript_basics.html#match

## Feature tags
Platform-specific [project settings via feature tags](https://twitter.com/reduzio/status/887337781642616832?lang=en).

## Named Physics layers

Physics layers, both 2D and 3D, can now be named in the settings

## Signals

Some signal names have been changed. They mostly moved from present tense to past tense:

* mouse_enter => mouse_entered
* mouse_exit => mouse_exited
* input_event => gui_input
* _on_ScaleTween_tween_complete => _on_ScaleTween_tween_completed

* PopupMenu: item_pressed => id_pressed
* Node: tree_exited => tree_exiting
  * tree_exited still exists, but refers to, when then node finally exited the tree. tree_exiting is emitted while the node is exiting the tree.
* AnimationPlayer: finished() => animation_finished(animation_name)
* AnimatedSprite: finished() => animation_finished()
* RigidBody2D: body_enter_shape(...) => body_shape_entered()

## Audio Engine

[Big changes in the audio engine](https://godotengine.org/article/godot-30-new-internals-progress-report-4)

* audio engine has named buses
* built-in effects can be assigned to each bus. to-do: example
* 3 audio nodes:
  * AudioPlayer, which is a generic player.
	* AudioSource, which works for 3D positional audio.
	* AudioSource2D, which is the same but for 2D.

## Import changes

[Overhauled import process](https://godotengine.org/article/godot-30-new-internals-progress-report-4)

* v3 can automatically import many types of assets, placed directly in the project, resulting in fewer steps for developers
* new import dock to change options, located next to "Scene" tab

## Images as StreamTextures

Images are now available as StreamTextures. ImageTextures only exist for compatibility reasons and scripting.

## Font extension

Bitmap font .fnt files now have the extension .font.

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
* name
* filename
* owner
* char

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

### extends -> is

Use `is` instead of `extends` when checking if object is of the specified type.
Use `extends` when inheriting a class (same as v2).
* v2: if x extends y:
* v3: if x is y:

### Boolean must be explicitly cast to integer

* v2: print(true + 1)
* v3: print(int(true) + 1)

### KinematicBody2D (@gururise)

* v2: move(Vector2)
* v3: move_and_slide(Vector2)

### RawArray -> PoolByteArray (@mhilbrunner)

RawArray is PoolByteArray now

### New Array types

* Vector2Array is now PoolVector2Array
* Vector3Array is now PoolVector3Array
* ColorArray is now PoolColorArray

### z => z_index

* [Parameter "z" is renamed to "z\_index" on Node2D](https://github.com/godotengine/godot/pull/15191) (@Jerome67000)

### Image & InputEvent -> Resource

InputEvent and Image are now Resource

### Matrix3 -> Basis

Matrix3 is renamed to Basis

### Matrix32 -> Transform2D

Matrix32 is renamed to Transform2D

### Patch9Frame -> NinePatchRect

Patch9Frame is renamed to NinePatchRect

### opacity -> modulate.a

Use `modulate.a` when referring to the opacity of a CanvasItem

### Dictionary.to\_json -> to\_json

`from_json` and `to_json` are now built-in (@GDScript)

### No more ERR\_WTF

ERR\_WTF is deprecated in v3

### Key constant changes

* Main enter/return (16777221): KEY\_RETURN is renamed to KEY\_ENTER
* Numpad enter (16777222): KEY\_ENTER is renamed to KEY\_KP\_ENTER 
* Old KEY\_KP\_ENTER constant (16777344) is deprecated

### Singleton changes

Short singleton names are deprecated: AS, PS, PS2D, SS, SS2D, TS, VS

### ColorRamp -> Gradient

v3 also removes all of the 's' functions (get_colors, get_offsets, set_colors, set_offsets) that operated on Array built-ins.

### Property changes

From: https://github.com/godotengine/godot/pull/15611#issue-287951575

* PROPERTY_USAGE_NOEDITOR is now PROPERTY_USAGE_STORAGE | PROPERTY_USAGE_NETWORK, without PROPERTY_USAGE_INTERNAL
* AnimationPlayer.playback/play became AnimationPlayer.current_animation, with a backward-compatibility fallback. I think I tested this part, but slight bugs are possible.
* Node.filename, Node.name and Node.owner might generate some GDScript errors about shadowing.
* ScrollContainer.h_scroll and ScrollContainer.v_scroll are now scroll_horizontal_enabled and scroll_vertical_enabled.
* CanvasLayer.rotation and Polygon2D.texture_rotation were in degrees, while the new property is in radians. Likely to break a few projects.
* Used OS.window_borderless instead of OS.borderless_window, so we might add a group at a later point.
* Unbound TextEdit.set_max_chars, but added potential bindings for later on, since it wasn't used anywhere. Now I see there is an issue for set_wrap as well, might unbind it as well...
