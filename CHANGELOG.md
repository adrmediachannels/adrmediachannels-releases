# CHANGELOG: ADRMediaChannels

# ADRMediaChannels v1.0.2 - Free & Open Architecture

Esta actualización marca un hito estructural en el proyecto. ADRMediaChannels pasa a un modelo 100% libre, extirpando cualquier barrera de entrada o límite de uso, y reconstruyendo desde cero los motores de reproducción y la estabilidad de red para operar bajo máxima exigencia en dispositivos como la Nvidia Shield TV.

## 🔓 Modelo 100% Gratuito y Abierto
- **Autenticación Silenciosa (Zero-Friction):** Eliminado el muro de pago de 16 días, la restricción de cuentas por correo electrónico y el límite de dispositivos de hardware por usuario.
- **Acceso Directo:** La aplicación emplea ahora una identidad local persistente y anónima. Instalar y usar.
- **Backend Optimizado:** Las reglas de Firebase Firestore se han reescrito para priorizar la estabilidad operativa (consumo de CPU del 0%) manteniendo un Kill-Switch administrativo exclusivo para auditorías de seguridad, sin afectar la experiencia de uso.

## ⚙️ Nuevos Motores de Decodificación (Zero-Lag)
- **Segregación Quirúrgica:** Se ha sustituido el reproductor monolítico obsoleto por una arquitectura híbrida especializada:
  - **Motor de Vídeo (LibVLC):** Integración nativa mediante JNI en C++ para asegurar una lectura fluida en red (SMB) de formatos Legacy y pesados (MKV, VOB, AVI, M2TS) sin transcodificación por parte del servidor.
  - **Motor de Audio (Media3 / ExoPlayer):** Nueva implementación aligerada con desactivación explícita de `Audio Offload` para garantizar compatibilidad bit-perfect en pistas musicales de alta fidelidad (FLAC/MP3) directamente sobre el hardware.

## 🎬 Movimiento de Cine (AFR)
- **Auto Frame Rate (AFR):** Implementada la sincronización automática de hercios entre el panel del televisor y el framerate nativo del contenido (23.976, 24, 25, 50, 60 Hz). Errradica el *judder* manteniendo intacta la resolución 4K de la interfaz de usuario.

## 🛡️ Estabilidad UI y Red
- **Cero Ghosting en UI:** Purgados los cierres forzados por pérdida de puntero de foco en Compose TV al navegar rápidamente entre subcarpetas.
- **Debounce de I/O de Red:** Implementado un escudo antiahogo en el cliente SMB. Retrasos dinámicos (150ms a 300ms) durante la exploración rápida con el D-Pad para evitar la saturación del servidor NAS cuando se cargan directorios con miles de archivos.

## 🛠️ Notas de Instalación y Requisitos
La aplicación requiere un ecosistema de hardware capaz de absorber el ancho de banda del protocolo SMB (ráfagas pesadas) sin caché de servidor intermediario. 
- **SO Mínimo:** Android 11+ (API 30).
- **Hardware Recomendado:** Nvidia Shield TV o TV Box equivalente (Arquitectura 64-bits, Mínimo 3 GB de RAM).

## [1.0.1] - 2026-09-30

### 🚀 Added (Cambios Estructurales)
- **Motor de Vídeo Absoluto (LibVLC):** Extirpación completa de `VodPlayerActivity`. La reproducción de vídeo se ha unificado al 100% bajo el motor C++ nativo de LibVLC (`VlcPlayerActivity`), garantizando soporte absoluto para 4K HDR, Auto Frame Rate (AFR) y Audio Passthrough HD hacia barras de sonido y receptores A/V.
- **Motor de Audio Dedicado (ExoPlayer/Media3):** Refactorización profunda de `AudioPlayerActivity`. Se ha delegado el procesamiento de flujos puramente auditivos y la extracción de metadatos (carátulas, ID3 tags) a la librería nativa Jetpack Media3, implementando un `DefaultLoadControl` con coraza dinámica de búferes para proteger la memoria RAM.

### ⚡ Changed (Rendimiento y Optimización)
- **Protección de Red en Navegación (Debouncing):** Erradicación del *Blind Queueing* en las cuadrículas del módulo de Fotos y Servidores. Inyección de un sistema estricto de retardo dinámico (*debouncing* de 150-300ms) y cancelación agresiva de corrutinas acopladas al ciclo de vida (*Lifecycle Scoping*) para evitar la saturación del *Thread Pool* de la CPU y del socket SMBJ durante el scroll rápido con el D-Pad.
- **Motor de Pases de Diapositivas:** Transición arquitectónica de *Stream & Decode* a *Download & Decode* en `ImageRepository`. Las imágenes pesadas ahora se descargan asíncronamente en una caché local temporal ultrarrápida antes de decodificarse directamente en la VRAM de la GPU, eliminando las fluctuaciones y caídas por timeout en la red SMB.
- **Metrónomo Asíncrono:** Sustitución del antiguo pre-cargador de imágenes por un Metrónomo Asíncrono (`async/await`) que garantiza transiciones visuales de tiempo exacto y estricto, absorbiendo descargas lentas en segundo plano sin reiniciar los *sockets* ni interrumpir el audio BGM.

