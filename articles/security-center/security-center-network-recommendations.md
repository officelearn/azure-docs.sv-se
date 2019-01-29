---
title: Skydda dina nätverksresurser i Azure Security Center | Microsoft Docs
description: Det här dokumentet behandlar rekommendationer i Azure Security Center som hjälper dig att skydda din Azure-nätverksresurser och uppfyller säkerhetsprinciper.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 96c55a02-afd6-478b-9c1f-039528f3dea0
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: c44e000a60e4391faf38cb275f35ec512e19aea3
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2019
ms.locfileid: "55101693"
---
# <a name="protect-your-network-resources-in-azure-security-center"></a>Skydda dina nätverksresurser i Azure Security Center
Azure Security Center analyserar kontinuerligt säkerhetsläget hos dina Azure-resurser för Metodtips för nätverkssäkerhet. När Security Center identifierar potentiella säkerhetsproblem skapas rekommendationer som guidar dig genom processen med att konfigurera de kontrollfunktioner som behövs för att förstärka och skydda dina resurser.

Den här artikeln belyser rekommendationer som gäller för dina Azure-resurser från ett säkerhetsperspektiv i nätverket. Nätverk rekommendationer center runt nästa generation-brandväggar, Nätverkssäkerhetsgrupper, JIT VM åtkomstregler alltför Tillåtande inkommande trafik och mer. En lista över nätverk rekommendationer och åtgärder som finns i [hantera säkerhetsrekommendationer i Azure Security Center](security-center-recommendations.md).

