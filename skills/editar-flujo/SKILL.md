---
name: editar-flujo
description: Inspeccionar y editar flujos de WhatsApp existentes en FlujosChat — cambiar nombre, trigger, prioridad o estado, duplicar, eliminar, o añadir, cambiar y borrar pasos y transiciones uno a uno. Úsalo cuando el usuario quiera modificar, revisar, clonar o borrar un flujo existente en FlujosChat.
allowed-tools: "Read Write Bash(node *)"
---

# Editar flujos de WhatsApp (FlujosChat)

Requiere el servidor MCP de FlujosChat conectado.

## Flujo de trabajo

1. **Localiza el flujo.** Usa **`list_flows`** (filtra por `isActive`/`trigger`) y **`get_flow`**
   (con el `flowId`) para ver sus pasos y transiciones actuales.

2. **Elige la acción:**
   - **Metadatos** (nombre, descripción, trigger, triggerKeywords, canales, prioridad,
     activar/desactivar): usa **`update_flow`** con `flowId` y solo los campos a cambiar.
   - **Canales:** `update_flow` con `channels` es la única forma de cambiar por qué canales
     responde el flujo, y la lista se REEMPLAZA entera: para que además conteste en el chat web,
     manda los que ya tenía más `"WEBCHAT"` (p. ej. `["WHATSAPP","WEBCHAT"]`). Omitirlo conserva
     los actuales. **`list_connected_channels`** dice qué canales tiene conectados la empresa.
   - **Duplicar:** **`duplicate_flow`** (`flowId`, `newName`). El clon queda inactivo.
   - **Eliminar:** **`delete_flow`** (`flowId`). Irreversible — confirma con el usuario.
   - **Imagen de un paso:** **`set_flow_step_image`** (`flowId`, `stepOrder` o `stepId`, `imageUrl`
     o `null` para quitarla) pone o quita la portada de un paso con botones o la imagen de un
     paso IMAGE sin recrear el flujo. La URL sale de **`upload_flow_image`** (base64 o una
     `sourceUrl` pública); **`list_flow_images`** enseña las ya subidas para reutilizarlas. Solo un
     paso BUTTONS admite portada: en una lista se guardaría y nunca se enviaría.
   - **Pasos y transiciones, uno a uno** (así se cambia un flujo que ya existe; no crees otro).
     Lee antes con `get_flow` los ids (UUID) de los pasos y las transiciones:
     - **`add_flow_step`** (`flowId`, `stepType`, `name`, `config`, opcionales `nextStepId` y
       `stepOrder`): añade UN paso; sin `stepOrder` va al final. Nace SIN conectar: enlázalo con
       `add_flow_transition` o poniendo `nextStepId` en el paso anterior.
     - **`update_flow_step`** (`stepId` y solo lo que cambia). ⚠️ `config` REEMPLAZA el config
       entero del paso: manda el config COMPLETO de `get_flow` con tu cambio, o se pierden las
       demás claves.
     - **`delete_flow_step`** (`stepId`). No re-enlaza la cadena: las ramas del config que lo
       apuntaban (`trueStepId`/`falseStepId`, `successStepId`/`timeoutStepId`,
       `nextStepIdOnError`) quedan apuntando a un paso que ya no existe. Repúntalas y reconecta la
       cadena ANTES de borrarlo.
     - **`add_flow_transition`** (`fromStepId`, `toStepId`, `condition`, `matchValue`,
       `priority`), **`update_flow_transition`** (`transitionId` y lo que cambia) y
       **`delete_flow_transition`** (`transitionId`). Los dos pasos tienen que ser del MISMO flujo;
       `ANY` no lleva `matchValue` y las demás condiciones sí; con varias gana la de mayor
       prioridad.
     - Si la respuesta trae avisos de canal, enséñalos: dicen qué no se verá igual en algún canal.
     - Rehacer el flujo entero (por ejemplo, otro diseño) sigue siendo **`create_flow`** con el
       JSON corregido (skill `crear-flujo` y su validador). `update_flow` no añade ni cambia pasos.

3. **Confirma** los cambios mostrando el resultado de la tool (y los `warnings` si aplica).

## Notas
- Toda operación se limita a la empresa del usuario autenticado (multi-tenant).
- Para el contrato del JSON al reconstruir un flujo, usa `get_flow_schema` o la skill `crear-flujo`.
