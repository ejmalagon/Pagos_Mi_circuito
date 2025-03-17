<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Control de Pagos</title>
    <script type="module">
        // Importar Firebase
        import { initializeApp } from "https://www.gstatic.com/firebasejs/9.6.1/firebase-app.js";
        import { getDatabase, ref, get, set, onValue } from "https://www.gstatic.com/firebasejs/9.6.1/firebase-database.js";

const firebaseConfig = {
  apiKey: "AIzaSyCAhgkaAYfRnNaMOd-fDBOT-JU99-D0Jbg",
  authDomain: "mi-circuito-d3dfc.firebaseapp.com",
  databaseURL: "https://mi-circuito-d3dfc-default-rtdb.firebaseio.com",
  projectId: "mi-circuito-d3dfc",
  storageBucket: "mi-circuito-d3dfc.firebasestorage.app",
  messagingSenderId: "433537289978",
  appId: "1:433537289978:web:44accf1c25277fca540343",
  measurementId: "G-FXNEYDK896"
};


        // Inicializar Firebase
        const app = initializeApp(firebaseConfig);
        const db = getDatabase(app);
        const pagosRef = ref(db, "pagos");

        // Lista de personas
        const personas = [
            "Alcaparros", "Almendros", "Arrayanes", "Bilbao", "Bosques de Suba",
            "Corinto", "Costa Azul", "Costa Rica", "El Pinar", "Granados",
            "Hatochico", "Imperial", "La Campiña", "Las Flores", "Lisboa",
            "Los Cerros", "Margaritas de Suba", "Nueva Tibabuyes", "Toscana"
        ];

        // Lista de meses
        const meses = ["Sep-2024", "Oct-2024", "Nov-2024", "Dic-2024", "Ene-2025",
                       "Feb-2025", "Mar-2025", "Abr-2025", "May-2025", "Jun-2025",
                       "Jul-2025", "Ago-2025", "Sep-2025"];

        let pagos = {};
        let admin = false; // Estado de autenticación

        // Cargar datos desde Firebase
        function cargarPagos() {
            onValue(pagosRef, (snapshot) => {
                pagos = snapshot.val() || {};
                mostrarTabla();
            });
        }

        // Guardar pagos en Firebase (solo si es admin)
        function guardarPagos() {
            if (admin) {
                set(pagosRef, pagos);
            }
        }

        // Cambiar estado de pago (solo si es admin)
        function togglePago(persona, mes) {
            if (!admin) return; // Si no es admin, no hace nada
            let key = `${persona}-${mes}`;
            pagos[key] = !pagos[key];
            guardarPagos();
            mostrarTabla();
        }

        // Mostrar tabla
        function mostrarTabla() {
            let tabla = `<table border="1">
                <tr>
                    <th>Persona</th>
                    ${meses.map(m => `<th>${m}</th>`).join("")}
                    <th>Saldo</th>
                </tr>`;

            let totalPorMes = Array(meses.length).fill(0);
            let cuotaMensual = 150000;

            personas.forEach(persona => {
                let saldo = 0;
                let fila = `<tr><td><b>${persona}</b></td>`;

                meses.forEach((mes, index) => {
                    let key = `${persona}-${mes}`;
                    let pagado = pagos[key];

                    if (pagado === undefined) {
                        fila += `<td style="background:white;" ${admin ? `onclick="togglePago('${persona}', '${mes}')"` : ""}></td>`;
                    } else if (pagado) {
                        fila += `<td style="background:green; color:white;" ${admin ? `onclick="togglePago('${persona}', '${mes}')"` : ""}>✔</td>`;
                        totalPorMes[index] += cuotaMensual;
                    } else {
                        fila += `<td style="background:red; color:white;" ${admin ? `onclick="togglePago('${persona}', '${mes}')"` : ""}>✘</td>`;
                        saldo += cuotaMensual;
                    }
                });

                fila += `<td><b>$${saldo.toLocaleString()}</b></td></tr>`;
                tabla += fila;
            });

            // Agregar fila de totales
            let filaTotales = `<tr><td><b>Total Recogido</b></td>`;
            totalPorMes.forEach(total => {
                filaTotales += `<td><b>$${total.toLocaleString()}</b></td>`;
            });
            filaTotales += `<td></td></tr>`;

            tabla += filaTotales + `</table>`;
            document.getElementById("contenedor").innerHTML = tabla;
        }

        // Ingresar como administrador
        function login() {
            let clave = prompt("Ingrese la clave de administrador:");
            if (clave === "1234") { // 🔹 Cambia "1234" por tu clave real
                admin = true;
                alert("✅ Acceso concedido");
            } else {
                alert("❌ Clave incorrecta");
            }
            mostrarTabla();
        }

        window.togglePago = togglePago;
        window.login = login;
        cargarPagos();
    </script>
</head>
<body>
    <h1>Control de Pagos</h1>
    <button onclick="login()">🔑 Ingresar como Administrador</button>
    <div id="contenedor"></div>
</body>
</html>
