 **guía rápida de EmailJS aplicada a un carrito en `localStorage`**. 
 
 La idea es:

1. El usuario añade productos → se almacenan en `localStorage` bajo la clave `carrito`.
2. Al pulsar *"Finalizar pedido"*, recogemos el contenido del carrito.
3. Con **EmailJS**, enviamos un correo al cliente (o al administrador) con el detalle.

---

## 🔹 Paso 1. Configurar EmailJS

1. Entra en [https://www.emailjs.com](https://www.emailjs.com).
2. Crea una cuenta gratuita.
3. Obtén:

   * **Service ID**
   * **Template ID**
   * **Public Key**
     (se ven en tu panel EmailJS).

---

## 🔹 Paso 2. Crear plantilla en EmailJS

En tu plantilla (`template_xxx`) defines variables como:

```text
Cliente: {{cliente}}
Email: {{email}}
Pedido:
{{pedido}}
```

---

## 🔹 Paso 3. Código HTML + JS

Aquí un ejemplo sencillo:

```html
<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <title>Pedido con EmailJS</title>
  <script src="https://cdn.jsdelivr.net/npm/emailjs-com@3/dist/email.min.js"></script>
  <script>
    (function() {
      emailjs.init("TU_PUBLIC_KEY"); // Reemplaza con tu Public Key
    })();
  </script>
</head>
<body>
  <h1>Carrito de compra</h1>

  <!-- Formulario para los datos del cliente -->
  <form id="pedidoForm">
    <input type="text" id="nombre" placeholder="Tu nombre" required><br>
    <input type="email" id="correo" placeholder="Tu email" required><br>
    <button type="submit">Finalizar pedido</button>
  </form>

  <script>
    // Ejemplo: simular un carrito en localStorage
    if (!localStorage.getItem("carrito")) {
      const carritoDemo = [
        { producto: "Camiseta", cantidad: 2, precio: 15 },
        { producto: "Pantalón", cantidad: 1, precio: 30 }
      ];
      localStorage.setItem("carrito", JSON.stringify(carritoDemo));
    }

    document.getElementById("pedidoForm").addEventListener("submit", function(e) {
      e.preventDefault();

      // Recuperar datos
      const nombre = document.getElementById("nombre").value;
      const correo = document.getElementById("correo").value;
      const carrito = JSON.parse(localStorage.getItem("carrito")) || [];

      // Formatear pedido
      let pedidoTexto = carrito.map(item =>
        `${item.producto} - Cantidad: ${item.cantidad} - Precio: ${item.precio}€`
      ).join("\n");

      // Parámetros para la plantilla de EmailJS
      const templateParams = {
        cliente: nombre,
        email: correo,
        pedido: pedidoTexto
      };

      // Enviar con EmailJS
      emailjs.send("TU_SERVICE_ID", "TU_TEMPLATE_ID", templateParams)
        .then(function(response) {
          alert("✅ Pedido enviado correctamente");
          console.log("SUCCESS!", response.status, response.text);
          localStorage.removeItem("carrito"); // Vaciar carrito
        }, function(error) {
          alert("❌ Error al enviar pedido");
          console.log("FAILED...", error);
        });
    });
  </script>
</body>
</html>
```

---

## 🔹 Paso 4. Ajustes recomendados

* En la plantilla de EmailJS, organiza bien el **pedido** en HTML (puedes usar `<ul>` en lugar de texto plano).
* Si quieres enviar también el **total**, calcula antes de enviar:

```js
const total = carrito.reduce((acc, item) => acc + item.cantidad * item.precio, 0);
pedidoTexto += `\nTOTAL: ${total} €`;
```

* Para producción, considera un backend (Node, PHP, etc.) si necesitas más seguridad (para no exponer `PUBLIC_KEY` y `SERVICE_ID`).

---

👉 ¿Quieres que te prepare también la versión con **correo en HTML** para que el cliente reciba una tabla bonita con los productos, en vez de texto plano?
