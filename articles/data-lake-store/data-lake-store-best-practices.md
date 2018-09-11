---
title: Bästa praxis för att använda Azure Data Lake Storage Gen1 | Microsoft Docs
description: Läs metodtipsen om datainmatning, datum säkerhet och prestanda för med hjälp av Azure Data Lake Storage Gen1 (kallades tidigare Azure Data Lake Store)
services: data-lake-store
documentationcenter: ''
author: sachinsbigdata
manager: jhubbard
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: sachins
ms.openlocfilehash: 37af9007a1572bf2f297909fe1351d7f122405e3
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2018
ms.locfileid: "44297030"
---
# <a name="best-practices-for-using-azure-data-lake-storage-gen1"></a>Metodtips för Azure Data Lake Storage Gen1

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

I den här artikeln får du lära dig om bästa praxis och överväganden för att arbeta med Azure Data Lake Store. Den här artikeln innehåller information om säkerhet, prestanda, flexibilitet och övervakning för Data Lake Store. Arbeta med verkligen big data i tjänster som Azure HDInsight var krånglig innan Data Lake Store. Var du tvungen att fragmentera data över flera Blob storage-konton så att petabyte lagringsutrymme och den optimala prestanda kan uppnås. De flesta av gränserna för storlek och prestanda med Data Lake Store, tas bort. Men finns det fortfarande ett par saker som den här artikeln beskriver så att du kan få bästa prestanda med Data Lake Store. 

## <a name="security-considerations"></a>Säkerhetsöverväganden

Azure Data Lake Store erbjuder POSIX åtkomst styr och detaljerad granskning för Azure Active Directory (Azure AD)-användare, grupper och tjänstens huvudnamn. Dessa åtkomstkontroller kan ställas in på befintliga filer och mappar. Åtkomstkontroller kan också användas för att skapa standardvärden som kan tillämpas på nya filer eller mappar. När behörigheter ställs in befintliga mappar och underordnade objekt, behöver behörigheterna spridda rekursivt på varje objekt. Om antalet filer som sprider behörigheter kan det ta lång tid. Den tid det tar kan variera mellan 30 – 50 objekt som bearbetas per sekund. Planera därför mappen struktur och användargrupper på lämpligt sätt. Annars kan orsaka det oväntade fördröjningar och problem när du arbetar med dina data. 

