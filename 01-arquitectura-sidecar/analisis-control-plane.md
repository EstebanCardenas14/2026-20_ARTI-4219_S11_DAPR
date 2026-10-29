# analisis-control-plane.md
# Completa este archivo con tus respuestas a los TODOs de la Fase 1.

## TODO 1: Análisis del Plano de Control

### 1. ¿Qué tipo de objeto de Kubernetes es el `dapr-sidecar-injector`?

Es un Deployment que corre un pod, y está registrado en Kubernetes como un MutatingWebhookConfiguration. Ese registro le pide al API server que lo consulte cada vez que se va a crear un pod. Tiene que ser mutating porque no basta con revisar el pod: necesita modificarlo para agregarle el contenedor daprd antes de que se guarde. Un webhook de validación solo podría aceptar o rechazar el pod, no cambiarlo.

### 2. Réplicas del `dapr-operator` y comparación con Istio

Hay una sola réplica, porque es la instalación por defecto, sin alta disponibilidad. Dapr es más liviano porque no se mete con todo el tráfico de red del pod como hace Istio. Istio intercepta cada conexión y tiene que mantener actualizada la configuración de red de todos los proxies del clúster. Dapr solo actúa cuando la aplicación le habla directamente al sidecar, y su plano de control básicamente reparte unos pocos archivos de configuración (los componentes).

### 3. Flujo de inyección del sidecar

Al aplicar el Deployment, Kubernetes crea el pod. Antes de guardarlo, el API server le pregunta al injector de Dapr. El injector ve la anotación dapr.io/enabled: "true" y le agrega al pod el contenedor daprd, con su configuración. El pod ya modificado se guarda y se asigna a un nodo, y el kubelet arranca los dos contenedores: la app y el sidecar. Al iniciar, el sidecar pide su certificado de seguridad a dapr-sentry, descarga los componentes que le corresponden desde el dapr-operator, y el pod queda en 2/2.

---

## TODO 2: Exploración de los CRDs de Dapr

### 4. CRDs instalados por Dapr

> _Tu respuesta aquí_

### 5. Relación entre `components.dapr.io` y el concepto de Building Block

> _Tu respuesta aquí_
