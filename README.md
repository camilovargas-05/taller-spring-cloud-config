# Taller: Spring Boot Cloud Configuration Server

Implementación paso a paso del tutorial [Spring Boot – Cloud Configuration Server](https://www.geeksforgeeks.org/advance-java/spring-boot-cloud-configuration-server/) (GeeksforGeeks).

Se construyen dos aplicaciones Spring Boot:

| Proyecto | Rol | Puerto |
|---|---|---|
| [`config-server`](config-server) | **Config Server**: almacena y sirve la configuración centralizada | 8888 |
| [`loan-service`](loan-service) | **Config Client**: obtiene su configuración del servidor al arrancar | 8081 / 8082 / 8083 según el perfil |

## Tecnologías

- Java 21
- Spring Boot 4.1.1
- Spring Cloud 2025.1.3 (Config Server / Config Client)
- Maven (se incluye el wrapper `mvnw`, no hace falta instalar Maven)

## Estructura

```
Taller_Spring_Cloud_Config/
├── config-server/
│   └── src/main/
│       ├── java/com/taller/configserver/ConfigServerApplication.java   (@EnableConfigServer)
│       └── resources/
│           ├── application.properties                                   (puerto 8888, perfil native)
│           └── config/
│               ├── loan-service.properties        (perfil default → 8081)
│               ├── loan-service-dev.properties    (perfil dev     → 8082)
│               └── loan-service-uat.properties    (perfil uat     → 8083)
├── loan-service/
│   └── src/main/
│       ├── java/com/taller/loanservice/
│       │   ├── LoanServiceApplication.java
│       │   └── LoanController.java                                      (GET /message)
│       └── resources/application.properties                             (spring.config.import + perfil activo)
└── docs/
    ├── Informe_Taller_Spring_Cloud_Config.pdf
    ├── capturas/
    └── logs/
```

## Cómo ejecutarlo

1. Arrancar el servidor de configuración:

   ```bash
   cd config-server
   ./mvnw spring-boot:run
   ```

   Comprobar: <http://localhost:8888/loan-service/dev>

2. En otra terminal, arrancar el cliente:

   ```bash
   cd loan-service
   ./mvnw spring-boot:run
   ```

   Comprobar: <http://localhost:8082/message>

3. Para cambiar de entorno, editar `spring.profiles.active` en `loan-service/src/main/resources/application.properties`:

   | Perfil | URL | Respuesta |
   |---|---|---|
   | *(sin perfil)* | <http://localhost:8081/message> | `Hola desde Loan Service - perfil DEFAULT ...` |
   | `dev` | <http://localhost:8082/message> | `Hola desde Loan Service - perfil DEV ...` |
   | `uat` | <http://localhost:8083/message> | `Hola desde Loan Service - perfil UAT ...` |

## Nota sobre el tutorial original

En el artículo, el servidor se configura con `server.port=8080` pero el cliente apunta a `http://localhost:8888`. Aquí el servidor usa **8888**, que es el puerto por defecto de Spring Cloud Config, para que ambos coincidan.

## Documentación

El informe completo, con la explicación de cada paso y las capturas, está en [`docs/Informe_Taller_Spring_Cloud_Config.pdf`](docs/Informe_Taller_Spring_Cloud_Config.pdf).
