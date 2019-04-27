---
title: Migrera dina data till SQL Data Warehouse | Microsoft Docs
description: Tips för att migrera dina data till Azure SQL Data Warehouse för utveckling av lösningar.
services: sql-data-warehouse
author: jrowlandjones
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/17/2018
ms.author: jrj
ms.reviewer: igorstan
ms.openlocfilehash: 6a2acf602252ee4319f9a5eccef53a25d8e2dd7f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60748194"
---
# <a name="migrate-your-data"></a>Migrera dina Data
Data kan flyttas från olika källor i SQL Data Warehouse med en mängd verktyg.  ADF kopia och SSIS bcp kan alla användas för att uppnå det här målet. Som mängden data ökar bör du dock tänka bryta ned data migreringsprocessen i steg. Detta får du möjlighet att optimera varje steg både för prestanda och flexibilitet i fokus att säkerställa en smidig datamigrering.

Den här artikeln beskrivs först enkel Migreringsscenarier av ADF kopia och SSIS bcp. Den sedan titta lite djupare på hur migreringen kan optimeras.

## <a name="azure-data-factory-adf-copy"></a>Azure Data Factory (ADF) kopiera
[ADF kopia] [ ADF Copy] är en del av [Azure Data Factory][Azure Data Factory]. Du kan använda ADF kopia för att exportera data till flat-filer som finns på en lokal lagringsenhet till fjärranslutna flat-filer som lagras i Azure blob storage eller direkt till SQL Data Warehouse.

Om dina data börjar i flata filer så behöver du först överföra den till Azure storage-blob innan du påbörjar en belastning den till SQL Data Warehouse. När data har överförts till Azure blob-lagring kan du välja att använda [ADF kopia] [ ADF Copy] igen för att skicka data till SQL Data Warehouse.

PolyBase erbjuder även en högpresterande alternativ för inläsning av data. Som betyder dock med två verktyg i stället för en. Om du behöver för bästa prestanda och Använd sedan PolyBase. Om du vill att en enda verktyget upplevelse (och data är inte enorma) är ADF ditt svar.

Följ [den här självstudien](../data-factory/load-azure-sql-data-warehouse.md) att lära dig hur du använder ADF för att läsa in data i ditt informationslager.

## <a name="integration-services"></a>Integrationstjänster
Integration Services (SSIS) är ett kraftfullt och flexibelt extrahera transformera och inläsning (ETL) som stöder komplexa arbetsflöden, Dataomvandling och flera alternativ för inläsning av data. Använda SSIS för att helt enkelt överföra data till Azure eller som en del av en bredare migrering.

> [!NOTE]
> SSIS kan exportera till UTF-8 utan byteordningsmärket i filen. Om du vill konfigurera detta måste du först använda komponenten härledda kolumnen för att konvertera teckendata i dataflödet att använda 65001 UTF-8-teckentabellen. När kolumnerna som har konverterats, skriva data till flat fil-målnätverkskort som säkerställer att 65001 också har markerats som en teckentabell för filen.
> 
> 

SSIS ansluter till SQL Data Warehouse precis som den ska ansluta till en SQL Server-distribution. Men måste dina anslutningar använder en ADO.NET-Anslutningshanteraren. Du bör även noga med för att konfigurera den ”Använd massinfogning när det är tillgängligt” inställningen för att maximera genomströmningen. Finns det [ADO.NET-målnätverkskort] [ ADO.NET destination adapter] du lär dig mer om den här egenskapen

> [!NOTE]
> Ansluta till Azure SQL Data Warehouse med hjälp av OLEDB stöds inte.
> 
> 

Det finns dessutom alltid risken för att ett paket kan misslyckas på grund av problem med begränsning eller. Design paket så att de kan återupptas där felet inträffade, utan göra om fungerar som slutförda före felet.

Mer information finns i [SSIS-dokumentationen][SSIS documentation].

## <a name="bcp"></a>bcp
BCP är ett kommandoradsverktyg som är utformad för platt fil importera och exportera data. Vissa omvandlingen kan äga rum under export av data. Om du vill utföra använder enkla omvandlingar du en fråga att välja och transformera data. När exporterat, flat-filer kan sedan att läsa in direkt till mål-SQL Data Warehouse-databas.

> [!NOTE]
> Det är ofta en bra idé att kapsla in transformationer som används vid export av data i en vy i källsystemet. Detta säkerställer att logiken som sparas och processen är repeterbara.
> 
> 

Fördelarna med bcp är:

