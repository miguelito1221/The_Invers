import tkinter as tk
from tkinter import messagebox
from tkinter import ttk
import os
import bcrypt
import sqlite3

# Configuración de la Base de Datos
def init_db():
    conn = sqlite3.connect("usuarios.db")
    cursor = conn.cursor()

    # Crear tabla de usuarios si no existe
    cursor.execute('''CREATE TABLE IF NOT EXISTS usuarios (
        id INTEGER PRIMARY KEY AUTOINCREMENT,
        email TEXT UNIQUE NOT NULL,
        usuario TEXT NOT NULL,
        cedula TEXT NOT NULL,
        contrasena TEXT NOT NULL
    )''')

    # Crear tabla de información adicional si no existe
    cursor.execute('''CREATE TABLE IF NOT EXISTS informacion (
        id INTEGER PRIMARY KEY AUTOINCREMENT,
        nombre TEXT,
        telefono TEXT,
        direccion TEXT,
        representante TEXT,
        cedula_representante TEXT,
        celular_representante TEXT
    )''')
    
    conn.commit()
    conn.close()

# Funciones de Seguridad
def hash_password(password):
    return bcrypt.hashpw(password.encode('utf-8'), bcrypt.gensalt())

def check_password(password, hashed):
    return bcrypt.checkpw(password.encode('utf-8'), hashed)

# Funciones de la Aplicación
def registrar_usuario():
    email = entry_email.get()
    usuario = entry_usuario.get()
    cedula = entry_cedula.get()
    contrasena = entry_contrasena.get()
    validar_contrasena = entry_validar_contrasena.get()

    if not email or not usuario or not cedula or not contrasena or not validar_contrasena:
        messagebox.showerror("Error", "Todos los campos son obligatorios.")
        return

    if "@" not in email:
        messagebox.showerror("Error", "El correo debe contener un '@'.")
        return

    if len(cedula) != 10 or not cedula.isdigit():
        messagebox.showerror("Error", "La cédula debe contener exactamente 10 números.")
        return

    if contrasena != validar_contrasena:
        messagebox.showerror("Error", "Las contraseñas no coinciden.")
        return

    hashed_password = hash_password(contrasena)

    try:
        conn = sqlite3.connect("usuarios.db")
        cursor = conn.cursor()
        cursor.execute("INSERT INTO usuarios (email, usuario, cedula, contrasena) VALUES (?, ?, ?, ?)",
                       (email, usuario, cedula, hashed_password))
        conn.commit()
        conn.close()

        messagebox.showinfo("Registro", "Usuario registrado exitosamente.")
        ventana_registro.destroy()
        ventana.deiconify()
    except sqlite3.IntegrityError:
        messagebox.showerror("Error", "El correo electrónico ya está registrado.")


def validar_inicio():
    email = entry_email_iniciar.get()
    contrasena = entry_contrasena_iniciar.get()

    if not email or not contrasena:
        messagebox.showerror("Error", "Todos los campos son obligatorios.")
        return

    try:
        conn = sqlite3.connect("usuarios.db")
        cursor = conn.cursor()
        cursor.execute("SELECT usuario, contrasena FROM usuarios WHERE email = ?", (email,))
        resultado = cursor.fetchone()
        conn.close()

        if resultado and check_password(contrasena, resultado[1]):
            messagebox.showinfo("Inicio de Sesión", "Inicio de sesión exitoso.")
            ventana_iniciar.destroy()
            mostrar_pagina_inicio(resultado[0])
        else:
            messagebox.showerror("Error", "Correo electrónico o contraseña incorrectos.")
    except sqlite3.Error as e:
        messagebox.showerror("Error", f"Error al conectar con la base de datos: {e}")

