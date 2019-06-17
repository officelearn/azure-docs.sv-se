---
title: Hantera autoshutdown principer i Azure DevTest Labs | Microsoft Docs
description: Lär dig mer om att skapa autoshutdown princip för ett labb så att virtuella datorer automatiskt stänga ned om de inte används.
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
ms.date: 05/17/2019
ms.author: spelluru
ms.openlocfilehash: 9adf8dd4a5a3c469ed130b29308a0d828aee40bf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65873997"
---
# <a name="manage-autoshutdown-policies-for-a-lab-in-azure-devtest-labs"></a>Hantera autoshutdown principer för ett labb i Azure DevTest Labs
Azure DevTest Labs kan du styra kostnader och minimera slöseri i dina labb genom att hantera principer (inställningar) för varje labb. Den här artikeln visar hur du konfigurerar autoshutdown princip för ett labbkonto och konfigurera autoshutdown inställningarna för ett labb i labbkonto. Information om hur du ställer in varje labb-princip finns i [definiera labbprinciper i Azure DevTest Labs](devtest-lab-set-lab-policy.md).  

## <a name="set-auto-shutdown-policy-for-a-lab"></a>Skapa princip för automatisk avstängning för ett labb
Som labbägare kan konfigurera du ett schema för avstängning för alla virtuella datorer i labbet. Du kan spara pengar från datorer som inte används körs (viloläge) genom att göra detta. Du kan tillämpa en princip för avstängning på alla labbet virtuella datorer centralt utan även spara din labbanvändare arbetet från att konfigurera ett schema för deras enskilda datorer. Den här funktionen kan du ställa in principen på ditt labb-schema med början från erbjuder ingen kontroll till fullständig behörighet till dina labbanvändare. Som labbägare kan konfigurera du den här principen genom att utföra följande steg:

1. På startsidan för labbet, väljer **konfiguration och principer**.
2. Välj **automatisk avstängning princip** i den **scheman** avsnitt i den vänstra menyn.
3. Välj något av alternativen. I följande avsnitt innehåller mer information om dessa alternativ: Set-principen gäller endast för nya virtuella datorer som skapas i labbet och inte till de befintliga virtuella datorerna. 

    ![Stäng av alternativ för automatisk](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-options.png)

## <a name="configure-auto-shutdown-settings"></a>Konfigurera inställningar för automatisk avstängning
Principen autoshutdown hjälper till att minimera lab avfall genom att du kan ange hur lång tid som den här övningen virtuella datorer stängs av.

Om du vill visa och ändra principer för ett labb, Följ dessa steg:

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **alla tjänster**, och välj sedan **DevTest Labs** i listan.
3. Listan över labbar, Välj önskade labbet.   
4. Välj **konfiguration och principer**.

    ![Rutan inställningar för principen](./media/devtest-lab-set-lab-policy/policies-menu.png)
5. På testmiljön **konfiguration och principer** väljer **automatisk avstängning** under **scheman**.
   
    ![Autoshutdown](./media/devtest-lab-set-lab-policy/auto-shutdown.png)
