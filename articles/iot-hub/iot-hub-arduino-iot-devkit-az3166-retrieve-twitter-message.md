---
title: Hämta ett Twitter-meddelande med Azure Functions | Microsoft Docs
description: Använd rörelsedetektor för att identifiera skaka och Använd Azure Functions för att hitta en slumpmässig tweet med en hashtag som du anger
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 03/07/2018
ms.author: liydu
ms.openlocfilehash: f488f8aa991b3d4baae05097af9b6e2f2db481cc
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2019
ms.locfileid: "54158932"
---
# <a name="shake-shake-for-a-tweet----retrieve-a-twitter-message-with-azure-functions"></a>Skaka, skaka efter en Tweet – hämta ett Twitter-meddelande med Azure Functions

I det här projektet att lära dig hur du använder rörelsedetektor för att utlösa en händelse med Azure Functions. Appen hämtar en slumpmässig tweet med en #hashtag du konfigurerar i Arduino-skiss. Tweeten visas på skärmen DevKit.

## <a name="what-you-need"></a>Vad du behöver

Slutför den [Kom igång med](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) till:

* Din DevKit har anslutit till Wi-Fi.
* Förbereda utvecklingsmiljön.

En aktiv Azure-prenumeration. Om du inte har någon, kan du registrera via någon av följande metoder:

* Aktivera en [kostnadsfria 30-dagars utvärderingsversion av Microsoft Azure-konto](https://azure.microsoft.com/free/)
* Anspråk din [Azure-kredit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) om du är MSDN eller Visual Studio-prenumerant

## <a name="open-the-project-folder"></a>Öppna projektmappen

Starta genom att öppna projektmappen. 

### <a name="start-vs-code"></a>Starta VS Code

* Kontrollera att din DevKit är ansluten till datorn.

* Starta VS Code.

* Anslut DevKit till din dator.

   > [!NOTE]
   > När du startar VS Code, du får ett felmeddelande som Arduino IDE eller relaterade tavla paket inte får vara hittades. Om det här felet uppstår, Stäng VS Code och starta Arduino IDE igen. Arduino IDE-sökvägen ska nu hitta korrekt i VS Code.

### <a name="open-the-arduino-examples-folder"></a>Öppna mappen Arduino-exempel

Expandera till vänster **ARDUINO exempel** bläddrar du till **exempel för MXCHIP AZ3166 > AzureIoT**, och välj **ShakeShake**. Ett nytt VS Code-fönster öppnas och visar projektmappen. Om du inte ser avsnittet MXCHIP AZ3166, kontrollera att enheten är ansluten och starta om Visual Studio Code.  
den ![mini-solution-exempel](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/vscode_examples.png)

Du kan också öppna exempelprojektet från kommandopaletten. Klicka på `Ctrl+Shift+P` (Mac OS: `Cmd+Shift+P`) Skriv för att öppna kommandopaletten **Arduino**, och leta upp och välj **Arduino: Exempel**.

## <a name="provision-azure-services"></a>Etablera Azure-tjänster

Kör uppgiften i Lösningsfönstret `Ctrl+P` (Mac OS: `Cmd+P`) genom att ange `task cloud-provision`.

I VS Code-terminalen hjälper dig att etablera nödvändiga Azure-tjänster i en interaktiv kommandorad:

![cloud-etablering](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/cloud-provision.png)

> [!NOTE]
> Om sidan låser sig i laddningsstatus när du försöker logga in på Azure, referera till den [”inloggningssidan låser” steg i IoT DevKit vanliga frågor och svar](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#page-hangs-when-log-in-azure).
 
## <a name="modify-the-hashtag"></a>Ändra #hashtag

Öppna `ShakeShake.ino` och leta efter den här raden med kod:

```cpp
static const char* iot_event = "{\"topic\":\"iot\"}";
```

Ersätt strängen `iot` inom klamrarna mot din önskade hashtagg. DevKit hämtar senare en slumpmässig tweet som innehåller hashtag som du anger i det här steget.

## <a name="deploy-azure-functions"></a>Distribuera Azure Functions

Använd `Ctrl+P` (Mac OS: `Cmd+P`) att köra `task cloud-deploy` att börja distribuera Azure Functions-kod:

![distribuera i molnet](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/cloud-deploy.png)

> [!NOTE]
> Ibland kan fungerar Azure-funktionen inte korrekt. För att lösa problemet när det uppstår, kontrollera den [”kompileringsfel”-avsnittet i IoT DevKit vanliga frågor och svar](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#compilation-error-for-azure-function).

## <a name="build-and-upload-the-device-code"></a>Skapa och överföra kod för enhet

Sedan skapa och överföra kod för enheten.

### <a name="windows"></a>Windows

1. Använd `Ctrl+P` att köra `task device-upload`.

2. Terminalen uppmanas du att ange konfigurationsläge. Gör så här:

   * Håll ned knappen A

   * Push- och släpp återställningsknappen.

3. På skärmen visas DevKit ID och ”Configuration”.

### <a name="macos"></a>macOS

1. Placera DevKit i konfigurationsläge:

   Håll ned knappen A, och sedan push- och släpp återställningsknappen. På skärmen visas ”Configuration”.

2. Använd `Cmd+P` att köra `task device-upload` att ange anslutningssträngen som har hämtats från den `task cloud-provision` steg.

### <a name="verify-upload-and-run"></a>Kontrollera, ladda upp och kör

Nu anslutningssträngen har angetts, den verifierar och laddar upp appen och sedan kör den. 

1. VS Code startar verifierar och laddar upp Arduino skissen till din DevKit:

   ![enhet-överföringen](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/device-upload.png)

2. DevKit startar om och börjat köra koden.

Du kan få en ”fel: AZ3166: Okänd package ”felmeddelande. Det här felet uppstår när den tavla paketindexet inte uppdateras korrekt. Lös problemet genom att kontrollera den [”okänt package” fel i IoT DevKit vanliga frågor och svar](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>Testa projektet

Efter initieringen av appen, klickar du på och utgåva knappen A sedan försiktigt skaka DevKit tavlan. Den här åtgärden hämtar en slumpmässig tweet som innehåller hashtag som du angav tidigare. Inom några sekunder visas en tweet på skärmen DevKit:

### <a name="arduino-application-initializing"></a>Arduino program initierar...

![Arduino-program – initiering](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-1.png)

### <a name="press-a-to-shake"></a>Tryck på A till skaka...

![Tryck på en till skaka](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-2.png)

### <a name="ready-to-shake"></a>Redo att skaka...

![Redo att skaka](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-3.png)

### <a name="processing"></a>Bearbetar ...

![Bearbetar](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-4.png)

### <a name="press-b-to-read"></a>Tryck på B för att läsa...

![Tryck på B-att läsa](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-5.png)

### <a name="display-a-random-tweet"></a>Visa en slumpmässig tweet...

![Visa-a-slumpmässiga-tweet](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-6.png)

- Tryck på knappen A igen och skaka för en ny tweet.
- Tryck på knappen B för att bläddra igenom resten av tweeten.

## <a name="how-it-works"></a>Hur det fungerar

![Diagram](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/diagram.png)

Arduino skissen skickar en händelse till Azure IoT Hub. Den här händelsen utlöses Azure Functions-app. Azure Functions-app innehåller logik för att ansluta till Twitter's API och hämta en tweet. Därefter omsluts tweet-text till en C2D (moln till enhet) meddelandet och skickar det tillbaka till enheten.

## <a name="optional-use-your-own-twitter-bearer-token"></a>Valfritt: Använd din egen Twitter ägar-token

Det här exempelprojektet använder en förkonfigurerad Twitter-ägartoken i testsyfte. Det finns dock en [hastighetsbegränsning](https://dev.twitter.com/rest/reference/get/search/tweets) för varje Twitter-konto. Följ dessa steg om du vill överväga att använda din egen token:

1. Gå till [Twitter utvecklarportalen](https://dev.twitter.com/) att registrera en ny Twitter-app.

2. [Få använda nyckeln och konsumenten hemligheter](https://support.yapsody.com/hc/en-us/articles/360003291573-How-do-I-get-a-Twitter-Consumer-Key-and-Consumer-Secret-key-) för din app.

3. Använd [vissa verktyget](https://gearside.com/nebula/utilities/twitter-bearer-token-generator/) att skapa en Twitter ägar-token från dessa två nycklar.

4. I den [Azure-portalen](https://portal.azure.com/){: target = ”_blank”}, komma in i **resursgrupp** och hitta Azure-funktion (typ: App Service) för projektet ”skaka, skaka”. Namnet innehåller alltid ”skaka...” sträng.

   ![Azure-funktion](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/azure-function.png)

5. Uppdatera koden för `run.csx` inom **funktioner > shakeshake cs** med din egen token:

   ```csharp
   string authHeader = "Bearer " + "[your own token]";
  ```
  
  ![Twitter-token](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/twitter-token.png)

6. Spara filen och klicka på **kör**.

## <a name="problems-and-feedback"></a>Problem och feedback

Så här att felsöka problem eller ge feedback. 

### <a name="problems"></a>Problem

Ett problem kan du få se om skärmen visar 'Nej Tweets ”vid varje steg har kunnat köras. Det här tillståndet inträffar vanligtvis den första gången du distribuerar och kör exemplet eftersom funktionsappen kräver var som helst från några sekunder till så mycket som en minut att kallstart appen. 

Eller när du kör koden, det finns vissa signaler som orsakar en omstart av appen. När det här tillståndet inträffar kan app för enheter få en timeout för hämtning av tweeten. I det här fallet kan du prova ett eller båda av följande metoder för att lösa problemet:

1. Klicka på återställningsknappen på DevKit köra enhetsappen igen.

2. I den [Azure-portalen](https://portal.azure.com/), hitta Azure Functions-app som du skapade och starta om den:

   ![Azure-funktion-omstart](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/azure-function-restart.png)

### <a name="feedback"></a>Feedback

Om du har andra problem kan se den [IoT DevKit vanliga frågor och svar](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) eller kontakta oss via följande kanaler:

* [Gitter.IM](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du ansluter en DevKit enhet till din Azure IoT lösningsacceleratorn för fjärrövervakning och hämtar en tweet, är här nästa föreslagna steg:

* [IoT-Remote Monitoring solution accelerator översikt över Azure](https://docs.microsoft.com/azure/iot-suite/)