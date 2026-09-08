# Almacén 3D Inteligente (Vite + TypeScript)

Reescritura desde cero del prototipo original en HTML/JS suelto, con arquitectura
modular, tipado estricto y mejoras al algoritmo de optimización.

## Cómo abrir en VS Code

1. Descomprime el ZIP y abre la carpeta `almacen-3d-ts` en VS Code
   (`Archivo > Abrir carpeta...`).
2. Abre una terminal integrada (``Ctrl+ñ`` / ``Ctrl+` ``) e instala dependencias:
   ```bash
   npm install
   ```
3. Levanta el servidor de desarrollo (con recarga en caliente):
   ```bash
   npm run dev
   ```
4. Abre la URL que muestra la terminal (normalmente `http://localhost:5173`).

Extensión recomendada en VS Code: **ESLint** o al menos el soporte de TypeScript
integrado (ya viene activado por defecto al abrir un archivo `.ts`).

## Estructura del proyecto

```
src/
  types.ts       Modelos de datos (Scenario, BoxDef, Placement, etc.)
  state.ts       Store central (única fuente de verdad + notificación de cambios)
  geometry.ts    Colisiones AABB, pasillo, obstáculos, índice espacial
  optimizer.ts   Algoritmo de ubicación de cajas (estanterías -> piso)
  storage.ts     localStorage, exportar/importar JSON, validación de escenario
  scene.ts       Escena Three.js: cámara, luces, render, selección por clic
  ui.ts          Conecta el DOM (inputs/botones) con el store
  main.ts        Punto de entrada
  style.css      Estilos (idénticos al prototipo original)
index.html       Marcado de la interfaz, sin JS inline
```

## Qué cambió respecto al prototipo original

- **Arquitectura modular**: cada responsabilidad (estado, geometría, optimización,
  almacenamiento, render 3D, UI) vive en su propio módulo en vez de una única
  función autoejecutada con variables globales.
- **Tipado estricto (TypeScript)**: los datos de caja, estante, obstáculo y
  colocación son interfaces explícitas; errores de forma se detectan al compilar,
  no en producción.
- **Rotación de cajas**: si "Permitir rotación 90°" está activo, el optimizador
  prueba también la orientación girada de la caja al buscar dónde ubicarla,
  mejorando el aprovechamiento del espacio.
- **Índice espacial (hash de celdas)**: las pruebas de colisión ya no comparan
  cada posición candidata contra *todas* las cajas colocadas (O(n²)); se agrupan
  en celdas y solo se comparan las cercanas, lo que acelera la optimización con
  muchas cajas.
- **Validación de escenario al importar/cargar**: un JSON incompleto o corrupto
  da un mensaje de error específico en vez de romper el render silenciosamente.
- **Sin JS inline en el HTML**: los botones usan `addEventListener` en vez de
  `onclick="..."`, más fácil de mantener y depurar.

## Próximos pasos sugeridos

- Permitir varios pasillos/zonas de circulación en vez de uno solo.
- Mostrar una barra de progreso durante `optimize()` para almacenes grandes.
- Exportar la vista 3D como imagen (captura del `<canvas>`).
- Separar el algoritmo en un *Web Worker* si el número de cajas crece mucho,
  para no bloquear el hilo principal de la interfaz.

## Nota

El algoritmo de optimización sigue siendo un prototipo heurístico. Antes de
usarlo para un almacén real, deben validarse cargas estructurales, seguridad,
circulación y normativa correspondiente.
