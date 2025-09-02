# 📝 Laboratorio Práctico: Configuración de Contraseñas de Acceso

En este laboratorio trabajé con la configuración de **nombres de host** y **contraseñas de acceso** en dispositivos Cisco, aplicando distintos métodos de seguridad para proteger el acceso.

---

## 📋 Pasos que seguí

### 1. Cambio de nombres de host

Configuré los nombres de host en el router y en el switch de la siguiente manera:

```bash
# Configuración del router
Router> enable
Router# configure terminal
Router(config)# hostname R1
R1(config)# exit

# Configuración del switch
Switch> enable
Switch# configure terminal
Switch(config)# hostname SW1
SW1(config)# exit
Nota: Cambiar los nombres de host ayuda a identificar los dispositivos en la red y facilita la administración.


```
### 2. Configuración de contraseña `enable` sin cifrar

```bash
R1> enable
R1# configure terminal
R1(config)# enable password CCNA
```

Probé el acceso al modo EXEC privilegiado con la contraseña.

---

### 3. Verificación en el `running-config` (contraseña en texto plano)

```bash
R1# show running-config
```

![Contraseña en texto plano](images/lab-dia4/contrasena-texto-plano.png)

---

### 4. Cifrado de contraseñas con `service password-encryption`

```bash
R1(config)# service password-encryption
```

Al volver a revisar el `running-config`, confirmé que la contraseña ya no se mostraba en texto plano.

![Contraseña cifrada](images/lab-dia4/contrasena-cifrada.png)

---

### 5. Configuración de `enable secret`

```bash
R1(config)# enable secret Cisco
```

Probé ambas contraseñas (`enable password CCNA` y `enable secret Cisco`) y confirmé que la de **enable secret** tiene prioridad.

---

### 6. Revisión de contraseñas y tipos de cifrado

```bash
R1# show running-config
```

![Contraseñas visualizadas en running-config](images/lab-dia4/contrasena-md5.png)

* `enable password CCNA` → aparece cifrada con **Type 7**.
* `enable secret Cisco` → aparece cifrada con **MD5 (Type 5)**.

---

### 7. Guardado de la configuración

```bash
R1# copy running-config startup-config
Destination filename [startup-config]? 
Building configuration...
[OK]
```

---

## Conclusiones

* Configuré correctamente los nombres de host y las contraseñas de acceso en router y switch.
* Practiqué el uso de **service password-encryption** para proteger contraseñas básicas.
* Verifiqué la diferencia entre **enable password** (Type 7, débil) y **enable secret** (Type 5, más segura).
* Confirmé que **enable secret** tiene prioridad sobre **enable password**.
* Guardé la configuración para que los cambios persistieran después de un reinicio.

```