# Funciones de Interfaz Gráfica
def abrir_pagina_registro():
    global entry_email, entry_usuario, entry_cedula, entry_contrasena, entry_validar_contrasena

    ventana.iconify()
    global ventana_registro
    ventana_registro = tk.Toplevel()
    ventana_registro.title("Registro")
    ventana_registro.geometry("900x700")

    ttk.Label(ventana_registro, text="Correo Electrónico:").pack(pady=5)
    entry_email = ttk.Entry(ventana_registro)
    entry_email.pack(pady=5)

    ttk.Label(ventana_registro, text="Usuario:").pack(pady=5)
    entry_usuario = ttk.Entry(ventana_registro)
    entry_usuario.pack(pady=5)

    ttk.Label(ventana_registro, text="Cédula:").pack(pady=5)
    entry_cedula = ttk.Entry(ventana_registro)
    entry_cedula.pack(pady=5)

    ttk.Label(ventana_registro, text="Contraseña:").pack(pady=5)
    entry_contrasena = ttk.Entry(ventana_registro, show='*')
    entry_contrasena.pack(pady=5)

    ttk.Label(ventana_registro, text="Confirmar Contraseña:").pack(pady=5)
    entry_validar_contrasena = ttk.Entry(ventana_registro, show='*')
    entry_validar_contrasena.pack(pady=5)

    ttk.Button(ventana_registro, text="Registrar", command=registrar_usuario).pack(pady=10)
    ttk.Button(ventana_registro, text="Regresar", command=lambda: [ventana_registro.destroy(), ventana.deiconify()]).pack(pady=5)


def abrir_pagina_iniciar():
    global entry_email_iniciar, entry_contrasena_iniciar

    ventana.iconify()
    global ventana_iniciar
    ventana_iniciar = tk.Toplevel()
    ventana_iniciar.title("Inicio de Sesión")
    ventana_iniciar.geometry("900x700")

    ttk.Label(ventana_iniciar, text="Correo Electrónico:").pack(pady=5)
    entry_email_iniciar = ttk.Entry(ventana_iniciar)
    entry_email_iniciar.pack(pady=5)

    ttk.Label(ventana_iniciar, text="Contraseña:").pack(pady=5)
    entry_contrasena_iniciar = ttk.Entry(ventana_iniciar, show='*')
    entry_contrasena_iniciar.pack(pady=5)

    ttk.Button(ventana_iniciar, text="Iniciar Sesión", command=validar_inicio).pack(pady=10)
    ttk.Button(ventana_iniciar, text="Regresar", command=lambda: [ventana_iniciar.destroy(), ventana.deiconify()]).pack(pady=5)

# Función para mostrar u ocultar la contraseña
def mostrar_contrasena(entry):
    if entry.cget('show') == '*':
        entry.config(show='')
        entry.master.winfo_children()[-1].config(text='Ocultar')
    else:
        entry.config(show='*')
        entry.master.winfo_children()[-1].config(text='Mostrar')

# Función para registrar usuario
def registrar_usuario():
    email = entry_email.get()
    usuario = entry_usuario.get()
    cedula = entry_cedula.get()
    contrasena = entry_contrasena.get()
    validar_contrasena = entry_validar_contrasena.get()

    if not email or not usuario or not cedula or not contrasena or not validar_contrasena:
        messagebox.showerror("Error", "Todos los campos son obligatorios.")
        return

    if "@" not in email:
        messagebox.showerror("Error", "El correo electrónico debe contener un '@'.")
        return

    if len(cedula) != 10 or not cedula.isdigit():
        messagebox.showerror("Error", "La cédula debe contener exactamente 10 números.")
        return

    if contrasena != validar_contrasena:
        messagebox.showerror("Error", "Las contraseñas no coinciden.")
        return

    with open("usuarios.txt", "a") as file:
        file.write(f"{email},{usuario},{cedula},{contrasena}\n")

    messagebox.showinfo("Registro", "Usuario registrado exitosamente.")
    ventana_registro.destroy()
    mostrar_pagina_inicio()

# Función para validar inicio de sesión
def validar_inicio():
    email = entry_email_iniciar.get()
    contrasena = entry_contrasena_iniciar.get()

    if "@" not in email:
        messagebox.showerror("Error", "El correo electrónico debe contener un '@'.")
        return

    if not os.path.exists("usuarios.txt"):
        messagebox.showerror("Error", "No hay usuarios registrados.")
        return

    with open("usuarios.txt", "r") as file:
        for line in file:
            datos = line.strip().split(',')
            if email == datos[0] and contrasena == datos[3]:
                messagebox.showinfo("Iniciar Sesión", "Inicio de sesión exitoso.")
                ventana_iniciar.destroy()
                mostrar_pagina_inicio(datos[1])
                return

    messagebox.showerror("Error", "Correo electrónico o contraseña incorrectos.")

