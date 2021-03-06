---
title: Diseño de flujos de trabajo de directiva como código
description: Aprenda a diseñar flujos de trabajo para implementar sus definiciones de Azure Policy como código y validar automáticamente los recursos.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: 935616145ee32bea8c3b514c495690ce4105cb80
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/14/2019
ms.locfileid: "74072072"
---
# <a name="design-policy-as-code-workflows"></a>Diseño de flujos de trabajo de directiva como código

A medida que avanza en el recorrido con el gobierno en la nube, querrá cambiar de la administración manual de definición de cada directiva en Azure Portal o a través de los distintos SDK a algo más fácil de administrar y repetir a escala empresarial. Dos de los enfoques predominantes para administrar sistemas a escala en la nube son:

- Infraestructura como código: Práctica de tratar el contenido que define los entornos, desde plantillas de Resource Manager, pasando por definiciones de Azure Policy, hasta Azure Blueprints, como código fuente.
- DevOps: Unión de personas, procesos y productos para hacer posible la entrega continua de valor a los usuarios finales.

La directiva como código es la combinación de estas ideas. En esencia, mantenga las definiciones de directivas en el control de código fuente y, cada vez que se realice un cambio, pruebe y valide ese cambio. Sin embargo, eso no debe ser el alcance de la implicación de las directivas con la infraestructura como código ni DevOps.

El paso de validación también debe ser un componente de otros flujos de trabajo de integración continua o implementación continua. Entre los ejemplos se incluye la implementación de un entorno de aplicación o una infraestructura virtual. Al hacer que la validación de Azure Policy sea un componente temprano del proceso de compilación e implementación, los equipos de operaciones y de aplicaciones detectan si sus cambios cumplen o no, mucho antes de que sea demasiado tarde y estén intentando implementar en producción.

## <a name="workflow-overview"></a>Introducción al flujo de trabajo

El flujo de trabajo general recomendado de la directiva como código es similar al de este diagrama:

![Información general del flujo de trabajo de directiva como código](../media/policy-as-code/policy-as-code-workflow.png)

### <a name="create-and-update-policy-definitions"></a>Crear y actualizar definiciones de directivas

Las definiciones de directiva se crean mediante JSON y se almacenan en el control de código fuente. Cada directiva tiene su propio conjunto de archivos, como los parámetros, las reglas y los parámetros de entorno, que se deben almacenar en la misma carpeta. La estructura siguiente es una manera recomendada de mantener las definiciones de directiva en el control de código fuente.

```text
.
|
|- policies/  ________________________ # Root folder for policies
|  |- policy1/  ______________________ # Subfolder for a policy
|     |- policy.json _________________ # Policy definition
|     |- policy.parameters.json ______ # Policy definition of parameters
|     |- policy.rules.json ___________ # Policy rule
|     |- params.dev.json _____________ # Parameters for a Dev environment
|     |- params.prd.json _____________ # Parameters for a Prod environment
|     |- params.tst.json _____________ # Parameters for a Test environment
|
|  |- policy2/  ______________________ # Subfolder for a policy
|     |- policy.json _________________ # Policy definition
|     |- policy.parameters.json ______ # Policy definition of parameters
|     |- policy.rules.json ___________ # Policy rule
|     |- params.dev.json _____________ # Parameters for a Dev environment
|     |- params.prd.json _____________ # Parameters for a Prod environment
|     |- params.tst.json _____________ # Parameters for a Test environment
|
```

Cuando se agrega una nueva directiva o se actualiza una existente, el flujo de trabajo debe actualizar automáticamente la definición de la directiva en Azure. Las pruebas de la definición de la directiva nueva o actualizada se incluyen en un paso posterior.

### <a name="create-and-update-initiative-definitions"></a>Creación y actualización de definiciones de iniciativas

Del mismo modo, las iniciativas tienen su propio archivo JSON y archivos relacionados que se deben almacenar en la misma carpeta. La definición de la iniciativa requiere que ya exista la definición de la directiva, por lo que no se puede crear ni actualizar hasta que el origen de la directiva se haya actualizado en el control de código fuente y se haya actualizado luego en Azure. La estructura siguiente es una manera recomendada de mantener las definiciones de iniciativa en el control de código fuente:

```text
.
|
|- initiatives/ ______________________ # Root folder for initiatives
|  |- init1/ _________________________ # Subfolder for an initiative
|     |- policyset.json ______________ # Initiative definition
|     |- policyset.definitions.json __ # Initiative list of policies
|     |- policyset.parameters.json ___ # Initiative definition of parameters
|     |- params.dev.json _____________ # Parameters for a Dev environment
|     |- params.prd.json _____________ # Parameters for a Prod environment
|     |- params.tst.json _____________ # Parameters for a Test environment
|
|  |- init2/ _________________________ # Subfolder for an initiative
|     |- policyset.json ______________ # Initiative definition
|     |- policyset.definitions.json __ # Initiative list of policies
|     |- policyset.parameters.json ___ # Initiative definition of parameters
|     |- params.dev.json _____________ # Parameters for a Dev environment
|     |- params.prd.json _____________ # Parameters for a Prod environment
|     |- params.tst.json _____________ # Parameters for a Test environment
|
```

Al igual que las definiciones de directiva, al agregar o actualizar una iniciativa existente, el flujo de trabajo debe actualizar automáticamente la definición de la iniciativa en Azure. Las pruebas de la definición de la iniciativa nueva o actualizada se incluyen en un paso posterior.

### <a name="test-and-validate-the-updated-definition"></a>Prueba y validación de la definición actualizada

