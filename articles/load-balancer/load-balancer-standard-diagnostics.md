---
title: Azure Standard Load Balancer-diagnostik
titlesuffix: Azure Load Balancer
description: Använd tillgänglig information för mått och hälsa för diagnostik för Azure Standard Load Balancer.
services: load-balancer
documentationcenter: na
author: KumudD
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2018
ms.author: Kumud
ms.openlocfilehash: 77c3c595994092ff2ca68f3cefa5eb3c8a54bcd6
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53189055"
---
# <a name="metrics-and-health-diagnostics-for-standard-load-balancer"></a>Mått och hälsotillstånd diagnostik för Standard Load Balancer

Azure Standard Load Balancer exponerar Azure Standard Load Balancer ger dina resurser följande diagnostiska funktioner:
* **Flerdimensionella mått**: Innehåller nya flerdimensionella diagnostiska funktioner via [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) för både offentliga och interna laddar belastningsutjämnaren konfigurationer. Du kan övervaka, hantera och felsöka dina resurser för belastningsutjämning.

* **Resurshälsa**: Sidan belastningsutjämnare i Azure portal och Resource Health-sidan (under övervakning) exponerar avsnittet Resource Health för den offentliga belastningsutjämningskonfigurationen för Standard Load Balancer.

Den här artikeln innehåller en snabb genomgång av funktionerna och sätt att använda dem för Standard Load Balancer.

## <a name = "MultiDimensionalMetrics"></a>Flerdimensionella mått

Azure Load Balancer ger nya flerdimensionella mätvärden via den nya Azure-mått (förhandsversion) i Azure-portalen och det hjälper dig att få diagnostisk realtidsinsikter i inläsningen resurser för belastningsutjämning. 

De olika konfigurationerna som Standard Load Balancer ger följande mått:

| Mått | Resurstyp | Beskrivning | Rekommenderade aggregering |
| --- | --- | --- | --- |
| Tillgängligheten för VIP (datatillgänglighet för sökväg) | Offentlig belastningsutjämnare | Standard Load Balancer utför kontinuerligt datasökväg från inom en region till på klientsidan belastningsutjämnare, ända till SDN-stacken som har stöd för den virtuella datorn. Så länge felfria instanser förblir följer mätningen samma sökväg som ditt programs belastningsutjämnad trafik. Datasökväg som kunderna använder har också verifierats. Måttet är osynliga för ditt program och störa inte andra åtgärder.| Medel |
| Tillgängligheten för DIP (hälsostatus för avsökning) |  Offentliga och interna belastningsutjämnare | Standard Load Balancer använder en distribuerad avsökning av hälsotillstånd tjänst som övervakar din programslutpunkt hälsa enligt dina inställningar. Det här måttet tillhandahåller en mängd- eller per slutpunkt filtrerad vy för varje instans-slutpunkt i load balancer poolen. Du kan se hur belastningsutjämnaren visar hälsotillståndet för ditt program, som anges av din konfiguration för avsökning av hälsotillstånd. |  Medel |
| SYN (synkronisera) paket |  Offentlig belastningsutjämnare | Standard Load Balancer inte avsluta (TCP) anslutningar och interagera med flöden för TCP eller UDP-paket. Flöden och deras handskakningar är alltid mellan käll- och VM-instansen. Om du vill felsöka bättre scenariet TCP-protokollet, kan du använda SYN paket räknare att förstå hur många TCP-anslutning försök görs. Måttet rapporterar antalet TCP SYN-paket som tagits emot.| Medel |
| SNAT-anslutningar |  Offentlig belastningsutjämnare |Standard Load Balancer rapporterar antalet utgående flöden som masqueraded till klientdelen för offentliga IP-adress. Källportar network adress translation (SNAT) är en icke förnybara resurs. Det här måttet kan ge en indikation på hur mycket programmet förlitar sig på SNAT för utgående trafikflöden. Räknare för lyckade och misslyckade utgående SNAT flöden rapporteras och kan användas för att felsöka och förstå hälsotillståndet för dina utgående flöden.| Medel |
| Räknare för byte |  Offentliga och interna belastningsutjämnare | Standard Load Balancer rapporterar data som bearbetas per klient.| Medel |
| Paket-räknare |  Offentliga och interna belastningsutjämnare | Standard Load Balancer rapporterar de paket som bearbetas per klient.| Medel |

### <a name="view-your-load-balancer-metrics-in-the-azure-portal"></a>Visa dina load balancer mått i Azure portal

