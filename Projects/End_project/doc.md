# Análisis de Requerimientos para la Arquitectura en AWS

## 1. Introducción

El objetivo de este análisis es diseñar una arquitectura en la nube utilizando AWS, que permita la implementación de un sistema para el análisis de impactos de la ansiedad.
El sistema debe ser escalable, seguro y altamente disponible, cumpliendo con las necesidades especificadas.

## 2. Requerimientos funcionales (RF)

### 2.1. Acceso y autenticación

- **RF-01:** El sistema debe permitir que los usuarios se registren con correo electrónico y contraseña o mediante proveedores externos (Amazon Cognito).
- **RF-02:** Se debe implementar autenticación multifactor (MFA) para usuarios administrativos y críticos.
- **RF-03:** El sistema debe permitir la gestión de roles y permisos.

### 2.2. Procesamiento de datos

- **RF-04:** El sistema debe permitir a los usuarios ingresar datos manualmente o mediante archivos CSV.
- **RF-05:** El procesamiento de datos debe realizarse en tiempo real mediante servicios serverless o contenedores en AWS.
- **RF-06:** Los resultados del análisis deben presentarse en formatos visuales e informes descargables (PDF).

### 2.3. Gestión del sistema

- **RF-07:** Los administradores deben poder modificar, eliminar y agregar datos de referencia al sistema.
- **RF-08:** El sistema debe permitir soporte técnico remoto para resolución de incidentes.
- **RF-09:** Se debe almacenar un historial de accesos y cambios en los datos para auditoría.

### 2.4. Disponibilidad y rendimiento

- **RF-10:** El sistema debe estar disponible 24/7 con un nivel de disponibilidad del 99.9%.
- **RF-11:** El tiempo de respuesta de cualquier solicitud no debe superar 3 segundo en condiciones normales de uso.
- **RF-12:** El sistema debe permitir el acceso simultáneo de hasta 10,000 usuarios sin degradación del servicio.

## 3. Requerimientos no funcionales-

El sistema debe garantizar alta disponibilidad y resiliencia frente a fallos, asegurando que no haya pérdida de datos ni interrupciones de servicio.

### 3.1. Escalabilidad

- **RNF-01:** La infraestructura debe escalar horizontalmente, aumentando o reduciendo instancias según la carga.
- **RNF-02:** Se debe usar Auto Scaling en los microservicios para gestionar variaciones en la demanda.

### 3.2. Seguridad

- **RNF-03:** Toda la comunicación debe estar cifrada.
- **RNF-04:** Se debe implementar detección de intrusos y ataques DDoS con AWS Shield.
- **RNF-05:** Se debe implementar control de acceso granular mediante AWS IAM y políticas estrictas de permisos.

### 3.3. Mantenimiento y Monitoreo

- **RNF-06:** Se debe configurar CloudWatch y CloudTrail para monitorear actividad y detectar fallos.
- **RNF-07:** Se deben realizar pruebas de seguridad periódicas.
- **RNF-08:** El sistema debe permitir actualizaciones sin afectar la disponibilidad mediante implementaciones.

## 4. Arquitectura del Sistema

El sistema se diseñará con una arquitectura basada en microservicios desplegados en AWS, con integración entre los siguientes componentes:

- **Amazon API Gateway:** Punto de entrada centralizado para las solicitudes de los usuarios.
- **AWS Lambda:** Procesamiento serverless de los datos.
- **Amazon DynamoDB:** Base de datos NoSQL escalable para almacenar datos de análisis.
- **Amazon S3:** Almacenamiento de archivos, incluyendo PDFs generados.
- **Amazon SES:** Servicio para envío de correos electrónicos con resultados.
- **Amazon Cognito:** Gestión de autenticación y autorización.
- **AWS WAF y GuardDuty:** Seguridad avanzada contra ataques.
- **AWS CloudWatch:** Monitoreo y logs de actividad.
- **AWS Auto Scaling:** Ajuste dinámico de capacidad para optimizar costos y rendimiento.

