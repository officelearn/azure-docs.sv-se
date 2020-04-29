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
ms.openlocfilehash: 9003d35ce2eea18aa912a866802b026bb923aa08
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81272703"
---
# <a name="standard-load-balancer-diagnostics-with-metrics-alerts-and-resource-health"></a>Standard Load Balancer-diagnostik med mått, aviseringar och resurshälsa

Azure Standard Load Balancer visar följande diagnostiska funktioner:

* **Flerdimensionella mått och aviseringar**: ger stöd för flera dimensionella diagnoser via [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) för standard konfiguration av belastningsutjämnare. Du kan övervaka, hantera och felsöka standard belastnings Utjämnings resurserna.

* **Resurs hälsa**: Load Balancer sidan på sidan Azure Portal och Resource Health (under övervakaren) visar Resource Health avsnittet för standard Load Balancer. 

Den här artikeln ger en snabb genom gång av de här funktionerna och ger dig möjlighet att använda dem för Standard Load Balancer. 

## <a name="multi-dimensional-metrics"></a><a name = "MultiDimensionalMetrics"></a>Flerdimensionella mått

Azure Load Balancer tillhandahåller flerdimensionella mått via Azure-måtten i Azure Portal och det hjälper dig att få diagnostiska insikter i real tid i dina belastnings Utjämnings resurser. 

De olika Standard Load Balancer-konfigurationerna tillhandahåller följande mått:

| Mått | Resurstyp | Beskrivning | Rekommenderad agg regering |
| --- | --- | --- | --- |
| Tillgänglighet för data Sök väg | Offentlig och intern belastningsutjämnare | Standard Load Balancer använder kontinuerligt data Sök vägen inifrån en region för belastnings utjämningens klient del, hela vägen till den SDN-stack som har stöd för den virtuella datorn. Så länge som felfria instanser är kvar, följer måtten samma sökväg som programmets belastningsutjämnade trafik. Den data Sök väg som dina kunder använder verifieras också. Måttet är osynligt för ditt program och stör inte andra åtgärder.| Medel |
| Status för hälso avsökning | Offentlig och intern belastningsutjämnare | Standard Load Balancer använder en distribuerad hälso-/Bing-tjänst som övervakar din program slut punkts hälsa enligt dina konfigurations inställningar. Det här måttet innehåller en filtrerad mängd eller per slut punkt för varje instans slut punkt i belastningsutjämnaren. Du kan se hur Load Balancer visar hälso tillståndet för ditt program, som anges i konfigurationen för hälso avsökningen. |  Medel |
| SYN-paket (Synchronization) | Offentlig och intern belastningsutjämnare | Standard Load Balancer avslutar inte Transmission Control Protocol (TCP) anslutningar eller interagerar med TCP-eller UDP-paketfilter. Flöden och deras hand skakningar är alltid mellan källan och den virtuella dator instansen. För att bättre felsöka dina scenarier med TCP-protokoll kan du använda räknare för SYN paket för att förstå hur många TCP-anslutningsfel som görs. Måttet rapporterar antalet TCP-SYN-paket som tagits emot.| Medel |
| SNAT-anslutningar | Offentlig belastningsutjämnare |Standard Load Balancer rapporterar antalet utgående flöden som är maskerade till den offentliga IP-adressen klient delen. Käll Network Address Translations portar (SNAT) är en exhaustible-resurs. Det här måttet kan ge en indikation på hur mycket ditt program förlitar sig på SNAT för utgående, ursprungliga flöden. Räknare för lyckade och misslyckade utgående SNAT-flöden rapporteras och kan användas för att felsöka och förstå hälso tillståndet för dina utgående flöden.| Medel |
| Allokerade SNAT-portar | Offentlig belastningsutjämnare | Standard Load Balancer rapporterar antalet SNAT-portar som tilldelas per server dels instans | Snitt. |
| Använda SNAT-portar | Offentlig belastningsutjämnare | Standard Load Balancer rapporterar antalet SNAT-portar som används per server dels instans. | Medel | 
| Byte räknare |  Offentlig och intern belastningsutjämnare | Standard Load Balancer rapporterar de data som bearbetas per klient del. Du kanske märker att bytena inte distribueras jämnt över Server dels instanserna. Detta förväntas vara att Azures Load Balancer algoritmen baseras på flöden | Medel |
| Paket räknare |  Offentlig och intern belastningsutjämnare | Standard Load Balancer rapporterar de paket som bearbetas per klient del.| Medel |

