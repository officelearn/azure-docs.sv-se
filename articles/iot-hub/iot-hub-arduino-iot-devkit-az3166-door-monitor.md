---
title: Få ett e-postmeddelande när dörren öppnas med SendGrid-tjänsten och Azure Functions | Microsoft Docs
description: Övervaka magnetiska sensor för att upptäcka när en dörr öppnas och använder Azure Functions för att skicka ett e-postmeddelande.
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
ms.date: 03/19/2018
ms.author: liydu
ms.openlocfilehash: 4eb13a588f0ffd1377caf3ce9bac4886f052a22f
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="door-monitor"></a>Dörren Övervakare          

IoT-DevKit MXChip innehåller en inbyggd magnetiska sensor. I det här projektet identifiera du förekomsten eller frånvaron av en närliggande starkt magnetiska fältet i det här fallet kommer från en liten. Permanentmagnet.

## <a name="what-you-learn"></a>Detta får du får lära dig

I det här projektet du lära dig:
- Hur du använder MXChip IoT DevKit magnetisk sensor för att identifiera att flytta en närliggande magneten.
- Hur du använder tjänsten SendGrid för att skicka ett meddelande till din e-postadress.

> [!NOTE]
> För en praktisk användning av det här projektet:
> - Montera en magneten i utkanten av en dörr.
> - Montera DevKit på dörren jamb nära magneten. Öppna eller stänga dörren utlöser sensor, vilket resulterar i att får ett e-postmeddelande för händelsen.

## <a name="what-you-need"></a>Vad du behöver

Slutför den [Getting Started Guide]({{"/docs/get-started/" | absolute_url }}) till:

* Har din DevKit ansluten till Wi-Fi
* Förbereda utvecklingsmiljön

En aktiv Azure-prenumeration. Om du inte har någon, kan du registrera via någon av följande metoder:

* Aktivera en [kostnadsfria 30-dagars utvärderingsversion av Microsoft Azure-konto](https://azure.microsoft.com/free/).
* Anspråk din [Azure-kredit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) om du prenumererar på en MSDN- eller Visual Studio.

## <a name="deploy-sendgrid-service-in-azure"></a>Distribuera SendGrid tjänst i Azure

[SendGrid](https://sendgrid.com/) är en plattform för leverans av molnbaserade e-post. Den här tjänsten används för att skicka e-postmeddelanden.

> [!NOTE]
> Om du redan har distribuerat en tjänst för SendGrid du kan fortsätta direkt till [distribuera IoT-hubb i Azure](#deploy-iot-hub-in-azure).

### <a name="sendgrid-deployment"></a>SendGrid-distribution

För att etablera Azure-tjänster, använda den **till Azure** knappen. Den här knappen kan snabbt och enkelt distribution av öppen källkod projekt till Microsoft Azure.

Klicka på den **till Azure** knappen nedan. 

[![Distribuera till Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2FSendGridDeploy%2Fazuredeploy.json)

Då visas följande sida.

> [!NOTE]
> Om du inte ser följande sida kan du behöva först logga in på ditt Azure-konto.

Fyllt i registreringsformuläret:

  * **Resursgruppen**: skapa en resursgrupp som värd för tjänsten SendGrid, eller Använd en befintlig. Se [hantera Azure-resurser med hjälp av resursgrupper](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).

  * **Namnet**: namn för din SendGrid-tjänst. Välj ett unikt namn som skiljer sig från andra tjänster som du kan ha.

  * **Lösenordet**: tjänsten kräver ett lösenord som inte för något i det här projektet.

  * **E-post**: den SendGrid tjänsten skickar verifiering till e-postadressen.

  > [!NOTE]
  > Kontrollera den **fäst på instrumentpanelen** alternativet för att göra det här programmet ska vara lättare att hitta i framtiden.
 
![SendGrid-distribution](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-deploy.png)

### <a name="sendgrid-api-key-creation"></a>Skapa en SendGrid API-nyckel

När distributionen lyckas, klickar du på den och klicka sedan på den **hantera** knappen. Du kommer till sidan SendGrid och behöver verifiera din e-postadress.

![Hantera SendGrid](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-manage.png)

På sidan SendGrid **inställningar** > **API-nycklar** > **skapa API-nyckeln**. Indata i **API-nyckelnamn** och på **skapa vy**.

![Skapa SendGrid API först](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-first.png)

![Skapa SendGrid API andra](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-second.png)

API-nyckeln visas bara en gång. Kom ihåg att kopiera och spara den på ett säkert sätt, eftersom den används i nästa steg.

## <a name="deploy-iot-hub-in-azure"></a>Distribuera IoT-hubb i Azure

Följande steg etablerar andra Azure IoT-relaterade tjänster och distribuera Azure Functions för det här projektet.

Klicka på den **till Azure** knappen nedan. 

[![Distribuera till Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2Fazuredeploy.json)

Då visas följande sida.

> [!NOTE]
> Om du inte ser följande sida, kan du behöva först logga in på ditt Azure-konto.

Fyllt i registreringsformuläret:

  * **Resursgruppen**: skapa en resursgrupp som värd för tjänsten SendGrid, eller Använd en befintlig. Se [hantera Azure-resurser med hjälp av resursgrupper](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).

  * **IOT-Hubbnamnet**: namn för din IoT-hubb. Välj ett unikt namn som skiljer sig från andra tjänster som du kan ha.

  * **IOT-hubb Sku**: F1 (begränsad ett per prenumeration) är ledig. Du kan se mer prisinformation på [prissättnings- och skalningsnivå](https://azure.microsoft.com/pricing/details/iot-hub/).

  * **Från e-**: Detta bör vara samma e-postadress som du använde när du konfigurerar tjänsten SendGrid.

  > [!NOTE]
  > Kontrollera den **fäst på instrumentpanelen** alternativet för att göra det här programmet ska vara lättare att hitta i framtiden.
 
![IoTHub-distribution](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/iot-hub-deploy.png)

## <a name="build-and-upload-the-code"></a>Skapa och ladda upp koden

### <a name="start-vs-code"></a>Starta VS-kod

- Se till att din DevKit **inte** ansluten till datorn.
- Starta VS-kod.
- Anslut DevKit till datorn.

> [!NOTE]
> Du kan få ett felmeddelande om att det går inte att hitta Arduino IDE- eller relaterade board paket när du startar VS-kod. Om du får detta felmeddelande ska Stäng VS-kod, starta Arduino IDE igen och VS kod hitta Arduino IDE-sökvägen korrekt.

### <a name="open-arduino-examples-folder"></a>Öppna Arduino exempel mapp

Expandera till vänster **ARDUINO exempel** bläddrar du till **exempel MXCHIP AZ3166 > AzureIoT**, och välj **DoorMonitor**. Den här åtgärden öppnar ett nytt kod jämfört med en projektmapp i den.

![Mini-solution-exempel](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/vscode-examples.png)

> [!NOTE]
> Du kan också öppna exempel kommandot paletten. Använd `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) för att öppna paletten kommando skriver **Arduino**, och sedan söka efter och välj **Arduino: exempel**.

### <a name="provision-azure-services"></a>Etablera Azure-tjänster

Kör molnet etablering aktivitet i fönstret lösningen:
- Typen `Ctrl+P` (macOS: `Cmd+P`).
- Ange `task cloud-provision` i textrutan.

I terminalen VS kod hjälper dig att etablera nödvändiga Azure-tjänster i en interaktiv kommandorad. Välj alla samma objekt i listan tillfrågas som du tidigare har etablerats i [distribuera IoT-hubb i Azure](#deploy-iot-hub-in-azure).

![Etablera moln](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/cloud-provision.png)

> [!NOTE]
> Om sidan låser sig statusen lästes in när du försöker logga in på Azure, referera till [vanliga frågor och svar](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#page-hangs-when-log-in-azure) att lösa problemet. 

### <a name="build-and-upload-the-device-code"></a>Skapa och skicka kod för enheten

#### <a name="windows"></a>Windows

1. Använd `Ctrl+P` att köra `task device-upload`.
2. Terminalen uppmanas du att ange konfigurationsläge. Om du vill göra det, hålla ned A, och sedan push och släpp återställningsknappen. Skärmen visar DevKit ID-nummer och ordet *Configuration*.

Den här proceduren anger anslutningssträngen som har hämtats från den [etablera Azure services](#provision-azure-services) steg.

VS-kod och sedan startar verifiering och ladda upp Arduino skissa till DevKit:

![enheten-överföringen](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/device-upload.png)

DevKit startar om och börjar köras koden.

> [!NOTE]
> Ibland kan du få ett ”fel: AZ3166: Okänd paketet” felmeddelande. Det här felet uppstår när paketindexet board inte uppdateras på rätt sätt. För att lösa det här felet finns i det här [vanliga frågor och svar](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

#### <a name="macos"></a>macOS

1. Placera DevKit i konfigurationsläge: Håll ned knappen A, och sedan push och versionen återställningsknappen. Skärmen visar ”Configuration”.
2. Använd `Cmd+P` att köra `task device-upload`.

Den här proceduren anger anslutningssträngen som har hämtats från den [etablera Azure services](#provision-azure-services) steg.

VS-kod och sedan startar verifiering och ladda upp Arduino skissa till DevKit:

![enheten-överföringen](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/device-upload.png)

DevKit startar om och börjar köras koden.

> [!NOTE]
> Ibland kan du få ett ”fel: AZ3166: Okänd paketet” felmeddelande. Det här felet uppstår när paketindexet board inte uppdateras på rätt sätt. För att lösa det här felet finns i det här [vanliga frågor och svar](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>Testa projektet

Programmet initierar först när DevKit med ett stabilt magnetiska fält.

Efter initieringen `Door closed` visas på skärmen. När en ändring i fältet magnetiska tillståndet ändras till `Door opened`. Varje gång tillståndsändringar dörren, du får ett e-postmeddelande. (Dessa e-postmeddelanden kan ta upp till fem minuter som ska tas emot.)

![Magneter nära sensorn: dörren stängd](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-closed.jpg "magneter nära sensorn: dörren stängd")

![Magneten flyttas från sensorn: dörren öppnas](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-opened.jpg "magneten flyttas från sensorn: dörren öppnas")

## <a name="problems-and-feedback"></a>Problem och feedback

Om du stöter på problem, läser du [vanliga frågor och svar](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) eller Anslut via följande kanaler:

* [Gitter.IM](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [StackOverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Nästa steg

Du har lärt dig hur du ansluter en DevKit enhet till din Azure IoT-Fjärrövervaknings lösningsaccelerator och använda SendGrid-tjänsten för att skicka ett e-postmeddelande. Här är de föreslagna nästa steg:

* [Azure IoT fjärråtkomst övervakning solution accelerator-översikt](https://docs.microsoft.com/azure/iot-suite/)
* [Anslut en MXChip IoT DevKit enhet till din Azure IoT centralt program](https://docs.microsoft.com/microsoft-iot-central/howto-connect-devkit)
