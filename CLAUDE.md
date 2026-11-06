# TexasExplo

App web de inventario técnico y operativa diaria de equipos de LiquidMedia (equipos ENG para La Sexta y Antena 3).
La usan los trabajadores y coordinadores para ver qué cámara está libre, quién se la ha llevado y a qué cobertura,
qué está en mantenimiento y qué vehículos hay en cada delegación.

- **Publicada en:** https://charlylopez-png.github.io/TexasExplo (GitHub Pages, rama `main`)
- **Responsable:** Carlos (coordinación técnica)

## Stack

- Un único archivo: `index.html` (HTML + CSS + JS en línea, sin build ni dependencias locales).
- Backend: **Supabase** (`supa = supabase.createClient(SB_URL, SB_KEY)`; helpers `sbGet`, etc.). La clave en el código es la pública (anon).
- Librerías por CDN: supabase-js 2, SheetJS (xlsx), jsPDF + autotable, Chart.js 4.
- Fuentes: Barlow / Barlow Condensed (Google Fonts).
- Datos de respaldo embebidos (`CAMERAS_RAW`, `VEHICLES_RAW`) y copia en `localStorage` (`tx_cameras`, `tx_vehicles`, `tx_cam_order`).

## Dominio

- **Delegaciones:** MADRID, PAÍS VASCO (Bilbao), GALICIA, VALENCIA.
- **Producciones:** `A3` (Antena 3) y `L6` (La Sexta).
- **Cámaras:** Sony PXW-X320, X180, Z280, Z300, Z200. Tipo `PROPIO` o `VISUAL` (alquiler, con `empresa`).
  - Estados: `operativa`, `mantenimiento`, `custodia` (apagada en casa de un operador), `baja`.
  - `code` es el identificador único (coincide con el QR físico). `numEquip` es el número visible (ej. X180.**2**).
  - Puede estar desplazada temporalmente a otra delegación (`temp_del`, `temp_prod`, `temp_desde`, `temp_motivo`).
  - Accesorios: Live-U (`liveu`, `liveu_unidad`), WifiCam/MiFi (`wificam`, `wificam_unidad`), cuaderno ATA (`ata`), `nota`.
- **Salidas (asignaciones):** una cámara sale con un operador (y opcional segundo operador) a una cobertura:
  `asignado_a`, `operador2_nombre`, `cobertura`, `horaSalida`, `en_viaje`. También hay reservas para mañana (`reservaManana`).
- **Flota de vehículos:** renting KINTO / VWR, modelos RAV4, Corolla, León ST, León E0; incidencias, km y citas de taller.
- Otros módulos: Operadores hoy (asistencia), Coberturas, estadísticas (equipos, operadores, coberturas, mantenimiento), Admin, Usuarios.
- Todos los usuarios con acceso tienen CRUD completo; algunas vistas de estadísticas son solo para `rol = 'admin'`.

## Diseño

- **Modo claro por defecto**, con interruptor claro/oscuro en la cabecera (`toggleTheme()`, se guarda en `localStorage.tx_theme`).
- Los colores van siempre por **variables CSS** definidas en `:root` (claro) y `:root[data-theme="dark"]` (oscuro).
  No meter colores fijos en estilos en línea: usar `var(--text)`, `var(--muted)`, `var(--ok)`, `var(--warn)`,
  `var(--purple)`, `var(--sky)`, `var(--hl-1…5)`, etc. Para Chart.js usar `resolveCssColor('var(--…)')`.
- Accesibilidad: texto mínimo 11–12 px, botones de al menos ~38–42 px de alto, foco visible, contraste AA en modo claro.
  Los estados de una ficha se indican con **color + texto** (franja `.cam-status`), nunca atenuando la ficha.
- La capa "MEJORAS 2026-11" al final del `<style>` contiene los ajustes de legibilidad y móvil.
- Debe funcionar bien en móvil: los trabajadores la usan desde el teléfono.

## Cómo trabajar en este repo

- Cambios quirúrgicos: el archivo es grande (~9.000 líneas). Editar solo lo necesario y conservar la lógica existente.
- Trabajar en una rama y abrir PR; `main` se publica directamente en GitHub Pages para todos los trabajadores.
- Antes de subir: comprobar que el JS no tiene errores de sintaxis y revisar en navegador escritorio y móvil, en claro y oscuro.
- Cambios de esquema en Supabase: inspeccionar primero las tablas y avisar antes de aplicar migraciones (afectan a datos reales).
