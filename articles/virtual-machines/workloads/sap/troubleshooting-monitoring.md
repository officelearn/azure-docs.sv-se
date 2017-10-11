---
title: "Felsökning och övervakning av SAP HANA i Azure (stora instanser) | Microsoft Docs"
description: "Felsöka och övervaka SAP HANA på ett Azure (stora instanser)."
services: virtual-machines-linux
documentationcenter: 
author: RicksterCDN
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/01/2016
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ee5be707b443cbe42bf4a492d79390e534d4b91f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-troubleshoot-and-monitor-sap-hana-large-instances-on-azure"></a>Hur du felsöker och övervaka SAP HANA (stora instanser) på Azure


## <a name="monitoring-in-sap-hana-on-azure-large-instances"></a>Övervakning av SAP HANA i Azure (stora instanser)

SAP HANA i Azure (stora instanser) skiljer sig inte från andra IaaS-distribution, behöver du övervaka vad Operativsystemet och programmet gör och hur de använder följande resurser:

- Processor
- Minne
- Nätverkets bandbredd
- Diskutrymme

Med Azure virtuella datorer som du behöver ta reda på om resursen klasserna som anges ovan är tillräckligt eller om de få slut. Här är mer information om var och en av de olika klasserna:

**CPU-resursanvändningen:** förhållandet SAP som definierats för vissa arbetsbelastningar mot HANA tillämpas för att se till att det ska vara tillräckligt med processorresurser som är tillgänglig för arbete med data som lagras i minnet. Det kan dock finnas fall där HANA förbrukar en stor mängd CPU köra frågor på grund av index som saknas eller liknande problem. Det innebär att du ska övervaka CPU resursförbrukning HANA stora instans enhet samt CPU-resurser som används av specifika HANA-tjänster.

**Minnesförbrukning:** är viktigt att övervaka från inom HANA samt utanför HANA på enheten. Övervaka inom HANA, hur data förbrukar allokerat minne för att hålla sig inom de nödvändiga storlek riktlinjerna för SAP HANA. Du bör också övervaka minnesförbrukning på instansnivå stora för att säkerställa att ytterligare installerade icke-HANA programvara inte förbrukar för mycket minne och därför konkurrerar med HANA för minne.

**Nätverkets bandbredd:** i Azure-VNet-gateway har begränsad bandbredd på data flyttas till Azure-VNet, så är det bra att övervaka data tas emot av alla virtuella Azure-datorer inom ett VNet att ta reda på hur nära du kommer att gränserna för Azure gateway SKU du markering å. På HANA stora instans-enhet det vara klokt att övervaka inkommande och utgående nätverkstrafik samt och för att hålla reda på de volymer som ska hanteras med tiden.

**Diskutrymme:** förbrukningen av diskutrymme vanligtvis ökar med tiden. Det finns många orsaker, men de flesta av alla är: datavolym ökar, körning av säkerhetskopiering av transaktionsloggar, lagra spårningsfiler och utföra storage snapshots. Det är därför viktigt att övervaka användning av diskutrymme och hantera det diskutrymme som är associerade med HANA stora instans-enhet.

## <a name="monitoring-and-troubleshooting-from-hana-side"></a>Övervakning och felsökning från HANA sida

För att effektivt analysera problem relaterade till SAP HANA i Azure (stora instanser), är det bra att begränsa den grundläggande orsaken till problemet. SAP har publicerat en stor mängd dokumentation som hjälper dig.

Tillämpliga vanliga frågor och svar för SAP HANA prestanda finns i följande SAP-information:

- [SAP-kommentar #2222200 – vanliga frågor och svar: SAP HANA-nätverk](https://launchpad.support.sap.com/#/notes/2222200)
- [SAP-kommentar #2100040 – vanliga frågor och svar: SAP HANA-processor](https://launchpad.support.sap.com/#/notes/0002100040)
- [SAP-kommentar #199997 – vanliga frågor och svar: SAP HANA-minne](https://launchpad.support.sap.com/#/notes/2177064)
- [SAP-kommentar #200000 – vanliga frågor och svar: SAP HANA prestandaoptimering](https://launchpad.support.sap.com/#/notes/2000000)
- [SAP-kommentar #199930 – vanliga frågor och svar: SAP HANA-i/o analys](https://launchpad.support.sap.com/#/notes/1999930)
- [SAP-kommentar #2177064 – vanliga frågor och svar: SAP HANA-tjänsten startas om och kraschar](https://launchpad.support.sap.com/#/notes/2177064)

**SAP HANA aviseringar**

Som ett första steg i loggarna aktuella SAP HANA avisering. SAP HANA Studio, gå till **administrationskonsolen: aviseringar: visa: alla aviseringar**. Den här fliken visas alla aviseringar för SAP HANA för specifika värden (fysiskt minne, CPU-användning osv.) som faller utanför ange lägsta och högsta tröskelvärden. Som standard är kontrollerar uppdateras automatiskt var 15: e minut.

![Gå till Administration Console i SAP HANA Studio: aviseringar: visa: alla aviseringar](./media/troubleshooting-monitoring/image1-show-alerts.png)

**CPU**

För en avisering som aktiveras på grund av felaktig tröskelvärdet inställning är en lösning att återställa till standardvärdet eller ett rimliga tröskelvärde.

![Återställer till standardvärdet eller ett rimliga tröskelvärde](./media/troubleshooting-monitoring/image2-cpu-utilization.png)

Följande aviseringar kan tyda på problem med CPU företagsresurser:

- Värd för CPU-användning (varning 5)
- Senaste lagringspunktsåtgärd (varning 28)
- Lagringspunkt varaktighet (varning 54)

Du kan upptäcka hög CPU-förbrukning på din SAP HANA-databas från något av följande:

- Aviseringen 5 (värd processoranvändning) aktiveras aktuella eller förfallna CPU-användning
- Processoranvändningen visas på översiktsskärmen

![CPU-användning visas på översiktsskärmen](./media/troubleshooting-monitoring/image3-cpu-usage.png)

Läs in diagrammet kan visa hög CPU-förbrukning eller hög förbrukning tidigare:

![Läs in diagrammet kan visa hög CPU-förbrukning eller hög förbrukning tidigare](./media/troubleshooting-monitoring/image4-load-graph.png)

En avisering aktiveras på grund av hög processoranvändning kan orsakas av olika orsaker, inklusive men inte begränsat till: körning av vissa transaktioner, datainläsning, hängande för jobb, långvariga SQL-satser och felaktiga frågeprestanda (till exempel med BW på HANA kuber).

Referera till den [SAP HANA Felsökning: relaterade gör att CPU och lösningar](http://help.sap.com/saphelp_hanaplatform/helpdata/en/4f/bc915462db406aa2fe92b708b95189/content.htm?frameset=/en/db/6ca50424714af8b370960c04ce667b/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=46&amp;show_children=false) för detaljerad felsökning.

**Operativsystem**

En av de viktigaste kontrollerna för SAP HANA på Linux är att se till att Transparent stora sidor är inaktiverat, se [SAP Obs #2131662 – Transparent stora sidor (THP) på SAP HANA-servrar](https://launchpad.support.sap.com/#/notes/2131662).

- Du kan kontrollera om Transparent stora sidor aktiveras via följande Linux-kommando: **cat /sys/kernel/mm/transparent\_hugepage aktiveras**
- Om _alltid_ omges inom hakparenteser enligt nedan, betyder det att Transparent stora sidor är aktiverad: [alltid] madvise aldrig; om _aldrig_ omges inom hakparenteser enligt nedan, innebär det att Transparent enorma Sidor är inaktiverade: madvise alltid [aldrig]

Följande Linux-kommandot ska returnera ingenting: **rpm - qa | grep kommandot ulimit.** Om det verkar som _kommandot ulimit_ är installerad avinstallerar du den omedelbart.

**Minne**

Du kan se att mängden minne som allokerats av SAP HANA-databas är högre än förväntat. Följande aviseringarna indikerar problem med hög minnesanvändning:

- Värden fysisk minnesanvändning (varning 1)
- Minnesanvändning för namnserver (varning 12)
- Totalt antal minnesanvändningen för kolumnen Store tabeller (varning 40)
- Minnesanvändning av tjänster (avisering 43)
- Mängden minne på huvudsakliga lagring av kolumn Store tabeller (varning 45)
- Runtime dumpfiler (varning 46)

Referera till den [SAP HANA Felsökning: minnesproblem](http://help.sap.com/saphelp_hanaplatform/helpdata/en/db/6ca50424714af8b370960c04ce667b/content.htm?frameset=/en/59/5eaa513dde43758b51378ab3315ebb/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=26&amp;show_children=false) för detaljerad felsökning.

**Nätverk**

Referera till [SAP Obs #2081065 – felsökning av nätverk för SAP HANA](https://launchpad.support.sap.com/#/notes/2081065) och utföra felsökning i den här SAP-kommentar nätverk.

1. Analysera tidsfördröjningen mellan servern och klienten.
  A. Kör SQL-skript [ _HANA\_nätverk\_klienter_](https://launchpad.support.sap.com/#/notes/1969700)_._
  
2. Analysera dessa kommunikation.
  A. Kör SQL-skript [ _HANA\_nätverk\_Services_](https://launchpad.support.sap.com/#/notes/1969700)_._

3. Kör Linux-kommando **ifconfig** (utdata visar om paketförluster sker).
4. Kör Linux-kommando **tcpdump**.

Använd öppen källkod [IPERF](https://iperf.fr/) verktyget (eller liknande) att mäta verkliga programmet nätverkets prestanda.

Referera till den [SAP HANA Felsökning: nätverk prestanda och anslutningsproblem](http://help.sap.com/saphelp_hanaplatform/helpdata/en/a3/ccdff1aedc4720acb24ed8826938b6/content.htm?frameset=/en/dc/6ff98fa36541e997e4c719a632cbd8/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=142&amp;show_children=false) för detaljerad felsökning.

**Storage**

En ur slutanvändaren ett program (eller system som helhet) körs långsamt, svarar inte eller även kan verka låsa om det finns problem med i/o-prestanda. I den **volymer** fliken i SAP HANA Studio visas anslutna volymer och vilka volymer som används av varje tjänst.

![I fliken volymer i SAP HANA Studio visas anslutna volymer och vilka volymer som används av varje tjänst](./media/troubleshooting-monitoring/image5-volumes-tab-a.png)

Anslutna volymer i den nedre delen av skärmen ser du information om volymer, till exempel filer och i/o-statistik.

![Anslutna volymer i den nedre delen av skärmen ser du information om volymer, till exempel filer och i/o-statistik](./media/troubleshooting-monitoring/image6-volumes-tab-b.png)

Referera till den [SAP HANA Felsökning: i/o relaterade rotorsaker och lösningar](http://help.sap.com/saphelp_hanaplatform/helpdata/en/dc/6ff98fa36541e997e4c719a632cbd8/content.htm?frameset=/en/47/4cb08a715c42fe9f7cc5efdc599959/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=55&amp;show_children=false) och [SAP HANA Felsökning: Disk relaterade rotorsaker och lösningar](http://help.sap.com/saphelp_hanaplatform/helpdata/en/47/4cb08a715c42fe9f7cc5efdc599959/content.htm?frameset=/en/44/3e1db4f73d42da859008df4f69e37a/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=53&amp;show_children=false) för detaljerad felsökning.

**Verktyg för Nätverksdiagnostik**

Utföra en SAP HANA hälsokontroll via HANA\_Configuration\_Minichecks. Det här verktyget returnerar allvarliga tekniska problem som bör har redan aktiverats som aviseringar i SAP HANA Studio.

Referera till [SAP Obs #1969700 – SQL-instruktionen samlingen för SAP HANA](https://launchpad.support.sap.com/#/notes/1969700) och hämta SQL Statements.zip-filen som bifogas anteckningen. Lagra den här ZIP-filen på den lokala hårddisken.

I SAP HANA Studio på den **Systeminformation** , högerklickar på i den **namn** kolumn och markera **SQL importuttryck**.

![Högerklicka i kolumnen namn i SAP HANA Studio på fliken Systeminformation och välj Importera SQL-instruktioner](./media/troubleshooting-monitoring/image7-import-statements-a.png)

Välj SQL Statements.zip filen lagras lokalt och en mapp med motsvarande SQL-uttryck som ska importeras. Nu kan du köra många olika diagnostiska kontroller med de här SQL-instruktioner.

Till exempel om du vill testa SAP HANA System Replication krav på bandbredd, högerklickar du på den **bandbredd** instruktionen under **replikering: bandbredd** och välj **öppna** i SQL-konsolen.

Den fullständiga SQL-instruktionen öppnas så att indataparametrar (ändring avsnittet) har ändrats och därmed köras.

![Den fullständiga SQL-instruktionen öppnar så att indataparametrar (ändring avsnittet) har ändrats och därmed köras](./media/troubleshooting-monitoring/image8-import-statements-b.png)

Ett annat exempel är Högerklicka på instruktionerna under **replikering: översikt över**. Välj **kör** på snabbmenyn:

![Ett annat exempel är Högerklicka på instruktionerna under replikeringen: översikt. Välj Kör på snabbmenyn](./media/troubleshooting-monitoring/image9-import-statements-c.png)

Detta resulterar i information som hjälper med felsökning:

![Detta leder till information som hjälper med felsökning](./media/troubleshooting-monitoring/image10-import-statements-d.png)

Gör likadant för HANA\_Configuration\_Minichecks och Sök efter alla _X_ markerar i den _C_ (kritisk)-kolumn.

Exempel utdata:

**HANA\_Configuration\_MiniChecks\_Rev102.01 + 1** för allmän SAP HANA kontrollerar.

![HANA\_Configuration\_MiniChecks\_Rev102.01 + 1 för allmänna SAP HANA-kontroller](./media/troubleshooting-monitoring/image11-configuration-minichecks.png)

**HANA\_Services\_översikt** en översikt över vad SAP HANA tjänster som körs.

![HANA\_Services\_översikt för en översikt över vad SAP HANA tjänster som körs](./media/troubleshooting-monitoring/image12-services-overview.png)

**HANA\_Services\_statistik** för SAP HANA tjänstinformation (CPU, minne, osv.).

![HANA\_Services\_statistik för SAP HANA tjänstinformation ](./media/troubleshooting-monitoring/image13-services-statistics.png)

**HANA\_Configuration\_översikt\_Rev110 +** allmän information om SAP HANA-instans.

![HANA\_Configuration\_översikt\_Rev110 + allmän information om SAP HANA-instans](./media/troubleshooting-monitoring/image14-configuration-overview.png)

**HANA\_Configuration\_parametrar\_Rev70 +** att kontrollera SAP HANA-parametrar.

![HANA\_Configuration\_parametrar\_Rev70 + för att kontrollera SAP HANA-parametrar](./media/troubleshooting-monitoring/image15-configuration-parameters.png)

