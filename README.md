# Phishing Awareness & Cyber Threat Intelligence (CTI) Lab 🎣🛡️

> **Laboratorio integral de simulación de ingeniería social (GoPhish), despliegue de infraestructura de correo segura (Postfix/Dovecot/BIND9) e indexación automatizada de fuentes abiertas de inteligencia de amenazas (OSINT/CTI) en Python.**  
> *Curso de Especialización en Ciberseguridad (CETI) – CIFP Juan de Colonia.*

---

## 📌 Visión general del proyecto

Este repositorio documenta el diseño, despliegue y explotación de un entorno corporativo aislado para evaluar y mitigar el vector de ataque del factor humano frente a campañas de ingeniería social (SPEAR-Phishing y BEC). 

El proyecto integra:
1. **Infraestructura de red y correo interna:** Servidor autoritativo DNS (BIND9), motor de correo SMTP con relay controlado (Postfix) y acceso de cliente cifrado mediante IMAPS/TLS (Dovecot).
2. **Plataforma de simulación y métricas:** Orquestación y segmentación de GoPhish con monitorización forense de tráfico de red (`tcpdump` / Wireshark).
3. **Módulo CTI en Python:** Script de monitorización continua sobre feeds públicos de inteligencia en Telegram para parametrizar señuelos basados en amenazas reales en circulación.

---

## 🏗️ Topología del laboratorio

El entorno opera sobre un segmento de red virtualizado estricto (`192.168.10.0/24`) sin salida directa a Internet para preservar el aislamiento de pruebas:

* **Servidor Central (Ubuntu 24.04 Server - `192.168.10.10`):**
  * `BIND9`: Resolución de nombres corporativos (`mail.novatec.test`).
  * `Postfix`: Servicio SMTP local restringido a `127.0.0.0/8` para prevenir *open relay*.
  * `Dovecot`: Acceso seguro IMAP sobre TLS (puerto 993) con autenticación en texto plano deshabilitada.
  * `GoPhish`: Plataforma de simulación confinada:
    * Panel administrativo: `127.0.0.1:3333` (confinado a loopback).
    * Servidor de captura / Landing page: `0.0.0.0:8080`.
* **Puesto Cliente (Windows 11 - `192.168.10.20`):**
  * Estación de trabajo corporativa con cliente de correo Mozilla Thunderbird y navegador web para interacción con los vectores de prueba.

---

## 🐍 Motor de Inteligencia de Amenazas (`telegram_cti_indexer.py`)

Para evitar el uso de plantillas de ataque estáticas o desactualizadas, se diseñó un indexador automatizado en Python que procesa fuentes abiertas de CTI (Cyber Threat Intelligence) y canales institucionales/especializados:

* **Fuentes monitorizadas:** INCIBE, CCN-CERT, vx-underground, DarkWebInformer, MalwareBazaar, OpenPhish, BleepingComputer, TheHackerNews, RansomfeedNews.
* **Mecanismo:** Descarga e inspección de contenido mediante expresiones regulares (`re`) para detectar señuelos recurrentes (fraude de facturas, avisos urgentes de acceso, bypass de MFA, stealer/malware bancario).
* **Salida estructurada:** Generación de alertas en formato JSON (`telegram_cti_feeds.json`) directamente mapeables a las campañas formativas.

### Ejecución del script:

```bash
python src/telegram_cti_indexer.py
```

---

## 📊 Telemetría y Análisis de Red

* **Inspección de tráfico:** Captura y análisis de flujos mediante `tcpdump` filtrando tráfico entre el servidor y el puesto cliente (`ports 25, 993, 8080`).
* **Verificación de cifrado:** Validación de tránsito seguro en negociaciones SSL/TLS mediante comprobación de huellas criptográficas SHA-256 en Thunderbird.
* **Correlación de eventos:** Traza cruzada entre `Message-ID` de Postfix, ID de registro en la base de datos de GoPhish y sellos temporales en peticiones HTTP.

---

## 📈 Indicadores de Rendimiento y Riesgo (KPI / KRI)

El marco formativo se apoya en un sistema auditable de indicadores:
* **Tasa de Clic (TC):** Medición de porcentaje de interacción frente al total de impactos entregados.
* **Tasa de Notificación (TN):** Medición de reporte formal al buzón de seguridad corporativo en formato `.eml` intacto.
* **Tiempo hasta la Notificación ($T_{notif}$):** Mediana de respuesta de los empleados para la activación temprana de medidas de contención.

---

## 📄 Licencia

Este proyecto se distribuye bajo la licencia **MIT**.