### <a name="view-your-load-balancer-metrics-in-the-azure-portal"></a>Visa dina belastnings Utjämnings mått i Azure Portal

Azure Portal exponerar måtten för belastnings utjämning via sidan mått, som finns tillgänglig på resurs sidan för belastnings utjämning för en viss resurs och sidan Azure Monitor. 

Så här visar du måtten för dina Standard Load Balancer resurser:
1. Gå till sidan mått och gör något av följande:
   * På sidan belastnings Utjämnings resurs väljer du mått typen i list rutan.
   * På sidan Azure Monitor väljer du belastnings Utjämnings resursen.
2. Ange lämplig mått agg regerings typ.
3. Du kan också konfigurera filtrering och gruppering som krävs.
4. Du kan också konfigurera tidsintervallet och agg regeringen. Som standard visas tid i UTC.

  >[!NOTE] 
  >Tids mängd är viktigt när du tolkar vissa mått som data samplas en gång per minut. Om tids agg regering är inställt på fem minuter och typen summa av mått agg regerings typ används för mått som till exempel SNAT-allokering, visas fem gånger i diagrammet fem gånger antalet allokerade SNAT-portar. 

![Mått för Standard Load Balancer](./media/load-balancer-standard-diagnostics/lbmetrics1anew.png)

*Bild: tillgänglighets mått för data Sök väg för Standard Load Balancer*

### <a name="retrieve-multi-dimensional-metrics-programmatically-via-apis"></a>Hämta flerdimensionella mått via programmerings gränssnitt

