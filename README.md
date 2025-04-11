from moviepy.editor import *
from gtts import gTTS
import os
try:
    from googletrans import Translator
except:
    print("Para tradução automática, instale googletrans: pip install googletrans==4.0.0-rc1")
    Translator = None


def responder(entrada):
    # Aqui vai a lógica de resposta da IA
    resposta = f"Você disse: {entrada}"
    return resposta


def traduzir_texto(texto, destino='en'):
    if Translator:
        tradutor = Translator()
        traducao = tradutor.translate(texto, dest=destino)
        return traducao.text
    else:
        return texto  # Retorna o texto original se não houver tradutor


def gerar_video(texto, nome_arquivo="video_gerado.mp4", idioma="pt"):
    # Gera narração
    tts = gTTS(text=texto, lang=idioma)
    tts.save("audio.mp3")

    # Define duração
    duracao_audio = AudioFileClip("audio.mp3").duration

    # Fundo visual
    fundo = ColorClip(size=(1280, 720), color=(30, 30, 30), duration=duracao_audio)

    # Texto em tela
    txt = TextClip(texto, fontsize=48, color='white', size=(1100, None),
                   method='caption', align='center')
    txt = txt.set_position('center').set_duration(duracao_audio)

    # Áudio
    audio = AudioFileClip("audio.mp3")

    # Composição final
    video = CompositeVideoClip([fundo, txt]).set_audio(audio)
    video.write_videofile(nome_arquivo, fps=24)

    os.remove("audio.mp3")


def menu_principal():
    print("\n--- IA Multifuncional Ativada ---")
    print("1. Conversar com a IA")
    print("2. Gerar vídeo com texto e narração")
    print("3. Traduzir um texto")
    print("4. Sair")


def iniciar_ia():
    while True:
        menu_principal()
        escolha = input("\nEscolha uma opção (1-4): ")

        if escolha == "1":
            entrada = input("\nVocê: ")
            resposta = responder(entrada)
            print("IA:", resposta)

        elif escolha == "2":
            idioma = input("Idioma do vídeo (pt ou en): ").lower()
            usar_tradutor = input("Deseja traduzir automaticamente um texto em português para inglês? (s/n): ").lower()
            texto = input("Digite o texto: ")

            if usar_tradutor == 's' and idioma == 'en':
                texto = traduzir_texto(texto, 'en')
                print(f"Texto traduzido: {texto}")

            nome_arquivo = input("Nome do arquivo de vídeo (ex: meu_video.mp4): ")
            gerar_video(texto, nome_arquivo, idioma)

        elif escolha == "3":
            texto_original = input("Digite o texto para traduzir: ")
            idioma_destino = input("Idioma de destino (ex: en, pt, es, fr): ")
            texto_traduzido = traduzir_texto(texto_original, idioma_destino)
            print("Tradução:", texto_traduzido)

        elif escolha == "4":
            print("Encerrando a IA...")
            break

        else:
            print("Opção inválida. Tente novamente.")


if __name__ == "__main__":
    iniciar_ia()
    
