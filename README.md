## Example e-commerce catalog shop based on AWS Lambda
# AWS_App_Ecommerce_Serverless
**APP ECOMMERCE SERVERLESS**

`     `Es una web sin servidor que ofrece el servicio de ventas de productos a través de internet, desarrollada en AWS.

**CARACTERISTICAS:**

- Al ser serverless(sin servidor) solo paga por uso.
- Tiene una estructura de microservicios.
- Contiene carrito de compras, proveedor de pagos y galería de productos.

**Arquitectura:**

![](Aspose.Words.c764c2de-5b9d-44b1-afbb-ae11a33d8609.001.png)

`     `**Funcionamiento:**

`     `El cliente entra a la web, automáticamente la web buscara en el cloudfront para revisar si lo que el cliente pide esta almacenado (cache) en las zonas de borde con el fin de disminuir la latencia, sino esta ira al origen que es el S3 donde esta alojada nuestra app.

`    `Se activará la API Gateway a través un método llamado /get\_items para activar una función lambda que iterará sobre la base de datos para devolvernos todos los productos almacenados (catalogo).

`     `Nota: <a name="_int_ik7vj99i"></a>La api donde estaban las imágenes ya no funciona por lo que se le creo un nuevo atributo a los elementos de la BD llamado link que almacena URL de imágenes para mostrarse en la app.     

Si el cliente hace una petición de compras la API a través de otro método llamado /buy\_item activa una función lambda que te dirige a un proveedor de pago te envía un SNS que llegara a tu correo suscrito al topic (tema), lo que activa otra función lambda que interactúa con el servicio de Amazon simple email service, encarga de enviar un email a los interesados.

` `![](Aspose.Words.c764c2de-5b9d-44b1-afbb-ae11a33d8609.002.png)

**CREACION:**
**
`     `Para empezar, creamos un entorno de desarrollo, vamos a la consola de aws y en el panel servicios, buscamos cloud9 (click).

![](Aspose.Words.c764c2de-5b9d-44b1-afbb-ae11a33d8609.003.png)

`     `Nos dirigirá a esta ventana donde le daremos a crear entorno (parte superior derecha). 

![](Aspose.Words.c764c2de-5b9d-44b1-afbb-ae11a33d8609.004.png)

Nos mostrara esta ventana donde configuraremos nuestro entorno y una instancia de EC2 para acceder al entorno.

![](Aspose.Words.c764c2de-5b9d-44b1-afbb-ae11a33d8609.005.png)

Le damos Crear y nos devolverá a la página anterior.

![](Aspose.Words.c764c2de-5b9d-44b1-afbb-ae11a33d8609.006.png)

Vemos que esta el entorno que acabamos de crear, le damos a abrir:

![](Aspose.Words.c764c2de-5b9d-44b1-afbb-ae11a33d8609.007.png)

En la terminal vamos a colocar el siguiente comando para clonar el repositorio:

git clone <https://github.com/epsagon/retail-store-workshop.git>

Confirmamos que se hizo la descarga:

![](Aspose.Words.c764c2de-5b9d-44b1-afbb-ae11a33d8609.008.png)

Luego vamos a ubicarnos dentro del proyecto:

cd retail-store-workshop

Vamos a comenzar trabajando con el Backend, para ello vamos a ubicarnos en la carpeta del backend:

cd backend

**Nota:** Antes de continuar abra el archivo serverless.yml** y en la línea 15, cambie python3.7 por uno más actual, en mi caso python3.9 (guarda); ahora vamos al archivo requirements.txt y vamos a agregar esta librería urllib3==1.26.6 (guarda).

Luego vamos a ejecutar una serie de comandos:

sudo pip3 install boto3 

npm install 

npm install -g serverless

En el siguiente asegúrese de cambiar la región por la que está usando, en mi caso eu-west-1:

sls deploy --region <REGION>

Tras una implementación exitosa, podemos ver el siguiente resultado:

![](Aspose.Words.c764c2de-5b9d-44b1-afbb-ae11a33d8609.009.png)

**Nota:** Asegúrese de copiar el punto final, porque podremos usarlo pronto.

Ahora creemos algunos artículos en nuestro stock:

python3 update\_db.py 4 <REGION>

(El numero 4, representa en número de artículos que vamos a almacenar en nuestra DynamoDB).

