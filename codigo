language = "pt" # Definição de linguagem, sendo possivel utilizar demais idiomas (es, en, pt, fr e outros)

from IPython.display import Audio, display, Javascript #Codificação que executa junto do JavaScript para gravação de audio.
from google.colab import output
from base64 import b64decode

RECORD = """
const sleep = time => new Promise(resolve => setTimeout(resolve, time))
const b2text = blob => new Promise(resolve => {
  const reader = new FileReader()
  reader.onloadend = e => resolve(e.srcElement.result)
  reader.readAsDataURL(blob)
})
var record = time => new Promise(async resolve => {
  stream = await navigator.mediaDevices.getUserMedia({ audio: true })
  recorder = new MediaRecorder(stream)
  chunks = []
  recorder.ondataavailable = e => chunks.push(e.data)
  recorder.start()
  await sleep(time)
  recorder.onstop = async ()=>{
    blob = new Blob(chunks)
    text = await b2text(blob)
    resolve(text)
  }
  recorder.stop()
})
"""

def record(sec=5):
  display(Javascript(RECORD))
  js_result = output.eval_js('record(%s)' % (sec * 1000))
  audio = b64decode(js_result.split(',')[1])
  file_name = 'request_audio.wav'
  with open(file_name, 'wb') as f:
    f.write(audio)
  return f'/content/{file_name}'

print("gravando....\n")
record_file = record()
display(Audio(filename=record_file, autoplay=False, rate=44100))

!pip install git+https://github.com/openai/whisper.git -q #Instalador do Whisper da OpenAi.

import whisper #Whisper cria a transcrição do audio criado inicialmente (a pergunta).

model = whisper.load_model("small")
result = model.transcribe(record_file, fp16=False, language=language)
transcription = result["text"]
print(transcription)

!pip install groq #Instalador de GroqCloud para execução do arquivo.

from groq import Groq #Chama a função do GroqCloud para resposta automatica da IA.

client = Groq(api_key="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")

resp = client.chat.completions.create(
    model="llama-3.3-70b-versatile",
    messages=[
        {"role": "user", "content": transcription}
    ]
)
groq_response = resp.choices[0].message.content
print(groq_response)

!pip install gTTS #instala o sintetizador de audio.

from gtts import gTTS #Chama o sintetizador de audio e executa a resposta em audio automaticamente com o autoplay.
gtts_object = gTTS(text=groq_response, lang=language, slow=False)
response_audio = "/content/response_audio.wav"
gtts_object.save(response_audio)
display(Audio(response_audio, autoplay=True))
