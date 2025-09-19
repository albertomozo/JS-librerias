
 **correo en HTML** para que el cliente reciba un pedido mucho más claro, como una **tabla de productos**.

---

## 🔹 1. Plantilla en EmailJS

En tu plantilla de EmailJS (`template_xxx`), usa variables con HTML.
Ejemplo:

```html
<h2>Nuevo pedido de {{cliente}}</h2>
<p><b>Email:</b> {{email}}</p>

<h3>Detalles del pedido:</h3>
{{pedido_html}}

<p><b>Total:</b> {{total}} €</p>
```

👉 Observa que aquí usamos `{{pedido_html}}` para incrustar la tabla que vamos a generar con JS.

---

## 🔹 2. Código HTML + JS con tabla en Email

Aquí va el ejemplo completo:

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
    // Simulamos un carrito si no existe
    if (!localStorage.getItem("carrito")) {
      const carritoDemo = [
        { producto: "Camiseta", cantidad: 2, precio: 15 },
        { producto: "Pantalón", cantidad: 1, precio: 30 }
      ];
      localStorage.setItem("carrito", JSON.stringify(carritoDemo));
    }

    document.getElementById("pedidoForm").addEventListener("submit", function(e) {
      e.preventDefault();

      // Datos del cliente
      const nombre = document.getElementById("nombre").value;
      const correo = document.getElementById("correo").value;
      const carrito = JSON.parse(localStorage.getItem("carrito")) || [];

      // Crear tabla HTML para el correo
      let pedidoHTML = `
        <table border="1" cellspacing="0" cellpadding="6" style="border-collapse: collapse; width:100%; font-family: Arial, sans-serif;">
          <thead style="background:#f0f0f0;">
            <tr>
              <th>Producto</th>
              <th>Cantidad</th>
              <th>Precio</th>
              <th>Subtotal</th>
            </tr>
          </thead>
          <tbody>
      `;

      let total = 0;
      carrito.forEach(item => {
        const subtotal = item.cantidad * item.precio;
        total += subtotal;
        pedidoHTML += `
          <tr>
            <td>${item.producto}</td>
            <td style="text-align:center;">${item.cantidad}</td>
            <td style="text-align:right;">${item.precio} €</td>
            <td style="text-align:right;">${subtotal} €</td>
          </tr>
        `;
      });

      pedidoHTML += `
          </tbody>
        </table>
      `;

      // Parámetros de la plantilla
      const templateParams = {
        cliente: nombre,
        email: correo,
        pedido_html: pedidoHTML,
        total: total
      };

      // Enviar correo
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

## 🔹 Resultado

* El cliente recibe un email con:

  * Nombre y correo.
  * Una **tabla bien formateada** con productos, cantidades, precios y subtotales.
  * El **total del pedido** destacado.

---

👉 ¿Quieres que te prepare también una **versión responsive** del correo (con estilos que se adapten a móvil) o prefieres mantenerlo simple y compatible con todos los clientes de correo?
