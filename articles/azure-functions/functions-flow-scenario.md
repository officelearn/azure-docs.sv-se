---
title: Anropa en Azure-funktion från Microsoft Flow | Microsoft Docs
description: Skapa en anpassad anslutningsapp och sedan anropa en funktion med hjälp av kopplingen.
services: functions
keywords: molnappar, cloud services, Microsoft Flow, affärsprocesser, affärsprogram
author: ggailey777
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: glenga
ms.reviewer: sunayv
ms.custom: ''
ms.openlocfilehash: 31e18285bf6211e73d994e037a91adc396972715
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62106978"
---
# <a name="call-a-function-from-microsoft-flow"></a>Anropa en funktion från Microsoft Flow

[Microsoft Flow](https://flow.microsoft.com/) gör det enkelt att automatisera arbetsflöden och affärsprocesser mellan dina favoritappar och tjänster. Professionella utvecklare kan använda Azure Functions för att utöka funktionerna i Microsoft Flow samtidigt flow builders från de tekniska detaljerna.

Du skapar ett flöde i det här avsnittet baseras på ett scenario med Underhåll för vind syfte. Det här avsnittet visar hur du anropar funktionen som du definierade i [skapa en OpenAPI-definition för en funktion](functions-openapi-definition.md). Funktionen avgör om en nödreparation på en vindturbin är kostnadseffektiv. Om det är kostnadseffektivt skickar flödet ett e-postmeddelande för att rekommendera reparationen.

Information om anropa samma funktion från PowerApps, finns i [anropa en funktion från PowerApps](functions-powerapps-scenario.md).

I det här avsnittet lär du dig hur du:

> [!div class="checklist"]
> * Skapa en lista i SharePoint.
> * Exportera en API-definition.
> * Lägga till en anslutning till API: et.
> * Skapa ett flöde för att skicka e-postmeddelande om en reparation är kostnadseffektiv.
> * Kör flödet.

## <a name="prerequisites"></a>Nödvändiga komponenter

+ En aktiv [Microsoft Flow-konto](https://flow.microsoft.com/documentation/sign-up-sign-in/) med samma tecken i autentiseringsuppgifter som din Azure-konto. 
+ SharePoint, som du använder som en datakälla för det här flödet. Registrera dig för [en utvärderingsversion av Office 365](https://signup.microsoft.com/Signup?OfferId=467eab54-127b-42d3-b046-3844b860bebf&dl=O365_BUSINESS_PREMIUM&ali=1) om du inte redan har SharePoint.
+ I självstudiekursen [skapa en OpenAPI-definition för en funktion](functions-openapi-definition.md).

## <a name="create-a-sharepoint-list"></a>Skapa en SharePoint-lista
Du börja på genom att skapa en lista som du använder som en datakälla för flödet. Listan innehåller följande kolumner.

| Listkolumn     | Datatyp           | Anteckningar                                    |
|-----------------|---------------------|------------------------------------------|
| **Rubrik**           | Enskild rad med text | Namnet på turbinen                      |
| **LastServiceDate** | Date                |                                          |
| **MaxOutput**       | Tal              | Utdata från turbinen i KwH            |
| **ServiceRequired** | Ja/nej              |                                          |
| **EstimatedEffort** | Tal              | Beräknad tid för reparation, i timmar |

1. I SharePoint-webbplats, klickar eller trycker du på **New**, sedan **lista**.

    ![Skapa ny SharePoint-lista](./media/functions-flow-scenario/new-list.png)

2. Ange namnet `Turbines`och klicka eller tryck på **skapa**.

    ![Ange namn för ny lista](./media/functions-flow-scenario/create-list.png)

    Den **sig** lista skapas med standardfältet **rubrik** fält.

    ![Sig lista](./media/functions-flow-scenario/initial-list.png)

3. Klicka eller tryck på ![ikonen nytt objekt](./media/functions-flow-scenario/icon-new.png) sedan **datum**.

    ![Lägg till fält med enskild textrad](./media/functions-flow-scenario/add-column.png)

4. Ange namnet `LastServiceDate`och klicka eller tryck på **skapa**.

    ![Skapa LastServiceDate kolumn](./media/functions-flow-scenario/date-column.png)

5. Upprepa de två sista stegen för de andra tre kolumnerna i listan:

    1. **Antal** > ”MaxOutput”

    2. **Ja/Nej** > ”ServiceRequired”

    3. **Antal** > ”EstimatedEffort”

Det var allt för tillfället, bör du ha en tom lista som ser ut som följande bild. Du lägger till data i listan när du har skapat flödet.

![Tom lista](media/functions-flow-scenario/empty-list.png)

[!INCLUDE [Export an API definition](../../includes/functions-export-api-definition.md)]

## <a name="add-a-connection-to-the-api"></a>Lägg till en anslutning till API
Anpassade API: et (även känt som en anpassad anslutningsapp) är tillgänglig i Microsoft Flow, men du måste göra en anslutning till API innan du kan använda den i ett flöde.

1. I [flow.microsoft.com](https://flow.microsoft.com), klicka på kugghjulsikonen (i det övre högra hörnet) och sedan på **anslutningar**.

    ![Flow-anslutningar](media/functions-flow-scenario/flow-connections.png)

1. Klicka på **Skapa anslutning**, rulla ned till den **turbinen** connector, och klicka på den.

    ![Skapa anslutning](media/functions-flow-scenario/create-connection.png)

1. Ange API-nyckeln och klickar på **Skapa anslutning**.

    ![Ange API-nyckeln och skapa](media/functions-flow-scenario/api-key.png)

> [!NOTE]
> Om du delar ditt flöde med andra, måste varje person som fungerar på och använder flödet också ange API-nyckel för att ansluta till API: et. Detta kan ändras i framtiden, och vi kommer att uppdatera det här avsnittet för att avspegla.


## <a name="create-a-flow"></a>Skapa ett flöde

Nu är du redo att skapa ett flöde som använder den anpassade anslutningsappen och SharePoint-listan som du skapade.

### <a name="add-a-trigger-and-specify-a-condition"></a>Lägg till en utlösare och ange ett villkor

Du först skapa ett flöde från början (utan en mall) och lägga till en *utlösaren* som utlöses när ett objekt skapas i SharePoint-listan. Du sedan lägga till en *villkor* att avgöra vad som händer härnäst.

1. I [flow.microsoft.com](https://flow.microsoft.com), klickar du på **Mina flöden**, sedan **skapa från tom**.

    ![Skapa från början](media/functions-flow-scenario/create-from-blank.png)

2. Klicka på SharePoint-utlösare **när ett objekt skapas**.

    ![Välj en utlösare](media/functions-flow-scenario/choose-trigger.png)

    Om du inte redan har loggat in på SharePoint, uppmanas du att göra detta.

3. För **webbplatsadress**, ange namnet på SharePoint-platsen och för **listnamn**, ange den lista som innehåller data som turbinen.

    ![Välj en utlösare](media/functions-flow-scenario/site-list.png)

4. Klicka på **nytt steg**, sedan **Lägg till ett villkor**.

    ![Lägg till ett villkor](media/functions-flow-scenario/add-condition.png)

    Microsoft Flow lägger till två grenar till flödet: **Om Ja** och **om ingen**. Lägger du till steg en eller båda grenar när du definierar de villkor som du vill matcha.

    ![Villkoret grenar](media/functions-flow-scenario/condition-branches.png)

5. På den **villkor** kortet, klickar du på den första rutan och välj sedan **ServiceRequired** från den **dynamiskt innehåll** dialogrutan.

    ![Välj fält för villkoret](media/functions-flow-scenario/condition1-field.png)

6. Ange ett värde av `True` för villkoret.

    ![Ange true för villkor](media/functions-flow-scenario/condition1-yes.png)

    Värdet visas som `Yes` eller `No` i SharePoint-lista, men den lagras som en *booleskt*, antingen `True` eller `False`. 

7. Klicka på **Skapa flöde** överst på sidan. Se till att klicka på **uppdatering Flow** med jämna mellanrum.

För alla objekt som skapats i listan över flödet kontrollerar om den **ServiceRequired** anges till `Yes`, går sedan till den **om Ja** gren eller **om ingen** grenen som lämpliga. Att spara tid, i det här avsnittet som du bara ange åtgärder för den **om Ja** gren.

### <a name="add-the-custom-connector"></a>Lägga till den anpassade anslutningsappen

Du nu lägga till den anpassade anslutningsappen som anropar funktionen i Azure. Du lägger till den anpassade anslutningsappen till flödet precis som en standard anslutningsapp. 

1. I den **om Ja** grenen, klickar du på **Lägg till en åtgärd**.

    ![Lägga till en åtgärd](media/functions-flow-scenario/condition1-yes-add-action.png)

2. I den **Välj en åtgärd** dialogrutan, söka efter `Turbine Repair`, välj sedan åtgärden **turbinen - beräknar kostnaderna**.

    ![Välj en åtgärd](media/functions-flow-scenario/choose-turbine-repair.png)

    Följande bild visar det kort som har lagts till i flödet. Fält och beskrivningar som kommer från OpenAPI-definitionen för anslutningsappen.

    ![Beräknar kostnaderna standardvärden](media/functions-flow-scenario/calculates-costs-default.png)

3. På den **beräknar kostnaderna** kortet, Använd den **dynamiskt innehåll** dialogrutan Välj indata för funktionen. Microsoft Flow visar numeriska fält, men inte datafältet eftersom OpenAPI-definitionen anger att **timmar** och **kapacitet** är numeriska.

    För **timmar**väljer **EstimatedEffort**, och för **kapacitet**väljer **MaxOutput**.

    ![Välj en åtgärd](media/functions-flow-scenario/calculates-costs-fields.png)

     Nu får du lägga till ytterligare ett villkor baserat på resultatet av funktionen.

4. Längst ned på den **om Ja** grenen, klickar du på **mer**, sedan **Lägg till ett villkor**.

    ![Lägg till ett villkor](media/functions-flow-scenario/condition2-add.png)

5. På den **villkor 2** kortet, klickar du på den första rutan och välj sedan **meddelande** från den **dynamiskt innehåll** dialogrutan.

    ![Välj fält för villkoret](media/functions-flow-scenario/condition2-field.png)

6. Ange ett värde av `Yes`. Flödet går till nästa **om Ja** gren eller **om ingen** grenbaserat på om meddelandet som returnerades av funktionen är Ja (du reparationen) eller på Nej (inte kontrollera reparationen). 

    ![Ange yes för villkor](media/functions-flow-scenario/condition2-yes.png)

Flödet bör nu se ut som på följande bild.

![Ange yes för villkor](media/functions-flow-scenario/flow-checkpoint1.png)

### <a name="send-email-based-on-function-results"></a>Skicka e-post baserat på resultatet av funktionen

I det här läget i flödet, funktionen har returnerat en **meddelande** värdet för `Yes` eller `No` från funktionen, samt annan information på kostnaderna och potentiella intäkter. I den **om Ja** gren för det andra villkoret du skickar ett e-postmeddelande, men du kan göra en mängd olika saker, som skriver tillbaka till SharePoint-lista eller starta en [godkännandeprocess](https://flow.microsoft.com/documentation/modern-approvals/).

1. I den **om Ja** gren i det andra villkoret klickar du på **Lägg till en åtgärd**.

    ![Lägga till en åtgärd](media/functions-flow-scenario/condition2-yes-add-action.png)

2. I den **Välj en åtgärd** dialogrutan, söka efter `email`, välj sedan en skicka e-post-åtgärd baserat på e-postsystemet (i det här fallet Outlook).

    ![Outlook-skicka ett e-postmeddelande](media/functions-flow-scenario/outlook-send-email.png)

3. På den **skicka ett e-postmeddelande** kortet, består av ett e-postmeddelande. Ange ett giltigt namn i din organisation för den **till** fält. I bilden nedan ser du de andra fälten är en kombination av text och tokens från den **dynamiskt innehåll** dialogrutan. 

    ![E-postfält](media/functions-flow-scenario/email-fields.png)

    Den **rubrik** token kommer från SharePoint-lista och **CostToFix** och **RevenueOpportunity** som returneras av funktionen.

    Slutfört flöde bör se ut som på följande bild (vi utelämnat först **om ingen** gren att spara utrymme).

    ![Fullständiga flödet](media/functions-flow-scenario/complete-flow.png)

4. Klicka på **uppdatering Flow** överst på sidan, sedan klickar du på **klar**.

## <a name="run-the-flow"></a>Kör flödet

Nu när flödet är slutfört kan du lägga till en rad i SharePoint-listan och se hur flödet svarar.

1. Gå tillbaka till SharePoint-listan och klicka på **Snabbredigering**.

    ![Snabbredigering](media/functions-flow-scenario/quick-edit.png)

2. Ange följande värden i rutnätet Redigera.

    | Listkolumn     | Värde           |
    |-----------------|---------------------|
    | **Rubrik**           | Turbinen 60 |
    | **LastServiceDate** | 08/04/2017 |
    | **MaxOutput**       | 2500 |
    | **ServiceRequired** | Ja |
    | **EstimatedEffort** | 10 |

3. Klicka på **Klar**.

    ![Snabbredigering klar](media/functions-flow-scenario/quick-edit-done.png)

    När du lägger till objektet utlöser flödet som du ta en titt på Nästa.

4. I [flow.microsoft.com](https://flow.microsoft.com), klickar du på **Mina flöden**, klicka sedan på flödet som du skapade.

    ![Mina flöden](media/functions-flow-scenario/my-flows.png)

5. Under **KÖRNINGSHISTORIK**, klickar du på körning av flöde.

    ![Körningshistorik](media/functions-flow-scenario/run-history.png)

    Om körningen lyckades, kan du granska flow-åtgärder på nästa sida. Om körningen misslyckades av någon anledning, felsökningsinformation nästa sida.

6. Expandera kort om du vill se vad som hände vid flödet. Expandera till exempel den **beräknar kostnaderna** kort för att visa indata och utdata från funktionen. 

    ![Beräknar kostnaderna för indata och utdata](media/functions-flow-scenario/calculates-costs-outputs.png)

7. Kontrollera e-postkontot för den person som du angav i den **till** i den **skicka ett e-postmeddelande** kort. E-postmeddelandet som skickas från flödet bör se ut som på följande bild.

    ![Flow e-post](media/functions-flow-scenario/flow-email.png)

    Du kan se hur token som har ersatts med de rätta värdena från SharePoint-listan och funktionen.

## <a name="next-steps"></a>Nästa steg
I det här avsnittet får du lära dig hur du:

> [!div class="checklist"]
> * Skapa en lista i SharePoint.
> * Exportera en API-definition.
> * Lägga till en anslutning till API: et.
> * Skapa ett flöde för att skicka e-postmeddelande om en reparation är kostnadseffektiv.
> * Kör flödet.

Läs mer om Microsoft Flow i [Kom igång med Microsoft Flow](https://flow.microsoft.com/documentation/getting-started/).

Mer information om andra intressanta scenarier som använder Azure Functions, se [anropa en funktion från PowerApps](functions-powerapps-scenario.md) och [skapa en funktion som kan integreras med Azure Logic Apps](functions-twitter-email.md).
