# Informe de Avance 3: Octubre 202x

## 14/10/2026

-  
En la clase de hoy 17/10 aplicamos los ultimos cambios a la caja siendo la funcion de la Radio.
Usando la microbit que controla todo como un transmisor y otra como receptor, se envia un pitido rapido para detectar que hay un intento incorrecto de abrir la caja, y un pitido lento para detectar que se abrio correctamente.

Se uso el nivel 7 de poder de la radio para la maxima distancia entre ellas.

La distancia maxima aprox. es de 70m.
La distancia maxima aprox. con obstaculos baja la distancia mientras mas obstaculos hayan.

Detectamo que pueden haber problemas incluso desde o 3 metros si hay muchas cosas entre medio.

Actualmente la caja tiene el sistema de apretura y cerradura con servomotor con el metodo de seguridad de pin de 6 digitos que cambia cada vez que se reinicia el codigo.
Al tener el circuito completo y la caja activada, el led rojo estara prendido continuamente hasta que se coloque el pin para abrirla, si el pin es correcto se apagara el led rojo, prendera el verde y se enviara el pitido lento al otro microbit. En caso de que se introduzca mal el pin, o se tarde mucho entre digitos el led rojo continuara encendido y el microbit receptor reproducira el pitido rapido para mostrar que fue intento de apretura incorrecto.
En este enlace encontrarás un [ejemplo como debe completarse el informe de avance](avance_ejemplo.md).
