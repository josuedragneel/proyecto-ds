import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;

class Producto {
    String nombre;
    double precio;

    public Producto(String nombre, double precio) {
        this.nombre = nombre;
        this.precio = precio;
    }
}

class Venta {
    Producto producto;
    int cantidad;

    public Venta(Producto producto, int cantidad) {
        this.producto = producto;
        this.cantidad = cantidad;
    }

    public double getTotal() {
        return producto.precio * cantidad;
    }

    public String getTipoProducto() {
        return producto.nombre;
    }
}

class PorcentajeDeVenta {
    public static void calcularPorcentajesVenta(Venta[] ventas, double totalVentas) {
        System.out.println("Consulta 3: Porcentaje de aporte a las ventas por tipo de producto");
        String[] tiposProducto = {"Popcorn", "Hotdog", "Refrescos", "Agua", "Chocolate"};
        for (String tipo : tiposProducto) {
            double totalPorTipo = 0;
            for (Venta venta : ventas) {
                if (venta != null && venta.getTipoProducto().contains(tipo)) {
                    totalPorTipo += venta.getTotal();
                }
            }
            double porcentaje = (totalPorTipo / totalVentas) * 100;
            System.out.println(tipo + " " + porcentaje + "%");
        }
    }
}

class MenuDeProductos {
    private Producto[] productos;

    public MenuDeProductos() {
        productos = new Producto[12];
        productos[0] = new Producto("Popcorn Chico", 1.25);
        productos[1] = new Producto("Popcorn Mediano", 2.00);
        productos[2] = new Producto("Popcorn Grande", 3.00);
        productos[3] = new Producto("Hotdog", 2.50);
        productos[4] = new Producto("Refresco Pequeño", 1.30);
        productos[5] = new Producto("Refresco Mediano", 2.00);
        productos[6] = new Producto("Refresco Grande", 2.75);
        productos[7] = new Producto("Agua", 1.50);
        productos[8] = new Producto("Chocolate", 1.75);
        productos[9] = new Producto("Combo 1 (Popcorn Mediano, Refresco Grande)", 4.50);
        productos[10] = new Producto("Combo 2 (Hotdog, Refresco Grande)", 5.00);
        productos[11] = new Producto("Combo 3 (Popcorn Grande, 2 Popcorn Mediano)", 6.80);
    }

    public void imprimirMenu() {
        System.out.println("Menú:");
        System.out.println("+------------------------------+---------+");
        System.out.printf("| %-28s | %-8s |%n", "Producto", "Precio");
        System.out.println("+------------------------------+---------+");
        for (int i = 0; i < productos.length; i++) {
            System.out.printf("| %d | %-36s $%.2f |%n", (i + 1), productos[i].nombre, productos[i].precio);
        }
        System.out.println("+------------------------------+---------+");
    }
    
    public Producto getProduct(int index) {
        return productos[index];
    }
}

class VentasManager {
    private Venta[] ventas;
    private int numVentas;
    private double totalVentas;
    private double totalDescuentoJubilados;
    private double totalCaramelos;
    private double[] totalPorTipoProducto;

    public VentasManager() {
        ventas = new Venta[100];
        numVentas = 0;
        totalVentas = 0;
        totalDescuentoJubilados = 0;
        totalCaramelos = 0;
        totalPorTipoProducto = new double[5];
    }

    public void procesarVenta(int opcion, BufferedReader reader) throws IOException {
        Producto producto = MenuDeProductos.getProduct(opcion - 1);
        System.out.println("Cantidad:");
        String cantidadStr = reader.readLine();
        int cantidad = 0;

        try {
            cantidad = Integer.parseInt(cantidadStr);
        } catch (NumberFormatException e) {
            System.out.println("Cantidad no válida. Ingrese la cantidad nuevamente.");
            return;
        }

        ventas[numVentas] = new Venta(producto, cantidad);
        numVentas++;
        totalVentas += producto.precio * cantidad;

        if (opcion >= 1 && opcion <= 3) {
            totalPorTipoProducto[0] += producto.precio * cantidad;
        } else if (opcion == 4) {
            totalPorTipoProducto[1] += producto.precio * cantidad;
        } else if (opcion >= 5 && opcion <= 7) {
            totalPorTipoProducto[2] += producto.precio * cantidad;
        } else if (opcion >= 8 && opcion <= 12) {
            totalPorTipoProducto[3] += producto.precio * cantidad;
        }
    }

