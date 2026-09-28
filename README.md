# ADRMediaChannels 📺

**Centro Multimedia de Alto Rendimiento para Android TV y Nvidia Shield.**

[![Web Oficial](https://img.shields.io/badge/Web_Oficial-adrmediachannels.web.app-FFB74D?style=for-the-badge&logo=googlechrome&logoColor=black)](https://adrmediachannels.web.app/)
[![Canal Telegram](https://img.shields.io/badge/Telegram-Canal_Oficial-2CA5E0?style=for-the-badge&logo=telegram&logoColor=white)](https://t.me/ADRMediaChannels)
[![Versión](https://img.shields.io/badge/Versi%C3%B3n-1.0_ARM64-0A0A0A?style=for-the-badge&border=white)](https://adrmediachannels.web.app/)

ADRMediaChannels es un centro multimedia independiente, sin publicidad y de diseño "Zero-Click", concebido para operar con máxima fluidez en ecosistemas **Android TV (Android 11+)**, optimizado específicamente para hardware con un mínimo de 3GB de RAM (Nvidia Shield TV).

Permite la indexación y reproducción de contenido local en red mediante protocolo SMB, gestión de IPTV, visor fotográfico acelerado por GPU y reproducción musical en alta fidelidad.

---

## ⚠️ Estado del Repositorio: Infraestructura (Shadow Repo)

Este repositorio opera exclusivamente como **motor de alojamiento de infraestructura backend** (integración pasiva con Firebase) y **distribuidor de binarios (Releases / OTA updates)**.

*   **Código Cerrado:** El código fuente completo (Kotlin/Compose, Astro, Reglas de Seguridad) se mantiene en repositorios locales privados.
*   **Participación Desactivada:** No se aceptan *Pull Requests* ni se revisan *Issues* en este repositorio. Cualquier PR será cerrado automáticamente.

Para soporte, reporte de fallos, dudas de hardware (betatesters) o descarga de la última versión estable (APK), dirígete exclusivamente a nuestro **[Canal Oficial de Telegram](https://t.me/ADRMediaChannels)**.

---

## ⚙️ Arquitectura y Stack Tecnológico

Aunque el código fuente es privado, la arquitectura técnica de la aplicación se basa en los siguientes estándares de ingeniería:

*   **Frontend Nativo:** Desarrollado íntegramente en Kotlin bajo **Jetpack Compose for TV**. Implementa un motor gráfico basado en Coil (forzando decodificación a 16-bits `RGB_565`) para dividir el consumo de RAM a la mitad.
*   **Doble Motor de Reproducción:**
    *   **AndroidX Media3 (ExoPlayer):** Motor principal con carga asíncrona, control dinámico de búfer (64MB para flujos 4K HDR) y soporte *passthrough* de audio inalterado a barras de sonido.
    *   **LibVLC:** Motor secundario de rescate renderizado mediante JNI (C++) para archivos *Legacy* (AVI, VOB, M2TS) garantizando compatibilidad absoluta.
*   **Protocolo de Red (SMBJ):** Extirpación de rutinas genéricas en favor de SMBJ puro acelerado por hardware (BCSecurityProvider), con enrutamiento bifurcado en corrutinas (`Dispatchers.IO.limitedParallelism`) para erradicar cuellos de botella (Network Stalls).
*   **Auto Frame Rate (AFR):** Ajuste algorítmico dinámico a nivel de kernel para sincronizar los hercios del panel OLED con los fotogramas del contenedor de vídeo, eliminando el *judder*.
*   **Backend (Firebase):** Autenticación y base de datos (Firestore) blindados mediante validación en la capa del servidor (cálculo de tokens de prueba de 16 días contra el reloj inmutable del sistema) y control anti-fraude de hardware mediante inyecciones `arrayUnion`.

---

## 🔗 Enlaces Oficiales

*   🌐 **Sitio Web Oficial:** [adrmediachannels.web.app](https://adrmediachannels.web.app/)
*   📢 **Canal de Soporte y Actualizaciones (Telegram):** [t.me/ADRMediaChannels](https://t.me/ADRMediaChannels)
*   ☕ **Colaborar con el proyecto:** [Ko-fi](https://ko-fi.com/adrmediachannels)
*   📧 **Contacto Comercial:** adrmediachannels@gmail.com

> *El diseño, código y binarios de ADRMediaChannels han sido creados por adrlucho. El autor no proporciona, aloja ni enlaza contenido multimedia protegido por derechos de autor.*
