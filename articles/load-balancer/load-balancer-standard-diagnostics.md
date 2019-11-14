---
title: Diagnostik med mått, aviseringar och resurs hälsa – Azure Standard Load Balancer
description: Använd de tillgängliga måtten, aviseringarna och resurs hälso informationen för att diagnostisera Azure-Standard Load Balancer.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/14/2019
ms.author: allensu
ms.openlocfilehash: ff42c6e9bd3c25721d2b77e49c2dd98a3eebdb43
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048733"
---
# <a name="standard-load-balancer-diagnostics-with-metrics-alerts-and-resource-health"></a>Standard Load Balancer-diagnostik med mått, aviseringar och resurshälsa

Azure Standard Load Balancer visar följande diagnostiska funktioner:

* **Flerdimensionella mått och aviseringar**: tillhandahåller nya funktioner för flera dimensionella diagnoser via [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) för standard konfiguration av belastningsutjämnare. Du kan övervaka, hantera och felsöka standard belastnings Utjämnings resurserna.

* **Resurs hälsa**: Load Balancer sidan på sidan Azure Portal och Resource Health (under övervakaren) visar Resource Health avsnittet för standard Load Balancer. 

Den här artikeln ger en snabb genom gång av de här funktionerna och ger dig möjlighet att använda dem för Standard Load Balancer.

## <a name = "MultiDimensionalMetrics"></a>Flerdimensionella mått

Azure Load Balancer ger nya flerdimensionella mått via de nya Azure-måtten i Azure Portal och det hjälper dig att få diagnostiska insikter i real tid i dina belastnings Utjämnings resurser. 

De olika Standard Load Balancer-konfigurationerna tillhandahåller följande mått:

| Mått | Resurstyp | Beskrivning | Rekommenderad agg regering |
| --- | --- | --- | --- |
| Tillgänglighet för data Sök väg (VIP-tillgänglighet)| Offentlig och intern belastningsutjämnare | Standard Load Balancer använder kontinuerligt data Sök vägen inifrån en region för belastnings utjämningens klient del, hela vägen till den SDN-stack som har stöd för den virtuella datorn. Så länge som felfria instanser är kvar, följer måtten samma sökväg som programmets belastningsutjämnade trafik. Den data Sök väg som dina kunder använder verifieras också. Måttet är osynligt för ditt program och stör inte andra åtgärder.| Medel |
| Status för hälso avsökning (DIP-tillgänglighet) | Offentlig och intern belastningsutjämnare | Standard Load Balancer använder en distribuerad hälso-/Bing-tjänst som övervakar din program slut punkts hälsa enligt dina konfigurations inställningar. Det här måttet innehåller en filtrerad mängd eller per slut punkt för varje instans slut punkt i belastningsutjämnaren. Du kan se hur Load Balancer visar hälso tillståndet för ditt program, som anges i konfigurationen för hälso avsökningen. |  Medel |
| SYN-paket (Synchronization) | Offentlig och intern belastningsutjämnare | Standard Load Balancer avslutar inte Transmission Control Protocol (TCP) anslutningar eller interagerar med TCP-eller UDP-paketfilter. Flöden och deras hand skakningar är alltid mellan källan och den virtuella dator instansen. För att bättre felsöka dina scenarier med TCP-protokoll kan du använda räknare för SYN paket för att förstå hur många TCP-anslutningsfel som görs. Måttet rapporterar antalet TCP-SYN-paket som tagits emot.| Medel |
| SNAT-anslutningar | Offentlig belastningsutjämnare |Standard Load Balancer rapporterar antalet utgående flöden som är maskerade till den offentliga IP-adressen klient delen. Käll Network Address Translations portar (SNAT) är en exhaustible-resurs. Det här måttet kan ge en indikation på hur mycket ditt program förlitar sig på SNAT för utgående, ursprungliga flöden. Räknare för lyckade och misslyckade utgående SNAT-flöden rapporteras och kan användas för att felsöka och förstå hälso tillståndet för dina utgående flöden.| Medel |
| Byte räknare |  Offentlig och intern belastningsutjämnare | Standard Load Balancer rapporterar de data som bearbetas per klient del.| Medel |
| Paket räknare |  Offentlig och intern belastningsutjämnare | Standard Load Balancer rapporterar de paket som bearbetas per klient del.| Medel |

