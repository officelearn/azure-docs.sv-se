---
title: Hantera principer för automatisk nedskalning i Azure DevTest Labs | Microsoft-dokument
description: Lär dig hur du ställer in principen för automatisk nedfördelning för ett labb så att virtuella datorer stängs av automatiskt när de inte används.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/10/2020
ms.author: spelluru
ms.openlocfilehash: 7cdc9f9a4503c786065b6d514f61fe17eae4ce5e
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81270918"
---
# <a name="configure-autoshutdown-for-lab-and-compute-virtual-machines-in-azure-devtest-labs"></a>Konfigurera automatisk nedrullning för virtuella labb- och beräkningsdatorer i Azure DevTest Labs

I den här artikeln beskrivs hur du konfigurerar inställningar för automatisk nedstämd nedskrivning för virtuella labb i DevTest Labs och beräkna virtuella datorer. 

## <a name="configure-autoshutdown-for-lab-vms-devtest-labs"></a>Konfigurera automatisk nedrullning för virtuella labb -datorer (DevTest Labs)
Med Azure DevTest Labs kan du styra kostnader och minimera avfall i dina labb genom att hantera principer (inställningar) för varje labb. Den här artikeln visar hur du konfigurerar principen för automatisk nedskrivning för ett labbkonto och konfigurerar inställningar för automatisk nedskrivning för ett labb i labbkontot. Information om hur du anger alla labbprinciper finns [i Definiera labbprinciper i Azure DevTest Labs](devtest-lab-set-lab-policy.md).  

### <a name="set-auto-shutdown-policy-for-a-lab"></a>Ange automatisk avstängningsprincip för ett labb
Som labbägare kan du konfigurera ett avstängningsschema för alla virtuella datorer i labbet. På så sätt kan du spara kostnader från att köra datorer som inte används (inaktiv). Du kan tillämpa en avstängningsprincip för alla virtuella labb-datorer centralt men också spara labbanvändarna från att konfigurera ett schema för sina enskilda datorer. Med den här funktionen kan du ställa in principen på ditt labbschema från att erbjuda ingen kontroll till fullständig kontroll, till dina labbanvändare. Som labbägare kan du konfigurera den här principen genom att vidta följande åtgärder:

1. På startsidan för labbet väljer du **Konfiguration och principer**.
2. Välj **automatisk avstängningsprincip** i avsnittet **Scheman** på den vänstra menyn.
3. Välj ett av alternativen. I följande avsnitt får du mer information om dessa alternativ: Principen gäller endast för nya virtuella datorer som skapats i labbet och inte för de redan befintliga virtuella datorerna. 

    ![Stäng av principalternativ automatiskt](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-options.png)

### <a name="configure-auto-shutdown-settings"></a>Konfigurera inställningar för automatisk avstängning
Principen för automatisk nedskrivning hjälper till att minimera labbavfall genom att du kan ange den tid som det här labbets virtuella datorer stängs av.

Så här visar (och ändrar) principerna för ett labb:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **Alla tjänster**och välj sedan **DevTest Labs** i listan.
3. Välj önskat labb i listan över labb.   
4. Välj **Konfiguration och principer**.

    ![Fönstret Principinställningar](./media/devtest-lab-set-lab-policy/policies-menu.png)
5. I fönstret **Konfiguration och principer** i labbet väljer du Automatisk **avstängning** under **Scheman**.
   
    ![Automatisk nedrullning](./media/devtest-lab-set-lab-policy/auto-shutdown.png)
