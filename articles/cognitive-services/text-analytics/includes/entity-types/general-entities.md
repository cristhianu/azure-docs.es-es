---
title: Entidades con nombre generales
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/18/2019
ms.author: aahi
ms.openlocfilehash: 693a81cfb15407541311d7ab053bb2ab6a267b29
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/07/2019
ms.locfileid: "73800136"
---
## <a name="general-entity-types"></a>Tipos de entidades generales:

### <a name="person"></a>Persona
Nombres reconocidos y otras personas en el texto.
Lenguajes:
* Versión preliminar pública: `English`

| Nombre del subtipo | DESCRIPCIÓN             |
|--------------|-------------------------|
| N/D          | Nombres reconocidos, como `Bill Gates` o `Marie Curie` |

### <a name="location"></a>Location

Puntos de referencia, estructuras y características geográficas naturales y artificiales.

Lenguajes:


* Versión preliminar pública: `English`

| Nombre del subtipo | DESCRIPCIÓN                                                                                      |
|--------------|--------------------------------------------------------------------------------------------------|
| N/D          | Organizaciones, como `Atlantic Ocean`, `library`, `Eiffel Tower` o `Statue of Liberty` |

### <a name="organization"></a>Organización  

Organizaciones, corporaciones, agencias y otros grupos de personas reconocidos. Por ejemplo: empresas, grupos políticos, bandas musicales, clubs deportivos, organismos gubernamentales y organizaciones públicas. Las nacionalidades y las religiones no se incluyen en este tipo de entidad. Lenguajes: 

* Versión preliminar pública: `English`

| Nombre del subtipo | DESCRIPCIÓN                                                                                      |
|--------------|--------------------------------------------------------------------------------------------------|
| N/D          | Organizaciones, como `Microsoft`, `NASA` `National Oceanic and Atmospheric Administration` |

### <a name="phone-number"></a>Número de teléfono

Números de teléfono. 

Lenguajes:


* Versión preliminar pública: `English`

| Nombre del subtipo | DESCRIPCIÓN                                  |
|----------|----------------------------------------------|
| N/D         | Números de teléfono, como `+1 123-123-123`. |

### <a name="email"></a>Email

Dirección de correo electrónico. 

Lenguajes:


* Versión preliminar pública: `English`

| Nombre del subtipo | DESCRIPCIÓN                                  |
|----------|----------------------------------------------|
| N/D         | Dirección de correo electrónico, por ejemplo `support@contoso.com` |

### <a name="url"></a>URL

Direcciones URL de Internet.

Lenguajes:


* Versión preliminar pública: `English`

| Nombre del subtipo | DESCRIPCIÓN                                           |
|----------|-------------------------------------------------------|
| N/D         | Direcciones URL a sitios web, como `https://www.bing.com`. |

###  <a name="number"></a>Number

Números y cantidades numéricas. 

Lenguajes:


* Versión preliminar pública: `English`

| Nombre del subtipo    | Ejemplos                     |
|-------------|------------------------------|
| N/D         | `6`, `six`                   |
| Porcentaje  | `50%`, `fifty percent`       |
| Ordinal     | `2nd`, `second`              |
| Moneda    | `$10.99`, `€30.00`           |
| Dimension Data   | `10 miles`, `40 cm`          |
| Temperatura | `32 degrees`, `10°C`         |
