---
title: Azure-fakturering och kostnaden scenario för hantering av budget | Microsoft Docs
description: Lär dig hur du använder Azure automatation för att Stäng av virtuella datorer baserat på specifika budgetgränser.
services: billing
documentationcenter: ''
author: Erikre
manager: dougeby
editor: ''
tags: billing
ms.assetid: db93f546-6b56-4b51-960d-1a5bf0274fc8
ms.service: billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 7/25/2018
ms.author: erikre
ms.openlocfilehash: 08cdce23fe298bdac932d1ca8abb8166f4d7bbbd
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50094821"
---
# <a name="manage-costs-with-azure-budgets"></a>Hantera kostnader med Azure Budgets

Kostnadskontroll är en kritisk komponent för att maximera värdet av din investering i molnet. Det finns flera scenarier där kostnaden synlighet, rapportering och kostnadsbaserad orchestration är viktiga för driften av fortsatt verksamhet. [Azure Cost Management API: er](https://docs.microsoft.com/rest/api/consumption/) tillhandahåller en uppsättning API: er stöder var och en av dessa scenarier. API: er ger information om användning, så att du kan visa detaljerad nivå instanskostnader.

Budgetar används ofta som en del av kostnadskontroll. Budgetar kan begränsas i Azure. Du kan exempelvis begränsa budget vyn baserat på prenumerationen, resursgrupper eller en samling resurser. Förutom att använda budgetar API för att meddela dig via e-post när ett budget tröskelvärde har uppnåtts kan du använda [Azure Monitor åtgärdsgrupper](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) att utlösa en dirigerad uppsättning åtgärder till följd av en budget-händelse.

Ett vanligt scenario för budgetar för en kund som en icke-kritiska körning kan inträffa när de vill hantera mot en budget och även komma till en förutsägbar kostnad när du tittar på månatliga faktura. Det här scenariot kräver vissa kostnadsbaserad dirigering av resurser som ingår i Azure-miljön. En månatlig budget på 1 000 för prenumerationen som har angetts i det här scenariot. Dessutom ställs tröskelvärden för att utlösa några orkestreringar. Det här scenariot börjar med ett tröskelvärde som kostnaden med 80%, vilket stoppar alla virtuella datorer i resursgruppen **valfritt**. Sedan kommer att stoppas alla VM-instanser med 100% kostnaden-tröskelvärde.
Om du vill konfigurera det här scenariot utför du följande åtgärder genom att följa stegen i varje avsnitt i den här självstudien. 

De här åtgärderna som ingår i den här självstudien kan du:

- Skapa en Azure Automation-Runbook för att stoppa virtuella datorer med hjälp av webhooks.
- Skapa en Azure Logic App ska utlösas baserat på budget tröskelvärdet och anropar en runbook med rätt parametrar.
- Skapa en grupp av Azure Monitor-åtgärd som ska konfigureras för att utlösa Azure Logic App när budget-tröskelvärdet har uppnåtts.
- Skapa Azure budgeten med önskade trösklar och ansluta det till i åtgärdsgruppen.

## <a name="create-an-azure-automation-runbook"></a>Skapa en Azure Automation-Runbook

[Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) är en tjänst som gör att du kan skriva de flesta av dina resurs-hanteringsuppgifter och köra dessa uppgifter som schemalagd eller på begäran. Som en del av det här scenariot, skapar du en [Azure Automation-runbook](https://docs.microsoft.com/azure/automation/automation-runbook-types) som ska användas för att stoppa virtuella datorer. Du kommer att använda den [stoppa Azure V2-datorer](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) grafisk runbook från den [galleriet](https://docs.microsoft.com/azure/automation/automation-runbook-gallery) att skapa det här scenariot. Genom att importera denna runbook på Azure-kontot och publicera dem, kommer du att kunna stoppa virtuella datorer när ett budget tröskelvärde har uppnåtts. 

### <a name="create-an-azure-automation-account"></a>Skapa ett Azure Automation-konto

1.  Logga in på [Azure Portal](https://portal.azure.com/) med autentiseringsuppgifterna för ditt Azure-konto.
2.  Klicka på den **skapa en resurs** knappen hittades på det övre vänstra hörnet i Azure.
3.  Välj **hanteringsverktyg** > **Automation**.
    > [!NOTE]
    > Om du inte har ett Azure-konto kan du skapa en [kostnadsfritt konto](https://azure.microsoft.com/free/).
4.  Ange din kontoinformation. För **skapa kör som-konto**, Välj **Ja** automatisk aktivering av de inställningar som behövs för att förenkla autentisering till Azure.
5.  När du är färdig klickar du på **Skapa** för att starta distributionen av Automation-kontot.

### <a name="import-the-stop-azure-v2-vms-runbook"></a>Importera runbook stoppa Azure V2-datorer

Med hjälp av en [Azure Automation-runbook](https://docs.microsoft.com/azure/automation/automation-runbook-types), importera den [stoppa Azure V2-datorer](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) grafisk runbook från galleriet.

1.  Logga in på [Azure Portal](https://portal.azure.com/) med autentiseringsuppgifterna för ditt Azure-konto.
2.  Öppna ditt Automation-konto genom att välja **alla tjänster** > **Automationskonton**. Välj ditt Automation-konto.
3.  Klicka på **runbookgalleri** från den **Processautomatisering** avsnittet.
4.  Ange den **Gallerikälla** till **Skriptcenter** och välj **OK**.
5.  Leta upp och välj den [stoppa Azure V2-datorer](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) galleri-objekt i Azure-portalen.
6.  Klicka på den **Import** så öppnas den **Import** bladet och välj **OK**. Översiktsbladet för runbook visas.
7.  När runbooken har slutförts importen väljer **redigera** visar en grafisk runbook-redigeraren och publicering.
    
    ![Azure – redigera grafisk runbook](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-01.png)
8.  Klicka på den **publicera** knappen för att publicera en runbook och välj sedan **Ja** när du tillfrågas. När du publicerar en runbook kan åsidosätta du eventuella befintliga publicerade versionen med utkastversionen. I det här fallet har ingen publicerad version eftersom du har skapat runbooken. 

    Mer information om hur du publicerar en runbook finns i [skapa en grafisk runbook](https://docs.microsoft.com/azure/automation/automation-first-runbook-graphical).

## <a name="create-webhooks-for-the-runbook"></a>Skapa webhooks för runbook

Med hjälp av den [stoppa Azure V2-datorer](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) grafisk runbook, skapar du två Webhooks för att starta en runbook i Azure Automation via en HTTP-begäran. Första webhooken anropar en runbook med ett tröskelvärde för 80% budget med resursgruppens namn som en parameter, så att de valfria virtuella datorerna stoppas. Andra webhook kommer sedan anropa runbook utan parametrar (vid 100%), vilket stoppar alla återstående VM-instanser.

1.  Från den **Runbooks** sidan i den [Azure-portalen](https://portal.azure.com/), klickar du på den **StopAzureV2Vm** runbook som visar runbookens översiktsbladet. 
2.  Klicka på **Webhook** överst på sidan för att öppna den **Lägg till Webhook** bladet.
3.  Klicka på **Skapa ny webhook** att öppna den **Skapa ny webhook** bladet.
4.  Ange den **namn** webhookens till **valfritt**. Den **aktiverad** egenskapen måste vara **Ja**. Den **förfaller** värdet behöver inte ändras. Mer information om Webhook-egenskaper finns i [information om en webhook](https://docs.microsoft.com/azure/automation/automation-webhooks#details-of-a-webhook).
5.  Klicka på Kopiera-ikonen för att kopiera Webbadressen till webhooken bredvid URL-värdet. 
    > [!IMPORTANT]
    > Spara URL: en för webhook med namnet **valfritt** på en säker plats. Senare i den här självstudien använder du URL: en. Av säkerhetsskäl, när du har skapat webhooken, kan inte du visa eller hämta URL: en igen.
6.  Klicka på **OK** att skapa en ny webhook.
7.  Klicka på **konfigurera parametrar och körningsinställningar** att visa parametern värden för runbook. 
    > [!NOTE]
    > Om runbooken har obligatoriska parametrar, är du inte kunna skapa webhooken om inte värden anges.
8.  Klicka på **OK** att acceptera webhook parametrarnas värden.
9.  Klicka på **skapa** du skapar webhooken.
10. Sedan följer du stegen ovan för att skapa en andra webhook med namnet **Slutför**. 
    > [!IMPORTANT]
    > Glöm inte att spara båda webhook-URL: er att använda senare i den här självstudien. Av säkerhetsskäl, när du har skapat webhooken, kan inte du visa eller hämta URL: en igen.

Du bör nu ha två konfigurerade webhooks som är tillgängliga med de webbadresser som du sparade. 

![Webhooks - valfritt och fullständig](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-02.png)

Du är nu klar med Azure Automation-installationen. Du kan testa webhooks med ett enkelt Postman-test för att verifiera att webhooken fungerar. Därefter skapar du Logikapp för dirigering.

## <a name="create-an-azure-logic-app-for-orchestration"></a>Skapa ett Azure Logic Apps för dirigering

Logikappar kan du skapa, schemalägga och automatisera processer som arbetsflöden så att du kan integrera appar, data, system och tjänster för företag eller organisationer. I det här scenariot i [Logikapp](https://docs.microsoft.com/azure/logic-apps/) du skapar kommer göra lite mer än att bara anropa automation-webhook som du skapade. 

Budgetar kan ställas in för att utlösa en avisering när ett angivet tröskelvärde uppfylls. Du kan ange flera tröskelvärdena för att bli meddelad vid och Logikappen visar möjligheten att utföra olika åtgärder baserat på tröskeln uppfylls. I det här exemplet ska du ange dig ett scenario där du får ett par av meddelanden är den första aviseringen för när 80% av budgeten har uppnåtts och det andra meddelandet är om 100% av budgeten har uppnåtts. Logikappen används för att stänga av alla virtuella datorer i resursgruppen. Först och den **valfritt** infaller tröskelvärdet på 80% och andra tröskelvärdet kommer att nå där alla virtuella datorer i prenumerationen kommer att stängas av.

Logic apps kan du kan användas som exempelschema för HTTP-utlösaren, men måste du ange den **Content-Type** rubrik. Åtgärdsgruppen har inte anpassade huvuden för webhooken, måste du tolka nyttolasten i ett separat steg. Du kommer att använda den **parsa** åtgärd och tillhandahålla en exempelnyttolast.

### <a name="create-the-logic-app"></a>Skapa logic app

Logikappen utför flera åtgärder. I följande lista ger en övergripande uppsättning åtgärder som utförs med logikappen:
- Identifierar när en HTTP-begäran tas emot
- Analysera den överförda i JSON-data för att fastställa tröskelvärdet har uppnåtts
- Använda en villkorlig instruktion för att kontrollera om tröskelvärdet har uppnått 80% eller mer för budget-intervallet, men inte är större än eller lika med 100%.
    - Om den här tröskelvärde har uppnåtts, skicka en HTTP POST med hjälp av webhook med namnet **valfritt**. Den här åtgärden stängs de virtuella datorerna i gruppen ”valfritt”.
- Använda en villkorlig instruktion för att kontrollera om tröskelvärdet har nått eller överskridit 100% av budget-värdet.
    - Om tröskelvärdet har uppnåtts, skicka en HTTP POST med hjälp av webhook med namnet **Slutför**. Den här åtgärden stänger alla återstående virtuella datorerna.

Följande steg krävs för att skapa den logikapp som utför stegen ovan:

1.  I den [Azure-portalen](https://portal.azure.com/)väljer **skapa en resurs** > **integrering** > **Logikapp**.
    
    ![Azure - Välj logikappresursen](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-03.png)
2.  I den **skapa en logikapp** bladet ger information måste du skapa din logikapp, Välj **fäst på instrumentpanelen**, och klicka på **skapa**. 
    
    ![Azure – skapa en Logikapp](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-03a.png)

När Azure har distribuerat din logikapp den **Logic Apps Designer** öppnas och visar ett blad med en introduktion till video- och vanliga utlösare. 

### <a name="add-a-trigger"></a>Lägg till en utlösare

Varje logikapp måste börja med en utlösare som utlöses när en specifik händelse sker eller när ett särskilt villkor uppfylls. Varje gång utlösaren körs skapar Logic Apps-motorn en logikappinstans som startar och kör ditt arbetsflöde. Åtgärder är alla steg som sker efter utlösaren. 

1.  Under **mallar** av den **Logic Apps Designer** bladet välj **tom Logikapp**.
2.  Lägg till en [utlösaren](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview#logic-app-concepts) genom att ange ”http-begäran” i den **Logic Apps Designer** sökrutan för att söka efter och välja utlösare med namnet **begär – när en HTTP-begäran tas emot**.
    
    ![Azure - logikapp – Http-utlösare](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-04.png) 
3.  Välj **nytt steg** > **Lägg till en åtgärd**. 
    
    ![Azure - nytt steg – Lägg till en åtgärd](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-05.png) 
4.  Sök efter ”parsa JSON” i den **Logic Apps Designer** sökrutan för att söka efter och välja den **dataåtgärder – parsa JSON** [åtgärd](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview#logic-app-concepts). 
    
    ![Lägg till Azure - logikapp – parsa JSON-åtgärd](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-06.png) 
5.  Ange ”nyttolast” som den **innehåll** namn för parsa JSON-nyttolasten eller Använd taggen ”Body” från dynamiskt innehåll.
6.  Välj den **Använd exempel för att generera schemat** alternativet i den **parsa JSON** box.
    
    ![Azure - logikapp – Använd JSON-exempeldata generera schemat genom att](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-07.png) 
7.  Klistra in följande JSON-exempelnyttolast i textrutan: `{"schemaId":"AIP Budget Notification","data":{"SubscriptionName":"CCM - Microsoft Azure Enterprise - 1","SubscriptionId":"<GUID>","SpendingAmount":"100","BudgetStartDate":"6/1/2018","Budget":"50","Unit":"USD","BudgetCreator":"email@contoso.com","BudgetName":"BudgetName","BudgetType":"Cost","ResourceGroup":"","NotificationThresholdAmount":"0.8"}}`
    
    Textrutan visas som följande:
        
    ![Azure - logikapp – JSON-exempelnyttolast](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-08.png) 
8.  Klicka på **Klar**.

### <a name="add-the-first-conditional-action"></a>Lägg till den första villkorlig åtgärden

Använda en villkorlig instruktion för att kontrollera om tröskelvärdet har uppnått 80% eller mer för budget-intervallet, men inte är större än eller lika med 100%. Om den här tröskelvärde har uppnåtts, skicka en HTTP POST med hjälp av webhook med namnet **valfritt**. Den här åtgärden stängs de virtuella datorerna i den **valfritt** grupp.

1.  Välj **nytt steg** > **Lägg till ett villkor**.
    
    ![Azure - logikapp – Lägg till ett villkor](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-09.png) 
2.  I den **villkor** klickar du på den textruta som innehåller **Välj ett värde** att visa en lista över tillgängliga värden.
    
    ![Azure - logikapp – villkor box](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-10.png) 
        
3.  Klicka på **uttryck** överst i listan och anger sedan följande uttryck i uttrycksredigeraren: `float()`
    
    ![Azure - logikapp – flyttal uttryck](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-11.png) 
        
4.  Välj **dynamiskt innehåll**, placera markören mellan parenteser (-) och välj **NotificationThresholdAmount** från listan för att fylla i det fullständiga uttrycket. 
    
    Uttrycket är följande:<br>
    `float(body('Parse_JSON')?['data']?['NotificationThresholdAmount'])`
    
5.  Välj **OK** att ställa in uttrycket. 
6.  Välj **är större än eller lika med** i listrutan för den **villkor**.
7.  I den **Välj ett värde** av villkoret Skriv `.8`.
    
    ![Azure - logikapp – flyttal uttryck med ett värde](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-12.png) 
        
8.  Klicka på **Lägg till** > **Lägg till rad** i rutan villkor för att lägga till ytterligare en del av villkoret.
9.  I den **villkor** klickar du på den textruta som innehåller **Välj ett värde**.
10. Klicka på **uttryck** överst i listan och anger sedan följande uttryck i uttrycksredigeraren: `float()`
11. Välj **dynamiskt innehåll**, placera markören mellan parenteser (-) och välj **NotificationThresholdAmount** från listan för att fylla i det fullständiga uttrycket. 
12. Välj **OK** att ställa in uttrycket. 
13. Välj **är mindre än** i listrutan för den **villkor**.
14. I den **Välj ett värde** av villkoret Skriv `1`.
    
    ![Azure - logikapp – flyttal uttryck med ett värde](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-13.png) 
        
15. I den **om värdet är true** väljer **Lägg till en åtgärd**. Du lägger till en HTTP POST-åtgärd som ska stänga av valfria virtuella datorer.
    
    ![Azure - logikapp – Lägg till en åtgärd](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-14.png) 
        
16. Ange **HTTP** att söka efter HTTP-åtgärden och välj den **HTTP – HTTP** åtgärd.
    
    ![Azure - logikapp – Lägg till HTTP-åtgärd](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-15.png) 
        
17. Välj **Post** som den för den **metoden** värde.
18. Ange URL: en för webhook med namnet **valfritt** som du skapade tidigare i självstudien som den **Uri** värde.
    
    ![Azure - logikapp – HTTP-åtgärd URI](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-16.png) 
        
19. Välj **Lägg till en åtgärd** i den **om värdet är true** box. Du lägger till en e poståtgärd som skickar ett e-postmeddelande till mottagaren om att de valfria virtuella datorerna har stängts av.
20. Sök efter ”skicka e-post” och välj en *skicka e-postmeddelande* åtgärd baserat på e-posttjänst som du använder.
        
    ![Azure - logikapp – skicka e-poståtgärd](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-17.png) 
    
    För personliga Microsoft-konton väljer du **Outlook.com**. För Azure arbets- eller skolkonto väljer du **Office 365 Outlook**. Om du inte har någon anslutning ombeds du logga in på ditt e-postkonto. Logic Apps skapar en anslutning till ditt e-postkonto.
        
    Du behöver så att Logikappen att komma åt din e-post-information.
    
    ![Azure - logikapp – åtkomst meddelande](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-18.png) 
        
21. Lägg till den **till**, **ämne**, och **brödtext** texten för e-postmeddelande som informerar mottagaren om att de valfria virtuella datorerna har stängts av. Använd den **BudgetName** och **NotificationThresholdAmount** dynamiskt innehåll för att fylla i fälten ämne och brödtext.
    
    ![Information om Azure - logikapp – e-post](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-19.png) 
 
### <a name="add-the-second-conditional-action"></a>Lägg till andra villkorlig åtgärd

Använda en villkorlig instruktion för att kontrollera om tröskelvärdet har nått eller överskridit 100% av budget-värdet. Om tröskelvärdet har uppnåtts, skicka en HTTP POST med hjälp av webhook med namnet **Slutför**. Den här åtgärden stänger alla återstående virtuella datorerna.

1.  Välj **nytt steg** > **Lägg till ett villkor**.
    
    ![Azure - logikapp – Lägg till åtgärd](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-20.png) 
        
2.  I den **villkor** klickar du på den textruta som innehåller **Välj ett värde** att visa en lista över tillgängliga värden.
3.  Klicka på **uttryck** överst i listan och anger sedan följande uttryck i uttrycksredigeraren: `float()`
4.  Välj **dynamiskt innehåll**, placera markören mellan parenteser (-) och välj **NotificationThresholdAmount** från listan för att fylla i det fullständiga uttrycket. 
    
    Uttrycket är följande:<br>
    `float(body('Parse_JSON')?['data']?['NotificationThresholdAmount'])`
        
5.  Välj **OK** att ställa in uttrycket. 
6.  Välj **är större än eller lika med** i listrutan för den **villkor**.
7.  I den **väljer en värderuta** av villkoret ange `1`.
    
    ![Azure - logikapp – uppsättning villkor värde](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-21.png) 
        
8.  I den **om värdet är true** väljer **Lägg till en åtgärd**. Du lägger till en HTTP POST-åtgärd som stänger alla återstående virtuella datorerna.
    
    ![Azure - logikapp – Lägg till en åtgärd](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-22.png) 
    
9.  Ange **HTTP** att söka efter HTTP-åtgärden och välj den **HTTP – HTTP** åtgärd.
10. Välj **Post** som den för den **metoden** värde.
11. Ange URL: en för webhook med namnet **Slutför** som du skapade tidigare i självstudien som den **Uri** värde.
    
    ![Azure - logikapp – Lägg till en åtgärd](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-23.png) 
        
12. Välj **Lägg till en åtgärd** i den **om värdet är true** box. Du lägger till en e poståtgärd som skickar ett e-postmeddelande till mottagaren om att de återstående virtuella datorerna har stängts av.
13. Sök efter ”skicka e-post” och välj en *skicka e-postmeddelande* åtgärd baserat på e-posttjänst som du använder.
14. Lägg till den **till**, **ämne**, och **brödtext** texten för e-postmeddelande som informerar mottagaren om att de valfria virtuella datorerna har stängts av. Använd den **BudgetName** och **NotificationThresholdAmount** dynamiskt innehåll för att fylla i fälten ämne och brödtext.
    
    ![Information om Azure - logikapp – skicka e-post](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-24.png) 
        
15. Klicka på **spara** överst i den **Logic App Designer** bladet.

### <a name="logic-app-summary"></a>Logic App-sammanfattning

Här är din Logikapp ser det ut när du är klar. I den mest grundläggande scenarier där du inte behöver någon tröskelbaserade orchestration kan du direkt anropar automationsskript från **övervakaren** och hoppa över den **Logikapp** steg.

   ![Azure - logikapp – fullständig vy](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-25.png) 
 
När du har sparat din logikapp, skapades en URL som du kommer att kunna anropa. I nästa avsnitt av den här självstudiekursen ska du använda den här URL: en. 

## <a name="create-an-azure-monitor-action-group"></a>Skapa en åtgärdsgrupp för Azure Monitor

En åtgärdsgrupp är en samling av aviseringsinställningarna som du definierar. När en avisering har utlösts grupp för en specifik åtgärd kan ta emot aviseringen genom att ett meddelande. En Azure avisering proaktivt genererar en avisering baserat på specifika villkor och ger möjlighet att vidta åtgärder. En avisering kan använda data från flera källor, inklusive mått och loggar.

Åtgärdsgrupper är den enda slutpunkt som du vill integrera med din budget. Du kan ställa in meddelanden i flera kanaler, men i det här scenariot fokuserar på att Logikappen som du skapade tidigare i den här självstudien. 

### <a name="create-an-action-group-in-azure-monitor"></a>Skapa en åtgärdsgrupp i Azure Monitor

När du skapar åtgärdsgruppen pekar du på den Logikapp som du skapade tidigare i den här självstudien. 

1.  Om du inte redan har loggat in till den [Azure-portalen](https://portal.azure.com/), logga in och välj **alla tjänster** > **övervakaren**. 
2.  Välj **åtgärder grupper** från den **inställningen** avsnittet.
3.  Välj **Lägg till en åtgärdsgrupp** från den **åtgärdsgrupper** bladet.
4.  Lägg till och kontrollera följande:
    - Åtgärdsgruppnamn
    - Kortnamn
    - Prenumeration
    - Resursgrupp
    
    ![Azure - logikapp – Lägg till en åtgärdsgrupp](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-26.png) 
        
5.  I den **Lägg till åtgärdsgrupp** fönstret Lägg till en LogicApp-åtgärd. Namnge åtgärden **Budget BudgetLA**. I den **Logikapp** väljer den **prenumeration** och **resursgrupp**. Välj den **logikapp** som du skapade tidigare i självstudien.
6.  Klicka på **OK** att ställa in Logikappen. Välj **OK** i den **Lägg till åtgärdsgrupp** fönstret för att skapa åtgärdsgruppen.
    
Du är klar med alla de komponenter som behövs för att effektivt dirigera din budget. Nu behöver du bara skapa budgeten och konfigurera den att använda åtgärdsgrupp som du skapade.

## <a name="create-the-azure-budget"></a>Skapa Azure budgeten 

Budgetar har inte för närvarande ett portalgränssnitt i Azure. Men kan du anropa antingen REST API: er, Powershell-cmdletar, eller använda CLI. Följande procedur använder REST-API. Innan du anropar REST-API, måste en autentiseringstoken. Om du vill skapa en autentiseringstoken, kan du använda den [ARMClient](https://github.com/projectkudu/ARMClient) projekt. Den **ARMClient** kan du autentiserar dig till Azure Resource Manager och hämta en token för att anropa API: erna. 

### <a name="create-an-authentication-token"></a>Skapa en autentiseringstoken

1.  Navigera till den [ARMClient](https://github.com/projectkudu/ARMClient) projekt på GitHub.
2.  Klona lagringsplatsen för att få en lokal kopia.
3.  Öppna projektet i Visual Studio och skapa den.
4.  När versionen har lyckats, den körbara filen måste vara i den *\bin\debug* mapp.
5.  Kör ARMClient. Öppna en kommandotolk och navigera till den *\bin\debug* mappen från projektroten.
6.  Om du vill logga in och autentiseras, anger du följande kommando i Kommandotolken:<br>
    `ARMClient login prod`
7.  Kopiera den **prenumeration guid** från utdata.
8.  Ange följande kommando i Kommandotolken, men se till att använda kopierade prenumerations-ID från ovanstående steg om du vill kopiera en autentiseringstoken till Urklipp: <br>
    `ARMClient token <subscription GUID from previous step>`
    
    När du har slutfört ovanstående steg kan se du följande:<br>
    **Token är har kopierats till Urklipp.**
9.  Spara token som ska användas för stegen i nästa avsnitt i den här självstudien.

### <a name="create-the-budget"></a>Skapa budgeten

Därefter konfigurerar du **Postman** att skapa en budget genom att anropa Azure Consumption REST-API: er. Postman är en API-utvecklingsmiljö. Du importerar miljö och samling filer till Postman. Samlingen innehåller grupperade definitioner av HTTP-begäranden som anropar Azure Consumption REST API: er. Miljö-filen innehåller variabler som används av samlingen.

1.  Ladda ned och öppna den [Postman REST-klient](https://www.getpostman.com/) att köra REST-API: er.
2.  Skapa en ny begäran i Postman.
    
    ![Postman - skapa en ny begäran](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-27.png) 
        
3.  Spara den nya förfrågan som en samling så att den nya förfrågan har inget på den.
    
    ![Postman - spara den nya förfrågan](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-28.png) 
        
4.  Ändra begäran från en `Get` till en `Put` åtgärd.
5.  Ändra följande URL genom att ersätta `{subscriptionId}` med den **prenumerations-ID** som du använde i föregående avsnitt i den här självstudien. Ändra också URL och inkludera ”SampleBudget” som värde för `{budgetName}`: `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Consumption/budgets/{budgetName}?api-version=2018-03-31`
6.  Välj den **rubriker** flik i Postman.
7.  Lägga till en ny **nyckel** med namnet ”Authorization”.
8.  Ange den **värdet** till den token som har skapats med ArmClient i slutet av det sista avsnittet. 
9.  Välj **brödtext** flik i Postman. 
10. Välj den **raw** knappen alternativet.
11. I textrutan, klistra in den nedan exempeldefinition budget, men du måste ersätta den **subscriptionid**, **budgetname**, och **actiongroupname** parametrar med din prenumerations-id, ett unikt namn för din budget och namnet på åtgärden som du skapade i både URL: en och begärandetexten:
    
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
12. Tryck på **skicka** att skicka din begäran.

Nu har alla bitar på plats måste du anropa den [budget API](https://docs.microsoft.com/rest/api/consumption/budgets). API-referens för budgetar innehåller mer information om specifika begäranden, inklusive följande:
    - **budgetName** -flera budgetar stöds.  Budgetnamn måste vara unikt.
    - **kategori** -måste vara antingen **kostnaden** eller **användning**. API: et stöder budgetar för både kostnader och användning.
    - **timeGrain** -en budget per månad, kvartal eller år. Hur mycket återställer i slutet av perioden.
    - **filter** -filter kan du begränsa budget till en specifik uppsättning resurser som ligger inom det valda omfånget. Ett filter kan till exempel vara en samling resursgrupper för en prenumeration på budget.
    - **meddelanden** – avgör vilka meddelandeinformation och tröskelvärden. Du kan konfigurera flera tröskelvärden och ange en e-postadress eller en åtgärdsgrupp som tar emot ett meddelande.

## <a name="summary"></a>Sammanfattning

Genom att följa den här självstudien lärde du dig:
- Så här skapar du en Azure Automation-Runbook för att stoppa virtuella datorer.
- Så här skapar du en Logikapp i Azure som utlöses baserat på tröskelvärdena för budget och anropar en runbook som är relaterade med rätt parametrar.
- Så här skapar du en Azure Monitor-åtgärdsgrupp som har konfigurerats för att utlösa Azure Logic App när budget-tröskelvärdet har uppnåtts.
- Hur du skapar Azure budgeten med önskade trösklar och ansluta det till i åtgärdsgruppen.

Nu har du en helt funktionella budget för prenumerationen som stängs av de virtuella datorerna när du når dina konfigurerade budgetgränser. 

## <a name="next-steps"></a>Nästa steg

- Läs mer om Azure-fakturering scenarier [fakturerings- och kostnaden för automation hanteringsscenarier](billing-cost-management-automation-scenarios.md).
