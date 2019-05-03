---
title: 'Snabbstart: Kör tal enheter SDK på Android – Speech Services'
titleSuffix: Azure Cognitive Services
description: Krav och anvisningar för att komma igång med en Android-enheter SDK-versionen för tal.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: d5af2bb61eeb986f02a31d45ff9236ecc0c8427e
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026201"
---
# <a name="quickstart-run-the-speech-devices-sdk-sample-app-on-android"></a>Snabbstart: Kör exempelappen tal Devices SDK på Android

I den här snabbstarten lär du dig att använda tal Devices SDK för Android för att skapa en produkt med stöd för tal.

Den här guiden kräver en [Azure Cognitive Services](get-started.md) konto med en Speech Services-resurs. Om du inte har ett konto kan du använda den [kostnadsfria utvärderingsversionen](https://azure.microsoft.com/try/cognitive-services/) för att hämta en prenumerationsnyckel.

Källkoden för exempelprogrammet som ingår i SDK: N för tal-enheter. Det är också [finns på GitHub](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du börjar använda SDK: N för tal enheter, måste du:

* Följ instruktionerna som medföljer din [development kit](get-speech-devices-sdk.md) att starta enheten.

* Ladda ned den senaste versionen av den [tal Devices SDK](https://aka.ms/sdsdk-download), och extrahera ZIP till din arbetskatalog.
   > [!NOTE]
   > ZIP-filen innehåller Android-exempelappen.

* Att hämta en [prenumerationsnyckel för Azure för Speech Services](get-started.md)

* Om du planerar att använda Speech Services för att identifiera avsikter (eller åtgärder) från användaren yttranden, behöver du en [Language Understanding Service (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription) prenumeration. Läs mer om LUIS- och taligenkänning i [känna igen tal avsikter med LUIS kan C# ](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp).

    Du kan [skapa en enkel LUIS-modell](https://docs.microsoft.com/azure/cognitive-services/luis/) eller Använd exemplet LUIS-modell, LUIS-example.json. Exemplet LUIS-modellen är tillgänglig från den [tal Devices SDK hämtningsplats](https://aka.ms/sdsdk-luis). Ladda upp din modell JSON-filen till den [LUIS portal](https://www.luis.ai/home)väljer **importera ny app**, och välj sedan JSON-filen.

* Installera [Android Studio](https://developer.android.com/studio/) och [Vysor](https://vysor.io/download/) på din dator.

## <a name="set-up-the-device"></a>Konfigurera enheten

1. Starta Vysor på datorn.

    ![Vysor](media/speech-devices-sdk/qsg-3.png)

1. Enheten bör visas under **väljer du en enhet**. Välj den **visa** knappen bredvid enheten.

1. Ansluta till det trådlösa nätverket genom att välja mappikonen och välj sedan **inställningar** > **WLAN**.

    ![Vysor WLAN](media/speech-devices-sdk/qsg-4.png)

    > [!NOTE]
    > Om ditt företag har principer om att ansluta enheter till dess Wi-Fi-system, måste du skaffa MAC-adressen och Kontakta IT-avdelningen om hur du ansluter till ditt företags Wi-Fi.
    >
    > Välj filmappsikonen på skrivbordet för dev-paket för att hitta MAC-adressen för dev-paket.
    >
    >  ![Vysor filmapp](media/speech-devices-sdk/qsg-10.png)
    >
    > Välj **inställningar**. Sök efter ”mac-adress” och välj sedan **Mac-adress** > **avancerade WLAN**. Skriv ned MAC-adressen som visas längst ned i dialogrutan.
    >
    > ![Vysor MAC-adress](media/speech-devices-sdk/qsg-11.png)
    >
    > Vissa företag kan ha en tidsgräns på hur lång tid en enhet kan vara ansluten till deras Wi-Fi-system. Du kan behöva utöka dev-paket registreringen med din Wi-Fi-system efter ett visst antal dagar.

## <a name="run-the-sample-application"></a>Köra exempelprogrammet

Om du vill verifiera din development kit-konfiguration, skapa och installera exempelprogrammet:

1. Starta Android Studio.

1. Välja **Öppna ett befintligt Android Studio-projekt**.

   ![Android Studio – öppna ett befintligt projekt](media/speech-devices-sdk/qsg-5.png)

1. Gå till C:\SDSDK\Android-Sample-Release\example. Välj **OK** att öppna exempelprojektet.

1. Lägga till din prenumerationsnyckel för tal i källkoden. Om du vill prova taligenkänning kan också lägga till din [tjänst för Språkförståelse](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) prenumerationsnyckel och program-ID.

   Dina nycklar och information om programmet finns i följande rader i källfilen MainActivity.java:

   ```java
   // Subscription
   private static final String SpeechSubscriptionKey = "[your speech key]";
   private static final String SpeechRegion = "westus";
   private static final String LuisSubscriptionKey = "[your LUIS key]";
   private static final String LuisRegion = "westus2.api.cognitive.microsoft.com";
   private static final String LuisAppId = "[your LUIS app ID]"
   ```

1. Standard wake ordet (nyckelordet) är ”dator”. Du kan också prova något av de andra tillhandahålls wake ord, som till exempel ”dator” eller ”assistenten”. Resursfiler för dessa alternativa wake ord är i tal enheter SDK i mappen nyckelord. Till exempel innehåller C:\SDSDK\Android-Sample-Release\keyword\Computer de filer som används för wake ordet ”dator”.

   > [!TIP]
   > Du kan också [skapa en anpassad aktivering word](speech-devices-sdk-create-kws.md).

    För att använda ett nytt wake ord, uppdaterar du följande två rader i `MainActivity.java`, och kopiera wake word paketet till din app. Till exempel för att använda wake ordet ”Machine” från wake word paketet kws-machine.zip:

   * Kopiera wake word paketet till mappen ”C:\SDSDK\Android-Sample-Release\example\app\src\main\assets\”.
   * Uppdatera den `MainActivity.java` med nyckelordet och paketets namn:

     ```java
     private static final String Keyword = "Machine";
     private static final String KeywordModel = "kws-machine.zip" // set your own keyword package name.
     ```

1. Uppdatera följande rader, som innehåller inställningar för mikrofon lagringsmatris geometri:

   ```java
   private static final String DeviceGeometry = "Circular6+1";
   private static final String SelectedGeometry = "Circular6+1";
   ```

   Den här tabellen innehåller värden som stöds:

   |Variabel|Betydelse|Tillgängliga värden|
   |--------|-------|----------------|
   |`DeviceGeometry`|Fysiska mic-konfiguration|För en cirkulär dev-paket: `Circular6+1` |
   |||För en linjär dev-paket: `Linear4`|
   |`SelectedGeometry`|Mic programvarukonfiguration|För en cirkulär dev-paket använder som alla mikrofoner: `Circular6+1`|
   |||För en cirkulär dev-paket använder som fyra mikrofoner: `Circular3+1`|
   |||För en linjär dev-paket använder som alla mikrofoner: `Linear4`|
   |||För en linjär dev-paket använder som två mikrofoner: `Linear2`|

1. Att skapa programmet på den **kör** menyn och välj **kör ”app”**. Den **Välj distributionsmålet** dialogrutan visas.

1. Välj din enhet och välj sedan **OK** du distribuerar program till enheten.

    ![Välj dialogrutan distribution mål](media/speech-devices-sdk/qsg-7.png)

1. Exempelprogram för tal Devices SDK startar och välja mellan följande alternativ:

   ![Exempelprogrammet tal Devices SDK exempel och alternativ](media/speech-devices-sdk/qsg-8.png)

1. Experiment!

## <a name="troubleshooting"></a>Felsökning

   Om du inte kan ansluta till tal-enheten. Skriv följande kommando i Kommandotolken. Den returnerar en lista över enheter:

   ```powershell
    adb devices
   ```

   > [!NOTE]
   > Detta kommando använder Android Debug-bryggan, `adb.exe`, vilket är en del av Android Studio-installationen. Det här verktyget finns i C:\Users\[användarnamn] \AppData\Local\Android\Sdk\platform-verktyg. Du kan lägga till den här katalogen till sökvägen att göra det mer praktiskt att anropa `adb`. Annars måste du ange den fullständiga sökvägen för din installation av adb.exe i alla kommandon som anropar `adb`.
   >
   > Om du ser ett fel `no devices/emulators found` och sedan kontrollera USB-kabeln är ansluten och se till att en högkvalitativ kabel används.
   >

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Granska viktig information](devices-sdk-release-notes.md)
