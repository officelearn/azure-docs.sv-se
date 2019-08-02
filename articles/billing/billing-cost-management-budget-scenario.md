---
title: Budget scenario för Azures fakturerings-och kostnads hantering | Microsoft Docs
description: Lär dig hur du använder Azure Automation för att stänga av virtuella datorer baserat på vissa budget trösklar.
services: billing
documentationcenter: ''
author: bandersmsft
manager: dougeby
editor: ''
tags: billing
ms.assetid: db93f546-6b56-4b51-960d-1a5bf0274fc8
ms.service: billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 03/13/2019
ms.author: banders
ms.openlocfilehash: 37f129526cb184a2eeee9e36028e8f00b5bbc247
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443465"
---
# <a name="manage-costs-with-azure-budgets"></a>Hantera kostnader med Azure Budgets

Kostnads kontroll är en viktig komponent för att maximera värdet av din investering i molnet. Det finns flera scenarier där kostnads-, rapporterings-och kostnadsbaserade Orchestration är kritiska för fortsatt affärs verksamhet. [Azure Cost Management API:](https://docs.microsoft.com/rest/api/consumption/) er tillhandahåller en uppsättning API: er som stöder var och en av dessa scenarier. API: erna tillhandahåller användnings information som gör att du kan visa detaljerade kostnader på instans nivå.

Budgetar används ofta som en del av kostnads kontrollen. Budgetar kan omfattas av Azure. Du kan till exempel begränsa din budgetvy baserat på prenumeration, resurs grupper eller en samling resurser. Förutom att använda budget-API: et för att meddela dig via e-post när ett budget tröskelvärde uppnås, kan du använda [Azure Monitor åtgärds grupper](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) för att utlösa en dirigerad uppsättning åtgärder som ett resultat av en budget händelse.

Ett vanligt budget scenario för en kund som kör icke-kritiska arbets belastningar kan inträffa när de vill hantera mot en budget och även få till gång till en förutsägbar kostnad när de tittar på månads fakturan. Det här scenariot kräver viss kostnadsbaserade dirigering av resurser som ingår i Azure-miljön. I det här scenariot anges en månatlig budget på $1000 för prenumerationen. Tröskelvärden för meddelanden är också inställda på att utlösa några dirigeringar. Det här scenariot börjar med en kostnads tröskel på 80%, vilket stoppar alla virtuella datorer i resurs gruppen **valfritt**. Sedan kommer alla VM-instanser att stoppas på 100% kostnads tröskeln.
Om du vill konfigurera det här scenariot utför du följande åtgärder genom att följa stegen i de olika avsnitten i den här självstudien.

De här åtgärderna som ingår i den här självstudien gör att du kan:

- Skapa en Azure Automation Runbook för att stoppa virtuella datorer genom att använda Webhooks.
- Skapa en Azure Logic-app som ska utlösas baserat på budget tröskelns värde och anropa runbooken med rätt parametrar.
- Skapa en Azure Monitor åtgärds grupp som ska konfigureras för att utlösa Azure Logic-appen när budget tröskeln uppfylls.
- Skapa Azure-budgeten med önskade tröskelvärden och sätt den i åtgärds gruppen.

## <a name="create-an-azure-automation-runbook"></a>Skapa en Azure Automation Runbook

[Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) är en tjänst som du kan använda för att skapa skript för de flesta resurs hanterings aktiviteter och köra dessa uppgifter som antingen schemalagda eller på begäran. Som en del i det här scenariot skapar du en [Azure Automation Runbook](https://docs.microsoft.com/azure/automation/automation-runbook-types) som ska användas för att stoppa virtuella datorer. Du kommer att använda den grafiska Runbook [v2](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) -datorns grafiska [](https://docs.microsoft.com/azure/automation/automation-runbook-gallery) Runbook från galleriet för att bygga det här scenariot. Genom att importera den här runbooken till ditt Azure-konto och publicera den, kan du stoppa virtuella datorer när ett budget tröskelvärde nås.

### <a name="create-an-azure-automation-account"></a>Skapa ett Azure Automation-konto

1. Logga in på [Azure Portal](https://portal.azure.com/) med autentiseringsuppgifterna för ditt Azure-konto.
2. Klicka på knappen **skapa en resurs** som finns i det övre vänstra hörnet i Azure.
3. Välj **hanterings verktyg** > **Automation**.
   > [!NOTE]
   > Om du inte har ett Azure-konto kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/).
4. Ange din konto information. För **Skapa Azure kör som-konto**väljer du **Ja** för att automatiskt aktivera de inställningar som krävs för att förenkla autentiseringen till Azure.
5. När du är färdig klickar du på **Skapa** för att starta distributionen av Automation-kontot.

### <a name="import-the-stop-azure-v2-vms-runbook"></a>Importera Runbook v2-Runbook för virtuella datorer

Med hjälp av en [Azure Automation Runbook](https://docs.microsoft.com/azure/automation/automation-runbook-types)importerar du det [stoppade Azure v2](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) -grafiska Runbook-flödet från galleriet.

1.  Logga in på [Azure Portal](https://portal.azure.com/) med autentiseringsuppgifterna för ditt Azure-konto.
2.  Öppna ditt Automation-konto genom att välja **alla tjänster** > **Automation-konton**. Välj sedan ditt Automation-konto.
3.  Klicka på **Runbook** -galleriet i avsnittet **process automatisering** .
4.  Ange **Galleri källan** till **Script Center** och välj **OK**.
5.  Leta upp och markera Galleri objektet [stoppa Azure v2-virtuella datorer](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) i Azure Portal.
6.  Klicka på **Importera** om du vill visa **import** bladet och välj **OK**. Bladet för Runbook-översikten visas.
7.  När runbook har slutfört import processen väljer du **Redigera** för att visa den grafiska Runbook-redigeraren och publicerings alternativet.

    ![Azure – redigera grafisk Runbook](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-01.png)
8.  Klicka på knappen **publicera** för att publicera runbooken och välj sedan **Ja** när du uppmanas att göra det. När du publicerar en Runbook åsidosätter du eventuell befintlig publicerad version med utkastet. I det här fallet har du ingen publicerad version eftersom du har skapat runbooken.

    Mer information om hur du publicerar en Runbook finns i [skapa en grafisk Runbook](https://docs.microsoft.com/azure/automation/automation-first-runbook-graphical).

## <a name="create-webhooks-for-the-runbook"></a>Skapa Webhooks för Runbook

Med hjälp av den grafiska runbooken för [Azure v2-datorer](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) skapar du två Webhooks för att starta runbooken i Azure Automation via en enda http-begäran. Den första webhooken anropar runbooken vid en 80%-budget tröskel med resurs gruppens namn som en parameter, vilket gör att de valfria virtuella datorerna kan stoppas. Sedan anropar den andra webhooken Runbook utan parametrar (vid 100%) som stoppar alla återstående VM-instanser.

1. På sidan **Runbooks** i [Azure Portal](https://portal.azure.com/)klickar du på den **StopAzureV2Vm** Runbook som visas på presentations bladet för Runbook.
2. Klicka  på webhook överst på sidan för att öppna bladet **Lägg till webhook** .
3. Klicka på **Skapa ny webhook** för att öppna bladet **skapa en ny webhook** .
4. Ange **namnet** på webhooken till **valfritt**. Egenskapen **Enabled** måste vara **Yes**. Värdet  för Expires behöver inte ändras. Mer information om webhook-egenskaper finns i [information om en webhook](https://docs.microsoft.com/azure/automation/automation-webhooks#details-of-a-webhook).
5. Klicka på kopierings ikonen bredvid URL-värdet för att kopiera webb adressen för webhooken.
   > [!IMPORTANT]
   > Spara URL: en för webhooken som heter **valfri** på ett säkert ställe. Du kommer att använda URL-adressen senare i den här självstudien. Av säkerhets skäl kan du inte Visa eller Hämta URL: en igen när du skapar webhooken.
6. Klicka på **OK** för att skapa den nya webhooken.
7. Klicka på **Konfigurera parametrar och kör inställningar** för att Visa parameter värden för runbooken.
   > [!NOTE]
   > Om runbooken har obligatoriska parametrar kan du inte skapa webhooken om inte värden anges.
8. Klicka på **OK** om du vill acceptera värdena för webhook-parametern.
9. Skapa webhooken genom att klicka på **skapa** .
10. Följ sedan stegen ovan för att skapa en andra webhook med namnet **Complete**.
    > [!IMPORTANT]
    > Se till att spara både webhook-URL: er för att använda dem senare i den här självstudien. Av säkerhets skäl kan du inte Visa eller Hämta URL: en igen när du skapar webhooken.

Nu bör du ha två konfigurerade webhookar som är tillgängliga med de URL: er som du har sparat.

![Webhook-valfria och fullständiga](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-02.png)

Du är nu klar med Azure Automation-installationen. Du kan testa webhookarna med ett enkelt Postman-test för att kontrol lera att webhooken fungerar. Sedan måste du skapa Logi Kap par för dirigering.

## <a name="create-an-azure-logic-app-for-orchestration"></a>Skapa en Azure Logic-app för dirigering

Logic Apps hjälpa dig att bygga, schemalägga och automatisera processer som arbets flöden så att du kan integrera appar, data, system och tjänster i företag eller organisationer. I det här scenariot gör den [logiska appen](https://docs.microsoft.com/azure/logic-apps/) som du skapar lite mer än att bara anropa Automation-webhooken som du skapade.

Budgetar kan konfigureras för att utlösa ett meddelande när ett angivet tröskelvärde uppfylls. Du kan ange flera tröskelvärden som ska meddelas och Logic-appen visar möjligheten att utföra olika åtgärder baserat på tröskelvärdet. I det här exemplet ska du skapa ett scenario där du får ett par aviseringar, det första meddelandet är när 80% av budgeten har nåtts och det andra meddelandet är när 100% av budgeten har uppnåtts. Logic-appen kommer att användas för att stänga av alla virtuella datorer i resurs gruppen. Först kommer det **valfria** tröskelvärdet att nås vid 80% och sedan kommer det andra tröskelvärdet att nås där alla virtuella datorer i prenumerationen stängs av.

Med Logic Apps kan du ange ett exempel schema för HTTP-utlösaren, men du måste ange **Content-Type-** huvudet. Eftersom åtgärds gruppen inte har anpassade sidhuvuden för webhooken, måste du tolka nytto lasten i ett separat steg. Du kommer att använda **parse** -åtgärden och tillhandahålla en exempel nytto Last.

### <a name="create-the-logic-app"></a>Skapa Logic-appen

Logic-appen utför flera åtgärder. I följande lista finns en uppsättning åtgärder på hög nivå som ska utföras av Logic app:
- Identifierar när en HTTP-begäran tas emot
- Parsa de överförda JSON-data för att fastställa tröskelvärdet som har nåtts
- Använd en villkorlig instruktion för att kontrol lera om tröskel beloppet har uppnått 80% eller mer av budget intervallet, men inte större än eller lika med 100%.
    - Om det här tröskelvärdet har uppnåtts skickar du ett HTTP-inlägg med webhooken som heter **valfri**. Den här åtgärden stänger de virtuella datorerna i den "valfria" gruppen.
- Använd en villkors instruktion för att kontrol lera om tröskel beloppet har nått eller överskridit 100% av budget värdet.
    - Om tröskel beloppet har nåtts skickar du ett HTTP-inlägg med webhooken med namnet **Complete**. Den här åtgärden stänger alla återstående virtuella datorer.

Följande steg krävs för att skapa den Logic app som ska utföra stegen ovan:

1.  På [Azure-portalen](https://portal.azure.com/) väljer du **Skapa en resurs** > **Integrering** > **Logikapp**.

    ![Azure – Välj Logic app-resursen](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-03.png)
2.  På bladet **skapa logisk app** anger du informationen som behövs för att skapa din Logi Kap par. Välj **Fäst på instrument panelen**och klicka på **skapa**.

    ![Azure – Skapa en logisk app](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-03a.png)

När Azure har distribuerat din Logic app öppnas **Logic Apps designer** och visar ett blad med en introduktions video och ofta använda utlösare.

### <a name="add-a-trigger"></a>Lägg till en utlösare

Varje logikapp måste börja med en utlösare som utlöses när en specifik händelse sker eller när ett särskilt villkor uppfylls. Varje gång utlösaren körs skapar Logic Apps-motorn en logikappinstans som startar och kör ditt arbetsflöde. Åtgärder är alla steg som sker efter utlösaren.

1.  Under **mallar** på bladet **Logic Apps designer** väljer du **Tom Logic app**.
2.  Lägg till [](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview#logic-app-concepts) en utlösare genom att ange "http-begäran" i sökrutan i **Logic Apps designer** för att hitta och välja utlösaren med namnet **Request – när en http-begäran tas emot**.

    ![Azure-Logic app – http-utlösare](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-04.png)
3.  Välj **nytt steg** > **Lägg till en åtgärd**.

    ![Azure – nytt steg – Lägg till en åtgärd](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-05.png)
4.  Sök efter "parsa JSON" i sökrutan i **Logic Apps designer** för att hitta och välja **data åtgärder – parsa JSON-** [åtgärd](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview#logic-app-concepts).

    ![Azure – Logic app – Lägg till parse JSON-åtgärd](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-06.png)
5.  Ange "nytto Last" som **innehålls** namn för PARSa JSON-nyttolasten eller Använd body-taggen från dynamiskt innehåll.
6.  Välj alternativet **Använd exempel nytto last för att skapa schema** i rutan **parsa JSON** .

    ![Azure-Logic app – Använd exempel-JSON-data för att generera schemat](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-07.png)
7.  Klistra in följande JSON-exempel nytto Last i text rutan:`{"schemaId":"AIP Budget Notification","data":{"SubscriptionName":"CCM - Microsoft Azure Enterprise - 1","SubscriptionId":"<GUID>","SpendingAmount":"100","BudgetStartDate":"6/1/2018","Budget":"50","Unit":"USD","BudgetCreator":"email@contoso.com","BudgetName":"BudgetName","BudgetType":"Cost","ResourceGroup":"","NotificationThresholdAmount":"0.8"}}`

    Text rutan ser ut så här:

    ![Azure – Logic app – exempel-JSON-nyttolasten](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-08.png)
8.  Klicka på **Klar**.

### <a name="add-the-first-conditional-action"></a>Lägg till den första villkorliga åtgärden

Använd en villkorlig instruktion för att kontrol lera om tröskel beloppet har uppnått 80% eller mer av budget intervallet, men inte större än eller lika med 100%. Om det här tröskelvärdet har uppnåtts skickar du ett HTTP-inlägg med webhooken som heter **valfri**. Den här åtgärden stänger de virtuella datorerna i den **valfria** gruppen.

1.  Välj **nytt steg** > **Lägg till ett villkor**.

    ![Azure – Logic app – Lägg till ett villkor](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-09.png)
2.  I rutan **villkor** klickar du på text rutan som innehåller **Välj ett värde** om du vill visa en lista över tillgängliga värden.

    ![Rutan Azure-Logic app-Condition](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-10.png)

3.  Klicka på **uttryck** högst upp i listan och ange följande uttryck i uttrycks redigeraren:`float()`

    ![Azure-Logic app-float-uttryck](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-11.png)

4.  Välj **dynamiskt innehåll**, placera markören inuti parentesen () och välj **NotificationThresholdAmount** i listan för att fylla i det fullständiga uttrycket.

    Uttrycket är följande:<br>
    `float(body('Parse_JSON')?['data']?['NotificationThresholdAmount'])`

5.  Välj **OK** för att ställa in uttrycket.
6.  Select **är större än eller lika** med i list rutan för villkoret .
7.  I rutan **Välj ett värde** för villkoret anger `.8`du.

    ![Azure-Logic app-float-uttryck med ett värde](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-12.png)

8.  Klicka på **Lägg** > till**Lägg till rad** i rutan villkor om du vill lägga till ytterligare en del av villkoret.
9.  I rutan **villkor** klickar du på text rutan som innehåller **Välj ett värde**.
10. Klicka på **uttryck** högst upp i listan och ange följande uttryck i uttrycks redigeraren:`float()`
11. Välj **dynamiskt innehåll**, placera markören inuti parentesen () och välj **NotificationThresholdAmount** i listan för att fylla i det fullständiga uttrycket.
12. Välj **OK** för att ställa in uttrycket.
13. Select **är mindre än** i list rutan för villkoret .
14. I rutan **Välj ett värde** för villkoret anger `1`du.

    ![Azure-Logic app-float-uttryck med ett värde](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-13.png)

15. I rutan **om sant** väljer du **Lägg till en åtgärd**. Du kommer att lägga till en HTTP POST-åtgärd som stänger av valfria virtuella datorer.

    ![Azure – Logic app – Lägg till en åtgärd](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-14.png)

16. Ange **http** om du vill söka efter http-åtgärden och välj **http – http** -åtgärd.

    ![Azure-Logic app – Lägg till HTTP-åtgärd](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-15.png)

17. Välj **post** som för **metod** värde.
18. Ange URL: en för webhooken som heter **valfri** som du skapade tidigare i den här självstudien som **URI** -värdet.

    ![Azure-Logic app – HTTP-åtgärds-URI](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-16.png)

19. Välj **Lägg till en åtgärd** i rutan **om sant** . Du kommer att lägga till en e-poståtgärd som skickar ett e-postmeddelande som meddelar mottagaren att de valfria virtuella datorerna har stängts av.
20. Sök efter "skicka e-post" och Välj åtgärden *skicka e-post* baserat på den e-posttjänst som du använder.

    ![Azure – Logic app – åtgärden skicka e-post](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-17.png)

    För personliga Microsoft-konton väljer du **Outlook.com**. För Azure arbets- eller skolkonto väljer du **Office 365 Outlook**. Om du inte har någon anslutning ombeds du logga in på ditt e-postkonto. Logic Apps skapar en anslutning till ditt e-postkonto.

    Du måste tillåta att Logic-appen får åtkomst till din e-postinformation.

    ![Meddelande om Azure-Logic app-Access](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-18.png)

21. Lägg till texten **till**, **ämne**och **brödtext** för e-postmeddelandet som meddelar mottagaren om att de valfria virtuella datorerna har stängts av. Använd **BudgetName** och det dynamiska innehållet i **NotificationThresholdAmount** för att fylla i fälten ämne och brödtext.

    ![Azure – Logic app – e-postinformation](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-19.png)

### <a name="add-the-second-conditional-action"></a>Lägg till den andra villkorliga åtgärden

Använd en villkors instruktion för att kontrol lera om tröskel beloppet har nått eller överskridit 100% av budget värdet. Om tröskel beloppet har nåtts skickar du ett HTTP-inlägg med webhooken med namnet **Complete**. Den här åtgärden stänger alla återstående virtuella datorer.

1.  Välj **nytt steg** > **Lägg till ett villkor**.

    ![Azure – Logic app – Lägg till åtgärd](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-20.png)

2.  I rutan **villkor** klickar du på text rutan som innehåller **Välj ett värde** om du vill visa en lista över tillgängliga värden.
3.  Klicka på **uttryck** högst upp i listan och ange följande uttryck i uttrycks redigeraren:`float()`
4.  Välj **dynamiskt innehåll**, placera markören inuti parentesen () och välj **NotificationThresholdAmount** i listan för att fylla i det fullständiga uttrycket.

    Uttrycket är följande:<br>
    `float(body('Parse_JSON')?['data']?['NotificationThresholdAmount'])`

5.  Välj **OK** för att ställa in uttrycket.
6.  Select **är större än eller lika** med i list rutan för villkoret .
7.  I **rutan Välj ett värde** för villkoret anger `1`du.

    ![Azure – Logic app-ange villkors värde](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-21.png)

8.  I rutan **om sant** väljer du **Lägg till en åtgärd**. Du kommer att lägga till en HTTP POST-åtgärd som stänger av alla återstående virtuella datorer.

    ![Azure – Logic app – Lägg till en åtgärd](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-22.png)

9.  Ange **http** om du vill söka efter http-åtgärden och välj **http – http** -åtgärd.
10. Välj **post** som för **metod** värde.
11. Ange URL: en för webhooken som heter **Complete** som du skapade tidigare i den här självstudien som **URI** -värdet.

    ![Azure – Logic app – Lägg till en åtgärd](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-23.png)

12. Välj **Lägg till en åtgärd** i rutan **om sant** . Du kommer att lägga till en e-poståtgärd som skickar ett e-postmeddelande som meddelar mottagaren att de återstående virtuella datorerna har stängts av.
13. Sök efter "skicka e-post" och Välj åtgärden *skicka e-post* baserat på den e-posttjänst som du använder.
14. Lägg till texten **till**, **ämne**och **brödtext** för e-postmeddelandet som meddelar mottagaren om att de valfria virtuella datorerna har stängts av. Använd **BudgetName** och det dynamiska innehållet i **NotificationThresholdAmount** för att fylla i fälten ämne och brödtext.

    ![Azure – Logic app – skicka e-postinformation](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-24.png)

15. Klicka på **Spara** överst på bladet **Logic App Designer** .

### <a name="logic-app-summary"></a>Sammanfattning av Logic app

Så här ser din Logic app ut när du är klar. I de flesta scenarier där du inte behöver något tröskel-baserat dirigering kan du anropa Automation-skriptet direkt från **övervakaren** och hoppa över steget för **Logic app** .

   ![Azure – Logic app – fullständig vy](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-25.png)

När du sparade din Logic app genererades en URL som du kan anropa. Du kommer att använda den här URL: en i nästa del av den här självstudien.

## <a name="create-an-azure-monitor-action-group"></a>Skapa en Azure Monitor åtgärds grupp

En åtgärds grupp är en samling aviserings inställningar som du definierar. När en avisering utlöses kan en speciell åtgärds grupp ta emot aviseringen genom att meddelas. En Azure-avisering genererar proaktivt ett meddelande baserat på vissa villkor och ger möjlighet att vidta åtgärder. En avisering kan använda data från flera källor, inklusive mått och loggar.

Åtgärds grupper är den enda slut punkten som du kommer att integrera med din budget. Du kan ställa in aviseringar i ett antal kanaler, men i det här scenariot ska du fokusera på den Logic-app som du skapade tidigare i den här självstudien.

### <a name="create-an-action-group-in-azure-monitor"></a>Skapa en åtgärds grupp i Azure Monitor

När du skapar åtgärds gruppen pekar du på den Logic-app som du skapade tidigare i den här självstudien.

1.  Om du inte redan är inloggad på [Azure Portal](https://portal.azure.com/), logga in och välj **alla tjänster** > **övervakare**.
2.  Välj **Åtgärds grupper** i **inställnings** avsnittet.
3.  Välj **Lägg till en åtgärds grupp** på bladet **Åtgärds grupper** .
4.  Lägg till och verifiera följande objekt:
    - Åtgärdsgruppnamn
    - Kortnamn
    - Subscription
    - Resource group

    ![Azure – Logic app – Lägg till en åtgärds grupp](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-26.png)

5.  I fönstret **Lägg till åtgärds grupp** lägger du till en LogicApp-åtgärd. Namnge åtgärden **budget-BudgetLA**. I fönstret **Logic app** väljer du prenumerationen  och **resurs gruppen**. Välj sedan den **Logic-app** som du skapade tidigare i den här självstudien.
6.  Klicka på **OK** för att ange Logic-appen. Välj sedan **OK** i fönstret **Lägg till åtgärds grupp** för att skapa åtgärds gruppen.

Du är klar med alla stöd komponenter som krävs för att effektivt dirigera din budget. Nu behöver du bara skapa budgeten och konfigurera den så att den använder den åtgärds grupp som du skapade.

## <a name="create-the-azure-budget"></a>Skapa Azure-budgeten

Du kan skapa en budget i Azure Portal med hjälp av [budget funktionen](../cost-management/tutorial-acm-create-budgets.md) i Cost Management. Du kan också skapa en budget med hjälp av REST-API: er, PowerShell-cmdletar eller använda CLI. I följande procedur används REST API. Innan du anropar REST API behöver du en autentiseringstoken. Om du vill skapa en autentiseringstoken kan du använda [ARMClient](https://github.com/projectkudu/ARMClient) -projektet. Med **ARMClient** kan du autentisera dig själv till Azure Resource Manager och hämta en token för att anropa API: erna.

### <a name="create-an-authentication-token"></a>Skapa en autentiseringstoken

1.  Navigera till [ARMClient](https://github.com/projectkudu/ARMClient) -projektet på GitHub.
2.  Klona lagrings platsen för att få en lokal kopia.
3.  Öppna projektet i Visual Studio och skapa det.
4.  När kompileringen är klar ska den körbara filen finnas i mappen *\Bin\Debug*
5.  Kör ARMClient. Öppna en kommando tolk och navigera till mappen *\Bin\Debug* från projekt roten.
6.  Logga in och autentisera genom att ange följande kommando i kommando tolken:<br>
    `ARMClient login prod`
7.  Kopiera prenumerations- **GUID** från utdata.
8.  Om du vill kopiera en autentiseringstoken till Urklipp anger du följande kommando i kommando tolken, men se till att använda det kopierade prenumerations-ID: t från steget ovan: <br>
    `ARMClient token <subscription GUID from previous step>`

    När du har slutfört steget ovan visas följande:<br>
    **Token har kopierats till Urklipp.**
9.  Spara token som ska användas för steg i nästa del av den här självstudien.

### <a name="create-the-budget"></a>Skapa budgeten

Sedan konfigurerar du Postman  för att skapa en budget genom att anropa REST-API: erna för Azure-förbrukning. Postman är en API-utvecklings miljö. Du kommer att importera miljö-och samlings filer till Postman. Samlingen innehåller grupperade definitioner av HTTP-begäranden som anropar Azure REST-API: er. Miljö filen innehåller variabler som används av samlingen.

1.  Hämta och öppna [Postman rest-klienten](https://www.getpostman.com/) för att köra REST-API: erna.
2.  I Postman skapar du en ny begäran.

    ![Postman – skapa en ny begäran](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-27.png)

3.  Spara den nya begäran som en samling så att den nya begäran inte innehåller något.

    ![Postman – Spara den nya begäran](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-28.png)

4.  Ändra begäran från en `Get` till en `Put` åtgärd.
5.  Ändra följande URL genom att ersätta `{subscriptionId}` med det **prenumerations-ID** som du använde i föregående avsnitt i den här självstudien. Ändra också URL: en så att den inkluderar "SampleBudget" som värde `{budgetName}`för:`https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Consumption/budgets/{budgetName}?api-version=2018-03-31`
6.  Välj fliken **sidhuvud** i Postman.
7.  Lägg till en ny **nyckel** med namnet "Authorization".
8.  Ange **värdet** till den token som skapades med ArmClient i slutet av det sista avsnittet.
9.  Fliken Välj **brödtext** i Postman.
10. Välj alternativet för **RAW** -knappen.
11. I text rutan klistrar du in i exempel budget definitionen nedan, men du måste ersätta parametrarna **subscriptionId**, **budgetname**och **actiongroupname** med ditt prenumerations-ID, ett unikt namn för din budget och åtgärds gruppens namn du skapade i både URL: en och begär ande texten:

    ```
        {
            "properties": {
                "category": "Cost",
                "amount": 100.00,
                "timeGrain": "Monthly",
                "timePeriod": {
                "startDate": "2018-06-01T00:00:00Z",
                "endDate": "2018-10-31T00:00:00Z"
                },
                "filters": {
                },
            "notifications": {
                "Actual_GreaterThan_80_Percent": {
                    "enabled": true,
                    "operator": "GreaterThan",
                    "threshold": 80,
                    "contactEmails": [
                    ],
                    "contactRoles": [
                    ],
                    "contactGroups": [
                    "/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/microsoft.insights/actionGroups/{actiongroupname}
                    ]
                },
            "Actual_EqualTo_100_Percent": {
                    "operator": "EqualTo",
                    "threshold": 100,
                    "contactGroups": [
                "/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/microsoft.insights/actionGroups/{actiongroupname}"
                    ]
                }
            }
        }
    ```
12. Skicka begäran genom att trycka på **Skicka** .

Nu har du alla delar som du behöver för att anropa [budget-API: et](https://docs.microsoft.com/rest/api/consumption/budgets). Budget-API-referensen innehåller ytterligare information om de olika förfrågningarna, inklusive följande:
    - **budgetName** – flera budgetar stöds.  Budget namn måste vara unika.
    - **kategori** – måste vara antingen **kostnad** eller **användning**. API: et stöder både kostnads-och användnings budgetar.
    - **timeGrain** – en månatlig, kvartals Visa eller årlig budget. Mängden återställs i slutet av perioden.
    - **filter** – filter gör att du kan begränsa budgeten till en specifik uppsättning resurser i det valda omfånget. Ett filter kan till exempel vara en samling resurs grupper för en budget på en prenumerations nivå.
    - **meddelanden** – bestämmer meddelande informationen och tröskelvärdena. Du kan ställa in flera tröskelvärden och ange en e-postadress eller en åtgärds grupp för att ta emot ett meddelande.

## <a name="summary"></a>Sammanfattning

Genom att följa den här självstudien har du lärt dig:
- Så här skapar du en Azure Automation Runbook för att stoppa virtuella datorer.
- Så här skapar du en Azure Logic-app som utlöses baserat på budget tröskel värden och anropar den relaterade runbooken med rätt parametrar.
- Så här skapar du en Azure Monitor åtgärds grupp som ska konfigureras för att utlösa Azure Logic-appen när budget tröskeln uppfylls.
- Så här skapar du en Azure-budget med önskade tröskelvärden och tråd den till åtgärds gruppen.

Nu har du en helt funktionell budget för din prenumeration som stänger av dina virtuella datorer när du når dina konfigurerade budget trösklar.

## <a name="next-steps"></a>Nästa steg

- Mer information om fakturerings scenarier i Azure finns i avsnittet fakturerings- [och kostnads hantering automatiserings scenarier](billing-cost-management-automation-scenarios.md).
