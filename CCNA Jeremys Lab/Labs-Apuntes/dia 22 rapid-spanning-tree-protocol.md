
# CCNA 200-301 - Día 22: Rapid Spanning Tree Protocol (RSTP)

## 📌 Cosas que Cubriremos

* Comparación de versiones de **STP** (estándar vs. Cisco)
* **Rapid PVST+**
* Estados y roles de puerto de **RSTP**
* Características de **STP** integradas en **RSTP**
* Tipos de enlace de **RSTP**

***

## 🔄 Versiones de Spanning Tree

El **RSTP** (`802.1w`) es una mejora significativa sobre el STP original (`802.1D`), ofreciendo una convergencia mucho más rápida al no depender de temporizadores prolongados. Cisco ha desarrollado sus propias versiones mejoradas para soportar la carga por VLAN.

| Estándares de la Industria (IEEE) | Versiones de Cisco |
| :-------------------------------- | :---------------------------------- |
| **STP** (`802.1D`)               | **PVST+** |
| **RSTP** (`802.1w`)               | **Rapid PVST+** |
| **MSTP** (`802.1s`)               | **Rapid PVST+** (MSTP está integrado en PVST+ y Rapid PVST+) |

***

## ⚡ Rapid Spanning Tree Protocol (RSTP)

**RSTP** es un protocolo de capa 2 que previene los bucles en la red de manera más eficiente que su predecesor. Las diferencias clave radican en la velocidad de convergencia, los estados y roles de los puertos, y la forma en que maneja los **BPDUs**.

### 📉 Costos de RSTP vs. STP

**RSTP** utiliza costos de enlace que son diferentes a los de **STP** para calcular la mejor ruta. 

| Velocidad de Enlace | Costo STP | Costo RSTP |
| :------------------ | :-------- | :--------- |
| **10 Mbps** | 100       | 2,000,000  |
| **100 Mbps** | 19        | 200,000    |
| **1 Gbps** | 4         | 20,000     |
| **10 Gbps** | 2         | 2,000      |
| **100 Gbps** | X         | 200        |
| **1 Tbps** | X         | 20         |

***

## 🚦 Estados y Roles de Puerto

### Estados de Puerto

**RSTP** simplifica los estados de los puertos de **STP**, fusionando los estados de bloqueo, escucha y deshabilitado en un único estado de **descarte** (`discarding`).

| Estado de Puerto STP | Envío/Recepción de BPDUs | Reenvío de Trama | Aprendizaje de MAC | Estable/Transitorio |
| :------------------- | :----------------------- | :--------------- | :----------------- | :------------------ |
| **Blocking** | NO/YES                   | NO               | NO                 | Estable             |
| **Listening** | YES/YES                  | NO               | NO                 | Transitorio         |
| **Learning** | YES/YES                  | NO               | YES                | Transitorio         |
| **Forwarding** | YES/YES                  | YES              | YES                | Estable             |
| **Disabled** | NO/NO                    | NO               | NO                 | Estable             |

| Estado de Puerto RSTP | Envío/Recepción de BPDUs | Reenvío de Trama | Aprendizaje de MAC | Estable/Transitorio |
| :-------------------- | :----------------------- | :--------------- | :----------------- | :------------------ |
| **Discarding** | NO/YES                   | NO               | NO                 | Estable             |
| **Learning** | YES/YES                  | NO               | YES                | Transitorio         |
| **Forwarding** | YES/YES                  | YES              | YES                | Estable             |

### Roles de Puerto

**RSTP** introduce nuevos roles de puerto, **`Alternate`** y **`Backup`**, que reemplazan el rol `Non-Designated` de **STP** para una convergencia más rápida.

* **Root Port**: El puerto en un switch que está más cerca del puente raíz.
* **Designated Port**: El puerto en un segmento de red que envía el mejor **BPDU**.
* **Alternate Port**: Un puerto de descarte que es un respaldo para un puerto raíz. Se activa inmediatamente si el puerto raíz falla. 
* **Backup Port**: Un puerto de descarte que es un respaldo para un puerto designado. 

***

## 💡 Características de STP Integradas en RSTP

**RSTP** incluye de forma nativa funcionalidades opcionales de **STP** que mejoran la velocidad de convergencia.

* **UplinkFast**: Permite que los puertos alternos pasen rápidamente al estado de reenvío si el puerto raíz falla.
* **BackboneFast**: Permite que los switches se adapten rápidamente a un cambio en la topología de la red principal.
* **PortFast**: Se utiliza en puertos de borde (`edge ports`) conectados a hosts, permitiéndoles pasar directamente al estado de reenvío.

***

## 🌐 Tipos de Enlace de RSTP

**RSTP** distingue entre diferentes tipos de enlaces para optimizar su comportamiento.

* **Edge Port**: Un puerto conectado a un dispositivo final (PC, servidor). Se configura con **PortFast** para pasar inmediatamente a **`forwarding`**.
* **Point-to-Point Port**: Un enlace directo entre dos switches. Funciona en modo **full-duplex**.
* **Shared Port**: Un enlace a través de un hub. Funciona en modo **half-duplex**.

***

## ❓ Cuestionario

### 1. ¿Qué características opcionales de **IEEE 802.1D** se integraron en el estándar **IEEE 802.1w**?

**a)** Root Guard
**b)** PortFast
**c)** BPDU Guard
**d)** UplinkFast
**e)** BackboneFast
**f)** Loop Guard
**g)** RootFast

* **Respuesta Correcta:** **b**, **d**, **e**

### 2. ¿Qué comando se utiliza para configurar un puerto de borde **802.1w** para que los hosts conectados puedan enviar tráfico de inmediato?

**a)** `SW1(config-if)# spanning-tree link-type edge`
**b)** `SW1(config-if)# spanning-tree mode edge`
**c)** `SW1(config-if)# spanning-tree link-type portfast`
**d)** `SW1(config-if)# spanning-tree portfast`

* **Respuesta Correcta:** **d**

### 3. Identifica el puente raíz y los roles y tipos de puerto en la siguiente topología. 

* **Solución**: SW1 es el puente raíz debido a su menor ID de puente (Prioridad + MAC). 

***
```