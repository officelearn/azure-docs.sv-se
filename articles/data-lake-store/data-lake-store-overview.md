---
title: Översikt över Azure Data Lake Store | Microsoft Docs
description: Förstå Azure Data Lake Store och dess värde över andra dataarkiv
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: paulettm
editor: cgronlun

ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/02/2016
ms.author: nitinme

---
# Översikt över Azure Data Lake Store
Azure Data Lake Store är en företagsomfattande storskalig lagringsplats för analytiska arbetsbelastningar för stordata. Med Azure Data Lake kan du samla in data av olika storlekar, former, typer och inmatningshastighet på en enda plats för drifts- och undersökande analyser.

> [!TIP]
> Använd [Data Lake Stores utbildningsväg](https://azure.microsoft.com/documentation/learning-paths/data-lake-store-self-guided-training/) för att börja utforska Azure Data Lake Store-tjänsten.
> 
> 

Azure Data Lake Store kan nås från Hadoop (tillgänglig med HDInsight-kluster) med hjälp av WebHDFS-kompatibla REST-API:er. Den är speciellt utformad för att aktivera analyser på lagrade data och är inställd för prestanda för dataanalyser. Och den innehåller alla funktioner i företagsklass direkt ur lådan – säkerhet, hanterbarhet, skalbarhet, tillförlitlighet och tillgänglighet – grundläggande för verkliga företagsfall.

![Azure Data Lake](./media/data-lake-store-overview/data-lake-store-concept.png)

Följande är några av de viktigaste funktionerna i Azure Data Lake.

### Byggt för Hadoop
Azure Data Lake store är ett Apache Hadoop-filsystem som är kompatibelt med Hadoop Distributed File System (HDFS) och fungerar med Hadoop-ekosystemet.  Dina befintliga HDInsight-program eller -tjänster som använder WebHDFS API kan enkelt integreras med Data Lake Store. Data Lake Store har också ett WebHDFS-kompatibelt REST-gränssnitt för program

Data som lagras i Data Lake Store kan enkelt analyseras med hjälp av analytiska Hadoop-ramar, till exempel MapReduce eller Hive. Microsoft Azure HDInsight-kluster kan etableras och konfigurerats för direkt åtkomst till data som lagras i Data Lake Store.

### Obegränsad lagring, petabytefiler
Azure Data Lake Store ger obegränsad lagring och lämpar sig för att lagra en mängd olika data för analyser. Det medför inte några gränser för kontostorlekar, filstorlekar eller mängden data som kan lagras i en datasjö. Enskilda filer kan variera mellan kilobyte till petabyte i storlek vilket gör det till ett bra alternativ för lagring av alla typer av data. Data lagras varaktigt genom att göra flera kopior och det finns ingen gräns på vilken tidsperiod som data kan lagras i datasjön.

### Prestandajusterad för analyser av stordata
Azure Data Lake Store är utformat för att köra storskaliga analytiska system som kräver omfattande genomströmning till fråga och analys av stora mängder data. Datasjön sprider delar av en fil i ett antal enskilda lagringsservrar. Detta förbättrar läsgenomströmning vid läsning av filen parallellt för att utföra dataanalyser.

### Företagsredo: Hög tillgänglighet och säkert
Azure Data Lake Store ger branschstandard för tillgänglighet och pålitlighet. Dina datatillgångar lagras varaktigt genom att göra redundanta kopior som skyddar mot oväntade fel. Företag kan använda Azure Data Lake i sina lösningar som en viktig del av deras befintliga dataplattform.

Data Lake Store innehåller också säkerhet i företagsklass för lagrade data. Mer information finns i [Skydda data i Azure Data Lake Store](#DataLakeStoreSecurity).

### Alla data
Azure Data Lake Store kan lagra data i deras ursprungliga format, så som de är, utan några tidigare transformationer. Data Lake Store kräver inte att något schema ska definieras innan data läses in, vilket lämnar det upp till det enskilda analytiska ramverket att tolka data och definiera ett schema vid analysens tidpunkt. Att kunna lagra filer av godtycklig storlek och format gör det möjligt för Data Lake Store att hantera strukturerade, delvis strukturerade och ostrukturerade data.

Azure Data Lake Store-behållare för data är i stort sett mappar och filer. Du använder lagrade data med hjälp av Azure Powershell, SDK:er och Azure Portal. Så länge du placerar dina data i lagret med hjälp av dessa gränssnitt och använder lämpliga behållare kan du lagra alla typer av data. Data Lake Store utför inte någon särskild hantering av data baserat på vilken typ av data som lagras.

## <a name="DataLakeStoreSecurity"></a>Skydda data i Azure Data Lake Store
Azure Data Lake Store använder Azure Active Directory för autentisering och åtkomstkontrollistor (ACL) för att hantera åtkomst till dina data.

| Funktion | Beskrivning |
| --- | --- |
| Autentisering |Azure Data Lake Store integreras med Azure Active Directory (AAD) för identitets- och åtkomsthantering för alla data som lagras i Azure Data Lake Store. Med integrationen drar Azure Data Lake fördelar från alla AAD-funktioner, inklusive multifaktorautentisering, villkorlig åtkomst, rollbaserad åtkomstkontroll, övervakning av programanvändning, säkerhetsövervakning och -avisering, osv. Azure Data Lake Store stöder OAuth 2.0-protokollet för autentisering i REST-gränssnittet. |
| Åtkomstkontroll |Azure Data Lake Store ger åtkomstkontroll genom att stödja behörigheter av POSIX-typ som exponeras av WebHDFS-protokollet. I den aktuella versionen kan ACL: er aktiveras i rotmappen, undermappar och enskilda filer. De ACL: er som du använder till rotmappen kommer också att gälla för alla underordnade mappar/filer. |

Mer information om hur du skyddar data i Data Lake Store: Följ länkarna nedan.

* Anvisningar för hur du skyddar data i Data Lake Store finns i [Skydda data i Azure Data Lake Store](data-lake-store-secure-data.md).
* Föredrar du video? [Titta på det här videoklippet](https://mix.office.com/watch/1q2mgzh9nn5lx) om hur du skyddar data som lagras i Data Lake Store.

## Program som är kompatibla med Azure Data Lake Store
Azure Data Lake Store är kompatibelt med de flesta öppna källkomponenter i Hadoop-ekosystemet. Det är även snyggt integrerat med andra Azure-tjänster. På så sätt blir Data Lake Store ett perfekt alternativ för dina datalagringsbehov. Följ länkarna nedan för att lära dig mer om hur Data Lake Store kan användas både med öppna källkodskomponenter samt andra Azure-tjänster.

* Se [Program och tjänster som är kompatibla med Azure Data Lake Store](data-lake-store-compatible-oss-other-applications.md) för en lista med öppna källkodsprogram som är kompatibla med Data Lake Store.
* Se [Integrering med andra Azure-tjänster](data-lake-store-integrate-with-other-services.md) för att förstå hur Data Lake Store kan användas med andra Azure-tjänster för att aktivera ett bredare spektrum av scenarier.
* Se [Scenarier för användning av Data Lake Store](data-lake-store-data-scenarios.md) för att få information om hur du använder Data Lake Store i scenarier såsom att mata in data, bearbeta data, hämta data och visualisera data.

## Vad är Azure Data Lake Store-filsystem (adl://)?
Data Lake Store kan nås via det nya filsystemet AzureDataLakeFilesystem (adl://) i Hadoop-miljöer (tillgängligt med HDInsight-kluster). Program och tjänster som använder adl:// kan dra nytta av ytterligare prestandaoptimering som inte är tillgänglig i WebHDFS. Därför ger Data Lake Store dig möjlighet att utnyttja antingen bästa prestanda med det rekommenderade alternativet för att använda adl:// eller hantera befintlig kod genom att fortsätta att använda WebHDFS API direkt. Azure HDInsight utnyttjar till fullo AzureDataLakeFilesystem för att tillhandahålla bästa prestanda på Data Lake Store.

Du kan komma åt dina data i Data Lake Store med hjälp av `adl://<data_lake_store_name>.azuredatalakestore.net`. Mer information om hur du kan komma åt data i Data Lake Store finns i [Visa egenskaper för lagrade data](data-lake-store-get-started-portal.md#properties)

## Hur börjar jag använda Azure Data Lake Store?
Se [Kom igång med Data Lake Store med hjälp av Azure Portal](data-lake-store-get-started-portal.md) för information om hur du etablerar ett Data Lake Store med hjälp av Azure Portal. När du har etablerat Azure Data Lake kan du lära dig hur du använder erbjudanden för stordata, till exempel Azure Data Lake Analytics eller Azure HDInsight med Data Lake Store. Du kan också skapa ett .NET-program för att skapa ett Azure Data Lake Store-konto och utföra åtgärder, till exempel ladda upp eller hämta data osv.

* [Kom igång med Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Använd Azure HDInsight med Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Kom igång med Azure Data Lake Store med hjälp av .NET SDK](data-lake-store-get-started-net-sdk.md)

## Data Lake Store-videor
Om du vill titta på videor för att lära dig, har Data Lake Store videor om en mängd funktioner.

* [Skapa ett Azure Data Lake Store-konto](https://mix.office.com/watch/1k1cycy4l4gen)
* [Använd Data Explorer för att hantera data i Azure Data Lake Store](https://mix.office.com/watch/icletrxrh6pc)
* [Anslut Azure Data Lake Analytics till Azure Data Lake Store](https://mix.office.com/watch/qwji0dc9rx9k)
* [Åtkomst till Azure Data Lake Store via Data Lake Analytics](https://mix.office.com/watch/1n0s45up381a8)
* [Anslut Azure HDInsight till Azure Data Lake Store](https://mix.office.com/watch/l93xri2yhtp2)
* [Åtkomst till Azure Data Lake Store via Hive och Pig](https://mix.office.com/watch/1n9g5w0fiqv1q)
* [Använd DistCp (Hadoop-distribuerad kopia) för att kopiera data till och från Azure Data Lake Store](https://mix.office.com/watch/1liuojvdx6sie)
* [Använd Apache Sqoop för att flytta data mellan relationella källor och Azure Data Lake Store](https://mix.office.com/watch/1butcdjxmu114)
* [Data Orchestration med hjälp av Azure Data Factory för Azure Data Lake Store](https://mix.office.com/watch/1oa7le7t2u4ka)
* [Skydda data i Azure Data Lake Store](https://mix.office.com/watch/1q2mgzh9nn5lx)

<!--HONumber=sep16_HO1-->


