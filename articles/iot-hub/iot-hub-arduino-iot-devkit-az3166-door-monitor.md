---
title: Skicka e-post när dörren öppnas med Azure Functions
description: Övervaka den magnetiska sensorn för att identifiera när en dörr öppnas och använda Azure Functions för att skicka ett e-postmeddelande.
author: liydu
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 03/19/2018
ms.author: liydu
ms.openlocfilehash: 6bebe8ac6b9869466938600d6267fd0062c84477
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75977303"
---
# <a name="door-monitor----using-azure-functions-and-sendgrid-send-email-when-a-door-is-opened"></a>Dörrövervakare – Med Hjälp av Azure Functions och SendGrid skickar du e-post när en dörr öppnas           

MXChip IoT DevKit innehåller en inbyggd magnetisk sensor. I det här projektet upptäcker du närvaron eller frånvaron av ett närliggande starkt magnetfält - i det här fallet kommer från en liten, permanent magnet.

## <a name="what-you-learn"></a>Detta får du får lära dig

I det här projektet lär du dig:
- Hur man använder MXChip IoT DevKit magnetiska sensor för att upptäcka förflyttning av en närliggande magnet.
- Så här använder du SendGrid-tjänsten för att skicka ett meddelande till din e-postadress.

> [!NOTE]
> För en praktisk användning av detta projekt, utföra följande uppgifter:
> - Montera en magnet på kanten av en dörr.
> - Montera DevKit på dörren jamb nära magneten. Om du öppnar eller stänger dörren utlöses sensorn, vilket resulterar i att du får ett e-postmeddelande om händelsen.

## <a name="what-you-need"></a>Vad du behöver

Avsluta [komma igång-guiden](iot-hub-arduino-iot-devkit-az3166-get-started.md) till:

* Ha din DevKit ansluten till Wi-Fi
* Förbereda utvecklingsmiljön

En aktiv Azure-prenumeration. Om du inte har en, kan du registrera dig via någon av dessa metoder:

* Aktivera ett [kostnadsfritt 30-dagars testversionskonto för Microsoft Azure](https://azure.microsoft.com/free/).
* Gör anspråk på din [Azure-kredit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) om du är msdn- eller Visual Studio-prenumerant.

## <a name="deploy-the-sendgrid-service-in-azure"></a>Distribuera SendGrid-tjänsten i Azure

[SendGrid](https://sendgrid.com/) är en molnbaserad e-postleveransplattform. Den här tjänsten används för att skicka e-postmeddelanden.

> [!NOTE]
> Om du redan har distribuerat en SendGrid-tjänst kan du gå direkt till [Distribuera IoT Hub i Azure](#deploy-iot-hub-in-azure).

### <a name="sendgrid-deployment"></a>SendGrid-distribution

Om du vill etablera Azure-tjänster använder du knappen **Distribuera till Azure.** Med den här knappen kan du snabbt och enkelt distribuera dina projekt med öppen källkod till Microsoft Azure.

Klicka på knappen **Distribuera till Azure** nedan. 

[![Distribuera till Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2FSendGridDeploy%2Fazuredeploy.json)

Om du inte redan är inloggad på ditt Azure-konto loggar du in nu. 

Nu visas registreringsformuläret för SendGrid.

![SendGrid-distribution](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-deploy.png)

Fyll i registreringsformuläret:

   * **Resursgrupp**: Skapa en resursgrupp som värd för SendGrid-tjänsten eller använd en befintlig. Se [Använda resursgrupper för att hantera dina Azure-resurser](../azure-resource-manager/management/manage-resource-groups-portal.md).

   * **Namn**: Namnet på din SendGrid-tjänst. Välj ett unikt namn, som skiljer sig från andra tjänster du kan ha.

   * **Lösenord**: Tjänsten kräver ett lösenord, som inte kommer att användas för något i det här projektet.

   * **E-post:** SendGrid-tjänsten skickar verifiering till den här e-postadressen.

Kontrollera alternativet **Fäst på instrumentpanelen** för att göra det här programmet lättare att hitta i framtiden och klicka sedan på **Köp** för att skicka inloggningsformuläret.
 
### <a name="sendgrid-api-key-creation"></a>Skapa SendGrid API-nyckel

När distributionen är klar klickar du på den och sedan på knappen **Hantera.** Din SendGrid-kontosida visas, där du måste verifiera din e-postadress.

![SendGrid Hantera](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-manage.png)

Klicka på **Inställningar** > **API-nycklar** > **Skapa API-nyckel**på sidan SendGrid .

![SendGrid Skapa API först](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-first.png)

På sidan **Skapa API-nyckel** anger du **API-nyckelnamnet** och klickar på **Skapa & Visa**.

![SendGrid Skapa API Andra](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-second.png)

Din API-nyckel visas bara en gång. Var noga med att kopiera och lagra den på ett säkert sätt, eftersom det används i nästa steg.

## <a name="deploy-iot-hub-in-azure"></a>Distribuera IoT-hubb i Azure

Följande steg kommer att etablera andra Azure IoT-relaterade tjänster och distribuera Azure Functions för det här projektet.

Klicka på knappen **Distribuera till Azure** nedan. 

[![Distribuera till Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2Fazuredeploy.json)

Registreringsformuläret visas.

![IoTHub-distribution](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/iot-hub-deploy.png)

Fyll i fälten i registreringsformuläret.

   * **Resursgrupp**: Skapa en resursgrupp som värd för SendGrid-tjänsten eller använd en befintlig. Se [Använda resursgrupper för att hantera dina Azure-resurser](../azure-resource-manager/management/manage-resource-groups-portal.md).

   * **Iot Hub Namn:** Namnet på din IoT hub. Välj ett unikt namn, som skiljer sig från andra tjänster du kan ha.

   * **Iot Hub Sku:** F1 (begränsad till en per prenumeration) är gratis. Du kan se mer prisinformation på [prissidan](https://azure.microsoft.com/pricing/details/iot-hub/).

   * **Från e-post**: Det här fältet ska vara samma e-postadress som du använde när du konfigurerade SendGrid-tjänsten.

Kontrollera alternativet **Fäst på instrumentpanelen** för att göra det här programmet lättare att hitta i framtiden och klicka sedan på **Köp** när du är redo att fortsätta till nästa steg.
 
## <a name="build-and-upload-the-code"></a>Skapa och ladda upp koden

Läs sedan in exempelkoden i VS-kod och etablera nödvändiga Azure-tjänster.

### <a name="start-vs-code"></a>Starta VS-kod

- Kontrollera att Din DevKit **inte** är ansluten till datorn.
- Starta VS Code.
- Anslut DevKit till datorn.

> [!NOTE]
> När du startar VS-kod kan du få ett felmeddelande om att den inte kan hitta Arduino IDE eller relaterade anslagstavlor. Om du får det här felet stänger du VS-koden, startar Arduino IDE igen och VS-koden ska hitta Arduino IDE-sökvägen korrekt.

### <a name="open-arduino-examples-folder"></a>Öppna mappen Arduino Examples

Expandera avsnittet **ARDUINO EXAMPLES på** vänster sida, bläddra till **Exempel för MXCHIP AZ3166 > AzureIoT**och välj **DoorMonitor**. Den här åtgärden öppnar ett nytt VS-kodfönster med en projektmapp i.

![mini-lösning-exempel](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/vscode-examples.png)

Du kan också öppna exempelappen från kommandopaletten. Använd `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) för att öppna kommandopaletten, skriv **Arduino**och leta sedan upp och välja **Arduino: Exempel**.

### <a name="provision-azure-services"></a>Tillhandahålla Azure-tjänster

I lösningsfönstret kör du aktiviteten för molnetablering:
- Typ `Ctrl+P` (macOS: `Cmd+P`).
- Skriv `task cloud-provision` in den medföljande textrutan.

I VS-kodterminalen guidar en interaktiv kommandorad dig genom att etablera de nödvändiga Azure-tjänsterna. Markera alla samma objekt i den informationslista som du tidigare har etablerat i [Deploy IoT Hub i Azure](#deploy-iot-hub-in-azure).

![Molnetablering](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/cloud-provision.png)

> [!NOTE]
> Om sidan låser sig i inläsningsstatus när du försöker logga in på Azure läser du [avsnittet "Sidhanges när du loggar in" i vanliga frågor och svar om IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#page-hangs-when-log-in-azure) för att lösa problemet. 

### <a name="build-and-upload-the-device-code"></a>Skapa och ladda upp enhetskoden

Ladda sedan upp koden för enheten.

#### <a name="windows"></a>Windows

1. Används `Ctrl+P` för `task device-upload`att köra .

2. Terminalen uppmanar dig att gå in i konfigurationsläge. För att göra det, håll ned knapp A och tryck sedan på och släpp återställningsknappen. På skärmen visas DevKit-identifieringsnumret och ordet *Konfiguration*.

#### <a name="macos"></a>macOS

1. Sätt DevKit i konfigurationsläge: Håll ned knapp A och tryck sedan på och släpp återställningsknappen. Skärmen visar "Konfiguration".

2. Klicka `Cmd+P` här `task device-upload`om du vill köra .

#### <a name="verify-upload-and-run-the-sample-app"></a>Verifiera, ladda upp och köra exempelappen

Anslutningssträngen som hämtas från steget [Etablera Azure-tjänster](#provision-azure-services) har nu angetts. 

VS-koden börjar sedan verifiera och ladda upp Arduino-skissen till DevKit.

![ladda upp enheten](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/device-upload.png)

DevKit startar om och börjar köra koden.

> [!NOTE]
> Ibland kan felmeddelandet "Fel: AZ3166: Okänt paket" visas. Det här felet uppstår när kartongpaketindexet inte uppdateras korrekt. LÃ¶s problemet genom att [fÃ¶ã¶ ge dig avsnittet utveckling i vanliga frågor och svar om IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>Testa projektet

Programmet initieras först när DevKit är i närvaro av ett stabilt magnetfält.

Efter initieringen `Door closed` visas på skärmen. När magnetfältet ändras ändras tillståndet till `Door opened`. Varje gång dörrtillståndet ändras får du ett e-postmeddelande. (Det kan ta upp till fem minuter innan dessa e-postmeddelanden tas emot.)

![Magneter nära sensorn: Dörren stängd](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-closed.jpg "Magneter nära sensorn: Dörren stängd")

![Magnet flyttas bort från sensorn: Dörren öppnas](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-opened.jpg "Magnet flyttas bort från sensorn: Dörren öppnas")

## <a name="problems-and-feedback"></a>Problem och feedback

Om du stöter på problem kan du läsa vanliga frågor och svar om [IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) eller ansluta med följande kanaler:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stackspill](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Nästa steg

Du har lärt dig hur du ansluter en DevKit-enhet till din Azure IoT Remote Monitoring-lösningsaccelerator och använde SendGrid-tjänsten för att skicka ett e-postmeddelande. Här är de föreslagna nästa stegen:

* [Azure IoT Remote Monitoring solution accelerator översikt](https://docs.microsoft.com/azure/iot-suite/)
* [Ansluta en MXChip IoT DevKit-enhet till ditt Azure IoT Central-program](/azure/iot-central/core/howto-connect-devkit)
