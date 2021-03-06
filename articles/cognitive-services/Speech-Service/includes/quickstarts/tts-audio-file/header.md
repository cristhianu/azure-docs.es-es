---
title: 'Inicio rápido: Síntesis de voz en un archivo de audio: servicio Voz'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: f69c00f9cf787a192c62f12a707927c5c51a1d31
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2019
ms.locfileid: "73504927"
---
En este inicio rápido, usará el SDK de [Voz](~/articles/cognitive-services/speech-service/speech-sdk.md) para convertir texto en voz sintetizada en un archivo de audio. Una vez que se cumplen los requisitos previos, la síntesis de voz en un archivo solo necesita realizar estos cinco pasos:
> [!div class="checklist"]
> * Cree un objeto ````SpeechConfig```` a partir de la clave y la región de suscripción.
> * Cree un objeto de configuración de audio que especifique el nombre del archivo .WAV.
> * Cree un objeto ````SpeechSynthesizer```` con los objetos de configuración anteriores.
> * Con el objeto ````SpeechSynthesizer````, convierta el texto en voz sintetizada y guárdelo en el archivo de audio especificado.
> * Inspeccione el objeto ````SpeechSynthesizer```` devuelto en caso de errores.
