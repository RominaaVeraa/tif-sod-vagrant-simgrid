# Distribución de tareas — TIF AE4, Propuesta B

**Cátedra:** Sistemas Operativos Distribuidos · Ingeniería en Sistemas de Información · FITA
**Integrantes:** Quintana, Sofía · Salazar, Facundo · Vera, Romina

Los nueve pasos de la consigna se repartieron en partes iguales: tres pasos por
integrante, según el rol de cada uno (ver [`acta-de-roles.md`](acta-de-roles.md)).
Los dos pasos críticos se hicieron en pares, frente a la misma pantalla y
alternando quién escribía. Los entregables que no corresponden a un paso en
particular se asignaron aparte.

## 1. Pasos de la consigna

| Paso | Responsable | En pares con | Qué incluye | Evidencia en el repositorio |
|---|---|---|---|---|
| 0 — Diagnóstico y acta de roles | Quintana, Sofía | Todo el grupo (autoevaluación) | Autoevaluación, asignación de roles, relevamiento de computadoras | `docs/acta-de-roles.md` |
| 1 — Clúster como código con Vagrant | Salazar, Facundo | Vera, Romina | Vagrantfile multimáquina, primera puesta en marcha, ciclo `halt` / `up` / `destroy` | `Vagrantfile` |
| 2 — Aprovisionamiento con Ansible | Vera, Romina | — | Roles `common`, `worker`, `coordinator` y `monitoring`; netem y CPUQuota; Prometheus y Grafana; idempotencia (`changed=0`) | `provisioning/` |
| 3 — Implementación de las estrategias | Vera, Romina | — | Round Robin, Menos cargado y Robo de trabajo en un módulo común; coordinador y trabajadores con ZeroMQ; pruebas | `src/agrolab/strategies.py`, `coordinator.py`, `worker.py`, `tests/` |
| 4 — Modelo en SimGrid | Salazar, Facundo | — | Actores S4U con el mismo módulo de estrategias, plataformas XML, verificación con una tarea y un nodo | `src/agrolab/sim.py`, `platform.py` |
| 5 — Experimentación en el clúster real | Quintana, Sofía | Supervisión: Vera, Romina | Planilla de 90 corridas, ejecución de la campaña, exportación de métricas | `experiments/planilla.csv`, `experiments/results/real/` |
| 6 — Calibración y validación | Vera, Romina | Salazar, Facundo | Medición de CPU y red, ajuste del modelo, comparación real contra simulado en las 18 configuraciones | `experiments/calibration/`, `experiments/results/sim/validation*.csv` |
| 7 — Extrapolación a gran escala | Salazar, Facundo | — | Simulaciones de 3 a 768 trabajadores, límite del coordinador | `experiments/results/sim/scale*.csv` |
| 8 — Prueba de reproducibilidad | Quintana, Sofía | — | Prueba desde un clon nuevo y prueba en otra computadora | `docs/prueba-reproducibilidad.md` |

## 2. Entregables transversales

| Entregable | Responsable | Participación del resto |
|---|---|---|
| Informe APA (compilación, formato y referencias) | Vera, Romina | Cada integrante redactó la sección de sus pasos y su reflexión |
| Cuaderno Jupyter de análisis | Quintana, Sofía | — |
| README de reproducción | Quintana, Sofía | Romina y Facundo aportaron los problemas conocidos del paso 1 |
| Video de demostración | Salazar, Facundo | — |
| Presentación de la defensa | Salazar, Facundo | Cada integrante preparó las diapositivas de sus pasos |
| Bitácora individual | Cada integrante | — |

## 3. Resumen por integrante

| Integrante | Pasos | Entregables transversales |
|---|---|---|
| Quintana, Sofía | 0, 5 y 8 | Cuaderno Jupyter, README, bitácora |
| Salazar, Facundo | 1, 4 y 7 | Video, presentación, bitácora |
| Vera, Romina | 2, 3 y 6 | Informe, supervisión del paso 5, bitácora |

En la defensa, cada integrante presenta sus tres pasos, y todos deben poder
explicar la arquitectura completa.

## 4. Conformidad

| Integrante | Firma | Fecha |
|---|---|---|
| Quintana, Sofía | Sofía Quintana | 26/10/2026 |
| Salazar, Facundo | Facundo Salazar | 26/10/2026 |
| Vera, Romina | Romina Vera | 26/10/2026 |