### <a name="view-your-load-balancer-metrics-in-the-azure-portal"></a>Visa dina belastnings Utjämnings mått i Azure Portal

Azure Portal exponerar måtten för belastnings utjämning via sidan mått, som finns tillgänglig på resurs sidan för belastnings utjämning för en viss resurs och sidan Azure Monitor. 

Så här visar du måtten för dina Standard Load Balancer resurser:
1. Gå till sidan mått och gör något av följande:
   * På sidan belastnings Utjämnings resurs väljer du mått typen i list rutan.
   * På sidan Azure Monitor väljer du belastnings Utjämnings resursen.
2. Ange lämplig agg regerings typ.
3. Du kan också konfigurera filtrering och gruppering som krävs.

    ![Mått för Standard Load Balancer](./media/load-balancer-standard-diagnostics/lbmetrics1anew.png)

    *Bild: tillgänglighets mått för data Sök väg för Standard Load Balancer*

### <a name="retrieve-multi-dimensional-metrics-programmatically-via-apis"></a>Hämta flerdimensionella mått via programmerings gränssnitt

API-vägledning för att hämta flerdimensionella mått definitioner och värden finns i [genom gång av Azure monitoring REST API](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough#retrieve-metric-definitions-multi-dimensional-api).

### <a name = "DiagnosticScenarios"></a>Vanliga diagnostiska scenarier och rekommenderade vyer

#### <a name="is-the-data-path-up-and-available-for-my-load-balancer-vip"></a>Är data Sök vägen tillgänglig för alla VIP för belastningsutjämnare?

Måttet VIP-tillgänglighet beskriver hälsan för data Sök vägen inom regionen till den beräknings värd där de virtuella datorerna finns. Måttet är en reflektion av Azure-infrastrukturens hälso tillstånd. Du kan använda måttet för att:
- Övervaka extern tillgänglighet för din tjänst
- Gå djupare och lär dig om den plattform där din tjänst distribueras är felfri eller om ditt gäst operativ system eller program instansen är felfritt.
- Isolera om en händelse är relaterad till din tjänst eller det underliggande data planet. Blanda inte ihop detta mått med status för hälso avsökning ("DIP-tillgänglighet").

Så här hämtar du data Sök vägs tillgänglighet för dina Standard Load Balancer-resurser:
1. Kontrol lera att rätt belastnings Utjämnings resurs har valts. 
2. I list rutan **mått** väljer du **tillgänglighet för data Sök väg**. 
3. Välj **AVG**i list rutan **agg regering** . 
4. Lägg också till ett filter på klient delens IP-adress eller frontend-port som dimension med den begärda frontend-IP-adressen eller klient dels porten och gruppera dem sedan efter den valda dimensionen.

![VIP-sökning](./media/load-balancer-standard-diagnostics/LBMetrics-VIPProbing.png)

*Bild: information om Load Balancer-frontend-probing*

Måttet genereras av en aktiv mätning på band. En avsöknings tjänst i regionen kommer att ha trafik för måttet. Tjänsten aktive ras så snart du skapar en distribution med en offentlig klient del och den fortsätter tills du tar bort klient delen. 

Ett paket som matchar distributionens klient del och regel genereras med jämna mellanrum. Den passerar regionen från källan till värden där en virtuell dator i backend-poolen finns. Belastnings Utjämnings infrastrukturen utför samma belastnings utjämning och översättnings åtgärder som för all annan trafik. Den här avsökningen är inaktive rad på den belastningsutjämnade slut punkten. När avsökningen har inträffat på beräknings värden, där en felfri virtuell dator i backend-poolen finns, genererar beräknings värden ett svar till tjänsten för avsökning. Den virtuella datorn ser inte den här trafiken.

VIP-tillgängligheten Miss lyckas av följande orsaker:
- Distributionen har inga friska virtuella datorer kvar i backend-poolen. 
- Det har uppstått ett avbrott i infrastrukturen.

I diagnostiskt syfte kan du använda [tillgänglighets måttet data Sök väg tillsammans med status för hälso avsökningen](#vipavailabilityandhealthprobes).

Använd **Average** som agg regering för de flesta scenarier.

#### <a name="are-the-back-end-instances-for-my-vip-responding-to-probes"></a>Är backend-instanserna för min VIP att svara på avsökningar?

Måttet hälso avsöknings status beskriver hälso tillståndet för din program distribution enligt konfigurationen när du konfigurerar belastnings utjämningens hälso avsökning. Belastningsutjämnaren använder status för hälso avsökningen för att avgöra var nya flöden ska skickas. Hälso avsökningar härstammar från en Azure-infrastruktur och visas i den virtuella datorns gäst operativ system.

Så här hämtar du hälso avsöknings statusen för dina Standard Load Balancer resurser:
1. Välj status mått för **hälso avsökning** med **genomsnittlig** agg regerings typ. 
2. Använd ett filter för den begärda IP-adressen eller porten för klient delen (eller båda).

Hälso avsökningar fungerar inte på grund av följande orsaker:
- Du konfigurerar en hälso avsökning på en port som inte lyssnar eller inte svarar eller som använder fel protokoll. Om tjänsten använder regler för direkt Server retur (DSR eller flytande IP) kontrollerar du att tjänsten lyssnar på IP-adressen för NÄTVERKSKORTets IP-konfiguration och inte bara på den loopback som är konfigurerad med klient delens IP-adress.
- Din avsökning tillåts inte av nätverks säkerhets gruppen, den virtuella datorns gäst operativ system brand vägg eller program skikts filter.

Använd **Average** som agg regering för de flesta scenarier.

#### <a name="how-do-i-check-my-outbound-connection-statistics"></a>Hur gör jag för att kontrollerar du statistiken för utgående anslutning? 

Måttet SNAT-anslutningar beskriver volymen av lyckade och misslyckade anslutningar för [utgående flöden](https://aka.ms/lboutbound).

En misslyckad anslutnings volym på större än noll anger antalet SNAT-portar. Du måste undersöka ytterligare för att avgöra vad som kan orsaka dessa problem. SNAT-portens överbelastnings manifest kan inte upprätta ett [utgående flöde](https://aka.ms/lboutbound). Läs artikeln om utgående anslutningar för att förstå scenarier och mekanismer i arbetet, och för att lära dig hur du minimerar och designar för att undvika SNAT-portar. 

För att hämta SNAT-anslutnings statistik:
1. Välj Metric-typ för **SNAT-anslutningar** och **Sum** som agg regering. 
2. Gruppera efter **anslutnings tillstånd** för lyckade och MISSLYCKAde SNAT-anslutnings räkningar som representeras av olika rader. 

![SNAT-anslutning](./media/load-balancer-standard-diagnostics/LBMetrics-SNATConnection.png)

*Bild: antal Load Balancer SNAT-anslutningar*


#### <a name="how-do-i-check-inboundoutbound-connection-attempts-for-my-service"></a>Hur gör jag för att kontrol lera inkommande/utgående anslutnings försök för min tjänst?

Måttet SYN paket beskriver volymen av TCP-SYN-paket, som har anlänt eller skickats (för [utgående flöden](https://aka.ms/lboutbound)) som är associerade med en speciell klient del. Du kan använda det här måttet för att förstå TCP-anslutnings försök till din tjänst.

Använd **Total** som agg regering för de flesta scenarier.

![SYN anslutning](./media/load-balancer-standard-diagnostics/LBMetrics-SYNCount.png)

*Bild: Load Balancer SYN antal*


#### <a name="how-do-i-check-my-network-bandwidth-consumption"></a>Hur gör jag för att kontrollerar du användningen av nätverks bandbredden? 

Måttet byte och paket räknare beskriver mängden byte och paket som skickas eller tas emot av din tjänst per klient del.

Använd **Total** som agg regering för de flesta scenarier.

Så här hämtar du statistik för byte eller antal paket:
1. Välj Metric-typen antal **byte** och/eller **antal paket** , med **AVG** as aggregation. 
2. Gör något av följande:
   * Använd ett filter på en speciell frontend IP-adress, frontend-port, backend-IP eller backend-port.
   * Hämta allmän statistik för belastnings Utjämnings resursen utan filtrering.

![Antal byte](./media/load-balancer-standard-diagnostics/LBMetrics-ByteCount.png)

*Bild: antal Load Balancer byte*

#### <a name = "vipavailabilityandhealthprobes"></a>Hur gör jag för att diagnostisera distribution av belastningsutjämnare?

Genom att använda en kombination av måtten VIP-tillgänglighet och hälso avsökning i ett enskilt diagram kan du identifiera var du ska leta efter problemet och lösa problemet. Du kan se till att Azure fungerar som den ska och använda den här kunskapen för att avgörande fastställa att konfigurationen eller programmet är rotor saken.

Du kan använda hälso avsöknings mått för att förstå hur Azure visar hälsan för distributionen enligt den konfiguration som du har angett. Att titta på hälso avsökningar är alltid ett bra första steg vid övervakning eller bestämning av en orsak.

Du kan ta steget vidare och använda VIP-tillgänglighet för att få inblick i hur Azure visar hälso tillståndet för det underliggande data planet som ansvarar för din speciella distribution. När du kombinerar båda måtten kan du isolera var felet kan vara, vilket visas i det här exemplet:

![Kombinera status mått för data Sök vägs tillgänglighet och hälso avsökning](./media/load-balancer-standard-diagnostics/lbmetrics-dipnvipavailability-2bnew.png)

*Bild: kombinera status mått för data Sök vägs tillgänglighet och hälso avsökning*

Diagrammet visar följande information:
- Den infrastruktur som är värd för dina virtuella datorer var inte tillgänglig och 0 procent i början av diagrammet. Senare var infrastrukturen felfritt och de virtuella datorerna var tillgängliga och fler än en VM placerades i Server delen. Den här informationen anges av den blå spårningen för tillgänglighet för data Sök vägar (VIP-tillgänglighet), som var senare vid 100 procent. 
- Status för hälso avsökning (DIP-tillgänglighet), som anges av den lila spåret, är vid 0 procent i början av diagrammet. Det inringade området i grönt visar var hälso avsökningens status (DIP-tillgänglighet) blev felfri, och vid vilken tidpunkt kundens distribution kunde ta emot nya flöden.

Med hjälp av diagrammet kan kunderna felsöka distributionen på egen hand utan att gissa eller be om andra problem. Tjänsten var inte tillgänglig eftersom hälso avsökningar misslyckades på grund av en felaktig konfiguration eller ett misslyckat program.

## <a name = "ResourceHealth"></a>Resurs hälso status

Hälso status för de Standard Load Balancer resurserna exponeras via den befintliga **resurs hälsan** under **övervaka > service Health**.

Så här visar du hälso tillståndet för dina offentliga Standard Load Balancer-resurser:
1. Välj **övervaka** > **service Health**.

   ![Övervaknings sida](./media/load-balancer-standard-diagnostics/LBHealth1.png)

   *Bild: Service Health länken på Azure Monitor*

2. Välj **Resource Health**och kontrol lera att **prenumerations-ID** och **resurs typ = Load Balancer** har marker ATS.

   ![Resurs hälso status](./media/load-balancer-standard-diagnostics/LBHealth3.png)

   *Bild: Välj resurs för hälso tillstånds visning*

3. I listan väljer du Load Balancer resursen för att visa dess historiska hälso status.

    ![Load Balancer hälso status](./media/load-balancer-standard-diagnostics/LBHealth4.png)

   *Bild: Load Balancer vyn resurs hälsa*
 
De olika resurs hälso status och deras beskrivningar visas i följande tabell: 

| Resurs hälso status | Beskrivning |
| --- | --- |
| Tillgängligt | Standard belastnings Utjämnings resursen är felfri och tillgänglig. |
| Inte tillgänglig | Standard belastnings Utjämnings resursen är inte felfri. Diagnostisera hälsan genom att välja **Azure Monitor** > **mått**.<br>(Statusen*otillgänglig* kan även betyda att resursen inte är ansluten till standard belastnings utjämningen.) |
| Okänt | Resursens hälso status för din standard belastnings Utjämnings resurs har ännu inte uppdaterats.<br>(*Okänd* status kan också betyda att resursen inte är ansluten till din standard belastningsutjämnare.)  |

## <a name="next-steps"></a>Nästa steg

- Mer information finns i [Standard Load Balancer](load-balancer-standard-overview.md).
- Läs mer om den [utgående anslutningen till belastningsutjämnaren](https://aka.ms/lboutbound).
- Läs mer om [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview).
- Lär dig mer om [Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/) och [hur du hämtar mått via REST API](/rest/api/monitor/metrics/list).
