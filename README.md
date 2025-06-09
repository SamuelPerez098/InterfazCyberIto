
# CyberIto



Este proyecto está diseñado para gestionar productos, usuarios y autenticaciones, integrando una interfaz gráfica Java Swing y una base de datos PostgreSQL. Es útil como sistema de ventas o inventario ligero, adaptable a distintos entornos.

---

## 📁 Estructura del Proyecto----


## ▣══ Paquete: `Controlador` ══▣

Contiene la lógica de control entre la interfaz de usuario y los modelos. Coordina acciones del sistema como autenticación, manejo de productos y flujo de tickets.
### ┣▇ 'conUsuarios.java'
Controlador para operaciones CRUD sobre los usuarios del sistema.

Funciones:
mostrarUsuarios(DefaultTableModel modelo):
Llena una tabla con los usuarios registrados en la base de datos.

registrarUsuario(String nombre, String rol):
Ejecuta una inserción en la base de datos para agregar un nuevo usuario.

editarUsuario(int id, String nuevoNombre, String nuevoRol):
Actualiza la información del usuario con base en su ID.

eliminarUsuario(int id):
Borra un usuario del sistema con su identificador.

Conexiones:
Utiliza la clase CoenBD para conectar con PostgreSQL.

Utiliza objetos Usuario para el transporte de datos.
###  ┣▇ `AccionTicket.java`

Clase que representa una acción sobre el ticket (agregar o eliminar productos). Guarda los datos del producto y el tipo de operación.

- **Tipos definidos:** `AGREGAR`, `ELIMINAR`
- **Uso principal:** Registrar acciones realizadas por el usuario en la interfaz (como agregar o quitar productos del ticket).
- **Constructores:**
  - Uno que recibe el tipo y un arreglo de objetos (nombre, precio, stock).
  - Otro que recibe directamente los valores del producto y los convierte internamente.

### ┣▇ `ControladorLogin.java`

Gestiona la autenticación de usuarios y verifica la existencia de roles (usuarios) en la base de datos PostgreSQL.

- `autenticar(String username, String password)`:  
  Autentica al usuario contra la base de datos. Si es exitoso, obtiene el nombre del rol actual y lo asigna al objeto `Usuario`.

- `existeUsuario(String username)`:  
  Verifica si un nombre de usuario (rol) existe en `pg_roles`, útil para evitar registros duplicados o para validaciones.

### ┣▇  `conProductos.java`

Clase encargada de interactuar con la base de datos para operaciones CRUD sobre productos y categorías.

- `obtenerCategorias()`:  
  Recupera una lista de todas las categorías registradas.

- `obtenerProductos()`:  
  Recupera todos los productos desde la base de datos.

- `insertarProducto(Connection conn, producto prod, int categoriaId)`:  
  Inserta un nuevo producto asociado a una categoría.

- `eliminarProductoPorId(Connection conn, int idProducto)`:  
  Elimina un producto usando su identificador.

- `obtenerProductoPorNombre(Connection conn, String nombre)`:  
  Busca un producto específico por su nombre y devuelve su información.

------

## ▣══ Paquete: `Modelo`══▣

El paquete `Modelo` contiene las clases que representan las entidades principales del sistema. Estas clases encapsulan los datos relacionados con usuarios, productos y categorías, y se utilizan para trasladar información entre la base de datos y la interfaz.

---

### ┣▇ `Usuario.java`

Modelo que representa a un usuario del sistema. Puede ser un cliente, administrador, u otro tipo según el contexto.

#### Atributos:
- `int id`: Identificador único del usuario.
- `String username`: Nombre de usuario (rol en PostgreSQL).
- `String tipoUsuario`: Tipo o rol del usuario (por ejemplo: `cliente_user`, `admin_user`).

#### Métodos principales:
- Getters y Setters para cada atributo.

---

### ┣▇ `categoria.java`

Modelo que representa una categoría de productos.

#### Atributos:
- `int id`: Identificador único de la categoría.
- `String nombre`: Nombre de la categoría (por ejemplo: "Electrónica", "Hogar").

#### Métodos:
- Constructor con parámetros: `categoria(int id, String nombre)`
- Métodos de acceso: `getId()`, `getNombre()`
- Método `toString()` sobreescrito para mostrar el nombre directamente, útil para listas desplegables (`JComboBox`).

---

### ┣▇ `producto.java`

Modelo que representa un producto del inventario.

#### Atributos:
- `int id`: Identificador único del producto.
- `String nombre`: Nombre del producto.
- `String marca`: Marca del producto.
- `String modelo`: Modelo o referencia técnica.
- `double precio`: Precio unitario.
- `int stock`: Cantidad en existencia.

#### Constructores:
- **Para inserción:**  
  `producto(String nombre, String marca, String modelo, double precio, int stock)`
