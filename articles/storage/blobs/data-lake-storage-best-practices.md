---
title: Bästa praxis för att använda Azure Data Lake Storage Gen2 | Microsoft Docs
description: Läs metodtipsen om datainmatning, datum säkerhet och prestanda för med hjälp av Azure Data Lake Storage Gen2 (kallades tidigare Azure Data Lake Store)
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: article
ms.date: 12/06/2018
ms.author: normesta
ms.reviewer: sachins
ms.openlocfilehash: 8b39866b990812913924118c564a5e93f898b1cb
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64939472"
---
# <a name="best-practices-for-using-azure-data-lake-storage-gen2"></a>Metodtips för Azure Data Lake Storage Gen2

I den här artikeln får du lära dig om bästa praxis och överväganden för att arbeta med Azure Data Lake Storage Gen2. Den här artikeln innehåller information om säkerhet, prestanda, flexibilitet och övervakning för Data Lake Storage Gen2. Arbeta med verkligen big data i tjänster som Azure HDInsight var krånglig innan Data Lake Storage Gen2. Var du tvungen att fragmentera data över flera Blob storage-konton så att petabyte lagringsutrymme och den optimala prestanda kan uppnås. De flesta av gränserna för storlek och prestanda med Data Lake Storage Gen2 tas bort. Men finns det fortfarande ett par saker som den här artikeln beskriver så att du kan få bästa prestanda med Data Lake Storage Gen2.

## <a name="security-considerations"></a>Säkerhetsöverväganden

Azure Data Lake Storage Gen2 erbjuder POSIX åtkomstkontroller för användare, grupper och tjänstens huvudnamn för Azure Active Directory (AD Azure). Dessa åtkomstkontroller kan ställas in att befintliga filer och kataloger. Åtkomstkontroller kan också användas för att skapa standardbehörigheterna som kan tillämpas automatiskt på nya filer eller kataloger. Mer information om Data Lake Storage Gen2 ACL: er finns på [åtkomstkontroll i Azure Data Lake Storage Gen2](storage-data-lake-storage-access-control.md).

### <a name="use-security-groups-versus-individual-users"></a>Använda säkerhetsgrupper jämfört med enskilda användare

WWhen arbeta med stordata i Data Lake Storage Gen2, är det troligt att ett huvudnamn för tjänsten används för att ge tjänster, till exempel Azure HDInsight för att arbeta med data. Det kan dock finnas fall där enskilda användare behöver åtkomst till data samt. I samtliga fall starkt överväga att använda Azure Active Directory [säkerhetsgrupper](../common/storage-auth-aad.md) i stället för att tilldela enskilda användare till kataloger och filer.

