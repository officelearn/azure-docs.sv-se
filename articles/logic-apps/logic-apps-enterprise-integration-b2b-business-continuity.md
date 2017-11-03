---
title: "Haveriberedskap för B2B-integrering konto - Azure Logic Apps | Microsoft Docs"
description: "Logic Apps B2B-katastrofåterställning"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: cf44af18-1fe5-41d5-9e06-cc57a968207c
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/10/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: 4896d9da456bcc17b1a4d92259ef3d57f8575d8b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="logic-apps-b2b-cross-region-disaster-recovery"></a>Logic Apps B2B mellan region-katastrofåterställning

B2B arbetsbelastningar involverar pengar transaktioner som order och fakturor. Under en katastrofåterställning händelse det är viktigt för ett företag att snabbt återställa att uppfylla SLA för affärsnivå överens med sina partner. Den här artikeln visar hur du skapar en kontinuitetsplan för B2B-arbetsbelastningar. 

* Disaster Recovery beredskap 
* Växla över till sekundär region under en katastrofåterställning-händelse 
* Återgå till primär region efter en katastrof-händelse

## <a name="disaster-recovery-readiness"></a>Disaster Recovery beredskap  

1. Ange en sekundär region och skapa en [integrering konto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) i den sekundära regionen.

2. Lägg till partners, scheman och avtal för meddelandet krävs flödena där Körstatus måste replikeras till sekundär region integration konto.

   > [!TIP]
   > Kontrollera att det finns konsekvens i integration konto artefakt namngivningskonvention över regioner. 

3. Om du vill dra Körstatus från den primära regionen, skapa en logikapp i den sekundära regionen. 

   Den här logikapp ska ha en *utlösaren* och en *åtgärd*. 
   Utlösaren ska ansluta till den primära regionen integration konto och åtgärden som ska ansluta till sekundär region integration konto. 
   Baserat på tidsintervallet utlösaren avsöker den primära regionen kör status tabell och hämtar nya poster, om sådana finns. Åtgärden uppdaterar dem till sekundära regionen integration konto. 
   Detta hjälper för att hämta inkrementell Körningsstatus från primära region till sekundär region.

4. Kontinuitet för företag i Logic Apps integration konto är avsedd att stöder baserat på B2B-protokoll - X12 AS2 och EDIFACT. Välj respektive länkar för att hitta detaljerade anvisningar.

5. Det rekommenderas att distribuera alla primära region resurser i en sekundär region för. 

   Primär region resurser inkluderar Azure SQL Database eller Azure Cosmos DB, Azure Service Bus och Azure Event Hubs används för meddelanden, Azure API Management och funktionen Azure Logic Apps i Azure App Service.   

6. Upprätta en anslutning från en primär region till en sekundär region. Skapa en logikapp i en sekundär region om du vill dra Körstatus från en primär region. 

   Logikappen bör ha en utlösare och en åtgärd. 
   Utlösaren ska ansluta till en primär region integration konto. 
   Åtgärden ska ansluta till en sekundär region integration konto. 
   Baserat på tidsintervallet utlösaren avsöker den primära regionen kör status tabell och hämtar nya poster, om sådana finns. 
   Åtgärden uppdaterar dem till ett konto för integrering av sekundär region. 
   Den här processen kan du hämta inkrementell Körningsstatus från den primära regionen till den sekundära regionen.

