---
title: Diagnostik med mått, aviseringar och resurshälsa - Azure Standard Load Balancer
description: Använd tillgänglig mått, aviseringar och resurshälsoinformation för att diagnostisera din Azure Standard Load Balancer.
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
ms.openlocfilehash: 861961bb66adc7ed9509eab973516a964cb67492
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521058"
---
# <a name="standard-load-balancer-diagnostics-with-metrics-alerts-and-resource-health"></a>Standard Load Balancer-diagnostik med mått, aviseringar och resurshälsa

Azure Standard Load Balancer visar följande diagnostikfunktioner:

* **Flerdimensionella mått och aviseringar**: Tillhandahåller flerdimensionella diagnostikfunktioner via [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) för standardbelastningsutjämnarekonfigurationer. Du kan övervaka, hantera och felsöka dina standardbelastningsutjämnareresurser.

* **Resurshälsa**: Sidan Belastningsutjämnare i Azure-portalen och sidan Resurshälsa (under Övervakar) visar avsnittet Resurshälsa för standardbelastningsutjämning. 

Den här artikeln innehåller en snabb rundtur i dessa funktioner, och den erbjuder sätt att använda dem för Standard Load Balancer. 

## <a name="multi-dimensional-metrics"></a><a name = "MultiDimensionalMetrics"></a>Flerdimensionella mätvärden

Azure Load Balancer tillhandahåller flerdimensionella mått via Azure-måtten i Azure-portalen, och det hjälper dig att få diagnostiska insikter i realtid i dina belastningsbalansresurser. 

De olika standardbelastningsutjämningskonfigurationerna innehåller följande mått:

| Mått | Resurstyp | Beskrivning | Rekommenderad aggregering |
| --- | --- | --- | --- |
| Tillgänglighet för datasökväg (VIP-tillgänglighet)| Offentlig och intern belastningsutjämnare | Standardbelastningsutjämnaren tränar kontinuerligt datasökvägen inifrån en region till belastningsutjämnarfronten, hela vägen till SDN-stacken som stöder den virtuella datorn. Så länge felfria instanser finns kvar följer mätningen samma sökväg som programmets belastningsbalanserade trafik. Den datasökväg som dina kunder använder valideras också. Mätningen är osynlig för ditt program och stör inte andra åtgärder.| Medel |
| Hälsoavsökningsstatus(DIP-tillgänglighet) | Offentlig och intern belastningsutjämnare | Standardbelastningsutjämnaren använder en distribuerad hälsoavsökningstjänst som övervakar programmets slutpunkts hälsotillstånd enligt dina konfigurationsinställningar. Det här måttet ger en samlad eller filtrerad vy per slutpunkt för varje instansslutpunkt i belastningsutjämnaren. Du kan se hur belastningsutjämnaren visar hälsotillståndet för ditt program, vilket indikeras av hälsoavsökningskonfigurationen. |  Medel |
| SYN-paket (synkronisera) | Offentlig och intern belastningsutjämnare | Standardbelastningsutjämnar inte TCP-anslutningar (Transmission Control Protocol) eller interagerar med TCP- eller UDP-paketflöden. Flöden och deras handslag är alltid mellan källan och VM-instansen. Om du vill felsöka dina TCP-protokollscenarier kan du använda SYN-paketräknare för att förstå hur många TCP-anslutningsförsök som görs. Måttet rapporterar antalet TCP SYN-paket som togs emot.| Medel |
| SNAT-anslutningar | Offentlig belastningsutjämnare |Standardbelastningsutjämnaren rapporterar antalet utgående flöden som är maskerade till den offentliga IP-adressens klientdel. SNAT-portar (Source Network Address Translation) är en uttömlig resurs. Det här måttet kan ge en indikation på hur tungt ditt program är beroende av SNAT för utgående ursprungsflöden. Räknare för lyckade och misslyckade utgående SNAT-flöden rapporteras och kan användas för att felsöka och förstå hälsotillståndet för dina utgående flöden.| Medel |
| Tilldelade SNAT-portar | Offentlig belastningsutjämnare | Standardbelastningsutjämnare rapporterar antalet SNAT-portar som allokerats per serverdelsinstans | Genomsnittliga. |
| Använda SNAT-portar | Offentlig belastningsutjämnare | Standardbelastningsutdelningsrapporter antalet SNAT-portar som används per serverd-instans. | Medel | 
| Byte räknare |  Offentlig och intern belastningsutjämnare | Standardbelastningsutjämnare rapporterar de data som bearbetas per frontend. Du kanske märker att bytena inte fördelas lika mellan serverdelsinstanserna. Detta förväntas eftersom Azures load balancer-algoritm baseras på flöden | Medel |
| Paketräknare |  Offentlig och intern belastningsutjämnare | Standardbelastningsutjämnaren rapporterar de paket som bearbetas per frontend.| Medel |

