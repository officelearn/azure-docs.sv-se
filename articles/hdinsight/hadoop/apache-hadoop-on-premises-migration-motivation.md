---
title: Migrera lokala Apache Hadoop-kluster till Azure HDInsight - motivation och fördelar
description: Lär dig motivation och förmåner för migrera lokala Hadoop-kluster till Azure HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: hrasheed
ms.openlocfilehash: efaedccde854d8cd7fa777aa9457db7203ce833a
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2018
ms.locfileid: "50221944"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---motivation-and-benefits"></a>Migrera lokala Apache Hadoop-kluster till Azure HDInsight - motivation och fördelar

Den här artikeln är den första i en serie på Metodtips för att migrera lokala distributioner av Apache Hadoop-ekosystemet till Azure HDInsight. Den här artikelserien är för personer som ansvarar för design, distributionen och migreringen av Apache Hadoop-lösningar i Azure HDInsight. De roller som kan ha nytta av de här artiklarna innehåller molnarkitekter, Hadoop-administratörer och DevOps-tekniker. Programutvecklare, dataingenjörer och datatekniker bör också dra nytta av förklaring av hur olika typer av kluster arbete i molnet.

## <a name="why-to-migrate-to-azure-hdinsight"></a>Varför ska man migrera till Azure HDInsight

Azure HDInsight är en molndistribution av Hadoop-komponenterna från den [Hortonworks Data Platform(HDP)](https://hortonworks.com/products/data-center/hdp/). Med Azure HDInsight kan du bearbeta stora mängder data på ett enkelt, snabbt och kostnadseffektivt sätt. HDInsight omfattar de populäraste ramverken med öppen källkod som:

- Apache Hadoop
- Apache Spark
- Apache Hive med LLAP
- Apache Kafka
- Apache Storm
- Apache HBase
- R

## <a name="advantages-that-azure-hdinsight-offers-over-on-premises-hadoop"></a>Fördelarna med Azure HDInsight erbjuder över lokala Hadoop-

- **Låg kostnad** -kan minska kostnaderna genom att [Skapa kluster på begäran](../hdinsight-hadoop-create-linux-clusters-adf.md) och endast betala för det du använder. Fristående beräkning och lagring ger flexibilitet genom att hålla datavolymen oberoende av klusterstorleken.
- **Automatiserad klustergenerering** – automatisk klustergenerering kräver minimal installation och konfiguration. Automation kan användas för kluster på begäran.
- **Hanterade maskinvara och konfiguration** -behöver inte bekymra dig om fysisk maskinvara eller infrastruktur med ett HDInsight-kluster. Ange bara konfigurationen för klustret och Azure ställer den in.
- **Lätt att skala** -HDInsight kan du [skala](../hdinsight-administer-use-portal-linux.md) upp eller ned arbetsbelastningar. Azure hand tar om vidaredistribution av data och arbetsbelastningen ombalansering utan att avbryta jobb för databehandling.
- **Global tillgänglighet** -HDInsight är tillgängligt i fler [regioner](https://azure.microsoft.com/regions/services/) än alla andra erbjudande för stordataanalys. Azure HDInsight är också tillgängligt i Azure Government, Kina och Tyskland så att du kan uppfylla företagets behov i viktiga områden.
- **Säker och kompatibel** -HDInsight kan du skydda företagets datatillgångar med [Azure Virtual Network](../hdinsight-extend-hadoop-virtual-network.md), [kryptering](../hdinsight-hadoop-create-linux-clusters-with-secure-transfer-storage.md), och integrering med [Azure Active Directory](../domain-joined/apache-domain-joined-introduction.md). HDInsight uppfyller också de populäraste bransch- och myndighetsstandarderna [efterlevnadsstandarder](https://azure.microsoft.com/overview/trusted-cloud).
- **Förenklad versionshantering** -Azure HDInsight hanterar versionen av Hadoop-ekosystemet komponenter och håller dem uppdaterade. Programuppdateringar är vanligtvis en komplicerad process för lokala distributioner.
- **Mindre kluster som optimerats för specifika arbetsbelastningar med färre beroenden mellan komponenter** – en typisk lokala Hadoop-installationen använder ett enda kluster som har många användningsområden. Med Azure HDInsight kan du skapa belastningsspecifikt kluster. Skapa kluster för specifika arbetsbelastningar tar bort komplexiteten i att underhålla ett enda kluster med växande komplexitet.
- **Produktivitet** – du kan använda olika verktyg för Hadoop och Spark i din utvecklingsmiljö du föredrar.
- **Utökningsbarhet med anpassade verktyg eller program från tredje part** -HDInsight-kluster kan utökas genom att installera komponenter och kan även integreras med andra lösningar för stordata med hjälp av [klick](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/)  distributioner från Azure Marketplace.
- **Enkel hantering, administration och övervakning** -Azure HDInsight kan integreras med [Azure Log Analytics](../hdinsight-hadoop-oms-log-analytics-tutorial.md) som ett enda gränssnitt som du kan övervaka alla dina kluster.
- **Integrering med andra Azure-tjänster** -HDInsight kan enkelt integreras med andra populära Azure-tjänster, till exempel följande:

    - Azure Data Factory (ADF)
    - Azure Blob Storage
    - Azure Data Lake Storage Gen2
    - Azure Cosmos DB
    - Azure SQL Database
    - Azure Analysis Services

- **Självåterställning processer och komponenter** -HDInsight ständigt kontrollerar de infrastruktur och öppen källkod-komponenter med hjälp av en egen övervakade infrastrukturen. Den återställer automatiskt allvarliga fel, till exempel otillgänglig komponenter med öppen källkod och noder. Aviseringar har utlösts i Ambari om någon komponent OSS misslyckades.

Mer information finns i artikeln [vad är Azure HDInsight och Hadoop-teknikstacken](../hadoop/apache-hadoop-introduction.md).

## <a name="migration-planning-process"></a>Planeringsprocess för migrering

Följande steg rekommenderas för att planera en migrering av lokala Hadoop-kluster till Azure HDInsight:

1. Förstå aktuella lokal distribution och topologier.
2. Förstå aktuella projektets omfattning, tidslinjer och team expertis.
3. Förstå Azure-kraven.
4. Bygga ut en detaljerad plan baserat på bästa praxis.

## <a name="next-steps"></a>Nästa steg

Läs nästa artikel i den här serien:

- [Arkitektur för bästa praxis för lokalt till Azure HDInsight Hadoop-migrering](apache-hadoop-on-premises-migration-best-practices-architecture.md)