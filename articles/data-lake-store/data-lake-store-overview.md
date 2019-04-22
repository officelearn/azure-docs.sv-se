---
title: Vad är Azure Data Lake Storage Gen1? | Microsoft Docs
description: Översikt över Data Lake Storage Gen1 (kallades tidigare Azure Data Lake Store) och dess värde över andra dataarkiv
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: twooley
ms.openlocfilehash: 518c129aedf3161ab761d09139e0c4d988dd2cbc
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59681836"
---
# <a name="what-is-azure-data-lake-storage-gen1"></a>Vad är Azure Data Lake Storage Gen1?

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Azure Data Lake Storage Gen1 är en företagsomfattande storskalig lagringsplats för analytiska arbetsbelastningar för stordata. Med Azure Data Lake kan du samla in data av olika storlekar, former, typer och inmatningshastighet på en enda plats för drifts- och undersökande analyser.

Data Lake Storage Gen1 kan nås från Hadoop (tillgängligt med HDInsight-kluster) med hjälp av WebHDFS-kompatibla REST API: er. Det har utformats för att aktivera analyser på lagrade data och är inställd för prestanda för dataanalyser. Data Lake Storage Gen1 innehåller alla funktioner i företagsklass: säkerhet, hanterbarhet, skalbarhet, tillförlitlighet och tillgänglighet.

![Azure Data Lake](./media/data-lake-store-overview/data-lake-store-concept.png)

## <a name="key-capabilities"></a>De viktigaste funktionerna

Några av de viktigaste funktionerna i Data Lake Storage Gen1 är följande.

### <a name="built-for-hadoop"></a>Byggt för Hadoop

Data Lake Storage Gen1 är en Apache Hadoop-filsystem som är kompatibelt med Hadoop Distributed File System (HDFS) och fungerar med Hadoop-ekosystemet. Dina befintliga HDInsight-program eller tjänster som använder WebHDFS API kan enkelt integreras med Data Lake Storage Gen1. Data Lake Storage Gen1 visar också ett WebHDFS-kompatibelt REST-gränssnitt för program.

Du kan enkelt analysera data som lagras i Data Lake Storage Gen1 med hjälp av analytiska Hadoop-ramar, till exempel MapReduce eller Hive. Du kan etablera Azure HDInsight-kluster och konfigurera dem för att komma åt data som lagras i Data Lake Storage Gen1 direkt.

### <a name="unlimited-storage-petabyte-files"></a>Obegränsad lagring, petabytefiler

Data Lake Storage Gen1 ger obegränsad lagring och kan lagra en mängd olika data för analys. Den har inte några gränser för kontostorlekar, filstorlekar eller mängden data som kan lagras i en datasjö. Enskilda filer kan variera mellan kilobyte till petabyte i storlek. Data lagras varaktigt genom att göra flera kopior. Det finns ingen gräns på tiden som data kan lagras i datasjön.

### <a name="performance-tuned-for-big-data-analytics"></a>Prestandajusterad för analyser av stordata

Data Lake Storage Gen1 har utformats för att köra storskaliga analytiska system som kräver enormt dataflöde för att fråga och analysera stora mängder data. Datasjön sprider delar av en fil i ett antal enskilda lagringsservrar. Detta förbättrar läsgenomströmning vid läsning av filen parallellt för att utföra dataanalyser.

### <a name="enterprise-ready-highly-available-and-secure"></a>Redo för företagsanvändning: Hög tillgänglighet och säkerhet

Data Lake Storage Gen1 ger branschstandard tillgänglighet och tillförlitlighet. Dina datatillgångar lagras varaktigt genom att göra redundanta kopior som skyddar mot oväntade fel.

