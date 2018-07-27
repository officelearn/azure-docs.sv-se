---
title: Kom igång med SDK för tal-enheter
description: Krav och anvisningar för att komma igång med SDK för tal-enheter.
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 05/18/2018
ms.author: v-jerkin
ms.openlocfilehash: 266315a731eec8a2c0ab0a880ce9e1db58331184
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/27/2018
ms.locfileid: "39283144"
---
# <a name="get-started-with-the-speech-devices-sdk"></a>Kom igång med SDK för tal-enheter

Den här artikeln beskriver hur du konfigurerar din dator för utveckling och tal enheten development kit för att utveckla tal-aktiverade enheter med hjälp av SDK för tal-enheter. Du kommer sedan skapar och distribuerar ett exempelprogram till enheten. 

Källkoden för exempelprogrammet som ingår i SDK: N för tal enheter och är också [finns på GitHub](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Förutsättningar

Samla in information och programvara som du behöver innan du startar utveckling med tal enheter SDK.

* Hämta en Utvecklingskit [från Roobo](http://ddk.roobo.com/). Startpaket är tillgängliga med linjära eller cirkulär mikrofon matris konfigurationer. välja rätt för dina behov.

    |Development kit konfiguration|Talare plats|
    |-----------------------------|------------|
    |Cirkulär|Oavsett riktning från enheten|
    |Linjär|Framför enheten|

* Hämta den senaste versionen av tal enheter SDK, inklusive en Android exempelapp från tal enheter SDK [hämtningswebbplats](https://shares.datatransfer.microsoft.com/). Extrahera ZIP-filen till en lokal mapp (till exempel `C:\SDSDK`).

* Installera [Android Studio](https://developer.android.com/studio/) och [Vysor](http://vysor.io/download/) på din dator.

* Hämta en taltjänst [prenumerationsnyckel](get-started.md). Du kan skaffa en 30-dagars kostnadsfri utvärderingsversion, eller en nyckel från instrumentpanelen i Azure.

* Om du vill använda tjänsten tal taligenkänning prenumerera på den [tjänst för Språkförståelse](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) (LUIS) och [skaffa en prenumerationsnyckel](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/azureibizasubscription). 

    Du kan [skapa en enkel LUIS-modell](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/) eller Använd exemplet LUIS-modell, `LUIS-example.json`är tillgängligt från tal Devices SDK [hämtningswebbplats](https://shares.datatransfer.microsoft.com/). Ladda upp din modell JSON-filen till den [LUIS portal](https://www.luis.ai/home) genom att klicka på **importera ny app** och välja JSON-filen.

## <a name="set-up-the-development-kit"></a>Ställ in i development kit

1. Plugin-adapterns i development kit. En grön power indikator bör tändas under den översta tavlan.

1. Ansluta i development kit till en dator med en mini USB-kabel.

    ![ansluta dev-paket](media/speech-devices-sdk/qsg-1.jpg)

1. Förstå din development kit på rätt sätt.

    |Development kit konfiguration|Orientering|
    |-----------------------------|------------|
    |Cirkulär|Handen, riktas mot taket med mikrofoner|
    |Linjär|På sidan, med mikrofoner mot du (visas nedan)|

    ![linjär dev kit orientering](media/speech-devices-sdk/qsg-2.jpg)

1. Installera certifikaten och filen wake word (nyckelordet) tabell och ange behörigheter för enheten. Skriv följande kommandon i Kommandotolken.

    > [!NOTE]
    > De här kommandona använder Android Debug-bryggan, `adb.exe`, vilket är en del av Android Studio-installationen. Det här verktyget finns i `C:\Users\[user name]\AppData\Local\Android\Sdk\platform-tools`. Du kan lägga till den här katalogen till sökvägen att göra det mer praktiskt att anropa `adb`. Annars måste du ange den fullständiga sökvägen till din installation av `adb.exe` i alla kommandon som anropar `adb`.

    ```
    adb push C:\SDSDK\Android-Sample-Release\scripts\roobo_setup.sh /data/ 
    adb shell
    cd /data/ 
    chmod 777 roobo_setup.sh
    ./roobo_setup.sh
    exit
    ```

    > [!TIP]
    > Stäng av datorns mikrofon och högtalare. På så sätt kan du vara säker på att du arbetar med i development kit mikrofoner och du av misstag utlöser ingen enhet med ljud från datorn.
    
1.  Starta Vysor på datorn.

    ![Vysor](media/speech-devices-sdk/qsg-3.png)

1.  Enheten bör visas under ”Välj en enhet”. Klicka på den **visa** -knapp. 
 
1.  Ansluta till det trådlösa nätverket genom att klicka på **inställningar**, sedan **WLAN**.

    ![Vysor WLAN](media/speech-devices-sdk/qsg-4.png)
 
## <a name="run-a-sample-application"></a>Kör ett exempelprogram

Om du vill köra testerna Roobo och verifiera din development kit-konfiguration, skapa och installera exempelprogrammet.

1.  Starta Android Studio.

1.  Om du vill öppna ett befintligt Android Studio-projekt.

    ![Android studio – Öppna befintligt projekt](media/speech-devices-sdk/qsg-5.png)
 
1.  Bläddra till `C:\SDSDK\Android-Sample-Release\example`, klicka sedan på **OK** att öppna exempelprojektet.
 
1.  Lägga till din prenumerationsnyckel för tal i källkoden. Om du vill prova att använda taligenkänning kan också lägga till din [tjänst för Språkförståelse](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) prenumerationsnyckel och program-ID. 

    Dina nycklar och programinformation börjar i följande rader i källfilen `MainActivity.java`.

    ```java
    // Subscription
    private static final String SpeechSubscriptionKey = "[your speech key]";
    private static final String SpeechRegion = "westus";
    private static final String LuisSubscriptionKey = "[your LUIS key]";
    private static final String LuisRegion = "westus2.api.cognitive.microsoft.com";
    private static final String LuisAppId = "[your LUIS app id]"
    ```

1. Standard wake ordet (nyckelordet) är ”dator”.  Om du vill kan du kan testa någon av de andra tillhandahålls wake ord, ”dator” och ”Assistant”. Resursfiler för dessa alternativa ord finns i SDK: N för tal-enheter i mappen ”nyckelordet”. Till exempel `C:\SDSDK\Android-Sample-Release\keyword\Computer` innehåller de filer som används för ”dator”.

    Du kan också [skapa en anpassad aktivering word](speech-devices-sdk-create-kws.md).

    Installera önskade wake word:
 
    * Skapa en `keyword` mappen i mappen \data\ på enheten genom att köra följande kommandon i kommandofönstret.

        ```
        adb shell
        cd /data
        mkdir keyword
        exit
        ```

    * Kopiera filerna `kws.table`, `kws_g.fst`, `kws_k.fst`, och `words_kw.txt`) till enhetens \data\keyword\ mapp. Kör följande kommandon i o kommandofönstret.

        ```
        adb push C:\SDSDK\Android-Sample-Release\keyword\kws.table /data/keyword
        adb push C:\SDSDK\Android-Sample-Release\keyword\Computer\kws_g.fst /data/keyword
        adb push C:\SDSDK\Android-Sample-Release\keyword\Computer\kws_k.fst /data/keyword
        adb push C:\SDSDK\Android-Sample-Release\keyword\Computer\words_kw.txt /data/keyword
        ```
    
    * Referera till dessa filer i exempelprogrammet. Hitta följande rader i `MainActivity.java`. Kontrollera att det angivna nyckelordet är den som du använder och att sökvägen till den `kws.table` -fil som du överförde till enheten.
        
        ```java
        private static final String Keyword = "Computer";
        private static final String KeywordModel = "/data/keyword/kws.table";
        ```

        > [!NOTE]
        > Du kan använda i din egen kod i `kws.table` filen för att skapa en instans för nyckelordet modellen och starta erkännande på följande sätt.
        >
        > ```java
        > KeywordRecognitionModel km = KeywordRecognitionModel.fromFile(KeywordModel);
        > final Task<?> task = reco.startKeywordRecognitionAsync(km);
        > ```

1.  Uppdatera följande rader som innehåller inställningar för mikrofon lagringsmatris geometri.

    ```java
    private static final String DeviceGeometry = "Circular6+1";
    private static final String SelectedGeometry = "Circular6+1";
    ```

    |Variabel|Betydelse|Tillgängliga värden|
    |--------|-------|----------------|
    |`DeviceGeometry`|Fysiska mic-konfiguration|`Circular6+1` för cirkulär dev-paket|
    ||| `Linear4` för linjär dev-paket|
    |`SelectedGeometry`|Mic programvarukonfiguration|`Circular6+1` för cirkulär dev-paket med alla mikrofoner|
    |||`Circular3+1` med hjälp av fyra mikrofoner för cirkulär dev-paket|
    |||`Linear4` för linjär dev-paket med alla mikrofoner|
    |||`Linear2` med hjälp av två mikrofoner för linjär dev-paket|


1.  Skapa programmet genom att välja **kör ”app”** kör-menyn. Dialogrutan Välj distributionsmålet visas. Välj din enhet och klicka på **OK** du distribuerar program till enheten.

    ![Välj mål för distribution](media/speech-devices-sdk/qsg-7.png)
 
1.  Exempelprogram för tal Devices SDK startar, alternativen som visas här.

    ![programmet på exempelenheten tal](media/speech-devices-sdk/qsg-8.png)

1. Experimentera med den!

## <a name="troubleshooting"></a>Felsökning

Om det uppstår fel certifikat när du använder Speech-tjänsten kan du kontrollera att enheten har rätt datum och tid.

Mer information om utveckling, finns i Roobo's [Utvecklingsguide](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf).

Roobo ger ett verktyg som samlar in alla ljud till flash-minne, vilket kan underlätta felsökningen ljud problem. En version av verktyget har angetts för varje development kit-konfiguration. Välj enheten på [webbplatsen Roobo](http://ddk.roobo.com/), klicka sedan på den **ROOBO verktyg** länken längst ned på sidan.
