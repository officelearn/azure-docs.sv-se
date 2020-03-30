---
title: Skydda dina nätverksresurser i Azure Security Center
description: Det här dokumentet innehåller rekommendationer i Azure Security Center som hjälper dig att skydda dina Azure-nätverksresurser och hålla sig i enlighet med säkerhetsprinciper.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 96c55a02-afd6-478b-9c1f-039528f3dea0
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/05/2019
ms.author: memildin
ms.openlocfilehash: 792b95b120f67afcd360730acbd783a3071388b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77431493"
---
# <a name="protect-your-network-resources"></a>Skydda dina nätverksresurser
Azure Security Center analyserar kontinuerligt säkerhetstillståndet för dina Azure-resurser för bästa praxis för nätverkssäkerhet. När Security Center identifierar potentiella säkerhetsproblem skapas rekommendationer som hjälper dig att konfigurera de kontroller som behövs för att härda och skydda dina resurser.

I den här artikeln **beskrivs nätverkssidan** i avsnittet resurssäkerhet i Security Center.

En fullständig lista över rekommendationerna för nätverk finns i [Nätverksrekommendationer](recommendations-reference.md#recs-network).

Den här artikeln innehåller rekommendationer som gäller för dina Azure-resurser från ett nätverkssäkerhetsperspektiv. Nätverksrekommendationerna kretsar kring nästa generations brandväggar, nätverkssäkerhetsgrupper, åtkomst till JIT-virtuella datorer, alltför tillåtande regler för inkommande trafik med mera. En lista över nätverksrekommendationer och reparationsåtgärder finns [i Hantera säkerhetsrekommendationer i Azure Security Center](security-center-recommendations.md).

> [!NOTE]
> På **nätverkssidan** kan du djupdyka din Azure-resurshälsa ur ett nätverksperspektiv. Nätverkskartan och adaptiva nätverkskontroller är endast tillgängliga för standardnivån i Azure Security Center. [Om du använder den kostnadsfria nivån kan du klicka på knappen för att **visa äldre nätverk** och få rekommendationer för nätverksresurser](#legacy-networking).
>

Sidan **Nätverk** innehåller en översikt över de avsnitt du kan fördjupa dig i för att få mer information om nätverkets hälsotillstånd:

- Nätverkskarta (endast Azure Security Center Standard-nivå)
- Anpassningsbar nätverkshärdning
- Säkerhetsrekommendationer för nätverk.
- Äldre **nätverksblad** (föregående nätverksblad) 
 
[![Fönstret Nätverk](./media/security-center-network-recommendations/networking-pane.png)](./media/security-center-network-recommendations/networking-pane.png#lightbox)

## <a name="network-map"></a>Nätverkskarta
Den interaktiva nätverkskartan ger en grafisk vy med säkerhetsöverlägg som ger dig rekommendationer och insikter för att härda dina nätverksresurser. Med hjälp av kartan kan du se nätverkstopologin för dina Azure-arbetsbelastningar, anslutningar mellan dina virtuella datorer och undernät och möjligheten att öka detaljnivån från kartan till specifika resurser och rekommendationerna för dessa resurser.

Så här öppnar du nätverkskartan:

1. Välj **Nätverk**under Resurssäkerhetshygien i Security Center.
2. Klicka på **Visa topologi**under **Nätverkskarta** .
 
Standardvyn för topologikartan visar:

- Prenumerationer som du har valt i Azure. Kartan stöder flera prenumerationer.
- Virtuella datorer, undernät och virtuella nätverk av resurstypen Resource Manager (Klassiska Azure-resurser stöds inte)
- Peered virtuella nätverk
- Endast resurser som har [nätverksrekommendationer](security-center-recommendations.md) med hög eller medelhög allvarlighetsgrad  
- Internet inför resurser
- Kartan är optimerad för de prenumerationer du valde i Azure. Om du ändrar ditt val beräknas kartan om och optimeras om baserat på dina nya inställningar.  

[![Karta över nätverkstopologi](./media/security-center-network-recommendations/network-map-info.png)](./media/security-center-network-recommendations/network-map-info.png#lightbox)

## <a name="understanding-the-network-map"></a>Förstå nätverkskartan

Nätverkskartan kan visa dina Azure-resurser i en **topologivy** och en **trafikvy.** 

### <a name="the-topology-view"></a>Topologivyn

I **topologivyn** för nätverkskartan kan du visa följande insikter om dina nätverksresurser:

- I den inre cirkeln kan du se alla virtuella nätverk i dina valda prenumerationer, nästa cirkel är alla undernät, den yttre cirkeln är alla virtuella datorer.
- De linjer som ansluter resurserna i kartan låter dig veta vilka resurser som är associerade med varandra och hur ditt Azure-nätverk är strukturerat. 
- Använd allvarlighetsgradindikatorerna för att snabbt få en översikt över vilka resurser som har öppna rekommendationer från Security Center.
- Du kan klicka på någon av resurserna för att öka detaljnivån i dem och visa information om resursen och dess rekommendationer direkt och i samband med nätverkskartan.  
- Om det finns för många resurser som visas på kartan använder Azure Security Center sin egen algoritm för att smarta kluster dina resurser, belyser de resurser som är i det mest kritiska tillståndet och har de mest hög allvarlighetsgradiga rekommendationerna. 

Eftersom kartan är interaktiv och dynamisk är varje nod klickbar och vyn kan ändras baserat på filtren:

1. Du kan ändra vad du ser på nätverkskartan med hjälp av filtren högst upp. Du kan fokusera kartan baserat på:

   -  **Säkerhetshälsa**: Du kan filtrera kartan baserat på allvarlighetsgrad (hög, medel, låg) för dina Azure-resurser.
   - **Rekommendationer**: Du kan välja vilka resurser som ska visas baserat på vilka rekommendationer som är aktiva på dessa resurser. Du kan till exempel bara visa resurser som Security Center rekommenderar att du aktiverar nätverkssäkerhetsgrupper för.
   - **Nätverkszoner**: Som standard visar kartan bara Internet-vänteresurser, du kan välja interna virtuella datorer också.
 
2. Du kan när som helst klicka på **Återställ** i det övre vänstra hörnet för att återställa kartan till standardläget.

Så här detaljgransar du till en resurs:

1. När du väljer en viss resurs på kartan öppnas den högra rutan och ger dig allmän information om resursen, anslutna säkerhetslösningar om det finns några och de rekommendationer som är relevanta för resursen. Det är samma typ av beteende för varje typ av resurs som du väljer. 
2. När du hovrar över en nod i kartan kan du visa allmän information om resursen, inklusive prenumeration, resurstyp och resursgrupp.
3. Använd länken för att zooma in i verktygsspetsen och fokusera kartan på den specifika noden. 
4. Zooma ut om du vill fokusera kartan på en viss nod.

### <a name="the-traffic-view"></a>Vyn Trafik

**I trafikvyn** får du en karta över all möjlig trafik mellan dina resurser. Detta ger dig en visuell karta över alla regler som du har konfigurerat som definierar vilka resurser som kan kommunicera med vem. På så sätt kan du se den befintliga konfigurationen av nätverkssäkerhetsgrupperna samt snabbt identifiera möjliga riskfyllda konfigurationer i dina arbetsbelastningar.

### <a name="uncover-unwanted-connections"></a>Upptäck oönskade anslutningar

Styrkan i den här vyn är i sin förmåga att visa dig dessa tillåtna anslutningar tillsammans med de sårbarheter som finns, så att du kan använda det här tvärsnittet av data för att utföra den nödvändiga härdningen på dina resurser. 

Du kan till exempel identifiera två datorer som du inte kände till kan kommunicera, så att du bättre kan isolera arbetsbelastningar och undernät.

### <a name="investigate-resources"></a>Undersöka resurser

Så här detaljgransar du till en resurs:

1. När du väljer en viss resurs på kartan öppnas den högra rutan och ger dig allmän information om resursen, anslutna säkerhetslösningar om det finns några och de rekommendationer som är relevanta för resursen. Det är samma typ av beteende för varje typ av resurs som du väljer. 
2. Klicka på **Trafik** om du vill visa listan över eventuell utgående och inkommande trafik på resursen – det här är en omfattande lista över vem som kan kommunicera med resursen och vem den kan kommunicera med och genom vilka protokoll och portar. När du till exempel väljer en virtuell dator visas alla virtuella datorer som den kan kommunicera med, och när du väljer ett undernät visas alla undernät som det kan kommunicera med.

**Dessa data baseras på analys av nätverkssäkerhetsgrupper samt avancerade maskininlärningsalgoritmer som analyserar flera regler för att förstå deras crossovers och interaktioner.** 

[![Trafikkarta för nätverk](./media/security-center-network-recommendations/network-map-traffic.png)](./media/security-center-network-recommendations/network-map-traffic.png#lightbox)


## <a name="legacy-networking"></a>Äldre nätverk<a name ="legacy-networking"></a>

Om du inte har Security Center Standard-nivå förklarar det här avsnittet hur du visar kostnadsfria nätverksrekommendationer.

Om du vill komma åt den här informationen klickar du på **Visa äldre nätverk**i bladet Nätverk. 

[![Äldre nätverk](./media/security-center-network-recommendations/legacy-networking.png)](./media/security-center-network-recommendations/legacy-networking.png#lightbox)

### <a name="internet-facing-endpoints-section"></a>Delen med internetuppkopplade slutpunkter
I avsnittet **Internet-vändande slutpunkter** kan du se de virtuella datorer som för närvarande är konfigurerade med en Internet-vänd slutpunkt och dess status.

Den här tabellen har slutpunktsnamnet, internetvänd IP-adressen och den aktuella allvarlighetsgraden för nätverkssäkerhetsgruppen och NGFW-rekommendationerna. Tabellen sorteras efter allvarlighetsgrad.

### <a name="networking-topology-section"></a>Delen med nätverkstopologi
Avsnittet **Nätverkstopologi** har en hierarkisk vy över resurserna.

Den här tabellen sorteras (virtuella datorer och undernät) efter allvarlighetsgrad.

I den här topologivyn visar den första nivån virtuella nätverk. Den andra visar undernät och den tredje nivån visar de virtuella datorer som tillhör dessa undernät. I den högra kolumnen visas den aktuella statusen för nätverkssäkerhetsgruppens rekommendationer för dessa resurser.

Den tredje nivån visar virtuella datorer, vilket liknar vad som beskrivs tidigare. Du kan klicka på valfri resurs om du vill veta mer eller använda den nödvändiga säkerhetskontrollen eller konfigurationen.

## <a name="next-steps"></a>Nästa steg

Mer information om rekommendationer som gäller för andra Azure-resurstyper finns i följande:

* [Skydda datorer och program i Azure Security Center](security-center-virtual-machine-protection.md)
* [Skydda din Azure SQL-tjänst i Azure Security Center](security-center-sql-service-recommendations.md)