import tkinter as tk
from tkinter import ttk, filedialog, messagebox
import yt_dlp
import os

# Crear ventana principal
ventana = tk.Tk()
ventana.geometry("600x400")
ventana.title("Descargador de YouTube con yt-dlp")

# Variables para almacenar la ruta de descarga y la URL del video
ruta_guardar = tk.StringVar()
url_video = tk.StringVar()

# Configurar el estilo de la barra de progreso para que sea verde
style = ttk.Style(ventana)
style.theme_use('clam')  # Usamos un tema que permita el cambio de color
style.configure("green.Horizontal.TProgressbar", foreground='green', background='green')

# Función para explorar y seleccionar el directorio de descarga
def explorador():
    ruta = filedialog.askdirectory()
    if ruta:
        ruta_guardar.set(ruta)
        messagebox.showinfo("Ruta Seleccionada", f"Los videos se guardarán en: {ruta}")

# Función para descargar el video o el audio usando yt-dlp
def descargar_video():
    url = url_video.get().strip()
    ruta = ruta_guardar.get().strip()

    if not url:
        messagebox.showwarning("URL Vacía", "Por favor, introduce una URL de YouTube válida.")
        return

    if not ruta:
        messagebox.showwarning("Ruta no Seleccionada", "Por favor, selecciona una carpeta de destino.")
        return

    # Preguntar al usuario si quiere descargar el video completo o solo el audio
    respuesta = messagebox.askquestion(
        "Selecciona Tipo de Descarga", 
        "¿Qué deseas descargar?\n\n"
        "Presiona 'si' para Video + Audio.\n"
        "Presiona 'no' para solo Audio."
    )

    # Establecer las opciones de descarga según la elección del usuario
    if respuesta == 'yes':  # Si el usuario eligió "Sí", descargar video + audio
        ydl_opts = {
            'format': 'bestvideo+bestaudio/best',
            'outtmpl': os.path.join(ruta, '%(title)s.%(ext)s'),
            'merge_output_format': 'mp4',
            'noplaylist': True,
            'quiet': True,
            'progress_hooks': [hook_progreso],  # Enlace de progreso
        }
    else:  # Si el usuario eligió "No", descargar solo audio
        ydl_opts = {
            'format': 'bestaudio/best',
            'outtmpl': os.path.join(ruta, '%(title)s.%(ext)s'),
            'postprocessors': [{
                'key': 'FFmpegExtractAudio',
                'preferredcodec': 'mp3',
                'preferredquality': '192',
            }],
            'noplaylist': True,
            'quiet': True,
            'progress_hooks': [hook_progreso],  # Enlace de progreso
        }

    # Reiniciar la barra de progreso antes de comenzar la descarga
    barra_progreso['value'] = 0
    ventana.update_idletasks()  # Asegurarnos de que la GUI se actualice inmediatamente

    try:
        with yt_dlp.YoutubeDL(ydl_opts) as ydl:
            ydl.download([url])
            tipo_descarga = "Video y Audio" if respuesta == 'yes' else "Audio"
            messagebox.showinfo("Descarga Completa", f"El {tipo_descarga} se ha descargado correctamente en {ruta}.")
    except Exception as e:
        messagebox.showerror("Error de Descarga", f"Ha ocurrido un error: {str(e)}")

# Función para mostrar el progreso de la descarga
def hook_progreso(d):
    if d['status'] == 'downloading':
        porcentaje = d.get('_percent_str', '0%').strip()  # Obtener el porcentaje y eliminar espacios
        try:
            # Convertir el porcentaje a float y asegurarse de que esté en el rango de 0 a 100
            porcentaje_float = float(porcentaje.strip('%'))
            if 0 <= porcentaje_float <= 100:
                barra_progreso['value'] = porcentaje_float
                ventana.update_idletasks()  # Actualizar la interfaz gráfica
            else:
                print(f"Porcentaje fuera de rango: {porcentaje}")
        except ValueError:
            print(f"Error al convertir el porcentaje a float: {porcentaje}")

# Creación de la barra de opciones
barra_de_opciones = ttk.Notebook(ventana)
barra_de_opciones.pack(fill="both", expand=True)

# Creación de la pestaña "Seleccionar Carpeta"
pestaña_abrir = ttk.Frame(barra_de_opciones)
barra_de_opciones.add(pestaña_abrir, text="Seleccionar Carpeta")

# Widgets para seleccionar carpeta de destino
label_seleccionar = ttk.Label(pestaña_abrir, text="Selecciona una carpeta de destino:")
label_seleccionar.pack(pady=10)
boton_explorar = ttk.Button(pestaña_abrir, text="Explorar", command=explorador)
boton_explorar.pack(pady=5)
label_ruta_seleccionada = ttk.Label(pestaña_abrir, textvariable=ruta_guardar, wraplength=500)
label_ruta_seleccionada.pack(pady=10)

# Creación de la pestaña "Pegar URL"
pestaña_pegar_url = ttk.Frame(barra_de_opciones)
barra_de_opciones.add(pestaña_pegar_url, text="Pegar URL")

# Widgets para ingresar URL y descargar video
label_ingresar_url = ttk.Label(pestaña_pegar_url, text="Introduce la URL del video de YouTube:")
label_ingresar_url.pack(pady=10)
entrada_url = ttk.Entry(pestaña_pegar_url, textvariable=url_video, width=50)
entrada_url.pack(pady=5)
boton_descargar = ttk.Button(pestaña_pegar_url, text="Descargar Video", command=descargar_video)
boton_descargar.pack(pady=20)

# Barra de progreso
barra_progreso = ttk.Progressbar(pestaña_pegar_url, orient='horizontal', length=400, mode='determinate', style="green.Horizontal.TProgressbar")
barra_progreso.pack(pady=20)

# Iniciar el bucle de eventos de Tkinter
ventana.mainloop()
