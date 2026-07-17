# MiniIdM - Infraestructura de Identidad Segura para la FIS

## 🎯 Objetivo

Diseñar, implementar y evaluar una infraestructura segura de autenticación y servicios de directorio para la FIS, integrando:

- Autenticación Kerberos (SSO con tickets)
- Infraestructura de Llave Pública (PKI)
- Servicios de Directorio LDAP
- Alta Disponibilidad (HA) en Linux

---

## 🏗️ Arquitectura del Sistema

### Componentes y Distribución

| VM | IP | Rol |
|---|---|---|
| **ca** | 192.168.56.10 | Autoridad Certificadora (PKI) |
| **ldap1** | 192.168.56.11 | Servidor LDAP Maestro |
| **ldap2** | 192.168.56.12 | Servidor LDAP Réplica |
| **kdc1** | 192.168.56.13 | KDC Primario (Kerberos) |
| **kdc2** | 192.168.56.14 | KDC Secundario (Kerberos) |
| **lb-web** | 192.168.56.15 | Balanceador HAProxy + Servicio Web |

### Diagrama de Red
