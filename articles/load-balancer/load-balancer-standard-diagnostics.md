---
title: Azure Standard belastningsutjämnaren diagnostik | Microsoft Docs
description: Använd informationen tillgängliga mått och hälsa för diagnostik för Azure Standard belastningsutjämnaren.
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
ms.openlocfilehash: 9d5d596254f673b86650e8d9754dacdb70be0666
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
ms.locfileid: "32179802"
---
# <a name="metrics-and-health-diagnostics-for-standard-load-balancer"></a>Mått hälsa och diagnostik för Standard belastningsutjämnare

Azure belastningsutjämnare som Standard ger dina resurser följande diagnostiska funktioner:
* **Flerdimensionella mått**: ger nya flerdimensionella diagnostiska funktioner för både offentliga och interna belastningsutjämnare konfigurationer. Du kan övervaka, hantera och felsöka belastningen belastningsutjämnaren resurserna.

* **Resurshälsa**: det belastningsutjämnare i Azure portal och Resource Health-sida (under övervakning) exponera Resource Health-avsnittet för den offentliga konfigurationen av belastningsutjämning för Standard belastningsutjämnare.

Den här artikeln innehåller en snabb genomgång av dessa funktioner och det finns olika sätt att använda dem för Standard belastningsutjämnaren.

## <a name = "MultiDimensionalMetrics"></a>Flerdimensionella mått

Azure belastningsutjämnare ger nya flerdimensionella mätvärden via den nya Azure-mått (förhandsgranskning) i Azure-portalen och det hjälper dig att hämta realtid diagnostiska insikter om dina belastningen belastningsutjämnaren resurser. 

De olika konfigurationerna som Standard belastningsutjämnaren innehåller följande mått:

| Mått | Resurstyp | Beskrivning | Rekommenderade aggregering |
| --- | --- | --- | --- |
| VIP-tillgänglighet (sökväg datatillgänglighet) | Offentlig belastningsutjämnare | Standard belastningsutjämnare använder kontinuerligt datasökväg från inom en region att läsa in belastningsutjämning klientdelen ända till stacken SDN som har stöd för den virtuella datorn. Så länge felfri instanser förblir följer mätningen samma sökväg som ditt program belastningsutjämnad trafik. Datasökväg för dina kunder använder också valideras. Måttet är osynliga för ditt program och störa inte andra åtgärder.| Medel |
| DIP tillgänglighet (hälsostatus för avsökning) |  Offentliga och interna belastningsutjämnare | Standard belastningsutjämnare använder en distribuerad avsökning av hälsotillstånd tjänst som övervakar dina program endpoint hälsa enligt inställningarna. Det här måttet innehåller en mängd- eller per slutpunkt filtrerad vy för varje instans slutpunkt i poolen belastningen belastningsutjämnaren. Du kan se hur belastningsutjämnaren visar hälsotillståndet för programmet, som anges av din konfiguration för avsökning av hälsotillstånd. |  Medel |
| SYN (synkronisera) paket |  Offentlig belastningsutjämnare | Standard belastningsutjämnaren inte avsluta (TCP) anslutningar och interagera med TCP eller UDP-paket-flöden. Flöden och deras handskakningar på är alltid mellan käll- och VM-instans. Om du vill felsöka bättre scenariet TCP-protokollet, kan du använda SYN paket räknare för att förstå hur många TCP-anslutning görs försök. Måttet rapporterar antalet TCP SYN-paket som tagits emot.| Medel |
| SNAT anslutningar |  Offentlig belastningsutjämnare |Standard belastningsutjämnaren rapporterar antalet utgående flöden masqueraded till klientdelen för offentlig IP-adress. Källportar network adress translation (SNAT) är en icke förnybara resurs. Det här måttet kan ge en indikation på hur mycket tillämpningsprogrammet förlitar sig på SNAT för utgående flöden som har sitt ursprung. Räknare för lyckade och misslyckade utgående SNAT flöden rapporteras och kan användas för att felsöka och förstå hälsotillståndet för din utgående flöden.| Medel |
| Räknare för byte |  Offentliga och interna belastningsutjämnare | Standard belastningsutjämnaren rapporterar data bearbetas per klient.| Medel |
| Paketet räknare |  Offentliga och interna belastningsutjämnare | Standard belastningsutjämnaren rapporterar de paket som bearbetas per klient.| Medel |

### <a name="view-your-load-balancer-metrics-in-the-azure-portal"></a>Visa din belastningen belastningsutjämnaren mätvärden i Azure-portalen

