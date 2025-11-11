# Archivos en JAVA
---

## 🧩 PARTE 1 – Archivos JSON con la librería GSON

### 🎯 Objetivo

Guardar, leer, actualizar y eliminar información de una clase `Universidad` que contiene una lista de `Estudiante` usando **JSON**.

---

### 📦 1. Estructura de Clases

```java
import java.util.ArrayList;
import java.util.List;

public class Universidad {
    private String nombre;
    private List<Estudiante> estudiantes;

    public Universidad(String nombre) {
        this.nombre = nombre;
        this.estudiantes = new ArrayList<>();
    }

    public String getNombre() {
        return nombre;
    }

    public List<Estudiante> getEstudiantes() {
        return estudiantes;
    }

    public void agregarEstudiante(Estudiante e) {
        estudiantes.add(e);
    }

    public void eliminarEstudiantePorId(int id) {
        estudiantes.removeIf(e -> e.getId() == id);
    }
}
```
Ahora se muestra la clase estudiante

```java
public class Estudiante {
    private int id;
    private String nombre;
    private String correo;
    private String fechaNacimiento;

    public Estudiante(int id, String nombre, String correo, String fechaNacimiento) {
        this.id = id;
        this.nombre = nombre;
        this.correo = correo;
        this.fechaNacimiento = fechaNacimiento;
    }

    public int getId() { return id; }
    public String getNombre() { return nombre; }
    public String getCorreo() { return correo; }
    public String getFechaNacimiento() { return fechaNacimiento; }
}
```

---

### 💾 2. Guardar y Leer JSON usando GSON
Instala la dependencia desde el JAR descargándolo  desde el siguiente enlace:

https://mvnrepository.com/artifact/com.google.code.gson/gson/2.13.2
 
Instala la dependencia de GSON si usas Maven:

```xml
<dependency>
  <groupId>com.google.code.gson</groupId>
  <artifactId>gson</artifactId>
  <version>2.10.1</version>
</dependency>
```

---

### 🧠 3. Clase de Gestión de Archivos JSON

```java
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import java.io.FileReader;
import java.io.FileWriter;
import java.io.IOException;

public class ArchivoJSON {

    private static final String RUTA = "universidad.json";
    private static final Gson gson = new GsonBuilder().setPrettyPrinting().create();

    public static void guardar(Universidad u) {
        try (FileWriter writer = new FileWriter(RUTA)) {
            gson.toJson(u, writer);
            System.out.println("✅ Universidad guardada en " + RUTA);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public static Universidad leer() {
        try (FileReader reader = new FileReader(RUTA)) {
            Universidad u = gson.fromJson(reader, Universidad.class);
            System.out.println("📂 Universidad cargada desde archivo.");
            return u;
        } catch (IOException e) {
            System.out.println("⚠ No se encontró el archivo, se crea una nueva universidad.");
            return new Universidad("Universidad Nacional");
        }
    }
}
```

---

### ⚙️ 4. Ejemplo de Uso

```java
public class MainJSON {
    public static void main(String[] args) {

        // Leer universidad existente o crear una nueva
        Universidad uni = ArchivoJSON.leer();

        // Crear estudiantes
        Estudiante e1 = new Estudiante(1, "Felipe Gómez", "felipe@correo.com", "2001-05-10");
        Estudiante e2 = new Estudiante(2, "Laura Pérez", "laura@correo.com", "2000-08-15");

        // Agregar a la lista
        uni.agregarEstudiante(e1);
        uni.agregarEstudiante(e2);

        // Guardar en JSON
        ArchivoJSON.guardar(uni);

        // Eliminar uno y guardar nuevamente
        uni.eliminarEstudiantePorId(1);
        ArchivoJSON.guardar(uni);
    }
}
```

---

## 📊 PARTE 2 – Exportar a CSV (sin librerías)

### 🎯 Objetivo

Exportar el listado de estudiantes de la universidad a un archivo **CSV** (valores separados por punto y coma).

---

### 🧠 Código

```java
import java.io.FileWriter;
import java.io.IOException;

public class ArchivoCSV {

    private static final String RUTA = "estudiantes.csv";

    public static void exportar(Universidad uni) {
        try (FileWriter writer = new FileWriter(RUTA)) {

            // Encabezado
            writer.write("ID,Nombre,Correo,FechaNacimiento\n");

            // Contenido
            for (Estudiante e : uni.getEstudiantes()) {
                writer.write(e.getId() + ";" + e.getNombre() + ";" +
                        e.getCorreo() + ";" + e.getFechaNacimiento() + "\n");
            }

            System.out.println("✅ Archivo CSV generado correctamente: " + RUTA);

        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

### 🧪 Ejemplo

```java
public class MainCSV {
    public static void main(String[] args) {
        Universidad uni = ArchivoJSON.leer(); // Cargamos desde JSON
        ArchivoCSV.exportar(uni);             // Exportamos a CSV
    }
}
```

---

## 📜 PARTE 3 – Crear un archivo .DOC (texto plano)

> Aquí no usaremos librerías como Apache POI, sino que generaremos un **.doc como archivo de texto plano**, el cual puede abrirse en Word.

---

### 🧠 Código

```java
import java.io.FileWriter;
import java.io.IOException;

public class ArchivoDOC {

    public static void generarCertificado(Estudiante e) {
        String nombreArchivo = "Certificado_" + e.getNombre().replace(" ", "_") + ".doc";

        try (FileWriter writer = new FileWriter(nombreArchivo)) {

            writer.write("UNIVERSIDAD NACIONAL DE TECNOLOGÍA\n");
            writer.write("====================================\n\n");
            writer.write("CERTIFICADO DE ESTUDIO\n\n");
            writer.write("Se certifica que el estudiante:\n\n");
            writer.write("Nombre: " + e.getNombre() + "\n");
            writer.write("Correo: " + e.getCorreo() + "\n");
            writer.write("Fecha de Nacimiento: " + e.getFechaNacimiento() + "\n\n");
            writer.write("Ha cursado satisfactoriamente sus estudios.\n\n");
            writer.write("__________________________________________\n");
            writer.write("Firma del Director Académico\n");

            System.out.println("✅ Certificado generado: " + nombreArchivo);

        } catch (IOException ex) {
            ex.printStackTrace();
        }
    }
}
```

### 🧪 Ejemplo

```java
public class MainDOC {
    public static void main(String[] args) {
        Estudiante e = new Estudiante(2, "Laura Pérez", "laura@correo.com", "2000-08-15");
        ArchivoDOC.generarCertificado(e);
    }
}
```

---

## 🧾 RESUMEN GENERAL

| Tipo de archivo       | Librería usada | Contenido                          | Función principal                   |
| --------------------- | -------------- | ---------------------------------- | ----------------------------------- |
| **JSON**              | GSON           | Universidad + lista de estudiantes | Guardar, leer, actualizar, eliminar |
| **CSV**               | Ninguna        | Exportar listado de estudiantes    | Reporte plano de datos              |
| **DOC (texto plano)** | Ninguna        | Certificado individual             | Documento formal tipo carta         |

---
