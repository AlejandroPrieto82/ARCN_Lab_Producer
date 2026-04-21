# Producer Service 🚀

**Autor:** Alejandro Prieto Reyes  
**Curso:** Arquitecturas de Software (ARCN)

## Descripción

Microservicio Spring Boot que actúa como **productor de eventos** en una arquitectura orientada a eventos. Expone un endpoint REST para enviar mensajes a una cola de RabbitMQ mediante un `DirectExchange`.

## Tecnologías

- Java 17
- Spring Boot 4.0.5
- Spring AMQP (RabbitMQ)
- Spring Web
- Docker

## Imagen Docker

```bash
docker pull samuelprietor/producer-service
```

🔗 [Ver en Docker Hub](https://hub.docker.com/repository/docker/samuelprietor/producer-service/general)

## Endpoints

| Método | Ruta | Descripción |
|--------|------|-------------|
| POST | `/api/messages/send?message=<texto>` | Envía un mensaje a RabbitMQ |

### Ejemplo

```bash
curl -X POST "http://localhost:8080/api/messages/send?message=HolaMundo"
# Respuesta: Mensaje 'HolaMundo' enviado!
```

## Configuración RabbitMQ

| Propiedad | Valor |
|-----------|-------|
| Exchange | `messages.exchange` |
| Queue | `messages.queue` |
| Routing Key | `messages.routingkey` |

## Ejecutar con Docker Compose

Este repositorio incluye el `docker-compose.yml` que levanta los tres servicios juntos: **RabbitMQ**, **Producer** y **Consumer**.

### Prerrequisitos

- Docker Desktop instalado y corriendo
- Git instalado

### Pasos

```bash
# 1. Clona el repositorio
git clone https://github.com/AlejandroPrieto82/ARCN_Lab_Producer
cd ARCN_Lab_Producer

# 2. Levanta todos los servicios
docker-compose up -d

# 3. Espera ~30 segundos a que RabbitMQ esté listo, luego envía un mensaje
curl -X POST "http://localhost:8080/api/messages/send?message=HolaMundo"
# Respuesta esperada: Mensaje 'HolaMundo' enviado!

# 4. Verifica que el Consumer recibió el mensaje
docker-compose logs consumer
# Deberías ver:
# INFO  : Mensaje recibido: 'HolaMundo'
# >>> Mensaje Procesado: HolaMundo

# 5. Para detener los servicios
docker-compose down
```

### Servicios levantados

| Servicio | Puerto | Descripción |
|----------|--------|-------------|
| RabbitMQ AMQP | `5672` | Broker de mensajes |
| RabbitMQ UI | `15672` | Panel de administración (guest/guest) |
| Producer API | `8080` | Endpoint REST para enviar mensajes |
| Consumer | — | Escucha y procesa mensajes automáticamente |

### docker-compose.yml

```yaml
version: '3.8'

services:
  rabbitmq:
    image: rabbitmq:management
    container_name: rabbitmq
    hostname: rabbitmq
    ports:
      - "5672:5672"
      - "15672:15672"
    networks:
      - event_network

  producer:
    image: samuelprietor/producer-service
    container_name: producer-service
    ports:
      - "8080:8080"
    depends_on:
      - rabbitmq
    environment:
      - SPRING_RABBITMQ_HOST=rabbitmq
      - SPRING_RABBITMQ_PORT=5672
      - SPRING_RABBITMQ_USERNAME=guest
      - SPRING_RABBITMQ_PASSWORD=guest
    networks:
      - event_network

  consumer:
    image: samuelprietor/consumer-service
    container_name: consumer-service
    depends_on:
      - rabbitmq
    environment:
      - SPRING_RABBITMQ_HOST=rabbitmq
      - SPRING_RABBITMQ_PORT=5672
      - SPRING_RABBITMQ_USERNAME=guest
      - SPRING_RABBITMQ_PASSWORD=guest
    networks:
      - event_network

networks:
  event_network:
    driver: bridge
```

## Estructura del Proyecto

```
producer/
├── src/main/java/com/eci/arcn/producer/
│   ├── ProducerApplication.java
│   ├── config/
│   │   └── RabbitMQConfig.java
│   └── controller/
│       └── MessageController.java
├── src/main/resources/
│   └── application.properties
├── docker-compose.yml
├── Dockerfile
└── pom.xml
```