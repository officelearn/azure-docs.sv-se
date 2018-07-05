---
title: Ta emot ett e-postmeddelande när dörren öppnas med hjälp av SendGrid-tjänsten och Azure Functions | Microsoft Docs
description: Övervaka den magnetiska sensorn för att upptäcka när en dörr öppnas och Använd Azure Functions för att skicka ett e-postmeddelande.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 03/19/2018
ms.author: liydu
ms.openlocfilehash: 25cb3ba53c663a642f0871becbfbcab39d521c67
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37437723"
---
# <a name="door-monitor"></a>Dörren Övervakare          

MXChip IoT DevKit innehåller en inbyggd magnetiska sensorn. I det här projektet identifiera du närvaron eller frånvaron av ett i närheten stark magnetiska fält – i det här fallet kommer från en liten. Permanentmagnet.

## <a name="what-you-learn"></a>Detta får du får lära dig

I det här projektet lär du dig:
- Hur du använder MXChip IoT DevKit magnetiska sensorn för att identifiera att flytta en närliggande magneten.
- Hur du använder SendGrid-tjänsten för att skicka ett meddelande till din e-postadress.

> [!NOTE]
> För en praktisk användning av det här projektet:
> - Montera en magneten i utkanten av en dörr.
> - Montera DevKit på dörren jamb nära magneten. Öppna eller stänga dörren utlöser sensorn, vilket resulterar i att få ett e-postmeddelande för händelsen.

## <a name="what-you-need"></a>Vad du behöver

Slutför den [Kom igång med](iot-hub-arduino-iot-devkit-az3166-get-started.md) till:

* Har din DevKit som är ansluten till Wi-Fi
* Förbereda utvecklingsmiljön

En aktiv Azure-prenumeration. Om du inte har någon kan registrera du via någon av följande metoder:

* Aktivera en [kostnadsfria 30-dagars utvärderingsversion av Microsoft Azure-konto](https://azure.microsoft.com/free/).
* Anspråk din [Azure-kredit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) om du är MSDN eller Visual Studio-prenumerant.

## <a name="deploy-sendgrid-service-in-azure"></a>Distribuera SendGrid-tjänsten i Azure

[SendGrid](https://sendgrid.com/) är en plattform för leverans av molnbaserade e-post. Den här tjänsten används för att skicka e-postmeddelanden.

> [!NOTE]
> Om du redan har distribuerat en SendGrid-tjänsten, du kan fortsätta direkt till [distribuera IoT Hub i Azure](#deploy-iot-hub-in-azure).

### <a name="sendgrid-deployment"></a>SendGrid-distribution

För att etablera Azure-tjänster som använder den **distribuera till Azure** knappen. Den här knappen kan du snabbt och enkelt distribuera dina projekt med öppen källkod till Microsoft Azure.

Klicka på den **distribuera till Azure** knappen nedan. 

[![Distribuera till Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2FSendGridDeploy%2Fazuredeploy.json)

Då visas följande sida.

> [!NOTE]
> Om du inte ser följande sida kan du behöva först logga in på ditt Azure-konto.

Har fyllt i registreringsformuläret:

  * **Resursgrupp**: skapa en resursgrupp för att vara värd för SendGrid-tjänsten eller Använd en befintlig. Se [hantera Azure-resurser med hjälp av resursgrupper](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).

  * **Namn på**: namnet på SendGrid-tjänsten. Välj ett unikt namn som skiljer sig från andra tjänster som du kan ha.

  * **Lösenord**: tjänsten kräver ett lösenord som inte är för något i det här projektet.

  * **E-post**: The SendGrid-tjänsten ska skicka verifieringen till e-postadressen.

  > [!NOTE]
  > Kontrollera den **fäst på instrumentpanelen** alternativ för att göra det här programmet blir lättare att hitta i framtiden.
 
![SendGrid-distribution](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-deploy.png)

### <a name="sendgrid-api-key-creation"></a>Skapa en SendGrid API-nyckel

När distributionen är klar klickar du på den och klicka sedan på den **hantera** knappen. Du kommer till din SendGrid-sida och behöver verifiera din e-postadress.

![Hantera SendGrid](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-manage.png)

På sidan SendGrid **inställningar** > **API-nycklar** > **skapa API-nyckel**. Indata i **API-nyckelnamn** och klicka på **skapa vy**.

![SendGrid först skapa API](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-first.png)

![Skapa SendGrid API andra](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-second.png)

Din API-nyckel visas bara en gång. Glöm inte att kopiera och lagra den på ett säkert sätt, eftersom den används i nästa steg.

## <a name="deploy-iot-hub-in-azure"></a>Distribuera IoT-hubb i Azure

Följande steg kommer att etablera andra Azure-IoT-relaterade tjänster och distribuera Azure Functions för det här projektet.

Klicka på den **distribuera till Azure** knappen nedan. 

[![Distribuera till Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2Fazuredeploy.json)

Då visas följande sida.

> [!NOTE]
> Om du inte ser följande sida, kan du behöva först logga in på ditt Azure-konto.

Har fyllt i registreringsformuläret:

  * **Resursgrupp**: skapa en resursgrupp för att vara värd för SendGrid-tjänsten eller Använd en befintlig. Se [hantera Azure-resurser med hjälp av resursgrupper](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).

  * **Namnet på IOT Hub**: namn för din IoT-hubb. Välj ett unikt namn som skiljer sig från andra tjänster som du kan ha.

  * **IOT Hub Sku**: F1 (begränsad ett per prenumeration) är kostnadsfri. Du kan se mer prisinformation på [pris- och skalnivå](https://azure.microsoft.com/pricing/details/iot-hub/).

  * **Från e-post**: Detta bör vara samma e-postadress som du använde när du konfigurerar SendGrid-tjänsten.

  > [!NOTE]
  > Kontrollera den **fäst på instrumentpanelen** alternativ för att göra det här programmet blir lättare att hitta i framtiden.
 
![IoTHub-distribution](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/iot-hub-deploy.png)

## <a name="build-and-upload-the-code"></a>Skapa och ladda upp koden

### <a name="start-vs-code"></a>Starta VS Code

- Kontrollera att din DevKit är **inte** ansluten till datorn.
- Starta VS Code.
- Anslut DevKit till din dator.

> [!NOTE]
> När du startar VS Code, får du ett felmeddelande om att den inte hittar Arduino IDE eller relaterade tavla paketet. Om du får det här felet, ska Stäng VS Code, launch Arduino IDE igen och VS Code hitta Arduino IDE-sökvägen korrekt.

### <a name="open-arduino-examples-folder"></a>Öppna Arduino exempel mapp

Expandera till vänster **ARDUINO exempel** bläddrar du till **exempel för MXCHIP AZ3166 > AzureIoT**, och välj **DoorMonitor**. Den här åtgärden öppnar ett nytt VS Code-fönster med en projektmapp, till exempel i den.

![Mini-solution-exempel](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/vscode-examples.png)

> [!NOTE]
> Du kan också öppna exemplet från kommandopaletten. Använd `Ctrl+Shift+P` (Mac OS: `Cmd+Shift+P`) Skriv för att öppna kommandopaletten **Arduino**, och leta upp och välj **Arduino: exempel**.

### <a name="provision-azure-services"></a>Etablera Azure-tjänster

Kör molnet etablering uppgiften i Lösningsfönstret:
- Typ `Ctrl+P` (Mac OS: `Cmd+P`).
- Ange `task cloud-provision` i textrutan.

I VS Code-terminalen hjälper en interaktiv kommandorad dig att etablera nödvändiga Azure-tjänster. Välj alla samma objekt i begärd listan som du tidigare har etablerats i [distribuera IoT Hub i Azure](#deploy-iot-hub-in-azure).

![Etablera i molnet](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/cloud-provision.png)

> [!NOTE]
> Om sidan låser sig i laddningsstatus när du försöker logga in på Azure, se [vanliga frågor och svar](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#page-hangs-when-log-in-azure) att lösa problemet. 

### <a name="build-and-upload-the-device-code"></a>Skapa och överföra kod för enhet

#### <a name="windows"></a>Windows

1. Använd `Ctrl+P` att köra `task device-upload`.
2. Terminalen uppmanas du att ange konfigurationsläge. För att göra det, håll ned knappen A, och sedan push- och släpp återställningsknappen. Skärmen visar DevKit ID-nummer och ordet *Configuration*.

Den här proceduren anger anslutningssträngen som har hämtats från den [etablera Azure-tjänster](#provision-azure-services) steg.

VS Code och sedan startar verifierar och laddar upp Arduino skiss till DevKit:

![enhet-överföringen](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/device-upload.png)

DevKit startar om och börjat köra koden.

> [!NOTE]
> Ibland kan du få ett ”fel: AZ3166: Okänd paketet” felmeddelande. Det här felet uppstår när paketindexet tavla inte uppdateras korrekt. Lös felet genom att referera till denna [vanliga frågor och svar](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

#### <a name="macos"></a>macOS

1. Placera DevKit i konfigurationsläge: Håll ned knappen A, och sedan push-meddelanden och versionen återställningsknappen. På skärmen visas ”Configuration”.
2. Använd `Cmd+P` att köra `task device-upload`.

Den här proceduren anger anslutningssträngen som har hämtats från den [etablera Azure-tjänster](#provision-azure-services) steg.

VS Code och sedan startar verifierar och laddar upp Arduino skiss till DevKit:

![enhet-överföringen](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/device-upload.png)

DevKit startar om och börjat köra koden.

> [!NOTE]
> Ibland kan du få ett ”fel: AZ3166: Okänd paketet” felmeddelande. Det här felet uppstår när paketindexet tavla inte uppdateras korrekt. Lös felet genom att referera till denna [vanliga frågor och svar](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>Testa projektet

Programmet initierar först när DevKit är när det finns ett stabilt magnetiska fält.

Efter initieringen `Door closed` visas på skärmen. Om det finns en ändring i fältet magnetiska, tillståndet ändras till `Door opened`. Varje gång dörren tillståndsändringar, du får ett e-postmeddelande. (Dessa e-postmeddelanden kan ta upp till fem minuter som ska tas emot.)

![Magneter nära sensorn: dörren stängd](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-closed.jpg "magneter nära sensorn: dörren stängd")

![Magneten flyttas från sensorn: dörren öppnas](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-opened.jpg "magneten flyttas från sensorn: dörren öppnas")

## <a name="problems-and-feedback"></a>Problem och feedback

Om du får problem kan se [vanliga frågor och svar](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) eller Anslut via följande kanaler:

* [Gitter.IM](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [StackOverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Nästa steg

Du har lärt dig hur du ansluter en DevKit enhet till din Azure IoT lösningsacceleratorn för fjärrövervakning och använda SendGrid-tjänsten för att skicka ett e-postmeddelande. Här följer nästa föreslagna steg:

* [IoT-Remote Monitoring solution accelerator översikt över Azure](https://docs.microsoft.com/azure/iot-suite/)
* [Anslut en enhet för MXChip IoT DevKit till programmet Azure IoT Central](https://docs.microsoft.com/microsoft-iot-central/howto-connect-devkit)
