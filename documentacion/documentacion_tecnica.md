# Documentación Técnica

*Fecha: 4 de noviembre de 2026*

Este documento contiene la documentación técnica para el proyecto STEAM del curso **Laboratorio STEAM+** de la tecnicatura **Redes y Software** del **Instituto Superior Brazo Oriental** de **UTU**, año 2026.

# Proyecto: MAX-TAC 3000

## 1. Integrantes
- Mateo De Los Santos
- Joaquin Cedrez
- Ignacio Beares
- Mateo Artave

## 2. Descripción

**MAX-TAC 3000** es una caja fuerte electrónica controlada por una micro:bit. Se abre ingresando una clave de 5 pulsaciones con los botones **A** y **B**, y cuenta con una tranca accionada por un servomotor, luces LED que indican el estado de la caja (rojo = cerrada, verde = abierta) y alertas sonoras para aperturas correctas e intentos incorrectos.

Además de la caja, el proyecto incluye una **segunda micro:bit que funciona como receptor remoto**. Cada vez que alguien intenta abrir la caja, esta le envía por radio un aviso indicando si la clave fue correcta o incorrecta. La micro:bit receptora muestra un ícono en pantalla y emite un sonido distinto según el caso, de modo que el dueño pueda enterarse de lo que ocurre con su caja aunque no esté al lado de ella.

**Problema que se busca resolver:** una caja con cerradura común no avisa cuando alguien intenta abrirla, y el dueño solo se entera después. **Solución:** una caja con clave configurable, indicadores visuales y sonoros, y un sistema de notificación inalámbrica con confirmación de recepción (ACK) y reintentos, para que los avisos no se pierdan.

Características principales:
- La clave se define al encender (cada vez que se flashea o reinicia la micro:bit se puede elegir una nueva), por lo que no queda fija en el código.
- Si pasan más de 2 segundos entre pulsaciones, el ingreso se reinicia.
- La caja permanece abierta hasta que se presiona **B**, momento en que se cierra.
- Comunicación por radio entre ambas micro:bit (grupo 23, canal 7, potencia 7), con alcance aproximado de 70 m sin obstáculos.

## 3. Materiales
- 2 × micro:bit (una para la caja fuerte y otra para el receptor)
<img width="300" height="300" alt="images" src="https://github.com/user-attachments/assets/51182b96-7279-49e5-aca1-fcc03e2b4b88" />

- 1 × servomotor (tranca de la caja)
<img width="300" height="300" alt="ek1813-1" src="https://github.com/user-attachments/assets/1075e6e4-e4d3-4278-a5a0-364c35375b47" />

- 1 × LED rojo (caja cerrada)
- 1 × LED verde (caja abierta)
<img width="300" height="300" alt="red" src="https://github.com/user-attachments/assets/b4f21fbf-533d-4caf-af33-b750935ba380" />

- Cables de conexión (jumpers) y estaño para soldar
<img width="300" height="300" alt="cables" src="https://github.com/user-attachments/assets/5ac0b68d-a6c3-4f3f-a973-4c4d3705d5e6" />

- Fuente de alimentación / pilas para las micro:bit*
<img width="300" height="300" alt="bateria" src="https://github.com/user-attachments/assets/e3b663ed-aec8-41a5-ad7f-f05ee939f6db" />

- Caja de material *(completar: madera, cartón, plástico)* pintada de color negro
<img width="300" height="300" alt="caja" src="https://github.com/user-attachments/assets/88f54982-9321-4dca-bd4f-79e48f716ec6" />

- Computadora con el editor de Python de micro:bit (o Mu) y cable USB
<img width="300" height="300" alt="Kudle_OmniBook_14_CS_SoftHoneyDew_TNT_BL_WIN11_Catalog_Front_8103362" src="https://github.com/user-attachments/assets/d09e7210-443c-4df0-b65b-c5b0f85bfec5" />


## 4. Diseño Mecánico

**Funcionamiento mecánico.** La tranca de la caja está unida al eje de un servomotor. En la posición de **cerrado** (valor analógico 25, aproximadamente 0°) la tranca bloquea la tapa; en la posición de **abierto** (valor analógico 64) la libera. El servomotor solo se mueve cuando la clave es correcta o cuando se presiona **B** para cerrar.

**Armado (instrucciones de ensamblaje):**
1. Preparar la caja y verificar que la tapa cierre correctamente.
2. Fijar el servomotor en el interior, de manera que su brazo pueda bloquear y liberar la tapa. Durante el desarrollo se ajustó la cerradura para que el servomotor no se moviera por fuerza externa.
3. Colocar los LED rojo y verde en un lugar visible de la caja.
4. Ubicar la micro:bit con acceso a los botones **A** y **B** y a la pantalla.
5. Realizar las conexiones eléctricas (ver sección 5), soldar los circuitos y pegarlos junto con los bordes de la caja.
6. Pintar la caja de color negro y dejar todo preparado.
7. Probar servomotor, LED y alertas sonoras antes de cerrar definitivamente.

**Fotografías:** *[FOTOS PENDIENTES.]*

## 5. Diseño Electrónico

**Funcionamiento.** La micro:bit de la caja fuerte controla tres elementos externos y se comunica por radio con la micro:bit receptora:

