import tkinter as tk from gtts import gTTS from langdetect import detect import playsound import os import time

latest_voice_file = None # Stores the most recent voice file

----------------- Functions -----------------
def send_message(): global latest_voice_file

message = entry.get()
if not message.strip():
    return
chat_log.insert(tk.END, f"You: {message}\n")
entry.delete(0, tk.END)

# Detect language
try:
    lang = detect(message)
except:
    lang = 'en'

# List of languages supported by gTTS
supported_langs = [
    'af', 'sq', 'ar', 'bn', 'bs', 'ca', 'cs', 'cy', 'da', 'de', 'el', 'en', 'en-au', 'en-uk', 'en-us',
    'eo', 'es', 'et', 'fi', 'fr', 'gu', 'hi', 'hr', 'hu', 'id', 'is', 'it', 'ja', 'jw', 'km', 'kn',
    'ko', 'la', 'lv', 'ml', 'mr', 'my', 'ne', 'nl', 'no', 'pl', 'pt', 'ro', 'ru', 'si', 'sk', 'sq',
    'sr', 'su', 'sv', 'sw', 'ta', 'te', 'th', 'tl', 'tr', 'uk', 'ur', 'vi', 'zh-cn', 'zh-tw'
]

if lang not in supported_langs:
    lang = 'en'  # fallback if not supported

try:
    # Unique filename for Windows
    timestamp = time.strftime("%Y%m%d_%H%M%S")
    filename = f"voice_{lang}_{timestamp}.mp3"

    tts = gTTS(text=message, lang=lang)
    tts.save(filename)
    latest_voice_file = filename

    chat_log.insert(tk.END, f"🎤 Voice message ready ({lang})\n")

    # Play immediately
    playsound.playsound(filename)
except Exception as e:
    chat_log.insert(tk.END, f"❌ Error generating voice: {str(e)}\n")
def play_latest(): """Play the most recent generated voice message""" global latest_voice_file if latest_voice_file and os.path.exists(latest_voice_file): chat_log.insert(tk.END, "▶️ Playing last voice message...\n") playsound.playsound(latest_voice_file) else: chat_log.insert(tk.END, "❌ No voice messages yet.\n")

----------------- GUI -----------------
root = tk.Tk() root.title("🎤 Voice Chat App") root.geometry("450x450")

Chat display
chat_log = tk.Text(root, height=20, width=55) chat_log.pack(pady=10)

Entry box
entry = tk.Entry(root, width=45) entry.pack(pady=5)

Buttons
button_frame = tk.Frame(root) button_frame.pack(pady=5)

send_button = tk.Button(button_frame, text="Send Message", command=send_message, width=20, bg="#0b84ff", fg="white") send_button.grid(row=0, column=0, padx=5)

play_button = tk.Button(button_frame, text="Play Latest Voice", command=play_latest, width=20, bg="#00b894", fg="white") play_button.grid(row=0, column=1, padx=5)

root.mainloop()
