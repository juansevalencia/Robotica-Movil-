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

El robot tiene que seguir una trayectoria de 2m cuadrada, seguir las esquinas siempre mirando para afuera. La trayectoria es fija, los waypoints no cambian, pero la trayectoria real entre ellos si puede variar por errores de movimiento. Por eso usamos el controlador paa corregir.

v = Kp.e

e = Xgoal - Xrobot

Kp -> controla q tan agresivo es el mov, si es chico el Kp -> Robot lento, si es grande -> robot rapido pero puede pasarse del obj.

El error se computa en cuestion de las cordenadas del mapa y se rota en su angulo.

error_x_robot =  cos(θ) × error_x_map + sin(θ) × error_y_map

error_y_robot = -sin(θ) × error_x_map + cos(θ) × error_y_map

En vez de tener un unico objetivo, tiene muchos chicos. Con position_tolerance y angle_tolerance, pasa dirctamente al otro.

atan2(y,x) para que apunte por fuera del centro.

Usamos 20 wayponts por recorrido entre esquina y esquina

3. Implementar un nodo de ROS el cual debe:
a. Publicar comandos de velocidad de control (lineal y angular) de manera peri´odica a trav´es
del t´opico /robot/cmd vel que permitan la convergencia hac´ıa una pose objetivo.
b. Utilizar la estimaci´on de la pose actual provista como la transformaci´on map → base link
(mapTR) como feedback del m´etodo.
c. Redefinir la pose objetivo actual si se considera que el robot se encuentra lo suficientemente
cerca (Pursuit-Based goal selection).


## EKF - Filtro de Kalman.

1. Implementar un nodo de ROS2 que interprete la informaci´on provista por el sensor LiDAR,
debe:
a. Recibir los escaneos por el tópico /robot/front laser/scan y detectar postes que se
encuentren frente al robot por medio del método de clusterizaci´on.
b. Publicar mensajes de tipo robmovil msgs/msg/LandmarkArray por el t´opico /landmarks.
La información publicada de las referencias (landmarks) debe estar en relaci´on al marco
de coordenadas del robot.





2. Modelar el estado⃗x, las entradas de control⃗u, las mediciones⃗z, el ruido del actuador⃗w, el ruido
del sensor⃗v, el modelo de movimiento f (⃗x,⃗u,⃗w), el modelo de sensado h(⃗x,⃗v) y los respectivos
Jacobianos.
3. Proponer matrices de covarianza iniciales para el modelo de movimiento y de sensado de manera
que reflejen una mayor incertidumbre al momento de predecir la pose. Se espera que en la etapa
de correcci´on la informaci´on proveniente de los sensores sea considerada “m´as confiable”.
4. Implementar un nodo de ROS2 que aplique el modelo del filtro:
6
a. Considerar que el mapa de postes se encuentra en referencia al marco de coordenadas del
mapa. Esto se debe a que, en esta oportunidad, el mapa no es construido a partir del
primer sensado del l´aser (a diferencia a lo visto durante la cursada).
b. Utilizar la estimaci´on odom´etrica publicada por el t´opico /robot/odometry como entrada
de control del m´etodo.
c. Se debe publicar una estimaci´on refinada de la pose del robot a trav´es de una transfor-
maci´on map → base link ekf (mapTRekf ).
5. Realizar el seguimiento de la trayectoria planteada anteriormente utilizando la estimaci´on refi-
nada de la pose y el m´etodo de lazo cerrado.