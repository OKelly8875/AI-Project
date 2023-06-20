# AI-Project

# import math
# Load credentials
# import os
# import speech_recognition as sr
# import os
# import webbrowser
import ast
import math
import os
import sys
import time
import webbrowser
from contextlib import contextmanager
# Mute ALSA errors...
from ctypes import *
from fileinput import filename

import openai
import pygame
import pyttsx3
import wikipedia
import wolframalpha

# import openai
# import pyttsx3
# import wikipedia
# import wolframalpha
# from dotenv import load_dotenv
# load_dotenv()
# Google TTS
# import google.cloud.texttospeech as tts
# import pygame
# import time
# import speech_recognition as sr

ERROR_HANDLER_FUNC = CFUNCTYPE(None, c_char_p, c_int, c_char_p, c_int, c_char_p)


def py_error_handler(filename, line, function, err, fmt):
    pass


c_error_handler = ERROR_HANDLER_FUNC(py_error_handler)


@contextmanager
def noalsaerr():
    try:
        asound = cdll.LoadLibrary('libasound.so')
        asound.snd_lib_error_set_handler(c_error_handler)
        yield
        asound.snd_lib_error_set_handler(None)
    except:
        yield
        print('')


### PARAMETERS ###
activationWords = ['wednesday', 'maxus']
tts_type = 'local'  # google or local

# Local speech engine initialisation
engine = pyttsx3.init()
voices = engine.getProperty('voices')
engine.setProperty('voice', voices[1].id)  # 1 = male, 0 = female


# Optimized Google TTS Client
def optimized_google_text_to_wav(voice_name: str, text: str, tts=None):
    # Set the text input to be synthesized
    text_input = tts.SynthesisInput(text=text)

    # Build the voice request
    voice_params = tts.VoiceSelectionParams(
        language_code=voice_name.split("-")[0] + "-" + voice_name.split("-")[1],
        name=voice_name.split("-")[2] + "-" + voice_name.split("-")[3]
    )

    # Select the type of audio file you want returned
    audio_config = tts.AudioConfig(audio_encoding=tts.AudioEncoding.LINEAR16)

    client = tts.TextToSpeechClient()
    response = client.synthesize_speech(
        input=text_input, voice=voice_params, audio_config=audio_config
    )

    return response.audio_content



# WolframAlpha

# Wolfram Alpha client
appId = 'VR2P7T-4VY3WWL44X'
wolframClient = wolframalpha.Client(appId)


def google_text_to_wav(param, text):
    pass


def speak(text, rate=120):
    time.sleep(0.3)
    try:
        if tts_type == 'local':
            engine.setProperty('rate', rate)
            engine.say(text, 'txt')
            engine.runAndWait()
        if tts_type == 'google':
            speech = google_text_to_wav('en-US-News-K', text)
            pygame.mixer.init(frequency=12000, buffer=512)
            speech_sound = pygame.mixer.Sound(speech)
            speech_length = int(math.ceil(pygame.mixer.Sound.get_length(speech_sound)))
            speech_sound.play()
            time.sleep(speech_length)
            pygame.mixer.quit()

    ## The standard keyboard interrupt is Ctrl+C. This interrupts the Google speech synthesis.
    except KeyboardInterrupt:
        try:
            if tts_type == 'google':
                pygame.mixer.quit()
        except:
            pass
        return


def parseCommand():
    with noalsaerr():
        listener = sr.Recognizer()
        print('Listening for a command')

        with sr.Microphone() as source:
            listener.pause_threshold = 2
            input_speech = listener.listen(source)

        try:
            print('Recognizing speech...')
            query = listener.recognize_google(input_speech, language='en_gb')
            print(f'The input speech was: {query}')

        except Exception as exception:
            print('I did not catch that')
            print(exception)

            return 'None'

        return query


def search_wikipedia(keyword=''):
    searchResults = wikipedia.search(keyword)
    if not searchResults:
        return 'Got nothing on my end'
    try:
        wikiPage = wikipedia.page(searchResults[0])
    except wikipedia.DisambiguationError as error:
        wikiPage = wikipedia.page(error.options[0])
    print(wikiPage.title)
    wikiSummary = str(wikiPage.summary)
    return wikiSummary


def listOrDict(var):
    if isinstance(var, list):
        return var[0]['plaintext']
    else:
        return var['plaintext']


def search_wolframalpha(keyword=''):
    response = wolframClient.query(keyword)

    # @


# Path: main.py
import speech_recognition as sr

# WolframAlpha

# Wolfram Alpha client
appId = 'VR2P7T-4VY3WWL44X'
wolframClient = wolframalpha.Client(appId)


