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

## Codigo
from microbit import *
import music

# Contraseña
clave = ""
ingreso = ""

# Tiempo de la última pulsación
ultimo_tiempo = 0

cerrado = 25
abierto = 64

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

# Caja cerrada al iniciar
pin9.write_digital(1)   # Rojo encendido
pin8.write_digital(0)   # Verde apagado

while True:

  # Si no hay una clave configurada
  if clave == "":

      display.show("CLAVE")
      sleep(1000)
      display.clear()

      # Ingresar nueva clave
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

      # Guardar la clave ingresada
      clave = ingreso
      ingreso = ""

      display.show(Image.YES)
      music.play(["C5:1"])
      sleep(1000)
      display.clear()

  else:

      # Si pasaron más de 2 segundos entre pulsaciones
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

      # Verificar automáticamente al llegar a 5 caracteres
      if len(ingreso) == 5:

          if ingreso == clave:

              # Sonido de apertura
              music.play(["G5:2"])

              # Abrir la tranca
              pin0.write_analog(abierto)

              # Rojo apagado
              pin9.write_digital(0)

              # Verde encendido
              pin8.write_digital(1)

              # Mantener abierta hasta que se presione B
              while True:

                  if button_b.was_pressed():

                      # Sonido de cierre
                      music.play(["C5:2"])

                      # Cerrar la tranca
                      pin0.write_analog(cerrado)
                      sleep(500)

                      # Verde apagado
                      pin8.write_digital(0)

                      # Rojo encendido
                      pin9.write_digital(1)

                      break

                  sleep(20)

          else:
              display.show(Image.NO)
              sleep(1000)

          # Reiniciar el ingreso
          ingreso = ""
          display.clear()

            sleep(1000)

            ingreso = ""
            display.clear()
