---
title: Hämta ett Twitter-meddelande med Azure Functions | Microsoft-dokument
description: Använd rörelsesensorn för att upptäcka skakningar och använda Azure Functions för att hitta en slumpmässig tweet med en hashtagg som du anger
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 03/07/2018
ms.author: liydu
ms.openlocfilehash: dc4ff35ff04680e8635d54c25212c8ae639ae472
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60779907"
---
# <a name="shake-shake-for-a-tweet----retrieve-a-twitter-message-with-azure-functions"></a>Skaka, skaka för en Tweet – Hämta ett Twitter-meddelande med Azure Functions

I det här projektet får du lära dig hur du använder rörelsesensorn för att utlösa en händelse med Hjälp av Azure Functions. Appen hämtar en slumpmässig tweet med en #hashtag du konfigurerar i din Arduino-skiss. Tweeten visas på DevKit-skärmen.

## <a name="what-you-need"></a>Vad du behöver

Avsluta [komma igång-guiden](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) till:

* Ha din DevKit ansluten till Wi-Fi.
* Förbered utvecklingsmiljön.

En aktiv Azure-prenumeration. Om du inte har någon kan du registrera dig via någon av dessa metoder:

* Aktivera ett [kostnadsfritt 30-dagars testversionskonto för Microsoft Azure](https://azure.microsoft.com/free/)
* Hämta din [Azure-kredit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) om du är msdn- eller Visual Studio-prenumerant

## <a name="open-the-project-folder"></a>Öppna projektmappen

Börja med att öppna projektmappen. 

### <a name="start-vs-code"></a>Starta VS-kod

* Kontrollera att Din DevKit är ansluten till datorn.

* Starta VS Code.

* Anslut DevKit till datorn.

   > [!NOTE]
   > När du startar VS-kod kan du få ett felmeddelande om att Arduino IDE- eller relaterade anslagstavlor inte kan hittas. Om det här felet uppstår stänger du VS-koden och startar Arduino IDE igen. VS-koden bör nu hitta Arduino IDE-sökvägen korrekt.

### <a name="open-the-arduino-examples-folder"></a>Öppna mappen Arduino Examples

Expandera avsnittet **ARDUINO EXAMPLES på** vänster sida, bläddra till **Exempel för MXCHIP AZ3166 > AzureIoT**och välj **ShakeShake**. Ett nytt VS-kodfönster öppnas som visar projektmappen. Om avsnittet MXCHIP AZ3166 inte kan visas kontrollerar du att enheten är korrekt ansluten och startar om Visual Studio-koden.  
mini-lösning-exempel ![](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/vscode_examples.png)

Du kan också öppna exempelprojektet från kommandopaletten. Klicka `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) för att öppna kommandopaletten, skriv **Arduino**och leta sedan upp och välj **Arduino: Exempel**.

## <a name="provision-azure-services"></a>Tillhandahålla Azure-tjänster

I lösningsfönstret kör du `Ctrl+P` uppgiften via `Cmd+P`(macOS: ) genom att ange `task cloud-provision`.

I VS-kodterminalen guidar en interaktiv kommandorad dig genom att etablera de Azure-tjänster som krävs:

![moln-etablering](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/cloud-provision.png)

> [!NOTE]
> Om sidan låser sig i inläsningsstatusen när du försöker logga in på Azure läser du [steget "inloggningssida hänger sig" i vanliga frågor och svar om IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#page-hangs-when-log-in-azure).
 
## <a name="modify-the-hashtag"></a>Ändra #hashtag

Öppna `ShakeShake.ino` och leta efter den här kodraden:

```cpp
static const char* iot_event = "{\"topic\":\"iot\"}";
```

Ersätt strängen `iot` i klammerparenteserna med önskad hashtagg. DevKit hämtar senare en slumpmässig tweet som innehåller hashtaggen som du anger i det här steget.

## <a name="deploy-azure-functions"></a>Distribuera Azure Functions

Använd `Ctrl+P` (macOS: `Cmd+P`) `task cloud-deploy` för att köra för att börja distribuera Azure Functions-koden:

![distribuera molnet](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/cloud-deploy.png)

> [!NOTE]
> Ibland kanske Azure-funktionen inte fungerar korrekt. LÃ¶s problemet nÃ¥r nÃ¤tt nÃ¤r det inträffar genom att lÃ¤sa [avsnittet "kompileringsfel" i vanliga frågor och svar om IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#compilation-error-for-azure-function).

## <a name="build-and-upload-the-device-code"></a>Skapa och ladda upp enhetskoden

Skapa och ladda sedan upp enhetskoden.

### <a name="windows"></a>Windows

1. Används `Ctrl+P` för `task device-upload`att köra .

2. Terminalen uppmanar dig att gå in i konfigurationsläge. Gör så här:

   * Håll ned knapp A

   * Tryck och släpp återställningsknappen.

3. På skärmen visas DevKit ID och "Konfiguration".

### <a name="macos"></a>macOS

1. Sätt DevKit i konfigurationsläge:

   Håll ned knapp A och tryck sedan och släpp återställningsknappen. Skärmen visar "Konfiguration".

2. Används `Cmd+P` för `task device-upload` att köra för att ange `task cloud-provision` anslutningssträngen som hämtas från steget.

### <a name="verify-upload-and-run"></a>Verifiera, ladda upp och köra

Nu är anslutningssträngen inställd, den verifierar och laddar upp appen och kör den sedan. 

1. VS-kod börjar verifiera och ladda upp Arduino-skissen till din DevKit:

   ![ladda upp enheten](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/device-upload.png)

2. DevKit startar om och börjar köra koden.

Felmeddelandet "Fel: AZ3166: Okänt paket". Det här felet uppstår när anslagstavlans paketindex inte uppdateras korrekt. LÃ¶s problemet genom att kontrollera [felet "okänt paket" i vanliga frågor och svar om IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>Testa projektet

Efter appinitering klickar du och släpper knapp A och skakar sedan försiktigt DevKit-kortet. Den här åtgärden hämtar en slumpmässig tweet som innehåller hashtaggen som du angav tidigare. Inom några sekunder visas en tweet på din DevKit-skärm:

### <a name="arduino-application-initializing"></a>Arduino ansökan initiera ...

![Arduino-ansökan-initiera](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-1.png)

### <a name="press-a-to-shake"></a>Tryck på A för att skaka...

![Tryck-A-till-skaka](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-2.png)

### <a name="ready-to-shake"></a>Redo att skaka...

![Klar att skaka](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-3.png)

### <a name="processing"></a>Bearbetning...

![Bearbetar](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-4.png)

### <a name="press-b-to-read"></a>Tryck på B för att läsa...

![Tryck-B-för-att läsa](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-5.png)

### <a name="display-a-random-tweet"></a>Visa en slumpmässig tweet...

![Visa-en-slumpmässig-tweet](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-6.png)

- Tryck på knapp A igen och skaka sedan för en ny tweet.
- Tryck på knapp B för att bläddra igenom resten av tweeten.

## <a name="how-it-works"></a>Hur det fungerar

![översikt](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/diagram.png)

Arduino-skissen skickar en händelse till Azure IoT Hub. Den här händelsen utlöser Azure Functions-appen. Azure Functions-appen innehåller logiken för att ansluta till Twitters API och hämta en tweet. Den radbryts sedan tweet texten i en C2D (Cloud-to-device) meddelande och skickar tillbaka den till enheten.

## <a name="optional-use-your-own-twitter-bearer-token"></a>Valfritt: Använd din egen Twitter bärare token

I testsyfte använder det här exempelprojektet en förkonfigurerad Twitter-innehavartoken. Det finns dock en [kursgräns](https://dev.twitter.com/rest/reference/get/search/tweets) för varje Twitter-konto. Om du vill överväga att använda din egen token gör du så här:

1. Gå till [Twitter Developer portal](https://dev.twitter.com/) för att registrera en ny Twitter app.

2. [Hämta konsumentnyckel- och konsumenthemligheter](https://support.yapsody.com/hc/en-us/articles/360003291573-How-do-I-get-a-Twitter-Consumer-Key-and-Consumer-Secret-key-) i din app.

3. Använd [något verktyg](https://gearside.com/nebula/utilities/twitter-bearer-token-generator/) för att generera en Twitter bärare token från dessa två nycklar.

4. I [Azure-portalen](https://portal.azure.com/){:target="_blank"}, gå in i **resursgruppen** och hitta Azure-funktionen (typ: App Service) för ditt "Shake, Shake"-projekt. Namnet innehåller alltid "skaka ..." Sträng.

   ![azure-funktion](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/azure-function.png)

5. Uppdatera koden `run.csx` för inom **Funktioner > shakeshake-cs** med din egen token:

   ```csharp
   string authHeader = "Bearer " + "[your own token]";
   ```
  
   ![Twitter-token](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/twitter-token.png)

6. Spara filen och klicka på **Kör**.

## <a name="problems-and-feedback"></a>Problem och feedback

Felsöka problem eller ge feedback. 

### <a name="problems"></a>Problem

Ett problem du kan se om skärmen visar "Inga Tweets" medan varje steg har körts framgångsrikt. Det här tillståndet inträffar normalt första gången du distribuerar och kör exemplet eftersom funktionsappen kräver allt från ett par sekunder till så mycket som en minut för att kalla starta appen. 

Eller, när du kör koden, det finns några blips som orsakar en omstart av appen. När detta tillstånd inträffar kan enhetsappen få en timeout för att hämta tweeten. I det här fallet kan du prova en eller båda av dessa metoder för att lösa problemet:

1. Klicka på återställningsknappen på DevKit för att köra enhetsappen igen.

2. I [Azure-portalen](https://portal.azure.com/)hittar du azure functions-appen som du skapade och startar om den:

   ![azure-function-omstart](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/azure-function-restart.png)

### <a name="feedback"></a>Feedback

Om du upplever andra problem, se [IoT DevKit FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) eller kontakta oss med hjälp av följande kanaler:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stackspill](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du ansluter en DevKit-enhet till din Azure IoT Remote Monitoring-lösningsaccelerator och hämtar en tweet, här är de föreslagna stegen:

* [Azure IoT Remote Monitoring solution accelerator översikt](https://docs.microsoft.com/azure/iot-suite/)