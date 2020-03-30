---
title: Metodtips för användning av Azure Data Lake Storage Gen1 | Microsoft-dokument
description: Lär dig de bästa metoderna för datainmatning, datumsäkerhet och prestanda relaterade till användning av Azure Data Lake Storage Gen1 (tidigare kallat Azure Data Lake Store)
services: data-lake-store
documentationcenter: ''
author: sachinsbigdata
manager: mtillman
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: sachins
ms.openlocfilehash: a8ca67d1ff3100aee02ed473c9cc2180de3973b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638943"
---
# <a name="best-practices-for-using-azure-data-lake-storage-gen1"></a>Metodtips för användning av Azure Data Lake Storage Gen1

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

I den här artikeln får du lära dig mer om metodtips och överväganden för att arbeta med Azure Data Lake Storage Gen1. Den här artikeln innehåller information om säkerhet, prestanda, återhämtning och övervakning för Data Lake Storage Gen1. Innan Data Lake Storage Gen1 var det komplext att arbeta med verkligt stordata i tjänster som Azure HDInsight. Du var tvungen att fragmentera data över flera Blob-lagringskonton så att petabyte-lagring och optimal prestanda i den skalan kunde uppnås. Med Data Lake Storage Gen1 tas de flesta hårda gränser för storlek och prestanda bort. Det finns dock fortfarande vissa överväganden som den här artikeln täcker så att du kan få bästa prestanda med Data Lake Storage Gen1.

## <a name="security-considerations"></a>Säkerhetsöverväganden

Azure Data Lake Storage Gen1 erbjuder POSIX-åtkomstkontroller och detaljerad granskning för Azure Active Directory -användare, grupper och tjänsthuvudnamn. Dessa åtkomstkontroller kan ställas in på befintliga filer och mappar. Åtkomstkontrollerna kan också användas för att skapa standardvärden som kan tillämpas på nya filer eller mappar. När behörigheter är inställda på befintliga mappar och underordnade objekt måste behörigheterna spridas rekursivt för varje objekt. Om det finns ett stort antal filer kan det ta lång tid att sprida behörigheterna. Den tid det tar kan variera mellan 30-50 objekt som bearbetas per sekund. Planera därför mappstrukturen och användargrupperna på rätt sätt. Annars kan det orsaka oförutsedda förseningar och problem när du arbetar med dina data.

