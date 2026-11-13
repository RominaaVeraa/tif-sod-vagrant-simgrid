# Prueba de reproducibilidad (paso 8)

Responsable: Quintana, Sofía (rol Inicial). Objetivo: levantar el laboratorio en una
computadora que **no** participó del desarrollo siguiendo **solamente** el
`README.md`, y anotar cada paso que falló y cómo se corrigió.

La prueba se hizo en una notebook de Vera, Romina, que no participó del
desarrollo, con Quintana y Vera presentes. La computadora de Quintana tiene 8 GB de RAM, pero no le queda memoria libre
suficiente para las cuatro VM (3,2 GB) más el sistema.

## Computadora usada

| Dato | Valor |
|---|---|
| Equipo | Notebook Dell Inspiron 5575 |
| Sistema operativo y versión | Windows 11 Home 22H2 (10.0.22621) |
| Procesador / núcleos | AMD Ryzen 7 2700U, 4 núcleos y 8 hilos, 2,2 GHz |
| RAM | 16 GB |
| ¿Hyper-V, WSL2 o Docker Desktop activos? | No |
| Versión de VirtualBox | 7.1.6 |
| Versión de Vagrant | 2.4.9 (sin instalar, extraído del instalador oficial) |
| Fecha de la prueba | 26/10/2026 (un intento previo el 25/10, descartado; ver abajo) |
| Alimentación | Enchufada, plan de energía Equilibrado |

## Registro paso a paso

| # | Paso del README | Comando | ¿Funcionó? | Tiempo | Qué pasó / cómo se corrigió |
|---|---|---|---|---|---|
| 1 | Clonar el repositorio | `git clone …` | Sí | 3,5 s | Los scripts quedaron con finales de línea LF gracias a `.gitattributes`. |
| 2 | Levantar el clúster | `vagrant up` | Sí, al segundo intento | 11,1 min | La primera vez falló al nombrar `agrolab-w1` (`VERR_ALREADY_EXISTS`): el `vagrant destroy` del intento del 25/10 había dejado carpetas `agrolab-*` en `VirtualBox VMs` (falla 2). Se borraron y el segundo `vagrant up` terminó sin intervención, con `failed=0` en los cuatro nodos. La box (0,6 GB) se descargó en esta prueba. Aviso no bloqueante: Guest Additions 7.2.4 de la box contra VirtualBox 7.1. |
| 3 | Estado de las VM | `vagrant status` | Sí | 12 s | Las cuatro máquinas en `running`. |
| 4 | Idempotencia | `vagrant provision coord --provision-with ansible` | Sí | 1,3 min | `changed=0` en coord, w1, w2 y w3. |
| 5 | Grafana | abrir http://192.168.56.10:3000 | Sí | — | Grafana y Prometheus responden (HTTP 200). |
| 6 | Una corrida real | `vagrant ssh coord -c "agrolab run --only L20-H1-ws-r1 --out /tmp/prueba"` | Sí | 1,2 min | Makespan 49,69 s (campaña: 40,50 s). Se agregaron las otras dos estrategias con la misma semilla: Menos cargado 52,03 s (campaña 43,92 s) y Round Robin 73,60 s (campaña 60,93 s). Mismo orden de estrategias, unos 20 % más lentas. |
| 7 | Simulación equivalente | `vagrant ssh coord -c "agrolab simulate trivial"` | Sí | 8 s | Diferencia relativa con la cuenta a mano: 1,19 × 10⁻⁷. Con `agrolab simulate one … --hetero H1 --latency 20 --seed 1000`: Robo 40,03 s, Menos cargado 41,89 s y Round Robin 61,50 s. |
| 8 | Apagar y volver a encender | `vagrant halt` y `vagrant up` | Sí | 0,6 + 3,2 min | Las cuatro máquinas volvieron a `running` y Grafana respondió. Una corrida de Robo de trabajo justo después del arranque dio 66,70 s, más lenta que antes del reinicio. |
| 9 | Destruir | `vagrant destroy -f` | Sí, con un residuo | 0,5 min | Las VMs se borraron, pero quedaron las carpetas vacías `agrolab-*` en `VirtualBox VMs` (falla 2, reproducida). |

## Fallas encontradas

1. **Medir a batería (intento del 25/10, descartado).** Con la notebook a batería,
   Windows baja la frecuencia de la CPU: la misma corrida de Robo de trabajo dio
   74,91 s. Al enchufarla, el cargador no llegaba a cargar la batería y las corridas
   siguieron degradadas (Round Robin 1014 s). Además, al volver a encender después
   de un `vagrant halt`, w2 se colgó en el arranque del kernel y hubo que
   reiniciarla (`VBoxManage controlvm agrolab-w2 reset`). Ese intento no se usa para
   comparar. La prueba del 26/10 se repitió desde cero con la notebook reiniciada y
   cargando.
2. **`vagrant destroy` deja carpetas en `VirtualBox VMs`.** VirtualBox 7.1 crea
   certificados VRDE en la carpeta de cada VM y `vagrant destroy` no los borra.
   El siguiente `vagrant up` falla con `VERR_ALREADY_EXISTS` al nombrar `agrolab-w1`.
   Solución: borrar las carpetas `agrolab-*` vacías de `VirtualBox VMs`.

## Cambios que hubo que hacer al repositorio

| Falla | Archivo corregido | Commit |
|---|---|---|
| 1. Medir a batería | `README.md` (problemas conocidos y requisitos) | Commit de entrega |
| 2. Carpetas que deja `vagrant destroy` | `README.md` (problemas conocidos) | Commit de entrega |

Los resultados de las corridas de esta prueba están en
`experiments/results/real/repro_notebook_runs.csv`.

## Conclusión

El laboratorio se levanta en otra computadora siguiendo solo el README y el
orden de las estrategias se mantiene. Los valores absolutos dependen del
anfitrión: la notebook es más lenta que la computadora de desarrollo, sobre la que
se calibró el simulador, y por eso las corridas reales dan unos 20 % más que
la campaña y que SimGrid. Para comparar números finos con el simulador habría que
calibrarlo de nuevo en la notebook (`agrolab calibrate`).

## Prueba previa en la computadora de desarrollo

Antes de la prueba en otra computadora, se clonó el repositorio desde GitHub
en una carpeta nueva de la misma computadora de desarrollo y se levantó desde
cero siguiendo el README (ver el informe, paso 8). Esa prueba no reemplaza a
esta: la computadora de desarrollo ya tenía la box descargada y los problemas
de su entorno resueltos.
