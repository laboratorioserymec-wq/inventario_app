# Inventario UTR (Flutter + Google Sheets vía Apps Script)

La app lee y escribe directamente en tu hoja `carrots` y **funciona sin conexión**:
todo lo que captures se guarda primero en el teléfono y se sube solo a la hoja cuando
hay internet (reintenta cada 20 s, al abrir la app y al deslizar la lista hacia abajo).
Los campos del formulario salen de los **encabezados de la fila 3**, así que si agregas
o renombras columnas en la hoja, la app se adapta sola (no toca la col. A ni la B).

## 1. Apps Script
1. En tu proyecto UT_MECR reemplaza todo `Código.gs` por el archivo `Codigo.gs` de este paquete
   (revisa que `SPREADSHEET_ID` sea el tuyo).
2. Cambia `API_TOKEN` por una clave tuya.
3. Ejecuta la función **`autorizar`** una vez y acepta los permisos (hoja + Drive para fotos).
4. **Implementar > Nueva implementación > Aplicación web**
   - Ejecutar como: **Yo**
   - Quién tiene acceso: **Cualquier persona** (la protege el token)
5. Copia la URL que termina en `/exec`.
> Si cambias el script después: Implementar > Administrar implementaciones > editar > **Nueva versión**.

## 2. App
1. `lib/config.dart`: pega la URL y el mismo token.
2. `flutter create inventario_app`, copia `pubspec.yaml` y `lib/` encima, `flutter pub get`.
3. **Android**: en `android/app/src/main/AndroidManifest.xml` agrega dentro de `<manifest>`:
   `<uses-permission android:name="android.permission.INTERNET"/>`
   (sin esto el APK release no tiene internet).
4. `flutter run` o `flutter build apk --release`.

## Fotos (opcional)
Agrega una columna con encabezado **FOTOS** en la fila 3. Las fotos se suben a la carpeta
"Inventario UTR - Fotos" de tu Drive y los enlaces se guardan en esa celda.
Sin esa columna, la sección de fotos no aparece.

## Modo sin conexión
- La lista de la hoja se guarda en el teléfono cada vez que se sincroniza. Descárgala **una vez con internet**
  antes de salir a campo.
- Al guardar un registro sin señal queda en cola (icono de nube naranja). El banner amarillo de arriba
  muestra cuántos faltan; tócalo para ver el detalle, reintentar o descartar.
- Si el servidor rechaza un cambio (p. ej. token incorrecto o fila borrada), queda en rojo con el motivo y
  no se pierde hasta que lo descartes.
- Si dos personas editan la misma celda, gana el último que sincroniza.

## Uso
- Lista con buscador y filtro **Pendientes** (filas con alguna columna vacía).
- Tocar un registro para llenar/editar sus columnas. **Nuevo** crea una fila al final.
- Los campos con pocos valores repetidos (marca, ubicación...) sugieren lo ya capturado.

## Novedades
- **Eliminar**: dentro de un registro, icono de bote de basura (o botón rojo). Borra la fila de la hoja; sin internet queda en cola.
- **Fotos**: botones *Tomar foto* (cámara; en Windows usa la cámara integrada) y *Subir foto*. Si la hoja no tiene columna FOTOS, el script la crea al final.
- **Exportar a Excel**: icono de tabla en la barra superior. Exporta todos los registros; si hay búsqueda o filtro activo pregunta si quieres todos o solo los visibles.

## Generar el APK (Windows)
Doble clic en `crear_apk.bat` (requiere Flutter y Android Studio instalados).
Crea la carpeta `android/`, aplica el permiso de internet y compila.
El APK queda en `build\app\outputs\flutter-apk\app-release.apk`.

Si el build falla con "compile against version 36 or later", `crear_apk.bat` ya reemplaza `android\build.gradle.kts` por la versión de `android_manifest\build.gradle.kts` que fuerza compileSdk 36.

## Cambios recientes
- **Pantalla principal = registrar un artículo nuevo.** El botón "Registros" (arriba) abre la lista de lo ya registrado (buscar, editar, eliminar, exportar a Excel).
- **Buscador:** busca en todas las columnas (número, salón, banco, marca, notas...), sin importar acentos ni mayúsculas; varias palabras se buscan todas.
- **Notas:** columna NOTAS (el script la crea sola en la hoja). Es opcional: no cuenta para "Incompletos".
- **Fotos:** las que tomas en el teléfono se conservan siempre en él; las demás se bajan de Drive a través del script (no necesitan ser públicas).

## Ícono y APK
- El ícono (letras UT) está en `assets/icon/`. Windows ya lo trae en `windows/runner/resources/app_icon.ico`.
- Para generar el APK: doble clic / `.\crear_apk.bat` (necesita Flutter y Android Studio con el Android SDK).
