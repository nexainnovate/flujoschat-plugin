# Changelog

## 1.5.0 — 2026-11-26

Cómo escribir un buen prompt y cómo conectar bien las herramientas. Es lo del PR #7, que se había
quedado sin mergear, puesto al día con la 1.4.0 y vuelto a verificar contra el servidor.

- **Nuevo** en `references/buenas-practicas-prompt.md`:
  - § 6 bis, **condiciones** en sus dos formas, `{{#if}}` / `{{else if}}` / `{{else}}` / `{{/if}}`
    y `{% if %}` / `{% elif %}` / `{% else %}` / `{% endif %}`, con `and`, `or`, `not`, paréntesis,
    comillas simples o dobles y `true` / `false`. Sirven para tono y contexto, nunca para conceder
    algo, y en `run_team` toda condición es falsa.
  - Comentarios `{# … #}` (notas que el modelo no lee) y el valor de reserva
    `{{campo.x|default("…")}}`, con cuándo no usarlo.
  - "Si vienes de Jinja": qué se pega tal cual y qué hay que reescribir.
  - § 11 bis, **cambiar un prompt que ya atiende**: borrador → diff → publicar con nota,
    `basedOnVersion` al empezar el borrador, `rebase` si mientras tanto se publicó otra versión, y
    volver atrás con `restoreVersion`.
  - Qué tiene el agente solo y qué hay que atar; políticas de prompt para las fotos y para cada
    capacidad de Google; checklist ampliada.
- **Nuevo** en `references/catalogo-capacidades.md`:
  - "Las tres piezas" para conectar cualquier cosa: la herramienta existe, está atada al agente y
    el prompt dice la política. Los cuatro tipos (BUILTIN, HTTP, MCP, Apps Script), `allowedTools`
    para limitar las funciones de un servidor MCP y lo que exige el plan.
  - "Usarlas bien": el mínimo necesario, cómo describir lo que creas, combinaciones obligadas y
    prohibidas, y probar de lo barato a lo caro (`validate_team` → `test_ai_tool` → un `run_team`
    por escenario).
  - El paso de teléfono de la agenda (`telefono_para_cita`), que `flujos` solo ve los flujos del
    canal de la conversación y la fila de los números del dueño en "Solo desde el panel".
- **Cambiado**: `crear-agente` remite al catálogo y a `allowedTools`; `actualizar-agente` prepara
  el prompt como borrador desde el paso 3 y separa herramientas (`addToolIds` / `removeToolIds`) de
  categorías; `editar-flujo` toma el `config` de `get_flow_schema`, relee el flujo al terminar y, si
  lo rehace, desactiva el viejo en vez de borrarlo; `crear-equipo` añade el paso `AI_HANDOFF` con
  `add_flow_step` en vez de mandar al panel. El auditor lee además versiones, diff, campos y fotos.
- **Nuevo** en `references/errores-y-costes.md`, `references/sintomas-y-arreglos.md` y
  `references/cuestionario-descubrimiento.md`: los errores de las plantillas del prompt y del
  borrador, y los síntomas y preguntas que faltaban.
- **Corregido**: el catálogo decía que Gmail no envía hasta que Google apruebe la app; lo que
  cuenta es que la conexión tenga el permiso de envío.
- **Corregido**: en una prueba con `run_team` (sin cliente real) solo se niega lo que ESCRIBE
  —agendar, reprogramar o cancelar citas, la ficha, arrancar un flujo, las filas de una hoja, los
  correos y las herramientas HTTP que usan datos del cliente—. Los horarios libres de la agenda, las
  lecturas de Google y tus herramientas HTTP, MCP y Apps Script se ejecutan de verdad.
- Nota nueva F35 en el catálogo (capacidades por MCP, `allowedTools`, `addToolIds` /
  `removeToolIds`). El catálogo sigue contando dieciséis capacidades integradas, once de Google.

Verificado contra el servidor de FlujosChat (`chatboxabel`, `master` en 385ebfc3) con
`npm run check:plugin`.

## 1.4.0 — 2026-11-19

Las redes sociales y las tools que el servidor ya exponía y el plugin no enseñaba. El
comprobador del servidor (`npm run check:plugin`) marcaba 41 tools sin enseñar y 4 capacidades
integradas sin mencionar.

- **Nuevo skill `publicar-en-redes`**: publicar y programar en Instagram, en la página de
  Facebook y en la página de empresa de LinkedIn; ver, cambiar y cancelar la cola; responder,
  ocultar (Instagram, Facebook) o borrar (LinkedIn) comentarios; métricas. Con la puerta de
  acuerdo antes de todo lo público, las diferencias por red (LinkedIn sin `accountId`, sin cambio
  de programadas y sin «ocultar»; el planificador de Meta en Facebook; Instagram no se borra por la
  API), el formato `[CODIGO] texto` de sus errores y qué hacer con cada uno, incluida la regla de
  no repetir una publicación incierta sin mirar la página.
- **Nuevo** en `editar-flujo`: los pasos y las transiciones se editan uno a uno
  (`add_flow_step`, `update_flow_step`, `delete_flow_step`, `add_flow_transition`,
  `update_flow_transition`, `delete_flow_transition`).
