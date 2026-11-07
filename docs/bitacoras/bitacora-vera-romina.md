# Universidad de la Cuenca del Plata

## Ingeniería en Sistemas de Información

### Sistemas Operativos Distribuidos

### Bitácora de la Actividad de Evaluación 4

**Profesores:** Jara, Juan Emiliano e Ing. Quiñones Paola.

**Carrera:** Ingeniería en Sistemas de Información.

**Facultad:** Facultad de Ingeniería, Tecnología y Arquitectura.

**Cátedra:** Sistemas Operativos Distribuidos.

**Año:** Cuarto.

**Comisión:** "A" (única).

**Sede:** Posadas.

**Integrante:**

- Vera, Romina. DNI: 45.849.372.

**Fecha:** 10/11/2026.

---

# Bitácora: Vera, Romina

En este trabajo me tocó ser Ingeniera de infraestructura como código, en el nivel avanzado. Me encargué de los pasos 2, 3 y 6, que abarcaban Ansible, netem, CPUQuota, Prometheus y Grafana, el módulo común de estrategias con el coordinador y los trabajadores en Python, y la calibración y validación del simulador. También supervisé el paso 5 y armé el informe final.

## 21/10/2026

### Paso 1: primera puesta en marcha

Arrancamos con Facundo levantando el clúster juntos, turnándonos para escribir. Mientras uno tipeaba, yo iba anotando los comandos y los problemas que surgían, y después todo eso terminó en la sección de problemas conocidos del README.

### Paso 2: aprovisionamiento con Ansible

Acá escribí provisioning/site.yml y los cuatro roles (common, worker, coordinator y monitoring), que se ejecutan desde el coordinador con ansible_local. Después hice conditions.yml y los scripts lab-netem y lab-cpuquota, que son los que aplican la latencia y la heterogeneidad de cada bloque, y dejé configurados Prometheus y el tablero de Grafana.

Para estar segura de que el playbook era idempotente lo corrí dos veces con vagrant provision coord --provision-with ansible, y la segunda vez dio changed=0 en los cuatro nodos, que era lo que esperaba.

El primer obstáculo apareció enseguida: Windows no puede ser nodo de control de Ansible. La salida fue usar ansible_local, que instala Ansible dentro del coordinador y desde ahí configura a los trabajadores, así no hacía falta instalar nada en la máquina anfitriona.

Más adelante acompañé a Sofía en las corridas del paso 5, fijándome sobre todo en que netem y CPUQuota se aplicaran bien en cada bloque.

Lo que me llevo de este paso es que la idempotencia no se puede dar por hecha, hay que comprobarla. Y que si uno describe el estado al que quiere llegar en vez de escribir una lista de comandos, el playbook se puede correr cuantas veces haga falta sin romper nada.

### Paso 3: implementación de las estrategias

En este paso escribí el paquete agrolab. Tiene un generador de carga con semilla, las tres estrategias juntas en strategies.py, el protocolo de mensajes, el trabajador con sus dos hilos (uno para las comunicaciones y otro que ejecuta) y el coordinador, que usa un solo hilo con un bucle de eventos.

Para la mensajería elegimos ZeroMQ y no RabbitMQ. Con máquinas virtuales de 512 MB, un broker en Erlang se iba a comer la memoria, y además el robo de trabajo necesita que los trabajadores puedan hablar entre ellos sin pasar por el coordinador.

Como todavía no tenía las máquinas virtuales listas, probé el coordinador y los trabajadores en localhost con tests/smoke_local.py, y aproveché para escribir pruebas del módulo común en tests/test_strategies.py.

Esa prueba local falló al principio por un detalle bastante tonto: la función que arma los mensajes tenía un parámetro que se llamaba kind, y el mensaje de tarea también tenía un campo kind. Se arregló haciendo que el primer parámetro de pack() fuera solo posicional.

Algo que no sabía y me sorprendió es que el ROUTER de ZeroMQ descarta en silencio los mensajes que van a destinos que no conoce, a menos que se active ROUTER_MANDATORY. Cuando lo activé empezaron a aparecer los errores de conexión que antes pasaban desapercibidos. También entendí que, para no comparar relojes de máquinas distintas, lo mejor es medir todo con el reloj del coordinador y que los trabajadores solo informen cuánto tardaron.

### Paso 6: calibración y validación

Esta parte la volví a hacer con Facundo. Empecé midiendo la velocidad de cada máquina virtual con la tarea de referencia. Sin límite daba unas 53 000 unidades por segundo, con CPUQuota al 60 % bajaba a 33 000, al 50 % a 28 000 y al 30 % a 16 000. Con agrolab calibrate medí también cuánto tardaban en llegar mensajes de 0 a 256 KiB con 0, 1, 20 y 100 ms de netem.

Con esas mediciones simulé las 18 configuraciones del clúster usando las mismas semillas, con agrolab simulate validate. El makespan simulado quedó muy cerca del real, con una diferencia promedio de 1,1 % y un máximo de 6,8 %, y el orden de las estrategias fue el mismo en las seis condiciones. Donde sí hubo diferencia fue en la espera media, que llegó a desviarse un 28 %, porque el modelo no tiene en cuenta lo que cuesta en Python procesar cada mensaje.

Lo que más me hizo pensar fue el ancho de banda. Con 100 ms de latencia medíamos apenas 0,47 MB/s, y al principio parecía que esa era la capacidad de la red, pero no: era lo que rinde una conexión TCP con su ventana. Multiplicando el rendimiento por el tiempo de ida y vuelta daba una ventana casi igual en los cuatro niveles, entre 100 y 140 KiB.

Si cargábamos ese valor como capacidad de los enlaces, el simulador decía que a gran escala el enlace del coordinador se saturaba con muy poco tráfico, cosa que no tenía sentido. Con Facundo lo resolvimos poniendo en el modelo la capacidad medida sin netem para el enlace del coordinador, y el rendimiento de una sola conexión para el de cada trabajador.

## 26/10/2026

### Paso 8: prueba de reproducibilidad

La prueba de reproducibilidad le tocaba a Sofía, y para hacerla usamos mi notebook, que no había estado en el desarrollo. La hicimos juntas siguiendo solamente lo que decía el README. Cuando terminamos, sumé al informe los resultados y las dos fallas que encontramos.

## 21/10 al 26/10/2026

### Informe

En esos días fui juntando las secciones de cada integrante para armar el informe en formato APA, y revisé que las referencias estuvieran bien.

## Reflexión final desde el rol

Mirando todo el trabajo, me quedo con que la idempotencia hay que comprobarla y que pensar en términos de estado, y no de comandos, es lo que hace que el playbook se pueda correr una y otra vez.

Programar el coordinador y los trabajadores me hizo chocar de frente con problemas típicos de los sistemas distribuidos: que los relojes de máquinas distintas no se pueden comparar, o que un mensaje puede perderse sin que nadie se entere si el middleware no avisa.

La calibración fue lo que más me enseñó. Darme cuenta de que ese ancho de banda medido con tanta latencia no era la capacidad de la red, sino lo que rendía una conexión TCP, nos salvó de una predicción totalmente equivocada a gran escala.

Validar el simulador me mostró hasta dónde se le puede creer: acierta el makespan y el orden de las estrategias, pero con la espera no es tan confiable.

Y trabajar en pares con Facundo me ayudó mucho a entender cómo el modelo de SimGrid usa las mediciones que fuimos tomando.
