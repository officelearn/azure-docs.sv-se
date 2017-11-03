---
title: "Anropa en Azure-funktion från Microsoft Flow | Microsoft Docs"
description: "Skapa en anpassad koppling och sedan anropa en funktion med hjälp av kopplingen."
services: functions
keywords: "molnappar, cloud services, Microsoft Flow affärsprocesser affärsprogram"
documentationcenter: 
author: mgblythe
manager: cfowler
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2017
ms.author: mblythe
ms.custom: 
ms.openlocfilehash: 120f5d69441c5e01ffafbdb8dccb179bf00bdb0a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="call-a-function-from-microsoft-flow"></a>Anropa en funktion från Microsoft Flow

[Microsoft Flow](https://flow.microsoft.com/) gör det enkelt att automatisera arbetsflöden och affärsprocesser mellan din favorit appar och tjänster. Professionella utvecklare kan använda Azure Functions för att utöka funktionerna i Microsoft Flow samtidigt flödet builders från teknisk information.

Du skapar ett flöde i det här avsnittet baseras på ett scenario för underhåll för vind turbiner. Det här avsnittet beskrivs hur du anropar funktionen som du definierade i [en OpenAPI definition för en funktion för att skapa](functions-openapi-definition.md). Funktionen avgör om en reparation på en vind turbin är kostnadseffektiv. Om den är kostnadseffektiv, skickar ett e-postmeddelande att rekommendera att reparera flödet.

Mer information om anropa samma funktion från PowerApps finns [anropa en funktion från PowerApps](functions-powerapps-scenario.md).

I det här avsnittet lär du dig hur du:

> [!div class="checklist"]
> * Skapa en lista i SharePoint.
> * Exportera en API-definition.
> * Lägga till en anslutning till API.
> * Skapa ett flöde för att skicka e-post om en reparation är kostnadseffektiv.
> * Kör flödet.

## <a name="prerequisites"></a>Krav

+ En aktiv [Microsoft Flow konto](https://flow.microsoft.com/documentation/sign-up-sign-in/) med samma tecken i autentiseringsuppgifter som din Azure-konto. 
+ SharePoint, som du använder som en datakälla för det här flödet. Registrera dig för [en utvärderingsversion av Office 365](https://signup.microsoft.com/Signup?OfferId=467eab54-127b-42d3-b046-3844b860bebf&dl=O365_BUSINESS_PREMIUM&ali=1) om du inte redan har SharePoint.
+ Slutför guiden [en OpenAPI definition för en funktion för att skapa](functions-openapi-definition.md).

## <a name="create-a-sharepoint-list"></a>Skapa en SharePoint-lista
Du börja på genom att skapa en lista som du använder som en datakälla för flödet. Listan innehåller följande kolumner.

| Listkolumnen     | Datatyp           | Anteckningar                                    |
|-----------------|---------------------|------------------------------------------|
| **Rubrik**           | Rad med text | Namnet på turbinen                      |
| **LastServiceDate** | Date                |                                          |
| **MaxOutput**       | Tal              | Utdata för turbinen i KwH            |
| **ServiceRequired** | Ja/nej              |                                          |
| **EstimatedEffort** | Tal              | Beräknad tid för att reparera i timmar |

1. I SharePoint-webbplatsen klicka eller tryck på **ny**, sedan **listan**.

    ![Skapa nya SharePoint-listan](./media/functions-flow-scenario/new-list.png)

2. Ange namnet `Turbines`, klicka eller tryck på **skapa**.

    ![Ange namn på ny lista](./media/functions-flow-scenario/create-list.png)

    Den **turbiner** lista skapas med standard **rubrik** fältet.

    ![Turbiner lista](./media/functions-flow-scenario/initial-list.png)

3. Klicka eller tryck på ![ikon för nya objekt](./media/functions-flow-scenario/icon-new.png) sedan **datum**.

    ![Lägg till rad med textfält](./media/functions-flow-scenario/add-column.png)

4. Ange namnet `LastServiceDate`, klicka eller tryck på **skapa**.

    ![Skapa LastServiceDate kolumn](./media/functions-flow-scenario/date-column.png)

5. Upprepa de två sista stegen för andra tre kolumner i listan:

    1. **Antal** > ”MaxOutput”

    2. **Ja/Nej** > ”ServiceRequired”

    3. **Antal** > ”EstimatedEffort”

Som är den för tillfället - bör du ha en tom lista som ser ut som följande bild. Du lägger till data i listan när du har skapat flödet.

![Listan är tom](media/functions-flow-scenario/empty-list.png)

[!INCLUDE [Export an API definition](../../includes/functions-export-api-definition.md)]

## <a name="add-a-connection-to-the-api"></a>Lägga till en anslutning till API
Anpassade API: et (även kallat en anpassad koppling) finns i Microsoft Flow, men du måste upprätta en anslutning till API innan du kan använda den i ett flöde.

1. I [flow.microsoft.com](https://flow.microsoft.com), klicka på kugghjulsikonen (i det övre högra hörnet) och sedan på **anslutningar**.

    ![Flödet anslutningar](media/functions-flow-scenario/flow-connections.png)

1. Klicka på **Skapa anslutning**, rulla ned till den **turbinen reparera** connector, och klicka på den.

    ![Skapa anslutning](media/functions-flow-scenario/create-connection.png)

1. Ange API-nyckeln och klickar på **Skapa anslutning**.

    ![Ange API-nyckel och skapa](media/functions-flow-scenario/api-key.png)

> [!NOTE]
> Om du delar din flöde med andra måste varje person som fungerar på och använder flödet också ange API-nyckel för att ansluta till API: et. Detta kan ändras i framtiden, och vi kommer att uppdatera det här avsnittet för att återspegla.


## <a name="create-a-flow"></a>Skapa ett flöde

Nu är du redo att skapa ett flöde som använder anpassad koppling och SharePoint-listan som du skapade.

### <a name="add-a-trigger-and-specify-a-condition"></a>Lägg till en utlösare och ange ett villkor

Du först skapa ett flöde från tomt (utan en mall) och Lägg till en *utlösaren* som utlöses när ett objekt skapas i SharePoint-listan. Du lägger till en *villkoret* att avgöra vad händer nu.

1. I [flow.microsoft.com](https://flow.microsoft.com), klickar du på **Mina flödar**, sedan **skapa från tomt**.

    ![Skapa från tomt](media/functions-flow-scenario/create-from-blank.png)

2. På SharePoint-utlösare **när ett objekt skapas**.

    ![Välj en utlösare](media/functions-flow-scenario/choose-trigger.png)

    Om du inte redan har loggat in på SharePoint, uppmanas du att göra detta.

3. För **webbplatsadress**, ange namnet på SharePoint-platsen och för **namn**, ange den lista som innehåller turbinen data.

    ![Välj en utlösare](media/functions-flow-scenario/site-list.png)

4. Klicka på **nytt steg**, sedan **Lägg till ett villkor**.

    ![Lägg till ett villkor](media/functions-flow-scenario/add-condition.png)

    Microsoft Flow lägger till två grenar flödet: **fall** och **om inget**. Du lägger till steg grenar för en eller båda när du definierar det villkor som du vill matcha.

    ![Villkor filialer](media/functions-flow-scenario/condition-branches.png)

5. På den **villkoret** kort, klickar du på den första rutan och välj sedan **ServiceRequired** från den **dynamiskt innehåll** dialogrutan.

    ![Välj fält för villkoret](media/functions-flow-scenario/condition1-field.png)

6. Ange ett värde för `True` för villkoret.

    ![Ange true för villkoret](media/functions-flow-scenario/condition1-yes.png)

    Värdet som visas som `Yes` eller `No` i SharePoint-listan, men den lagras som en *booleskt*, antingen `True` eller `False`. 

7. Klicka på **skapa flödet** överst på sidan. Klicka på **uppdatering flöda** med jämna mellanrum.

Flödet för objekt som skapats i listan, kontrollerar om den **ServiceRequired** fältet är inställt på `Yes`, går sedan till den **fall** branch eller **om inget** Förgrena som det behövs. Du kan spara tid, i det här avsnittet om du bara ange åtgärder för den **fall** grenen.

### <a name="add-the-custom-connector"></a>Lägg till anpassad koppling

Du har nu lägga till anpassad koppling som anropar funktionen i Azure. Du lägger till anpassad koppling flödet precis som en koppling som standard. 

1. I den **fall** Förgrena klickar du på **lägga till en åtgärd**.

    ![Lägga till en åtgärd](media/functions-flow-scenario/condition1-yes-add-action.png)

2. I den **välja en åtgärd** dialogrutan, söka efter `Turbine Repair`, väljer du åtgärden **turbinen reparation – beräknar kostnader**.

    ![Välj en åtgärd](media/functions-flow-scenario/choose-turbine-repair.png)

    Följande bild visar kortet som läggs till flödet. Fält och beskrivningar komma från OpenAPI definitionen för anslutningen.

    ![Beräknar kostnader standardvärden](media/functions-flow-scenario/calculates-costs-default.png)

3. På den **beräknar kostnader** kort använder den **dynamiskt innehåll** dialogrutan Välj indata för funktionen. Microsoft Flow visas numeriska fält, men inte fältet, eftersom OpenAPI definition anger att **timmar** och **kapacitet** är numeriska.

    För **timmar**väljer **EstimatedEffort**, och för **kapacitet**väljer **MaxOutput**.

    ![Välj en åtgärd](media/functions-flow-scenario/calculates-costs-fields.png)

     Nu lägga till ytterligare ett villkor baserat på resultatet av funktionen.

4. Längst ned i den **fall** Förgrena klickar du på **mer**, sedan **Lägg till ett villkor**.

    ![Lägg till ett villkor](media/functions-flow-scenario/condition2-add.png)

5. På den **villkor 2** kort, klickar du på den första rutan och välj sedan **meddelandet** från den **dynamiskt innehåll** dialogrutan.

    ![Välj fält för villkoret](media/functions-flow-scenario/condition2-field.png)

6. Ange ett värde för `Yes`. Flödet går till nästa **fall** gren eller **om inget** gren baserat på om meddelandet som returnerades av funktionen Ja (du reparationen) eller på Nej (se inte att reparera). 

    ![Ange yes för villkoret](media/functions-flow-scenario/condition2-yes.png)

Flödet bör nu se ut som följande bild.

![Ange yes för villkoret](media/functions-flow-scenario/flow-checkpoint1.png)

### <a name="send-email-based-on-function-results"></a>Skicka e-post baserat på resultatet av funktionen

Nu i flödet, funktionen har returnerat en **meddelandet** värdet för `Yes` eller `No` från funktionen samt annan information om kostnader och potentiella intäkter. I den **fall** grenen för det andra villkoret du skickar ett e-postmeddelande, men du kan göra en mängd olika saker som skrivs tillbaka till SharePoint-listan eller starta en [godkännandeprocessen](https://flow.microsoft.com/documentation/modern-approvals/).

1. I den **fall** gren i det andra villkoret klickar du på **lägga till en åtgärd**.

    ![Lägga till en åtgärd](media/functions-flow-scenario/condition2-yes-add-action.png)

2. I den **välja en åtgärd** dialogrutan, söka efter `email`, välj sedan en sändningsåtgärd e-post baserat på e-postsystemet (i det här fallet Outlook).

    ![Outlook skicka ett e-postmeddelande](media/functions-flow-scenario/outlook-send-email.png)

3. På den **skickar ett e-** kort, består av ett e-postmeddelande. Ange ett giltigt namn i din organisation för den **till** fältet. I bilden nedan ser du de andra fälten är en kombination av text och token från den **dynamiskt innehåll** dialogrutan. 

    ![E-fält](media/functions-flow-scenario/email-fields.png)

    Den **rubrik** token kommer från SharePoint-lista och **CostToFix** och **RevenueOpportunity** returnerades av funktionen.

    Slutförda flödet bör se ut som följande bild (vi utelämnat första **om inget** gren för att spara utrymme).

    ![Fullständiga flödet](media/functions-flow-scenario/complete-flow.png)

4. Klicka på **uppdatering flöda** längst upp på sidan, klicka sedan på **klar**.

## <a name="run-the-flow"></a>Kör flödet

Nu när flödet har slutförts kan du lägga till en rad i SharePoint-listan och se hur flödet svarar.

1. Gå tillbaka till SharePoint-listan och klicka på **snabbt redigera**.

    ![Redigera Snabbstart](media/functions-flow-scenario/quick-edit.png)

2. Ange följande värden i rutnätet Redigera.

    | Listkolumnen     | Värde           |
    |-----------------|---------------------|
    | **Rubrik**           | Turbinen 60 |
    | **LastServiceDate** | 08/04/2017 |
    | **MaxOutput**       | 2500 |
    | **ServiceRequired** | Ja |
    | **EstimatedEffort** | 10 |

3. Klicka på **Klar**.

    ![Snabb redigering klar](media/functions-flow-scenario/quick-edit-done.png)

    När du lägger till objektet utlöser flödet som du kan ta en titt på Nästa.

4. I [flow.microsoft.com](https://flow.microsoft.com), klickar du på **Mina flödar**, klicka på flödet som du skapade.

    ![Min flöden](media/functions-flow-scenario/my-flows.png)

5. Under **KÖRNINGSHISTORIK**, klicka på Kör flödet.

    ![Kör historik](media/functions-flow-scenario/run-history.png)

    Om körningen lyckades kan du granska åtgärderna flöde på nästa sida. Om du kör misslyckades av någon anledning, felsökningsinformation nästa sida.

6. Expandera kort för att se vad som hände vid flödet. Expandera till exempel den **beräknar kostnader** kort för att visa indata till och utdata från funktionen. 

    ![Beräknar kostnader indata och utdata](media/functions-flow-scenario/calculates-costs-outputs.png)

7. Kontrollera e-postadressen för den person som du angav i den **till** i den **skickar ett e-** kort. E-postmeddelandet som skickas från flödet bör se ut som följande bild.

    ![Flödet för e-post](media/functions-flow-scenario/flow-email.png)

    Du kan se hur token har ersatts av de rätta värdena från SharePoint-lista och funktionen.

## <a name="next-steps"></a>Nästa steg
I det här avsnittet får du lära dig hur du:

> [!div class="checklist"]
> * Skapa en lista i SharePoint.
> * Exportera en API-definition.
> * Lägga till en anslutning till API.
> * Skapa ett flöde för att skicka e-post om en reparation är kostnadseffektiv.
> * Kör flödet.

Läs mer om Microsoft Flow i [komma igång med Microsoft Flow](https://flow.microsoft.com/documentation/getting-started/).

Mer information om andra intressanta scenarier som använder Azure Functions, se [anropa en funktion från PowerApps](functions-powerapps-scenario.md) och [skapa en funktion som kan integreras med Azure Logikappar](functions-twitter-email.md).