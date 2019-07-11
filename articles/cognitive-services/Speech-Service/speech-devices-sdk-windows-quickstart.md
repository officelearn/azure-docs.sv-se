---
title: 'Snabbstart: Kör tal enheter SDK på Windows - Taltjänster'
titleSuffix: Azure Cognitive Services
description: Krav och anvisningar för att komma igång med en Windows tal Devices SDK.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/10/2019
ms.author: erhopf
ms.openlocfilehash: ac0ed218aa27a36b3b8cd8ed8123e2baef6948c6
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723386"
---
# <a name="quickstart-run-the-speech-devices-sdk-sample-app-on-windows"></a>Snabbstart: Kör exempelappen tal Devices SDK på Windows

I den här snabbstarten får du lära dig hur du använder tal enheter SDK för Windows för att skapa en talbaserade produkt eller använda det som en [konversationen avskrift](conversation-transcription-service.md) enhet. För närvarande bara den [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/) stöds.

Programmet har byggts med tal SDK-paketet och Eclipse Java IDE (v4) på 64-bitars Windows. Det körs i en 64-bitars Java 8-körningsmiljö (JRE).

Den här guiden kräver en [Azure Cognitive Services](get-started.md) konto med en Speech Services-resurs. Om du inte har ett konto kan du använda den [kostnadsfria utvärderingsversionen](https://azure.microsoft.com/try/cognitive-services/) för att hämta en prenumerationsnyckel.

Källkoden för den [exempelprogrammet](https://aka.ms/sdsdk-download-JRE) ingår i SDK: N för tal-enheter. Det är också [finns på GitHub](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Förutsättningar

För den här snabbstarten krävs:

* Operativsystem: 64-bitars Windows
* [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)
* [Eclipse Java IDE](https://www.eclipse.org/downloads/)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) eller [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html) endast.
* [Microsoft Visual C++ Redistributable](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)
* En Azure-prenumerationsnyckel för Speech Service. [Skaffa en kostnadsfritt](get-started.md).
* Ladda ned den senaste versionen av den [tal Devices SDK](https://aka.ms/sdsdk-download-JRE) för Java och extrahera .zip till din arbetskatalog.
   > [!NOTE]
   > JRE-exempel – Release.zip-filen innehåller JRE-exempelapp och den här snabbstarten förutsätter att appen ska extraheras till C:\SDSDK\JRE-Sample-Release

Konversationen avskrift finns för närvarande endast för ”en-US” och ”zh-CN”, i regionerna ”centralus” och ”asienöstra”. Du måste ha en tal-nyckel i någon av dessa regioner för att använda konversationen avskrift.

Om du planerar att använda avsikter behöver du en [Language Understanding Service (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription) prenumeration. Läs mer om LUIS- och taligenkänning i [känna igen tal avsikter med LUIS kan C# ](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp). En [exempel LUIS-modellen](https://aka.ms/sdsdk-luis) är tillgänglig för den här appen.

## <a name="create-and-configure-the-project"></a>Skapa och konfigurera projektet

1. Starta Eclipse.

1. I den **Eclipse IDE starta**i den **arbetsytan** fältet, anger du namnet på en ny katalog för arbetsytan. Välj sedan **Starta**.

   ![Skärmbild av Eclipse-startfönstret](media/speech-devices-sdk/eclipse-launcher.png)

1. Efter en liten stund visas huvudfönstret i Eclipse IDE. Stäng välkomstskärmen om en sådan visas.

1. Skapa ett nytt projekt genom att välja från menyraden Eclipse **filen** > **New** > **Java-projekt**. Om det är inte tillgängligt väljer **projekt** och sedan **Java-projekt**.

1. Den **ny Java-projekt** guiden startar. **Bläddra** för platsen för exempelprojektet. Välj **Slutför**.

   ![Skärmbild av guiden Nytt Java-projekt](media/speech-devices-sdk/eclipse-new-java-project.png)

1. I den **Package explorer**, högerklicka på ditt projekt. Välj **Konfigurera** > **Convert to Maven Project** (Konvertera till Maven-projekt) från snabbmenyn. Välj **Slutför**.

   ![Skärmbild av Paketutforskaren](media/speech-devices-sdk/eclipse-convert-to-maven.png)

1. Kopiera `kws.table`, `participants.properties` och `Microsoft.CognitiveServices.Speech.extension.pma.dll` till projektmappen **target\classes**

## <a name="configure-the-sample-application"></a>Konfigurera exempelprogrammet

1. Lägga till din prenumerationsnyckel för tal i källkoden. Om du vill prova taligenkänning kan också lägga till din [tjänst för Språkförståelse](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) prenumerationsnyckel och program-ID.

   För tal och LUIS informationen hamnar i `FunctionsList.java`:

   ```java
    // Subscription
    private static String SpeechSubscriptionKey = "<enter your subscription info here>";
    private static String SpeechRegion = "westus"; // You can change this if your speech region is different.
    private static String LuisSubscriptionKey = "<enter your subscription info here>";
    private static String LuisRegion = "westus2"; // you can change this, if you want to test the intent, and your LUIS region is different.
    private static String LuisAppId = "<enter your LUIS AppId>";
   ```

    Om du använder konversationen avskrift krävs också informationen om tal nyckel och region i `Cts.java`:

   ```java
    private static final String CTSKey = "<Conversation Transcription Service Key>";
    private static final String CTSRegion="<Conversation Transcription Service Region>";// Region may be "centralus" or "eastasia"
    ```

1. Standard wake ordet (nyckelordet) är ”dator”. Du kan också prova något av de andra tillhandahålls wake ord, som till exempel ”dator” eller ”assistenten”. Resursfiler för dessa alternativa wake ord är i tal enheter SDK i mappen nyckelord. Till exempel `C:\SDSDK\JRE-Sample-Release\keyword\Computer` innehåller de filer som används för wake ordet ”dator”.

   > [!TIP]
   > Du kan också [skapa en anpassad aktivering word](speech-devices-sdk-create-kws.md).

    För att använda ett nytt wake ord, uppdaterar du följande två rader i `FunctionsList.java`, och kopiera wake word paketet till din app. Till exempel för att använda wake ordet ”Machine” från word-paketet wake `kws-machine.zip`:

   * Kopiera wake word paketet till projektmappen **målklasserna/** .

   * Uppdatera den `FunctionsList.java` med nyckelordet och paketets namn:

     ```java
     private static final String Keyword = "Machine";
     private static final String KeywordModel = "kws-machine.zip" // set your own keyword package name.
     ```

## <a name="run-the-sample-application-from-eclipse"></a>Kör exempelprogrammet från Eclipse

1. Från menyraden Eclipse **kör** > **kör som** > **Java-program**. Välj sedan **FunctionsList** och **OK**.

   ![Skärmbild av väljer Java-program](media/speech-devices-sdk/eclipse-run-sample.png)

1. Exempelprogram för tal Devices SDK startar och välja mellan följande alternativ:

   ![Exempelprogrammet tal Devices SDK exempel och alternativ](media/speech-devices-sdk/java-sample-app-windows.png)

1. Prova den nya **konversationen avskrift** demo. Börja skriva med **Session** > **starta**. Som standard är alla gäst. Men om du har deltagarens röst signaturer de kan placeras i en fil `participants.properties` i projektmappen **målklasserna/** . Om du vill skapa signaturen som röst, titta på [transkribera konversationer (SDK)](how-to-use-conversation-transcription-service.md).

   ![Konversationen avskrift demoprogram](media/speech-devices-sdk/cts-sample-app-windows.png)

## <a name="create-and-run-a-standalone-application"></a>Skapa och köra ett fristående program

1. I den **Package explorer**, högerklicka på ditt projekt. Välj **exportera**. 

1. Den **exportera** fönster visas. Expandera **Java** och välj **att köra flödet JAR-fil** och välj sedan **nästa**.

   ![Skärmbild av fönstret Export](media/speech-devices-sdk/eclipse-export-windows.png) 

1. Den **att köra flödet JAR-filen exportera** fönster visas. Välj en **exportera** för programmet och välj sedan **Slutför**.
 
   ![Skärmbild av Export för att köra flödet JAR-filen](media/speech-devices-sdk/eclipse-export-jar-windows.png)

1. . Placera `kws.table`, `participants.properties`, `unimic_runtime.dll`, `pma.dll` och `Microsoft.CognitiveServices.Speech.extension.pma.dll` i målmappen som valts ovan eftersom filerna som krävs av programmet.

1. Att köra fristående program

     ```powershell
     java -jar SpeechDemo.jar
     ```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Granska viktig information](devices-sdk-release-notes.md)
