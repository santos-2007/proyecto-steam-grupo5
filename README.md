# Caja Fuerte

## Descripción
El proyecto es una caja fuerte con 2 metdoos de seguridad, el primer metodo seria una contraseña tipica como la mayoria de cajas fuertes y el segundo se activaria si la constraseña es incorrecta, es basicamente un sensor de proximidad que avisa ante forcejeos o cualquier intento de obviar la seguridad

*Nota:* Este proyecto se realiza durante el curso **Laboratorio STEAM+** de la
tecnicatura **Redes y Software** del [**Instituto Superior de Brazo Oriental**] de **UTU**.

## Integrantes
- Mateo De Los Santos
- Mateo Artave
- Joaquin Cedrez
- Ignacio Beares

## Documentación
- [Informe de Avance - Agosto 2026](documentacion/avance1.md)
- [Informe de Avance - Septiembre 2026](documentacion/avance2.md)
- [Informe de Avance - Octubre 2026](documentacion/avance3.md)
- [Documentación técnica](documentacion/documentacion_tecnica.md)

## Código fuente
- Agregar enlace a la carpeta de GitHub donde se haya guardado el código fuente]
- Ejemplo: `codigo/programa1.py`

## Presentación Final
- Agregar enlace a la carpeta de GitHub donde se hayan guardado diapositivas, videos, etc usados en la presentación final
- Ejemplo:
- `presentacion/presentacion-final.pptx`
- `presentacion/video.mov`

-CAJA FUERTE:

```python
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

# Servomotor
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
pin9.write_digital(1)
pin8.write_digital(0)


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


# Programa principal
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


```

```python
# Configuración de radio: igual a la caja fuerte

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

```
      ingreso = ""
            display.clear()
