# 1. ¿Qué es JDBC y cuáles son sus componentes?

JDBC (Java Database Connectivity) es una API de Java que permite a las aplicaciones Java interactuar con bases de datos relacionales. JDBC actúa como un puente entre una aplicación Java y una base de datos, permitiendo ejecutar consultas SQL, recuperar datos y realizar operaciones de actualización.

**Componentes principales de JDBC:**

- Driver JDBC: Proporciona la implementación necesaria para comunicarse con una base de datos específica.
- Conexión (Connection): Representa una conexión física con la base de datos. Se utiliza para enviar consultas SQL.
- Sentencia (Statement): Se usa para ejecutar consultas SQL estáticas y dinámicas.
- Resultado (ResultSet): Contiene los resultados devueltos por una consulta SQL.
- Metadatos (DatabaseMetaData y ResultSetMetaData): Proporcionan información sobre la base de datos y los resultados de las consultas.

# 2. Librerías de Scala para conectarse a bases de datos relacionales

**Slick**

- Descripción: Una biblioteca de Scala que permite interactuar con bases de datos relacionales utilizando un enfoque funcional y un ORM (Object Relational Mapping).
- Características:
  - Compatible con varias bases de datos (MySQL, PostgreSQL, SQLite, etc.).
  - Ofrece una API de alto nivel y funcional.
  - Admite consultas asíncronas.
  - Soporte de tipos seguros.

**Doobie**

- Descripción: Una librería funcional y ligera para interactuar con bases de datos relacionales en Scala.
- Características:
  - Totalmente funcional y basada en Cats Effect.
  - No utiliza ORM, lo que lo hace más transparente para los desarrolladores.
  - Compatible con varias bases de datos.

| Característica |	Slick |	Doobie |
| --- | --- | --- | 
| Paradigma |	Funcional con ORM	| Funcional puro, sin ORM |
| API |	Abstracción de alto nivel |	Bajo nivel, más control directo |
| Dependencias |	Menos dependencias funcionales | Basado en Cats Effect |
| Curva de aprendizaje |	Más fácil debido a su enfoque ORM |	Más pronunciada |

# 3. Establecer una conexión a una base de datos relacional (MySQL)

**Base de datos en MySQL**

```
CREATE DATABASE scala_test;

USE scala_test;

CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100),
    email VARCHAR(100),
    age INT
);

INSERT INTO users (name, email, age) VALUES
('Daniel', 'dsludena@utpl.edu.ec', 19),
('Santiago', 'sfrosales@utpl.edu.ec', 19),
('Ricardo', 'rfespinosa@utpl.edu.ec', 20);
```

**Codigo de Scala**

```
import cats.effect.{IO, IOApp, Resource}
import java.sql.{Connection, DriverManager}

object MySQLConnector extends IOApp.Simple:

  def createConnection: Resource[IO, Connection] =
    Resource.make {
      IO {
        val url = "jdbc:mysql://127.0.0.1:3306/scala_test"
        val username = "root"
        val password = "passsword"
        DriverManager.getConnection(url, username, password)
      }
    } { conn =>
      IO(conn.close()).handleErrorWith(_ => IO.unit)
    }


  def run: IO[Unit] =
    createConnection.use { conn =>
      IO {
        val statement = conn.createStatement()
        val resultSet = statement.executeQuery("SELECT * FROM scala_test.users")
        println ("DANIEL LUDENA")
        while (resultSet.next()) {
          println(s"ID: ${resultSet.getInt("id")}, Name: ${resultSet.getString("name")}, Email: ${resultSet.getString("email")}, Age: ${resultSet.getInt("age")}")
        }
      }
    }

```

# Resultados

![image](https://github.com/user-attachments/assets/6f497ef0-c132-4890-85fb-7ddd9860f53d)


![image](https://github.com/user-attachments/assets/06ae4cbe-6f21-4934-9471-583a9c69f4ba)

