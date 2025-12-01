import streamlit as st
import yt_dlp
import os
import time

# Configuração da página
st.set_page_config(page_title="Baixador YouTube", page_icon="📺")

st.title("📺 Baixador de Vídeos do YouTube")
st.write("Cole o link abaixo e clique em processar.")

# Campo de entrada
url = st.text_input("URL do Vídeo:")

# Função para baixar
def baixar_video(link):
    nome_arquivo = ""
    ydl_opts = {
        'format': 'best',  # Baixa o melhor formato único (para evitar erro de ffmpeg no servidor)
        'outtmpl': '%(title)s.%(ext)s',
        'noplaylist': True,
    }
    
    with yt_dlp.YoutubeDL(ydl_opts) as ydl:
        info = ydl.extract_info(link, download=True)
        nome_arquivo = ydl.prepare_filename(info)
    
    return nome_arquivo

# Botão de Ação
if st.button("Processar Vídeo"):
    if url:
        try:
            with st.spinner('Baixando vídeo no servidor... aguarde.'):
                # 1. Baixa o vídeo para a pasta do script
                arquivo_baixado = baixar_video(url)
                
                st.success(f"Vídeo processado: {arquivo_baixado}")
                
                # 2. Abre o arquivo para permitir que o usuário faça o download
                with open(arquivo_baixado, "rb") as file:
                    btn = st.download_button(
                        label="⬇️ CLIQUE AQUI PARA SALVAR NO SEU PC",
                        data=file,
                        file_name=arquivo_baixado,
                        mime="video/mp4"
                    )
                    
        except Exception as e:
            st.error(f"Ocorreu um erro: {e}")
    else:
        st.warning("Por favor, cole uma URL válida.")

# Nota de rodapé
st.markdown("---")
st.caption("Ferramenta criada com Python e Streamlit")