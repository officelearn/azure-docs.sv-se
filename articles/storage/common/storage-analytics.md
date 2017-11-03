---
title: "Använd Azure Storage Analytics för att samla in loggar och mått data | Microsoft Docs"
description: "Storage Analytics gör det möjligt att spåra mått data för alla storage-tjänster och för att samla in loggar för lagring av Blob, köer och tabellen."
services: storage
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 7894993b-ca42-4125-8f17-8f6dfe3dca76
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/03/2017
ms.author: tamram
ms.openlocfilehash: 9ae9dd0b078911a695d441cd3891be720dc204ac
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="storage-analytics"></a>Lagringsanalys

Med Azure Storage Analytics kan du logga och skapa statistik för ett lagringskonto. Du kan använda dessa data för att spåra förfrågningar, analysera användningstrender och diagnostisera problem med lagringskontot.

Om du vill använda Storage Analytics, måste du aktivera den separat för varje tjänst som du vill övervaka. Du kan aktivera det från den [Azure Portal](https://portal.azure.com). Mer information finns i [övervaka ett lagringskonto i Azure Portal](storage-monitor-storage-account.md). Du kan också aktivera Storage Analytics programmässigt via REST API eller klientbiblioteket. Använd den [hämta egenskaper för Blob-tjänsten](https://msdn.microsoft.com/library/hh452239.aspx), [hämta egenskaper för kön](https://msdn.microsoft.com/library/hh452243.aspx), [hämta Service Tabellegenskaper](https://msdn.microsoft.com/library/hh452238.aspx), och [hämta egenskaper för filtjänsten](https://msdn.microsoft.com/library/mt427369.aspx) åtgärder för att aktivera Storage Analytics för varje tjänst.

Sammanställda data lagras i en välkänd blob (för loggning) och välkända tabeller (för mått), som kan nås med hjälp av Blob-tjänsten och tabelltjänsten API: er.

Storage Analytics har en 20 TB gräns på mängden lagrade data som är oberoende av den totala gränsen för ditt lagringskonto. Läs mer om fakturering och datakvarhållningsprinciper [Storage Analytics och fakturering för](https://msdn.microsoft.com/library/hh360997.aspx). Läs mer om lagringskontogränser [Azure Storage skalbarhets- och prestandamål](storage-scalability-targets.md).

En detaljerad vägledning om använder Storage Analytics och andra verktyg för att identifiera, diagnostisera och felsöka problem med Azure Storage finns [övervaka, diagnostisera och felsöka Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="about-storage-analytics-logging"></a>Om Storage Analytics loggning
Storage Analytics loggar detaljerad information om lyckade och misslyckade förfrågningar till en storage-tjänst. Den här informationen kan användas för att övervaka enskilda begäranden och diagnostisera problem med en storage-tjänst. Begäranden loggas för bästa prestanda.

Loggposter skapas endast om tjänsteaktivitet för lagring. Till exempel skapas ett lagringskonto har aktivitet i dess Blob-tjänsten men inte i dess tabell eller Queue-tjänster, endast loggar som hör till Blob-tjänsten.

Storage Analytics loggning är inte tillgänglig för Azure-filer.

### <a name="logging-authenticated-requests"></a>Autentiserade loggningsbegäranden
Följande typer av autentiserade begäranden loggas:

* Lyckade begäranden.
* Misslyckade begäranden, inklusive timeout, begränsning, nätverk, auktorisering och andra fel.
* Begäranden som använder en delad signatur åtkomst (SAS), inklusive misslyckade och lyckade begäranden.
* Begäranden till analysdata.

Förfrågningar som görs av Storage Analytics, till exempel loggen skapas eller tas bort, loggas inte. En fullständig lista över data som loggats dokumenteras i den [Storage Analytics loggade åtgärder och statusmeddelanden](https://msdn.microsoft.com/library/hh343260.aspx) och [Storage Analytics loggformatet](https://msdn.microsoft.com/library/hh343259.aspx) avsnitt.

### <a name="logging-anonymous-requests"></a>Loggning anonyma förfrågningar
Följande typer av anonyma begäranden loggas:

* Lyckade begäranden.
* Serverfel.
* Timeout-fel för både klient och server.
* Misslyckade GET-begäranden med felkoden 304 (ändra inte).

Alla andra misslyckade anonyma begäranden har inte loggats. En fullständig lista över data som loggats dokumenteras i den [Storage Analytics loggade åtgärder och statusmeddelanden](https://msdn.microsoft.com/library/hh343260.aspx) och [Storage Analytics loggformatet](https://msdn.microsoft.com/library/hh343259.aspx) avsnitt.

### <a name="how-logs-are-stored"></a>Hur loggar lagras
Alla loggar lagras i blockblobbar i en behållare med namnet $logs som skapas automatiskt när Storage Analytics har aktiverats för ett lagringskonto. Behållaren $logs finns i namnrymd blob storage-konto till exempel: `http://<accountname>.blob.core.windows.net/$logs`. Den här behållaren kan inte tas bort när Storage Analytics har aktiverats, även om dess innehåll kan tas bort.

> [!NOTE]
> Behållaren $logs visas inte när en behållare med åtgärden utförs, till exempel den [ListContainers](https://msdn.microsoft.com/library/azure/dd179352.aspx) metod. Den måste kunna nås direkt. Du kan till exempel använda den [ListBlobs](https://msdn.microsoft.com/library/azure/dd135734.aspx) metod för att komma åt i det `$logs` behållare.
> Storage Analytics kommer att som begäranden loggas överföra mellanresultat som block. Med jämna mellanrum Storage Analytics genomför dessa block och göra dem tillgängliga som en blob.
> 
> 

Kan det finnas dubbletter för loggar skapas i samma timme. Du kan fastställa om en post är en dubblett genom att kontrollera den **RequestId** och **åtgärden** nummer.

### <a name="log-naming-conventions"></a>Logga namngivningskonventioner
Varje logg skrivs i följande format.

    <service-name>/YYYY/MM/DD/hhmm/<counter>.log

I följande tabell beskrivs alla attribut i namnet på loggen.

| Attribut | Beskrivning |
| --- | --- |
| < tjänstnamn > |Namnet på storage-tjänst. Till exempel: blob-, tabell- eller köberoenden. |
| ÅÅÅÅ |Fyrsiffriga år för loggen. Till exempel: 2011. |
| MM |Månad med två siffror i loggen. Till exempel: 07. |
| DD |Månad med två siffror i loggen. Till exempel: 07. |
| hh |Två siffror timme som anger den första timmen för loggarna, i 24-timmarsformat UTC. Till exempel: 18. |
| mm |De två siffror som anger den första minuten för loggarna. Det här värdet stöds inte i den aktuella versionen av Storage Analytics och dess värde ska alltid vara 00. |
| <counter> |En Nollbaserad räknare med sex siffror som anger antalet loggen blob som genererats för storage-tjänst på en timme tidsperiod. Den här räknaren startar vid 000000. Till exempel: 000001. |

Följande är ett fullständigt exempel loggnamn som kombinerar i föregående exempel.

    blob/2011/07/31/1800/000001.log

Följande är ett exempel på en URI som kan användas för åtkomst till den tidigare loggen.

    https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log

När en begäran om lagring loggas motsvarar resulterande loggnamn timmen när den begärda åtgärden slutförts. Om exempelvis en begäran om GetBlob slutfördes klockan 6:30. loggen skulle skrivas med prefixet på 2011-7-31:`blob/2011/07/31/1800/`

### <a name="log-metadata"></a>Log-metadata
Alla loggen BLOB lagras med metadata som kan användas för att identifiera vilka loggningsdata som innehåller blob. I följande tabell beskrivs varje metadataattribut.

| Attribut | Beskrivning |
| --- | --- |
| LogType |Beskriver om loggen innehåller information som rör läsa, skriva eller ta bort. Det här värdet kan innehålla en typ eller en kombination av alla tre, avgränsade med kommatecken. Exempel 1: skrivning. Exempel 2: läsa, skriva; Exempel 3: läsa, skriva, ta bort. |
| StartTime |Den tidigaste tidpunkten för en post i loggen, i formatet ÅÅÅÅ-MM-ddTHH. Till exempel: 2011-07-31T18:21:46Z. |
| Sluttid |Senaste tid för en post i loggen, i formatet ÅÅÅÅ-MM-ddTHH. Till exempel: 2011-07-31T18:22:09Z. |
| LogVersion |Versionen av loggformatet. Det enda värdet som stöds är för närvarande 1.0. |

Följande lista visar fullständigt exempel metadata med hjälp av föregående exempel.

* LogType = skriva
* StartTime = 2011-07-31T18:21:46Z
* EndTime = 2011-07-31T18:22:09Z
* LogVersion = 1.0

### <a name="accessing-logging-data"></a>Åtkomst till loggningsdata
Alla data i den `$logs` behållare kan nås med hjälp av API: er för Blob-tjänsten, inklusive .NET API: er som tillhandahålls av Azure hanterade biblioteket. Lagringskontoadministratören kan läsa och ta bort loggar, men det går inte att skapa eller uppdatera dem. Både den log-metadata och loggnamn kan användas när du frågar efter en logg. Det är möjligt för en given timme loggarna visas i ordning, men metadata anger alltid timespan poster i en logg. Därför kan du använda en kombination av namn på loggen och metadata när du söker efter en viss logg.

## <a name="about-storage-analytics-metrics"></a>Om Storage Analytics mätvärden
Storage Analytics kan lagra mått som innehåller aggregerade statistik och kapacitet transaktionsdata om begäranden till en storage-tjänst. Transaktioner rapporteras på båda API åtgärden nivå samt på tjänstnivå lagring och kapacitet rapporteras på tjänstnivå lagring. Mätvärdesdata kan användas för att analysera lagringskvoten på tjänsten, diagnostisera problem med begäranden som görs mot lagringstjänsten och för att förbättra prestanda för program som använder en tjänst.

Om du vill använda Storage Analytics, måste du aktivera den separat för varje tjänst som du vill övervaka. Du kan aktivera det från den [Azure Portal](https://portal.azure.com). Mer information finns i [övervaka ett lagringskonto i Azure Portal](storage-monitor-storage-account.md). Du kan också aktivera Storage Analytics programmässigt via REST API eller klientbiblioteket. Använd den **hämta tjänstegenskaper** åtgärder för att aktivera Storage Analytics för varje tjänst.

### <a name="transaction-metrics"></a>Transaktionen mått
En kraftfull uppsättning data registreras med timvis eller minuters intervall för varje lagringstjänsten begärde API-åtgärd, inklusive ingång-/ utgång, tillgänglighet, fel, och kategoriseras begäran procenttal. Du kan se en fullständig lista över transaktionsinformation i den [Storage Analytics mätvärden tabellschemat](https://msdn.microsoft.com/library/hh343264.aspx) avsnittet.

Transaktionsdata registreras på två nivåer – servicenivån och åtgärden API-nivå. På tjänstnivå begärt statistik som sammanfattar alla API: et skrivs till en tabell entitet varje timme även om inga begäranden har gjorts till tjänsten. På API-nivå för åtgärden skrivs statistik enbart till en entitet om åtgärden begärdes inom den timmen.

Till exempel om du utför en **GetBlob** åtgärden på tjänsten Blob Storage Analytics mätvärden kommer loggar begäran och inkludera den i den sammanställda data för båda Blob-tjänsten samt de **GetBlob** igen. Men om inget **GetBlob** åtgärden begärs under timme, en entitet kommer inte att skriva till den `$MetricsTransactionsBlob` för den åtgärden.

Transaktionen mått registreras för både användarförfrågningar och förfrågningar som görs av Storage Analytics sig själv. Till exempel registreras begäranden av Storage Analytics att skriva loggar och tabellentiteter. Mer information om hur dessa begäranden debiteras finns [Storage Analytics och fakturering för](https://msdn.microsoft.com/library/hh360997.aspx).

### <a name="capacity-metrics"></a>Kapacitet mått
> [!NOTE]
> Kapacitetsdata finns för närvarande endast tillgängligt för Blob-tjänst. Kapacitet mått för tabelltjänsten och kötjänsten blir tillgänglig i framtida versioner av Storage Analytics.
> 
> 

Kapacitet data registreras dagligen för ett lagringskonto Blob-tjänsten och två tabellentiteter skrivs. En entitet innehåller statistik för användardata och den andra innehåller statistik om den `$logs` blob-behållare som används av Storage Analytics. Den `$MetricsCapacityBlob` tabellen innehåller följande statistik:

* **Kapacitet**: hur mycket lagringsutrymme som används av storage-konto Blob-tjänsten, i byte.
* **ContainerCount**: antal blobbbehållare i Blob storage-konto-tjänsten.
* **ObjectCount**: antal bekräftade och ogenomförda block eller sidan blobbar i Blob storage-konto-tjänsten.

Mer information om kapacitetsmått finns [Storage Analytics mätvärden tabellschemat](https://msdn.microsoft.com/library/hh343264.aspx).

### <a name="how-metrics-are-stored"></a>Hur mått lagras
Alla mätvärdesdata för var och en av lagringstjänsterna lagras i tre tabeller som är reserverade för tjänsten: en tabell för transaktionsinformation, en tabell för minut transaktionsinformation och en annan tabell för kapacitetsinformation. Transaktionen och minut transaktionsinformation består av data för begäran och svar och kapacitetsinformation består av användningsdata för lagring. Mätvärden för timme, minut mätvärden och kapacitet för ett lagringskonto Blob-tjänsten kan nås i tabeller som är namngivna enligt beskrivningen i följande tabell.

| Mått nivå | Tabellnamn | Versioner som stöds |
| --- | --- | --- |
| Varje timme statistik, primär plats |$MetricsTransactionsBlob <br/>$MetricsTransactionsTable <br/> $MetricsTransactionsQueue |Versioner före 2013-08-15 endast. När dessa namn stöds fortfarande, rekommenderas att du istället använda tabeller som anges nedan. |
| Varje timme statistik, primär plats |$MetricsHourPrimaryTransactionsBlob <br/>$MetricsHourPrimaryTransactionsTable <br/>$MetricsHourPrimaryTransactionsQueue |Alla versioner, inklusive 2013-08-15. |
| Minut statistik, primär plats |$MetricsMinutePrimaryTransactionsBlob <br/>$MetricsMinutePrimaryTransactionsTable <br/>$MetricsMinutePrimaryTransactionsQueue |Alla versioner, inklusive 2013-08-15. |
| Varje timme statistik, sekundär plats |$MetricsHourSecondaryTransactionsBlob <br/>$MetricsHourSecondaryTransactionsTable <br/>$MetricsHourSecondaryTransactionsQueue |Alla versioner, inklusive 2013-08-15. Läsbehörighet geo-redundant replikering måste aktiveras. |
| Minut statistik, sekundär plats |$MetricsMinuteSecondaryTransactionsBlob <br/>$MetricsMinuteSecondaryTransactionsTable <br/>$MetricsMinuteSecondaryTransactionsQueue |Alla versioner, inklusive 2013-08-15. Läsbehörighet geo-redundant replikering måste aktiveras. |
| Kapacitet (endast Blob-tjänst) |$MetricsCapacityBlob |Alla versioner, inklusive 2013-08-15. |

Dessa tabeller skapas automatiskt när Storage Analytics har aktiverats för ett lagringskonto. De kan nås via namnområdet för lagringskontot, till exempel:`https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")`

### <a name="accessing-metrics-data"></a>Dataåtkomst mått
Alla data i tabellerna mått kan nås med hjälp av tabelltjänsten API: er, inklusive .NET API: er som tillhandahålls av Azure hanterade biblioteket. Lagringskontoadministratören kan läsa och ta bort tabellentiteter, men det går inte att skapa eller uppdatera dem.

## <a name="billing-for-storage-analytics"></a>Fakturering för Storage Analytics
Alla mätvärdesdata skrivs av tjänster i ett lagringskonto. Därför är varje skrivåtgärd som utförs av Storage Analytics fakturerbar. Dessutom är mängden lagringsutrymme som används av mätvärdesdata också fakturerbar.

Följande åtgärder utförs av Storage Analytics är fakturerbar:

* Begäranden om att skapa BLOB för loggning. 
* Begäranden om att skapa tabellentiteter för mått.

Om du har konfigurerat en databevarandeprincip debiteras inte du för att ta borttagningstransaktioner när Storage Analytics tar bort gamla data för loggning och mått. Ta borttagningstransaktioner från en klient är dock fakturerbar. Läs mer om bevarandeprinciper [ange en Storage Analytics Databevarandeprincip](https://msdn.microsoft.com/library/azure/hh343263.aspx).

### <a name="understanding-billable-requests"></a>Förstå fakturerbar begäranden
Alla begäranden som görs till ett konto storage-tjänsten är antingen fakturerbar eller icke Debiterbart. Storage Analytics loggar varje enskild begäran till en tjänst, inklusive ett statusmeddelande som anger hur begäran behandlades. På liknande sätt, lagrar Storage Analytics mätvärden för både en tjänst och API-driften av tjänsten, inklusive procenttal och antalet vissa statusmeddelanden. Sammantaget ser kan dessa funktioner hjälpa dig analysera dina fakturerbar begäranden, göra förbättringar för ditt program och diagnostisera problem med förfrågningar till dina tjänster. Mer information om fakturering finns [förstå Azure Storage fakturerings - bandbredd, transaktioner och kapacitet](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx).

När du tittar på Storage Analytics data, du kan använda tabellerna i den [Storage Analytics loggade åtgärder och statusmeddelanden](https://msdn.microsoft.com/library/azure/hh343260.aspx) avsnittet för att fastställa vilka begäranden som fakturerbar. Du kan sedan jämföra loggar och mått data till statusmeddelanden för att se om du har debiteras för en viss begäran. Du kan också använda tabellerna i avsnittet ovan för att undersöka tillgänglighet för en lagringstjänsten eller enskilda API-åtgärd.

## <a name="next-steps"></a>Nästa steg
### <a name="setting-up-storage-analytics"></a>Konfigurera Storage Analytics
* [Övervaka ett lagringskonto i Azure Portal](storage-monitor-storage-account.md)
* [Aktivera och konfigurera Storage Analytics](https://msdn.microsoft.com/library/hh360996.aspx)

### <a name="storage-analytics-logging"></a>Storage Analytics loggning
* [Om Storage Analytics loggning](https://msdn.microsoft.com/library/hh343262.aspx)
* [Storage Analytics loggformat](https://msdn.microsoft.com/library/hh343259.aspx)
* [Storage Analytics loggade åtgärder och statusmeddelanden](https://msdn.microsoft.com/library/hh343260.aspx)

### <a name="storage-analytics-metrics"></a>Storage Analytics mätvärden
* [Om Storage Analytics mätvärden](https://msdn.microsoft.com/library/hh343258.aspx)
* [Storage Analytics mätvärden tabellens Schema](https://msdn.microsoft.com/library/hh343264.aspx)
* [Storage Analytics loggade åtgärder och statusmeddelanden](https://msdn.microsoft.com/library/hh343260.aspx)  

