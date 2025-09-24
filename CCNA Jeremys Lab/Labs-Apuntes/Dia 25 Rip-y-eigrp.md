# CCNA 200-301 Día 25 - RIP & EIGRP



## RIP (Routing Information Protocol)
RIP es un protocolo de enrutamiento de vector de distancia (Distance Vector) que utiliza el conteo de saltos (hop count) como su métrica principal. Es conocido por su simplicidad, pero tiene limitaciones importantes.
### Características Principales

| Característica | Descripción |
|----------------|-------------|
| **Tipo** | Distance vector IGP (estándar de la industria) |
| **Métrica** | Hop count (número de saltos) |
| **Límite máximo** | 15 hops (16+ = inalcanzable) |
| **Lógica** | Routing-by-rumor para aprender/compartir rutas |
| **Frecuencia** | Comparte tabla de routing cada 30 segundos |

### Versiones de RIP

  * **RIPv1:**

      * **No** soporta **VLSM** ni **CIDR**.
      * No incluye la máscara de subred en los anuncios, lo que lleva a la pérdida de información de subred (ej: `10.1.1.0/24` se convierte en `10.0.0.0/8`).
      * Los mensajes son enviados por **broadcast** (`255.255.255.255`).

  * **RIPv2:**

      * **Sí** soporta **VLSM** y **CIDR**.
      * Incluye la máscara de subred, manteniendo la información.
      * Los mensajes son enviados por **multicast** (`224.0.0.9`), lo que reduce el tráfico innecesario en la red.

  * **RIPng (RIP Next Generation):**
    *  Utilizada para IPv6

### Diferencias entre RIPv1 y RIPv2

| Aspecto | RIPv1 | RIPv2 |
|---------|-------|-------|
| **Direcciones** | Solo classful (Clase A, B, C) | Soporta VLSM, CIDR |
| **Subnet mask** | No incluye información | Incluye información de subnet mask |
| **Envío** | Broadcast (255.255.255.255) | Multicast (224.0.0.9) |

### Ejemplos de Conversión RIPv1
- `10.1.1.0/24` → `10.0.0.0` (Clase A, asume /8)
- `172.16.192.0/18` → `172.16.0.0` (Clase B, asume /16)
- `192.168.1.4/30` → `192.168.1.0` (Clase C, asume /24)

### Tipos de Mensajes
1. **Request**: Solicita tabla de routing a vecinos RIP
2. **Response**: Envía tabla de routing local a routers vecinos

![Diagrama de topología RIP](images/lab-dia25/rip-topology.png)



---

## Configuración RIP
El comando `network` en RIP es **classful**, lo que significa que el router asume la máscara de red por defecto de la clase a la que pertenece la red (ej: `network 10.0.12.0` se convierte en `network 10.0.0.0`).

### Comando Network
El comando `network` es **classful** y automáticamente convierte a redes classful.

```cisco
router rip
version 2
network 10.0.0.0
network 172.16.0.0
```

![Configuración de red RIP](images/lab-dia25/rip-network-config.png)

### Funcionalidad del Comando Network
El comando `network` le dice al router que:
1. **Busque interfaces** con dirección IP en el rango especificado
2. **Active RIP** en las interfaces que caigan en el rango
3. **Forme adyacencias** con vecinos RIP conectados
4. **Anuncie el prefijo de red** de la interfaz (NO el prefijo del comando network)

> **Importante**: OSPF y EIGRP operan de la misma manera.

### Interfaz Pasiva
```cisco
router rip
passive-interface g2/0
```

**Propósito**:
- Detiene el envío de anuncios RIP por la interfaz especificada
- Continúa anunciando el prefijo de red de la interfaz a vecinos RIP
- **Usar siempre** en interfaces sin vecinos RIP para evitar tráfico innecesario

![Configuración interfaz pasiva](images/lab-dia25/rip-passive-interface.png)

### Ruta por Defecto
Para anunciar una ruta por defecto en RIP:

```cisco
router rip
default-information originate
```

![Comando default-information originate](images/lab-dia25/rip-default-route.png)

---
## Comandos de Verificación

### RIP
```cisco
show ip protocols
show ip route
show ip rip database
```
## EIGRP (Enhanced Interior Gateway Routing Protocol)
EIGRP es un protocolo de enrutamiento híbrido, más avanzado y rápido que RIP. Aunque fue propietario de Cisco, ahora es un estándar abierto. Su métrica no se basa en el conteo de saltos, lo que le permite ser más eficiente.

