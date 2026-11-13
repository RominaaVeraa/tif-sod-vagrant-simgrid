# Bitácora individual: Quintana, Sofía

En este trabajo me tocó ser Analista de experimentación y datos, en el nivel inicial. Me encargué de los pasos 0, 5 y 8: el diagnóstico y el acta de roles, la campaña de experimentos en el clúster real y la prueba de reproducibilidad. También armé el cuaderno Jupyter con el análisis de los resultados y el README con las instrucciones para reproducir el trabajo. El paso 5 lo hice con la supervisión de Romina.

## 21/10 y 25/10/2026 — Paso 0: diagnóstico y acta de roles

Empecé organizando la autoevaluación de los tres integrantes y redacté el acta de roles (`docs/acta-de-roles.md`). También relevamos qué computadoras del grupo podían correr VirtualBox y Vagrant, y Facundo creó el repositorio en su cuenta de GitHub.

La única dificultad fue que Romina y Facundo quedaron en el mismo nivel (Avanzado). Lo resolvimos con la regla de la consigna: les asignamos los roles según las dimensiones en las que cada uno tuvo mayor puntaje.

## 21/10/2026 — Paso 5: experimentación en el clúster real

Generé la planilla de corridas con `agrolab plan` a partir de `experiments/params.toml`. El diseño combina 3 estrategias, 2 niveles de heterogeneidad y 3 latencias (1, 20 y 100 ms), con 5 repeticiones cada una. Las corridas van en orden aleatorio y con las mismas semillas en todas las configuraciones. Después ejecuté la campaña completa con `agrolab run`, supervisada por Romina: fueron 90 corridas en 66 minutos, sin fallas de comunicación ni corridas trabadas. Por último, exporté el consumo de las VMs desde Prometheus con `analysis/export_prometheus.py`.

Tuve un problema: tres corridas del primer bloque quedaron registradas con otro *hash* de código, porque se modificaron archivos del simulador mientras se ejecutaban. Las repetí al final de la campaña y obtuve tiempos casi iguales a los originales (36,41, 37,18 y 38,20 s contra 36,36, 37,18 y 38,27 s). Conservé las filas originales en `descartadas_runs.csv` y `descartadas_tasks.csv` para no perder ningún dato.

Aprendí que usar las mismas semillas en todas las configuraciones y aleatorizar el orden evita confundir el efecto de la estrategia con la carga sorteada o con una deriva del anfitrión (Jain, 1991).

## 21/10, 25/10 y 26/10/2026 — Paso 8: prueba de reproducibilidad

Hicimos dos pruebas. La primera fue el 21/10 en la computadora de desarrollo: destruimos el clúster, clonamos el repositorio desde GitHub en una carpeta nueva y seguimos solo el README. El `vagrant up` terminó en 18,4 minutos sin intervención, la segunda ejecución de Ansible dio `changed=0` y la corrida de prueba dio 40,41 s contra 40,50 s de la campaña original (40,03 s en SimGrid).

La segunda prueba, en otra computadora, la hice con Romina el 26/10 en su notebook, que no participó del desarrollo. No usamos la mía porque tiene 8 GB de RAM y no le queda memoria libre suficiente para las cuatro VM (3,2 GB) más el sistema. Seguimos solo el README y registramos cada paso en `docs/prueba-reproducibilidad.md`. El `vagrant up` desde un clon nuevo tardó 11,1 minutos, la idempotencia dio `changed=0`, Grafana y Prometheus respondieron y el ciclo de apagado y encendido funcionó. Con la misma semilla, las corridas reales dieron 49,7 s (Robo de trabajo), 52,0 s (Menos cargado) y 73,6 s (Round Robin), contra 40,5, 43,9 y 60,9 s de la campaña: el mismo orden de estrategias, pero unos 20 % más lentas.

Esta prueba mostró dos problemas que no habían aparecido antes. El primer intento, con la notebook a batería, dio tiempos casi el doble de largos porque Windows baja la frecuencia de la CPU; lo descartamos y repetimos todo desde cero con la notebook reiniciada y cargando. Además, `vagrant destroy` dejó carpetas vacías en `VirtualBox VMs` y el siguiente `vagrant up` falló con `VERR_ALREADY_EXISTS`; borramos las carpetas sobrantes y dejamos las dos fallas en los problemas conocidos del README.

Aprendí que una prueba en la misma computadora no alcanza, porque esa máquina ya tenía la imagen base descargada y los problemas de su entorno resueltos. También aprendí que el laboratorio se reproduce, pero los tiempos dependen del anfitrión: la calibración del simulador vale para la computadora donde se hizo.

## 21/10 y 26/10/2026 — Cuaderno de análisis y README

Armé el cuaderno `analysis/analisis.ipynb`, con tablas resumen (media, desvío e intervalo de confianza), ANOVA y las figuras del clúster real, de la validación y de la extrapolación. También redacté el README de reproducción paso a paso, con los problemas conocidos que fuimos encontrando.

Desde la experimentación aprendí que el diseño (factorial completo, repeticiones, mismas semillas y orden aleatorio) es lo que permite afirmar que una diferencia se debe a la estrategia y no al azar o al entorno. También aprendí que la reproducibilidad se cuida en los detalles: tres corridas se repitieron solo porque cambió la versión del código registrada, y una prueba en la misma computadora no alcanza para decir que el laboratorio es reproducible. Escribir el README pensando en alguien que no conoce el proyecto me obligó a entender la arquitectura completa, no solo mi parte.