### 🐛 Fixed (Corrección de Errores)
- **Fugas de Foco y Ghosting en UI:** Implementados escudos de estado (`isNewFolderLock`) y limpieza explícita de rastros de anclaje en el *FocusTracker* al retroceder por subdirectorios, erradicando los cierres forzados del motor de Compose TV al intentar enfocar elementos que ya no están en pantalla.
- **Sincronización de Caché en Audio:** Corregido el conflicto de compatibilidad en la obtención de carátulas dentro de `AudioPlayerActivity`, adaptando la llamada de red a la nueva arquitectura de inyección de directorio de caché monolítica.

Todas las actualizaciones y cambios arquitectónicos notables de este proyecto se documentarán en este archivo.

Este proyecto no utiliza versionado semántico estricto, sino iteraciones de compilación directa orientadas a producción para Android TV.

## [1.0.0] - 2026-09-28

### Lanzamiento Inicial: Arquitectura y Capacidades Base (Added)

**Orígenes de Datos y Conectividad**
- Explorador de archivos dual: Acceso a almacenamiento local (memoria interna/dispositivos USB) y escaneo de red local para servidores NAS/PC (Protocolo SMBJ estricto).
- Módulo IPTV & Radio: Analizador sintáctico de listas M3U/M3U8, categorización automática por grupos y gestión de canales favoritos persistidos en SQLite (Room).

**Motor de Vídeo (LibVLC Integrado)**
- Aceleración por hardware (MediaCodec/HWC) forzada para decodificación 4K HDR fluida en múltiples contenedores (MKV, AVI, MP4, VOB, M2TS).
- Enrutamiento *Audio Passthrough* (`--aout=audiotrack`, `--spdif`) para delegar formatos HD (Dolby Atmos, TrueHD, DTS:X) al receptor AV o barra de sonido.
- Gestor automático de sincronización de fotogramas (AFR) contra los hercios del panel de la TV.
- Memoria de reproducción (`PlaybackMemoryManager`) con algoritmo LRU: guarda la posición exacta de vídeos inacabados y lanza un cuadro de diálogo para reanudar.
- Selector interactivo en vivo para conmutación de pistas de audio y subtítulos.

**Motor de Audio (Media3 / ExoPlayer)**
- Reproducción de alta fidelidad (MP3, FLAC) con búfer de red ampliado para prevenir latencia.
- Extracción de metadatos incrustados (ID3/Vorbis) para inyección de Carátulas, Artista y Álbum en el sistema operativo (MediaSession) y pantalla.
- Generación automática de historial reciente limitado a 20 elementos.

**Motor Fotográfico y UI Visual**
- Visor UHD con precarga asíncrona de miniaturas y decodificación orientada a VRAM (`ALLOCATOR_HARDWARE`) para protección de la memoria de la Shield TV.
- Controles interactivos con D-Pad: Zoom progresivo (hasta 2.5x), paneo fluido XY y rotación angular.
- Pase de diapositivas inteligente con intervalo de transición personalizable, orden secuencial/aleatorio y banda sonora de fondo (BGM) integrada.

**Gestor de Aplicaciones Nativas**
- Detección dual de paquetes: Lista aplicaciones diseñadas para TV (Leanback) y aplicaciones móviles forzadas (Sideload).
- Modo de edición "Gravedad Cero": Reordenación espacial de aplicaciones en la cuadrícula usando las flechas direccionales.
- Acción dual para ejecución instantánea o aislamiento de aplicaciones indeseadas (Ocultar) en una base de datos secundaria.

**Seguridad, Interfaz y Backend**
- Interfaz gráfica (Jetpack Compose TV) 100% mapeada para control por mando a distancia. Incorpora salva-pantallas flotante de trayectorias variables para evitar retención de imagen en paneles OLED durante la reproducción de emisoras de radio.
- Cifrado AES-256-GCM (`CryptoManager`) apoyado en Android KeyStore para el almacenamiento de contraseñas de red en local.
- Backend en Firebase Firestore con Reglas de Seguridad (RBAC): Control de acceso, cálculo inmutable de caducidad del modo prueba (16 días) y vector antifraude con límite estricto de 3 ID de hardware por usuario.
