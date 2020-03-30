---
title: Metodtips för användning av Azure Data Lake Storage Gen2 | Microsoft-dokument
description: Lär dig de bästa metoderna för datainmatning, datumsäkerhet och prestanda relaterade till användning av Azure Data Lake Storage Gen2 (tidigare kallat Azure Data Lake Store)
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.reviewer: sachins
ms.openlocfilehash: ac4e126c7ecbd1fc781db74e5b19635b273bbb34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72299670"
---
# <a name="best-practices-for-using-azure-data-lake-storage-gen2"></a>Metodtips för användning av Azure Data Lake Storage Gen2

I den här artikeln får du lära dig mer om metodtips och överväganden för att arbeta med Azure Data Lake Storage Gen2. Den här artikeln innehåller information om säkerhet, prestanda, återhämtning och övervakning för Data Lake Storage Gen2. Innan Data Lake Storage Gen2 var det komplext att arbeta med verkligt stordata i tjänster som Azure HDInsight. Du var tvungen att fragmentera data över flera Blob-lagringskonton så att petabyte-lagring och optimal prestanda i den skalan kunde uppnås. Data Lake Storage Gen2 stöder enskilda filstorlekar så höga som 5 TB och de flesta hårda gränser för prestanda har tagits bort. Det finns dock fortfarande vissa överväganden som den här artikeln täcker så att du kan få bästa prestanda med Data Lake Storage Gen2.

## <a name="security-considerations"></a>Säkerhetsöverväganden

Azure Data Lake Storage Gen2 erbjuder POSIX-åtkomstkontroller för Azure Active Directory (Azure AD) användare, grupper och tjänsthuvudnamn. Dessa åtkomstkontroller kan ställas in på befintliga filer och kataloger. Åtkomstkontrollerna kan också användas för att skapa standardbehörigheter som kan tillämpas automatiskt på nya filer eller kataloger. Mer information om Data Lake Storage Gen2-ACL:er finns på [Åtkomstkontroll i Azure Data Lake Storage Gen2](storage-data-lake-storage-access-control.md).

### <a name="use-security-groups-versus-individual-users"></a>Använda säkerhetsgrupper jämfört med enskilda användare

När du arbetar med stordata i Data Lake Storage Gen2 är det troligt att ett huvudnamn för tjänsten används för att tillåta tjänster som Azure HDInsight att arbeta med data. Det kan dock finnas fall där enskilda användare behöver tillgång till data också. I alla fall bör du överväga att använda Azure Active [Directory-säkerhetsgrupper](../common/storage-auth-aad.md) i stället för att tilldela enskilda användare till kataloger och filer.

