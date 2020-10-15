---
title: E-poståtgärd inom fjärr styrning – Azure | Microsoft Docs
description: Den här instruktions guiden visar hur du lägger till en e-poståtgärd till en ny eller befintlig regel.
author: dominicbetts
manager: hegate
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/12/2018
ms.topic: conceptual
ms.openlocfilehash: 2776b18dc3faded103b49e8ff58a8a0a6dccf144
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2020
ms.locfileid: "92074472"
---
# <a name="add-an-email-action"></a>Lägg till en e-poståtgärd

Med e-poståtgärder kan du se till att du aldrig saknar aviseringar. Du kan lägga till en e-poståtgärd till en befintlig regel eller när du skapar en ny regel.

För att slutföra stegen i den här instruktions guiden behöver du en distribuerad instans av lösningen för fjärrövervakning i din Azure-prenumeration.

Om du vill skapa eller ändra en regel måste du vara [ **administratör**eller ha rätt behörighet](iot-accelerators-remote-monitoring-rbac.md).

## <a name="edit-an-existing-rule"></a>Redigera en befintlig regel

Följ dessa steg om du vill lägga till en e-poståtgärd i en befintlig regel:

1. Navigera till din lösning för fjärrövervakning.

1. Från **instrument panelen**navigerar du till sidan **regler** :

    ![Sidan Regler](./media/iot-accelerators-remote-monitoring-email-actions/rules-email.png)

1. Klicka på kryss rutan bredvid den befintliga regeln för att ändra och klicka sedan på **Redigera** överst. En redigerings bar **regel** panel visas.

1. Växla **e-post aktive rad** till **på**i avsnittet **åtgärd** .

