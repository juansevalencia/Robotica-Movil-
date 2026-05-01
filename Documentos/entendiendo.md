# Entendiendo que nos piden -> como lo resolvemos

## Modelo Cinemático

El modelo cinemático nos dice (evalua) como se comporta el robot en funcion de sus entradas. Si tiene x,y, theeta como variable de posicion y w , v como control de velocidades angulares y lineales. La combinación de ellos va a ser el modelo cinematico.

### Nos piden:

1. Implementar un nodo de ROS2, el cual debe:


a. "Recibir comandos de velocidad lineal y angular geometry msgs/msg/Twist por el tópico
/robot/cmd vel"  = lee los mensajes de tipo twist que son las velocidades angulares y lineales del robot.
Los mensajes twist tienen v ang y lin

"y publicar velocidades de rotación por cada rueda a través de los tópicos
anteriormente descriptos." = 


El nodo de ros2 publica en topicos, tiene subscriptores (reciben info leyendo sensores o lo q sea), procesa (callbacks), envia info (publishers mediante topics).


"b. Utilizar las mediciones de los encoders para la estimación de la pose actual del robot (odometrı́a)
en relación al marco inercial (marco odom)."

leemos de los encoders para la etimacion de la pose actual.

c. Publicar la información odométrica como una transformación odom → base link (odom TR )
por el tópico /tf y a su vez como un mensaje de tipo nav msgs/msg/Odometry por el tópico
/robot/odometry.

odom es el marco padre, el mundo fijo donde arranco el robot.
Base link es el marco hijo, el centro del robot.

¿Donde esta base_link visto desde odom? eso responde. 

Todo esto es asi porque yo en mis calculos con los ticks calculo la velocidad del robot en base a las cordenadas del propio robot, roto con theeta y calculo la diferencia y posteriormente la acumulación en base a la pose global.

Y usamos tf que es donde se acumulan todos estos frames e arbol de frames.

## 