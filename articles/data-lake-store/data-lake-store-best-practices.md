---
title: Bästa praxis för att använda Azure Data Lake Store | Microsoft Docs
description: Läs Metodtips om datapåfyllning, datum säkerhet och prestanda som rör bruket av Azure Data Lake Store
services: data-lake-store
documentationcenter: ''
author: sachinsbigdata
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/02/2018
ms.author: sachins
ms.openlocfilehash: 7493c10407bfe83bdc7277c49dae1a7e9d7c39f2
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="best-practices-for-using-azure-data-lake-store"></a>Metodtips för Azure Data Lake Store
I den här artikeln får information du om metodtips och överväganden för att arbeta med Azure Data Lake Store. Den här artikeln innehåller information kring säkerhet, prestanda, återhämtning och övervakning för Data Lake Store. Arbeta med verkligen stordata i tjänster som Azure HDInsight var krånglig innan Data Lake Store. Var du tvungen att fragmentera data över flera Blob storage-konton så att petabyte lagring och optimala prestanda som kan byggas ut kan uppnås. De flesta av de hårda gränsvärdena för storlek och prestanda tas bort med Data Lake Store. Det finns dock fortfarande vissa aspekter som den här artikeln täcker så att du kan få bästa möjliga prestanda med Data Lake Store. 

## <a name="security-considerations"></a>Säkerhetsöverväganden

Azure Data Lake Store ger POSIX åtkomst styr och detaljerad granskning för Azure Active Directory (AD Azure) användare, grupper och tjänstens huvudnamn. Dessa åtkomstkontroller kan anges till befintliga filer och mappar. Åtkomstkontroller kan också användas för att skapa standardvärden som kan tillämpas på nya filer eller mappar. När behörigheter har angetts till befintliga mappar och underordnade objekt behöver behörigheterna spridda rekursivt på varje objekt. Om många filer, sprider behörigheter kan det ta lång tid. Den tid det tar kan variera mellan 30 50 objekt som behandlas per sekund. Planera därför mappen struktur och användargrupper på lämpligt sätt. Annars kan orsaka oväntade fördröjningar och problem när du arbetar med dina data. 