Kontinuitet för företag i ett konto för Logic Apps-integration stöder baserat på B2B-protokollen X12, AS2 och EDIFACT. Detaljerade anvisningar om hur du använder X12 och AS2 finns [X12](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#x12) och [AS2](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#as2) i den här artikeln.

## <a name="fail-over-to-a-secondary-region-during-a-disaster-event"></a>Växla över till en sekundär region under en katastrofåterställning-händelse

Under en katastrofåterställning händelse, när den primära regionen inte är tillgänglig för affärskontinuitet, trafiken till den sekundära regionen. En sekundär region kan ett företag för att återställa funktioner snabbt för att uppfylla Återställningspunktmål/Återställningstidsmål kommit överens om sina partner. Det minskar också ansträngningar för att växla över från en region till en annan region. 

Det finns en förväntade fördröjning vid kopiering av kontrollen siffror från en primär region till en sekundär region. För att undvika att skicka dubbla genererade kontrollen siffror till partners under en händelse för katastrofåterställning, rekommenderar vi att öka kontrollen talen i avtal som sekundär region med hjälp av [PowerShell-cmdlets](https://blogs.msdn.microsoft.com/david_burgs_blog/2017/03/09/fresh-of-the-press-new-azure-powershell-cmdlets-for-upcoming-x12-connector-disaster-recovery).

## <a name="fall-back-to-a-primary-region-post-disaster-event"></a>Växla över till en primär region efter katastrofåterställning händelse

Om du vill återgå till en primär region när den är tillgänglig, gör du följande:

1. Stoppa ta emot meddelanden från partner i den sekundära regionen.  

2. Öka genererade kontroll-numren för alla primära region avtal med hjälp av [PowerShell-cmdlets](https://blogs.msdn.microsoft.com/david_burgs_blog/2017/03/09/fresh-of-the-press-new-azure-powershell-cmdlets-for-upcoming-x12-connector-disaster-recovery).  

3. Trafiken från den sekundära regionen till den primära regionen.

4. Kontrollera att logikappen som skapats i den sekundära regionen för att ta emot kör status från den primära regionen är aktiverad.

## <a name="x12"></a>X12 

Kontinuitet för företag för EDI X 12 dokument baserat på kontrollen siffror:

> [!TIP]
> Du kan också använda den [X12 snabb start mallen](https://azure.microsoft.com/documentation/templates/201-logic-app-x12-disaster-recovery-replication/) att skapa logikappar. Skapa primära och sekundära integrationskonton är förutsättningar för att använda mallen. Mallen som hjälper dig för att skapa två logikappar, en för mottagna kontrollen tal och en annan för genererade kontrollen tal. Respektive utlösare och åtgärder skapas i logikappar, ansluter utlösaren till primära integration kontot och åtgärder som sekundär integration-konto.

**Förutsättningar**

Om du vill aktivera katastrofåterställning för inkommande meddelanden, väljer du kontrollen av dubblett-inställningar i X12 avtalet tar emot inställningar.

![Välj kontrollen av dubblett-inställningar](./media/logic-apps-enterprise-integration-b2b-business-continuity/dupcheck.png)  

1. Skapa en [logikapp](../logic-apps/logic-apps-create-a-logic-app.md) i en sekundär region.    

2. Sök på **X12**, och välj **X12-när ett kontrollnummer ändras**.   

   ![Sök efter X12](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn1.png)

   Utlösaren uppmanas du att upprätta en anslutning till ett konto för integrering. 
   Utlösaren ska anslutas till en primär region integration konto.

3. Ange ett anslutningsnamn, markera din *primära region integration konto* i listan och välj **skapa**.   

   ![Primär region integration kontonamn](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn2.png)

4. Den **DateTime att starta kontrollen nummer synkronisering** inställningen är valfri. Den **frekvens** kan anges till **dag**, **timme**, **minut**, eller **andra** med ett intervall.   

   ![DateTime och frekvens](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn3.png)

5. Välj **nytt steg** > **lägga till en åtgärd**.

   ![Nytt steg, Lägg till en åtgärd](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn4.png)

6. Sök på **X12**, och välj **X12-Lägg till eller uppdatera kontroll siffror**.   

   ![Lägg till eller uppdatera kontroll siffror](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn5.png)

7. För att ansluta en åtgärd på en sekundär region integration konto, Välj **ändra anslutningen** > **Lägg till ny anslutning** för en lista över tillgängliga integrationskonton. Ange ett anslutningsnamn, markera din *sekundär region integration konto* i listan, och välj **skapa**. 

   ![Sekundär region integration kontonamn](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn6.png)

8. Växla till rå indata genom att klicka på ikonen i övre högra hörnet.

   ![Växla till rå indata](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12rawinputs.png)

9. Välj brödtext väljaren för dynamiskt innehåll och spara logikappen.

   ![Dynamiskt innehåll fält](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn7.png)

   Baserat på tidsintervallet utlösaren avsöker tabellen primära region som togs emot kontroll nummer och hämtar de nya posterna. 
   Åtgärden uppdaterar posterna i kontot för integrering sekundär region. 
   Om det finns inga uppdateringar, utlösare status visas som **ignoreras**.   

   ![Register för kontrollen](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12recevicedcn8.png)

Baserat på tidsintervallet replikerar inkrementell Körningsstatus från en primär region till en sekundär region. Under en katastrofåterställning händelse, när den primära regionen inte är tillgänglig, direkt trafik till den sekundära regionen för verksamhetskontinuitet. 

## <a name="edifact"></a>EDIFACT 

Kontinuitet för företag för EDI EDIFACT-dokument baseras på kontrollen siffror.

**Förutsättningar**

Om du vill aktivera katastrofåterställning för inkommande meddelanden, väljer du dubbla Kontrollera inställningarna i EDIFACT-avtal får inställningarna.

![Välj kontrollen av dubblett-inställningar](./media/logic-apps-enterprise-integration-b2b-business-continuity/edifactdupcheck.png)  

1. Skapa en [logikapp](../logic-apps/logic-apps-create-a-logic-app.md) i en sekundär region.    

2. Sök på **EDIFACT**, och välj **EDIFACT - när ett kontrollnummer ändras**.

   ![Sök efter EDIFACT](./media/logic-apps-enterprise-integration-b2b-business-continuity/edifactcn1.png)

   Utlösaren uppmanas du att upprätta en anslutning till ett konto för integrering. 
   Utlösaren ska anslutas till en primär region integration konto. 

3. Ange ett anslutningsnamn, markera din *primära region integration konto* i listan och välj **skapa**.    

   ![Primär region integration kontonamn](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12CN2.png)

4. Den **DateTime att starta kontrollen nummer synkronisering** inställningen är valfri. Den **frekvens** kan anges till **dag**, **timme**, **minut**, eller **andra** med ett intervall.    

   ![DateTime och frekvens](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn3.png)

6. Välj **nytt steg** > **lägga till en åtgärd**.    

   ![Nytt steg, Lägg till en åtgärd](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn4.png)

7. Sök på **EDIFACT**, och välj **EDIFACT - Lägg till eller uppdatera kontroll siffror**.   

   ![Lägg till eller uppdatera kontroll siffror](./media/logic-apps-enterprise-integration-b2b-business-continuity/EdifactChooseAction.png)

8. För att ansluta en åtgärd på en sekundär region integration konto, Välj **ändra anslutningen** > **Lägg till ny anslutning** för en lista över tillgängliga integrationskonton. Ange ett anslutningsnamn, markera din *sekundär region integration konto* i listan, och välj **skapa**.

   ![Sekundär region integration kontonamn](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn6.png)

9. Växla till rå indata genom att klicka på ikonen i övre högra hörnet.

   ![Växla till rå indata](./media/logic-apps-enterprise-integration-b2b-business-continuity/Edifactrawinputs.png)

10. Välj brödtext väljaren för dynamiskt innehåll och spara logikappen.   

   ![Dynamiskt innehåll fält](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12CN7.png)

   Baserat på tidsintervallet utlösaren avsöker tabellen primära region som togs emot kontroll nummer och hämtar de nya posterna.
   Åtgärden uppdaterar posterna på sekundär region integration kontot. 
   Om det finns inga uppdateringar, utlösare status visas som **ignoreras**.

   ![Register för kontrollen](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12recevicedcn8.png)

Baserat på tidsintervallet replikerar inkrementell Körningsstatus från en primär region till en sekundär region. Under en katastrofåterställning händelse, när den primära regionen inte är tillgänglig, direkt trafik till den sekundära regionen för verksamhetskontinuitet. 

## <a name="as2"></a>AS2 

Kontinuitet för företag för dokument som använder AS2-protokollet baseras på meddelande-ID och MIC-värdet.

> [!TIP]
> Du kan också använda den [AS2 Snabbstart mallen](https://github.com/Azure/azure-quickstart-templates/pull/3302) att skapa logikappar. Skapa primära och sekundära integrationskonton är förutsättningar för att använda mallen. Mallen hjälper dig att skapa en logikapp som har en utlösare och en åtgärd. Logikappen skapar en anslutning från en utlösare på en primär integrering och en åtgärd till en sekundär integration konto.

1. Skapa en [logikapp](../logic-apps/logic-apps-create-a-logic-app.md) i den sekundära regionen.  

2. Sök på **AS2**, och välj **AS2 - när ett MIC värdet skapas**.   

   ![Sök efter AS2](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid1.png)

   En utlösare uppmanas du att upprätta en anslutning till ett konto för integrering. 
   Utlösaren ska anslutas till en primär region integration konto. 
   
3. Ange ett anslutningsnamn, markera din *primära region integration konto* i listan och välj **skapa**.

   ![Primär region integration kontonamn](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid2.png)

4. Den **DateTime att starta MIC värdet synkronisering** inställningen är valfri. Den **frekvens** kan anges till **dag**, **timme**, **minut**, eller **andra** med ett intervall.   

   ![DateTime och frekvens](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid3.png)

5. Välj **nytt steg** > **lägga till en åtgärd**.  

   ![Nytt steg, Lägg till en åtgärd](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid4.png)

6. Sök på **AS2**, och välj **AS2 - Lägg till eller uppdatera MIC innehållet**.  

   ![MIC tillägg eller uppdatering](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid5.png)

7. För att ansluta en åtgärd till en sekundär integration konto, Välj **ändra anslutningen** > **Lägg till ny anslutning** för en lista över tillgängliga integrationskonton. Ange ett anslutningsnamn, markera din *sekundär region integration konto* i listan, och välj **skapa**.

   ![Sekundär region integration kontonamn](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid6.png)

8. Växla till rå indata genom att klicka på ikonen i övre högra hörnet.

   ![Växla till rå indata](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2rawinputs.png)

9. Välj brödtext väljaren för dynamiskt innehåll och spara logikappen.   

   ![Dynamiskt innehåll](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid7.png)

   Baserat på tidsintervallet utlösaren avsöker tabellen primär region och hämtar de nya posterna. Åtgärden uppdaterar dem till sekundära regionen integration konto. 
   Om det finns inga uppdateringar, utlösare status visas som **ignoreras**.  

   ![Primär region tabell](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid8.png)

Baserat på tidsintervallet replikeras inkrementell Körningsstatus från den primära regionen till den sekundära regionen. Under en katastrofåterställning händelse, när den primära regionen inte är tillgänglig, direkt trafik till den sekundära regionen för verksamhetskontinuitet. 

## <a name="next-steps"></a>Nästa steg

[Övervaka B2B-meddelanden](logic-apps-monitor-b2b-message.md)

