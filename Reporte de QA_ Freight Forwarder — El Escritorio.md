# Reporte de QA: Freight Forwarder — El Escritorio

Se ha realizado un testeo exhaustivo del juego "Freight Forwarder — El Escritorio" (versión demo en GitHub Pages) con el objetivo de encontrar bugs, casos borde y vulnerabilidades.

A continuación, se detallan los hallazgos categorizados por severidad y área de impacto.

## 1. Vulnerabilidades Críticas (Rompen la economía del juego)

### 1.1. Ausencia de validación en el precio mínimo de cotización
Aunque la interfaz de usuario (el slider de precio) limita el precio mínimo a `costo * 1.10` y el máximo a `costo * 1.55`, la función `sendQuote()` no valida estos límites en el backend/lógica del juego. 
Si se manipula el estado del juego o se inyecta código, es posible enviar una cotización con `price = 0` o un valor negativo. El juego procesará la operación normalmente, lo que resultará en un margen negativo (pérdida de dinero) sin que el sistema lo impida o lance un error.

### 1.2. Incidentes con costo extra superior al precio (Ganancia negativa)
Cuando ocurre un "problema operativo" (incidente), el juego genera un costo extra aleatorio (`extra`). Si el jugador decide "absorber el costo", la lógica suma al cash: `price - extra`.
Sin embargo, no hay una validación que impida que `extra` sea mayor que `price`. En el caso extremo donde `extra > price`, absorber el costo resulta en una resta neta de dinero de la caja (`G.cash += price - extra`), lo que puede llevar a la bancarrota inmediata de forma inesperada.

## 2. Bugs de Interfaz y Lógica (Glitches)

### 2.1. Acumulación de modales "Operaciones en curso"
La función `showOpsModal()` crea un nuevo elemento `div` con id `opsModal` y lo añade al `document.body` cada vez que se llama. A diferencia de otros modales (como `capModal` o `lossModal`), no verifica si ya existe un modal abierto ni lo elimina antes de crear uno nuevo.
Si el usuario hace clic múltiples veces en el botón de operaciones, se acumularán múltiples modales superpuestos en el DOM, lo que puede causar problemas de rendimiento y visualización.

### 2.2. Error en la renegociación de agente sin cliente activo
Si se intenta abrir la pantalla de negociación de un agente (`openAgentNeg`) cuando no hay un cliente activo (`G.st = null`), el juego lanza un error fatal de JavaScript: `TypeError: Cannot read properties of undefined (reading 'price')`. Esto ocurre porque la función asume que `G.st.offers` siempre existe.

### 2.3. Plazo de pago del cliente excede el plazo máximo del agente
Aunque cada agente tiene un `plazoMax` (ej. Asia Cargo = 15 días), la interfaz permite al jugador ofrecer al cliente un plazo de hasta 60 días sin ninguna validación restrictiva. El juego solo muestra una advertencia visual ("Cobras en 60d pero pagas al agente en 15d: usas tu caja"), pero permite cerrar la operación. Esto no es un bug *per se* (es una mecánica de riesgo de caja), pero la falta de un límite duro podría ser explotada si el jugador tiene caja infinita.

### 2.4. Validez de la tarifa del agente es puramente cosmética
Cada agente muestra un parámetro de "validez" (ej. 7 días, 10 días). Sin embargo, en la lógica del juego, este valor no se utiliza para expirar la tarifa. Si el jugador reserva una tarifa y avanza el tiempo más allá de los días de validez, la tarifa sigue estando disponible al mismo precio sin necesidad de renegociar.

## 3. Casos Borde y Comportamientos Inesperados

### 3.1. Rechazo infinito de clientes
Si el jugador decide rechazar a todos los clientes que llegan, el juego continuará generando clientes infinitamente y avanzando el tiempo. Eventualmente, los "Gastos del mes" (`G.monthly`) consumirán toda la caja, llevando a un Game Over por bancarrota en el día ~121 (asumiendo caja inicial de $6k y gastos de $2k/mes con inflación del 4%). El juego maneja esto correctamente, pero no hay penalización de reputación por rechazar sistemáticamente a todos los clientes.

### 3.2. Límite de capacidad de operaciones (Bypass)
El juego tiene un límite de capacidad (`G.capacity = 4`). Si el jugador alcanza este límite, no llegan nuevos clientes. Sin embargo, si el jugador ya tiene un cliente en negociación, cierra un trato (llegando a 4) y luego, por alguna manipulación de estado, logra cerrar otro trato simultáneo, el array `G.ops` excederá la capacidad (ej. 5 operaciones). El juego no se rompe, pero la UI del modal de operaciones mostrará `(5/4)`.

### 3.3. Tácticas ilimitadas (Exploit de lógica)
La función `checkTacticPrize()` otorga una táctica gratis cada `PRIZE_EVERY` (3) tratos cerrados. Si un jugador manipula `G.deals` y `G.lastPrizeAt`, puede forzar la aparición del modal de premio repetidas veces, acumulando tácticas infinitas en su inventario (`G.inv`).

### 3.4. Condición de victoria pasiva
El objetivo del juego es llegar a $20.000 de caja. Sin embargo, cuando se alcanza este monto, el juego no termina ni muestra una pantalla de victoria (Game Over positivo). Simplemente cambia el texto de la barra de progreso a "¡LOGRADA! 🏆" y permite seguir jugando indefinidamente.

## Conclusión

El juego es muy robusto en su flujo principal. La mayoría de las formas de "romperlo" requieren inyección de JavaScript a través de la consola para manipular el estado global (`G`), lo cual es esperado en un juego de navegador de un solo jugador. Sin embargo, el bug de acumulación de modales (`showOpsModal`) es un problema real de UI que debería corregirse, al igual que la falta de implementación real de la "validez" de las tarifas de los agentes.
