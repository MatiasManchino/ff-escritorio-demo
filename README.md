# FF — El Escritorio (v1 de testeo)

Prototipo de negociación de Freight Forwarder para probar la experiencia sin el mapa 3D.
Abre `index.html` en el navegador para jugar.

## Cómo usar este repositorio en GitHub
- Este repositorio está listo para verse en GitHub como demo web.
- Busca `index.html` en la raíz y ábrelo localmente para probar la demo.
- No necesitas instalar nada: basta con abrir el archivo en el navegador.

## Cómo jugar
- Llega un cliente con una carga. Vos ves **tu costo de flete**.
- Movés 3 palancas: **Precio** (nunca por debajo del 10% de margen), **Plazo de pago**, **Transporte**.
- Podés usar 3 **tácticas**: presión de tiempo, argumento de valor, pago anticipado.
- La barra **"Interés"** te dice qué tan cerca estás de que acepte.
- Enviás cotización → el cliente acepta, contraoferta o se va.
- **Cuidá la caja:** le pagás al proveedor ya, pero al cliente le cobrás según el plazo. Si la caja se va a 0 → **bancarrota**.
- Hay 3 tipos de cliente ocultos (Rata, Apurado, Confiable). Vas viendo **pistas** mientras negociás.

> El truco: con un **Rata** no le ganás bajando el precio. Tenés que usar plazo de pago, transporte y
> tácticas para cerrar la venta.

## Cómo probarlo localmente
1. Doble click en `index.html` para abrirlo en el navegador.
2. O usa un servidor local desde esta carpeta:
   ```bash
   python -m http.server 8000
   ```
3. Luego abre `http://localhost:8000`.

## Qué observar
- ¿Piden **"una más"** o se aburren?
- ¿Entienden cuál es la decisión importante, o solo apretan "Enviar"?
- ¿Se siente diferente negociar con un Rata vs. un Apurado?
- ¿La presión de **caja** y los plazos se sienten reales?

Anotá dónde se prenden y dónde se traban; con eso ajustamos los números.

## Nota de privacidad
El archivo `feedback_ff_escritorio_juan.txt` es privado y no forma parte del contenido público en GitHub.
