# CESE - Sistemas Operativos de Tiempo Real II

## TP3 - Actividad 02 - Rate Monotonic Scheduling

## 1. Alcance y método

Se consideran tareas periódicas, independientes, totalmente apropiables, sin bloqueos ni sobrecarga del sistema operativo, liberadas inicialmente en `t = 0`, con `D_i = T_i`. El planificador ejecuta siempre la tarea lista de mayor prioridad.

Rate Monotonic (RM) asigna prioridades fijas inversamente proporcionales al período: cuanto menor es `T_i`, mayor es la prioridad.

El factor de uso y el hiperperíodo se calculan como:

\[
U=\sum_{i=1}^{n}\frac{C_i}{T_i},\qquad
H=\operatorname{mcm}(T_1,T_2,\ldots,T_n)
\]

### 1.1 Test suficiente de Liu y Layland

\[
U\le U_{LL}(n)=n\left(2^{1/n}-1\right)
\]

Los límites utilizados son `U_LL(3) = 0,7798` y `U_LL(4) = 0,7568`. Si se cumple la desigualdad, el sistema queda garantizado. Si no se cumple, el resultado es inconcluso y se aplica el análisis exacto de tiempo de respuesta.

### 1.2 Análisis exacto de tiempo de respuesta

Para cada tarea ordenada por prioridad se itera:

\[
R_i^{(k+1)}=C_i+\sum_{j\in hp(i)}
\left\lceil\frac{R_i^{(k)}}{T_j}\right\rceil C_j
\]

Se parte de `R_i^(0) = C_i`. La tarea está garantizada si la iteración converge a `R_i <= D_i`.

### 1.3 Período secundario

RM es dirigido por prioridades y eventos, por lo que no posee un período secundario o trama como el ejecutivo cíclico. Para completar el campo solicitado por la consigna se informa `mcd(T_i)` como grilla temporal de análisis; no constituye una restricción del algoritmo RM.

## 2. Resumen de resultados

| Sistema | Factor de uso | Hiperperíodo | Grilla `mcd(T_i)` | Test de Liu-Layland | Test exacto | Resultado final |
| :--: | --: | --: | --: | :--: | :--: | :--: |
| 1 | `0,9000` | 20 | 1 | No garantiza | Cumple | Planificable |
| 2 | `0,4778` | 90 | 2 | Cumple | Cumple | Planificable |
| 3 | `0,7977` | 1320 | 1 | No garantiza | Cumple | Planificable |
| 4 | `0,9000` | 120 | 1 | No garantiza | Cumple | Planificable |

> Fallar el límite de Liu-Layland no significa que el sistema sea inviable; sólo obliga a realizar un test más preciso.

## 3. Sistema 1

### 3.1 Prioridades y parámetros

| Prioridad | Tarea | `C` | `T = D` | Utilización |
| :--: | :--: | --: | --: | --: |
| P1 | T1 | 1 | 4 | `0,25` |
| P2 | T2 | 2 | 5 | `0,40` |
| P3 | T3 | 5 | 20 | `0,25` |

\[
U_1=0,90,\qquad H_1=20,\qquad \operatorname{mcd}=1
\]

Como `0,90 > U_LL(3) = 0,7798`, el test suficiente no garantiza el sistema.

### 3.2 Test exacto

| Tarea | Sucesión de tiempos de respuesta | Resultado final |
| :--: | :-- | :--: |
| T1 | `1 -> 1` | `R1 = 1 <= 4` |
| T2 | `2 -> 3 -> 3` | `R2 = 3 <= 5` |
| T3 | `5 -> 9 -> 12 -> 14 -> 15 -> 15` | `R3 = 15 <= 20` |

El Sistema 1 es planificable por RM.

### 3.3 Diagrama de Gantt, `0 <= t < 20`

| Intervalo | `[0,1)` | `[1,3)` | `[3,4)` | `[4,5)` | `[5,7)` | `[7,8)` | `[8,9)` | `[9,10)` |
| :-- | :--: | :--: | :--: | :--: | :--: | :--: | :--: | :--: |
| CPU | T1.1 | T2.1 | T3.1 | T1.2 | T2.2 | T3.1 | T1.3 | T3.1 |

| Intervalo | `[10,12)` | `[12,13)` | `[13,15)` | `[15,16)` | `[16,17)` | `[17,18)` | `[18,20)` |
| :-- | :--: | :--: | :--: | :--: | :--: | :--: | :--: |
| CPU | T2.3 | T1.4 | T3.1 | T2.4 | T1.5 | T2.4 | Idle |

T3.1 completa en `t = 15`, antes de su plazo `t = 20`. En `t = 20` comienza el siguiente hiperperíodo.

## 4. Sistema 2

### 4.1 Prioridades y parámetros

