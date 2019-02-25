---
title: E-åtgärd i fjärrövervakning – Azure | Microsoft Docs
description: Den här guiden visar hur du lägger till en e poståtgärd på en ny eller befintlig regel.
author: asdonald
manager: hegate
ms.author: asdonald
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/12/2018
ms.topic: conceptual
ms.openlocfilehash: 3e9d7191af3a870f60b40311be03e0ef9d9bf7ad
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/24/2019
ms.locfileid: "56750216"
---
# <a name="add-an-email-action"></a>Lägg till en e poståtgärd

E-post åtgärder att se att du aldrig missar aviseringar. Du kan lägga till en e poståtgärd till en befintlig regel eller när du skapar en ny regel.

För att slutföra stegen i den här guiden måste behöver du en distribuerad instans av lösningsacceleratorn för fjärrövervakning i din Azure-prenumeration.

Om du vill skapa eller ändra en regel måste du vara en [ **administratör**, eller har rätt behörigheter](iot-accelerators-remote-monitoring-rbac.md).

## <a name="edit-an-existing-rule"></a>Redigera en befintlig regel

Följ dessa steg om du vill lägga till en e poståtgärd i en befintlig regel:

1. Gå till lösningen för fjärrövervakning.

1. Från den **instrumentpanelen**, navigera till den **regler** sidan:

    ![Sidan principregler](./media/iot-accelerators-remote-monitoring-email-actions/rules-email.png)

1. Klicka på kryssrutan bredvid den befintliga regeln att ändra och klicka sedan på **redigera** högst upp. Ett redigerbart **regeln** visas panelen.

1. I den **åtgärd** avsnittet, växla **e-postaktiverade** till **på**.

