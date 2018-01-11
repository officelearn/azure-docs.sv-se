---
title: "Vad är Azure Databricks? | Microsoft Docs"
description: "Läs mer om vad Azure Databricks är och hur det för in Spark på Databricks i Azure. Azure Databricks är en Apache Spark-baserad analysplattform som är optimerad för Microsoft Azures plattform för molntjänster."
services: azure-databricks
documentationcenter: 
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: azure-databricks
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 11/15/2017
ms.author: nitinme
ms.custom: mvc
ms.openlocfilehash: b96896b22d406954e80d1df268f55c1c5a02ec6f
ms.sourcegitcommit: c87e036fe898318487ea8df31b13b328985ce0e1
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/19/2017
---
# <a name="what-is-azure-databricks"></a>Vad är Azure Databricks?

Azure Databricks är en Apache Spark-baserad analysplattform som är optimerad för Microsoft Azures plattform för molntjänster. Databricks har utformats med grundarna av Apache Spark och är integrerat med Azure för att tillhandahålla konfiguration med ett klick, effektiva arbetsflöden och en interaktiv arbetsyta som möjliggör samarbete mellan dataanalytiker, dataingenjörer och affärsanalytiker.

![Vad är Azure Databricks?](./media/what-is-azure-databricks/azure-databricks-overview.png "Vad är Azure Databricks?")

## <a name="apache-spark-based-analytics-platform"></a>Apache Spark-baserad analysplattform

Azure Databricks består av Apache Spark-klusterteknik och funktioner med helt öppen källkod. Spark i Azure Databricks innehåller följande komponenter:

![Apache Spark i Azure Databricks](./media/what-is-azure-databricks/apache-spark-ecosystem-databricks.png "Apache Spark i Azure Databricks")

* **Spark SQL och DataFrames**: Spark SQL är Spark-modulen för att arbeta med strukturerade data. En DataFrame är en distribuerad datasamling som har ordnats i namngivna kolumner. Begreppsmässigt motsvarar den en tabell i en relationsdatabas eller en dataram i R/Python.

* **Strömning**: Realtidsbearbetning av data och analys för analysprogram och interaktiva program. Integreras med HDFS, Flume och Kafka.

* **MLib**: Machine Learning-bibliotek som består av vanliga algoritmer och verktyg, inklusive klassificering, regression, klustring, samarbetsfilter, dimensionsminskning samt underliggande optimeringsprimitiver.

* **GraphX**: Diagram och diagramberäkning för ett brett omfång av användarfall, från kognitiv analys till datautforskning.

* **Spark Core API**: Har stöd för R, SQL, Python, Scala och Java.

## <a name="apache-spark-in-azure-databricks"></a>Apache Spark i Azure Databricks

Azure Databricks bygger på funktionerna i Spark genom att tillhandahålla en underhållsfri molnplattform som innehåller:

- Helt hanterade Spark-kluster
- En interaktiv arbetsyta för utforskning och visualisering
- En plattform för Spark-baserade program

### <a name="fully-managed-apache-spark-clusters-in-the-cloud"></a>Helt hanterade Apache Spark-kluster i molnet

Azure Databricks har en säker och tillförlitlig produktionsmiljö i molnet, som hanteras och stöds av Spark-experter. Du kan:

* Skapa kluster på några sekunder.
* Autoskala automatiskt kluster upp och ned, inklusive serverlösa kluster, och dela dem i olika grupper. 
* Använd kluster programmässigt med hjälp av REST-API:er. 
* Använd säkra funktioner för dataintegrering som byggs ovanpå Spark och gör det möjligt att göra data enhetliga utan centralisering. 
* Få åtkomst direkt till de senaste Apache Spark-funktionerna med varje version.

### <a name="databricks-runtime"></a>Databricks Runtime
Databricks Runtime bygger på Apache Spark och är utformat för Azure-molnet. 

Med det **serverlösa** alternativet kan Azure Databricks helt ta bort komplexiteten i infrastrukturen och behovet av specialkunskaper för att installera och konfigurera datainfrastrukturen. Det serverlösa alternativet hjälper dataexperter att snabbt iterera som ett team.

För datatekniker som bryr sig om prestanda för produktionsjobb tillhandahåller Azure Databricks en Spark-motor som är snabbare och har bättre prestanda i olika optimeringar i I/O-lagret och bearbetningslagret (Databricks I/O).

### <a name="workspace-for-collaboration"></a>Arbetsyta för samarbete

Via en gemensam och integrerad miljö effektiviserar Azure Databricks processen med att utforska data, skapa prototyper och köra datadrivna program i Spark.

* Avgör hur du vill använda data med enkel datautforskning.
* Dokumentera förloppet på bärbara datorer i R, Python, Scala eller SQL.
* Visualisera data med några få klickningar och använd välbekanta verktyg som Matplotlib, ggplot eller d3.
* Använd interaktiva instrumentpaneler för att skapa dynamiska rapporter.
* Använd Spark och interagera med data samtidigt.

## <a name="enterprise-security"></a>Företagssäkerhet

Azure Databricks ger Azure-säkerhet i företagsklass, inklusive integrering av Azure Active Directory, rollbaserade kontroller och serviceavtal som skyddar data och företaget.

* Med hjälp av integrering med Azure Active Directory kan du köra fullständiga Azure-baserade lösningar med Azure Databricks.
* Den rollbaserade åtkomsten i Azure Databricks möjliggör detaljerad användarbehörighet för bärbara datorer, kluster, jobb och data.
* Serviceavtal i företagsklass. 

## <a name="integration-with-azure-services"></a>Integrering med Azure-tjänster

Azure Databricks kan integreras djupt med Azure-databaser och lager: SQL Data Warehouse, Cosmos DB, Data Lake Store och Blob Storage. 

## <a name="integration-with-power-bi"></a>Integrering med Power BI
Genom omfattande integrering med Power BI gör Azure Databricks det möjligt att identifiera och dela dina effektfulla insikter snabbt och enkelt. Du kan även använda andra BI-verktyg, som Tableau Software via JDBC/ODBC-klusterslutpunkter.

## <a name="next-steps"></a>Nästa steg

* [Snabbstart: Kör ett Spark-jobb på Azure Databricks](quickstart-create-databricks-workspace-portal.md)
* [Arbeta med Spark-kluster](https://docs.azuredatabricks.net/user-guide/clusters/index.html)
* [Arbeta med bärbara datorer](https://docs.azuredatabricks.net/user-guide/notebooks/index.html)
* [Skapa Spark-jobb](https://docs.azuredatabricks.net/user-guide/jobs.html)

 









