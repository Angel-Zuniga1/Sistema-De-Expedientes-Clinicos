# Sistema de Expedientes Clínicos

Este proyecto se enfoca en el desarrollo de un módulo para la gestión de expedientes clínicos dentro de una clínica de psicología, como parte de un sistema más amplio. Su propósito principal es modernizar el manejo de la información de los pacientes mediante la digitalización de expedientes que actualmente se administran de forma física. A través de este módulo, se busca facilitar la consulta y gestión de la información clínica, permitiendo un acceso más ágil, organizado y seguro para el personal autorizado de la clínica.

## Estructura del repositorio (árbol)

```
.
├── README.md
├── backend/
│   ├── Dockerfile
│   ├── pom.xml
│   └── src/
│       └── main/
│           └── java/
├── Bitácoras/
│   ├── Generales/
│   └── Semanales/
├── Documentación/
│   ├── Diagramas/
│   ├── Diseño y arquitectura/
│   └── Modelado y datos/
├── frontend/
│   ├── index.html
│   ├── administrador.html
│   ├── supervisor.html
│   ├── terapeuta.html
│   ├── css/
│   └── js/
└── README.md
```

## Navegación para documentación

- Documentación general:
    - [Alcances y limitaciones](Documentación/Documentación%20%20general/Alcances_y_limitaciones.md)

- Requisitos:
    - [Requisitos funcionales](Documentación/Requisitos/Requisitos_Funcionales.md)
    - [Requisitos no funcionales](Documentación/Requisitos/Requisitos_No_Funcionales.md)
    - [Casos de uso](Documentación/Requisitos/Casos_de_uso.md)

- Modelado y datos:
    - [Estructura del Expediente Clínico](Documentación/Modelado%20y%20datos/Estructura_Del_Expediente_Clínico.md)
    - [Diccionario de datos](Documentación/Modelado%20y%20datos/Diccionario_Datos.md)
    - [Especificación de la API REST](Documentación/Modelado%20y%20datos/Especificacion_Api_Endpoints.md)
    - [Modelo de estados](Documentación/Modelado%20y%20datos/Modelo_De_Estados.md)
    - [Reglas de negocio](Documentación/Modelado%20y%20datos/Reglas_De_Negocio.md)
    - [Definición de clases del sistema](Documentación/Modelado%20y%20datos/Definicion_Clases_Del_Sistema.md)

- Diseño y arquitectura:
    - [Arquitectura y patrón de diseño](Documentación/Diseño%20y%20arquitectura/Arquitectura_y_patrón.md)
    - [Auditoría](Documentación/Diseño%20y%20arquitectura/Auditoria.md)

- Bitácoras:
    - [Generales](Bitácoras/Generales/)
    - [Semanales](Bitácoras/Semanales/)

## Stack tecnológico

A continuación se listan las capas, tecnologías y herramientos de desarrollo para producción.

- Backend
    - Lenguaje: Java 17+
    - Framework: Spring Boot (Spring Web, Spring Data JPA)
    - Seguridad: Spring Security
    - ORM: Hibernate / JPA
    - Migraciones: Flyway (migrations en `backend/src/main/resources/db/migration`)
    - Build: Maven (pom.xml)
    - Deploy: Render

- Base de datos
    - Producción: PostgreSQL 
    - Desarrollo: Instancia local o Docker Compose 
    - Consideraciones: Migraciones gestionadas por Flyway
    - Deploy: Render

- Frontend
    - Prototipo actual: HTML/CSS/JavaScript en `frontend/` (estático)
    - Deploy: Vercel