Anta att du har en mapp med 100 000 underordnade objekt. Om du skapar den undre gränsen för 30 objekt som bearbetas per sekund kan för att uppdatera behörigheten för hela mappen ta en timme. Mer information om Data Lake Store-ACL: er finns på [åtkomstkontroll i Azure Data Lake Store](data-lake-store-access-control.md). Du kan använda Azure Data Lake-kommandoradsverktyget för bättre prestanda om hur du tilldelar ACL: er rekursivt. Verktyget skapar flera trådar och rekursiv navigering logik för att snabbt tillämpa ACL: er till miljoner filer. Verktyget är tillgängligt för Linux och Windows, och [dokumentation](https://github.com/Azure/data-lake-adlstool) och [hämtar](http://aka.ms/adlstool-download) för det här verktyget finns på GitHub. Dessa samma prestandaförbättringar kan aktiveras med dina egna verktyg som skrivits med Data Lake Store [.NET](data-lake-store-data-operations-net-sdk.md) och [Java](data-lake-store-get-started-java-sdk.md) SDK: er.

### <a name="use-security-groups-versus-individual-users"></a>Använda säkerhetsgrupper jämfört med enskilda användare 

När du arbetar med stordata i Data Lake Store, är förmodligen används ett huvudnamn för tjänsten för att ge tjänster, till exempel Azure HDInsight för att arbeta med data. Det kan dock finnas fall där enskilda användare behöver åtkomst till data samt. I sådana fall måste du använda Azure Active Directory [säkerhetsgrupper](data-lake-store-secure-data.md#create-security-groups-in-azure-active-directory) i stället för att tilldela enskilda användare till mappar och filer. 

När en säkerhetsgrupp tilldelas behörigheter, kräver inte att lägga till eller ta bort användare i gruppen Alla uppdateringar av Data Lake Store. Detta kan också se till att du inte överskrider gränsen på [32 åtkomst- och standard-ACL](../azure-subscription-service-limits.md#data-lake-store-limits) (Detta omfattar fyra POSIX-typ ACL: er som alltid är associerade med varje fil och mapp: [ägande användare](data-lake-store-access-control.md#the-owning-user), [den ägande gruppen](data-lake-store-access-control.md#the-owning-group), [masken](data-lake-store-access-control.md#the-mask), och andra).

### <a name="security-for-groups"></a>Säkerhet för grupper 

Enligt beskrivningen, när användare behöver åtkomst till Data Lake Store, är det bäst att använda Azure Active Directory-säkerhetsgrupper. Några av de rekommenderade grupper att börja med kan vara **ReadOnlyUsers**, **WriteAccessUsers**, och **FullAccessUsers** för roten i kontot, och även avgränsa sådana för nyckeln undermappar. Om det inte finns någon annan kan förväntade grupper av användare som kan läggas till senare, men inte har identifierats men eventuellt skapa dummy-säkerhetsgrupper som har åtkomst till vissa mappar. Användning av säkerhetsgrupp garanterar att senare inte behöver lång tid för att tilldela nya behörigheter till filer. 

### <a name="security-for-service-principals"></a>Säkerhet för tjänstens huvudnamn 

Azure Active Directory-tjänstobjekt som vanligtvis används av tjänster som Azure HDInsight för att komma åt data i Data Lake Store. Beroende på kraven för åtkomst mellan flera arbetsbelastningar, kan det finnas några saker att garantera säkerheten i och utanför organisationen. En enda Azure Active Directory-tjänstens huvudnamn kan vara lämplig för många kunder och den kan ha fullständig behörighet i roten för Data Lake Store. Andra kunder kan kräva flera kluster med olika tjänsthuvudnamn där ett kluster har fullständig åtkomst till data och ett annat kluster med bara läsbehörighet. Precis som med säkerhetsgrupper, kan du göra ett huvudnamn för tjänsten för var och en förväntat scenario (läsa, skriva, fullständig) när ett Data Lake Store-konto har skapats. 

### <a name="enable-the-data-lake-store-firewall-with-azure-service-access"></a>Aktivera brandväggen för Data Lake Store med Azure-tjänståtkomst 

Data Lake Store stöder aktivering av en brandvägg och begränsa åtkomst till Azure-tjänster, vilket rekommenderas för en mindre angreppsvinkel från utanför intrång. Brandväggen kan vara aktiverad på Data Lake Store-konto i Azure-portalen via den **brandväggen** > **aktivera brandvägg (på)** > **Tillåt åtkomst till Azure-tjänster**  alternativ.  

![Brandväggsinställningar i Data Lake Store](./media/data-lake-store-best-practices/data-lake-store-firewall-setting.png "brandväggsinställningar i Data Lake Store")

När brandväggen är aktiverad, endast Azure-tjänster, till exempel HDInsight, har Data Factory, SQL Data Warehouse osv åtkomst till Data Lake Store. På grund av den interna nätverksadresser används av Azure, Data Lake Store-brandväggen har stöd inte för att begränsa specifika tjänster efter IP och är endast avsedd för begränsningar för slutpunkter utanför Azure, till exempel lokalt. 

## <a name="performance-and-scale-considerations"></a>Överväganden för prestanda och skalning

En av de mest kraftfulla funktionerna i Data Lake Store är att det tar bort hårda gränser för dataflödet. Ta bort gränserna ger kunder möjlighet att utöka sina datastorlek och medföljer prestandakrav utan att behöva Fragmentera data. En av rum för att optimera prestanda för Data Lake Store är att det fungerar bäst när angivna parallellitet.

### <a name="improve-throughput-with-parallelism"></a>Förbättra dataflödet med parallellitet 

Överväg att ge 8 – 12 trådar per kärna för den mest optimala Läs/Skriv-dataflöden. Detta beror på blockerande läsningar och skrivningar på en enda tråd och flera trådar kan tillåta högre samtidighet på den virtuella datorn. Om du vill se till att nivåer är felfria och parallellitet kan utökas, bör du övervaka den Virtuella datorns CPU-användning.   

### <a name="avoid-small-file-sizes"></a>Undvik att små filstorlekar

POSIX-behörigheter och granskning i Data Lake Store levereras med en belastning som blir tydligt när du arbetar med ett stort antal små filer. Som bästa praxis, måste du batch dina data i större filer jämfört med skrivning tusentals eller miljontals små filer till Data Lake Store. Undvika små filstorlekar får flera fördelar, till exempel:

* Sänka kontrollerna för autentisering över flera filer
* Minskad öppna anslutningar
* Snabbare kopierar/replikering
* Färre filer som ska bearbetas när du uppdaterar Data Lake Store POSIX-behörigheter 

Beroende på vilka tjänster och arbetsbelastningar med hjälp av data, en bra att tänka på för filer är 256 MB eller större. Om inte kan batchhanteras filstorleken när hamnar i Data Lake Store, kan du ha ett separat komprimering jobb som kombinerar dessa filer till större. Mer information och rekommendationer om filstorlekar och ordna data i Data Lake Store finns i [strukturera datauppsättningen](data-lake-store-performance-tuning-guidance.md#structure-your-data-set).

### <a name="large-file-sizes-and-potential-performance-impact"></a>Stora filstorlekar och eventuell prestandapåverkan

Data Lake Store har stöd för stora filer upp till petabyte i storlek för optimala prestanda och beroende på vilken process som läser data, kanske den inte är perfekt för att gå över 2 GB i genomsnitt. Till exempel när du använder **Distcp** för att kopiera data mellan platser eller olika lagringskonton filer är finest granularitetsnivån används för att fastställa kartan uppgifter. Om du kopierar 10 filer som är 1 TB allokeras så högst 10 Mappningskomponenter. Även om du har massor av filer med Mappningskomponenter tilldelas arbeta inledningsvis Mappningskomponenter parallellt att flytta stora filer. När jobbet startar likvidation endast några Mappningskomponenter är fortfarande allokerade och du kan ha fastnat med en enda mapper som tilldelats en stor fil. Microsoft har skickat förbättringar av Distcp för att lösa detta problem i framtida versioner av Hadoop.  

Ett annat exempel att tänka på är när du använder Azure Data Lake Analytics med Data Lake Store. Beroende på det bearbetas av extraktor vissa filer inte kan delas (till exempel, XML, JSON) kan bli sämre i prestanda när den är större än 2 GB. I fall där filer kan delas av en extraktor (till exempel CSV), stora filer som är standard.

### <a name="capacity-plan-for-your-workload"></a>Kapacitet planera för din arbetsbelastning 

Azure Data Lake Store tar bort hårda I/O nätverksbegränsningar som är placerade på Blob storage-konton. Det finns dock fortfarande mjuka gränser som ska övervägas. Standardgränser för ingående/utgående trafik begränsning uppfyller behoven hos de flesta scenarier. Om din arbetsbelastning behöver gränserna ökar kan fungera med Microsoft-supporten. Dessutom titta på gränserna under valet proof of concept så att i/o-begränsningar gränser inte uppnås under produktion. Om detta händer, kräva väntetiden för en manuell ökning från Microsofts tekniker. Om i/o-begränsning inträffar, Azure Data Lake Store returnerar felkod 429 och helst ska göras med en lämplig exponentiell backoff-princip. 

### <a name="optimize-writes-with-the-data-lake-store-driver-buffer"></a>Optimera ”skriver” med Data Lake Store-drivrutinen bufferten 

För att optimera prestanda och minska IOPS vid skrivning till Data Lake Store från Hadoop, utföra skrivåtgärder så nära buffertstorleken för Data Lake Store-drivrutin som möjligt. Försök inte att överskrida buffertstorleken innan, till exempel när strömning med Apache Storm eller Spark-strömning arbetsbelastningar. Vid skrivning till Data Lake Store från HDInsight/Hadoop, är det viktigt att veta att Data Lake Store har en drivrutin med en buffert 4 MB. Som drivrutiner för filsystemet många, kan bufferten manuellt tömmas innan storleken 4 MB. Om inte, den skickas direkt till storage om nästa skrivåtgärder överskrider den maximala buffertstorleken. Om möjligt, måste du undvika ett överskridande eller en betydande underskridning buffertens synkronisering/bokför princip per antal eller tid.

## <a name="resiliency-considerations"></a>Att tänka på om återhämtning 

När utforma ett system med Data Lake Store eller valfri molntjänst, måste du överväga att kraven på tillgänglighet och hur du svarar på eventuella avbrott i tjänsten. Ett problem kan lokaliseras till den specifika instansen eller även regionomfattande, så att ha en plan för både är viktigt. Beroende på den **återställningstid** och **mål för återställningspunkt** serviceavtal för din arbetsbelastning kan du välja en mer eller mindre aggressiv strategi för hög tillgänglighet och haveriberedskap.

### <a name="high-availability-and-disaster-recovery"></a>Hög tillgänglighet och haveriberedskap 

Hög tillgänglighet och katastrofåterställning (DR) kan ibland kombineras tillsammans, även om var och en har en något annorlunda strategi, särskilt när det gäller data. Data Lake Store hanterar redan 3 x-replikering för att skydda mot lokaliserade maskinvarufel. Eftersom replikering över regioner inte är inbyggd måste du dock hantera den själv. När du skapar en plan för hög tillgänglighet i händelse av ett tjänstavbrott behöver arbetsbelastningen åtkomst till den senaste informationen så snabbt som möjligt genom att växla till en separat replikerade instansen lokalt eller i en ny region.  

I en DR-strategi för att förbereda för osannolika ett oåterkalleligt fel på en region, är det också viktigt att du har data som replikeras till en annan region. Dessa data kan ursprungligen vara desamma som de replikerade data för hög tillgänglighet. Du måste också beakta dina krav på gränsfall, till exempel skadade data där du kanske vill skapa regelbundna ögonblicksbilder till. Beroende på prioritet och storleken på data kan du överväga att löpande delta ögonblicksbilder av 1 – 6- och 24 timmar punkter i lokala och/eller sekundära store, enligt risk toleranser. 

För dataåterhämtning med Data Lake Store rekommenderar vi att du geo-replikera dina data till en separat region med en frekvens som uppfyller dina hr/DR-krav, helst varje timme. Den här replikeringsfrekvensen minimerar enorma dataflyttningar som kan ha konkurrerande dataflöde behöver med huvudsakliga systemet och ett bättre återställningspunktmål (RPO). Dessutom bör du överväga att sätt för programmet med Data Lake Store att automatiskt växla över till det sekundära kontot fram till övervakning utlösare eller längden på misslyckade försök eller minst skicka ett meddelande till administratörer för manuella åtgärder. Tänk på att det finns en kompromiss vid fel och väntar på att en tjänst ska komma online igen. Om data inte har slutförts replikering, kan redundans orsaka förlust av data, inkonsekvens eller komplexa sammanslagning av data. 

Nedan visas de översta tre rekommendera alternativ för att dirigera replikeringen mellan Data Lake Store-konton och viktiga skillnader mellan dem.


|  |Distcp  |Azure Data Factory  |AdlCopy  |
|---------|---------|---------|---------|
|**Gränser för skalning**     | Begränsas av arbetsnoder        | Begränsas av Molndataflytt för maximalt antal enheter        | Bunden av analysenheter        |
|**Har stöd för kopiering av deltan**     |   Ja      | Nej         | Nej         |
|**Inbyggda orchestration**     |  Nej (Använd Oozie luftflödet eller ett cron-jobb)       | Ja        | Nej (använda Azure Automation eller Schemaläggaren i Windows)         |
|**Filsystem som stöds**     | ADL, HDFS, WASB, S3, GS, CFS        |Ett stort antal, se [Anslutningsappar](../data-factory/connector-azure-blob-storage.md).         | ADL till ADL/WASB till ADL (endast samma region)        |
|**OS-support**     |Alla operativsystem som körs Hadoop         | Gäller inte          | Windows 10         |
   

### <a name="use-distcp-for-data-movement-between-two-locations"></a>Använd Distcp för dataförflyttning mellan två platser 

Kort för distribuerad kopia Distcp är ett kommandoradsverktyg för Linux med Hadoop och ger distribuerade dataförflyttning mellan två platser. De två platserna kan vara Data Lake Store, HDFS, WASB eller S3. Det här verktyget använder MapReduce-jobb på ett Hadoop-kluster (till exempel HDInsight) för att skala ut på alla noder. Distcp anses vara det snabbaste sättet att flytta big data utan komprimering för särskilda nätverksenheter. Distcp innehåller också ett alternativ för att endast uppdatera deltan mellan två platser, hanterar automatiska omförsök, samt dynamisk skalning av beräkning. Den här metoden är mycket effektiva när det gäller att replikera saker som Hive/Spark-tabeller som kan ha många stora filer i en enskild katalog och du bara vill kopiera över ändrade data. Därmed behöver är Distcp det mest rekommenderade verktyget för att kopiera data mellan datalager för stordata. 

Kopiera jobb kan utlösas av Apache Oozie-arbetsflöden med frekvensen eller utlösare för data samt Linux cron-jobb. För intensiva replikeringsjobb rekommenderar vi att du skapa ett separat HDInsight Hadoop-kluster som kan justerade och skalas specifikt för jobb som kopia. Detta säkerställer att kopiera jobb inte störa kritiska jobb. Om du kör replikering på en liten frekvens, kan även klustret tas mellan varje jobb. Om redundansväxla till sekundär region, se till att ett annat kluster också startas i den sekundära regionen att replikera nya data tillbaka till det primära Data Lake Store-kontot när den visas. Exempel på hur du använder Distcp finns [Använd Distcp för att kopiera data mellan Azure Storage-Blobbar och Data Lake Store](data-lake-store-copy-data-wasb-distcp.md).

### <a name="use-azure-data-factory-to-schedule-copy-jobs"></a>Använd Azure Data Factory för att schemalägga jobb för kopiering 

Azure Data Factory kan också användas för att schemalägga kopia jobb med hjälp av en **Kopieringsaktiviteten**, och kan även ställas in på en frekvens via den **Kopieringsguiden**. Tänk på att Azure Data Factory har en gräns på enheter för molndataflytt (DMUs) och slutligen caps dataflöde/beräkning för stora arbetsbelastningar. Dessutom erbjuder Azure Data Factory för närvarande inte delta uppdateringar mellan Data Lake Store-konton, så kräver en fullständig kopia att replikera mappar som Hive-tabeller. Referera till den [Justeringsguiden för Kopieringsaktivitet](../data-factory/copy-activity-performance.md) för mer information om hur du kopierar med Data Factory. 

### <a name="adlcopy"></a>AdlCopy

AdlCopy är en Windows-kommandoradsverktyg som gör det möjligt att kopiera data mellan två Data Lake Store-konton endast inom samma region. AdlCopy verktyget ger en fristående alternativet eller alternativet för att köra dina kopieringsjobb med ett Azure Data Lake Analytics-konto. Även om det har ursprungligen skapats för på begäran kopior istället en robust replikering, är ett annat alternativ att göra distribuerade kopiera över Data Lake Store-konton inom samma region. För tillförlitlighet rekommenderar vi för att använda Data Lake Analytics-alternativet premium för produktionsarbetsbelastningar. Den fristående versionen kan returnera upptagen svar och har begränsad skala och övervakning. 

Som Distcp måste AdlCopy vara orkestreras via något som Azure Automation eller Schemaläggaren i Windows. Precis som med Data Factory, AdlCopy stöder inte kopiera uppdaterade filer, men recopies, och skriver över befintliga filer. Mer information och exempel på användning av AdlCopy finns [kopiera data från Azure Storage BLOB till Data Lake Store](data-lake-store-copy-data-azure-storage-blob.md).

## <a name="monitoring-considerations"></a>Överväganden för övervakning 

Data Lake Store ger detaljerad diagnostikloggar och granskning. Data Lake Store innehåller vissa grundläggande i Azure-portalen under Data Lake Store-konto och Azure Monitor. Tillgängligheten för Data Lake Store visas i Azure-portalen. Det här måttet uppdateras var sju minuter och kan inte frågas via ett offentligt exponerade API. För att få den senaste tillgängligheten för ett Data Lake Store-konto, måste du köra dina egna syntetiska tester för att verifiera tillgänglighet. Andra mått som totalt Lagringsutnyttjande, läsningar/skrivningar och ingående/utgående trafik kan ta upp till 24 timmar att uppdatera. Därför måste uppdaterade mått beräknas manuellt via Hadoop-kommandoradsverktyg eller aggregering logginformation. Det snabbaste sättet att få den senaste lagringsanvändningen kör det här HDFS-kommandot från en Hadoop-klusternod (till exempel huvudnod):   

    hdfs dfs -du -s -h adl://<adls_account_name>.azuredatalakestore.net:443/

### <a name="export-data-lake-store-diagnostics"></a>Exportera Data Lake Store-diagnostik 

Ett av de snabbaste sätten att få åtkomst till sökbara loggar från Data Lake Store är att aktivera loggöverföring till **Log Analytics** under den **diagnostik** bladet för Data Lake Store-konto. Det ger omedelbar åtkomst till inkommande loggar tid och innehåll filter, tillsammans med aviseringar alternativ (e-post/webhook) utlöses inom 15 minuters mellanrum. Anvisningar finns i [åtkomst till diagnostikloggar för Azure Data Lake Store](data-lake-store-diagnostic-logs.md). 

Överväg att exportera loggar till Azure EventHub där innehåll kan analyseras individuellt eller via ett tidsfönster för att kunna skicka meddelanden till en kö i realtid för mer avisering i realtid och mer kontroll om var du hamnar loggarna. Ett separat program som en [Logikapp](../connectors/connectors-create-api-azure-event-hubs.md) kan sedan använda och kommunicera aviseringar för rätt kanal, samt skicka mått till övervakningsverktyget som NewRelic, Datadog eller AppDynamics. Om du använder ett verktyg från tredje part, till exempel ElasticSearch kan du också exportera loggarna till Blob Storage och använda den [Azure Logstash plugin-programmet](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob) att använda data i din stack Elasticsearch, Kibana och Logstash (ELK).

### <a name="turn-on-debug-level-logging-in-hdinsight"></a>Aktivera felsökningsnivå loggning i HDInsight 

Om Data Lake Store loggöverföring inte är påslagen, Azure HDInsight innehåller också ett sätt att aktivera [Klientloggning för Data Lake Store](data-lake-store-performance-tuning-mapreduce.md) via log4j. Du måste ange följande egenskap i **Ambari** > **YARN** > **Config** > **avancerade yarn-log4j konfigurationer**: 

    log4j.logger.com.microsoft.azure.datalake.store=DEBUG 

När egenskapen och noderna startas om, Data Lake Store-diagnostik skrivs till YARN-loggarna på noderna (/tmp/<user>/yarn.log), och viktig information som fel eller begränsning (HTTP 429 felkod) kan övervakas. Samma information kan övervakas i Log Analytics eller var loggarna skickas till i den [diagnostik](data-lake-store-diagnostic-logs.md) bladet för Data Lake Store-konto. Vi rekommenderar minst ha klientsidan loggning aktiveras eller använda alternativet med Data Lake Store för ökar du synligheten och enklare felsökning av loggöverföring.

### <a name="run-synthetic-transactions"></a>Kör syntetiska transaktioner 

Tillgänglighet tjänstmåttet för Data Lake Store i Azure-portalen har för närvarande 7 minuters uppdatering fönster. Dessutom kan inte efterfrågas med hjälp av ett offentligt exponerad API. Därför rekommenderar vi att du skapar ett grundläggande program som har syntetiska transaktioner till Data Lake Store som kan tillhandahålla upp till minut tillgängligheten. Ett exempel kan vara att skapa en WebJob, Logikapp eller Azure Function-App för att utföra en läsning, skapa, och uppdatera mot Data Lake Store och skicka resultaten till din lösning för övervakning. Åtgärderna kan görs i en tillfällig mapp och sedan tas bort efter test, som kan köras med 30 – 60 sekunders mellanrum, beroende på kraven.

## <a name="directory-layout-considerations"></a>Överväganden för Directory-layout

När du hanterar data i en datasjö, är det viktigt att planera inför strukturen för data så att säkerhet, partitionering och bearbetning kan vara används effektivt. Många av följande rekommendationer kan användas om det är med Azure Data Lake Store, Blob Storage eller HDFS. Varje arbetsbelastning har olika krav på hur data används, men här följer några vanliga layouter att tänka på när du arbetar med IoT och batch-scenarier.

### <a name="iot-structure"></a>IoT-struktur 

I IoT-arbetsbelastningar, kan det finnas en stor mängd data som nu visas det datalager som sträcker sig över flera produkter, enheter, organisationer och kunder. Det är viktigt i förväg directory layouten för organisationen, säkerhet och effektiv bearbetning av data för ned-ström konsumenter. En allmän mall att tänka på kanske följande layout: 

    {Region}/{SubjectMatter(s)}/{yyyy}/{mm}/{dd}/{hh}/ 

Landing telemetri för en motor med flygplan i Storbritannien kan till exempel se ut som följande struktur: 

    UK/Planes/BA1293/Engine1/2017/08/11/12/ 

Det finns en viktig anledning att placera datum i slutet av mappstrukturen. Om du vill låsa vissa geografiska områden eller ett ämne som är viktigt att användare/grupper kan du göra det enkelt med POSIX-behörigheter. Annars, om det är viktigt att begränsa en viss säkerhetsgrupp till visar bara data Storbritannien eller vissa plan, med datum struktur framför en separat behörighet skulle krävas för ett stort antal mappar under varje timme. Dessutom skulle har datum strukturen framför exponentiellt öka antalet mappar enligt tid som gått.

### <a name="batch-jobs-structure"></a>Struktur för batch-jobb 

Från en hög nivå är en metod som används ofta i batch-bearbetning hamnar data i en ”in”-mapp. Sedan, när data har bearbetats kan placera nya data i en ”ut”-mapp för underordnade processer som ska använda. Den här katalogstrukturen är används ibland för jobb som kräver bearbetning på enskilda filer och kanske inte kräver massiv parallellbearbetning över stora datauppsättningar. T.ex. IoT-struktur som rekommenderas ovan, har en bra katalogstruktur överordnad mappar för sådant som region och ämne frågor (till exempel organisation, produkt/producent). Den här strukturen hjälper med att skydda data i din organisation och bättre hantering av data i dina arbetsbelastningar. Dessutom, beakta datum och tid i strukturen för att ge bättre organisation, filtrerad sökningar, säkerhet och automatisering i bearbetningen. Granularitetsnivån för datum-strukturen bestäms av det tidsintervall som data laddas upp till eller bearbetas, till exempel varje timme, varje dag, men även varje månad. 

Ibland filen bearbetning är misslyckades på grund av skadade data eller oväntat format. I sådana fall kan katalogstruktur kan dra nytta av en **/felaktiga** mapp att flytta filerna till för mer kontroll. Batch-jobbet kan också hantera rapportering eller meddelanden om dessa *felaktig* filer för manuella åtgärder. Överväg följande mallstruktur: 

    {Region}/{SubjectMatter(s)}/In/{yyyy}/{mm}/{dd}/{hh}/ 
    {Region}/{SubjectMatter(s)}/Out/{yyyy}/{mm}/{dd}/{hh}/ 
    {Region}/{SubjectMatter(s)}/Bad/{yyyy}/{mm}/{dd}/{hh}/ 

Till exempel får ett marknadsföring företag dagliga data extrakt av kunden uppdateringar från sina kunder i Nordamerika. Det kan se ut som följande fragment före och efter att ha behandlats: 

    NA/Extracts/ACMEPaperCo/In/2017/08/14/updates_08142017.csv 
    NA/Extracts/ACMEPaperCo/Out/2017/08/14/processed_updates_08142017.csv 
 
I vanliga fall av batchdata som bearbetas direkt till databaser, till exempel Hive eller traditionella SQL-databaser, det inte finns behov av en **/in** eller **/ut** mappen eftersom utdata redan hamnar i en Avgränsa mapp för Hive-tabell eller extern databas. Till exempel dagliga utdrag ur kunder skulle hamna i sina respektive mappar och samordning med något som liknar Azure Data Factory, Apache Oozie eller Apache luftflödet ska utlösa en daglig Hive eller Spark-jobb för att bearbeta och skriva data till en Hive-tabell.

## <a name="next-steps"></a>Nästa steg     

* [Översikt över Azure Data Lake Store](data-lake-store-overview.md) 
* [Åtkomstkontroll i Azure Data Lake Store](data-lake-store-access-control.md) 
* [Säkerhet i Azure Data Lake Store](data-lake-store-security-overview.md)
* [Justering av Azure Data Lake Store för prestanda](data-lake-store-performance-tuning-guidance.md)
* [Prestandajusteringsvägledning för användning av HDInsight Spark med Azure Data Lake Store](data-lake-store-performance-tuning-spark.md)
* [Prestandajusteringsvägledning för att använda Hive med HDInsight med Azure Data Lake Store](data-lake-store-performance-tuning-hive.md)
* [Data Orchestration med hjälp av Azure Data Factory för Azure Data Lake Store](https://mix.office.com/watch/1oa7le7t2u4ka)
* [Skapa HDInsight-kluster med Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md) 