- **Para lectura desde la base de datos (incluye ID):**  
  `producto(String nombre, String marca, String modelo, double precio, int stock, int id)`

#### Métodos:
- Getters y Setters para todos los atributos.
- Encapsula toda la información necesaria para mostrar, insertar o actualizar productos en la base de datos.

---

### ┣▇ Nota

Estas clases son POJOs (Plain Old Java Objects) y se utilizan como estructuras de datos para facilitar la comunicación entre la capa de control (`Controlador`) y la base de datos (`Conexion`).


---

##  ▣══ Paquete: `conexion`══▣

El paquete `conexion` se encarga de manejar la conexión a la base de datos PostgreSQL y de facilitar componentes visuales conectados a datos, como listas desplegables dinámicas.

---

### ┣▇ `CoenBD.java`

Clase encargada de gestionar las conexiones con la base de datos PostgreSQL.

#### Atributos:
- `URL`: Ruta JDBC hacia la base de datos (localhost:5432/BDTAP).
- `usuarioActual`, `contraseñaActual`: Variables estáticas para mantener la sesión del usuario activo.

#### Métodos:
- `Connection conectar(String username, String password)`:  
  Establece una conexión usando credenciales proporcionadas (usado en el login).

- `void establecerUsuarioActual(String usuario, String contraseña)`:  
  Guarda las credenciales del usuario autenticado para futuras conexiones automáticas.

- `Connection conectarPorDefecto()`:  
  Intenta conectar con la base de datos usando las credenciales del usuario actual. Devuelve `null` si falla.

 **Uso típico**: Centraliza la lógica de conexión para que otros paquetes puedan conectarse sin reescribir código de conexión.

---

### ┣▇ `JCategoriaComboBox.java`

Componente visual personalizado que extiende `JComboBox<categoria>` y permite cargar dinámicamente las categorías desde la base de datos.

#### Métodos:
- `inicializar(Connection conn)`:  
  Inicializa la lista con una conexión existente. Se recomienda usar en ventanas que ya gestionan la conexión.

- `cargarCategorias()`:  
  Carga categorías usando `CoenBD.conectarPorDefecto()` y las asigna al combo box. Ideal para ventanas simples.

- `categoria getCategoriaSeleccionada()`:  
  Devuelve el objeto `categoria` actualmente seleccionado en el combo box.

- `void seleccionarCategoriaPorId(int idCategoria)`:  
  Selecciona la categoría correspondiente al ID recibido.

 **Uso típico**: Este componente facilita que la interfaz gráfica siempre tenga las categorías actualizadas, evitando errores y duplicación de lógica.

---

## ▣══ Paquete: `Utilidades`══▣

Este paquete contiene clases para manipulación visual de componentes Swing y envío de correos con archivos adjuntos.


### ┣▇ Clase `Sesion`

Clase utilitaria para gestionar la sesión actual del usuario dentro de la aplicación.

Guarda y mantiene la información del usuario loggeado mientras la aplicación está abierta, permitiendo saber quién está autenticado y qué tipo de rol posee (admin o cliente).

#### ▪️ Campos estáticos:

- `private static String tipoUsuario`  
  Tipo del usuario loggeado (`"admin"` o `"cliente"`).

- `private static String nombreUsuario`  
  Nombre del usuario actualmente en sesión.

---

#### ▪️ Métodos principales:

- `public static void iniciarSesion(String usuario, String tipo)`  
  Inicia la sesión del usuario especificado.  
  - **Parámetros:**  
    - `usuario`: Nombre de usuario.  
    - `tipo`: Tipo de usuario (`"admin"` o `"cliente"`).

- `public static String getTipoUsuario()`  
  Retorna el tipo del usuario actualmente loggeado.

- `public static String getNombreUsuario()`  
  Retorna el nombre del usuario actualmente loggeado.

- `public static void cerrarSesion()`  
  Cierra la sesión actual, limpiando el nombre y tipo de usuario.

- `public static boolean sesionActiva()`  
  Verifica si hay una sesión activa.  
  - **Retorna:** `true` si hay un usuario loggeado, `false` si no.

---

---

### ┣▇ Clase `CambiosVisuales`

Clase utilitaria para manejar imágenes y efectos visuales en componentes Swing (`JLabel`, `JButton`).

#### Métodos principales:

- `ajustarImagenAlComponente(String ruta, JComponent componente)`  
  Ajusta una imagen al tamaño del componente.

- `aplicarCambioImagen(JComponent componente, String rutaNormal, String rutaHover)`  
  Aplica imágenes normal y hover sin escalar al componente.

- `aplicarCambioImagenEscalada(JComponent componente, String rutaNormal, String rutaHover)`  
  Aplica imágenes normal y hover escaladas automáticamente con alta calidad.

