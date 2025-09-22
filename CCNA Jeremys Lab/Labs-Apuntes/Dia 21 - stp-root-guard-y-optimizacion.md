# CCNA 200-301 - Día 22: STP Toolkit: Root Guard y Optimización

## Introducción al STP Toolkit
El **STP Toolkit** ofrece herramientas avanzadas para optimizar y proteger la topología de red. En este resumen, nos enfocaremos en **Root Guard**, un mecanismo crucial para mantener la estabilidad del **Root Bridge** en entornos de múltiples organizaciones.

### Repaso Rápido de Herramientas
- **PortFast**: Permite a los puertos de acceso entrar instantáneamente en el estado **Forwarding**.
- **BPDU Guard**: Deshabilita un puerto si recibe un BPDU inesperado, previniendo bucles.
- **BPDU Filter**: Evita que un puerto envíe o procese BPDUs.
- **Root Guard**: Protege la posición del **Root Bridge** deshabilitando puertos que reciben BPDUs superiores.
- **Loop Guard**: Previene bucles al deshabilitar un puerto si deja de recibir BPDUs.

---

## Root Guard: Protegiendo el Root Bridge

### El Problema
En redes complejas, como las que conectan a un proveedor de servicios con un cliente, un switch externo con una prioridad más baja (un BPDU superior) podría convertirse accidentalmente en el **Root Bridge** de la red. Esto podría desviar el tráfico y causar una congestión severa, afectando el rendimiento de la red.

![Diagrama del problema de Root Guard sin configurar](images/lab-dia1/root-guard-problem.png)

### La Solución: Root Guard
**Root Guard** se configura en los puertos que se conectan a switches fuera de nuestro control, asegurando que nuestro **Root Bridge** no sea reemplazado.
- Si un puerto con **Root Guard** habilitado recibe un BPDU superior (que anuncia un Root Bridge mejor), el puerto se desactiva.
- El puerto entra en un estado **Broken (BKN)** o **Root Inconsistent (ROOT_Inc)**, lo que impide que el switch ajeno afecte la topología.

![Diagrama de la solución con Root Guard en acción](images/lab-dia1/root-guard-solution.png)

### Configuración y Comportamiento
- **Habilitación**: `SW(config-if)# spanning-tree guard root`
- **Nota**: No hay un comando global para habilitar **Root Guard** por defecto. Se debe configurar por interfaz.
- **Recuperación**: El puerto se reactivará automáticamente una vez que deje de recibir BPDUs superiores. El tiempo de recuperación es el **Max Age** de los BPDUs (por defecto, 20 segundos).

---

## Puntos Clave para la Colocación del Root Bridge

Para una topología de red óptima, considera lo siguiente al elegir la ubicación de tu **Root Bridge**:
- **Flujo de tráfico óptimo**: Coloca el Root Bridge de manera que minimice la latencia y la congestión del tráfico.
- **Estabilidad y fiabilidad**: Elige un switch central y estable, preferiblemente uno que no sea propenso a fallas.

![Diagrama de un Root Bridge bien posicionado para el flujo de tráfico](images/lab-dia1/root-bridge-placement.png)

---

## Resumen del Día 22

- El **Root Guard** es una herramienta crucial para proteger la topología de STP en redes interconectadas.
- Previene que un switch externo se convierta en el **Root Bridge** al deshabilitar los puertos que reciben BPDUs superiores.
- A diferencia de otras herramientas del **STP Toolkit**, **Root Guard** se habilita exclusivamente por interfaz.
- Un puerto deshabilitado por **Root Guard** se recupera automáticamente cuando el problema subyacente (la recepción de BPDUs superiores) se resuelve.