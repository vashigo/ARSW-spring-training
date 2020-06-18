# ARSW-TallerClientService

*En el presente taller realizamos una introducción a las herramientas principales del paquete java.net 
necesario para realizar conecciones UDP y TCP.*  

## Prerequisitos

Tener instalado Java jdk versiones 7+, Maven comandos y git

# Documentación

la documentacion de este proyecto esta en la siguiente ruta del proyecto:

```
raiz /documentation
```

# EJERCICIO 1 - 3.1

Escriba un programa en el cual usted cree un objeto URL e imprima en
pantalla cada uno de los datos que retornan los 8 métodos de la sección anterior.

```java
public class URLManager {

    public static void main(String[] args) {

        try {
            URL personalSite = new URL("https://www.google.com.co:80/search?q=youtube&oq=youtube&aqs=chrome..69i57j69i60j69i65l3j69i60.1623j0j9&sourceid=chrome&ie=UTF-8#YouTube");
            System.out.println("Protocol " + personalSite.getProtocol());
            System.out.println("Authority " + personalSite.getAuthority());
            System.out.println("Host " + personalSite.getHost());
            System.out.println("Port " + personalSite.getPort());
            System.out.println("Path " + personalSite.getPath());
            System.out.println("Query " + personalSite.getQuery());
            System.out.println("File " + personalSite.getFile());
            System.out.println("Ref " + personalSite.getRef());

        } catch (MalformedURLException e) {
            e.printStackTrace();
        }
    }
}
```

<p align="center">
 <img  src="img/ejercicio1-31.JPG">
</p>

# EJERCICIO 2 - 3.2

Escriba una aplicación browser que pregunte una dirección URL al usuario
y que lea datos de esa direcci´on y que los almacene en un archivo con el nombre
resultado.html. Luego intente ver este archivo en el navegador.

```java
public class Browser {

    public static void main(String[] args) throws Exception {
        URL google;
        if (args.length == 0 || "".equals(args[0])) {
            google = new URL("http://www.google.com/");
        } else {
            google = new URL(args[0]);
        }
        try (BufferedReader reader
                = new BufferedReader(new InputStreamReader(google.openStream()))) {
            String inputLine = null;
            FileWriter myWriter = new FileWriter("resultado.html");
            while ((inputLine = reader.readLine()) != null) {
                myWriter.write(inputLine);
            }
            myWriter.close();
        } catch (IOException x) {
            System.err.println(x);
        }
    }
}
```

<p align="center">
 <img  src="img/ejercicio2-32.JPG">
</p>

# EJERCICIO 1 - 4.3.1

Escriba un servidor que reciba un número y responda el cuadrado de este
número.


```java
public class EchoClientSquare {

    public static void main(String[] args) throws IOException {

        Socket echoSocket = null;
        PrintWriter out = null;
        BufferedReader in = null;

        try {
            echoSocket = new Socket("127.0.0.1", 35000);
            out = new PrintWriter(echoSocket.getOutputStream(), true);
            in = new BufferedReader(new InputStreamReader(echoSocket.getInputStream()));
            System.out.println("Numero: ");
        } catch (UnknownHostException e) {
            System.err.println("Don’t know about host!.");
            System.exit(1);
        } catch (IOException e) {
            System.err.println("Couldn’t get I/O for "
                    + "the connection to: localhost.");
            System.exit(1);
        }

        BufferedReader stdIn = new BufferedReader(new InputStreamReader(System.in));
        String userInput;

        while ((userInput = stdIn.readLine()) != null) {
            out.println(userInput);
            System.out.println("echo: " + in.readLine());
        }
        out.close();
        in.close();
        stdIn.close();
        echoSocket.close();
    }
}

public class EchoServerSquare {

    public static void main(String[] args) throws IOException {
        ServerSocket serverSocket = null;
        try {
            serverSocket = new ServerSocket(35000);
            System.out.println("Esperando ...");
        } catch (IOException e) {
            System.err.println("Could not listen on port: 35000.");
            System.exit(1);
        }
        Socket clientSocket = null;
        try {
            clientSocket = serverSocket.accept();
        } catch (IOException e) {
            System.err.println("Accept failed.");
            System.exit(1);
        }
        PrintWriter out = new PrintWriter(clientSocket.getOutputStream(), true);
        BufferedReader in = new BufferedReader(new InputStreamReader(clientSocket.getInputStream()));
        String inputLine, outputLine;
        while ((inputLine = in.readLine()) != null) {
            if (inputLine.equals("Bye.")) {
                break;
            }
            System.out.println("Mensaje: " + inputLine);
            outputLine = "Respuesta: " + String.valueOf(Math.pow(Integer.parseInt(inputLine), 2));
            out.println(outputLine);
        }
        out.close();
        in.close();
        clientSocket.close();
        serverSocket.close();
    }
}
```

<p align="center">
    <img  src="img/ejercicio1a-431.JPG">
    <img  src="img/ejercicio1b-431.JPG">