| Componente | Conexión | Función |
|---|---|---|
| Servomotor | Pin 0 (señal PWM, período de 20 ms) | Abre y cierra la tranca |
| LED rojo | Pin 9 | Encendido = caja cerrada |
| LED verde | Pin 8 | Encendido = caja abierta |
| Botones A y B | Integrados en la micro:bit | Ingreso de la clave y cierre |
| Altavoz | Integrado en la micro:bit | Alertas sonoras |
| Radio | Integrada en la micro:bit | Comunicación con el receptor |

La micro:bit receptora no lleva componentes externos: usa su pantalla, su altavoz y la radio integrada.

**Diagrama de Tinkercad:** *[PENDIENTE.]*

**Observaciones de la radio:** con potencia 7 el alcance es de unos 20 m sin obstáculos, pero con obstáculos entre ambas micro:bit el alcance baja mucho y llegó a dar problemas incluso a 10 m. La solución propuesta fue reducir los obstáculos entre ambas.

## 6. Diseño Software

El software está escrito en **MicroPython** y se divide en dos programas: el de la **caja fuerte** y el del **receptor**. Ambos usan la misma configuración de radio para poder comunicarse.

### 6.1 Caja fuerte

Bloques principales:

1. **Configuración inicial:** se configura la radio, se coloca el servomotor en posición de cerrado y se enciende el LED rojo.
2. **Definición de la clave:** si no hay clave guardada, se muestra "CLAVE" y se esperan 5 pulsaciones de A/B. Esa secuencia se guarda en memoria como clave hasta el siguiente reinicio o flasheo.
3. **Ingreso de la clave:** cada pulsación se agrega a `ingreso` y registra la hora. Si pasan más de 2 segundos sin pulsar, se muestra una X y se reinicia el ingreso.
4. **Verificación:** al llegar a 5 pulsaciones se compara `ingreso` con `clave`.
   - **Correcta:** se avisa por radio, suena el tono de apertura, el servomotor pasa a posición abierta, el LED rojo se apaga y el verde se enciende. La caja queda abierta hasta presionar **B**, que la cierra e invierte los LED. Si no llegó la confirmación del receptor, se muestra una carita triste.
   - **Incorrecta:** se avisa por radio, se muestra una X y se reinicia el ingreso.
5. **Función `enviar_aviso(resultado)`:** arma un mensaje con formato `identificador|resultado` (por ejemplo `15230-3|CORRECTA`), lo envía y espera hasta 300 ms la respuesta `ACK|identificador`. Reintenta hasta 3 veces. Devuelve `True` si recibió la confirmación y `False` si no.

### 6.2 Receptor

1. Usa la misma configuración de radio que la caja fuerte.
2. Escucha mensajes, los separa por el carácter `|` y valida que el resultado sea `CORRECTA` o `INCORRECTA`.
3. Siempre responde con `ACK|identificador`, para que el emisor sepa que el aviso llegó.
4. Guarda el último identificador recibido (`ultimo_id`) para **procesar cada aviso una sola vez**: si la caja reenvía un mensaje porque no recibió el ACK, el receptor confirma de nuevo pero no repite la alerta.
5. Alerta: para `CORRECTA`, ícono de visto y un pitido corto; para `INCORRECTA`, ícono de X y dos pitidos largos de advertencia.

### 6.3 Código fuente: caja fuerte

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

# Posición inicial cerrado
pin0.set_analog_period(20)
pin0.write_analog(cerrado)

# LEDs: Rojo = pin 9, Verde = pin 8
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

        # Verificar al llegar a 5 caracteres
        if len(ingreso) == 5:

            if ingreso == clave:

                # Avisar que la contraseña es correcta
                recibido = enviar_aviso("CORRECTA")

                # Sonido de apertura y apertura de la tranca
                music.play(["G5:2"])
                pin0.write_analog(abierto)

                # Rojo apagado, verde encendido
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
                        music.play(["C5:2"])
                        pin0.write_analog(cerrado)
                        sleep(500)

                        # Verde apagado, rojo encendido
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

### 6.4 Código fuente: receptor

```python
from microbit import *
import music
import radio

# Configuración de radio: igual a la caja fuerte
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

## 7. Referencias y recursos

- Documentación de MicroPython para micro:bit: https://microbit-micropython.readthedocs.io
- Módulo `radio` de micro:bit: https://microbit-micropython.readthedocs.io/en/v2-docs/radio.html
- Módulo `music` de micro:bit: https://microbit-micropython.readthedocs.io/en/v2-docs/music.html
- Ejemplos de armado de proyectos: https://www.instructables.com
- Simulador de circuitos: https://www.tinkercad.com
- Informes de avance 2 (septiembre) y 3 (octubre) del equipo.

## 8. Otros

**Pruebas realizadas.** El 28/10/2026 se hizo una revisión general del servomotor, los LED, las conexiones y la radio sin encontrar problemas, y el proyecto quedó listo para presentarse.

**Limitaciones conocidas:**
- La clave se guarda solo en memoria: al reiniciar o reflashear la micro:bit hay que definirla de nuevo.
- El alcance de la radio depende mucho de los obstáculos entre las dos micro:bit.
- La clave es de 5 pulsaciones con solo dos botones (32 combinaciones posibles), por lo que ofrece una seguridad básica.


**Posibles mejoras futuras:** guardar la clave de forma persistente, bloquear la caja tras varios intentos fallidos, y alargar la clave o usar más entradas.

## Nota

En este enlace encontrarás un [ejemplo como debe completarse la documentación técnica](documentacion_tecnica_ejemplo.md).
