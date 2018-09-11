---
title: Övervakning och felsökning från HANA sida på SAP HANA på Azure (stora instanser) | Microsoft Docs
description: Övervakning och felsökning från HANA sida på SAP HANA på Azure (stora instanser).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 10709f4f2fcc341840753ef4c4eb479e29fb58d5
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2018
ms.locfileid: "44357436"
---
# <a name="monitoring-and-troubleshooting-from-hana-side"></a>Övervakning och felsökning från HANA-sida

För att effektivt analysera problem relaterade till SAP HANA på Azure (stora instanser), är det praktiskt att begränsa de grundläggande orsakerna till problem. SAP har publicerat en stor mängd dokumentation som hjälper dig att.

Tillämpliga vanliga frågor och svar för SAP HANA prestanda finns i följande SAP-information:

- [SAP-kommentar #2222200 – vanliga frågor och svar: SAP HANA-nätverk](https://launchpad.support.sap.com/#/notes/2222200)
- [SAP-kommentar #2100040 – vanliga frågor och svar: SAP HANA-processor](https://launchpad.support.sap.com/#/notes/0002100040)
- [SAP-kommentar #199997 – vanliga frågor och svar: SAP HANA-minne](https://launchpad.support.sap.com/#/notes/2177064)
- [SAP-kommentar #200000 – vanliga frågor och svar: Prestandaoptimering SAP HANA](https://launchpad.support.sap.com/#/notes/2000000)
- [SAP-kommentar #199930 – vanliga frågor och svar: SAP HANA i/o-analys](https://launchpad.support.sap.com/#/notes/1999930)
- [SAP-kommentar #2177064 – vanliga frågor och svar: SAP HANA-tjänsten startas om och kraschar](https://launchpad.support.sap.com/#/notes/2177064)

## <a name="sap-hana-alerts"></a>SAP HANA-aviseringar

Kontrollera de aktuella avisering SAP HANA-loggarna som ett första steg. I SAP HANA Studio går du till **administrationskonsolen: aviseringar: visa: alla aviseringar**. Den här fliken visar alla SAP HANA-aviseringar för specifika värden (ledigt fysiskt minne, CPU-användning osv.) som faller utanför ange lägsta och högsta tröskelvärden. Som standard statusen kontrollerar uppdateras automatiskt var 15: e minut.

![Gå till Administration Console i SAP HANA Studio: aviseringar: visa: alla aviseringar](./media/troubleshooting-monitoring/image1-show-alerts.png)

## <a name="cpu"></a>Processor

För en avisering utlöses på grund av felaktig tröskelvärde genom är en lösning att återställa till standardvärdet eller ett mer rimligt tröskelvärde.

![Återställ till standardvärde eller ett mer rimligt tröskelvärde](./media/troubleshooting-monitoring/image2-cpu-utilization.png)

Följande aviseringar kan tyda på problem med CPU systemresurser:

- Processoranvändning för värd (varning 5)
- Senaste lagringspunktsåtgärd (varning 28)
- Lagringspunkt varaktighet (varning 54)

Du kanske ser hög CPU-förbrukning på SAP HANA-databas från något av följande:

- Aviseringen 5 (värd CPU-användning) aktiveras aktuella eller förfallna CPU-användning
- Processoranvändningen visas på skärmen

![CPU-användningen visas på skärmen](./media/troubleshooting-monitoring/image3-cpu-usage.png)

Läs in diagram kan visa hög CPU-förbrukning eller hög förbrukning tidigare:

![Läs in diagram kan visa hög CPU-förbrukning eller hög förbrukning i förflutna](./media/troubleshooting-monitoring/image4-load-graph.png)

En avisering utlöses på grund av hög processoranvändning kan bero på flera orsaker, bland annat, men inte begränsat till: körning av vissa transaktioner, datainläsning, hängande för jobb, tidskrävande SQL-uttryck och felaktiga frågeprestanda (till exempel med BW på HANA kuber).

Referera till den [felsökning av SAP HANA: CPU relaterade orsakar och lösningar](http://help.sap.com/saphelp_hanaplatform/helpdata/en/4f/bc915462db406aa2fe92b708b95189/content.htm?frameset=/en/db/6ca50424714af8b370960c04ce667b/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=46&amp;show_children=false) för detaljerade felsökningsanvisningar.

## <a name="operating-system"></a>Operativsystem

Ett av de viktigaste kontrollerna för SAP HANA på Linux är att se till att Transparent stora sidor är inaktiverat, se [SAP Obs! #2131662 – Transparent stora sidor (THP) på SAP HANA-servrar](https://launchpad.support.sap.com/#/notes/2131662).

- Du kan kontrollera om Transparent stora sidor aktiveras via följande Linux-kommandot: **cat /sys/kernel/mm/transparent\_hugepage/aktiverat**
- Om _alltid_ omges med hakparenteser enligt nedan, innebär det att Transparent enorm sidorna är aktiverade: [alltid] madvise aldrig; om _aldrig_ omges med hakparenteser enligt nedan, innebär det att Transparent enorm Sidor är inaktiverade: madvise alltid [aldrig]

Kommandot Linux ska returnera inget: **rpm - qa | grep ulimit.** Om den visas _ulimit_ är installerad avinstallerar du den omedelbart.

## <a name="memory"></a>Minne

Du kan se att mängden minne som allokerats av SAP HANA-databas är högre än förväntat. Följande aviseringar tyder på problem med hög minnesanvändning:

- Fysisk minnesanvändning för värd (varning 1)
- Minnesanvändningen för namnservern (varning 12)
- Total minnesanvändning i kolumnen Store tabeller (varning 40)
- Minnesanvändning över tjänster (avisering 43)
- Minnesanvändning av huvudsakliga för lagring av kolumnen Store tabeller (varning 45)
- Runtime-kraschdumpfiler (varning 46)

Referera till den [felsökning av SAP HANA: minnesproblem](http://help.sap.com/saphelp_hanaplatform/helpdata/en/db/6ca50424714af8b370960c04ce667b/content.htm?frameset=/en/59/5eaa513dde43758b51378ab3315ebb/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=26&amp;show_children=false) för detaljerade felsökningsanvisningar.

## <a name="network"></a>Nätverk

Referera till [SAP Obs! #2081065 – felsökning av nätverk för SAP HANA](https://launchpad.support.sap.com/#/notes/2081065) och utföra felsökning i den här SAP-kommentar nätverk.

1. Analysera mäts tiden mellan servern och klienten.
  A. Kör SQL-skript [ _HANA\_nätverk\_klienter_](https://launchpad.support.sap.com/#/notes/1969700)_._
  
2. Analysera kommunikation mellan.
  A. Kör SQL-skript [ _HANA\_nätverk\_Services_](https://launchpad.support.sap.com/#/notes/1969700)_._

3. Kör Linux-kommandot **ifconfig** (utdata visar om några paketförluster sker).
4. Kör Linux-kommandot **tcpdump**.

Du kan också använda den öppna källkoden [IPERF](https://iperf.fr/) verktyget (eller liknande) att mäta verkliga program nätverkets prestanda.

Referera till den [felsökning av SAP HANA: nätverk prestanda och problem med nätverksanslutningen](http://help.sap.com/saphelp_hanaplatform/helpdata/en/a3/ccdff1aedc4720acb24ed8826938b6/content.htm?frameset=/en/dc/6ff98fa36541e997e4c719a632cbd8/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=142&amp;show_children=false) för detaljerade felsökningsanvisningar.

## <a name="storage"></a>Storage

Ur ett för slutanvändare, ett program (eller systemet som helhet) körs långsamt, inte svarar eller även kan verka låsa om det finns problem med i/o-prestanda. I den **volymer** fliken i SAP HANA Studio ser du de anslutna volymerna och vilka volymer som används av varje tjänst.

![I fliken volymer i SAP HANA-Studio ser du de anslutna volymerna och vilka volymer som används av varje tjänst](./media/troubleshooting-monitoring/image5-volumes-tab-a.png)

Anslutna volymer i den nedre delen av skärmen ser du information om volymer, till exempel filer och i/o-statistik.

![Anslutna volymer i den nedre delen av skärmen ser du information om volymer, till exempel filer och i/o-statistik](./media/troubleshooting-monitoring/image6-volumes-tab-b.png)

Referera till den [felsökning av SAP HANA: i/o-relaterade rotorsaken och lösningar](http://help.sap.com/saphelp_hanaplatform/helpdata/en/dc/6ff98fa36541e997e4c719a632cbd8/content.htm?frameset=/en/47/4cb08a715c42fe9f7cc5efdc599959/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=55&amp;show_children=false) och [felsökning av SAP HANA: Disk relaterade rotorsaker och lösningar](http://help.sap.com/saphelp_hanaplatform/helpdata/en/47/4cb08a715c42fe9f7cc5efdc599959/content.htm?frameset=/en/44/3e1db4f73d42da859008df4f69e37a/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=53&amp;show_children=false) för detaljerade felsökningsanvisningar.

## <a name="diagnostic-tools"></a>Diagnostiska verktyg

Utföra en SAP HANA-hälsokontroll via HANA\_Configuration\_Minichecks. Det här verktyget returnerar potentiellt allvarligt tekniska problem som bör har redan aktiverats som aviseringar i SAP HANA-Studio.

Referera till [SAP Obs! #1969700 – SQL-instruktionen samling för SAP HANA](https://launchpad.support.sap.com/#/notes/1969700) och ladda ned filen SQL Statements.zip kopplad till noden. Store ZIP-filen på den lokala hårddisken.

I SAP HANA Studio på den **Systeminformation** , högerklickar på i den **namn** kolumnen och välj **SQL importuttryck**.

![Högerklicka i kolumnen namn i SAP HANA-Studio, på fliken Systeminformation och välj SQL-uttryck för Import](./media/troubleshooting-monitoring/image7-import-statements-a.png)

Välj den SQL Statements.zip-fil som lagras lokalt och en mapp med motsvarande SQL-uttryck som ska importeras. Nu kan du köra många olika diagnostiska kontroller med de här SQL-uttryck.

Till exempel om du vill testa SAP HANA System Replication krav på bandbredd, högerklickar du på den **bandbredd** instruktionen under **replikering: bandbredd** och välj **öppna** i SQL-konsolen.

Den fullständiga SQL-instruktionen öppnas så att indataparametrar (ändring av avsnittet) som ska ändras och därmed körts.

![Den fullständiga SQL-instruktionen öppnas så att indataparametrar (ändring av avsnittet) som ska ändras och därmed körts](./media/troubleshooting-monitoring/image8-import-statements-b.png)

Ett annat exempel är Högerklicka på rapporterna under **replikering: översikt över**. Välj **kör** från snabbmenyn som visas:

![Ett annat exempel är Högerklicka på rapporterna under replikering: översikt. Välj Kör på snabbmenyn](./media/troubleshooting-monitoring/image9-import-statements-c.png)

Detta leder till information som underlättar felsökning:

![Detta leder till information som hjälper med felsökning](./media/troubleshooting-monitoring/image10-import-statements-d.png)

Gör likadant för HANA\_Configuration\_Minichecks och Sök efter någon _X_ markerar i den _C_ (kritisk) kolumn.

Exemplet utdata:

**HANA\_Configuration\_MiniChecks\_Rev102.01 + 1** för allmän SAP HANA kontrollerar.

![HANA\_Configuration\_MiniChecks\_Rev102.01 + 1 för allmänna SAP HANA-kontroller](./media/troubleshooting-monitoring/image11-configuration-minichecks.png)

**HANA\_Services\_översikt** en översikt över vad SAP HANA tjänster körs för närvarande.

![HANA\_Services\_översikt över en översikt över vad SAP HANA tjänster som körs](./media/troubleshooting-monitoring/image12-services-overview.png)

**HANA\_Services\_statistik** för SAP HANA tjänstinformation (processor, minne, osv.).

![HANA\_Services\_statistik för SAP HANA tjänstinformation ](./media/troubleshooting-monitoring/image13-services-statistics.png)

**HANA\_Configuration\_översikt\_Rev110 +** allmän information om SAP HANA-instans.

![HANA\_Configuration\_översikt\_Rev110 + allmän information om SAP HANA-instans](./media/troubleshooting-monitoring/image14-configuration-overview.png)

**HANA\_Configuration\_parametrar\_Rev70 +** kontrollera SAP HANA-parametrarna.

![HANA\_Configuration\_parametrar\_Rev70 + för att kontrollera SAP HANA-parametrar](./media/troubleshooting-monitoring/image15-configuration-parameters.png)

**Nästa steg**

- Se [hög tillgänglighet i SUSE med hjälp av STONITH](ha-setup-with-stonith.md).