    public void procesarCaramelos(BufferedReader reader) throws IOException {
        System.out.println("¿Desea agregar caramelos? (S/N):");
        String respuestaCaramelos = reader.readLine();

        if (respuestaCaramelos.equalsIgnoreCase("S")) {
            System.out.println("Cantidad de caramelos:");
            String cantidadCaramelosStr = reader.readLine();

            try {
                int cantidadCaramelos = Integer.parseInt(cantidadCaramelosStr);
                totalCaramelos = 0.50 * cantidadCaramelos;
                totalVentas += totalCaramelos;
            } catch (NumberFormatException e) {
                System.out.println("Cantidad de caramelos no válida. Se omitirán los caramelos.");
            }
        }
    }

    public void procesarDescuentoJubilado(String respuestaJubilado) {
        if (respuestaJubilado.equalsIgnoreCase("S")) {
            totalDescuentoJubilados = totalVentas * 0.20;
            totalVentas -= totalDescuentoJubilados;
        }
    }

    public void imprimirFactura() {
        System.out.println("Factura:");
        for (int i = 0; i < numVentas; i++) {
            Venta venta = ventas[i];
            System.out.printf("%d %s - $%.2f%n", venta.cantidad, venta.producto.nombre, venta.getTotal());
        }

        if (totalCaramelos > 0) {
            System.out.printf("%.0f caramelos - $%.2f%n", (totalCaramelos / 0.50), totalCaramelos);
        }

        System.out.println("Total de compra: $" + totalVentas);
    }

    public void mostrarTotalPagar() {
        double totalPagar = totalVentas;
        if (totalDescuentoJubilados > 0) {
            System.out.println("Descuento por jubilado (20%): $" + totalDescuentoJubilados);
            totalPagar -= totalDescuentoJubilados;
        }

        if (totalCaramelos > 0) {
            totalPagar += totalCaramelos;
        }

        System.out.println("Total a pagar: $" + totalPagar);
        System.out.println("Gracias por su compra. ¡Vuelva pronto!");
    }

    public void mostrarPorcentajesVenta() {
        PorcentajeDeVenta.calcularPorcentajesVenta(ventas, totalVentas);
    }
}

class IntegrantesProyecto {
    public static void mostrarIntegrantes() {
        System.out.println("Integrantes del proyecto:");
        System.out.println("David Garcia, 8-992-210");
        System.out.println("Russell Tuñon, 8-967-1019");
        System.out.println("Josue Castillero, 8-999-2315");
    }
}

public class Main {
    public static void main(String[] args) throws IOException {
        BufferedReader reader = new BufferedReader(new InputStreamReader(System.in));
        VentasManager ventasManager = new VentasManager();

        MenuDeProductos menu = new MenuDeProductos();
        menu.imprimirMenu();

        while (true) {
            System.out.println("Seleccione un producto (1-12) o 0 para finalizar:");
            int opcion;
            try {
                opcion = Integer.parseInt(reader.readLine());
            } catch (NumberFormatException e) {
                System.out.println("Opción no válida. Por favor, ingrese un número válido.");
                continue;
            }

            if (opcion == 0) {
                break;
            }

            ventasManager.procesarVenta(opcion, reader);
        }

        ventasManager.procesarCaramelos(reader);

        System.out.println("¿Es el cliente jubilado? (S/N):");
        String respuestaJubilado = reader.readLine();
        ventasManager.procesarDescuentoJubilado(respuestaJubilado);

        ventasManager.imprimirFactura();
        ventasManager.mostrarTotalPagar();

        // Verificar si se debe mostrar el porcentaje de venta
        System.out.println("¿Desea ver los porcentajes de ventas? (ventas de hoy/S para ver, cualquier otra tecla para omitir):");
        String opcionPorcentajes = reader.readLine();
        if (opcionPorcentajes.equalsIgnoreCase("ventas de hoy")) {
            ventasManager.mostrarPorcentajesVenta();
        }

        // Verificar si se deben mostrar los participantes del proyecto
        if (opcionPorcentajes.equalsIgnoreCase("integrantes")) {
            IntegrantesProyecto.mostrarIntegrantes();
        }
    }
}
