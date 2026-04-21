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

Clona el repositorio de orquestación y ejecuta:

```bash
git clone https://github.com/AlejandroPrieto82/ARCN_Lab_Producer
docker-compose up -d
curl -X POST "http://localhost:8080/api/messages/send?message=Test"
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
├── Dockerfile
└── pom.xml
```