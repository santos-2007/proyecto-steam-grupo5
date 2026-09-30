# Informe de Avance 2: Septiembre 2026

## 2/9/2026
Durante esta fecha se analizó la posibilidad de incorporar LEDs a la caja fuerte como elemento adicional del proyecto. También se revisó y ajustó el código del micro:bit para mejorar el funcionamiento del sistema de apertura y cierre.

- **Tareas completadas:**
  - Análisis de la incorporación de LEDs al proyecto.
  - Revisión y ajuste del código del micro:bit.
  - Ajustes en el funcionamiento del servomotor.

- **Problemas encontrados y soluciones/alternativas propuestas:**
  - Se evaluó cómo incorporar los LEDs sin afectar el funcionamiento del servomotor y del micro:bit.
  - Se realizaron ajustes en el código para mejorar el control de la tranca.

- **Próximos pasos:**
  - Continuar con las pruebas del sistema.
  - Evaluar e implementar los LEDs en la caja.
 
  - Código ajustado:
```
  from microbit import *
import music

# Contraseña
clave = "ABAAB"
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
pin0.write_analog(cerrado)   # Aproximadamente 0°

while True:

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
            music.play(["C5:1", "E5:1", "G5:2"])

            pin0.write_analog(abierto)

            # Mantener abierta hasta que se presione B
            while True:

                if button_b.was_pressed():

                    # Sonido de cierre
                    music.play(["G5:1", "E5:1", "C5:2"])

                    # Cerrar la tranca
                    pin0.write_analog(cerrado)
                    sleep(500)

                    break

                sleep(20)

        else:
            display.show(Image.NO)
            sleep(1000)

        ingreso = ""
        display.clear()
```

  


## 9/9/2026
No hubo clases durante esta fecha, por lo que no se realizaron avances en el proyecto.

- **Tareas completadas:**
  - No se realizaron tareas debido a la ausencia de clases.

- **Problemas encontrados y soluciones/alternativas propuestas:**
  - No aplica.

- **Próximos pasos:**
  - Retomar el desarrollo del proyecto en la siguiente clase.

- **Imágenes o videos ilustrativos del avance:**
  - No aplica.

## 16/9/2026


- **Tareas completadas:**
- Mejoramos el codigo en base a una idea del profe, cual fue que cada vez flasheeamos el microbit se podia meter la contraseña que quisieras y se mantenga en todo el proceso hasta que sea haga un flasheo
-Se mejoro la cerradura de la caja para que el servomotor no se mueva y se logro   

- **Próximos pasos:**
agregar la luz led para confirmar que esta abierta o cerrada la caja fuerte




## 30/9/2026

  - **Tareas completadas:**
  - Probar y diseñar las luces leds para la caja fuerte.

  Nuevo código:
  ```
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




  ```

  - **Próximos pasos:**
    Incorporar las luces LED en la caja.


  - **Video:**
  - 
https://github.com/user-attachments/assets/6a8d7ba4-70cb-4d47-9eba-6de533140a34