Azure-portalen visar belastning belastningsutjämnaren mätvärden via sidan mått (förhandsgranskning), som finns på båda belastningen belastningsutjämnaren resurssidan för en viss resurs och Azure-Monitor-sidan. 

Visa mått för dina resurser Standard belastningsutjämnare:
1. Gå till sidan mått (förhandsversion) och gör något av följande:
   * Välj måttypen i den nedrullningsbara listan på sidan belastningen belastningsutjämnaren resurs.
   * Välj belastningsutjämningsresurs på Azure-Monitor-sidan.
2. Ange lämpliga Aggregeringstyp.
3. Du kan också konfigurera nödvändiga filtrering och gruppering.

![Mått förhandsgranskning för Standard belastningsutjämnare](./media/load-balancer-standard-diagnostics/LBMetrics1.png)

*Bild: DIP tillgänglighet och hälsotillstånd avsökningen status mått för Standard belastningsutjämnaren*

### <a name="retrieve-multi-dimensional-metrics-programmatically-via-apis"></a>Hämta flerdimensionella mätvärden via programmering via API: er

API-vägledning för att hämta flerdimensionella måttdefinitioner och värden finns i [Azure övervakning REST API genomgången](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough#retrieve-metric-definitions-multi-dimensional-api).

### <a name = "DiagnosticScenarios"></a>Vanliga scenarier för diagnostik- och rekommenderade vyer

#### <a name="is-the-data-path-up-and-available-for-my-load-balancer-vip"></a>Är datasökväg in och är tillgänglig för min VIP för belastningsutjämnare?

VIP tillgänglighet måttet beskriver hälsotillstånd datasökväg för regionen till compute-värden där din virtuella dator finns. Måttet är reflektion av hälsotillståndet för Azure-infrastrukturen. Du kan använda mått till:
- Övervaka externa tillgängligheten för din tjänst
- Gå på djupet och förstå om den plattform som din tjänst har distribuerats är felfri eller om ditt operativsystem eller programinstansen är felfri.
- Ta reda på om en händelse är relaterad till din tjänst eller av underliggande data. Blanda inte ihop det här måttet med avsökning hälsostatus (”DIP tillgänglighet”).

Hämta VIP tillgängligheten för dina resurser Standard belastningsutjämnare:
1. Kontrollera att rätt belastningsutjämningsresurs är markerad. 
2. I den **mått** listrutan, Välj **VIP tillgänglighet**. 
3. I den **aggregering** listrutan, Välj **Avg**. 
4. Dessutom kan lägga till ett filter på VIP-adressen eller VIP-porten som dimensionen med nödvändiga frontend IP-adress eller frontend-port och gruppera dem efter den valda dimensionen.

![VIP-sökning](./media/load-balancer-standard-diagnostics/LBMetrics-VIPProbing.png)

*Bild: VIP för belastningsutjämnare avsökning av information*

Måttet har genererats av en aktiv, in-band-mätning. En sökning tjänst för regionen kommer trafik för måttet. Tjänsten är aktiverad när du skapar en distribution med en offentlig klientdel och fortsätter tills du tar bort klientdelen. 

>[!NOTE]
>Internt frontwebbservrarna stöds inte just nu. 

Ett paket som matchar din distribution klientdelen och regeln genereras med jämna mellanrum. Den passerar regionen från källan till värden där det finns en virtuell dator i backend-poolen. Läs in belastningsutjämnaren infrastruktur utför samma belastningen belastningsutjämning och översättning åtgärder som för all annan trafik. Den här avsökningen är i band på slutpunkten Utjämning av nätverksbelastning. När avsökningen anländer på beräknings-värden där det finns en felfri VM i backend-poolen, genererar ett svar till tjänsten avsöknings beräknings-värden. Den virtuella datorn kan inte se den här trafiken.

VIP-tillgänglighet misslyckas av följande skäl:
- Distributionen har ingen felfri VMs kvar i backend-poolen. 
- Det uppstod ett avbrott för infrastruktur.

Du kan använda för att ställa diagnoser den [VIP mått för tillgänglighet tillsammans med avsökning hälsostatus](#vipavailabilityandhealthprobes).

Använd **genomsnittliga** som sammanställning för de flesta scenarier.

#### <a name="are-the-back-end-instances-for-my-vip-responding-to-probes"></a>Backend-instanser för min VIP svarar på avsökningar?

Hälsotillstånd avsökningen status måttet beskriver hälsotillståndet för programdistributionen som konfigureras av dig när du konfigurerar hälsoavsökningen av din belastningsutjämnare. Belastningsutjämnaren använder status för avsökningen hälsotillstånd för att avgöra var du vill skicka nya flöden. Hälsotillståndsavsökning kommer från en adress för Azure-infrastrukturen och är synlig i gästoperativsystemet för den virtuella datorn.

Hämta DIP tillgängligheten för dina resurser Standard belastningsutjämnare:
1. Välj den **DIP tillgänglighet** mått med **Avg** Aggregeringstyp. 
2. Använda ett filter på krävs VIP IP-adressen och porten (eller båda).

![DIP tillgänglighet](./media/load-balancer-standard-diagnostics/LBMetrics-DIPAvailability.png)

*Bild: VIP för belastningsutjämnare tillgänglighet*

Hälsoavsökningar misslyckas av följande skäl:
- Du konfigurerar en hälsoavsökningen till en port som inte lyssnar eller inte svarar eller använder fel-protokollet. Om din tjänst använder direkt serverretur (DSR eller flytande IP) regler, se till att tjänsten lyssnar på IP-adressen för Nätverkskortets IP-konfiguration och inte bara på loopback som är konfigurerad med frontend IP-adress.
- Din avsökningen tillåts inte av Nätverkssäkerhetsgruppen, den virtuella datorn gäst OS brandvägg eller application layer-filter.

Använd **genomsnittliga** som sammanställning för de flesta scenarier.

#### <a name="how-do-i-check-my-outbound-connection-statistics"></a>Hur jag för att kontrollera min utgående anslutningsstatistik? 

SNAT anslutningar måttet beskriver volymen av lyckade och misslyckade anslutningar för [utgående flöden](https://aka.ms/lboutbound).

En misslyckad anslutningar volym som är större än noll indikerar SNAT port resursuttömning. Du måste undersöka vidare för att avgöra vad orsakar dessa fel. SNAT port uttömning manifestet som inte går att upprätta en [utgående flödet](https://aka.ms/lboutbound). Läsa artikeln om utgående anslutningar för att förstå de scenarier och metoder i arbetet, och för att lära dig hur du minskar och utforma för att undvika SNAT port resursuttömning. 

Hämta SNAT anslutningsstatistik:
1. Välj **SNAT anslutningar** måttypen och **summan** som aggregering. 
2. Gruppera efter **anslutningsstatus** för lyckade och misslyckade SNAT antal för anslutningen som representeras av olika rader. 

![SNAT anslutning](./media/load-balancer-standard-diagnostics/LBMetrics-SNATConnection.png)

*Bild: Load Balancer SNAT anslutningsräknare*


#### <a name="how-do-i-check-inboundoutbound-connection-attempts-for-my-service"></a>Hur kontrollerar inkommande/utgående anslutningsförsök för min tjänsten?

SYN paket måttet beskriver mängden TCP SYN-paket som har tagits emot eller skickats (för [utgående flöden](https://aka.ms/lboutbound)) som är associerade med en specifik klientdel. Du kan använda det här måttet för att förstå TCP-anslutningsförsök till din tjänst.

Använd **totala** som sammanställning för de flesta scenarier.

![SYN anslutning](./media/load-balancer-standard-diagnostics/LBMetrics-SYNCount.png)

*Bild: Läs in belastningsutjämnaren SYN antal*


#### <a name="how-do-i-check-my-network-bandwidth-consumption"></a>Hur kontrollerar jag min förbrukningen av nätverksbandbredd 

Byte och paket räknare måttet beskriver mängden byte och paket som skickas eller tas emot av tjänsten på grundval av per framför-slutpunkt.

Använd **totala** som sammanställning för de flesta scenarier.

Hämta paket eller byte sammanställd statistik:
1. Välj den **byte antal** och/eller **antal** måttypen, med **Avg** som aggregering. 
2. Gör något av följande:
   * Tillämpa ett filter på en specifik frontend IP frontend-port, backend-IP- eller backend-port.
   * Hämta totala statistiken för dina belastningsutjämningsresurs utan någon filtrering.

![Byteantal](./media/load-balancer-standard-diagnostics/LBMetrics-ByteCount.png)

*Bild: Byteantal för belastningsutjämnare*

#### <a name = "vipavailabilityandhealthprobes"></a>Hur jag för att diagnostisera min belastningen belastningsutjämnaren distributionen?

Du kan identifiera var du vill titta på problemet och åtgärda problemet genom att använda en kombination av VIP tillgänglighet och hälsotillstånd avsökningen mått på ett enda diagram. Du kan få försäkran om att Azure fungerar korrekt och använda den här informationen för att fastställa säkerhet konfiguration eller program är rotorsaken.

Du kan använda hälsa avsökningen mått för att förstå hur Azure visar hälsotillståndet för din distribution enligt konfigurationen som du har angett. Titta på hälsoavsökningar är alltid bra det första steget i övervakning eller fastställer en orsak.

Du kan ta ett steg ytterligare och använda VIP tillgänglighet mått för att få insyn i hur Azure visar hälsotillståndet för det underliggande dataplan som ansvarar för din distribution. När du kombinerar båda mått kan du isolera där felet kan vara, enligt beskrivningen i det här exemplet:

![VIP-diagnostik](./media/load-balancer-standard-diagnostics/LBMetrics-DIPnVIPAvailability.png)

*Bild: Kombinera DIP och VIP mått för tillgänglighet*

Diagrammet visar följande information:
- Infrastrukturen som själva har felfri, den infrastruktur som är värd för dina virtuella datorer var tillgängligt och mer än en virtuell dator har placerats i serverdelen. Den här informationen visas med blå spårningen för VIP-tillgänglighet, vilket är 100 procent. 
- Avsökningen hälsostatus (DIP tillgänglighet) är dock på 0 procent i början av diagram, vilket indikeras med orange spårningen. Området inringad i grön visar där status (DIP tillgänglighet) blev felfri, och vid vilken kundens distribution kunde acceptera nya flöden.

Diagrammet möjligheten för kunder att felsöka distributionen på egen hand utan att gissa eller kontakta support om andra problem uppstår. Tjänsten kunde inte tillgänglig eftersom hälsoavsökningar misslyckas på grund av felaktig konfiguration eller ett program med fel.

### <a name = "Limitations"></a>Begränsningar

VIP-tillgänglighet är endast tillgänglig för offentliga frontwebbservrarna.

## <a name = "ResourceHealth"></a>Resurshälsotillståndets status

Hälsostatus för resurserna som Standard belastningsutjämnare är åtkomlig via den befintliga **resurshälsa** under **övervakaren > tjänstens hälsa**.

>[!NOTE]
>Resurshälsotillståndets status för belastningsutjämnaren är tillgänglig för offentliga konfiguration av belastningsutjämnare som Standard bara. Interna belastningsutjämnare eller grundläggande SKU: er av belastningsutjämnare resurser exponerar inte resurshälsa.

Visa dina offentliga Standard belastningsutjämnaren-resursers hälsa:
1. Välj **övervakaren** > **tjänsten hälsa**.

   ![Övervakare för sidan](./media/load-balancer-standard-diagnostics/LBHealth1.png)

   *Bild: Tjänstens hälsa länken på Azure-Monitor*

2. Välj **Resurshälsa**, och kontrollera att **prenumerations-ID** och **resurstyp = belastningsutjämnaren** har valts.

   ![Resurshälsotillståndets status](./media/load-balancer-standard-diagnostics/LBHealth3.png)

   *Bild: Välj resurs för hälsotillståndsvy*

3. Markera resursen belastningsutjämnare för att visa dess historiska hälsostatus i listan.

    ![Läsa in hälsostatus för belastningsutjämnare](./media/load-balancer-standard-diagnostics/LBHealth4.png)

   *Bild: Belastningsutjämnaren resource health vy*
 
Olika resource health statusvärden och beskrivningar finns i följande tabell: 

| Resurshälsotillståndets status | Beskrivning |
| --- | --- |
| Tillgänglig | Din offentliga standard belastningsutjämningsresurs är felfri och tillgängliga. |
| Ej tillgänglig | Din offentliga standard belastningsutjämningsresurs är inte felfri. Diagnostisera hälsa genom att välja **Azure-Monitor** > **mått**.<br>(*Ej tillgänglig* status kan också innebära att resursen inte är kopplad till en offentlig belastningsutjämnare standard.) |
| Okänt | Resurshälsotillståndets status för din offentliga standard belastningsutjämningsresurs har inte uppdaterats ännu.<br>(*Okänd* status kan också innebära att resursen inte är kopplad till en offentlig belastningsutjämnare standard.)  |

## <a name="next-steps"></a>Nästa steg

- Mer information finns i [Standardbelastningsutjämnare](load-balancer-standard-overview.md).
- Lär dig mer om din [belastningsutjämnarens utgående anslutning för att läsa in](https://aka.ms/lboutbound).


