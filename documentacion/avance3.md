# Informe de Avance 3: Octubre 2026

## 14/10/2026
- **Tareas completadas:**
- Implementación de la comunicación por radio entre dos micro:bit.
- Configuración de la potencia de radio en nivel 7, con un alcance aproximado de 70 m sin obstáculos.
- Integración de alertas sonoras para intentos incorrectos y aperturas correctas.
- Implementación del servomotor, PIN de 6 dígitos y luces LED roja y verde.

- **Problemas encontrados y soluciones/alternativas propuestas:**
- La presencia de obstáculos reduce considerablemente el alcance de la radio, llegando a presentar problemas incluso a 3 m. Se propone reducir los obstáculos entre ambas micro:bit.

- **Próximos pasos:**.
- Verificar el correcto funcionamiento del sistema de seguridad, el servomotor, los LED y las alertas sonoras.
- Pintar la caja de color negro y dejar todo preparado.
- Soldar y pegar circuitos y bordes de caja para lograr el 100%.

- **Codigo nuevo:**
-CAJA FUERTE:
from microbit import *
import music
import radio

# Configuración de radio
radio.config(
    group=23,
    power=7,
    queue=3,
    length=32,
    channel=7
)
radio.on()

# Contraseña
clave = ""
ingreso = ""

# Tiempo de la última pulsación
ultimo_tiempo = 0

# Servomotor: valores originales
cerrado = 25
abierto = 64

# Identificador de avisos
contador = 0

# Imagen de candado abierto
candado_abierto = Image(
    "00000:"
    "09090:"
    "90009:"
    "99999:"
    "99999"
)

# Posición inicial cerrado
pin0.set_analog_period(20)
pin0.write_analog(cerrado)

# LEDs
# Rojo = Signal 9
# Verde = Signal 8

pin9.write_digital(1)  # Rojo encendido
pin8.write_digital(0)  # Verde apagado


# Enviar aviso y esperar confirmación
def enviar_aviso(resultado):
    global contador
    contador += 1

    identificador = str(running_time()) + "-" + str(contador)
    mensaje = identificador + "|" + resultado
    confirmacion = "ACK|" + identificador

    # Hasta 3 intentos
    for intento in range(3):
        radio.send(mensaje)

        inicio_espera = running_time()

        while running_time() - inicio_espera < 300:
            respuesta = radio.receive()

            if respuesta == confirmacion:
                return True

            sleep(10)

    return False


    while True:

    # Configurar contraseña al iniciar
    if clave == "":
        display.show("CLAVE")
        sleep(1000)
        display.clear()

        while len(ingreso) < 5:

            if button_a.was_pressed():
                ingreso += "A"
                display.show("A")
                sleep(150)
                display.clear()

            if button_b.was_pressed():
                ingreso += "B"
                display.show("B")
                sleep(150)
                display.clear()

        # Guardar contraseña
        clave = ingreso
        ingreso = ""

        display.show(Image.YES)
        music.play(["C5:1"])
        sleep(1000)
        display.clear()

    else:

        # Reiniciar ingreso si pasan más de 2 segundos
        if (
            len(ingreso) > 0
            and running_time() - ultimo_tiempo > 2000
        ):
            display.show(Image.NO)
            sleep(1000)
            ingreso = ""
            display.clear()

        # Botón A
        if button_a.was_pressed():
            if len(ingreso) < 5:
                ingreso += "A"
                ultimo_tiempo = running_time()
                display.show("A")
                sleep(150)
                display.clear()

        # Botón B
        if button_b.was_pressed():
            if len(ingreso) < 5:
                ingreso += "B"
                ultimo_tiempo = running_time()
                display.show("B")
                sleep(150)
                display.clear()

        # Verificar al llegar a 5 caracteres
        if len(ingreso) == 5:

            if ingreso == clave:

                # Avisar que la contraseña es correcta
                recibido = enviar_aviso("CORRECTA")

                # Sonido de apertura
                music.play(["G5:2"])

                # Abrir la tranca
                pin0.write_analog(abierto)

                # Rojo apagado y verde encendido
                pin9.write_digital(0)
                pin8.write_digital(1)

                # Avisar visualmente si no hubo confirmación
                if not recibido:
                    display.show(Image.SAD)
                    sleep(500)
                    display.clear()

                # Mantener abierta hasta presionar B
                while True:
                    if button_b.was_pressed():

                        # Sonido de cierre
                        music.play(["C5:2"])

                        # Cerrar la tranca
                        pin0.write_analog(cerrado)
                        sleep(500)

                        # Verde apagado y rojo encendido
                        pin8.write_digital(0)
                        pin9.write_digital(1)

                        break

                    sleep(20)

            else:

                # Avisar que la contraseña es incorrecta
                enviar_aviso("INCORRECTA")

                display.show(Image.NO)
                sleep(1000)
                display.clear()

            # Reiniciar ingreso
            ingreso = ""
            display.clear() 


# Configuración de radio: igual a la caja fuerte


    from microbit import *
    import music 
    import radio

    radio.config(
    group=23,
    power=7,
    queue=3,
    length=32,
    channel=7
    )

     radio.on()

    ultimo_id = ""

    while True:
    mensaje = radio.receive()

    if mensaje is not None:
        partes = mensaje.split("|")

        if len(partes) == 2:
            identificador = partes[0]
            resultado = partes[1]

            if resultado == "CORRECTA" or resultado == "INCORRECTA":

                # Confirmar recepción
                radio.send("ACK|" + identificador)

                # Procesar cada aviso una sola vez
                if identificador != ultimo_id:
                    ultimo_id = identificador

                    if resultado == "CORRECTA":
                        # Un pitido corto
                        display.show(Image.YES)
                        music.play(["C6:1"])
                        sleep(1000)
                        display.clear()

                    elif resultado == "INCORRECTA":
                        # Dos pitidos largos de advertencia
                        display.show(Image.NO)
                        music.play(["G4:4", "R:2", "G4:4"])
                        sleep(1000)
                        display.clear()

    sleep(10)
    


## 21/10/2026

**Tareas realizadas**

En este día implementamos las luz led a la caja fuerte que quedaron exitosamente complementadas, se arreglaron algunos errores de codigo en el tema del sintaxis, se arreglaron toques esteticos

## 28/10/2626

**informe**

En este día preparamos todo para presentar el proyecto hicimos check-in de todas las cosas como el servomotor,luces led, la conexiones de los cables, probamos el funcionamiento de la radio y no tuvimos ningun problema asi que, quedamos pronto para presentar el proyecto
            
En este enlace encontrarás un [ejemplo como debe completarse el informe de avance](avance_ejem from microbit import *
