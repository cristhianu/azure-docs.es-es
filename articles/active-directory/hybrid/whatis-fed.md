---
title: ¿En qué consiste la federación con Azure AD? | Microsoft Docs
description: Describe la federación con Azure AD.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/28/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: aeb8886987c09b504262d6ea12863a646d6022cc
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/09/2019
ms.locfileid: "73883650"
---
# <a name="what-is-federation-with-azure-ad"></a>¿En qué consiste la federación con Azure AD?

La federación es una colección de dominios que han establecido confianza. El nivel de confianza puede variar, pero normalmente incluye la autenticación y casi siempre la autorización. Una federación típica podría incluir un número de organizaciones que han establecido confianza para el acceso compartido a un conjunto de recursos.

Puede federar el entorno local con Azure AD y usar esta federación para la autenticación y autorización.  Este método de inicio de sesión garantiza que toda la autenticación de usuarios tiene lugar de forma local.  Este método permite a los administradores implementar niveles más rigurosos de control de acceso. Está disponible la federación con AD FS y PingFederate.

![Identidad federada](./media/whatis-hybrid-identity/federated-identity.png)


> [!TIP]
> Si opta por usar Federación con Servicios de federación de Active Directory (AD FS), tiene la posibilidad de configurar la sincronización de hash de contraseñas como copia de seguridad en caso de error en la infraestructura de AD FS.


## <a name="next-steps"></a>Pasos siguientes

- [¿Qué es la identidad híbrida?](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity)
- [¿Qué es Azure AD Connect y Connect Health?](whatis-azure-ad-connect.md)
- [¿Qué es la sincronización de hash de contraseñas?](whatis-phs.md)
- [¿Qué es la federación?](whatis-fed.md)
- [¿Qué es el inicio de sesión único?](how-to-connect-sso.md)
- [Cómo funciona la federación](how-to-connect-fed-whatis.md)
- [Federación con PingFederate](how-to-connect-install-custom.md#configuring-federation-with-pingfederate)
