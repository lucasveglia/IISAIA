# EduLayout - Editor de Layouts CMOS Educativo (GPDK045)

**EduLayout** es un editor de layouts de circuitos integrados CMOS orientado a la enseñanza (basado en las reglas del PDK **GPDK045 de 45nm**). Cuenta con una arquitectura cliente-servidor separada en **Frontend (React + Konva + Zustand)** y **Backend (Python FastAPI + SQLite)**, incluyendo verificación **DRC (Design Rule Check)**, extracción de netlist y validación **LVS (Layout Versus Schematic)**, además de control de versiones colaborativo con bloqueo exclusivo (**Checkout / Check-in**).

---

## ✨ Características Principales

1. **Autenticación de Usuarios con Contraseña**:
   - Inicio de sesión obligatorio al abrir el editor.
   - Incluye cuentas rápidas de prueba precargadas (`demo@edulayout.org`, `alumno1@fiuba.ar`, `alumno2@fiuba.ar` — contraseña por defecto: `1234`) y permite crear nuevas cuentas ingresando un email y contraseña.
   - Cambio rápido de cuenta desde el encabezado para demostrar el trabajo colaborativo entre distintos usuarios.

2. **Control de Versiones y Bloqueo por Checkout / Check-in**:
   - **Checkout exclusivo**: Cuando un usuario hace *Checkout* de un layout, queda bloqueado a su nombre (`checked_out_by`).
   - **Protección contra escritura concurrente**: Si otro usuario abre ese mismo layout, lo verá en **Modo Solo Lectura** y el backend rechazará cualquier intento de modificación o guardado (`HTTP 403` / `HTTP 409`) hasta que el usuario dueño realice el *Check-in*.
   - **Historial con comentarios**: Cada *Check-in* o guardado genera una nueva versión numerada incrementalmente con un comentario descriptivo de los cambios realizados.

3. **Verificación DRC (Design Rule Check)**:
   - Validación geométrica sobre el proceso GPDK045 (`Nwell`, `Oxide`, `Poly`, `Nimp`, `Pimp`, `Cont`, `Metal1`, `Via1`, `Metal2`).
   - Resaltado visual de violaciones directamente sobre el canvas.

4. **Extracción de Netlist y Validación LVS (Layout Versus Schematic)**:
   - Extracción automática de nodos eléctricos mediante conectividad geométrica (BFS) y detección de transistores **NMOS** y **PMOS** por intersección de capas (`Poly` + `Oxide` + implantes/pozo).
   - Comparación topológica contra **5 celdas esquemáticas de referencia**:
     - `inv_cmos`: Inversor CMOS (1 PMOS + 1 NMOS).
     - `nand_cmos`: Compuerta NAND de 2 entradas (2 PMOS en paralelo + 2 NMOS en serie).
     - `nor_cmos`: Compuerta NOR de 2 entradas (2 PMOS en serie + 2 NMOS en paralelo).
     - `and_cmos`: Compuerta AND de 2 entradas (etapa NAND2 + etapa inversora).
     - `or_cmos`: Compuerta OR de 2 entradas (etapa NOR2 + etapa inversora).

5. **Librería de Celdas e Instanciación**:
   - Celdas estándar precargadas (`nmos_min`, `pmos_min`, `inv_cmos`) y reutilización de diseños guardados como sub-instancias en el canvas.

---

## 🚀 Cómo empezar

El proyecto está configurado para arrancar fácilmente en cualquier computadora. La base de datos SQLite (`backend/simulator.db`) se autogenera e inicializa sola al levantar el backend.

### 1. Iniciar el Backend (FastAPI + SQLite)

Abre una terminal en la carpeta `tp-final/backend`, instala las dependencias de Python y ejecuta el servidor:

```bash
cd backend
pip install -r requirements.txt
python main.py
```

* El servidor quedará corriendo en **`http://localhost:3001`**.
* Al iniciar, [`main.py`](backend/main.py) ejecuta [`database.sql`](backend/database.sql), crea o migra automáticamente el archivo `simulator.db`, inserta los usuarios de prueba y precarga las celdas estándar del PDK GPDK045.
* Puedes consultar la documentación interactiva de la API (Swagger UI) en `http://localhost:3001/docs`.

### 2. Iniciar el Frontend (React + Vite)

Abre una **nueva terminal** en la carpeta `tp-final/frontend` y ejecuta:

```bash
cd frontend
npm install
npm run dev
```

* Abre en tu navegador el enlace local que muestra la consola (generalmente **`http://localhost:5173`**).

---

## 🗂️ Estructura del Proyecto

```text
tp-final/
├── backend/
│   ├── main.py            # Punto de entrada FastAPI, inicialización y migración de DB
│   ├── handler.py         # Rutas de la API REST (usuarios, login, layouts, checkout, versiones)
│   ├── database.sql       # Esquema relacional SQLite y datos semilla iniciales
│   ├── openapi.yaml       # Especificación OpenAPI 3.0 de referencia
│   ├── requirements.txt   # Dependencias de Python (FastAPI, Uvicorn, Pydantic)
│   └── simulator.db       # Base de datos SQLite (autogenerada)
└── frontend/
    ├── src/
    │   ├── components/    # Componentes visuales (Auth, Editor/Canvas, Header, DRC, LVS, Library, LSW, Toolbar)
    │   ├── data/          # Capas GPDK045, reglas DRC, celdas estándar y netlists esquemáticas LVS
    │   ├── pages/         # Vista principal del editor
    │   ├── store/         # Estado global con Zustand (useAuthStore, useLayoutStore, useToolStore)
    │   └── utils/         # Motores de verificación (drcEngine, netlistExtractor, lvsEngine)
    └── package.json       # Dependencias de React, Konva, Zustand y Vite
```

---

## 🔌 Resumen de Endpoints de la API (`backend/handler.py`)

| Método | Ruta | Descripción |
| :--- | :--- | :--- |
| `GET` | `/api/health` | Estado del servidor y PDK activo |
| `GET` | `/api/users` | Lista los usuarios registrados |
| `POST` | `/api/login` | Inicia sesión con `email` y `password` (o registra si es nuevo) |
| `GET` | `/api/layouts` | Lista todos los layouts con su estado de checkout y autor del bloqueo |
| `POST` | `/api/layouts` | Crea un nuevo layout en la base de datos |
| `DELETE` | `/api/layouts/{id}` | Elimina un layout y su historial de versiones en cascada |
| `POST` | `/api/layouts/{id}/checkout` | Bloquea exclusivamente el layout para el `user_id` actual (409 si otro lo tiene) |
| `GET` | `/api/layouts/{id}/versions` | Obtiene el historial de versiones de un layout (incluyendo comentarios) |
| `POST` | `/api/layouts/{id}/versions` | **Check-in**: guarda una nueva versión con comentario y libera el bloqueo (403 si está bloqueado por otro usuario) |