- `invertirColoresBoton(JButton boton)`  
  Invierte los colores de fondo y texto de un botón al pasar el mouse.

- `invertirColoresLabel(JLabel label)`  
  Invierte los colores de fondo y texto de un label al pasar el mouse.

- `escalarIconoConCalidad(JComponent componente)`  
  Escala con alta calidad el icono actual del componente.

- `cambiarImagen(JLabel label, String rutaImagen)`  
  Cambia la imagen de un JLabel escalándola con alta calidad.

---

### ┣▇ Clase `CorreoUtil`

Clase utilitaria para el envío de correos electrónicos con archivos PDF adjuntos usando Gmail SMTP.

#### Método principal:

- `enviarCorreoConPDF(String destinatario, String rutaArchivo)`  
  Envía un correo electrónico con asunto "Ticket de Compra" y adjunta un archivo PDF.  
  Usa autenticación SMTP con cuenta Gmail y clave de aplicación.

### ┣▇ Clase `EfectoEscritura`

Proporciona animaciones de escritura y borrado progresivo de texto en `JLabel` y efectos combinados con `JButton`.

#### Métodos principales:

- `aplicarAnimacion(JLabel label)`  
  Aplica animación de escritura/borrado en un `JLabel` al pasar el mouse sobre él.

- `aplicarEfecto(JButton boton, JLabel label)`  
  Aplica animación sincronizada de escritura/borrado en un `JLabel` controlado por un `JButton`.

---

### ┣▇ Clase `GeneradorPDF`

Genera un archivo PDF tipo ticket con los datos de un modelo de tabla y total, usando una plantilla PDF predefinida.

#### Método principal:

- `generarTicketPDF(DefaultTableModel modeloTicket, double total)`  
  Genera un archivo PDF llamado `ticket_final.pdf` con los datos del ticket basado en el modelo de tabla y el total especificado.  
  Utiliza la plantilla `Tciket.pdf` ubicada en el paquete `utilidades`.


---
## ▣══ Paquete: `interfaz`══▣

## Clase: `IngresoClienteFrame` (paquete `Interfaz`)

Ventana gráfica (`JFrame`) que permite ingresar los datos de un cliente, como su nombre y correo electrónico. Ideal para registrar clientes antes de una compra o actividad.


### ┣▇ Componentes de la interfaz:

- `JTextField txtCorreo`: Campo de texto para ingresar el correo electrónico del cliente.
- `JTextField txtNombre`: Campo de texto para ingresar el nombre del cliente.
- `JButton btnAceptar`: Botón para confirmar el ingreso de datos.

---

### ┣▇ Funcionalidad:

- Organiza los elementos en un `GridBagLayout` para una disposición flexible.
- Al presionar el botón "Aceptar":
  - Se valida que ambos campos estén completos.
  - Se muestra un mensaje si algún campo está vacío.
  - Si los campos están completos, se imprimen en consola (simulación de guardado en base de datos) y se cierra la ventana.

---

###┣▇ Método principal:

- `public static void main(String[] args)`  
  Lanza la ventana utilizando el estilo visual *Nimbus* si está disponible.


##  Clase: `Loggin` (paquete `Interfaz`)

Ventana principal para el inicio de sesión de usuarios. Incluye efectos visuales, placeholders en los campos, validación de credenciales y rutas diferenciadas para usuarios *admin* y *cliente*.

---

###  ┣▇Interfaz gráfica:

- `JTextField txtUsuario`: Campo de texto con *placeholder* "Usuario".
- `JPasswordField txtContrasena`: Campo de contraseña con *placeholder* "Contraseña".
- `JLabel lblE1`, `lblE2`: Etiquetas para mostrar errores de validación.
- `JButton btnAcceder`: Botón para iniciar sesión.
- `JLabel lblBie`: Etiqueta con animación de efecto escritura (usando `EfectoEscritura`).
- `JLabel lblUsuario`, `lblContrasena`: Íconos con cambio visual al pasar el cursor (usando `CambiosVisuales`).

---

### ┣▇ Efectos y mejoras visuales:

- `EfectoEscritura.aplicarAnimacion(lblBie)`: Aplica un efecto de texto que se borra y escribe nuevamente cuando se pasa el cursor sobre la etiqueta de bienvenida.
- `CambiosVisuales.aplicarCambioImagenEscalada(...)`: Cambia dinámicamente los íconos al pasar el cursor, mejorando la experiencia visual del login.
- *Placeholders inteligentes* que desaparecen al enfocar y reaparecen si no se escribe nada.

---

### ┣▇ Lógica de autenticación (`btnAccederActionPerformed`):

1. **Validaciones previas**:
   - Verifica que los campos no estén vacíos.
   - Muestra mensajes de error personalizados en rojo.

