---
title: 'Solución de problemas de un cliente VPN de autenticación Azure AD: Azure VPN Gateway | Microsoft Docs'
description: Solución de problemas de clientes de autenticación de VPN Gateway P2S Azure AD
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: e239f283d5065e032e98e87a72a4bfe9fbafbdb1
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2019
ms.locfileid: "73511653"
---
# <a name="troubleshoot-an-azure-ad-authentication-vpn-client"></a>Solución de problemas de un cliente VPN de autenticación Azure AD

Este artículo le ayuda a solucionar problemas de un cliente VPN para conectarse a una red virtual con una VPN de punto a sitio y la autenticación Azure Active Directory.

## <a name="status"></a>Ver Registro de estado

Vea el registro de estado para ver los mensajes de error.

![logs](./media/troubleshoot-ad-vpn-client/1.png)

1. Haga clic en el icono de flechas situado en la esquina inferior derecha de la ventana de cliente para mostrar los **Registros de estado**.
2. Compruebe los registros en busca de errores que puedan indicar el problema.
3. Los mensajes de error se muestran en rojo.

## <a name="clear"></a>Borrar información de inicio de sesión

Borrar información de inicio de sesión.

![iniciar sesión](./media/troubleshoot-ad-vpn-client/2.png)

1. Seleccione... junto al perfil que quiere solucionar. Seleccione **Configurar > Borrar cuenta guardada**.
2. Seleccione **Guardar**.
3. Intente conectarse.
4. Si la conexión sigue sin funcionar, continúe con siguiente sección.

## <a name="diagnostics"></a>Ejecución de diagnósticos

Ejecutar diagnósticos en el cliente VPN.

![diagnóstico](./media/troubleshoot-ad-vpn-client/3.png)

1. Haga clic en **…** junto al perfil en el que desea ejecutar diagnósticos. Seleccione **Diagnosticar-> Ejecutar diagnósticos**.
2. El cliente ejecutará una serie de pruebas y mostrará el resultado de la prueba

   * Acceso a Internet: comprueba si el cliente tiene Conectividad a Internet
   * Credenciales del cliente: comprueba si el punto de conexión de autenticación Azure Active Directory es accesible
   * Resolución del servidor: se pone en contacto con el servidor DNS para resolver la dirección IP del servidor VPN configurado
   * Disponibilidad del servidor: comprueba si el servidor VPN responde o no
3. Si se produce un error en alguna de las pruebas, póngase en contacto con el administrador de red para resolver el problema.
4. En la sección siguiente se muestra cómo recopilar los registros, si es necesario.

## <a name="logfiles"></a>Recopilar archivos de registro del cliente

Recopilar archivos de registro del cliente VPN. Los archivos de registro se pueden enviar al soporte técnico o al administrador a través de un método de su elección. Por ejemplo, correo electrónico.

1. Haga clic en "..." junto al perfil en el que desea ejecutar diagnósticos. Seleccione **Diagnosticar > Mostrar directorio de registros**.

   ![mostrar registros](./media/troubleshoot-ad-vpn-client/4.png)
2. El explorador de Windows se abre en la carpeta que contiene los archivos de registro.

   ![ver archivo](./media/troubleshoot-ad-vpn-client/5.png)

## <a name="next-steps"></a>Pasos siguientes

Para más información, consulte [crear un inquilino de Azure Active Directory para conexiones VPN abiertas de P2S que usan Autenticación de Azure AD](openvpn-azure-ad-tenant.md).