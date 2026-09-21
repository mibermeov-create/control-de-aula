# Bitácora de observación de aula | Eight Academy

Sistema institucional de registro, observación y seguimiento de visitas de aula para las secciones Kids, Primaria y Secundaria. Funciona en el navegador y en el celular, guarda todo en Firebase (Firestore) y se publica con Firebase Hosting.

## Qué incluye

| Archivo | Uso |
|---|---|
| `public/index.html` | La aplicación completa |
| `public/firebase-config.js` | Datos de conexión del proyecto de Firebase (se completan una vez) |
| `public/logo.png` | Logo institucional (ícono del navegador) |
| `firestore.rules` | Reglas de seguridad: solo cuentas autorizadas y permisos por rol |
| `firestore.indexes.json` | Excluye las fotografías de los índices |
| `firebase.json`, `.firebaserc` | Configuración de despliegue |

## Datos que se almacenan en Firestore

| Colección | Contenido |
|---|---|
| `visitas` | Un documento por visita, con ID `VA-AAAA-MM-DD-###` |
| `fotos` | Hasta 2 fotografías por visita, comprimidas |
| `contadores` | Numeración correlativa por día; evita IDs repetidos entre observadores |
| `config` | Nómina de docentes, observadores, materias y logo |
| `autorizados` | Correos con acceso y su rol |

Las fotografías se guardan comprimidas dentro de Firestore para que el proyecto funcione en el plan gratuito (Spark).

## Roles

| Rol | Puede |
|---|---|
| Administrador | Todo, incluida la gestión de usuarios, listas, logo e importación |
| Editor | Registrar, editar y eliminar visitas, y subir fotografías |
| Lector | Consultar registros, docentes y tablero |

## Puesta en marcha

### 1. Crear el proyecto de Firebase

1. Entra a https://console.firebase.google.com y crea un proyecto (por ejemplo, `bitacora-eight-academy`). Google Analytics no es necesario.
2. **Firestore Database** > Crear base de datos > modo producción. Elige la ubicación con cuidado porque no se puede cambiar después; para Ecuador conviene una de Estados Unidos, como `nam5`.
3. **Authentication** > Comenzar > Método de acceso > **Google** > Habilitar.
4. **Configuración del proyecto** (engranaje) > Tus apps > ícono web `</>` > registra la app. No marques Firebase Hosting en este paso. Copia el bloque `firebaseConfig`.

### 2. Completar tres archivos

- `public/firebase-config.js`: pega los valores de `firebaseConfig`.
- `.firebaserc`: reemplaza `REEMPLAZA_ID_DEL_PROYECTO` por el ID del proyecto.
- `firestore.rules`: reemplaza `REEMPLAZA_CORREO_ADMIN@gmail.com` por el correo de Google de quien administrará la bitácora. Puedes poner más de uno separado por comas.

### 3. Subir a GitHub

Crea un repositorio vacío en https://github.com/new (por ejemplo, `bitacora-eight-academy`, privado) y, desde esta carpeta:

```bash
git init
git add .
git commit -m "Bitácora de observación de aula"
git branch -M main
git remote add origin https://github.com/TU_USUARIO/bitacora-eight-academy.git
git push -u origin main
```

### 4. Desplegar

Requiere Node.js (https://nodejs.org).

```bash
npm install -g firebase-tools
firebase login
firebase deploy
```

Al terminar, la terminal muestra la URL pública:
`https://ID_DEL_PROYECTO.web.app`

### 5. Primer ingreso

1. Abre la URL e ingresa con el correo de administración que pusiste en `firestore.rules`. La cuenta se registra sola como Administrador.
2. En **Ajustes > Usuarios autorizados**, añade el correo de Google de cada persona del equipo con su rol.
3. En **Ajustes > Nómina**, carga los docentes, observadores y materias.

### 6. Despliegue automático desde GitHub (opcional)

```bash
firebase init hosting:github
```

Responde con el repositorio `TU_USUARIO/bitacora-eight-academy`, acepta el despliegue al hacer merge en `main` y deja vacío el comando de build. Desde entonces, cada cambio en `main` se publica solo.

## Migrar los registros de la versión en Claude

En la versión anterior: **Ajustes > Descargar respaldo JSON**. En esta versión, con una cuenta de Administrador: **Ajustes > Importar respaldo**. Se conservan todos los registros y sus IDs; las fotografías de la versión anterior no se pueden trasladar.

## Consideraciones

- **Sin conexión:** la aplicación guarda una copia local y sincroniza los cambios al recuperar la señal. Para asignar el ID de una visita nueva se necesita conexión.
- **Plan gratuito:** incluye 1 GiB en Firestore y 50 000 lecturas diarias, suficiente para un año lectivo con uso normal. Si el equipo crece mucho, revisa el consumo en la consola y, de ser necesario, activa el plan Blaze con una alerta de presupuesto.
- **Respaldo:** descarga el CSV o el JSON periódicamente desde Ajustes.
