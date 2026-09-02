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

from microbit import *
import music

# Contraseña
clave = "ABAAB"
ingreso = ""

# Tiempo de la última pulsación
ultimo_tiempo = 0

# Imagen de candado abierto
candado_abierto = Image(
    "00000:"
    "09090:"
    "90009:"
    "99999:"
    "99999"
)

# CONFIGURACIÓN DEL SERVO
pin0.set_analog_period(20)

# Servo cerrado
pin0.write_analog(20)

while True:

    # Tiempo máximo entre pulsaciones
    if len(ingreso) > 0 and running_time() - ultimo_tiempo > 2000:
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

    # Contraseña completa
    if len(ingreso) == 5:

        if ingreso == clave:

            # Mostrar candado abierto
            display.show(candado_abierto)

            # Sonido de apertura
            music.play(["C5:1", "E5:1", "G5:2"])

            # ABRIR SERVO
            pin0.write_analog(77)

            # Borrar contraseña
            ingreso = ""

            # Esperar a que se suelte B
            while button_b.is_pressed():
                sleep(20)

            # ESPERAR PARA CERRAR
            while True:

                if button_b.was_pressed():

                    # Sonido de cierre
                    music.play(["G5:1", "E5:1", "C5:2"])

                    # CERRAR SERVO
                    pin0.write_analog(20)

                    sleep(500)

                    display.clear()
                    break

                sleep(20)

        else:

            # Contraseña incorrecta
            display.show(Image.NO)
            sleep(1000)

            ingreso = ""
            display.clear()
