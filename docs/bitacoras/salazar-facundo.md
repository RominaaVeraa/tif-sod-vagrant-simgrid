# Bitácora individual — Salazar, Facundo

**Rol:** Investigador/a de modelado y simulación (Avanzado)
**Entregables propios:** Pasos 1, 4 y 7 (Vagrantfile multimáquina, modelo SimGrid y plataformas XML, extrapolación a gran escala); además, video de demostración y presentación de la defensa.

## 21/10/2026 — Paso 1: clúster como código con Vagrant (en pares con Romina)

**Qué hice**
- Escribí el `Vagrantfile`: los trabajadores salen de la variable `N_WORKERS` (3 por defecto) y la misma definición genera el inventario de Ansible.
- Con Romina hicimos la primera puesta en marcha alternando quién escribía: `vagrant up`, `vagrant ssh`, `vagrant halt`, `vagrant destroy -f` y un nuevo `vagrant up` sin intervención.

**Qué aprendí**
- Que definir máquinas e inventario en un único lugar evita que se desincronicen cuando cambia la cantidad de trabajadores.

**Dificultades que encontré**
- `vagrant up` falló con `E_ACCESSDENIED`: VirtualBox tenía registrada una VM vieja cuyos archivos ya no existían, y Vagrant consulta los puertos de todas las VMs registradas.
- Las VMs quedaban colgadas en "Loading essential drivers". El anfitrión tiene Hyper-V activo y VirtualBox corre encima; con 2 vCPU el arranque se colgaba en forma intermitente.

**Cómo las resolví**
- Desregistramos la VM huérfana (con copia de seguridad del registro de VirtualBox).
- Probé por separado chipset, paravirtualización y cantidad de vCPU; solo 1 vCPU arrancó siempre, así que todas las VMs quedaron con 1 vCPU y la heterogeneidad se controla con `CPUQuota`.

## 21/10/2026 — Paso 4: modelo en SimGrid

**Qué hice**
- Escribí el modelo de SimGrid (S4U, Python; `src/agrolab/sim.py`) con la misma estructura de actores que el sistema real: un coordinador y un actor por trabajador, que importan el mismo módulo de estrategias (`strategies.py`) que usa el clúster.
- Escribí el generador de plataformas XML en estrella (`src/agrolab/platform.py`): un enlace para el coordinador y uno por trabajador.
- Verifiqué el modelo con una tarea y un nodo (`agrolab simulate trivial`): el tiempo simulado coincide con la cuenta a mano, con una diferencia relativa del orden de 10⁻⁷.

**Qué aprendí**
- Que para que simulación y realidad ejecuten "el mismo algoritmo" lo más seguro es que importen el mismo código, no reescribirlo dos veces.

**Dificultades que encontré**
- Modifiqué archivos del simulador mientras Sofía corría la campaña real, y la columna con el *hash* del código cambió en tres corridas aunque el código medido era el mismo.

**Cómo las resolví**
- Aparté mis cambios hasta el final de la campaña y Sofía repitió esas corridas, para que todo el conjunto de datos tuviera la misma versión.

## 21/10/2026 — Paso 6: calibración (en pares con Romina)

**Qué hice**
- Acompañé a Romina en `agrolab calibrate`, alternando quién escribía, y en llevar los valores medidos a las plataformas XML del modelo.

## 21/10/2026 — Paso 7: extrapolación a gran escala

**Qué hice**
- Extrapolé de 3 a 512 trabajadores (270 simulaciones, `agrolab simulate scale`), con la carga creciendo en proporción a los trabajadores (8N/3 tareas por lote) para mantener la utilización nominal del clúster real.
- Agregué 384, 640 y 768 trabajadores con 20 ms para ubicar el codo: el límite del coordinador es su enlace de red, cerca de 490 trabajadores; su CPU nunca pasó del 16 %.

**Qué aprendí**
- Que el simulador sirve para decidir (el orden de las estrategias no cambió en ninguna escala) pero no para dar números finos: la espera y el desbalance son las métricas donde más se nota lo que el modelo deja afuera.

**Dificultades que encontré**
- Grafana, con el tablero refrescándose cada 5 s, llegó a ocupar casi 1 GB del coordinador en dos horas y frenó las simulaciones.

**Cómo las resolví**
- Reinicié Grafana y no dejé el tablero abierto durante las simulaciones largas.

## 21/10/2026 — Video y presentación de la defensa

**Qué hice**
- Grabé el video de demostración (5:10, `docs/video/`): `vagrant up` desde un clon nuevo, idempotencia, apagar y encender, una corrida real con Grafana, la misma corrida simulada y los resultados.
- Armé la presentación de la defensa (13 diapositivas), con una parte para los pasos de cada integrante.

## Reflexión final desde el rol

Desde el modelado aprendí que lo difícil de simular no es escribir los actores sino decidir qué detalle del sistema real importa. Que el modelo de SimGrid importe el mismo módulo de estrategias que usa el clúster eliminó una fuente entera de discrepancias. La extrapolación me enseñó a separar decidir de predecir: el orden de las estrategias se mantuvo en todas las escalas, pero los valores absolutos lejos de la escala validada hay que leerlos como tendencias. Levantar el clúster con Vagrant me mostró que un laboratorio definido como código depende también del entorno donde corre: los dos problemas que más tiempo costaron, la VM huérfana registrada en VirtualBox y el cuelgue con 2 vCPU sobre Hyper-V, no estaban en nuestros archivos sino en la computadora anfitriona. Calibrar en pares con Romina me hizo ver cuánto depende el modelo de la calidad de las mediciones.
