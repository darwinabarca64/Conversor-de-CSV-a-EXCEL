# Conversor de CSV a Excel 📊✨

[![Licencia](https://img.shields.io/badge/license-Apache--2.0-green.svg)](./LICENSE)
[![Platform](https://img.shields.io/badge/platform-Browser-blue.svg)](#)
[![Stack](https://img.shields.io/badge/stack-ES6%20%7C%20Vanilla%20JS%20%7C%20CSS-orange.svg)](#)

Una herramienta profesional, moderna y ultraligera escrita en Javascript puro (Vanilla JS) para convertir archivos **CSV a hojas de cálculo de Excel (.xlsx)** de manera instantánea y directa en el cliente. 

---

## 🎯 Objetivo del Proyecto

El objetivo principal de este proyecto es ofrecer una solución de conversión rápida y segura que procese archivos de datos tabulares (CSV) en hojas de cálculo estilizadas de Microsoft Excel, ofreciendo una experiencia similar a **Power Query** para filtrar y editar los datos antes de exportarlos.

🔒 **Privacidad Total (100% Client-Side):** El procesamiento se realiza localmente en la memoria del navegador utilizando APIs web nativas. Tus datos nunca se suben a ningún servidor externo.

---

## 🚀 Características Clave

*   **Interfaz Microsoft 365 / Excel:** Diseñado con los lineamientos de interfaz de Microsoft Office, incluyendo zonas de arrastrar y soltar (Drag & Drop) interactivas.
*   **Editor de Consultas (Power Query Flotante):**
    *   **Edición de Celdas:** Modifica el contenido de cualquier fila haciendo doble clic directo sobre la celda.
    *   **Renombrado de Columnas:** Cambia las cabeceras de tus columnas al instante.
    *   **Selección de Columnas:** Activa o desactiva columnas enteras mediante checkboxes; las columnas deshabilitadas se omiten en la exportación.
    *   **Filtrado de Filas:** Excluye filas manualmente o utiliza el buscador dinámico en tiempo real.
*   **Inferencia Inteligente de Tipos:** Detecta automáticamente si las columnas son números enteros, flotantes, porcentajes, fechas, horas o texto simple.
*   **Fila de Resumen Automática:** Calcula automáticamente la sumatoria total para números y el promedio para porcentajes al final de la tabla Excel.
*   **Ajuste Óptimo de Ancho:** Calcula visualmente el ancho de columna ideal en base al contenido utilizando `OffscreenCanvas` de forma nativa.
*   **Sin Dependencias Pesadas:** Libre de frameworks pesados o librerías externas de compresión complejas.

---

## ⚙️ Guía de Arranque y Ejecución Local

Dado que la aplicación se compone de módulos ES6 nativos (`type="module"`), los navegadores bloquean el uso directo a través de archivos locales (`file:///`) por políticas de CORS. Requiere ser servido desde un servidor web local simple.

### Paso 1: Clonar el proyecto e ingresar al directorio
```bash
git clone https://github.com/darwinabarca64/Conversor-de-CSV-a-EXCEL.git
cd Conversor-de-CSV-a-EXCEL
```

### Paso 2: Iniciar el servidor local (Recomendado sin caché)
Ejecuta la herramienta `http-server` de Node.js en la raíz del proyecto desactivando la caché para evitar almacenar en memoria temporal cambios de desarrollo:
```bash
npx http-server -c-1 -p 8080
```

### Paso 3: Probar en el Navegador
Abre tu navegador de preferencia y accede a:
👉 **[http://localhost:8080/_demo/demo.html](http://localhost:8080/_demo/demo.html)**

---

## 🛠️ Cómo Utilizar la Aplicación

1.  **Cargar el archivo:** Arrastra tu archivo `.csv` a la zona central o haz clic en ella para buscar en tu explorador de archivos.
2.  **Configurar Parámetros:** En la parte inferior, puedes elegir el separador de columnas (Coma, Punto y coma, Tabulación o detección automática), el charset de lectura y si deseas incluir la fila de totales en el Excel.
3.  **Refinar en Power Query:** Haz clic en **"Configurar y Editar Datos (Power Query)"** para abrir el modal interactivo. Modifica celdas, renombra columnas o descarta datos que no te interesen y haz clic en *Aplicar Cambios*.
4.  **Generar y Descargar:** Haz clic en **"Convertir a Excel (.xlsx)"** para descargar de forma automática el archivo procesado.

---

## 📖 Documentación de la API del Motor

Si deseas integrar el motor en otros desarrollos, puedes importar e invocar directamente la clase controladora:

```javascript
import { Csv2Xlsx } from './src/Csv2Xlsx.js';

// Invocación pasando un objeto File del navegador
const fileInput = document.getElementById('csv-input');
const file = fileInput.files[0];

const metaData = {
    title: 'Mi Reporte',
    creator: 'Usuario',
    company: 'Mi Empresa'
};

// Generar enlace a la descarga
const aTag = await Csv2Xlsx.convertCsv(file, 'reporte_final', metaData);
document.body.appendChild(aTag);
aTag.click();
```

---

## 📄 Licencia y Créditos

Este proyecto está bajo la Licencia **Apache 2.0**. Consulta el archivo [LICENSE](./LICENSE) para más detalles.

### Librerías y Recursos Utilizados:

*   [Nullxlsx] (https://github.com/netas-ch/csv2xlsx) (JavaScript csv to spreadsheet (xlsx) converter) - Copyright © 2024 Lukas Buchs, netas.ch, Apache License, Version 2.0.
*   [Nullxlsx](https://github.com/Neovici/nullxlsx) (Compresión ZIP y generación XML ligera) - Copyright © 2020 Neovici (Apache 2.0).
*   [csvjson-csv2json](https://github.com/FlatFilers/csvjson-csv2json) (Parser léxico PEG.js para CSV) - Copyright © 2019 Martin Drapeau (MIT).
