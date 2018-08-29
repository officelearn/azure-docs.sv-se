---
title: Haveriberedskap för B2B-integrationskonton – Azure Logic Apps | Microsoft Docs
description: Gör dig redo för interregionala katastrofåterställning i Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: cf44af18-1fe5-41d5-9e06-cc57a968207c
ms.date: 04/10/2017
ms.openlocfilehash: 3d465123f814887282bf2b29a5b6e0836601c243
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2018
ms.locfileid: "43123912"
---
# <a name="cross-region-disaster-recovery-for-b2b-integration-accounts-in-azure-logic-apps"></a>Interregionala katastrofåterställning för B2B-integrationskonton i Azure Logic Apps

B2B-arbetsbelastningar innehåller pengar transaktioner som order och fakturor. Vid ett haveri, det är viktigt för ett företag att snabbt återgå till att uppfylla serviceavtal för företag på företagsnivå överenskomna med deras partner. Den här artikeln visar hur du skapar en kontinuitetsplan för B2B-arbetsbelastningar. 

* Disaster Recovery-beredskap 
* Växla över till sekundär region under ett haveri 
* Återgå till den primära regionen efter ett haveri

## <a name="disaster-recovery-readiness"></a>Disaster Recovery-beredskap  

1. Ange en sekundär region och skapa en [integrationskontot](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) i den sekundära regionen.

2. Lägg till partners, scheman och avtal för meddelandet krävs flöden var körningsstatusen måste replikeras till den sekundära regionen integrationskontot.

   > [!TIP]
   > Kontrollera att det finns konsekvens i integration konto artefakten namngivningskonvention i olika regioner. 

3. Om du vill hämta körningsstatusen från den primära regionen, skapa en logikapp i den sekundära regionen. 

   Den här logikappen bör ha en *utlösaren* och en *åtgärd*. 
   Utlösaren ska ansluta till primär region integrationskontot och åtgärden ska ansluta till den sekundära regionen integrationskontot. 
   Baserat på tidsintervallet, utlösaren avsöker den primära regionen kör status tabell och hämtar nya poster, om sådana. Åtgärden uppdaterar dem till sekundär region integrationskontot. 
   Detta hjälper till att hämta inkrementella Körningsstatus från primära regionen till sekundär region.

4. Affärskontinuitet i Logic Apps integrationskontot är avsett att stödja baserat på B2B-protokoll - X12, AS2 och EDIFACT. Klicka på respektive länkarna för att hitta detaljerade anvisningar.

5. Rekommendationen är att distribuera alla primära regionen resurser i en sekundär region för. 

   Primär region resurser inkluderar Azure SQL Database eller Azure Cosmos DB, Azure Service Bus och Azure Event Hubs används för meddelanden, Azure API Management och Azure Logic Apps-funktionen i Azure App Service.   

6. Upprätta en anslutning från en primär region till en sekundär region. Om du vill hämta körningsstatusen från en primär region, skapar du en logikapp i en sekundär region. 

   Logikappen bör ha en utlösare och en åtgärd. 
   Utlösaren ska ansluta till en primär region integrationskontot. 
   Åtgärden ska ansluta till en sekundär region integrationskontot. 
   Baserat på tidsintervallet, utlösaren avsöker den primära regionen kör status tabell och hämtar nya poster, om sådana. 
   Åtgärden uppdaterar dem till en sekundär region integrationskontot. 
   Den här processen hjälper till att hämta inkrementella Körningsstatus från den primära regionen till den sekundära regionen.