El sistema se desplegará en una **VPC con subredes públicas y privadas**, asegurando la seguridad y aislamiento de recursos críticos.

## 5. Implementación de CI/CD en AWS (Github-actions)

### **CI/CD para el Frontend (S3 + CloudFront)**

Cada vez que haya un cambio en el código del frontend, el despliegue debe ser automático:

1. **Construcción del proyecto (`npm build`)**.
2. **Subida del código a S3**.
3. **Invalidación de caché en CloudFront**.

### **CI/CD para el Backend (Lambda + API Gateway) con Github-actions**

Cada vez que haya un cambio en el backend:

1. **Empaquetar la función Lambda**.
2. **Actualizar la Lambda en AWS**.
3. **Desplegar API Gateway (si es necesario)**.

## 6. Arquitectura en la nube

![Diagrama de arquitectura](./AWS_diagram.drawio.png)

# 7. Arquitectura Detallada en AWS

## **📌 Flujo del Sistema**

### **1️⃣ Acceso del Usuario**

1. **El usuario ingresa a la plataforma a través de Route 53**.
   - *Amazon Route 53* es el servicio de DNS que permite a los usuarios acceder a la plataforma mediante un nombre de dominio.
   - Redirige el tráfico al **frontend alojado en S3 y distribuido con CloudFront**.

---

### **2️⃣ Carga del Frontend**

2. **El frontend está alojado en S3 y distribuido con CloudFront**.
   - *Amazon S3* almacena los archivos estáticos del frontend.
   - *Amazon CloudFront* actúa como CDN, optimizando la entrega de contenido y reduciendo la latencia global.

3. **El frontend se comunica con el backend a través de API Gateway**.
   - *Amazon API Gateway* gestiona las solicitudes HTTP del usuario.
   - Redirige las peticiones a una **Lambda en la subred privada** dentro de la VPC.

---

### **3️⃣ Procesamiento en el Backend**

4. **La Lambda en la subred privada procesa los datos con Amazon Comprehend**.
   - *AWS Lambda* ejecuta código en Python sin necesidad de servidores.
   - *Amazon Comprehend* realiza análisis de lenguaje natural sobre los datos enviados por el usuario.

5. **Los resultados del análisis se almacenan en Amazon DynamoDB**.
   - *DynamoDB* es una base de datos NoSQL altamente escalable.
   - Permite consultas rápidas sin afectar el rendimiento del sistema.

---

### **4️⃣ Generación y Almacenamiento de PDF**

6. **La Lambda genera un PDF con los resultados y lo guarda en S3**.
   - *Amazon S3 (Bucket de PDFs)* almacena los informes generados.
   - **VPC Endpoint (Gateway) para S3** permite la conexión privada entre Lambda y S3.
   - La **tabla de enrutamiento** de la subred privada direcciona las solicitudes a S3.

---

### **5️⃣ Notificación al Usuario**

7. **La Lambda envía una notificación al usuario por correo con SES**.
   - *Amazon Simple Email Service (SES)* envía correos con los resultados.
   - **VPC Endpoint (Interface) para SES** permite que Lambda acceda a SES sin salir a Internet.

---

## **🔍 Monitoreo y Seguridad**

### **🔹 Monitoreo**

- *Amazon CloudWatch*: Captura logs y métricas en tiempo real.
- *AWS CloudTrail*: Registra acciones realizadas en la infraestructura para auditoría.

### **🔹 Seguridad**

- *AWS IAM*: Control de acceso a recursos.
- *AWS Shield*: Protección contra ataques DDoS.
- *AWS Secrets Manager*: Gestión de credenciales y claves API.
- *AWS Certificate Manager*: Manejo de certificados SSL.
- *Amazon Cognito*: Gestión de autenticación y autorización de usuarios.

## 8. Conclusión

El diseño basado en microservicios y la implementación en AWS permitirán una solución escalable y segura. La automatización mediante infraestructura como código optimiza la gestión y reducción de costos operativos.

## 9. Trabajo futuro

- Implementación de Machine Learning con Amazon SageMaker para mejorar el análisis de datos.
