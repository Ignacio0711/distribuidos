# Trabajo Práctico Ingeniería Inversa

El objetivo de esta actividad es lograr que ambos proyectos corran de manera simultánea en sus computadoras y extraer de ellos toda la información sobre como se conectan diferentes microservicios.

Trabajando en grupos (o de forma individual según indique el profesor), realicen un informe técnico o documento Markdown (README.md) que responda y detalle lo siguiente:

Mapa de Endpoints: Liste las rutas (URLs, métodos HTTP y parámetros) que ofrece cada microservicio basándose estrictamente en el código fuente.

Estructura del Proyecto: Describan brevemente cómo están organizadas las capas en cada proyecto (Controladores, Servicios, Repositorios, Entidades/Modelos).

Independencia vs. Comunicación: Analicen si los microservicios operan completamente aislados o si existe algún punto de contacto o dependencia entre ellos.

**Conclusiones:** Redacten una breve reflexión sobre las ventajas de separar un sistema monolítico en estos dos microservicios.





>Este ejemplo práctico está basado en los materiales educativos desarrollados por la ingeniera y creadora de contenido Luisina de Paula (TodoCode Academy). Todo el mérito por la estructura inicial del código corresponde a su labor docente y de divulgación en tecnología.

## Respuestas

### Mapa de Endpoints:

En ambos microservicios ofrece la creacion de los objetos con sus atributos, borrar por id, lista completa, listar por id o dni, editar.

En Paciente hay 6 endpoints:

- /pacientes/crear 
- /pacientes/traer
- /pacientes/borrar/{id}
- /pacientes/editar/{id_original}
- /pacientes/traer/{id}
- /pacientes/traerdni/{dni}

En Turnos hay 6 endpoints:

- /turnos
- /creador
- /traer
- /borrar/{id}
- /editar/{id_original}
- /traer/{id}


### Estructura del Proyecto:

En amabos microservicios cuentan con la misma estructura de capaz:
- **Model**: es donde esta la clase principal del objeto con sus atributos.
- **Repository**: se encunetra la inferzas que heread los metodos de JPA.
- **Service**: En esta capa hay:
  - *Interfazes*: donde estan las firmas de los metodos, con o sin parametros.
  - *Implementacion* (Clase): aqui es donde se implementan los mentodos de la Interfaz, y se arma la logica. En ella se crea un objeto de tipo IRepository para utilizar los metodos de JPA.
- **Controller**: En esta clase estan los metodos HTTP con sus parametros, y las rutas url. Aqui se instancia un objeto de la clase del paquete Service (por ejemplo PacienteService), para poder llamar a los metodos con su logica.
  
Con la unica diferencia es que en el microservicio de Turno, hay una capa DTO, la cual contiene una clase TurnoDTO, que su unica funcion es transportar datos necesarios, y ocultar información privada. Y por ella trae el DNI del paciente.


### Independencia vs. Comunicación:
 
 Los microservicios operan de forma aislada tanto el de Pacientes como el de Turnos, cada uno con su base de datos propia.
 Existe un punto de contacto, por medio de un metodo RestTemplate(), que funciona como API par que Turno consuma de Pacientes, ya que para crear un turno depende de un paciente para poder completar la informacion escencial y necesaria, es decir sin un paciente, no existe un turno.

> **Conclusion:**<br>
>La division de un sistema monolitico a dos microservicios, ya que cada parte del sistema en general opera de manera independiente, a pesar de que Turnos depende mayormente de un paciente, si alguno de los dos falla, no afecta en nada al otro. Tiene una alta proteccion de datos, al estar separdos, ya que difernte informacion o datos se encuentran en base de datos distintas.

