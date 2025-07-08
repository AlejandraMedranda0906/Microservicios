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
```  
    

## 12. Implementación del API Gateway

El Gateway permite enrutar automáticamente las peticiones según el nombre del servicio registrado en Eureka.

Puerto: 8080

** Archivo application.yml:**

```spring:
  cloud:
    gateway:
      discovery:
        locator:
          enabled: true
      routes:
        - id: user-service
          uri: lb://USER-SERVICE
          predicates:
            - Path=/users/**
        - id: appointment-service
          uri: lb://APPOINTMENT-SERVICE
          predicates:
            - Path=/appointments/**
```  
## 13. Resultados de la Implementación

### 13.1. Pruebas Realizadas

- Registro y login de usuarios  
- Visualización del catálogo de servicios  
- Agendamiento y cancelación de citas  
- Envío de notificaciones por correo electrónico y WhatsApp

### 13.2. Ventajas Obtenidas

- Reducción del acoplamiento entre componentes  
- Escalabilidad individual por microservicio  
- Mejor trazabilidad de errores  
- Facilidad en el mantenimiento  
- Despliegue independiente de cada microservicio  
- Centralización de rutas mediante el API Gateway

### 13.3. Dificultades

- Configuración inicial de Eureka Server y API Gateway  
- Sincronización entre servicios durante pruebas conjuntas

### 13.4. Soluciones Implementadas

- Pruebas unitarias por separado en cada microservicio  
- Documentación de los endpoints utilizando Swagger

## 14.  Conclusiones
La migración del sistema del spa a una arquitectura de microservicios permite una estructura más escalable, mantenible y robusta, con componentes independientes que mejoran el rendimiento y la organización del proyecto.

Se comprobó la eficiencia del uso de Eureka como servicio descubridor y del API Gateway como punto de acceso unificado.

La arquitectura implementada permite futuras extensiones, como integración de sistemas de pago o chat, sin afectar el resto de los componentes.

## 15. Anexos
Video explicativo del proyecto: https://youtu.be/1qXU4fDfiHw

## 16. Referencias (Formato APA 7)

- Newman, S. (2022). *Building Microservices* (2nd ed.). SamNewman.io.  
- Späth, P., Cosmina, I., Harrop, R., & Schaefer, C. (2023). *Pro Spring 6: An In-Depth Guide to the Spring Framework*. Apress.  
- Heckler, M. (2021). *Spring Boot: Up and Running: Building Cloud Native Java and Kotlin Applications*. O’Reilly Media.  
- Spring Cloud. (2023). *Spring Cloud 2023.0 Release Notes*. GitHub.  
- Baresi, L., Quattrocchi, G., & Tamburri, D. A. (2022). Microservice Architecture Practices and Experience: A Focused Look on Docker Configuration Files. *arXiv*. :contentReference[oaicite:1]{index=1}  
- Billawa, P., Bambhore Tukaram, A., Díaz Ferreyra, N. E., Steghöfer, J.-P., Scandariato, R., & Simhandl, G. (2022). SoK: Security of Microservice Applications: A Practitioners' Perspective on Challenges and Best Practices. *arXiv*. :contentReference[oaicite:2]{index=2}  
- Waseem, M., Liang, P., Ahmad, A., Shahin, M., Ali Khan, A., & Márquez, G. (2022). Decision Models for Selecting Patterns and Strategies in Microservices Systems and their Evaluation by Practitioners. *arXiv*. :contentReference[oaicite:3]{index=3}  
- Waseem, M., Liang, P., Shahin, M., Di Salle, A., & Márquez, G. (2021). Design, Monitoring, and Testing of Microservices Systems: The Practitioners' Perspective. *arXiv*. :contentReference[oaicite:4]{index=4}  