När en säkerhetsgrupp tilldelas behörigheter att lägga till eller ta bort användare i gruppen kräver inte några uppdateringar till Data Lake Storage Gen2. Detta kan också se till att du inte överskrider det maximala antalet åtkomstkontrollposter per åtkomstkontrollistan (ACL). Det numret är för närvarande 32, (inklusive fyra POSIX-typ ACL: er som alltid är associerade med alla filer och kataloger): den ägande användaren, ägande grupp, masken och andra. Varje katalog kan ha två typer av ACL, access ACL och standard ACL för totalt 64 åtkomstkontrollposter. Mer information om dessa ACL: er finns i [åtkomstkontroll i Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="security-for-groups"></a>Säkerhet för grupper

När du eller dina användare behöver åtkomst till data i ett lagringskonto med hierarkiskt namnområde aktiverad, är det bäst att använda Azure Active Directory-säkerhetsgrupper. Några av de rekommenderade grupper att börja med kan vara **ReadOnlyUsers**, **WriteAccessUsers**, och **FullAccessUsers** för roten för filsystemet, och även avgränsa sådana för viktiga underkataloger. Om det inte finns någon annan kan förväntade grupper av användare som kan läggas till senare, men inte har identifierats men eventuellt skapa dummy-säkerhetsgrupper som har åtkomst till vissa mappar. Användning av säkerhetsgrupp garanterar att du kan undvika länge bearbetningstid när du tilldelar nya behörigheter till filer.

### <a name="security-for-service-principals"></a>Säkerhet för tjänstens huvudnamn

Azure Active Directory-tjänstobjekt som vanligtvis används av tjänster som Azure Databricks för att komma åt data i Data Lake Storage Gen2. En enda Azure Active Directory-tjänstens huvudnamn kan vara lämplig för många kunder och den kan ha fullständig behörighet i roten för Data Lake Storage Gen2 filsystemet. Andra kunder kan kräva flera kluster med olika tjänsthuvudnamn där ett kluster har fullständig åtkomst till data och ett annat kluster med bara läsbehörighet. 

### <a name="enable-the-data-lake-storage-gen2-firewall-with-azure-service-access"></a>Aktivera Data Lake Storage Gen2-brandväggen med Azure-tjänståtkomst

Data Lake Storage Gen2 har stöd för aktivering av en brandvägg och begränsa åtkomst till Azure-tjänster, vilket rekommenderas för att begränsa vektor av externa attacker. Brandväggen kan vara aktiverad på ett lagringskonto i Azure-portalen via den **brandväggen** > **aktivera brandvägg (på)** > **Tillåt åtkomst till Azure-tjänster** alternativ.

Lägga till Azure Databricks-kluster till ett virtuellt nätverk som kan beviljas åtkomst till via brandväggen lagring kräver användning av en funktion i förhandsversionen av Databricks. Om du vill aktivera den här funktionen, placerar du en supportförfrågan.

## <a name="resiliency-considerations"></a>Att tänka på om återhämtning

När utforma ett system med Data Lake Storage Gen2 eller valfri molntjänst, måste du överväga att kraven på tillgänglighet och hur du svarar på eventuella avbrott i tjänsten. Ett problem kan lokaliseras till den specifika instansen eller även regionomfattande, så att ha en plan för både är viktigt. Beroende på återställningstiden och återställningen mål serviceavtal för din arbetsbelastning för återställningspunkt kan du välja en mer eller mindre aggressiv strategi för hög tillgänglighet och haveriberedskap.

### <a name="high-availability-and-disaster-recovery"></a>Hög tillgänglighet och haveriberedskap

Hög tillgänglighet och katastrofåterställning (DR) kan ibland kombineras tillsammans, även om var och en har en något annorlunda strategi, särskilt när det gäller data. Data Lake Storage Gen2 hanterar redan 3 x-replikering för att skydda mot lokaliserade maskinvarufel. Dessutom kan andra replikeringsalternativ för, till exempel ZRS förbättra hög tillgänglighet när GRS och RA-GRS förbättra DR. När du skapar en plan för hög tillgänglighet i händelse av ett tjänstavbrott behöver arbetsbelastningen åtkomst till den senaste informationen så snabbt som möjligt genom att växla till en separat replikerade instansen lokalt eller i en ny region.

I en DR-strategi för att förbereda för osannolika ett oåterkalleligt fel på en region, är det också viktigt att du har data som replikeras till en annan region med GRS eller RA-GRS-replikering. Du måste också beakta dina krav på gränsfall, till exempel skadade data där du kanske vill skapa regelbundna ögonblicksbilder till. Beroende på prioritet och storleken på data kan du överväga att löpande delta ögonblicksbilder av 1 – 6- och 24 timmar perioder, enligt risk toleranser.

För dataåterhämtning med Data Lake Storage Gen2 rekommenderar vi att du geo-replikera dina data via GRS eller RA-GRS som uppfyller dina krav med hr/DR. Dessutom bör du överväga att sätt för programmet med Data Lake Storage Gen2 att automatiskt växla över till den sekundära regionen via övervakning utlösare eller längden på misslyckade försök eller minst skicka ett meddelande till administratörer för manuella åtgärder. Tänk på att det finns en kompromiss vid fel och väntar på att en tjänst ska komma online igen.

### <a name="use-distcp-for-data-movement-between-two-locations"></a>Använd Distcp för dataförflyttning mellan två platser

Kort för distribuerad kopia DistCp är ett kommandoradsverktyg för Linux med Hadoop och ger distribuerade dataförflyttning mellan två platser. De två platserna kan vara Data Lake Storage Gen2, HDFS- eller S3. Det här verktyget använder MapReduce-jobb på ett Hadoop-kluster (till exempel HDInsight) för att skala ut på alla noder. Distcp anses vara det snabbaste sättet att flytta big data utan komprimering för särskilda nätverksenheter. Distcp innehåller också ett alternativ för att endast uppdatera deltan mellan två platser, hanterar automatiska omförsök, samt dynamisk skalning av beräkning. Den här metoden är mycket effektiva när det gäller att replikera saker som Hive/Spark-tabeller som kan ha många stora filer i en enskild katalog och du bara vill kopiera över ändrade data. Därmed behöver är Distcp det mest rekommenderade verktyget för att kopiera data mellan datalager för stordata.

Kopiera jobb kan utlösas av Apache Oozie-arbetsflöden med frekvensen eller utlösare för data samt Linux cron-jobb. För intensiva replikeringsjobb rekommenderar vi att du skapa ett separat HDInsight Hadoop-kluster som kan justerade och skalas specifikt för jobb som kopia. Detta säkerställer att kopiera jobb inte störa kritiska jobb. Om du kör replikering på en liten frekvens, kan även klustret tas mellan varje jobb. Om redundansväxla till sekundär region, se till att ett annat kluster också startas i den sekundära regionen att replikera nya data tillbaka till det primära Gen2 för Data Lake Storage-kontot när den visas. Exempel på hur du använder Distcp finns [Använd Distcp för att kopiera data mellan Azure Storage-Blobbar och Data Lake Storage Gen2](../blobs/data-lake-storage-use-distcp.md).

### <a name="use-azure-data-factory-to-schedule-copy-jobs"></a>Använd Azure Data Factory för att schemalägga jobb för kopiering

Azure Data Factory kan också användas för att schemalägga kopia jobb med en Kopieringsaktivitet och kan även ställas in på en frekvens via guiden Kopiera. Tänk på att Azure Data Factory har en gräns på enheter för molndataflytt (DMUs) och slutligen caps dataflöde/beräkning för stora arbetsbelastningar. Dessutom erbjuder Azure Data Factory för närvarande inte delta uppdateringar mellan Data Lake Storage Gen2 konton, så kataloger som Hive-tabeller kräver en fullständig kopia att replikera. Referera till den [data factory artikeln](../../data-factory/load-azure-data-lake-storage-gen2.md) för mer information om hur du kopierar med Data Factory.

## <a name="monitoring-considerations"></a>Överväganden för övervakning

Data Lake Storage Gen2 innehåller mått i Azure-portalen under kontot Data Lake Storage Gen2 och i Azure Monitor. Tillgängligheten för Data Lake Storage Gen2 visas i Azure-portalen. För att få den senaste tillgängligheten för ett Data Lake Storage Gen2-konto, måste du köra dina egna syntetiska tester för att verifiera tillgänglighet. Andra mått, till exempel totalt Lagringsutnyttjande Läs/Skriv-begäranden och ingående/utgående trafik är tillgängliga som ska användas genom att övervaka program och kan även utlösa aviseringar när tröskelvärden (till exempel genomsnittlig svarstid eller antal fel per minut) har överskridits.

## <a name="directory-layout-considerations"></a>Överväganden för Directory-layout

När du hanterar data i en datasjö, är det viktigt att planera inför strukturen för data så att säkerhet, partitionering och bearbetning kan vara används effektivt. Många av följande rekommendationer kan användas för alla arbetsbelastningar med stordata. Varje arbetsbelastning har olika krav på hur data används, men här följer några vanliga layouter att tänka på när du arbetar med IoT och batch-scenarier.

### <a name="iot-structure"></a>IoT-struktur

I IoT-arbetsbelastningar, kan det finnas en stor mängd data som nu visas det datalager som sträcker sig över flera produkter, enheter, organisationer och kunder. Det är viktigt i förväg directory layouten för organisationen, säkerhet och effektiv bearbetning av data för ned-ström konsumenter. En allmän mall att tänka på kanske följande layout:

    {Region}/{SubjectMatter(s)}/{yyyy}/{mm}/{dd}/{hh}/

Landing telemetri för en motor med flygplan i Storbritannien kan till exempel se ut som följande struktur:

    UK/Planes/BA1293/Engine1/2017/08/11/12/

Det finns en viktig anledning att placera datum i slutet av katalogstrukturen. Om du vill låsa vissa geografiska områden eller ett ämne som är viktigt att användare/grupper kan du göra det enkelt med POSIX-behörigheter. I annat fall om det är viktigt att begränsa en viss säkerhetsgrupp till visar bara data Storbritannien eller vissa plan, skulle med datum struktur framför en separat behörighet krävas för flera kataloger under varje timme-katalog. Dessutom skulle har datum strukturen framför exponentiellt öka antalet kataloger som tid som gått.

### <a name="batch-jobs-structure"></a>Struktur för batch-jobb

Från en hög nivå är en metod som används ofta i batch-bearbetning hamnar data i en ”in”-katalog. Sedan, när data har bearbetats kan placera nya data i en ”ut”-katalog för underordnade processer som ska använda. Den här katalogstrukturen är används ibland för jobb som kräver bearbetning på enskilda filer och kanske inte kräver massiv parallellbearbetning över stora datauppsättningar. T.ex. IoT-struktur som rekommenderas ovan, har en bra katalogstruktur överordnad kataloger för sådant som region och ämne frågor (till exempel organisation, produkt/producent). Den här strukturen hjälper med att skydda data i din organisation och bättre hantering av data i dina arbetsbelastningar. Dessutom, beakta datum och tid i strukturen för att ge bättre organisation, filtrerad sökningar, säkerhet och automatisering i bearbetningen. Granularitetsnivån för datum-strukturen bestäms av det tidsintervall som data laddas upp till eller bearbetas, till exempel varje timme, varje dag, men även varje månad.

Ibland filen bearbetning är misslyckades på grund av skadade data eller oväntat format. I sådana fall kan katalogstruktur kan dra nytta av en **/felaktiga** mapp att flytta filerna till för mer kontroll. Batch-jobbet kan också hantera rapportering eller meddelanden om dessa *felaktig* filer för manuella åtgärder. Överväg följande mallstruktur:

    {Region}/{SubjectMatter(s)}/In/{yyyy}/{mm}/{dd}/{hh}/
    {Region}/{SubjectMatter(s)}/Out/{yyyy}/{mm}/{dd}/{hh}/
    {Region}/{SubjectMatter(s)}/Bad/{yyyy}/{mm}/{dd}/{hh}/

Till exempel får ett marknadsföring företag dagliga data extrakt av kunden uppdateringar från sina kunder i Nordamerika. Det kan se ut som följande fragment före och efter att ha behandlats:

    NA/Extracts/ACMEPaperCo/In/2017/08/14/updates_08142017.csv
    NA/Extracts/ACMEPaperCo/Out/2017/08/14/processed_updates_08142017.csv

I vanliga fall av batchdata som bearbetas direkt till databaser, till exempel Hive eller traditionella SQL-databaser, det inte finns behov av en **/in** eller **/ut** mappen eftersom utdata redan hamnar i en Avgränsa mapp för Hive-tabell eller extern databas. Till exempel dagliga utdrag ur kunder skulle hamna i sina respektive mappar och samordning med något som liknar Azure Data Factory, Apache Oozie eller Apache luftflödet ska utlösa en daglig Hive eller Spark-jobb för att bearbeta och skriva data till en Hive-tabell.