1. Första gången du aktiverar en e poståtgärd i lösningsaccelerator, måste du [logga in på Outlook](#outlook).

1. Ange en e-postadress i rutan mottagare och tryck på den **RETUR** nyckel för varje e-postadress att lägga till:

    ![Adresspost](./media/iot-accelerators-remote-monitoring-email-actions/address-email.png)

1. Ange ett ämne för e-postmeddelandet.

1. Ange ytterligare anteckningar för e-postmottagare som oformaterad text. Du kan använda HTML-formatering om du [redigera e-postmallen](#htmledit).

1. Se till att den **Regelstatus** är inställd på **aktiverad**.

1. Klicka på **Verkställ**.

## <a name="create-a-new-rule"></a>Skapa en ny regel

Följ dessa steg för att lägga till en e poståtgärd när du skapar en ny regel:

1. Gå till lösningen för fjärrövervakning.

1. Från den **instrumentpanelen**, navigera till den **regler** sidan:

    ![Sidan principregler](./media/iot-accelerators-remote-monitoring-email-actions/rules-email.png)

1. Följ stegen i den [skapa en regel del](iot-accelerators-remote-monitoring-automate.md#create-a-rule). Följ stegen i den [skapa en avancerad regel](iot-accelerators-remote-monitoring-automate.md#create-an-advanced-rule) avsnittet fram till den punkt där du anger en **allvarlighetsgrad**. Klicka inte på **tillämpa** ännu.

1. I den **åtgärd** avsnittet, växla **e-postaktiverade** till **på**.

1. Första gången du aktiverar en e poståtgärd i lösningsaccelerator, måste du [logga in på Outlook](#outlook).

1. Ange en e-postadress i rutan mottagare och tryck på den **RETUR** nyckel för varje e-postadress att lägga till:

    ![Adresspost](./media/iot-accelerators-remote-monitoring-email-actions/address-email.png)

1. Ange ett ämne för e-postmeddelandet.

1. Ange ytterligare anteckningar för e-postmottagare som oformaterad text. Du kan använda HTML-formatering om du [redigera e-postmallen](#htmledit).

1. Se till att den **Regelstatus** är inställd på **aktiverad**.

1. Klicka på **Verkställ**.

Nu har aktiverats för din regel med en e poståtgärd. Varje gång åtgärden utlöses skickas ett nytt e-postmeddelande till mottagarna.

## Logga in till Outlook <a name="outlook"></a>

Första gången du aktiverar en e poståtgärd i utvecklingsacceleratorn, du måste logga in på Outlook. Den här åtgärden ställer in e-postkonto som skickar ut e-postmeddelanden.

> [!NOTE]
> Du skapar ett specifikt Outlook-konto för solution accelerator meddelanden och använda det kontot när du aktiverar din första e poståtgärd.

### <a name="contributor-role-outlook-setup"></a>Installationen av deltagare roll för Outlook

Om någon i den **deltagare** rollen i prenumerationen distribueras solution accelerator, programmet har inte tillräcklig behörighet för att konfigurera och verifiera e-poståtgärder via Webbgränssnittet.

Innan du börjar måste du skapa ett Outlook-konto du använder för att skicka e-postmeddelanden från din lösningsaccelerator.

Följande steg visar hur du ställer in och kontrollera e-poståtgärder manuellt:

1. Navigera till [Azure-portalen](https://portal.azure.com).

1. Navigera till resursgruppen för utvecklingsacceleratorn.

1. Klicka på den **Office 365-anslutningsappen**:

    ![API-anslutning](./media/iot-accelerators-remote-monitoring-email-actions/apiconnector.png)

1. Klicka på banderollen för att påbörja auktoriseringsprocessen:

    ![auktorisera](./media/iot-accelerators-remote-monitoring-email-actions/connector.png)

1. Klicka på **auktorisera**. Du uppmanas att logga in. Det konto som används för att logga in bör vara den e-postadressen programmet använder för att skicka e-postmeddelanden:

    ![Auktorisera knappen](./media/iot-accelerators-remote-monitoring-email-actions/authorize.png)

1. Klicka på **spara** längst ned på sidan. Din behörighet kommer att lyckas om popup-meddelandet är borta.

1. Så här ändrar du den e-postadress som meddelanden skickas från **redigera API-anslutningen**.

    ![ändra e-post](./media/iot-accelerators-remote-monitoring-email-actions/editemail.png)

### <a name="owner-role-outlook-setup"></a>Installationen av ägare roll för Outlook

Om någon i den **ägare** rollen i prenumerationen distribueras solution accelerator, programmet kan verifiera e-poståtgärder är korrekt inställda via Webbgränssnittet.

Innan du börjar måste du skapa ett Outlook-konto du använder för att skicka e-postmeddelanden från din lösningsaccelerator.

Följande steg hjälper dig att logga in och konfigurera e poståtgärder:

1. Klicka för att logga in på Outlook. Kommer du till Azure-portalen:

  ![Logga in till Outlook](./media/iot-accelerators-remote-monitoring-email-actions/owneroutlook-email.png)

1. Klicka på **auktorisera**. Du uppmanas att logga in. Det konto som används för att logga in bör vara den e-postadressen programmet använder för att skicka e-postmeddelanden:

1. Klicka på **Spara**. Gå tillbaka till utvecklingsacceleratorn och uppdatera sidan.

1. Om du har konfigurerat e-postmeddelandet, visas följande meddelande:

  ![Lyckad Outlook logga in](./media/iot-accelerators-remote-monitoring-email-actions/success-email.png)

## Anpassa e-post HTML <a name="htmledit"></a>

Out-of the box, lösningsacceleratorn för fjärrövervakning innehåller en grundläggande HTML-mall för e-postmeddelanden för åtgärden. E-postmallen används värden från inställningarna för e-post-åtgärd. Här är ett exempel e-postmeddelande:

![e-exempel](./media/iot-accelerators-remote-monitoring-email-actions/emailtemplate.png)

Följande steg visar hur du redigerar HTML-e-postmallen. Du kan till exempel ta med mer information eller lägga till anpassade avbildningar:

1. Klona databasen för Java eller .NET Remote Monitoring-GitHub:

1. Gå till platsen för e-mall:
  
    `Dotnet: device-telemetry\ActionsAgent\data\EmailTemplate.html`
  
    `Java device-telemetry/app/resources/data/EmailTemplate.html`

1. Du kan lägga till eller ta bort eventuella parametrar i den här mallen för att anpassa meddelandet. Du kan också lägga till, ta bort eller Ersätt anrop efter behov:

    Till exempel i .NET-kod:  `emailTemplate = emailTemplate.Replace("${subject}", emailAction.GetSubject());`

    Till exempel i Java-kod:  `this.emailTemplate.replace("${subject}", emailAction.GetSubject());`

1. Parametrar i mallen utgöras av `${...}`. Ta bort raden krävs för att ta bort en parameter. Om du vill lägga till en parameter, lägger du till en rad med värdet att infoga.

1. Lägg till bilder eller anpassad text genom att uppdatera filen EmailTemplate.HTML direkt.

## <a name="throttling"></a>Begränsning

Lösningsacceleratorn för fjärrövervakning använder Outlook för att skicka e-postmeddelanden. Outlook begränsar antalet e-postmeddelanden som skickas till [30 e-postmeddelanden per 1 minut](https://docs.microsoft.com/office365/servicedescriptions/exchange-online-service-description/exchange-online-limits#receiving-and-sending-limits). E-postklienter som tar emot e-postmeddelanden kan också begränsa hur många e-postmeddelanden per minut. Kontakta din specifika e-postklient på begränsningar. När du ställer in e-postmeddelande för en regel ska regeln beräkna genomsnittsvärden under en period av minst en minut och inte använda omedelbara värden:

![Genomsnittlig beräkning](./media/iot-accelerators-remote-monitoring-email-actions/calculation-email.png)

## <a name="next-steps"></a>Nästa steg

Den här guiden visar dig hur du lägger till en e poståtgärd på en ny eller befintlig regel inom en lösning för fjärrövervakning. Guiden visade också du och hur du redigerar den HTML som definierar meddelandeformatet.

Föreslagna nästa steg är att lära dig [hur du använder aviseringar och åtgärda enhetsproblem](iot-accelerators-remote-monitoring-maintain.md).