6. Välj **På** om du vill aktivera den här principen och **Av** för att inaktivera den.
7. Om du aktiverar den här principen anger du vilken tid (och tidszon) som ska stängas av alla virtuella datorer i det aktuella labbet.
8. Ange **Ja** eller **Nej** för alternativet att skicka ett meddelande 30 minuter före den angivna automatiska nedskrivningstiden. Om du väljer **Ja**anger du en webhook-URL-slutpunkt eller e-postadress som anger var du vill att meddelandet ska bokföras eller skickas. Användaren får ett meddelande och ges möjlighet att fördröja avstängningen. Mer information finns i avsnittet [Meddelanden.](#notifications) 
9. Välj **Spara**.

    Som standard gäller den här principen för alla virtuella datorer i det aktuella labbet när den är aktiverad. Om du vill ta bort den här inställningen från en viss virtuell dator öppnar du hanteringsfönstret för den virtuella datorn och ändrar inställningen **För automatisk nedrullning.**
    
> [!NOTE]
> Om du uppdaterar schemat för automatisk nedskrivning för ditt labb eller en specifik virtuell labbdator inom 30 minuter från den aktuella schemalagda tiden, gäller den uppdaterade avstängningstiden mot nästa dags schema. 

### <a name="user-sets-a-schedule-and-can-opt-out"></a>Användaren anger ett schema och kan välja bort
Om du ställer in labbet på den här principen kan labbanvändarna åsidosätta eller välja bort labbschemat. Det här alternativet ger labbanvändare full kontroll över schemat för automatisk avstängning av sina virtuella datorer. Labbanvändare ser ingen ändring i sin sida för automatisk avstängning av virtuella datorer.

![Automatiskt stänga av principalternativet - 1](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-1.png)

### <a name="user-sets-a-schedule-and-cannot-opt-out"></a>Användaren anger ett schema och kan inte välja bort
Om du ställer in labbet på den här principen kan labbanvändare åsidosätta labbschemat. De kan dock inte välja bort automatisk avstängningsprincip. Det här alternativet ser till att alla datorer i labbet är under ett schema för automatisk avstängning. Labbanvändare kan uppdatera schemat för automatisk avstängning av sina virtuella datorer och konfigurera avstängningsmeddelanden.

![Automatiskt stänga av principalternativet - 2](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-2.png)

### <a name="user-has-no-control-over-the-schedule-set-by-lab-admin"></a>Användaren har ingen kontroll över schemat som fastställts av labbadministratör
Om du ställer in labbet på den här principen kan labbanvändare inte åsidosätta eller välja bort labbschemat. Det här alternativet ger labbadministratören fullständig kontroll på schemat för varje dator i labbet. Labbanvändare kan bara ställa in meddelanden om automatisk avstängning för sina virtuella datorer.

![Automatiskt stänga av principalternativet - 3](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-3.png)

### <a name="notifications"></a>Meddelanden
När automatisk neddelning som inrättats av labbägaren, meddelanden kommer att skickas till labbet användare 30 minuter innan autoshutdown utlöses om någon av deras virtuella datorer kommer att påverkas. Det här alternativet ger labbanvändare en chans att spara sitt arbete innan avstängningen. Meddelandet innehåller också länkar för varje virtuell dator för följande åtgärder:

- Hoppa över autoshutdown för den här gången
- Snooze autoshutdown i en timme eller 2 timmar, så att de kan fortsätta arbeta på den virtuella datorn.

Meddelandet skickas via den konfigurerade slutpunkten för webbkroken eller en e-postadress som anges av labbägare i inställningarna för automatisk nedskärning. Webhooks kan du bygga eller ställa in integrationer som prenumererar på vissa händelser. När en av dessa händelser utlöses skickar DevTest Labs en HTTP POST-nyttolast till webhooks konfigurerade URL. Mer information om webhooks finns i [Skapa en webhook- eller API Azure-funktion](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

Vi rekommenderar att du använder webbkrokar eftersom de stöds i stor utsträckning av olika appar (till exempel Slack, Azure Logic Apps och så vidare.) och låter dig implementera ditt eget sätt att skicka meddelanden. Som ett exempel får du den här artikeln dig igenom hur du får automatisk nedskrivning av e-postmeddelanden med hjälp av Azure Logic Apps. Låt oss först snabbt gå igenom de grundläggande stegen för att aktivera avisering om automatisk nedskrivning i labbet.   

### <a name="create-a-logic-app-that-receives-email-notifications"></a>Skapa en logikapp som tar emot e-postmeddelanden
[Azure Logic Apps](../logic-apps/logic-apps-overview.md) innehåller många färdiga anslutningsappar som gör det enkelt att integrera en tjänst med andra klienter, till exempel Office 365 och Twitter. På hög nivå kan stegen för att konfigurera en Logic App för e-postmeddelande delas in i fyra faser: 

- Skapa en logikapp. 
- Konfigurera den inbyggda mallen.
- Integrera med din e-postklient
- Hämta Webhook-URL:en.

### <a name="create-a-logic-app"></a>Skapa en logikapp
För att komma igång genom att skapa en logikapp i din Azure-prenumeration med hjälp av följande steg:

1. Välj **+ Skapa en resurs** på den vänstra menyn, välj **Integration**och välj Logic **App**. 

    ![Ny logikappmeny](./media/devtest-lab-auto-shutdown/new-logic-app.png)
2. Gör så här på sidan **Logikapp - Skapa:** 
    1. Ange ett **namn** på logikappen.
    2. Välj din **Azure-prenumeration**.
    3. Skapa en ny **resursgrupp** eller välj en befintlig resursgrupp. 
    4. Välj en **plats** för logikappen. 

        ![Ny logikapp - inställningar](./media/devtest-lab-auto-shutdown/new-logic-app-page.png)
3. Välj **Gå till resursen** i **meddelandena.** 

    ![Gå till resurs](./media/devtest-lab-auto-shutdown/go-to-resource.png)
4. Välj **Logikappdesigner** under kategorin **Distributionsverktyg.**

    ![Välj HTTP-begäran/svar](./media/devtest-lab-auto-shutdown/select-http-request-response-option.png)
5. På sidan **HTTP-begäran-svar** väljer du **Använd den här mallen**. 

    ![Välj Använd det här mallalternativet](./media/devtest-lab-auto-shutdown/select-use-this-template.png)
6. Kopiera följande JSON till avsnittet **Begärandetext JSON Schema:** 

    ```json
    {
        "$schema": "http://json-schema.org/draft-04/schema#",
        "properties": {
            "delayUrl120": {
                "type": "string"
            },
            "delayUrl60": {
                "type": "string"
            },
            "eventType": {
                "type": "string"
            },
            "guid": {
                "type": "string"
            },
            "labName": {
                "type": "string"
            },
            "owner": {
                "type": "string"
            },
            "resourceGroupName": {
                "type": "string"
            },
            "skipUrl": {
                "type": "string"
            },
            "subscriptionId": {
                "type": "string"
            },
            "text": {
                "type": "string"
            },
            "vmName": {
                "type": "string"
            }
        },
        "required": [
            "skipUrl",
            "delayUrl60",
            "delayUrl120",
            "vmName",
            "guid",
            "owner",
            "eventType",
            "text",
            "subscriptionId",
            "resourceGroupName",
            "labName"
        ],
        "type": "object"
    }
    ```
    
    ![Begär JSON-schema för begäran](./media/devtest-lab-auto-shutdown/request-json.png)
7. Välj **+ Nytt steg** i designern och gör så här:
    1. Sök efter **Office 365 Outlook - Skicka ett e-postmeddelande**. 
    2. Välj **Skicka ett e-postmeddelande** från **Åtgärder**. 
    
        ![Alternativet Skicka e-post](./media/devtest-lab-auto-shutdown/select-send-email.png)
    3. Välj **Logga in** för att logga in på ditt e-postkonto. 
    4. Välj **fältet TILL** och välj ägare.
    5. Välj **ÄMNE**och ange ett ämne för e-postmeddelandet. Till exempel: "Avstängning av dator vmName för Lab: labName."
    6. Välj **BODY**och definiera brödtextinnehållet för e-postmeddelande. Till exempel: "vmName är schemalagt att stängas av på 15 minuter. Hoppa över den här avstängningen genom att klicka på: URL. Fördröja avstängningen i en timme: delayUrl60. Fördröja avstängning i 2 timmar: delayUrl120."

        ![Begär JSON-schema för begäran](./media/devtest-lab-auto-shutdown/email-options.png)
1. Välj **Spara** i verktygsfältet. Nu kan du kopiera **HTTP POST URL**. Välj knappen Kopiera om du vill kopiera URL:en till Urklipp. 

    ![WebHook URL](./media/devtest-lab-auto-shutdown/webhook-url.png)

## <a name="configure-autoshutdown-for-compute-vms"></a>Konfigurera automatisk nedskalning för virtuella beräkningsresurser

1. På sidan **Virtuell dator** väljer du **Automatisk avstängning** på den vänstra menyn i avsnittet **Operationer.** 
2. På sidan **Automatisk avstängning** väljer du **På** för att aktivera den här principen och **Av** för att inaktivera den.
3. Om du aktiverar den här principen anger du den **tid** (och **tidszon)** då den virtuella datorn ska stängas av.
4. Ange **Ja** eller **Nej** för alternativet att skicka ett meddelande 30 minuter före den angivna automatiska nedskrivningstiden. Om du väljer **Ja**anger du en webhook-URL-slutpunkt eller e-postadress som anger var du vill att meddelandet ska bokföras eller skickas. Användaren får ett meddelande och ges möjlighet att fördröja avstängningen. Mer information finns i avsnittet [Meddelanden.](#notifications) 
9. Välj **Spara**.

    ![Konfigurera automatisk nedrullning för en dator för beräkning](./media/devtest-lab-auto-shutdown/comnpute-auto-shutdown.png)

### <a name="view-activity-logs-for-auto-shutdown-updates"></a>Visa aktivitetsloggar för uppdateringar av automatisk avstängning
När du uppdaterar inställningen för automatisk nedteckning visas aktiviteten som loggas i aktivitetsloggen för den virtuella datorn. 

1. Navigera till startsidan för den virtuella datorn i [Azure-portalen.](https://portal.azure.com)
2. Välj **Aktivitetslogg** på den vänstra menyn. 
3. Ta bort **resurs: mycomputevm** från filter.
3. Bekräfta att åtgärden **Lägg till eller ändra scheman** visas i aktivitetsloggen. Om du inte ser det väntar du någon gång och uppdaterar aktivitetsloggen.

    ![Aktivitetsloggpost](./media/devtest-lab-auto-shutdown/activity-log-entry.png)
4. Välj åtgärden **Lägg till eller ändra scheman** om du vill visa följande information på sidan **Sammanfattning:**

    - Åtgärdsnamn (Lägg till eller ändra scheman)
    - Datum och tid då inställningen för automatisk nedfördelning uppdaterades.
    - E-postadressen till den användare som uppdaterade inställningen. 

        ![Sammanfattning av aktivitetsloggposten](./media/devtest-lab-auto-shutdown/activity-log-entry-summary.png)
5. Växla till fliken **Ändra historik** på sidan Lägg till eller ändra scheman visas **ändringshistoriken** för inställningen. I följande exempel ändrades avstängningstiden från 19.00 till 18.00 den 10 april 2020 kl. Och var inställningen inaktiverad vid 15:25:09 EST. 

    ![Aktivitetslogg - ändringshistorik](./media/devtest-lab-auto-shutdown/activity-log-entry-change-history.png)
6. Om du vill se mer information om åtgärden växlar du till fliken **JSON** på sidan **Lägg till eller ändra scheman.**

## <a name="next-steps"></a>Nästa steg
Mer information om hur du anger alla principer finns [i Definiera labbprinciper i Azure DevTest Labs](devtest-lab-set-lab-policy.md).

