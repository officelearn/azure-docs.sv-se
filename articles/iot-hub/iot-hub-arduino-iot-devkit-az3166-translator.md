---
title: Skapa en IoT DevKit translator med Azure Functions och Cognitive Services | Microsoft Docs
description: Använda mikrofonen på en IoT DevKit att ta emot ett meddelande och sedan använda Azure Cognitive Services för bearbetning till översatt text på engelska
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 12/19/2018
ms.author: liydu
ms.openlocfilehash: df7e7b426a8c85c8051d7f588c706a6f8811e183
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60518911"
---
# <a name="use-iot-devkit-az3166-with-azure-functions-and-cognitive-services-to-make-a-language-translator"></a>Använda IoT DevKit AZ3166 med Azure Functions och Cognitive Services för att göra en språköversättaren

I den här artikeln får du lära dig hur du gör IoT DevKit som en språköversättaren med hjälp av [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/). Den registrerar din röst och är därför den engelska texten som visas på skärmen DevKit.

Den [MXChip IoT DevKit](https://aka.ms/iot-devkit) är en allt-i-ett Arduino kompatibla tavla med omfattande kringutrustning och sensorer. Du kan utveckla för den med hjälp av [Azure IoT Device Workbench](https://aka.ms/iot-workbench) eller [Azure IoT Tools](https://aka.ms/azure-iot-tools) tilläggspaketet i Visual Studio Code. Den [projekt catalog](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) innehåller exempelprogram som hjälper dig att skapa prototyper IoT-lösningar.

## <a name="before-you-begin"></a>Innan du börjar

För att slutföra stegen i den här självstudien måste du först göra följande uppgifter:

* Förbereda din DevKit genom att följa stegen i [ansluta IoT DevKit AZ3166 på Azure IoT Hub i molnet](/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started).

## <a name="create-azure-cognitive-service"></a>Skapa Azure Cognitive Service

1. I Azure-portalen klickar du på **skapa en resurs** och Sök efter **tal**. Fyll i formuläret för att skapa Speech-tjänsten.
  ![Med taltjänsten](media/iot-hub-arduino-iot-devkit-az3166-translator/speech-service.png)

1. Gå till tal-tjänst du just skapat, klicka på **nycklar** avsnitt för att kopiera och anteckna den **Key1** för DevKit åtkomst till den.
  ![Kopiera nycklar](media/iot-hub-arduino-iot-devkit-az3166-translator/copy-keys.png)

## <a name="open-sample-project"></a>Öppna exempelprojektet

1. Kontrollera att din IoT DevKit är **inte ansluten** till datorn. Starta VS Code först och sedan ansluta DevKit till din dator.

1. Klicka på `F1` för att öppna kommandopaletten, skriver du och väljer **Azure IoT Device Workbench: Öppna exempel...** . Välj sedan **IoT DevKit** som tavla.

1. På sidan IoT Workbench exempel hitta **DevKit Translator** och klicka på **öppna exemplet**. Sedan väljer standardsökvägen för att hämta exempelkoden.
  ![Öppna exemplet](media/iot-hub-arduino-iot-devkit-az3166-translator/open-sample.png)

## <a name="use-speech-service-with-azure-functions"></a>Använd Speech Service med Azure Functions

1. I VS Code, klickar du på `F1`skriver du och väljer **Azure IoT Device Workbench: Etablera Azure-tjänster...** . ![Etablera Azure-tjänster](media/iot-hub-arduino-iot-devkit-az3166-translator/provision.png)

1. Följ stegen för att slutföra etableringen av Azure IoT Hub och Azure Functions.
   ![Etablera steg](media/iot-hub-arduino-iot-devkit-az3166-translator/provision-steps.png)

   Anteckna namnet på Azure IoT Hub enheten som du skapade.

1. Öppna `Functions\DevKitTranslatorFunction.cs` och uppdaterar följande rader med kod med enhetens namn och Speech Service-nyckeln som du antecknade.
   ```csharp
   // Subscription Key of Speech Service
   const string speechSubscriptionKey = "";

   // Region of the speech service, see https://docs.microsoft.com/azure/cognitive-services/speech-service/regions for more details.
   const string speechServiceRegion = "";

   // Device ID
   const string deviceName = "";
   ```

1. Klicka på `F1`skriver du och väljer **Azure IoT Device Workbench: Distribuera till Azure...** . Om VS Code begär bekräftelse för omdistribution, klickar du på **Ja**.
   ![Distribuera varning](media/iot-hub-arduino-iot-devkit-az3166-translator/deploy-warning.png)

1. Kontrollera att distributionen genomförs.
   ![Distribuera lyckades](media/iot-hub-arduino-iot-devkit-az3166-translator/deploy-success.png)

1. I Azure-portalen går du till **Funktionsappar** avsnittet, hitta Azure Function-app som nyss skapade. Klicka på `devkit_translator`, klicka sedan på **<> / hämta Funktionswebbadress** att kopiera URL: en.
   ![Kopiera funktions-URL](media/iot-hub-arduino-iot-devkit-az3166-translator/get-function-url.png)

1. Klistra in URL: en i `azure_config.h` fil.
   ![Azure config](media/iot-hub-arduino-iot-devkit-az3166-translator/azure-config.png)

   > [!NOTE]
   > Om funktionsappen inte fungerar korrekt, markerar du det [vanliga frågor och svar](https://microsoft.github.io/azure-iot-developer-kit/docs/faq#compilation-error-for-azure-function) avsnitt för att lösa den.

## <a name="build-and-upload-device-code"></a>Skapa och ladda upp enheten

1. Växla DevKit till **konfigurationsläge** genom att:
   * Håll ned knappen **A**.
   * Tryck på och släpp **återställa** knappen.

   Du ser skärmen visar DevKit-ID och **Configuration**.

   ![DevKit konfigurationsläge](media/iot-hub-arduino-iot-devkit-az3166-translator/devkit-configuration-mode.png)

1. Klicka på `F1`skriver du och väljer **Azure IoT Device Workbench: Konfigurera Enhetsinställningar... > Config enhetens anslutningssträng**. Välj **Välj IoT Hub enhetens anslutningssträng** att konfigurera den att DevKit.
   ![Konfigurera anslutningssträngen](media/iot-hub-arduino-iot-devkit-az3166-translator/configure-connection-string.png)

1. Meddelandet visas när det har är klart.
   ![Konfigurera anslutningen sträng lyckas](media/iot-hub-arduino-iot-devkit-az3166-translator/configure-connection-string-success.png)

1. Klicka på `F1` igen, ange och välj **Azure IoT Device Workbench: Ladda upp enheten**. Den börjar kompilera och ladda upp koden till DevKit.
   ![Ladda upp enheten](media/iot-hub-arduino-iot-devkit-az3166-translator/device-upload.png)

## <a name="test-the-project"></a>Testa projektet

Följ anvisningarna på skärmen DevKit efter app initiering. Standardspråk för källan är kinesiska.

Att välja ett annat språk för översättning:

1. Tryck på knappen A in i installationsprogrammet.

2. Tryck på knappen B rulla alla källa språk som stöds.

3. Tryck på knappen A att bekräfta valet av källspråk.

4. Tryck på och håller knappen B när du talar och släpp B för att initiera översättningen.

5. Den översatta texten i engelska visas på skärmen.

![Bläddra för att välja språk](media/iot-hub-arduino-iot-devkit-az3166-translator/select-language.jpg)

![Översättning resultat](media/iot-hub-arduino-iot-devkit-az3166-translator/translation-result.jpg)

På skärmen translation resultatet kan du:

- Tryck på knapparna A och B för att bläddra och välja en källspråket.

- Tryck på knappen B prata. Om du vill skicka röst och få Översättningstext, släpper du knappen B.

## <a name="how-it-works"></a>Hur det fungerar

![Mini-Solution-Voice-to-tweet-diagram](media/iot-hub-arduino-iot-devkit-az3166-translator/diagram.png)

IoT DevKit registrerar din röst när du sedan skickar en HTTP-begäran till Azure Functions-utlösare. Azure Functions anropar talöversättning cognitive service API för att göra översättningen. När Azure Functions hämtar Översättningstext, skickar den ett C2D-meddelande till enheten. Översättningen visas på skärmen.

## <a name="problems-and-feedback"></a>Problem och feedback

Om du får problem kan se den [IoT DevKit vanliga frågor och svar](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) eller kontakta oss med hjälp av följande kanaler:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Nästa steg

Du har lärt dig hur du använder IoT DevKit som en translator med hjälp av Azure Functions och Cognitive Services. I den här anvisningen, lärde du dig hur du:

> [!div class="checklist"]
> * Använd Visual Studio Code-aktiviteten för att automatisera molnet bestämmelser
> * Konfigurera Azure IoT-enhetens anslutningssträng
> * Distribuera Azure-funktion
> * Testa översättning för röst-meddelande

Gå vidare till andra självstudierna för att lära dig:

> [!div class="nextstepaction"]
> [Ansluta IoT DevKit AZ3166 till Azure IoT lösningsacceleratorn för fjärrövervakning](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring)