# Función para mostrar la página de inicio
def mostrar_pagina_inicio(usuario_nombre=None):
    ventana_inicio = tk.Toplevel()
    ventana_inicio.title("The Invers")
    ventana_inicio.geometry("900x700")

    # Encabezado de la página
    encabezado = tk.Frame(ventana_inicio, bg="#f0f0f0", height=100)
    encabezado.pack(fill="x", side="top")

    # Crear un Canvas para el efecto de degradado
    canvas = tk.Canvas(encabezado, bg="#f0f0f0", height=100, width=900)
    canvas.pack()

    # Función para crear un degradado
    def crear_degradado(canvas, texto, x, y, font):
        start_color = (0, 106, 193)  # Color inicial: #006ac1
        end_color = (79, 175, 255)    # Color final: #4fafff
        steps = 256

        for i in range(steps):
            r = int(start_color[0] + (end_color[0] - start_color[0]) * (i / steps))
            g = int(start_color[1] + (end_color[1] - start_color[1]) * (i / steps))
            b = int(start_color[2] + (end_color[2] - start_color[2]) * (i / steps))
            color = f'#{r:02x}{g:02x}{b:02x}'  # Convertir a formato hexadecimal
            canvas.create_text(x, y, text=texto, fill=color, font=font, anchor='n')

    # Texto "The Invers" como encabezado con degradado
    crear_degradado(canvas, "The Invers", 450, 50, ("Times New Roman", 55, "bold"))  # Tamaño de letra aumentado a 55

    # Línea divisoria bajo el encabezado
    separador = tk.Canvas(ventana_inicio, height=3, width=900, bg="#0076cf", highlightthickness=0)
    separador.pack()

    # Mensaje de bienvenida debajo de la línea
    bienvenida_texto = f"Bienvenido, {usuario_nombre}" if usuario_nombre else "Bienvenido a la Página de Inicio"
    mensaje_bienvenida = tk.Label(
        ventana_inicio, text=bienvenida_texto, font=("Spectral", 16), fg="black"
    )
    mensaje_bienvenida.pack(pady=20)

    # Menú de sándwich
    def mostrar_menu_sandwich(event):
        menu_sandwich.post(event.x_root, event.y_root)

    # Crear el menú desplegable
    menu_sandwich = tk.Menu(ventana_inicio, tearoff=0, font=("Arial", 14))
    menu_sandwich.add_command(label="Perfil", command=lambda: abrir_pagina_perfil(usuario_nombre))
    menu_sandwich.add_separator()  # Línea separadora
    menu_sandwich.add_command(label="Cerrar Sesión", command=lambda: [ventana_inicio.destroy(), ventana.deiconify()])

    # Botón del menú de sándwich
    boton_sandwich = tk.Button(
        encabezado,
        text="☰",  # Símbolo del menú de sándwich
        font=("Arial", 20, "bold"),
        bg="#f0f0f0",
        fg="black",
        bd=0,
        command=lambda: mostrar_menu_sandwich(event=None)
    )
    boton_sandwich.place(x=20, y=20)

    # Detectar clic derecho en el botón
    boton_sandwich.bind("<Button-1>", mostrar_menu_sandwich)

    # Contenedor principal para otros botones
    contenedor = tk.Frame(ventana_inicio)
    contenedor.place(relx=0.5, rely=0.5, anchor="center")

    nuevo_boton = tk.Button(contenedor, text="Nuevo+", font=("Arial", 16), width=15, command=abrir_pagina_nuevo)
    nuevo_boton.pack(pady=10)

    mios_boton = tk.Button(contenedor, text="Míos", font=("Arial", 16), width=15, command=abrir_pagina_mios)  # Cambiar función según implementación
    mios_boton.pack(pady=10)

# Función para abrir la página de perfil
def abrir_pagina_perfil(usuario_nombre):
    ventana_perfil = tk.Toplevel()
    ventana_perfil.title("The Invers")
    ventana_perfil.geometry("900x700")

    label_perfil = tk.Label(ventana_perfil, text="Información de Perfil", font=("Arial", 16))
    label_perfil.pack(pady=10)

    with open("usuarios.txt", "r") as file:
        usuario = file.readlines()[-1].strip().split(',')

    campos = ["Correo Electrónico", "Nombre de Usuario", "Cédula"]

    entries = []
    for i, campo in enumerate(campos):
        label = tk.Label(ventana_perfil, text=f"{campo}:")
        label.pack(pady=5)
        entry = tk.Entry(ventana_perfil)
        entry.insert(0, usuario[i])
        entry.pack(pady=5)
        entries.append(entry)

    def guardar_cambios():
        usuario_editado = [entry.get() for entry in entries]
        with open("usuarios.txt", "r") as file:
            usuarios = file.readlines()
        usuarios[-1] = ",".join(usuario_editado) + "\n"
        with open("usuarios.txt", "w") as file:
            file.writelines(usuarios)
        messagebox.showinfo("Perfil", "Información actualizada correctamente.")

    guardar_boton = tk.Button(ventana_perfil, text="Guardar Cambios", command=guardar_cambios)
    guardar_boton.pack(pady=5)

    regresar_boton = tk.Button(ventana_perfil, text="Regresar", command=ventana_perfil.destroy)
    regresar_boton.pack(pady=5)

