---
title: 'Guía de inicio rápido: Configuración del cifrado SSL de un extremo a otro con Azure Application Gateway: Azure Portal | Microsoft Docs'
description: Aprenda a usar Azure Portal para crear una puerta de enlace de aplicaciones con el cifrado SSL de un extremo a otro.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/30/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: ba31b5ebf83edcd08060a2acc3b5639a521e2729
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243666"
---
# <a name="configure-end-to-end-ssl-by-using-application-gateway-with-the-portal"></a>Configuración de SSL de un extremo a otro con Application Gateway mediante el portal

En este artículo se describe cómo usar Azure Portal para configurar el cifrado de Capa de sockets seguros (SSL) de un extremo a otro a través de una SKU de Application Gateway v1.

> [!NOTE]
> La SKU de Application Gateway v2 requiere certificados raíz de confianza para habilitar configuración de un extremo a otro.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="before-you-begin"></a>Antes de empezar

Para configurar SSL de un extremo a otro con una puerta de enlace de aplicaciones, se necesita un certificado para la puerta de enlace. También se necesitan certificados para los servidores back-end. El certificado de puerta de enlace se usa para derivar una clave simétrica en cumplimiento de la especificación del protocolo SSL. A continuación, la clave simétrica se usa para cifrar y descifrar el tráfico que se envía a la puerta de enlace. 

Para el cifrado SSL de un extremo a otro, los servidores back-end correctos se deben permitir en Application Gateway. Para permitir este acceso, cargue los certificados públicos de los servidores back-end, también conocidos como certificados de autenticación (v1) o certificados raíz de confianza (v2), en la puerta de enlace de aplicaciones. Al agregar el certificado, se garantiza que la puerta de enlace de aplicaciones solo se comunique con instancias back-end conocidas. Esta configuración protege aún más la comunicación de un extremo a otro.

