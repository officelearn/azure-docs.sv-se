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
ms.openlocfilehash: a620b592a33f9de11de53d623d257f203da2157b
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57547709"
---
# <a name="door-monitor"></a>Dörren Övervakare          

MXChip IoT DevKit innehåller en inbyggd magnetiska sensorn. I det här projektet identifiera du närvaron eller frånvaron av ett i närheten stark magnetiska fält – i det här fallet kommer från en liten Permanentmagnet.

## <a name="what-you-learn"></a>Detta får du får lära dig

I det här projektet lär du dig:
- Hur du använder MXChip IoT DevKit magnetiska sensorn för att identifiera att flytta en närliggande magneten.
- Hur du använder SendGrid-tjänsten för att skicka ett meddelande till din e-postadress.

> [!NOTE]
> Utför följande uppgifter för en praktisk användning av det här projektet är:
> - Montera en magneten i utkanten av en dörr.
> - Montera DevKit på dörren jamb nära magneten. Öppna eller stänga dörren utlöser sensorn, vilket resulterar i att få ett e-postmeddelande för händelsen.

## <a name="what-you-need"></a>Vad du behöver

Slutför den [Kom igång med](iot-hub-arduino-iot-devkit-az3166-get-started.md) till:

* Har din DevKit som är ansluten till Wi-Fi
* Förbereda utvecklingsmiljön

En aktiv Azure-prenumeration. Om du inte har någon kan registrera du via någon av följande metoder:

* Aktivera en [kostnadsfria 30-dagars utvärderingsversion av Microsoft Azure-konto](https://azure.microsoft.com/free/).
* Anspråk din [Azure-kredit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) om du är MSDN eller Visual Studio-prenumerant.

## <a name="deploy-the-sendgrid-service-in-azure"></a>Distribuera SendGrid-tjänsten i Azure

[SendGrid](https://sendgrid.com/) är en plattform för leverans av molnbaserade e-post. Den här tjänsten används för att skicka e-postmeddelanden.

> [!NOTE]
> Om du redan har distribuerat en SendGrid-tjänsten, du kan fortsätta direkt till [distribuera IoT Hub i Azure](#deploy-iot-hub-in-azure).

### <a name="sendgrid-deployment"></a>SendGrid Deployment

För att etablera Azure-tjänster som använder den **distribuera till Azure** knappen. Den här knappen kan du snabbt och enkelt distribuera dina projekt med öppen källkod till Microsoft Azure.

Klicka på den **distribuera till Azure** knappen nedan. 

[![Distribuera till Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2FSendGridDeploy%2Fazuredeploy.json)

Om du inte redan är inloggad på ditt Azure-konto, logga in nu. 

Du kan nu se SendGrid fyllt i registreringsformuläret.

![SendGrid Deployment](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-deploy.png)

Har fyllt i registreringsformuläret:

   * **Resursgrupp**: Skapa en resursgrupp för att vara värd för SendGrid-tjänsten eller Använd en befintlig. Se [hantera Azure-resurser med hjälp av resursgrupper](../azure-resource-manager/manage-resource-groups-portal.md).

   * **Namn**: Namnet på SendGrid-tjänsten. Välj ett unikt namn som skiljer sig från andra tjänster som du kan ha.

   * **Lösenord**: Tjänsten kräver ett lösenord som inte ska användas för vad som helst i det här projektet.

   * **e-post**: SendGrid-tjänsten skickar verifiering till e-postadressen.

Kontrollera den **fäst på instrumentpanelen** så att det här programmet blir lättare att hitta i framtiden och sedan klicka på **köp** skicka formuläret av inloggning.
 
### <a name="sendgrid-api-key-creation"></a>Skapa en SendGrid API-nyckel

När distributionen är klar klickar du på den och klicka sedan på den **hantera** knappen. Din SendGrid-konto visas, där du behöver verifiera din e-postadress.

![SendGrid Manage](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-manage.png)

På sidan SendGrid **inställningar** > **API-nycklar** > **skapa API-nyckel**.

![SendGrid först skapa API](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-first.png)

På den **skapa API-nyckel** sidan, ange den **API-nyckelnamn** och klicka på **skapa vy**.

![Skapa SendGrid API andra](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-second.png)

Din API-nyckel visas bara en gång. Glöm inte att kopiera och lagra den på ett säkert sätt, eftersom den används i nästa steg.

## <a name="deploy-iot-hub-in-azure"></a>Distribuera IoT-hubb i Azure

Följande steg kommer att etablera andra Azure-IoT-relaterade tjänster och distribuera Azure Functions för det här projektet.

Klicka på den **distribuera till Azure** knappen nedan. 

[![Distribuera till Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2Fazuredeploy.json)

Fyllt i registreringsformuläret visas.

![IoTHub-distribution](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/iot-hub-deploy.png)

Fyll i fälten i fyllt i registreringsformuläret.

   * **Resursgrupp**: Skapa en resursgrupp för att vara värd för SendGrid-tjänsten eller Använd en befintlig. Se [hantera Azure-resurser med hjälp av resursgrupper](../azure-resource-manager/manage-resource-groups-portal.md).

   * **IOT-Hubbnamn**: Namn för din IoT-hubb. Välj ett unikt namn som skiljer sig från andra tjänster som du kan ha.

   * **IOT Hub Sku**: F1 (begränsat till en per prenumeration) är kostnadsfri. Du kan se mer information om priser på den [prissättningssidan](https://azure.microsoft.com/pricing/details/iot-hub/).

   * **Från e-post**: Fältet måste innehålla samma e-postadress som du använde när du konfigurerar SendGrid-tjänsten.

Kontrollera den **fäst på instrumentpanelen** så att det här programmet blir lättare att hitta i framtiden och sedan klicka på **köp** när du är redo att fortsätta till nästa steg.
 
## <a name="build-and-upload-the-code"></a>Skapa och ladda upp koden

Sedan läsa in exempelkoden i VS Code och etablera nödvändiga Azure-tjänster.

### <a name="start-vs-code"></a>Starta VS Code

- Kontrollera att din DevKit är **inte** ansluten till datorn.
- Starta VS Code.
- Anslut DevKit till din dator.

> [!NOTE]
> När du startar VS Code, får du ett felmeddelande om att den inte hittar Arduino IDE eller relaterade tavla paketet. Om du får det här felet, ska Stäng VS Code, launch Arduino IDE igen och VS Code hitta Arduino IDE-sökvägen korrekt.

### <a name="open-arduino-examples-folder"></a>Öppna Arduino exempel mapp

Expandera till vänster **ARDUINO exempel** bläddrar du till **exempel för MXCHIP AZ3166 > AzureIoT**, och välj **DoorMonitor**. Den här åtgärden öppnar ett nytt VS Code-fönster med en projektmapp, till exempel i den.

![mini-solution-examples](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/vscode-examples.png)

Du kan också öppna exempelappen från kommandopaletten. Använd `Ctrl+Shift+P` (Mac OS: `Cmd+Shift+P`) Skriv för att öppna kommandopaletten **Arduino**, och leta upp och välj **Arduino: Exempel**.

### <a name="provision-azure-services"></a>Etablera Azure-tjänster

Kör molnet etablering uppgiften i Lösningsfönstret:
- Type `Ctrl+P` (macOS: `Cmd+P`).
- Ange `task cloud-provision` i textrutan.

I VS Code-terminalen hjälper en interaktiv kommandorad dig att etablera nödvändiga Azure-tjänster. Välj alla samma objekt i begärd listan som du tidigare har etablerats i [distribuera IoT Hub i Azure](#deploy-iot-hub-in-azure).

![Etablera i molnet](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/cloud-provision.png)

> [!NOTE]
> Om sidan låser sig i laddningsstatus när du försöker logga in på Azure, referera till den [”page gnorera när du loggar in” avsnittet av vanliga IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#page-hangs-when-log-in-azure) att lösa problemet. 

### <a name="build-and-upload-the-device-code"></a>Skapa och överföra kod för enhet

Ladda sedan upp koden för enheten.

#### <a name="windows"></a>Windows

1. Använd `Ctrl+P` att köra `task device-upload`.

2. Terminalen uppmanas du att ange konfigurationsläge. För att göra det, håll ned knappen A, och sedan push- och släpp återställningsknappen. Skärmen visar DevKit ID-nummer och ordet *Configuration*.

#### <a name="macos"></a>macOS

1. Placera DevKit i konfigurationsläge: Håll ned knappen A, och sedan push- och släpp återställningsknappen. På skärmen visas ”Configuration”.

2. Klicka på `Cmd+P` att köra `task device-upload`.

#### <a name="verify-upload-and-run-the-sample-app"></a>Verifiera, ladda upp och köra exempelappen

Anslutningssträngen som har hämtats från den [etablera Azure-tjänster](#provision-azure-services) steg nu är inställd. 

VS Code och sedan startar verifierar och laddar upp Arduino skiss till DevKit.

![enhet-överföringen](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/device-upload.png)

DevKit startar om och börjat köra koden.

> [!NOTE]
> Ibland kan du få ett ”fel: AZ3166: Okänd package ”felmeddelande. Det här felet uppstår när paketindexet tavla inte uppdateras korrekt. Lös felet genom att referera till den [utveckling delen av vanliga IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>Testa projektet

Programmet initierar först när DevKit är när det finns ett stabilt magnetiska fält.

Efter initieringen `Door closed` visas på skärmen. Om det finns en ändring i fältet magnetiska, tillståndet ändras till `Door opened`. Varje gång dörren tillståndsändringar, du får ett e-postmeddelande. (Dessa e-postmeddelanden kan ta upp till fem minuter som ska tas emot.)

![Magneter nära sensorn: Luckan är stängd](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-closed.jpg "magneter nära sensorn: Luckan är stängd")

![Magneten flyttas från sensorn: Luckan är öppnad](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-opened.jpg "magneten flyttas från sensorn: Dörren öppnas")

## <a name="problems-and-feedback"></a>Problem och feedback

Om du får problem kan se den [IoT DevKit vanliga frågor och svar](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) eller Anslut via följande kanaler:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Nästa steg

Du har lärt dig hur du ansluter en DevKit enhet till din Azure IoT lösningsacceleratorn för fjärrövervakning och används SendGrid-tjänsten för att skicka ett e-postmeddelande. Här följer nästa föreslagna steg:

* [IoT-Remote Monitoring solution accelerator översikt över Azure](https://docs.microsoft.com/azure/iot-suite/)
* [Anslut en enhet för MXChip IoT DevKit till programmet Azure IoT Central](https://docs.microsoft.com/microsoft-iot-central/howto-connect-devkit)
