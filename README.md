<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Registro de Pagos</title>
    <style>
        body { font-family: Arial, sans-serif; text-align: center; }
        table { width: 80%; margin: auto; border-collapse: collapse; }
        th, td { border: 1px solid black; padding: 10px; }
        .pagado { background-color: green; color: white; }
        .pendiente { background-color: red; color: white; }
        .total { font-weight: bold; }
    </style>
</head>
<body>
    <h1>Registro de pagos cuota de Mi circuito</h1>
    <input type="password" id="clave" placeholder="Ingrese clave de administrador">
    <button onclick="validarClave()">Acceder</button>
    <table>
        <thead>
            <tr>
                <th>Nombre</th>
                <th>Saldo</th>
                <th>Sept 2024</th>
                <th>Oct 2024</th>
                <th>Nov 2024</th>
                <th>Dic 2024</th>
                <th>Ene 2025</th>
                <th>Feb 2025</th>
                <th>Mar 2025</th>
                <th>Abr 2025</th>
                <th>May 2025</th>
                <th>Jun 2025</th>
                <th>Jul 2025</th>
                <th>Ago 2025</th>
                <th>Sept 2025</th>
            </tr>
        </thead>
        <tbody id="tabla-pagos">
        </tbody>
    </table>
    <script>
        const nombres = ["Alcaparros", "Almendros", "Arrayanes", "Bilbao", "Bosques de Suba", "Corinto", "Costa Azul", "Costa Rica", "El Pinar", "Granados", "Hatochico", "Imperial", "La Campiña", "Las Flores", "Lisboa", "Los Cerros", "Margaritas de Suba", "Nueva Tibabuyes", "Toscana"];
        const cuota = 150000;
        const claveAdmin = "admin123";  // Cambia esta clave para mayor seguridad
        let pagos = {};
        let admin = false;
        
        function validarClave() {
            let claveIngresada = document.getElementById("clave").value;
            if (claveIngresada === claveAdmin) {
                admin = true;
                alert("Acceso concedido");
                renderizarTabla();
            } else {
                alert("Clave incorrecta");
            }
        }
        
        function togglePago(nombre, mes) {
            if (!admin) return;
            pagos[nombre][mes] = !pagos[nombre][mes];
            renderizarTabla();
        }
        
        function renderizarTabla() {
            let tbody = document.getElementById("tabla-pagos");
            tbody.innerHTML = "";
            let hoy = new Date();
            let mesActual = hoy.getMonth() + (hoy.getFullYear() === 2024 ? 4 : -8);
            
            nombres.forEach(nombre => {
                if (!pagos[nombre]) pagos[nombre] = Array(13).fill(false);
                let saldo = pagos[nombre].reduce((acc, pagado) => acc + (pagado ? 0 : cuota), 0);
                
                let row = `<tr><td>${nombre}</td><td>$${saldo.toLocaleString()}</td>`;
                
                pagos[nombre].forEach((pagado, i) => {
                    if (i <= mesActual) {
                        row += `<td class="${pagado ? 'pagado' : 'pendiente'}" onclick="togglePago('${nombre}', ${i})">${pagado ? '✔' : '✖'}</td>`;
                    } else {
                        row += `<td>-</td>`;
                    }
                });
                row += `</tr>`;
                tbody.innerHTML += row;
            });
        }
        
        renderizarTabla();
    </script>
</body>
</html>