2. **Proceso de autenticación**:
   - Usa `ControladorLogin.autenticar(usuario, contraseña)` para validar.
   - Si el usuario es correcto:
     - Abre la ventana `OpcionR` si es *admin*.
     - Abre la ventana `productoos` si es *cliente*.
     - Cierra la ventana de login.
   - Si falla:
     - Verifica si el usuario existe y muestra errores específicos:
       - "Usuario no registrado" si el nombre no se encuentra.
       - "Contraseña incorrecta" si el usuario existe pero la contraseña es errónea.

---

### ┣▇ Método principal:

- Lanza la interfaz de login en el hilo de eventos de Swing:
  ```java
  public static void main(String args[]) {
      java.awt.EventQueue.invokeLater(() -> new Loggin().setVisible(true));
  }

---
##  Clase: `OpcionR` (paquete `Interfaz`)

Ventana gráfica que actúa como **panel de opciones** para usuarios de tipo **admin**, ofreciendo accesos a funcionalidades clave como agregar usuarios, modificar productos o cerrar sesión.

---

### ┣▇ Elementos de interfaz y mejoras visuales

-  `lblBi`, `lblAgregar`, `lblModificar`, `lblRegresar`: Etiquetas con animaciones y cambio de color.
-  `btnAgregar`, `btnModificar`, `btnRegresar`: Botones con imágenes interactivas al pasar el cursor.
-  `lblEnca1`: Etiqueta decorativa con imagen.

#### ┣▇ Efectos aplicados:

- `EfectoEscritura.aplicarEfecto(...)`: Efecto visual de texto al pasar el cursor sobre etiquetas asociadas a botones.
- `EfectoEscritura.aplicarAnimacion(lblBi)`: Aplica animación de escritura sobre la etiqueta de bienvenida.
- `CambiosVisuales.invertirColoresLabel(...)`: Inversión de colores en etiquetas para destacar acciones.
- `CambiosVisuales.aplicarCambioImagenEscalada(...)`: Cambia la imagen de los botones al pasar el mouse, mejorando la experiencia visual.

---

### ┣▇ Funcionalidades principales

#### ┣▇ `btnAgregarActionPerformed(...)`
Conecta a la base de datos y abre la ventana de registro de usuario.


- Lanza la interfaz de login en el hilo de eventos de Swing:
  ```java
  Connection conn = CoenBD.conectarPorDefecto();
new Registrar(conn).setVisible(true);
dispose();

### ┣▇ btnModificarActionPerformed(...)

Abre la interfaz `productoos`, que permite modificar productos.  
Cierra la ventana actual.

### ┣▇ btnRegresarActionPerformed(...)
Muestra un cuadro de confirmación para cerrar sesión.

Si el usuario confirma, vuelve a la ventana de login (Loggin)

---

##  Clase `VentanaAgregarProducto`

Ventana de interfaz gráfica para agregar productos a la base de datos en una aplicación Java Swing.

---

### ┣▇ Constructor `VentanaAgregarProducto(productoos ventanaPrincipal)`

Inicializa la ventana y aplica configuraciones visuales.  
Recibe una referencia a la ventana principal `productoos` para poder actualizarla tras agregar un producto.

- Aplica cambios visuales a etiquetas e íconos.
- Carga las categorías desde la base de datos.
- Configura *placeholders* en los campos de texto.

```java
this.ventanaPrincipal = ventanaPrincipal;
initComponents();
setDefaultCloseOperation(JFrame.DISPOSE_ON_CLOSE);
```

┣▇  configurarPlaceholders()
Asigna texto temporal (placeholder) a los campos de entrada:
Nombre, Marca, Modelo, Stock, Precio.

- Cambia el color al ganar/perder el foco.
- Limpia el texto si el usuario comienza a escribir

```java
txtN.setText("Nombre");
txtN.setForeground(Color.GRAY);
txtN.addFocusListener(new java.awt.event.FocusAdapter() {
    public void focusGained(java.awt.event.FocusEvent evt) {
        if (txtN.getText().equals("Nombre")) {
            txtN.setText("");
            txtN.setForeground(Color.BLACK);
        }
    }
    public void focusLost(java.awt.event.FocusEvent evt) {
        if (txtN.getText().isEmpty()) {
            txtN.setText("Nombre");
            txtN.setForeground(Color.GRAY);
        }
    }
});

// Repite la misma lógica para:
// txtMa -> "Marca"
// txtMo -> "Modelo"
// txtS  -> "Stock"
// txtP  -> "Precio"
```

┣▇ cargarCategorias()
Carga las categorías disponibles en el combo personalizado (jCategoriaComboBox).

```java
jCategoriaComboBox.cargarCategorias();
```

