---
title: Katastrofåterställning för integrationskonton
description: Konfigurera dina integrationskonton och B2B-artefakter med haveriberedskap mellan regioner i Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/10/2017
ms.openlocfilehash: 09b77862ad3379efeb8b3063a9d6c60b062ca2d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905135"
---
# <a name="set-up-cross-region-disaster-recovery-for-integration-accounts-in-azure-logic-apps"></a>Konfigurera haveriberedskap mellan regioner för integrationskonton i Azure Logic Apps

B2B-arbetsbelastningar omfattar pengatransaktioner som order och fakturor. Under en katastrofhändelse är det viktigt för ett företag att snabbt återhämta sig för att uppfylla de serviceavtal på affärsnivå som överenskommits med sina partner. Den här artikeln visar hur du skapar en kontinuitetsplan för B2B-arbetsbelastningar. 

* Beredskap för katastrofåterställning 
* Växla över till sekundär region under en katastrofhändelse 
* Återgå till primär region efter en katastrofhändelse

## <a name="disaster-recovery-readiness"></a>Beredskap för katastrofåterställning  

1. Identifiera en sekundär region och skapa ett [integrationskonto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) i den sekundära regionen.

2. Lägg till partner, scheman och avtal för de meddelandeflöden som krävs där körningsstatusen måste replikeras till sekundärt regionintegrationskonto.

   > [!TIP]
   > Kontrollera att det finns konsekvens i namnkonventionen för integrationskontot i olika regioner. 

3. Om du vill hämta körningsstatusen från den primära regionen skapar du en logikapp i den sekundära regionen. 

   Den här logikappen ska ha en *utlösare* och en *åtgärd*. 
   Utlösaren ska ansluta till det primära regionintegrationskontot och åtgärden ska ansluta till sekundärt regionintegrationskonto. 
   Baserat på tidsintervallet avrundar utlösaren statustabellen för primär regionkörning och hämtar de nya posterna, om sådana finns. Åtgärden uppdaterar dem till sekundärt regionintegrationskonto. 
   Detta hjälper till att få inkrementell körningsstatus från primär region till sekundär region.

4. Affärskontinuitet i Logic Apps integrationskonto är utformat för att stödja baserat på B2B-protokoll - X12, AS2 och EDIFACT. Om du vill hitta detaljerade steg väljer du respektive länkar.

5. Rekommendationen är att distribuera alla primära regionresurser i en sekundär region också. 

   Primära regionresurser inkluderar Azure SQL Database eller Azure Cosmos DB, Azure Service Bus och Azure Event Hubs som används för meddelanden, Azure API Management och Azure Logic Apps-funktionen i Azure App Service.   

6. Upprätta en anslutning från en primär region till en sekundär region. Om du vill hämta körningsstatusen från en primär region skapar du en logikapp i en sekundär region. 

   Logikappen ska ha en utlösare och en åtgärd. 
   Utlösaren ska ansluta till ett primärt regionintegrationskonto. 
   Åtgärden ska ansluta till ett sekundärt regionintegrationskonto. 
   Baserat på tidsintervallet avrundar utlösaren statustabellen för primär regionkörning och hämtar de nya posterna, om sådana finns. 
   Åtgärden uppdaterar dem till ett sekundärt regionintegrationskonto. 
   Den här processen hjälper till att få inkrementell körningsstatus från den primära regionen till den sekundära regionen.

