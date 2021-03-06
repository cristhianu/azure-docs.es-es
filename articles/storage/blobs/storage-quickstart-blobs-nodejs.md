---
title: 'Inicio rápido: Biblioteca de Azure Blob Storage v12: JavaScript'
description: En esta guía de inicio rápido, obtendrá información sobre cómo usar la biblioteca cliente de Azure Blob Storage versión 12 para JavaScript a fin de crear un contenedor y un blob en Blob Storage (objeto). A continuación, aprenderá a descargar el blob en un equipo local y a enumerar todos los blobs en un contenedor.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/05/2019
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 2e5a5f2a4de4e01d2e4fa66f819e55839959afd0
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2019
ms.locfileid: "74130699"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-for-javascript"></a>Inicio rápido: Biblioteca cliente de Azure Blob Storage v12 para JavaScript

Introducción a la biblioteca cliente de Azure Blob Storage v12 para JavaScript. Azure Blob Storage es la solución de almacenamiento de objetos de Microsoft para la nube. Siga los pasos para instalar el paquete y probar el código de ejemplo para realizar tareas básicas. Blob Storage está optimizado para el almacenamiento de cantidades masivas de datos no estructurados.

> [!NOTE]
> Para empezar a trabajar con la versión anterior del SDK, consulte [Inicio rápido: Biblioteca cliente de Azure Blob Storage para JavaScript](storage-quickstart-blobs-nodejs-v10.md).

Use la biblioteca cliente de Azure Blob Storage v12 para JavaScript para:

* Crear un contenedor
* Cargar un blob en Azure Storage
* Enumerar todos los blobs de un contenedor
* Descargar el blob en el equipo local
* Eliminación de un contenedor

