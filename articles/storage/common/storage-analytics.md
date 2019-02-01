---
title: Använd Azure Storage analytics för att samla in loggar och mått data | Microsoft Docs
description: Storage Analytics kan du spåra mätvärden för alla lagringstjänster och samla in loggar för Blob, Queue och Table storage.
services: storage
author: roygara
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 03/03/2017
ms.author: rogarana
ms.subservice: common
ms.openlocfilehash: 9cd4845bcf107941f969255eb223567d4341ea41
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55508512"
---
# <a name="storage-analytics"></a>Lagringsanalys

Med Azure Storage Analytics kan du logga och skapa statistik för ett lagringskonto. Du kan använda dessa data för att spåra förfrågningar, analysera användningstrender och diagnostisera problem med lagringskontot.

Om du vill använda Storage Analytics, måste du aktivera det individuellt för varje tjänst som du vill övervaka. Du kan aktivera det från den [Azure-portalen](https://portal.azure.com). Mer information finns i [övervaka ett lagringskonto i Azure Portal](storage-monitor-storage-account.md). Du kan också aktivera Storage Analytics programmässigt via REST API eller klientbiblioteket. Använd den [hämta egenskaper för Blob Service](https://msdn.microsoft.com/library/hh452239.aspx), [hämta egenskaper för kötjänst](https://msdn.microsoft.com/library/hh452243.aspx), [hämta egenskaper för tabellen](https://msdn.microsoft.com/library/hh452238.aspx), och [hämta filegenskaper Service](https://msdn.microsoft.com/library/mt427369.aspx)åtgärder för att aktivera Storage Analytics för varje tjänst.

Sammanställda data lagras i en välkänd blob (för loggning) och välkänd tabeller (för mått), som kan nås med hjälp av Blob- och Table service API: er.

Lagringsanalys har en 20 TB gräns för mängden lagrade data som är oberoende av den totala gränsen för ditt lagringskonto. Läs mer om fakturering och datalagringsprinciper [Lagringsanalys och fakturering](https://msdn.microsoft.com/library/hh360997.aspx). Läs mer om lagringskontogränser [skalbarhet för lagring av Azure- och prestandamål](storage-scalability-targets.md).

Utförliga instruktioner om hur du använder Storage Analytics och andra verktyg för att identifiera, diagnostisera och felsöka Azure Storage-relaterade problem, se [övervaka, diagnostisera och Felsök Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="about-storage-analytics-logging"></a>Om loggningen i Storage analytics
Lagringsanalys loggar detaljerad information om lyckade och misslyckade begäranden till en lagringstjänst. Den här informationen kan användas för att övervaka enskilda begäranden och diagnostisera problem med en lagringstjänst. Förfrågningar loggas på basis av bästa prestanda.

Loggposter skapas endast om det finns storage service-aktivitet. Till exempel skapas ett storage-konto har aktivitet i dess Blob service, men inte i dess tabell- eller -tjänster, endast loggar som hör till Blob-tjänsten.

Storage Analytics loggning är inte tillgänglig för Azure Files.

### <a name="logging-authenticated-requests"></a>Autentiserade loggningsbegäranden
Följande typer av autentiserade begäranden loggas:

* Lyckade begäranden.
* Misslyckade förfrågningar, inklusive timeout, begränsning, nätverk, auktorisering och andra fel.
* Begäranden med hjälp av en signatur för delad åtkomst (SAS), inklusive misslyckade och lyckade förfrågningar.
* Begäranden till analytics-data.

Begäranden som görs av Storage Analytics, till exempel log skapas eller tas bort, loggas inte. En fullständig lista över data som loggats dokumenteras i den [Storage Analytics loggade åtgärder och statusmeddelanden](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages.md) och [Storage Analytics loggformat](/rest/api/storageservices/storage-analytics-log-format.md) ämnen.

### <a name="logging-anonymous-requests"></a>Logga anonyma förfrågningar
Följande typer av anonyma förfrågningar loggas:

* Lyckade begäranden.
* Serverfel.
* Timeout-fel för både klienten och servern.
* Misslyckade GET-begäranden med felkoden 304 (har inte ändrats).

Alla övriga misslyckade anonyma förfrågningar loggas inte. En fullständig lista över data som loggats dokumenteras i den [Storage Analytics loggade åtgärder och statusmeddelanden](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages.md) och [Storage Analytics loggformat](/rest/api/storageservices/storage-analytics-log-format.md) ämnen.

### <a name="how-logs-are-stored"></a>Hur loggar lagras
Alla loggar lagras i blockblob-objekt i en behållare med namnet $logs som skapas automatiskt när Lagringsanalys har aktiverats för ett lagringskonto. Behållaren $logs finns i blob-namnområdet för storage-konto, till exempel: `http://<accountname>.blob.core.windows.net/$logs`. Den här behållaren kan inte tas bort när Storage Analytics har aktiverats, även om dess innehåll kan tas bort.

> [!NOTE]
> Behållaren $logs visas inte när en behållare med åtgärden utförs, till exempel den [ListContainers](https://msdn.microsoft.com/library/azure/dd179352.aspx) metod. Den måste kunna nås direkt. Du kan till exempel använda den [ListBlobs](https://msdn.microsoft.com/library/azure/dd135734.aspx) metod för att komma åt blobarna i den `$logs` behållare.
> Eftersom förfrågningar loggas, ska Storage Analytics överföra mellanresultat som block. Lagringsanalys kommer med jämna mellanrum, genomför de här block och göra dem tillgängliga som en blob.
>
>

Dubbla poster kan finnas för loggar som skapas i en och samma timme. Du kan avgöra om en post är en dubblett genom att markera den **RequestId** och **åtgärden** tal.

### <a name="log-naming-conventions"></a>Logga namngivningskonventioner
Varje logg skrivs i följande format.

    <service-name>/YYYY/MM/DD/hhmm/<counter>.log

I följande tabell beskrivs alla attribut i namnet på loggen.

| Attribut | Beskrivning |
| --- | --- |
| <service-name> |Namnet på storage-tjänsten. Till exempel: blob-, tabell- eller kön. |
| ÅÅÅÅ |Fyrsiffrigt år för loggen. Exempel: 2011. |
| MM |Månad med två siffror i loggen. Exempel: 07. |
| DD |Månad med två siffror i loggen. Exempel: 07. |
| hh |En timme med två siffror som anger från timme för loggarna i 24-timmarsformat UTC. Exempel: 18. |
| mm |De två siffror som anger från minut för loggarna. Det här värdet stöds inte i den aktuella versionen av Storage Analytics och dess värde är alltid 00. |
| <counter> |Ett nollbaserat räknare med sex siffror som anger antalet loggblobarna som genererats för storage-tjänsten i en timme lång tid. Den här räknaren börjar vid 000000. Exempel: 000001. |

Följande är ett fullständigt exempel loggnamn som kombinerar i föregående exempel.

    blob/2011/07/31/1800/000001.log

Följande är ett exempel på URI: N som kan användas för att få åtkomst till den tidigare loggen.

    https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log

När en begäran om storage loggas, motsvarar det resulterande Loggnamnet timme när den begärda åtgärden har slutförts. Exempel: om en GetBlob begäran slutfördes klockan 6:30. den 7/31/2011 skulle loggen skrivas med följande prefix: `blob/2011/07/31/1800/`

### <a name="log-metadata"></a>Log-metadata
Alla loggblobarna lagras med metadata som kan användas för att identifiera vilka loggning blobben innehåller. I följande tabell beskrivs alla metadataattribut.

| Attribut | Beskrivning |
| --- | --- |
| LogType |Beskriver om loggen innehåller information om för att läsa, skriva eller ta bort åtgärder. Det här värdet kan innehålla en typ eller en kombination av alla tre, avgränsade med kommatecken. Exempel 1: skrivning. Exempel 2: läsa, skriva; Exempel 3: läsa, skriva, ta bort. |
| startTime |Den tidigaste tidpunkten för en post i loggen, i formatet ÅÅÅÅ-MM-: ssZ. Exempel: 2011-07-31T18:21:46Z. |
| endTime |Senaste tid för en post i loggen, i formatet ÅÅÅÅ-MM-: ssZ. Exempel: 2011-07-31T18:22:09Z. |
| LogVersion |Versionen av loggformatet. Det enda värdet som stöds är för närvarande 1.0. |

Följande lista visar hela exemplet metadata med hjälp av föregående exempel.

* LogType=write
* StartTime=2011-07-31T18:21:46Z
* EndTime=2011-07-31T18:22:09Z
* LogVersion=1.0

### <a name="accessing-logging-data"></a>Åtkomst till loggningsdata
Alla data i den `$logs` behållare kan nås med hjälp av Blob-tjänstens API: er, inklusive .NET API: er tillhandahålls av Azure hanterade biblioteket. Kontoadministratör lagring kan läsa och ta bort loggar, men det går inte att skapa eller uppdatera dem. Både den log-metadata och loggnamn kan användas när du frågar efter en logg. Det är möjligt för en viss timme loggarna visas i fel ordning, men metadata som anger alltid timespan av poster i en logg. Därför kan du använda en kombination av loggen namn och metadata när du söker efter en viss logg.

## <a name="about-storage-analytics-metrics"></a>Om Storage Analytics mätvärden
Lagringsanalys kan lagra mätvärden som innehåller aggregerad Transaktionsstatistik och kapacitetsdata om förfrågningar till en lagringstjänst. Transaktioner rapporteras på både den API-åtgärdsnivå samt på tjänstnivå lagring och kapacitet rapporteras på tjänstnivå lagring. Måttdata kan användas för att analysera användning av storage-tjänsten, diagnostisera problem med begäranden som görs mot tjänsten storage och förbättra prestanda för program som använder en tjänst.

Om du vill använda Storage Analytics, måste du aktivera det individuellt för varje tjänst som du vill övervaka. Du kan aktivera det från den [Azure-portalen](https://portal.azure.com). Mer information finns i [övervaka ett lagringskonto i Azure Portal](storage-monitor-storage-account.md). Du kan också aktivera Storage Analytics programmässigt via REST API eller klientbiblioteket. Använd den **hämta tjänstegenskaper** åtgärder för att aktivera Storage Analytics för varje tjänst.

### <a name="transaction-metrics"></a>Transaktionsmått
En stabil uppsättning data registreras med timvis eller minuters intervall för varje lagringstjänst och den begärda API-åtgärden, inklusive ingående/utgående trafik, tillgänglighet, fel, och kategoriseras begäran procenttal. Du kan se en fullständig lista över transaktionsuppgifter i den [Schema över Måttabeller i Storage Analytics](https://msdn.microsoft.com/library/hh343264.aspx) avsnittet.

Transaktionsdata registreras på två nivåer – servicenivån och API-åtgärdsnivå. På servicenivån begärda statistik som sammanfattar alla API-åtgärder skrivs till en tabellentitet varje timme även om inga ansökningar har gjorts till tjänsten. På åtgärdsnivå API skrivs statistik enbart till en entitet om åtgärden begärdes den timmen.

Exempel: Om du utför en **GetBlob** åtgärden på din Blob-tjänst, mätvärden i Storage Analytics kommer loggar begäran och inkludera den i den sammanställda data för både Blob service samt de **GetBlob** åtgärden. Men om ingen **GetBlob** åtgärd har begärts under timmen, en entitet kommer inte att skriva till den `$MetricsTransactionsBlob` för den åtgärden.

Transaktionsmått registreras för både användarförfrågningar och begäranden som görs av Lagringsanalys själva. Till exempel registreras begäranden av Storage Analytics för att skriva loggar och tabellenheter. Läs mer om hur dessa begäranden faktureras [Lagringsanalys och fakturering](https://msdn.microsoft.com/library/hh360997.aspx).

### <a name="capacity-metrics"></a>Kapacitet mått
> [!NOTE]
> Kapacitet är för närvarande endast tillgängliga för Blob-tjänsten.
>
>

Registreras kapacitetsdata varje dag för ett lagringskontos Blob service och två tabellenheter skrivs. En entitet innehåller statistik för användardata och den andra innehåller statistik om den `$logs` blob-behållare som används av Storage Analytics. Den `$MetricsCapacityBlob` tabell innehåller följande statistik:

* **Kapacitet**: Mängden lagringsutrymme som används av lagringskontots Blob service i byte.
* **ContainerCount**: Antal blob-behållare i lagringskontots Blob service.
* **ObjectCount**: Antal allokerade och ogenomförda block- eller sidtyp blobar i lagringskontots Blob service.

Läs mer om kapacitetsmåtten [Schema över Måttabeller i Storage Analytics](/rest/api/storageservices/storage-analytics-metrics-table-schema.md).

### <a name="how-metrics-are-stored"></a>Hur mått lagras
Alla mätvärden för var och en av lagringstjänsterna lagras i tre tabeller som är reserverade för tjänsten: en tabell för transaktionsinformation, en tabell för minut transaktionsinformation och en annan tabell för kapacitetsinformation. Transaktionen och minut transaktionsinformation består av data för begäran och svar och kapacitetsinformation består av användningsdata för lagring. Timmått, minutmått och kapacitet för ett lagringskontos Blob service kan nås i tabeller som namnges enligt beskrivningen i följande tabell.

| Mått-nivå | Tabellnamn | Versioner som stöds |
| --- | --- | --- |
| Mätvärden för varje timme, primär plats |$MetricsTransactionsBlob <br/>$MetricsTransactionsTable <br/> $MetricsTransactionsQueue |Versioner före 2013-08-15 endast. Dessa namn finns fortfarande stöd för rekommenderar vi att du istället använda registren nedan. |
| Mätvärden för varje timme, primär plats |$MetricsHourPrimaryTransactionsBlob <br/>$MetricsHourPrimaryTransactionsTable <br/>$MetricsHourPrimaryTransactionsQueue |Alla versioner, inklusive 2013-08-15. |
| Minutmått, primär plats |$MetricsMinutePrimaryTransactionsBlob <br/>$MetricsMinutePrimaryTransactionsTable <br/>$MetricsMinutePrimaryTransactionsQueue |Alla versioner, inklusive 2013-08-15. |
| Mätvärden för varje timme, sekundär plats |$MetricsHourSecondaryTransactionsBlob <br/>$MetricsHourSecondaryTransactionsTable <br/>$MetricsHourSecondaryTransactionsQueue |Alla versioner, inklusive 2013-08-15. Read-access geo-redundant replikering måste aktiveras. |
| Minutmått, sekundär plats |$MetricsMinuteSecondaryTransactionsBlob <br/>$MetricsMinuteSecondaryTransactionsTable <br/>$MetricsMinuteSecondaryTransactionsQueue |Alla versioner, inklusive 2013-08-15. Read-access geo-redundant replikering måste aktiveras. |
| Kapacitet (endast Blob-tjänst) |$MetricsCapacityBlob |Alla versioner, inklusive 2013-08-15. |

Dessa tabeller skapas automatiskt när Storage Analytics är aktiverat för ett lagringskonto. De kan nås via namnområdet för storage-konto, till exempel: `https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")`

### <a name="accessing-metrics-data"></a>Komma åt mätvärden
Alla data i tabellerna mått kan nås med hjälp av Table service API: er, inklusive .NET API: er tillhandahålls av Azure hanterade biblioteket. Kontoadministratör lagring kan läsa och ta bort tabellenheter, men det går inte att skapa eller uppdatera dem.

## <a name="billing-for-storage-analytics"></a>Faktureringen för Storage Analytics
Alla mätvärden skrivs av tjänster i ett lagringskonto. Därmed kan är varje skrivåtgärd som utförs av Storage Analytics fakturerbara. Dessutom är mängden lagringsutrymme som används av mätvärden också faktureringsbara.

Följande åtgärder utförs av Storage Analytics är fakturerbara:

* Begäranden om att skapa blobar för loggning.
* Begäranden om att skapa tabellentiteter för mått.

Om du har konfigurerat en databevarandeprincip, debiteras du inte för ta borttagningstransaktioner när Lagringsanalys tar bort gamla data för loggning och mått. Ta borttagningstransaktioner från en klient är dock faktureringsbara. Läs mer om principer för kvarhållning [ställa in en bevarandeprincip för Storage Analytics Data](https://msdn.microsoft.com/library/azure/hh343263.aspx).

### <a name="understanding-billable-requests"></a>Förstå debiterbara begäranden
Varje begäran som görs till ett konto lagringstjänst är fakturerbara eller icke-fakturerbara. Lagringsanalys loggar varje enskild begäran till en tjänst, inklusive ett statusmeddelande som anger hur begäran behandlades. På samma sätt lagrar Storage Analytics mätvärden för både en tjänst och API-åtgärder för tjänsten, inklusive procenttal och antalet vissa statusmeddelanden. Tillsammans de här funktionerna kan hjälpa dig att analysera din debiterbara begäranden, gör förbättringar för ditt program och diagnostisera problem med förfrågningar till dina tjänster. Läs mer om fakturering, [förstå Azure Storage-fakturering – bandbredd, transaktioner och kapacitet](https://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx).

När du granskar data i Storage Analytics kan du använda tabellerna i den [Storage Analytics loggade åtgärder och statusmeddelanden](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages.md) avsnittet för att avgöra vilka begäranden som fakturerbara. Du kan sedan jämföra dina loggar och mätdata till statusmeddelanden för att se om du debiterades för en viss begäran. Du kan också använda tabellerna i det föregående avsnittet för att undersöka tillgänglighet för en lagringstjänst eller för enskilda API-åtgärden.

## <a name="next-steps"></a>Nästa steg
* [Övervaka ett lagringskonto i Azure Portal](storage-monitor-storage-account.md)
* [Loggningen i Storage Analytics](https://msdn.microsoft.com/library/hh343262.aspx)
* [Mätvärden i Storage Analytics](https://msdn.microsoft.com/library/hh343258.aspx)
