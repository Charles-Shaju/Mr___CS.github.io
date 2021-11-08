---
layout: post
title: "Voice Assistant using Python and Tensorflow"
subtitle: "How to build a Intelligent Voice Assistant in Python"
background: '/img/posts/mlvoice.jpg'
---

# Intelligent Voice Assistant using Python and Tensorflow

## Introduction

  This is a simple guide for coding a **Voice Assistant in Python**. The code is pure python and I this you need a basic knowledge in python. Using this assistant you can automate your daily task and interact with the system by voice. It is just like Siri and OK google.

The prerequisite for this project are:
 - Python should be installed on your device
 - Tensorflow
 - Any code-editor

>The program records the audio of the user using microphone and convert it into text.Then the text is inserted into the model and provide the required output
#### Python
[How to install python](https://medium.com/co-learning-lounge/how-to-download-install-python-on-windows-2021-44a707994013)

The latest version of python is recomended!, but the new version of python3.10 have some issues with tensorflow so, install the previous version [python3.9](https://www.python.org/downloads/release/python-397/)

#### Tensorflow
[How to install tensorflow](https://www.tensorflow.org/install)

#### Code-Editors
[Pycharm](https://www.jetbrains.com/pycharm/download/)(specificaly for python)
[Visual Studio Code](https://code.visualstudio.com/download)(recommended).For Vs code you should install  the python extension.
etc.....
Anyone as you like.

###Working
 The program will input voice command from the user and return the output.The voice input given by the user is gone through a machine learning algorithm and convert it into text. The converted text is then cross-verified with the [intents.json](https://github.com/Charles-Shaju/Intelligent-Voice-Assistant/blob/master/Voice-Assistant/intents.json) file(will discuss soon).If the input approximately matches the intents in the JSON file, then the specific output is produced. There are two files needed for this program- one is the python file that specifices what output should be provided and second, a JSON file to recognise the input.


 ###### For those who are only interested in running the program, you can download the program file from my [github page](https://github.com/Charles-Shaju/Intelligent-Voice-Assistant.git)
 - open up the terminal in your editor and type
  `git clone https://github.com/Charles-Shaju/Intelligent-Voice-Assistant.git`
 - inorder to run the program you need to install some python packages

 `pip install email  pyjokes pywhatkit.main speech_recognition  pyttsx3 neuralintents sys datetime email.message smtplib webbrowser time requests pywhatkit wolframalpha wikipedia bs4`

## Detailed Explaination

 ##### intent.json
 This is a JavaScript Object Notation file that contain different intents.Each intents contain a tag, pattern and a response. The tag is used to specify the intent. The patterns are used to check whether the given user query exist in the pattern. Responses are the output given for the input if it is present in the patterns.


 ```json
 {"intents": [
    {
      "tag": "greeting",
      "patterns": ["Hey", "Hello", "Hi", "What's up?", "Good Day"],
      "responses": ["Hello there!", "Hello, what can I do for you?","Hello sir, friday at your service","I'm listening","yes sir, i am listening"]
    },
    {
       "tag": "exit",
      "patterns": ["Bye", "See you", "Quit","Offline","Exit","Stop"],
      "responses": ["Thank you for spending time with me.","good bye sir","see you ,sir"]
    },
    {
      "tag":"how are you",
      "patterns":["How are you","How do you do","How are you doing"],
      "responses":["i am fine","i am a virtual assistant, how can i get tired, use your brain dummy!","I am better than i was, but not nearly as good as i am going to be","somewhere between better and best ","much better now that you are here","Armed and Ready"]
    }
]}
```

Lets say that if we give "how're you" as user input, it will print any one of the responses of the "how are you" `tag`, because the algorithm looks for words closer to the strings given in `patterns`. Likewise if we say "what is up", the program will provide the responses of "greeting" `tag` as output.

If the response of the output is a function in the main program, then we don't provide `responses` in `.json` file.
```json
    {
      "tag":"whatsapp",
      "patterns":["send a whatsapp message","whatsapp message"],
      "responses":[""]
    },
    {
      "tag":"email",
      "patterns":["send email","send mail","gmail"],
      "responses":[""]
    }
```
###### Now lets dive into the main program

First install the required python modules
 `pip install email  pyjokes pywhatkit.main speech_recognition  pyttsx3 neuralintents sys datetime email.message smtplib webbrowser time requests pywhatkit wolframalpha wikipedia bs4`

 The pyttsx3 is the python module which converts text string into audio so,  we initialise the  `pyttsx3` engine first. You can change the voice of the engine. You can also change the speed rate according to your need. 
```python
engine = pyttsx3.init()
voices = engine.getProperty('voices')
engine.setProperty('voice', voices[1].id)###changing index  to 0 if you need change the voice of the engine
engine.setProperty("rate", 150)
user = "" ## Enter your name
```

Now we create a python function to convert text to audio
```python

def say(audiostring):
    print(audiostring)
    engine.say(audiostring)
    engine.runAndWait()
```
The user input is recorded through the microphone and converted into text so that the machine learning algorithm can interpret the output. We use `google recogniser` to convert audio into text.
The `if statement` is given to provide output when then `wh` questions are asked by the user. We use wolframalpha and wikipedia as the source of the answers.

```python
def record():
    r = sr.Recognizer()
    with sr.Microphone(device_index=0) as source:
            print('I am listening!....')
            r.adjust_for_ambient_noise(source, duration=1)
            audio = r.listen(source)
            try:
                data = r.recognize_google(audio, language="en-IN")
                print(data)
            except sr.UnknownValueError:
                print('sorry, i didnt understand')
            if 'what' in data or 'which' in data or 'who' in data or 'when' in data or 'explain' in data or 'describe' in data or 'define' in data:
                  try:
                    app_id = '7JKRXQ-AQW9YQJQJT'
                    client = wolframalpha.Client(app_id)
                    res1 = client.query(data)
                    answer = next(res1.results).text
                    say(answer)
                    if answer == "None":
                        say(wikipedia.summary(data, sentences=2))
                  except StopIteration:
                        say(wikipedia.summary(data, sentences=2))
                  except AssertionError:
                        say(data)
            else:
                message = data.lower()
                assistant.request(message)
    return data
```
To provide greetings to the user whenever he run the program

```python

hour = datetime.datetime.now().hour
if(hour >= 6) and (hour < 12):
    say(f"Good Morning {user}")
elif(hour >= 12) and (hour < 18):
    say(f"Good afternoon {user}")
elif(hour >= 18) and (hour < 21):
    say(f"Good Evening {user}")
say("how may i assist you")
```
We make a weather functon to provide the user with weather information when required
```python
def weather():
    city = ""#Enter the name of your city
    city = city + " weather"
    city = city.replace(" ", "+")
    headers = {'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/58.0.3029.110 Safari/537.3'}

    res = requests.get(f'https://www.google.com/search?q={city}&oq={city}'
                       f'&aqs=chrome.0.35i39l2j0l4j46j69i60.6128j1j7&sourceid=chrome&ie=UTF-8', headers=headers)
    print("Searching...\n")
    soup = BeautifulSoup(res.text, 'html.parser')
    location = soup.select('#wob_loc')[0].getText().strip()
    info = soup.select('#wob_dc')[0].getText().strip()
    weather1 = soup.select('#wob_tm')[0].getText().strip()
    say(f"location is {location}")
    say(f"climate is {info}")
    say(f"temperature is {weather1}"+"°C")
```
To provide news  we use `news api`

```python
def today_news():
    url = 'https://newsapi.org/v2/everything?'
    parameters = {
        'q': 'big data', # query phrase
        'pageSize': 5,  # maximum is 100
        'apiKey': "" # sign up an account in https://newsapi.org and paste the api key here
    }
    response = requests.get(url, params=parameters)
    response_json = response.json()
    for i in response_json['articles']:
       say(i['title'])
```
To get the covid report of the day
```python 
def covid():
    r = requests.get("https://coronavirus-19-api.herokuapp.com/all").json()
    say(f'Confirmed cases: {r["cases"]} \nDeaths: {r["deaths"]} \nRecovered: {r["recovered"]}')
```
To search on youtube we create another record function
```python
def record1():
    r = sr.Recognizer()
    with sr.Microphone(device_index=0) as source:
            print('I am listening!....')
            r.adjust_for_ambient_noise(source, duration=1)
            audio = r.listen(source)
            try:
                audio_string = r.recognize_google(audio, language="en-IN")
                print(audio_string)
            except sr.UnknownValueError:
                say('sorry, i didnt understand')
            say("what you want to search on YouTube?")
            pywhatkit.playonyt(audio_string)
            sys.exit(0)
```
Now the final part is to map the `fuctions` with the `tags` of `.json.` file

```python


mappings = {
    "exit": stop,
    "email": sendemail,
    "climate": weather,
    "news": today_news,
    "now": time1,
    "today": date,
    "google": search,
    "corona": covid,
    "comedy": joke,
    }
```
Now we load the machine learning model

```python
ssistant = GenericAssistant('intents.json', intent_methods=mappings)
assistant.train_model()
assistant.save_model()
assistant.load_model()
```
Finally wel call the `record()` function in a while loop so it will keep repeat its recording
```python
while True:
    data1=record()
    if 'search on YouTube' in data1 or 'YouTube' in data1:#we call the record1() function here
        record1()
```
Now you can run the program.

**The full code of this program is available in my [github page](https://github.com/Charles-Shaju/Intelligent-Voice-Assistant.git)**

###### You can add as many features as you like into this model by creating the right functions in the `main` file and intents in the `.json` file