Antes continuar con el frontend (interfaz), vamos a ir a la consola de aws y creamos un bucket de S3 para almacenar nuestra app.

En servicios colocamos S3 y seleccionamos:

![](Aspose.Words.c764c2de-5b9d-44b1-afbb-ae11a33d8609.010.png)

Le damos a Crear Bucket:

![](Aspose.Words.c764c2de-5b9d-44b1-afbb-ae11a33d8609.011.png)

Configuramos el bucket:

- Colocamos la región en mi caso es us-east-1.
- Seleccionamos de uso general.
- Y le damos el nombre al bucket (el nombre del bucket es único si no te lo acepta coloca otro nombre).

![](Aspose.Words.c764c2de-5b9d-44b1-afbb-ae11a33d8609.012.png)

- Vamos a hacerlo de acceso público por lo que hay que quitar esta selección.

![](Aspose.Words.c764c2de-5b9d-44b1-afbb-ae11a33d8609.013.png)

- Te aparecerá esta ventana selecciona que lo reconoces.

![](Aspose.Words.c764c2de-5b9d-44b1-afbb-ae11a33d8609.014.png)

- Por último, vamos al final y le damos a Crear Bucket.

![](Aspose.Words.c764c2de-5b9d-44b1-afbb-ae11a33d8609.015.png)

Ahora vamos a darle los permisos al bucket creamos una política:

- Vamos a donde están todos nuestros bucket y hacemos click sobre el nombre del que acabamos de crear, en mí caso serverlesss318:

![](Aspose.Words.c764c2de-5b9d-44b1-afbb-ae11a33d8609.016.png)

- Vamos más abajo hasta donde dice Política de Bucket y clicamos en editar:

![](Aspose.Words.c764c2de-5b9d-44b1-afbb-ae11a33d8609.017.png)






- Copiamos este Json, que nos dará acceso a los objetos almacenados en el S3:

{

`    `"Version": "2008-10-17",

`    `"Statement": [

`        `{

`            `"Effect": "Allow",

`            `"Principal": "\*",

`            `"Action": "s<a name="_int_t169gage"></a>3:GetObject",

`            `"Resource": [

`                `"<BucketName>/\*",

`                `" <BucketName> "

`            `]

`        `}

`    `]

}

- Sustituye <BucketName> por el arn de tu bucket.
- Y le damos a Guardar Cambios.

![](Aspose.Words.c764c2de-5b9d-44b1-afbb-ae11a33d8609.018.png)

Ahora en Servicios colocamos IAM y seleccionamos:

![](Aspose.Words.c764c2de-5b9d-44b1-afbb-ae11a33d8609.019.png)

- En el panel seleccionamos Roles y damos click sobre este rol catalog-shop-dev-us-east-1-lambdaRole.
- Luego vamos a Permisos y Clicamos en Agregar permisos y luego en Asociar políticas.

![](Aspose.Words.c764c2de-5b9d-44b1-afbb-ae11a33d8609.020.png)

- Añadimos las siguientes políticas administradas para el correcto funcionamiento de nuestra app.

![](Aspose.Words.c764c2de-5b9d-44b1-afbb-ae11a33d8609.021.png)

Vamos a trabajar ahora en el frontend.

Volvemos a nuestro entorno de cloud9 y colocamos el siguiente comando para entrar en la carpeta del frontend:

cd<a name="_int_jdxukelc"></a> ../frontend

Y Luego los siguientes comandos:

npm install -g yarn 

scottyjs yarn install

Ahora vamos al fichero config.js que se encuentra en la siguiente ruta frontend/src/config.js y copiamos el endpoint que habíamos obtenido anteriormente y la copiamos y vamos a config.js y la pegamos en donde dice API\_URL: de la siguiente manera:

![](Aspose.Words.c764c2de-5b9d-44b1-afbb-ae11a33d8609.022.png)

Guardamos los cambios.

**Nota:** Solo copia hasta <a name="_int_pec9z7jk"></a>el .com y asegúrate de dejar el /dev al final y colocarlo entre comillas dobles.

Ahora vamos a habilitar el alojamiento de sitios web estáticos para que se nos genere una URL, vamos a la consola de AWS y en Servicios colocamos S3 y seleccionamos el bucket que acabamos de crear:

- Dentro del bucket, seleccionamos Propiedades.
- Vamos al final donde dice Alojamiento de sitios web estáticos y le damos a Editar.
- Nos abrirá una ventana, en Alojamiento de sitios web estáticos le damos a Habilitar.
- En Documento de Índice vamos a colocar index.html y le damos a Guardar Cambios.

![](Aspose.Words.c764c2de-5b9d-44b1-afbb-ae11a33d8609.023.png)

Vamos nuevamente a Alojamiento de sitios web estáticos, vemos que nos ha generado una URL.

![](Aspose.Words.c764c2de-5b9d-44b1-afbb-ae11a33d8609.024.png)

Volvemos a cloud9 e implementamos nuestra interfaz, para ello usamos los siguientes comandos:

npm run build

npm add @babel/runtime

En el siguiente comando cambia <name-bucket> por el nombre del S3 que creaste para esta app:

aws s3 cp ./build s3://<name-bucket>/ --recursive

Obtendrás esta respuesta si todo esta ok:

![](Aspose.Words.c764c2de-5b9d-44b1-afbb-ae11a33d8609.025.png)

Copia la URL y abra una nueva ventana en su navegador pegue la URL, podrá visualizar la app:

![](Aspose.Words.c764c2de-5b9d-44b1-afbb-ae11a33d8609.026.png)

Ya la app esta activa sin embargo no podemos visualizar el catálogo de productos, esto se debe a que la API IMAGEN donde estaban las imágenes ya no está en funcionamiento, vamos a solucionarlo:

- Vamos a la consola de AWS y en Servicios colocamos Lambda y seleccionamos la llamada catalog-shop-dev-get-items.
- Seleccionamos código y vamos a eliminar el bucle for que está en la función **get\_items,** la función debe quedar como te muestro a continuación:

![](Aspose.Words.c764c2de-5b9d-44b1-afbb-ae11a33d8609.027.png)

- Guardamos (Ctrl+s) y damos click en Deploy.

Vamos nuevamente a la pestaña donde esta nuestra app y refrescamos, ya deberían verse los 4 espacios que guardamos en nuestra DynamoDB.

Ahora vamos a la consola de AWS y en servicios colocamos DynamoDB y seleccionamos.

