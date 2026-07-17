# MiniIdM - Infraestructura de Identidad Segura para la FIS

## Descripción general

Este proyecto implementa una infraestructura de autenticación y directorio distribuida sobre Ubuntu Server, con integración de Kerberos, LDAP, PKI, HAProxy y Prometheus. El objetivo es ofrecer un entorno seguro y resiliente para la FIS, con soporte para SSO, certificados digitales y alta disponibilidad.

## Objetivo

Diseñar, implementar y evaluar una infraestructura segura de autenticación y servicios de directorio para la FIS, integrando:

- Autenticación Kerberos con SSO mediante tickets
- Infraestructura de llave pública (PKI)
- Servicios de directorio LDAP
- Alta disponibilidad en Linux
- Monitoreo con Prometheus

---

## Arquitectura del sistema

### Componentes y distribución

| VM | IP | Rol |
|---|---|---|
| ca | 192.168.56.10 | Autoridad certificadora (PKI) |
| ldap1 | 192.168.56.11 | Servidor LDAP maestro |
| ldap2 | 192.168.56.12 | Servidor LDAP réplica |
| kdc1 | 192.168.56.13 | KDC primario (Kerberos) |
| kdc2 | 192.168.56.14 | KDC secundario (Kerberos) |
| lb-web | 192.168.56.15 | Balanceador HAProxy + servicio web |

---

## Componentes implementados

### 1. PKI (OpenSSL - ECDSA)
- CA raíz para la FIS
- Certificados emitidos para LDAP, Kerberos y web
- Algoritmo ECDSA con curva prime256v1
- TLS para LDAPS y HTTPS

### 2. LDAP (OpenLDAP)
- Estructura DIT: dc=fis,dc=epn,dc=ec
- Usuarios de prueba: jperez, malvan, dnoboa, jsaeteros
- Replicación maestro-réplica en tiempo real

### 3. Kerberos (MIT)
- Realm: FIS.EPN.EC
- KDC primario y secundario
- Principals para usuarios y servicios LDAP/HTTP

### 4. Alta disponibilidad (HA)
- LDAP con HAProxy entre ldap1 y ldap2
- Failover automático para Kerberos
- Monitoreo con Prometheus

### 5. Servicio web (Apache + SPNEGO)
- Autenticación Kerberos + SPNEGO
- Uso de certificados emitidos por la PKI

### 6. Monitoreo (Prometheus)
- Node Exporter en todas las VMs
- Métricas de CPU, memoria, disco y red

---

## Pruebas de alta disponibilidad

| Experimento | Resultado | Tiempo de recuperación |
|---|---|---|
| Crash de ldap1 | ✅ Failover a ldap2 | ~2-3 s |
| Partición de red | ✅ Failover a ldap2 | ~2-3 s |
| Certificado expirado | ❌ Conexión rechazada | N/A |
| Fallo de KDC1 | ✅ Failover a KDC2 | ~3.14 s |

Tasa de autenticaciones exitosas: 100%

---

## Tecnologías utilizadas

| Componente | Tecnología | Versión |
|---|---|---|
| LDAP | OpenLDAP | 2.6.10 |
| Kerberos | MIT Kerberos | 1.22.1 |
| PKI | OpenSSL | 3.x |
| Balanceo | HAProxy | 3.2.9 |
| Servicio web | Apache + mod_auth_gssapi | 2.4 |
| Monitoreo | Prometheus | 2.53.5 |
| SO | Ubuntu Server | 26.04 LTS |

---

## Requisitos previos

- VirtualBox con 6 VMs Ubuntu Server 26.04 LTS
- Red interna: 192.168.56.0/24
- SSH habilitado en todas las VMs
- Usuario de acceso configurado como vboxuser

---

## Inicio rápido

```bash
git clone https://github.com/JASL22/SaeterosJ-MiniIdM.git
cd SaeterosJ-MiniIdM
make help
make all
```

> Nota: el despliegue asume que las VMs están encendidas, accesibles por SSH y que los archivos de configuración están presentes en la carpeta configs.

---

## Uso del Makefile

Los targets disponibles son:

- make help: muestra la ayuda del proyecto
- make install-ldap: instala y configura OpenLDAP
- make install-kerberos: instala y configura Kerberos
- make install-haproxy: instala y configura HAProxy
- make install-sssd: instala y configura SSSD
- make install-prometheus: instala y configura Prometheus
- make test-failover: ejecuta una prueba básica de resiliencia
- make clean: limpia archivos temporales

---

## Monitoreo

- Interfaz de Prometheus: http://192.168.56.10:9090
- Targets: http://192.168.56.10:9090/classic/targets

![Vista de Prometheus](image.png)

---

## Estructura del repositorio

- configs/ldap/: archivos de configuración de OpenLDAP
- configs/kerberos/: configuración de Kerberos
- configs/haproxy/: configuración de HAProxy
- configs/sssd/: configuración de SSSD
- configs/prometheus/: configuración de Prometheus

---

## Evidencias del proyecto

A continuación se presentan enlaces rápidos a las evidencias más relevantes del proyecto. Al hacer clic, podrás abrir la información o imagen relacionada.

### 1. Arquitectura del sistema
- [Vista general del proyecto](image.png)

### 2. PKI
- [Configuración de certificados y PKI](configs/kerberos/krb5.conf)
- [Documentación de evidencias](docs/evidencias.md)

### 3. LDAP
- [Configuración LDAP](configs/ldap/slapd-config.ldif)
- [Estructura de usuarios y directorio](configs/ldap/base-structure.ldif)

### 4. Kerberos
- [Configuración de Kerberos](configs/kerberos/krb5.conf)

### 5. HAProxy (balanceo de carga)
- [Configuración de HAProxy](configs/haproxy/haproxy.cfg)

### 6. Servicio web (Apache + SPNEGO)
- [Archivo de referencia del proyecto](README.md)

### 7. Monitoreo (Prometheus)
- [Configuración de Prometheus](configs/prometheus/prometheus.yml)
- [Vista de targets](http://192.168.56.10:9090/classic/targets)

> Para una vista resumida de las evidencias, revisa [docs/evidencias.md](docs/evidencias.md). Además, en la carpeta [docs](docs) se encuentran las capturas de monitoreo y rendimiento del proyecto.

---

## Autor

**Jhonn Alexander Saeteros Laime**  