Affärskontinuitet i ett Logic Apps-integrationskonto ger support baserat på B2B-protokollen X12, AS2 och EDIFACT. Detaljerade steg om hur du använder X12 och AS2 finns i [X12](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#x12) och [AS2](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#as2) i den här artikeln.

## <a name="fail-over-to-a-secondary-region-during-a-disaster-event"></a>Växla över till en sekundär region under en katastrofhändelse

Under en katastrofhändelse, när den primära regionen inte är tillgänglig för affärskontinuitet, dirigerar du trafik till den sekundära regionen. En sekundär region hjälper ett företag att snabbt återställa funktioner för att uppfylla rpo/rto som överenskommits av deras partner. Det minimerar också ansträngningarna att växla över från en region till en annan region. 

Det finns en förväntad svarstid när du kopierar kontrollnummer från en primär region till en sekundär region. För att undvika att skicka dubblettgenererade kontrollnummer till partner under en katastrofhändelse är rekommendationen att öka kontrollnumren i de sekundära regionavtalen med hjälp av [PowerShell-cmdlets](https://docs.microsoft.com/powershell/module/azurerm.logicapp/set-azurermintegrationaccountgeneratedicn?view=azurermps-6.13.0).

## <a name="fall-back-to-a-primary-region-post-disaster-event"></a>Återgå till en primär region efter katastrofen

Så här går du tillbaka till en primär region när den är tillgänglig:

1. Sluta ta emot meddelanden från partner i den sekundära regionen.  

2. Öka de genererade kontrollnumren för alla primära regionavtal med hjälp av [PowerShell-cmdlets](https://docs.microsoft.com/powershell/module/azurerm.logicapp/set-azurermintegrationaccountgeneratedicn?view=azurermps-6.13.0).  

3. Direkt trafik från den sekundära regionen till den primära regionen.

4. Kontrollera att logikappen som skapats i den sekundära regionen för att hämta körningsstatus från den primära regionen är aktiverad.

## <a name="x12"></a>X12 

Affärskontinuiteten för EDI X12-dokument baseras på kontrollnummer:

> [!TIP]
> Du kan också använda [snabbstartsmallen X12](https://azure.microsoft.com/resources/templates/201-logic-app-b2b-disaster-recovery-replication/) för att skapa logikappar. Att skapa primära och sekundära integrationskonton är förutsättningar att använda mallen. Mallen hjälper till att skapa två logikappar, en för mottagna kontrollnummer och ett annat för genererade kontrollnummer. Respektive utlösare och åtgärder skapas i logikapparna, som ansluter utlösaren till det primära integrationskontot och åtgärden till det sekundära integrationskontot.

**Krav**

Om du vill aktivera haveriberedskap för inkommande meddelanden väljer du inställningarna för dubblettkontroll i X12-avtalets mottagningsinställningar.

![Välj inställningar för dubblettkontroll](./media/logic-apps-enterprise-integration-b2b-business-continuity/dupcheck.png)  

1. Skapa en [logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md) i en sekundär region.    

2. Sök på **X12**och välj **X12 - När ett kontrollnummer ändras**.   

   ![Sök efter X12](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn1.png)

   Utlösaren uppmanar dig att upprätta en anslutning till ett integrationskonto. 
   Utlösaren ska vara ansluten till ett primärt regionintegrationskonto.

3. Ange ett anslutningsnamn, välj ditt *primära regionintegrationskonto* i listan och välj **Skapa**.   

   ![Namn på primärt områdesintegreringskonto](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn2.png)

4. DateTime **för att starta synkronisering av kontrollnummer** är valfritt. **Frekvensen** kan ställas in på **Dag,** **Timme,** **Minut**eller **Sekund** med ett intervall.   

   ![Datumtid och frekvens](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn3.png)

5. Välj **Nytt steg** > **Lägg till en åtgärd**.

   ![Nytt steg, Lägg till en åtgärd](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn4.png)

6. Sök på **X12**och välj **X12 - Lägg till eller uppdatera kontrollnummer**.   

   ![Lägga till eller uppdatera kontrollnummer](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn5.png)

7. Om du vill koppla en åtgärd till ett sekundärt regionintegrationskonto väljer du **Ändra anslutning** > **Lägg till ny anslutning** för en lista över tillgängliga integrationskonton. Ange ett anslutningsnamn, välj ditt *sekundära regionintegrationskonto* i listan och välj **Skapa**. 

   ![Kontonamn för sekundär regionintegration](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn6.png)

8. Växla till råa ingångar genom att klicka på ikonen i övre högra hörnet.

   ![Växla till råingångar](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12rawinputs.png)

9. Välj Brödtext i den dynamiska innehållsväljaren och spara logikappen.

   ![Fält för dynamiskt innehåll](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn7.png)

   Baserat på tidsintervallet avsökningar av den primära regionen som tas emot kontrollnummertabellen och hämtar de nya posterna. 
   Åtgärden uppdaterar posterna i det sekundära regionintegrationskontot. 
   Om det inte finns några uppdateringar visas utlösarstatusen som **Överhoppad**.   

   ![Tabell för kontrollnummer](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12recevicedcn8.png)

Baserat på tidsintervallet replikeras den inkrementella körningsstatusen från en primär region till en sekundär region. Under en katastrofhändelse, när den primära regionen inte är tillgänglig, dirigerar du trafik till den sekundära regionen för affärskontinuitet. 

## <a name="edifact"></a>EDIFACT 

Affärskontinuitet för EDI EDIFACT-dokument baseras på kontrollnummer.

**Krav**

Om du vill aktivera haveriberedskap för inkommande meddelanden väljer du dubblettkontrollinställningarna i EDIFACT-avtalets mottagningsinställningar.

![Välj inställningar för dubblettkontroll](./media/logic-apps-enterprise-integration-b2b-business-continuity/edifactdupcheck.png)  

1. Skapa en [logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md) i en sekundär region.    

2. Sök på **EDIFACT**och välj **EDIFACT - När ett kontrollnummer ändras**.

   ![Sök efter EDIFACT](./media/logic-apps-enterprise-integration-b2b-business-continuity/edifactcn1.png)

   Utlösaren uppmanar dig att upprätta en anslutning till ett integrationskonto. 
   Utlösaren ska vara ansluten till ett primärt regionintegrationskonto. 

3. Ange ett anslutningsnamn, välj ditt *primära regionintegrationskonto* i listan och välj **Skapa**.    

   ![Namn på primärt områdesintegreringskonto](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12CN2.png)

4. DateTime **för att starta synkronisering av kontrollnummer** är valfritt. **Frekvensen** kan ställas in på **Dag,** **Timme,** **Minut**eller **Sekund** med ett intervall.    

   ![Datumtid och frekvens](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn3.png)

6. Välj **Nytt steg** > **Lägg till en åtgärd**.    

   ![Nytt steg, Lägg till en åtgärd](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn4.png)

7. Sök på **EDIFACT**och välj **EDIFACT - Lägg till eller uppdatera kontrollnummer**.   

   ![Lägga till eller uppdatera kontrollnummer](./media/logic-apps-enterprise-integration-b2b-business-continuity/EdifactChooseAction.png)

8. Om du vill koppla en åtgärd till ett sekundärt regionintegrationskonto väljer du **Ändra anslutning** > **Lägg till ny anslutning** för en lista över tillgängliga integrationskonton. Ange ett anslutningsnamn, välj ditt *sekundära regionintegrationskonto* i listan och välj **Skapa**.

   ![Kontonamn för sekundär regionintegration](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn6.png)

9. Växla till råa ingångar genom att klicka på ikonen i övre högra hörnet.

   ![Växla till råingångar](./media/logic-apps-enterprise-integration-b2b-business-continuity/Edifactrawinputs.png)

10. Välj Brödtext i den dynamiska innehållsväljaren och spara logikappen.   

   ![Fält för dynamiskt innehåll](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12CN7.png)

   Baserat på tidsintervallet avsökningar av den primära regionen som tas emot kontrollnummertabellen och hämtar de nya posterna.
   Åtgärden uppdaterar posterna till det sekundära regionintegrationskontot. 
   Om det inte finns några uppdateringar visas utlösarstatusen som **Överhoppad**.

   ![Tabell för kontrollnummer](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12recevicedcn8.png)

Baserat på tidsintervallet replikeras den inkrementella körningsstatusen från en primär region till en sekundär region. Under en katastrofhändelse, när den primära regionen inte är tillgänglig, dirigerar du trafik till den sekundära regionen för affärskontinuitet. 

## <a name="as2"></a>AS2 

Affärskontinuiteten för dokument som använder AS2-protokollet baseras på meddelande-ID och MIC-värdet.

> [!TIP]
> Du kan också använda [snabbstartsmallen AS2](https://github.com/Azure/azure-quickstart-templates/pull/3302) för att skapa logikappar. Att skapa primära och sekundära integrationskonton är förutsättningar att använda mallen. Mallen hjälper till att skapa en logikapp som har en utlösare och en åtgärd. Logikappen skapar en anslutning från en utlösare till ett primärt integrationskonto och en åtgärd till ett sekundärt integrationskonto.

1. Skapa en [logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md) i den sekundära regionen.  

2. Sök på **AS2**och välj **AS2 - När ett MIC-värde skapas**.   

   ![Sök efter AS2](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid1.png)

   En utlösare uppmanar dig att upprätta en anslutning till ett integrationskonto. 
   Utlösaren ska vara ansluten till ett primärt regionintegrationskonto. 
   
3. Ange ett anslutningsnamn, välj ditt *primära regionintegrationskonto* i listan och välj **Skapa**.

   ![Namn på primärt områdesintegreringskonto](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid2.png)

4. DateTime **för att starta synkroniseringsinställningen för MIC-värde** är valfri. **Frekvensen** kan ställas in på **Dag,** **Timme,** **Minut**eller **Sekund** med ett intervall.   

   ![Datumtid och frekvens](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid3.png)

5. Välj **Nytt steg** > **Lägg till en åtgärd**.  

   ![Nytt steg, Lägg till en åtgärd](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid4.png)

6. Sök på **AS2**och välj **AS2 - Lägg till eller uppdatera MIC-innehåll**.  

   ![MIC-tillägg eller uppdatering](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid5.png)

7. Om du vill koppla en åtgärd till ett sekundärt integrationskonto väljer du **Ändra anslutning** > **Lägg till ny anslutning** för en lista över tillgängliga integrationskonton. Ange ett anslutningsnamn, välj ditt *sekundära regionintegrationskonto* i listan och välj **Skapa**.

   ![Kontonamn för sekundär regionintegration](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid6.png)

8. Växla till råa ingångar genom att klicka på ikonen i övre högra hörnet.

   ![Växla till råingångar](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2rawinputs.png)

9. Välj Brödtext i den dynamiska innehållsväljaren och spara logikappen.   

   ![Dynamiskt innehåll](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid7.png)

   Baserat på tidsintervallet avblöt utlösaren den primära regiontabellen och hämtar de nya posterna. Åtgärden uppdaterar dem till det sekundära regionintegrationskontot. 
   Om det inte finns några uppdateringar visas utlösarstatusen som **Överhoppad**.  

   ![Primär regiontabell](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid8.png)

Baserat på tidsintervallet replikeras den inkrementella körningsstatusen från den primära regionen till den sekundära regionen. Under en katastrofhändelse, när den primära regionen inte är tillgänglig, dirigerar du trafik till den sekundära regionen för affärskontinuitet. 

## <a name="next-steps"></a>Nästa steg

[Övervaka B2B-meddelanden med Azure Monitor-loggar](../logic-apps/monitor-b2b-messages-log-analytics.md)

