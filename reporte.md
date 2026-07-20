# Reporte Técnico de Arquitectura - csv2xlsx

Este reporte detalla el análisis del motor de conversión de CSV a XLSX y define los lineamientos para la integración de una interfaz web de usuario (Frontend).

---

## 1. Estructura de Archivos del Espacio de Trabajo

La lógica del proyecto está dividida de forma modular. Así es como se conectan los archivos clave para realizar la conversión:

*   **Punto de Entrada ([Csv2Xlsx.js](file:///d:/programs/cuidar_el_alma/csv2xlsx-main/src/Csv2Xlsx.js)):** Coordina el flujo completo. Descarga el CSV, decodifica el texto binario según el *charset*, importa los submódulos dinámicamente y expone el resultado (un enlace o una URL).
*   **Procesador de Datos ([CsvProcessing.js](file:///d:/programs/cuidar_el_alma/csv2xlsx-main/src/csv/CsvProcessing.js)):** Recibe el texto plano del CSV. Se encarga de inferir el tipo de dato de cada columna, normalizar los nombres de los encabezados (haciéndolos únicos), convertir los valores a sus equivalentes en JS (fechas, floats, enteros) y calcular el ancho visual óptimo de cada celda en Excel.
*   **Parser de CSV ([Csv2Json.js](file:///d:/programs/cuidar_el_alma/csv2xlsx-main/src/csv/Csv2Json.js)):** Es un analizador léxico robusto (basado en PEG.js) escrito por Martin Drapeau. Su única tarea es procesar el texto plano del CSV y generar una matriz bidimensional (Array de Arrays), manejando correctamente las comillas y los saltos de línea dentro de las celdas.
*   **Generador XLSX ([Spreadsheet.js](file:///d:/programs/cuidar_el_alma/csv2xlsx-main/src/xl/Spreadsheet.js)):** Toma los datos procesados y crea la estructura interna de un archivo OpenXML (.xlsx). Instancia y empaqueta las hojas de cálculo, metadatos, tablas y estilos en formato XML que requiere Excel.
*   **Empaquetador ZIP ([NullZipArchive.js](file:///d:/programs/cuidar_el_alma/csv2xlsx-main/src/zip/NullZipArchive.js)):** Un archivador ZIP ultra ligero implementado en Vanilla JS que escribe los archivos binarios del Excel estructurado en memoria y genera un objeto binario (`Blob`).
*   **Demo de Cliente ([demo.html](file:///d:/programs/cuidar_el_alma/csv2xlsx-main/_demo/demo.html)):** Importa dinámicamente la clase principal `Csv2Xlsx` para consumir el archivo estático [demodata.csv](file:///d:/programs/cuidar_el_alma/csv2xlsx-main/_demo/demodata.csv), demostrando su uso práctico.

---

## 2. Stack Técnico Actual

El motor destaca por ser **100% de ejecución del lado del cliente (Client-Side)** y no requiere ningún backend ni dependencias externas de NPM pesadas. Utiliza:

1.  **Javascript Moderno (ES6+):** Uso exhaustivo de módulos de ES6 (`import`/`export`), clases, métodos estáticos y campos privados de clase (`#zip`, `#rows`).
2.  **Web APIs Nativas:**
    *   `fetch` para descargar el archivo CSV.
    *   `TextDecoder` para decodificar los datos crudos a texto plano soportando múltiples *encodings* (por ejemplo, `Windows-1252` o `UTF-8`).
    *   `Blob` (Binary Large Object) y `URL.createObjectURL` para almacenar la salida binaria del ZIP en la memoria del navegador y generar una URL de descarga local instantánea.
3.  **Parsers y Generadores Custom:**
    *   **Parser CSV:** Un transpilador ligero PEG.js en [Csv2Json.js](file:///d:/programs/cuidar_el_alma/csv2xlsx-main/src/csv/Csv2Json.js).
    *   **Compresor ZIP:** Algoritmo propio simplificado en [NullZipArchive.js](file:///d:/programs/cuidar_el_alma/csv2xlsx-main/src/zip/NullZipArchive.js) para evitar el uso de dependencias externas como `JSZip`.

---

## 3. Flujo de Datos del Motor (`Csv2Xlsx.convertCsv`)

### Parámetros del Método
El método estático `Csv2Xlsx.convertCsv` acepta los siguientes parámetros:
*   `csvUrl` *(String - Obligatorio)*: La dirección URL desde donde descargar el CSV.
*   `filename` *(String - Opcional)*: Nombre del archivo de salida (por defecto es `'document.xlsx'`).
*   `metaData` *(Object - Opcional)*: Diccionario con llaves como `title`, `subject`, `creator`, `company`, `created` y `modified`.
*   `charset` *(String - Opcional)*: Codificación del archivo CSV (por defecto `'Windows-1252'`).
*   `updateFn` *(Function - Opcional)*: Callback de estado (`download` ➡️ `processing` ➡️ `finished` o `fail`).
*   `returnAsLink` *(Boolean - Opcional)*: Si es `true` (por defecto), devuelve un elemento HTML `<a>` listo para hacer clic; si es `false`, devuelve la URL del Blob (`blob:...`).
*   `csvSeparator` *(String - Opcional)*: Fuerza un delimitador (`,`, `;`, `\t`). Si es `null`, se autodetecta.
*   `formatCodes` *(Object - Opcional)*: Códigos de formato Excel personalizados para fechas, números y porcentajes.
*   `showSummaryRow` *(Boolean - Opcional)*: Muestra una fila inferior de suma/promedio en Excel (por defecto `true`).

### Detección Interna
*   **Separadores:** En `Csv2Json.js#detectSeparator`, el motor cuenta las ocurrencias de comas (`,`), puntos y comas (`;`) y tabulaciones (`\t`) en el texto del CSV. El caracter que más se repite es seleccionado como el delimitador del archivo.
*   **Tipos de Datos:** El procesador analiza celda por celda cada columna (excluyendo el encabezado en la fila 0). Mediante expresiones regulares en [Utils.js](file:///d:/programs/cuidar_el_alma/csv2xlsx-main/src/util/Utils.js):
    *   Si detecta que todas las celdas de una columna son enteras o flotantes, define el tipo en Excel como numérico/float y aplica los formatos decimales correspondientes.
    *   Si detecta fechas (`YYYY-MM-DD`) o fecha-hora, las parsea a objetos `Date` de Javascript.
    *   Si encuentra un patrón con símbolo `%` (ej: `20%`), lo convierte internamente a decimal (`0.2`) y le asigna formato de porcentaje en Excel.
    *   Si una sola celda de la columna no encaja en estos patrones o contiene letras, la columna entera se degrada a tipo `text`.

---

## 4. Puntos de Anclaje para el Frontend (Carga de Archivos Locales)

Actualmente, el motor requiere obligatoriamente una URL para hacer un `fetch`:
```javascript
let rep = await fetch(csvUrl, { cache: 'no-store' });
// ...
const buf = await rep.arrayBuffer();
const decoder = new TextDecoder(charset);
const rawCsv = decoder.decode(buf);
```

### Cómo inyectar un CSV físico sin romper el código original
Para permitir que el usuario suba un archivo local (usando un `<input type="file" accept=".csv">`), podemos añadir un método alternativo o ampliar el existente. 

El punto de anclaje perfecto para interceptar el flujo está justo antes de crear la instancia de `CsvProcessing`. El motor solo necesita el String crudo (`rawCsv`).

#### Propuesta de Refactorización / Integración:
Podemos modificar `Csv2Xlsx.js` para que el primer parámetro (`csvUrl`) admita tanto un `String` (URL) como un objeto `File` (del navegador):

```javascript
// Si csvUrl es un objeto File (de un input HTML5)
let rawCsv;
if (csvUrl instanceof File) {
    // Leemos el archivo directamente como ArrayBuffer o Texto
    const buf = await csvUrl.arrayBuffer();
    const decoder = new TextDecoder(charset);
    rawCsv = decoder.decode(buf);
    
    // Si no se especificó un nombre de archivo, tomamos el del input físico
    if (!filename) {
        filename = csvUrl.name.replace(/\.csv$/i, '');
    }
} else {
    // Ejecutamos la petición fetch original para URL externa
    let rep = await fetch(csvUrl, { cache: 'no-store' });
    // ... decodificación original ...
}
```

De esta forma, en el Frontend de la nueva interfaz web simplemente capturarías el archivo y llamarías al motor así:

```javascript
const fileInput = document.getElementById('my-csv-file');
const file = fileInput.files[0];

// Invocación directa del motor pasando el archivo físico
const aTag = await Csv2Xlsx.convertCsv(file, 'mi_reporte', metaData);
document.body.appendChild(aTag);
aTag.click();
```
