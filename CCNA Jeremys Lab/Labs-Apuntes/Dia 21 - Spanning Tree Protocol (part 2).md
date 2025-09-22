# CCNA 200-301 - Día 21: STP (Spanning Tree Protocol) Parte 2

## Resumen de Estados y Temporizadores de STP

El **Spanning Tree Protocol (STP)** es esencial para prevenir bucles en redes de Capa 2. Para lograrlo, los puertos de los switches atraviesan diferentes estados.

### Estados de los Puertos STP
Los puertos de un switch que ejecutan STP pueden estar en uno de los siguientes estados.

| Estado del Puerto | Descripción | Duración (por defecto) |
| :--- | :--- | :--- |
| **Blocking** | No reenvía frames ni aprende direcciones MAC. Recibe BPDUs. Previene bucles. | Indefinido |
| **Listening** | No reenvía frames. Escucha BPDUs para determinar la topología. | 15 segundos (Forward Delay) |
| **Learning** | No reenvía frames. Aprende direcciones MAC para poblar la tabla CAM. | 15 segundos (Forward Delay) |
| **Forwarding** | Opera normalmente. Reenvía frames y aprende direcciones MAC. | Indefinido |
| **Disabled** | El puerto está apagado administrativamente. | Indefinido |

![Diagrama de flujo de los estados de un puerto STP](images/lab-dia1/stp-states-diagram.png)

### Temporizadores de STP
Los temporizadores de STP son fundamentales para la convergencia de la red.

| Temporizador | Propósito | Duración (por defecto) |
| :--- | :--- | :--- |
| **Hello Time** | Frecuencia con la que el switch raíz envía BPDUs. | 2 segundos |
| **Forward Delay** | Tiempo que un puerto pasa en los estados de Listening y Learning. | 15 segundos |
| **Max Age** | Tiempo que un switch espera sin recibir BPDUs antes de reevaluar la topología. | 20 segundos |

---

## Características Opcionales (STP Toolkit)

### PortFast
- **Función**: Permite que un puerto pase de inmediato al estado **Forwarding**, saltándose los estados de Listening y Learning.
- **Uso**: Únicamente en puertos que se conectan a **dispositivos finales** (ej. PCs, servidores).
- **Riesgo**: Si se usa en un puerto conectado a otro switch, puede crear un **bucle de Capa 2**.

![Diagrama de la funcionalidad de PortFast](images/lab-dia1/stp-portfast-diagram.png)

### BPDU Guard
- **Función**: Deshabilita automáticamente un puerto si recibe un **BPDU**.
- **Uso**: Complementa a PortFast. Previene que un switch se conecte a un puerto de acceso y cause un bucle.

![Diagrama de la funcionalidad de BPDU Guard](images/lab-dia1/stp-bpdu-guard-diagram.png)

---

## Configuración y Ejemplos Prácticos

### Configuración del Root Bridge
Para influir en la topología de STP, se puede designar un switch como el **Root Bridge**.

| Comando | Función |
| :--- | :--- |
| `spanning-tree vlan <vlan> root primary` | Establece la prioridad del switch a 24576 para convertirlo en el root primario. |
| `spanning-tree vlan <vlan> root secondary` | Establece la prioridad del switch a 28672 para convertirlo en el root secundario (servidor de respaldo). |

![Ejemplo de topología con múltiples VLANs y Root Bridge](images/lab-dia1/stp-vlan-topologies.png)

### Quiz de Repaso

1. **Pregunta**: Conectas una PC a un switch y la conectividad tarda unos 30 segundos en establecerse. ¿Qué dos opciones podrían solucionar esto?
   - **Respuesta**: Habilitar **PortFast** o reducir el temporizador **Forward Delay**.

2. **Pregunta**: Un `packet capture` muestra que un puerto tiene un ID de `0x8002`. ¿Cuál es su prioridad?
   - **Respuesta**: La prioridad del puerto es **128**.

3. **Pregunta**: ¿Qué característica de STP previene que un bucle de Capa 2 se forme si un usuario conecta un switch a un puerto de acceso?
   - **Respuesta**: **BPDU Guard**.

````