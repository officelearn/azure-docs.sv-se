---
title: Azure Standard belastningsutjämnare - diagnostik | Microsoft Docs
description: Med hjälp av informationen om tillgängliga mått och hälsa för diagnostik för Azure Standard belastningsutjämnaren
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 46b152c5-6a27-4bfc-bea3-05de9ce06a57
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2018
ms.author: Kumud
ms.openlocfilehash: 7d60925381abe617f6e2fac51176b8e30517c3ba
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2018
---
# <a name="metrics-and-health-diagnostics-for-standard-load-balancer"></a>Mått hälsa och diagnostik för Standard belastningsutjämnare

Azure belastningsutjämnare som Standard innehåller följande diagnostiska funktioner för dina resurser:
* Flerdimensionella mått: Standard belastningsutjämnare ger nya flerdimensionella diagnostiska funktioner för både offentliga och interna belastningsutjämnare konfigurationer. På så sätt kan du övervaka, hantera och felsöka resurserna i belastningsutjämnaren.

* Resource Health: Sidan belastningsutjämnare i Azure portal och Resource Health-sida (under övervakning) exponera Resurshälsa offentliga belastningsutjämnare konfiguration av Standard belastningsutjämnaren.

Den här artikeln ger en snabb genomgång av dessa funktioner och metoder för att använda dessa för Standard belastningsutjämnaren.

## <a name = "MultiDimensionalMetrics"></a>Flerdimensionella mått

Azure belastningsutjämnare ger de nya flerdimensionella mätvärdena via den nya Azure-mått (förhandsgranskning) i portalen och hjälper dig att få realtid diagnostiska insikter om dina resurser för belastningsutjämnaren. 

Följande mått finns numera tillgängliga för olika konfigurationer som Standard belastningen belastningsutjämnare (LB):

| Mått | Resurstyp | Beskrivning | Rekommenderade aggregering |
| --- | --- | --- | --- |
| VIP-tillgänglighet (sökväg datatillgänglighet) | Public LB | Standard belastningsutjämnare använder kontinuerligt datasökväg från inom en region till belastningsutjämnaren klientdel ända till stacken SDN som har stöd för den virtuella datorn. Så länge felfri instanser förblir följer mätningen samma sökväg som ditt program belastningsutjämnad trafik. Datasökväg som används av dina kunder också valideras. Måttet är osynliga för ditt program och störa inte andra åtgärder.| Medel |
| DIP tillgänglighet (hälsostatus för avsökning) |  Offentliga och interna LB | Standard belastningsutjämnare använder en distribuerad hälsa avsökning av tjänst som övervakar dina program endpoint hälsa enligt inställningarna. Det här måttet ger en aggregering eller per slutpunkt filtrerad vy för varje enskild instans slutpunkt i belastningsutjämnaren poolen.  Du kan se hur belastningsutjämnaren visar hälsotillståndet för programmet som anges av din konfiguration för avsökning av hälsotillstånd. |  Medel |
| SYN-paket |  Public LB | Standard belastningsutjämnaren inte avsluta TCP-anslutningar och interagera med TCP eller UDP-paket-flöden. Flöden och deras handskakningar på är alltid mellan käll- och VM-instans. Om du vill felsöka bättre scenariet TCP-protokollet, kan du använda SYN paket räknare för att förstå hur många TCP-anslutning görs försök. Måttet rapporterar antalet TCP SYN-paket som tagits emot.| Medel |
| SNAT anslutningar |  Public LB |Standard belastningsutjämnaren rapporterar antalet utgående flöden masqueraded till klientdel för offentlig IP-adress. SNAT portar är en icke förnybara resurs. Det här måttet kan ge en indikation på hur mycket tillämpningsprogrammet förlitar sig på SNAT för utgående flöden som har sitt ursprung.  Räknare för lyckade och misslyckade utgående SNAT flöden rapporteras och kan användas för att felsöka och förstå hälsotillståndet för din utgående flöden.| Medel |
| Räknare för byte |  Offentliga och interna LB | Standard belastningsutjämnaren rapporterar data bearbetas per klientdel.| Medel |
| Paketet räknare |  Offentliga och interna LB | Standard belastningsutjämnaren rapporterar de paket som bearbetas per klientdel.| Medel |

### <a name="view-load-balancer-metrics-in-the-portal"></a>Visa belastningsutjämnaren mått i portalen

