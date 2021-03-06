---
title: ¿Qué es Personalizer?
titleSuffix: Azure Cognitive Services
description: Personalizer es un servicio de API basado en la nube que permite elegir la mejor experiencia, mostrársela a los usuarios y aprender de su comportamiento en tiempo real.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 10/23/2019
ms.author: diberry
ms.openlocfilehash: 6b55ce851bb12e37aed37039889aa8e69223a286
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467189"
---
# <a name="what-is-personalizer"></a>¿Qué es Personalizer?

Azure Personalizer es un servicio de API basado en la nube que permite elegir la mejor experiencia, mostrársela a los usuarios y aprender de su comportamiento colectivo en tiempo real.

* Proporcione información acerca de los usuarios y del contenido, y reciba la principal acción que va a mostrar a los usuarios. 
* Para utilizar Personalizer no es preciso limpiar y etiquetar los datos.
* Proporcione los comentarios a Personalizer cuando le resulte más cómodo. 
* Vea análisis en tiempo real. 

Vea una demostración de [cómo funciona Personalizer](https://personalizercontentdemo.azurewebsites.net/).

## <a name="how-does-personalizer-work"></a>Funcionamiento de Personalizer

Personalizer utiliza modelos de aprendizaje automático para detectar qué acción se debe colocar la primera en un contexto. La aplicación cliente proporciona una lista de posibles acciones, con información sobre ellas, así como información acerca del contexto, que puede incluir información sobre el usuario, dispositivo, etc. Personalizer determina la acción que se va a realizar. Una vez que la aplicación cliente use la acción elegida, proporciona comentarios a Personalizer en forma de puntuación de recompensa. Tras recibir los comentarios, Personalizer actualiza automáticamente el modelo propio que ha utilizado para usarlo en el futuro. Con el tiempo, Personalizer entrenará un modelo que puede sugerir la acción más apropiada para cada contexto en función de sus características.

## <a name="how-do-i-use-the-personalizer"></a>¿Cómo se usa Personalizer?

![Uso de Personalizer para elegir el vídeo que se va a mostrar a un usuario](media/what-is-personalizer/personalizer-example-highlevel.png)

1. Elija en su aplicación lo que desea personalizar.
1. Cree y configure una instancia del servicio Personalization en Azure Portal. Cada instancia es un bucle de Personalizer.
1. Use [Rank API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank) para llamar a Personalizer con información (_características_) sobre los usuarios y el contenido (_acciones_). Para usar Personalizer no es preciso proporcionar datos limpios y con etiqueta. Se puede llamar a las API directamente o usar los SDK disponibles para los diferentes lenguajes de programación.
1. En la aplicación cliente, muestre al usuario la acción que ha seleccionado Personalizer.
1. Use [Reward API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward) para proporcionar comentarios a Personalizer para indicar si el usuario seleccionó la acción de Personalizer. Esta es una _[puntuación de recompensa](concept-rewards.md)_ .
1. Vea el análisis en Azure Portal y evalúe el funcionamiento del sistema y cómo ayudan los datos a la personalización.

## <a name="where-can-i-use-personalizer"></a>¿Dónde se puede utilizar Personalizer?

Por ejemplo, una aplicación cliente puede agregar Personalizer para:

* Personalizar qué artículo se resalta en un sitio web de noticias.    
* Optimizar la colocación de anuncios en un sitio web.
* Mostrar un "elemento recomendado" personalizado en un sitio web de compras.
* Sugerir elementos de la interfaz de usuario, como filtros, para aplicarlos a una foto concreta.
* Elegir la respuesta de un bot de chat para clarificar la intención del usuario o sugerir una acción.
* Dar prioridad a las sugerencias relativas a lo debe hacer un usuario en el siguiente paso de un proceso empresarial.

Personalizer no es un servicio para conservar y administrar la información de los perfiles de los usuarios, ni para registrar las preferencias o el historial de cada usuario. Personalizer aprende de las características de cada interacción de la acción de un contexto en un modelo único que puede obtener las máximas recompensas cuando aparecen características similares. 

## <a name="personalization-for-developers"></a>Personalization para desarrolladores

El servicio Personalizer tiene dos API:

* Envíe información (_características_) acerca de los usuarios y el contenido (_acciones_) para realizar la personalización. Personalizer responde con la acción principal.
* Envíe a Personalizer sus comentarios sobre el funcionamiento de la clasificación como [puntuación de recompensa](concept-rewards.md). 

![Secuencia básica de eventos para Personalization](media/what-is-personalizer/personalization-intro.png)

## <a name="next-steps"></a>Pasos siguientes

* [Novedades de Personalizer](whats-new.md)
* [Funcionamiento de Personalizer](how-personalizer-works.md)
* [¿Qué es el aprendizaje de refuerzo?](concepts-reinforcement-learning.md)
* [Más información sobre las características y acciones de la solicitud de Rank](concepts-features.md)
* [Más información sobre cómo determinar la puntuación de la solicitud de Reward](concept-rewards.md)
* [Uso de la demostración interactiva](https://personalizationdemo.azurewebsites.net/)
