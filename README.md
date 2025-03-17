 <!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Registro de Pagos</title>
    <style>
        body { font-family: Arial, sans-serif; text-align: center; }
        table { width: 90%; margin: auto; border-collapse: collapse; table-layout: fixed; }
        th, td { padding: 10px; border: 1px solid black; text-align: center; }
        th:first-child, td:first-child { position: sticky; left: 0; background: white; z-index: 2; }
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
                <th>Saldo</th> <!-- Columna movida a la izquierda -->
                <th>Nombre</th>
                <th>Sept</th><th>Oct</th><th>Nov</th><th>Dic</th>
                <th>Ene</th><th>Feb</th><th>Mar</th><th>Abr</th>
                <th>May</th><th>Jun</th><th>Jul</th><th>Ago</th><th>Sep</th>
            </tr>
        </thead>
        <tbody id="tablaPagos"></tbody>
        <tfoot>
            <tr>
                <td></td> <td><b>Total recogido:</b></td>
                <td id="total-sept">0</td> <td id="total-oct">0</td> <td id="total-nov">0</td> <td id="total-dic">0</td>
                <td id="total-ene">0</td> <td id="total-feb">0</td> <td id="total-mar">0</td> <td id="total-abr">0</td>
                <td id="total-may">0</td> <td id="total-jun">0</td> <td id="total-jul">0</td> <td id="total-ago">0</td> <td id="total-sep">0</td>
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
        
        let pagos = {}; // Objeto para guardar pagos

        async function cargarPagos() {
            try {
                const response = await fetch("https://tuusuario.github.io/tu-repositorio/pagos.json");
                pagos = await response.json();
            } catch (error) {
                pagos = {}; // Si hay error, inicia vacío
            }
            mostrarTabla();
        }

        async function guardarPagos() {
            const url = "https://api.github.com/repos/tuusuario/tu-repositorio/contents/pagos.json";
            const shaResponse = await fetch(url);
            const shaData = await shaResponse.json();
            const sha = shaData.sha;

            const contenido = btoa(JSON.stringify(pagos, null, 2));

            await fetch(url, {
                method: "PUT",
                headers: {
                    "Authorization": "token TU_GITHUB_TOKEN",
                    "Content-Type": "application/json"
                },
                body: JSON.stringify({
                    message: "Actualizar pagos",
                    content: contenido,
                    sha: sha
                })
            });
        }

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

            let totalPorMes = Array(meses.length).fill(0);

            nombres.forEach((nombre, i) => {
                let pagosRealizados = 0;
                const fila = document.createElement("tr");

                // Calcular saldo de la persona
                let saldoPendiente = (meses.length * cuotaMensual);

                fila.innerHTML = `<td><b>$${saldoPendiente.toLocaleString()}</b></td><td>${nombre}</td>`;

                meses.forEach((mes, j) => {
                    let estado = pagos[`${i}-${j}`] || null; // Estado nulo = celda en blanco
                    let clase = estado === true ? "pagado" : estado === false ? "no-pagado" : "";
                    let botonHTML = esAdmin ? `<button onclick="togglePago(${i}, ${j})">✓</button>` : "";

                    if (estado === false) {
                        pagosRealizados += cuotaMensual;
                        totalPorMes[j] += cuotaMensual;
                    }

                    fila.innerHTML += `<td class="${clase}" id="pago-${i}-${j}">${botonHTML}</td>`;
                });

                fila.cells[0].innerHTML = `<b>$${(saldoPendiente - pagosRealizados).toLocaleString()}</b>`;

                tabla.appendChild(fila);
            });

            meses.forEach((mes, j) => {
                document.getElementById(`total-${mes}`).innerText = `$${totalPorMes[j].toLocaleString()}`;
            });
        }

        function togglePago(persona, mes) {
            let estado = pagos[`${persona}-${mes}`];
            pagos[`${persona}-${mes}`] = estado === false ? null : false;
            guardarPagos();
            mostrarTabla();
        }

        cargarPagos();
    </script>

</body>
</html>
