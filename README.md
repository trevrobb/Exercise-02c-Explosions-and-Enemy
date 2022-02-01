# Exercise-02c-Explosions-and-Enemy

Exercise for MSCH-C220

Fork the repository. When that process has completed, make sure that the top of the repository reads `[your username]/Exercise-02c-Explosions-and-Enemy`. Edit the LICENSE and replace BL-MSCH-C220-S22 with your full name. Commit your changes.

Press the green "Code" button and select "Open in GitHub Desktop". Allow the browser to open (or install) GitHub Desktop. Once GitHub Desktop has loaded, you should see a window labeled "Clone a Repository" asking you for a Local Path on your computer where the project should be copied. Choose a location; make sure the Local Path ends with "Exercise-02c-Explosions-and-Enemy" and then press the "Clone" button. GitHub Desktop will now download a copy of the repository to the location you indicated.

Open Godot. In the Project Manager, tap the "Import" button. Tap "Browse" and navigate to the repository folder. Select the project.godot file and tap "Open".

You will now see where we left off in Exercise-02b: .

This is what you will need to accomplish as part of this exercise:

Asteroid Explosion:
  - Create a new Explosion_Asteroid scene. The root of the scene should be an AnimatedSprite node named Explosion_Asteroid. In the Inspector Panel->Frames, select new SpriteFrames, and then edit the new SpriteFrames. In the AnimationFrames section of the bottom panel, choose Add Frames from a Sprite Sheet. Select all 64 frames from res://Assets/Explosion_Asteroid.png (8 horizontal and 8 vertical). Set the speed to 100 FPS and make sure Loop is off. Back in the Inspector, set the Offset to (0,-30).
  - Add a script to the Explosion_Asteroid node. In the `_ready()` callback, play the "default" animation. Give it a random rotation. Give it a random scale (between 0.7 and 1.5) and a random speed_scale (between 0.8 and 2.0)
  - Add an `animation_finished` signal to the Explosion_Asteroid node. When the animation has completed, it should `queue_free()`
  - Save the scene as res://Effects/Explosion_Asteroid.tscn

Bullet Explosion:
  - Create a new Explosion_Bullet scene. The root of the scene should be an AnimatedSprite node named Explosion_Bullet. In the Inspector Panel->Frames, select new SpriteFrames, and then edit the new SpriteFrames. In the AnimationFrames section of the bottom panel, choose Add Frames from a Sprite Sheet. Select all 64 frames from res://Assets/Explosion_Bullet.png (8 horizontal and 8 vertical). Set the speed to 100 FPS and make sure Loop is off.
  - Add a script to the Explosion_Bullet node. In the `_ready()` callback, play the "default" animation
  - Add an `animation_finished` signal to the Explosion_Bullet node. When the animation has completed, it should `queue_free()`
  - Save the scene as res://Effects/Explosion_Bullet.tscn

Ship Explosion:
   - Create a new Explosion_Ship scene. The root of the scene should be an AnimatedSprite node named Explosion_Ship. In the Inspector Panel->Frames, select new SpriteFrames, and then edit the new SpriteFrames. In the AnimationFrames section of the bottom panel, choose Add Frames from a Sprite Sheet. Select all 64 frames from res://Assets/Explosion_Ship.png (8 horizontal and 8 vertical). Set the speed to 100 FPS and make sure Loop is off.
  - Add a script to the Explosion_Ship node. In the `_ready()` callback, play the "default" animation. Give it a random rotation.
  - Add an `animation_finished` signal to the Explosion node. When the animation has completed, it should `queue_free()`
  - Save the scene as res://Effects/Explosion_Ship.tscn

In res://Player/Bullet.gd
  - Create variables for Effects (which can be initialized to null) and Explosion. Load the res://Effects/Explosion_Bullet.tscn scene into the Explosion variable
  - Update the `_on_Area2d_Body_Entered(body)` callback to the following:
  ```
  func _on_Area2D_body_entered(body):
	if body.has_method("damage"):
		body.damage(damage)
	Effects = get_node_or_null("/root/Game/Effects")
	if Effects != null:
		var explosion = Explosion.instance()
		Effects.add_child(explosion)
		explosion.global_position = global_position
	queue_free()
  ```

