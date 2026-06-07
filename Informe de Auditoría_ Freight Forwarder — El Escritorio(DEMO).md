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


INFORME DE KIMI:

🐛 Informe Completo de Bugs — Freight Forwarder: El Escritorio
Analicé el código fuente completo (2.580 líneas, 93 funciones), realicé pruebas interactivas extensas en el juego, y simulé más de 5.000 escenarios matemáticos. Encontré 12 bugs y problemas de diseño clasificados por severidad.
🔴 SEVERIDAD CRÍTICA (3 bugs)
1. Tratos matemáticamente imposibles (~11% de las veces)
El juego genera clientes donde costo * 1.10 (precio mínimo) > costo * viableMult (máximo que acepta el cliente). En estas situaciones, ni siquiera ofreciendo el precio más bajo posible el cliente aceptará — la función acceptance() devuelve 2% de chance. El jugador pierde tiempo y reputación (-2) sin posibilidad de evitarlo.
Causa: La combinación del 8% de "clientes imposibles" (viableMult 1.05–1.12) con costos de agente altos (mala relación o markup alto) produce esta condición. Mi simulación confirmó que 11.1% de los tratos son imposibles de ganar.
2. Meta de $20.000 prácticamente inalcanzable
Caja inicial: $6.000 | Meta: $20.000 | Necesitás ganar $14.000
Gastos mensuales: $1.500 que suben 4% compuesto cada mes
A los 12 meses los gastos pasan de $1.500 a ~$2.400/mes
Mi simulación con 50% de éxito en tratos terminó en negativo antes de llegar a la meta. Con 30% de éxito: bancarrota a los ~110 días. Los gastos crecen exponencialmente mientras las ganancias lo hacen linealmente.
3. Botón "Pedir descuento" sigue activo después de agotado
Después de 3 descuentos (an.round >= 3), el botón sigue clickeable. Si el jugador aprieta una 4ta vez:
No obtiene descuento (mensaje: "no baja más")
Pierde relación (-2 puntos) sin beneficio alguno
Lo confirmé interactivamente: pedí 4 descuentos, el 4to solo penalizó la relación.
🟠 SEVERIDAD ALTA (5 bugs)
4. Plazos de pago fuera de límite son clickeables
Para agentes con plazoMax=15d (SkyBridge Air, Asia Cargo, RutaSur), los botones de 30d y 45d aparecen con opacity: 0.35 pero siguen siendo clickeables. Al clickearlos no pasa nada visible (solo un toast efímero que el jugador puede no notar).
5. 3er descuento baja cantidades insignificantes
Confirmado en prueba: $4.124 → $3.794 (-$330) → $3.490 (-$304) → $3.445 (-$45). El último descuento es ~7x menor que los anteriores, confundiendo al jugador sobre la eficacia del botón.
6. Precio de venta se modifica sin aviso al renegociar
La función syncCostoSiReservado() sobreescribe G.st.price cuando renegociás un agente ya reservado. Si habías ajustado tu precio manualmente hacia abajo para ser competitivo, el juego lo sube automáticamente al nuevo piso sin notificación.
7. closeDeal() no verifica capacidad máxima
Podés cerrar un trato aunque ya tengas 4 operaciones en curso (el máximo). La operación se agrega igual a G.ops y recién después el juego muestra "estás a full".
8. Solo 2 agentes para modo aéreo (vs 3 marítimos y 3 terrestres)
Hay solo SkyBridge Air y Andes Air para cargas aéreas. Andes Air tiene markup 1.18 (muy caro), dejando una sola opción económica viable para todo el modo aéreo.
🟡 SEVERIDAD MEDIA (4 problemas)
9. El "Ahorrador" tiene target por debajo del piso mínimo
targetMult = 1.05 (precio que quiere pagar)
floor = 1.10 (precio mínimo que podés ofrecer)
El Ahorrador siempre ve tu precio mínimo como "muy caro" porque su target está debajo de tu piso. Tenés que usar tácticas + plazo de 60 días para tener chance > 50%.
10. Tiempo avanza durante modales de premio
nextOrEnd() llama tick() (avance de tiempo + pagos) antes de checkTacticPrize(). Podés ver un modal de premio mientras en segundo plano se pagan agentes, ocurren incidentes, o incluso game over por bancarrota.
11. Economía inconsistente en incidentes
Cuando absorbés un costo de incidente: caja += precio - extra (ganás menos). Cuando no absorbés: caja += precio (cobrás TODO, el cliente paga el extra). Es más rentable dejar que el cliente se enoje que absorber el costo, lo cual es contraintuitivo.
12. Modal de feedback aparece repetidamente
Se dispara a los 2 minutos y reaparece si lo cerrás sin enviar, interrumpiendo el flujo de juego constantemente.
📊 Resumen Ejecutivo
表格
Severidad	Cantidad	Problemas
🔴 Crítica	3	Tratos imposibles, meta inalcanzable, botón activo
🟠 Alta	5	UI plazos, UX descuento, sync precio, capacidad, agentes
🟡 Media	4	Balance rata, flujo modales, economía incidentes, feedback
Total	12	
🔧 Recomendaciones prioritarias
Agregar verificación antes de sendQuote(): si floor > clientMaxViable, mostrar "Este cliente no tiene presupuesto" sin penalizar
Reducir crecimiento de gastos de 4% a 2% mensual, o bajar la meta a $15.000
Deshabilitar botón de descuento cuando an.round >= 3
Deshabilitar botones de plazo que exceden plazoMax (atributo disabled)
Agregar 1-2 agentes aéreos más para equilibrar opciones
Ajustar targetMult del Ahorrador de 1.05 a 1.08–1.12