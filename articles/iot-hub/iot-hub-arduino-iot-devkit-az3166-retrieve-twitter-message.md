---
title: Hämta ett Twitter-meddelande med Azure Functions | Microsoft Docs
description: Använda rörelse sensor för att identifiera skaka och använda Azure Functions för att hitta en slumpmässig tweet med en hashtaggar som du anger.
services: iot-hub
documentationcenter: ''
author: liydu
manager: timlt
tags: ''
keywords: ''
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/07/2018
ms.author: liydu
ms.openlocfilehash: 8bcc1b2c76ff1e312d1435997ef6d677656510d8
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="shake-shake-for-a-tweet----retrieve-a-twitter-message-with-azure-functions"></a>Skaka, skaka i en Tweet – hämta ett Twitter-meddelande med Azure Functions!

I det här projektet beskrivs du hur du använder rörelse sensor för att utlösa en händelse med hjälp av Azure-funktioner. Appen hämtar slumpmässiga tweets med en #hashtag du konfigurerar i din Arduino skiss. Tweet visas på skärmen DevKit.

## <a name="what-you-need"></a>Vad du behöver

Slutför den [Getting Started Guide](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) till:

* Din DevKit har anslutit till Wi-Fi.
* Förbered utvecklingsmiljö.

En aktiv Azure-prenumeration. Om du inte har någon, kan du registrera via någon av följande metoder:

* Aktivera en [kostnadsfria 30-dagars utvärderingsversion av Microsoft Azure-konto](https://azure.microsoft.com/free/)
* Anspråk din [Azure-kredit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) om du prenumererar på MSDN eller Visual Studio

## <a name="open-the-project-folder"></a>Öppna projektmappen

### <a name="start-vs-code"></a>Starta VS-kod

- Se till att din DevKit **inte** ansluten till datorn.
- Starta VS-kod.
- Anslut DevKit till datorn.

> [!NOTE]
> När den startas VS-koden får ett felmeddelande som Arduino IDE- eller relaterade board paketet inte kan hittas. Om det här felet uppstår Stäng VS-kod och starta Arduino IDE igen. VS-koden ska nu hitta Arduino IDE-sökvägen på rätt sätt.

### <a name="open-arduino-examples-folder"></a>Öppna Arduino exempel mapp

Expandera vänster **ARDUINO exempel** bläddrar du till **exempel MXCHIP AZ3166 > AzureIoT**, och välj **ShakeShake**. En ny kod jämfört med en projektmapp i den öppnas.

![Mini-solution-exempel](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/vscode_examples.png)

> [!NOTE]
> Du kan också öppna exempel kommandot paletten. Använd `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) för att öppna paletten kommando skriver **Arduino**, och sedan söka efter och välj **Arduino: exempel**.

## <a name="provision-azure-services"></a>Etablera Azure-tjänster

I fönstret lösning kör uppgiften `Ctrl+P` (macOS: `Cmd+P`) genom att ange `task cloud-provision`.

I terminalen VS kod hjälper dig att etablera nödvändiga Azure-tjänster i en interaktiv kommandorad:

![etablera moln](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/cloud-provision.png)

> [!NOTE]
> Om sidan låser sig statusen lästes in när du försöker logga in på Azure, [vanliga frågor och svar steg](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#page-hangs-when-log-in-azure).
 
## <a name="modify-the-hashtag"></a>Ändra #hashtag

Öppna `ShakeShake.ino` och leta efter kod:

```cpp
static const char* iot_event = "{\"topic\":\"iot\"}";
```

Ersätt strängen `iot` inom klamrarna med din önskade hashtaggar. DevKit hämtar senare slumpmässiga tweets som innehåller hashtaggar som du anger i det här steget.

## <a name="deploy-azure-functions"></a>Distribuera Azure Functions

Använd `Ctrl+P` (macOS: `Cmd+P`) att köra `task cloud-deploy` att börja distribuera Azure Functions-koden:

![distribuera moln](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/cloud-deploy.png)

> [!NOTE]
> Ibland kan fungerar Azure-funktionen inte korrekt. Markera det här alternativet för att lösa problemet när det uppstår, [vanliga frågor och svar steg](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#compilation-error-for-azure-function).

## <a name="build-and-upload-the-device-code"></a>Skapa och skicka kod för enheten

### <a name="windows"></a>Windows

1. Använd `Ctrl+P` att köra `task device-upload`.
2. Terminalen uppmanas du att ange konfigurationsläge. Gör så här:

   * Håll ned knappen A
   * Dra och släpp återställningsknappen.

3. Skärmen visar DevKit ID och ”Configuration”.
4. Detta anger anslutningssträngen som har hämtats från den `task cloud-provision` steg.
5. VS-kod och sedan startar verifiering och ladda upp Arduino skissa till din DevKit: ![enhet-överföringen](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/device-upload.png)
6. DevKit startar om och börjar köras koden.

> [!NOTE]
> Du kan få ett ”fel: AZ3166: Okänd paketet” felmeddelande. Detta fel uppstår när den paketindexet board inte uppdateras korrekt. Markera det här alternativet för att lösa problemet, [vanliga frågor och svar steg](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

### <a name="macos"></a>macOS

1. Placera DevKit i konfigurationsläge: Håll ned knappen A, och sedan push och versionen återställningsknappen. Skärmen visar ”Configuration”.
2. Använd `Cmd+P` att köra `task device-upload` ange anslutningssträngen som har hämtats från den `task cloud-provision` steg.
3. VS-kod och sedan startar verifiering och ladda upp Arduino skissa till din DevKit: ![enhet-överföringen](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/device-upload.png)
4. DevKit startar om och börjar köras koden.

> [!NOTE]
> Du kan få ett ”fel: AZ3166: Okänd paketet” felmeddelande. Detta fel uppstår när den paketindexet board inte uppdateras korrekt. Markera det här alternativet för att lösa problemet, [vanliga frågor och svar steg](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>Testa projektet

Efter app initialisering klickar du på och släpper knappen A sedan försiktigt skaka DevKit-kort. Den här åtgärden hämtar en slumpmässig tweet som innehåller hashtaggar som du angav tidigare. Inom några sekunder visas en tweet på skärmen DevKit:

### <a name="arduino-application-initializing"></a>Arduino program initierar...
![Arduino program initiering](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-1.png)

### <a name="press-a-to-shake"></a>Trycka skaka...
![Tryck på A att skaka](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-2.png)

### <a name="ready-to-shake"></a>Redo att skaka...
![Redo att skaka](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-3.png)

### <a name="processing"></a>Bearbetar ...
![Bearbetning](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-4.png)

### <a name="press-b-to-read"></a>Tryck på B för att läsa...
![Tryck på B-att läsa](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-5.png)

### <a name="display-a-random-tweet"></a>Visa en slumpmässig tweet...
![Visa-en-slumpmässiga-tweet](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-6.png)

- Tryck på knappen A igen och sedan skaka för en ny tweet.
- Tryck på knappen B för att rulla igenom resten av tweet.

## <a name="how-it-works"></a>Hur det fungerar

![diagram](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/diagram.png)

Arduino ritningarna skickar en händelse till Azure IoT Hub. Den här händelsen utlöses appen Azure Functions. Azure Functions-program innehåller logik för att ansluta till Twitter's API och hämta en tweet. Packar sedan tweet texten i en C2D meddelandet (moln till enhet) och skickar tillbaka till enheten.

## <a name="optional-use-your-own-twitter-bearer-token"></a>Valfritt: Använd egna Twitter ägar-token

För testning, använder den här exempelprojektet en förkonfigurerad Twitter ägar-token. Det finns dock en [hastighetsbegränsning](https://dev.twitter.com/rest/reference/get/search/tweets) för varje Twitter-konto. Följ dessa steg om du vill överväga att använda din egen token:

1. Gå till [Twitter Developer-portalen](https://dev.twitter.com/) att registrera en ny Twitter-app.

2. [Hämta konsumentnyckel och konsumenten hemligheter](https://support.yapsody.com/hc/en-us/articles/203068116-How-do-I-get-a-Twitter-Consumer-Key-and-Consumer-Secret-key-) för din app.

3. Använd [vissa verktyg](https://gearside.com/nebula/utilities/twitter-bearer-token-generator/) att generera en Twitter ägar-token från dessa två nycklar.

4. I den [Azure-portalen](https://portal.azure.com/){: target = ”_blank”}, komma åt den **resursgruppen** och hitta Azure-funktion (typ: App Service) för projektet ”skaka, skaka”. Namnet innehåller alltid 'skaka... ”sträng.
  ![azure-function](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/azure-function.png)

5. Uppdatera koden för `run.csx` inom **funktioner > shakeshake cs** med din egen token:
  ```csharp
  ...
  string authHeader = "Bearer " + "[your own token]";
  ...
  ```
  ![Twitter-token](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/twitter-token.png)

6. Spara filen och klicka på **kör**.


## <a name="problems-and-feedback"></a>Problem och feedback

### <a name="the-screen-displays-no-tweets-while-every-step-has-run-successfully"></a>Skärmen visar 'Nej Tweets' medan varje steg har körts

Detta tillstånd inträffar normalt för första gången du distribuera och köra exemplet eftersom funktionen appen kräver var som helst från några sekunder till så mycket som en minut att kallstart appen. Eller när du kör koden, det finns vissa signaler som orsakar en omstart av appen. När det här tillståndet inträffar kan enheten appen få en tidsgräns för hämtning av tweet. I det här fallet kan du prova en eller båda av dessa metoder för att lösa problemet:

1. Klicka på återställningsknappen på DevKit köra appen enheten igen.

2. I den [Azure-portalen](https://portal.azure.com/), hitta Azure Functions-appen som du skapade och starta om den: ![azure funktionen omstart](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/azure-function-restart.png)

### <a name="feedback"></a>Feedback

Om det andra problem, referera till [vanliga frågor och svar](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) eller kontakta oss från följande kanaler:

* [Gitter.IM](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [StackOverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du ansluter en DevKit enhet till din Azure IoT Suite och hämtar en tweet är här de föreslagna nästa steg:

* [Översikt över Azure IoT Suite](https://docs.microsoft.com/azure/iot-suite/)