┣▇ guardarProducto()
Guarda el producto capturado en la base de datos.

- Valida los campos y asegura que se haya seleccionado una categoría.

- Inserta el producto usando conProductos.insertarProducto(...)

Si fue exitoso:

- Muestra un mensaje de éxito.

- Llama ventanaPrincipal.cargarTablaProductos() para refrescar la tabla.

- Cierra la ventana actual con dispose().

```java
String nombre = txtN.getText().trim();
String marca = txtMa.getText().trim();
String modelo = txtMo.getText().trim();
int stock = Integer.parseInt(txtS.getText().trim());
double precio = Double.parseDouble(txtP.getText().trim());
categoria cat = (categoria) jCategoriaComboBox.getSelectedItem();

if (cat == null) {
    JOptionPane.showMessageDialog(this, "Selecciona una categoría.");
    return;
}

producto nuevo = new producto(nombre, marca, modelo, precio, stock);
try (Connection conn = CoenBD.conectarPorDefecto();) {
    boolean insertado = conProductos.insertarProducto(conn, nuevo, cat.getId());
    if (insertado) {
        JOptionPane.showMessageDialog(this, "Producto agregado con éxito.");
        if (ventanaPrincipal != null) {
            ventanaPrincipal.cargarTablaProductos();
        }
        dispose();
    } else {
        JOptionPane.showMessageDialog(this, "No se pudo insertar el producto.");
    }
} catch (NumberFormatException e) {
    JOptionPane.showMessageDialog(this, "ID, stock o precio inválidos.");
} catch (SQLException e) {
    JOptionPane.showMessageDialog(this, "Error de BD: " + e.getMessage());
}
```
┣▇ invertirLabel(JLabel lbl)
Invierte los colores de un JLabel (fondo ↔ texto) después de restaurar los colores originales de todos los labels.

```java
resetearLabels();
Color fg = lbl.getForeground();
Color bg = lbl.getBackground();
lbl.setForeground(bg);
lbl.setBackground(fg);
```

┣▇ resetearLabels()
Restaura los colores originales (fondo y texto) de todas las etiquetas del formulario

---

# ┣▇ Clase `Registrar`

Clase de la interfaz gráfica en Java Swing para el registro de usuarios. Permite ingresar un nombre de usuario, contraseña y seleccionar el tipo de usuario (`admin` o `cliente`). Conecta con una base de datos PostgreSQL y crea el usuario como un rol con permisos asignados.

---

## ┣▇  Paquetes y dependencias

```java
package Interfaz;

import Utilidades.CambiosVisuales;
import Utilidades.EfectoEscritura;
import conexion.CoenBD;

import java.awt.Color;
import javax.swing.*;
import java.sql.*;
```

## ┣▇ Constructor

- Inicializa la ventana y aplica efectos visuales:
- Anima botones e iconos con EfectoEscritura y CambiosVisuales.
- Carga imágenes escaladas al pasar el mouse.
- Establece placeholders en los campos de entrada.
- Configura el combo box con opciones admin y cliente.
- Establece la conexión a la base de datos con CoenBD.conectarPorDefecto().

```java
public Registrar(Connection conn)
```
## ┣▇  Método configurarPlaceholders()

Asigna texto temporal a los campos:

Campo Usuario:

- Texto: "Usuario" (gris).
- Al enfocar: se limpia y se pone en negro.
- Al desenfocar: se restaura si está vacío.

Campo Contraseña:

- Texto: "Contraseña" (gris).
- Inicialmente en texto plano.
- Al enfocar: se oculta el texto y cambia a negro.
- Al desenfocar: vuelve a texto plano si está vacío.

```java
private void configurarPlaceholders()
```
## ┣▇  Método  Método agregarUsuario()

```java
private boolean agregarUsuario()

```

Lógica para crear un nuevo usuario en PostgreSQL:

- Verifica que no haya campos vacíos.
- Valida el formato del nombre de usuario.
- Ejecuta SQL:

```sql
CREATE ROLE nombre WITH LOGIN PASSWORD 'contraseña' IN ROLE usuarios_base;
GRANT rol_admin/rol_cliente TO nombre;
```

### ┣▇ Evento btnAActionPerformed
```java
private void btnAActionPerformed(java.awt.event.ActionEvent evt)
```
- Llama a agregarUsuario().
- Si fue exitoso, abre la ventana de Loggin y cierra esta ventana.

### ┣▇ Evento btnRActionPerformed
```java
private void btnRActionPerformed(java.awt.event.ActionEvent evt)
```

- Cierra la ventana actual.
- Abre la interfaz OpcionR.

---

# Clase `VentanaAgregarProducto`

**Paquete:** `Interfaz`  
**Archivo:** `VentanaAgregarProducto.java`

