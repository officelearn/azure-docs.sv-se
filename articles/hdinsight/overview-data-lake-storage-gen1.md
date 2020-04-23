---
title: Översikt över Azure Data Lake Storage Gen1 i HDInsight
description: Översikt över Data Lake Storage Gen1 i HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: 15d7f0621ffbf883d267d389fb634a13aa927430
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81873360"
---
# <a name="azure-data-lake-storage-gen1-overview-in-hdinsight"></a>Översikt över Azure Data Lake Storage Gen1 i HDInsight

Azure Data Lake Storage Gen1 är en företagsomfattande hyperskalalagringsdatabas för stordata analytiska arbetsbelastningar. Med Hjälp av Azure Data Lake kan du samla in data av valfri storlek, typ och inmatningshastighet. Och på ett ställe för operativa och undersökande analyser.

Access Data Lake Storage Gen1 från Hadoop (tillgängligt med ett HDInsight-kluster) med hjälp av WebHDFS-kompatibla REST-API:er. Data Lake Storage Gen1 är utformad för att möjliggöra analys av lagrade data och är inställd för prestanda i dataanalysscenarier. Gen1 innehåller de funktioner som är nödvändiga för verkliga företagsanvändningsfall. Dessa funktioner omfattar säkerhet, hanterbarhet, anpassningsförmåga, tillförlitlighet och tillgänglighet.

Mer information om Azure Data Lake Storage Gen1 finns i den detaljerade [översikten över Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md).

De viktigaste funktionerna i Data Lake Storage Gen1 inkluderar följande.

## <a name="compatibility-with-hadoop"></a>Kompatibilitet med Hadoop

Data Lake Storage Gen1 är ett Apache Hadoop-filsystem som är kompatibelt med HDFS och Hadoop-miljö.  HDInsight-program eller -tjänster som använder WebHDFS API kan enkelt integreras med Data Lake Storage Gen1. Data Lake Storage Gen1 exponerar också ett WebHDFS-kompatibelt REST-gränssnitt för program.

Data som lagras i Data Lake Storage Gen1 kan enkelt analyseras med Hadoop analytiska ramverk. Ramverk som MapReduce eller Hive. Azure HDInsight-kluster kan etableras och konfigureras för att direkt komma åt data som lagras i Data Lake Storage Gen1.

## <a name="unlimited-storage-petabyte-files"></a>Obegränsad lagring, petabytefiler

Data Lake Storage Gen1 ger obegränsad lagring och är lämplig för lagring av olika typer av data för analys. Det innebär inte gränser för kontostorlekar eller filstorlekar. Eller mängden data som kan lagras i en datasjö. Enskilda filer varierar i storlek från kilobyte till petabyte, vilket gör Data Lake Storage Gen1 till ett utmärkt val för att lagra alla typer av data. Data lagras på ett sätt genom att göra flera kopior. Och det finns inga gränser för hur länge data kan lagras i datasjön.

## <a name="performance-tuning-for-big-data-analytics"></a>Prestandajustering för stordataanalys

Data Lake Storage Gen1 är utformad för analytiska system. System som kräver massivt dataflöde för att fråga och analysera stora mängder data. Datasjön sprider delar av en fil över flera enskilda lagringsservrar. När du analyserar data förbättrar den här inställningen läsdataflödet när filen läss parallellt.

## <a name="readiness-for-enterprise-highly-available-and-secure"></a>Beredskap för företag: Mycket tillgänglig och säker

Data Lake Storage Gen1 ger branschstandard tillgänglighet och tillförlitlighet. Datatillgångar lagras på ett sätt: överflödiga kopior skyddar mot oväntade fel. Företag kan använda Data Lake Storage Gen1 i sina lösningar som en viktig del av sin befintliga dataplattform.