# Función para abrir la página de "Nuevo+"
def abrir_pagina_nuevo():
    ventana_nuevo = tk.Toplevel()
    ventana_nuevo.title("The Invers")
    ventana_nuevo.geometry("900x700")

    campos = ["Nombre", "Número de Teléfono", "Dirección", "Representante", "Cédula del Representante", "Celular del Representante"]

    entries = []
    for campo in campos:
        label = tk.Label(ventana_nuevo, text=f"{campo}:")
        label.pack(pady=5)
        entry = tk.Entry(ventana_nuevo)
        entry.pack(pady=5)
        entries.append(entry)

    def agregar_campo():
        label = tk.Label(ventana_nuevo, text="Otro Campo:")
        label.pack(pady=5)
        entry = tk.Entry(ventana_nuevo)
        entry.pack(pady=5)
        entries.append(entry)

    def guardar_informacion():
        # Validar el número de teléfono
        telefono = entries[1].get()  # Suponiendo que el número de teléfono es el segundo campo
        if not telefono.isdigit() or len(telefono) != 9:
            messagebox.showerror("Error", "El número de teléfono debe contener exactamente 9 dígitos.")
            return

        # Validar la cédula
        cedula = entries[4].get()  # Suponiendo que la cédula es el quinto campo
        if not cedula.isdigit() or len(cedula) != 10:
            messagebox.showerror("Error", "La cédula debe contener exactamente 10 dígitos.")
            return

        # Validar el celular
        celular = entries[5].get()  # Suponiendo que el celular es el sexto campo
        if not (celular.isdigit() and len(celular) == 10 and celular.startswith("09")):
            messagebox.showerror("Error", "El celular debe contener 10 dígitos y comenzar con '09'.")
            return

        # Guardar la información en el archivo
        with open("informacion_nuevo.txt", "a") as file:
            file.write(",".join(entry.get() for entry in entries) + "\n")
        messagebox.showinfo("Nuevo+", "Información guardada correctamente.")

    agregar_boton = tk.Button(ventana_nuevo, text="Añadir Campo", command=agregar_campo)
    agregar_boton.pack(pady=5)

    guardar_boton = tk.Button(ventana_nuevo, text="Guardar", command=guardar_informacion)
    guardar_boton.pack(pady=5)

    regresar_boton = tk.Button(ventana_nuevo, text="Regresar", command=ventana_nuevo.destroy)
    regresar_boton.pack(pady=5)

# Función para abrir la página de "Míos"
def abrir_pagina_mios():
    ventana_mios = tk.Toplevel()
    ventana_mios.title("The Invers")
    ventana_mios.geometry("900x700")

    label_mios = tk.Label(ventana_mios, text="Archivos Guardados", font=("Arial", 16))
    label_mios.pack(pady=10)

    tree = ttk.Treeview(ventana_mios, columns=("#1",), show="headings")
    tree.heading("#1", text="Información")
    tree.pack(expand=True, fill=tk.BOTH, pady=10)

    # Cargar información desde el archivo
    if os.path.exists("informacion_nuevo.txt"):
        with open("informacion_nuevo.txt", "r") as file:
            for linea in file:
                tree.insert("", tk.END, values=(linea.strip(),))

    # Función para editar la información seleccionada
    def editar_informacion():
        selected_item = tree.selection()
        if selected_item:
            values = tree.item(selected_item, 'values')[0].split(',')
            entries = []
            for i, value in enumerate(values):
                entry = tk.Entry(ventana_mios)
                entry.insert(0, value)
                entry.pack(pady=5)
                entries.append(entry)

            def guardar_cambios():
                new_values = [entry.get() for entry in entries]
                with open("informacion_nuevo.txt", "r") as file:
                    lineas = file.readlines()
                lineas[tree.index(selected_item)] = ",".join(new_values) + "\n"
                with open("informacion_nuevo.txt", "w") as file:
                    file.writelines(lineas)
                messagebox.showinfo("Míos", "Información actualizada correctamente.")
                ventana_mios.destroy()
                abrir_pagina_mios()  # Recargar la página para mostrar los cambios

            guardar_boton = tk.Button(ventana_mios, text="Guardar Cambios", command=guardar_cambios)
            guardar_boton.pack(pady=5)

    editar_boton = tk.Button(ventana_mios, text="Editar Selección", command=editar_informacion)
    editar_boton.pack(pady=5)

    regresar_boton = tk.Button(ventana_mios, text="Regresar", command=ventana_mios.destroy)
    regresar_boton.pack(pady=5)

