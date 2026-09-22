# Config Server Microservice

> Servidor de configuraciones centralizadas para los microservcios, en la que estos consultan sus propias configuraciones a traves del protocolo `HTTP GET`
> 
> <a href="https://spring.io/projects/spring-cloud-config#learn">
>     <img src="https://img.shields.io/badge/Spring_Cloud_Config-2ea44f?style=for-the-badge&logo=SpringBoot&logoColor=white" alt="Spring Cloud Config" />
> </a>

## Flujo de Funcionamiento

<img width="1000" height="500" alt="Pasted image 20260921215839" src="https://github.com/user-attachments/assets/3d56eb3a-4f8d-4f99-88d1-867b52f497b5" />

1. **SOLICITO LAS CONFIGURACIONES**  
   Al iniciarse un microservicio, este le pide al Config Server que le mande las configuraciones YAML a través de una petición `HTTP GET`.

2. **DAME LAS CONFIGURACIONES**  
   El Config Server hace un `git fetch` o `git pull` para buscar los cambios más recientes del repositorio.

   > 💡
   > Al iniciarse el **Microservicio Config Server**, este realiza un `git clone` del repositorio central.

3. **TE DOY LAS CONF DEL REPO**  
   El repositorio alojado en **GitHub** le entrega las configuraciones más recientes de los archivos YAML.

4. **AQUÍ LAS TIENES**  
   El Config Server le retorna a los microservicios las configuraciones solicitadas en formato JSON.

<p align="right">
  <a href="https://github.com/AlbertoDeTeresaChaves/moviesreview-config-server">
    <img src="https://img.shields.io/badge/Ver_Configuraciones_del_Repositorio-007ACC?style=for-the-badge&logo=github&logoColor=white" alt="Ver Configuraciones del Repositorio" />
  </a>
</p>

---

## Activar el Config Server

De nada nos sirve configurar las propiedades de nuestro `config-server` si no habilitamos la propiedad `@EnableConfigServer`, que lo que hara sera transformar nuestro proyecto de Spring Boot en un Servidor Centralizado de Gestion Configuraciones
```java
@EnableConfigServer
@SpringBootApplication
public class ConfigserverApplication {

	public static void main(String[] args) {
		SpringApplication.run(ConfigserverApplication.class, args);
	}

}

```
---

## Como conectarse al repositorio de GitHub
```xml
spring:
  application:
    name: configserver
  profiles:
    active: git

  cloud:
    config:
      server:
        native:
          search-locations: "classpath:/config"
        git:
          uri: "https://github.com/AlbertoDeTeresaChaves/MoviesReview-config"
          default-label: master
          timeout: 5
          clone-on-start: true
```

Tenemos varias opciones, pero lo hare lo mas simple del mundo. Simplemente pondremos la ruta `uri` de nuestro repo y darle algunas propiedades mas si queremos.

 * **`default-label`:** Establecemos un branch por defecto donde nuestro servidor escuchara.
 * **`timeout`:** Le damos unos segundos de espera para adquirir los recursos
 * **`clone-on-start`:** Establecemos que nada mas iniciar el `config-server` haga un `git-clone` del repositorio

A parte de toda la configuracion para conectarse al repositorio de GitHub, tengo puesto una configuracion local, en la que puedo alternar en `profiles`.

---

## Como pueden los Microservicios solicitar sus configuraciones al `Config Server`

Basta con que dentro de `application.yaml` de nuestros micros importemos la ruta del `config-server`

```xml
spring:
  application:
    name: accounts
  config:
    import: "configserver:${CONFIG_SERVER_URL}"
```