- **Nuevo** en `actualizar-agente`: el prompt con borrador, diff y publicación
  (`list_agent_prompt_versions`, `save_agent_prompt_draft`, `diff_agent_prompt`,
  `publish_agent_prompt`, también para volver a una versión anterior), y `addToolIds` /
  `removeToolIds`.
- **Nuevo** en `crear-agente`, `actualizar-agente` y el catálogo: las fotos que envía el agente
  (`upload_agent_image`, `list_agent_images`, `imageAttachments` y la capacidad `imagenes`) y los
  campos de la ficha del cliente (`list_custom_fields`, `create_custom_field`).
- **Nuevo** en el catálogo: las capacidades `google_calendar_dueno` (agenda completa solo para los
  números del dueño), `google_directorio` (Directorio de Workspace) y `google_contactos`
  (Contactos de Google). Ahora son dieciséis capacidades integradas, once de Google.
- **Corregido**: `editar-flujo` decía que el MCP no edita pasos uno a uno.
- **Corregido**: `crear-agente`, `actualizar-agente` y el catálogo decían que una capacidad
  integrada solo se activa en el panel. Se enciende también por MCP con `create_ai_tool` (tipo
  `BUILTIN`); sus ajustes siguen en el panel. Y los campos personalizados se pueden crear por MCP
  (no editar ni borrar).
- README: 88 tools, el sexto skill, los scopes `instagram:*`, `facebook:*` y `linkedin:*` (hay que
  reconectar el servidor para concederlos) y dónde se conecta cada red.

Verificado contra el servidor de FlujosChat (repo `chatboxabel`, `master` con LinkedIn fase 3) con
`npm run check:plugin`. Los códigos de error de las redes que no salen en las descripciones de las
tools están tomados del código de los módulos de contenido, que el comprobador no lee.

## 1.3.2 — 2026-11-10

- **Nuevo**: el campo `channels` de un flujo (`WHATSAPP`, `INSTAGRAM`, `TELEGRAM`, `TIKTOK`,
  `MESSENGER` y el nuevo `WEBCHAT`) en `crear-flujo`, su contrato JSON y `editar-flujo`. Para que
  un flujo responda en el chat web del negocio hay que incluir `"WEBCHAT"` en `channels`: es
  opt-in estricto y por defecto solo va `["WHATSAPP"]`.
- **Nuevo**: `list_connected_channels` (qué canales tiene conectados la empresa, chat web
  incluido) y `get_channel_connect_link` (enlace del panel para conectar uno).

## 1.3.1 — 2026-10-25

- **Cambiado**: el repositorio pasa a la organización `nexainnovate`
  (`/plugin marketplace add https://github.com/nexainnovate/flujoschat-plugin`). La URL vieja
  sigue funcionando por la redirección de GitHub; el nombre del marketplace y el comando de
  instalación no cambian.
- **Cambiado**: el servidor MCP y los enlaces usan el dominio `flujoschat.com` en lugar de
  `flujoschat.foo`. Quien ya tenía el plugin conectado tendrá que iniciar sesión una vez más.

## 1.3.0 — 2026-09-13

Cuatro capacidades de Google más y un tipo de herramienta nuevo, que el servidor ya expone y
aquí no se enseñaban.

- **Nuevo** en `references/catalogo-capacidades.md`:
  - `google_sheets_consultar`: ver las hojas elegidas, listar sus pestañas, buscar filas y leer
    un tramo. Con el aviso de privacidad: lee cualquier fila que coincida, sea de quien sea.
  - `google_sheets_actualizar`: modificar celdas de una fila existente. Explica la convención de
    columnas "(editable)", que va siempre junto con consultar, que no hay autorización por
    cliente y que la columna que identifica al cliente no se marca.
  - `google_gmail_escribir_cliente`: correo al cliente, solo a una dirección guardada desde el
    panel o por la API. Cubre también el interruptor del panel, la copia oculta al equipo, los
    topes y el riesgo de exfiltración si el agente lee datos de otros clientes.
  - `google_drive_buscar`: listar lo elegido y leer por páginas de 4000 caracteres. Solo lo
    elegido y sus hijos directos, y de una hoja solo la primera pestaña.
  - El tipo de herramienta `APPS_SCRIPT` (`execUrl`, `action`, `params`), con la clave
    compartida que solo se pone desde el panel.
- **Nuevo**: la importación de documentos de Google Drive a la base de conocimiento, que es una
  función del PANEL y no una capacidad del agente. Aparece en el catálogo, en la tabla de gastos
  y en las tablas de «solo desde el panel».
- **Nuevo** en `references/errores-y-costes.md`: los errores de Apps Script al crear o editar la
  herramienta, y una sección con lo que las herramientas de Google y Apps Script le devuelven al
  agente. Además, `run_team` ya dice que ejecuta de verdad las herramientas Apps Script y las
  lecturas de hojas y de Drive.
- **Nuevo** en `references/cuestionario-descubrimiento.md` y en `references/sintomas-y-arreglos.md`:
  preguntas y síntomas de las cuatro capacidades y de Apps Script.
