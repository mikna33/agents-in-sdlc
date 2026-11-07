---
title: "Ejercicio 4: supervisa y administra agentes"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

En los dos ejercicios anteriores, pediste a Copilot cloud agent que se ocupara de tres tareas distintas para mejorar la experiencia de usuario y añadir funcionalidades. Aunque cloud agent trabaja de forma autónoma y asíncrona, es importante poder supervisar esas tareas.

Hay varias herramientas para administrar las tareas asignadas a cloud agent, como la [página de agentes][agents-page] de GitHub.com. Desde este centro puedes ver todas las tareas del agente que tienen solicitudes de incorporación de cambios (PR) abiertas, explorar las operaciones que se han realizado y orientar una sesión en curso.

En esta lección:

- explorarás la página de agentes para supervisar las tareas de cloud agent.
- orientarás una sesión en curso para pedir funcionalidad adicional.

## Escenario

Después de asignar al agente la tarea de crear un modo de alto contraste, el equipo decidió que sería un buen momento para añadir también un modo claro. Como ya se estaba actualizando el estilo del sitio y añadiendo controles de alternancia, parecía lógico incluir esta funcionalidad. Orientarás el trabajo del agente para que añada el modo claro además del modo de alto contraste.

## Revisa las tareas de Copilot cloud agent

Vamos a consultar el estado de las tareas asignadas a Copilot cloud agent.

1. Ve a la [página de agentes de Copilot](https://github.com/copilot/agents).
2. Revisa la lista de tareas, tanto en el panel principal como en el panel izquierdo. Deberías ver las tareas que has asignado a Copilot, entre ellas:
   - actualizar la documentación del código.
   - generar API para modificar juegos.
   - añadir un modo de alto contraste al sitio web.
3. Selecciona una de las tareas en curso. Revisa las operaciones que ha realizado Copilot. Por ejemplo:
   - ha descargado el código del repositorio.
   - ha creado el entorno de trabajo.
   - ha configurado servidores MCP.
   - ha realizado distintas operaciones para completar la tarea.

> [!NOTE]
> Los pasos concretos varían según el estado del trabajo de Copilot y el enfoque que haya seguido.

4. Observa también el panel de solicitudes de incorporación de cambios (PR), que aparece a la derecha y permite consultar la PR y los archivos modificados.

## Orienta cloud agent

Ahora que has visto las tareas activas, pide a Copilot que añada el modo claro mientras trabaja en el modo de alto contraste.

1. Selecciona la sesión relacionada con el modo de alto contraste. El título exacto dependerá del nombre que haya elegido Copilot y del estado del trabajo.

    ![Sesión de accesibilidad en mission control](../../../_images/ex6-accessibility-session.png)

2. Espera unos minutos, hasta que la sesión indique que Copilot ha terminado la configuración y ha empezado a trabajar. Lo sabrás cuando empiecen a aparecer mensajes parecidos a los que se muestran a continuación.
3. En el cuadro de diálogo **Steer active session while Copilot is working**, escribe esta indicación:

    ```plaintext
    While we are working on a high contrast mode, let's also add a light mode. There should be a switch for this mode as well where users can select their desired display mode.
    ```

    ![Captura de una tarea de Copilot cloud agent en la página de agentes, con el cuadro de diálogo para orientar la sesión activa resaltado](../../../_images/ex6-steer-cloud-agent-task.png)

4. Pulsa <kbd>Enter</kbd> para enviar la indicación.
5. Observa cómo Copilot la confirma y la incorpora al flujo de trabajo.

## Deja que Copilot trabaje

Como antes, Copilot empezará a trabajar en la tarea actualizada. Incorporará la nueva solicitud a su flujo al terminar el paso que estaba ejecutando cuando enviaste el mensaje.

El proceso tardará varios minutos. Puedes aprovechar el tiempo para repasar lo que has aprendido hasta ahora.

## Resumen y siguientes pasos

En esta lección has explorado la página de agentes de Copilot, el centro para supervisar y orientar las tareas de Copilot cloud agent. Permite seguir las tareas activas y completadas, revisar el trabajo y redirigir las tareas en curso para ajustar su alcance o añadir indicaciones.

Has aprendido:

- a usar mission control y la página de agentes para supervisar las tareas de cloud agent.
- a redirigir una sesión en curso para solicitar funcionalidad adicional.

Mientras Copilot completa el trabajo de accesibilidad, puedes [iterar sobre las solicitudes de incorporación de cambios que ha creado][next-lesson].

## Recursos

- [Página de agentes de Copilot][agents-page]
- [Acerca de los agentes personalizados][custom-agents]

---

| [← Lección anterior: agentes personalizados][previous-lesson] | [Siguiente lección: itera sobre el trabajo de Copilot →][next-lesson] |
|:--|--:|

[previous-lesson]: ../3-custom-agents/
[next-lesson]: ../5-iterating/
[agents-page]: https://github.blog/changelog/2025-07-03-agents-page-for-copilot-coding-agent-in-public-preview
[custom-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-custom-agents