Una vez que la automatización ha tomado las definiciones de directivas o de iniciativas recién creadas o actualizadas, y ha realizado la actualización del objeto en Azure, es el momento de probar los cambios realizados. La directiva o las iniciativas a las que pertenece deben asignarse a los recursos del entorno más alejado de producción. Este entorno suele ser _Dev_.

La asignación debe utilizar [enforcementMode](./assignment-structure.md#enforcement-mode) en _disabled_ para que la creación de recursos y las actualizaciones no se bloqueen, pero que los recursos existentes sigan siendo auditados para el cumplimiento de la definición de directiva actualizada. Incluso con enforcementMode, se recomienda que el ámbito de asignación sea un grupo de recursos o una suscripción que se use específicamente para validar directivas.

> [!NOTE]
> Aunque el modo de cumplimiento es útil, no es un sustituto para probar exhaustivamente la definición de una directiva en varias condiciones. La definición de la directiva se debe probar con las llamadas API de REST `PUT` y `PATCH`, recursos compatibles y no compatibles, y casos extremos, como una propiedad que falte en el recurso.

Una vez implementada la asignación, use el SDK de directivas para [obtener los datos de cumplimiento](../how-to/get-compliance-data.md) para la nueva asignación. El entorno que se usa para probar las directivas y asignaciones debe tener recursos compatibles y no compatibles. Al igual que una buena prueba unitaria del código, desea probar que los recursos son los esperados y que tampoco tiene falsos positivos ni falsos negativos. Si prueba y valida solo lo que espera, puede haber un impacto inesperado y no identificado de la directiva. Para obtener más información, consulte [Evaluación del impacto de una nueva directiva de Azure](./evaluate-impact.md).

### <a name="enable-remediation-tasks"></a>Habilitación de tareas de corrección

Si la validación de la asignación cumple las expectativas, el paso siguiente es validar la corrección.
Las directivas que usan [deployIfNotExists](./effects.md#deployifnotexists) o [modify](./effects.md#modify) se pueden convertir en una tarea de corrección y corregir los recursos desde un estado no compatible.

El primer paso para hacerlo es conceder a la asignación de directiva la asignación de roles definida en la definición de la directiva. Esta asignación de roles permite que la identidad administrada de la asignación de directivas tenga suficientes derechos para realizar los cambios necesarios para que el recurso sea compatible.

Una vez que la asignación de directiva tiene los derechos adecuados, use el SDK de directivas para desencadenar una tarea de corrección con respecto a un conjunto de recursos que se sabe que no son compatibles. Se deben completar tres pruebas en estas tareas corregidas antes de continuar:

- Validar que la tarea de corrección se completó correctamente
- Ejecutar la evaluación de directivas para ver que los resultados de cumplimiento de la directiva se actualicen según lo previsto
- Ejecutar una prueba unitaria de entorno con los recursos directamente para validar que sus propiedades han cambiado

Probar los resultados de la evaluación de la directiva actualizada y el entorno directamente ofrece la confirmación de que las tareas de corrección cambiaron lo que se esperaba, y que la definición de la directiva vio el cambio de cumplimiento según lo esperado.

### <a name="update-to-enforced-assignments"></a>Actualizar a asignaciones aplicadas

Una vez completadas todas las validaciones, actualice la asignación para utilizar **enforcementMode** en _enabled_. Este cambio debe realizarse inicialmente en el mismo entorno alejado de producción. Una vez que se valida que ese entorno funciona según lo previsto, se debe definir el ámbito del cambio para que incluya el siguiente entorno y así sucesivamente hasta que la directiva se implemente en los recursos de producción.

## <a name="process-integrated-evaluations"></a>Procesamiento de evaluaciones integradas

El flujo de trabajo general de la directiva como código es desarrollar e implementar directivas e iniciativas en un entorno a escala. Sin embargo, la evaluación de directivas debe formar parte del proceso de implementación de cualquier flujo de trabajo que implemente o cree recursos en Azure, como la implementación de aplicaciones o la ejecución de plantillas de Resource Manager para crear la infraestructura.

En estos casos, una vez que la implementación de la aplicación o infraestructura se ha completado para una suscripción o grupo de recursos de prueba, se debe realizar la evaluación de la directiva para ese ámbito mediante la comprobación de la validación de todas las directivas e iniciativas existentes. Aunque es posible que se configuren como **enforcementMode** _disabled_ en este entorno, es útil saber pronto si la implementación de una aplicación o infraestructura infringe las definiciones de directivas en un principio. Por lo tanto, esta evaluación de directivas debe ser un paso en esos flujos de trabajo, y hacer fracasar las implementaciones que crean recursos no compatibles.

## <a name="review"></a>Revisión

En este artículo se describe el flujo de trabajo general de la directiva como código, así como la evaluación de la directiva que debe formar parte de otros flujos de trabajo de implementación. Este flujo de trabajo se puede usar en cualquier entorno que admita pasos con scripts y automatización basada en desencadenadores.

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre la [estructura de la definición de directiva](./definition-structure.md).
- Más información sobre la [estructura de asignaciones de directivas](./assignment-structure.md)
- Obtenga información acerca de cómo se pueden [crear directivas mediante programación](../how-to/programmatically-create.md).
- Obtenga información sobre cómo [obtener datos de cumplimiento](../how-to/getting-compliance-data.md).
- Obtenga información sobre cómo [corregir recursos no compatibles](../how-to/remediate-resources.md).
- En [Organización de los recursos con grupos de administración de Azure](../../management-groups/overview.md), obtendrá información sobre lo que es un grupo de administración.