Anta att du har en mapp med 100 000 underordnade objekt. Om du tar den nedre gränsen på 30 objekt som bearbetas per sekund kan det ta en timme att uppdatera behörigheten för hela mappen. Mer information om Data Lake Storage Gen1-ACL:er finns på [Åtkomstkontroll i Azure Data Lake Storage Gen1](data-lake-store-access-control.md). Om du vill ha bättre prestanda när du tilldelar ACL:er rekursivt kan du använda Kommandoradsverktyget för Azure Data Lake. Verktyget skapar flera trådar och rekursiv navigeringslogik för att snabbt tillämpa ACL:er på miljontals filer. Verktyget är tillgängligt för Linux och Windows, och [dokumentation](https://github.com/Azure/data-lake-adlstool) och [nedladdningar](https://aka.ms/adlstool-download) för det här verktyget finns på GitHub. Samma prestandaförbättringar kan aktiveras med dina egna verktyg skrivna med Data Lake Storage Gen1 [.NET](data-lake-store-data-operations-net-sdk.md) och [Java](data-lake-store-get-started-java-sdk.md) SDK: er.

### <a name="use-security-groups-versus-individual-users"></a>Använda säkerhetsgrupper jämfört med enskilda användare

När du arbetar med stordata i Data Lake Storage Gen1 används troligen ett tjänsthuvudnamn för att tillåta tjänster som Azure HDInsight att arbeta med data. Det kan dock finnas fall där enskilda användare behöver tillgång till data också. I sådana fall måste du använda Azure Active [Directory-säkerhetsgrupper](data-lake-store-secure-data.md#create-security-groups-in-azure-active-directory) i stället för att tilldela enskilda användare till mappar och filer.

När en säkerhetsgrupp har tilldelats behörigheter kräver det inga uppdateringar av Data Lake Storage Gen1 om du lägger till eller tar bort användare från gruppen. Detta säkerställer också att du inte [överskrider](data-lake-store-access-control.md#the-owning-user)gränsen på [32 åtkomst- och standard-ACL:er](../azure-resource-manager/management/azure-subscription-service-limits.md#data-lake-store-limits) (detta inkluderar de fyra ACL-erna i POSIX-stil som alltid är associerade med varje fil och mapp: den ägande användaren , [den ägande gruppen](data-lake-store-access-control.md#the-owning-group), [masken](data-lake-store-access-control.md#the-mask)och andra).

### <a name="security-for-groups"></a>Säkerhet för grupper

Som diskuterats, när användare behöver åtkomst till Data Lake Storage Gen1, är det bäst att använda Azure Active Directory säkerhetsgrupper. Vissa rekommenderade grupper som ska börja med kan vara **ReadOnlyUsers,** **WriteAccessUsers**och **FullAccessUsers** för roten till kontot och till och med separata för viktiga undermappar. Om det finns andra förväntade grupper av användare som kan läggas till senare, men som inte har identifierats ännu, kan du överväga att skapa dummy säkerhetsgrupper som har åtkomst till vissa mappar. Med hjälp av säkerhetsgruppen säkerställer du att du senare inte behöver en lång bearbetningstid för att tilldela nya behörigheter till tusentals filer.

### <a name="security-for-service-principals"></a>Säkerhet för tjänstens huvudmän

Azure Active Directory-tjänsthuvudnamn används vanligtvis av tjänster som Azure HDInsight för att komma åt data i Data Lake Storage Gen1. Beroende på åtkomstkraven för flera arbetsbelastningar kan det finnas vissa överväganden för att säkerställa säkerheten inom och utanför organisationen. För många kunder kan ett enda Azure Active Directory-tjänsthuvudnamn vara tillräckligt och det kan ha fullständig behörighet i roten för Data Lake Storage Gen1-kontot. Andra kunder kan kräva flera kluster med olika tjänsthuvudnamn där ett kluster har fullständig åtkomst till data och ett annat kluster med endast läsbehörighet. Precis som med säkerhetsgrupperna kan du överväga att göra ett tjänsthuvudnamn för varje förväntat scenario (läsa, skriva, fullt) när ett Data Lake Storage Gen1-konto har skapats.

### <a name="enable-the-data-lake-storage-gen1-firewall-with-azure-service-access"></a>Aktivera brandväggen Data Lake Storage Gen1 med Azure-tjänståtkomst

Data Lake Storage Gen1 stöder möjligheten att aktivera en brandvägg och begränsa åtkomsten endast till Azure-tjänster, vilket rekommenderas för en mindre attackvektor från intrång utifrån. Brandvägg kan aktiveras på Data Lake Storage Gen1-kontot i Azure-portalen via > **brandväggsaktiveringsbrandväggen (ON)** > **Tillåt åtkomst till Azure-tjänster.** **Firewall**

![Brandväggsinställningar i Gen1 för lagring av datasjö](./media/data-lake-store-best-practices/data-lake-store-firewall-setting.png "Brandväggsinställningar i Gen1 för lagring av datasjö")

När brandväggen är aktiverad har endast Azure-tjänster som HDInsight, Data Factory, SQL Data Warehouse osv. På grund av den interna nätverksadressöversättningen som används av Azure stöder brandväggen Data Lake Storage Gen1 inte att begränsa specifika tjänster via IP och är endast avsedd för begränsningar av slutpunkter utanför Azure, till exempel lokalt.

## <a name="performance-and-scale-considerations"></a>Prestanda- och skalningsöverväganden

En av de mest kraftfulla funktionerna i Data Lake Storage Gen1 är att den tar bort de hårda gränserna för datadataflöde. Om du tar bort gränserna kan kunderna öka sin datastorlek och följa med prestandakraven utan att behöva fragmentera data. En av de viktigaste övervägandena för att optimera Data Lake Storage Gen1 prestanda är att den presterar bäst när de ges parallellism.

### <a name="improve-throughput-with-parallelism"></a>Förbättra genomströmningen med parallellism

Överväg att ge 8-12 trådar per kärna för den mest optimala läs/skrivgenomströmning. Detta beror på att blockera läsningar/skrivningar på en enda tråd, och fler trådar kan tillåta högre samtidighet på den virtuella datorn. Se till att nivåerna är felfria och parallellismen kan ökas, se till att övervaka den virtuella datorns CPU-användning.

### <a name="avoid-small-file-sizes"></a>Undvik små filstorlekar

POSIX-behörigheter och granskning i Data Lake Storage Gen1 levereras med en overhead som blir uppenbar när du arbetar med många små filer. Bäst att du kan gruppera dina data i större filer jämfört med att skriva tusentals eller miljontals små filer till Data Lake Storage Gen1. Om du undviker små filstorlekar kan det finnas flera fördelar, till exempel:

* Sänka autentiseringskontrollerna mellan flera filer
* Minskade öppna filanslutningar
* Snabbare kopiering/replikering
* Färre filer att bearbeta vid uppdatering av datasjölagring gen1 POSIX-behörigheter

Beroende på vilka tjänster och arbetsbelastningar som använder data är en bra storlek att tänka på för filer 256 MB eller mer. Om filstorlekarna inte kan batchas när du landar i Data Lake Storage Gen1 kan du ha ett separat komprimeringsjobb som kombinerar dessa filer till större. Mer information och rekommendation om filstorlekar och ordna data i DataSjölagring Gen1 finns i [Strukturera datauppsättningen](data-lake-store-performance-tuning-guidance.md#structure-your-data-set).

### <a name="large-file-sizes-and-potential-performance-impact"></a>Stora filstorlekar och potentiell prestandapåverkan

Även om Data Lake Storage Gen1 stöder stora filer upp till petabyte i storlek, för optimal prestanda och beroende på vilken process som läser data, kanske det inte är idealiskt att gå över 2 GB i genomsnitt. När du till exempel använder **Distcp** för att kopiera data mellan platser eller olika lagringskonton är filer den finaste detaljnivån som används för att bestämma kartuppgifter. Så om du kopierar 10 filer som är 1 TB vardera, tilldelas högst 10 mappers. Om du har många filer med mappers tilldelade, fungerar mappers till en början parallellt för att flytta stora filer. Men eftersom jobbet börjar varva ner endast ett fåtal mappers förblir allokerade och du kan fastna med en enda mapper som tilldelats en stor fil. Microsoft har skickat förbättringar till Distcp för att lösa problemet i framtida Hadoop-versioner.

Ett annat exempel att tänka på är när du använder Azure Data Lake Analytics med Data Lake Storage Gen1. Beroende på hur extraheraren bearbetas kan vissa filer som inte kan delas (till exempel XML, JSON) drabbas av prestanda när de är större än 2 GB. I de fall där filer kan delas av en utsug (till exempel CSV) föredras stora filer.

### <a name="capacity-plan-for-your-workload"></a>Kapacitetsplan för din arbetsbelastning

Azure Data Lake Storage Gen1 tar bort de hårda IO-begränsningsgränser som placeras på Blob-lagringskonton. Det finns dock fortfarande mjuka gränser som måste beaktas. Standardgränserna för begränsning av in-/utgående utsteg uppfyller behoven i de flesta scenarier. Om din arbetsbelastning behöver höja gränserna kan du arbeta med Microsoft-support. Titta också på gränserna under proof-of-concept-stadiet så att IO-begränsningsgränser inte träffas under produktionen. Om det händer kan det krävas en manuell ökning från Microsofts teknikteam. Om IO-begränsning inträffar returnerar Azure Data Lake Storage Gen1 en felkod på 429 och bör helst göras om med en lämplig exponentiell backoff-princip.

### <a name="optimize-writes-with-the-data-lake-storage-gen1-driver-buffer"></a>Optimera "skrivningar" med drivrutinsbufferten Data Lake Storage Gen1

Om du vill optimera prestanda och minska IOPS när du skriver till Data Lake Storage Gen1 från Hadoop utför du skrivåtgärder så nära drivrutinsbuffertstorleken Data Lake Storage Gen1 som möjligt. Försök att inte överskrida buffertstorleken innan du tömmer, till exempel när du streamar med Apache Storm eller Spark-arbetsbelastningar. När du skriver till Data Lake Storage Gen1 från HDInsight/Hadoop är det viktigt att veta att Data Lake Storage Gen1 har en drivrutin med en buffert på 4 MB. Liksom många drivrutiner för filsystemet kan den här bufferten rensas manuellt innan den når storleken på 4 MB. Om inte, spolas den omedelbart till lagring om nästa skrivning överskrider buffertens maximala storlek. Om möjligt måste du undvika ett överskridande eller ett betydande underkörning av bufferten vid synkronisering/tömningsprincip efter antal eller tidsfönster.

## <a name="resiliency-considerations"></a>Att tänka på om återhämtning

När du skapar ett system med Data Lake Storage Gen1 eller någon molntjänst måste du ta hänsyn till dina tillgänglighetskrav och hur du ska svara på potentiella avbrott i tjänsten. Ett problem kan vara lokaliserat till den specifika instansen eller till och med regionomfattande, så det är viktigt att ha en plan för båda. Beroende på **återställningstidens mål** och målsassa för **återställningspunkt** för din arbetsbelastning kan du välja en mer eller mindre aggressiv strategi för hög tillgänglighet och haveriberedskap.

### <a name="high-availability-and-disaster-recovery"></a>Hög tillgänglighet och haveriberedskap

Hög tillgänglighet (HA) och katastrofåterställning (DR) kan ibland kombineras tillsammans, även om var och en har en något annorlunda strategi, särskilt när det gäller data. Data Lake Storage Gen1 hanterar redan 3x replikering under huven för att skydda mot lokaliserade maskinvarufel. Men eftersom replikering mellan regioner inte är inbyggd, måste du hantera detta själv. När du skapar en plan för HA, i händelse av ett avbrott i tjänsten arbetsbelastningen behöver tillgång till de senaste uppgifterna så snabbt som möjligt genom att växla över till en separat replikerad instans lokalt eller i en ny region.

I en DR-strategi, för att förbereda sig för den osannolika händelsen av ett katastrofalt fel i en region, är det också viktigt att ha data replikerade till en annan region. Dessa data kan inledningsvis vara samma som replikerade HA-data. Du måste dock också överväga dina krav för kantfall, till exempel dataskador där du kanske vill skapa periodiska ögonblicksbilder att falla tillbaka till. Beroende på datas betydelse och storlek bör du överväga rullande deltabilder av 1-, 6- och 24-timmarsperioder på den lokala och/eller sekundära arkivet, enligt risktoleranser.

För dataåtersåterhet med Data Lake Storage Gen1 rekommenderas att geo-replikera dina data till en separat region med en frekvens som uppfyller dina HA/DR-krav, helst varje timme. Denna frekvensen av replikering minimerar massiva datarörelser som kan ha konkurrerande dataflödesbehov med huvudsystemet och ett bättre återställningspunktmål (RPO). Dessutom bör du överväga olika sätt för programmet att använda Data Lake Storage Gen1 att automatiskt växla över till det sekundära kontot genom att övervaka utlösare eller längden på misslyckade försök, eller åtminstone skicka ett meddelande till administratörer för manuella åtgärder. Tänk på att det finns avvägning av att misslyckas över kontra väntar på en tjänst att komma tillbaka online. Om data inte har replikerats kan en redundans orsaka potentiell dataförlust, inkonsekvens eller komplex sammanslagning av data.

Nedan finns de tre rekommenderade alternativen för att dirigera replikering mellan Data Lake Storage Gen1-konton och viktiga skillnader mellan var och en av dem.

|  |Distcp (olikart)  |Azure Data Factory  |AdlCopy (AdlCopy)  |
|---------|---------|---------|---------|
|**Skalbegränsningar**     | Avgränsad av arbetsnoder        | Begränsas av Max Cloud Data Movement-enheter        | Bunden av Analytics-enheter        |
|**Stöder kopiering av deltan**     |   Ja      | Inga         | Inga         |
|**Inbyggd orkestrering**     |  Nej (använd Oozie Airflow eller cron jobb)       | Ja        | Nej (Använd Azure Automation eller Windows Schemaläggaren)         |
|**Filsystem som stöds**     | ADL, HDFS, WASB, S3, GS, CFS        |Många, se [Kontakter](../data-factory/connector-azure-blob-storage.md).         | ADL till ADL, WASB till ADL (endast samma region)        |
|**Stöd för operativsystemet**     |Alla OS som kör Hadoop         | Ej tillämpligt          | Windows 10         |

### <a name="use-distcp-for-data-movement-between-two-locations"></a>Använd Distcp för dataflyttning mellan två platser

Distcp är ett Linux-kommandoradsverktyg som levereras med Hadoop och tillhandahåller distribuerad dataförflyttning mellan två platser. De två platserna kan vara Data Lake Storage Gen1, HDFS, WASB eller S3. Det här verktyget använder MapReduce-jobb i ett Hadoop-kluster (till exempel HDInsight) för att skala ut på alla noder. Distcp anses vara det snabbaste sättet att flytta stordata utan speciella nätverkskompressionsapparater. Distcp ger också ett alternativ för att bara uppdatera deltan mellan två platser, hanterar automatiska återförsök samt dynamisk skalning av beräkning. Detta tillvägagångssätt är otroligt effektivt när det gäller att replikera saker som Hive / Spark tabeller som kan ha många stora filer i en enda katalog och du bara vill kopiera över de ändrade data. Av dessa skäl är Distcp det mest rekommenderade verktyget för att kopiera data mellan stordatalager.

Kopieringsjobb kan utlösas av Apache Oozie-arbetsflöden med frekvens- eller datautlösare samt Linux cron-jobb. För intensiva replikeringsjobb rekommenderas att snurra upp ett separat HDInsight Hadoop-kluster som kan justeras och skalas specifikt för kopieringsjobben. Detta säkerställer att kopieringsjobb inte stör kritiska jobb. Om du kör replikering på en tillräckligt bred frekvens kan klustret till och med tas ned mellan varje jobb. Om det inte går över till sekundär region, se till att ett annat kluster också snurras upp i den sekundära regionen för att replikera nya data tillbaka till det primära Data Lake Storage Gen1-kontot när det kommer tillbaka upp. Exempel på hur du använder Distcp finns i [Använda Distcp för att kopiera data mellan Azure Storage Blobbar och Data Lake Storage Gen1](data-lake-store-copy-data-wasb-distcp.md).

### <a name="use-azure-data-factory-to-schedule-copy-jobs"></a>Använda Azure Data Factory för att schemalägga kopieringsjobb

Azure Data Factory kan också användas för att schemalägga kopieringsjobb med hjälp av en **kopieringsaktivitet**och kan även ställas in på en frekvens via **kopieringsguiden**. Tänk på att Azure Data Factory har en gräns för molndataflyttningar (DMUs) och så småningom begränsar dataflödet/beräkningen för stora dataarbetsbelastningar. Dessutom erbjuder Azure Data Factory för närvarande inte deltauppdateringar mellan Data Lake Storage Gen1-konton, så mappar som Hive-tabeller kräver en fullständig kopia för att replikera. Mer information om hur du kopierar med Data Factory finns i [guiden Kopiera aktivitet.](../data-factory/copy-activity-performance.md)

### <a name="adlcopy"></a>AdlCopy (AdlCopy)

AdlCopy är ett kommandoradsverktyg i Windows som gör att du kan kopiera data mellan två Data Lake Storage Gen1-konton endast inom samma region. AdlCopy-verktyget är ett fristående alternativ eller möjlighet att använda ett Azure Data Lake Analytics-konto för att köra ditt kopieringsjobb. Även om det ursprungligen byggdes för kopior på begäran i motsats till en robust replikering, ger det ett annat alternativ för att göra distribuerad kopiering över Data Lake Storage Gen1-konton inom samma region. För tillförlitlighet rekommenderar vi att du använder alternativet premiumdatasjöanalys för alla produktionsarbetsbelastningar. Den fristående versionen kan returnera upptagna svar och har begränsad skala och övervakning.

Precis som Distcp måste AdlCopy orkestreras av något som Liknar Azure Automation eller Windows Schemaläggaren. Precis som med Data Factory stöder AdlCopy inte kopiering endast uppdaterade filer, utan omkopior och skriva över befintliga filer. Mer information och exempel på hur du använder AdlCopy finns i [Kopiera data från Azure Storage Blobbar till Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md).

## <a name="monitoring-considerations"></a>Övervakning överväganden

Data Lake Storage Gen1 ger detaljerade diagnostikloggar och granskning. Data Lake Storage Gen1 innehåller några grundläggande mått i Azure-portalen under Data Lake Storage Gen1-kontot och i Azure Monitor. Tillgängligheten för Data Lake Storage Gen1 visas i Azure-portalen. Det här måttet uppdateras dock var sjunde minut och kan inte efterfrågas via ett offentligt exponerat API. Om du vill få den senaste tillgängligheten för ett Data Lake Storage Gen1-konto måste du köra dina egna syntetiska tester för att validera tillgängligheten. Andra mått som total lagringsanvändning, läs-/skrivbegäranden och ingående/utgående kan ta upp till 24 timmar att uppdatera. Så, mer up-to-date mått måste beräknas manuellt via Hadoop kommandoradsverktyg eller aggregering logginformation. Det snabbaste sättet att få den senaste lagringsanvändningen är att köra det här HDFS-kommandot från en Hadoop-klusternod (till exempel huvudnod):

    hdfs dfs -du -s -h adl://<adlsg1_account_name>.azuredatalakestore.net:443/

### <a name="export-data-lake-storage-gen1-diagnostics"></a>Exportera datalagringsgenm1-diagnostik

Ett av de snabbaste sätten att få tillgång till sökbara loggar från Data Lake Storage Gen1 är att aktivera loggleverans till **Log Analytics** under **diagnostikbladet** för Data Lake Storage Gen1-kontot. Detta ger omedelbar åtkomst till inkommande loggar med tid och innehållsfilter, tillsammans med varningsalternativ (e-post /webhook) som utlöses inom 15 minuters intervall. Instruktioner finns i [Komma åt diagnostikloggar för Azure Data Lake Storage Gen1](data-lake-store-diagnostic-logs.md).

För mer aviseringar i realtid och mer kontroll över var loggarna ska landa kan du överväga att exportera loggar till Azure EventHub där innehåll kan analyseras individuellt eller över ett tidsfönster för att skicka meddelanden i realtid till en kö. Ett separat program som en [logikapp](../connectors/connectors-create-api-azure-event-hubs.md) kan sedan använda och kommunicera aviseringarna till rätt kanal, samt skicka mått till övervakningsverktyg som NewRelic, Datadog eller AppDynamics. Alternativt, om du använder ett verktyg från tredje part som ElasticSearch, kan du exportera loggarna till Blob Storage och använda [Azure Logstash-insticksprogrammet](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob) för att använda data i din Elasticsearch-, Kibana- och Logstash -stack (ELK).

### <a name="turn-on-debug-level-logging-in-hdinsight"></a>Aktivera loggning på felsökningsnivå i HDInsight

Om datasjölagring Gen1-loggleverans inte är aktiverat, är Azure HDInsight också ett sätt att aktivera loggning på [klientsidan för Data Lake Storage Gen1](data-lake-store-performance-tuning-mapreduce.md) via log4j. Du måste ange följande egenskap i **Ambari** > **YARN** > **Config** > **Advanced yarn-log4j-konfigurationer:**

    log4j.logger.com.microsoft.azure.datalake.store=DEBUG

När egenskapen har angetts och noderna startas om skrivs Data Lake Storage Gen1-diagnostiken\<till\>YARN-loggarna på noderna (/tmp/ user /yarn.log) och viktiga detaljer som fel eller begränsning (HTTP 429-felkod) kan övervakas. Samma information kan också övervakas i Azure Monitor-loggar eller var helst loggar levereras till i [diagnostikbladet](data-lake-store-diagnostic-logs.md) för Data Lake Storage Gen1-kontot. Vi rekommenderar att du åtminstone har aktiverat loggning på klientsidan eller använda loggleveransalternativet med Data Lake Storage Gen1 för driftssynlighet och enklare felsökning.

### <a name="run-synthetic-transactions"></a>Kör syntetiska transaktioner

För närvarande har servicetillgänglighetsmåttet för Data Lake Storage Gen1 i Azure-portalen 7-minuters uppdateringsfönster. Det går inte heller att fråga sig med ett offentligt exponerat API. Därför rekommenderas att bygga ett grundläggande program som gör syntetiska transaktioner till Data Lake Storage Gen1 som kan ge upp till minut tillgänglighet. Ett exempel kan vara att skapa en WebJob-, Logic App- eller Azure Function App för att utföra en läs-, skapa- och uppdatering mot DataSjölagring gen1 och skicka resultaten till din övervakningslösning. Åtgärderna kan utföras i en tillfällig mapp och sedan tas bort efter testet, som kan köras var 30-60 sekunder, beroende på kraven.

## <a name="directory-layout-considerations"></a>Hänsyn till kataloglayout

När du landar data i en datasjö är det viktigt att förplanera datastrukturen så att säkerhet, partitionering och bearbetning kan användas effektivt. Många av följande rekommendationer kan användas oavsett om det är med Azure Data Lake Storage Gen1, Blob Storage eller HDFS. Varje arbetsbelastning har olika krav på hur data förbrukas, men nedan finns några vanliga layouter att tänka på när du arbetar med IoT- och batchscenarier.

### <a name="iot-structure"></a>IoT-struktur

I IoT-arbetsbelastningar kan det finnas en hel del data som landas i datalagret som sträcker sig över många produkter, enheter, organisationer och kunder. Det är viktigt att förplanera kataloglayouten för organisation, säkerhet och effektiv bearbetning av data för nedströmskonsumenter. En allmän mall att tänka på kan vara följande layout:

    {Region}/{SubjectMatter(s)}/{yyyy}/{mm}/{dd}/{hh}/

Till exempel kan landningstelemetri för en flygplansmotor inom Storbritannien se ut så här:

    UK/Planes/BA1293/Engine1/2017/08/11/12/

Det finns en viktig anledning att placera datumet i slutet av mappstrukturen. Om du vill låsa vissa regioner eller ämnesfrågor för användare/grupper kan du enkelt göra det med POSIX-behörigheterna. Annars, om det fanns ett behov av att begränsa en viss säkerhetsgrupp till att visa bara brittiska data eller vissa plan, med datumstrukturen framför ett separat tillstånd skulle krävas för många mappar under varje timme mapp. Dessutom, med datumstrukturen framför skulle exponentiellt öka antalet mappar med tiden.

### <a name="batch-jobs-structure"></a>Batch jobb struktur

Från en hög nivå är en vanlig metod vid batchbearbetning att landa data i en "i"-mapp. Sedan, när data bearbetas, placera nya data i en "ut" mapp för nedströms processer att konsumera. Den här katalogstrukturen ses ibland för jobb som kräver bearbetning på enskilda filer och kanske inte kräver massiv parallell bearbetning över stora datauppsättningar. Precis som IoT-strukturen som rekommenderas ovan har en bra katalogstruktur mappar på överordnad nivå för saker som region och ämnesfrågor (till exempel organisation, produkt/producent). Den här strukturen hjälper till att skydda data i hela organisationen och bättre hantering av data i dina arbetsbelastningar. Överväg dessutom datum och tid i strukturen för att möjliggöra bättre organisation, filtrerade sökningar, säkerhet och automatisering i bearbetningen. Detaljnivån för datumstrukturen bestäms av det intervall då data överförs eller bearbetas, till exempel timme, dag eller till och med varje månad.

Ibland misslyckas filbearbetningen på grund av skadade data eller oväntade format. I sådana fall kan katalogstrukturen dra nytta av en **/bad-mapp** för att flytta filerna till för ytterligare inspektion. Batch-jobbet kan också hantera rapportering eller meddelande om dessa *felaktiga* filer för manuella åtgärder. Tänk på följande mallstruktur:

    {Region}/{SubjectMatter(s)}/In/{yyyy}/{mm}/{dd}/{hh}/
    {Region}/{SubjectMatter(s)}/Out/{yyyy}/{mm}/{dd}/{hh}/
    {Region}/{SubjectMatter(s)}/Bad/{yyyy}/{mm}/{dd}/{hh}/

Till exempel får ett marknadsföringsföretag dagliga dataexektioner av kunduppdateringar från sina kunder i Nordamerika. Det kan se ut som följande utdrag före och efter att ha bearbetats:

    NA/Extracts/ACMEPaperCo/In/2017/08/14/updates_08142017.csv
    NA/Extracts/ACMEPaperCo/Out/2017/08/14/processed_updates_08142017.csv

I det vanliga fallet med batchdata som bearbetas direkt till databaser som Hive eller traditionella SQL-databaser, finns det inte något behov av en **/in** eller **/out-mapp** eftersom utdata redan går in i en separat mapp för Hive-tabellen eller extern databas. Dagliga utdrag från kunder skulle till exempel landa i sina respektive mappar, och orkestrering av något som Azure Data Factory, Apache Oozie eller Apache Airflow skulle utlösa ett dagligt Hive- eller Spark-jobb för att bearbeta och skriva data i en Hive-tabell.

## <a name="next-steps"></a>Nästa steg

* [Översikt över Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Åtkomstkontroll i Azure Data Lake Storage Gen1](data-lake-store-access-control.md)
* [Säkerhet i Azure Data Lake Storage Gen1](data-lake-store-security-overview.md)
* [Justera Azure Data Lake Storage Gen1 för prestanda](data-lake-store-performance-tuning-guidance.md)
* [Prestandajusteringsvägledning för användning av HDInsight Spark med Azure Data Lake Storage Gen1](data-lake-store-performance-tuning-spark.md)
* [Vägledning för prestandajustering för användning av HDInsight Hive med Azure Data Lake Storage Gen1](data-lake-store-performance-tuning-hive.md)
* [Skapa HDInsight-kluster med Gen1 för lagring av datasjö](data-lake-store-hdinsight-hadoop-use-portal.md)