# Función para abrir la página de inicio de sesión
def abrir_pagina_iniciar():
    ventana.iconify()  # Minimizar la ventana principal
    global ventana_iniciar
    ventana_iniciar = tk.Toplevel()
    ventana_iniciar.title("Iniciar Sesión")
    ventana_iniciar.geometry("900x700")

    label_email = tk.Label(ventana_iniciar, text="Correo Electrónico:")
    label_email.pack(pady=5)

    global entry_email_iniciar
    entry_email_iniciar = tk.Entry(ventana_iniciar)
    entry_email_iniciar.pack(pady=5)

    label_contrasena = tk.Label(ventana_iniciar, text="Contraseña:")
    label_contrasena.pack(pady=5)

    frame_contrasena = tk.Frame(ventana_iniciar)
    frame_contrasena.pack(pady=5)

    global entry_contrasena_iniciar
    entry_contrasena_iniciar = tk.Entry(frame_contrasena, show='*')
    entry_contrasena_iniciar.pack(side=tk.LEFT)

    mostrar_boton = tk.Button(frame_contrasena, text="Mostrar", command=lambda: mostrar_contrasena(entry_contrasena_iniciar))
    mostrar_boton.pack(side=tk.LEFT, padx=5)

    iniciar_boton = tk.Button(ventana_iniciar, text="Iniciar Sesión", command=validar_inicio)
    iniciar_boton.pack(pady=5)

    regresar_boton = tk.Button(ventana_iniciar, text="Regresar", command=lambda: [ventana_iniciar.destroy(), ventana.deiconify()])
    regresar_boton.pack(pady=5)

# Función para abrir la página de registro
def abrir_pagina_registro():
    ventana.iconify()  # Minimizar la ventana principal
    global ventana_registro
    ventana_registro = tk.Toplevel()
    ventana_registro.title("Registrarse")
    ventana_registro.geometry("900x700")

    global entry_email, entry_usuario, entry_cedula, entry_contrasena, entry_validar_contrasena

    label_email = tk.Label(ventana_registro, text="Correo Electrónico:")
    label_email.pack(pady=5)
    entry_email = tk.Entry(ventana_registro)
    entry_email.pack(pady=5)

    label_usuario = tk.Label(ventana_registro, text="Nombre de Usuario:")
    label_usuario.pack(pady=5)
    entry_usuario = tk.Entry(ventana_registro)
    entry_usuario.pack(pady=5)

    label_cedula = tk.Label(ventana_registro, text="Número de Cédula:")
    label_cedula.pack(pady=5)
    entry_cedula = tk.Entry(ventana_registro)
    entry_cedula.pack(pady=5)

    label_contrasena = tk.Label(ventana_registro, text="Contraseña:")
    label_contrasena.pack(pady=5)
    frame_contrasena = tk.Frame(ventana_registro)
    frame_contrasena.pack(pady=5)

    entry_contrasena = tk.Entry(frame_contrasena, show='*')
    entry_contrasena.pack(side=tk.LEFT)
    mostrar_boton = tk.Button(frame_contrasena, text="Mostrar", command=lambda: mostrar_contrasena(entry_contrasena))
    mostrar_boton.pack(side=tk.LEFT, padx=5)

    label_validar_contrasena = tk.Label(ventana_registro, text="Validar Contraseña:")
    label_validar_contrasena.pack(pady=5)
    frame_validar_contrasena = tk.Frame(ventana_registro)
    frame_validar_contrasena.pack(pady=5)

    entry_validar_contrasena = tk.Entry(frame_validar_contrasena, show='*')
    entry_validar_contrasena.pack(side=tk.LEFT)
    mostrar_boton_validar = tk.Button(frame_validar_contrasena, text="Mostrar", command=lambda: mostrar_contrasena(entry_validar_contrasena))
    mostrar_boton_validar.pack(side=tk.LEFT, padx=5)

    registrar_boton = tk.Button(ventana_registro, text="Registrarse", command=registrar_usuario)
    registrar_boton.pack(pady=5)

    regresar_boton = tk.Button(ventana_registro, text="Regresar", command=lambda: [ventana_registro.destroy(), ventana.deiconify()])
    regresar_boton.pack(pady=5)

