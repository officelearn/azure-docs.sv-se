---
title: IoT DevKit översättare med Azure-funktion och kognitiva Services | Microsoft Docs
description: Använda mikrofon på IoT DevKit att ta emot röstmeddelande och kognitiva Azure-tjänster för bearbetning till översatt text på engelska
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 02/28/2018
ms.author: liydu
ms.openlocfilehash: ba2325272552a13d6e464797b1fb523415393100
ms.sourcegitcommit: e34afd967d66aea62e34d912a040c4622a737acb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/26/2018
ms.locfileid: "36945946"
---
# <a name="use-iot-devkit-az3166-with-azure-function-and-cognitive-services-to-make-a-language-translator"></a>Använda IoT DevKit AZ3166 med Azure-funktion och kognitiva tjänster för att göra en språk översättare

I den här artikeln beskrivs hur du gör IoT DevKit som en översättare språk med hjälp av [Azure kognitiva Services](https://azure.microsoft.com/services/cognitive-services/). Den registrerar rösten och översätts till engelska text som visas på skärmen DevKit.

Den [MXChip IoT DevKit](https://aka.ms/iot-devkit) är en allt-i-ett Arduino kompatibel skiva med sensorer och omfattande kringutrustning. Du kan utveckla för den med hjälp av [Visual Studio Code-tillägget för Arduino](https://aka.ms/arduino). Och det ingår en växande [projekt katalogen](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) att hjälpa dig prototyp Sakernas Internet (IoT) lösningar som utnyttjar Microsoft Azure-tjänster.

## <a name="what-you-need"></a>Vad du behöver

Slutför den [Getting Started Guide](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) till:

* Har din DevKit ansluten till Wi-Fi
* Förbereda utvecklingsmiljön

En aktiv Azure-prenumeration. Om du inte har någon, kan du registrera via någon av följande två metoder:

* Aktivera en [kostnadsfria 30-dagars utvärderingsversion av Microsoft Azure-konto](https://azure.microsoft.com/free/)
* Anspråk din [Azure-kredit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) om du prenumererar på MSDN eller Visual Studio

## <a name="step-1-open-the-project-folder"></a>Steg 1. Öppna projektmappen

### <a name="a-start-vs-code"></a>A. Starta VS-kod

- Kontrollera att din DevKit är ansluten till datorn.
- Starta VS-kod
- Anslut DevKit till datorn.

### <a name="b-open-the-arduino-examples-folder"></a>B. Öppna mappen Arduino exempel

Expandera vänster **ARDUINO exempel > exempel MXCHIP AZ3166 > AzureIoT**, och välj **DevKitTranslator**. Ett nytt fönster för VS kod öppnas med DEVKITTRANSLATOR projektmappen i den.  

> [!NOTE]
> Om du inte ser exempel för MXCHIP AZ3166 avsnitt, kontrollera att enheten är korrekt ansluten och starta om Visual Studio-koden.  

![IoT DevKit-exempel](media/iot-hub-arduino-iot-devkit-az3166-translator/vscode_examples.png)

> [!NOTE]
> Du kan också öppna exempel kommandot paletten. Använd `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) för att öppna paletten kommando skriver **Arduino**, och sedan söka efter och välj **Arduino: exempel**.

## <a name="step-2-provision-azure-services"></a>Steg 2. Etablera Azure-tjänster

I fönstret lösning anger `Ctrl+P` (macOS: `Cmd+P`) och ange `task cloud-provision`.

I VS koden terminal hjälper en interaktiv kommandorad dig att konfigurera alla nödvändiga Azure-tjänster:

![Molnet etablera aktivitet](media/iot-hub-arduino-iot-devkit-az3166-translator/cloud-provision.png)

## <a name="step-3-deploy-azure-functions"></a>Steg 3. Distribuera Azure Functions

Använd `Ctrl+P` (macOS: `Cmd+P`) att köra `task cloud-deploy` att distribuera Azure Functions-kod. Den här processen tar normalt 2-5 minuter att slutföra:

![Molnet uppgift för agentdistribution](media/iot-hub-arduino-iot-devkit-az3166-translator/cloud-deploy.png)

När Azure-funktion distribuerar har, Fyll i filen azure_config.h med funktionen programnamn. Du kan navigera till [Azure-portalen](https://portal.azure.com/) att hitta den:

![Hitta Azure-funktion programnamn](media/iot-hub-arduino-iot-devkit-az3166-translator/azure-function.png)

> [!NOTE]
> Om funktionen Azure inte fungerar korrekt, markerar du det [vanliga frågor och svar](https://microsoft.github.io/azure-iot-developer-kit/docs/faq#compilation-error-for-azure-function) avsnittet för att lösa problemet.

## <a name="step-4-build-and-upload-the-device-code"></a>Steg 4. Skapa och skicka kod för enheten

1. Använd `Ctrl+P` (macOS: `Cmd+P`) att köra `task config-device-connection`.

2. Terminalen tillfrågas du om du vill använda anslutningssträngen som hämtas från `task cloud-provision` steg. Du kan också ange anslutningssträngen din egen enhet genom att välja **'Skapa nytt... ”**

3. Terminalen uppmanas du att ange konfigurationsläge. Om du vill göra det, hålla ned A, och sedan push och släpp återställningsknappen. Skärmen visar DevKit ID och ”Configuration”.
  ![Verifieringen och överföring av Arduino ritningarna](media/iot-hub-arduino-iot-devkit-az3166-translator/config-device-connection.png)

4. Efter `task config-device-connection` färdig, klicka på `F1` att läsa in VS kodkommandon och välj `Arduino: Upload`, VS kod startas verifierar och laddar upp Arduino skissa: ![kontroll och överföring av Arduino ritningarna](media/iot-hub-arduino-iot-devkit-az3166-translator/arduino-upload.png)

DevKit startar om och börjar köras koden.

## <a name="test-the-project"></a>Testa projektet

Följ instruktionerna på skärmen DevKit efter appen initiering. Standardspråket för datakällan är kinesiska.

Välj ett annat språk för översättning:

1. Tryck på knappen A in i installationsprogrammet.
2. Tryck på knappen B rulla alla källa språk som stöds.
3. Tryck på knappen A bekräfta dina val av språk för källa.
4. Tryck och håll knappen B när du talar och släpp B för att initiera översättningen.
5. Den översatta texten i engelska visas på skärmen.

![Rulla för att välja språk](media/iot-hub-arduino-iot-devkit-az3166-translator/select-language.jpg)

![Översättning av resultat](media/iot-hub-arduino-iot-devkit-az3166-translator/translation-result.jpg)

På skärmen översättning resultatet kan du:

- Tryck på knappen A och B för att bläddra och Välj språk för källa.
- Tryck på knappen B för att kommunicera, så att skicka rösten och hämta översättning texten

## <a name="how-it-works"></a>Hur det fungerar

![Mini-Solution-Voice-to-tweet-diagram](media/iot-hub-arduino-iot-devkit-az3166-translator/diagram.png)

Arduino skiss poster rösten skickar sedan en HTTP-begäran till utlösaren Azure Functions. Azure Functions anropar kognitiva service tal översättare API för att göra översättningen. När Azure Functions hämtar Översättningstext, skickar meddelandet C2D till enheten. Översättningen visas på skärmen.

## <a name="change-device-id"></a>Ändra enhets-ID

Standard-enhets-ID som registrerats i Azure IoT Hub är **AZ3166**. Om du vill ändra den följer [anvisningarna här](https://microsoft.github.io/azure-iot-developer-kit/docs/customize-device-id/).

## <a name="problems-and-feedback"></a>Problem och feedback

Om du stöter på problem, läser du [vanliga frågor och svar](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) eller nå oss från följande kanaler:

* [Gitter.IM](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [StackOverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Nästa steg

Nu kan du göra IoT DevKit som en översättare med hjälp av Azure-funktion och kognitiva tjänster. I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Använd Visual Studio Code aktiviteten för att automatisera bestämmelserna i molnet
> * Konfigurera anslutningssträngen för Azure IoT-enhet
> * Distribuera Azure-funktion
> * Testa översättning för röst-meddelande

Gå vidare till andra självstudiekurser att lära dig:

> [!div class="nextstepaction"]
> [Ansluta IoT DevKit AZ3166 till Azure IoT-Fjärrövervaknings solution accelerator](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring)
