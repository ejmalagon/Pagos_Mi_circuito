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
                <th>Sept</th><th>Oct</th><th>Nov</th><th>Dic</th>
                <th>Ene</th><th>Feb</th><th>Mar</th><th>Abr</th>
                <th>May</th><th>Jun</th><th>Jul</th><th>Ago</th><th>Sep</th>
                <th>Saldo</th> <!-- Nueva columna -->
            </tr>
        </thead>
        <tbody id="tablaPagos"></tbody>
        <tfoot>
            <tr>
                <td><b>Total recogido:</b></td>
                <td id="total-sept">0</td> <td id="total-oct">0</td> <td id="total-nov">0</td> <td id="total-dic">0</td>
                <td id="total-ene">0</td> <td id="total-feb">0</td> <td id="total-mar">0</td> <td id="total-abr">0</td>
                <td id="total-may">0</td> <td id="total-jun">0</td> <td id="total-jul">0</td> <td id="total-ago">0</td> <td id="total-sep">0</td>
                <td></td>
            </tr>
        </tfoot>
    </table>

    <script>
        const claveCorrecta = "admin123"; // Cambia esto por tu clave real
        let esAdmin = false;
        const cuotaMensual = 150000;

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

            let totalPorMes = Array(meses.length).fill(0); // Inicializa el total de cada mes en 0

            nombres.forEach((nombre, i) => {
                let pagosRealizados = 0;
                const fila = document.createElement("tr");
                fila.innerHTML = `<td>${nombre}</td>`;
                
                meses.forEach((mes, j) => {
                    let estado = obtenerEstadoPago(i, j);
                    let clase = estado ? "pagado" : "no-pagado";
                    let botonHTML = esAdmin ? `<button onclick="togglePago(${i}, ${j})">✓</button>` : "";

                    if (estado) {
                        pagosRealizados += cuotaMensual; // Sumar si ha pagado
                        totalPorMes[j] += cuotaMensual; // Sumar al total del mes
                    }

                    fila.innerHTML += `<td class="${clase}" id="pago-${i}-${j}">${botonHTML}</td>`;
                });

                // Calcular saldo de la persona
                let saldoPendiente = (meses.length * cuotaMensual) - pagosRealizados;
                fila.innerHTML += `<td><b>$${saldoPendiente.toLocaleString()}</b></td>`;

                tabla.appendChild(fila);
            });

            // Actualizar los totales de cada mes
            meses.forEach((mes, j) => {
                document.getElementById(`total-${mes}`).innerText = `$${totalPorMes[j].toLocaleString()}`;
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
