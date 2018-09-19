---
title: Översikt över Azure Data Lake Storage Gen1 | Microsoft Docs
description: Förstå vilka Data Lake Storage Gen1 är (tidigare kallat Azure Data Lake Store) och dess värde över andra dataarkiv
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: nitinme
ms.openlocfilehash: b733a0bec20c7abae5df41acd74284bdf75bca2c
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2018
ms.locfileid: "46124141"
---
# <a name="overview-of-azure-data-lake-storage-gen1"></a>Översikt över Azure Data Lake Storage Gen1

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Azure Data Lake Storage Gen1 är en företagsomfattande storskalig lagringsplats för analytiska arbetsbelastningar för stordata. Med Azure Data Lake kan du samla in data av olika storlekar, former, typer och inmatningshastighet på en enda plats för drifts- och undersökande analyser.

> [!TIP]
> Använd den [utbildningsvägen för Data Lake Storage Gen1](https://azure.microsoft.com/documentation/learning-paths/data-lake-store-self-guided-training/) att börja utforska Data Lake Storage Gen1-tjänsten.
> 
> 

Data Lake Storage Gen1 kan nås från Hadoop (tillgängligt med HDInsight-kluster) med hjälp av WebHDFS-kompatibla REST API: er. Den är speciellt utformad för att aktivera analyser på lagrade data och är inställd för prestanda för dataanalyser. Och den innehåller alla funktioner i företagsklass direkt ur lådan – säkerhet, hanterbarhet, skalbarhet, tillförlitlighet och tillgänglighet – grundläggande för verkliga företagsfall.

![Azure Data Lake](./media/data-lake-store-overview/data-lake-store-concept.png)

Några av de viktigaste funktionerna i Data Lake Storage Gen1 är följande.

### <a name="built-for-hadoop"></a>Byggt för Hadoop
Data Lake Storage Gen1 är en Apache Hadoop-filsystem som kompatibelt med Hadoop Distributed File System (HDFS) och fungerar med Hadoop-ekosystemet.  Dina befintliga HDInsight-program eller tjänster som använder WebHDFS API kan enkelt integreras med Data Lake Storage Gen1. Data Lake Storage Gen1 visar också ett WebHDFS-kompatibelt REST-gränssnitt för program

Data som lagras i Data Lake Storage Gen1 kan enkelt analyseras med hjälp av analytiska Hadoop-ramar, till exempel MapReduce eller Hive. Microsoft Azure HDInsight-kluster kan etableras och konfigurerats för direkt åtkomst till data som lagras i Data Lake Storage Gen1.

### <a name="unlimited-storage-petabyte-files"></a>Obegränsad lagring, petabytefiler
Data Lake Storage Gen1 ger obegränsad lagring och lämpar sig för att lagra en mängd olika data för analys. Det medför inte några gränser för kontostorlekar, filstorlekar eller mängden data som kan lagras i en datasjö. Enskilda filer kan variera mellan kilobyte till petabyte i storlek vilket gör det till ett bra alternativ för lagring av alla typer av data. Data lagras varaktigt genom att göra flera kopior och det finns ingen gräns på vilken tidsperiod som data kan lagras i datasjön.

### <a name="performance-tuned-for-big-data-analytics"></a>Prestandajusterad för analyser av stordata
Data Lake Storage Gen1 har utformats för att köra storskaliga analytiska system som kräver enormt dataflöde för att fråga och analysera stora mängder data. Datasjön sprider delar av en fil i ett antal enskilda lagringsservrar. Detta förbättrar läsgenomströmning vid läsning av filen parallellt för att utföra dataanalyser.

### <a name="enterprise-ready-highly-available-and-secure"></a>Företagsredo: Hög tillgänglighet och säkert
Data Lake Storage Gen1 ger branschstandard tillgänglighet och tillförlitlighet. Dina datatillgångar lagras varaktigt genom att göra redundanta kopior som skyddar mot oväntade fel. Företag kan använda Data Lake Storage Gen1 i sina lösningar som en viktig del av deras befintliga dataplattform.

Data Lake Storage Gen1 innehåller också säkerhet i företagsklass för lagrade data. Mer information finns i [skydda data i Azure Data Lake Storage Gen1](#DataLakeStoreSecurity).

### <a name="all-data"></a>Alla data
Data Lake Storage Gen1 kan lagra data i ursprungligt format, så som de är, utan några tidigare transformationer. Data Lake Storage Gen1 kräver inte något schema ska definieras innan data läses lämnar det upp till det enskilda analytiska ramverket att tolka data och definiera ett schema vid analysens tidpunkt. Att kunna lagra filer av godtycklig storlek och format gör det möjligt för Data Lake Storage Gen1 hanteringen av strukturerade, halvstrukturerade och Ostrukturerade data.

Data Lake Storage Gen1 behållare för data är i stort sett mappar och filer. Du använder lagrade data med hjälp av Azure Powershell, SDK:er och Azure Portal. Så länge du placerar dina data i lagret med hjälp av dessa gränssnitt och använder lämpliga containrar kan du lagra alla typer av data. Data Lake Storage Gen1 utför inte någon särskild hantering av data baserat på vilken typ av data som lagras.

## <a name="DataLakeStoreSecurity"></a>Skydda data i Data Lake Storage Gen1
Data Lake Storage Gen1 använder Azure Active Directory för autentisering och åtkomst åtkomstkontrolllistor (ACL) för att hantera åtkomst till dina data.

| Funktion | Beskrivning |
| --- | --- |
| Autentisering |Data Lake Storage Gen1 integreras med Azure Active Directory (AAD) för identitets- och åtkomsthantering för alla data som lagras i Data Lake Storage Gen1. Med integrationen drar Data Lake Storage Gen1 fördelar från alla AAD-funktioner, inklusive multifaktorautentisering, villkorlig åtkomst, rollbaserad åtkomstkontroll, övervakning av programanvändning, säkerhetsövervakning och avisering, osv. Data Lake Storage Gen1 stöder OAuth 2.0-protokollet för autentisering i REST-gränssnittet. Se [Data Lake Storage Gen1 autentisering](data-lakes-store-authentication-using-azure-active-directory.md)|
| Åtkomstkontroll |Data Lake Storage Gen1 ger åtkomstkontroll genom att stödja behörigheter av POSIX-typ exponeras av WebHDFS-protokollet. Du kan aktivera ACL:er i rotmappen, i undermappar och i enskilda filer. Läs mer om hur åtkomstkontrollposter fungerar i kontexten för Data Lake Storage Gen1 [åtkomstkontroll i Data Lake Storage Gen1](data-lake-store-access-control.md). |
| Kryptering |Data Lake Storage Gen1 innehåller också kryptering för data som lagras i kontot. Du kan ange krypteringsinställningarna när du skapar ett Data Lake Storage Gen1-konto. Du kan välja att ha krypterade data eller välja ingen kryptering. Mer information finns i [kryptering i Data Lake Storage Gen1](data-lake-store-encryption.md). Anvisningar för hur du skapar en krypteringsrelaterad konfiguration finns i [Kom igång med Azure Data Lake Storage Gen1 med hjälp av Azure-portalen](data-lake-store-get-started-portal.md). |

Om du vill veta mer om hur du skyddar data i Data Lake Storage Gen1? Följ länkarna nedan.

* Anvisningar om hur du skyddar data i Data Lake Storage Gen1 finns i [skydda data i Azure Data Lake Storage Gen1](data-lake-store-secure-data.md).
* Föredrar du video? [Den här videon](https://mix.office.com/watch/1q2mgzh9nn5lx) om hur du skyddar data som lagras i Data Lake Storage Gen1.

## <a name="applications-compatible-with-data-lake-storage-gen1"></a>Program som är kompatibla med Data Lake Storage Gen1
Data Lake Storage Gen1 är kompatibelt med de flesta öppna källkomponenter i Hadoop-ekosystemet. Det är även snyggt integrerat med andra Azure-tjänster. På så sätt blir Data Lake Storage Gen1 ett perfekt alternativ för dina datalagringsbehov. Följ länkarna nedan för att lära dig mer om hur Data Lake Storage Gen1 kan användas både med öppna källkodskomponenter samt andra Azure-tjänster.

* Se [program och tjänster som är kompatibla med Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md) för en lista med öppna källkodsprogram med Data Lake Storage Gen1.
* Se [integrera med andra Azure-tjänster](data-lake-store-integrate-with-other-services.md) att förstå hur Data Lake Storage Gen1 kan användas med andra Azure-tjänster för att aktivera ett bredare spektrum av scenarier.
* Se [scenarier för att använda Data Lake Storage Gen1](data-lake-store-data-scenarios.md) att lära dig hur du använder Data Lake Storage Gen1 i scenarier som matar in data, bearbetning av data, hämta data och visualisera data.

## <a name="what-is-data-lake-storage-gen1-file-system-adl"></a>Vad är Data Lake Storage Gen1 filsystem (adl: / /)?
Data Lake Storage Gen1 kan nås via det nya filsystemet AzureDataLakeFilesystem (adl: / /), i Hadoop-miljöer (tillgängligt med HDInsight-kluster). Program och tjänster som använder adl:// kan dra nytta av ytterligare prestandaoptimering som inte är tillgänglig i WebHDFS. Därför kan Data Lake Storage Gen1 ger dig möjlighet att utnyttja antingen bästa prestanda med det rekommenderade alternativet för att använda adl: / / eller hantera befintlig kod genom att fortsätta att använda WebHDFS API direkt. Azure HDInsight utnyttjar till fullo AzureDataLakeFilesystem för att ge bästa möjliga prestanda i Data Lake Storage Gen1.

Du kan komma åt dina data i Data Lake Storage Gen1 med `adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`. Mer information om hur du kommer åt data i Data Lake Storage Gen1 finns i [visa egenskaper för lagrade data](data-lake-store-get-started-portal.md#properties)

## <a name="next-steps"></a>Nästa steg

* [Komma igång med Data Lake Storage Gen1 med hjälp av Azure-portalen](data-lake-store-get-started-portal.md)
* [Kom igång med Azure Data Lake Storage Gen1 med .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Använd Azure HDInsight med Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)