API-vägledning för att hämta flerdimensionella mått definitioner och värden finns i [genom gång av Azure monitoring REST API](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough#retrieve-metric-definitions-multi-dimensional-api). Dessa mått kan skrivas till ett lagrings konto via alternativet "alla mått". 

### <a name="configure-alerts-for-multi-dimensional-metrics"></a>Konfigurera aviseringar för flerdimensionella mått ###

Azure Standard Load Balancer stöder enkelt konfigurerbara aviseringar för flerdimensionella mått. Konfigurera anpassade tröskelvärden för vissa mått för att utlösa aviseringar med varierande nivåer av allvarlighets grad för att ge en berörings lös resurs övervaknings upplevelse.

Så här konfigurerar du varningar:
1. Gå till under bladet avisering för belastningsutjämnaren
1. Skapa en ny aviseringsregel
    1.  Konfigurera aviserings villkor
    1.  Valfritt Lägg till åtgärds grupp för automatisk reparation
    1.  Tilldela allvarlighets grad, namn och beskrivning för aviseringar som möjliggör intuitiv reaktion

  >[!NOTE]
  >I fönstret konfiguration av aviserings villkor visas tids serier för signal historik. Det finns ett alternativ för att filtrera den här tids serien efter dimensioner som backend-IP. Detta filtrerar tids serie diagrammet men **inte** själva aviseringen. Det går inte att konfigurera aviseringar för vissa IP-adresser för Server delen.

### <a name="common-diagnostic-scenarios-and-recommended-views"></a><a name = "DiagnosticScenarios"></a>Vanliga diagnostiska scenarier och rekommenderade vyer

#### <a name="is-the-data-path-up-and-available-for-my-load-balancer-frontend"></a>Är data Sök vägen tillgänglig och tillgänglig för min Load Balancer-frontend?
<details><summary>Visa</summary>

Måttet tillgänglighet för data Sök väg för tillgänglighets tillgänglighet beskriver hälsan för data Sök vägen inom regionen till den beräknings värd där de virtuella datorerna finns. Måttet är en reflektion av Azure-infrastrukturens hälso tillstånd. Du kan använda måttet för att:
- Övervaka extern tillgänglighet för din tjänst
- Gå djupare och lär dig om den plattform där din tjänst distribueras är felfri eller om ditt gäst operativ system eller program instansen är felfritt.
- Isolera om en händelse är relaterad till din tjänst eller det underliggande data planet. Blanda inte ihop det här måttet med hälso avsöknings status ("Server dels instans tillgänglighet").

Så här hämtar du data Sök vägs tillgänglighet för dina Standard Load Balancer-resurser:
1. Kontrol lera att rätt belastnings Utjämnings resurs har valts. 
2. I list rutan **mått** väljer du **tillgänglighet för data Sök väg**. 
3. Välj **AVG**i list rutan **agg regering** . 
4. Lägg också till ett filter på klient delens IP-adress eller frontend-port som dimension med den begärda frontend-IP-adressen eller klient dels porten och gruppera dem sedan efter den valda dimensionen.

![VIP-sökning](./media/load-balancer-standard-diagnostics/LBMetrics-VIPProbing.png)

*Bild: information om Load Balancer-frontend-probing*

Måttet genereras av en aktiv mätning på band. En avsöknings tjänst i regionen kommer att ha trafik för måttet. Tjänsten aktive ras så snart du skapar en distribution med en offentlig klient del och den fortsätter tills du tar bort klient delen. 

Ett paket som matchar distributionens klient del och regel genereras med jämna mellanrum. Den passerar regionen från källan till värden där en virtuell dator i backend-poolen finns. Belastnings Utjämnings infrastrukturen utför samma belastnings utjämning och översättnings åtgärder som för all annan trafik. Den här avsökningen är inaktive rad på den belastningsutjämnade slut punkten. När avsökningen har inträffat på beräknings värden, där en felfri virtuell dator i backend-poolen finns, genererar beräknings värden ett svar till tjänsten för avsökning. Den virtuella datorn ser inte den här trafiken.

Tillgänglighets tillgänglighet för Datapath Miss lyckas av följande orsaker:
- Distributionen har inga friska virtuella datorer kvar i backend-poolen. 
- Det har uppstått ett avbrott i infrastrukturen.

I diagnostiskt syfte kan du använda [tillgänglighets måttet data Sök väg tillsammans med status för hälso avsökningen](#vipavailabilityandhealthprobes).

Använd **Average** som agg regering för de flesta scenarier.
</details>

#### <a name="are-the-backend-instances-for-my-load-balancer-responding-to-probes"></a>Finns det Server dels instanser för mina Load Balancer som svarar på avsökningar?
<details>
  <summary>Visa</summary>
Måttet hälso avsöknings status beskriver hälso tillståndet för din program distribution enligt konfigurationen när du konfigurerar belastnings utjämningens hälso avsökning. Belastningsutjämnaren använder status för hälso avsökningen för att avgöra var nya flöden ska skickas. Hälso avsökningar härstammar från en Azure-infrastruktur och visas i den virtuella datorns gäst operativ system.

Så här hämtar du hälso avsöknings statusen för dina Standard Load Balancer resurser:
1. Välj status mått för **hälso avsökning** med **genomsnittlig** agg regerings typ. 
2. Använd ett filter för den begärda IP-adressen eller porten för klient delen (eller båda).

Hälso avsökningar fungerar inte på grund av följande orsaker:
- Du konfigurerar en hälso avsökning på en port som inte lyssnar eller inte svarar eller som använder fel protokoll. Om tjänsten använder regler för direkt Server retur (DSR eller flytande IP) kontrollerar du att tjänsten lyssnar på IP-adressen för NÄTVERKSKORTets IP-konfiguration och inte bara på den loopback som är konfigurerad med klient delens IP-adress.
- Din avsökning tillåts inte av nätverks säkerhets gruppen, den virtuella datorns gäst operativ system brand vägg eller program skikts filter.

Använd **Average** som agg regering för de flesta scenarier.
</details>

#### <a name="how-do-i-check-my-outbound-connection-statistics"></a>Hur gör jag för att kontrollerar du statistiken för utgående anslutning? 
<details>
  <summary>Visa</summary>
Måttet SNAT-anslutningar beskriver volymen av lyckade och misslyckade anslutningar för [utgående flöden](https://aka.ms/lboutbound).

En misslyckad anslutnings volym på större än noll anger antalet SNAT-portar. Du måste undersöka ytterligare för att avgöra vad som kan orsaka dessa problem. SNAT-portens överbelastnings manifest kan inte upprätta ett [utgående flöde](https://aka.ms/lboutbound). Läs artikeln om utgående anslutningar för att förstå scenarier och mekanismer i arbetet, och för att lära dig hur du minimerar och designar för att undvika SNAT-portar. 

För att hämta SNAT-anslutnings statistik:
1. Välj Metric-typ för **SNAT-anslutningar** och **Sum** som agg regering. 
2. Gruppera efter **anslutnings tillstånd** för lyckade och MISSLYCKAde SNAT-anslutnings räkningar som representeras av olika rader. 

![SNAT-anslutning](./media/load-balancer-standard-diagnostics/LBMetrics-SNATConnection.png)

*Bild: antal Load Balancer SNAT-anslutningar*
</details>


#### <a name="how-do-i-check-my-snat-port-usage-and-allocation"></a>Hur gör jag för att kontrollerar du användning och allokering av SNAT-porten?
<details>
  <summary>Visa</summary>
Användnings måttet för SNAT anger hur många unika flöden som upprättas mellan en Internet källa och en virtuell server dels dator eller skalnings uppsättning för virtuella datorer som finns bakom en belastningsutjämnare och inte har en offentlig IP-adress. Genom att jämföra detta med tilldelnings måttet för SNAT kan du avgöra om tjänsten är utsatt för eller riskerar att överskrida SNAT och resulterande utgående flödes haveri. 

Om dina mått indikerar risk för [utgående flödes](https://aka.ms/lboutbound) fel, referera till artikeln och vidta steg för att minimera det för att säkerställa tjänstens hälsa.

Visa SNAT-port användning och allokering:
1. Ange tids agg regeringen för grafen till 1 minut för att se till att önskade data visas.
1. Välj **SNAT-användning** och/eller **SNAT-allokering** som mått typ och **genomsnitt** som agg regering
    * Som standard är detta det genomsnittliga antalet SNAT-portar som allokerats till eller används av varje server dels virtuella datorer eller VMSS, som motsvarar alla offentliga IP-adresser som är mappade till Load Balancer, aggregerade över TCP och UDP.
    * Om du vill visa totalt antal SNAT-portar som används av eller allokeras för belastningsutjämnaren använder du mått mängds **summan**
1. Filtrera till en angiven **protokoll typ**, en uppsättning **Server dels IP-adresser**och/eller **klient delens IP-adresser**.
1. Om du vill övervaka hälso tillståndet per backend-eller klient dels instans använder du delning. 
    * Med antecknings delning kan du bara visa ett enda mått i taget. 
1. Om du till exempel vill övervaka SNAT-användning för TCP-flöden per dator, aggregera i **genomsnitt**, dela efter **Server dels IP-adresser** och filtrera efter **protokoll typ**. 

![Tilldelning och användning av SNAT](./media/load-balancer-standard-diagnostics/snat-usage-and-allocation.png)

*Bild: genomsnittlig TCP SNAT-port tilldelning och användning för en uppsättning Server dels datorer*

![SNAT-användning efter server dels instans](./media/load-balancer-standard-diagnostics/snat-usage-split.png)

*Bild: TCP SNAT-port användning per server dels instans*
</details>

#### <a name="how-do-i-check-inboundoutbound-connection-attempts-for-my-service"></a>Hur gör jag för att kontrol lera inkommande/utgående anslutnings försök för min tjänst?
<details>
  <summary>Visa</summary>
Måttet SYN paket beskriver volymen av TCP-SYN-paket, som har anlänt eller skickats (för [utgående flöden](https://aka.ms/lboutbound)) som är associerade med en speciell klient del. Du kan använda det här måttet för att förstå TCP-anslutnings försök till din tjänst.

Använd **Total** som agg regering för de flesta scenarier.

![SYN anslutning](./media/load-balancer-standard-diagnostics/LBMetrics-SYNCount.png)

*Bild: Load Balancer SYN antal*
</details>


#### <a name="how-do-i-check-my-network-bandwidth-consumption"></a>Hur gör jag för att kontrollerar du användningen av nätverks bandbredden? 
<details>
  <summary>Visa</summary>
Måttet byte och paket räknare beskriver mängden byte och paket som skickas eller tas emot av din tjänst per klient del.

Använd **Total** som agg regering för de flesta scenarier.

Så här hämtar du statistik för byte eller antal paket:
1. Välj Metric-typen antal **byte** och/eller **antal paket** , med **AVG** as aggregation. 
2. Gör något av följande:
   * Använd ett filter på en speciell frontend IP-adress, frontend-port, backend-IP eller backend-port.
   * Hämta allmän statistik för belastnings Utjämnings resursen utan filtrering.

![Antal byte](./media/load-balancer-standard-diagnostics/LBMetrics-ByteCount.png)

*Bild: antal Load Balancer byte*
</details>

#### <a name="how-do-i-diagnose-my-load-balancer-deployment"></a><a name = "vipavailabilityandhealthprobes"></a>Hur gör jag för att diagnostisera distribution av belastningsutjämnare?
<details>
  <summary>Visa</summary>
Genom att använda en kombination av status måtten tillgänglighet för data Sök väg och hälso avsökning i ett enskilt diagram kan du identifiera var du ska leta efter problemet och lösa problemet. Du kan se till att Azure fungerar som den ska och använda den här kunskapen för att avgörande fastställa att konfigurationen eller programmet är rotor saken.

Du kan använda hälso avsöknings mått för att förstå hur Azure visar hälsan för distributionen enligt den konfiguration som du har angett. Att titta på hälso avsökningar är alltid ett bra första steg vid övervakning eller bestämning av en orsak.

Du kan ta ett steg mer och använda tillgänglighets måttet data Sök väg för att få information om hur Azure visar hälso tillståndet för det underliggande data planet som ansvarar för din speciella distribution. När du kombinerar båda måtten kan du isolera var felet kan vara, vilket visas i det här exemplet:

![Kombinera status mått för data Sök vägs tillgänglighet och hälso avsökning](./media/load-balancer-standard-diagnostics/lbmetrics-dipnvipavailability-2bnew.png)

*Bild: kombinera status mått för data Sök vägs tillgänglighet och hälso avsökning*

Diagrammet visar följande information:
- Den infrastruktur som är värd för dina virtuella datorer var inte tillgänglig och 0 procent i början av diagrammet. Senare var infrastrukturen felfritt och de virtuella datorerna var tillgängliga och fler än en VM placerades i Server delen. Den här informationen anges av den blå spårningen för tillgänglighet för data Sök vägar, som var senare vid 100 procent. 
- Status för hälso avsökningen, som anges av den lila spåren, är vid 0 procent i början av diagrammet. Det cirkelformade området i grönt visar var hälso avsöknings statusen blev felfri, och vid vilken tidpunkt kundens distribution kunde ta emot nya flöden.

Med hjälp av diagrammet kan kunderna felsöka distributionen på egen hand utan att gissa eller be om andra problem. Tjänsten var inte tillgänglig eftersom hälso avsökningar misslyckades på grund av en felaktig konfiguration eller ett misslyckat program.
</details>

## <a name="resource-health-status"></a><a name = "ResourceHealth"></a>Resurs hälso status

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
