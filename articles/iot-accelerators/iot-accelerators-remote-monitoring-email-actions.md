---
title: E-poståtgärd inom fjärrövervakning – Azure | Microsoft-dokument
description: Den här guiden visar hur du lägger till en e-poståtgärd i en ny eller befintlig regel.
author: dominicbetts
manager: hegate
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/12/2018
ms.topic: conceptual
ms.openlocfilehash: c192ba73da0cfaf1832b6a1e572bd71b250a976b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74168360"
---
# <a name="add-an-email-action"></a>Lägga till en e-poståtgärd

E-poståtgärder hjälper till att se till att du aldrig missar aviseringar. Du kan lägga till en e-poståtgärd i en befintlig regel eller när du skapar en ny regel.

För att kunna slutföra stegen i den här programguiden behöver du en distribuerad instans av lösningsacceleratorn för fjärrövervakning i din Azure-prenumeration.

Om du vill skapa eller ändra en regel måste du vara [ **administratör**eller ha rätt behörighet](iot-accelerators-remote-monitoring-rbac.md).

## <a name="edit-an-existing-rule"></a>Redigera en befintlig regel

Följ dessa steg för att lägga till en e-poståtgärd i en befintlig regel:

1. Navigera till lösningen för fjärrövervakning.

1. Gå till sidan **Regler** på sidan **Instrumentpanel:**

    ![Sidan Regler](./media/iot-accelerators-remote-monitoring-email-actions/rules-email.png)

1. Klicka på kryssrutan bredvid den befintliga regeln som ska ändras och klicka sedan på **Redigera** högst upp. En redigerbar **regelpanel** visas.

1. I avsnittet **Åtgärd** växlar du **e-post aktiverat** till **På**.