### <a name="view-your-load-balancer-metrics-in-the-azure-portal"></a>Visa dina belastningsutjämnaremått i Azure-portalen

Azure-portalen visar belastningsutjämnarmåtten via sidan Mått, som är tillgänglig på både belastningsutjämnarresurssidan för en viss resurs och sidan Azure Monitor. 

Så här visar du måtten för standardbelastningsutjämningsresurserna:
1. Gå till sidan Mått och gör något av följande:
   * På sidan belastningsutjämnareresurs väljer du måtttypen i listrutan.
   * På sidan Azure Monitor väljer du belastningsutjämnarens resurs.
2. Ange lämplig måttaggregeringstyp.
3. Du kan också konfigurera den nödvändiga filtrningen och gruppningen.
4. Du kan också konfigurera tidsintervall och aggregering. Som standard visas tid i UTC.

  >[!NOTE] 
  >Tidsaggregering är viktigt när du tolkar vissa mått eftersom data samplas en gång per minut. Om tidsaggregering är inställd på fem minuter och måttaggregeringstyp Summa används för mått som SNAT-allokering, visar diagrammet fem gånger den totala allokerade SNAT-portarna. 

![Mått för standardbelastningsbalanser](./media/load-balancer-standard-diagnostics/lbmetrics1anew.png)

*Bild: Tillgänglighetsmått för datasökväg för standardbelastningsutjämning*

### <a name="retrieve-multi-dimensional-metrics-programmatically-via-apis"></a>Hämta flerdimensionella mått programmässigt via API:er

