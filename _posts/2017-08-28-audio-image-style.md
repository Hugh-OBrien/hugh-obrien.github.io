---
layout: post
title:  "Style Transfer Between Sound and Images"
date:   2017-08-28
categories: blog
head_image: "/assets/images/ok_computer_small.jpg"
---

<script>
function showImage(button) {
    var img = document.getElementById('tate-gif');
    console.log(img.style.display)
    if (img.style.display == ''){
        img.style.display = 'none';
        button.innerHTML = 'Show gif';
    } else {
        img.style.display = '';
        button.innerHTML = 'Hide gif';
    }
}
</script>

The idea for this post came from two interesting programmatic transformations of images and sound:

- [Here](https://dmitryulyanov.github.io/audio-texture-synthesis-and-style-transfer/) Dmitry Ulyanov describes using the popular style transfer methods using neural networks for audio, rather than for images. The combination of two sound clips was interesting and provided some curious results.

- [Here](https://github.com/robertfoss/audio_shop) Robert Foss shows off a script that transforms images to sound and applies common sound effect transformations on them, before converting them back into images, with interesting results.

For the latter here's a gif I made of the Tate Modern with the pitch of the intermediate sound messed with to produce a surreal video effect...

<img id="tate-gif" src="/assets/images/tate_pitch.gif">
<button onclick="javascript:showImage(this)">Hide gif</button>

The goal here was to see if the core of these two could be combined into something interesting. We've seen that we can take the style from a piece of music and apply it to another sound. We've also seen that we can transform images into sound data and mess with them. So why not use the intermediate sound from raw image data as one of our style transfer samples?

Initially my thought was that what we'd end up with sound wise would be just noise and we could transfer that into an image at the end. This turned out to be wishful thinking. Perhaps with the right tuning it would be possible to end up with an interesting image; however the basic concept doesn't bring us anywhere close. The sound leftover after the style transfer (or whatever you want to call it) to me sounds pretty interesting. While describing this process to a friend they quite aptly described it as, "Music mixed with chaos".

## Chaos Music

**Radiohead - OK Computer - Karma Police**

What better an example to look at than Radiohead's OK Computer...

Let's take the album cover and make it sound:

![](/assets/sounds/radiohead/ok_computer.jpg)

becomes... (beware it gets pretty loud)

<audio controls>
  <source src="/assets/sounds/radiohead/cover.mp3" type="audio/mpeg">
</audio>

Now lets run that through some style transfer with some clips from a song from the album - Karma Police
<audio controls>
  <source src="/assets/sounds/radiohead/quiet.mp3" type="audio/mpeg">
</audio>

becomes

<audio controls>
  <source src="/assets/sounds/radiohead/kp_quiet_transferred.mp3" type="audio/mpeg">
</audio>


Taking a louder bit of the song...

<audio controls>
  <source src="/assets/sounds/radiohead/loud.mp3" type="audio/mpeg">
</audio>

becomes

<audio controls>
  <source src="/assets/sounds/radiohead/kp_loud_transferred.mp3" type="audio/mpeg">
</audio>

Convering the sound back to images doesn't give anything worth looking at really, it ends up pretty much as noise with some slightly visible features:

![](/assets/sounds/radiohead/kp_loud_transferred.png)

Training this took quite a while as I was doing this while travelling and without access to a GPU. For that reason I started going with smaller source images...


<br/>

**Broken Social Scene - Broken Social Scene -  Fire eye'd boy**



![](/assets/sounds/broken/broken.jpg)


Image as sound:
<audio controls>
  <source src="/assets/sounds/broken/broken_social_scene_style.mp3" type="audio/mpeg">
</audio>

Song sample:
<audio controls>
  <source src="/assets/sounds/broken/fire_start.mp3" type="audio/mpeg">
</audio>

Result:
<audio controls>
  <source src="/assets/sounds/broken/fire_eyed_start_transferred.mp3" type="audio/mpeg">
</audio>


<br/>

**Feist - The Reminder-  I feel it all**

For something more chill

![](/assets/sounds/feist/thereminder.jpg)


Image as sound:
<audio controls>
  <source src="/assets/sounds/feist/readable_sound.mp3" type="audio/mpeg">
</audio>

Song sample:
<audio controls>
  <source src="/assets/sounds/feist/feel.mp3" type="audio/mpeg">
</audio>

Result:
<audio controls>
  <source src="/assets/sounds/feist/transferred.mp3" type="audio/mpeg">
</audio>


<br/>

**Muse - Origin of Symmetry - Plug in Baby**

For a reasonably isolated riff...

![](/assets/sounds/muse/origin.jpg)


Image as sound:
<audio controls>
  <source src="/assets/sounds/muse/origin_sound.mp3" type="audio/mpeg">
</audio>

Song sample:
<audio controls>
  <source src="/assets/sounds/muse/plug_start.mp3" type="audio/mpeg">
</audio>

Result:
<audio controls>
  <source src="/assets/sounds/muse/plug_start_transferred.mp3" type="audio/mpeg">
</audio>



<br/>

I find these resulting sounds interesting and surprisingly cool to listen to... however I don't have another suggested usecase. Some things are just interesting by themselves sometimes...

Code lives here (sans data):

[https://github.com/Hugh-OBrien/chaos_music](https://github.com/Hugh-OBrien/chaos_music)