# FF — El Escritorio (v1 de testeo)

Prototipo de negociación de Freight Forwarder para probar la experiencia sin el mapa 3D.
Abre `index.html` en el navegador para jugar.

## Demo y cómo probarlo
- Prueba la demo desde el repositorio: https://matiasmanchino.github.io/ff-escritorio-demo/  
- Abre `index.html` en tu navegador o publica el repositorio en GitHub Pages.
- Todas las observaciones también deben dejarse desde `index.html` usando el botón de feedback.

## Cómo jugar
- Llega un cliente con una carga. Vos ves **tu costo de flete**.
- Movés 3 palancas: **Precio** (nunca por debajo del 10% de margen), **Plazo de pago**, **Transporte**.
- Podés usar 3 **tácticas**: presión de tiempo, argumento de valor, pago anticipado.
- La barra **"Interés"** te dice qué tan cerca estás de que acepte.
- Enviás cotización → el cliente acepta, contraoferta o se va.
- **Cuidá la caja:** le pagás al proveedor ya, pero al cliente le cobrás según el plazo. Si la caja se va a 0 → **bancarrota**.
- Hay 3 tipos de cliente ocultos (Ahorrador, Apurado, Confiable). Vas viendo **pistas** mientras negociás.

> El truco: con un **Ahorrador** no le ganás bajando el precio. Tenés que usar plazo de pago, transporte y
> tácticas para cerrar la venta.

## Qué observar
- ¿Piden **"una más"** o se aburren?
- ¿Entienden cuál es la decisión importante, o solo apretan "Enviar"?
- ¿Se siente diferente negociar con un Ahorrador vs. un Apurado?
- ¿La presión de **caja** y los plazos se sienten reales?

Anotá dónde se prenden y dónde se traban; con eso ajustamos los números.

## Feedback
- Hay un botón de opinión dentro de `index.html` para dejar todas las observaciones.
- El feedback se guarda en el navegador como respuestas locales.
- El HUD muestra cuántas respuestas quedaron guardadas.

