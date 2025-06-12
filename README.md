# estructuradedatos
TAREA
import java.util.*;

// Modelo de respuesta para operaciones
class OperationResult {
    public String message;
    public boolean success;
    public Object data;

    public OperationResult(String message, boolean success, Object data) {
        this.message = message;
        this.success = success;
        this.data = data;
    }
}

// Clase base estudiante
abstract class Estudiante {
    protected String nombre;
    protected String id;
    protected double examen;
    protected double practica;

    public Estudiante(String nombre, String id) {
        this.nombre = nombre;
        this.id = id;
    }

    public void registrarNotas(double examen, double practica) {
        this.examen = examen;
        this.practica = practica;
    }

    public abstract double calcularNotaFinal();

    public abstract String tipo();

    public String mostrarDatos() {
        return tipo() + " - " + nombre + " (" + id + ") - Nota final: " + calcularNotaFinal();
    }

    public boolean aprobado() {
        return calcularNotaFinal() >= 70;
    }
}

// Estudiante presencial
class EstudiantePresencial extends Estudiante {
    public EstudiantePresencial(String nombre, String id) {
        super(nombre, id);
    }

    @Override
    public double calcularNotaFinal() {
        return examen * 0.6 + practica * 0.4;
    }

    @Override
    public String tipo() {
        return "Presencial";
    }
}

// Estudiante a distancia
class EstudianteDistancia extends Estudiante {
    public EstudianteDistancia(String nombre, String id) {
        super(nombre, id);
    }

    @Override
    public double calcularNotaFinal() {
        return examen * 0.7 + practica * 0.3;
    }

    @Override
    public String tipo() {
        return "Distancia";
    }
}

// Grupo de una asignatura
class Grupo {
    private String nombre;
    private List<Estudiante> estudiantes;

    public Grupo(String nombre) {
        this.nombre = nombre;
        this.estudiantes = new ArrayList<>();
    }

    public OperationResult agregarEstudiante(Estudiante estudiante) {
        estudiantes.add(estudiante);
        return new OperationResult("Estudiante agregado", true, null);
    }

    public OperationResult registrarNotas(String id, double examen, double practica) {
        for (Estudiante e : estudiantes) {
            if (e.id.equals(id)) {
                e.registrarNotas(examen, practica);
                return new OperationResult("Notas registradas", true, null);
            }
        }
        return new OperationResult("Estudiante no encontrado", false, null);
    }

    public void mostrarCalificaciones() {
        System.out.println("--- Grupo: " + nombre + " ---");
        for (Estudiante e : estudiantes) {
            System.out.println(e.mostrarDatos());
        }
    }

    public double porcentajeAprobados() {
        if (estudiantes.isEmpty()) return 0;
        long aprobados = estudiantes.stream().filter(Estudiante::aprobado).count();
        return (aprobados * 100.0) / estudiantes.size();
    }
}

// Clase principal para probar
public class GestionEstudiantes {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        Grupo grupo = new Grupo("Grupo 1 - Programación");

        System.out.println("Ingrese número de estudiantes a registrar:");
        int n = Integer.parseInt(sc.nextLine());

        for (int i = 0; i < n; i++) {
            System.out.println("Nombre:");
            String nombre = sc.nextLine();
            System.out.println("ID:");
            String id = sc.nextLine();
            System.out.println("Tipo (1 = Presencial, 2 = Distancia):");
            int tipo = Integer.parseInt(sc.nextLine());

            Estudiante estudiante;
            if (tipo == 1) {
                estudiante = new EstudiantePresencial(nombre, id);
            } else {
                estudiante = new EstudianteDistancia(nombre, id);
            }
            grupo.agregarEstudiante(estudiante);

            System.out.println("Calificación examen:");
            double ex = Double.parseDouble(sc.nextLine());
            System.out.println("Calificación práctica:");
            double pr = Double.parseDouble(sc.nextLine());
            grupo.registrarNotas(id, ex, pr);
        }

        grupo.mostrarCalificaciones();
        System.out.println("Porcentaje de aprobados: " + grupo.porcentajeAprobados() + "%");
    }
}
