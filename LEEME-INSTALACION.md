# UTH Tracker — Registro y analizador estadístico

## Qué se ha construido

Aplicación web privada, sin servicios externos, con registro local de sesiones y rondas de Ultimate Texas Hold'em. Incluye cartas de 6 jugadores + crupier, 5 comunitarias, baraja roja/azul, decisiones por fase, resultado manual o comparación automática, ranking de manos de 7 cartas, análisis estadístico por ronda, historial editable, evolución de bankroll, exportación CSV/JSON e importación JSON.

**Las rondas se crean de inmediato como borradores y se guardan automáticamente mientras se editan.** Nunca inventa cartas faltantes ni ganancias. Si faltan cartas del crupier o del board, no calcula el ganador de esa mano.

## Instalación duradera en iPhone

1. Extrae el ZIP en tu ordenador. Encontrarás `index.html`, `app.js`, `style.css`, manifest, iconos y `sw.js`.
2. Publica **el contenido de la carpeta junto** en una ubicación web **HTTPS** de tu propiedad, por ejemplo mediante una plataforma de alojamiento de sitios estáticos (Netlify Drop, GitHub Pages o Cloudflare Pages). Netlify Drop permite desplegar una carpeta estática mediante arrastrar y soltar desde un ordenador.
3. Abre la dirección HTTPS obtenida en **Safari en el iPhone**.
4. Toca **Compartir → Añadir a pantalla de inicio** y ábrela desde el icono.
5. Crea una sesión y registra una ronda de prueba. Cierra y vuelve a abrir la app para verificar que sigue disponible.
6. En **Datos → Exportar y copias**, exporta una copia **JSON completa** y guárdala en la app Archivos / iCloud Drive. Repite la operación después de cada sesión. Para recuperar un respaldo usa **Importar JSON** (sustituye el conjunto de datos existente tras confirmación).

La aplicación puede abrirse sin conexión tras una primera carga desde HTTPS gracias al service worker. No requiere servicios de pago ni un servidor de datos. Todo se guarda en el almacenamiento del navegador dentro de ese origen (IndexedDB). No hay sincronización entre dispositivos ni un backup remoto automático.

## Archivo HTML único

Además se entrega `UTH-Tracker-un-archivo.html` para probar la interfaz en un ordenador sin desplegarla. **En iOS abrir archivos HTML locales desde Archivos no garantiza almacenamiento persistente**; no confíes en esta modalidad para sesiones reales. La versión publicada por HTTPS es la recomendada.

## Registro recomendado de una ronda

- Pulsa **+ Nueva ronda**: se almacena un borrador automáticamente.
- Marca la baraja roja o azul si la has identificado. Si no, déjala en “?”.
- Elige una carta en **dos pulsaciones** con el teclado inferior: rango y palo. Tras elegirla salta a la siguiente posición vacía.
- Se priorizan las dos cartas propias, cinco comunitarias, dos del crupier, y al final las de otros jugadores. Puedes tocar cualquier campo para editarlo.
- Los campos de jugadores desconocidos se dejan vacíos. En el desplegable puedes registrar acciones preflop (check/3x/4x), flop (check/2x), final (1x/fold) y resultado neto.
- Pulsa **Guardar y siguiente** para iniciar otro borrador. Para completar rondas anteriores ve a **Rondas → Editar**.

## Qué hacen los tests estadísticos

- Frecuencias de cartas observadas: descriptivas. Varias cartas de una misma ronda están relacionadas, por lo que no se tratan todas como realizaciones independientes.
- Para contraste de rangos/palos: **Flop 1**, una posición de carta fija por ronda.
- Para contraste de boards: cinco comunitarias conocidas, un resultado por ronda.
- Para frecuencias pequeñas: prueba binomial exacta (indicadores de board) o simulación multinomial/permutación con p-valor Monte Carlo (tablas multicategoría); χ² cuando los esperados son ≥ 5.
- Intervalos de Wilson al 95 % para los indicadores de board; corrección de Holm para contrastes simultáneos.
- Comparación roja/azul: test de Fisher para indicadores binarios; χ² o permutación para rangos/palos fijos.
- Los p-valores no demuestran manipulación ni permiten pronosticar cartas. Requieren muestreo razonablemente no selectivo y dependencia controlada entre rondas.

## Limitaciones importantes

- Los cambios se guardan en **el mismo dispositivo y origen web**. Limpiar datos de Safari, usar navegación privada o desinstalar/reconfigurar puede causar pérdida. Las copias JSON externas son indispensables.
- No hay alojamiento, publicación o URL pública incluidos en el archivo ZIP: necesitas colocarlo en HTTPS para obtener la experiencia instalable en iOS.
- No hay reconocimiento óptico de las cartas ni conexión con el casino.
- No se estima un pago casino automáticamente: diferentes casinos pueden tener tablas complementarias y apuestas opcionales distintas. La columna de ganancias/pérdidas se introduce a mano. La comparación de manos sí es automática cuando hay siete cartas.
- “Call” es una opción excepcional configurable; en las reglas estándar de Ultimate Texas Hold'em no existe como acción de apuesta propia.
