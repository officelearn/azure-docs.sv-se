---
title: Migrera dina data till SQL Data Warehouse | Microsoft Docs
description: Tips för att migrera dina data till Azure SQL Data Warehouse för utveckling av lösningar.
services: sql-data-warehouse
documentationcenter: NA
author: sqlmojo
manager: jhubbard
editor: ''
ms.assetid: d78f954a-f54c-4aa4-9040-919bc6414887
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: migrate
ms.date: 06/29/2017
ms.author: joeyong;barbkess
ms.openlocfilehash: 4fbab223963989eab16cecea2facd9c15ba646d4
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="migrate-your-data"></a>Migrera dina Data
Data kan flyttas från olika källor till din SQL Data Warehouse med en olika verktyg.  ADF kopiera, SSIS och bcp kan alla användas för att uppnå det här målet. Som mängden data ökar bör du dock tänka bryta ned migrering av data i steg. Detta ger möjlighet att optimera varje steg både för prestanda och återhämtning att säkerställa en smidig datamigrering.

Den här artikeln beskrivs först enkla Migreringsscenarier ADF kopia, SSIS och bcp. Den sedan studera lite djupare hur migreringen kan optimeras.

## <a name="azure-data-factory-adf-copy"></a>Azure Data Factory (ADM) kopiera
[Kopiera ADF] [ ADF Copy] är en del av [Azure Data Factory][Azure Data Factory]. Du kan använda ADF kopiera för att exportera data till flat-filer som finns på lokal lagring till fjärranslutna flat-filer som lagras i Azure blob storage eller direkt till SQL Data Warehouse.

Om dina data startar i flat-filer, så behöver du först överföra den till Azure storage blob innan du påbörjar en belastning till SQL Data Warehouse. När data överförs till Azure blob storage kan du använda [ADF kopiera] [ ADF Copy] igen för att skicka data till SQL Data Warehouse.

PolyBase ger också ett alternativ för högpresterande för inläsning av data. Dock innebär som använder två verktyg i stället för en. Om du behöver för att uppnå bästa prestanda och Använd sedan PolyBase. Om du vill använda en enda verktyg upplevelse (och data är inte massiv) är ADF svaret.


> 
> 

HEAD via följande artikel för vissa bra [ADF prover][ADF samples].

## <a name="integration-services"></a>Integrationstjänster
Integration Services (SSIS) är ett kraftfullt och flexibelt extrahera Transform och Load (ETL) som stöder komplexa arbetsflöden, DTS och flera alternativ för inläsning av data. Använda SSIS att helt enkelt överföra data till Azure eller som en del av en bredare migrering.

> [!NOTE]
> SSIS exportera till UTF-8 utan byte-ordningsmarkering i filen. Om du vill konfigurera detta måste du först använda komponenten härledd kolumn för att konvertera teckendata i dataflöde för att använda 65001 UTF-8-teckentabellen. När kolumnerna som har konverterats skriva data till flat fil-målnätverkskort säkerställer att 65001 också har markerats som en teckentabell för filen.
> 
> 

SSIS ansluter till SQL Data Warehouse precis som den ska ansluta till en SQL Server-distributionen. Dock behöver dina anslutningar använder en ADO.NET Anslutningshanteraren. Du bör också var noga med för att konfigurera den ”Använd massinfogning när det är tillgängligt” inställningen för att maximera genomströmningen. Mer information finns i [ADO.NET-målnätverkskort] [ ADO.NET destination adapter] artikeln om du vill veta mer om den här egenskapen

> [!NOTE]
> Ansluter till Azure SQL Data Warehouse med hjälp av OLEDB stöds inte.
> 
> 

Det finns dessutom alltid möjligheten att ett paket kan misslyckas på grund av att problem med begränsning eller nätverk. Design paket så att de kan återupptas vid fel, utan göra om fungerar som slutförda före felet.

Mer information finns i [SSIS-dokumentationen][SSIS documentation].

## <a name="bcp"></a>bcp
BCP är ett kommandoradsverktyg som har utformats för flat fil dataimport och export. Vissa omvandling kan ske under export av data. Om du vill utföra Använd enkla transformationer en fråga för att välja och transformera data. När exporterat, kan flat-filer sedan laddas direkt i målet SQL Data Warehouse-databas.

> [!NOTE]
> Det är ofta en bra idé att kapsla in omvandlingarna används under export av data i en vy i källsystemet. Detta säkerställer att logiken sparas och processen repeterbara.
> 
> 

