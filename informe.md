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

Esta migración busca mejorar la escalabilidad, la flexibilidad, el mantenimiento y la disponibilidad del sistema, permitiendo un desarrollo y despliegue independiente de funcionalidades clave y mejorando así la eficiencia del sistema(Newman, 2022).
---

## 4. Objetivos

- Aplicar los conceptos de arquitectura de microservicios a un proyecto práctico.

- Abordar la separación de responsabilidades, despliegue independiente y uso de Eureka y API Gateway.

- Usar herramientas de descubrimiento de servicios y punto de acceso único para facilitar la comunicación entre servicios distribuidos.

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
- **API Gateway** como punto de entrada centralizado, ejecutándose en el puerto `8080` y ruteando solicitudes según el nombre del microservicio registrado en Eureka(Newman, 2022).

---

## 8. Configuración General

Cada microservicio fue configurado con:

- Configuración de Eureka Server usando `@EnableEurekaServer` y `application.yml`.  
- Puerto aleatorio (`server.port: 0`).  
- Registro automático en Eureka.  
- Redirección de rutas desde el Gateway.  
- Base de datos propia para cada microservicio.

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
---

## 12. Implementación del API Gateway

El Gateway permite enrutar automáticamente las peticiones según el nombre del servicio registrado en Eureka(Heckler, 2021).

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
---

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

---

## 14. Conclusiones

La implementación de una arquitectura de microservicios en el sistema de agendamiento de citas del SPA permitió cumplir satisfactoriamente los objetivos planteados.

Se logró aplicar los conceptos clave de microservicios, desacoplando funcionalidades críticas como la gestión de usuarios, citas, servicios y notificaciones. Esto facilitó el mantenimiento, la escalabilidad y el desarrollo independiente de cada módulo.

Además, se abordó correctamente la separación de responsabilidades entre servicios, así como su despliegue independiente, gracias al uso del **Eureka Server** como servicio descubridor y del **API Gateway** como punto centralizado de entrada.

Finalmente, se utilizaron herramientas que facilitaron la comunicación dinámica entre los microservicios, permitiendo una integración fluida mediante descubrimiento automático y enrutamiento basado en rutas. Esto validó la eficacia del enfoque distribuido en un entorno real y funcional.

En conclusión, la arquitectura propuesta no solo mejora la estructura técnica del sistema, sino que también sienta las bases para futuras ampliaciones, como integración con sistemas de pago, chat en línea o analítica de citas, sin comprometer la estabilidad general del sistema.


---

## 15. Anexos
Video explicativo del proyecto: https://youtu.be/1qXU4fDfiHw

---

## 16. Referencias (Formato APA 7)

Heckler, M. (2021). Spring Boot: Up and Running: Building Cloud Native Java and Kotlin Applications. O’Reilly Media.

Newman, S. (2022). Building Microservices: Designing Fine-Grained Systems (2nd ed.). O’Reilly Media.

Spring Cloud. (2023). Spring Cloud 2023.0 Release Notes. GitHub. https://github.com/spring-cloud/spring-cloud-release/wiki/Spring-Cloud-2023.0-Release-Notes


 



