!pip -q install openai gTTS
!apt-get -qq update
!apt-get -qq install -y ffmpeg

import os, getpass
os.environ["OPENAI_API_KEY"] = getpass.getpass("Cole sua OPENAI_API_KEY: ")

import subprocess

wav_path = "input.wav"
subprocess.run([
    "ffmpeg", "-y",
    "-i", audio_in,
    "-ac", "1",
    "-ar", "16000",
    wav_path
], check=True)

wav_path

from openai import OpenAI

client = OpenAI(api_key=os.environ["OPENAI_API_KEY"])

with open(wav_path, "rb") as f:
    transcricao = client.audio.transcriptions.create(
        model="whisper-1",
        file=f
    )

texto_usuario = transcricao.text
print("📝 Transcrição:")
print(texto_usuario)

resposta = client.chat.completions.create(
    model="gpt-4o-mini",
    messages=[
        {"role": "system", "content": "Responda de forma clara e objetiva em português do Brasil."},
        {"role": "user", "content": texto_usuario}
    ]
)

texto_resposta = resposta.choices[0].message.content
print("\n🤖 Resposta do ChatGPT:")
print(texto_resposta)