# Crear la ventana principal
ventana = tk.Tk()
ventana.title("The Invers")
ventana.geometry("900x700")

# Encabezado de la página
encabezado = tk.Frame(ventana, bg="#f0f0f0", height=100)
encabezado.pack(fill="x", side="top")

# Crear un Canvas para el efecto de degradado
canvas = tk.Canvas(encabezado, bg="#f0f0f0", height=100, width=900)
canvas.pack()

# Función para crear un degradado
def crear_degradado(canvas, texto, x, y, font):
    start_color = (0, 106, 193)  # Color inicial: #006ac1
    end_color = (79, 175, 255)    # Color final: #4fafff
    steps = 256

    for i in range(steps):
        r = int(start_color[0] + (end_color[0] - start_color[0]) * (i / steps))
        g = int(start_color[1] + (end_color[1] - start_color[1]) * (i / steps))
        b = int(start_color[2] + (end_color[2] - start_color[2]) * (i / steps))
        color = f'#{r:02x}{g:02x}{b:02x}'  # Convertir a formato hexadecimal
        canvas.create_text(x, y, text=texto, fill=color, font=font, anchor='n')

# Crear el Label fuera del encabezado
label_titulo = tk.Label(ventana, text="", font=("Times New Roman", 55, "bold"), bg="#f0f0f0")
label_titulo.pack(pady=20)  # Ajusta el padding según sea necesario

# Función para crear un degradado de texto
def crear_degradado_texto(label, texto):
    start_color = (0, 106, 193)  # Color inicial: #006ac1
    end_color = (79, 175, 255)    # Color final: #4fafff
    steps = len(texto)

    for i in range(steps):
        r = int(start_color[0] + (end_color[0] - start_color[0]) * (i / steps))
        g = int(start_color[1] + (end_color[1] - start_color[1]) * (i / steps))
        b = int(start_color[2] + (end_color[2] - start_color[2]) * (i / steps))
        color = f'#{r:02x}{g:02x}{b:02x}'  # Convertir a formato hexadecimal
        label.config(fg=color)  # Cambiar el color del texto

        # Actualizar el texto en la etiqueta
        label.config(text=texto)
        ventana.update()  # Actualizar la ventana para mostrar el cambio

# Llamar a la función para crear el degradado
crear_degradado_texto(label_titulo, "The Invers")

# Contenedor principal para otros botones
contenedor = tk.Frame(ventana)
contenedor.place(relx=0.5, rely=0.5, anchor="center")

# Función para crear un botón con bordes redondeados
def crear_boton_redondeado(parent, text, command, color="#2196f3"):
    # Crear un Canvas para el botón
    canvas = tk.Canvas(parent, width=200, height=50, bg=color, highlightthickness=0, borderwidth=0)
    canvas.create_oval(0, 0, 20, 20, fill=color, outline=color)  # Esquinas redondeadas
    canvas.create_oval(180, 0, 200, 20, fill=color, outline=color)  # Esquinas redondeadas
    canvas.create_rectangle(10, 0, 190, 50, fill=color, outline=color)  # Cuerpo del botón
    canvas.create_text(100, 25, text=text, fill="white", font=("Spectral", 24))  # Tamaño de letra aumentado a 24
    canvas.bind("<Button-1>", lambda e: command())  # Llamar a la función al hacer clic
    return canvas

# Ejemplo de uso en el contenedor principal
iniciar_boton = crear_boton_redondeado(contenedor, "Iniciar Sesión", abrir_pagina_iniciar, color="#1565c0")  # Color más oscuro
iniciar_boton.pack(pady=10)

registrar_boton = crear_boton_redondeado(contenedor, "Registrarse", abrir_pagina_registro)
registrar_boton.pack(pady=10)

# Iniciar el bucle principal de la ventana
ventana.mainloop()
