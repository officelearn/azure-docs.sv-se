---
title: Gör översättare med Azure-funktioner, Kognitiva tjänster, IoT DevKit
description: Använd mikrofonen på en IoT DevKit för att ta emot ett röstmeddelande och sedan använda Azure Cognitive Services för att bearbeta den till översatt text på engelska
author: liydu
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 12/19/2018
ms.author: liydu
ms.openlocfilehash: 8e840a1ae7161ea3e7b370889a1f0fb648ca120e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73953362"
---
# <a name="use-iot-devkit-az3166-with-azure-functions-and-cognitive-services-to-make-a-language-translator"></a>Använda IoT DevKit AZ3166 med Azure Functions och Cognitive Services för att göra en språköversättare

I den här artikeln får du lära dig hur du gör IoT DevKit till språköversättare med hjälp av [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/). Den spelar in din röst och översätter den till engelsk text som visas på DevKit-skärmen.

[MXChip IoT DevKit](https://aka.ms/iot-devkit) är en allt-i-ett Arduino kompatibel styrelse med rik kringutrustning och sensorer. Du kan utveckla för det med Azure [IoT Device Workbench](https://aka.ms/iot-workbench) eller [Azure IoT Tools](https://aka.ms/azure-iot-tools) tilläggspaket i Visual Studio Code. [Projektkatalogen](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) innehåller exempelprogram som hjälper dig att prototypen av IoT-lösningar.

## <a name="before-you-begin"></a>Innan du börjar

Gör så här för att slutföra stegen i den här självstudien:

* Förbered din DevKit genom att följa stegen i [Connect IoT DevKit AZ3166 till Azure IoT Hub i molnet](/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started).

## <a name="create-azure-cognitive-service"></a>Skapa Azure Cognitive Service

1. Klicka på Skapa **en resurs** i Azure-portalen och sök efter **tal**. Fyll i formuläret för att skapa taltjänsten.
  ![Tjänst för taligenkänning](media/iot-hub-arduino-iot-devkit-az3166-translator/speech-service.png)

1. Gå till taltjänsten som du just skapade, klicka på **Avsnittet Nycklar** för att kopiera och anteckna **key1** för DevKit som kommer åt den.
  ![Kopiera nycklar](media/iot-hub-arduino-iot-devkit-az3166-translator/copy-keys.png)

## <a name="open-sample-project"></a>Öppna exempelprojekt

1. Kontrollera att din IoT DevKit inte är **ansluten** till datorn. Starta VS-koden först och anslut sedan DevKit till datorn.

1. Klicka `F1` här om du vill öppna kommandopaletten, skriv och välj **Azure IoT Device Workbench: Öppna exempel...**. Välj sedan **IoT DevKit** som anslagstavla.

1. Leta reda på **DevKit Translator** på sidan IoT Workbench Examples och klicka på **Öppna exempel**. Väljer sedan standardsökvägen för att hämta exempelkoden.
  ![Öppna exempel](media/iot-hub-arduino-iot-devkit-az3166-translator/open-sample.png)

## <a name="use-speech-service-with-azure-functions"></a>Använda taltjänst med Azure-funktioner

1. I VS-kod `F1`klickar du på , skriver och väljer **Azure IoT Device Workbench: Provision Azure Services...**. ![Tillhandahålla Azure-tjänster](media/iot-hub-arduino-iot-devkit-az3166-translator/provision.png)

1. Följ stegen för att slutföra etableringen av Azure IoT Hub och Azure Functions.
   ![Steg för etablering](media/iot-hub-arduino-iot-devkit-az3166-translator/provision-steps.png)

   Anteckna azure IoT Hub-enhetsnamnet som du skapade.

1. Öppna `Functions\DevKitTranslatorFunction.cs` och uppdatera följande kodrader med enhetens namn och taltjänstnyckel som du noterade nedåt.
   ```csharp
   // Subscription Key of Speech Service
   const string speechSubscriptionKey = "";

   // Region of the speech service, see https://docs.microsoft.com/azure/cognitive-services/speech-service/regions for more details.
   const string speechServiceRegion = "";

   // Device ID
   const string deviceName = "";
   ```

1. Klicka `F1`på , skriv och välj **Azure IoT Device Workbench: Distribuera till Azure...**. Om VS-kod ber om bekräftelse för omdistribution klickar du på **Ja**.
   ![Distribuera varning](media/iot-hub-arduino-iot-devkit-az3166-translator/deploy-warning.png)

1. Kontrollera att distributionen lyckas.
   ![Distribuera lyckade](media/iot-hub-arduino-iot-devkit-az3166-translator/deploy-success.png)

1. Gå till avsnittet **Funktionerppar i** Azure-portalen och hitta azure-funktionsappen som just skapats. Klicka `devkit_translator`sedan på **</> Hämta funktions-URL:en** för att kopiera webbadressen.
   ![URL för kopieringsfunktion](media/iot-hub-arduino-iot-devkit-az3166-translator/get-function-url.png)

1. Klistra in `azure_config.h` URL:en i filen.
   ![Azure-konfiguration](media/iot-hub-arduino-iot-devkit-az3166-translator/azure-config.png)

   > [!NOTE]
   > Om funktionsappen inte fungerar som den ska kontrollerar du det här [avsnittet Vanliga frågor och svar](https://microsoft.github.io/azure-iot-developer-kit/docs/faq#compilation-error-for-azure-function) för att lösa det.

## <a name="build-and-upload-device-code"></a>Skapa och ladda upp enhetskod

1. Växla DevKit till **konfigurationsläge** genom att:
   * Håll ned knapp **A**.
   * Tryck och släpp **knappen Återställ.**

   Du kommer att se skärmen visar DevKit ID och **Konfiguration**.

   ![Konfigurationsläge för DevKit](media/iot-hub-arduino-iot-devkit-az3166-translator/devkit-configuration-mode.png)

1. Klicka `F1`på , skriv och välj **Azure IoT Device Workbench: Konfigurera enhetsinställningar... > Anslutningssträng**för konfigurationsenhet . Välj **Välj IoT Hub Device Connection String** om du vill konfigurera den till DevKit.
   ![Konfigurera anslutningssträng](media/iot-hub-arduino-iot-devkit-az3166-translator/configure-connection-string.png)

1. Du kommer att se meddelandet när det är gjort.
   ![Konfigurera framgång för anslutningssträngen](media/iot-hub-arduino-iot-devkit-az3166-translator/configure-connection-string-success.png)

1. Klicka `F1` igen, skriv och välj **Azure IoT Device Workbench: Ladda upp enhetskod**. Det börjar kompilera och ladda upp koden till DevKit.
   ![Uppladdning av enheter](media/iot-hub-arduino-iot-devkit-az3166-translator/device-upload.png)

## <a name="test-the-project"></a>Testa projektet

Efter appinitiering följer du instruktionerna på DevKit-skärmen. Standardkällaspråket är kinesiska.

Så här väljer du ett annat språk för översättning:

1. Tryck på knappen A för att gå in i inställningsläge.

2. Tryck på knapp B för att rulla alla källspråk som stöds.

3. Tryck på knapp A för att bekräfta ditt val av källspråk.

4. Tryck och håll ned knapp B medan du talar och släpp sedan knapp B för att initiera översättningen.

5. Den översatta texten på engelska visas på skärmen.

![Bläddra för att välja språk](media/iot-hub-arduino-iot-devkit-az3166-translator/select-language.jpg)

![Resultat av översättning](media/iot-hub-arduino-iot-devkit-az3166-translator/translation-result.jpg)

På skärmen för översättningsresultat kan du:

- Tryck på knapparna A och B för att rulla och välj källspråk.

- Tryck på B-knappen för att prata. Om du vill skicka rösten och få översättningstexten släpper du B-knappen.

## <a name="how-it-works"></a>Hur det fungerar

![mini-lösning-röst-till-tweet-diagram](media/iot-hub-arduino-iot-devkit-az3166-translator/diagram.png)

IoT DevKit registrerar din röst och publicerar sedan en HTTP-begäran om att utlösa Azure-funktioner. Azure Functions anropar API:et för kognitiv tjänsttalöversättare för att göra översättningen. När Azure Functions får översättningstexten skickas ett C2D-meddelande till enheten. Sedan visas översättningen på skärmen.

## <a name="problems-and-feedback"></a>Problem och feedback

Om du stöter på problem, se [IoT DevKit FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) eller kontakta oss med hjälp av följande kanaler:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stackspill](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Nästa steg

Du har lärt dig hur du använder IoT DevKit som översättare med hjälp av Azure Functions och Cognitive Services. I detta hur-till, lärde du dig att:

> [!div class="checklist"]
> * Använd Visual Studio-koduppgift för att automatisera molnbestämmelser
> * Konfigurera Anslutningssträng för Azure IoT-enhet
> * Distribuera Azure-funktionen
> * Testa översättningen av röstmeddelanden

Gå vidare till de andra tutorials att lära sig:

> [!div class="nextstepaction"]
> [Ansluta IoT DevKit AZ3166 till Azure IoT Remote Monitoring solution accelerator](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring)
