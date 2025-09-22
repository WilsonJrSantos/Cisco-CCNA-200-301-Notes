# CCNA 200-301 - Día 21: STP Toolkit: PortFast y Otros


---

## STP Toolkit
El STP Toolkit incluye varias características que protegen y optimizan la topología STP.

- **PortFast**: Permite que un puerto conectado a un host final pase inmediatamente al estado **Forwarding**, sin pasar por los estados de Listening y Learning.
- **BPDU Guard**: Deshabilita automáticamente un puerto que recibe un BPDU, previniendo que dispositivos no autorizados se unan a la red.
- **BPDU Filter**: Evita que un puerto envíe o procese BPDUs.
- **Root Guard**: Mantiene la jerarquía de la red al deshabilitar un puerto si recibe BPDUs superiores, asegurando que un dispositivo no deseado se convierta en Root Bridge.
- **Loop Guard**: Deshabilita un puerto si deja de recibir BPDUs, evitando que un enlace bidireccional se convierta en un bucle.

![Imagen del STP Toolkit en acción](images/lab-dia1/stp-toolkit-diagram.png)

---

## PortFast: Problema y Solución

### El Problema
Por defecto, cuando un host (como un PC) se conecta a un puerto de switch, el puerto tarda 30 segundos en estar operativo. Este tiempo de espera se debe a que el puerto pasa 15 segundos en estado de **Listening** y 15 segundos en **Learning**.
- **Esto genera una mala experiencia de usuario**, ya que el usuario percibe que "el internet no funciona" durante 30 segundos.
- Este retraso es innecesario, ya que no hay riesgo de un bucle de Capa 2 al conectar un host final.

![Diagrama del problema de PortFast sin configurar](images/lab-dia1/portfast-problem-before.png)

### La Solución: PortFast
**PortFast** soluciona este problema haciendo que el puerto pase inmediatamente al estado **Forwarding** cuando un dispositivo se conecta.
- **Beneficio**: El host final obtiene conectividad instantánea, mejorando la experiencia del usuario.

![Diagrama de la solución con PortFast](images/lab-dia1/portfast-solution-after.png)

---

## Configuración de PortFast

La configuración de PortFast es sencilla y se puede realizar de dos maneras:

1.  **Por interfaz**:
    - Comando: `SW1(config-if)# spanning-tree portfast`
    - **Uso**: Habilita PortFast solo en una interfaz específica.

2.  **Por defecto (global)**:
    - Comando: `SW1(config)# spanning-tree portfast default`
    - **Uso**: Habilita PortFast en **todos los puertos de acceso** del switch. Los puertos troncales no se ven afectados.

### Consideraciones importantes
- **Nunca** habilites PortFast en puertos conectados a otros switches. Esto podría crear bucles temporales de Capa 2.
- A pesar de la advertencia, el comando `spanning-tree portfast` solo se activará en puertos que estén en modo de acceso.
- El comando `show spanning-tree interface <nombre-de-interfaz> detail` te permite verificar que PortFast está activo.

### PortFast en Puertos Troncales
En casos especiales como la conexión a servidores de virtualización o a un router con "Router-on-a-stick", se puede habilitar PortFast en un puerto troncal.
- Comando: `SW1(config-if)# spanning-tree portfast trunk`
- **Uso**: Habilita PortFast en una interfaz troncal.

![Diagrama de PortFast en un puerto troncal](images/lab-dia1/portfast-on-trunk.png)

---

## Resumen Final
- **PortFast** permite que los puertos de switch conectados a hosts finales entren inmediatamente en el estado **Forwarding**, omitiendo los 30 segundos de espera.
- Se puede configurar por **interfaz** o de forma **global** en todos los puertos de acceso.
- La configuración por defecto no afecta a los puertos troncales, pero existe un comando específico para habilitar PortFast en ellos.
- Es crucial **no habilitar PortFast** en puertos que se conectan a otros switches para evitar la creación de bucles.

````