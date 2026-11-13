# Acta de roles — TIF AE4, Propuesta B

**Cátedra:** Sistemas Operativos Distribuidos · Ingeniería en Sistemas de Información · FITA
**Docentes:** Jara, Juan Emiliano · Ing. Quiñones, Paola
**Ciclo lectivo:** 2026 · Comisión "A" · Sede Posadas

## 1. Autoevaluación de experiencia

Escala por dimensión: 0 = nunca lo usé · 1 = lo usé con guía · 2 = lo uso con cierta autonomía · 3 = lo uso con autonomía y puedo explicarlo a otros.

| Dimensión | Quintana, Sofía | Salazar, Facundo | Vera, Romina |
|---|:-:|:-:|:-:|
| Linux y línea de comandos | 1 | 3 | 2 |
| Programación (Python/C/JS, concurrencia, sockets) | 1 | 3 | 3 |
| Redes (TCP/IP, puertos, NAT, DNS, diagnóstico) | 1 | 2 | 2 |
| Virtualización y contenedores | 1 | 3 | 3 |
| Conceptos de SOD (Ejes 1 y 2) | 1 | 2 | 2 |
| **Total (/15)** | **5** | **13** | **12** |
| **Nivel** | Inicial | Avanzado | Avanzado |

Referencia: 0–5 Inicial · 6–10 Intermedio · 11–15 Avanzado.

> Cada integrante completa su columna de forma individual. Si dos integrantes
> quedan en el mismo nivel, los roles se asignan por las dimensiones de mayor
> puntaje (fortalezas complementarias).

## 2. Asignación de roles

Los nueve pasos de la consigna (0 a 8) se reparten tres por integrante. El
detalle de cada paso y de los entregables transversales está en
[`distribucion-de-tareas.md`](distribucion-de-tareas.md). Como Salazar y Vera quedaron
en el nivel Avanzado, los roles entre ellos se asignaron por las dimensiones de
mayor puntaje (regla de la sección 1).

| Rol | Nivel | Integrante | Pasos a cargo | Entregables propios |
|---|---|---|---|---|
| Investigador/a de modelado y simulación | Avanzado | Salazar, Facundo | 1, 4 y 7 | Vagrantfile multimáquina, modelo SimGrid (S4U Python) y plataformas XML, extrapolación a gran escala, video de demostración y presentación de la defensa |
| Ingeniero/a de infraestructura como código | Avanzado | Vera, Romina | 2, 3 y 6 | Playbooks y roles de Ansible, emulación de red con netem y CPUQuota, Prometheus y tablero de Grafana, módulo común de estrategias, coordinador y trabajadores en Python, calibración y validación del simulador, supervisión del paso 5 y compilación del informe |
| Analista de experimentación y datos | Inicial | Quintana, Sofía | 0, 5 y 8 | Diagnóstico y acta de roles, planilla de corridas y campaña real, prueba de reproducibilidad en otra computadora, cuaderno Jupyter y README de reproducción |

## 3. Pasos críticos en pares

| Paso | Integrantes | Modalidad |
|---|---|---|
| Paso 1 — primera puesta en marcha del clúster con Vagrant | Salazar (Avanzado) + Vera (Avanzado) | misma terminal o pantalla compartida, alternando quién escribe |
| Paso 6 — calibración del simulador | Vera (Avanzado) + Salazar (Avanzado) | ídem |

## 4. Reglas acordadas

- Cada rol es responsable de sus entregables, pero todo el grupo debe poder explicar la arquitectura completa en la defensa.
- Cada integrante registra su bitácora semanal en `docs/bitacoras/`.
- Todo cambio de código, configuración o datos pasa por el repositorio.

## 5. Conformidad

| Integrante | Firma | Fecha |
|---|---|---|
| Quintana, Sofía | Sofía Quintana | 26/10/2026 |
| Salazar, Facundo | Facundo Salazar | 26/10/2026 |
| Vera, Romina | Romina Vera | 26/10/2026 |

Validación del equipo docente: ______________________ Fecha: ____/____/2026
