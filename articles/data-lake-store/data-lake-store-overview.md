---
title: Vad är Azure Data Lake Storage Gen1? | Microsoft Docs
description: Översikt över Data Lake Storage Gen1 (tidigare kallat Azure Data Lake Store) och det värde som den ger över andra datalager
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: twooley
ms.openlocfilehash: 99384374226fd89cfd672c6b4f851a1743db0764
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67118793"
---
# <a name="what-is-azure-data-lake-storage-gen1"></a>Vad är Azure Data Lake Storage Gen1?

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Azure Data Lake Storage Gen1 är en företagsomfattande hyperskalalagringsdatabas för stordata analytiska arbetsbelastningar. Med Azure Data Lake kan du samla in data av olika storlekar, former, typer och inmatningshastighet på en enda plats för drifts- och undersökande analyser.

Data Lake Storage Gen1 kan nås från Hadoop (tillgängligt med HDInsight-kluster) med hjälp av WebHDFS-kompatibla REST-API:er. Den är utformad för att aktivera analyser av lagrade data och är inställd för prestanda för dataanalysscenarier. Data Lake Storage Gen1 innehåller alla funktioner i företagsklass: säkerhet, hanterbarhet, skalbarhet, tillförlitlighet och tillgänglighet.

![Azure Data Lake](./media/data-lake-store-overview/data-lake-store-concept.png)

## <a name="key-capabilities"></a>De viktigaste funktionerna

Några av de viktigaste funktionerna i Data Lake Storage Gen1 inkluderar följande.

### <a name="built-for-hadoop"></a>Byggt för Hadoop

Data Lake Storage Gen1 är ett Apache Hadoop-filsystem som är kompatibelt med Hadoop Distributed File System (HDFS) och fungerar med Hadoop-ekosystemet. Dina befintliga HDInsight-program eller -tjänster som använder WebHDFS API kan enkelt integreras med Data Lake Storage Gen1. Data Lake Storage Gen1 exponerar också ett WebHDFS-kompatibelt REST-gränssnitt för program.

Du kan enkelt analysera data som lagras i Data Lake Storage Gen1 med Hadoop analytiska ramverk som MapReduce eller Hive. Du kan etablera Azure HDInsight-kluster och konfigurera dem för att direkt komma åt data som lagras i Gen1 för lagring av datasjö.

### <a name="unlimited-storage-petabyte-files"></a>Obegränsad lagring, petabytefiler

Data Lake Storage Gen1 ger obegränsad lagring och kan lagra en mängd olika data för analys. Det medför inga begränsningar för kontostorlekar, filstorlekar eller mängden data som kan lagras i en datasjö. Enskilda filer kan variera från kilobyte till petabyte i storlek. Data lagras på ett sätt genom att göra flera kopior. Det finns ingen gräns för hur länge data kan lagras i datasjön.

### <a name="performance-tuned-for-big-data-analytics"></a>Prestandajusterad för analyser av stordata

Data Lake Storage Gen1 är byggd för att köra storskaliga analytiska system som kräver massivt dataflöde för att fråga och analysera stora mängder data. Datasjön sprider delar av en fil i ett antal enskilda lagringsservrar. Detta förbättrar läsgenomströmning vid läsning av filen parallellt för att utföra dataanalyser.

### <a name="enterprise-ready-highly-available-and-secure"></a>Enterprise ready: Mycket tillgänglig och säker

Data Lake Storage Gen1 ger branschstandard tillgänglighet och tillförlitlighet. Dina datatillgångar lagras varaktigt genom att göra redundanta kopior som skyddar mot oväntade fel.