Para más información, consulte [Terminación SSL y SSL de un extremo a otro](https://docs.microsoft.com/azure/application-gateway/ssl-overview).

## <a name="create-a-new-application-gateway-with-end-to-end-ssl"></a>Crear una nueva instancia de Application Gateway con SSL de un extremo a otro

Para crear una nueva instancia de Application Gateway con el cifrado SSL de un extremo a otro, deberá habilitar primero la terminación SSL durante la creación de una nueva instancia de Application Gateway. Esta acción habilita el cifrado SSL para la comunicación entre el cliente y la puerta de enlace de aplicaciones. A continuación, deberá incluir en la lista de destinatarios seguros los certificados de los servidores back-end de la configuración HTTP. Esta configuración habilita el cifrado SSL para la comunicación entre la puerta de enlace de aplicaciones y los servidores back-end. Así se consigue el cifrado SSL de un extremo a otro.

### <a name="enable-ssl-termination-while-creating-a-new-application-gateway"></a>Habilitar la terminación SSL durante la creación de una nueva instancia de Application Gateway

Para más información, consulte [Habilitación de la terminación SSL durante la creación de una nueva puerta de enlace de aplicaciones](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal).

### <a name="add-authenticationroot-certificates-of-back-end-servers"></a>Incorporación de certificados de autenticación o certificados raíz de los servidores back-end

1. Seleccione **Todos los recursos** y, después, seleccione **myAppGateway**.

2. Seleccione **Configuración HTTP** en el menú de la izquierda. Azure creó automáticamente una configuración HTTP predeterminada, **appGatewayBackendHttpSettings** cuando creó la puerta de enlace de aplicaciones. 

3. Seleccione **appGatewayBackendHttpSettings**.

4. En **Protocolo**, seleccione **HTTPS**. Aparece un panel para los **Certificados de autenticación back-end o Certificados raíz de confianza**.

5. Seleccione **Crear nuevo**.

6. En el campo **Nombre**, escriba un nombre adecuado.

7. Seleccione el archivo de certificado en el cuadro **Cargar certificado CER**.

   En puertas de enlace de aplicaciones Standard y WAF (v1), debe cargar la clave pública del certificado de servidor back-end con formato .cer.

   ![Agregar certificado](./media/end-to-end-ssl-portal/addcert.png)

   En las puertas de enlace de aplicaciones Standard_v2 y WAF_v2, debe cargar el certificado raíz del certificado de servidor back-end con formato .cer. Si el certificado de back-end lo emite una entidad de certificación (CA) conocida, puede seleccionar la casilla **Usar el certificado de una entidad de certificación reconocida** y así no tiene que cargar un certificado.

   ![Incorporación de un certificado raíz de confianza](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

   ![Certificado raíz](./media/end-to-end-ssl-portal/trustedrootcert.png)

8. Seleccione **Guardar**.

## <a name="enable-end-to-end-ssl-for-an-existing-application-gateway"></a>Habilitación de SSL de un extremo a otro para una puerta de enlace de aplicaciones existente

Para configurar una puerta de enlace de aplicaciones existente con cifrado SSL de un extremo a otro, deberá habilitar primero la terminación SSL en el cliente de escucha. Esta acción habilita el cifrado SSL para la comunicación entre el cliente y la puerta de enlace de aplicaciones. A continuación, incluya los certificados de los servidores back-end de la configuración HTTP en la lista de destinatarios seguros. Esta configuración habilita el cifrado SSL para la comunicación entre la puerta de enlace de aplicaciones y los servidores back-end. Así se consigue el cifrado SSL de un extremo a otro.

Para habilitar la terminación SSL, deberá usar un cliente de escucha con el protocolo HTTPS y un certificado. Puede usar un cliente de escucha existente que cumpla esas condiciones o bien crear uno nuevo. Si elige la primera opción, puede omitir la sección "Habilitación de la terminación SSL en una puerta de enlace de aplicaciones existente" y pasar directamente a la sección "Incorporación de certificados de autenticación o raíz de confianza de servidores back-end".

Si elige la segunda opción, aplique los pasos del siguiente procedimiento.
### <a name="enable-ssl-termination-in-an-existing-application-gateway"></a>Habilitación de la terminación SSL en una puerta de enlace de aplicaciones existente

1. Seleccione **Todos los recursos** y, después, seleccione **myAppGateway**.

2. Seleccione **Clientes de escucha** en el menú de la izquierda.

3. Seleccione **Básico** o **Multisitio**, en función de sus requisitos.

4. En **Protocolo**, seleccione **HTTPS**. Aparece un panel para **Certificado**.

5. Cargue el certificado PFX que va a usar para la terminación SSL entre el cliente y la puerta de enlace de aplicaciones.

   > [!NOTE]
   > Para fines de prueba, puede usar un certificado autofirmado. Sin embargo, estos certificados no se recomiendan para las cargas de trabajo de producción, ya que son más difíciles de administrar y no son completamente seguros. Para más información, consulte [Creación de un certificado autofirmado](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal#create-a-self-signed-certificate).

6. Agregue otras opciones necesarias para el **Cliente de escucha**, en función de sus requisitos.

7. Seleccione **Aceptar** para guardar.

### <a name="add-authenticationtrusted-root-certificates-of-back-end-servers"></a>Incorporación de certificados de autenticación o raíz de confianza de servidores back-end

1. Seleccione **Todos los recursos** y, después, seleccione **myAppGateway**.

2. Seleccione **Configuración HTTP** en el menú de la izquierda. Puede incluir en la lista de destinatarios seguros certificados de una configuración HTTP de back-end existente o crear un nuevo valor HTTP. En el paso siguiente se agrega a la lista de destinatarios seguros el certificado de la configuración HTTP predeterminada, **appGatewayBackendHttpSettings**.

3. Seleccione **appGatewayBackendHttpSettings**.

4. En **Protocolo**, seleccione **HTTPS**. Aparece un panel para los **Certificados de autenticación back-end o Certificados raíz de confianza**. 

5. Seleccione **Crear nuevo**.

6. En el campo **Nombre**, escriba un nombre adecuado.

7. Seleccione el archivo de certificado en el cuadro **Cargar certificado CER**.

   En puertas de enlace de aplicaciones Standard y WAF (v1), debe cargar la clave pública del certificado de servidor back-end con formato .cer.

   ![Agregar certificado](./media/end-to-end-ssl-portal/addcert.png)

   En las puertas de enlace de aplicaciones Standard_v2 y WAF_v2, debe cargar el certificado raíz del certificado de servidor back-end con formato .cer. Si el certificado de back-end lo emite una entidad de certificación conocida, puede seleccionar la casilla **Usar el certificado de una entidad de certificación reconocida** y así no tiene que cargar un certificado.

   ![Incorporación de un certificado raíz de confianza](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

8. Seleccione **Guardar**.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Administrar el tráfico web con Application Gateway mediante la CLI de Azure](./tutorial-manage-web-traffic-cli.md)
