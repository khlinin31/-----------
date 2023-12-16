University: [ITMO University](https://itmo.ru/ru/)

Faculty: [FICT](https://fict.itmo.ru)

Course: [Introduction in routing](https://github.com/itmo-ict-faculty/introduction-in-routing)

Year: 2023/2024

Group: K33202

Author: Khlynin Kirill Dmitriyevich

Lab: Lab2

Date of create: 16.11.2023

Date of finished:

# Отчёт по лабораторной работе №2 "Эмуляция распределенной корпоративной сети связи, настройка статической маршрутизации между филиалами"


**Цель работы:** Ознакомиться с принципами планирования IP адресов, настройке статической маршрутизации и сетевыми функциями устройств.

### **Результаты:**

#### **1** Схема связи:

![](https://github.com/khlinin31/2023_2024-introduction_in_routing-k33202-khlynin_K_D/blob/main/lab2/img/sh.jpeg "Схема связи")

#### **2** Файл yaml:

```
name: lab2


mgmt:
  network: statics
  ipv4_subnet: 172.20.20.0/24


topology:
  nodes:
    R01.MSK:
      kind: vr-ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt_ipv4: 172.20.20.10

    R01.BRL:
      kind: vr-ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt_ipv4: 172.20.20.11

    R01.FRT:
      kind: vr-ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt_ipv4: 172.20.20.12

    PC1:
      kind: vr-ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt_ipv4: 172.20.20.13

    PC2:
      kind: vr-ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt_ipv4: 172.20.20.14

    PC3:
      kind: vr-ros
      image: vrnetlab/vr-routeros:6.47.9
      mgmt_ipv4: 172.20.20.15


  links:
    - endpoints: ["R01.MSK:eth2", "R01.FRT:eth2"]
    - endpoints: ["R01.MSK:eth1", "R01.BRL:eth1"]
    - endpoints: ["R01.BRL:eth2", "R01.FRT:eth1"]
    - endpoints: ["R01.MSK:eth3", "PC1:eth3"]
    - endpoints: ["R01.FRT:eth3", "PC2:eth3"]
    - endpoints: ["R01.BRL:eth3", "PC3:eth3"]
```

#### **3** Результат запуска lab2.yaml:

![](https://github.com/khlinin31/2023_2024-introduction_in_routing-k33202-khlynin_K_D/blob/main/lab2/img/clab.jpg)

#### **4** Текст конфигураций для каждого сетевого устройства:

**Роутер R01.MSK:**
```
/interface wireless security-profiles
set [ find default=yes ] supplicant-identity=MikroTik
/ip pool
add name=pool11 ranges=192.168.10.10-192.168.10.254
/ip dhcp-server
add address-pool=pool11 disabled=no interface=ether4 name=dhcp11
/ip address
add address=172.31.255.30/30 interface=ether1 network=172.31.255.28
add address=10.10.1.1/30 interface=ether2 network=10.10.1.0
add address=10.10.2.1/30 interface=ether3 network=10.10.2.0
add address=192.168.10.1/24 interface=ether4 network=192.168.10.0
/ip dhcp-client
add disabled=no interface=ether1
/ip route
add distance=1 dst-address=192.168.20.0/24 gateway=10.10.2.2
add distance=1 dst-address=192.168.30.0/24 gateway=10.10.1.2
/system identity
set name=R01.MSK
```
![](https://github.com/khlinin31/2023_2024-introduction_in_routing-k33202-khlynin_K_D/blob/main/lab2/img/msk.jpg)

**Роутер R01.BRL:**
```
/interface wireless security-profiles
set [ find default=yes ] supplicant-identity=MikroTik
/ip pool
add name=pool33 ranges=192.168.30.10-192.168.30.254
/ip dhcp-server
add address-pool=pool33 disabled=no interface=ether4 name=dhcp33
/ip address
add address=172.31.255.30/30 interface=ether1 network=172.31.255.28
add address=10.10.1.2/30 interface=ether2 network=10.10.1.0
add address=10.10.3.2/30 interface=ether3 network=10.10.3.0
add address=192.168.30.1/24 interface=ether4 network=192.168.30.0
/ip dhcp-client
add disabled=no interface=ether1
add disabled=no interface=ether2
/ip route
add distance=1 dst-address=192.168.10.0/24 gateway=10.10.1.1
add distance=1 dst-address=192.168.20.0/24 gateway=10.10.3.1
/system identity
set name=R01.BRL
```
![](https://github.com/khlinin31/2023_2024-introduction_in_routing-k33202-khlynin_K_D/blob/main/lab2/img/brl.jpg)

**Роутер R01.FRT:**
```
/interface wireless security-profiles
set [ find default=yes ] supplicant-identity=MikroTik
/ip pool
add name=pool22 ranges=192.168.20.10-192.168.20.254
/ip dhcp-server
add address-pool=pool22 disabled=no interface=ether4 name=dhcp22
/ip address
add address=172.31.255.30/30 interface=ether1 network=172.31.255.28
add address=10.10.2.2/30 interface=ether3 network=10.10.2.0
add address=10.10.3.1/30 interface=ether2 network=10.10.3.0
add address=192.168.20.1/24 interface=ether4 network=192.168.20.0
/ip dhcp-client
add disabled=no interface=ether1
/ip route
add distance=1 dst-address=192.168.10.0/24 gateway=10.10.2.1
add distance=1 dst-address=192.168.30.0/24 gateway=10.10.3.2
/system identity
set name=R01.FRT
```
![](https://github.com/khlinin31/2023_2024-introduction_in_routing-k33202-khlynin_K_D/blob/main/lab2/img/frt.jpg)

**PC1:**
```
/interface wireless security-profiles
set [ find default=yes ] supplicant-identity=MikroTik
/ip address
add address=172.31.255.30/30 interface=ether1 network=172.31.255.28
/ip dhcp-client
add disabled=no interface=ether1
add disabled=no interface=ether4
/ip route
add distance=1 dst-address=10.10.1.0/30 gateway=192.168.10.1
add distance=1 dst-address=10.10.2.0/30 gateway=192.168.10.1
add distance=1 dst-address=192.168.20.0/24 gateway=192.168.10.1
add distance=1 dst-address=192.168.30.0/24 gateway=192.168.10.1
/system identity
set name=PC1
```
![](https://github.com/khlinin31/2023_2024-introduction_in_routing-k33202-khlynin_K_D/blob/main/lab2/img/pc1.jpg)

**PC2:**
```
/interface wireless security-profiles
set [ find default=yes ] supplicant-identity=MikroTik
/ip address
add address=172.31.255.30/30 interface=ether1 network=172.31.255.28
/ip dhcp-client
add disabled=no interface=ether1
add disabled=no interface=ether4
/ip route
add distance=1 dst-address=10.10.2.0/30 gateway=192.168.20.1
add distance=1 dst-address=10.10.3.0/30 gateway=192.168.20.1
add distance=1 dst-address=192.168.10.0/24 gateway=192.168.20.1
add distance=1 dst-address=192.168.30.0/24 gateway=192.168.20.1
/system identity
set name=PC2
```
![](https://github.com/khlinin31/2023_2024-introduction_in_routing-k33202-khlynin_K_D/blob/main/lab2/img/pc2.jpg)

**PC3:**
```
/interface wireless security-profiles
set [ find default=yes ] supplicant-identity=MikroTik
/ip address
add address=172.31.255.30/30 interface=ether1 network=172.31.255.28
/ip dhcp-client
add disabled=no interface=ether1
add disabled=no interface=ether4
/ip route
add distance=1 dst-address=10.10.1.0/30 gateway=192.168.30.1
add distance=1 dst-address=10.10.3.0/30 gateway=192.168.30.1
add distance=1 dst-address=192.168.10.0/24 gateway=192.168.30.1
add distance=1 dst-address=192.168.20.0/24 gateway=192.168.30.1
/system identity
set name=PC3
```
![](https://github.com/khlinin31/2023_2024-introduction_in_routing-k33202-khlynin_K_D/blob/main/lab2/img/pc3.jpg)

#### **5** Результаты пингов, проверки локальной связности:

![](https://github.com/khlinin31/2023_2024-introduction_in_routing-k33202-khlynin_K_D/blob/main/lab2/img/p1.jpg)
![](https://github.com/khlinin31/2023_2024-introduction_in_routing-k33202-khlynin_K_D/blob/main/lab2/img/p2.jpg)
![](https://github.com/khlinin31/2023_2024-introduction_in_routing-k33202-khlynin_K_D/blob/main/lab2/img/p3.jpg)