def speak(text, rate=120):
    time.sleep(0.3)
    try:
        if tts_type == 'local':
            engine.setProperty('rate', rate)
            engine.say(text, 'txt')
            engine.runAndWait()
        if tts_type == 'google':
            speech = google_text_to_wav('en-US-News-K', text)
            pygame.mixer.init(frequency=12000, buffer=512)
            speech_sound = pygame.mixer.Sound(speech)
            speech_length = int(math.ceil(pygame.mixer.Sound.get_length(speech_sound)))
            speech_sound.play()
            time.sleep(speech_length)
            pygame.mixer.quit()

    ## The standard keyboard interrupt is Ctrl+C. This interrupts the Google speech synthesis.
    except KeyboardInterrupt:
        try:
            if tts_type == 'google':
                pygame.mixer.quit()
        except:
            pass
        return


def parseCommand():
    with noalsaerr():
        listener = sr.Recognizer()
        print('Listening for a command')

        with sr.Microphone() as source:
            listener.pause_threshold = 2
            input_speech = listener.listen(source)

        try:
            print('Recognizing speech...')
            query = listener.recognize_google(input_speech, language='en_gb')
            print(f'The input speech was: {query}')

        except Exception as exception:
            print('I did not catch that')
            print(exception)

            return 'None'

        return query


def search_wikipedia(keyword=''):
    searchResults = wikipedia.search(keyword)
    if not searchResults:
        return 'Got nothing on my end'
    try:
        wikiPage = wikipedia.page(searchResults[0])
    except wikipedia.DisambiguationError as error:
        wikiPage = wikipedia.page(error.options[0])
    print(wikiPage.title)
    wikiSummary = str(wikiPage.summary)
    return wikiSummary


def listOrDict(var: object) -> object:
    if isinstance(var, list):
        return var[0]['plaintext']
    else:
        return var['plaintext']


def getResponse(options):
    pass


def search_wolframalpha(keyword: object = '') -> object:
    response = wolframClient.query(keyword)

    # @success: Wolfram Alpha was able to resolve the query
    # @numpods: Number of results returned
    # pod: List of results. This can also contain subpods

    # Query not resolved
    if response['@success'] == 'false':
        speak('I could not compute')
    # Query resolved
    else:
        result = ''
        # Question
        pod0 = response['pod'][0]
        # May contain answer (Has highest confidence value)
        # if it's primary or has the title of result or definition, then it's the official result
        pod1 = response['pod'][1]
        if ('result' in pod1['@title'].lower()) or (pod1.get('@primary', 'false') == 'true') or (
                'definition' in pod1['@title'].lower()):
            # Get the result
            result = listOrDict(pod1['subpod'])
            # Remove bracketed section
            return result.split('(')[0]
        else:
            # Question
            pod0 = response['pod'][0]
            # Question Interpretation
            question = listOrDict(pod0['subpod'])
            # Possible answers
            pod1 = response['pod'][1]
            # List of options to choose from
            options = listOrDict(pod1['subpod'])
            # Convert to dictionary
            optionsDict = ast.literal_eval(options)
            # Get keys
            options = list(optionsDict.keys())
            # Remove brackets from key
            options = [x.split('(')[0] for x in options]
            # Remove brackets from value
            values = list(optionsDict.values())
            values = [x.split('(')[0] for x in values]
            # Create a dictionary from key and values
            optionsDict = dict(zip(options, values))
            # Convert to string
            options = ', '.join(options)
            # Speak the question
            speak(question)
            # Get answer from the user
            getResponse(options)
            # Check if answer is correct
            # noinspection PyUnresolvedReferences
            if answer.lower() in optionsDict.keys():
                # Get the correct answer
                # noinspection PyUnresolvedReferences
                correctAns = optionsDict[answer.lower()]
                # Speak the correct answer
                speak(correctAns)
            else:
                speak("I don't know")
            return


def query_openai(prompt=""):
    openai.organization = os.environ['OPENAI_ORG']
    openai.api_key = os.environ['OPENAI_API_KEY']

    # Temperature is a measure of randomness
    # Max_tokens is the number of tokens to generate
    response = openai.Completion.create(
        model="text-davinci-003",
        prompt="",
        temperature=1,
        max_tokens=256,
        top_p=1,
        frequency_penalty=0,
        presence_penalty=0
    )

    return response.choices[0].text


