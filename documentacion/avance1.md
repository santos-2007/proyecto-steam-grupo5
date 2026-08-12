# Informe de Avance 1: Agosto 2026

## 5/8/2026
- La idea principal es hacer una caja fuerte con contraseña y sensor de movimiento, básicamente una caja fuerte con 2 métodos de seguridad, el primero sería la contraseña y el segundo funcionaria solo si la contraseña es incorrecta, si la contraseña no es correcta se activa el sensor de movimiento para detectar si la persona está intentando abrir la caja de otra manera y si detecta movimiento se acciona una alarma.

- Se armo el equipo entre: Mateo De Los Santos, Joaquin Cedrez, Ignacio Beares, Mateo Artave

- Ideas:
Actualmente estamos viendo si hacer el modelo con microbit de manera convencional o usar los kits de robótica de lego como el lego SPIKE y los respectivos sensores de esos kits

- Ideas:
Definimos que no vamos a usar los kits de lego, vamos a hacerlo con microbit y arduino, nuestra idea actual es usar un servomotor como tranca por la parte de afuera, cuando la contraseña es correcta se mueve del camino de la puerta y se puede abrir

- Ideas:
Tecnologías y materiales: Ahora mismo no tenemos las tecnologías y materiales definidos debido a que estamos analizando si hacerlo con los kits de lego spike antes mencionados o de manera convencional con microbit y arduino


<img width="900" height="1600" alt="2520e104-5d61-4ddb-b2d0-7b458af11b3d" src="https://github.com/user-attachments/assets/9cf06b5f-4aaf-4bc0-a142-3be024a35530" />

## 12/8/2026
- En este dia empezamos con el codigó del microbit que seria para ejecutar la contraseña de nuestra caja fuerte (dejamos el avance más abajo), esto puede ir cambiando según vayamos avanazando.
- Estamos pensando usar una tarjeta de expanción para la microbit porque al momento de conectar el servomotor que no haga un falso contacto con otra conexión y por comodidad
- ```from microbit import *
import music

#Contraseña
clave = "ABAAB"
ingreso = ""

#Tiempo de la última pulsación
ultimo_tiempo = 0

#Imagen de candado abierto
candado_abierto = Image(
    "00000:"
    "09090:"
    "90009:"
    "99999:"
    "99999"
)

#Posición inicial cerrado
pin0.set_analog_period(20)
pin0.write_analog(26)   # Aproximadamente 0°

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
            display.show(candado_abierto)

            # Abrir la tranca
            pin0.write_analog(77)   # Aproximadamente 90°
            sleep(3000)

            # Cerrar nuevamente
            pin0.write_analog(26)   # Aproximadamente 0°
            sleep(500)

        else:
            display.show(Image.NO)
            sleep(1000)

        ingreso = ""
        display.clear()
- 

<img width="900" height="1600" alt="512746a4-4f95-4de5-919f-d0c9f7fd9957" src="https://github.com/user-attachments/assets/1c8767e1-7b96-4a60-a90b-5ae34595efe5" />