1. Första gången du aktiverar en e-poståtgärd i Solution Accelerator måste du [Logga in på Outlook](#outlook).

1. Ange en e-postadress i rutan mottagare och tryck på **RETUR** för varje e-postadress som ska läggas till:

    ![Adress post](./media/iot-accelerators-remote-monitoring-email-actions/address-email.png)

1. Ange ett ämne för e-postmeddelandet.

1. Ange eventuella ytterligare kommentarer till e-postmottagare som oformaterad text. Du kan använda HTML-formatering om du [redigerar e-postmallen](#htmledit).

1. Kontrol lera att **regel statusen** har angetts till **aktive rad**.

1. Klicka på **Använd**.

## <a name="create-a-new-rule"></a>Skapa en ny regel

Följ dessa steg om du vill lägga till en e-poståtgärd när du skapar en ny regel:

1. Navigera till din lösning för fjärrövervakning.

1. Från **instrument panelen**navigerar du till sidan **regler** :

    ![Sidan Regler](./media/iot-accelerators-remote-monitoring-email-actions/rules-email.png)

1. Följ stegen i [avsnittet Skapa en regel](iot-accelerators-remote-monitoring-automate.md#create-a-rule). Följ stegen i avsnittet [skapa en avancerad regel](iot-accelerators-remote-monitoring-automate.md#create-an-advanced-rule) upp till den punkt där du ställer in en **allvarlighets**grad. Klicka inte på **Använd** ännu.

1. Växla **e-post aktive rad** till **på**i avsnittet **åtgärd** .

1. Första gången du aktiverar en e-poståtgärd i Solution Accelerator måste du [Logga in på Outlook](#outlook).

1. Ange en e-postadress i rutan mottagare och tryck på **RETUR** för varje e-postadress som ska läggas till:

    ![Adress post](./media/iot-accelerators-remote-monitoring-email-actions/address-email.png)

1. Ange ett ämne för e-postmeddelandet.

1. Ange eventuella ytterligare kommentarer till e-postmottagare som oformaterad text. Du kan använda HTML-formatering om du [redigerar e-postmallen](#htmledit).

1. Kontrol lera att **regel statusen** har angetts till **aktive rad**.

1. Klicka på **Använd**.

Regeln med en e-poståtgärd är nu aktive rad. Varje gången en åtgärd utlöses skickas ett nytt e-postmeddelande till mottagarna.

## <a name="sign-in-to-outlook"></a>Logga in på Outlook <a name="outlook"></a>

Första gången du aktiverar en e-poståtgärd i Solution Accelerator måste du logga in på Outlook. Den här åtgärden konfigurerar e-postkontot som skickar ut e-postaviseringarna.

> [!NOTE]
> Du bör endast skapa ett särskilt Outlook-konto för Solution Accelerator-aviseringar och använda det kontot när du aktiverar den första e-poståtgärden.

### <a name="contributor-role-outlook-setup"></a>Deltagar roll Outlook-konfiguration

Om någon i rollen **deltagare** i prenumerationen har distribuerat Solution Accelerator har programmet inte tillräckliga behörigheter för att konfigurera och verifiera e-poståtgärder via webb gränssnittet.

Innan du börjar ska du skapa ett Outlook-konto som ska användas för att skicka e-postmeddelanden från Solution Accelerator.

Följande steg visar hur du konfigurerar och verifierar e-poståtgärder manuellt:

1. Navigera till [Azure Portal](https://portal.azure.com).

1. Navigera till resurs gruppen för din lösnings Accelerator.

1. Klicka på **Office365-Connector**:

    ![API-anslutning](./media/iot-accelerators-remote-monitoring-email-actions/apiconnector1.png)

1. Starta auktoriseringsprocessen genom att klicka på banderollen:

    ![auktorisera](./media/iot-accelerators-remote-monitoring-email-actions/connector1.png)

1. Klicka på **auktorisera**. Du uppmanas att logga in. Det konto som du använder för att logga in ska vara den e-postadress som programmet använder för att skicka e-postmeddelanden:

    ![Knappen auktorisera](./media/iot-accelerators-remote-monitoring-email-actions/authorize.png)

1. Klicka på **Spara** längst ned. Ditt godkännande kommer att lyckas om banderollen är borta.

1. Om du vill ändra e-postadressen som meddelandena skickas från klickar du på **Redigera API-anslutning**.

    ![ändra e-post](./media/iot-accelerators-remote-monitoring-email-actions/editemail1.png)

### <a name="owner-role-outlook-setup"></a>Konfiguration av ägar roll för Outlook

Om någon i **ägar** rollen i prenumerationen har distribuerat Solution Accelerator kan programmet verifiera att e-poståtgärder har kon figurer ATS korrekt via webb gränssnittet.

Innan du börjar ska du skapa ett Outlook-konto som ska användas för att skicka e-postmeddelanden från Solution Accelerator.

Följande steg hjälper dig att logga in och konfigurera e-poståtgärder:

1. Klicka om du vill logga in på Outlook. Du kommer till Azure Portal:

   ![Logga in på Outlook](./media/iot-accelerators-remote-monitoring-email-actions/owneroutlook-email.png)

1. Klicka på **auktorisera**. Du uppmanas att logga in. Det konto som du använder för att logga in ska vara den e-postadress som programmet använder för att skicka e-postmeddelanden:

1. Klicka på **Spara**. Gå tillbaka till Solution Accelerator och uppdatera sidan.

1. Om du har konfigurerat e-postaviseringen visas följande meddelande:

   ![Outlook-inloggningen har slutförts](./media/iot-accelerators-remote-monitoring-email-actions/success-email.png)

## <a name="customize-the-email-html"></a>Anpassa e-postadressen <a name="htmledit"></a>

Den färdiga lösningen för fjärrövervakning innehåller en grundläggande HTML-mall för e-poståtgärder. E-postmallen använder värden från inställningarna för e-poståtgärder. Här är ett exempel på ett e-postmeddelande:

![e-postexempel](./media/iot-accelerators-remote-monitoring-email-actions/emailtemplate1.png)

Följande steg visar hur du redigerar HTML-e-postmallen. Du kan till exempel ta med mer information eller lägga till anpassade avbildningar:

1. Klona antingen Java-eller .NET-GitHub lagrings plats:

1. Navigera till platsen för e-postmallen:
  
    `Dotnet: device-telemetry\ActionsAgent\data\EmailTemplate.html`
  
    `Java device-telemetry/app/resources/data/EmailTemplate.html`

1. Du kan lägga till eller ta bort parametrar i den här mallen för att anpassa meddelandet. Du kan också lägga till, ta bort eller ersätta samtal efter behov:

    Till exempel i .NET-koden:  `emailTemplate = emailTemplate.Replace("${subject}", emailAction.GetSubject());`

    I t. ex. java-koden:  `this.emailTemplate.replace("${subject}", emailAction.GetSubject());`

1. Parametrarna i mallen ska ha formatet `${...}` . Ta bort en parameter genom att ta bort raden som krävs. Lägg till en parameter genom att lägga till en rad med det värde som ska infogas.

1. Om du vill lägga till bilder eller anpassad text uppdaterar du EmailTemplate.HTML-filen direkt.

## <a name="throttling"></a>Begränsning

Lösnings acceleratorn för fjärrövervakning använder Outlook för att skicka e-postaviseringar. Outlook begränsar antalet e-postmeddelanden som skickas till [30 e-postmeddelanden per 1 minut](/office365/servicedescriptions/exchange-online-service-description/exchange-online-limits#receiving-and-sending-limits). E-postklienter som tar emot e-postmeddelandena kan också begränsa antalet e-postmeddelanden som tas emot per minut. Kontrol lera med din speciella e-postklient om begränsningarna. När du ställer in e-postaviseringar för en regel ska regeln beräkna genomsnitts värden under en period på minst en minut och inte använda direkta värden:

![Genomsnittlig beräkning](./media/iot-accelerators-remote-monitoring-email-actions/calculation-email.png)

## <a name="next-steps"></a>Nästa steg

Den här guiden visar hur du lägger till en e-poståtgärd till en ny eller befintlig regel i en lösning för fjärrövervakning. Guiden visade också dig och hur du redigerar HTML-koden som definierar meddelande formatet.

Det föreslagna nästa steg är att lära dig [hur du använder aviseringar och löser enhets problem](iot-accelerators-remote-monitoring-maintain.md).