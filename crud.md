<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Panadería Rico Pan - Catálogo de productos</title>
    <style>
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background-color: #f4f4f4;
            display: flex;
            justify-content: center;
            align-items: center;
            margin: 0;
            padding: 20px 0;
        }

        .container {
            width: 750px;
            background-color: #ffffff;
            border: 2px solid #333;
            border-radius: 4px;
            box-shadow: 0 4px 8px rgba(0,0,0,0.1);
            overflow: hidden;
            display: flex;
            flex-direction: column;
        }

        .header {
            display: flex;
            align-items: center;
            justify-content: space-between;
            padding: 15px 30px;
            border-bottom: 2px solid #333;
        }

        .header-title {
            font-weight: bold;
            font-size: 1.2rem;
            color: #222;
        }

        .header-subtitle {
            font-size: 1.1rem;
            color: #333;
            margin-top: 4px;
        }

        .form-section {
            padding: 20px 30px;
            border-bottom: 2px solid #333;
            background-color: #fafafa;
        }

        .form-section h3 {
            margin-top: 0;
            font-size: 1.1rem;
            color: #333;
        }

        .form-group {
            margin-bottom: 12px;
            display: flex;
            flex-direction: column;
            gap: 5px;
        }

        .form-group input, .form-group select {
            padding: 8px;
            border: 1px solid #333;
            border-radius: 4px;
            font-size: 0.95rem;
        }

        .product-card {
            padding: 25px 30px;
            border-bottom: 2px solid #333;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        .product-info {
            display: flex;
            flex-direction: column;
            gap: 8px;
            font-size: 0.95rem;
            color: #333;
        }

        .product-name {
            font-weight: bold;
            font-size: 1.05rem;
            margin-bottom: 4px;
        }

        .btn {
            background-color: #ffffff;
            border: 2px solid #333;
            padding: 8px 18px;
            border-radius: 6px;
            font-size: 0.9rem;
            cursor: pointer;
            font-weight: 500;
            text-decoration: none;
            color: #333;
            display: inline-block;
            text-align: center;
        }

        .btn:hover {
            background-color: #f0f0f0;
        }

        .btn-danger {
            border-color: #a94442;
            color: #a94442;
        }

        .btn-danger:hover {
            background-color: #f2dede;
        }

        .btn-container {
            display: flex;
            gap: 8px;
            justify-content: center;
        }

        .footer {
            padding: 20px 30px;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }
    </style>
</head>
<body>

    <div class="container">
        <!-- Cabecera -->
        <div class="header">
            <div class="header-titles">
                <span class="header-title">PANADERÍA RICO PAN</span>
                <span class="header-subtitle">Catálogo de productos (Node.js + Express)</span>
            </div>
        </div>

        <!-- Formulario para Crear / Actualizar -->
        <div class="form-section">
            <h3 id="form-title">Agregar Nuevo Producto</h3>
            <form id="product-form">
                <input type="hidden" id="producto-id">
                <div class="form-group">
                    <label>Nombre:</label>
                    <input type="text" id="nombre" required>
                </div>
                <div class="form-group">
                    <label>Descripción:</label>
                    <input type="text" id="descripcion" required>
                </div>
                <div class="form-group">
                    <label>Precio:</label>
                    <input type="number" id="precio" required>
                </div>
                <div class="form-group">
                    <label>Estado:</label>
                    <select id="estado">
                        <option value="Activo">Activo</option>
                        <option value="Inactivo">Inactivo</option>
                    </select>
                </div>
                
                <button type="submit" class="btn" id="btn-submit">Registrar Producto</button>
            </form>
        </div>

        <!-- Listado dinámico (READ, UPDATE y DELETE) -->
        <div id="productos-container"></div>

        <!-- Pie de página -->
        <div class="footer">
            <button class="btn" onclick="cargarProductos()">Refrescar Catálogo</button>
            <span style="font-size: 0.85rem; color: #666;">Panadería Rico Pan</span>
        </div>
    </div>

    <script>
        const API_URL = '/api/productos';

        // READ: Cargar todos los productos
        async function cargarProductos() {
            try {
                const res = await fetch(API_URL);
                const productos = await res.json();
                const container = document.getElementById('productos-container');
                container.innerHTML = '';

                if (productos.length === 0) {
                    container.innerHTML = '<div style="padding: 25px 30px; text-align: center;">No hay productos registrados en la base de datos.</div>';
                    return;
                }

                productos.forEach((p) => {
                    container.innerHTML += `
                        <div class="product-card">
                            <div class="product-info">
                                <span class="product-name">${p.nombre} (ID: ${p.id})</span>
                                <span>Descripción: ${p.descripcion}</span>
                                <span>Precio: $${p.precio}</span>
                                <span>Estado: ${p.estado}</span>
                            </div>
                            <div>
                                <div class="btn-container">
                                    <button class="btn" onclick="prepararEditar(${p.id}, '${p.nombre}', '${p.descripcion}', ${p.precio}, '${p.estado}')">Editar</button>
                                    <button class="btn btn-danger" onclick="eliminarProducto(${p.id})">Eliminar</button>
                                </div>
                            </div>
                        </div>
                    `;
                });
            } catch (error) {
                console.error("Error al cargar productos:", error);
            }
        }

        // CREATE y UPDATE: Manejar envío del formulario
        document.getElementById('product-form').addEventListener('submit', async (e) => {
            e.preventDefault();
            const id = document.getElementById('producto-id').value;
            const data = {
                nombre: document.getElementById('nombre').value,
                descripcion: document.getElementById('descripcion').value,
                precio: document.getElementById('precio').value,
                estado: document.getElementById('estado').value
            };

            if (id) {
                // UPDATE
                await fetch(`${API_URL}/${id}`, {
                    method: 'PUT',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify(data)
                });
            } else {
                // CREATE
                await fetch(API_URL, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify(data)
                });
            }

            document.getElementById('product-form').reset();
            document.getElementById('producto-id').value = '';
            document.getElementById('form-title').innerText = 'Agregar Nuevo Producto';
            document.getElementById('btn-submit').innerText = 'Registrar Producto';
            cargarProductos();
        });

        // Cargar datos en el formulario para editar
        function prepararEditar(id, nombre, descripcion, precio, estado) {
            document.getElementById('producto-id').value = id;
            document.getElementById('nombre').value = nombre;
            document.getElementById('descripcion').value = descripcion;
            document.getElementById('precio').value = precio;
            document.getElementById('estado').value = estado;
            document.getElementById('form-title').innerText = `Actualizar Producto #${id}`;
            document.getElementById('btn-submit').innerText = 'Guardar Cambios';
        }

        // DELETE: Eliminar producto
        async function eliminarProducto(id) {
            if (confirm('¿Estás seguro de eliminar este producto?')) {
                await fetch(`${API_URL}/${id}`, { method: 'DELETE' });
                cargarProductos();
            }
        }

        // Cargar productos al abrir la página
        cargarProductos();
    </script>
</body>
</html>
