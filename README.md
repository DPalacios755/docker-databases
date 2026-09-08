# Docker Database

Entorno centralizado de bases de datos para desarrollo local utilizando **Docker Compose**.
Este proyecto permite levantar diferentes motores de bases de datos de forma independiente o simultánea, evitando la necesidad de instalar cada servidor directamente en el sistema operativo.

Actualmente incluye:
- MySQL
- PostgreSQL
- Microsoft SQL Server

Está pensado para ser utilizado como entorno de desarrollo para proyectos con tecnologías como **Laravel, .NET, Angular, Node.js, NestJS** u otras aplicaciones que requieran acceso a bases de datos.


## Servicios disponibles

| Motor | Imagen | Puerto local | Puerto contenedor |
| MySQL | `mysql:8.4` | `3307` | `3306` |
| PostgreSQL | `postgres:17` | `5433` | `5432` |
| SQL Server | `mcr.microsoft.com/mssql/server:2022-latest` | `1433` | `1433` |

 MySQL utiliza el puerto `3307` en el host para evitar posibles conflictos con instalaciones locales que utilicen el puerto `3306`.

## Arquitectura

text
                    Aplicaciones locales
                 Laravel / .NET / Node.js
                           │
          ┌────────────────┼────────────────┐
          │                │                │
          ▼                ▼                ▼
     localhost:3307   localhost:5432   localhost:1433
          │                │                │
          ▼                ▼                ▼
    ┌───────────┐    ┌────────────┐    ┌────────────┐
    │   MySQL   │    │ PostgreSQL │    │ SQL Server │
    │   3306    │    │    5432    │    │    1433    │
    └─────┬─────┘    └─────┬──────┘    └──────┬─────┘
          │                │                   │
          ▼                ▼                   ▼
     mysql_data       postgres_data       sqlserver_data

                   Docker Compose


Cada motor utiliza un volumen independiente para mantener la información aunque los contenedores sean detenidos o recreados.

## Instalación

### 1. Clonar el repositorio

git clone <URL_DEL_REPOSITORIO>

Ingresar al proyecto:
cd docker-database

### 2. Configurar variables de entorno

El repositorio no debe contener contraseñas reales.
Copia el archivo:

.env.example
como: .env

En Linux/macOS:
cp .env.example .env

En PowerShell:
powershell
Copy-Item .env.example .env


Configura tus credenciales:

.env
# MYSQL
MYSQL_ROOT_PASSWORD=your_mysql_root_password
MYSQL_DATABASE=developer_db
MYSQL_USER=developer
MYSQL_PASSWORD=your_mysql_password

# POSTGRESQL
POSTGRES_DB=postgres_db
POSTGRES_USER=developer
POSTGRES_PASSWORD=your_postgres_password

# SQL SERVER
MSSQL_SA_PASSWORD=your_sqlserver_password


## Iniciar el entorno

Levantar todos los motores:

docker compose up -d

Verificar su estado:
docker compose ps

También puedes verificar los contenedores con:
docker ps

## Iniciar un solo motor
### MySQL
docker compose up -d mysql
### PostgreSQL
docker compose up -d postgres
### SQL Server
docker compose up -d sqlserver

Esto permite utilizar únicamente los recursos necesarios para cada proyecto.

## Detener los servicios

Detener los contenedores:
docker compose stop

Volver a iniciarlos:
docker compose start

Eliminar los contenedores manteniendo los datos:
docker compose down

## Eliminación de datos

Los datos se almacenan utilizando volúmenes persistentes de Docker.

Puedes consultar los volúmenes con:
docker volume ls

No ejecutes:
docker compose down -v
a menos que quieras eliminar también los volúmenes asociados al proyecto.

El parámetro `-v` puede eliminar los datos almacenados por los motores de bases de datos.

## Conexiones desde el host

Si la aplicación se ejecuta directamente desde Windows, Linux o macOS:

### MySQL
text
Host:     127.0.0.1
Port:     3307
Database: developer_db
Username: developer
Password: <MYSQL_PASSWORD>


### PostgreSQL
text
Host:     127.0.0.1
Port:     5433
Database: postgres_db
Username: developer
Password: <POSTGRES_PASSWORD>


### SQL Server
text
Host:     127.0.0.1
Port:     1433
Database: master
Username: sa
Password: <MSSQL_SA_PASSWORD>



## Conexión entre contenedores

Los servicios se encuentran conectados mediante una red Docker.

Por esta razón, una aplicación que también esté ejecutándose dentro de Docker debe utilizar el nombre del servicio en lugar de `localhost`.

Por ejemplo:

### MySQL
text
Host: mysql
Port: 3306

### PostgreSQL
text
Host: postgres
Port: 5432

### SQL Server
text
Host: sqlserver
Port: 1433


Por ejemplo, una aplicación Laravel dentro de Docker podría utilizar:
env
DB_CONNECTION=mysql
DB_HOST=mysql
DB_PORT=3306
DB_DATABASE=developer_db
DB_USERNAME=developer
DB_PASSWORD=your_password


Mientras que Laravel ejecutándose directamente en el host utilizaría:
env
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3307
DB_DATABASE=developer_db
DB_USERNAME=developer
DB_PASSWORD=your_password

##  Red Docker

Los contenedores utilizan una red compartida:

text
databases_network


Esto permite que otros contenedores puedan comunicarse con los motores utilizando sus respectivos nombres de servicio.

text
Application Container
        │
        ├──── mysql:3306
        │
        ├──── postgres:5432
        │
        └──── sqlserver:1433



## Persistencia

Cada motor utiliza su propio volumen:
text
mysql_data
postgres_data
sqlserver_data
Esto permite detener o recrear los contenedores sin perder las bases de datos.

##  Administración

Puedes conectarte utilizando cualquier cliente compatible, por ejemplo:
- Navicat
- DBeaver
- MySQL Workbench
- pgAdmin
- SQL Server Management Studio (SSMS)
Para SQL Server puede ser necesario instalar **Microsoft ODBC Driver for SQL Server** dependiendo del cliente utilizado.


## Estructura del proyecto

text
docker-database-environment/
├── docker-compose.yml
├── .env
├── .env.example
├── .gitignore
└── README.md



## Seguridad

Las credenciales reales se almacenan en `.env`.
Este archivo **no debe subirse al repositorio**.
El `.gitignore` debe contener:
gitignore
.env
En cambio, `.env.example` sí debe versionarse para documentar las variables necesarias.
Nunca almacenes contraseñas reales directamente en:
text
docker-compose.yml
README.md
.env.example

## 🎯 Objetivo del proyecto

Este entorno busca proporcionar una infraestructura reutilizable para desarrollo local que permita trabajar con diferentes tecnologías y motores de bases de datos sin realizar instalaciones independientes en el sistema operativo.

Puede utilizarse como infraestructura compartida para proyectos como:

text
Laravel
   └── MySQL / PostgreSQL

.NET
   └── SQL Server / PostgreSQL

NestJS
   └── PostgreSQL / MySQL

Node.js
   └── PostgreSQL / MySQL



##  Próximas mejoras

El entorno podrá ampliarse posteriormente con servicios como:

- Redis
- MongoDB
- Nginx
- RabbitMQ
- Mailpit
- herramientas de monitoreo

Estos servicios no forman parte de la configuración actual para mantener el entorno inicial simple y enfocado exclusivamente en bases de datos.


## Autor

**Daniel Palacios**

Proyecto creado como entorno de desarrollo y aprendizaje de infraestructura con Docker y diferentes motores de bases de datos.
