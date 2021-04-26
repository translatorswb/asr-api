# ASR-API
API for serving automatic speech recognition (ASR) models. 

## Loading models

### Where to get ASR models?

ASR API is only able to load Kaldi-based VOSK models currently. You can find open-source models for various languages at [Alpha Cepei's website](https://alphacephei.com/vosk/models). 

### How to integrate models?

1) Place the model you want into `models` directory
2) Specify it in the configuration file. 

### Config file

`config.json` contains all the information to load the models. `languages` dictionary is used for mapping language codes to language names. `models` is the list of available models in the system. A simple model specification looks like this:

```
{
    "lang": "en",
    "model_type": "vosk",
    "model_path": "vosk-model-small-en-us-0.15"
    "load": true
}
```

`model_path` refers to the directory under `models` where model data is stored. 

By default, models are given a unique id associated with the language code. If you would like to have alternative models for a language use `alt` field in configuration specification. For example:

```
{
    "lang": "en",
    "model_type": "vosk",
    "model_path": "my-special-english-model",
    "alt": "sp",
    "load": true
}
```

## Build and run

### Local running for testing

Set the environment variables:
```
MT_API_CONFIG=config.json
MODELS_ROOT=models
```
Install required libraries:
```
pip install -r requirements.txt
```

Run with unicorn:
```
uvicorn app.main:app --reload --port 8005
```

`run_local.sh` script can be called to run quickly once requirements are installed.

### Run it as a docker-container

``` 
docker-compose build
docker-compose up
```

## Sample requests and responses

### Transcription request

Transcription requests take in a mono PCM WAVE format file.

#### cURL
```
curl -L -X POST 'http://localhost:8005/transcribe' -F 'file=@"my_audio.wav"' -F 'lang="en"'
```

### Transcription response

```
{
    "results": [
        {
            "result": [
                {
                    "conf": 1.0,
                    "end": 1.14,
                    "start": 0.51,
                    "word": "hello"
                }
            ],
            "text": "hello"
        }
    ],
    "transcript": "hello"
}
```

### Retrieve languages

Retrieves languages supported by the API.

#### cURL

```
curl -L -X GET 'http://localhost:8005/transcribe/languages'
```

### Retrieve languages response

```
{
    "models": [
        "en"
    ],
    "languages": {
        "en": "English"
    }
}
```

