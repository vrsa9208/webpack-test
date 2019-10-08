# Webpack Test

## Instalando webpack

El primer paso es instalar webpack y las herramientas para ejecutarlo desde línea de comandos.

```
npm install --save-dev webpack
```

```
npm install --save-dev webpack-cli
```

## Configuración básica de webpack

Una vez instaladas las dependencias, crearemos una carpeta para el código (src) a nivel raíz. Dentro de esta carpeta crearemos nuestros dos primeros archivos de JS:

- index.js => Este archivo será el punto de entrada de nuestra aplicación.
- temp.js => Este archivo contendrá una función temporal, que exportaremos y nos servirá para probar la funcionalidad de webpack.

Dentro de temp.js, colocaremos el siguiente código:

```
export default function helloWorld() {
    return "Hello World";
}
```

Dentro de index.js importaremos la función helloWorld y la mandaremos a llamar para imprimir el resultado en consola:

```
import helloWorld from "./temp";

console.log(helloWorld());
```

Ahora que ya tenemos nuestro código, pasaremos a crear el archivo de configuración base para webpack. Este nos ayudará a tomar el archivo de entrada de nuestra aplicación (index.js) y resolver sus dependencias (temp.js).

Creamos un archivo en la carpeta raíz llamado webpack.config.dev.js y dentro de este escribiremos el siguiente código:

```
module.exports = {
    entry: './src/index.js'
}
```

Bien! Por el momento ya estamos listos para ejecutar webpack y generar nuestro primer bundle. Para esto crearemos un script de node en el archivo package.json.

En la sección de scripts agregamos el siguiente script:

```
"webpack": "webpack --config src/webpack.config.dev.js"
```

Con nuestro webpack definido, ya podemos saltar a la consola y ejecutar nuestro recién creado script:

```
npm run webpack
```

Como resultado de la ejecución veremos en pantalla un mensaje de advertencia que nos informa que no seleccionamos la opción "mode". Por el momento no nos preocuparemos por esto, pero después lo configuraremos y veremos la diferencia.

Vemos también que se ha creado la carpeta dist y dentro de esta se generó el archivo "main.js". Si lo abrimos ahora, veremos una línea con el código generado por webpack. Ejecutemos el archivo con la siguiente instrucción:

```
node dist/main.js
```

El resultado debe ser la cadena de texto "Hello World" en consola. Este es el comportamiento que definimos en "index.js" y notamos que webpack resolvió las dependencias con el archivo "temp.js", es decir integro ambos códigos y generon un bundle, el cual es el archivo "main.js".

## Cambiando la opción 'mode' de webpack

Ahora vamos a analizar el warning que nos lanzó webpack al ejecutarlo. Este tiene que ver con la opción "mode". Este es un campo que podemos agregar en la configuración de webpack. El valor por defecto es "production". Como resultado, webpack optimiza el código que ha leído y esto lo vemos reflejado en una sóla línea de código.

Probaremos a agregar el campo "mode" en la configuración de webpack. Pondremos el valor "development" y veremos cuál es el resultado. El archivo webpack.config.dev.js quedará así:

```
module.exports = {
    entry: './src/index.js',
    mode: 'development'
}
```

Volvemos a ejecutar el script de webpack y vemos como ha cambiado el contenido del archivo "main.js" en la carpeta dist.

Vemos que el contenido se ha repartido en varias líneas y que cuenta con varios comentarios que describen el proceso de webpack.

## Configurando la salida de webpack

Otro punto que podemos configurar con webpack es la salida que genera este proceso. Esto lo podemos lograr integrando el campo output en el archivo de configuración.

Para configurar esta salida, haremos uso de la libreria de node 'path' que ya está incluída. Para esto agregaremos la siguiente línea en el archivo de webpack.config.dev.js:

```
const path = require('path');
```

Ahora ya podemos agregar el campo de output y usaremos la siguiente configuración:

```
output: {
  path: path.resolve(__dirname, 'dist'),
  filename: 'bundle.js'
}
```

Lo que está haciendo esta configuración es decirle a webpack que el archivo generado estará en el directorio del proyecto en la carpeta dist. No es diferente a la configuración por defecto, pero ahora sabemos como cambiar la carpeta de salida.

Lo que si hemos cambiado es el nombre del archivo generado. Ya no será "main.js", sino "bundle.js".

El contenido del archivo webpack.config.dev.js debe haber quedado de la siguiente manera:

```
const path = require('path');

module.exports = {
  entry: "./src/index.js",
  mode: "development",
  output: {
    path: path.resolve(__dirname, "dist"),
    filename: "bundle.js"
  }
};

```

Si ejecutamos de nuevo el script de webpack, nos daremos cuenta de estos cambios.

## Configurando nuestro primer loader

Hasta el momento webpack ha tomado el contenido de nuestros archivos javascript y los ha integrado en un bundle sin cambiar realmente la funcionalidad de estos. Pero webpack puede ir más alla.

Para poner un ejemplo haremos uso de una funcionalidad nueva en javascript. El spread operator. Este nos permite tomar un objeto y descomponer sus partes, basicamente.

Para esto borraremos el archivo "temp.js" y sustituiremos el contenido de "index.js" por el siguiente:

```
const objeto = {
    a: 'atributo A',
    b: 'atributo B'
};

console.log(...objeto);
```

Este segmento de código está definiendo el objeto 'objeto' y con un console log está imprimiendo cada uno de sus atributos. Para lograrlo hace uso del ya mencionado spread operator.

Si ejecutamos ahora webpack, veremos que genera sin problemas el archivo de bundle, pero si intentamos ejecutarlo con node, veremos que nos genera errores.

Esto es porque la versión de javascript utilizada por Node no conoce el spread operator. Pero no nos preocupemos, para resolver este problema haremos uso de babel, el cuál es un transpiler que toma los features más nuevos de javascript y los traduce para que puedan ser utilizados por motores más antiguos.

Lo que hará webpack es hacer uso de babel antes de generar el bundle final y así preparar un archivo que Node sepa utilizar.

Lo que haremos es agregar el campo 'module' a nuestra configuración de webpack. Este campo consta de un objeto con un campo 'rules' que es un arreglo de objetos los cuales tienen la siguiente estructura:

```
{ 
    test: /\.txt$/, 
    use: "raw-loader" 
}
```

El campo test es una expresión regular para saber que archivos debe considerar al aplicar esa regla. El campo 'use' es el mecanismo o 'loader' que webpack usará para interpretar este archivo. En el ejemplo