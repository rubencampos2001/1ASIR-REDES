# 🌐 Planificación y Administración de Redes – Proyecto Intermodular 1º ASIR

Diseño e implementación en **Cisco Packet Tracer** de la red interna de una empresa con dos plantas, segmentada por departamentos con **VLANs**, enrutamiento **Router-on-a-stick**, **DHCP** centralizado, **ACLs** y WiFi separada para empleados e invitados.

**Alumno:** Rubén Campos Ibáñez · **Curso:** 1º ASIR · **Módulo:** Planificación y Administración de Redes

## 📁 Contenido

| Fichero | Descripción |
|---|---|
| `intermodular.pkt` | Simulación completa de la red (abrir con Cisco Packet Tracer) |
| `Redes.intermodular.pdf` | Memoria: diseño, direccionamiento, configuraciones y pruebas |

## 🏢 Escenario

- **Planta baja:** Recepción, Administración, Dirección y CPD (servidores)
- **Primera planta:** Desarrollo, Soporte Técnico y Aula de Formación

## 🧩 VLANs

| VLAN | Nombre | Departamento | Red | Gateway |
|---|---|---|---|---|
| 10 | ADMIN | Administración / Recepción | 192.168.10.0/24 | 192.168.10.1 |
| 20 | DIR | Dirección | 192.168.20.0/24 | 192.168.20.1 |
| 30 | DEV | Desarrollo | 192.168.30.0/24 | 192.168.30.1 |
| 40 | SOPORTE | Soporte Técnico | 192.168.40.0/24 | 192.168.40.1 |
| 50 | FORMACION | Aula de Formación | 192.168.50.0/24 | 192.168.50.1 |
| 60 | SRV | Servidores (CPD) | 192.168.60.0/24 | 192.168.60.1 |
| 99 | MGMT | Gestión de red (nativa) | 192.168.99.0/24 | 192.168.99.1 |

## 🗺️ Topología

Topología jerárquica: el switch **SW-CORE** (CPD) conecta con el router **R1** (Cisco 2901) y con los switches de cada planta (**SW-PB** y **SW-P1**, Cisco 2960) mediante enlaces **trunk 802.1Q**. En el CPD hay dos servidores (SRV1 hace de DHCP) y dos puntos de acceso: uno para empleados y otro para invitados.

## ⚙️ Configuración destacada

**Router-on-a-stick:** los switches 2960 son de capa 2, así que R1 tiene una subinterfaz por VLAN que actúa como gateway.

```
interface GigabitEthernet0/0.10
 encapsulation dot1Q 10
 ip address 192.168.10.1 255.255.255.0
 ip helper-address 192.168.60.10
!
interface GigabitEthernet0/0.99
 encapsulation dot1Q 99 native
 ip address 192.168.99.1 255.255.255.0
```

- **DHCP:** centralizado en SRV1 (192.168.60.10), con `ip helper-address` en cada subinterfaz.
- **ACLs extendidas:** controlan qué VLANs pueden comunicarse (Formación no accede a Dirección y Desarrollo no accede a Administración).
- **Trunks en SW-CORE:** cada enlace solo permite las VLANs necesarias (hacia SW-PB: 10, 20 y 99; hacia SW-P1: 30, 40, 50 y 99).
- **Gestión:** switches gestionables por la VLAN 99.

## ✅ Pruebas realizadas

| # | Prueba | Origen | Destino | Resultado |
|---|---|---|---|---|
| 1 | Misma VLAN | PC-ADM1 | 192.168.10.22 | ✔️ Responde |
| 2 | Inter-VLAN (Dirección) | PC-ADM1 | 192.168.20.1 | ✔️ Responde |
| 3 | Inter-VLAN (Desarrollo) | PC-ADM1 | 192.168.30.1 | ✔️ Responde |
| 4 | Administración → Servidores | PC-ADM1 | 192.168.60.10 | ✔️ Responde |
| 5 | ACL: Formación → Dirección | PC-AUL1 | 192.168.20.20 | ⛔ Bloqueado (ACL_FORMACION) |
| 6 | ACL: Desarrollo → Administración | PC-DEV1 | 192.168.10.22 | ⛔ Bloqueado (ACL_DEV) |

## 💡 Lo que aprendí

- Detecté con `show cdp neighbors` que los cables no estaban en los puertos configurados como trunk y lo corregí.
- Las ACLs extendidas se aplican en la subinterfaz de la VLAN de origen, en dirección *in*.

## 🛠️ Tecnologías

Cisco Packet Tracer · VLAN · 802.1Q · Router-on-a-stick · DHCP · ACL · WiFi
