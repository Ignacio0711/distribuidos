Apunte Técnico: Comunicación entre Microservicios con RestTemplate

# 1. Introducción: El desafío de conectar aplicaciones
Para comprender este concepto, imagine que construye una aplicación grande (lo que llamamos un sistema monolítico). En un monolito, todas las funciones de la empresa -como la administración de clientes, el catálogo de productos y la facturación- conviven juntas en el mismo programa y comparten la misma base de datos. Si una parte necesita un dato de otra, simplemente lo busca de manera interna en la memoria.

Sin embargo, en la actualidad es muy común utilizar una arquitectura de microservicios. Esto significa que dividimos el sistema en piezas más pequeñas e independientes. Cada microservicio funciona en su propio entorno y protege su propia base de datos de forma exclusiva.

El problema: Si el microservicio de Facturación necesita conocer los datos de un cliente que vive dentro del microservicio de Clientes, no puede entrar a la base de datos ajena.

La solución: Debe solicitar esa información a través de la red, haciendo una petición web (una API REST). Y para lograr esto de forma sencilla desde Java, utilizamos RestTemplate.

# 2. ¿Qué es exactamente RestTemplate?
RestTemplate es una clase que nos provee Spring Framework para que nuestra aplicación actúe como un cliente web programático.

Piense en RestTemplate como un asistente de envíos o un cartero:

Usted le indica a qué dirección web (URL) debe dirigirse.

El asistente realiza una petición (por ejemplo, un método GET para pedir información o un POST para enviarla).

Recibe la respuesta del otro servidor (habitualmente en formato JSON).

Transforma automáticamente esos datos en un objeto de Java listo para que usted pueda trabajar con ellos.

Un detalle importante: Las llamadas realizadas con RestTemplate son síncronas y bloqueantes. Esto significa que cuando el programa ejecuta la línea que llama a otro servicio, se detiene y espera pacientemente a que el otro servidor responda antes de continuar con la siguiente instrucción.

# 3. ¿Cómo se configura en Spring Boot?
Antes de poder utilizar RestTemplate en nuestras clases, es necesario registrarlo dentro del sistema de Spring para que esté disponible mediante inyección de dependencias. Esto se realiza habitualmente en una clase de configuración:

```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.client.RestTemplate;

@Configuration
public class AppConfig {

    @Bean
    public RestTemplate restTemplate() {
        return new RestTemplate();
    }
}
```
Una vez que se declara este @Bean, cualquier servicio de nuestra aplicación puede solicitarlo e integrarlo para comenzar a comunicarse con otros sistemas.

# 4. Ejemplo Práctico: Consultando datos entre servicios
Imagine un escenario donde el microservicio de Pedidos necesita consultar los detalles de un producto llamando al microservicio de Productos.

**Paso 1: Crear un DTO (Data Transfer Object)**
Para no exponer los datos internos del otro servicio, utilizamos un molde liviano llamado DTO que solo contiene los atributos que necesitamos recibir:

```java
public class ProductoDto {
    private Long id;
    private String nombre;
    private Double precio;

    // Constructor vacío, Getters y Setters
    public ProductoDto() {}

    public Long getId() { return id; }
    public String getNombre() { return nombre; }
    public Double getPrecio() { return precio; }
}
```

**Paso 2: Utilizar RestTemplate en el servicio**
Dentro de nuestro servicio, inyectamos RestTemplate y realizamos la consulta a la URL correspondiente: 
 ```java
 import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.springframework.web.client.RestTemplate;

@Service
public class PedidoService {

    @Autowired
    private RestTemplate restTemplate;

    public ProductoDto obtenerInformacionProducto(Long productoId) {
        // Dirección web del otro microservicio
        String url = "http://localhost:8081/api/productos/" + productoId;

        // Llamamos al servicio remoto y mapeamos la respuesta JSON directamente a nuestro DTO
        ProductoDto producto = restTemplate.getForObject(url, ProductoDto.class);

        return producto;
    }
} 
 ```
# 5. Buenas prácticas esenciales
Cuando diseñe sistemas que se comuniquen de esta manera, tenga en cuenta los siguientes consejos:

Desacoplamiento: Utilice siempre DTOs propios para recibir los datos. Esto evita que los cambios internos en el servidor proveedor rompan la aplicación cliente.

Control de Errores: ¿Qué sucede si el otro microservicio está apagado o tarda demasiado en responder? Es fundamental contemplar escenarios de fallo (por ejemplo, utilizando bloques de manejo de excepciones) para que la aplicación entera no colapse.

Evolución tecnológica: RestTemplate es excelente para aprender, comprender los fundamentos de la arquitectura de microservicios y desarrollar sistemas medianos. En proyectos profesionales muy complejos o masivos, la industria suele migrar hacia herramientas más avanzadas como WebClient o clientes declarativos.