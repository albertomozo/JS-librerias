**guía sencilla de la API de geolocalización** junto con un **ejercicio práctico** que combine:

1. **Geolocalización del navegador**
2. **Consulta del tiempo en tu ciudad usando una API meteorológica (ej: OpenWeatherMap)**
3. **Devolver el resultado mediante voz usando la Web Speech API**

---

# 📘 Guía rápida de la API de Geolocalización

### 1. ¿Qué es?

La **Geolocation API** de JavaScript permite obtener la posición del dispositivo (latitud y longitud).
Se suele usar en móviles y navegadores modernos.

### 2. Métodos principales

```js
navigator.geolocation.getCurrentPosition(success, error, options)
```

* `success`: función que se ejecuta si se obtiene la posición.
* `error`: función que se ejecuta si falla.
* `options`: parámetros (precisión, timeout, etc.).

### 3. Ejemplo básico

```js
navigator.geolocation.getCurrentPosition(
  (pos) => {
    console.log("Latitud:", pos.coords.latitude);
    console.log("Longitud:", pos.coords.longitude);
  },
  (err) => {
    console.error("Error:", err.message);
  }
);
```

---

# 🎯 Ejercicio: “Dime el tiempo con tu voz”

### Objetivo

1. Obtener la ubicación del usuario.
2. Consultar la API de **OpenWeatherMap** para saber el tiempo.
3. Usar **voz sintética** para leer el resultado en voz alta.

---

### 💻 Código HTML + JS

Guárdalo como `index.html` y recuerda poner tu API Key de [OpenWeatherMap](https://openweathermap.org/api).

```html
<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <title>Tiempo con voz</title>
</head>
<body>
  <h1>¿Qué tiempo hace?</h1>
  <button id="btnTiempo">Dime el tiempo</button>

  <script>
    const btn = document.getElementById("btnTiempo");

    btn.addEventListener("click", () => {
      if ("geolocation" in navigator) {
        navigator.geolocation.getCurrentPosition(async (pos) => {
          const lat = pos.coords.latitude;
          const lon = pos.coords.longitude;

          // API de OpenWeatherMap (necesitas tu API KEY)
          const apiKey = "TU_API_KEY_AQUI";
          const url = `https://api.openweathermap.org/data/2.5/weather?lat=${lat}&lon=${lon}&units=metric&lang=es&appid=${apiKey}`;

          try {
            const resp = await fetch(url);
            const data = await resp.json();
            console.log(`🟩 datos `);
            console.log(data);
            
            const ciudad = data.name;
            const temp = data.main.temp;
            const descripcion = data.weather[0].description;

            const mensaje = `En ${ciudad} la temperatura es de ${temp} grados y está ${descripcion}.`;

            // Mostrar en pantalla
            alert(mensaje);

            // Decirlo en voz alta
            const utter = new SpeechSynthesisUtterance(mensaje);
            utter.lang = "es-ES";
            speechSynthesis.speak(utter);

          } catch (err) {
            console.error("Error al obtener el tiempo:", err);
          }
        });
      } else {
        alert("Tu navegador no soporta geolocalización.");
      }
    });
  </script>
</body>
</html>
```

---

# 📝 Tareas para los alumnos

1. **Cambiar la voz**: usar otra voz disponible con `speechSynthesis.getVoices()`.
2. **Mejorar la presentación**: mostrar un icono del tiempo además del texto.
3. **Añadir un input**: permitir consultar el tiempo de una ciudad escrita, no solo la actual.
4. **Añadir en la locucion y texto** la fecha y hora del sistema.  