Data Lake Storage Gen1 ger också säkerhet i företagsklass för lagrade data. Mer information finns [i Skydda data i Azure Data Lake Storage Gen1](#DataLakeStoreSecurity).

### <a name="all-data"></a>Alla data

Data Lake Storage Gen1 kan lagra alla data i sitt ursprungliga format, utan att kräva några tidigare omvandlingar. Data Lake Storage Gen1 kräver inte att ett schema definieras innan data läses in, vilket gör att det är upp till det enskilda analytiska ramverket att tolka data och definiera ett schema vid tidpunkten för analysen. Möjligheten att lagra filer med godtyckliga storlekar och format gör det möjligt för Data Lake Storage Gen1 att hantera strukturerade, halvstrukturerade och ostrukturerade data.

Data Lake Storage Gen1 behållare för data är i huvudsak mappar och filer. Du arbetar med lagrade data med SDK:er, Azure-portalen och Azure Powershell. Om du placerar dina data i butiken med hjälp av dessa gränssnitt och använder lämpliga behållare kan du lagra alla typer av data. Data Lake Storage Gen1 utför inte någon särskild hantering av data baserat på vilken typ av data som lagras.

## <a name="securing-data"></a><a name="DataLakeStoreSecurity"></a>Skydda data

Data Lake Storage Gen1 använder Azure Active Directory (Azure AD) för autentisering och åtkomstkontrollistor (ACL: er) för att hantera åtkomst till dina data.

| Funktion | Beskrivning |
| --- | --- |
| Autentisering |Data Lake Storage Gen1 integreras med Azure AD för identitets- och åtkomsthantering för alla data som lagras i Data Lake Storage Gen1. På grund av integrationen drar Data Lake Storage Gen1 nytta av alla Azure AD-funktioner som multifaktorautentisering, villkorlig åtkomst, rollbaserad åtkomstkontroll, programanvändningsövervakning, säkerhetsövervakning och aviseringar och så vidare. Data Lake Storage Gen1 stöder OAuth 2.0-protokollet för autentisering inom REST-gränssnittet. Se [Autentiseringen för Datasjölagring Gen1](data-lakes-store-authentication-using-azure-active-directory.md).|
| Åtkomstkontroll |DataSjölagringsgenm1 ger åtkomstkontroll genom att stödja behörigheter i POSIX-format som exponeras av WebHDFS-protokollet. Du kan aktivera ACL:er i rotmappen, på undermappar och på enskilda filer. Mer information om hur ACL fungerar i samband med Data Lake Storage Gen1 finns [i Åtkomstkontroll i Gen1 för lagring av datasjö](data-lake-store-access-control.md). |
| Kryptering |Data Lake Storage Gen1 tillhandahåller också kryptering för data som lagras i kontot. Du anger krypteringsinställningarna när du skapar ett Data Lake Storage Gen1-konto. Du kan välja att få dina data krypterade eller välja ingen kryptering. Mer information finns [i Kryptering i DataSjölagring Gen1](data-lake-store-encryption.md). Instruktioner om hur du tillhandahåller krypteringsrelaterad konfiguration finns i [Komma igång med Data Lake Storage Gen1 med Azure-portalen](data-lake-store-get-started-portal.md). |

Instruktioner om hur du skyddar data i DataSjölagring gen1 finns [i Skydda data i Azure Data Lake Storage Gen1](data-lake-store-secure-data.md).

## <a name="application-compatibility"></a>Programkompatibilitet

Data Lake Storage Gen1 är kompatibel med de flesta komponenter med öppen källkod i Hadoop-ekosystemet. Den integrerar också väl med andra Azure-tjänster. Om du vill veta mer om hur du kan använda Data Lake Storage Gen1 med komponenter med öppen källkod och andra Azure-tjänster använder du följande länkar:

- Se [Program och tjänster som är kompatibla med Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md) för en lista över program med öppen källkod som är driftskompatibla med Gen1 för lagring av datasjö.
- Se [Integrera med andra Azure-tjänster](data-lake-store-integrate-with-other-services.md) för att förstå hur du använder Data Lake Storage Gen1 med andra Azure-tjänster för att aktivera ett bredare spektrum av scenarier.
- Se [Scenarier för att använda DataSjölagring Gen1](data-lake-store-data-scenarios.md) om du vill lära dig hur du använder DataSjölagring gen1 i scenarier som att använda data, bearbeta data, hämta data och visualisera data.

## <a name="data-lake-storage-gen1-file-system"></a>Data Lake Storage Gen1 filsystem

Data Lake Storage Gen1 kan nås via filsystemet AzureDataLakeFilesystem (adl://) i Hadoop-miljöer (tillgängligt med HDInsight-kluster). Program och tjänster som använder adl:// kan dra nytta av ytterligare prestandaoptimeringar som för närvarande inte är tillgängliga i WebHDFS. Som ett resultat ger Data Lake Storage Gen1 dig flexibiliteten att antingen använda sig av bästa prestanda med det rekommenderade alternativet att använda adl:// eller underhålla befintlig kod genom att fortsätta att använda WebHDFS API direkt. Azure HDInsight utnyttjar AzureDataLakeFilesystem fullt ut för att ge bästa prestanda på Data Lake Storage Gen1.

Du kan komma åt dina data `adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`i Data Lake Storage Gen1 med . Mer information om hur du kommer åt data i DataSjölagring Gen1 finns i [Visa egenskaper för lagrade data](data-lake-store-get-started-portal.md#properties).

## <a name="next-steps"></a>Nästa steg

- [Komma igång med Data Lake Storage Gen1 med Azure-portalen](data-lake-store-get-started-portal.md)
- [Komma igång med DataSjölagring Gen1 med .NET SDK](data-lake-store-get-started-net-sdk.md)
- [Använda Azure HDInsight med Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)