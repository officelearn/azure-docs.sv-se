---
title: Övervakning och felsökning från HANA-sidan på SAP HANA på Azure (stora instanser) | Microsoft-dokument
description: Övervakning och felsökning från HANA-sidan på SAP HANA på en Azure (stora instanser).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 047ea4d07f2b497ac8c7deb90c056d63976094f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617079"
---
# <a name="monitoring-and-troubleshooting-from-hana-side"></a>Övervaka och felsöka från HANA-sida

För att effektivt analysera problem relaterade till SAP HANA på Azure (stora instanser) är det användbart att begränsa orsaken till ett problem. SAP har publicerat en stor mängd dokumentation som hjälper dig.

Tillämpliga vanliga frågor relaterade till SAP HANA-prestanda finns i följande SAP Notes:

- [SAP Note #2222200 – Vanliga frågor och svar: SAP HANA-nätverk](https://launchpad.support.sap.com/#/notes/2222200)
- [SAP Note #2100040 – Vanliga frågor och svar: SAP HANA CPU](https://launchpad.support.sap.com/#/notes/0002100040)
- [SAP Note #199997 – Vanliga frågor och svar: SAP HANA-minne](https://launchpad.support.sap.com/#/notes/2177064)
- [SAP Note #200000 – Vanliga frågor och svar: SAP HANA-prestandaoptimering](https://launchpad.support.sap.com/#/notes/2000000)
- [SAP Note #199930 – Vanliga frågor och svar: SAP HANA I/O-analys](https://launchpad.support.sap.com/#/notes/1999930)
- [SAP Note #2177064 – Vanliga frågor och svar: OMSTART OCH krascher för SAP HANA-tjänsten](https://launchpad.support.sap.com/#/notes/2177064)

## <a name="sap-hana-alerts"></a>SAP HANA-varningar

Som ett första steg kontrollerar du de aktuella SAP HANA-varningsloggarna. I SAP HANA Studio går du till **Administrationskonsolen: Aviseringar: Visa: alla aviseringar**. Den här fliken visar alla SAP HANA-aviseringar för specifika värden (fritt fysiskt minne, CPU-användning osv.) som ligger utanför de inställda lägsta och högsta tröskelvärdena. Som standard uppdateras checkar automatiskt var 15:e minut.

![Gå till Administrationskonsolen i SAP HANA Studio: Aviseringar: Visa: alla aviseringar](./media/troubleshooting-monitoring/image1-show-alerts.png)

## <a name="cpu"></a>Processor

För en avisering som utlöses på grund av felaktig tröskelinställning är en lösning att återställa till standardvärdet eller ett mer rimligt tröskelvärde.

![Återställ till standardvärdet eller ett rimligare tröskelvärde](./media/troubleshooting-monitoring/image2-cpu-utilization.png)

Följande aviseringar kan tyda på problem med CPU-resurs:

- Värd-CPU-användning (Varning 5)
- Senaste savepoint-åtgärden (Varning 28)
- Sparapunktslängd (Varning 54)

Du kan märka hög CPU-förbrukning på din SAP HANA-databas från något av följande:

- Alert 5 (Host CPU-användning) utlöses för aktuell eller tidigare CPU-användning
- Den visade CPU-användningen på översiktsskärmen

![Visade CPU-användning på översiktsskärmen](./media/troubleshooting-monitoring/image3-cpu-usage.png)

Inläsningsdiagrammet kan visa hög CPU-förbrukning eller hög förbrukning tidigare:

![Inläsningsdiagrammet kan visa hög CPU-förbrukning eller hög förbrukning tidigare](./media/troubleshooting-monitoring/image4-load-graph.png)

En avisering som utlöses på grund av hög CPU-användning kan orsakas av flera orsaker, inklusive, men inte begränsat till: körning av vissa transaktioner, datainläsning, jobb som inte svarar, tidskrävande SQL-uttryck och dålig frågeprestanda (till exempel med BW på HANA-kuber).

Mer information om felsökningssteg finns på webbplatsen [SAP HANA Troubleshooting: CPU Related Causes and Solutions.](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4f/bc915462db406aa2fe92b708b95189/content.htm?frameset=/en/db/6ca50424714af8b370960c04ce667b/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=46&amp;show_children=false)

## <a name="operating-system"></a>Operativsystem

En av de viktigaste kontrollerna för SAP HANA på Linux är att se till att Transparent Huge Pages är inaktiverade, se [SAP Note #2131662 - Transparent Huge Pages (THP) på SAP HANA Servrar](https://launchpad.support.sap.com/#/notes/2131662).

- Du kan kontrollera om Transparent Huge Pages är aktiverade via följande Linux-kommando: **cat /sys/kernel/mm/transparent\_hugepage/enabled**
- Om _alltid_ är inneslutet inom parentes enligt nedan, betyder det att Transparent Huge Pages är aktiverade: [alltid] madvise aldrig; om _aldrig_ är inneslutna inom parentes enligt nedan, betyder det att Transparent Huge Pages är inaktiverade: alltid madvise [aldrig]

Följande Linux-kommando bör inte returnera någonting: **rpm -qa | grep ulimit.** Om det verkar _ulimit_ är installerat, avinstallera det omedelbart.

## <a name="memory"></a>Minne

Du kan observera att mängden minne som allokeras av SAP HANA-databasen är högre än förväntat. Följande aviseringar indikerar problem med hög minnesanvändning:

- Värd fysisk minnesanvändning (Varning 1)
- Minnesanvändning av namnserver (Varning 12)
- Total minnesanvändning av kolumnlagringstabeller (Varning 40)
- Minnesanvändning av tjänster (Varning 43)
- Minnesanvändning av huvudlagring av kolumnlagringstabeller (Varning 45)
- Körningsdumpfiler (Varning 46)

Mer information om felsökning av felsökningssteg finns på webbplatsen [SAP HANA Felsökning: Minnesproblem.](https://help.sap.com/saphelp_hanaplatform/helpdata/en/db/6ca50424714af8b370960c04ce667b/content.htm?frameset=/en/59/5eaa513dde43758b51378ab3315ebb/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=26&amp;show_children=false)

## <a name="network"></a>Nätverk

Se [SAP Note #2081065 – Felsökning av SAP HANA-nätverk](https://launchpad.support.sap.com/#/notes/2081065) och utför felsökningsstegen i det här SAP-meddelandet.

1. Analysera tur-och-retur-tid mellan server och klient.
  A. Kör SQL-skriptet [_HANA\_\_Network Clients_](https://launchpad.support.sap.com/#/notes/1969700)_._
  
2. Analysera internodekommunikation.
  A. Kör SQL script [_HANA\_\_Network Services_](https://launchpad.support.sap.com/#/notes/1969700)_._

3. Kör Linux-kommando **ifconfig** (utdata visar om några paketförluster inträffar).
4. Kör Linux-kommandot **tcpdump**.

Använd också [IPERF-verktyget](https://iperf.fr/) med öppen källkod (eller liknande) för att mäta verkliga programnätverksprestanda.

Mer information om felsökning av felsökningssteg finns på webbplatsen FÖR FELSÖKNING AV [nätverk.](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a3/ccdff1aedc4720acb24ed8826938b6/content.htm?frameset=/en/dc/6ff98fa36541e997e4c719a632cbd8/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=142&amp;show_children=false)

## <a name="storage"></a>Lagring

Ur ett slutanvändarperspektiv körs ett program (eller systemet som helhet) långsamt, svarar inte eller verkar till och med sluta svara om det finns problem med I/O-prestanda. På fliken **Volymer** i SAP HANA Studio kan du se de kopplade volymerna och vilka volymer som används av varje tjänst.

![På fliken Volymer i SAP HANA Studio kan du se de kopplade volymerna och vilka volymer som används av varje tjänst](./media/troubleshooting-monitoring/image5-volumes-tab-a.png)

Bifogade volymer i den nedre delen av skärmen kan du se information om volymerna, till exempel filer och I/O-statistik.

![Bifogade volymer i den nedre delen av skärmen kan du se information om volymerna, till exempel filer och I/O-statistik](./media/troubleshooting-monitoring/image6-volumes-tab-b.png)

Mer information om felsökning [av I/O-relaterade rotorsaker och lösningar](https://help.sap.com/saphelp_hanaplatform/helpdata/en/dc/6ff98fa36541e997e4c719a632cbd8/content.htm?frameset=/en/47/4cb08a715c42fe9f7cc5efdc599959/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=55&amp;show_children=false) och FELSÖKNING av SAP [HANA: Diskrelaterade rotorsaker och lösningar](https://help.sap.com/saphelp_hanaplatform/helpdata/en/47/4cb08a715c42fe9f7cc5efdc599959/content.htm?frameset=/en/44/3e1db4f73d42da859008df4f69e37a/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=53&amp;show_children=false) finns på webbplatsen för detaljerad felsökning.

## <a name="diagnostic-tools"></a>Diagnosverktyg

Utför en SAP HANA Health\_\_Check via HANA Configuration Minichecks. Det här verktyget returnerar potentiellt kritiska tekniska problem som redan borde ha tagits upp som aviseringar i SAP HANA Studio.

Se [SAP Note #1969700 – SQL-programsamling för SAP HANA](https://launchpad.support.sap.com/#/notes/1969700) och hämta filen SQL Statements.zip som är kopplad till den anteckningen. Spara zip-filen på den lokala hårddisken.

Högerklicka i kolumnen **Namn** **System Information** i SAP HANA Studio och välj **Importera SQL-uttryck**.

![Högerklicka i kolumnen Namn i SAP HANA Studio och välj Importera SQL-uttryck](./media/troubleshooting-monitoring/image7-import-statements-a.png)

Välj filen SQL Statements.zip som lagras lokalt och en mapp med motsvarande SQL-uttryck importeras. Nu kan de många olika diagnostiska kontrollerna köras med dessa SQL-uttryck.

Om du till exempel vill testa bandbreddskraven för SAP HANA System Replication högerklickar du på **bandbreddssatsen** under **Replikering: Bandbredd** och väljer **Öppna** i SQL Console.

Det fullständiga SQL-uttrycket öppnar så att indataparametrar (ändringsavsnitt) kan ändras och sedan köras.

![Det fullständiga SQL-uttrycket öppnar så att indataparametrar (ändringsavsnitt) kan ändras och sedan köras](./media/troubleshooting-monitoring/image8-import-statements-b.png)

Ett annat exempel är att högerklicka på satserna under **Replikering: Översikt**. Välj **Kör** på snabbmenyn:

![Ett annat exempel är att högerklicka på satserna under Replikering: Översikt. Välj Kör på snabbmenyn](./media/troubleshooting-monitoring/image9-import-statements-c.png)

Detta resulterar i information som hjälper till med felsökning:

![Detta kommer att resultera i information som hjälper till med felsökning](./media/troubleshooting-monitoring/image10-import-statements-d.png)

Gör samma sak\_för\_HANA Configuration Minichecks och kontrollera om det finns _X-markeringar_ i kolumnen _C_ (kritisk).

Exempel på utdata:

**\_HANA-konfiguration\_\_MiniChecks Rev102.01+1** för allmänna SAP HANA-kontroller.

![HANA-konfiguration\_\_MiniCheckar\_Rev102.01+1 för allmänna SAP HANA-kontroller](./media/troubleshooting-monitoring/image11-configuration-minichecks.png)

**Hana\_\_Services Översikt** för en översikt över vad SAP HANA-tjänster körs för närvarande.

![Hana\_\_Services Översikt för en översikt över vad SAP HANA-tjänster för närvarande körs](./media/troubleshooting-monitoring/image12-services-overview.png)

**HANA\_\_Services Statistics** for SAP HANA serviceinformation (CPU, memory, etc.).

![HANA\_\_Services Statistik för SAP HANA-serviceinformation](./media/troubleshooting-monitoring/image13-services-statistics.png)

**Hana-konfigurationsöversikt\_\_\_Rev110+** för allmän information om SAP HANA-instansen.

![Hana-konfigurationsöversikt\_\_\_Rev110+ för allmän information om SAP HANA-instansen](./media/troubleshooting-monitoring/image14-configuration-overview.png)

**HANA-konfigurationsparametrar\_\_\_Rev70+** för att kontrollera SAP HANA-parametrar.

![HANA-konfigurationsparametrar\_\_\_Rev70+ för att kontrollera SAP HANA-parametrar](./media/troubleshooting-monitoring/image15-configuration-parameters.png)

**Nästa steg**

- Se [Hög tillgänglighet som ställts in i SUSE med STONITH](ha-setup-with-stonith.md).