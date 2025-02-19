# desktop_assistant


import speech_recognition as sr
import webbrowser
import pyttsx3
import requests
import os
from gtts import gTTS
import pygame

# Initialize pygame and speech recognizer
pygame.init()
pygame.mixer.init()
recognizer = sr.Recognizer()
engine = pyttsx3.init()

# API key for NewsAPI
newsapi = "<Your Key Here>"

def speak(text):
    """Convert text to speech and play it using gTTS and pygame."""
    try:
        tts = gTTS(text)
        tts.save('temp.mp3')

        pygame.mixer.music.load('temp.mp3')
        pygame.mixer.music.play()

        while pygame.mixer.music.get_busy():
            pygame.time.Clock().tick(10)

        pygame.mixer.music.unload()
        os.remove("temp.mp3")
    except Exception as e:
        print(f"Error in speak function: {e}")

def process_command(command):
    """Process user command and perform corresponding actions."""
    command = command.lower().strip()

    if "open google" in command:
        webbrowser.open("https://google.com")
    elif "open facebook" in command:
        webbrowser.open("https://facebook.com")
    elif "open pycharm_community_edition" in command:
        webbrowser.open("https://pycharm_community_edition.com")
    elif "open whatsapp" in command:
        webbrowser.open("https://whatsapp.com")
    elif "open notepad" in command:
        webbrowser.open("https://notepad.com")
    elif "open youtube" in command:
        webbrowser.open("https://youtube.com")
    elif "open github_desktop" in command:
        webbrowser.open("https://github_desktop.com")
    elif "open linkedin" in command:
        webbrowser.open("https://linkedin.com")
    elif "open Terminal" in command:
        webbrowser.open("https://Terminal.com")
    elif "open instagram " in command:
        webbrowser.open("https://instagram.com")
    elif "open binance " in command:
        webbrowser.open("https://binance.com")

    elif "open chat_gpt " in command:
        webbrowser.open("https://chat_gpt.com")
    elif "open deepseek " in command:
        webbrowser.open("https://deepseek.com")
    elif "news" in command:
        try:
            r = requests.get(f"https://newsapi.org/v2/top-headlines?country=in&apiKey={newsapi}")
            if r.status_code == 200:
                data = r.json()
                articles = data.get('articles', [])
                for article in articles[:5]:  # Limit news count
                    speak(article['title'])
        except Exception as e:
            print(f"Error fetching news: {e}")
    elif "exit" in command:
        speak("Goodbye!")
        exit()
    else:
        speak("I didn't understand the command.")

if __name__ == "__main__":
    speak("Hello sir...")

    while True:
        r = sr.Recognizer()

        print("recognizing...")
        try:
            with sr.Microphone() as source:
                r.adjust_for_ambient_noise(source)  # Helps with background noise
                print("Listening...")
                audio = r.listen(source, timeout=3, phrase_time_limit=2)
                word = r.recognize_google(audio).strip().lower()

                if "Fryday" in word:  # More flexible detection
                    speak("Ya?")
                    print("Fryday Active...")
                    audio = r.listen(source)
                    command = r.recognize_google(audio)
                    process_command(command)

        except sr.UnknownValueError:
            print("Could not understand audio.")
        except sr.RequestError:
            print("Error connecting to speech recognition service.")
        except Exception as e:
            print(f"Error: {e}")