## Descripción
La clase `VentanaAgregarProducto` proporciona una interfaz gráfica para agregar productos a la base de datos PostgreSQL. Permite al usuario ingresar datos como nombre, marca, modelo, stock, precio y categoría del producto.

Esta ventana se integra con una ventana principal (`productoos`) para actualizar la tabla de productos después de insertar un nuevo producto exitosamente.

---

##  Estructura

### Atributos principales

```java
private productoos ventanaPrincipal;
private final Color colorFgOriginal;
private final Color colorBgOriginal;
```

- ventanaPrincipal: referencia a la ventana principal para refrescar la vista tras agregar un producto.
- colorFgOriginal y colorBgOriginal: colores originales de las etiquetas para invertir visualmente los estilos.

### ┣▇ Constructor

```java
public VentanaAgregarProducto(productoos ventanaPrincipal)
```
Inicializa la ventana, aplica estilos visuales con CambiosVisuales, carga categorías en el combo box y configura los placeholders en los campos de texto.


### ┣▇ Métodos principales

`configurarPlaceholders()`

Agrega textos sugeridos ("Nombre", "Marca", etc.) a los campos y los quita o restaura según el enfoque (focus) del usuario.


`guardarProducto()`
Guarda un nuevo producto en la base de datos. Incluye validaciones de entrada y notificaciones al usuario.

```java
producto nuevo = new producto(nombre, marca, modelo, precio, stock);
conProductos.insertarProducto(conn, nuevo, cat.getId());

```

- Si el producto se agrega correctamente, la tabla de productos se actualiza y la ventana se cierra.

- Si ocurre un error de SQL o de formato numérico, se informa mediante un JOptionPane.

invertirLabel(JLabel lbl)
Invierte los colores de un JLabel específico para destacar visualmente.

resetearLabels()
Restaura los colores originales de todas las etiquetas.

### ┣▇ Dependencias

`Controlador.conProductos`

`Modelo.categoria`

`Modelo.producto`

`Utilidades.CambiosVisuales`

`conexion.CoenBD`

`conexion.JCategoriaComboBox`

 Mensajes al usuario

`"Selecciona una categoría."`

`"Producto agregado con éxito."`

`"No se pudo insertar el producto."`

`"ID, stock o precio inválidos."`

`"Error de BD: ..."`


#  ┣▇ VentanaEditarProducto

Clase `VentanaEditarProducto` que representa una ventana gráfica para editar productos en una base de datos.

## Paquete

`Interfaz`

##  ┣▇ Descripción

Esta clase extiende `JFrame` y permite modificar los datos de un producto existente, incluyendo nombre, marca, modelo, stock y precio. Además, maneja la actualización en la base de datos y ofrece una interfaz visual con placeholders, efectos y cambio dinámico de imágenes.

---

##  ┣▇ Atributos principales

- `ventanaProductoos` : referencia a la ventana principal que lista productos para actualizar su tabla tras modificaciones.
- `idProducto` : entero que almacena el identificador del producto a editar.
- `colorFgOriginal`, `colorBgOriginal` : colores originales usados para los labels.

---

## ┣▇  Métodos públicos

### `setIdProducto(int idProducto)`

Establece el ID del producto que se va a editar.

### `setDatosProducto(producto prod)`

Carga los datos de un objeto `producto` en los campos de texto para su edición.

---

## ┣▇ Constructor

### `VentanaEditarProducto(productoos ventana)`

Recibe la ventana principal para referencia y llamada a actualización de tabla. Inicializa componentes, aplica estilos, efectos y configura placeholders en los campos de texto.

---

##  ┣▇ Métodos privados

### `actualizarProducto()`

Obtiene los datos de los campos, valida y actualiza el producto en la base de datos. Muestra mensajes de éxito o error y cierra la ventana en caso de éxito.

### `configurarPlaceholders()`

Define placeholders para los campos de texto (nombre, marca, modelo, stock, precio) con comportamiento para limpiar/restaurar texto y colores al enfocar o perder foco.

### `vincularCampoYLabels(JTextField campo, JLabel labelTexto, JLabel labelImagen, String rutaNormal, String rutaHover)`

Vincula un campo de texto con labels para cambiar imágenes y colores al enfocar y desenfocar.

### `invertirLabel(JLabel lbl)`

Invierte los colores de primer plano y fondo de un label.

### `resetearLabels()`

Restaura los colores originales en todos los labels.

---

##  ┣▇ Eventos

- `btnGActionPerformed`: Llama a `actualizarProducto()` para guardar los cambios.
- `btnRActionPerformed`: Cierra la ventana actual.

---

##  ┣▇ Dependencias

- Clases: `producto` (modelo), `productoos` (ventana principal), `CambiosVisuales` y `EfectoEscritura` (utilidades visuales), `CoenBD` (conexión BD).
- Librerías Java Swing, AWT, SQL.

