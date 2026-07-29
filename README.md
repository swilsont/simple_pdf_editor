# Editor de PDF

**Quince herramientas para PDF que caben en un archivo.** Unir, dividir, reordenar, comprimir, firmar, poner contraseña. Ninguna sube tu documento a ninguna parte.

Es la idea de [iLovePDF](https://www.ilovepdf.com/) sin el servidor: los mismos trabajos de todos los días, pero hechos dentro de tu navegador. No hay que crear cuenta, ni aceptar que tu contrato pase por un equipo ajeno, ni esperar la cola del plan gratuito.

---

## Lo que lo hace distinto

**Un solo archivo HTML.** Lo descargas, le das doble clic y funciona. No hay `npm install`, ni build, ni servidor, ni instalador.

**637 KB** (248 KB comprimidos con gzip). De eso, 513 KB son pdf-lib incrustado: el 82% del archivo es la librería que hace el trabajo pesado, y viene adentro justamente para que once de las quince herramientas funcionen sin conexión.

**Sin frameworks.** Sin React, sin Vue, sin Bootstrap, sin jQuery. Todo el JavaScript vive en un cierre y no publica nada al ámbito global.

**Tu documento no se sube a ninguna parte.** Todo el procesamiento ocurre en tu equipo. No hay servidor que lo reciba, ni analítica, ni rastreadores. Lo único que sale a internet son las tipografías y, si usas ciertas herramientas, los motores que se descargan bajo demanda: viajan hacia ti, nunca al revés.

---

## Las quince herramientas

| Herramienta | Qué hace |
|---|---|
| **Unir PDFs** | Junta varios archivos en uno, en el orden que arrastres |
| **Dividir un PDF** | Por rangos, cada N páginas, o una por archivo |
| **Ordenar, rotar y eliminar páginas** | Las tres cosas a la vez, sobre las miniaturas |
| **Varias páginas por hoja** | 2, 4, 6, 8, 9 o 16 páginas por hoja, para gastar menos papel |
| **Cambiar tamaño y márgenes** | Pasa el documento a otro papel, o hazle sitio al pliegue |
| **Imágenes → PDF** | Fotos o capturas, con el ppp de cada una a la vista |
| **PDF → imágenes** | PNG, JPG o WEBP, a la resolución que elijas |
| **Numerar, encabezado y pie** | Con vista previa sobre las miniaturas |
| **Marca de agua** | Texto, imagen o las dos, en mosaico o centrada |
| **Firmar** | Dibujada, subida o escrita; se coloca, gira y escala a mano |
| **Rellenar formulario** | Completa los campos y, si quieres, los deja fijos |
| **Comprimir** | Sin perder calidad, o página por página bajando las imágenes |
| **Metadatos** | Mira qué revela tu PDF sobre ti, y modifícalo |
| **Proteger con contraseña** | AES-256, con permisos de impresión, copia y edición |
| **Quitar contraseña** | Con la clave correcta, deja el archivo abierto para siempre |

---

## Cómo usarlo

**Pruébalo sin instalar nada:** [swilsont.github.io/simple_pdf_editor](https://swilsont.github.io/simple_pdf_editor/)

**O quédatelo:** [descarga `editor-pdf.min.html`](https://raw.githubusercontent.com/swilsont/simple_pdf_editor/main/editor-pdf.min.html), dale doble clic y arrastra un PDF a la ventana.

Las dos formas son la misma página, pero no hacen exactamente lo mismo. Abierto con doble clic —o sea, desde `file://`— funcionan las once herramientas que no necesitan motor externo; las otras cuatro no, porque los binarios WebAssembly no cargan de forma confiable desde ahí. **Si las necesitas, sírvelo por HTTPS**: desde el enlace de arriba, o desde cualquier hosting estático —GitHub Pages, Netlify, una carpeta en tu servidor— porque no requiere nada del lado del servidor.

Un archivo se puede soltar en cualquier parte de la ventana, no solo sobre el recuadro punteado. Las miniaturas tienen un selector de tamaño de 1 a 10 arriba a la izquierda, y esa preferencia se recuerda entre sesiones.

---

## Qué necesita internet

**Once herramientas funcionan sin conexión**, porque pdf-lib va incrustado: unir, dividir, ordenar, agrupar, reencuadrar, imágenes → PDF, numerar, marca de agua, firmar, formularios y metadatos.

**Las otras cuatro bajan un motor la primera vez que las usas en la sesión.** Queda en memoria mientras no cierres la pestaña, y cada una avisa cuánto pesa antes de empezar:

| Herramienta | Motor | Peso |
|---|---|---|
| PDF → imágenes | pdf.js | ~1,4 MB |
| Comprimir (sin pérdida) | qpdf | 1,3 MB |
| Comprimir (página por página) | Ghostscript | 16 MB |
| Proteger con contraseña | qpdf | 1,3 MB |
| Quitar contraseña | qpdf | 1,3 MB |

Las miniaturas también usan pdf.js, pero **son opcionales**: si no está disponible, la rejilla cae a tarjetas numeradas y todas las herramientas siguen funcionando igual.

---

## Cómo funciona por dentro

**Ninguna herramienta modifica el documento en su sitio.** Cada guardado relee los bytes originales y construye el resultado desde cero. Suena a desperdicio y no lo es: sin eso, guardar dos veces con ajustes distintos acumularía el intento anterior — dos firmas superpuestas, un giro aplicado dos veces. Ese bug existió y esa es la cura.

**Los metadatos del original se conservan.** Las seis herramientas que rearman el archivo copian título, autor, asunto, palabras clave, creador, productor y fechas al documento nuevo. Reordenar una página no debería borrarle el nombre a quien escribió el documento. La única que los borra es la de Metadatos, que es donde el usuario lo pide.

**El editor no firma tus archivos.** pdf-lib se estampa a sí misma como productora en tres momentos distintos —al abrir, al crear y al guardar— y los tres están apagados. Un documento que sale de aquí no declara con qué se hizo ni cuándo.

**Las miniaturas se dibujan solo cuando van a verse.** Un `IntersectionObserver` con 260 px de adelanto rasteriza lo que está por entrar en pantalla, al doble del ancho realmente mostrado. Un documento de 300 páginas abre igual de rápido que uno de tres.

El código está comentado en detalle en `editor-pdf.html` —cerca de un tercio del JavaScript son comentarios— incluyendo un mapa general al comienzo y la anatomía de una herramienta antes de las quince.

---

## Traducir a otro idioma

La interfaz está en español y toda su traducción vive en dos lugares de `editor-pdf.html`:

- **Los textos que genera el JavaScript** están agrupados en un único objeto `T` al inicio del `<script>`, cada uno con su equivalente en inglés como comentario al lado. Son 358 entradas. No contienen ni una etiqueta HTML, a propósito: el marcado vive en el código, así que quien traduce no puede romper la página cerrando mal un `<span>`.
- **Siete frases están escritas directamente en el marcado.** Una tabla al inicio del `<body>` te dice cuáles son, dónde están y qué deben decir en inglés.

Dos detalles que ahorran sorpresas. Los `{0}`, `{1}` son huecos que se rellenan en ejecución y **se pueden reordenar** al traducir. Los pares `['singular','plural']` los resuelve una función; si tu idioma necesita más formas, se cambia esa función y no el objeto.

Y una advertencia: cinco grupos de `T` se leen **por clave** y no por punto —los colores, los papeles, las tipografías, las seis posiciones del encabezado y los campos de metadatos—. Un buscador de texto no encontrará `T.color.red` en ningún lado; no los borres creyendo que sobran.

Cuando termines, regenera el minificado. Los comentarios en inglés no ocupan nada en esa versión, porque se eliminan al minificar.

---

## Modificar este proyecto (con o sin IA)

Este editor está pensado para que lo tomes y lo hagas tuyo. Buena parte de quienes lleguen aquí lo van a abrir junto a una IA y le van a pedir cambios. Si ese es tu caso, esto es lo que conviene que la IA sepa antes de tocar nada — puedes pegarle este bloque:

> Trabaja siempre sobre `editor-pdf.html` (la versión comentada), nunca sobre `editor-pdf.min.html`, que se genera. El encabezado del archivo trae un índice de secciones (4.1 a 4.9) y una lista de **invariantes**: léelas primero, porque romper cualquiera de ellas rompe una promesa del proyecto y no solo una función. Las más importantes: todo el texto visible vive en el objeto `T`; ninguna herramienta muta el documento de origen al guardar, cada guardado relee los bytes originales; `pdf.js` detacha el buffer que recibe, así que siempre hay que pasarle `copiaBuffer()`; pdf-lib se firma sola como productora al abrir, al crear y al guardar, y por eso existen las constantes `ABRIR` y `GUARDAR` — no llames a `load()`, `create()` ni `save()` sin ellas; y los metadatos del original se heredan con `ponMetadatos()`. Justo antes de las quince herramientas hay un comentario con la anatomía de un módulo y la lista de piezas reutilizables: revísala antes de escribir algo nuevo, porque casi todo lo que vas a necesitar ya existe. Al terminar, regenera el minificado y compruébalo abriéndolo y recorriendo las herramientas.

El archivo comentado explica el *porqué* de cada decisión no obvia, no solo el *qué*. Si algo parece raro o enrevesado, es muy probable que haya un comentario al lado contando qué problema resuelve — varias de esas notas son cicatrices de bugs reales. La matemática de `anclaje()`, por ejemplo, calcula desde el centro y no desde una esquina por una razón concreta que está escrita ahí.

---

## Limitaciones conocidas

**La memoria.** Un PDF con muchas imágenes se decodifica entero para trabajarlo. Documentos de cientos de megas pueden hacer sudar a un teléfono; conviene probar antes de confiar.

**El texto solo llega hasta el alfabeto latino.** Las herramientas que escriben sobre la página usan las 14 tipografías estándar del PDF, que no hay que incrustar pero solo cubren WinAnsi. El español entra completo, con tildes y eñes; el chino, el árabe y el cirílico no. Los caracteres que no caben se descartan en vez de reventar a mitad de camino.

**La marca de agua va siempre encima.** pdf-lib solo sabe agregar contenido, no meter algo por debajo de lo ya dibujado. Por eso la opacidad importa tanto y por defecto viene baja.

**Comprimir sin pérdida no siempre gana.** Reescribe la estructura interna del archivo: hace maravillas con PDF de escáneres y sistemas antiguos —hasta un 90% en archivos con los flujos sin comprimir— y absolutamente nada con los que ya venían apretados. Cuando no gana, te devuelve tu original en vez de un archivo idéntico con otro nombre.

**Los motores vienen de un CDN.** Se descargan bajo demanda desde jsDelivr, con respaldo en unpkg. Si tu red los bloquea, esas cuatro herramientas no funcionan; las otras once sí.

**No es un lector ni un editor de texto.** No se puede cambiar el contenido de una página, ni editar el texto existente, ni hacer OCR. Es deliberado.

---

## Archivos

| Archivo | Para qué |
|---|---|
| `editor-pdf.html` | Versión comentada. Es la fuente: edita esta. |
| `editor-pdf.min.html` | Versión minificada. Es la que se usa y se publica. |

La minificada se genera a partir de la fuente. Puedes usar la herramienta que prefieras, pero conviene tratar el archivo en tres partes separadas —CSS, marcado y JavaScript— y **dejar pasar el bloque de pdf-lib sin tocarlo**: son 513 KB que ya vienen minificados, y volver a procesarlos solo gasta tiempo.

Sea cual sea la herramienta, hay tres condiciones que hay que respetar:

- **No actives el acortamiento de nombres de propiedades.** El objeto `T` se recorre por clave desde el propio código —los colores, los papeles, las tipografías, las posiciones del encabezado y los campos de metadatos—, y renombrar sus propiedades dejaría la interfaz llena de `undefined` sin lanzar un solo error.
- **Conserva los comentarios que empiezan con `/*!`.** Son los avisos de licencia, el del editor y el de pdf-lib. Las licencias MIT piden que el aviso de copyright viaje con todas las copias, y como acá la copia es un archivo suelto, tiene que ir dentro.
- **Cuidado con las transformaciones marcadas como inseguras.** Hay geometría de por medio —la colocación de la firma sobre páginas giradas, las conversiones de milímetros a puntos— y en las pruebas ahorraban unos 200 bytes. No compensa.

Después de minificar, abre el resultado y recorre las quince herramientas antes de publicarlo.

---

## Compatibilidad

Chrome, Edge, Firefox y Safari en versiones recientes. Necesita Canvas 2D, WebAssembly para las cuatro herramientas con motor, y `IntersectionObserver` para las miniaturas.

En móviles funciona: la rejilla se adapta al ancho y el nivel 10 del selector de tamaño hace que una página ocupe el contenedor completo. La firma se dibuja con el dedo.

Las tipografías vienen de Google Fonts, pero se piden como hoja de impresión y se ascienden al cargar: si Google no responde o no hay conexión, el navegador no queda esperando y cae de inmediato a las del sistema. Si lo quieres completamente autónomo, borra los tres `<link>` del `<head>`; las reservas ya están declaradas en las variables `--sans` y `--mono` del CSS.

---

## Licencia

[MIT](LICENSE). Puedes clonarlo, modificarlo, extenderlo, incluirlo en algo tuyo y venderlo, sin pedir permiso ni pagar nada. La única condición es conservar el aviso de copyright.

Sobre las partes que no son de este proyecto:

- **[pdf-lib](https://github.com/Hopding/pdf-lib)** (MIT, © 2019 Andrew Dillon) va **incrustado** en el archivo. Es la única dependencia que se distribuye junto con el editor, y por eso su aviso de copyright viaja dentro: está como bloque `/*!` justo antes de su `<script>`, y el minificador lo conserva. Incluye a su vez tslib (Apache-2.0, © Microsoft), cuyo aviso también sobrevive.
- **[pdf.js](https://mozilla.github.io/pdf.js/)** (Apache-2.0) y **[qpdf](https://qpdf.sourceforge.io/)** (Apache-2.0, vía [@jspawn/qpdf-wasm](https://www.npmjs.com/package/@jspawn/qpdf-wasm)) no vienen incluidos: se descargan desde un CDN público en tiempo de ejecución.
- **[Ghostscript](https://www.ghostscript.com/)** (**AGPL-3.0**, vía [@jspawn/ghostscript-wasm](https://www.npmjs.com/package/@jspawn/ghostscript-wasm)) tampoco viene incluido, y solo se descarga si el usuario elige comprimir página por página. Su licencia es copyleft y bastante más exigente que las demás: si vas a distribuir este editor empaquetado de otra forma —dentro de una aplicación de escritorio, por ejemplo, o sirviendo tú mismo el binario en vez de usar el CDN— revisa ese punto con calma antes.

Esto es información, no asesoría legal.
