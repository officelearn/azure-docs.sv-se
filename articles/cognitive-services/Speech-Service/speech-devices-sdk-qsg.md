---
title: Kom igång med tal enheter SDK - Speech Services
titleSuffix: Azure Cognitive Services
description: Krav och anvisningar för att komma igång med SDK för tal-enheter.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 4c01cf93fe3bb66f9bce73acb3c2f100764d1f46
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55872551"
---
# <a name="get-started-with-the-speech-devices-sdk"></a>Kom igång med SDK för tal-enheter

Den här artikeln beskriver hur du konfigurerar din utveckling PC och tal Utvecklingskit för enheten för att utveckla tal-aktiverade enheter med hjälp av SDK för tal-enheter. Sedan skapar och distribuerar ett exempelprogram till enheten.

Källkoden för exempelprogrammet som ingår i SDK: N för tal-enheter. Det är också [finns på GitHub](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar utveckla med tal Devices SDK måste du samla in information och programvara som du behöver:

* Hämta en [development kit från ROOBO](http://ddk.roobo.com/). Startpaket är tillgängliga med linjära eller cirkulär mikrofon matris konfigurationer. Välj rätt konfigurationen för dina behov.

    |Development kit konfiguration|Talare plats|
    |-----------------------------|------------|
    |Cirkulär|Oavsett riktning från enheten|
    |Linjär|Framför enheten|

* Hämta den senaste versionen av tal enheter SDK, som innehåller en exempelapp för Android, från den [tal Devices SDK hämtningsplats](https://shares.datatransfer.microsoft.com/). Extrahera ZIP-filen till en lokal mapp, som C:\SDSDK.

* Installera [Android Studio](https://developer.android.com/studio/) och [Vysor](http://vysor.io/download/) på din dator.

* Hämta en [Speech service prenumerationsnyckel](get-started.md). Du kan hämta en 30-dagars kostnadsfri utvärderingsversion eller hämta en nyckel från instrumentpanelen i Azure.

* Om du vill använda tjänsten tal taligenkänning prenumerera på den [tjänst för Språkförståelse](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) (LUIS) och [få en prenumerationsnyckel](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription).

    Du kan [skapa en enkel LUIS-modell](https://docs.microsoft.com/azure/cognitive-services/luis/) eller Använd exemplet LUIS-modell, LUIS-example.json. Exemplet LUIS-modellen är tillgänglig från den [tal Devices SDK hämtningsplats](https://shares.datatransfer.microsoft.com/). Ladda upp din modell JSON-filen till den [LUIS portal](https://www.luis.ai/home)väljer **importera ny app**, och välj sedan JSON-filen.

## <a name="set-up-the-development-kit"></a>Ställ in i development kit
    
1. I development kit har två micro USB-anslutningar. Den vänstra anslutningen är att köra i development kit och markeras som kraften i bilden nedan. Höger en är ta kontroll över den och markeras felsöka i avbildningen.

    ![ansluta dev-paket](media/speech-devices-sdk/qsg-1.png)
       
1. Power i development kit med micro USB-kabel för att ansluta power-port till en dator eller driva nätverkskort. En grön power indikator tänds under den översta tavlan.

1. Om du vill styra hur ansluter kit debug-port till en dator med hjälp av en andra micro USB-kabel. Det är viktigt att använda en högkvalitativ kabel för att säkerställa tillförlitlig kommunikation.

1. Förstå din Utvecklingskit för antingen cirkulär eller linjär konfigurationen.

    |Development kit konfiguration|Orientering|
    |-----------------------------|------------|
    |Cirkulär|Handen, riktas mot taket med mikrofoner|
    |Linjär|På sidan, inför med mikrofoner du (visas i följande bild)|

    ![linjär dev kit orientering](media/speech-devices-sdk/qsg-2.png)

1. Installera certifikaten och filen wake word (nyckelordet) tabell och ange behörigheter för enheten. Skriv följande kommandon i Kommandotolken:

   ```
   adb push C:\SDSDK\Android-Sample-Release\scripts\roobo_setup.sh /data/
   adb shell
   cd /data/
   chmod 777 roobo_setup.sh
   ./roobo_setup.sh
   exit
   ```

    > [!NOTE]
    > De här kommandona använder Android Debug-bryggan, `adb.exe`, vilket är en del av Android Studio-installationen. Det här verktyget finns i C:\Users\[användarnamn] \AppData\Local\Android\Sdk\platform-verktyg. Du kan lägga till den här katalogen till sökvägen att göra det mer praktiskt att anropa `adb`. Annars måste du ange den fullständiga sökvägen för din installation av adb.exe i alla kommandon som anropar `adb`.
    >
    > Om du ser ett fel `no devices/emulators found` sedan kontrollera USB-kabel är ansluten och är en högkvalitativ kabel. Du kan använda `adb devices` att kontrollera att datorn kan kommunicera med i development kit som returneras en lista över enheter.

    > [!TIP]
    > Stäng av datorns mikrofon och högtalare för att säkerställa att du arbetar med i development kit mikrofoner. På så sätt kan du inte av misstag utlösa enheten med ljud från datorn.

1.  Starta Vysor på datorn.

    ![Vysor](media/speech-devices-sdk/qsg-3.png)

1.  Enheten bör visas under **väljer du en enhet**. Välj den **visa** knappen bredvid enheten.

1.  Ansluta till det trådlösa nätverket genom att välja mappikonen och välj sedan **inställningar** > **WLAN**.

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
    >
    > Om du vill koppla en talare till dev-paket kan ansluta du den till ljud rad ut. Du bör välja en god kvalitet, 3.5 mm talare.
    >
    > ![Vysor ljud](media/speech-devices-sdk/qsg-14.png)

## <a name="run-a-sample-application"></a>Kör ett exempelprogram

Om du vill köra testerna ROOBO och verifiera din development kit-konfiguration, skapa och installera exempelprogrammet:

1.  Starta Android Studio.

1.  Välja **Öppna ett befintligt Android Studio-projekt**.

    ![Android Studio – öppna ett befintligt projekt](media/speech-devices-sdk/qsg-5.png)

1.  Gå till C:\SDSDK\Android-Sample-Release\example. Välj **OK** att öppna exempelprojektet.

1.  Lägga till din prenumerationsnyckel för tal i källkoden. Om du vill prova taligenkänning kan också lägga till din [tjänst för Språkförståelse](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) prenumerationsnyckel och program-ID.

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

    Du kan också [skapa en anpassad aktivering word](speech-devices-sdk-create-kws.md).

    Om du vill installera wake-ord som du vill använda:

    * Skapa en mapp för nyckelord i datamappen på enheten genom att köra följande kommandon i Kommandotolken:

        ```
        adb shell
        cd /data
        mkdir keyword
        exit
        ```

    * Kopiera filer kws.table, kws_k.fst och words_kw.txt till enhetens \data\keyword mapp. Kör följande kommandon i Kommandotolken. Om du har skapat en [anpassade wake word](speech-devices-sdk-create-kws.md), kws.table-filen som genereras från webben finns i samma katalog som kws.table, kws_k.fst och words_kw.txt-filer. Ett ord på anpassad aktivering, Använd den `adb push C:\SDSDK\Android-Sample-Release\keyword\[wake_word_name]\kws.table /data/keyword` kommandot för att skicka filen kws.table till dev-paket:

        ```
        adb push C:\SDSDK\Android-Sample-Release\keyword\kws.table /data/keyword
        adb push C:\SDSDK\Android-Sample-Release\keyword\Computer\kws_k.fst /data/keyword
        adb push C:\SDSDK\Android-Sample-Release\keyword\Computer\words_kw.txt /data/keyword
        ```

    * Referera till dessa filer i exempelprogrammet. Hitta följande rader i MainActivity.java. Kontrollera att det angivna nyckelordet är den som du använder och att sökvägen till den `kws.table` -fil som du överförde till enheten.

        ```java
        private static final String Keyword = "Computer";
        private static final String KeywordModel = "/data/keyword/kws.table";
        ```

        > [!NOTE]
        > Du kan använda filen kws.table i din egen kod för att skapa en instans för nyckelordet modellen och starta igenkänning av:
        >
        > ```java
        > KeywordRecognitionModel km = KeywordRecognitionModel.fromFile(KeywordModel);
        > final Task<?> task = reco.startKeywordRecognitionAsync(km);
        > ```

1.  Uppdatera följande rader, som innehåller inställningar för mikrofon lagringsmatris geometri:

    ```java
    private static final String DeviceGeometry = "Circular6+1";
    private static final String SelectedGeometry = "Circular6+1";
    ```
    I följande tabell beskrivs de tillgängliga värdena:

    |Variabel|Betydelse|Tillgängliga värden|
    |--------|-------|----------------|
    |`DeviceGeometry`|Fysiska mic-konfiguration|För en cirkulär dev-paket: `Circular6+1` |
    |||För en linjär dev-paket: `Linear4`|
    |`SelectedGeometry`|Mic programvarukonfiguration|För en cirkulär dev-paket använder som alla mikrofoner: `Circular6+1`|
    |||För en cirkulär dev-paket använder som fyra mikrofoner: `Circular3+1`|
    |||För en linjär dev-paket använder som alla mikrofoner: `Linear4`|
    |||För en linjär dev-paket använder som två mikrofoner: `Linear2`|


1.  Att skapa programmet på den **kör** menyn och välj **kör ”app”**. Den **Välj distributionsmålet** dialogrutan visas.

1. Välj din enhet och välj sedan **OK** du distribuerar program till enheten.

    ![Välj dialogrutan distribution mål](media/speech-devices-sdk/qsg-7.png)

1.  Exempelprogram för tal Devices SDK startar och välja mellan följande alternativ:

    ![Exempelprogrammet tal Devices SDK exempel och alternativ](media/speech-devices-sdk/qsg-8.png)

1. Experiment!

## <a name="troubleshooting"></a>Felsökning

### <a name="certificate-failures"></a>Certifikat-fel

Om det uppstår fel certifikat när du använder Speech-tjänsten kan du kontrollera att du använder rätt datum och tid:

1. Gå till **inställningar**. Under **System**väljer **datum och tid**.

    ![Under inställningar, väljer du datum och tid](media/speech-devices-sdk/qsg-12.png)

1. Behåll den **automatisk datum och tid** alternativ som valts. Under **väljer tidszon**, Välj den aktuella tidszonen.

    ![Välj alternativ för datum och tidszon](media/speech-devices-sdk/qsg-13.png)

    När du ser att dev-paket tiden matchar tiden på datorn, är dev-paket ansluten till internet.

    Läs mer i utveckling, den [ROOBO Utvecklingsguide](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf).

### <a name="audio"></a>Ljud

ROOBO ger ett verktyg som samlar in alla ljud flash-minnet. Det kan hjälpa dig felsökning av problem med ljud. En version av verktyget har angetts för varje development kit-konfiguration. På den [ROOBO plats](http://ddk.roobo.com/), Välj din enhet och välj sedan den **ROOBO verktyg** länken längst ned på sidan.