Anta att du har en mapp med 100 000 underordnade objekt. Om du tar den undre gränsen för 30 objekt som behandlas per sekund kan för att uppdatera behörighet för hela mappen ta en timme. Mer information om Data Lake Store-ACL: er är tillgängliga på [åtkomstkontroll i Azure Data Lake Store](data-lake-store-access-control.md). Du kan använda Azure Data Lake-kommandoradsverktyget för bättre prestanda om hur du tilldelar ACL: er rekursivt. Verktyget skapar flera trådar och rekursiva navigering logik för att snabbt använda ACL: er till miljoner filer. Verktyget är tillgängligt för Linux och Windows, och [dokumentationen](https://github.com/Azure/data-lake-adlstool) och [hämtar](http://aka.ms/adlstool-download) för det här verktyget finns på GitHub. Dessa samma prestandaförbättringar kan aktiveras med dina egna verktyg som skrivits med Data Lake Store [.NET](data-lake-store-data-operations-net-sdk.md) och [Java](data-lake-store-get-started-java-sdk.md) SDK: er.

### <a name="use-security-groups-versus-individual-users"></a>Använda säkerhetsgrupper jämfört med enskilda användare 

När du arbetar med stordata i Data Lake Store troligen används en tjänst som för att tillåta tjänster, till exempel Azure HDInsight för att arbeta med data. Det kan dock finnas fall där enskilda användare behöver åtkomst till data samt. I sådana fall måste du använda Azure Active Directory [säkerhetsgrupper](data-lake-store-secure-data.md#create-security-groups-in-azure-active-directory) i stället för att enskilda användare mappar och filer. 

När en säkerhetsgrupp tilldelas behörigheter att lägga till eller ta bort användare från gruppen kräver inte några uppdateringar till Data Lake Store. Detta hjälper också se till att du inte överskrider gränsen på [32 åtkomst och standard-ACL: er](../azure-subscription-service-limits.md#data-lake-store-limits) (Detta omfattar fyra POSIX-typ ACL: erna som alltid är associerade med varje fil och mapp: [ägande användaren](data-lake-store-access-control.md#the-owning-user), [gruppen ägande](data-lake-store-access-control.md#the-owning-group), [masken](data-lake-store-access-control.md#the-mask-and-effective-permissions), och andra).

### <a name="security-for-groups"></a>Säkerhet för grupper 

Det är bäst att använda Azure Active Directory-säkerhetsgrupper som diskuterats, när användare behöver åtkomst till Data Lake Store. Vissa rekommenderade börja med grupper kan vara **ReadOnlyUsers**, **WriteAccessUsers**, och **FullAccessUsers** separata frågor om nyckeln för rot för kontot, och även undermappar. Om det finns andra förväntade grupper av användare som kan läggas till senare, men inte har identifierats ännu kan du överväga att skapa dummy säkerhetsgrupper som har åtkomst till vissa mappar. Säkerhetsgrupp garanterar att senare inte behöver du lång tid för att tilldela behörigheter för nya filer. 

### <a name="security-for-service-principals"></a>Säkerhet för tjänstens huvudnamn 

Azure Active Directory-tjänstens huvudnamn som vanligtvis används av tjänster som Azure HDInsight för att komma åt data i Data Lake Store. Beroende på kraven för åtkomst mellan flera arbetsbelastningar, kan det finnas vissa aspekter att säkerställa säkerheten i och utanför organisationen. En enda Azure Active Directory-tjänstens huvudnamn kan vara lämplig för många kunder och den kan ha fullständig behörighet i roten på Data Lake Store. Andra kunder kan kräva flera kluster med olika tjänstens huvudnamn där ett kluster har fullständig åtkomst till data och ett annat kluster med bara läsbehörighet. Precis som med säkerhetsgrupper, kan du göra ett huvudnamn för tjänsten för varje förväntat scenario (läsa, skriva, fullständig) när ett Data Lake Store-konto har skapats. 

### <a name="enable-the-data-lake-store-firewall-with-azure-service-access"></a>Aktivera Brandvägg för Data Lake Store med Azure-tjänståtkomst 

Data Lake Store stöder aktivering av en brandvägg och begränsa endast åtkomst till Azure-tjänster, vilket rekommenderas för en mindre angreppsvinkel från utanför intrång. Brandväggen kan vara aktiverad på Data Lake Store-konto i Azure-portalen via den **brandväggen** > **aktivera brandväggen (på)** > **Tillåt åtkomst till Azure-tjänster**  alternativ.  

![Brandväggsinställningar i Data Lake Store](./media/data-lake-store-best-practices/data-lake-store-firewall-setting.png "brandväggsinställningar i Data Lake Store")

När brandväggen är aktiverad, kan endast Azure-tjänster, till exempel HDInsight, har Data Factory, SQL Data Warehouse, etc. åtkomst till Data Lake Store. Data Lake Store-brandväggen har inte stöd för att begränsa vissa tjänster av IP på grund av interna network address translation används av Azure, och är endast avsedd för slutpunkter utanför Azure, till exempel lokalt. 

## <a name="performance-and-scale-considerations"></a>Överväganden för prestanda och skalning

En av de viktigaste funktionerna i Data Lake Store är det tar bort hårda gränser för överföring av data. Ta bort gränserna som ger kunder möjlighet att utöka sina datastorleken och medföljer prestandakrav utan att behöva Fragmentera data. En av de viktigaste övervägandena för att optimera prestanda för Data Lake Store är utförs bäst när angivna parallellitet.

### <a name="improve-throughput-with-parallelism"></a>Förbättra genomflöde med parallellitet 

Överväga att 12 8 trådar per kärna för det mest optimala dataflödet för läsning och skrivning. Detta beror på blockerande läsning/skrivning i en enda tråd och flera trådar kan tillåta högre samtidighet på den virtuella datorn. För att säkerställa att nivåer är felfria och parallellitet kan ökas, bör du övervaka processoranvändningen för den virtuella datorn.   

### <a name="avoid-small-file-sizes"></a>Undvika liten filstorlek

POSIX behörigheter och granskning i Data Lake Store innehåller en kostnader som är synliga när du arbetar med många små filer. Som bästa praxis, måste du batch dina data till större filer jämfört med skrivning tusentals eller miljontals små filer till Data Lake Store. Undvika liten filstorlek kan har flera fördelar, exempel:

* Sänker autentiseringskontroller i flera filer
* Minskad öppna filen anslutningar
* Snabbare kopiera/replikering
* Färre filer ska bearbeta när du uppdaterar Data Lake Store POSIX-behörigheter 

Beroende på vilka tjänster och arbetsbelastningar använder data, är en bra att tänka på för filstorlekar 256 MB till 1 GB, helst inte kommer under 100 MB eller över 2 GB. Om det går inte att vara batchar filstorlekarna när hamnar i Data Lake Store, kan du ha ett separat komprimering jobb som kombinerar de här filerna till de större. Mer information och rekommendation om filstorlekar och sortera data i Data Lake Store finns [struktur datauppsättningen](data-lake-store-performance-tuning-guidance.md#structure-your-data-set). 

### <a name="large-file-sizes-and-potential-performance-impact"></a>Mycket stora filer och eventuell prestandapåverkan 

Även om Data Lake Store stöder stora filer upp till petabyte i storlek för optimala prestanda och beroende på vilken process som läser data, kan den inte är optimal att gå över 2 GB i genomsnitt. Till exempel när du använder **Distcp** för att kopiera data mellan platser eller annan storage-konton, filer är nivån finest av granularitet som används för att avgöra kartan uppgifter. Så om du kopierar 10 filer som är 1 TB allokerade högst 10 mappers. Även om du har många filer med mappers som tilldelats fungerar från början på mappers parallellt att flytta stora filer. När jobbet startar likvidation endast några mappers förblir allokerade och du kan ha fastnat med en enda mapper som tilldelats en stor fil. Microsoft har skickat förbättringar av Distcp för det här problemet i framtida versioner av Hadoop.  

Ett annat exempel att tänka på är när du använder Azure Data Lake Analytics med Data Lake Store. Beroende på det bearbetas av extraktor kan vissa filer inte kan delas (till exempel, XML, JSON) bli lidande på prestanda när det är större än 2 GB. I fall där filer kan delas av en extraktor (till exempel CSV) rekommenderas stora filer.

### <a name="capacity-plan-for-your-workload"></a>Kapacitet plan för din arbetsbelastning 

Azure Data Lake Store tar bort hårda IO throttling-begränsning i Blob storage-konton. Det finns dock fortfarande mjuka gränser som måste beaktas. Standard ingång-/ utgång bandbreddsbegränsning gränser uppfyller behoven hos de flesta scenarier. Om din arbetsbelastning behöver de gränser som har ökat, tillsammans med Microsoft-supporten. Också titta på gränserna under fasen konceptbevis-så att bandbreddsbegränsning i/o-gränserna inte träffar under produktionen. Om det händer kan behöva väntar för en manuell ökning från Microsoft tekniker. Om i/o-begränsning inträffar Azure Data Lake Store returnerar en felkod för 429 och helst ska göras med en lämplig exponentiell backoff princip. 

### <a name="optimize-writes-with-the-data-lake-store-driver-buffer"></a>Optimera ”skriver” med buffert för Data Lake Store-drivrutin 

För att optimera prestandan och minska IOPS när du skriver till Data Lake Store från Hadoop, utföra skrivåtgärder som nära buffertstorleken för Data Lake Store-drivrutin som möjligt. Försök att inte överskrider buffertstorleken innan, exempelvis när strömning med Apache Storm eller Spark streaming arbetsbelastningar. Vid skrivning till Data Lake Store från HDInsight/Hadoop, är det viktigt att veta att Data Lake Store har en drivrutin med en buffert 4 MB. T.ex. drivrutiner för filsystemet många, kan bufferten manuellt tömmas innan storleken 4 MB. Om inte rensade omedelbart till lagring, om nästa skrivning överskrider den maximala buffertstorleken. Om möjligt bör undvika du ett överskridande eller en betydande underskridning buffertens när synkroniserar/lokaliseraren princip per antal eller tid.

## <a name="resiliency-considerations"></a>Att tänka på om återhämtning 

När bygga ett system med Data Lake Store eller tjänst i molnet, måste du tänka på din tillgänglighet och hur du svara på potentiella avbrott i tjänsten. Ett problem kan lokaliseras till den specifika instansen eller även region hela, så att ha en plan för både är viktig. Beroende på den **mål** och **återställningspunktmål** serviceavtal för din arbetsbelastning kan du välja en mer eller mindre aggressivt strategi för hög tillgänglighet och haveriberedskap.

### <a name="high-availability-and-disaster-recovery"></a>Hög tillgänglighet och haveriberedskap 

Hög tillgänglighet och katastrofåterställning (DR) kan ibland kombineras tillsammans, även om varje har en något annorlunda strategi, särskilt när det gäller att data. Data Lake Store hanterar redan 3 x-replikering för att skydda dig mot lokaliserade maskinvarufel. Eftersom replikering över regioner som inte är inbyggd måste du dock hanterar den själv. När du skapar en plan för hög tillgänglighet vid ett avbrott behöver arbetsbelastningen åtkomst till den senaste informationen så snabbt som möjligt genom att växla till en separat replikerade instansen lokalt eller i en ny region.  

I en strategi för Katastrofåterställning, för att förbereda för osannolika ett oåterkalleligt fel på en region, är det också viktigt att data som replikeras till en annan region. Dessa data kan först vara desamma som de replikerade data för hög tillgänglighet. Du måste också bestämma dina krav på edge fall, till exempel skadade data där du kanske vill skapa regelbundna ögonblicksbilder så att de. Beroende på vikten och storleken på data att överväga att delta ögonblicksbilder av 1 – 6- och 24-timmarsformat punkter till arkivet för lokala och/eller sekundära enligt risk toleranser. 

För dataåterhämtning med Data Lake Store, rekommenderas geo replikera data till en separat region med en frekvens som uppfyller kraven för hr/DR helst varje timme. Den här replikeringsfrekvensen minimerar massiv data-förflyttningar som kan ha konkurrerande genomströmning måste med huvudsakliga systemet och ett bättre återställningspunktmål (RPO). Dessutom bör du sätt för programmet som använder Data Lake Store att automatiskt växla över till det sekundära kontot genom övervakning utlösare eller längden på misslyckade försök eller minst ett meddelande skickas till administratörer för manuell åtgärd. Tänk på att det finns förhållandet vid fel och väntar på att en tjänst ska komma online igen. Om data inte har slutförts replikering, orsaka redundans potentiell dataförlust, inkonsekvens eller komplexa sammanslagning av data. 

Nedan visas de översta tre rekommenderade alternativ för samordna replikering mellan datasjölagerkonton och viktigaste skillnaderna mellan dem.


|  |Distcp  |Azure Data Factory  |AdlCopy  |
|---------|---------|---------|---------|
|**Gränser**     | Begränsas av arbetsnoder        | Begränsas av Max molnet dataflyttning enheter        | Bundna av Analytics enheter        |
|**Stöder kopiera går**     |   Ja      | Nej         | Nej         |
|**Inbyggda orchestration**     |  Nej (Använd Oozie luftflödet eller cron)       | Ja        | Nej (Använd Azure Automation eller Schemaläggaren i Windows)         |
|**Filsystem som stöds**     | ADL, HDFS, WASB, S3, GS, CFS        |Ett stort antal, se [kopplingar](../data-factory/connector-azure-blob-storage.md).         | ADL till ADL WASB till ADL (endast samma region)        |
|**OS-stöd**     |Någon OS kör Hadoop         | Gäller inte          | Windows 10         |
   

### <a name="use-distcp-for-data-movement-between-two-locations"></a>Använd Distcp för flytt av data mellan två platser 

Kort för distribuerad kopia Distcp är ett Linux-kommandoradsverktyg som medföljer Hadoop och ger distribuerade dataförflyttning mellan två platser. De två platserna kan vara Data Lake Store, HDFS, WASB eller S3. Det här verktyget använder MapReduce-jobb på ett Hadoop-kluster (till exempel HDInsight) för att skala ut på alla noder. Distcp anses det snabbaste sättet att flytta big data utan speciella nätverksinstallationer av komprimering. Distcp innehåller också ett alternativ för att endast uppdatera går mellan två platser, hanterar automatiska omförsök, samt dynamisk skalning av beräkning. Den här metoden är otroligt effektivt när det gäller att replikera sådant som Hive/Spark-tabeller som kan ha många stora filer i en katalog och du vill kopiera över ändrade data. Därför är Distcp verktyget rekommenderas för att kopiera data mellan stora datalager. 

Kopiera jobb kan utlösas av Apache Oozie arbetsflöden med hjälp av frekvens eller utlösare för data samt Linux cron jobb. För minnesintensiva replikeringsjobb rekommenderas att få igång en separat HDInsight Hadoop-kluster kan justerade och skalas för kopiera jobb. Detta säkerställer att kopiera jobb inte stör kritiska jobb. Om du kör replikering på ett litet frekvens, kan även klustret tas mellan varje jobb. Om du redundansväxlar till sekundär region, kontrollerar du att ett annat klustret är också de i den sekundära regionen för att replikera nya data tillbaka till det primära Data Lake Store-kontot när den visas. Exempel på hur du använder Distcp finns [Använd Distcp att kopiera data mellan Azure Storage-Blobbar och Data Lake Store](data-lake-store-copy-data-wasb-distcp.md).

### <a name="use-azure-data-factory-to-schedule-copy-jobs"></a>Använd Azure Data Factory för att schemalägga kopia 

Azure Data Factory kan också användas för att schemalägga kopiera jobb med hjälp av en **Kopieringsaktiviteten**, och kan även ställas in på en frekvens via den **guiden Kopiera**. Tänk på att Azure Data Factory har en begränsning på molnet data movement enheter (DMUs) och slutligen caps genomströmning/beräkning för stora arbetsbelastningar. Dessutom erbjuder Azure Data Factory för närvarande inte delta uppdateringar mellan datasjölagerkonton, så kräver en fullständig kopia att replikera mappar som Hive-tabeller. Referera till den [Kopieringsaktiviteten prestandajustering guiden](../data-factory/v1/data-factory-copy-activity-performance.md) för mer information om hur du kopierar med Data Factory. 

### <a name="adlcopy"></a>AdlCopy

AdlCopy är en Windows-kommandoradsverktyg som gör det möjligt att kopiera data mellan två datasjölagerkonton i samma region. Verktyget AdlCopy tillhandahåller ett fristående eller för att köra din kopieringsjobbet med ett Azure Data Lake Analytics-konto. Även om det ursprungligen har skapats för kopior på begäran till skillnad från en robust replikering, ger ett annat alternativ att göra distribuerade kopiera över Data Lake Store-konton inom samma region. För tillförlitlighet, har vi rekommenderar för att använda alternativet premium-Data Lake Analytics för produktion arbetsbelastningar. Den fristående versionen kan returnera upptagen svar och har begränsad skala och övervakning. 

Som Distcp måste AdlCopy vara styrd av något som Azure Automation eller Schemaläggaren i Windows. Som Data Factory AdlCopy stöder inte kopiera uppdaterade filer, men kopierar, och skriver över befintliga filer. Mer information och exempel på användning av AdlCopy finns [kopiera data från Azure Storage-Blobbar till Data Lake Store](data-lake-store-copy-data-azure-storage-blob.md).

## <a name="monitoring-considerations"></a>Överväganden för övervakning 

Data Lake Store ger detaljerad diagnostiska loggar och granskning. Data Lake Store ger vissa grundläggande mått i Azure portal under Data Lake Store-konto och i Azure-Monitor. Tillgängligheten för Data Lake Store visas i Azure-portalen. Det här måttet uppdateras var sjunde: e minut och kan inte frågas via ett offentligt exponerade API. För att få den senaste tillgängligheten för ett Data Lake Store-konto, måste du köra dina egna syntetiska tester för att verifiera tillgänglighet. Andra mått som totalt lagringsanvändningen, läsning och skrivning begäranden och ingång-/ utgång kan ta upp till 24 timmar att uppdatera. Så, måste flera uppdaterade mått beräknas manuellt via Hadoop-kommandoradsverktyg eller aggregering logginformation. Det snabbaste sättet att hämta den senaste lagringsanvändningen körs det här HDFS-kommandot från en nod i Hadoop (till exempel huvudnod):   

    hdfs dfs -du -s -h adl://<adls_account_name>.azuredatalakestore.net:443/

### <a name="export-data-lake-store-diagnostics"></a>Exportera Data Lake Store-diagnostik 

En av de snabbaste sätten att få åtkomst till sökbara loggar från Data Lake Store är att låta loggöverföring till **logganalys** under den **diagnostik** bladet för Data Lake Store-konto. Detta ger omedelbar åtkomst till inkommande loggar tid och innehåll filter tillsammans med aviseringar alternativ (e-post/webhook) som utlösts inom 15 minuters mellanrum. Instruktioner finns i [åtkomst till diagnostikloggarna för Azure Data Lake Store](data-lake-store-diagnostic-logs.md). 

Realtid mer och mer kontroll över var ska hamna i loggarna, bör du exporterar loggar till Azure EventHub där innehåll kan analyseras individuellt eller via ett tidsfönster för att skicka meddelanden i realtid till en kö. Ett separat program som en [Logikapp](../connectors/connectors-create-api-azure-event-hubs.md) kan sedan använda och kommunicera aviseringar till rätt kanal, samt skicka med övervakningsverktyg som NewRelic, Datadog eller AppDynamics. Om du använder ett verktyg från tredje part, till exempel ElasticSearch kan du också exportera loggarna till Blob Storage och använda den [plugin-program för Azure Logstash](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob) att nyttja data i Elasticsearch och Kibana Logstash (ELK)-stacken.

### <a name="turn-on-debug-level-logging-in-hdinsight"></a>Aktivera felsökning på objektnivå loggning i HDInsight 

Om Data Lake Store loggöverföring inte är påslagen, Azure HDInsight innehåller också ett sätt att aktivera [klientsidan loggning för Data Lake Store](data-lake-store-performance-tuning-mapreduce.md) via log4j. Du måste ange egenskapen följande i **Ambari** > **YARN** > **Config** > **avancerade yarn log4j konfigurationer**: 

    log4j.logger.com.microsoft.azure.datalake.store=DEBUG 

När egenskapen har angetts och noderna har startats om Data Lake Store diagnostik skrivs till YARN-loggar på noderna (/tmp/<user>/yarn.log), och viktig information som fel eller begränsning (felkod HTTP 429) kan övervakas. Samma information kan också övervakas i logganalys eller var loggar levereras till i den [diagnostik](data-lake-store-diagnostic-logs.md) bladet för Data Lake Store-konto. Det rekommenderas minst ha klientsidan loggning aktiverad eller använda alternativet med Data Lake Store för operativa synlighet och enklare felsökning av loggöverföring.

### <a name="run-synthetic-transactions"></a>Kör syntetiska transaktioner 

Den service måtten för tillgänglighet för Data Lake Store i Azure-portalen har för närvarande 7 minut uppdatera fönster. Dessutom kan efterfrågas med hjälp av ett offentligt exponerade API. Därför bör du skapar en grundläggande program som har syntetiska transaktioner till Data Lake Store kan ange upp till minut tillgängligheten. Ett exempel kan skapa ett Webbjobb, Logikapp eller Azure Funktionsapp om du vill utföra en Läs, skapa, uppdatera mot Data Lake Store och skicka resultaten till din lösning för övervakning. Åtgärder kan göras i en tillfällig mapp och sedan tas bort efter test som kan köras var 30 till 60 sekunder, beroende på krav.

## <a name="directory-layout-considerations"></a>Överväganden för Directory-layout

När hamnar data i en datasjö, är det viktigt att planera inför strukturen för data så att säkerhet, partitionering och bearbetning kan vara användas effektivt. Många av följande rekommendationer kan användas om det är med Azure Data Lake Store, Blob-lagring eller HDFS. Varje arbetsbelastning har olika krav på hur informationen används, men nedan visas några vanliga layouter att tänka på när du arbetar med IoT och batch-scenarier.

### <a name="iot-structure"></a>IoT-struktur 

I IoT-arbetsbelastningar, kan det finnas en stor mängd data som landat i datalagret som sträcker sig över flera produkter, enheter, organisationer och kunder. Det är viktigt att planera inför directory layouten för organisationen, säkerhet och effektiv bearbetning av data för konsumenter av dataströmmen. En allmän mall att överväga kanske följande layout: 

    {Region}/{SubjectMatter(s)}/{yyyy}/{mm}/{dd}/{hh}/ 

Hamnar telemetri för ett flygplan motorn i Storbritannien kan till exempel se ut följande struktur: 

    UK/Planes/BA1293/Engine1/2017/08/11/12/ 

Det finns ett viktigt skäl att placera datum i slutet av mappstrukturen. Om du vill låsa vissa regioner eller ämne frågor till användare eller grupper kan du göra det enkelt med POSIX-behörighet. I annat fall om det är viktigt att begränsa vissa säkerhetsgrupp till visar bara de Storbritannien data eller vissa plan, skulle med struktur datum fram en separat behörighet krävas för ett stort antal mappar under varje timme. Dessutom skulle har strukturen datum fram exponentiellt öka antalet mappar som tid har gått.

### <a name="batch-jobs-structure"></a>Struktur för batch-jobb 

Från en hög nivå är en metod som används ofta i batch-bearbetning ska hamna data i en mapp ”i”. Sedan, när data har bearbetats kan försätta nya data i en ”out”-mapp för underordnade processer som ska använda. Den här katalogstrukturen visas ibland för jobb som kräver bearbetning på enskilda filer och kan inte kräva massivt parallell bearbetning över stora datauppsättningar. Precis som IoT-struktur som rekommenderas ovan, har en bra katalogstruktur överordnad mappar för till exempel region och ämne frågor (till exempel organisation, produkt/producenten). Den här strukturen kan hjälpa dig med att skydda data i din organisation och en bättre hantering av data i dina arbetsbelastningar. Dessutom bör du datum och tid i strukturen för att tillåta bättre organisation, filtrerade sökningar, säkerhet och automatisering i bearbetningen. Önskad nivå för datum strukturen bestäms av intervallet då överförs data eller bearbetas, till exempel varje timme, varje dag eller även varje månad. 

Ibland filen bearbetning är misslyckas på grund av skadade data eller oväntat format. I sådana fall katalogstruktur kan dra nytta av en **/dåliga** mapp att flytta filerna till för mer kontroll. Batch-jobbet kan också hantera rapportering eller meddelanden om dessa *felaktig* filer manuellt. Överväg följande mallstruktur: 

    {Region}/{SubjectMatter(s)}/In/{yyyy}/{mm}/{dd}/{hh}/ 
    {Region}/{SubjectMatter(s)}/Out/{yyyy}/{mm}/{dd}/{hh}/ 
    {Region}/{SubjectMatter(s)}/Bad/{yyyy}/{mm}/{dd}/{hh}/ 

Marknadsföring företag får exempelvis dagliga data utdrag ur customer uppdateringar från sina kunder i Nordamerika. Den kan se ut som följande kodavsnitt före och efter att ha behandlats: 

    NA/Extracts/ACMEPaperCo/In/2017/08/14/updates_08142017.csv 
    NA/Extracts/ACMEPaperCo/Out/2017/08/14/processed_updates_08142017.csv 
 
I vanliga fall batch data bearbetas direkt i databaser som Hive eller traditionella SQL-databaser kan inte behovet av en **/in** eller **/out** mappen eftersom utdata redan försätts i ett Avgränsa mapp för Hive-tabell eller extern databas. Till exempel dagliga utdrag ur kunder skulle hamna i sina respektive mappar och orchestration av ungefär Azure Data Factory Apache Oozie eller Apache luftflödet initierar en Hive- eller Spark dagligen för att bearbeta och skriva data till en Hive-tabell.

## <a name="next-steps"></a>Nästa steg     

* [Översikt över Azure Data Lake Store](data-lake-store-overview.md) 
* [Åtkomstkontroll i Azure Data Lake Store](data-lake-store-access-control.md) 
* [Säkerhet i Azure Data Lake Store](data-lake-store-security-overview.md)
* [Justera Azure Data Lake Store för prestanda](data-lake-store-performance-tuning-guidance.md)
* [Prestandajustering riktlinjer för att använda HDInsight Spark med Azure Data Lake Store](data-lake-store-performance-tuning-spark.md)
* [Prestandajustering riktlinjer för att använda HDInsight Hive med Azure Data Lake Store](data-lake-store-performance-tuning-hive.md)
* [Data Orchestration med hjälp av Azure Data Factory för Azure Data Lake Store](https://mix.office.com/watch/1oa7le7t2u4ka)
* [Skapa HDInsight-kluster med Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md) 
