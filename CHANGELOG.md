# CHANGELOG: ADRMediaChannels

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