1. Första gången du aktiverar en e-poståtgärd i lösningsacceleratorn måste du [logga in i Outlook](#outlook).

1. Ange en e-postadress i mottagarrutan och tryck på **Retur** för varje e-postadress för att lägga till:

    ![Adresspost](./media/iot-accelerators-remote-monitoring-email-actions/address-email.png)

1. Ange ett ämne för e-postmeddelandet.

1. Ange eventuella ytterligare anteckningar för e-postmottagarna som oformaterad text. Du kan använda HTML-formatering om du [redigerar e-postmallen](#htmledit).

1. Kontrollera att **regelstatusen** är **aktiverad**.

1. Klicka på **Använd**.

## <a name="create-a-new-rule"></a>Skapa en ny regel

Följ dessa steg för att lägga till en e-poståtgärd när du skapar en ny regel:

1. Navigera till lösningen för fjärrövervakning.

1. Gå till sidan **Regler** på sidan **Instrumentpanel:**

    ![Sidan Regler](./media/iot-accelerators-remote-monitoring-email-actions/rules-email.png)

1. Följ stegen i avsnittet [Skapa en regel](iot-accelerators-remote-monitoring-automate.md#create-a-rule). Följ stegen i avsnittet [Skapa en avancerad regel](iot-accelerators-remote-monitoring-automate.md#create-an-advanced-rule) fram till den punkt där du anger en **allvarlighetsgrad**. Klicka inte på **Använd** ännu.

1. I avsnittet **Åtgärd** växlar du **e-post aktiverat** till **På**.

1. Första gången du aktiverar en e-poståtgärd i lösningsacceleratorn måste du [logga in i Outlook](#outlook).

1. Ange en e-postadress i mottagarrutan och tryck på **Retur** för varje e-postadress för att lägga till:

    ![Adresspost](./media/iot-accelerators-remote-monitoring-email-actions/address-email.png)

1. Ange ett ämne för e-postmeddelandet.

1. Ange eventuella ytterligare anteckningar för e-postmottagarna som oformaterad text. Du kan använda HTML-formatering om du [redigerar e-postmallen](#htmledit).

1. Kontrollera att **regelstatusen** är **aktiverad**.

1. Klicka på **Använd**.

Regeln med en e-poståtgärd är nu aktiverad. Varje gång åtgärden utlöses skickas ett nytt e-postmeddelande till mottagarna.

## <a name="sign-in-to-outlook"></a>Logga in i Outlook<a name="outlook"></a>

Första gången du aktiverar en e-poståtgärd i lösningsacceleratorn måste du logga in på Outlook. Den här åtgärden ställer in e-postkontot som skickar ut e-postmeddelandena.

> [!NOTE]
> Du bör skapa ett specifikt Outlook-konto bara för lösningsacceleratormeddelanden och använda det kontot när du aktiverar din första e-poståtgärd.

### <a name="contributor-role-outlook-setup"></a>Outlook-inställningar för deltagarroll

Om någon i **deltagarrollen** i prenumerationen har distribuerat lösningsacceleratorn har programmet inte tillräcklig behörighet för att konfigurera och verifiera e-poståtgärder via webbgränssnittet.

Innan du börjar skapar du ett Outlook-konto som ska användas för att skicka e-postmeddelanden från lösningsacceleratorn.

Följande steg visar hur du konfigurerar och verifierar e-poståtgärderna manuellt:

1. Navigera till [Azure-portalen](https://portal.azure.com).

1. Navigera till resursgruppen för din lösningsaccelerator.

1. Klicka på **office365-anslutningen:**

    ![API-anslutning](./media/iot-accelerators-remote-monitoring-email-actions/apiconnector1.png)

1. Klicka på banderollen för att påbörja auktoriseringsprocessen:

    ![auktorisera](./media/iot-accelerators-remote-monitoring-email-actions/connector1.png)

1. Klicka på **Auktorisera**. Du uppmanas att logga in. Kontot du använder för att logga in ska vara den e-postadress som programmet använder för att skicka e-postmeddelanden:

    ![Knappen Auktorisera](./media/iot-accelerators-remote-monitoring-email-actions/authorize.png)

1. Klicka på **Spara** längst ned. Ditt tillstånd kommer att lyckas om bannern är borta.

1. Om du vill ändra den e-postadress som meddelandena skickas från klickar du på **Redigera API-anslutning**.

    ![ändra e-post](./media/iot-accelerators-remote-monitoring-email-actions/editemail1.png)

### <a name="owner-role-outlook-setup"></a>Outlook-inställningar för ägarroll

Om någon i **ägarrollen** i prenumerationen har distribuerat lösningsacceleratorn kan programmet verifiera att e-poståtgärder har konfigurerats korrekt via webbgränssnittet.

Innan du börjar skapar du ett Outlook-konto som ska användas för att skicka e-postmeddelanden från lösningsacceleratorn.

Följande steg hjälper dig att logga in och konfigurera e-poståtgärder:

1. Klicka här om du vill logga in i Outlook. Du tas till Azure-portalen:

   ![Logga in i Outlook](./media/iot-accelerators-remote-monitoring-email-actions/owneroutlook-email.png)

1. Klicka på **Auktorisera**. Du uppmanas att logga in. Kontot du använder för att logga in ska vara den e-postadress som programmet använder för att skicka e-postmeddelanden:

1. Klicka på **Spara**. Återgå till lösningsacceleratorn och uppdatera sidan.

1. Om du har konfigurerat e-postmeddelandet visas följande meddelande:

   ![Lyckad Outlook-inloggning](./media/iot-accelerators-remote-monitoring-email-actions/success-email.png)

## <a name="customize-the-email-html"></a>Anpassa HTML-e-postmeddelandet<a name="htmledit"></a>

Out-of-the-box, fjärrövervakning lösning accelerator ger en grundläggande HTML-mall för åtgärd e-post. E-postmallen använder värden från e-åtgärdsinställningarna. Här är ett exempel på e-post:

![e-postexempel](./media/iot-accelerators-remote-monitoring-email-actions/emailtemplate1.png)

Följande steg visar hur du redigerar HTML-e-postmallen. Du kan till exempel inkludera mer information eller lägga till anpassade bilder:

1. Klona antingen Java- eller .NET Remote Monitoring GitHub-databasen:

1. Navigera till e-postmallens plats:
  
    `Dotnet: device-telemetry\ActionsAgent\data\EmailTemplate.html`
  
    `Java device-telemetry/app/resources/data/EmailTemplate.html`

1. Du kan lägga till eller ta bort alla parametrar i den här mallen för att anpassa meddelandet. Du kan också lägga till, ta bort eller ersätta samtal efter behov:

    I .NET-koden:`emailTemplate = emailTemplate.Replace("${subject}", emailAction.GetSubject());`

    Till exempel i Java-koden:`this.emailTemplate.replace("${subject}", emailAction.GetSubject());`

1. Parametrar i mallen har `${...}`formen av . Om du vill ta bort en parameter tar du bort den önskade raden. Om du vill lägga till en parameter lägger du till en rad med det värde som ska infogas.

1. Om du vill lägga till bilder eller anpassad text uppdaterar du filen EmailTemplate.HTML direkt.

## <a name="throttling"></a>Begränsning

Lösningsacceleratorn för fjärrövervakning använder Outlook för att skicka e-postmeddelanden. Outlook begränsar antalet e-postmeddelanden som skickas till [30 e-postmeddelanden per 1 minut](https://docs.microsoft.com/office365/servicedescriptions/exchange-online-service-description/exchange-online-limits#receiving-and-sending-limits). E-postklienter som tar emot e-postmeddelanden kan också begränsa antalet e-postmeddelanden som tas emot per minut. Kontrollera med din specifika e-postklient om begränsningar. När du ställer in e-postmeddelande för en regel ska regeln beräkna medelvärden under en period på minst en minut och inte använda direktvärden:

![Genomsnittlig beräkning](./media/iot-accelerators-remote-monitoring-email-actions/calculation-email.png)

## <a name="next-steps"></a>Nästa steg

Den här guiden visar hur du lägger till en e-poståtgärd till en ny eller befintlig regel i en lösning för fjärrövervakning. Guiden visade också dig och hur du redigerar HTML-koden som definierar meddelandeformatet.

Det föreslagna nästa steget är att lära sig [hur du använder aviseringar och åtgärda enhetsproblem](iot-accelerators-remote-monitoring-maintain.md).
