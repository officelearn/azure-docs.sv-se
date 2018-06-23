---
title: Kom igång med tal enheter SDK | Microsoft Docs
description: Krav och instruktioner för att komma igång med tal enheter SDK.
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 05/18/2018
ms.author: v-jerkin
ms.openlocfilehash: 6cc5ff1c532d67c48beac1a2a10d034f5d9d7501
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35355653"
---
# <a name="get-started-with-the-speech-devices-sdk"></a>Kom igång med tal enheter SDK

Den här artikeln beskriver hur du konfigurerar din dator för utveckling och ditt tal enheten development kit utveckla tal-aktiverade enheter med tal enheter SDK. Du kan sedan skapa och distribuera ett exempelprogram till enheten. 

Källkoden för exempelprogrammet som medföljer SDK för tal-enheter och är också [finns på GitHub](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Förutsättningar

Samla in information och programvara som du behöver innan du startar utveckling med tal enheter SDK.

* Hämta ett development kit [från Roobo](http://ddk.roobo.com/). Kits är tillgängliga med linjär eller cirkulär mikrofon matris konfigurationer; välja rätt för dina behov.

    |Development kit konfiguration|Talare plats|
    |-----------------------------|------------|
    |Cirkulära|Valfri riktning från enheten|
    |Linjär|Framför enheten|

* Hämta den senaste versionen av tal enheter SDK, inklusive en Android exempelapp från tal enheter SDK [hämtningsplats](https://shares.datatransfer.microsoft.com/). Extrahera ZIP-filen till en lokal mapp (exempelvis `C:\SDSDK`).

* Installera [Android Studio](https://developer.android.com/studio/) och [Vysor](http://vysor.io/download/) på din dator.

* Hämta en tal tjänst [prenumeration nyckeln](get-started.md). Du kan hämta en kostnadsfri 30-dagars utvärderingsversion eller hämta en nyckel från instrumentpanelen i Azure.

* Om du vill använda tjänsten tal avsiktshantering recognition prenumererar på [språk förstå service](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) (THOMAS) och [skaffa en prenumeration nyckel](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/azureibizasubscription). 

    Du kan [skapa en enkel THOMAS modell](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/) eller Använd exemplet THOMAS modellen `LUIS-example.json`, tillgänglig från tal enheter SDK [hämtningsplats](https://shares.datatransfer.microsoft.com/). Överföra din modell JSON-filen till den [THOMAS portal](https://www.lui.ai/applications) genom att klicka på **Importera nya app** och välja JSON-filen.

## <a name="set-up-the-development-kit"></a>Ställ in i development kit

1. Anslut adapterns i development kit. En grön power indikatorn ska lysa upp under översta planen.

1. I development kit att ansluta till en dator med en mini USB-kabel.

    ![ansluta dev kit](media/speech-devices-sdk/qsg-1.jpg)

1. Förstå din development kit på lämpligt sätt.

    |Development kit konfiguration|Orientering|
    |-----------------------------|------------|
    |Cirkulära|Upprätt med mikrofoner mot taket|
    |Linjär|På sidan, inför med mikrofoner du (se nedan)|

    ![linjär dev kit orientering](media/speech-devices-sdk/qsg-2.jpg)

1. Installera certifikat och wake word (nyckelordet) tabell-fil och ange behörigheter för ljudenheten. Skriv följande kommandon i Kommandotolken.

    > [!NOTE]
    > Dessa kommandon använder Debug-brygga Android `adb.exe`, vilket är en del av installationen av Android Studio. Det här verktyget finns i `C:\Users\[user name]\AppData\Local\Android\Sdk\platform-tools`. Du kan lägga till den här katalogen i din sökväg för att göra det mer praktiskt att anropa `adb`. Annars måste du ange den fullständiga sökvägen för din installation av `adb.exe` i varje kommando som anropar `adb`.

    ```
    adb push C:\SDSDK\Android-Sample-Release\scripts\roobo_setup.sh /data/ 
    adb shell
    cd /data/ 
    chmod 777 roobo_setup.sh
    ./roobo_setup.sh
    exit
    ```

    > [!TIP]
    > Stäng av datorns mikrofon och högtalaren. På så sätt kan du vara säker på att du arbetar med i development kit mikrofoner och du av misstag utlöser enheten med ljud från datorn.
    
1.  Starta Vysor på datorn.

    ![Vysor](media/speech-devices-sdk/qsg-3.png)

1.  Enheten bör anges ”Välj en enhet”. Klicka på den **visa** ellips-knapp. 
 
1.  Ansluta till det trådlösa nätverket genom att klicka på **inställningar**, sedan **WLAN**.

    ![Vysor WLAN](media/speech-devices-sdk/qsg-4.png)
 
## <a name="run-a-sample-application"></a>Kör ett exempelprogram

Om du vill köra testerna Roobo och verifiera inställningarna development kit, skapa och installera exempelprogrammet.

1.  Starta Android Studio.

1.  Välja att öppna ett befintligt Android Studio-projekt.

    ![Android studio – Öppna befintligt projekt](media/speech-devices-sdk/qsg-5.png)
 
1.  Bläddra till `C:\SDSDK\Android-Sample-Release\example`, klicka på **OK** öppna exempelprojektet.
 
1.  Lägg till din prenumeration tal-nyckel i källkoden. Om du vill testa avsiktshantering recognition kan också lägga till din [språk förstå service](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) prenumeration nyckel och program-ID. 

    Dina nycklar och information om programmet som ska ingå i följande rader i källfilen `MainActivity.java`.

    ```java
    // Subscription
    private static final String SpeechSubscriptionKey = "[your speech key]";
    private static final String SpeechRegion = "westus";
    private static final String LuisSubscriptionKey = "[your LUIS key]";
    private static final String LuisRegion = "westus2.api.cognitive.microsoft.com";
    private static final String LuisAppId = "[your LUIS app id]"
    ```

1. Standard wake ordet (nyckelordet) är ”dator”.  Om du vill kan du försöka av de andra angivna wake ord, ”dator” och ”assistenten”. Resursfiler för dessa alternativa ord finns i SDK tal enheter i mappen ”nyckelordet”. Till exempel `C:\SDSDK\Android-Sample-Release\keyword\Computer` innehåller de filer som används för ”dator”.

    Du kan också [skapa en anpassad aktivering word](speech-devices-sdk-create-kws.md).

    Installera önskade wake word:
 
    * Skapa en `keyword` mappen i mappen \data\ på enheten genom att köra följande kommandon i Kommandotolken.

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
    
    * Referera till dessa filer i exempelprogrammet. Sök efter följande rader i `MainActivity.java`. Kontrollera att det angivna nyckelordet är den som du använder och att sökvägen till den `kws.table` -fil som du pushas till enheten.
        
        ```java
        private static final String Keyword = "Computer";
        private static final String KeywordModel = "/data/keyword/kws.table";
        ```

        > [!NOTE]
        > Du kan använda i din kod i `kws.table` filen för att skapa en instans för nyckelordet modellen och starta recognition på följande sätt.
        >
        > ```java
        > KeywordRecognitionModel km = KeywordRecognitionModel.fromFile(KeywordModel);
        > final Task<?> task = reco.startKeywordRecognitionAsync(km);
        > ```

1.  Uppdatera följande rader som innehåller mikrofon matris geometri inställningar.

    ```java
    private static final String DeviceGeometry = "Circular6+1";
    private static final String SelectedGeometry = "Circular6+1";
    ```

    |Variabel|Betydelse|Tillgängliga värden|
    |--------|-------|----------------|
    |`DeviceGeometry`|Fysiska mic-konfiguration|`Circular6+1` för cirkulär dev kit|
    ||| `Linear4` linjär dev Kit|
    |`SelectedGeometry`|Konfiguration av programvara mic|`Circular6+1` med hjälp av alla mikrofoner för cirkulär dev kit|
    |||`Circular3+1` med hjälp av fyra mikrofoner för cirkulär dev kit|
    |||`Linear4` linjär dev kit med i alla mikrofoner|
    |||`Linear2` linjär dev Kit med två mikrofoner|


1.  Skapa programmet genom att välja **kör ”app”** kör-menyn. Dialogrutan Välj distributionsmålet visas. Välj enheten och på **OK** du distribuerar program till enheten.

    ![Välj mål för distribution](media/speech-devices-sdk/qsg-7.png)
 
1.  Exempelprogram tal enheter SDK startar, vilka alternativ som visas här.

    ![exempelprogrammet tal enhet](media/speech-devices-sdk/qsg-8.png)

1. Experimentera med den!

## <a name="troubleshooting"></a>Felsökning

Om det uppstår fel certifikat när du använder tjänsten tal kan du kontrollera att enheten har rätt datum och tid.

Mer information om utveckling, finns i Roobo's [development guide](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf).

Roobo ger ett verktyg som samlar in alla ljud till flash-minne som kan underlätta felsökning av problem med ljud. En version av verktyget har angetts för varje development kit konfiguration. Välj din enhet på [webbplatsen Roobo](http://ddk.roobo.com/), klicka på den **ROOBO verktyg** länken längst ned på sidan.
