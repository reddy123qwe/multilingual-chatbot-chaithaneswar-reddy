# multilingual-chatbot-chaithaneswar-reddy
!pip install spacy langdetect deep_translator
import spacy
from langdetect import detect
from deep_translator import GoogleTranslator

# Load spaCy English model
nlp = spacy.load("en_core_web_sm")

# Supported languages
languages = {
    'en': 'English',
    'hi': 'Hindi',
    'ta': 'Tamil',
    'te': 'Telugu',
    'bn': 'Bengali',
    'mr': 'Marathi',
    'kn': 'Kannada'
}

# Common greetings to detect
greetings = ['hi', 'hello', 'hey', 'good morning', 'good evening']

def detect_language(text):
    try:
        return detect(text)
    except:
        return None

def translate_text(text, src_lang, tgt_lang):
    try:
        if src_lang == tgt_lang:
            return text
        return GoogleTranslator(source=src_lang, target=tgt_lang).translate(text)
    except Exception as e:
        return f"[Translation Error: {e}]"

# NLP analysis function (tokenization, named entity recognition, intent detection)
def analyze_text(text):
    doc = nlp(text)
    
    print("\n🧠 NLP Analysis:")
    print("Tokens:", [token.text for token in doc])
    print("Entities:", [(ent.text, ent.label_) for ent in doc.ents])

    # Intent detection (check if the text matches simple greetings)
    normalized_text = text.lower().strip()
    if any(greeting in normalized_text for greeting in greetings):
        print("Intent: Greeting")
    elif any(token.lemma_.lower() in ['tell', 'show', 'give', 'give me', 'what', 'how'] for token in doc):
        print("Intent: Command or Question")
    elif doc[-1].text == '?':
        print("Intent: Question")
    else:
        print("Intent: Statement")

def multilingual_translate(text):
    detected_lang = detect_language(text)
    if detected_lang not in languages:
        print(f"❌ Unsupported or undetected language: {detected_lang}")
        return
    
    print(f"\n✅ Detected Language: {languages[detected_lang]} ({detected_lang})")

    # NLP Analysis
    analyze_text(text)

    # Show target language options excluding detected language
    print("\n🌍 Choose a language to translate into:")
    options = {i+1: (code, name) for i, (code, name) in enumerate(languages.items()) if code != detected_lang}
    for i, (code, name) in options.items():
        print(f"{i}. {name}")

    # User selects target language
    target_choice = input("\n🔁 Enter the number of the target language: ")
    if not target_choice.isdigit() or int(target_choice) not in options:
        print("❌ Invalid choice.")
        return

    target_lang_code, target_lang_name = options[int(target_choice)]
    translated = translate_text(text, detected_lang, target_lang_code)
    print(f"\n✅ Translation to {target_lang_name}: {translated}")

# Run loop
if _name_ == "_main_":
    while True:
        user_input = input("\n🗣️ Enter text (or type 'exit'): ")
        if user_input.lower() in ['exit', 'quit']:
            break
        multilingual_translate(user_input)