---

##  ┣▇ Uso básico

1. Crear instancia pasando la ventana principal.
2. Usar `setIdProducto` y `setDatosProducto` para cargar el producto a editar.
3. Mostrar la ventana para que el usuario modifique y guarde.

---

# Clase `productoos`

Esta clase implementa una interfaz gráfica para la gestión de productos y un ticket de venta en Java Swing. Permite visualizar, filtrar y agregar productos a un ticket con manejo de acciones deshacer/rehacer, y muestra el total acumulado.

---

##  ┣▇Descripción General

- Extiende `javax.swing.JFrame`.
- Muestra una tabla con productos disponibles.
- Permite filtrar productos por nombre o ID.
- Permite agregar productos al ticket mediante doble clic.
- Controla el stock y evita duplicados en el ticket.
- Calcula y actualiza el total del ticket en tiempo real.
- Implementa confirmación al cerrar la ventana.
- Aplica cambios visuales y efectos sobre botones e íconos.
- Soporta deshacer y rehacer acciones sobre el ticket.

---

##  ┣▇Atributos Principales

| Nombre            | Tipo                      | Descripción                             |
|-------------------|---------------------------|---------------------------------------|
| `productos`       | `List<producto>`          | Lista de productos disponibles.       |
| `tablaTicket`     | `JTable`                  | Tabla que muestra los productos en el ticket. |
| `modeloTicket`    | `DefaultTableModel`       | Modelo de datos para la tabla del ticket. |
| `totalTicket`     | `double`                  | Total acumulado del ticket.            |
| `lblTotal`        | `JLabel`                  | Etiqueta que muestra el total en pantalla. |
| `historialTicket` | `Stack<AccionTicket>`     | Pila para almacenar acciones realizadas (deshacer). |
| `pilaRehacer`     | `Stack<AccionTicket>`     | Pila para almacenar acciones para rehacer. |

---

##  ┣▇Constructores

### `productoos()`

- Inicializa componentes gráficos.
- Configura cierre de ventana con confirmación.
- Carga productos en la tabla.
- Inicializa listeners para interactividad.
- Aplica cambios visuales a botones e íconos.
- Configura el filtro dinámico para búsqueda de productos.
- Inicializa la tabla del ticket y su modelo.
- Añade listener para actualización automática del total.
- Configura selección y edición de cantidad en el ticket.

---

##  ┣▇Métodos Principales

### `void cargarTablaProductos()`

- Carga los productos desde la base de datos o controlador.
- Llena la tabla de productos con columnas: Nombre, Precio, Stock, ID.
- Bloquea la edición de las celdas.

### `void inicializarListenersTablaProductos()`

- Añade listener para actualizar detalles del producto seleccionado.
- Añade mouse listener para agregar producto al ticket con doble clic.
- Valida stock y evita agregar productos duplicados al ticket.
- Actualiza total y guarda acción para historial.

### `void filtrarProductos()`

- Filtra los productos mostrados en la tabla según texto de búsqueda.
- Busca coincidencias por nombre o ID (que comiencen con el texto).

### `void actualizarTotal()`

- Calcula el total multiplicando precio por cantidad para cada producto en el ticket.
- Actualiza la etiqueta `lblResultado` con el total formateado.

### `void cargarTodosLosProductos()`

- Carga todos los productos desde la base de datos directamente.
- Actualiza la lista interna y la tabla visual.

### `void cargarProductosPorCategoria(int categoriaId)`

- Filtra y carga productos de una categoría específica desde la base de datos.
- Actualiza la lista interna y la tabla visual.

### `boolean estaProductoEnTicket(String nombreProducto)`

- Verifica si un producto ya existe en el ticket para evitar duplicados.

### `void configurarListenerTablaProductos()`

- Configura (o resetea) el listener de mouse para la tabla de productos.
- Permite definir la lógica al hacer doble clic en un producto.

### `void configurarPlaceholders()`

- Configura texto y color placeholder para el campo de búsqueda.

---

##  ┣▇Eventos y Listeners

- **Ventana:** Pregunta confirmación antes de cerrar.
- **Campo búsqueda:** Filtra la tabla en cada cambio.
- **Tabla productos:** Doble clic agrega producto al ticket si hay stock.
- **Tabla ticket:** Permite seleccionar fila para editar cantidad.
- **Modelo ticket:** Actualiza total automáticamente al cambiar.

---

##  ┣▇Dependencias

- `Controlador.conProductos` para obtener lista de productos.
- `conexion.CoenBD` para conexión con base de datos.
- `Utilidades.CambiosVisuales` para efectos visuales en botones e íconos.
- `Utilidades.EfectoEscritura` para animaciones de texto (comentado).
- Clases modelo como `producto` y controlador `AccionTicket`.

