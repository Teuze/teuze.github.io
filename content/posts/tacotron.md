+++
title = "Voice cloning with Tacotron2"
description = "Training a neural network to sound like drunken Emmanuel Macron"
date = 2026-03-02

[taxonomies]
tags = ["ai", "tts", "pytorch"]
+++

## Introduction

This story started four years ago after a stupid bet I made with myself:
I wanted to train a neural network to talk like Emmanuel Macron so that I could prank my friends and colleagues.

Spoiler alert, the experiment failed.
Still, the training process remains interesting, and the outcome although useless is pretty funny.

## 1. Data collection

In order to train a text-to-speech (TTS) model, you need text transcripts as input data and sound files as output data. 

I made a list of all President Macron's public announcements from the [Elysée Youtube channel][1] and downloaded them using `yt-dlp`.
I then ran an automated speech recognition (ASR) model called `whisper` against the results in order to get the transcripts.
This also allowed me to get timestamp data for each sentence, which I used to split the original audio into smaller clips.

All in all, it amounts to 5425 clips totalling 4 hours and 43 minutes of speech data:
that's about a fifth of the reference dataset called [LJSpeech][2].

<figure>
	<img src="/tacotron/distribution_ljspeech.png" alt="LJSpeech">
	<figcaption>The original LJSpeech clip lengths</figcaption>
</figure>

<br/>

<figure>
	<img src="/tacotron/distribution_manu_truncated.png" alt="Custom">
	<figcaption>The custom dataset clip lengths</figcaption>
</figure>

Lastly, I dumped the clip paths and transcripts into a pipe-delimited CSV table.


## 2. Model selection

I chose [Tacotron2][3] because it doesn't require any data preprocessing.
Basically, what it does is that it generates spectrograms,
which in turn feed a vocoder called [Waveglow][4] to output audio waveform files. 

The project did require a few adjustments to make it work on Ubuntu 24.04 :

 - Freeze project dependencies
 - Delete tensorflow references
 - Adjust librosa function calls
 - Make hyperparameters a dict

You may also need to adjust some training parameters: in my case, I had to add French accents to the character set.
You can find the updated project [here](https://github.com/Teuze/tacotron2/tree/deps/remove-tensorflow).

## 3. Training results

I trained the model for 32k steps, it took about 14 hours on an NVidia RTX 3060.

<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/@nikba/easy-carousel/dist/easy-carousel.min.css">
<script src="https://cdn.jsdelivr.net/npm/@nikba/easy-carousel/dist/easy-carousel.min.js"></script>

<figure>
	<img src="/tacotron/training_loss.png" alt="Loss">
	<figcaption>Training loss</figcaption>
</figure>

<br/>

<figure>
    <div class="alignments">
        <img src="/tacotron/alignment_0.png" style="width: 100%" alt="Alignment start">
        <img src="/tacotron/alignment_2k.png" style="width: 100%" alt="Alignment 2k">
        <img src="/tacotron/alignment_4k.png" style="width: 100%" alt="Alignment 4k">
        <img src="/tacotron/alignment_8k.png" style="width: 100%" alt="Alignment 8k">
        <img src="/tacotron/alignment_16k.png" style="width: 100%" alt="Alignment 16k">
        <img src="/tacotron/alignment_32k.png" style="width: 100%" alt="Alignment 32k">
    </div>
	<figcaption>Alignment evolution</figcaption>
</figure>

<br/>

<figure>
    <div class="predictions">
        <img src="/tacotron/prediction_0.png" alt="Prediction start">
        <img src="/tacotron/prediction_2k.png" alt="Prediction 2k">
        <img src="/tacotron/prediction_4k.png" alt="Prediction 4k">
        <img src="/tacotron/prediction_8k.png" alt="Prediction 8k">
        <img src="/tacotron/prediction_16k.png" alt="Prediction 16k">
        <img src="/tacotron/prediction_32k.png" alt="Prediction 32k">
    </div>
	<figcaption>Waveform prediction evolution</figcaption>
</figure>

<script>
    const carouselParams = {items: 1, loop: true, nav: true, dots: false}
    const alignments = new EasyCarousel('.alignments', carouselParams);
    const predictions = new EasyCarousel('.predictions', carouselParams);
</script>

Now that the model is trained, let's try to generate some sentences ! :grin:

Here is the output for "Mesdames et messieurs":

![spectrogram](/tacotron/result.png)

<audio controls src="/tacotron/result.wav"></audio>

What? :thinking:

The tone and the timbre are recognizable, but the content is not.<br/>
The model did not converge: could it be an alignment problem?

## Conclusion

It was fun but the results are a little bit disappointing.
Nowadays it's just not worth training your own TTS to clone a voice, because
some open models have quite convincing one-shot voice cloning abilities like [XTTS][5] and [Chatterbox][6].

Case in point : _C'est une bonne situation, ça, scribe ?_

<audio controls src="/tacotron/manu.wav"></audio>

<audio controls src="/tacotron/marine.wav"></audio>

<audio controls src="/tacotron/meluche.wav"></audio>

![otis](/tacotron/otis.jpg)


[1]: https://www.youtube.com/@ELYSEE/videos
[2]: https://keithito.com/LJ-Speech-Dataset/
[3]: https://github.com/NVIDIA/tacotron2
[4]: https://github.com/NVIDIA/WaveGlow
[5]: https://github.com/coqui-ai/TTS
[6]: https://github.com/resemble-ai/chatterbox
