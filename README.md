# LJ Speech Tools
Tools for creating voices for Webaverse.

## What do I need to make a dataset?
30-60 minutes of clean audio (no sounds, noises or other speakers). Can be one file or many. The rest will be done for you.

Mono 22khz WAV is ideal but the system will try to convert other formats.

# Installation
You will need Python 3.8 installed on your system.

To install:

```bash
sudo apt install ffmpeg
sudo apt install zip
pip install -r requirements.txt
```

# Pipeline (Easy Mode)
1. Put your files in the `put_audio_files_here` folder

2. Run the python script:

Open AI Whisper model will automatically download the first time used.  The larger the model the more time it takes to download.  The smaller the model the faster the inference.<BR>

More information can be found here: https://github.com/openai/whisper 
You will need to install ffmpeg for Whisper to work.  See instructions on Whisper page.

Commandline parameters are:<br>
```bash

    --provider      Set transcription provider (google or whisper) default is whisper
    --speech_key    Google Speech API Key
    --model         Whisper model (tiny, base, small, medium, large, large-v2, or large-v3) to use, default large-v3
    --help          Show help
```
```sh
python pipeline.py
```

The dataset.zip folder will be what you need to train a voice with.

# Training

A colab Notebook has been provided [here](https://colab.research.google.com/drive/1F1BMC18XIJNFvkncdZHZvxd5oEAVsHxY)

Upload your dataset to your Google drive, then press "run all cells" to run the notebook.

You will need a Google account, but Colab is free. However, upgrading to Colab Pro will make training a lot easier, as you have guaranteed access to faster hardware.

# Individual Tools
`pipeline.py` is made from a collection of other scripts, which you can run individually. Speaker Separation is not included in the pipeline-- it is assumed that you have cleaned, separated tracks, but you can run it in a preprocess if you need it.

## Speaker Separation
In our tests, separation usually only worked in the case where two or three speakers with distinctly different voices were speaking.

To remove any wav files which contain audio which isn't the source speaker

First, place some example audio from your speaker in the 'target' folder.

Then, place any example audio from speakers who are *not* your speaker in the 'ignore' folder

Then run the separate.py with a --threshold, probably somewhere between 0.6 and 0.9

```bash
bash separate.sh
# or
python separator.py --threshold=0.65
```

## Audio Transcription

To transcribe audio files in the wavs folder.  Edit transciber.py to modify settings for whisper or to use googl speech api.

```bash
bash transcribe.sh
# or
python transcriber.py
```

Transcription will create an LJSpeech compatible 'metadata.csv'

Transcription removes swearing and replaces with ****. If you want some swearing back, you can run `python swearing.py` -- if you don't want swearing in your dataset you should remove that data entirely, as the asteriks will negatively affect alignment.

## Get length of audio dataset
```bash
python count_length.py
```

Will give you the total length, longest and shortest file lengths from the wavs folder

## Split long audio into shorter audio samples
```bash
python audiosplitter.py
```
Most training scripts prefer a variety of audio sample lengths from 2-12 seconds in length. The splitter will try to find silent points and break the audio into chunks up to 12 seconds. You can modify the script to your flavor, just change the 12 to a 10 or whatever you want.

## Prepare dataset
```bash
python make_dataset.py
```

This will create a train_filelist.txt, val_filelist.txt and dataset.zip which can be uploaded to the SortAnon TalkNet training colab notebook here: https://github.com/bycloudai/TalkNET-colab
You may need to reformat the name of the files if you use other notebooks.


## Complete Pipeline
If you are trying to process a voice in a noisy track, you should really bring the audio files into an audio software, mute any noise or speech from other speakers, and normalize and compress the remaining audio so it all sounds as similar as possible.

Assuming you wanted to record your own voice and didn't need to deal with speaker separation or isolation, here are the steps you would talk to do that.

1. Put all of your files in the wavs folder
If they are not wavs, convert them using ffmpeg - the default we are targeting is Mono WAV 22050 Hz

2. If your files are longer than 12 seconds and not hand-split, run the audiosplitter
3. 
```bash
python audiosplitter.py
```
Verify that the data is good, then delete the contents of the wavs folder and move the data_outputs there

3. Transcribe your dataset, if you want to use whisper, it will download the whisper_tts large-v3 model.  
If you wish to use another model modify the python file.
If you want to use google speech api you will need to modify the pythong file and set a key or use the pipeline
4. 
```bash
bash transcribe.sh
# or
python transcriber.py
```
This will create a metadata.csv, which is the standard format of LJSpeech -- for most training needs, the metadata.csv and wavs folder is all you need as input.

### Good Luck!

And thanks to all the hardworking Ponies who took the time to document this. The compendium of knowledge created by the Pony Preservation Project was instrumental in giving these tools shape and form.