Fördelarna med bcp är:

* Enkelhet. BCP kommandon är enkla att skapa och köra
* Nytt starta inläsningen. En gång exporterade belastningen kan vara körs många gånger

Begränsningar av bcp är:

* BCP fungerar med endast tabellform flata filer. Det fungerar inte med filer som XML- eller JSON
* Data transformation funktioner är begränsade till fasen export och är enkla till sin natur
* BCP har anpassats till att vara robust vid inläsning av data via internet. Instabilitet nätverket kan orsaka en Inläsningsfel.
* BCP är beroende av schemat finns i måldatabasen innan belastningen

Mer information finns i [Använd bcp för att läsa in data till SQL Data Warehouse][Use bcp to load data into SQL Data Warehouse].

## <a name="optimizing-data-migration"></a>Optimera datamigrering
Migrerar en SQLDW data kan delas effektivt upp till tre separata steg:

1. Export av källdata
2. Överföring av data till Azure
3. Läs in till måldatabasen SQLDW

Varje steg kan optimeras individuellt för att skapa en robust, starta om och flexibel migreringsprocessen som maximerar prestandan i varje steg.

## <a name="optimizing-data-load"></a>Optimera datainläsning
Titta på dessa i omvänd ordning för ett ögonblick; Det är det snabbaste sättet att läsa in data via PolyBase. Optimera för en PolyBase-inläsningen placeras krav på ovanstående steg så att det är bäst att förstå detta summa. De är:

1. Kodning av datafiler
2. Formatet för datafiler
3. Platsen för datafiler

### <a name="encoding"></a>Encoding
PolyBase kräver datafiler vara UTF-8- eller UTF-16FE. 



### <a name="format-of-data-files"></a>Formatet för datafiler
PolyBase kräver en fast radbegränsaren \n eller ny rad. Datafiler måste följa standarden. Det inte finns några begränsningar på avslutarna sträng eller en kolumn.

Du måste definiera alla kolumner i filen som en del av en extern tabell i PolyBase. Se till att alla exporterade kolumnerna är nödvändiga och att typerna som överensstämmer med kraven.

Kontrollera refererar tillbaka till den [migrera schemat] artikeln för information om vilka datatyper.

### <a name="location-of-data-files"></a>Platsen för datafiler
SQL Data Warehouse använder PolyBase för att läsa in data från Azure Blob Storage exklusivt. Följaktligen måste data ha först överförts till blob-lagring.

## <a name="optimizing-data-transfer"></a>Optimera dataöverföring
En av de långsammaste delarna av datamigrering är överföring av data till Azure. Inte bara nätverkets bandbredd kan bli ett problem utan också nätverket tillförlitlighet kan försvåra pågår. Är på Internet så att risken för överföringen eventuella fel som inträffar är rimligen kan som standard migrera data till Azure. Dessa fel kan dock kräver att data skickas på nytt helt eller delvis.

Lyckligtvis har du flera alternativ för att förbättra hastighet och återhämtning i den här processen:

### <a name="expressrouteexpressroute"></a>[ExpressRoute][ExpressRoute]
Du kanske vill använda [ExpressRoute] [ ExpressRoute] påskynda överföringen. [ExpressRoute] [ ExpressRoute] innehåller en privat upprättad anslutning till Azure så att anslutningen inte går via det offentliga internet. Under inga omständigheter är ett obligatoriskt steg. Dock förbättras dataflöde när skicka data till Azure från en lokal eller samplacering.

Fördelarna med att använda [ExpressRoute] [ ExpressRoute] är:

1. Ökad tillförlitlighet
2. Snabbare nätverkshastigheten
3. Lägre nätverksfördröjning
4. högre nätverkssäkerhet

[ExpressRoute] [ ExpressRoute] är bra för ett antal scenarier, inte bara migreringen.

Är du intresserad? Mer information och ange priser finns i [ExpressRoute dokumentation][ExpressRoute documentation].

### <a name="azure-import-and-export-service"></a>Azure Import och Export Service
Azure Import och Export Service är en process för överföring av data utformad för stora (GB ++) på massiv (TB ++) överföring av data till Azure. Det innebär att skriva data till diskar och leverera dem till ett Azure-datacenter. Diskinnehållet läses sedan in i Azure Storage Blobs å dina vägnar.

En översikt över för export importen är följande:

