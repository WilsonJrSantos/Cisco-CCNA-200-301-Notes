# STP Toolkit: PortFast, BPDU Guard y BPDU Filter

## 📝 **Resumen del contenido**

Este documento cubre herramientas esenciales de **Spanning Tree Protocol (STP)** que mejoran la seguridad y la eficiencia en una red conmutada.

- **PortFast**: Permite que los puertos conectados a dispositivos finales (como PCs) pasen inmediatamente al estado de reenvío de STP, saltándose los estados de **Listening** y **Learning**. A pesar de esto, el puerto continúa enviando BPDUs cada 2 segundos. Si un puerto con PortFast recibe una BPDU, revierte a un comportamiento STP normal.
- **BPDU Guard**: Protege la topología STP al deshabilitar automáticamente un puerto si recibe una BPDU. Esto evita que dispositivos no autorizados, como un switch conectado por error, se unan a la red y alteren la topología. Es un mecanismo de seguridad crítico.
- **BPDU Filter**: Detiene a un puerto de enviar o procesar BPDUs. Es útil para evitar el uso innecesario de ancho de banda y la divulgación de información de la topología STP a dispositivos de usuario.

---

### **PortFast y BPDUs**

**PortFast** acelera la convergencia de STP para los puertos de acceso. Aunque acelera el proceso, no deshabilita STP por completo. Un puerto con PortFast habilitado sigue enviando **BPDUs** cada 2 segundos.
![Configuración de PortFast y verificación de BPDUs enviadas](images/lab-dia6/portfast-bpdu-sent.png)
Si un puerto con PortFast recibe una BPDU, perderá su funcionalidad de **PortFast** y se comportará como un puerto STP estándar, pasando por los estados de **Listening** y **Learning**.

---

### **BPDU Guard: El Problema y la Solución**

#### **El Problema**

Un problema común ocurre cuando un usuario conecta un switch personal a un puerto de pared destinado a un PC. Si este puerto tiene **PortFast** habilitado, el nuevo switch podría convertirse en el **Root Bridge** de la red si tiene una prioridad de puente superior, lo que podría desestabilizar la topología.

![Diagrama de un usuario conectando un switch no autorizado](images/lab-dia6/bpdu-guard-problem.png)

#### **La Solución**

**BPDU Guard** protege la red al deshabilitar un puerto si detecta una BPDU entrante. Esto asegura que solo los dispositivos finales, que no envían BPDUs, se puedan conectar a puertos de acceso.

![Diagrama de cómo BPDU Guard deshabilita un puerto](images/lab-dia6/bpdu-guard-solution.png)

#### **Configuración**

- **Por puerto:**
```bash
  SW(config-if)# spanning-tree bpduguard enable
```

  - **Por defecto (recomendado):**
```bash
    SW(config)# spanning-tree portfast bpduguard default
```
    Esto habilita BPDU Guard en todos los puertos con PortFast.

-----

### **Estado ErrDisable y Recuperación**

Cuando BPDU Guard se activa, el puerto entra en el estado **`err-disabled`** y se deshabilita.

Para reactivar el puerto, puedes hacerlo manualmente con `shutdown` y `no shutdown`, o automáticamente con la función **ErrDisable Recovery**.

#### **ErrDisable Recovery**

Esta función permite que los puertos deshabilitados por una causa específica se reactiven automáticamente después de un período de tiempo.

  - **Habilitar para BPDU Guard:**
    ```bash
    SW(config)# errdisable recovery cause bpduguard
    ```
  - **Modificar el intervalo (por defecto 300 segundos):**
    ```bash
    SW(config)# errdisable recovery interval <segundos>
    ```
  - **Verificación:**
    ```bash
    SW# show errdisable recovery
    ```

-----

### **BPDU Filter: El Problema y la Solución**

#### **El Problema**

Un puerto de acceso sigue enviando BPDUs cada 2 segundos, lo cual es innecesario y puede ser una vulnerabilidad de seguridad, ya que revela la topología de la red.

#### **La Solución**

**BPDU Filter** detiene el envío de BPDUs. A diferencia de BPDU Guard, no deshabilita el puerto si recibe una BPDU, sino que la ignora (en el modo per-port) o se desactiva y el puerto vuelve a ser STP normal (en el modo por defecto).

#### **Configuración**

  - **Por puerto (usar con precaución):**
    ```bash
    SW(config-if)# spanning-tree bpdufilter enable
    ```
  - **Por defecto (recomendado):**
    ```bash
    SW(config)# spanning-tree portfast bpdufilter default
    ```

-----

### **Tabla de Referencia Rápida**

| Característica | Propósito | Comportamiento al recibir BPDU | Comando Clave |
| :--- | :--- | :--- | :--- |
| **PortFast** | Convergencia rápida para puertos de acceso | Revierte a STP normal | `spanning-tree portfast` |
| **BPDU Guard** | Seguridad, prevenir switches no autorizados | Deshabilita el puerto (`err-disabled`) | `spanning-tree bpduguard enable` |
| **BPDU Filter** | Deshabilitar el envío/procesamiento de BPDUs | **Modo por defecto:** Se desactiva el filtro. <br> **Modo por puerto:** Ignora la BPDU. | `spanning-tree bpdufilter enable` |

> 💡 **Recomendación:** Se sugiere habilitar **PortFast** y **BPDU Guard** en todos los puertos de acceso, y usar **BPDU Filter** con precaución, preferiblemente solo en el modo por defecto. La combinación de **BPDU Guard** y **PortFast** es una práctica estándar de seguridad en redes conmutadas.

```
```