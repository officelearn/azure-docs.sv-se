---
title: Skapa en IoT DevKit translator med Azure Functions och Cognitive Services | Microsoft Docs
description: Använda mikrofonen på en IoT DevKit att ta emot ett meddelande och sedan använda Azure Cognitive Services för bearbetning till översatt text på engelska
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 02/28/2018
ms.author: liydu
ms.openlocfilehash: acfff95afacfa1e5c75a799ba84d64cfa0579f66
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/07/2018
ms.locfileid: "39592099"
---
# <a name="use-iot-devkit-az3166-with-azure-functions-and-cognitive-services-to-make-a-language-translator"></a>Använda IoT DevKit AZ3166 med Azure Functions och Cognitive Services för att göra en språköversättaren

I den här artikeln får du lära dig hur du gör IoT DevKit som en språköversättaren med hjälp av [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/). Den registrerar din röst och är därför den engelska texten som visas på skärmen DevKit.

Den [MXChip IoT DevKit](https://aka.ms/iot-devkit) är en allt-i-ett Arduino kompatibla tavla med omfattande kringutrustning och sensorer. Du kan utveckla för den med hjälp av [Visual Studio Code-tillägg för Arduino](https://aka.ms/arduino). Och det levereras med en växande [projekt catalog](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) att guida dig genom prototyp Internet of Things (IoT) lösningar som utnyttjar Microsoft Azure-tjänster.

## <a name="what-you-need"></a>Vad du behöver

Slutför den [Kom igång med](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) till:

* Har din DevKit som är ansluten till Wi-Fi
* Förbereda utvecklingsmiljön

En aktiv Azure-prenumeration. Om du inte har någon kan registrera du via någon av följande två metoder:

* Aktivera en [kostnadsfria 30-dagars utvärderingsversion av Microsoft Azure-konto](https://azure.microsoft.com/free/)
* Anspråk din [Azure-kredit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) om du är MSDN eller Visual Studio-prenumerant

## <a name="open-the-project-folder"></a>Öppna projektmappen

Öppna först projektmappen. 

### <a name="start-vs-code"></a>Starta VS Code

- Kontrollera att din DevKit är ansluten till datorn.

- Starta VS Code.

- Anslut DevKit till din dator.

### <a name="open-the-arduino-examples-folder"></a>Öppna mappen Arduino-exempel

Expandera vänster **ARDUINO exempel > exempel för MXCHIP AZ3166 > AzureIoT**, och välj **DevKitTranslator**. Ett nytt VS Code-fönster öppnas och visar projektmappen. Om du inte kan se exemplen för MXCHIP AZ3166 avsnittet, kontrollera att enheten är ansluten och starta om VS Code.  

![IoT DevKit-exempel](media/iot-hub-arduino-iot-devkit-az3166-translator/vscode_examples.png)

Du kan också öppna exemplet från kommandopaletten. Använd `Ctrl+Shift+P` (Mac OS: `Cmd+Shift+P`) Skriv för att öppna kommandopaletten **Arduino**, och leta upp och välj **Arduino: exempel**.

## <a name="provision-azure-services"></a>Etablera Azure-tjänster

Skriv i Lösningsfönstret `Ctrl+P` (Mac OS: `Cmd+P`) och ange `task cloud-provision`.

I VS Code terminal hjälper en interaktiv kommandorad dig för att etablera alla nödvändiga Azure-tjänster:

![Molnet etablera uppgift](media/iot-hub-arduino-iot-devkit-az3166-translator/cloud-provision.png)

## <a name="deploy-the-azure-function"></a>Distribuera Azure-funktion

Använd `Ctrl+P` (Mac OS: `Cmd+P`) att köra `task cloud-deploy` att distribuera Azure Functions-kod. Den här processen tar normalt 2-5 minuter att slutföra.

![Molnet för distribution av agentuppgradering](media/iot-hub-arduino-iot-devkit-az3166-translator/cloud-deploy.png)

När Azure Function har distribuerats, fyller du i filen azure_config.h med funktionsappens namn. Du kan navigera till [Azure-portalen](https://portal.azure.com/) att hitta det:

![Hitta Azure Funktionsappens namn](media/iot-hub-arduino-iot-devkit-az3166-translator/azure-function.png)

> [!NOTE]
> Om Azure-funktionen inte fungerar korrekt, kontrollera den [”complication för Azure-funktion” felsida i vanliga IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq#compilation-error-for-azure-function) ska lösas.

## <a name="build-and-upload-the-device-code"></a>Skapa och överföra kod för enhet

1. Använd `Ctrl+P` (Mac OS: `Cmd+P`) att köra `task config-device-connection`.

2. Terminalen tillfrågas du om du vill använda anslutningssträngen som hämtas från `task cloud-provision` steg. Du kan också skriva egna enhetens anslutningssträng genom att välja **”Skapa nytt...”**

3. Terminalen uppmanas du att ange konfigurationsläge. För att göra det, håll ned knappen A, och sedan push- och släpp återställningsknappen. På skärmen visas DevKit ID och ”Configuration”.

   ![Verifiering och överföring av Arduino-skiss](media/iot-hub-arduino-iot-devkit-az3166-translator/config-device-connection.png)

4. Efter `task config-device-connection` är klar klickar du på `F1` att läsa in VS Code-kommandon och välj `Arduino: Upload`, som sedan VS Code startar verifierar och laddar upp Arduino skiss.

   ![Verifiering och överföring av Arduino-skiss](media/iot-hub-arduino-iot-devkit-az3166-translator/arduino-upload.png)

DevKit startar om och börjat köra koden.

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

Din röst när du sedan skickar en HTTP Arduino skiss posterna begära för att utlösa en Azure-funktion. Azure Function anropar talöversättning cognitive service API för att göra översättningen. När Azure Function hämtar Översättningstext, skickar den ett C2D (moln till enhet)-meddelande till enheten. Översättningen visas på skärmen.

## <a name="change-device-id"></a>Ändra enhets-ID

Standard-enhets-ID som registrerats i Azure IoT Hub är **AZ3166**. Om du vill ändra enhets-ID, se hur du [anpassa IoT enhets-ID för DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/customize-device-id/).

## <a name="problems-and-feedback"></a>Problem och feedback

Om du får problem kan se den [IoT DevKit vanliga frågor och svar](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) eller kontakta oss med hjälp av följande kanaler:

* [Gitter.IM](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [StackOverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

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