Data Lake Storage Gen1 innehåller också säkerhet i företagsklass för lagrade data. Mer information finns i [skydda data i Azure Data Lake Storage Gen1](#DataLakeStoreSecurity).

### <a name="all-data"></a>Alla data

Data Lake Storage Gen1 kan lagra data i ursprungligt format, utan några tidigare transformationer. Data Lake Storage Gen1 kräver inte något schema ska definieras innan data läses lämnar det upp till det enskilda analytiska ramverket att tolka data och definiera ett schema vid analysens tidpunkt. Möjligheten att lagra filer av godtycklig storlek och format gör det möjligt för Data Lake Storage Gen1 hanteringen av strukturerade, halvstrukturerade och Ostrukturerade data.

Data Lake Storage Gen1 behållare för data är i stort sett mappar och filer. Du använder lagrade data med hjälp av SDK: er, Azure-portalen och Azure Powershell. Om du placerar dina data i Windows store med hjälp av dessa gränssnitt och använder lämpliga behållare kan du lagra alla typer av data. Data Lake Storage Gen1 utför inte någon särskild hantering av data baserat på vilken typ av data som lagras.

## <a name="DataLakeStoreSecurity"></a>Skydda data

Data Lake Storage Gen1 använder Azure Active Directory (Azure AD) för autentisering och åtkomst åtkomstkontrolllistor (ACL) för att hantera åtkomst till dina data.

| Funktion | Beskrivning |
| --- | --- |
| Authentication |Data Lake Storage Gen1 integreras med Azure AD för identitets- och åtkomsthantering för alla data som lagras i Data Lake Storage Gen1. Tack vare integreringen, Data Lake Storage Gen1 fördelar från alla Azure AD funktion, till exempel multifaktorautentisering, villkorlig åtkomst, rollbaserad åtkomstkontroll, övervakning av programanvändning, säkerhetsövervakning och avisering och så vidare. Data Lake Storage Gen1 stöder OAuth 2.0-protokollet för autentisering i REST-gränssnittet. Se [Data Lake Storage Gen1 autentisering](data-lakes-store-authentication-using-azure-active-directory.md).|
| Åtkomstkontroll |Data Lake Storage Gen1 ger åtkomstkontroll genom att stödja behörigheter av POSIX-typ exponeras av WebHDFS-protokollet. Du kan aktivera ACL i rotmappen, undermappar och enskilda filer. Läs mer om hur ACL: er fungerar i kontexten för Data Lake Storage Gen1 [åtkomstkontroll i Data Lake Storage Gen1](data-lake-store-access-control.md). |
| Kryptering |Data Lake Storage Gen1 innehåller också kryptering för data som lagras i kontot. Du kan ange krypteringsinställningarna när du skapar ett Data Lake Storage Gen1-konto. Du kan välja att ha krypterade data eller välja Ingen kryptering. Mer information finns i [kryptering i Data Lake Storage Gen1](data-lake-store-encryption.md). Anvisningar för hur du skapar en krypteringsrelaterad konfiguration finns i [Kom igång med Data Lake Storage Gen1 med Azure portal](data-lake-store-get-started-portal.md). |

Anvisningar om hur du skyddar data i Data Lake Storage Gen1 finns i [skydda data i Azure Data Lake Storage Gen1](data-lake-store-secure-data.md).

## <a name="application-compatibility"></a>Programkompatibilitet

Data Lake Storage Gen1 är kompatibelt med de flesta öppna källkomponenter i Hadoop-ekosystemet. Tjänsten integreras även bra med andra Azure-tjänster. Använd följande länkar om du vill veta mer om hur du kan använda Data Lake Storage Gen1 med öppen källkod-komponenter och andra Azure-tjänster:

- Se [program och tjänster som är kompatibla med Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md) en lista över program med öppen källkod samverkande med Data Lake Storage Gen1.
- Se [integrera med andra Azure-tjänster](data-lake-store-integrate-with-other-services.md) att lära dig hur du använder Data Lake Storage Gen1 med andra Azure-tjänster för att aktivera ett bredare spektrum av scenarier.
- Se [scenarier för att använda Data Lake Storage Gen1](data-lake-store-data-scenarios.md) att lära dig hur du använder Data Lake Storage Gen1 i scenarier som matar in data, bearbetning av data, hämta data och visualisera data.

## <a name="data-lake-storage-gen1-file-system"></a>Data Lake Storage Gen1 filsystem

Data Lake Storage Gen1 kan nås via filsystemet AzureDataLakeFilesystem (adl: / /) i Hadoop-miljöer (tillgängligt med HDInsight-kluster). Program och tjänster som använder adl: / / kan dra nytta av ytterligare prestandaoptimering som inte är tillgänglig i WebHDFS. Därför använder Data Lake Storage Gen1 ger du möjlighet att antingen gör för bästa prestanda med det rekommenderade alternativet för att använda adl: / / eller hantera befintlig kod genom att fortsätta att använda WebHDFS API direkt. Azure HDInsight utnyttjar till fullo AzureDataLakeFilesystem för att ge bästa möjliga prestanda i Data Lake Storage Gen1.

Du kan komma åt dina data i Data Lake Storage Gen1 med `adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`. Läs mer om hur du kommer åt data i Data Lake Storage Gen1 [visa egenskaper för lagrade data](data-lake-store-get-started-portal.md#properties).

## <a name="next-steps"></a>Nästa steg

- [Kom igång med Data Lake Storage Gen1 med Azure portal](data-lake-store-get-started-portal.md)
- [Kom igång med Data Lake Storage Gen1 med .NET SDK](data-lake-store-get-started-net-sdk.md)
- [Använd Azure HDInsight med Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)