Data Lake Storage Gen1 ger också säkerhet i företagsklass för lagrade data. Mer information finns [i Skydda data i Azure Data Lake Storage Gen1](#data-security-in-data-lake-storage-gen1).

## <a name="flexible-data-structures"></a>Flexibla datastrukturer

Data Lake Storage Gen1 kan lagra alla data i sitt ursprungliga format, som det är, utan att kräva tidigare omvandlingar. Data Lake Storage Gen1 kräver inte att ett schema definieras innan data läses in. Det enskilda analytiska ramverket tolkar data och definierar ett schema vid tidpunkten för analysen. Data Lake Storage Gen1 kan hantera strukturerade data. Och semistrukturerade och ostrukturerade data.

Data Lake Storage Gen1 behållare för data är i huvudsak mappar och filer. Du arbetar med lagrade data med hjälp av SDK:er, Azure-portalen och Azure PowerShell. Data som lagras i butiken med dessa gränssnitt och behållare kan lagra vilken datatyp som helst. Data Lake Storage Gen1 gör ingen särskild hantering av data baserat på typen av data.

## <a name="data-security-in-data-lake-storage-gen1"></a>Datasäkerhet i Gen1 för lagring av datasjö

Data Lake Storage Gen1 använder Azure Active Directory för autentisering och använder åtkomstkontrollistor (ACL: er) för att hantera åtkomst till dina data.

| **Funktion** | **Beskrivning** |
| --- | --- |
| Autentisering |DataSjölagringsgenm1 integreras med Azure Active Directory (Azure AD) för identitets- och åtkomsthantering för alla data som lagras i Data Lake Storage Gen1. På grund av integrationen drar Data Lake Storage Gen1 nytta av alla Azure AD-funktioner. Dessa funktioner inkluderar: multifaktorautentisering, villkorlig åtkomst och rollbaserad åtkomstkontroll. Dessutom programanvändningsövervakning, säkerhetsövervakning och aviseringar och så vidare. Data Lake Storage Gen1 stöder OAuth 2.0-protokollet för autentisering inom REST-gränssnittet. Se [Autentisering i Azure Data Lake Storage Gen1 med Azure Active Directory](../data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)|
| Åtkomstkontroll |DataSjölagringsgenm1 ger åtkomstkontroll genom att stödja behörigheter i POSIX-format som exponeras av WebHDFS-protokollet. Du kan aktivera ACL:er i rotmappen, i undermappar och i enskilda filer. Mer information om hur ACL fungerar i samband med Data Lake Storage Gen1 finns [i Åtkomstkontroll i Gen1](../data-lake-store/data-lake-store-access-control.md)för lagring av datasjö . |
| Kryptering |Data Lake Storage Gen1 tillhandahåller också kryptering för data som lagras i kontot. Du anger krypteringsinställningarna när du skapar ett Data Lake Storage Gen1-konto. Du kan välja att få dina data krypterade eller välja ingen kryptering. Mer information finns [i Kryptering i DataSjölagring Gen1](../data-lake-store/data-lake-store-encryption.md). Instruktioner om hur du tillhandahåller en krypteringsrelaterad konfiguration finns i [Komma igång med Azure Data Lake Storage Gen1 med Azure-portalen](../data-lake-store/data-lake-store-get-started-portal.md). |

Mer information om hur du skyddar data i Gen1 för lagring av datasjö, finns [i Skydda data som lagras i Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-secure-data.md).

## <a name="applications-that-are-compatible-with-data-lake-storage-gen1"></a>Program som är kompatibla med Data Lake Storage Gen1

Data Lake Storage Gen1 är kompatibel med de flesta komponenter med öppen källkod i Hadoop-miljön. Det är även snyggt integrerat med andra Azure-tjänster.  Följ länkarna nedan om du vill veta mer om hur Data Lake Storage Gen1 kan användas både med komponenter med öppen källkod och andra Azure-tjänster.

* Se [Stordataprogram med öppen källkod som fungerar med Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-compatible-oss-other-applications.md).
* Se [Integrera Azure Data Lake Storage Gen1 med andra Azure-tjänster](../data-lake-store/data-lake-store-integrate-with-other-services.md) för att förstå hur du använder Data Lake Storage Gen1 med andra Azure-tjänster för att aktivera ett bredare spektrum av scenarier.
* Se [Använda Azure Data Lake Storage Gen1 för stordatakrav](../data-lake-store/data-lake-store-data-scenarios.md).

## <a name="data-lake-storage-gen1-file-system-adl"></a>Data Lake Storage Gen1 filsystem (adl://)

I Hadoop-miljöer kan du komma åt Data Lake Storage Gen1 via det nya filsystemet AzureDataLakeFilesystem (adl://). Prestanda för program och `adl://` tjänster som använder kan optimeras på ett sätt som för närvarande inte är tillgängligt i WebHDFS. Som ett resultat får du flexibiliteten att antingen utnyttja bästa prestanda genom att använda den rekommenderade adl://. Eller underhålla befintlig kod genom att fortsätta att använda WebHDFS API direkt. Azure HDInsight drar full nytta av AzureDataLakeFilesystem för att ge bästa prestanda på Data Lake Storage Gen1.

Få tillgång till dina data i DataSjölagring Gen1 med hjälp av följande URI:

`adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`

Mer information om hur du kommer åt data i DataSjölagring Gen1 finns i [Åtgärder som är tillgängliga för lagrade data](../data-lake-store/data-lake-store-get-started-portal.md#properties).

## <a name="next-steps"></a>Nästa steg

* [Introduktion till Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Introduktion till Azure Storage](../storage/common/storage-introduction.md)
* [Översikt över Azure Data Lake Storage Gen2](./overview-data-lake-storage-gen2.md)