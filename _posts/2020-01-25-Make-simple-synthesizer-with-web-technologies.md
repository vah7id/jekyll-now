---
layout: post
title: Make your first simple synthesizer with web technologies
---

### Introduction

Since I have started working on music production, audio web API became interesting for me to start experimenting the new ideas. Synthesizers allows musicians to create sounds never heard before and there are bunch of different synthesizers in the market for musicians and they're quite expensive as well. The idea that I'm working on these days is make the basic synthesizers with the web API's to start change the whole market issues and make the producing easier for the musicians.

I started with most basic synthesizers that I can make with the current audio web API and then I can make the variety of synthesizers out of this project in future. Before getting started I suggest you to have a look at ***Web Audio API*** references on MDN.

### Getting Started

Let's start by creating a basic HTML page.

```
<!doctype html>
<html>
    <head>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width">
        <title>Synthesizer</title>
        <link rel="stylesheet" href="styles/app.css">
    </head>
    <body>
        <div class="root">
            <h1>Synthesizer!</h1>
        </div>
    </body>
</html>
```

Perhaps the most important thing your synth needs is a keyboard. Luckily, I've created a little snippet of JavaScript that will add a virtual keyboard to your page. Import the ***Qwerty Hancock*** library which is an interactive HTML plugin-free keyboard for your web audio projects.

Then add an empty div to your DOM with an id of "keyboard".

```
<div id="keyboard"></div>
```

We'll also want to set up a dedicated JavaScript file for our synth, so let's create that too and import the Qwerty Hancock before that.

Within synth.js we'll initialise our keyboard by doing the following.

```
const keyboard = new QwertyHancock({
     id: 'keyboard',
     width: 600,
     height: 150,
     octaves: 2
});
```

Qwerty Hancock provides us with two event listeners, keyUp and keyDown. These allow us to hook into the keyboard and write code that fires when the keyboard is pressed. It also tells us which note was pressed, and its corresponding frequency in ***hertz***. 

```
keyboard.keyDown = (note, frequency) => {
    console.log('Note', note, 'has been pressed');
    console.log('Its frequency is', frequency);
};
 
keyboard.keyUp = (note, frequency) => {
    console.log('Note', note, 'has been released');
    console.log('Its frequency was', frequency);
};
```

### Oscillator

An electronic oscillator is an electronic circuit that produces a periodic, oscillating electronic signal, often a sine wave or a square wave. Oscillators convert direct current from a power supply to an alternating current signal.

Let's start an oscillator when a key is pressed. We'll stop it after one second so it doesn't go on forever. Now when we press a key, we hear a sound. It's a bit loud, so let's create a gainNode to act as master volume control.

```
const context = new AudioContext(),
    masterVolume = context.createGain();
 
masterVolume.gain.value = 0.3;
masterVolume.connect(context.destination);
 
keyboard.keyDown = (note, frequency) => {
    const osc = context.createOscillator();
 
    osc.connect(masterVolume);
    masterVolume.connect(context.destination);
 
    osc.start(context.currentTime);
    osc.stop(context.currentTime + 1);
};
```

A keyboard that plays one single note over and over isn't very fun, so let's plug in the frequency to the oscillator before we start it playing.

```
osc.frequency.value = frequency;
```

Lovely. We now need to stop the oscillator playing after we lift a key rather than after a second. Because we're creating the oscillator inside the keyDown function, we need to keep track of which oscillator is playing which frequency in order to stop it when the key is released.

```
const oscillators = {};
 
keyboard.keyDown = function (note, frequency) {
    // Previous code here
 
    oscillators[frequency] = osc;
 
    osc.start(context.currentTime);
};
```

This means we can easily use the frequency we get from our noteUp function to stop that specific oscillator.

```
keyboard.keyUp = function (note, frequency) {
    oscillators[frequency].stop(context.currentTime);
};
```

We now have a fully working (very basic) synthesizer in the browser! Ok, it doesn't sound great at the moment, but let's see if we can change that.

The first thing to do is change the type of wave the oscillator outputs. There are four basic types to choose from: sine, square, triangle and sawtooth. Each different shape of wave sounds different. Play about with them and choose your favourite.

```
osc.type = 'sawtooth';
```

It's very rare you'll find a synthesizer that uses a single oscillator. Most synths beef up their sound by combining different oscillators of different types.
We can add a bit of chorus to add more shimmer to our sound, by detuning the oscillators slightly.

```
osc.detune.value = -10;
osc2.detune.value = 10;
```

Lovely, that was the basic synthesizer that I wanted to make and in the near future I'll come up with the different variety of opensource synthesizer on the web enviorment.


