# VPN Site-to-Site IPSec IKEv2 — Route-Based
**Autor:** Yeury Lopez | **Matrícula:** 20250780 | **Repositorio:** YeuryLopez_20250780_IPSecIKEv2-Route-Based

---

## 1. Objetivo
Configurar una VPN Site-to-Site basada en enrutamiento utilizando IPSec con IKEv2. Se crea una interfaz virtual Tunnel0 protegida con un perfil IKEv2, y se enruta el tráfico a través de esa interfaz sin ACLs.

---

## 2. Topología

```
[Linux1] --- [SW1] --- [R1] --- [ISP] --- [R2] --- [SW2] --- [Linux2]
```

> 📸 **SCREENSHOT:** Insertar captura de la topología completa en EVE-NG

---

## 3. Direccionamiento IP

| Dispositivo | Interfaz | Dirección IP | Rol |
|---|---|---|---|
| R1 | Fa0/0 | 10.7.80.1/30 | WAN → ISP |
| R1 | Fa1/0 | 192.168.7.1/24 | LAN |
| R1 | Tunnel0 | 172.16.78.1/30 | Tunnel VPN |
| ISP | Fa0/1 | 10.7.80.2/30 | WAN → R1 |
| ISP | Fa0/0 | 10.7.81.1/30 | WAN → R2 |
| R2 | Fa0/0 | 10.7.81.2/30 | WAN → ISP |
| R2 | Fa1/0 | 192.168.80.1/24 | LAN |
| R2 | Tunnel0 | 172.16.78.2/30 | Tunnel VPN |

---

## 4. Parámetros VPN

| Parámetro | Valor |
|---|---|
| Tipo de VPN | IPSec IKEv2 Route-Based |
| IKEv2 — Cifrado | AES-CBC-128 |
| IKEv2 — Integridad | SHA-1 |
| IKEv2 — Grupo DH | Group 2 |
| Autenticación | Pre-Shared Key |
| Fase 2 — Modo | Tunnel |
| Pre-Shared Key | Yeury0780 |
| Interfaz Tunnel | Tunnel0 — 172.16.78.0/30 |

---

## 5. Configuración

### 5.1 Configuración R1

> 📸 **SCREENSHOT:** Insertar captura del `show running-config` de R1 mostrando ikev2 profile, crypto ipsec profile y Tunnel0

```
crypto ipsec profile VPN-PROFILE
 set transform-set TS
 set ikev2-profile PROF-IKEv2
interface Tunnel0
 ip address 172.16.78.1 255.255.255.252
 tunnel source FastEthernet0/0
 tunnel destination 10.7.81.2
 tunnel protection ipsec profile VPN-PROFILE
ip route 192.168.80.0 255.255.255.0 172.16.78.2
```

### 5.2 Configuración R2

> 📸 **SCREENSHOT:** Insertar captura del `show running-config` de R2

---

## 6. Verificación y Funcionamiento

### 6.1 Estado IKEv2 SA

Ejecutar en R1:
```
show crypto ikev2 sa
```
> 📸 **SCREENSHOT:** Insertar captura mostrando estado **READY**

### 6.2 Estado Tunnel0

Ejecutar en R1:
```
show interface tunnel0
```
> 📸 **SCREENSHOT:** Insertar captura mostrando `Tunnel0 is up, line protocol is up` y `Tunnel protection via IPSec (profile "VPN-PROFILE")`

### 6.3 Estado IPSec SA

Ejecutar en R1:
```
show crypto ipsec sa
```
> 📸 **SCREENSHOT:** Insertar captura mostrando `#pkts encaps` y `#pkts decaps` con valores mayores a 0

### 6.4 Demostración de conectividad

Ejecutar en Linux1:
```
ping -c 4 192.168.80.2
```
> 📸 **SCREENSHOT:** Insertar captura del ping exitoso

---

## 7. Archivos del repositorio

| Archivo | Descripción |
|---|---|
| `YeuryLopez_20250780_Script_P5.txt` | Script de configuración |
| `YeuryLopez_20250780_Informe_P5.pdf` | Documentación técnica en PDF |
| `YeuryLopez_20250780_Links_P5.txt` | Enlace al video |
| `README.md` | Este archivo |