Azure-portalen visar belastningsutjämnaren mätvärden via sidan mått (förhandsgranskning), som är tillgänglig på sidan Belastningsutjämningsresurs för en viss resurs för belastningsutjämnaren och även i Azure-Monitor-sidan. 

Om du vill visa måtten för Standard belastningsutjämnare resurser, bläddra av mått (förhandsversion) på någon av dessa platser väljer de (belastningsutjämningsresurs om under övervakaren sida) mått skriver du listrutan, ange lämpliga Aggregeringstyp och (valfritt) Konfigurera nödvändiga filtrering och gruppering och du kommer att kunna visa historiska för mått på de angivna villkor.

![Mått förhandsgranskning för Standard belastningsutjämnare](./media/load-balancer-standard-diagnostics/LBMetrics1.png)

*Bild - DIP tillgänglighet / hälsotillstånd avsökning status mått för belastningsutjämnare*

### <a name="retrieve-multi-dimensional-metrics-programmatically-via-apis"></a>Hämta flerdimensionella mätvärden via programmering via API: er

API-vägledning för att hämta Multi-Dimensional mått definitioner och värden finns på [övervakning REST API Walkthrouh > Multi-Dimensional API](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough#retrieve-metric-definitions-multi-dimensional-api)

### <a name = "DiagnosticScenarios"></a>Vanliga scenarier för diagnostik- och rekommenderade vyer

#### <a name="is-the-data-path-up-and-available-for-my-load-balancer-vip"></a>Är datasökvägen uppdaterad och tillgänglig för min VIP för belastningsutjämnare?

VIP-mått för tillgänglighet beskriver hälsotillstånd datasökväg för regionen till compute-värden där din virtuella dator finns. Det är en reflection-hälsotillståndet för Azure-infrastrukturen. Du kan använda det här måttet till:
- Övervaka externa tillgängligheten för din tjänst
- Gå på djupet och förstå om den plattform som din tjänst har distribuerats är felfri eller om ditt operativsystem eller programinstansen är felfria
- Ta reda på om en händelse är relaterad till din tjänst eller av underliggande data. Blanda inte ihop det med avsökning hälsostatus (”DIP tillgänglighet”).

Hämta VIP tillgängligheten för dina resurser Standard belastningsutjämnare:
- Kontrollera att rätt belastningsutjämnaren resursen är markerad. 
- Välj **VIP tillgänglighet** från den **mått** listrutan. 
- Välj **Avg** för **aggregering**. 
- Dessutom, lägga till filter på VIP-adressen eller VIP-porten som dimensionen med nödvändiga klientdelens IP-adress eller klientdelsport och gruppen av den valda dimensionen.

![VIP-sökning](./media/load-balancer-standard-diagnostics/LBMetrics-VIPProbing.png)

*Bild - avsökning av information VIP för belastningsutjämnare*

Måttet har genererats av en aktiv, in-band-mätning. En avsöknings tjänst för regionen kommer trafik för denna mätning och aktiveras när du skapar en distribution med en offentlig klientdels och fortsätter tills du tar bort klientdelen. 

>[!NOTE]
>Internt frontends stöds inte just nu. 

Ett paket som matchar din distribution klientdel och regeln genereras med jämna mellanrum. Den passerar regionen från källan till värden där det finns en virtuell dator i serverdelspoolen. Belastningsutjämnare infrastrukturen utför samma load balancing och översättning åtgärder som för all annan trafik. Den här avsökningen är i band belastningsutjämnad slutpunktsuppsättning på din belastningen. När avsökningen anländer på beräknings-värden där det finns en felfri virtuell dator i serverdelspoolen genererar beräkning värden ett svar till avsöknings-tjänsten. Den virtuella datorn kan inte se den här trafiken.
VIP-tillgänglighet misslyckas av följande skäl:
- Distributionen har inga felfri virtuella datorer i serverdelspoolen. 
- Ett avbrott i infrastrukturen har uppstått som orsakar VIP tillgänglighet misslyckas.

Du kan använda den [VIP mått för tillgänglighet tillsammans med avsökning hälsostatus för att ställa diagnoser](#vipavailabilityandhealthprobes).

Använd **genomsnittliga** som sammanställning för de flesta scenarier.

#### <a name="are-the-backend-instances-for-my-vip-responding-to-probes"></a>Backend-instanser för min VIP svarar på avsökningar?

Hälsotillstånd avsökningen status måttet beskriver hälsotillståndet för programdistributionen som konfigureras av dig när du konfigurerar hälsoavsökningen av belastningsutjämnare. Belastningsutjämnare använder status för avsökningen hälsotillstånd för att avgöra var du vill skicka nya flöden. Hälsotillståndsavsökning kommer från en adress för Azure-infrastrukturen och är synlig i gästoperativsystemet för den virtuella datorn.

Att hämta DIP tillgängligheten för resurserna Standard belastningsutjämnare
- Välj den **DIP tillgänglighet** mått med **Avg** Aggregeringstyp. 
- Använda ett filter på krävs VIP IP-adressen och porten (eller båda).

![DIP tillgänglighet](./media/load-balancer-standard-diagnostics/LBMetrics-DIPAvailability.png)

*Bild - tillgänglighet VIP för belastningsutjämnare*

Hälsoavsökningar misslyckas av följande skäl:
- Om du konfigurerar en hälsoavsökningen till en port som inte lyssnar eller inte svarar eller med fel-protokollet. Om din tjänst använder regler för direkt Serverreturnering (flytande IP), måste du se till att tjänsten lyssnar på IP-adressen för Nätverkskortets IP-konfiguration och inte bara på loopback har konfigurerats med klientdelen IP-adress.
- Om din avsökningen inte är tillåtet Nätverkssäkerhetsgruppen måste filtrerar den virtuella datorn gäst OS brandvägg eller Applikationsnivån.

Använd **genomsnittliga** som sammanställning för de flesta scenarier.

#### <a name="how-do-i-check-my-outbound-connection-statistics"></a>Hur jag för att kontrollera min utgående anslutningsstatistik? 

SNAT anslutningar måttet beskriver volymen av lyckade och misslyckade (för [utgående flöden](https://aka.ms/lboutbound)).

En misslyckad anslutningar volym som är större än noll indikerar SNAT port resursuttömning. Du måste undersöka vidare och bestämma vad orsakar dessa fel. SNAT port uttömning manifestet som kodfel att upprätta en [utgående flöden](https://aka.ms/lboutbound). Granska artikeln på utgående anslutningar för att förstå vilka scenarier och metoder i arbetet och hur du minimera / design för att undvika SNAT port resursuttömning. 

Att hämta SNAT anslutningsstatistik
- Välj **SNAT anslutningar** måttypen och **summan** som aggregering. 
- grupp av **anslutningsstatus** för lyckade och misslyckade SNAT anslutning räknar representeras av olika rader. 

![SNAT anslutning](./media/load-balancer-standard-diagnostics/LBMetrics-SNATConnection.png)

*Bild - SNAT anslutning antal för belastningsutjämnare*


#### <a name="how-do-i-check-inbound--outbound-connection-attempts-for-my-service"></a>Hur kontrollerar inkommande / utgående anslutningsförsök för min tjänsten?

SYN paket måttet beskriver mängden TCP SYN-paket som har tagits emot eller skickats (för [utgående flöden](https://aka.ms/lboutbound)) som är associerade med en viss klientdel. Det här måttet kan användas för att förstå TCP-anslutningsförsök till din tjänst.
Använd totalt som sammanställning för de flesta scenarier.

![SYN anslutning](./media/load-balancer-standard-diagnostics/LBMetrics-SYNCount.png)

*Bild - SYN antal för belastningsutjämnare*


#### <a name="how-do-i-check-my-network-bandwidth-consumption"></a>Hur kontrollerar jag min förbrukningen av nätverksbandbredd 

Mottagna byte / paket räknare måttet beskriver mängden byte och paket som skickas eller tas emot av tjänsten på grundval av per klientdel.
Använd **totala** som sammanställning för de flesta scenarier.

Hämta paket eller Byte sammanställd statistik
- Välj den **byte antal** och / eller **antal** måttypen med **Avg** som aggregering. 
- Filtret på specifika klientdels-IP, klientdelsport eller serverdels-IP och port i fråga. 
- eller hämta totala statistiken för Belastningsutjämningsresurs utan någon filtrering.


Vissa exempel vyer för mått med olika konfigurationer-

![Byteantal](./media/load-balancer-standard-diagnostics/LBMetrics-ByteCount.png)

*Bild - antal Byte för belastningsutjämnare*

#### <a name = "vipavailabilityandhealthprobes"></a>Hur jag för att diagnostisera distribution med belastningsutjämnare?

Kombinationen av VIP tillgänglighet & Hälsoavsökning mått i ett enda diagram gör att du kan identifiera var du vill titta på problemet och åtgärda problemet. Du kan få försäkran om att Azure fungerar och används för att bestämma säkerhet konfigurationen eller program är rotorsaken.

Du kan använda hälsa avsökningen mått för att förstå hur Azure visar hälsotillståndet för din distribution enligt konfigurationen som du har angett. Titta på hälsoavsökningar är alltid bra det första steget i övervakning eller fastställer en orsak.

Du kan vidta ytterligare ett steg och använda VIP tillgänglighet mått för att få insyn i hur Azure visar hälsotillståndet för det underliggande data planet som ansvarar för din distribution. När du kombinerar båda mått kan du isolera där felet kan vara enligt beskrivningen i det här exemplet:

![VIP-diagnostik](./media/load-balancer-standard-diagnostics/LBMetrics-DIPnVIPAvailability.png)

*Bild - kombinera DIP och VIP mått för tillgänglighet*

Diagrammet visar följande information:
- Infrastrukturen som själva har felfri, den infrastruktur som är värd för dina virtuella datorer var tillgängligt och mer än en virtuell dator har placerats i serverdelen. Detta visas med blå spårningen för VIP-tillgänglighet, som visar till 100%. 
- Avsökningen hälsostatus (DIP tillgänglighet) är dock vid 0% i början av diagrammet som anges med orange spårningen. Området inringad i rött visar där hälsoavsökning (DIP tillgänglighet) blev felfri, och då kundens distribution var acceptera nya flöden.

Diagrammet tillåtna kunden att felsöka distributionen på egen hand utan att gissa eller kontakta support om det fanns andra problem som uppstår. Kundens tjänsten var inte tillgänglig eftersom hälsoavsökningar misslyckas på grund av felaktig konfiguration eller ett program med fel.

### <a name = "Limitations"></a>Begränsningar

- VIP-tillgänglighet är endast tillgänglig för offentliga frontends.

## <a name = "ResourceHealth"></a>Resurshälsotillståndets status

Hälsostatus för resurserna som Standard belastningsutjämnare är åtkomlig via den befintliga **resurshälsa** under **övervakaren > tjänstens hälsa**.

>[!NOTE]
>Resurshälsa för belastningsutjämnaren är tillgänglig för offentliga konfiguration av endast Standard belastningsutjämnaren. Interna belastningsutjämnare eller grundläggande SKU belastningsutjämnaren resurser exponerar inte resurshälsa.

Visa hälsotillståndet för din offentliga Standard belastningsutjämnaren resurser
 - Bläddra till **övervakaren > tjänsten hälsa**.

  ![Övervakare för sidan](./media/load-balancer-standard-diagnostics/LBHealth1.png)

   *Bild - tjänstens hälsa på Azure-Monitor*

 - Välj **Resurshälsa** och kontrollera att rätt som **prenumerations-ID** och **resurstyp = belastningsutjämnaren** är markerad.

  ![Resurshälsotillståndets status](./media/load-balancer-standard-diagnostics/LBHealth3.png)

   *Bild - Välj resurs för hälsotillståndsvy*

 - Klicka på belastningsutjämnaren resurs från listan för att visa deras historiska hälsostatus.

    ![Läsa in hälsostatus för belastningsutjämnare](./media/load-balancer-standard-diagnostics/LBHealth4.png)

   *Bild - belastningsutjämnaren resource health vy*
 
I följande tabell visas olika resurshälsostatus och deras beskrivningar. 

| Resurshälsotillståndets Status | Beskrivning |
| --- | --- |
| Tillgänglig | Din offentliga Standard belastningsutjämnaren resurs är felfri och tillgänglig |
| Ej tillgänglig | Din offentliga Standard belastningsutjämnaren resursen är inte felfri. Diagnostisera hälsa via Azure-Monitor > mått. (*Detta kan också innebära att resursen inte är offentlig Standard belastningsutjämnaren*) |
| Okänt | Resource Health för din offentliga Standard belastningsutjämnaren har inte uppdaterats ännu. (*Detta kan också innebära att resursen inte är offentlig Standard belastningsutjämnaren*)  |

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [Standard belastningsutjämnare](load-balancer-standard-overview.md)
- Lär dig mer om [Load Balancer utgående anslutning](https://aka.ms/lboutbound)


