---
title: Azure SQL Data Warehouse viktig September 2018 | Microsoft Docs
description: Viktig information för Azure SQL Data Warehouse.
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 10/08/2018
ms.author: mausher
ms.reviewer: twounder
ms.openlocfilehash: f8073fd746bc02c23efab914c45c61830942a677
ms.sourcegitcommit: 0fc99ab4fbc6922064fc27d64161be6072896b21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2018
ms.locfileid: "51577992"
---
# <a name="whats-new-in-azure-sql-data-warehouse-september-2018"></a>Vad är nytt i Azure SQL Data Warehouse? September 2018
Azure SQL Data Warehouse tar emot förbättringar kontinuerligt. Den här artikeln beskriver nya funktioner och ändringar som har införts i September 2018.

## <a name="new-lower-entry-point-for-sql-data-warehouse-gen2"></a>Nya lägre startpunkten för SQL Data Warehouse Gen2
I April 2018 [Microsoft annouced](https://azure.microsoft.com/blog/turbocharge-cloud-analytics-with-azure-sql-data-warehouse/) Azure SQL Data Warehouse Gen2 som erbjuder 5 gånger prestandan, 5 x beräkning skalan, 4 x samtidighet och obegränsad lagring. Enligt vad som anges i den [datalager i molnet Benchmark](https://gigaom.com/report/data-warehouse-in-the-cloud-benchmark/) av Gigaom, SQL-informationslager Gen2 **överlägsen Amazon Redshift med 42%**.

Gen2 är nu allmänt tillgängligt på en lägre post punkt för DWU500c så att du kan köra en mindre storlek data warehouse eller dev/test-miljöer med alla de senaste förbättringarna för tjänsten. Den nya startpunkten behåller alla Gen2 funktioner inklusive [Adaptiv cachelagring](https://azure.microsoft.com/blog/adaptive-caching-powers-azure-sql-data-warehouse-performance-gains/), [belysning snabbt Data blandning](https://azure.microsoft.com/blog/lightning-fast-query-performance-with-azure-sql-data-warehouse/), samt stöd för [i realtid datalagret](https://azure.microsoft.com/blog/enabling-real-time-data-warehousing-with-azure-sql-data-warehouse/).

## <a name="improved-availability-with-query-restartability"></a>Förbättrad tillgänglighet med frågan restartability
Vid körning av fråga valfritt antal problem kan uppstå som kan orsaka en fråga att misslyckas. Ett nätverksavbrott, ett maskinvarufel eller andra frånkoppling kan orsaka störningar. SQL Data Warehouse stöder nu fråga restartability för steg eller en SELECT-instruktion på servernivå-frågor. 

Med frågan Restartability startas automatiskt en fråga som är aktiverade som avbryts på grund av ett fel. Beroende på antalet steg, formen på frågan, eller om frågan avbröts under körning, SQL Data Warehouse-motorn startas antingen den fullständiga frågan eller kommer att återupptas från den senaste slutförda frågesteg. Frågan slutförs bara ur en slutanvändare synvinkel. 

## <a name="maintenance-scheduling-preview"></a>Underhåll schemaläggning (förhandsversion)
Azure SQL Data Warehouse Underhåll schemaläggning finns nu i förhandsversion. Den här nya funktionen integreras sömlöst Service Health planerat underhåll meddelanden, kontrollera Resource Health Monitor och Azure SQL Data Warehouse Underhåll schemaläggningstjänsten. Schemalägga Underhåll kan du schemalägga ett tidsfönster när det är lämpligt att ta emot nya funktioner, uppgraderingar, och korrigeringar.

Underhåll schemaläggning drar nytta av Azure Monitor och gör att kunder kan bestämma hur de vill meddelas om nära förestående underhållshändelser och som automatiserade flöden ska aktiveras för att hantera avbrott och minimera påverkan på sin verksamhet. Meddelanden kan innehålla en e-post eller SMS. 

## <a name="wide-row-support-in-polybase"></a>Stöd för många rad i Polybase
När du läser in data till SQL Data Warehouse, allmänna riktlinjer är att använda [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/design-elt-data-loading#options-for-loading-with-polybase) med finns stöd för parallell inläsning av data. Den här versionen aktiverar stöd för fler kolumner från 32 kB till 1MB - så att du kan läsa in tabeller med många radstorleken. Stöd för många rader förenklar processen för tabeller med många rader för datainläsning.

> [!Note]
> Den totala Radstorleken får inte överskrida 1 MB i storlek.

## <a name="additional-language-support"></a>Stöd för ytterligare språk
Den här versionen innehåller stöd för följande språkelement för T-SQL:

### <a name="stringsplit"></a>STRING_SPLIT
Den [STRING_SPLIT](https://docs.microsoft.com/sql/t-sql/functions/string-split-transact-sql) funktionen delar upp en teckensträng med angiven avgränsare. STRING_SPLIT är användbart i scenarier där en kolumn kan ha flera värden att tolka och infoga i en annan tabell för inläsning av data.

#### <a name="example"></a>Exempel
```sql
DECLARE @tags NVARCHAR(400) = 'clothing,road,,touring,bike';

SELECT
    value
FROM
    STRING_SPLIT(@tags, ',')
WHERE
    RTRIM(value) <> '';
```

### <a name="compressdecompress-functions"></a>KOMPRIMERA/DECOMPRESS funktioner
Den [KOMPRIMERA](https://docs.microsoft.com/sql/t-sql/functions/compress-transact-sql) / [DECOMPRESS](https://docs.microsoft.com/sql/t-sql/functions/decompress-transact-sql) funktioner kan du komprimera eller expandera en sträng som indata med GZIP-algoritmen.

#### <a name="example"></a>Exempel

```sql
SELECT
    name [name_original]
    , COMPRESS(name) [name_compressed]
    , CAST(DECOMPRESS(COMPRESS(name)) AS NVARCHAR(MAX)) [name_decompressed]
FROM
    sys.objects;
```

### <a name="if-exists-clause-for-dropping-views"></a>Om EXISTS-sats för att släppa vyer
Tillägg av instruktionen IF finns i den [släppa VYN](https://docs.microsoft.com/sql/t-sql/statements/drop-view-transact-sql) instruktionen förenklar T-SQL-kod som krävs för att ta bort vyn från datalagret. Om det finns-syntax, när en släppa Visa sekretesspolicy kommer ta bort vyn om det finns eller ignorerar instruktionen om vyn inte finns.

#### <a name="example"></a>Exempel
```sql
DROP VIEW IF EXISTS dbo.TestView;
```
```
Message
--------------------------------------------------
Commands completed successfully.

```

## <a name="improved-compilation-time-for-singleton-inserts-and-ddl-statements"></a>Förbättrad kompileringstid för Singleton-infogningar och DDL-instruktionerna 
Följa en traditionell extrahering, transformering och inläsning (ETL) modell för data infogas ofta leder till att köra en singleton-insert ([infoga värden](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql)) till en tabell i databasen. Den här versionen förbättrar prestandan för singleton-infogningsåtgärder genom att minska kompileringstid som krävs för att utföra den här typen av instruktionen. I vissa fall är observerade kompilering förbättringen upp till 3 gånger snabbare. Den här förbättringen kommer att minska den tid som krävs för att utföra en singleton-sats. 

Förbättring samt fördelar data lager med ett stort antal objekt genom att på ett liknande sätt minska frågetiden för kompilering för Data Definition Language (DDL) instruktioner till exempel skapa, ändra och ta bort. 

Slutligen minskar förbättring övergripande körningen av instruktioner som kör över breda tabeller - tabeller som har ett stort antal kolumner. Förbättring är en minskning under kompilering frågesteg som minskar den totala körningstiden för frågor.

## <a name="bug-fixes"></a>Felkorrigeringar

| Titel | Beskrivning |
|:---|:---|
| **Åtgärda när du skapar statistik på distributioner för unika restriktioner** | Den här snabbkorrigeringen löser ett fel som användarna stöter på när du anger som kör UPDATE STATISTICS med endast tabellen när tabellen har en unik begränsning som definierats. |
| **Åtgärda vid kompilering frågor via externa tabeller** | Den här snabbkorrigeringen löser ett fel som påverkas kompileringstid för frågor som rör externa tabeller.|
| **Åtgärda när du kör en instruktion med stora typer** | Åtgärda ett fel i förberedd instruktion kompilering med parametrarna som har deklarerats som en av de *stora* typer (nvarchar(max), varchar(max) och varbinary(max)). |
| **Åtgärda när ett fel uppstår för djupt kapslade frågor** | Ger ett tydligt felmeddelande när en djupt kapslade fråga överskrider gränserna för system.|
| **Korrigering för kompileringsfel när en instruktion som innehåller en korrelerad underfråga och en körningstid konstant** |Adresser kompileringsfel för frågor med en viss kombination av korrelerade delfrågor och körningstid konstanter (till exempel GETDATE()).|
| **Åtgärda Timeout för att hämta Lås för PDW-objekt och samtidighet fack för autostats** |Korrigera lägger till timeout för lås för att förhindra att autostats begäranden blockerar de ursprungliga begärandena under en längre tid.|

## <a name="next-steps"></a>Nästa steg
Nu när du vet lite om SQL Data Warehouse, lär du dig hur du snabbt [skapa ett SQL Data Warehouse][create a SQL Data Warehouse]. Om du inte har erfarenhet av Azure kan [Azure-ordlistan][Azure glossary] vara till hjälp om du stöter på ny terminologi. Eller så kan du se över några av de övriga SQL Data Warehouse-resurserna.  

* [Kundernas framgångsberättelser]
* [Bloggar]
* [Funktionsbegäranden]
* [Videoklipp]
* [Customer Advisory Team-bloggar]
* [Stack Overflow-forum]
* [Twitter]


[Bloggar]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Customer Advisory Team-bloggar]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Kundernas framgångsberättelser]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Funktionsbegäranden]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Stack Overflow-forum]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videoklipp]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md