Api-vägledning för att hämta flerdimensionella måttdefinitioner och värden finns i [Azure Monitoring REST API-genomgång](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough#retrieve-metric-definitions-multi-dimensional-api). Dessa mått kan bara skrivas till ett lagringskonto via alternativet "Alla mått". 

### <a name="common-diagnostic-scenarios-and-recommended-views"></a><a name = "DiagnosticScenarios"></a>Vanliga diagnostiska scenarier och rekommenderade vyer

### <a name="configure-alerts-for-multi-dimensional-metrics"></a>Konfigurera aviseringar för flerdimensionella mått ###

Azure Standard Load Balancer stöder enkelt konfigurerbara aviseringar för flerdimensionella mått. Konfigurera anpassade tröskelvärden för specifika mått för att utlösa aviseringar med varierande allvarlighetsgrad för att ge en beröringslös resursövervakningsupplevelse.

Så här konfigurerar du varningar:
1. Gå till varningsunderbladet för lastjämföraren
1. Skapa en ny aviseringsregel
    1.  Konfigurera varningsvillkor
    1.  (Valfritt) Lägga till åtgärdsgrupp för automatisk reparation
    1.  Tilldela allvarlighetsgrad, namn och beskrivning som möjliggör intuitiv reaktion


  >[!NOTE]
  >Konfigurationsfönstret för varningsvillkor visar tidsserier för signalhistorik. Det finns ett alternativ för att filtrera den här tidsserien efter dimensioner som Serverda IP. Detta filtrerar tidsseriediagrammet men **inte** själva aviseringen. Du kan inte konfigurera aviseringar för specifika serverd-IP-adresser.
  
#### <a name="is-the-data-path-up-and-available-for-my-load-balancer-vip"></a>Är datasökvägen uppe och tillgänglig för min belastningsutjämnare VIP?

Vip-tillgänglighetsmåttet beskriver hälsotillståndet för datasökvägen i regionen till beräkningsvärden där dina virtuella datorer finns. Måttet är en återspegling av hälsotillståndet för Azure-infrastrukturen. Du kan använda måttet för att:
- Övervaka tjänstens externa tillgänglighet
- Gräva djupare och förstå om plattformen där din tjänst distribueras är felfri eller om gästoperativsystemet eller programinstansen är felfri.
- Isolera om en händelse är relaterad till din tjänst eller det underliggande dataplanet. Blanda inte ihop det här måttet med hälsoavsökningsstatusen ("DIP-tillgänglighet").

Så här hämtar du datasökvägstillgängligheten för standardbelastningsutjämningsresurserna:
1. Kontrollera att rätt belastningsutjämnad resurs är markerad. 
2. Välj Tillgänglighet för **datasökväg**i listrutan **Mått** . 
3. Välj **Medel**i listrutan **Aggregering** . 
4. Lägg dessutom till ett filter på frontend IP-adressen eller Frontend-porten som dimension med den erforderliga ip-adressen eller frontend-porten och gruppera dem efter den valda dimensionen.

![VIP-sondering](./media/load-balancer-standard-diagnostics/LBMetrics-VIPProbing.png)

*Figur: Belastningsutjämnare Frontend sondering detaljer*

Måttet genereras av en aktiv, in-band-mätning. En sonderingstjänst inom regionen har sitt ursprung i trafiken för mätningen. Tjänsten aktiveras så fort du skapar en distribution med en offentlig klientdel och den fortsätter tills du tar bort klientdelen. 

Ett paket som matchar distributionens klientdel och regel genereras med jämna mellanrum. Den korsar regionen från källan till värden där en virtuell dator i backend-poolen finns. Belastningsutjämnarinfrastrukturen utför samma belastningsutjämning och översättningsåtgärder som för all annan trafik. Den här avsökningen är i-bandet på din belastningsbalanserade slutpunkt. När avsökningen anländer till beräkningsvärden, där en felfri virtuell dator i backend-poolen finns, genererar beräkningsvärden ett svar på avsökningstjänsten. Den virtuella datorn ser inte den här trafiken.

VIP-tillgänglighet misslyckas av följande skäl:
- Distributionen har inga felfria virtuella datorer kvar i backend-poolen. 
- Ett avbrott i infrastrukturen har inträffat.

För diagnostikändamål kan du använda [måttet Tillgänglighet för datasökväg tillsammans med hälsoavsökningsstatusen](#vipavailabilityandhealthprobes).

Använd **Medel** som aggregering för de flesta scenarier.

#### <a name="are-the-back-end-instances-for-my-vip-responding-to-probes"></a>Svarar backend-instanserna för min VIP på sonder?

Hälsoavsökningsstatusmåttet beskriver hälsotillståndet för programdistributionen som konfigurerats av dig när du konfigurerar hälsoavsökningen för din belastningsutjämnare. Belastningsutjämnaren använder hälsoavsökningens status för att avgöra var nya flöden ska skickas. Hälsoavsökningar kommer från en Azure-infrastrukturadress och är synliga i gästoperativsystemet för den virtuella datorn.

Så här hämtar du hälsoavsökningsstatus för standardbelastningsutjämningsresurserna:
1. Välj måttet Status för **hälsoavsökning** med avg-aggregeringstyp. **Avg** 
2. Använd ett filter på önskad frontend IP-adress eller port (eller båda).

Hälsoavsökningar misslyckas av följande skäl:
- Du konfigurerar en hälsoavsökning till en port som inte lyssnar eller inte svarar eller använder fel protokoll. Om din tjänst använder DSR-regler (Direct Server return) eller flytande IP kontrollerar du att tjänsten lyssnar på IP-adressen för nätverkskortets IP-konfiguration och inte bara på loopback som konfigureras med klient-TV-adressen.
- Avsökningen är inte tillåten av nätverkssäkerhetsgruppen, den virtuella datorns gäst-OS-brandvägg eller programlagerfiltren.

Använd **Medel** som aggregering för de flesta scenarier.

#### <a name="how-do-i-check-my-outbound-connection-statistics"></a>Hur kollar jag min utgående anslutningsstatistik? 

SNAT-anslutningarna beskriver volymen för lyckade och misslyckade anslutningar för [utgående flöden](https://aka.ms/lboutbound).

En misslyckad anslutningsvolym på större än noll indikerar utmattning av SNAT-portar. Du måste undersöka ytterligare för att avgöra vad som kan orsaka dessa fel. SNAT-portutmatt manifesterar som ett misslyckande med att upprätta ett [utgående flöde](https://aka.ms/lboutbound). Läs artikeln om utgående anslutningar för att förstå scenarier och mekanismer på jobbet och lär dig hur du kan minska och utforma för att undvika utmattning av SNAT-portar. 

Så här hämtar du SNAT-anslutningsstatistik:
1. Välj **SNAT-anslutningsmåtttyp** och **Summa** som aggregering. 
2. Gruppera efter **anslutningstillstånd** för lyckade och misslyckade SNAT-anslutningsantal som representeras av olika rader. 

![SNAT-anslutning](./media/load-balancer-standard-diagnostics/LBMetrics-SNATConnection.png)

*Bild: Antal SNAT-anslutningar för belastningsutjämnare*


#### <a name="how-do-i-check-my-snat-port-usage-and-allocation"></a>Hur kontrollerar jag min SNAT-portanvändning och allokering?

SNAT-användningsmåttet anger hur många unika flöden som upprättas mellan en internetkälla och en serverad virtuell dator eller virtuell datorskalauppsättning som ligger bakom en belastningsutjämnare och inte har en offentlig IP-adress. Genom att jämföra detta med SNAT-allokeringsmåttet kan du avgöra om din tjänst har erfarenhet eller riskerar SNAT-utmattning och resulterande utgående flödesfel. 

Om dina mått indikerar risk för [utgående flödesfel](https://aka.ms/lboutbound) refererar du till artikeln och vidtar åtgärder för att minska detta för att säkerställa tjänstens hälsotillstånd.

Så här visar du SNAT-portanvändning och allokering:
1. Ställ in tidsaggregeringen för diagrammet till 1 minut för att säkerställa att önskade data visas.
1. Välj **SNAT-användning** och/eller **SNAT-allokering** som måtttyp och **Medel** som aggregering
    * Som standard är detta det genomsnittliga antalet SNAT-portar som allokerats till eller används av varje start-VM eller VMSSes, vilket motsvarar alla offentliga sidnummer som mappas till belastningsutjämnaren, aggregerade över TCP och UDP.
    * Om du vill visa totala SNAT-portar som används av eller allokerats för belastningsutjämnaren använder måttaggregering **Summa**
1. Filtrera till en viss **protokolltyp**, en uppsättning **serverdels-IP-adresser**och/eller **ip-adresser för klientdel**.
1. Om du vill övervaka hälsotillstånd per serverdels- eller klientdelsinstans använder du delning. 
    * Med delning av anteckningar kan endast ett enda mått visas åt gången. 
1. Om du till exempel vill övervaka SNAT-användning för TCP-flöden per dator, aggregera efter **medelvärde,** dela upp efter **serverdelars IP-adresser** och filtrera efter **protokolltyp**. 

![SNAT tilldelning och användning](./media/load-balancer-standard-diagnostics/snat-usage-and-allocation.png)

*Figur: Genomsnittlig TCP SNAT-portallokering och -användning för en uppsättning virtuella datorer med bakåtsträvning*

![SNAT-användning efter serverd-instans](./media/load-balancer-standard-diagnostics/snat-usage-split.png)

*Bild: TCP SNAT-portanvändning per serveradinstans*

#### <a name="how-do-i-check-inboundoutbound-connection-attempts-for-my-service"></a>Hur kontrollerar jag inkommande/utgående anslutningsförsök för min tjänst?

Ett SYN-paketmått beskriver volymen av TCP SYN-paket, som har anlänt eller skickats (för [utgående flöden)](https://aka.ms/lboutbound)som är associerade med en specifik klientdel. Du kan använda det här måttet för att förstå TCP-anslutningsförsök till din tjänst.

Använd **Summa** som aggregering för de flesta scenarier.

![SYN-anslutning](./media/load-balancer-standard-diagnostics/LBMetrics-SYNCount.png)

*Figur: Syn-antal belastningsutjämnare*


#### <a name="how-do-i-check-my-network-bandwidth-consumption"></a>Hur kontrollerar jag mobilens bandbredd? 

Måttet byte och paketräknare beskriver volymen av byte och paket som skickas eller tas emot av din tjänst per front-end-basis.

Använd **Summa** som aggregering för de flesta scenarier.

Så här hämtar du statistik över antal byte eller paket:
1. Välj måtttypen **Antal byte** och/eller **Antal paket,** med **Genomsnittlig** som aggregering. 
2. Gör något av följande:
   * Använd ett filter på en specifik front-end IP-, frontend-port, backend IP- eller backend-port.
   * Få övergripande statistik för din belastningsutjämnad resurs utan filtrering.

![Antal byte](./media/load-balancer-standard-diagnostics/LBMetrics-ByteCount.png)

*Bild: Antal belastningsutjämnare byte*

#### <a name="how-do-i-diagnose-my-load-balancer-deployment"></a><a name = "vipavailabilityandhealthprobes"></a>Hur diagnostiserar jag min belastningsutjämnad distribution?

Genom att använda en kombination av VIP-tillgänglighet och hälsoavsökningsmått på ett enda diagram kan du identifiera var du ska leta efter problemet och lösa problemet. Du kan få en försäkran om att Azure fungerar korrekt och använda den här kunskapen för att slutgiltigt fastställa att konfigurationen eller programmet är grundorsaken.

Du kan använda hälsoavsökningsmått för att förstå hur Azure visar hälsotillståndet för din distribution enligt den konfiguration du har angett. Att titta på hälso-sonder är alltid ett bra första steg i övervakning eller fastställande av en orsak.

Du kan ta det ett steg längre och använda VIP-tillgänglighetsmått för att få insikt i hur Azure visar hälsotillståndet för det underliggande dataplanet som är ansvarigt för din specifika distribution. När du kombinerar båda mätvärdena kan du isolera var felet kan vara, vilket illustreras i det här exemplet:

![Kombinera datasökvägstillgänglighet och statusmått för hälsoavsökning](./media/load-balancer-standard-diagnostics/lbmetrics-dipnvipavailability-2bnew.png)

*Bild: Kombinera datasökvägstillgänglighet och statusmått för hälsoavsökning*

Diagrammet visar följande information:
- Infrastrukturen som är värd för dina virtuella datorer var inte tillgänglig och 0 procent i början av diagrammet. Senare var infrastrukturen felfri och de virtuella datorerna kunde nås och mer än en virtuell dator placerades i den bakre delen. Den här informationen indikeras av den blå spårningen för datasökvägstillgänglighet (VIP-tillgänglighet), som senare var 100 procent. 
- Hälsoavsökningsstatusen (DIP-tillgänglighet), som indikeras av den lila spårningen, är 0 procent i början av diagrammet. Det inringade området i gröna högdagrar där hälsoavsökningsstatusen (DIP-tillgänglighet) blev felfri och då kunde kundens distribution acceptera nya flöden.

Diagrammet gör det möjligt för kunder att felsöka distributionen på egen hand utan att behöva gissa eller fråga support om andra problem uppstår. Tjänsten var inte tillgänglig eftersom hälsoavsökningar misslyckades på grund av antingen en felkonfiguration eller ett misslyckat program.

## <a name="resource-health-status"></a><a name = "ResourceHealth"></a>Hälsostatus för resurser

Hälsostatus för standardbelastningsutjämningsresurserna exponeras via den befintliga **resurshälsan** under **Övervaka > Service Health**.

Så här visar du hälsotillståndet för dina offentliga standardbelastningsutjämningsresurser:
1. Välj **Övervaka** > **tjänstens hälsotillstånd**.

   ![Sidan Övervaka](./media/load-balancer-standard-diagnostics/LBHealth1.png)

   *Bild: Länken Tjänsthälsa på Azure Monitor*

2. Välj **Resurshälsa**och kontrollera sedan att **prenumerations-ID** och **resurstyp = belastningsutjämnaren** är markerade.

   ![Hälsostatus för resurser](./media/load-balancer-standard-diagnostics/LBHealth3.png)

   *Bild: Välj resurs för hälsovy*

3. Välj resursen Belastningsutjämnare i listan för att visa dess historiska hälsostatus.

    ![Hälsostatus för belastningsutjämnaren](./media/load-balancer-standard-diagnostics/LBHealth4.png)

   *Bild: Hälsovy över resurshanteraren för belastningsutjämning*
 
De olika resurshälsostatusarna och beskrivningarna av dessa visas i följande tabell: 

| Hälsostatus för resurser | Beskrivning |
| --- | --- |
| Tillgängligt | Standardbelastningsutjämnarens resurs är felfri och tillgänglig. |
| Inte tillgänglig | Standardbelastningsutjämnarens resurs är inte felfri. Diagnostisera hälsotillståndet genom att välja **Azure Monitor** > **Metrics**.<br>(Status*som inte är tillgänglig* kan också innebära att resursen inte är ansluten till standardbelastningsutjämnaren.) |
| Okänt | Resurshälsostatus för standardbelastningsutjämningsresursen har inte uppdaterats ännu.<br>*(Okänd* status kan också innebära att resursen inte är ansluten till standardbelastningsutjämnaren.)  |

## <a name="next-steps"></a>Nästa steg

- Mer information finns i [Standard Load Balancer](load-balancer-standard-overview.md).
- Läs mer om utgående anslutning med [lastjämbalanserare](https://aka.ms/lboutbound).
- Läs mer om [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview).
- Lär dig mer om [AZURE Monitor REST API](https://docs.microsoft.com/rest/api/monitor/) och hur du hämtar mått via REST [API](/rest/api/monitor/metrics/list).