| Prioridad | Tarea | `C` | `T = D` | Utilización |
| :--: | :--: | --: | --: | --: |
| P1 | T1 | 1 | 6 | `0,1667` |
| P2 | T2 | 2 | 10 | `0,2000` |
| P3 | T3 | 2 | 18 | `0,1111` |

\[
U_2=\frac{43}{90}=0,4778,\qquad H_2=90,\qquad \operatorname{mcd}=2
\]

Como `0,4778 <= 0,7798`, el test suficiente de Liu-Layland garantiza el sistema.

### 4.2 Test exacto

| Tarea | Sucesión de tiempos de respuesta | Resultado final |
| :--: | :-- | :--: |
| T1 | `1 -> 1` | `R1 = 1 <= 6` |
| T2 | `2 -> 3 -> 3` | `R2 = 3 <= 10` |
| T3 | `2 -> 5 -> 5` | `R3 = 5 <= 18` |

### 4.3 Diagrama de Gantt representativo, `0 <= t < 30`

El ciclo mayor completo dura 90 unidades; el siguiente tramo permite observar las liberaciones y apropiaciones de las tres tareas. Los restantes trabajos obedecen la misma regla RM y quedan cubiertos por los tests anteriores.

| Intervalo | `[0,1)` | `[1,3)` | `[3,5)` | `[5,6)` | `[6,7)` | `[7,10)` | `[10,12)` | `[12,13)` |
| :-- | :--: | :--: | :--: | :--: | :--: | :--: | :--: | :--: |
| CPU | T1.1 | T2.1 | T3.1 | Idle | T1.2 | Idle | T2.2 | T1.3 |

| Intervalo | `[13,18)` | `[18,19)` | `[19,20)` | `[20,22)` | `[22,23)` | `[23,24)` | `[24,25)` | `[25,30)` |
| :-- | :--: | :--: | :--: | :--: | :--: | :--: | :--: | :--: |
| CPU | Idle | T1.4 | T3.2 | T2.3 | T3.2 | Idle | T1.5 | Idle |

El Sistema 2 es planificable por RM.

## 5. Sistema 3

### 5.1 Prioridades y parámetros

| Prioridad | Tarea | `C` | `T = D` | Utilización |
| :--: | :--: | --: | --: | --: |
| P1 | T1 | 1 | 8 | `0,1250` |
| P2 | T2 | 3 | 15 | `0,2000` |
| P3 | T3 | 4 | 20 | `0,2000` |
| P4 | T4 | 6 | 22 | `0,2727` |

\[
U_3=\frac{1053}{1320}=0,7977,\qquad H_3=1320,\qquad \operatorname{mcd}=1
\]

Como `0,7977 > U_LL(4) = 0,7568`, el límite suficiente es inconcluso.

### 5.2 Test exacto

| Tarea | Sucesión de tiempos de respuesta | Resultado final |
| :--: | :-- | :--: |
| T1 | `1 -> 1` | `R1 = 1 <= 8` |
| T2 | `3 -> 4 -> 4` | `R2 = 4 <= 15` |
| T3 | `4 -> 8 -> 8` | `R3 = 8 <= 20` |
| T4 | `6 -> 14 -> 15 -> 15` | `R4 = 15 <= 22` |

### 5.3 Diagrama de Gantt representativo, `0 <= t < 45`

Debido a que `H = 1320`, se muestra un tramo legible que contiene trabajos de las cuatro tareas. La garantía de todo el hiperperíodo proviene del análisis exacto.

| Intervalo | `[0,1)` | `[1,4)` | `[4,8)` | `[8,9)` | `[9,15)` | `[15,16)` | `[16,17)` | `[17,19)` | `[19,20)` |
| :-- | :--: | :--: | :--: | :--: | :--: | :--: | :--: | :--: | :--: |
| CPU | T1.1 | T2.1 | T3.1 | T1.2 | T4.1 | T2.2 | T1.3 | T2.2 | Idle |

| Intervalo | `[20,24)` | `[24,25)` | `[25,30)` | `[30,32)` | `[32,33)` | `[33,34)` | `[34,35)` | `[35,40)` | `[40,41)` | `[41,45)` |
| :-- | :--: | :--: | :--: | :--: | :--: | :--: | :--: | :--: | :--: | :--: |
| CPU | T3.2 | T1.4 | T4.2 | T2.3 | T1.5 | T2.3 | T4.2 | Idle | T1.6 | T3.3 |

El Sistema 3 es planificable por RM.

## 6. Sistema 4

### 6.1 Prioridades y parámetros

| Prioridad | Tarea | `C` | `T = D` | Utilización |
| :--: | :--: | --: | --: | --: |
| P1 | T1 | 0,5 | 4 | `0,1250` |
| P2 | T2 | 1 | 5 | `0,2000` |
| P3 | T3 | 2 | 10 | `0,2000` |
| P4 | T4 | 9 | 24 | `0,3750` |

