---
title: Skydda nätverks resurserna i Azure Security Center | Microsoft Docs
description: Det här dokumentet beskriver rekommendationer i Azure Security Center som hjälper dig att skydda dina Azure-nätverks resurser och hålla dig informerad om säkerhets principer.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 96c55a02-afd6-478b-9c1f-039528f3dea0
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/05/2019
ms.author: v-mohabe
ms.openlocfilehash: 6dfd3bd86e55d19bf1c15486a502a4cbab3c96a8
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2019
ms.locfileid: "69905306"
---
# <a name="protect-your-network-resources-in-azure-security-center"></a>Skydda nätverks resurserna i Azure Security Center
Azure Security Center fort löp ande analyserar säkerhets läget för dina Azure-resurser för metod tips för nätverks säkerhet. När Security Center identifierar potentiella säkerhets risker skapar den rekommendationer som vägleder dig genom processen att konfigurera de nödvändiga kontrollerna för att skärp och skydda dina resurser.

Den här artikeln beskriver rekommendationer som gäller för dina Azure-resurser från ett nätverks säkerhets perspektiv. Nätverks rekommendationer Center kring nästa generations brand väggar, nätverks säkerhets grupper, JIT VM-åtkomst för att tillåta inkommande trafik regler med mera. En lista över nätverks rekommendationer och reparations åtgärder finns [i hantera säkerhets rekommendationer i Azure Security Center](security-center-recommendations.md).

