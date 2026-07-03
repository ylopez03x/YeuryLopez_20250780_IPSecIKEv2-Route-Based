# VPN Site-to-Site IPSec IKEv2 — Route-Based
**Autor:** Yeury Lopez | **Matrícula:** 20250780 | **Repositorio:** YeuryLopez_20250780_IPSecIKEv2-Route-Based

---

## Video de demostración

[![Ver en YouTube](https://img.shields.io/badge/YouTube-Ver%20Video-red?logo=youtube)](https://youtu.be/xZsyZ1Rm1M0)

**Enlace directo:** https://youtu.be/xZsyZ1Rm1M0

---

## 1. Objetivo
Configurar una VPN Site-to-Site basada en enrutamiento utilizando IPSec con IKEv2. Se crea una interfaz virtual Tunnel0 protegida con un perfil IKEv2, y se enruta el tráfico a través de esa interfaz sin ACLs.

---

## 2. Topología


> <img width="940" height="589" alt="image" src="https://github.com/user-attachments/assets/33e4a6d6-8c70-4070-903e-f996efdcc2ee" />

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


---

## 6. Verificación y Funcionamiento

### 6.1 Estado IKEv2 SA

Ejecutar en R1:
```
show crypto ikev2 sa
```
> <img width="861" height="439" alt="image" src="https://github.com/user-attachments/assets/7dde606e-2d97-4bcb-b462-77276def1966" />


### 6.2 Estado Tunnel0

Ejecutar en R1:
```
show interface tunnel0
```
> <img width="940" height="258" alt="image" src="https://github.com/user-attachments/assets/eb58444c-d51e-4f2f-965f-862eaafc8244" />


### 6.3 Estado IPSec SA

Ejecutar en R1:
```
show crypto ipsec sa
```
> <img width="940" height="413" alt="image" src="https://github.com/user-attachments/assets/cb06f010-d7c1-4b6a-be37-9d2c51f0809d" />


### 6.4 Demostración de conectividad

Ejecutar en Linux1:
```
ping -c 4 192.168.80.2
```
> <img width="940" height="220" alt="image" src="https://github.com/user-attachments/assets/88f54cd4-bb5e-46bf-b150-41883783fbb7" />


---