\[
U_4=0,90,\qquad H_4=120,\qquad \operatorname{mcd}=1
\]

Como `0,90 > 0,7568`, el límite suficiente no garantiza el sistema.

### 6.2 Test exacto

| Tarea | Sucesión de tiempos de respuesta | Resultado final |
| :--: | :-- | :--: |
| T1 | `0,5 -> 0,5` | `R1 = 0,5 <= 4` |
| T2 | `1 -> 1,5 -> 1,5` | `R2 = 1,5 <= 5` |
| T3 | `2 -> 3,5 -> 3,5` | `R3 = 3,5 <= 10` |
| T4 | `9 -> 14,5 -> 18 -> 19,5 -> 19,5` | `R4 = 19,5 <= 24` |

### 6.3 Diagrama de Gantt representativo, `0 <= t < 24`

| Intervalo | `[0,0,5)` | `[0,5,1,5)` | `[1,5,3,5)` | `[3,5,4)` | `[4,4,5)` | `[4,5,5)` |
| :-- | :--: | :--: | :--: | :--: | :--: | :--: |
| CPU | T1.1 | T2.1 | T3.1 | T4.1 | T1.2 | T4.1 |

| Intervalo | `[5,6)` | `[6,8)` | `[8,8,5)` | `[8,5,10)` | `[10,11)` | `[11,12)` |
| :-- | :--: | :--: | :--: | :--: | :--: | :--: |
| CPU | T2.2 | T4.1 | T1.3 | T4.1 | T2.3 | T3.2 |

| Intervalo | `[12,12,5)` | `[12,5,13,5)` | `[13,5,15)` | `[15,16)` | `[16,16,5)` | `[16,5,19,5)` |
| :-- | :--: | :--: | :--: | :--: | :--: | :--: |
| CPU | T1.4 | T3.2 | T4.1 | T2.4 | T1.5 | T4.1 |

| Intervalo | `[19,5,20)` | `[20,20,5)` | `[20,5,21,5)` | `[21,5,23,5)` | `[23,5,24)` |
| :-- | :--: | :--: | :--: | :--: | :--: |
| CPU | Idle | T1.6 | T2.5 | T3.3 | Idle |

T4.1 termina en `t = 19,5`, antes de su plazo en `t = 24`. El Sistema 4 es planificable por RM.

## 7. Configuración de FreeRTOS para Rate Monotonic

FreeRTOS utiliza prioridades numéricas en las que un número mayor representa mayor prioridad. Para implementar RM deben asignarse prioridades estáticas según el período y habilitarse la apropiación.

Configuración relevante en `FreeRTOSConfig.h`:

```c
#define configUSE_PREEMPTION            1
#define configUSE_TIME_SLICING          0
#define configUSE_TICKLESS_IDLE         0
#define configMAX_PRIORITIES            6
#define configTICK_RATE_HZ              1000
```

Asignación ilustrativa para los sistemas de cuatro tareas:

```c
#define RM_PRIO_T1  (tskIDLE_PRIORITY + 4U)
#define RM_PRIO_T2  (tskIDLE_PRIORITY + 3U)
#define RM_PRIO_T3  (tskIDLE_PRIORITY + 2U)
#define RM_PRIO_T4  (tskIDLE_PRIORITY + 1U)
```

Patrón de una tarea periódica:

```c
static void vPeriodicTask(void *argument)
{
    const TickType_t period = pdMS_TO_TICKS(TASK_PERIOD_MS);
    TickType_t last_wake = xTaskGetTickCount();

    for (;;) {
        execute_job();
        vTaskDelayUntil(&last_wake, period);
    }
}
```

Consideraciones:

- El tick debe representar exactamente la menor unidad temporal; para el Sistema 4 debe poder representar `0,5` unidades.
- Cada trabajo debe terminar antes de llamar a `vTaskDelayUntil()` y su WCET medido debe ser menor o igual al `C_i` analizado.
- Deben incluirse en el análisis el tiempo de interrupciones, cambios de contexto y secciones críticas si no son despreciables.
- Las tareas no deben compartir una misma prioridad. Desactivar time slicing evita comportamiento round-robin accidental entre prioridades iguales.
- Si existen mutex o recursos compartidos, debe añadirse el bloqueo al análisis y usarse un protocolo adecuado de herencia de prioridad.

## 8. Conclusión

El test suficiente de Liu-Layland garantiza directamente el Sistema 2. Los Sistemas 1, 3 y 4 superan ese límite, pero el análisis exacto de tiempo de respuesta demuestra que también cumplen todos sus plazos. Por lo tanto, los cuatro sistemas son planificables mediante Rate Monotonic bajo los supuestos establecidos.

