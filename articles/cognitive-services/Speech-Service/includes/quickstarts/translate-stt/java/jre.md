---
title: 'Inicio rápido: Traducción de voz a texto en Java para Windows y Linux (Servicios de voz)'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: c9c793a19a97326f5098a2f6d228a542ebf45570
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2019
ms.locfileid: "73505055"
---
## <a name="prerequisites"></a>Requisitos previos

Antes de comenzar, compruebe lo siguiente:

> [!div class="checklist"]
> * [Ha creado un recurso de Voz de Azure](../../../../get-started.md)
> * [Ha configurado el entorno de desarrollo](../../../../quickstarts/setup-platform.md?tabs=jre)
> * [Ha creado un proyecto de ejemplo vacío](../../../../quickstarts/create-project.md?tabs=jre)

## <a name="add-sample-code"></a>Incorporación de código de ejemplo

1. Para agregar una nueva clase vacía al proyecto de Java, seleccione **File (Archivo)**  >  **New (Nuevo)**  >  **Class (Clase)** .

1. En la ventana **New Java Class** (Nueva clase de Java) escriba **speechsdk.quickstart** en el campo **Package** (Paquete) y **Main** en el campo **Name** (Nombre).

   ![Captura de pantalla de la ventana New Java Class (Nueva clase de Java)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-06-create-main-java.png)

1. Reemplace el código en `Main.java` con el siguiente fragmento de código:

   ```Java
   package quickstart;

   import java.io.IOException;
   import java.util.concurrent.Future;
   import java.util.concurrent.ExecutionException;
   import com.microsoft.cognitiveservices.speech.*;
   import com.microsoft.cognitiveservices.speech.translation.*;

   public class Main {

       public static void translationWithMicrophoneAsync() throws InterruptedException, ExecutionException, IOException
       {
           // Creates an instance of a speech translation config with specified
           // subscription key and service region. Replace with your own subscription key
           // and service region (e.g., "westus").

           int exitCode = 1;
           SpeechTranslationConfig config = SpeechTranslationConfig.fromSubscription(("YourSubscriptionKey",  "YourServiceRegion");
           assert(config != null);

           // Sets source and target languages.
           String fromLanguage = "en-US";
           String toLanguage = "de";
           config.setSpeechRecognitionLanguage(fromLanguage);
           config.addTargetLanguage(toLanguage);

           // Creates a translation recognizer using the default microphone audio input device.
           TranslationRecognizer recognizer = new TranslationRecognizer(config);
           assert(recognizer != null);

           System.out.println("Say something...");

           // Starts translation, and returns after a single utterance is recognized. The end of a
           // single utterance is determined by listening for silence at the end or until a maximum of 15
           // seconds of audio is processed. The task returns the recognized text as well as the translation.
           // Note: Since recognizeOnceAsync() returns only a single utterance, it is suitable only for single
           // shot recognition like command or query.
           // For long-running multi-utterance recognition, use startContinuousRecognitionAsync() instead.
           Future<TranslationRecognitionResult> task = recognizer.recognizeOnceAsync();
           assert(task != null);

           TranslationRecognitionResult result = task.get();
           assert(result != null);

           if (result.getReason() == ResultReason.TranslatedSpeech) {
               System.out.println("RECOGNIZED '" + fromLanguage + "': " + result.getText());
               System.out.println("TRANSLATED into '" + toLanguage + "': " + result.getTranslations().get(toLanguage));
               exitCode = 0;
           }
           else if (result.getReason() == ResultReason.RecognizedSpeech) {
               System.out.println("RECOGNIZED '" + fromLanguage + "': " + result.getText() + "(text could not be translated)");
               exitCode = 0;
           }
           else if (result.getReason() == ResultReason.NoMatch) {
               System.out.println("NOMATCH: Speech could not be recognized.");
           }
           else if (result.getReason() == ResultReason.Canceled) {
               CancellationDetails cancellation = CancellationDetails.fromResult(result);
               System.out.println("CANCELED: Reason=" + cancellation.getReason());

               if (cancellation.getReason() == CancellationReason.Error) {
                   System.out.println("CANCELED: ErrorCode=" + cancellation.getErrorCode());
                   System.out.println("CANCELED: ErrorDetails=" + cancellation.getErrorDetails());
                   System.out.println("CANCELED: Did you update the subscription info?");
               }
           }

           recognizer.close();

           System.exit(exitCode);
       }

       public static void main(String[] args) {
           try {
               translationWithMicrophoneAsync();
           } catch (Exception ex) {
               System.out.println("Unexpected exception: " + ex.getMessage());
               assert(false);
               System.exit(1);
           }
       }
   }
   ```

1. Reemplace la cadena `YourSubscriptionKey` por la clave de suscripción.

1. Reemplace la cadena `YourServiceRegion` por la [región](~/articles/cognitive-services/Speech-Service/regions.md) asociada con la suscripción (por ejemplo, `westus` para la suscripción de evaluación gratuita).

1. Guarde los cambios en el proyecto.

## <a name="build-and-run-the-app"></a>Compilación y ejecución de la aplicación

Presione F11, o seleccione **Run (Ejecutar)**  > **Debug (Depurar)** .

1. Diga una oración o frase en inglés. La aplicación transmite su voz a Speech Services, que la traduce y la transcribe a texto, en este caso, en alemán. Después, Speech Services envían el texto de nuevo a la aplicación para que se muestren.

````
Say something...
RECOGNIZED 'en-US': What's the weather in Seattle?
TRANSLATED into 'de': Wie ist das Wetter in Seattle?
````

## <a name="next-steps"></a>Pasos siguientes

[!INCLUDE [footer](./footer.md)]