> [!NOTE]
> Den **nätverk** sidan kan du djupdykning i dina Azure resource health från ett nätverksperspektiv. Nätverkskarta och anpassningsbar nätverkskontroller är tillgängliga för Azure Security Center standard-nivån endast. [Om du använder den kostnadsfria nivån kan du klicka på knappen för att **visa äldre nätverk** och ta emot resource nätverksrekommendationer](#legacy-networking).
>

Den **nätverk** sidan innehåller en översikt över de avsnitt som du har djupgående fördjupa dig i, för att få mer information om hälsotillståndet för nätverksresurserna:

- Nätverkskarta (endast Azure Security Center Standard-nivån)
- NSG härdning (kommer snart. Registrera dig för förhandsversionen)
- Säkerhetsrekommendationer för nätverk.
- Äldre **nätverk** bladet (tidigare nätverk bladet) 
 
![Fönstret för nätverk](./media/security-center-network-recommendations/networking-pane.png)

## <a name="network-map"></a>Nätverkskarta
Interaktiva kartan ger en grafisk vy security överlägg, vilket ger dig rekommendationer och insikter för att härda nätverksresurserna. Du kan se nätverkstopologin för dina Azure-arbetsbelastningar, anslutningar mellan dina virtuella datorer och undernät och möjligheten att granska nedåt från kartan i specifika resurser och rekommendationer för dessa resurser med hjälp av kartan.

Öppna kartan:

1. I Security Center under Resource Security hygien väljer **nätverk**.
2. Under **Nätverkskarta** klickar du på **finns i topologin**.
 
Standardvyn för topologisk karta visas:
- Prenumerationer som du har valt i Azure. Kartan stöder flera prenumerationer.
- Virtuella datorer, undernät och virtuella nätverk för Resource Manager-resurstyp (klassiska Azure-resurser inte stöds)
- Endast de resurser som har [network rekommendationer](security-center-recommendations.md) med en hög eller medelhög allvarlighetsgrad  
- Internet-riktade resurser
- Kartan är optimerat för de prenumerationer som du har valt i Azure. Om du ändrar valet av kartan är beräknas om och optimerad igen baserat på de nya inställningarna.  

![Nätverk topologisk karta](./media/security-center-network-recommendations/network-map-info.png)

## <a name="understanding-the-network-map"></a>Förstå kartan

Kartan kan du visa dina Azure-resurser i en **topologi** vy och en **trafik** vy.

### <a name="the-topology-view"></a>Topologiska vyn

I den **topologi** visa kartans nätverk kan du visa följande insikter om dina nätverksresurser:
- Du kan se de virtuella nätverken i de valda prenumerationerna i den inre cirkeln, nästa cirkeln är alla undernät, den yttersta cirkeln är alla virtuella datorer.
- Raderna ansluta resurser på kartan att du vet vilka resurser som är kopplade till varandra och hur din Azure-nätverk är strukturerad. 
- Använd allvarlighetsgrad-indikatorer för att snabbt få en översikt av vilka resurser behöver öppna rekommendationer från Security Center.
- Du kan klicka på någon av resurser för att granska nedåt i dem och visa information om den här resursen och dess rekommendationer direkt och i samband med nätverket mappa.  
- Om det finns för många resurser som visas på kartan, Azure Security Center använder dess upphovsrättsskyddade algoritm att smart kluster är dina resurser, om du markerar de resurser som finns i det mest kritiska tillståndet och har mest Hög allvarlighetsgrad rekommendationer. 

Eftersom kartan är interaktiva och dynamiskt, varje nod är klickbara och vyn kan ändra baserat på filter:

1. Du kan ändra det som visas på kartan genom att använda filtren högst upp. Du kan fokusera på kartan baserat på:
   -  **Säkerhetshälsa**: Du kan filtrera kartan baserat på allvarlighetsgrad (hög, medel, låg) för dina Azure-resurser.
   - **Rekommendationer**: Du kan välja vilka resurser visas baserat på vilka rekommendationerna är aktiv på de här resurserna. Du kan till exempel visa endast resurser som Security Center rekommenderar att du aktiverar Nätverkssäkerhetsgrupper.
   - **Network zoner**: Som standard kartan visar endast Internet-riktade resurser kan du välja interna virtuella datorer samt.
 
2. Du kan klicka på **återställa** i övre vänstra hörnet när som helst för att returnera kartan till sitt ursprungsläge.

Att granska nedåt i en resurs:
1. När du väljer en specifik resurs på kartan, den högra rutan öppnas och du får allmän information om resursen, anslutna säkerhetslösningar om det finns några, och rekommendationer till resursen. Det är samma typ av beteendet för varje typ av resurs som du väljer. 
2. När du hovrar över en nod i kartan kan du visa allmän information om resursen, inklusive prenumeration, resurstyp och resursgrupp.
3. Använd länken för att zooma in beskrivningen och fokusera på kartan i den specifika noden. 
4. Zooma ut för att rikta karta från en viss nod.

### <a name="the-traffic-view"></a>Traffic view

Den **trafik** vyn ger dig en karta över alla möjliga trafik mellan dina resurser. Detta ger du med en visual karta över alla regler som du har konfigurerat som definierar vilka resurser kan kommunicera med vem. På så sätt kan du se den befintliga konfigurationen för nätverkssäkerhetsgrupperna samt att snabbt identifiera möjliga riskabla inställningar i dina arbetsbelastningar.

### <a name="uncover-unwanted-connections"></a>Få oönskade anslutningar

Styrkan hos den här vyn är att visa dessa tillåtna anslutningar tillsammans med de säkerhetsrisker som finns, så du kan använda den här rad med data för att utföra nödvändiga härdning på resurserna i en tabell. 

Exempelvis kan kan du identifiera två datorer som du tänkte kan kommunicera hjälper dig att bättre isolera arbetsbelastningar och undernät.

### <a name="investigate-resources"></a>Undersöka resurser

Att granska nedåt i en resurs:
1. När du väljer en specifik resurs på kartan, den högra rutan öppnas och du får allmän information om resursen, anslutna säkerhetslösningar om det finns några, och rekommendationer till resursen. Det är samma typ av beteendet för varje typ av resurs som du väljer. 
2. Klicka på **trafik** för att se en lista över möjliga utgående och inkommande trafik på resursen – det här är en omfattande lista över vem som kan kommunicera med resursen och som den kan kommunicera med och genom vilka protokoll och portar.

**Dessa data baserat på analyser av Nätverkssäkerhetsgrupper samt avancerade machine learning-algoritmer som analyserar flera regler för att förstå deras crossovers och interaktioner.** 

![Nätverk trafik karta](./media/security-center-network-recommendations/network-map-traffic.png)

## Äldre nätverk <a name ="legacy-networking"></a>

Om du inte har Security Center Standard-nivån kan beskrivs det här avsnittet hur du visar ledigt nätverksrekommendationer.

För att komma åt den här informationen i bladet nätverk klickar du på **visa äldre nätverk**. 

![Äldre nätverk](./media/security-center-network-recommendations/legacy-networking.png)

### <a name="internet-facing-endpoints-section"></a>Delen med internetuppkopplade slutpunkter
I den **Internetuppkopplade slutpunkter** du hittar de virtuella datorer som är konfigurerade med en Internetuppkopplad slutpunkt och status.

Den här tabellen har namnet på slutpunkten, den internetuppkopplade IP-adressen och aktuell allvarlighetsgrad för nätverkssäkerhetsgruppen och NGFW-rekommendationer. Tabellen är sorterad efter allvarlighetsgrad.

### <a name="networking-topology-section"></a>Delen med nätverkstopologi
Den **nätverkstopologi** -avsnittet innehåller en hierarkisk vy över resurserna.

Den här tabellen är sorterad (virtuella datorer och undernät) efter allvarlighetsgrad.

I den här topologiska vyn visar virtuella nätverk i den första nivån. De andra visar hittar du undernät och den tredje nivån visar de virtuella datorer som finns på dessa undernät. I den högra kolumnen visas aktuell status för nätverk grupp säkerhetsrekommendationerna för dessa resurser.

Den tredje nivån visar virtuella datorer, som liknar vad som beskrivs ovan. Du kan klicka på en resurs att mer eller lägga till nödvändiga säkerhetskontroll eller konfiguration.

## <a name="network-recommendations"></a>Nätverksrekommendationer

|Resurstyp|Säkerhetspoäng|Rekommendation|Beskrivning|
|----|----|----|----|
|Dator|40|Aktivera nätverkssäkerhetsgrupper på virtuella datorer|Aktivera Nätverkssäkerhetsgrupper att styra nätverksåtkomst för dina virtuella datorer.|
|Undernät|35|Aktivera nätverkssäkerhetsgrupper på undernät |Aktivera nätverkssäkerhetsgrupper att styra nätverksåtkomsten för resurser som har distribuerats i dina undernät.|
|Dator|30|Tillämpa en just-in-time-nätverksåtkomstkontroll|Använd just-in-time åtkomstkontroll för virtuell dator kan låsa permanent åtkomst till valda portar och aktivera behöriga användare att öppna dem via samma metod och under en begränsad tid.|
|Dator|20|Begränsa åtkomst via slutpunkt mot Internet|Förstärka nätverkssäkerhetsgrupper på virtuella datorer som riktas mot genom att begränsa åtkomst till din befintliga Internet-Tillåt regler.|
|Dator|10|Lägg till en nästa generations brandvägg|Lägg till en nästa Generation Firewall (NGFW)-lösning för att bättre skydda virtuella datorer som riktas mot internet.|
|Dator|5|Dirigera trafik via nätverkets gateway brandvägg|Trafik till skyddade virtuella datorer som riktas mot internet ska dirigeras endast via nästa generations brandväggslösning för att slutföra distributionen av din nästa generations brandvägg.|
|VNet|5|Aktivera DDoS Protection Standard|Program med offentliga IP-adresser i dessa virtuella nätverk skyddas inte med DDOS protection-tjänsten standard. Det är bäst att aktivera den för att aktivera minskning i nätverket som överför och protocol-attacker.|
|Dator|10|Lägg till en nästa generations brandvägg|dd en nästa Generation Firewall (NGFW)-lösning för att bättre skydda virtuella datorer som riktas mot Internet.|
|Dator|5|Dirigera trafik via nätverkets gateway brandvägg|Trafik till skyddade virtuella datorer som riktas mot Internet ska dirigeras endast via nästa generations brandväggslösning för att slutföra distributionen av din nästa generations brandvägg.|
Virtuellt nätverk|5|Aktivera DDoS Protection Standard|Program med offentliga IP-adresser i dessa virtuella nätverk skyddas inte med DDOS protection-tjänsten standard. Det är bäst att aktivera den för att aktivera minskning i nätverket som överför och protocol-attacker.|
## <a name="see-also"></a>Se också
Om du vill veta mer om rekommendationer som gäller för andra Azure-resurstyper finns i:

* [Skydda virtuella datorer i Azure Security Center](security-center-virtual-machine-recommendations.md)
* [Skydda program i Azure Security Center](security-center-application-recommendations.md)
* [Skydda din Azure SQL-tjänst i Azure Security Center](security-center-sql-service-recommendations.md)

I följande avsnitt kan du lära dig mer om Security Center:

* [Ange säkerhetsprinciper i Azure Security Center](tutorial-security-policy.md) – Här får du lära dig hur du ställer in säkerhetsprinciper för prenumerationer och resursgrupper i Azure.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten.
