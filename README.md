# VTP-Attack
https://www.youtube.com/watch?v=sf8RZX4haqc
# VTP Attacks utilizando Yersinia

## Documentación Técnica Profesional

### Autor

**Darwing Peña**
**Matrícula:** 20242690

---

# 1. Objetivo del Laboratorio

Demostrar las vulnerabilidades del protocolo VTP (VLAN Trunking Protocol) mediante la utilización de herramientas de auditoría de red en un entorno controlado de laboratorio.

La práctica busca evidenciar cómo un atacante conectado a la misma infraestructura de switching puede modificar la base de datos de VLANs de un dominio VTP, provocando interrupciones del servicio o la propagación de configuraciones maliciosas.

---

# 2. Objetivo del Script

El script desarrollado tiene como finalidad automatizar ataques VTP utilizando la herramienta Yersinia.

Las funciones principales son:

* Automatizar la ejecución de ataques VTP.
* Capturar tráfico VTP mediante tcpdump.
* Inyectar nuevas VLANs dentro del dominio VTP.
* Eliminar VLANs existentes.
* Forzar intercambios de información VTP.
* Registrar evidencias para análisis posterior.
* Mostrar comandos de restauración y mitigación.

---

# 3. Descripción del Ataque

VTP (VLAN Trunking Protocol) es un protocolo propietario de Cisco diseñado para distribuir información de VLANs entre switches de un mismo dominio.

Un atacante puede aprovechar configuraciones inseguras para:

* Introducir VLANs no autorizadas.
* Eliminar VLANs existentes.
* Alterar la base de datos de VLANs.
* Generar interrupciones de conectividad.

La herramienta utilizada para realizar la simulación fue Yersinia, una plataforma especializada en auditorías de protocolos de capa 2.

---

# 4. Requisitos para Utilizar la Herramienta

## Hardware

* Equipo anfitrión con GNS3.
* Un switch Cisco IOSvL2.
* Una máquina Kali Linux.
* Router opcional para pruebas de conectividad.

## Software

* Kali Linux.
* Python 3.
* Yersinia.
* Tcpdump.

## Privilegios

El script requiere permisos de administrador:

```bash
sudo python3 vtp_20242690.py
```

---

# 5. Topología de Red

## Escenario Utilizado

```text
          [R1]
            |
         [SW-1]
            |
      [Kali Linux]
```

---

## Funciones

| Dispositivo | Función           |
| ----------- | ----------------- |
| SW-1        | Servidor VTP      |
| Kali Linux  | Equipo atacante   |
| R1          | Router de pruebas |

---

## Configuración VTP del Switch

```bash
vtp mode server
vtp domain LABVTP
vtp version 1
```

---

## VLANs Iniciales

```bash
vlan 10
 name VENTAS

vlan 20
 name RRHH
```

---

# 6. Parámetros Utilizados

## Valores por Defecto

| Parámetro        | Valor   |
| ---------------- | ------- |
| Interfaz         | eth0    |
| Dominio VTP      | LABVTP  |
| VLAN por defecto | 90      |
| Nombre VLAN      | DARWING |

---

## Filtro BPF utilizado

```python
VTP_BPF = (
    "ether dst 01:00:0c:cc:cc:cc and "
    "(ether[20:2] = 0x2003 or ether[24:2] = 0x2003)"
)
```

Este filtro permite capturar exclusivamente tráfico VTP.

---

# 7. Funcionamiento del Script

## Fase 1: Validación

El script verifica:

* Permisos root.
* Existencia de Yersinia.
* Existencia de Tcpdump.
* Disponibilidad de la interfaz de red.

---

## Fase 2: Captura de Tráfico

Se inicia tcpdump para capturar paquetes VTP.

```bash
tcpdump -eni eth0 -vvv
```

Toda la evidencia queda almacenada automáticamente.

---

## Fase 3: Inicio de Yersinia

El programa ejecuta Yersinia en modo interactivo.

```bash
yersinia -I
```

---

## Fase 4: Selección del Ataque

El usuario puede elegir entre:

### Opción 1

Eliminar todas las VLANs del dominio VTP.

### Opción 2

Agregar una nueva VLAN al dominio VTP.

### Opción 3

Enviar solicitudes VTP y capturar información.

### Opción 4

Mostrar comandos de restauración y mitigación.

---

# 8. Ataque Realizado

## Ataque de Inyección de VLAN

Durante la práctica se utilizó la opción:

```text
Agregar una VLAN mediante VTP
```

Valores utilizados:

```text
VLAN ID: 90
Nombre: DARWING
```

El script generó anuncios VTP para intentar propagar la nueva VLAN dentro del dominio.

---

## Resultado Esperado

Antes del ataque:

```text
VLAN 10  VENTAS
VLAN 20  RRHH
```

Después del ataque:

```text
VLAN 10  VENTAS
VLAN 20  RRHH
VLAN 90  DARWING
```

---

<img width="813" height="331" alt="image" src="https://github.com/user-attachments/assets/4a645e56-6aea-44c3-9be1-80f3a54d6bca" />
<img width="628" height="571" alt="image" src="https://github.com/user-attachments/assets/7408d5d4-ccf4-441b-bb7c-303adde1bc37" />
<img width="915" height="527" alt="image" src="https://github.com/user-attachments/assets/34bb29e4-8c94-4079-8d8d-af7b6e958cc0" />

<img width="920" height="453" alt="image" src="https://github.com/user-attachments/assets/f370ae54-4b10-4569-995f-86eff05ed36c" />

<img width="641" height="389" alt="image" src="https://github.com/user-attachments/assets/8794cf8b-9c4d-4778-9379-16a46e56326e" />

---

# 10. Comandos de Verificación

Verificar VLANs:

```bash
show vlan brief
```

Verificar VTP:

```bash
show vtp status
```

Verificar enlaces trunk:

```bash
show interfaces trunk
```

---

# 11. Restauración del Laboratorio

En caso de alteración de las VLANs, se utilizaron los siguientes comandos:

```bash
configure terminal

vlan 10
 name VENTAS

vlan 20
 name RRHH

interface gi0/0
 switchport mode trunk
 switchport trunk native vlan 1
 switchport trunk allowed vlan 1,10,20

end
write memory
```

---

# 12. Contramedidas

## Deshabilitar VTP

```bash
vtp mode transparent
```

---

## Utilizar Contraseñas VTP

```bash
vtp password contraseña_segura
```

---

## Restringir Acceso Físico

Limitar la conexión de dispositivos no autorizados a la infraestructura.

---

## Utilizar Puertos Access

```bash
switchport mode access
```

---

## Deshabilitar DTP

```bash
switchport nonegotiate
```

---

## Segmentación de Red

Implementar VLANs de administración separadas de los usuarios finales.

---

## Monitoreo

Utilizar IDS/IPS para detectar tráfico VTP anómalo.

---

# 13. Conclusiones

La práctica permitió comprender el funcionamiento interno de VTP y demostrar cómo una configuración insegura puede permitir que un atacante modifique la base de datos de VLANs de una red corporativa.

La automatización mediante Python, Yersinia y Tcpdump facilitó la ejecución del ataque y la recolección de evidencias para análisis posterior.

Los resultados obtenidos evidencian la importancia de deshabilitar VTP cuando no sea necesario y aplicar mecanismos de protección adecuados para evitar ataques de capa 2.

---