-   Utiliza el protocolo **multicast** con la dirección `224.0.0.10` para enviar sus mensajes.
-   Puede realizar balanceo de carga de costo desigual (`unequal-cost load-balancing`), a diferencia de RIP que solo hace balanceo de carga de costo igual (`ECMP`).
### Características Principales

| Característica | Descripción |
|----------------|-------------|
| **Tipo** | Advanced/Hybrid distance vector |
| **Propietario** | Antes Cisco, ahora estándar abierto |
| **Velocidad** | Mucho más rápido que RIP |
| **Límite** | Sin límite de 15 hops |
| **Multicast** | 224.0.0.10 |
| **Load balancing** | Único IGP con unequal-cost load-balancing |

### Configuración EIGRP
```cisco
router eigrp 1
no auto-summary
network 10.0.0.0 0.255.255.255
network 172.16.1.0 0.0.0.15
```

**Requisitos importantes**:
- **AS number** debe coincidir entre routers
- **Deshabilitar auto-summary** si está habilitado
- Usa **wildcard mask** en lugar de subnet mask regular

![Configuración EIGRP](images/lab-dia25/eigrp-configuration.png)

---

## Wildcard Masks

### Concepto
Una **wildcard mask** es básicamente una subnet mask "invertida":
- Todos los **1s** en subnet mask = **0s** en wildcard mask
- Todos los **0s** en subnet mask = **1s** en wildcard mask

### Ejemplos de Conversión

| CIDR | Subnet Mask | Wildcard Mask |
|------|-------------|---------------|
| /24 | 255.255.255.0 | 0.0.0.255 |
| /16 | 255.255.0.0 | 0.0.255.255 |
| /8 | 255.0.0.0 | 0.255.255.255 |
| /28 | 255.255.255.240 | 0.0.0.15 |
| /25 | 255.255.255.128 | 0.0.0.127 |

### Método Rápido
**Restar cada octeto** de la subnet mask de 255:

```
Subnet mask: 255.255.248.0 (/21)
Wildcard:    0.0.7.255
             ↑   ↑   ↑   ↑
           255-255=0, 255-248=7, etc.
```

![Ejemplos de wildcard masks](images/lab-dia25/wildcard-masks.png)

### Lógica de Coincidencia
- **'0'** en wildcard mask = **debe coincidir**
- **'1'** en wildcard mask = **no necesita coincidir**

---

## Router ID - Orden de Prioridad

### EIGRP Router ID
1. **Configuración manual** (`router-id <ID>`)
2. **Dirección IP más alta** en interfaz loopback
3. **Dirección IP más alta** en interfaz física

![Comandos show ip protocols y show ip route](images/lab-dia25/eigrp-show-commands.png)

---

## Comandos de Verificación



### EIGRP
```cisco
show ip protocols
show ip route
show ip eigrp neighbors
show ip eigrp topology
```

---

## Quiz de Práctica

### Pregunta 1
**R1 y R2 usan RIP para compartir rutas. R1 tiene una ruta por defecto al Internet que quieres anunciar a R2. ¿Qué comando debes usar?**

a) `R1(config-router)# default-information originate` ✓  
b) `R1(config-router)# network 203.0.113.0`  
c) `R2(config)# ip route 0.0.0.0 0.0.0.0 10.0.12.1`  
d) `R2(config-router)# default-information originate`

### Pregunta 2
**La interfaz G1/0 de R1 tiene IP 172.20.20.17 y G2/0 tiene IP 172.26.20.12. ¿Cuál comando network activará EIGRP en ambas interfaces?**

a) `R1(config-router)# network 128.0.0.0 127.255.255.255`  
b) `R1(config-router)# network 172.16.0.0 0.0.255.255`  
c) `R1(config-router)# network 172.20.0.0 0.0.127.255`  
d) `R1(config-router)# network 172.20.0.0 0.3.255.255` ✓

### Pregunta 3
**¿Cuál es el orden correcto de prioridad para determinar el EIGRP router ID?**

a) Loopback más alta, interfaz física más alta, configuración manual  
b) Interfaz física más alta, loopback más alta, configuración manual  
c) Configuración manual, interfaz física más alta, loopback más alta  
d) **Configuración manual, loopback más alta, interfaz física más alta** ✓

---

## Resumen
- **RIP**: Protocolo distance vector simple, métrica hop count, máximo 15 saltos
- **EIGRP**: Protocolo avanzado, más rápido, sin límite de saltos, soporta unequal-cost load balancing
- **Wildcard masks**: Versión invertida de subnet masks, críticas para configuración EIGRP
- **Router ID**: Importante para EIGRP, sigue orden de prioridad específico