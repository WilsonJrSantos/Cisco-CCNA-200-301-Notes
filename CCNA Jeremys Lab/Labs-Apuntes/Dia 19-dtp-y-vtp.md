# CCNA 200-301 - Día 19: DTP y VTP

## Introducción

Aunque **DTP (Dynamic Trunking Protocol)** y **VTP (VLAN Trunking Protocol)** fueron eliminados de la lista de temas para el examen CCNA 200-301, es crucial entender su función, ya que aún pueden aparecer preguntas sobre ellos.

---

## DTP (Dynamic Trunking Protocol)

* DTP es un protocolo **propietario de Cisco** que permite a los switches determinar dinámicamente si una interfaz debe ser de **acceso** o de **troncal**.
* Viene **habilitado por defecto** en todas las interfaces de los switches Cisco.
* Por motivos de seguridad, se recomienda la configuración manual (`switchport mode access` o `switchport mode trunk`) y **deshabilitar DTP**.

### Modos de Operación

* **`switchport mode trunk`**: Se configura como troncal y activamente intenta formar un trunk.
* **`switchport mode access`**: Se configura como puerto de acceso y deshabilita la negociación DTP.
* **`switchport mode dynamic desirable`**: Intenta activamente formar un trunk.
* **`switchport mode dynamic auto`**: No intenta activamente formar un trunk, pero lo hará si el otro lado lo solicita.

### Matriz de Conectividad DTP

| Administrative Mode | Trunk | Dynamic Desirable | Access | Dynamic Auto |
|:--------------------|:------|:------------------|:-------|:-------------|
| **Trunk** | Trunk | Trunk | X | Trunk |
| **Dynamic Desirable** | Trunk | Trunk | Access | Trunk |
| **Access** | X | Access | Access | Access |
| **Dynamic Auto** | Trunk | Trunk | Access | Access |

![Tabla que muestra los resultados de la negociación de DTP entre diferentes modos de switchport](images/lab-dia19/dtp-negotiation-table1.png)
![Tabla que muestra los resultados de la negociación de DTP entre diferentes modos de switchport](images/lab-dia19/dtp-negotiation-table2.png)
![Tabla que muestra los resultados de la negociación de DTP entre diferentes modos de switchport](images/lab-dia19/dtp-negotiation-table3.png)

![Tabla que muestra los resultados de la negociación de DTP entre diferentes modos de switchport](images/lab-dia19/dtp-negotiation-table4.png)


* En switches **antiguos**, el modo por defecto es `dynamic desirable`.
* En switches **nuevos**, el modo por defecto es `dynamic auto`.
* Puedes deshabilitar la negociación DTP con el comando `switchport nonegotiate`.

---

## VTP (VLAN Trunking Protocol)

* VTP permite configurar VLANs en un **servidor VTP** central para que los **clientes VTP** sincronicen su base de datos de VLANs.
* Es una tecnología que ya **no se recomienda su uso** debido a los riesgos que presenta.
* Existen tres versiones: **1, 2 y 3**.
* Los switches Cisco operan en modo **servidor** por defecto.

### Modos de VTP

* **Servidor**: Puede crear, modificar y eliminar VLANs. Almacena la base de datos en la NVRAM y propaga las actualizaciones. Si recibe una actualización con un número de revisión más alto, la acepta.
* **Cliente**: Sincroniza su base de datos de VLANs con la del servidor. No puede modificar las VLANs. En VTPv3, almacena la base de datos en la NVRAM.
* **Transparente**: No participa en la sincronización VTP, pero reenvía los anuncios VTP a través de sus puertos troncales. Mantiene su propia base de datos de VLANs.

![Diagrama que ilustra los diferentes modos de VTP en una red de switches](images/lab-dia19/vtp-modes-diagram.png)

### Peligros y Recomendaciones

* Un **riesgo significativo** de VTP es que un switch antiguo con un número de revisión alto puede sobrescribir accidentalmente la base de datos de VLAN de toda la red al conectarlo.
* Cambiar el nombre de dominio VTP o el modo a transparente **restablece el número de revisión a 0**.

---

## Conclusión

Este día cubrimos los conceptos principales de DTP y VTP, aunque son tecnologías que se consideran obsoletas y rara vez se usan en la práctica actual, es importante conocerlas para el examen. La práctica recomendada sigue siendo la configuración manual y la desactivación de estos protocolos.

---

## Quiz

**Pregunta 1:** SW1 y SW2 están conectados, ambos switches son nuevos y las interfaces conectadas operan como puertos de acceso. Sin embargo, la fuente de alimentación de SW2 falla, por lo que lo reemplazas temporalmente con un switch antiguo de repuesto. Restableces la configuración antes de conectarlo a SW1, pero cuando lo conectas, notas que se forma un *trunk* entre los dos switches. ¿Cuál podría ser la causa?

a) Las interfaces de los switches antiguos por defecto están en modo `switchport mode trunk`
**b) Las interfaces de los switches antiguos por defecto están en modo `switchport mode dynamic desirable`**
c) Los puertos de acceso son una característica de los switches más nuevos.

**Pregunta 2:** SW1 está conectado a SW2, y SW2 está conectado a SW3. Quieres que SW2 reenvíe la información de la base de datos de VLAN de SW1 a SW3, pero no quieres que SW2 sincronice su base de datos de VLAN con SW1. ¿Qué comando deberías usar en SW2?

**a) `vtp mode transparent`**
b) `vtp transparent mode`
c) `vlan mode transparent`
d) `vtp mode client`

**Pregunta 3:** ¿Cuáles son dos métodos para restablecer el número de revisión VTP de un switch a 0? (Elige dos. Cada respuesta es una solución completa).

**a) Cambiar el dominio VTP a un nombre de dominio no utilizado.**
b) Cambiar el switch a modo de servidor VTP.
**c) Cambiar el switch a modo transparente VTP.**
d) Usar el comando `vtp reset`.