1. Konfigurera en Azure Blob Storage-behållare för att ta emot data
2. Exportera data till lokal lagring
3. Kopiera data till 3,5-tums SATA II/III hårddiskar verktyget [Azure Import/Export]
4. Skapa en Import-jobb med hjälp av Azure Import och Export-tjänst som tillhandahåller journalfiler som produceras av [Azure Import/Export verktyg]
5. Leverera diskarna utsedda Azure datacentret
6. Dina data överförs till Azure Blob Storage-behållare
7. Läsa in data i SQLDW med PolyBase

### <a name="azcopyazcopy-utility"></a>[AZCopy][AZCopy] verktyget
Den [AZCopy][AZCopy] verktyget är ett bra verktyg för att hämta dina data som överförs till Azure Storage-Blobbar. Den är utformad för små (MB ++) till mycket stora (GB ++) överföring av data. [AZCopy] har också utformats för att ge bra flexibel dataflöde vid överföring av data till Azure och det är ett bra alternativ för steget data transfer. En gång överförda kan du läsa in data med PolyBase i SQL Data Warehouse. Du kan även inkludera AZCopy i din SSIS-paket med hjälp av en uppgift ”köra processen”.

Om du vill använda AZCopy måste du först hämta och installera den. Det finns en [produktionsversion] [ production version] och en [förhandsversionen] [ preview version] tillgängliga.

Om du vill överföra en fil från din dator behöver du ett kommando som i exemplet nedan:

```
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:abc.txt
```

En sammanfattning av övergripande processen kan vara:

1. Konfigurera en Azure storage blob-behållare för att ta emot data
2. Exportera data till lokal lagring
3. AZCopy dina data i Azure Blob Storage-behållare
4. Läsa in data till SQL Data Warehouse med PolyBase

Fullständig dokumentation tillgänglig: [AZCopy][AZCopy].

## <a name="optimizing-data-export"></a>Optimera export av data
Förutom att säkerställa att exporten överensstämmer med kraven av PolyBase begära du att optimera export av data till förbättra processen ytterligare.



### <a name="data-compression"></a>Datakomprimering
PolyBase kan läsa gzip komprimerade data. Om du ska kunna komprimera data till gzip-filer kan du minimera mängden data som skickas över nätverket.

### <a name="multiple-files"></a>Flera filer
Dela upp stora tabeller i flera filer hjälper inte bara att exportera snabbare, hjälper också med överföring re-startability och den övergripande hanteringen av data en gång i Azure blob storage. En av de många bra funktionerna för PolyBase är att den ska läsa alla filer i en mapp och behandla det som en tabell. Det är därför en bra idé att isolera filer för varje tabell till en egen mapp.

PolyBase stöder också en funktion som kallas ”rekursiv mappen traversal”. Du kan använda den här funktionen för att ytterligare förbättra organisationen för att förbättra din datahantering exporterade data.

Mer information om hur du läser data med PolyBase finns [Använd PolyBase för att läsa in data till SQL Data Warehouse][Use PolyBase to load data into SQL Data Warehouse].

## <a name="next-steps"></a>Nästa steg
Mer information om migrering finns i [migrera lösningen till SQL Data Warehouse][Migrate your solution to SQL Data Warehouse].
För fler utvecklingstips, se [utvecklingsöversikt][development overview].

<!--Image references-->

<!--Article references-->
[AZCopy]: ../storage/common/storage-use-azcopy.md
[ADF Copy]: ../data-factory/v1/data-factory-data-movement-activities.md 
[ADF samples]: ../data-factory/v1/data-factory-samples.md
[ADF Copy examples]: ../data-factory/v1/data-factory-copy-activity-tutorial-using-visual-studio.md
[development overview]: sql-data-warehouse-overview-develop.md
[migrera schemat]: sql-data-warehouse-migrate-schema.md
[Migrate your solution to SQL Data Warehouse]: sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse development overview]: sql-data-warehouse-overview-develop.md
[Use bcp to load data into SQL Data Warehouse]: sql-data-warehouse-load-with-bcp.md
[Use PolyBase to load data into SQL Data Warehouse]: load-data-wideworldimportersdw.md


<!--MSDN references-->

<!--Other Web references-->
[Azure Data Factory]: http://azure.microsoft.com/services/data-factory/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[ExpressRoute documentation]: http://azure.microsoft.com/documentation/services/expressroute/

[production version]: http://aka.ms/downloadazcopy/
[preview version]: http://aka.ms/downloadazcopypr/
[ADO.NET destination adapter]: https://msdn.microsoft.com/library/bb934041.aspx
[SSIS documentation]: https://msdn.microsoft.com/library/ms141026.aspx
