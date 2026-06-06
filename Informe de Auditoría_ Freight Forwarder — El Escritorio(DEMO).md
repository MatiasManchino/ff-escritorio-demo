# Informe de Auditoría: Freight Forwarder — El Escritorio

Se ha realizado una revisión exhaustiva del juego disponible en [https://matiasmanchino.github.io/ff-escritorio-demo/](https://matiasmanchino.github.io/ff-escritorio-demo/). A continuación se detallan los hallazgos divididos por categorías: **Lógica del Juego**, **Bugs Técnicos**, e **Interfaz de Usuario (UI/UX)**.

---

## 1. Problemas de Lógica e Imposibilidad de "Ganar"

| Problema | Descripción | Impacto |
| :--- | :--- | :--- |
| **Inexistencia de Condición de Victoria** | Aunque el juego establece una meta de **$20.000**, el código no contiene ninguna función que dispare un estado de "Victoria". Al alcanzar o superar la meta, la barra simplemente cambia de color y dice "¡LOGRADA!", pero el juego continúa indefinidamente sin un cierre formal. | **Alto**: El jugador no siente una gratificación final o conclusión del desafío. |
| **Imposibilidad de Perder por Tiempo** | No existe una condición de derrota basada en el tiempo o en no cerrar tratos. El jugador puede dejar pasar meses sin operar y, aunque los gastos mensuales (`G.monthly`) aumentan un **4% cada mes**, si no tiene operaciones activas, la caja no baja de cero a menos que ya estuviera cerca del límite. | **Medio**: Reduce la presión y el riesgo real del juego. |
| **Escalado Infinito de Gastos** | La variable `G.monthly` aumenta exponencialmente (`*1.04`) cada 30 días. Sin una condición de victoria, eventualmente los gastos superarán cualquier margen posible, haciendo el juego matemáticamente imposible de mantener a largo plazo. | **Bajo**: Solo afecta en partidas extremadamente largas. |

---

## 2. Bugs y Errores de Código

| Bug | Descripción Técnica | Consecuencia |
| :--- | :--- | :--- |
| **Falta de Sincronización en Renegociación** | Al renegociar con un agente una tarifa ya reservada, el costo en la pantalla del cliente se actualiza, pero el **precio de venta** seleccionado por el usuario puede quedar fuera de los nuevos límites de la barra (`floor` / `cap`), causando inconsistencias visuales. | El margen mostrado puede ser incorrecto hasta que el usuario mueva el slider. |
| **Doble Penalización de Reputación** | En la función `loseDeal`, se resta reputación (`addRep(-2)`). Sin embargo, si el usuario decide "Abandonar la carga" (`declineClient`), también se resta reputación (`addRep(-1)`). Si un cliente se enoja y se va solo, la penalización es mayor que si el usuario corta la relación proactivamente. | Inconsistencia en la estrategia de mitigación de daños. |
| **Táctica de Farol Inconsistente** | La probabilidad de éxito del farol (`0.45 + a.flex`) no considera la reputación actual del jugador, lo que hace que un jugador con "Fama" máxima tenga la misma credibilidad que uno novato. | Falta de profundidad en la mecánica de progresión. |

---

## 3. Dificultades y UX (Experiencia de Usuario)

*   **Bloqueo por Capacidad**: Al llegar al máximo de 4 operaciones, el juego muestra un modal que obliga a avanzar el tiempo. Si el jugador tiene mala suerte con los riesgos de pago, puede entrar en bancarrota sin poder reaccionar.
*   **Slider de Precio Limitado**: El precio de venta está restringido entre el 110% y 155% del costo. Esto impide estrategias de "crecimiento agresivo" (márgenes muy bajos para ganar fama rápido) o "márgenes de lujo" (márgenes muy altos para clientes premium).
*   **Información Oculta**: El "Target" del cliente es casi imposible de obtener (`tellChance` muy bajo), y el castigo por preguntar es desproporcionado (`-2` de fama), lo que desincentiva el uso de esa mecánica.

---

## Sugerencias de Mejora

1.  **Implementar Pantalla de Victoria**: Añadir un `renderVictory()` cuando `G.cash >= G.goal` para dar un cierre al juego.
2.  **Ajustar el Slider**: Permitir un rango más amplio de precios (ej. desde 100% hasta 200%) para dar más libertad estratégica.
3.  **Influencia de la Fama**: Hacer que la reputación (`G.rep`) afecte directamente las probabilidades de éxito de las tácticas y la agresividad de los agentes.
4.  **Eventos Aleatorios**: Introducir eventos externos (huelgas portuarias, cambios de moneda) que afecten el mercado de forma temporal.

> **Conclusión**: El juego es una base sólida y muy entretenida con una interfaz pulida, pero carece de un "final" y presenta pequeñas fricciones lógicas que, de corregirse, elevarían significativamente la calidad de la simulación.
