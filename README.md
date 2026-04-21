# Home Automation Stack

Stack de automatización del hogar basado en Docker Compose con Home Assistant, Frigate NVR y Mosquitto MQTT.

## Servicios

### Home Assistant
Plataforma central de automatización del hogar.
- **Imagen:** `ghcr.io/home-assistant/home-assistant:stable`
- **Red:** modo host
- **Componentes personalizados:** Frigate, HACS, Tapo Control
- **Configuración:** `ha/config/`

### Frigate
NVR (Network Video Recorder) con detección de objetos por IA.
- **Imagen:** `ghcr.io/blakeblackshear/frigate:stable`
- **Red:** modo host
- **Configuración:** `frigate/config.yml`
- **Media:** `frigate/media/`

#### Cámaras configuradas
| Nombre | Descripción | Objetos detectados |
|--------|-------------|-------------------|
| `puerta` | Cámara de la puerta exterior | persona, bicicleta, moto |
| `patio` | Cámara del patio | persona, perro, gato |

#### Características de Frigate
- **Detector:** OpenVINO (GPU Intel)
- **Aceleración de hardware:** VAAPI (Intel)
- **Detección de audio:** ladrido, alarma de incendio, gritos, conversación
- **Búsqueda semántica:** habilitada
- **Retención de grabaciones:** 30 días
- **Retención de snapshots:** 30 días (personas: 15 días)

### Mosquitto
Broker MQTT para comunicación entre servicios.
- **Imagen:** `eclipse-mosquitto`
- **Puertos:** `1883` (MQTT), `9001` (WebSockets)
- **Autenticación:** habilitada (sin conexiones anónimas)
- **Configuración:** `mosquitto/conf/mosquitto.conf`

## Estructura de directorios

```
.
├── docker-compose.yaml
├── frigate/
│   ├── config.yml          # Configuración de Frigate
├── ha/
│   └── config/             # Configuración de Home Assistant
│       ├── configuration.yaml
│       ├── automations.yaml
│       ├── scripts.yaml
│       ├── scenes.yaml
│       ├── secrets.yaml
└── mosquitto/
    ├── conf/               # Configuración del broker
```

## Variables de entorno

Crear un archivo `.env` en la raíz con las siguientes variables:

```env
FRIGATE_MQTT_USER=<usuario_mqtt>
FRIGATE_MQTT_PASSWORD=<contraseña_mqtt>
FRIGATE_RTSP_USER=<usuario_rtsp>
FRIGATE_RTSP_PASSWORD=<contraseña_rtsp>
FRIGATE_RTSP_USER=<usuario_rtsp>
FRIGATE_IP_CAMERA_PUERTA=<ip_camara_puerta>
FRIGATE_IP_CAMERA_PATIO=<ip_camara_patio>
FRIGATE_TAPO_TOKEN=<token_tapo>
HOST_IP=<ip_del_host>
```

## Uso

### Iniciar los servicios
```bash
docker compose up -d
```

### Detener los servicios
```bash
docker compose down
```

### Ver logs
```bash
# Todos los servicios
docker compose logs -f

# Servicio específico
docker compose logs -f frigate
docker compose logs -f homeassistant
docker compose logs -f mosquitto
```

### Reiniciar un servicio
```bash
docker compose restart frigate
```

## Acceso a las interfaces web

| Servicio | URL |
|----------|-----|
| Home Assistant | `http://<HOST_IP>:8123` |
| Frigate | `http://<HOST_IP>:5000` |

## Requisitos del host

- Docker y Docker Compose
- GPU Intel con soporte VAAPI (`/dev/dri/renderD128`)
- Drivers Intel OpenVINO (para detección por GPU)