[Documentación de referencia de API](/javascript/api/@azure/storage-blob) | [Código fuente de la biblioteca](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob) | [Paquete (administrador de paquetes de Node)](https://www.npmjs.com/package/@azure/storage-blob/v/12.0.0) | [Ejemplos](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob/samples)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Requisitos previos

* Una suscripción a Azure: [cree una cuenta gratuita](https://azure.microsoft.com/free/)
* Una cuenta de Azure Storage: [cree una cuenta de almacenamiento](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* [Node.js](https://nodejs.org/en/download/) actual del sistema operativo.

## <a name="setting-up"></a>Instalación

En esta sección se explica cómo preparar un proyecto para que funcione con la biblioteca cliente de Azure Blob Storage v12 para JavaScript.

### <a name="create-the-project"></a>Creación del proyecto

Cree una aplicación de JavaScript llamada *blob-quickstart-v12*.

1. En una ventana de la consola (como cmd, PowerShell o Bash), cree un directorio para el proyecto.

    ```console
    mkdir blob-quickstart-v12
    ```

1. Cambie al directorio *blob-quickstart-v12* recién creado.

    ```console
    cd blob-quickstart-v12
    ```

1. Cree un nuevo archivo de texto denominado *package.json*. Este archivo define el proyecto de Node.js. Guarde este archivo en el directorio *blob-quickstart-v12*. A continuación encontrará el contenido del archivo:

    ```json
    {
        "name": "blob-quickstart-v12",
        "version": "1.0.0",
        "description": "Use the @azure/storage-blob SDK version 12 to interact with Azure Blob storage",
        "main": "blob-quickstart-v12.js",
        "scripts": {
            "start": "node blob-quickstart-v12.js"
        },
        "author": "Your Name",
        "license": "MIT",
        "dependencies": {
            "@azure/storage-blob": "^12.0.0",
            "@types/dotenv": "^4.0.3",
            "dotenv": "^6.0.0"
        }
    }
    ```
    
    Puede poner su propio nombre en el campo `author`, si lo desea.
   
### <a name="install-the-package"></a>Instalación del paquete

Mientras sigue en el directorio *blob-quickstart-v12*, instale el paquete de la biblioteca cliente de Azure Blob Storage para JavaScript con el comando `npm install`. Este comando lee el archivo *package.json* e instala el paquete de la biblioteca cliente de Azure Blob Storage v12 para JavaScript y todas las bibliotecas de las que depende.

```console
npm install
```

### <a name="set-up-the-app-framework"></a>Instalación del marco de la aplicación

Desde el directorio del proyecto:

1. Abra otro nuevo archivo de texto en el editor de código.
1. Agregue llamadas `require` para cargar los módulos de Azure y Node.js.
1. Cree la estructura del programa, incluido un control de excepciones muy básico.

    Este es el código:

    ```javascript
    const { BlobServiceClient } = require('@azure/storage-blob');
    const uuidv1 = require('uuid/v1');
    
    async function main() {
        console.log('Azure Blob storage v12 - Javascript quickstart sample');
        // Quick start code goes here
    }
    
    main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));
    ```

1. Guarde el nuevo archivo como *blob-quickstart-v12.js* en el directorio *blob-quickstart-v12*.

### <a name="copy-your-credentials-from-the-azure-portal"></a>Copia de las credenciales desde Azure Portal

Cuando la aplicación de ejemplo realiza una solicitud a Azure Storage, debe estar autorizada. Para autorizar una solicitud, agregue a la aplicación las credenciales de la cuenta de almacenamiento en forma de cadena de conexión. Para ver las credenciales de la cuenta de almacenamiento, siga estos pasos:

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
2. Busque su cuenta de almacenamiento.
3. En la sección **Configuración** de la información general de la cuenta de almacenamiento, seleccione **Claves de acceso**. Aquí puede ver las claves de acceso de la cuenta, así como la cadena de conexión completa de cada clave.
4. Busque el valor de **Cadena de conexión** en **key1** y seleccione el botón **Copiar** para copiar la cadena de conexión. En el paso siguiente, agregará el valor de la cadena de conexión a una variable de entorno.

    ![Captura de pantalla que muestra cómo copiar una cadena de conexión desde Azure Portal](../../../includes/media/storage-copy-connection-string-portal/portal-connection-string.png)

### <a name="configure-your-storage-connection-string"></a>Configuración de la cadena de conexión de almacenamiento.

Una vez que haya copiado la cadena de conexión, escríbala en una variable de entorno nueva en la máquina local que ejecuta la aplicación. Para establecer la variable de entorno, abra una ventana de consola y siga las instrucciones de su sistema operativo. Reemplace `<yourconnectionstring>` por la cadena de conexión real.

#### <a name="windows"></a>Windows

```cmd
setx CONNECT_STR "<yourconnectionstring>"
```

Después de agregar la variable de entorno en Windows, debe iniciar una nueva instancia de la ventana de comandos.

#### <a name="linux"></a>Linux

```bash
export CONNECT_STR="<yourconnectionstring>"
```

#### <a name="macos"></a>macOS

```bash
export CONNECT_STR="<yourconnectionstring>"
```

#### <a name="restart-programs"></a>Reiniciar programas

Después de agregar la variable de entorno, reinicie todos los programas en ejecución que necesiten leer esta variable. Por ejemplo, reinicie el entorno de desarrollo o el editor antes de continuar.

## <a name="object-model"></a>Modelo de objetos

Azure Blob Storage está optimizado para el almacenamiento de cantidades masivas de datos no estructurados. Los datos no estructurados son datos que no cumplen un modelo de datos o definición concreta, como texto o datos binarios. Blob Storage ofrece tres tipos de recursos:

* La cuenta de almacenamiento
* Un contenedor en la cuenta de almacenamiento
* Un blob en el contenedor

En el siguiente diagrama se muestra la relación entre estos recursos.

![Diagrama de arquitectura de Blob Storage](./media/storage-blob-introduction/blob1.png)

Use las siguientes clases de JavaScript para interactuar con estos recursos:

* [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient): La clase `BlobServiceClient` permite manipular recursos de Azure Storage y contenedores de blobs.
* [ContainerClient](/javascript/api/@azure/storage-blob/containerclient): La clase `ContainerClient` permite manipular contenedores de Azure Storage y sus blobs.
* [BlobClient](/javascript/api/@azure/storage-blob/blobclient): La clase `BlobClient` permite manipular los blobs de Azure Storage.

## <a name="code-examples"></a>Ejemplos de código

Estos fragmentos de código de ejemplo muestran cómo realizar las siguientes acciones con la biblioteca cliente de Azure Blob Storage para JavaScript:

* [Obtención de la cadena de conexión](#get-the-connection-string)
* [Creación de un contenedor](#create-a-container)
* [Carga de los blobs en un contenedor](#upload-blobs-to-a-container)
* [Enumeración de los blobs de un contenedor](#list-the-blobs-in-a-container)
* [Descarga de los blobs](#download-blobs)
* [Eliminación de un contenedor](#delete-a-container)

### <a name="get-the-connection-string"></a>Obtención de la cadena de conexión

El código siguiente recupera la cadena de conexión de la cuenta de almacenamiento de la variable de entorno creada en la sección [Configuración de la cadena de conexión de almacenamiento](#configure-your-storage-connection-string).

Agregue este código dentro de la función `main`:

```javascript
// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called CONNECT_STR. If the environment variable is
// created after the application is launched in a console or with Visual Studio,
// the shell or application needs to be closed and reloaded to take the
// environment variable into account.
const CONNECT_STR = process.env.CONNECT_STR;
```

### <a name="create-a-container"></a>Crear un contenedor

Decida un nombre para el nuevo contenedor. El código siguiente anexa un valor de UUID al nombre de contenedor para asegurarse de que sea único.

> [!IMPORTANT]
> Los nombres de contenedor deben estar en minúsculas. Para más información acerca de los contenedores de nomenclatura y los blobs, consulte [Naming and Referencing Containers, Blobs, and Metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Asignación de nombres y realización de referencias a contenedores, blobs y metadatos).

Cree una instancia de la clase [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient) llamando al método [fromConnectionString](/javascript/api/@azure/storage-blob/blobserviceclient#fromconnectionstring-string--storagepipelineoptions-). A continuación, llame al método [getContainerClient](/javascript/api/@azure/storage-blob/blobserviceclient#getcontainerclient-string-) para obtener una referencia a un contenedor. Por último, llame a [create](/javascript/api/@azure/storage-blob/containerclient#create-containercreateoptions-) para crear realmente el contenedor en la cuenta de almacenamiento.

Agregue este código al final de la función `main`:

```javascript
// Create the BlobServiceClient object which will be used to create a container client
const blobServiceClient = await new BlobServiceClient.fromConnectionString(CONNECT_STR);

// Create a unique name for the container
const containerName = 'quickstart' + uuidv1();

console.log('\nCreating container...');
console.log('\t', containerName);

// Get a reference to a container
const containerClient = await blobServiceClient.getContainerClient(containerName);

// Create the container
await containerClient.create();
```

### <a name="upload-blobs-to-a-container"></a>Carga de los blobs en un contenedor

El siguiente fragmento de código:

1. Crea una cadena de texto que se va a cargar en un blob.
1. Obtiene una referencia a un objeto [BlockBlobClient](/javascript/api/@azure/storage-blob/blockblobclient) llamando al método [getBlockBlobClient](/javascript/api/@azure/storage-blob/containerclient#getblockblobclient-string-) en el [ContainerClient](/javascript/api/@azure/storage-blob/containerclient) de la sección [Crear un contenedor](#create-a-container).
1. Carga los datos de la cadena de texto en el blob llamando al método [upload](/javascript/api/@azure/storage-blob/blockblobclient#upload-httprequestbody--number--blockblobuploadoptions-).

Agregue este código al final de la función `main`:

```javascript
// Create a unique name for the blob
const blobName = 'quickstart' + uuidv1() + '.txt';

// Get a block blob client
const blockBlobClient = containerClient.getBlockBlobClient(blobName);

console.log('\nUploading to Azure Storage as blob:\n\t', blobName);

// Upload data to the blob
const data = 'Hello, World!';
await blockBlobClient.upload(data, data.length);
```

### <a name="list-the-blobs-in-a-container"></a>Enumerar los blobs de un contenedor

Enumere los blobs en el contenedor llamando al método [listBlobsFlat](/javascript/api/@azure/storage-blob/containerclient#listblobsflat-containerlistblobsoptions-). En este caso, solo se ha agregado un blob al contenedor, por lo que la operación de enumeración devuelve simplemente dicho blob.

Agregue este código al final de la función `main`:

```javascript
console.log('\nListing blobs...');

// List the blob(s) in the container.
for await (const blob of containerClient.listBlobsFlat()) {
    console.log('\t', blob.name);
}
```

### <a name="download-blobs"></a>Descargar blobs

Descargue el blob creado previamente llamando al método [download](/javascript/api/@azure/storage-blob/blockblobclient#download-undefined---number--undefined---number--blobdownloadoptions-). El código de ejemplo incluye una función auxiliar denominada `streamToString` que se usa para leer una secuencia legible de Node.js en una cadena.

Agregue este código al final de la función `main`:

```javascript
// Get blob content from position 0 to the end
// In Node.js, get downloaded data by accessing downloadBlockBlobResponse.readableStreamBody
// In browsers, get downloaded data by accessing downloadBlockBlobResponse.blobBody
const downloadBlockBlobResponse = await blockBlobClient.download(0);
console.log('\nDownloaded blob content...');
console.log('\t', await streamToString(downloadBlockBlobResponse.readableStreamBody));
```

Agregue esta función auxiliar *después* de la función `main`:

```javascript
// A helper function used to read a Node.js readable stream into a string
async function streamToString(readableStream) {
  return new Promise((resolve, reject) => {
    const chunks = [];
    readableStream.on("data", (data) => {
      chunks.push(data.toString());
    });
    readableStream.on("end", () => {
      resolve(chunks.join(""));
    });
    readableStream.on("error", reject);
  });
}
```

### <a name="delete-a-container"></a>Eliminación de un contenedor

El código siguiente limpia los recursos que creó la aplicación; para ello, quita todo el contenedor con el método [delete](/javascript/api/@azure/storage-blob/containerclient#delete-containerdeletemethodoptions-). Si lo desea, también puede eliminar los archivos locales.

Agregue este código al final de la función `main`:

```javascript
console.log('\nDeleting container...');

// Delete container
await containerClient.delete();
```

## <a name="run-the-code"></a>Ejecución del código

Esta aplicación crea una cadena de texto y la carga en Blob Storage. A continuación, en el ejemplo se enumeran los blobs del contenedor, se descarga el blob y se muestran los datos descargados.

Desde un símbolo del sistema de la consola, navegue hasta el directorio que contiene el archivo *blob-quickstart-v12.py* y, a continuación, ejecute el siguiente comando de `node` para ejecutar la aplicación.

```console
node blob-quickstart-v12.js
```

La salida de la aplicación es similar a la del ejemplo siguiente:

```output
Azure Blob storage v12 - JavaScript quickstart sample

Creating container...
         quickstart4a0780c0-fb72-11e9-b7b9-b387d3c488da

Uploading to Azure Storage as blob:
         quickstart4a3128d0-fb72-11e9-b7b9-b387d3c488da.txt

Listing blobs...
         quickstart4a3128d0-fb72-11e9-b7b9-b387d3c488da.txt

Downloaded blob content...
         Hello, World!

Deleting container...
Done
```

Recorra el código en el depurador y compruebe Azure Portal a lo largo del proceso. Consulte para ver si el contenedor se está creando. Puede abrir el blob dentro del contenedor y ver el contenido.

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, ha aprendido a cargar, descargar y enumerar blobs mediante JavaScript.

Para ver las aplicaciones de ejemplo de Blob Storage, siga estos pasos:

> [!div class="nextstepaction"]
> [Ejemplos de Azure Blob Storage v12 para JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob/samples)

* Para más información, consulte el [SDK de Azure para JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/README.md).
* Para ver tutoriales, ejemplos, guías de inicio rápido y otra documentación, visite [Documentación de Azure SDK para JavaScript](/azure/javascript/).