Luego elegimos la tabla [CatalogTable](https://us-east-1.console.aws.amazon.com/dynamodbv2/home?region=us-east-1#table?name=CatalogTable) y explorar elementos, observamos los cuatro objetos.

![](Aspose.Words.c764c2de-5b9d-44b1-afbb-ae11a33d8609.028.png)

Vamos a agregarle un atributo llamado link para guardar la URL de la imagen que desees guardar en tu BD para este elemento.

Para ello seleccionamos uno de los elementos damos click en acciones y luego en editar elemento.

` `![](Aspose.Words.c764c2de-5b9d-44b1-afbb-ae11a33d8609.029.png)

Ahora le damos a Agregar nuevo atributo, seleccionamos cadena (string), abrira una nueva fila; en la columna de nombre de atributo colocamos link y en valor colocamos la URL de la imagen que quieras para ese elemento.

![](Aspose.Words.c764c2de-5b9d-44b1-afbb-ae11a33d8609.030.png)

Le damos guardar, estos mismos pasos vamos a realizarlos para cada elemento de la tabla.

Vamos a la pestaña donde esta nuestra app, ahora debería verse así:

![](Aspose.Words.c764c2de-5b9d-44b1-afbb-ae11a33d8609.031.png)

Vamos a realizar una compra:

- Seleccionamos un producto, nos enviara a una nueva ventana con las características del producto:

![](Aspose.Words.c764c2de-5b9d-44b1-afbb-ae11a33d8609.032.png)

- Guardamos el producto en el carrito de compras (click en ADD TO CART):

![](Aspose.Words.c764c2de-5b9d-44b1-afbb-ae11a33d8609.033.png)

- ` `Esto nos envía a la página del carrito de compras, ahora compraremos el producto (click en checkout).
- Nos envía a un formulario de 3 pasos donde debemos colocar el email, la dirección de envió y datos de la tarjeta para el cobro del producto.

![](Aspose.Words.c764c2de-5b9d-44b1-afbb-ae11a33d8609.034.png)

` `![](Aspose.Words.c764c2de-5b9d-44b1-afbb-ae11a33d8609.035.png)

![](Aspose.Words.c764c2de-5b9d-44b1-afbb-ae11a33d8609.036.png)

Antes de darle a COMPLETE ORDER vamos a la consola de AWS para suscribirnos al topic de SNS para que nos lleguen los mensajes de la compra.

- Primero en servicio colocamos sns y seleccionamos Simple Notification Service y luego en panel elegimos tema (topic).![](Aspose.Words.c764c2de-5b9d-44b1-afbb-ae11a33d8609.037.png)
- En el topic con el nombre [PaymentTopic](https://us-east-1.console.aws.amazon.com/sns/v3/home?region=us-east-1#/topic/arn:aws:sns:us-east-1:992382498225:PaymentTopic) le damos click.
- Luego le damos a crear una suscripcion.

![](Aspose.Words.c764c2de-5b9d-44b1-afbb-ae11a33d8609.038.png)

- Nos enviara a configurar la suscripción:
- En ARN del tema colocamos la ARN que tiene el nombre del tema.
- En Protocolo elegimos Correo Electrónico.
- En Punto de enlace ponemos un email real donde queramos que nos llegue el mensaje.
- Finalmente, a Crear suscripción.

![](Aspose.Words.c764c2de-5b9d-44b1-afbb-ae11a33d8609.039.png)

- Nos llegara un email de confirmación al correo que coloco en la suscripción.

![](Aspose.Words.c764c2de-5b9d-44b1-afbb-ae11a33d8609.040.png)

- Dele a aceptar suscripción y se abrirá una nueva pestaña con un mensaje de confirmación.

![](Aspose.Words.c764c2de-5b9d-44b1-afbb-ae11a33d8609.041.png)

Ahora si compramos un producto nos llegara un email notificando la compra.

- Vamos a darle a COMPLETE ORDER.

![](Aspose.Words.c764c2de-5b9d-44b1-afbb-ae11a33d8609.042.png)

- Nos saldrá una ventana con el ID de la orden.

![](Aspose.Words.c764c2de-5b9d-44b1-afbb-ae11a33d8609.043.png)

- Simultáneamente nos llegara un email con los datos del producto y del comprador.

![](Aspose.Words.c764c2de-5b9d-44b1-afbb-ae11a33d8609.044.png) 


Ya nuestra app realiza todos los servicios necesarios para ser operativa, pero para mejorar la disponibilidad para los clientes implementaremos el servicio de AWS CloudFront para disminuir la latencia usando sus zonas de borde para almacenar en cache y disminuir el tiempo de respuesta.

- Vamos a la consola de AWS y en Servicios colocamos cloudfront, seleccionamos.
- Le damos a Crear distribución (Create Distribution) y configuramos.
- En dominio de origen (origin domain) seleccionamos el dominio del bucket donde esta alojada nuestra app, en mi caso es esta:

serverlesss318.s3-website-us-east-1.amazonaws.com

- En Web Application Firewall (WAF) seleccionamos Do not enable security protections.
- En Default root object - optional colocamos index.html.
- En este caso dejaremos todo lo demás por default.
- Clicamos en Crear distribución.

![](Aspose.Words.c764c2de-5b9d-44b1-afbb-ae11a33d8609.045.png)

![](Aspose.Words.c764c2de-5b9d-44b1-afbb-ae11a33d8609.046.png)

![](Aspose.Words.c764c2de-5b9d-44b1-afbb-ae11a33d8609.047.png)

![](Aspose.Words.c764c2de-5b9d-44b1-afbb-ae11a33d8609.048.png)


Ya estaría creada, esperamos a que en Status diga Enable, luego clicamos sobre el ID del cloudFront acabado de crear y seleccionamos General.

![](Aspose.Words.c764c2de-5b9d-44b1-afbb-ae11a33d8609.049.png)

Copiamos el Distribution domain name y abrimos una nueva pestaña en el navegador y pegamos.


![](Aspose.Words.c764c2de-5b9d-44b1-afbb-ae11a33d8609.050.png)

Listo ya podemos desplegar nuestra app a través CloudFront.

**Nota:** Prueba usar este dominio de CloudFront desde otra IP. Se desplegará igualmente nuestra app.














