in res://Player/Player.gd
  - Create a variable for Explosion_Ship. Load the res://Effects/Explosion_Ship.tscn scene into the Explosion_Ship variable
  - Create a variable health. Initialize health to 10
  - Create a new function called damage (it should accept a parameter d):
  ```
func damage(d):
	health -= d
	if health <= 0:
		Effects = get_node_or_null("/root/Game/Effects")
		if Effects != null:
			var explosion = Explosion_Ship.instance()
			Effects.add_child(explosion)
			explosion.global_position = global_position
			hide()
			yield(explosion, "animation_finished")
		queue_free()
  ```

Asteroid_Small:
  - Create a new scene (you will ultimately save it as res://Asteroid/Asteroid_Small.tscn). Make it identical to res://Asteroid/Asteroid.tscn, except use the res://Assets/Asteroid_Small.png texture for the sprite (with a corresponding CollisionPolygon2D)
  - Add a script to the Asteroid_Small KinematicBody2D node. The script should be as follows:
  ```
extends KinematicBody2D

var health = 1
var velocity = Vector2.ZERO

var Effects = null
onready var Explosion = load("res://Effects/Explosion.tscn")

func _physics_process(_delta):
	position += velocity
	position.x = wrapf(position.x,0,Global.VP.x)
	position.y = wrapf(position.y,0,Global.VP.y)

func damage(d):
	health -= d
	if health <= 0:
		Effects = get_node_or_null("/root/Game/Effects")
		if Effects != null:
			var explosion = Explosion.instance()
			Effects.add_child(explosion)
			explosion.global_position = global_position
		queue_free()
  ```
  - Save the scene as res://Asteroid/Asteroid_Small.tscn


Now, we should edit res://Asteroid/Asteroid.gd:
  - Add the following variables:
  ```
onready var Asteroid_small = load("res://Asteroid/Asteroid_small.tscn")
var small_asteroids = [Vector2(0, -30), Vector2(30,30), Vector2(-30,30)]
  ```
  - Now, add a new `damage(d)` function:
  ```
func damage(d):
	health -= d
	if health <= 0:
		collision_layer = 0
		var Asteroid_Container = get_node_or_null("/root/Game/Asteroid_Container")
		if Asteroid_Container != null:
			for s in small_asteroids:
				var asteroid_small = Asteroid_small.instance()
				var dir = randf()*2*PI
				var i = Vector2(0,randf()*small_speed).rotated(dir)
				Asteroid_Container.call_deferred("add_child", asteroid_small)
				asteroid_small.position = position + s.rotated(dir)
				asteroid_small.velocity = i
		queue_free()
  ```



Test it and make sure this is working correctly. 

Quit Godot. In GitHub desktop, you should now see the updated files listed in the left panel. In the bottom of that panel, type a Summary message (something like "Completes the mouse and keyboard exercise") and press the "Commit to master" button. On the right side of the top, black panel, you should see a button labeled "Push origin". Press that now.

If you return to and refresh your GitHub repository page, you should now see your updated files with the time when they were changed.

Now edit the README.md file. When you have finished editing, commit your changes, and then turn in the URL of the main repository page (`https://github.com/[username]/Exercise-Bullets-and-Asteroids`) on Canvas.

The final state of the file should be as follows (replacing my information with yours):
```
# Exercise-02c-Explosions-and-Enemy

Exercise for MSCH-C220

A user-controlled ship for a space-shooter game. Recently added the ability to shoot at asteroids. Created in Godot.

## Implementation

Created using [Godot 3.4.2](https://godotengine.org/download)

Assets are provided by [Kenney.nl](https://kenney.nl/assets/space-shooter-extension), provided under a [CC0 1.0 Public Domain License](https://creativecommons.org/publicdomain/zero/1.0/).

## References
None

## Future Development
None

## Created by
Jason Francis
```