<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Registro de Pagos</title>
    <style>
        body { font-family: Arial, sans-serif; text-align: center; }
        table { width: 80%; margin: auto; border-collapse: collapse; }
        th, td { padding: 10px; border: 1px solid black; text-align: center; }
        .pagado { background-color: lightgreen; }
        .no-pagado { background-color: lightcoral; }
        .admin-controls { display: none; } /* Oculto por defecto */
    </style>
</head>
<body>

    <h2>Registro de Pagos Mensuales</h2>

    <label for="password">Clave de administrador:</label>
    <input type="password" id="password">
    <button onclick="verificarClave()">Ingresar</button>

    <table>
        <thead>
            <tr>
                <th>Nombre</th>
                <th>Sept</th>
                <th>Oct</th>
                <th>Nov</th>
                <th>Dic</th>
                <th>Ene</th>
                <th>Feb</th>
                <th>Mar</th>
                <th>Abr</th>
                <th>May</th>
                <th>Jun</th>
                <th>Jul</th>
                <th>Ago</th>
                <th>Sep</th>
            </tr>
        </thead>
        <tbody id="tablaPagos"></tbody>
    </table>

    <script>
        const claveCorrecta = "admin123"; // Cambia esto por tu clave real
        let esAdmin = false;

        const nombres = [
            "Alcaparros", "Almendros", "Arrayanes", "Bilbao", "Bosques de Suba", "Corinto",
            "Costa Azul", "Costa Rica", "El Pinar", "Granados", "Hatochico", "Imperial",
            "La Campiña", "Las Flores", "Lisboa", "Los Cerros", "Margaritas de Suba",
            "Nueva Tibabuyes", "Toscana"
        ];

        const meses = ["sept", "oct", "nov", "dic", "ene", "feb", "mar", "abr", "may", "jun", "jul", "ago", "sep"];
        
        function verificarClave() {
            const password = document.getElementById("password").value;
            if (password === claveCorrecta) {
                esAdmin = true;
                alert("Acceso concedido");
                mostrarTabla();
            } else {
                alert("Clave incorrecta");
            }
        }

        function mostrarTabla() {
            const tabla = document.getElementById("tablaPagos");
            tabla.innerHTML = "";

            nombres.forEach((nombre, i) => {
                const fila = document.createElement("tr");
                fila.innerHTML = `<td>${nombre}</td>`;
                
                meses.forEach((mes, j) => {
                    let estado = obtenerEstadoPago(i, j);
                    let clase = estado ? "pagado" : "no-pagado";
                    let botonHTML = esAdmin ? `<button onclick="togglePago(${i}, ${j})">✓</button>` : "";

                    fila.innerHTML += `<td class="${clase}" id="pago-${i}-${j}">${botonHTML}</td>`;
                });

                tabla.appendChild(fila);
            });
        }

        function togglePago(persona, mes) {
            let estado = obtenerEstadoPago(persona, mes);
            guardarEstadoPago(persona, mes, !estado);
            mostrarTabla();
        }

        function obtenerEstadoPago(persona, mes) {
            let pagos = JSON.parse(localStorage.getItem("pagos")) || {};
            return pagos[`${persona}-${mes}`] || false;
        }

        function guardarEstadoPago(persona, mes, estado) {
            let pagos = JSON.parse(localStorage.getItem("pagos")) || {};
            pagos[`${persona}-${mes}`] = estado;
            localStorage.setItem("pagos", JSON.stringify(pagos));
        }

        mostrarTabla(); // Cargar la tabla al inicio
    </script>

</body>
</html>
