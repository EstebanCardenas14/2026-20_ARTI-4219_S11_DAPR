# analisis-abstraccion.md
# Completa este archivo con tus respuestas a los TODOs de la Fase 2.

## TODO 2: Análisis de la Abstracción

### 1. Migración de broker: ¿qué archivos cambiarían?

Solo el pubsub-redis.yaml. Se cambia el spec.type a pubsub.kafka y en spec.metadata se ponen los datos de Kafka, como los brokers y el consumer group. El código Python del publisher y del subscriber no se toca, porque los dos solo conocen el nombre order-pubsub y le hablan al sidecar por HTTP.

### 2. Inventario de 5 brokers soportados por Dapr

| Broker | `spec.type` en el YAML |
|---|---|
| Redis | `pubsub.redis` |
| Apache Kafka | pubsub.kafka |
| RabbitMQ | pubsub.rabbitmq |
| AWS SNS/SQS | pubsub.aws.snssqs |
| Azure Service Bus | pubsub.azure.servicebus.topics |

### 3. Manejo seguro de secretos en producción

Dapr tiene componentes de tipo secret store, que se conectan a Kubernetes Secrets, AWS Secrets Manager, Azure Key Vault o HashiCorp Vault. En el YAML, en lugar de escribir la contraseña, se usa secretKeyRef para apuntar al secreto, y Dapr lo resuelve en tiempo de ejecución. Así en Git solo queda la referencia, nunca el valor.
