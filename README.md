# Fireworks!

A relatively simple and fun Predigame example that highlights image sprites, callbacks, and sound effects.

## Prerequisites
You'll need to have the Predigame platform installed, a trusty text editor handy, and the command prompt (or terminal) open to complete these examples. Visit [http://predigame.io](http://predigame.io) for installation instructions.

## Download Existing Game (Optional)
We have a version of this game already done and ready to go. Just run the following command:

```
pred pull fireworks
```

Then read the rest of the tutorial and follow along.

## Getting Started
To get things started, we're going to create a new Predigame game. This can be done by typing the following the command in the terminal:

```
pred new fireworks
```

Now in the text editor, find and open the file `fireworks/game.py`. Now let's get started!

For the code, we'll start by defining our screen dimensions (30 x 20 grid cells) and window title:

```python
WIDTH = 30
HEIGHT = 20
TITLE = 'Fireworks!'
```
Since fireworks normally launch in the evening, we'll paint our background black.

```
# make the background black
BACKGROUND = (0,0,0)
```

This defines the background using a RGB (RED, GREEN, BLUE) tuple. We also have some constants defined for common colors, so setting the line this way would have the same effect:

```
# make the background black
BACKGROUND = BLACK
```

For your game, you may wish to use a background image instead. That's possible provided that the image is stored in the `backgrounds/` directory. Then the code would look something like this (*NOTE that we don't use file extensions for images, backgrounds, and sounds*).

```python
# use a background image
BACKGROUND = 'castle_at_night'
```
The core of the game consists of two callbacks. First is a one to launch the firework and the second to create the explosion effect. Let's cover launching first:

```python
def launch():
        # play a sound
        sound('fireworks')

        # create a random position with some padding to keep away from edges
        p = rand_pos(x_padding=5, y_padding=10)

        # create a really small yellow circle
        # position circle at point p's x coordinate but under the viewable area
        s = shape(CIRCLE, YELLOW, (p[0], WIDTH-5), size=0.1)

        # move the sprite to the designated area
        # callback (destroy) will be invoked when sprite arrives at destination
        s.speed(15).move_to(p, callback=s.destroy)

        # schedule to have the launch function called again..
        # some random time between 0 and 3 seconds
        callback(launch, rand(0, 3))

# start show in 1 second!
callback(launch, 1)
```
Now let's give it a test run!

```
pred game.py
```

You'll notice that fireworks launch and sound effects are playing, but there is *no explosion*! That's due to this line:
```python
        s.speed(15).move_to(p, callback=s.destroy)
```
Once the yellow circle sprite moves to point `p` the callback `s.destroy` is invoked. As the name implies, that simply destroys the sprite.

Now let's handle the explosion. We'll define a function to handle that effect:

```python
def explode(s):
        s.destroy()
        # pick a random number between 1 and 5
        num = randint(1, 5)
        # load the fireworks image sprite
        img = image('fireworks-'+str(num), s.pos, size=0.5)
        # make the image pulse (get big), destroy between 0.1 and 4 seconds
        img.pulse(time=4, size=10).destruct(rand(0.1,4))
```
In our example, we currently have five fireworks images in our `images/` directory:
```
fireworks-1.png fireworks-2.png fireworks-3.png fireworks-4.png fireworks-5.png
```
Go ahead a find some fireworks of your own. Transparent images always work best. You'll want to label them similar to how we did so it's easy to randomly pick one of the images. The `randint(1,5)` line simply picks a number between 1 and 5 at random. The image will initially render at roughly sized at one half a grid cell. The image will slowly pulse with a four second interval. Note that the image will `destruct` between 0.1 and 4 seconds, so this basically means the firework will expand and never contract.

With the explosion defined, we'll now want to go back and reset the yellow sprite callback to use our new `explode` function:
```python
        s.speed(15).move_to(p, callback=partial(explode, s))
```

Here's the completed version (without comments):

```python
WIDTH = 30
HEIGHT = 20
TITLE = 'Fireworks!'

BACKGROUND = (0,0,0)

def explode(s):
        s.destroy()
        num = randint(1, 5)
        img = image('fireworks-'+str(num), s.pos, size=0.5)
        img.pulse(time=4, size=10).destruct(rand(0.1,4))

def launch():
        sound('fireworks')
        p = rand_pos(x_padding=5, y_padding=10)
        s = shape(CIRCLE, YELLOW, (p[0], WIDTH-5), size=0.1)
        s.speed(15).move_to(p, callback=partial(explode, s))
        callback(launch, rand(0, 3))

callback(launch, 1)
```

Save your changes and enjoy the show!
```
pred game.py
```