- **Corregido**: el catálogo decía que de una hoja entregada el agente solo lee el nombre y la
  primera fila. Con `google_sheets_consultar` lee cualquier fila de cualquier pestaña.
- **Corregido**: no se decía que el envío de Gmail —avisar al equipo y escribir al cliente—
  depende de que Google apruebe ese permiso para FlujosChat.
- **Corregido**: los recuentos. Hay doce capacidades integradas, ocho de ellas de Google.
- `crear-agente`, `actualizar-agente` y el subagente `auditor-agente-ia` conocen las dos reglas
  de combinación: actualizar exige consultar, y el correo al cliente no se combina con lecturas
  de datos de otros clientes.
- `marketplace.json` se había quedado en 1.1.0; se alinea con el manifiesto.

Verificado contra el servidor de FlujosChat (repo `chatboxabel`, rama con `google_drive_buscar`)
con `npm run check:plugin`.

## 1.2.0 — 2026-09-06

Las cuatro capacidades de Google Workspace, que el servidor ya expone y aquí no se enseñaban:
un agente podía usarlas y este plugin no sabía que existían.

- **Nuevo** en `references/catalogo-capacidades.md`: `google_calendar` (qué calendarios hay y
  qué horas están ocupadas), `google_calendar_editar` (crear, mover y cancelar eventos),
  `google_gmail_enviar` (avisar por correo al equipo) y `google_sheets_anotar` (añadir una fila
  a una hoja que eligió el dueño), con sus prerrequisitos, sus límites y una sección sobre qué
  contarle al dueño antes de que las elija.
- **Nuevo** en `references/cuestionario-descubrimiento.md`: preguntas de descubrimiento para las
  tres —el calendario, el correo de aviso y la hoja de cálculo—, con lo que hay que preguntar
  ADEMÁS del «sí quiero»: a qué direcciones, y qué columnas tiene la primera fila de la hoja.
- **Corregido** en `references/errores-y-costes.md`: se decía que en `run_team` las capacidades
  integradas «no actúan». Es cierto solo para las herramientas que ESCRIBEN; las que consultan
  se ejecutan de verdad, y con Google eso sale hacia la cuenta real del dueño. Ya era inexacto
  antes de Google (`consultar_horarios_libres` nunca se negó en una prueba); Google lo vuelve
  visible porque ahora la consulta sale de la casa.
- **Corregido** «las 4 capacidades integradas» en las notas de dos referencias: son ocho.

## 1.1.0 — 2026-09-05

Skills de agentes de IA reescritos como guías paso a paso, con puertas de acuerdo antes de
escribir en la cuenta del cliente y avisos de lo que gasta saldo.

- **Nuevo** `actualizar-agente`: revisar y modificar un agente existente (antes/después, listas
  completas, impacto en sus equipos, borrado con doble confirmación).
- **Nuevo** subagente `auditor-agente-ia`: auditoría de solo lectura de un agente o equipo.
- **Reescrito** `crear-agente`: descubrimiento en dos rondas, menú de lo posible, diseño,
  redacción del prompt con buenas prácticas, dos puertas, comprobación y pendientes del panel.
- **Reescrito** `crear-equipo`: elección entre HANDOFF y SUPERVISOR, fichas de handoff, grafo
  completo, lectura de `validate_team` (errores y avisos), protocolo de prueba con `run_team`.
- **Nuevo** `references/`: cuestionario de descubrimiento, catálogo de capacidades, buenas
  prácticas de prompt, plantillas por caso de uso, diseño de equipos, síntomas y arreglos,
  errores y costes. Cada una termina en "Verificado contra" con el archivo del servidor y la
  fecha.
- **Corregido** en `crear-agente`: se enseñaba a enviar una credencial (`authSecret`) que el
  servidor rechaza; se decía que los documentos solo se suben desde el panel (por MCP entra
  texto); `params` de una herramienta HTTP se describía sin decir que es una lista de
  definiciones.
- **Corregido** en `crear-equipo`: `create_team` devuelve `id`, no `teamId`; faltaban
  `orchestrationMode`, `handoffDescription`, `validate_team`, `run_team`, `isActive` y las
  posiciones del lienzo.
- **Corregido** en `crear-flujo`: el validador admite `REFERRAL` y `PAYMENT`, marca como error
  un `ANY` en pasos con botones o lista y un botón o fila sin transición, y comprueba los
  límites de WhatsApp (3 botones, 20/24/72 caracteres, 10 filas, cuerpo 1024). El contrato y el
  ejemplo dejan de recomendar el `ANY` sobre botones.
- `editar-flujo` menciona `set_flow_step_image`, `list_flow_images` y `upload_flow_image`.
- Añadidos `LICENSE` (MIT, ya declarado en el manifiesto) y este changelog.

Verificado contra el servidor de FlujosChat (repo `chatboxabel`) con `npm run check:plugin`.

## 1.0.0

Primera versión: servidor MCP remoto y skills `crear-flujo`, `editar-flujo`, `crear-agente`,
`crear-equipo`.