6. Välj **på** att aktivera den här principen och **av** att inaktivera den.
7. Om du aktiverar den här principen kan du ange tid (och tidszon) för att stänga av alla virtuella datorer i den aktuella testmiljön.
8. Ange **Ja** eller **nr** för alternativet att skicka en avisering 15 minuter innan den angivna autoshutdown tid. Om du väljer **Ja**anger en webhook-URL-slutpunkt eller e-postadressen för att ange var du vill att meddelanden ska lagt in eller skickas. Användaren får ett meddelande och ges möjlighet att skjuta avstängningen. Mer information finns i den [meddelanden](#notifications) avsnittet. 
9. Välj **Spara**.

    Som standard när du har aktiverat, den här principen gäller för alla virtuella datorer i den aktuella testmiljön. Om du vill ta bort den här inställningen från en specifik virtuell dator, öppna fönstret för hantering av den virtuella datorn och ändra dess **Autoshutdown** inställningen.

### <a name="user-sets-a-schedule-and-can-opt-out"></a>Användaren anger ett schema och kan avanmäla dig
Om du anger ditt labb till den här principen kan labbanvändare åsidosätta eller välja bort lab-schema. Det här alternativet ger labbanvändare fullständig kontroll över schemat för automatisk avstängning av sina virtuella datorer. Lab användarna ser ingen ändring i sina schemasidan för VM automatisk avstängning.

![Automatisk stänga principalternativ - 1](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-1.png)

### <a name="user-sets-a-schedule-and-cannot-opt-out"></a>Användaren anger ett schema och det går inte att avanmäla dig
Om du anger ditt labb till den här principen kan labbanvändare åsidosätta lab-schema. Men kan inte de välja bort principen för automatisk avstängning. Det här alternativet ser till att alla virtuella datorer i labbet är under ett schema för automatisk avstängning. Labbanvändare kan uppdatera schemat för automatisk avstängning av sina virtuella datorer och konfigurera avslutning av meddelanden.

![Automatisk stänga principalternativ - 2](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-2.png)

### <a name="user-has-no-control-over-the-schedule-set-by-lab-admin"></a>Användaren har ingen kontroll över det schema som angetts av administratören för labb
Om du anger ditt labb till den här principen kan inte labbanvändare åsidosätta eller välja bort lab-schema. Det här alternativet erbjuder lab admin fullständig kontroll över schemat för varje dator i labbet. Labbanvändare kan bara ställa in automatisk avstängning meddelanden för sina virtuella datorer.

![Automatisk stänga principalternativ - 3](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-3.png)

## <a name="notifications"></a>Meddelanden
När autoshutdown som konfigurerats av labbägare meddelanden ska skickas till användarna lab 15 minuter innan autoshutdown utlöses om något av de virtuella datorerna kommer att påverkas. Det här alternativet ger labbanvändare en chans att spara arbetet innan avstängningen. Meddelandet innehåller även länkar för varje virtuell dator för följande åtgärder:

- Hoppa över autoshutdown för den här gången
- Viloläge i autoshutdown i en timme eller två timmar, så att de kan fortsätta att arbeta på den virtuella datorn.

Meddelandet skickas via den konfigurerade web hook-slutpunkten eller en e-postadress anges av labbägare i inställningarna för autoshutdown. Webhooks kan du skapa eller konfigurera integreringar som prenumererar på vissa händelser. När någon av dessa händelser utlöses skickas en HTTP POST-nyttolast till konfigurerade Webhooksadressen DevTest Labs. Läs mer om webhooks [skapa en webhook eller API Azure-funktion](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

Vi rekommenderar att du kan använda webhooks eftersom de är stor utsträckning stöds av olika appar (till exempel Slack, Azure Logic Apps och så vidare.) och du kan implementera ditt eget sätt för att skicka meddelanden. Exempelvis kan beskriver den här artikeln du hur du hämtar autoshutdown meddelande från e-postmeddelanden med hjälp av Azure Logic Apps. Först ska vi snabbt gå igenom de grundläggande stegen för att aktivera autoshutdown avisering i labbet.   

## <a name="create-a-logic-app-that-receives-email-notifications"></a>Skapa en logikapp som tar emot e-postaviseringar
[Med Azure Logic Apps](../logic-apps/logic-apps-overview.md) innehåller många out-of the box-anslutningsappar som gör det enkelt att integrera en tjänst med andra klienter, till exempel Office 365 och twitter. På hög nivå, kan hur du konfigurerar en Logikapp för e-postmeddelande delas in i fyra faser: 

- Skapa en logikapp. 
- Konfigurera inbyggd mall.
- Integrera med din e-postklient
- Få en Webhook-URL.

### <a name="create-a-logic-app"></a>Skapa en logikapp
Kom igång genom att skapa en logikapp i Azure-prenumerationen med hjälp av följande steg:

1. Välj **+ skapa en resurs** på menyn till vänster väljer **integrering**, och välj **Logikapp**. 

    ![Ny logikappmenyn](./media/devtest-lab-auto-shutdown/new-logic-app.png)
2. På den **Logikapp – skapa** utför de här stegen: 
    1. Ange en **namn** för logikappen.
    2. Välj din Azure-**prenumeration**.
    3. Skapa en ny **resursgrupp** eller välj en befintlig resursgrupp. 
    4. Välj en **plats** för logikappen. 

        ![Ny logikapp – inställningar](./media/devtest-lab-auto-shutdown/new-logic-app-page.png)
3. I den **meddelanden**väljer **gå till resurs** på meddelandet. 

    ![Gå till resurs](./media/devtest-lab-auto-shutdown/go-to-resource.png)
4. Välj **logikappdesignern** under **distributionsverktyg** kategori.

    ![Välj HTTP-begäran/svar](./media/devtest-lab-auto-shutdown/select-http-request-response-option.png)
5. På den **HTTP Request-Response** väljer **Använd den här mallen**. 

    ![Välj att använda det här mallalternativet om](./media/devtest-lab-auto-shutdown/select-use-this-template.png)
6. Kopiera följande JSON till det **begär JSON-Brödtextsschema** avsnittet: 

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
    
    ![Begär JSON-Brödtextsschema](./media/devtest-lab-auto-shutdown/request-json.png)
7. Välj **+ nytt steg** i designer och gör följande:
    1. Sök efter **Office 365 Outlook – skicka ett e-postmeddelande**. 
    2. Välj **skicka ett e-postmeddelande** från **åtgärder**. 
    
        ![Skicka e-postalternativ](./media/devtest-lab-auto-shutdown/select-send-email.png)
    3. Välj **logga in** att logga in på ditt e-postkonto. 
    4. Välj **till** och Välj ägare.
    5. Välj **ämne**, och ange ett ämne för e-postmeddelandet. Exempel: ”Stänga av datorn vmName för Labb: labName”.
    6. Välj **BRÖDTEXT**, och definiera brödtext i e-postmeddelande. Till exempel: ”vmName är schemalagd att stängas av om 15 minuter. Hoppa över denna avstängning genom att klicka på: URL: EN. Fördröjning avstängning i en timme: delayUrl60. Fördröjning avstängning i 2 timmar: delayUrl120 ”.

        ![Begär JSON-Brödtextsschema](./media/devtest-lab-auto-shutdown/email-options.png)
1. Välj **Spara** i verktygsfältet. Nu kan du kopiera den **HTTP post-URL**. Välj kopieringsknappen för att kopiera Webbadressen till Urklipp. 

    ![WebHook-URL](./media/devtest-lab-auto-shutdown/webhook-url.png)

## <a name="next-steps"></a>Nästa steg
Läs hur du ställer in alla principer i [definiera labbprinciper i Azure DevTest Labs](devtest-lab-set-lab-policy.md).
