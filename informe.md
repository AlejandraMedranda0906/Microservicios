# TENDENCIAS TECNOLÓGICAS

## 1. Título

**Arquitectura de Microservicios para la Aplicación de Agendamiento de Citas en un SPA**

---

## 2. Integrantes

- Mateo Gabriel Campo  
- Mayra Alejandra Medranda  

---

## 3. Introducción

Los sistemas monolíticos presentan limitaciones en cuanto a escalabilidad, mantenibilidad y tolerancia a fallos. En este proyecto práctico se observa la migración del sistema de agendamiento de citas de un centro de spa desde una arquitectura monolítica hacia una arquitectura de microservicios.

Esta migración busca mejorar la escalabilidad, la flexibilidad, el mantenimiento y la disponibilidad del sistema, permitiendo un desarrollo y despliegue independiente de funcionalidades clave y mejorando así la eficiencia del sistema.

---

## 4. Objetivos

- Aplicar los conceptos de arquitectura de microservicios a un proyecto práctico.  
- Abordar la separación de responsabilidades, despliegue independiente y uso de componentes clave como el servicio descubridor (Eureka) y el API Gateway.  
- Aplicar herramientas de descubrimiento de servicios y punto de acceso único para facilitar la comunicación dinámica y centralizada entre múltiples servicios distribuidos.

---

## 5. Selección de Funcionalidades

Se han seleccionado las siguientes funcionalidades para ser desacopladas como microservicios independientes:

- **Gestor de Usuarios**: Registro, inicio de sesión, autenticación y recuperación de contraseña.  
- **Gestor de Citas**: Agendamiento, modificación, cancelación, consulta y estado de citas.  
- **Catálogo de Servicios del SPA**: Visualización de los servicios disponibles.  
- **Servicio de Notificaciones**: Envío de correos electrónicos y mensajes por WhatsApp.

---

## 6. Diseño Modular

**Tabla 1. Diseño de microservicios**

| Microservicio           | Descripción                                               | Tecnología                    | Base de Datos         | API REST Endpoints                              |
|-------------------------|-----------------------------------------------------------|-------------------------------|------------------------|-------------------------------------------------|
| `user-service`          | Registro, login, autenticación JWT                        | Spring Boot, JWT, Node.js     | MySQL (ElephantSQL)    | `POST /register`, `POST /login`, `POST /profile` |
| `appointment-service`   | Gestión de citas, disponibilidad, modificación y estado   | Spring Boot                   | MySQL                  | `GET/POST/PUT/DELETE /appointments`             |
| `service-catalog`       | Catálogo de servicios                                     | FastAPI                       | PostgreSQL             | `/api/services`, `/api/services/{id}`, `/reschedule` |
| `notification-service`  | Envío de notificaciones por correo y WhatsApp             | Node.js, Twilio, Nodemailer   | No aplica              | `/notify/email`, `/notify/whatsapp`             |

---

## 7. Infraestructura de Microservicios

Para permitir el descubrimiento y la comunicación entre microservicios, se implementaron:

- **Servicio Descubridor (Eureka Server)** ejecutándose en el puerto `8761`.  
- **API Gateway** como punto de entrada centralizado, ejecutándose en el puerto `8080` y ruteando solicitudes según el nombre del microservicio registrado en Eureka.

---

## 8. Configuración General

Cada microservicio fue configurado con:

- Configuración de Eureka Server usando `@EnableEurekaServer` y `application.yml`.  
- Puerto aleatorio (`server.port: 0`).  
- Registro automático en Eureka.  
- Redirección de rutas desde el Gateway.  
- Base de datos propia para cada microservicio (ElephantSQL y MySQL).

---

## 9. Funcionalidades Separadas

Separar las funcionalidades permite mejor mantenimiento, escalabilidad y despliegue independiente, conforme al principio de responsabilidad única.

**Tabla 2. Funciones desacopladas por microservicio**

| Microservicio           | Funciones Separadas                                   |
|-------------------------|-------------------------------------------------------|
| `user-service`          | Registro, login, autenticación y seguridad            |
| `service-catalog`       | Visualización de servicios                            |
| `appointment-service`   | Agendamiento, cancelación y modificación de citas     |
| `notification-service`  | Envío de correos y mensajes de confirmación           |

---

## 10. Relación entre Microservicios

**Tabla 3. Comunicación entre servicios**

| Emisor               | Receptor              | Motivo                                                        | Tipo de Comunicación |
|----------------------|-----------------------|---------------------------------------------------------------|----------------------|
| `user-service`       | `appointment-service` | Validar usuario antes de agendar o modificar citas            | REST API / JWT       |
| `appointment-service`| `service-catalog`     | Obtener información del servicio antes de agendar una cita    | REST API             |
| `appointment-service`| `notification-service`| Enviar confirmación por correo y WhatsApp                     | REST API             |

---

## 11. Implementación de Eureka Server

- Proyecto basado en Spring Boot con la dependencia `spring-cloud-starter-netflix-eureka-server`.  
- Se utiliza la anotación `@EnableEurekaServer` en la clase principal.

**Archivo `application.yml`:**

```yaml
server:
  port: 8761

eureka:
  client:
    register-with-eureka: false
    fetch-registry: false
