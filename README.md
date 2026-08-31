```html
<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Control de Servicios</title>

<style>
* {
    box-sizing: border-box;
}

body {
    margin: 0;
    font-family: Arial, sans-serif;
    background: #f3f4f6;
    color: #222;
}

header {
    background: #111827;
    color: white;
    padding: 20px;
    text-align: center;
}

.container {
    max-width: 1000px;
    margin: auto;
    padding: 20px;
}

.card {
    background: white;
    border-radius: 14px;
    padding: 20px;
    margin-bottom: 20px;
    box-shadow: 0 3px 12px rgba(0,0,0,.08);
}

h2 {
    margin-top: 0;
}

.grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(180px, 1fr));
    gap: 12px;
}

input, select, button {
    width: 100%;
    padding: 13px;
    border-radius: 8px;
    border: 1px solid #ccc;
    font-size: 16px;
}

button {
    background: #111827;
    color: white;
    border: none;
    cursor: pointer;
    font-weight: bold;
}

button:hover {
    opacity: .9;
}

.total {
    font-size: 28px;
    font-weight: bold;
    text-align: center;
    padding: 20px;
}

table {
    width: 100%;
    border-collapse: collapse;
}

th, td {
    padding: 10px;
    border-bottom: 1px solid #ddd;
    text-align: left;
}

th {
    background: #f1f1f1;
}

.eliminar {
    background: #dc2626;
    padding: 8px;
}

@media(max-width:600px) {
    table {
        font-size: 13px;
    }

    .container {
        padding: 10px;
    }
}
</style>
</head>

<body>

<header>
    <h1>Control de Servicios</h1>
    <p>Registro diario y control de dinero</p>
</header>

<div class="container">

    <div class="card">
        <h2>Registrar servicio</h2>

        <div class="grid">

            <div>
                <label>Fecha</label>
                <input type="date" id="fecha">
            </div>

            <div>
                <label>Código</label>
                <select id="codigo">
                    <option value="">Seleccionar</option>
                    <option value="1">1 - Servicio 1</option>
                    <option value="2">2 - Servicio 2</option>
                    <option value="3">3 - Servicio 3</option>
                    <option value="4">4 - Servicio 4</option>
                    <option value="5">5 - Servicio 5</option>
                    <option value="6">6 - Servicio 6</option>
                    <option value="7">7 - Servicio 7</option>
                    <option value="8">8 - Servicio 8</option>
                    <option value="9">9 - Servicio 9</option>
                    <option value="10">10 - Spa de pies</option>
                </select>
            </div>

            <div>
                <label>Cantidad</label>
                <input type="number" id="cantidad" min="1" value="1">
            </div>

        </div>

        <br>

        <button onclick="agregarServicio()">
            + Agregar servicio
        </button>
    </div>


    <div class="card">
        <h2>Resumen del día</h2>

        <div class="total">
            $<span id="totalDia">0</span>
        </div>
    </div>


    <div class="card">

        <h2>Registros</h2>

        <div style="overflow-x:auto">

            <table>

                <thead>
                    <tr>
                        <th>Fecha</th>
                        <th>Código</th>
                        <th>Servicio</th>
                        <th>Cantidad</th>
                        <th>Valor</th>
                        <th>Total</th>
                        <th></th>
                    </tr>
                </thead>

                <tbody id="tabla"></tbody>

            </table>

        </div>

    </div>


    <div class="card">

        <h2>Resumen general</h2>

        <div class="grid">

            <div>
                <strong>Total de servicios</strong>
                <h2 id="totalServicios">0</h2>
            </div>

            <div>
                <strong>Dinero acumulado</strong>
                <h2>$<span id="totalGeneral">0</span></h2>
            </div>

        </div>

        <br>

        <button onclick="borrarTodo()">
            Borrar todos los registros
        </button>

    </div>

</div>


<script>

// ========================================
// SERVICIOS Y PRECIOS
// AQUÍ CAMBIAMOS LOS VALORES DEL EXCEL
// ========================================

const servicios = {

    1: {
        nombre: "Servicio 1",
        precio: 0
    },

    2: {
        nombre: "Servicio 2",
        precio: 0
    },

    3: {
        nombre: "Servicio 3",
        precio: 0
    },

    4: {
        nombre: "Servicio 4",
        precio: 0
    },

    5: {
        nombre: "Servicio 5",
        precio: 0
    },

    6: {
        nombre: "Servicio 6",
        precio: 0
    },

    7: {
        nombre: "Servicio 7",
        precio: 0
    },

    8: {
        nombre: "Servicio 8",
        precio: 0
    },

    9: {
        nombre: "Servicio 9",
        precio: 0
    },

    10: {
        nombre: "Spa de pies",
        precio: 50000
    }

};


// ========================================
// GUARDAR DATOS
// ========================================

let registros = JSON.parse(
    localStorage.getItem("registrosServicios")
) || [];


// ========================================
// FECHA ACTUAL
// ========================================

document.getElementById("fecha").value =
    new Date().toISOString().split("T")[0];


// ========================================
// AGREGAR SERVICIO
// ========================================

function agregarServicio() {

    const fecha =
        document.getElementById("fecha").value;

    const codigo =
        document.getElementById("codigo").value;

    const cantidad =
        parseInt(document.getElementById("cantidad").value);

    if (!fecha || !codigo || cantidad <= 0) {

        alert("Completa todos los campos.");

        return;
    }

    const servicio = servicios[codigo];

    const total =
        servicio.precio * cantidad;

    registros.push({

        id: Date.now(),

        fecha: fecha,

        codigo: codigo,

        nombre: servicio.nombre,

        cantidad: cantidad,

        precio: servicio.precio,

        total: total

    });


    guardar();

    mostrar();

    document.getElementById("codigo").value = "";

    document.getElementById("cantidad").value = 1;

}


// ========================================
// GUARDAR
// ========================================

function guardar() {

    localStorage.setItem(
        "registrosServicios",
        JSON.stringify(registros)
    );

}


// ========================================
// MOSTRAR
// ========================================

function mostrar() {

    const tabla =
        document.getElementById("tabla");

    tabla.innerHTML = "";

    let totalGeneral = 0;

    let totalDia = 0;

    let totalServicios = 0;

    const fechaSeleccionada =
        document.getElementById("fecha").value;


    registros.forEach(registro => {

        totalGeneral += registro.total;

        totalServicios += registro.cantidad;

        if (registro.fecha === fechaSeleccionada) {

            totalDia += registro.total;

        }


        const fila =
            document.createElement("tr");

        fila.innerHTML = `

            <td>${registro.fecha}</td>

            <td>${registro.codigo}</td>

            <td>${registro.nombre}</td>

            <td>${registro.cantidad}</td>

            <td>
                $${formato(registro.precio)}
            </td>

            <td>
                $${formato(registro.total)}
            </td>

            <td>
                <button
                    class="eliminar"
                    onclick="eliminar(${registro.id})">
                    X
                </button>
            </td>

        `;

        tabla.appendChild(fila);

    });


    document.getElementById("totalDia").innerText =
        formato(totalDia);

    document.getElementById("totalGeneral").innerText =
        formato(totalGeneral);

    document.getElementById("totalServicios").innerText =
        totalServicios;

}


// ========================================
// ELIMINAR
// ========================================

function eliminar(id) {

    registros =
        registros.filter(
            registro => registro.id !== id
        );

    guardar();

    mostrar();

}


// ========================================
// BORRAR TODO
// ========================================

function borrarTodo() {

    if (
        confirm(
            "¿Seguro que quieres borrar todos los registros?"
        )
    ) {

        registros = [];

        guardar();

        mostrar();

    }

}


// ========================================
// FORMATO DINERO
// ========================================

function formato(numero) {

    return new Intl.NumberFormat(
        "es-CO"
    ).format(numero);

}


// ========================================
// ACTUALIZAR AL CAMBIAR FECHA
// ========================================

document
    .getElementById("fecha")
    .addEventListener(
        "change",
        mostrar
    );


// ========================================
// INICIAR
// ========================================

mostrar();

</script>

</body>
</html>
```