Affärskontinuitet i ett konto för integrering av Logic Apps ger stöd baserat på de B2B-protokollen X12, AS2 och EDIFACT. Detaljerade anvisningar om hur du använder X12 och AS2 i [X12](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#x12) och [AS2](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#as2) i den här artikeln.

## <a name="fail-over-to-a-secondary-region-during-a-disaster-event"></a>Växla över till en sekundär region under ett haveri

Vid ett haveri, när den primära regionen inte är tillgänglig för affärskontinuitet, dirigera trafik till den sekundära regionen. En sekundär region kan ett företag för att återställa funktioner snabbt för att uppfylla Återställningspunktmål/Återställningstidsmål kommit överens om deras partner. Det minskar också ansträngningar för att växla över från en region till en annan region. 

Det finns en förväntad svarstid när du kopierar kontrollnummer från en primär region till en sekundär region. Om du vill undvika att skicka duplicerade genererade kontrollnummer till partners vid ett haveri, rekommendationen är att öka kontrollnumren i avtalen sekundär region med hjälp av [PowerShell-cmdletar](https://blogs.msdn.microsoft.com/david_burgs_blog/2017/03/09/fresh-of-the-press-new-azure-powershell-cmdlets-for-upcoming-x12-connector-disaster-recovery).

## <a name="fall-back-to-a-primary-region-post-disaster-event"></a>Växla över till en primär region efter haveri

Om du vill växla tillbaka till en primär region när den är tillgänglig, Följ dessa steg:

1. Sluta ta emot meddelanden från partner i den sekundära regionen.  

2. Öka den genererade kontrollnummer för alla de primära regionen avtal med hjälp av [PowerShell-cmdletar](https://blogs.msdn.microsoft.com/david_burgs_blog/2017/03/09/fresh-of-the-press-new-azure-powershell-cmdlets-for-upcoming-x12-connector-disaster-recovery).  

3. Dirigera trafik från den sekundära regionen till den primära regionen.

4. Kontrollera att den logikapp som skapats i den sekundära regionen för att ta emot Körningsstatus från den primära regionen är aktiverad.

## <a name="x12"></a>X12 

Affärskontinuitet för EDI X 12 dokument baseras på kontrollnummer:

> [!TIP]
> Du kan också använda den [X12 snabb start mallen](https://azure.microsoft.com/resources/templates/201-logic-app-b2b-disaster-recovery-replication/) att skapa logikappar. Skapa primära och sekundära integrationskonton är förutsättningar för att använda mallen. Det hjälper dig för att skapa två logic apps, en för mottagna kontrollnummer och en annan för genererade kontrollnummer mallen. Respektive utlösare och åtgärder som skapas i logic apps kan ansluta utlösaren till primära integrationskontot samt åtgärden att sekundära integrationskontot.

**Förutsättningar**

Om du vill aktivera haveriberedskap för inkommande meddelanden, Välj dubbla Kontrollera inställningarna i X12 avtalet tar emot inställningar.

![Välj kontrollen av dubblett-inställningar](./media/logic-apps-enterprise-integration-b2b-business-continuity/dupcheck.png)  

1. Skapa en [logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md) i en sekundär region.    

2. Sök på **X12**, och välj **X12-när ett kontrollnummer ändras**.   

   ![Sök efter X12](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn1.png)

   Utlösaren uppmanas du att upprätta en anslutning till ett integrationskonto. 
   Utlösaren ska anslutas till en primär region integrationskontot.

3. Anger du ett namn på anslutningen, väljer din *primära regionen integrationskontot* i listan och välj **skapa**.   

   ![Primär regionnamn på integrationskonto](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn2.png)

4. Den **DateTime för start av synkronisering av kontrollnummer** inställningen är valfri. Den **frekvens** kan anges till **dag**, **timme**, **minut**, eller **andra** med ett intervall.   

   ![Datum/tid och frekvens](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn3.png)

5. Välj **nytt steg** > **Lägg till en åtgärd**.

   ![Nytt steg, Lägg till en åtgärd](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn4.png)

6. Sök på **X12**, och välj **X12 – lägga till eller uppdatera kontrollnummer**.   

   ![Lägg till eller uppdatera kontrollnummer](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn5.png)

7. Om du vill ansluta en åtgärd till en sekundär region integrationskontot, Välj **ändra anslutning** > **Lägg till ny anslutning** en lista över tillgängliga integrationskonton. Ange ett namn, Välj din *sekundär region integrationskontot* i listan och välj **skapa**. 

   ![Sekundär regionnamn på integrationskonto](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn6.png)

8. Växla till råindata genom att klicka på ikonen i övre högra hörnet.

   ![Växla till rå-indata](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12rawinputs.png)

9. Välj brödtext från väljaren för dynamiskt innehåll och spara logikappen.

   ![Dynamiskt innehåll fält](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn7.png)

   Baserat på tidsintervallet, utlösaren avsöker tabellen primära region som tas emot kontroll nummer och hämtar de nya posterna. 
   Åtgärden uppdaterar posterna i integrationskontot sekundär region. 
   Om det finns inga uppdateringar, utlösarens status visas som **ignoreras**.   

   ![Register för kontroll](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12recevicedcn8.png)

Baserat på tidsintervallet, replikerar inkrementella Körningsstatus från en primär region till en sekundär region. Vid ett haveri, när den primära regionen inte är tillgänglig, direkt trafik till den sekundära regionen för affärskontinuitet. 

## <a name="edifact"></a>EDIFACT 

Affärskontinuitet för EDI EDIFACT-dokument baseras på kontrollnummer.

**Förutsättningar**

Välj dubbla Kontrollera inställningarna i din EDIFACT-avtal ta emot inställningar för att aktivera haveriberedskap för inkommande meddelanden.

![Välj kontrollen av dubblett-inställningar](./media/logic-apps-enterprise-integration-b2b-business-continuity/edifactdupcheck.png)  

1. Skapa en [logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md) i en sekundär region.    

2. Sök på **EDIFACT**, och välj **EDIFACT - när ett kontrollnummer ändras**.

   ![Sök efter EDIFACT](./media/logic-apps-enterprise-integration-b2b-business-continuity/edifactcn1.png)

   Utlösaren uppmanas du att upprätta en anslutning till ett integrationskonto. 
   Utlösaren ska anslutas till en primär region integrationskontot. 

3. Anger du ett namn på anslutningen, väljer din *primära regionen integrationskontot* i listan och välj **skapa**.    

   ![Primär regionnamn på integrationskonto](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12CN2.png)

4. Den **DateTime för start av synkronisering av kontrollnummer** inställningen är valfri. Den **frekvens** kan anges till **dag**, **timme**, **minut**, eller **andra** med ett intervall.    

   ![Datum/tid och frekvens](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn3.png)

6. Välj **nytt steg** > **Lägg till en åtgärd**.    

   ![Nytt steg, Lägg till en åtgärd](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn4.png)

7. Sök på **EDIFACT**, och välj **EDIFACT - Lägg till eller uppdatera kontrollnummer**.   

   ![Lägg till eller uppdatera kontrollnummer](./media/logic-apps-enterprise-integration-b2b-business-continuity/EdifactChooseAction.png)

8. Om du vill ansluta en åtgärd till en sekundär region integrationskontot, Välj **ändra anslutning** > **Lägg till ny anslutning** en lista över tillgängliga integrationskonton. Ange ett namn, Välj din *sekundär region integrationskontot* i listan och välj **skapa**.

   ![Sekundär regionnamn på integrationskonto](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn6.png)

9. Växla till råindata genom att klicka på ikonen i övre högra hörnet.

   ![Växla till rå-indata](./media/logic-apps-enterprise-integration-b2b-business-continuity/Edifactrawinputs.png)

10. Välj brödtext från väljaren för dynamiskt innehåll och spara logikappen.   

   ![Dynamiskt innehåll fält](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12CN7.png)

   Baserat på tidsintervallet, utlösaren avsöker tabellen primära region som tas emot kontroll nummer och hämtar de nya posterna.
   Åtgärden uppdaterar poster till integrationskontot sekundär region. 
   Om det finns inga uppdateringar, utlösarens status visas som **ignoreras**.

   ![Register för kontroll](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12recevicedcn8.png)

Baserat på tidsintervallet, replikerar inkrementella Körningsstatus från en primär region till en sekundär region. Vid ett haveri, när den primära regionen inte är tillgänglig, direkt trafik till den sekundära regionen för affärskontinuitet. 

## <a name="as2"></a>AS2 

Affärskontinuitet för dokument som använder AS2-protokollet baseras på det meddelande-ID och MIC-värdet.

> [!TIP]
> Du kan också använda den [AS2 Snabbstart mallen](https://github.com/Azure/azure-quickstart-templates/pull/3302) att skapa logikappar. Skapa primära och sekundära integrationskonton är förutsättningar för att använda mallen. Mallen hjälper dig att skapa en logikapp som har en utlösare och en åtgärd. Logikappen skapar en anslutning från en utlösare för en primär integrationskontot och en åtgärd till en sekundär integrationskontot.

1. Skapa en [logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md) i den sekundära regionen.  

2. Sök på **AS2**, och välj **AS2 - när ett mikrofonvärde skapas**.   

   ![Sök efter AS2](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid1.png)

   En utlösare uppmanas du att upprätta en anslutning till ett integrationskonto. 
   Utlösaren ska anslutas till en primär region integrationskontot. 
   
3. Anger du ett namn på anslutningen, väljer din *primära regionen integrationskontot* i listan och välj **skapa**.

   ![Primär regionnamn på integrationskonto](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid2.png)

4. Den **DateTime för start av MIC värdet synkronisering** inställningen är valfri. Den **frekvens** kan anges till **dag**, **timme**, **minut**, eller **andra** med ett intervall.   

   ![Datum/tid och frekvens](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid3.png)

5. Välj **nytt steg** > **Lägg till en åtgärd**.  

   ![Nytt steg, Lägg till en åtgärd](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid4.png)

6. Sök på **AS2**, och välj **AS2 - Lägg till eller uppdatera MIC-innehåll**.  

   ![MIC-tillägg eller uppdatering](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid5.png)

7. Om du vill ansluta en åtgärd till en sekundär integrationskontot, Välj **ändra anslutning** > **Lägg till ny anslutning** en lista över tillgängliga integrationskonton. Ange ett namn, Välj din *sekundär region integrationskontot* i listan och välj **skapa**.

   ![Sekundär regionnamn på integrationskonto](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid6.png)

8. Växla till råindata genom att klicka på ikonen i övre högra hörnet.

   ![Växla till rå-indata](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2rawinputs.png)

9. Välj brödtext från väljaren för dynamiskt innehåll och spara logikappen.   

   ![Dynamiskt innehåll](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid7.png)

   Baserat på tidsintervallet, utlösaren avsöker den primära region tabellen och hämtar de nya posterna. Åtgärden uppdaterar dem till integrationskontot sekundär region. 
   Om det finns inga uppdateringar, utlösarens status visas som **ignoreras**.  

   ![Primär region tabell](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid8.png)

Baserat på tidsintervallet, replikeras inkrementella Körningsstatus från den primära regionen till den sekundära regionen. Vid ett haveri, när den primära regionen inte är tillgänglig, direkt trafik till den sekundära regionen för affärskontinuitet. 

## <a name="next-steps"></a>Nästa steg

[Övervaka B2B-meddelanden](logic-apps-monitor-b2b-message.md)

