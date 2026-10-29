# analisis-cloudevents.md
# Completa este archivo con tus respuestas al TODO 4 de la Fase 3.

## TODO 4: Análisis de Trazabilidad (CloudEvents)

### 1. El atributo `id`: ¿quién lo genera y para qué sirve?

Lo genera el sidecar de Dapr del publisher, no el código: es un UUID único por mensaje, por ejemplo 3844e0f2-3d60-4a90-842d-1a66dc01f53d. Importa porque los brokers pueden entregar el mismo mensaje más de una vez. Si el subscriber guarda los id que ya procesó, puede ignorar los repetidos y no, por ejemplo, cobrar dos veces la misma orden.

### 2. El atributo `source`: ¿qué valor tiene y de dónde lo toma Dapr?

El valor es order-publisher. Dapr lo toma del app-id del publisher, o sea, de la anotación dapr.io/app-id del Job. Sirve para saber qué servicio originó el evento sin que el publisher lo tenga que escribir.

### 3. El atributo `traceparent` y su relación con OpenTelemetry

Implementa el estándar W3C Trace Context, que es el formato que usa OpenTelemetry para propagar trazas entre servicios. Con él, una herramienta como Jaeger o Zipkin puede unir en una sola traza el paso del mensaje del publisher al broker y luego al subscriber. En mi prueba llegó vacío porque no activamos el tracing en Dapr: el campo existe en el sobre, pero se llena solo cuando la plataforma configura una política de trazas.

### 4. Valor de negocio para auditoría: atributos más útiles del sobre CloudEvents

- id, para identificar cada evento sin ambigüedad.
- source, para saber qué servicio lo produjo.
- type y el tópico, para clasificar qué tipo de evento fue.
- time, para filtrar las últimas 24 horas.
- traceparent, para seguir el recorrido completo del evento entre servicios.