Azure-portalen visar load balancer mätvärden via sidan mått (förhandsgranskning), som finns på båda load balancer resource sidan för en viss resurs och Azure Monitor-sidan. 

Visa mått för dina resurser för Standard Load Balancer:
1. Gå till sidan mått (förhandsgranskning) och gör något av följande:
   * Välj måttypen på resurssidan för load balancer i den nedrullningsbara listan.
   * Välj belastningsutjämningsresursen på sidan Azure Monitor.
2. Ange lämpliga Aggregeringstyp.
3. Alternativt kan du konfigurera de nödvändiga filtrerings- och gruppering.

![Förhandsversionen av mått för Standard Load Balancer](./media/load-balancer-standard-diagnostics/LBMetrics1.png)

*Bild: DIP tillgänglighet och hälsotillstånd avsökningen status mått för Standard Load Balancer*

### <a name="retrieve-multi-dimensional-metrics-programmatically-via-apis"></a>Hämta flerdimensionella mätvärden via programmering via API: er

Vägledning för API för att hämta definitioner av mätvärden för flerdimensionella och värden, se [Azure Monitoring REST API-genomgång](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough#retrieve-metric-definitions-multi-dimensional-api).

### <a name = "DiagnosticScenarios"></a>Vanliga scenarier för diagnostik och rekommenderade vyer

#### <a name="is-the-data-path-up-and-available-for-my-load-balancer-vip"></a>Är datasökväg och är tillgängliga för Mina VIP för belastningsutjämnare?

Mått för VIP-tillgänglighet beskriver hälsotillståndet för datasökväg för regionen till compute-värden där dina virtuella datorer är placerade. Måttet är en avbildning av hälsotillståndet för Azure-infrastrukturen. Du kan använda mått till:
- Övervaka externa tillgängligheten för din tjänst
- Gå på djupet och förstå om den plattform som din tjänst har distribuerats är felfritt eller om din gäst-OS eller programinstans är felfri.
- Ta reda på om en händelse är relaterat till din tjänst eller underliggande dataplanet. Blanda inte ihop det här måttet med avsökning hälsostatus (”DIP tillgänglighet”).

Hämta VIP-tillgänglighet för dina resurser för Standard Load Balancer:
1. Kontrollera att rätt belastningsutjämningsresursen är markerad. 
2. I den **mått** listrutan, väljer **VIP tillgänglighet**. 
3. I den **aggregering** listrutan, väljer **genomsnittlig**. 
4. Dessutom lägga till ett filter på VIP-adress eller VIP-port som dimensionen med nödvändiga frontend IP-adressen eller frontend-porten och gruppera dem i den valda dimensionen.

![VIP-avsökning](./media/load-balancer-standard-diagnostics/LBMetrics-VIPProbing.png)

*Bild: Läsa in VIP för belastningsutjämnaren information-avsökning*

Måttet genereras av en aktiv, in-band-mätning. En sökning-tjänst inom regionen kommer trafik för mätning. Tjänsten är aktiverad när du skapar en distribution med en offentlig klientdel och den fortsätter tills du tar bort klientdelen. 

>[!NOTE]
>Interna klientdelar stöds inte just nu. 

Ett paket som matchar din distribution klientdelen och regeln genereras med jämna mellanrum. Det inom regionen från källan till värden där det finns en virtuell dator i backend poolen. Load balancer infrastruktur utför samma belastningen belastningsutjämning och översättning åtgärder som för all annan trafik. Den här avsökningen är i band på din slutpunkt för Utjämning av nätverksbelastning. När avsökningen anländer på compute-värden där det finns en felfri virtuell dator i backend poolen, genererar compute-värd ett svar till avsöknings-tjänsten. Den virtuella datorn kan inte se den här trafiken.

VIP-tillgänglighet misslyckas av följande skäl:
- Distributionen har inga felfria virtuella datorer kvar i backend-poolen. 
- Driftstörningar infrastruktur har uppstått.

I diagnostiskt syfte kan du använda den [VIP mått för tillgänglighet tillsammans med avsökning hälsostatus](#vipavailabilityandhealthprobes).

Använd **genomsnittliga** som sammansättning för de flesta scenarier.

#### <a name="are-the-back-end-instances-for-my-vip-responding-to-probes"></a>Backend-instanser för min VIP svarar på avsökningar?

Hälsotillstånd avsökningen status måttet beskriver hälsotillståndet för programdistributionen av som konfigureras av dig när du konfigurerar hälsoavsökning för belastningsutjämnaren. Belastningsutjämnaren använder status för hälsoavsökningen för att avgöra var du vill skicka nya flöden. Hälsokontroller av slutpunkter kommer från en adress för Azure-infrastrukturen och är synliga i gästoperativsystemet för den virtuella datorn.

Hämta DIP tillgängligheten för dina resurser för Standard Load Balancer:
1. Välj den **DIP tillgänglighet** mått med **genomsnittlig** aggregeringstypen. 
2. Tillämpa ett filter på krävs VIP IP-adressen eller port (eller båda).

![Tillgängligheten för DIP](./media/load-balancer-standard-diagnostics/LBMetrics-DIPAvailability.png)

*Bild: VIP för belastningsutjämnaren tillgänglighet*

Hälsokontroller av slutpunkter inte av följande skäl:
- Du konfigurerar en hälsoavsökning till en port som inte lyssnar eller inte svarar eller använder fel-protokollet. Om tjänsten använder direkt serverretur (DSR eller flytande IP) regler, se till att tjänsten lyssnar på IP-adressen för Nätverkskortets IP-konfiguration och inte bara på loopback som är konfigurerad med frontend-IP-adress.
- Din avsökning tillåts inte av Nätverkssäkerhetsgruppen, den Virtuella datorns OS gästbrandvägg eller application layer-filter.

Använd **genomsnittliga** som sammansättning för de flesta scenarier.

#### <a name="how-do-i-check-my-outbound-connection-statistics"></a>Hur kontrollerar jag min statistik över utgående anslutningar? 

SNAT-anslutningar måttet beskriver mängden lyckade och misslyckade anslutningar för [utgående flöden](https://aka.ms/lboutbound).

En volym för misslyckade anslutningar är större än noll indikerar SNAT portöverbelastning. Du måste undersöka vidare för att fastställa vad orsakar de här felen. SNAT portöverbelastning manifest som inte går att upprätta en [utgående flow](https://aka.ms/lboutbound). Läsa artikeln om utgående anslutningar att förstå scenarier och metoder på jobbet, och för att lära dig hantera och design för att undvika SNAT portöverbelastning. 

Hämta statistik över SNAT-anslutningar:
1. Välj **SNAT-anslutningar** Måttyp och **summan** som aggregering. 
2. Gruppera efter **anslutningsläge** för lyckade och misslyckade SNAT antal för anslutning som representeras av olika rader. 

![SNAT anslutning](./media/load-balancer-standard-diagnostics/LBMetrics-SNATConnection.png)

*Bild: Load Balancer SNAT-anslutningsräknare*


#### <a name="how-do-i-check-inboundoutbound-connection-attempts-for-my-service"></a>Hur kontrollerar jag inkommande/utgående anslutningsförsök för min tjänst?

Ett mått för SYN-paket beskrivs mängden TCP SYN-paket som har anlänt eller skickades (för [utgående flöden](https://aka.ms/lboutbound)) som är associerade med en specifik klientdel. Du kan använda det här måttet för att förstå TCP-anslutningsförsök till din tjänst.

Använd **totala** som sammansättning för de flesta scenarier.

![SYN-anslutning](./media/load-balancer-standard-diagnostics/LBMetrics-SYNCount.png)

*Bild: Load Balancer SYN antal*


#### <a name="how-do-i-check-my-network-bandwidth-consumption"></a>Hur kontrollerar jag min förbrukningen av nätverksbandbredd? 

Byte och paket räknare måttet beskriver mängden byte och paket som skickas eller tas emot av din tjänst på basis av per klientdelsservrarna.

Använd **totala** som sammansättning för de flesta scenarier.

Hämta paket eller byte sammanställd statistik:
1. Välj den **byte antal** och/eller **paket antal** Måttyp, med **genomsnittlig** som aggregering. 
2. Gör något av följande:
   * Tillämpa ett filter på en specifik frontend-IP, frontend-porten, backend-IP- eller backend-porten.
   * Hämta övergripande statistik för din belastningsutjämningsresursen utan någon filtrering.

![Antal byte](./media/load-balancer-standard-diagnostics/LBMetrics-ByteCount.png)

*Bild: Läsa in antal byte för belastningsutjämnare*

#### <a name = "vipavailabilityandhealthprobes"></a>Hur diagnostiserar jag min load balancer-distribution?

Du kan identifiera var du vill titta på problemet och åtgärda problemet genom att använda en kombination av tillgängligheten för VIP och hälsomått för avsökning i ett enda diagram. Du kan få säkra på att Azure fungerar och använder den här informationen för att fastställa definitivt konfiguration eller program är rotorsaken.

Du kan använda hälsomått för avsökningen för att förstå hur Azure visar hälsotillståndet för din distribution enligt konfigurationen som du har angett. Titta på hälsokontroller av slutpunkter är alltid en bra första steg i övervakning eller fastställa en orsak.

Du kan ta ett steg ytterligare och använda VIP tillgänglighetsmått få insikt i hur Azure visar hälsotillståndet för underliggande dataplanet som ansvarar för din specifika distribution. När du kombinerar både mått, kan du isolera där felet kan vara, enligt beskrivningen i det här exemplet:

![VIP-diagnostik](./media/load-balancer-standard-diagnostics/LBMetrics-DIPnVIPAvailability.png)

*Bild: Kombinera mått för DIP och VIP-tillgänglighet*

Diagrammet visar följande information:
- Infrastrukturen själva har felfri, den infrastruktur som är värd för dina virtuella datorer har kan nås och mer än en virtuell dator har placerats i serverdelen. Den här informationen visas med blå spårningen för VIP-tillgänglighet, vilket är 100 procent. 
- Hälsostatus för avsökning (DIP tillgänglighet) är dock på 0 procent i början av diagrammet, som anges av orange spårningen. Området inringad i grön höjdpunkter där status (DIP tillgänglighet) blivit felfria, och då kundens distribution kunde acceptera nya flöden.

Diagrammet kan kunderna att felsöka distributionen på egen hand utan att gissa eller be supporten om andra problem uppstår. Tjänsten kunde inte tillgänglig eftersom hälsoavsökningar misslyckas på grund av en felkonfiguration eller ett program med fel.

### <a name = "Limitations"></a>Begränsningar

VIP-tillgänglighet finns för närvarande endast för offentliga klientdelar.

## <a name = "ResourceHealth"></a>Resource health-status

Hälsostatus för resurserna som Standard Load Balancer exponeras via den befintliga **resurshälsa** under **övervakaren > Tjänstehälsa**.

>[!NOTE]
>Resurshälsostatus för belastningsutjämnaren är tillgänglig för offentliga konfigurationen för Standard Load Balancer endast. Resurser för intern belastningsutjämning eller SKU: er för belastningsutjämnare resurser visar inte resurshälsa.

Visa hälsotillståndet för dina offentlig Standard Load Balancer-resurser:
1. Välj **övervakaren** > **Tjänsthälsan**.

   ![Övervaka sidan](./media/load-balancer-standard-diagnostics/LBHealth1.png)

   *Bild: Länken Service Health på Azure Monitor*

2. Välj **Resource Health**, och kontrollera att **prenumerations-ID** och **resurstyp = belastningsutjämnaren** har valts.

   ![Resource Health-status](./media/load-balancer-standard-diagnostics/LBHealth3.png)

   *Bild: Välj resurs för vyn hälsotillstånd*

3. I listan väljer du Load Balancer-resursen för att visa dess historiska hälsostatus.

    ![Läsa in hälsostatusen för belastningsutjämnaren](./media/load-balancer-standard-diagnostics/LBHealth4.png)

   *Bild: Läsa in belastningsutjämnare resurshälsovyn*
 
Olika resource health statusvärden och deras beskrivningar finns i följande tabell: 

| Resource Health-status | Beskrivning |
| --- | --- |
| Tillgängligt | Din offentliga standard belastningsutjämningsresursen är felfri och tillgängliga. |
| Ej tillgänglig | Offentlig standard load balancer resursen är inte felfri. Diagnostisera hälsotillståndet genom att välja **Azure Monitor** > **mått**.<br>(*Ej tillgänglig* status kan även innebära att resursen inte är ansluten med offentlig standard load balancer.) |
| Okänt | Resurshälsostatus för offentlig standard load balancer har inte uppdaterats ännu.<br>(*Okänd* status kan även innebära att resursen inte är ansluten med offentlig standard load balancer.)  |

## <a name="next-steps"></a>Nästa steg

- Mer information finns i [Standard Load Balancer](load-balancer-standard-overview.md).
- Läs mer om din [läsa in belastningsutjämnare utgående anslutning](https://aka.ms/lboutbound).
- Lär dig mer om [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview).
- Lär dig mer om den [Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/) och [hur du hämtar måtten via REST API](/rest/api/monitor/metrics/list).