> [!NOTE]
> På sidan **nätverk** kan du ta en djupare insikt i din Azure-resurs hälsa från ett nätverks perspektiv. Kontrollerna nätverks karta och adaptiva nätverk är bara tillgängliga för den Azure Security Center standard nivån. [Om du använder den kostnads fria nivån kan du klicka på knappen för att **Visa tidigare nätverk** och få rekommendationer för nätverks resurser](#legacy-networking).
>

Sidan **nätverk** ger en översikt över de avsnitt som du kan ta del av för att få mer information om hälso tillståndet för dina nätverks resurser:

- Nätverks karta (endast Azure Security Center standard nivå)
- Adaptiv nätverkshärdning
- Rekommendationer för nätverks säkerhet.
- Bladet äldre **nätverk** (det tidigare nätverks bladet) 
 
![Fönstret nätverk](./media/security-center-network-recommendations/networking-pane.png)

## <a name="network-map"></a>Nätverks karta
Den interaktiva nätverks kartan är en grafisk vy med säkerhets överlägg som ger dig rekommendationer och insikter för att härdning av dina nätverks resurser. Med hjälp av kartan kan du se nätverk sto pol Ogin för dina Azure-arbetsbelastningar, anslutningar mellan dina virtuella datorer och undernät och möjligheten att öka detalj nivån från kartan till vissa resurser och rekommendationerna för dessa resurser.

Så här öppnar du nätverks kartan:

1. I Security Center, under resurs säkerhets hygien, väljer du **nätverk**.
2. Under **nätverks karta** klickar du på **Se topologi**.
 
Standard visningen av Topology-kartan visar:

- Prenumerationer som du har valt i Azure. Kartan stöder flera prenumerationer.
- Virtuella datorer, undernät och virtuella nätverk för resurs typen Resource Manager (klassiska Azure-resurser stöds inte)
- Peer-virtuella nätverk
- Endast resurser som har [nätverks rekommendationer](security-center-recommendations.md) med hög eller medelhög allvarlighets grad  
- Resurser som riktas mot Internet
- Kartan är optimerad för de prenumerationer som du har valt i Azure. Om du ändrar ditt val, beräknas kartan om och optimeras på nytt baserat på de nya inställningarna.  

![Karta över nätverkstopologi](./media/security-center-network-recommendations/network-map-info.png)

## <a name="understanding-the-network-map"></a>Förstå nätverks kartan

Nätverks kartan kan visa dina Azure-resurser i en **Topology** -vy och i vyn **trafik** .

### <a name="the-topology-view"></a>Vyn topologi

I vyn **topologi** i nätverks kartan kan du visa följande insikter om dina nätverks resurser:

- I den inre cirkeln kan du se alla virtuella nätverk i dina valda prenumerationer, nästa cirkel är alla undernät, den yttre cirkeln är alla virtuella datorer.
- De linjer som ansluter resurserna i kartan kan du se vilka resurser som är associerade med varandra och hur Azure-nätverket är strukturerat. 
- Använd allvarlighets gradernas indikatorer för att snabbt få en översikt över vilka resurser som har öppna rekommendationer från Security Center.
- Du kan klicka på någon av resurserna för att öka detalj nivån och se information om den här resursen och dess rekommendationer direkt, och i kontexten för nätverks kartan.  
- Om det finns för många resurser som visas på kartan, Azure Security Center använda sin egen algoritm för att få ett smart kluster för dina resurser, markera de resurser som är i det mest kritiska läget och ha de mest höga rekommendationerna för allvarlighets grad. 

Eftersom kartan är interaktiv och dynamisk går det att klicka på varje nod och vyn kan ändras baserat på filtren:

1. Du kan ändra vad som visas på nätverks kartan genom att använda filtren överst. Du kan fokusera kartan baserat på:

   -  **Säkerhets hälsa**: Du kan filtrera kartan baserat på allvarlighets grad (hög, medel, låg) för dina Azure-resurser.
   - **Rekommendationer**: Du kan välja vilka resurser som ska visas baserat på vilka rekommendationer som är aktiva för dessa resurser. Du kan till exempel bara visa resurser för vilka Security Center rekommenderar att du aktiverar nätverks säkerhets grupper.
   - **Nätverks zoner**: Som standard visar kartan endast Internet-riktade resurser. du kan även välja interna virtuella datorer.
 
2. Du kan när som helst klicka på **Återställ** i det övre vänstra hörnet för att återställa kartan till dess standard tillstånd.

För att öka detalj nivån till en resurs:

1. När du väljer en resurs på kartan öppnas den högra rutan och du får allmän information om resursen, anslutna säkerhetslösningar om det finns några, och de rekommendationer som är relevanta för resursen. Det är samma typ av beteende för varje typ av resurs som du väljer. 
2. När du hovrar över en nod i kartan kan du Visa allmän information om resursen, inklusive prenumeration, resurs typ och resurs grupp.
3. Använd länken för att zooma in i verktygs tipset och fokusera på kartan på den noden. 
4. Om du vill fokusera på kartan bort från en speciell nod zoomar du ut.

### <a name="the-traffic-view"></a>Vyn trafik

I vyn **trafik** visas en karta över all möjlig trafik mellan dina resurser. Detta ger dig en visuell karta över alla regler som du har konfigurerat som definierar vilka resurser som kan kommunicera med vem. På så sätt kan du se den befintliga konfigurationen av nätverks säkerhets grupperna samt snabbt identifiera möjliga riskfyllda konfigurationer i dina arbets belastningar.

### <a name="uncover-unwanted-connections"></a>Återställ oönskade anslutningar

Styrkan i den här vyn är att du kan visa de här tillåtna anslutningarna tillsammans med de säkerhets risker som finns, så att du kan använda det här data området för att utföra de åtgärder som krävs för dina resurser. 

Du kan till exempel identifiera två datorer som du inte kände till kan kommunicera, så att du bättre kan isolera arbets belastningarna och under näten.

### <a name="investigate-resources"></a>Undersök resurser

För att öka detalj nivån till en resurs:

1. När du väljer en resurs på kartan öppnas den högra rutan och du får allmän information om resursen, anslutna säkerhetslösningar om det finns några, och de rekommendationer som är relevanta för resursen. Det är samma typ av beteende för varje typ av resurs som du väljer. 
2. Klicka på **trafik** om du vill se en lista över möjliga utgående och inkommande trafik i resursen – det här är en omfattande lista över vem som kan kommunicera med resursen och vem den kan kommunicera med, och via vilka protokoll och portar. Om du till exempel väljer en virtuell dator visas alla de virtuella datorer som den kan kommunicera med, och när du väljer ett undernät visas alla undernät som det kan kommunicera med.

**Dessa data baseras på analyser av nätverks säkerhets grupper samt avancerade algoritmer för maskin inlärning som analyserar flera regler för att förstå deras korsning och interaktioner.** 

![Nätverks trafik karta](./media/security-center-network-recommendations/network-map-traffic.png)

## Äldre nätverk<a name ="legacy-networking"></a>

Om du inte har Security Center standard nivån förklarar det här avsnittet hur du kan visa de kostnads fria nätverks rekommendationerna.

Du kommer åt den här informationen genom att klicka på **Visa äldre nätverk**på bladet nätverk. 

![Äldre nätverk](./media/security-center-network-recommendations/legacy-networking.png)

### <a name="internet-facing-endpoints-section"></a>Delen med internetuppkopplade slutpunkter
I avsnittet **Internet-riktade slut punkter** kan du se de virtuella datorer som för närvarande är konfigurerade med en slut punkt mot Internet och dess status.

Den här tabellen har slut punkts namnet, IP-adressen som riktas mot Internet och den aktuella allvarlighets graden för nätverks säkerhets gruppen och NGFW-rekommendationerna. Tabellen sorteras efter allvarlighets grad.

### <a name="networking-topology-section"></a>Delen med nätverkstopologi
Avsnittet **nätverk sto pol Ogin** innehåller en hierarkisk vy över resurserna.

Den här tabellen är sorterad (virtuella datorer och undernät) efter allvarlighets grad.

I den här Topology-vyn visar den första nivån virtuella nätverk. Den andra visas har undernät och den tredje nivån visar de virtuella datorer som tillhör dessa undernät. I den högra kolumnen visas den aktuella statusen för nätverks säkerhets gruppens rekommendationer för dessa resurser.

På den tredje nivån visas virtuella datorer, vilket liknar det som beskrivs ovan. Du kan klicka på en resurs för att lära dig mer eller använda den säkerhets kontroll eller konfiguration som krävs.

## <a name="network-recommendations"></a>Nätverksrekommendationer

|Rekommendations namn|Beskrivning|severity|Säkerhetspoäng|Resurstyp|
|----|----|----|----|----|----|
|Nätverks säkerhets grupper på under näts nivån måste vara aktiverade|Aktivera nätverks säkerhets grupper för att kontrol lera nätverks åtkomst till resurser som har distribuerats i dina undernät.|Hög/medel|30|Subnet|
|Virtuella datorer ska associeras med en nätverks säkerhets grupp|Aktivera nätverks säkerhets grupper för att kontrol lera nätverks åtkomst för dina virtuella datorer.|Hög/medel|30|Virtuell dator|
|Åtkomsten ska begränsas för tillåtade nätverks säkerhets grupper med virtuella datorer som riktas mot Internet|Skärp nätverks säkerhets grupperna för dina Internet-riktade virtuella datorer genom att begränsa åtkomsten till dina befintliga Tillåt-regler.|Hög|20|Virtuell dator|
|Reglerna för webb program på IaaS NSG: er bör vara härdade|Skärp nätverks säkerhets gruppen (NSG) för dina virtuella datorer som kör webb program, med NSG-regler som kan tillåtas med avseende på webb program portar.|Hög|20|Virtuell dator|
|Åtkomst till App Services bör vara begränsad|Begränsa åtkomsten till din App Services genom att ändra nätverks konfigurationen för att neka inkommande trafik från intervall som är för breda.|Hög|10|Apptjänst|
|Hanterings portar bör stängas på dina virtuella datorer|Skärp nätverks säkerhets gruppen för dina virtuella datorer för att begränsa åtkomsten till hanterings portar.|Hög|10|Virtuell dator|
DDoS Protection standard ska vara aktive rad|Skydda virtuella nätverk som innehåller program med offentliga IP-adresser genom att aktivera DDoS Protection Service standard. DDoS Protection möjliggör minskning av nätverks-och protokoll attacker.|Hög|10|Virtuellt nätverk|
|IP-vidarebefordran på den virtuella datorn bör inaktive ras|Inaktivera IP-vidarebefordring. När IP-vidarebefordring är aktiverat på en virtuell dators nätverkskort kan datorn ta emot trafik som är adresserad till andra mål. IP-vidarebefordran krävs sällan (t. ex. när den virtuella datorn används som en virtuell nätverks installation) och bör därför granskas av nätverks säkerhets teamet.|Medel|10|Virtuell dator|
|Webbprogram bör enbart vara åtkomliga via HTTPS|Aktivera "endast HTTPS"-åtkomst för webb program. Användning av HTTPS garanterar serverautentisering och skyddar data i överföring från angrepp på nätverks nivå.|Medel|20|Webbprogram|
|Just-in-Time-kontroll för nätverks åtkomst ska tillämpas på virtuella datorer|Använd just-in-Time (JIT)-åtkomst kontroll för att permanent låsa åtkomsten till valda portar och aktivera behöriga användare för att öppna dem via JIT, under en begränsad tid.|Hög|20|Virtuell dator|
|Function-appar bör endast vara tillgängliga via HTTPS|Aktivera "endast HTTPS"-åtkomst för Function Apps. Användning av HTTPS garanterar serverautentisering och skyddar data i överföring från angrepp på nätverks nivå.|Medel|20|Funktionsapp|
|Säker överföring till lagrings konton ska vara aktiverat|Aktivera säker överföring till lagrings konton. Säker överföring är ett alternativ som tvingar ditt lagrings konto att endast godkänna begär Anden från säkra anslutningar (HTTPS). Användning av HTTPS garanterar autentisering mellan servern och tjänsten och skyddar data i överföring från angrepp på nätverks nivå, till exempel man-in-the-Middle, avlyssning och session-kapning.|Hög|20|Lagringskonto|

## <a name="see-also"></a>Se också
Om du vill veta mer om rekommendationer som gäller för andra Azure-resurstyper finns i:

* [Skydda datorer och program i Azure Security Center](security-center-virtual-machine-protection.md)
* [Skydda program i Azure Security Center](security-center-application-recommendations.md)
* [Skydda din Azure SQL-tjänst i Azure Security Center](security-center-sql-service-recommendations.md)

I följande avsnitt kan du lära dig mer om Security Center:

* [Ange säkerhetsprinciper i Azure Security Center](tutorial-security-policy.md) – Här får du lära dig hur du ställer in säkerhetsprinciper för prenumerationer och resursgrupper i Azure.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten.
