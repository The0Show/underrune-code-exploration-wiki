# Room of Dog

The Room of Dog is an error handler in Undertale, which appears if something goes wrong.

You can learn more about this room on [The Cutting Room Floor](https://tcrf.net/Undertale#Dog\_Error\_Screens).

The most common variant of this room is the dancing dog variant, as shown below.

{% embed url="https://youtu.be/0_4-WC6Mf1A?t=18" %}
Footage by [vee](https://www.youtube.com/channel/UCNiA2v0dBQ1tYdEcwL99ZqQ)
{% endembed %}

There is a more rare variant of the screen, The Sigh of Dog. This variant contains a calmer song, as well as a different animation for the dog. The screen is shown below.

{% embed url="https://youtu.be/JP8oDUu62xM?t=18" %}
Footage by [vee](https://www.youtube.com/channel/UCNiA2v0dBQ1tYdEcwL99ZqQ)
{% endembed %}

After seeing these two screens for myself, both in-game and through these videos, I wondered how rare the second variant was.

### The differences and rarity

After decompiling my data.win file, I was able to locate the script that controls this, `gml_Object_obj_roomofdog_Alarm_0`.

{% code title="gml_Object_obj_roomofdog_Alarm_0" %}
```gml
caster_free(-3)
visible = true
type = floor(random(8))
image_xscale = 2
image_yscale = 2
if (type == 7)
{
    x = (room_width / 2)
    y = (room_height / 2)
    sprite_index = spr_tobdog_sleep_trash
    thissong = caster_load("music/sigh_of_dog.ogg")
    caster_loop(thissong, 1, (0.8 + random(0.2)))
    image_speed = 0.05
}
else
{
    x = ((room_width / 2) - (sprite_width / 2))
    y = ((room_height / 2) - (sprite_height / 2))
    thissong = caster_load("music/dance_of_dog.ogg")
    caster_loop(thissong, 1, (0.95 + random(0.1)))
    image_speed = 0.15
}
```
{% endcode %}

The fate of the Annoying Dog on a blank screen is the output of a random number generator. Cool!

Let's explore what each variant does, line-by-line.

#### Dance of Dog

If the output of the random number generator (defined in the code as `type`) is not equal to 7, then this variant is displayed.

```gml
x = ((room_width / 2) - (sprite_width / 2))
y = ((room_height / 2) - (sprite_height / 2))
```

At the start, we update the position of the dog sprite. In this case, we divide the room width and height by 2, divide the sprite width and height by 2, and then subtract them to get the final x and y values.

$$
x = (a \div 2) - (c \div 2) \newline y = (b \div 2) - (d \div 2)
$$

After, we load the song, loop it with a random pitch, and then set the image speed to `0.15`.

```gml
thissong = caster_load("music/dance_of_dog.ogg")
caster_loop(thissong, 1, (0.95 + random(0.1)))
image_speed = 0.15
```

#### Sigh of Dog

If the output of the random number generator (defined in the code as `type`) is equal to 7, then this variant is displayed.

```gml
x = (room_width / 2)
y = (room_height / 2)
```

At the start, we update the position of the dog sprite. In this case, we just divide the room width and height by 2 to get the final x and y values.

$$
x = (a \div 2) \newline y = (b \div 2)
$$

After, we update the sprite index to be a sleeping dog instead of a dancing dog.

```gml
sprite_index = spr_tobdog_sleep_trash
```

Finally, we load the song, loop it with a random pitch, and then set the image speed to `0.05`.

```gml
thissong = caster_load("music/sigh_of_dog.ogg")
caster_loop(thissong, 1, (0.8 + random(0.2)))
image_speed = 0.05
```

### Making only a specific variant appear

Now, what if we only wanted a specific variant to appear? Since we understand how the two variants are chosen, let's try modifying the code. I'll show off two of the ways I thought about doing this, as well as a bonus third way.

#### Approach 1

One way you could do this is by just removing the other variant from the code. In this example, I removed the dancing variant.

{% hint style="info" %}
Comment out unwanted lines instead of fully deleting them (like I did), it will make not only switching variants, but resetting it a lot easier.
{% endhint %}

```gml
caster_free(-3)
visible = true
image_xscale = 2
image_yscale = 2
x = (room_width / 2)
y = (room_height / 2)
sprite_index = spr_tobdog_sleep_trash
thissong = caster_load("music/sigh_of_dog.ogg")
caster_loop(thissong, 1, (0.8 + random(0.2)))
image_speed = 0.05
```

Here's the result:

![Only the Sigh variant appears.](.gitbook/assets/7Noth88s7g.gif)

#### Approach 2

You could also just override the random number. On line 3, where the `type` variable is declared, replace `floor(random(8))` with 7 for the calm variant, or any other number for the dancing variant. You can also replace the values in the if statement to make it easier, like how I changed it to 1 instead of 7. This example only shows the dancing variant.

```gml
caster_free(-3)
visible = true
type = 0
image_xscale = 2
image_yscale = 2
if (type == 1)
{
    x = (room_width / 2)
    y = (room_height / 2)
    sprite_index = spr_tobdog_sleep_trash
    thissong = caster_load("music/sigh_of_dog.ogg")
    caster_loop(thissong, 1, (0.8 + random(0.2)))
    image_speed = 0.05
}
else
{
    x = ((room_width / 2) - (sprite_width / 2))
    y = ((room_height / 2) - (sprite_height / 2))
    thissong = caster_load("music/dance_of_dog.ogg")
    caster_loop(thissong, 1, (0.95 + random(0.1)))
    image_speed = 0.15
}
```

The result is the same as Approach 1, but instead only the dancing variant appears.

#### Bonus Approach 3

If you don't want a specific variant, but just want the calm variant to be more common, you can easily do that by changing the random number generator. In the below example, the random number generator was changed to generate a number between 0 and 1, turning it into a 50% chance for both rooms.

{% hint style="warning" %}
Remember that in GameMaker Language, the number inserted into the `random` function is the **upper range**, and the random number will be from zero up to the provided number **minus one**. [Read the GameMaker Language documentation for more info.](https://manual.yoyogames.com/GameMaker\_Language/GML\_Reference/Maths\_And\_Numbers/Number\_Functions/random.htm)
{% endhint %}

{% hint style="warning" %}
Also, remember to change the `if` statement accordingly!
{% endhint %}

```gml
caster_free(-3)
visible = true
type = floor(random(2))
image_xscale = 2
image_yscale = 2
if (type == 1)
{
    x = (room_width / 2)
    y = (room_height / 2)
    sprite_index = spr_tobdog_sleep_trash
    thissong = caster_load("music/sigh_of_dog.ogg")
    caster_loop(thissong, 1, (0.8 + random(0.2)))
    image_speed = 0.05
}
else
{
    x = ((room_width / 2) - (sprite_width / 2))
    y = ((room_height / 2) - (sprite_height / 2))
    thissong = caster_load("music/dance_of_dog.ogg")
    caster_loop(thissong, 1, (0.95 + random(0.1)))
    image_speed = 0.15
}
```

Here's the result:



### Conclusion

We discovered that the Sigh variant of `room_of_dog` appears when a random number generator outputs `7`, making it a 12.5% chance. We also explored methods of modifying the code to only display a specific variant, and modifying the rarity of the Sigh variant.
