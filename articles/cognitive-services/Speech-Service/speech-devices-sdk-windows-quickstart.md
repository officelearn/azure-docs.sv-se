---
title: 'Snabbstart: Kör tal enheter SDK i Windows-Speech-tjänsten'
titleSuffix: Azure Cognitive Services
description: Krav och anvisningar för att komma igång med en Windows Speech-enhet SDK.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/10/2019
ms.author: erhopf
ms.openlocfilehash: ad90a6443cc1c94bcdb730e783b82dfdd4798676
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68553040"
---
# <a name="quickstart-run-the-speech-devices-sdk-sample-app-on-windows"></a>Snabbstart: Kör exempel programmet för tal enheter SDK i Windows

I den här snabb starten lär du dig att använda tal enheter SDK för Windows för att bygga en tal aktive rad produkt eller använda den som en avskrifts enhet för [konversation](conversation-transcription-service.md) . För närvarande stöds endast [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/) .

Programmet har skapats med tal-SDK-paketet och Sol förmörkelse Java IDE (v4) på 64-bitars Windows. Det körs i en 64-bitars Java 8-körningsmiljö (JRE).

Den här guiden kräver ett [Azure Cognitive Services](get-started.md) -konto med en Speech Services-resurs. Om du inte har ett konto kan du använda den [kostnadsfria utvärderingsversionen](https://azure.microsoft.com/try/cognitive-services/) för att hämta en prenumerationsnyckel.

Käll koden för [exempel programmet](https://aka.ms/sdsdk-download-JRE) ingår i tal enheter SDK. Det är också [finns på GitHub](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Förutsättningar

För den här snabbstarten krävs:

* Operativsystem: 64-bitars Windows
* [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)
* [Eclipse Java IDE](https://www.eclipse.org/downloads/)
* Endast [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) eller [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html) .
* [Microsoft Visual C++ Redistributable](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)
* En Azure-prenumerationsnyckel för Speech Service. [Skaffa en kostnadsfritt](get-started.md).
* Ladda ned den senaste versionen av [tal enheter SDK](https://aka.ms/sdsdk-download-JRE) för Java och extrahera zip-filen till din arbets katalog.
   > [!NOTE]
   > JRE-Sample-Release. zip-filen innehåller exempel appen JRE och den här snabb starten förutsätter att appen extraheras till C:\SDSDK\JRE-Sample-Release

Konversations avskrift är för närvarande endast tillgängligt för "en-US" och "zh-CN" i regionerna "Central" och "asienöstra". Du måste ha en tal nyckel i någon av dessa regioner för att kunna använda konversations avskrifter.

Om du planerar att använda de avsikter behöver du en LUIS-prenumeration [(Language Understanding service)](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription) . Mer information om LUIS och avsikts igenkänning finns i [känna igen tal C# ](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp)insikter med Luis. En [exempel modell för Luis](https://aka.ms/sdsdk-luis) är tillgänglig för den här appen.

## <a name="create-and-configure-the-project"></a>Skapa och konfigurera projektet

1. Starta Eclipse.

1. I fältet **arbets yta** i **sol förmörkelses IDE-start**, anger du namnet på en ny arbets ytans katalog. Välj sedan **Starta**.

   ![Skärmbild av Eclipse-startfönstret](media/speech-devices-sdk/eclipse-launcher.png)

1. Efter en liten stund visas huvudfönstret i Eclipse IDE. Stäng välkomstskärmen om en sådan visas.

1. Skapa ett nytt projekt från meny raden för Sol förmörkelse genom att välja **Arkiv** > **nytt** > **Java-projekt**. Om det inte är tillgängligt väljer du **projekt** och sedan **Java-projekt**.

1. Guiden **nytt Java-projekt** startar. **Bläddra** efter exempel projektets plats. Välj **Slutför**.

   ![Skärmbild av guiden Nytt Java-projekt](media/speech-devices-sdk/eclipse-new-java-project.png)

1. Högerklicka på ditt projekt i **Package Explorer**. Välj **Konfigurera** > **Convert to Maven Project** (Konvertera till Maven-projekt) från snabbmenyn. Välj **Slutför**.

   ![Skärmbild av Paketutforskaren](media/speech-devices-sdk/eclipse-convert-to-maven.png)

1. Kopiera `kws.table`och  till projektmappen target\classes `participants.properties` `Microsoft.CognitiveServices.Speech.extension.pma.dll`

## <a name="configure-the-sample-application"></a>Konfigurera exempel programmet

1. Lägg till din tal prenumerations nyckel i käll koden. Om du vill prova taligenkänning kan också lägga till din [tjänst för Språkförståelse](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) prenumerationsnyckel och program-ID.

   För tal-och LUIS går din information till `FunctionsList.java`:

   ```java
    // Subscription
    private static String SpeechSubscriptionKey = "<enter your subscription info here>";
    private static String SpeechRegion = "westus"; // You can change this if your speech region is different.
    private static String LuisSubscriptionKey = "<enter your subscription info here>";
    private static String LuisRegion = "westus2"; // you can change this, if you want to test the intent, and your LUIS region is different.
    private static String LuisAppId = "<enter your LUIS AppId>";
   ```

    Om du använder en konversations avskrift krävs även din information om din röst nyckel och region `Cts.java`i:

   ```java
    private static final String CTSKey = "<Conversation Transcription Service Key>";
    private static final String CTSRegion="<Conversation Transcription Service Region>";// Region may be "centralus" or "eastasia"
    ```

1. Standard wake ordet (nyckelordet) är ”dator”. Du kan också prova något av de andra tillhandahålls wake ord, som till exempel ”dator” eller ”assistenten”. Resursfiler för dessa alternativa wake ord är i tal enheter SDK i mappen nyckelord. `C:\SDSDK\JRE-Sample-Release\keyword\Computer` Innehåller till exempel de filer som används för ordet "dator" i Wake.

   > [!TIP]
   > Du kan också [skapa en anpassad aktivering word](speech-devices-sdk-create-kws.md).

    Om du vill använda ett nytt aktiverings ord uppdaterar du följande två `FunctionsList.java`rader i och kopierar Väcknings ord paketet till din app. Om du till exempel vill använda Väcknings ordet "dator" från Väcknings ord paketet `kws-machine.zip`:

   * Kopiera Väcknings ord paketet till projektmappen **/-klasserna**i Project-mappen.

   * `FunctionsList.java` Uppdatera med nyckelordet och paket namnet:

     ```java
     private static final String Keyword = "Machine";
     private static final String KeywordModel = "kws-machine.zip" // set your own keyword package name.
     ```

## <a name="run-the-sample-application-from-eclipse"></a>Köra exempel programmet från Sol förmörkelse

1. I meny raden för Sol förmörkelse **Kör** > du**Kör som** > **Java-program**. Välj sedan **FunctionsList** och **OK**.

   ![Skärm bild av Välj Java-program](media/speech-devices-sdk/eclipse-run-sample.png)

1. Exempelprogram för tal Devices SDK startar och välja mellan följande alternativ:

   ![Exempelprogrammet tal Devices SDK exempel och alternativ](media/speech-devices-sdk/java-sample-app-windows.png)

1. Prova den nya  demonstrationen av konversations avskrift. Börja skriva med **session** > **Start**. Som standard är alla gäst. Men om du har deltagares röst-signaturer kan de placeras i en fil `participants.properties` i projektmappen **mål/klasser**. Om du vill generera röst signaturen tittar du på Skicka [konversationer (SDK)](how-to-use-conversation-transcription-service.md).

   ![Avskrifts program för demo konversation](media/speech-devices-sdk/cts-sample-app-windows.png)

## <a name="create-and-run-a-standalone-application"></a>Skapa och köra ett fristående program

1. Högerklicka på ditt projekt i **Package Explorer**. Välj **Exportera**. 

1. **Export** fönstret visas. Expandera **Java** och välj **körbara jar-fil** och välj sedan **Nästa**.

   ![Skärm bild av export fönstret](media/speech-devices-sdk/eclipse-export-windows.png) 

1. **KÖRBARA jar File export** -fönstret visas. Välj ett **export mål** för programmet och välj sedan **Slutför**.
 
   ![Skärm bild av körbara JAR File export](media/speech-devices-sdk/eclipse-export-jar-windows.png)

1. Lägg `kws.table`till `participants.properties` ,`pma.dll` , och imålmappensomväljsovansomdehärfilernabehövsavprogrammet.`Microsoft.CognitiveServices.Speech.extension.pma.dll` `unimic_runtime.dll`

1. Köra det fristående programmet

     ```powershell
     java -jar SpeechDemo.jar
     ```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Granska viktig information](devices-sdk-release-notes.md)
