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

### Source

Cada vez que abro una terminal, shell no tiene idea donde ros2 o mis paquees estan. 
El comando source lee un arhcivo setup y lo agrega a todos mis paths en la actual  sesion shell. Dura lo que dure esa terminal.

1. source /opt/ros/humble/setup.bash          # 1. ROS2 itself (humble)

2. source /root/ros2_ws/install/setup.bash    # 2. robmovil_msgs + sim_ros2_interface (pre-built by teacher)

3. source /root/ros2_ws/install/setup.bash    # 3. your modelo_omnidireccional (same file, now includes yours too)

### Colcon build

El paso 2 y 3 son las mismos archivos.Despues de hacer colcon build, mis paquetes se añaden a install.

### Como automatizarlo 
Si yo borrro (docker rm ros2_omni) y lo recreo, se pierden los packetes compilados.

### ./bashrc | grep source 
Buscamos archivos source dentro del .bashrc y vemos q no vamos a tener que tipear source manualmente porque el workspace (omni), ros2 se incluyen automaticamente,

.ttt

Nos los dan sin script de ros2.

1. Necesitamos que se traiga el simros2 plugin

2. que lea los valores de los encoders de las ruedas.

3. Publicarlos en /robot/encoders

4. Subscribirse a los comandos de velocidad de las ruedas

## Controlador a lazo abierto y cerrado.

Cualquier pose es alcanzable sin importar orientación, ya que es holonómico el robot. Esto simplifica los metodos de control y convergencia a pose objetivo.