* Enkelhet. BCP-kommandon är enkla att skapa och köra
* Starta igen från inläsningen. En gång exporterade belastningen kan vara körs många gånger

Begränsningar av bcp är:

* BCP fungerar med endast tabellform flata filer. Det fungerar inte med filer som xml eller JSON
* Funktioner för omvandling av data är begränsad till endast export mellanlagring och är enkla sin natur
* BCP är inte avsedd att vara robust vid inläsning av data via internet. Alla nätverk instabilitet kan orsaka ett fel vid inläsning av.
* BCP är beroende av schemat finns i måldatabasen innan belastningen

Mer information finns i [Använd bcp för att läsa in data i SQL Data Warehouse][Use bcp to load data into SQL Data Warehouse].

## <a name="optimizing-data-migration"></a>Optimera datamigrering
Migrerar en SQLDW data kan delas effektivt upp till tre separata steg:

1. Export av källdata
2. Överföring av data till Azure
3. Läs in till måldatabasen SQLDW

Varje steg kan optimeras separat om du vill skapa en robust, starta från igen och robusta migreringsprocessen som maximerar prestanda vid varje steg.

## <a name="optimizing-data-load"></a>Optimera datainläsning
Titta på dessa i omvänd ordning för ett ögonblick; Det snabbaste sättet att läsa in data är via PolyBase. Optimera för en PolyBase-inläsningen placerar krav på föregående steg så det är bäst att förstå detta krav. De är:

1. Kodning av datafiler
2. Formatet för datafiler
3. Platsen för datafiler

### <a name="encoding"></a>Kodning
PolyBase kräver datafilerna som ska vara UTF-8- eller UTF-16FE. 



### <a name="format-of-data-files"></a>Formatet för datafiler
PolyBase innehåller principer för en fast radbegränsaren \n eller ny rad. Dina filer måste följa den här standarden. Det finns några begränsningar på sträng eller kolumnen avslutarna.

Du måste definiera alla kolumner i filen som en del av din extern tabell i PolyBase. Se till att alla exporterade kolumner krävs och att typerna som överensstämmer med kraven.

Se tillbaka den [migrera ditt schema] artikeln för information om datatyper som stöds.

### <a name="location-of-data-files"></a>Platsen för datafiler
SQL Data Warehouse polybase för att läsa in data från Azure Blob Storage exklusivt. Därför måste data ha först överförts till blob-lagring.

## <a name="optimizing-data-transfer"></a>Optimera dataöverföring
En av de långsammaste delarna av migrering av data är överföring av data till Azure. Inte bara nätverkets bandbredd kan bli ett problem utan också nätverk tillförlitlighet kan försvåra pågår. Är via internet så att risken för överföring fel uppstår sannolikt rimligen som standard som migrerar data till Azure. De här felen kan dock kräver att data skickas på nytt helt eller delvis.

Som tur är har du flera alternativ för att förbättra hastighet och flexibilitet i den här processen:

### <a name="expressrouteexpressroute"></a>[ExpressRoute][ExpressRoute]
Du kanske vill överväga att använda [ExpressRoute] [ ExpressRoute] påskynda överföringen. [ExpressRoute] [ ExpressRoute] innehåller en etablerad privat anslutning till Azure så att anslutningen inte går via det offentliga internet. Det här är inte menat ett obligatoriskt steg. Men det kan du förbättra dataflödet när push-överföra data till Azure från en lokal eller samplaceringsmiljö.

Fördelarna med att använda [ExpressRoute] [ ExpressRoute] är:

1. Ökad tillförlitlighet
2. Snabbare nätverk
3. Lägre nätverksfördröjning
4. högre nätverkssäkerhet

[ExpressRoute] [ ExpressRoute] är bra för ett antal scenarier, inte bara migreringen.

Är du intresserad? Mer information och prisinformation finns på [dokumentation om ExpressRoute][ExpressRoute documentation].

### <a name="azure-import-and-export-service"></a>Azure Import och Export-tjänsten
Azure Import och Export-tjänsten är en process för överföring av data som avsett för stora (GB ++) för stora (TB ++) överföring av data till Azure. Det innebär att skriva data till diskar och leverera dem till ett Azure-datacenter. Diskinnehållet läses sedan in i Azure-Lagringsblobar för din räkning.

En övergripande bild av export importen är följande:

1. Konfigurera en Azure Blob Storage-behållare för att ta emot data
2. Exportera data till lokal lagring
3. Kopiera data till 3,5-tums SATA II/III hårddiskar med [Azure Import/Export-verktyget]
4. Skapa ett importjobb med hjälp av Azure Import och Export-tjänst som tillhandahåller journalfiler som produceras av [Azure Import/Export-verktyget]
5. Skicka tillbaka diskarna utsedda Azure Datacenter
6. Dina data överförs till Azure Blob Storage-behållare
7. Läsa in data i SQLDW med PolyBase

### <a name="azcopyazcopy-utility"></a>[AZCopy][AZCopy] verktyget
Den [AZCopy][AZCopy] verktyget är ett bra verktyg för att få dina data som överförts till Azure Storage-Blobbar. Den är utformad för små (MB ++) för mycket stora dataöverföringar för (GB ++). [AZCopy] har också utformats för att ge bra elastiska dataflöde när data överförs till Azure och det är ett bra alternativ för steget för överföring av data. En gång överförda kan du läsa in data med PolyBase i SQL Data Warehouse. Du kan också införliva AZCopy i dina SSIS-paket med hjälp av ett ”kör processen”-uppgiften.

Om du vill använda AZCopy måste du först hämta och installera den. Det finns en [produktionsversion] [ production version] och en [förhandsversionen] [ preview version] tillgängliga.

Om du vill överföra en fil från filsystemet behöver du ett kommando som i exemplet nedan:

```
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:abc.txt
```

En sammanfattning av generella kan vara följande:

1. Konfigurera en Azure storage blob-behållare för att ta emot data
2. Exportera data till lokal lagring
3. AZCopy dina data i Azure Blob Storage-behållare
4. Läsa in data till SQL Data Warehouse med PolyBase

Fullständig dokumentation som är tillgängliga: [AZCopy][AZCopy].

## <a name="optimizing-data-export"></a>Optimera export av data
Förutom att säkerställa att exporten uppfyller kraven som anges av PolyBase kan du också försöka optimera export av data att förbättra den ytterligare.



### <a name="data-compression"></a>Datakomprimering
PolyBase kan läsa gzip-komprimerade data. Om det går att komprimera data till gzip-filer ska du minimera mängden data som skickas över nätverket.

### <a name="multiple-files"></a>Flera filer
Dela upp stora tabeller i flera filer hjälper inte bara att exportera snabbare, gör det också lättare med överföring re-startability och övergripande hantering av data en gång i Azure blob storage. En av många användbara funktioner för PolyBase är att den ska läsa alla filer i en mapp och behandlar det som en tabell. Det är därför en bra idé att isolera filer för varje tabell till en egen mapp.

PolyBase stöder också en funktion som kallas ”rekursiva mappen edge traversal”. Du kan använda den här funktionen för att förbättra hur din exporterade data att förbättra din datahantering.

Läs mer om att läsa in data med PolyBase i [använda PolyBase för att läsa in data i SQL Data Warehouse][Use PolyBase to load data into SQL Data Warehouse].

## <a name="next-steps"></a>Nästa steg
Mer information om migrering finns i [migrera lösningen till SQL Data Warehouse][Migrate your solution to SQL Data Warehouse].
Fler utvecklingstips, se [utvecklingsöversikt][development overview].

<!--Image references-->

<!--Article references-->
[AZCopy]: ../storage/common/storage-use-azcopy.md
[ADF Copy]: ../data-factory/load-azure-sql-data-warehouse.md 
[ADF Copy examples]: ../data-factory/quickstart-create-data-factory-dot-net.md
[development overview]: sql-data-warehouse-overview-develop.md
[Migrera ditt schema]: sql-data-warehouse-migrate-schema.md
[Migrate your solution to SQL Data Warehouse]: sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse development overview]: sql-data-warehouse-overview-develop.md
[Use bcp to load data into SQL Data Warehouse]: /sql/tools/bcp-utility
[Use PolyBase to load data into SQL Data Warehouse]: load-data-wideworldimportersdw.md


<!--MSDN references-->

<!--Other Web references-->
[Azure Data Factory]: https://azure.microsoft.com/services/data-factory/
[ExpressRoute]: https://azure.microsoft.com/services/expressroute/
[ExpressRoute documentation]: https://azure.microsoft.com/documentation/services/expressroute/

[production version]: https://aka.ms/downloadazcopy/
[preview version]: https://aka.ms/downloadazcopypr/
[ADO.NET destination adapter]: https://msdn.microsoft.com/library/bb934041.aspx
[SSIS documentation]: https://msdn.microsoft.com/library/ms141026.aspx