# Main loop
if __name__ == '__main__':
    speak('Alright Orlando, lets get this party started.', 120)

    while True:
        # Parse as a list
        # query = 'computer say hello'.split()
        query = parseCommand().lower().split()

        if query[0] in activationWords and len(query) > 1:
            query.pop(0)

            # Set commands
            if query[0] == 'say':
                if 'hello' in query:
                    speak('Whats good!')
                else:
                    query.pop(0)  # Remove 'say'
                    speech = ' '.join(query)
                    speak(speech)

            # Query OpenAI
            if query[0] == 'new job':
                query.pop(0)  # Remove 'new job'
                query = ' '.join(query)
                speech = query_openai(query)
                speak("Ok")
                speak(speech)

            # Navigation
            if query[0] == 'go' and query[1] == 'to':
                speak('Opening... ')
                query = ' '.join(query[2:])
                import webbrowser

                query = 'https://www.google.com'
                chrome_path = 'C:/Program Files (x86)/Google/Chrome/Application/chrome.exe %s'

                webbrowser.get(chrome_path).open_new(query)

            # Wikipedia
            if query[0] == 'wikipedia':
                query = ' '.join(query[1:])
                speak('Querying the universal databank')
                time.sleep(2)
                speak(search_wikipedia(query))

            # Wolfram Alpha
            if query[0] == 'compute' or query[0] == 'wednesday':
                query = ' '.join(query[1:])
                try:
                    result = search_wolframalpha(query)
                    speak(result)
                except:
                    speak('Unable to compute')


            # function to find desktop applications
            def findDesktopApplication(command):
                query_string = "find " + command + " application"
                engine = wolframalpha.Client("API KEY")
                res = engine.query(query_string)
                return res['pods'][1]['subpods'][0]['text']


            # function to create to-Do list
            def createTodoList(command):
                tasks = command.split(";")
                todoList = []
                for task in tasks:
                    todoList.append(task)
                return todoList


            # function to update libraries
            def updateLibraries():
                openai.update()


            # loop while listening for commands
            while True:
                spoken_command = listen()
                print(spoken_command)
                command = voiceCommands.get(spoken_command)
                if command == 'searchWeb':
                    searchWeb(spoken_command)
                elif command == 'readEmail':
                    readEmail()
                else:
                    print('Invalid command')
                    if __name__ == "__main__":
                        main()


                        def main():
                            voiceCommands = {
                                'search Google': 'searchWeb',
                                'check emails': 'readEmail'
                            }

                            while True:
                                spoken_command = listen()
                                print(spoken_command)
                                command = voiceCommands.get(spoken_command)
                                if command == 'searchWeb':
                                    searchWeb(spoken_command)
                                elif command == 'readEmail':
                                    readEmail()
                                else:
                                    print('Invalid command')


                        # define listen() function to receive user input
                        def listen():
                            spoken_command = input('Enter a command: ')
                            return spoken_command


                        # define searchWeb() function
                        def searchWeb(spoken_command):
                            query = spoken_command.split('Google ')[-1]
                            print('Searching the web for', query)


                        # define readEmail() function
                        def readEmail():
                            print('Reading emails...')


                        if __name__ == "__main__":
                            main()


                    def findDesktopApplication(command):
                        app = command.split(sep=" ")[2]
                        for (dirpath, dirnames, filenames) in os.walk(os.path.expanduser("~/Desktop")):
                            for filename in filenames:
                                if filename.startswith(app):
                                    return f'Application on desktop is {filename}'


                    def createTodoList(command):
                        file_name = command.split(sep=" ")[2]
                        path = os.input.expanduser(f'~/Desktop/{filename}.txt')
                        with open(path, "w") as f:
                            f.write(" ToDo List \n")
                        return f'Todo List saved as {file_name}.txt'



                    # Function for listening
                    def listen():
                        # prompt user to enter a command
                        command = input("Please enter a command: ")
                        return command


                    # Function for searching the web
                    def findAction(command):
                        pass


                    def searchWeb(spoken_command):
                        pass


                    def readEmail():
                        pass


                    def runVoiceActivatedApp():
                        while True:
                            spoken_command = listen()
                            action = findAction(command)
                            if action == 'searchWeb':
                                searchWeb(spoken_command)
                            elif action == 'readEmail':
                                readEmail()
                            elif action == 'findDesktopApplication':
                                command = listen()
                                print(findDesktopApplication(command))
                            elif action == 'createTodoList':
                                command = listen()
                                print(createTodoList(command))
                            elif action == 'updateLibraries':
                                updateLibraries()
                            else:
                                print("Command not detected)")


                    # Run voice activated application
                    runVoiceActivatedApp()



            # noinspection PyUnreachableCode
            def shutdown():
                while True:
                    query = input('Enter command: ')
                    query = query.lower()
                    if query[0] == 'exit':
                        speak('Shutting down')
                        sys.exit()
                        speak('Be safe')
                    else:
                        speak('Invalid command')


