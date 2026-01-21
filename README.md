# 🛡️ Packet Tracer: Configuración de ACL Extendidas IPv4 - Escenario 2

<div align="center">

**Laboratorio CISCO - Access Control Lists Extendidas con Nombre**

[![Cisco Packet Tracer](https://img.shields.io/badge/Cisco-Packet_Tracer-1BA0D7?style=for-the-badge&logo=cisco&logoColor=white)](https://www.netacad.com)
[![ACL Protocol](https://img.shields.io/badge/Protocol-ACL_Extendida-00A86B?style=for-the-badge)](https://www.cisco.com/)
[![CCNA](https://img.shields.io/badge/Certification-CCNA-blue?style=for-the-badge)](https://www.cisco.com/c/en/us/training-events/training-certifications/certifications/associate/ccna.html)
[![License](https://img.shields.io/badge/License-MIT-yellow?style=for-the-badge)](LICENSE)

[🎯 Objetivos](#-objetivos) • 
[📊 Tabla de Direcciones](#️-tabla-de-asignación-de-direcciones) • 
[📋 Escenario](#️-antecedentesescenario) • 
[⚙️ Configuración](#️-configuración-paso-a-paso) • 
[🔍 Verificación](#️-aplicar-y-verificar-la-acl-extendida) • 
[👨‍💻 Autor](#️-autor)

</div>

---

## 📋 Descripción del Proyecto
Este laboratorio de Cisco Packet Tracer implementa **ACL Extendidas con Nombre** para controlar el acceso desde una red local a servidores en Internet. Se configura una política granular que bloquea servicios específicos (HTTP, HTTPS, FTP, ICMP) desde hosts específicos hacia servidores específicos, demostrando el control preciso que ofrecen las ACL extendidas.

### 🎯 Objetivos
**Parte 1:** Configurar una ACL extendida con nombre con políticas específicas  
**Parte 2:** Aplicar y verificar la operación de la ACL extendida  

### 📋 Antecedentes/Escenario
En este escenario, se permiten dispositivos específicos en la LAN a varios servicios en servidores ubicados en Internet. La política requiere bloquear:
- HTTP y HTTPS desde PC1 a Server1 y Server2
- FTP desde PC2 a Server1 y Server2  
- ICMP (ping) desde PC3 a Server1 y Server2

---

## 📊 Tabla de Asignación de Direcciones

| Dispositivo | Interfaz | Dirección IP | Máscara de Subred | Gateway |
|-------------|----------|--------------|-------------------|---------|
| **RT1** | G0/0 | 172.31.1.126 | 255.255.255.224 | N/A |
| **RT1** | S0/0/0 | 209.165.1.2 | 255.255.255.252 | N/A |
| **PC1** | NIC | 172.31.1.101 | 255.255.255.224 | 172.31.1.126 |
| **PC2** | NIC | 172.31.1.102 | 255.255.255.224 | 172.31.1.126 |
| **PC3** | NIC | 172.31.1.103 | 255.255.255.224 | 172.31.1.126 |
| **Server1** | NIC | 64.101.255.254 | N/A | N/A |
| **Server2** | NIC | 64.103.255.254 | N/A | N/A |

### 🌐 Topología de Red
```
[PC1:172.31.1.101]        [Server1:64.101.255.254]
[PC2:172.31.1.102]   LAN    [RT1]   Internet   [Server2:64.103.255.254]
[PC3:172.31.1.103]          G0/0    S0/0/0
     172.31.1.96/27         209.165.1.0/30
```

---

## ⚙️ Configuración Paso a Paso

### Parte 1: Configurar una ACL Extendida con Nombre

#### Política a Implementar:
1. **Bloquear** acceso HTTP y HTTPS desde PC1 a Server1 y Server2
2. **Bloquear** acceso FTP desde PC2 a Server1 y Server2  
3. **Bloquear** acceso ICMP desde PC3 a Server1 y Server2
4. **Permitir** todo el resto del tráfico

#### Paso 1: Denegar a PC1 acceso HTTP/HTTPS a Server1 y Server2
```cisco
! Crear ACL extendida con nombre
RT1(config)# ip access-list extended ACL

! Denegar HTTP (puerto 80) de PC1 a Server1
RT1(config-ext-nacl)# deny tcp host 172.31.1.101 host 64.101.255.254 eq 80

! Denegar HTTPS (puerto 443) de PC1 a Server1
RT1(config-ext-nacl)# deny tcp host 172.31.1.101 host 64.101.255.254 eq 443

! Denegar HTTP de PC1 a Server2
RT1(config-ext-nacl)# deny tcp host 172.31.1.101 host 64.103.255.254 eq 80

! Denegar HTTPS de PC1 a Server2
RT1(config-ext-nacl)# deny tcp host 172.31.1.101 host 64.103.255.254 eq 443
```

#### Paso 2: Denegar a PC2 acceso FTP a Server1 y Server2
```cisco
! Denegar FTP (puerto 21) de PC2 a Server1
RT1(config-ext-nacl)# deny tcp host 172.31.1.102 host 64.101.255.254 eq 21

! Denegar FTP de PC2 a Server2
RT1(config-ext-nacl)# deny tcp host 172.31.1.102 host 64.103.255.254 eq 21
```

#### Paso 3: Denegar a PC3 acceso ICMP a Server1 y Server2
```cisco
! Denegar ICMP (ping) de PC3 a Server1
RT1(config-ext-nacl)# deny icmp host 172.31.1.103 host 64.101.255.254

! Denegar ICMP de PC3 a Server2
RT1(config-ext-nacl)# deny icmp host 172.31.1.103 host 64.103.255.254
```

#### Paso 4: Permitir el resto del tráfico IP
```cisco
! Permitir todo el resto del tráfico
RT1(config-ext-nacl)# permit ip any any
```

#### Paso 5: Verificar configuración ACL
```cisco
! Ver ACL con números de secuencia
RT1# show access-lists

! Ver ACL en configuración running
RT1# show running-config | begin access-list
```

**Salida Esperada:**
```
Extended IP access list ACL
    10 deny tcp host 172.31.1.101 host 64.101.255.254 eq www
    20 deny tcp host 172.31.1.101 host 64.101.255.254 eq 443
    30 deny tcp host 172.31.1.101 host 64.103.255.254 eq www
    40 deny tcp host 172.31.1.101 host 64.103.255.254 eq 443
    50 deny tcp host 172.31.1.102 host 64.101.255.254 eq ftp
    60 deny tcp host 172.31.1.102 host 64.103.255.254 eq ftp
    70 deny icmp host 172.31.1.103 host 64.101.255.254
    80 deny icmp host 172.31.1.103 host 64.103.255.254
    90 permit ip any any
```

### Parte 2: Aplicar y Verificar la ACL Extendida

#### Paso 1: Aplicar ACL a la interfaz apropiada
**Pregunta:** ¿En qué interfaz y dirección se debe aplicar la ACL?

**Respuesta:** La ACL debe aplicarse en la interfaz **G0/0** en dirección **OUT**. Esto porque:
- El tráfico a filtrar **sale** de la red 172.31.1.96/27 hacia Internet
- Las ACL extendidas deben colocarse cerca del origen
- Aplicar en OUT en G0/0 filtra tráfico desde la LAN hacia Internet

```cisco
! Aplicar ACL a interfaz G0/0 en dirección OUT
RT1(config)# interface G0/0
RT1(config-if)# ip access-group ACL out
```

#### Paso 2: Probar acceso para cada PC

**Pruebas para PC1:**
```cisco
! Desde PC1:
1. Acceder a http://64.101.255.254    → ❌ DEBE FALLAR (regla 10)
2. Acceder a https://64.101.255.254   → ❌ DEBE FALLAR (regla 20)
3. Acceder a http://64.103.255.254    → ❌ DEBE FALLAR (regla 30)
4. Acceder a https://64.103.255.254   → ❌ DEBE FALLAR (regla 40)
5. FTP a 64.101.255.254               → ✅ DEBE FUNCIONAR (no bloqueado para PC1)
6. Ping a 64.101.255.254              → ✅ DEBE FUNCIONAR (no bloqueado para PC1)
```

**Pruebas para PC2:**
```cisco
! Desde PC2:
1. Acceder a HTTP/HTTPS               → ✅ DEBE FUNCIONAR (no bloqueado para PC2)
2. FTP a 64.101.255.254               → ❌ DEBE FALLAR (regla 50)
3. FTP a 64.103.255.254               → ❌ DEBE FALLAR (regla 60)
4. Ping a servidores                  → ✅ DEBE FUNCIONAR (no bloqueado para PC2)
```

**Pruebas para PC3:**
```cisco
! Desde PC3:
1. Acceder a HTTP/HTTPS               → ✅ DEBE FUNCIONAR (no bloqueado para PC3)
2. FTP a servidores                   → ✅ DEBE FUNCIONAR (no bloqueado para PC3)
3. Ping a 64.101.255.254              → ❌ DEBE FALLAR (regla 70)
4. Ping a 64.103.255.254              → ❌ DEBE FALLAR (regla 80)
```

#### Paso 3: Verificar contadores ACL
```cisco
! Verificar qué reglas están coincidiendo
RT1# show ip access-lists ACL

! Limpiar contadores para nuevas pruebas
RT1# clear access-list counters ACL

! Ejemplo de salida después de pruebas:
Extended IP access list ACL
    10 deny tcp host 172.31.1.101 host 64.101.255.254 eq www (5 matches)
    20 deny tcp host 172.31.1.101 host 64.101.255.254 eq 443 (3 matches)
    ...
    90 permit ip any any (25 matches)
```

---

## 🔍 Análisis de Configuración

### 🎯 Estructura de la ACL Configurada
| Número | Acción | Protocolo | Origen | Destino | Puerto | Propósito |
|--------|--------|-----------|--------|---------|--------|-----------|
| 10 | deny | tcp | PC1 | Server1 | 80 | Bloquear HTTP |
| 20 | deny | tcp | PC1 | Server1 | 443 | Bloquear HTTPS |
| 30 | deny | tcp | PC1 | Server2 | 80 | Bloquear HTTP |
| 40 | deny | tcp | PC1 | Server2 | 443 | Bloquear HTTPS |
| 50 | deny | tcp | PC2 | Server1 | 21 | Bloquear FTP |
| 60 | deny | tcp | PC2 | Server2 | 21 | Bloquear FTP |
| 70 | deny | icmp | PC3 | Server1 | - | Bloquear Ping |
| 80 | deny | icmp | PC3 | Server2 | - | Bloquear Ping |
| 90 | permit | ip | any | any | - | Permitir resto |

### 📊 Consideraciones de Diseño
1. **Orden de Reglas:** Específicas primero, general al final
2. **Ubicación:** Cerca del origen (G0/0 OUT)
3. **Dirección:** OUT filtra tráfico saliente de la LAN
4. **Números de Secuencia:** Automáticos, permiten edición posterior
5. **Política Implícita:** Al final hay `permit ip any any`

### 🔧 Comandos Clave Utilizados
```cisco
! Crear ACL extendida con nombre
ip access-list extended NOMBRE

! Reglas específicas con host
deny tcp host [IP-origen] host [IP-destino] eq [puerto]
deny icmp host [IP-origen] host [IP-destino]

! Aplicar ACL a interfaz
interface [interfaz]
 ip access-group NOMBRE [in|out]

! Verificación
show access-lists [NOMBRE]
show ip access-lists
clear access-list counters [NOMBRE]
```

---

## 💡 Conceptos Fundamentales Aprendidos

### 🎯 ACL Extendidas con Nombre vs Numeradas
| Característica | ACL Extendida Numerada | ACL Extendida con Nombre |
|----------------|------------------------|--------------------------|
| **Sintaxis** | `access-list 100 deny tcp...` | `ip access-list extended NOMBRE` |
| **Rango** | 100-199, 2000-2699 | Cualquier nombre descriptivo |
| **Edición** | Limitada (solo añadir al final) | Completa (insertar, eliminar, modificar) |
| **Documentación** | Poco descriptiva | Muy descriptiva |
| **Recomendación** | Para configuraciones simples | Para configuraciones complejas |

### 🌐 Protocolos y Puertos Utilizados
| Servicio | Protocolo | Puerto | Descripción |
|----------|-----------|--------|-------------|
| **HTTP** | TCP | 80 | Tráfico web no encriptado |
| **HTTPS** | TCP | 443 | Tráfico web encriptado |
| **FTP** | TCP | 21 | Control FTP (puerto de comando) |
| **ICMP** | ICMP | - | Protocolo de mensajes de control |
| **FTP Data** | TCP | 20 | Datos FTP (no bloqueado en este lab) |

### 📖 Mejores Prácticas Implementadas
1. **Reglas Específicas Primero:** `deny` específicas antes de `permit` generales
2. **Hosts Específicos:** Uso de `host` para direcciones individuales
3. **Documentación Interna:** Nombres descriptivos para ACL
4. **Verificación Antes de Aplicar:** Revisar ACL con `show` commands
5. **Contadores:** Uso de `clear` y `show` para verificar operación

---

## 🚀 Solución de Problemas de ACL Extendida

### Síntomas Comunes y Soluciones

#### ❌ ACL no está filtrando tráfico
```cisco
! Verificar aplicación correcta
RT1# show ip interface G0/0
! Buscar: "Outgoing access list is ACL"

! Verificar dirección (in/out)
RT1# show running-config interface G0/0

! Probar conectividad básica
PC1> ping 64.101.255.254
```

#### ❌ Tráfico legítimo bloqueado incorrectamente
```cisco
! Verificar orden de reglas
RT1# show access-lists ACL

! Ver contadores para ver qué regla coincide
RT1# show ip access-lists ACL

! Insertar regla en posición específica
RT1(config)# ip access-list extended ACL
RT1(config-ext-nacl)# 15 permit tcp host 172.31.1.101 any eq 80
```

#### ❌ Problemas con puertos FTP
```cisco
! FTP usa puerto 21 para control y 20 para datos
! Para bloquear FTP completamente:
deny tcp host [origen] host [destino] eq ftp
deny tcp host [origen] host [destino] eq ftp-data
```

### 📋 Checklist de Verificación ACL
- [ ] Reglas en orden correcto (específicas → generales)
- [ ] Sintaxis correcta para protocolos y puertos
- [ ] ACL aplicada en interfaz correcta
- [ ] Dirección correcta (in/out según flujo)
- [ ] Regla `permit ip any any` al final (si es política)
- [ ] Contadores incrementando en pruebas
- [ ] No hay reglas conflictivas

---

## 📚 Recursos Adicionales

### Documentación Oficial Cisco
- [Cisco Extended ACL Configuration](https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/sec_data_acl/configuration/15-mt/sec-acl-15-mt-book.html)
- [Named ACL Configuration Guide](https://www.cisco.com/c/en/us/td/docs/switches/lan/catalyst3560/software/release/12-2_55_se/configuration/guide/3560_scg/swacl.html)
- [ACL Port Numbers Reference](https://www.cisco.com/c/en/us/support/docs/security/ios-firewall/23602-confaccesslists.html)

### Libros Recomendados
- "CCNA 200-301 Official Cert Guide" - ACL Chapter
- "Cisco IOS Access Lists" - O. Held
- "Network Security with ACLs" - Cisco Press

### Laboratorios Relacionados
- **ACL Estándar:** Filtrado básico por dirección de origen
- **ACL Reflexivas:** Control de sesiones stateful
- **ACL Basadas en Tiempo:** Filtrado por horario específico
- **ACL para IPv6:** Filtrado en redes IPv6

### 🎓 Preguntas de Práctica CCNA
1. ¿Cuál es la diferencia entre `eq 80` y `eq www` en ACL?
2. ¿Por qué se recomienda usar ACL con nombre en configuraciones complejas?
3. ¿Cómo afecta la dirección (in/out) al filtrado de tráfico?
4. ¿Qué comando permite insertar una regla en medio de una ACL existente?

---

## 👨‍💻 Autor

<div align="center">

**Darwin Manuel Ovalles Cesar**

<p align="center">
<a href="https://www.linkedin.com/in/darwin-manuel-ovalles-cesar-dev" target="_blank">
<img align="center" src="https://raw.githubusercontent.com/rahuldkjain/github-profile-readme-generator/master/src/images/icons/Social/linked-in-alt.svg" alt="LinkedIn - Darwin Ovalles" height="40" width="50" />
</a>
<a href="https://github.com/dovalless" target="_blank">
<img align="center" src="https://raw.githubusercontent.com/rahuldkjain/github-profile-readme-generator/master/src/images/icons/Social/github.svg" alt="GitHub - Darwin Ovalles" height="40" width="50" />
</a>
</p>

💼 **LinkedIn**: [darwin-manuel-ovalles-cesar-dev](https://www.linkedin.com/in/darwin-manuel-ovalles-cesar-dev/)  
🌐 **GitHub**: [@dovalless](https://github.com/dovalless)  
🎓 **Certificaciones**: CCNA, Network+, A+  

*"Las ACL extendidas son como un guardia de seguridad altamente entrenado: no solo verifica de dónde vienes (dirección IP), sino también a dónde vas (destino), qué llevas (protocolo) y para qué propósito (puerto). Este nivel de granularidad es esencial para la seguridad moderna de red."*

**#Cisco #PacketTracer #ACL #ExtendedACL #NetworkSecurity #CCNA #FirewallRules**

</div>

---

## 📄 Licencia

Este proyecto está bajo la Licencia MIT. Consulta el archivo [LICENSE](LICENSE) para más detalles.

```
MIT License
Copyright (c) 2024 Darwin Manuel Ovalles Cesar
```

---

## 🙏 Agradecimientos

- **Cisco Networking Academy** - Por Packet Tracer y recursos educativos
- **Comunidad de Seguridad de Redes** - Por compartir mejores prácticas
- **Profesionales de IT** - Por su trabajo en proteger infraestructuras críticas

<div align="center">

### ⭐ Si este laboratorio te ayudó a entender ACL extendidas, compártelo ⭐

### 🔄 **Reflexión Final:**
*"Configurar ACL es como programar un semáforo inteligente: cada regla es una luz que dice 'verde' o 'rojo' para tipos específicos de tráfico. Las ACL extendidas nos permiten ser tan específicos como 'rojo para camiones que vienen de la calle A hacia la calle B entre 9am y 5pm'."*

**Desarrollado con 💙 para futuros ingenieros de seguridad**

---
*Laboratorio completado: Packet Tracer - Configure Extended IPv4 ACLs - Scenario 2*  
*Habilidades demostradas: ACL Extendidas, Filtrado por Puerto, Control por Host, Verificación de Contadores*

</div>
```
