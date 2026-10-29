# reflexion-arquitectonica.md
# Completa este archivo con tus respuestas al Reto 3 de la Fase 4.

## Reto 3: Reflexión Arquitectónica

### 1. Sin Dapr: ¿dónde vive el código de TTL y consumer group?

Estaría dentro de cada microservicio. El nombre del consumer group se configuraría en el cliente de Kafka de cada app, y el TTL habría que manejarlo a mano: revisar la fecha del mensaje y descartarlo si es viejo, o configurar la retención del tópico en el propio Kafka. En la práctica eso termina repartido entre el equipo de la app, que escribe el código, y el que administra Kafka, y cada equipo lo implementa a su manera. Con Dapr, esa configuración pasa a un YAML que puede gobernar el equipo de plataforma.

### 2. El patrón sidecar como frontera de responsabilidad

El equipo de producto es el que sabe que una orden vence a los 2 minutos, así que debe ser el dueño de ese valor. El equipo de plataforma es dueño del componente, del broker y de las reglas, por ejemplo un TTL máximo permitido o valores por defecto. Lo ideal es que producto declare el TTL como parte del contrato de su servicio (un parámetro en una plantilla o un Golden Path) y que la plataforma lo valide y lo aplique. Así producto no tiene que saber de Redis, y plataforma no tiene que adivinar reglas de negocio.

### 3. Limitaciones de la abstracción: ¿migración 100% transparente?

No del todo, y lo vimos en el propio laboratorio:

El TTL en el componente no funcionó. Configuré ttlInSeconds: 120 en el YAML del componente Redis, y los mensajes llegaron 14 minutos después. La documentación de Dapr muestra el TTL como un dato que se envía al publicar cada mensaje, así que para que funcione hay que tocar el código del publisher. En Azure Service Bus, en cambio, el broker lo maneja de forma nativa. Cada broker se comporta distinto.

El {namespace} cambia más de lo que parece. No solo creó un consumer group nuevo: también renombró el tópico a defaultorders. Los mensajes viejos quedaron en orders, sin nadie que los lea. En una migración real, cambiar ese campo sin plan puede dejar mensajes huérfanos.
