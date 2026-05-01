# Estructura ros2

## Archivos

1. El CMakeLists.txt le dice a ros2 como compilar el nodo, sin esto no puedo hacer colcon build.

2. package.xml — declara el paquete ROS2 y sus dependencias (rclcpp, nav_msgs, tf2_ros, robmovil_msgs, etc.).

3. El entorno Docker — que ya tiene instalado ROS2, CoppeliaSim y los paquetes de la materia (robmovil_msgs, etc.).

Con los -v en el start-docker.sh hacemos que se monten dentro del container.