</p>

# EJERCICIO 2 - 4.3.2

Escriba un servidor que pueda recibir un número y responda con un operación sobre este número. Este servidor puede recibir un mensaje que empiece por
“fun:”, si recibe este mensaje cambia la operación a las especificada. El servidor
debe responder las funciones seno, coseno y tangente. Por defecto debe empezar
calculando el coseno. Por ejemplo, si el primer número que recibe es 0, debe
responder 1, si después recibe π/2 debe responder 0, si luego recibe “fun:sin”
debe cambiar la operación actual a seno, es decir a a partir de ese momento
debe calcular senos. Si enseguida recibe 0 debe responder 0.


```java
package edu.escuelaing.arsw.function;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.PrintWriter;
import java.net.Socket;
import java.net.UnknownHostException;

/**
 *
 * @author vashi
 */
public class EchoClientFunction {

    public static void main(String[] args) throws IOException {

        Socket echoSocket = null;
        PrintWriter out = null;
        BufferedReader in = null;

        try {
            echoSocket = new Socket("127.0.0.1", 35000);
            out = new PrintWriter(echoSocket.getOutputStream(), true);
            in = new BufferedReader(new InputStreamReader(
                    echoSocket.getInputStream()));
        } catch (UnknownHostException e) {
            System.err.println("Don’t know about host!.");
            System.exit(1);
        } catch (IOException e) {
            System.err.println("Couldn’t get I/O for "
                    + "the connection to: localhost.");
            System.exit(1);
        }

        BufferedReader stdIn = new BufferedReader(
                new InputStreamReader(System.in));
        String userInput;

        while ((userInput = stdIn.readLine()) != null) {
            out.println(userInput);
            System.out.println("echo: " + in.readLine());
        }
        out.close();
        in.close();
        stdIn.close();
        echoSocket.close();
    }
}

public class EchoServerFunction {

    private static Map<String, TFunction> selector = new HashMap<String, TFunction>() {
        {
            put("cos", (a) -> Math.cos(a));
            put("sen", (a) -> Math.sin(a));
            put("tan", (a) -> Math.tan(a));
        }
    };

    public interface TFunction {

        double operate(double n);
    }

    public static void main(String[] args) throws IOException {
        ServerSocket serverSocket = null;
        try {
            serverSocket = new ServerSocket(35000);
            System.out.println("Waiting for numbers");
        } catch (IOException e) {
            System.err.println("Could not listen on port: 35000.");
            System.exit(1);
        }
        Socket clientSocket = null;
        try {
            clientSocket = serverSocket.accept();
        } catch (IOException e) {
            System.err.println("Accept failed.");
            System.exit(1);
        }
        PrintWriter out = new PrintWriter(clientSocket.getOutputStream(), true);
        BufferedReader in = new BufferedReader(new InputStreamReader(clientSocket.getInputStream()));
        String inputLine, outputLine, option = "cos";
        TFunction funcion = selector.get(option);
        while ((inputLine = in.readLine()) != null) {
            System.out.println("Mensaje: " + inputLine);
            if (inputLine.contains("fun:")) {
                option = inputLine.split(":")[1];
                if (selector.containsKey(option)) {
                    funcion = selector.get(option);
                    outputLine = "Funcion aceptada";
                } else {
                    System.out.println("Opcion incorrecta");
                    outputLine = "Funcion ignorada";
                }
            } else {
                try {
                    outputLine = funcion.operate(Double.valueOf(inputLine)) + "";
                } catch (NumberFormatException e) {
                    outputLine = "No es un numero";
                }
            }
            out.println(outputLine);
            if (outputLine.equals("Respuesta: Bye.")) {
                break;
            }
        }
        out.close();
        in.close();
        clientSocket.close();
        serverSocket.close();
    }

}
```

<p align="center">
    <img  src="img/ejercicio1a-432.JPG">
    <img  src="img/ejercicio1b-432.JPG">
</p>

# EJERCICIO 1 - 4.5.1

Escriba un servidor web que soporte múltiples solicitudes seguidas (no concurrentes). El servidor debe retornar todos los archivos solicitados, incluyendo
páginas html e imágenes.

## Diseño

<p align="center">
    <img  src="img/diagrama.JPG">
</p>

## Prueba con una plantilla bootstrap

<p align="center">
    <img  src="img/ejercicio1a-451.JPG">
</p>

## respuesta:

<p align="center">
    <img  src="img/pruebas1.JPG">
</p>





# Built con

* [Maven](https://maven.apache.org/) - Dependency Management
* [JAVA JDK 8](http://www.oracle.com/technetwork/java/javase/overview/index.html) - construcción
* [JUnit 3.8.1](https://mvnrepository.com/artifact/junit/junit/3.8.1) - Test Dependency


# Autor

* **ANDRES DAVID VASQUEZ IBAÑEZ** - *Initial work* - [VASHIGO](https://github.com/vashigo)