---

##  ┣▇Uso Básico

1. Crear instancia de `productoos`.
2. Mostrar ventana con `setVisible(true)`.
3. Interactuar con la tabla para agregar productos al ticket.
4. Usar filtro para buscar productos por nombre o ID.
5. Ver total actualizado en tiempo real.
6. Cerrar ventana con confirmación.

---

##  ┣▇Ejemplo de inicialización

```java
public static void main(String[] args) {
    java.awt.EventQueue.invokeLater(() -> {
        new productoos().setVisible(true);
    });
}
```

#  ┣▇ Controlador de interfaz

## btnCobrarActionPerformed

Muestra un diálogo con opciones para enviar por correo o guardar la compra.  
- **Enviar:** Pide correo del cliente, genera un PDF del ticket y lo envía por email.  
- **Guardar:** Valida stock, actualiza inventario en la base de datos y limpia el ticket.  

---

## btnEdicionActionPerformed

Permite editar el producto seleccionado en la tabla.  
- Obtiene el producto seleccionado.  
- Busca el producto en la base de datos.  
- Abre ventana de edición con los datos del producto.  

---

## btnAvanzarActionPerformed

Rehace la última acción realizada en el ticket (agregar o eliminar producto).  
- Recupera acción de la pila de rehacer.  
- Actualiza el modelo del ticket y total.  
- Guarda la acción en el historial.  

---

## btnRegresarActionPerformed

Deshace la última acción realizada en el ticket.  
- Recupera acción del historial.  
- Revierta los cambios en el modelo y total.  
- Guarda la acción en la pila de rehacer.  

---

## btnBorrarActionPerformed

Elimina un producto seleccionado de la lista principal.  
- Confirma la eliminación con el usuario.  
- Busca y elimina el producto en la base de datos.  
- Actualiza la tabla de productos.  

---

## btnBorrarTicketActionPerformed

Elimina el producto seleccionado del ticket actual.  
- Guarda la acción en el historial.  
- Actualiza el total descontando el precio del producto eliminado.  

---

## btnMasProductoActionPerformed

Incrementa la cantidad del producto seleccionado en el ticket.  
- Verifica que no se exceda el stock disponible.  
- Actualiza la cantidad en la tabla.  

---

## btnMenosProductoActionPerformed

Disminuye la cantidad del producto seleccionado en el ticket.  
- Si la cantidad llega a 1, pregunta para eliminar el producto del ticket.  
- Actualiza la cantidad o elimina el producto.  

---

## txtCantidadKeyPressed

Permite cambiar la cantidad del producto seleccionado con enter.  
- Valida que la cantidad sea numérica y positiva.  
- Verifica disponibilidad de stock.  
- Actualiza la cantidad en la tabla.  

---

## btnCategoriaRedActionPerformed

Carga productos de la categoría con ID 2.  

---

## btnCategoriaPCActionPerformed

Carga productos de la categoría con ID 1.  

---

## btnCategoriaPapeleriaActionPerformed

Carga productos de la categoría con ID 3.  

---

## btnCategoriaAllActionPerformed

Carga todos los productos desde la base de datos.  
- Limpia tabla y lista interna antes de cargar.  
- Añade productos a la tabla.  

---

## txtBuscarActionPerformed

Evento pendiente de implementación para búsqueda.  

---

## btnAgregarActionPerformed

Abre ventana para agregar un nuevo producto.  

---

## btnBorrarTodoActionPerformed

Confirma y borra todo el contenido del ticket.  
- Limpia la tabla del ticket y el total.  
- Limpia historial de acciones.  

---

## btnRActionPerformed

Confirma cierre de sesión y vuelve a la pantalla de login.  

---

┣▇ EU.java
Ventana para editar información de usuarios existentes.

Funcionalidades:
Muestra los datos del usuario seleccionado.

Permite modificar campos como nombre de usuario y tipo de rol.

Utiliza un JComboBox con roles cargados por JRolComboBox.

Componentes:
Campos de texto, botón para guardar cambios y validaciones básicas.

┣▇ RU.java
Ventana para registrar nuevos usuarios en el sistema.

Funcionalidades:
Formulario para ingresar nombre de usuario y rol.

Guarda la información en la base de datos utilizando controladores del sistema.

Componentes:
Campos de entrada, botón de registro, combo de roles cargado desde la base de datos.

┣▇ OpcionUsuarios.java
Ventana principal de gestión de usuarios.

Funcionalidades:
Lista todos los usuarios registrados.

Ofrece botones para agregar, editar y eliminar usuarios.

Integra otras ventanas como RU (Registro) y EU (Edición).

Componentes:
Tabla para visualizar los usuarios y barra de navegación básica.