När en säkerhetsgrupp har tilldelats behörigheter kräver det inga uppdateringar av Data Lake Storage Gen2 om du lägger till eller tar bort användare från gruppen. Detta säkerställer också att du inte överskrider det maximala antalet åtkomstkontrollposter per åtkomstkontrollista (ACL). För närvarande är det numret 32 (inklusive de fyra POSIX-stil ACL:er som alltid är associerade med varje fil och katalog): den ägande användaren, den ägande gruppen, masken och andra. Varje katalog kan ha två typer av åtkomstkontrollista, åtkomstförteckningen och standard-åtkomstkontrollposterna, för totalt 64 åtkomstkontrollposter. Mer information om dessa ACL:er finns [i Åtkomstkontroll i Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="security-for-groups"></a>Säkerhet för grupper

När du eller dina användare behöver åtkomst till data i ett lagringskonto med hierarkiskt namnområde aktiverat är det bäst att använda Azure Active Directory-säkerhetsgrupper. Vissa rekommenderade grupper som ska börja med kan vara **ReadOnlyUsers,** **WriteAccessUsers**och **FullAccessUsers** för behållarens rot och till och med separata för viktiga underkataloger. Om det finns andra förväntade grupper av användare som kan läggas till senare, men som inte har identifierats ännu, kan du överväga att skapa dummy säkerhetsgrupper som har åtkomst till vissa mappar. Med hjälp av säkerhetsgruppen säkerställer du att du kan undvika lång bearbetningstid när du tilldelar nya behörigheter till tusentals filer.

### <a name="security-for-service-principals"></a>Säkerhet för tjänstens huvudmän

Azure Active Directory-tjänsthuvudnamn används vanligtvis av tjänster som Azure Databricks för att komma åt data i Data Lake Storage Gen2. För många kunder kan ett enda Azure Active Directory-tjänsthuvudnamn vara tillräckligt och det kan ha fullständig behörighet i roten av Data Lake Storage Gen2-behållaren. Andra kunder kan kräva flera kluster med olika tjänsthuvudnamn där ett kluster har fullständig åtkomst till data och ett annat kluster med endast läsbehörighet. 

### <a name="enable-the-data-lake-storage-gen2-firewall-with-azure-service-access"></a>Aktivera datasjölagringsgend2-brandväggen med Azure-tjänståtkomst

Data Lake Storage Gen2 stöder möjligheten att aktivera en brandvägg och begränsa åtkomsten endast till Azure-tjänster, vilket rekommenderas för att begränsa vektorn för externa attacker. Brandvägg kan aktiveras på ett lagringskonto i Azure-portalen via > **brandväggsaktiveringsbrandväggen (ON)** > **Tillåt åtkomst till Azure-tjänster.** **Firewall**

Om du vill komma åt ditt lagringskonto från Azure Databricks distribuerar du Azure Databricks till ditt virtuella nätverk och lägger sedan till det virtuella nätverket i brandväggen. Se [Konfigurera Azure Storage-brandväggar och virtuella nätverk](https://docs.microsoft.com/azure/storage/common/storage-network-security).

## <a name="resiliency-considerations"></a>Att tänka på om återhämtning

När du skapar ett system med Data Lake Storage Gen2 eller någon molntjänst måste du ta hänsyn till dina tillgänglighetskrav och hur du ska svara på potentiella avbrott i tjänsten. Ett problem kan vara lokaliserat till den specifika instansen eller till och med regionomfattande, så det är viktigt att ha en plan för båda. Beroende på återställningstidens mål och målsassa för återställningspunkt för din arbetsbelastning kan du välja en mer eller mindre aggressiv strategi för hög tillgänglighet och haveriberedskap.

### <a name="high-availability-and-disaster-recovery"></a>Hög tillgänglighet och haveriberedskap

Hög tillgänglighet (HA) och katastrofåterställning (DR) kan ibland kombineras tillsammans, även om var och en har en något annorlunda strategi, särskilt när det gäller data. Data Lake Storage Gen2 hanterar redan 3x replikering under huven för att skydda mot lokaliserade maskinvarufel. Dessutom förbättrar andra replikeringsalternativ, till exempel ZRS eller GZRS (förhandsversion), HA, medan GRS & RA-GRS förbättrar DR. När du skapar en plan för HA, i händelse av ett avbrott i tjänsten arbetsbelastningen behöver tillgång till de senaste uppgifterna så snabbt som möjligt genom att växla över till en separat replikerad instans lokalt eller i en ny region.

I en DR-strategi, för att förbereda sig för den osannolika händelsen av ett katastrofalt fel i en region, är det också viktigt att ha data replikerade till en annan region med grs- eller RA-GRS-replikering. Du måste också överväga dina krav för kantfall, till exempel dataskador där du kanske vill skapa periodiska ögonblicksbilder att falla tillbaka till. Beroende på datas betydelse och storlek bör du överväga rullande deltabilder av 1-, 6- och 24-timmarsperioder, enligt risktoleranser.

För dataåterfliklighet med Data Lake Storage Gen2 rekommenderas att geo-replikera dina data via GRS eller RA-GRS som uppfyller dina HA/DR-krav. Dessutom bör du överväga olika sätt för programmet att använda Data Lake Storage Gen2 att automatiskt växla över till den sekundära regionen genom att övervaka utlösare eller längden på misslyckade försök, eller åtminstone skicka ett meddelande till administratörer för manuella åtgärder. Tänk på att det finns avvägning av att misslyckas över kontra väntar på en tjänst att komma tillbaka online.

### <a name="use-distcp-for-data-movement-between-two-locations"></a>Använd Distcp för dataflyttning mellan två platser

DistCp är ett Linux-kommandoradsverktyg som levereras med Hadoop och tillhandahåller distribuerad dataförflyttning mellan två platser. De två platserna kan vara Data Lake Storage Gen2, HDFS eller S3. Det här verktyget använder MapReduce-jobb i ett Hadoop-kluster (till exempel HDInsight) för att skala ut på alla noder. Distcp anses vara det snabbaste sättet att flytta stordata utan speciella nätverkskompressionsapparater. Distcp ger också ett alternativ för att bara uppdatera deltan mellan två platser, hanterar automatiska återförsök samt dynamisk skalning av beräkning. Detta tillvägagångssätt är otroligt effektivt när det gäller att replikera saker som Hive / Spark tabeller som kan ha många stora filer i en enda katalog och du bara vill kopiera över de ändrade data. Av dessa skäl är Distcp det mest rekommenderade verktyget för att kopiera data mellan stordatalager.

Kopieringsjobb kan utlösas av Apache Oozie-arbetsflöden med frekvens- eller datautlösare samt Linux cron-jobb. För intensiva replikeringsjobb rekommenderas att snurra upp ett separat HDInsight Hadoop-kluster som kan justeras och skalas specifikt för kopieringsjobben. Detta säkerställer att kopieringsjobb inte stör kritiska jobb. Om du kör replikering på en tillräckligt bred frekvens kan klustret till och med tas ned mellan varje jobb. Om det inte går över till sekundär region, se till att ett annat kluster också snurras upp i den sekundära regionen för att replikera nya data tillbaka till det primära Data Lake Storage Gen2-kontot när det kommer tillbaka upp. Exempel på hur du använder Distcp finns i [Använda Distcp för att kopiera data mellan Azure Storage Blobbar och Data Lake Storage Gen2](../blobs/data-lake-storage-use-distcp.md).

### <a name="use-azure-data-factory-to-schedule-copy-jobs"></a>Använda Azure Data Factory för att schemalägga kopieringsjobb

Azure Data Factory kan också användas för att schemalägga kopieringsjobb med hjälp av en kopieringsaktivitet och kan även ställas in på en frekvens via kopieringsguiden. Tänk på att Azure Data Factory har en gräns för molndataflyttningar (DMUs) och så småningom begränsar dataflödet/beräkningen för stora dataarbetsbelastningar. Dessutom erbjuder Azure Data Factory för närvarande inte deltauppdateringar mellan Data Lake Storage Gen2-konton, så kataloger som Hive-tabeller kräver en fullständig kopia för att replikera. Mer information om hur du kopierar med Data Factory finns i [artikeln datafabrik.](../../data-factory/load-azure-data-lake-storage-gen2.md)

## <a name="monitoring-considerations"></a>Övervakning överväganden

Data Lake Storage Gen2 innehåller mått i Azure-portalen under Data Lake Storage Gen2-kontot och i Azure Monitor. Tillgängligheten för Data Lake Storage Gen2 visas i Azure-portalen. Om du vill få den senaste tillgängligheten för ett Data Lake Storage Gen2-konto måste du köra dina egna syntetiska tester för att validera tillgängligheten. Andra mått som total lagringsanvändning, läs-/skrivbegäranden och ingående/utgående är tillgängliga för att utnyttjas genom att övervaka program och kan även utlösa aviseringar när tröskelvärden (till exempel Genomsnittlig svarstid eller # av fel per minut) överskrids.

## <a name="directory-layout-considerations"></a>Hänsyn till kataloglayout

När du landar data i en datasjö är det viktigt att förplanera datastrukturen så att säkerhet, partitionering och bearbetning kan användas effektivt. Många av följande rekommendationer gäller för alla arbetsbelastningar med stordata. Varje arbetsbelastning har olika krav på hur data förbrukas, men nedan finns några vanliga layouter att tänka på när du arbetar med IoT- och batchscenarier.

### <a name="iot-structure"></a>IoT-struktur

I IoT-arbetsbelastningar kan det finnas en hel del data som landas i datalagret som sträcker sig över många produkter, enheter, organisationer och kunder. Det är viktigt att förplanera kataloglayouten för organisation, säkerhet och effektiv bearbetning av data för nedströmskonsumenter. En allmän mall att tänka på kan vara följande layout:

    {Region}/{SubjectMatter(s)}/{yyyy}/{mm}/{dd}/{hh}/

Till exempel kan landningstelemetri för en flygplansmotor inom Storbritannien se ut så här:

    UK/Planes/BA1293/Engine1/2017/08/11/12/

Det finns en viktig anledning att sätta datumet i slutet av katalogstrukturen. Om du vill låsa vissa regioner eller ämnesfrågor för användare/grupper kan du enkelt göra det med POSIX-behörigheterna. Annars, om det fanns ett behov av att begränsa en viss säkerhetsgrupp till att visa bara brittiska data eller vissa plan, med datumstrukturen framför ett separat tillstånd skulle krävas för många kataloger under varje timme katalog. Dessutom, med datum struktur framför skulle exponentiellt öka antalet kataloger med tiden.

### <a name="batch-jobs-structure"></a>Batch jobb struktur

Från en hög nivå är en vanlig metod vid batchbearbetning att landa data i en "in"-katalog. Sedan, när data bearbetas, placera nya data i en "ut" katalog för nedströms processer att konsumera. Den här katalogstrukturen ses ibland för jobb som kräver bearbetning på enskilda filer och kanske inte kräver massiv parallell bearbetning över stora datauppsättningar. Liksom IoT-strukturen som rekommenderas ovan har en bra katalogstruktur katalogkatalogerna för saker som region och ämnesfrågor (till exempel organisation, produkt/producent). Den här strukturen hjälper till att skydda data i hela organisationen och bättre hantering av data i dina arbetsbelastningar. Överväg dessutom datum och tid i strukturen för att möjliggöra bättre organisation, filtrerade sökningar, säkerhet och automatisering i bearbetningen. Detaljnivån för datumstrukturen bestäms av det intervall då data överförs eller bearbetas, till exempel timme, dag eller till och med varje månad.

Ibland misslyckas filbearbetningen på grund av skadade data eller oväntade format. I sådana fall kan katalogstrukturen dra nytta av en **/bad-mapp** för att flytta filerna till för ytterligare inspektion. Batch-jobbet kan också hantera rapportering eller meddelande om dessa *felaktiga* filer för manuella åtgärder. Tänk på följande mallstruktur:

    {Region}/{SubjectMatter(s)}/In/{yyyy}/{mm}/{dd}/{hh}/
    {Region}/{SubjectMatter(s)}/Out/{yyyy}/{mm}/{dd}/{hh}/
    {Region}/{SubjectMatter(s)}/Bad/{yyyy}/{mm}/{dd}/{hh}/

Till exempel får ett marknadsföringsföretag dagliga dataexektioner av kunduppdateringar från sina kunder i Nordamerika. Det kan se ut som följande utdrag före och efter att ha bearbetats:

    NA/Extracts/ACMEPaperCo/In/2017/08/14/updates_08142017.csv
    NA/Extracts/ACMEPaperCo/Out/2017/08/14/processed_updates_08142017.csv

I det vanliga fallet med batchdata som bearbetas direkt till databaser som Hive eller traditionella SQL-databaser, finns det inte något behov av en **/in** eller **/out-mapp** eftersom utdata redan går in i en separat mapp för Hive-tabellen eller extern databas. Dagliga utdrag från kunder skulle till exempel landa i sina respektive mappar, och orkestrering av något som Azure Data Factory, Apache Oozie eller Apache Airflow skulle utlösa ett dagligt Hive- eller Spark-jobb för att bearbeta och skriva data i en Hive-tabell.
