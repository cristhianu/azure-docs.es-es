---
title: EXP en el lenguaje de consulta de Azure Cosmos DB
description: Obtenga información sobre la función del sistema SQL EXP en Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 21c7ae63f46f2acd961245c59805220174c106f1
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351048"
---
# <a name="exp-azure-cosmos-db"></a>EXP (Azure Cosmos DB)
 Devuelve el valor exponencial de la expresión numérica especificada.  
  
## <a name="syntax"></a>Sintaxis
  
```sql
EXP (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*numeric_expr*  
   Es una expresión numérica.  
  
## <a name="return-types"></a>Tipos de valor devuelto
  
  Devuelve una expresión numérica.  
  
## <a name="remarks"></a>Comentarios
  
  La constante **e** (2,718281 …) es la base de los logaritmos naturales.  
  
  El exponente de un número es la constante **e** elevada a la potencia del número. Por ejemplo EXP(1,0) = e^1,0 = 2,71828182845905 y EXP(10) = e^10 = 22026,4657948067.  
  
  El valor exponencial del logaritmo natural de un número es el mismo número: EXP (LOG [n]) = n. Y el logaritmo natural del valor exponencial de un número es el mismo número: LOG (EXP (n)) = n.  
  
## <a name="examples"></a>Ejemplos
  
  En el ejemplo siguiente se declara una variable y se devuelve el valor exponencial de la variable especificada (10).  
  
```sql
SELECT EXP(10) AS exp  
```  
  
 El conjunto de resultados es el siguiente:  
  
```json
[{exp: 22026.465794806718}]  
```  
  
 En el ejemplo siguiente se devuelve el valor exponencial del logaritmo natural de 20 y el logaritmo natural del valor exponencial de 20. Dado que estas funciones son inversas entre sí, el valor devuelto con redondeo para las operaciones matemáticas de punto flotante en ambos casos es 20.  
  
```sql
SELECT EXP(LOG(20)) AS exp1, LOG(EXP(20)) AS exp2  
```  
  
 El conjunto de resultados es el siguiente:  
  
```json
[{exp1: 19.999999999999996, exp2: 20}]  
```  

## <a name="next-steps"></a>Pasos siguientes

- [Funciones matemáticas (Azure Cosmos DB)](sql-query-mathematical-functions.md)
- [Funciones del sistema (Azure Cosmos DB)](sql-query-system-functions.md)
- [Introducción a Azure Cosmos DB](introduction.md)
