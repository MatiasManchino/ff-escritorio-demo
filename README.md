# FF — El Escritorio (v1 de testeo)

Prototipo para **probar si la negociación de Freight Forwarder es divertida**, sin el mapa 3D.
Es un solo archivo: `index.html`. No necesita instalar nada.

## Cómo jugar (en la compu)
Doble-click en **`index.html`** → se abre en el navegador. Listo.

## Cómo se juega
- Llega un cliente con una carga. Vos ves **tu costo de flete**.
- Movés 3 palancas: **Precio** (nunca por debajo del 10% de margen), **Plazo de pago**, **Transporte**.
- Podés usar 3 **tácticas**: presión de tiempo, argumento de valor, pago anticipado.
- La barra **"Interés"** te dice qué tan cerca estás de que acepte.
- Enviás cotización → el cliente acepta, contraoferta o se va.
- **Cuidá la caja:** le pagás al proveedor ya, pero al cliente le cobrás según el plazo. Todos los
  meses pagás gastos fijos. Si la caja se va a 0 → **bancarrota**.
- Hay 3 tipos de cliente ocultos (Rata, Apurado, Confiable). Vas viendo **pistas** mientras negociás.

> El truco: contra un **Rata** no le ganás bajando el precio (el piso 10% no te deja). Tenés que
> cerrarlo con plazo de pago, transporte y tácticas. **Ahí está la gracia.**

## Cómo compartirlo con tus amigos
Cualquiera de estas:
1. **Hosting gratis (lo más fácil para celular):** subí `index.html` a [Netlify Drop](https://app.netlify.com/drop)
   o GitHub Pages → te da un link que abrís en cualquier teléfono.
2. **Misma red WiFi:** en la PC corré `python -m http.server 8000` dentro de esta carpeta y desde el
   celular entrá a `http://IP-DE-TU-PC:8000`.
3. **Mandar el archivo:** pasale `index.html` por mail/WhatsApp; lo abren en el navegador del celular.

## Qué mirar cuando lo prueben (esto es lo importante)
- ¿Piden **"una más"** o se aburren?
- ¿Entienden **qué decisión** es interesante, o solo aprietan "Enviar"?
- ¿Se siente **distinto** negociar con un Rata vs. un Apurado?
- ¿La presión de **caja** se siente (cobrar tarde duele)?

Anotá dónde se prenden y dónde se aburren. Con eso ajustamos los números.
