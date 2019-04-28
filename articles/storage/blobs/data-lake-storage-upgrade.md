---
title: Uppgradera din big data analytics-lösningar från Azure Data Lake Storage Gen1 till Azure Data Lake Storage Gen2
description: Uppgradera din lösning för att använda Azure Data Lake Storage Gen2
services: storage
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.date: 02/07/2019
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 84e3aff9c1c8cb3e7fe399c861c2c7d58c278fed
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62107881"
---
# <a name="upgrade-your-big-data-analytics-solutions-from-azure-data-lake-storage-gen1-to-azure-data-lake-storage-gen2"></a>Uppgradera din big data analytics-lösningar från Azure Data Lake Storage Gen1 till Azure Data Lake Storage Gen2

Om du använder Azure Data Lake Storage Gen1 i dina lösningar för analys av stordata, hjälper den här guiden dig att uppgradera lösningarna för att använda Azure Data Lake Storage Gen2. Du kan använda det här dokumentet för att utvärdera de beroenden som din lösning har på Data Lake Storage Gen1. Den här guiden visar också hur du planerar och utför uppgraderingen.

Vi hjälper dig genom följande uppgifter:

:heavy_check_mark: Utvärdera din uppgraderingsberedskap

:heavy_check_mark: Planera för en uppgradering

:heavy_check_mark: Utför uppgraderingen

## <a name="assess-your-upgrade-readiness"></a>Utvärdera din uppgraderingsberedskap

Vårt mål är att alla funktioner som finns i Data Lake Storage Gen1 ska göras tillgänglig i Data Lake Storage Gen2. Hur dessa funktioner visas t.ex. i SDK, CLI osv, kan variera mellan Data Lake Storage Gen1 och Gen2 för Data Lake Storage. Program och tjänster som fungerar med Data Lake Storage Gen1 måste fungerar på samma sätt med Data Lake Storage Gen2. Slutligen några av funktionerna visas inte i Data Lake Storage Gen2 direkt. När de blir tillgängliga kommer vi presentera dem i det här dokumentet.

Dessa nästa avsnitt hjälper dig att bestämma hur du bäst att uppgradera till Data Lake Storage Gen2 och det kan vara bäst att göra detta.

### <a name="data-lake-storage-gen1-solution-components"></a>Data Lake Storage Gen1 lösningskomponenter

Mest sannolikt när du använder Data Lake Storage Gen1 Analyslösningar eller pipelines, finns det många ytterligare tekniker som du använder för att uppnå övergripande från slutpunkt till slutpunkt-funktioner. Detta [artikeln](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-data-scenarios) beskriver olika komponenter i dataflödet som innehåller mata in, bearbeta och använda data.

Det finns dessutom övergripande komponenter att etablera, hantera och övervaka dessa komponenter. Var och en av komponenterna fungerar med Data Lake Storage Gen1 med hjälp av ett gränssnitt som passar bäst för dem. När du planerar att uppgradera din lösning till Data Lake Storage Gen2 måste du känna till de gränssnitt som används. Du måste först uppgradera såväl den hanteringsgränssnitt som gränssnitt för data eftersom varje gränssnitt har olika krav.

![Lösningskomponenter för data Lake-lagring](./media/data-lake-storage-upgrade/solution-components.png)

**Bild 1** ovan visar komponenterna funktioner som du skulle se i de flesta Analyslösningar.

**Bild 2** visar ett exempel på hur dessa komponenter kommer att implementeras med hjälp av specifika tekniker.

Funktionen lagring i **figur 1** tillhandahålls av Data Lake Storage Gen1 (**bild 2**). Observera hur de olika komponenterna i dataflödet interagera med Data Lake Storage Gen1 med hjälp av REST API: er eller SDK för Java. Tänk också på hur de övergripande funktioner komponenterna samverkar med Data Lake Storage Gen1. Komponenten etablering använder Azure Resource-mallar, medan övervakningskomponenten som använder Azure Monitor-loggar använder driftdata som kommer från Data Lake Storage Gen1.

Om du vill uppgradera en lösning från att använda Data Lake Storage Gen1 till Data Lake Storage Gen2, måste du kopiera data och metadata, nytt koppla dataflöden och sedan alla komponenter behöver för att kunna arbeta med Data Lake Storage Gen2.

I avsnitten nedan innehåller information om hur du kan fatta bättre beslut:

:heavy_check_mark: Plattformsfunktioner

:heavy_check_mark: Programmeringsgränssnitt

:heavy_check_mark: Azure-ekosystemet

:heavy_check_mark: Partnerekosystem

:heavy_check_mark: Funktionsinformation

I varje avsnitt kommer du att kunna fastställa de ”måste-haves” för uppgraderingen. När du är säker på att funktionerna är tillgängliga eller är du säker på att det är rimligt lösningar på plats, fortsätter du till den [planering för uppgradering](#planning-for-an-upgrade) i den här guiden.

### <a name="platform-capabilities"></a>Plattformsfunktioner

Det här avsnittet beskriver vilka Data Lake Storage Gen1 plattformsfunktioner som är tillgängliga i Data Lake Storage Gen2.

| | Data Lake Storage Gen1 | Data Lake Storage Gen2 - mål | Data Lake Storage Gen2 - tillgänglighetsstatus  |
|-|------------------------|-------------------------------|-----------------------------------------------|
| Dataorganisation| Stöd för data som lagras som mappar och filer | Stöd för data som lagras som objekt/blobbar som mappar och filer – [länk](https://docs.microsoft.com/azure/storage/data-lake-storage/namespace) | Stöd för data som lagras som mappar och filen – *tillgängligt nu* <br><br> Stöd för data som lagras som objekt/blobbar - *ännu inte tillgänglig* |
| Namnområde| Hierarkisk | Hierarkisk |  *Tillgängligt nu*  |
| API  | REST-API via HTTPS | REST-API via HTTP/HTTPS| *Tillgängligt nu* |
| API för serversidan| [WebHDFS-kompatibla REST-API](https://msdn.microsoft.com/library/azure/mt693424.aspx) | REST-API för Azure Blob Service [REST-API för Data Lake Storage Gen2](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2) | Data Lake Storage Gen2 REST-API – *tillgängligt för tillfället* <br><br> Azure Blob Service REST API – *ännu inte tillgänglig*       |
| Hadoop-klienten för System | Ja ([Azure Data Lake Storage](https://hadoop.apache.org/docs/current/hadoop-azure-datalake/index.html)) | Ja ([ABFS](https://jira.apache.org/jira/browse/HADOOP-15407))  | *Tillgängligt nu*  |  
| Dataåtgärder – auktorisering  | Fil- och nivå POSIX åtkomstkontrollistor (ACL) baserat på Azure Active Directory-identiteter  | Fil- och nivå POSIX åtkomstkontrollistor (ACL) baserat på Azure Active Directory-identiteter [Delningsnyckel](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key) för kontot på auktorisering rollbaserad åtkomstkontroll ([RBAC](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac)) för åtkomst till behållare | *Tillgängligt nu* |
| Dataåtgärder – loggar  | Ja | Oneoff-begäranden för loggar för viss tid med hjälp av supportärende Azure Monitoring-integrering | Oneoff-begäranden för loggar för att använda specifika varaktighet supportärende – *tillgängligt nu*<br><br> Integrering med Azure övervakning – *ännu inte tillgänglig* |
| Vilande krypteringsdata | Transparent, på serversidan med tjänst-hanterade nycklar och med Kundhanterade nycklar i Azure Key | Transparent, på serversidan med tjänst-hanterade nycklar och med Kundnycklar hanterade nycklar i Azure KeyVault | Service-hanterade nycklar – *tillgängligt nu*<br><br> Kundhanterade nycklar – *tillgängligt nu*  |
| Management-åtgärder (t.ex. Account Create) | [Rollbaserad åtkomstkontroll](https://docs.microsoft.com/azure/role-based-access-control/overview) (RBAC) som tillhandahålls av Azure för kontohantering | [Rollbaserad åtkomstkontroll](https://docs.microsoft.com/azure/role-based-access-control/overview) (RBAC) som tillhandahålls av Azure för kontohantering | *Tillgängligt nu*|
| SDK: er för utvecklare | .NET, Java, Python, Node.js  | .NET, Java, Python, Node.js, C++, Ruby, PHP, Go, Android, iOS| *Ännu inte tillgänglig* |
| |Optimerad prestanda för arbetsbelastningar för parallella analyser. Högt dataflöde och IOPS. | Optimerad prestanda för arbetsbelastningar för parallella analyser. Högt dataflöde och IOPS. | *Tillgängligt nu* |
| Stöd för Virtual Network (VNet)  | [Med Virtual Network-integration](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-network-security)  | [Med hjälp av tjänsteslutpunkt för Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | *Tillgängligt nu* |
| Storleksbegränsningar | Inga gränser för kontostorlekar, filstorlekar eller antal filer | Inga gränser för kontostorlekar eller många filer. Filstorleken är begränsad till 5TB. | *Tillgängligt nu*|
| GEO-redundans| Lokalt redundant (LRS) | Lokalt redundant (LRS) zonredundant (ZRS) globalt redundant (GRS) läsbehörighet globalt redundant (RA-GRS) Se [här](https://docs.microsoft.com/azure/storage/common/storage-redundancy) för mer information| *Tillgängligt nu* |
| Regional tillgänglighet | Se [här](https://azure.microsoft.com/regions/) | Alla [Azure-regioner](https://azure.microsoft.com/global-infrastructure/regions/)                                                                                                                                                                                                                                                                                                                                       | *Tillgängligt nu*                                                                                                                           |
| Pris                                       | Se [priser](https://azure.microsoft.com/pricing/details/data-lake-store/)                                                                            | Se [priser](https://azure.microsoft.com/pricing/details/storage/data-lake/)                                                                                                                                                                                                                                                                                                                                         |                                                                                                                                           |
| Serviceavtal finns                            | [Se SLA](https://azure.microsoft.com/support/legal/sla/data-lake-store/v1_0/)                                                                   | [Se SLA](https://azure.microsoft.com/support/legal/sla/storage/v1_3/)                                                                                                                                                                                                                                                                                                                                                | *Tillgängligt nu*                                                                                                                           |
| Databearbetning                             | Förfallodatum för filer                                                                                                                                        | Principer för livscykel                                                                                                                                                                                                                                                                                                                                                                                                          | *Ännu inte tillgänglig*                                                                                                                       |

### <a name="programming-interfaces"></a>Programmeringsgränssnitt

Den här tabellen beskriver som API som är tillgängliga för dina anpassade program. Om du vill göra det lite tydligare, har vi avgränsade dessa API-uppsättningarna i 2 typer: management API: er och filsystemet API: er.

Management-API: er hjälper dig att hantera konton, samtidigt som filsystem som API: erna hjälpa dig att arbeta på filer och mappar.

|  API: n                           |  Data Lake Storage Gen1                                                                                                                                                                                                                                                                                                   | Tillgänglighet för Data Lake Storage Gen2 - med autentisering med delad nyckel | Tillgänglighet för Data Lake Storage Gen2 - med OAuth-autentisering                                                                                                  |
|----------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| .NET SDK - hantering                  | [Länk](https://docs.microsoft.com/dotnet/api/overview/azure/datalakestore/management?view=azure-dotnet)                                                                                                                                                                                                                 | *Stöds inte*                                                      | *Tillgängligt för tillfället -* [länk](https://docs.microsoft.com/rest/api/storageservices/operations-on-the-account--blob-service-)                                    |
| .NET SDK – filsystem                  | [Länk](https://docs.microsoft.com/dotnet/api/overview/azure/datalakestore/client?view=azure-dotnet)                                                                                                                                                                                                                     | *Ännu inte tillgänglig*                                                | *Ännu inte tillgänglig*                                                                                                                                             |
| Java SDK - hantering                  | [Länk](https://docs.microsoft.com/java/api/overview/azure/datalakestore/management)                                                                                                                                                                                                                                     | *Stöds inte*                                                      | *Tillgängligt för tillfället –* [länk](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob?view=azure-java-stable)                                     |
| Java SDK – filsystem                  | [Länk](https://docs.microsoft.com/java/api/overview/azure/datalake)                                                                                                                                                                                                                                         | *Ännu inte tillgänglig*                                                | *Ännu inte tillgänglig*                                                                                                                                             |
| Node.js - hantering                   | [Länk](https://www.npmjs.com/package/azure-arm-datalake-store)                                                                                                                                                                                                                                                                | Stöds inte                                                      | *Tillgängligt för tillfället -* [länk](https://azure.github.io/azure-storage-node/)                                                                                            |
| Node.js - filsystem                   | [Länk](https://www.npmjs.com/package/azure-arm-datalake-store)                                                                                                                                                                                                                                                                | *Ännu inte tillgänglig*                                                | *Ännu inte tillgänglig*                                                                                                                                             |
| Python - hantering                    | [Länk](https://docs.microsoft.com/python/api/overview/azure/datalakestore/management?view=azure-python)                                                                                                                                                                                                                 | *Stöds inte*                                                      | *Tillgängligt för tillfället -* [länk](https://docs.microsoft.com/python/api/overview/azure/storage/management?view=azure-python)                                       |
| Python - filsystem                    | [Länk](https://azure-datalake-store.readthedocs.io/en/latest/)                                                                                                                                                                                                                                                                 | *Ännu inte tillgänglig*                                                | *Ännu inte tillgänglig*                                                                                                                                             |
| REST API - hantering                  | [Länk](https://docs.microsoft.com/rest/api/datalakestore/accounts)                                                                                                                                                                                                                                                      | *Stöds inte*                                                      | *Tillgängligt nu-*                                                                                                                                               |
| REST API - filsystem                  | [Länk](https://docs.microsoft.com/rest/api/datalakestore/webhdfs-filesystem-apis)                                                                                                                                                                                                                                       | *Tillgängligt nu*                                                    | *Tillgängligt för tillfället -* [länk](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2)                                                      |
| PowerShell – hantering och filsystemet | [Länk](https://docs.microsoft.com/powershell/module/az.datalakestore)                                                                                                                                                                                                                        | Hantering – har inte stöd för filsystem - *ännu inte tillgänglig*        | Hantering – *tillgängligt för tillfället -* [länk](https://docs.microsoft.com/powershell/module/az.storage) <br><br>Filsystem - *ännu inte tillgänglig* |
| CLI-hantering                       | [Länk](https://docs.microsoft.com/cli/azure/dls/account?view=azure-cli-latest)                                                                                                                                                                                                                                          | *Stöds inte*                                                      | *Tillgängligt för tillfället -* [länk](https://docs.microsoft.com/cli/azure/storage?view=azure-cli-latest)                                                              |
| CLI - filsystem                       | [Länk](https://docs.microsoft.com/cli/azure/dls/fs?view=azure-cli-latest)                                                                                                                                                                                                                                               | *Ännu inte tillgänglig*                                                | *Ännu inte tillgänglig*                                                                                                                                             |
| Azure Resource Manager-mallar - hantering             | [Mall1](https://azure.microsoft.com/resources/templates/101-data-lake-store-no-encryption/)  [Template2](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-adls/)  [Template3](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-key-vault/)  | *Stöds inte*                                                      | *Tillgängligt för tillfället -* [länk](https://docs.microsoft.com/azure/templates/microsoft.storage/2018-07-01/storageaccounts)                                         |

### <a name="azure-ecosystem"></a>Azure-ekosystemet

När du använder Data Lake Storage Gen1, kan du använda olika Microsoft-tjänster och produkter i pipelines slutpunkt till slutpunkt. Arbeta med Data Lake Storage Gen1 direkt eller indirekt dessa produkter och tjänster. Den här tabellen visas en lista över tjänsterna vi har ändrat för att arbeta med Data Lake Storage Gen1 och visar vilka som är kompatibel med Data Lake Storage Gen2.

| **Område**             | **Tillgänglighet för Data Lake Storage Gen1**                                                                                                                                    | **Tillgänglighet för Data Lake Storage Gen2 – med autentisering med delad nyckel**                                                                                                           | **Tillgänglighet för Data Lake Storage Gen2 – med OAuth**                                                                                        |
|----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|
| Analytics-ramverk  | [Apache Hadoop](https://hadoop.apache.org/docs/current/hadoop-azure-datalake/index.html)                                                                                       | *Tillgängligt nu*                                                                                                                                                              | *Tillgängligt nu*                                                                                                                                 |
|                      | [HDInsight](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-hdinsight-hadoop-use-portal)                                                               | [HDInsight](https://docs.microsoft.com/azure/storage/data-lake-storage/quickstart-create-connect-hdi-cluster) 3.6 - *nu tillgängligt* HDInsight 4.0 - *ännu inte tillgänglig*      | HDInsight 3.6 ESP – *tillgängligt för tillfället* <br><br>  HDInsight 4.0 ESP - *ännu inte tillgänglig*                                                                 |
|                      | Databricks Runtime 3.1 och senare                                                                                                                                               | [Databricks Runtime 5.1 och senare](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html#azure-data-lake-storage-gen2) *– tillgängligt nu* | [Databricks Runtime 5.1 och senare](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html#azure-data-lake-storage-gen2) – *tillgängligt för tillfället*                                                                                              |
|                      | [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store)                                            | *Stöds inte*                                                                                                                                                              | *Tillgängligt för tillfället* [länk](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql?view=sql-server-2017) |
| Dataintegrering     | [Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-store)                                                                                | [Version 2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage) – *nu tillgängligt* Version 1 – *stöds inte*                               | [Version 2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage) – *tillgängligt för tillfället* <br><br> Version 1 – *stöds inte*  |
|                      | [AdlCopy](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)                                                                 | *Stöds inte*                                                                                                                                                              | *Stöds inte*                                                                                                                                 |
|                      | [SQL Server Integration Services](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager?view=sql-server-2017) | *Ännu inte tillgänglig*                                                                                                                                                          | *Ännu inte tillgänglig*                                                                                                                             |
|                      | [Data Catalog](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-with-data-catalog)                                                                       | *Ännu inte tillgänglig*                                                                                                                                                          | *Ännu inte tillgänglig*                                                                                                                             |
|                      | [Logic Apps](https://docs.microsoft.com/connectors/azuredatalake/)                                                                                                      | *Ännu inte tillgänglig*                                                                                                                                                          | *Ännu inte tillgänglig*                                                                                                                             |
| IoT                  | [Event Hubs – Capture](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-archive-eventhub-capture)                                                       | *Ännu inte tillgänglig*                                                                                                                                                          | *Ännu inte tillgänglig*                                                                                                                             |
|                      | [Stream Analytics](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-stream-analytics)                                                                   | *Ännu inte tillgänglig*                                                                                                                                                          | *Ännu inte tillgänglig*                                                                                                                             |
| Förbrukning          | [Power BI Desktop](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-power-bi)                                                                           | *Ännu inte tillgänglig*                                                                                                                                                          | *Ännu inte tillgänglig*                                                                                                                             |
|                      | [Excel](https://techcommunity.microsoft.com/t5/Excel-Blog/Announcing-the-Azure-Data-Lake-Store-Connector-in-Excel/ba-p/91677)                                                 | *Ännu inte tillgänglig*                                                                                                                                                          | *Ännu inte tillgänglig*                                                                                                                             |
|                      | [Analysis Services](https://blogs.msdn.microsoft.com/analysisservices/2017/09/05/using-azure-analysis-services-on-top-of-azure-data-lake-storage/)                            | *Ännu inte tillgänglig*                                                                                                                                                          | *Ännu inte tillgänglig*                                                                                                                             |
| Produktivitet         | [Azure Portal](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal)                                                                      | *Stöds inte*                                                                                                                                                              | Kontohantering *– nu tillgängligt* <br><br>Dataåtgärder *–**ännu inte tillgänglig*                                                                   |
|                      | [Data Lake Tools för Visual Studio](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-data-lake-tools-install)                                   | *Ännu inte tillgänglig*                                                                                                                                                          | *Ännu inte tillgänglig*                                                                                                                             |
|                      | [Azure Storage Explorer](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-in-storage-explorer)                                                          | *Tillgängligt nu*                                                                                                                                                              | *Tillgängligt nu*                                                                                                                                 |
|                      | [Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=usqlextpublisher.usql-vscode-ext)                                                                     | *Ännu inte tillgänglig*                                                                                                                                                          | *Ännu inte tillgänglig*                                                                                                                             |

### <a name="partner-ecosystem"></a>Partnerekosystem

Den här tabellen visas en lista över tjänster från tredje part och produkter som har ändrats för att arbeta med Data Lake Storage Gen1. Den visar även vilka som är kompatibel med Data Lake Storage Gen2.

| Område            | Partner  | Produkt/tjänst  | Tillgänglighet för Data Lake Storage Gen1                                                                                                                                               | Tillgänglighet för Data Lake Storage Gen2 – med autentisering med delad nyckel                                                   | Tillgänglighet för Data Lake Storage Gen2 – med Oauth                                                             |
|---------------------|--------------|----------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------|
| Analytics-ramverk | Cloudera     | CDH                  | [Länk](https://www.cloudera.com/documentation/enterprise/5-11-x/topics/admin_adls_config.html)                                                                                            | *Ännu inte tillgänglig*                                                                                                  | [Länk](https://www.cloudera.com/documentation/enterprise/latest/topics/admin_adls2_config.html)                                                                                                  |
|                     | Cloudera     | Altus                | [Länk](https://www.cloudera.com/more/news-and-blogs/press-releases/2017-09-27-cloudera-introduces-altus-data-engineering-microsoft-azure-hybrid-multi-cloud-data-analytic-workflows.html) | *Stöds inte*                                                                                                                  | *Ännu inte tillgänglig*                                                                                                  |
|                     | HortonWorks  | HDP 3.0              | [Länk](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.3/bk_cloud-data-access/content/adls-get-started.html)                                                                       | *Ännu inte tillgänglig*                                                                                                  | *Ännu inte tillgänglig*                                                                                                  |
|                     | Qubole       |                      | [Länk](https://www.qubole.com/blog/big-data-analytics-microsoft-azure-data-lake-store-qubole/)                                                                                            | *Ännu inte tillgänglig*                                                                                                  | *Ännu inte tillgänglig*                                                                                                  |
| ETL                 | StreamSets   |                      | [Länk](https://streamsets.com/blog/ingest-data-azure)                                                                                                                                     | *Ännu inte tillgänglig*                                                                                                  | *Ännu inte tillgänglig*                                                                                                  |
|                     | Informatica  |                      | [Länk](https://kb.informatica.com/proddocs/Product%20Documentation/6/IC_Spring2017_MicrosoftAzureDataLakeStoreV2ConnectorGuide_en.pdf)                                                    | *Ännu inte tillgänglig*                                                                                                  | *Ännu inte tillgänglig*                                                                                                  |
|                     | Attunity     |                      | [Länk](https://www.attunity.com/company/press-releases/microsoft-and-attunity-announce-strategic-partnership-for-data-migration/)                                                         | *Ännu inte tillgänglig*                                                                                                  | *Ännu inte tillgänglig*                                                                                                  |
|                     | Alteryx      |                      | [Länk](https://help.alteryx.com/2018.2/DataSources/AzureDataLake.htm)                                                                                                                     | *Ännu inte tillgänglig*                                                                                                  | *Ännu inte tillgänglig*                                                                                                  |
|                     | ImanisData   |                      | [Länk](https://www.imanisdata.com/expansion-azure-support-azure-data-lake-store-integration/)                                                                                             | *Ännu inte tillgänglig*                                                                                                  | *Ännu inte tillgänglig*                                                                                                  |
|                     | WANdisco     |                      | [Länk](https://azure.microsoft.com/blog/globally-replicated-data-lakes-with-livedata-using-wandisco-on-azure/)                                                                      | [Länk](https://azure.microsoft.com/blog/globally-replicated-data-lakes-with-livedata-using-wandisco-on-azure/) | [Länk](https://azure.microsoft.com/blog/globally-replicated-data-lakes-with-livedata-using-wandisco-on-azure/) |

### <a name="operational-information"></a>Funktionsinformation

Data Lake Storage Gen1 skickar specifik information och data till andra tjänster som hjälper dig att operationalisera dina pipelines. Den här tabellen visar tillgängligheten för motsvarande stöd i Data Lake Storage Gen2.

| Typ av data                                                                                       | Tillgänglighet för Data Lake Storage Gen1                                                                 | Tillgänglighet för Data Lake Storage Gen2                                                                                               |
|--------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------|
| Faktureringsdata - mätare som skickas till commerce team för fakturering och sedan görs tillgängliga för kunder  | *Tillgängligt nu*                                                                                             | *Tillgängligt nu*                                                                                                                           |
| Aktivitetsloggar                                                                                          | [Länk](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs#audit-logs)   | Oneoff-begäranden för loggar för att använda specifika varaktighet supportärende – *nu tillgängligt* integrering med Azure Monitoring - *ännu inte tillgänglig* |
| Diagnostikloggar                                                                                        | [Länk](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs#request-logs) | Oneoff-begäranden för loggar för att använda specifika varaktighet supportärende – *nu tillgängligt* integrering med Azure Monitoring - *ännu inte tillgänglig* |
| Mått                                                                                                | *Stöds inte*                                                                                               | *Tillgängligt för tillfället -* [länk](https://docs.microsoft.com/azure/storage/common/storage-metrics-in-azure-monitor)                          |

## <a name="planning-for-an-upgrade"></a>Planera för en uppgradering

Det här avsnittet förutsätter att du har granskat den [utvärdera din uppgraderingsberedskap](#assess-your-upgrade-readiness) i den här guiden och att alla dina beroenden uppfylls. Om det finns funktioner som är fortfarande är inte tillgänglig i Data Lake Storage Gen2, Fortsätt bara om du känner till motsvarande lösningar om detta. Följande avsnitt innehåller vägledning i hur du kan planera inför uppgraderingen av dina pipelines. Utför den faktiska uppgraderingen beskrivs i den [uppgraderas](#performing-the-upgrade) i den här guiden.

### <a name="upgrade-strategy"></a>Uppgradera strategi

Den viktigaste delen av uppgraderingen är att bestämma vilken strategi. Det här beslutet avgör alternativ som är tillgängliga.

Den här tabellen innehåller några vanliga strategier som har använts för att migrera databaser, Hadoop-kluster, osv. Vi antar liknande strategier inom vår vägledning och anpassa dem till vår kontext.

| Strategi                   | Experter                                                                                  | Nackdelar                                                           | När du ska använda?                                                                                                                                                                                             |
|--------------------------------|-------------------------------------------------------------------------------------------|--------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Lift and shift                 | Enklaste.                                                                                 | Kräver avbrott kopiera över data, flytta jobb, och flytta inkommande och utgående                                             | För enklare lösningar där det inte finns flera lösningar åtkomst till samma Gen1 konto och därför kan flyttas tillsammans i en snabb kontrollerat sätt.                                                  |
| Kopiera en gång – och-kopiering inkrementella | Minska stopptiden genom att utföra kopian i bakgrunden när källsystemet fortfarande är aktiv. | Kräver avbrott för att flytta ingående och utgående.                   | Mängden data som ska kopieras över är stor och stilleståndstid som är associerade med liv-and-shift kan inte godkännas. Testa krävas med betydande produktionsdata i målsystemet innan övergång. |
| Parallell införande              | Minst driftstopp tillåter tid för program att migrera efter eget gottfinnande.           | Mest avancerade sedan 2-vägs synkroniseringen krävs mellan de två systemen. | För avancerade scenarier där program som bygger på Data Lake Storage Gen1 får inte vara startpunkt på en gång och måste kunna flyttas över på ett inkrementellt sätt.                                                      |

Nedan finns mer information om vilka steg ingår för var och en av strategierna. Stegen en lista över vad du skulle göra med komponenterna som ingår i uppgraderingen. Detta inkluderar övergripande källsystemet, övergripande målsystemet, ingående källor för källsystem, utgående mål för källsystemet och jobb som körs i källsystemet.

De här stegen är inte avsedda att vara förebyggande. De är avsedda att ställa in framework om hur vi funderar på att varje strategi. Vi ger fallstudier för varje strategi som vi se dem som implementerats.

#### <a name="lift-and-shift"></a>Lift and shift

1. Pausa källsystemet – inkommande källor, jobb, utgående mål.
2. Kopiera alla data från källsystemet till målsystemet.
3. Peka alla ingress-källor på målsystemet. Peka på målet för utgående trafik från målsystemet.
4. Flytta, ändra genom att köra alla jobb i målsystemet.
5. Inaktivera källsystemet.

#### <a name="copy-once-and-copy-incremental"></a>Kopiera – när och kopiera inkrementella

1. Kopiera över data från källsystemet till målsystemet.
2. Kopiera över inkrementella data från källsystemet till målsystemet med jämna mellanrum.
3. Peka på målet för utgående trafik från målsystemet.
4. Flytta, ändra, köra alla jobb på målsystemet.
5. Peka ingående källor stegvis målsystemet används för att underlätta för dig.
6. När alla inkommande källor pekar på målsystemet.
    1. Inaktivera inkrementell kopiering.
    2. Inaktivera källsystemet.

#### <a name="parallel-adoption"></a>Parallell införande

1. Ställ in målsystemet.
2. Ställ in en dubbelriktad replikering mellan källsystemet och målsystemet.
3. Peka ingående källor stegvis på målsystemet.
4. Flytta, ändra, köra jobb stegvis till målsystemet.
5. Peka på utgående mål stegvis från målsystemet.
6. När alla ursprungliga ingress-källor, jobb och utgående mål arbetar med målsystemet kan du inaktivera källsystemet.

### <a name="data-upgrade"></a>Datauppgradering av

Övergripande strategi som används för att utföra uppgraderingen (beskrivs i den [uppgradera strategi](#upgrade-strategy) i den här guiden), avgör de verktyg som du kan använda för uppgraderingen av data. De verktyg som anges nedan är baserade på aktuell information och är förslag. 

#### <a name="tools-guidance"></a>Verktyg vägledning

| Strategi                       | Verktyg                                                                                                             | Experter                                                                                                                             | Överväganden                                                                                                                                                                                                                                                                                                                |
|------------------------------------|-----------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Lift and shift**                 | [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2-from-gen1) | Hanterad molntjänst                                                                                                                | Endast kopiera över data. ACL: er kan inte kopieras över för närvarande.                                                                                                                                                                                                                                                                      |
|                                    | [Distcp](https://hadoop.apache.org/docs/r1.2.1/distcp.html)                                                           | Välkända Hadoop-angivna verktyget behörigheter d.v.s. ACL: er kan kopieras till det här verktyget                                                   | Kräver ett kluster som kan ansluta till både Data Lake Storage Gen1 och Gen2 på samma gång.                                                                                                                                                                                   |
| **Kopiera en gång – och-kopiering inkrementella** | Azure Data Factory                                                                                                    | Hanterad molntjänst                                                                                                                | För att stödja inkrementell kopiering i ADF, behöver data ordnas i en time series-sätt. Kortaste intervall mellan inkrementella kopior är [15 minuter](https://docs.microsoft.com/azure/data-factory/how-to-create-tumbling-window-trigger). För kortare intervall fungerar inte ADF. ACL: er kan inte kopieras över för närvarande. |
| **Parallell införande**              | [WANdisco](https://docs.wandisco.com/bigdata/wdfusion/adls/)                                                           | Stöd för replikeringsgruppen om med en ren Hadoop-miljö ansluten till Azure Data Lake Storage har stöd för dubbelriktad replikering | Om du inte använder en ren Hadoop-miljö kan finnas det en fördröjning i replikeringen.                                                                                                                                                                                                                                                  |

Observera att det finns en tredje part som kan hantera Data Lake Storage Gen1 till Data Lake Storage Gen2 uppgraderingen utan att inbegripa data/meta-informationen som beskrivs ovan kopiera verktyg (till exempel: [Cloudera](https://blog.cloudera.com/blog/2017/08/use-amazon-s3-with-cloudera-bdr/)). De tillhandahåller en ”affär”-upplevelse som utför migrering av data samt migrering av arbetsbelastning. Du kan behöva utföra en uppgradering på out-of-band för verktyg som finns utanför sina ekosystem.

#### <a name="considerations"></a>Överväganden

* Du behöver manuellt skapa Gen2 för Data Lake Storage-konto innan du börjar någon del av uppgraderingen, eftersom de aktuella riktlinjerna inte inkluderar en automatisk Gen2 konto process baserat på din kontoinformation för Gen1. Se till att du jämföra konton gruppskapande processer för [Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal) och [Gen2](https://docs.microsoft.com/azure/storage/data-lake-storage/quickstart-create-account).

* Data Lake Storage Gen2 filer endast stöd för upp till 5 TB i storlek. Om du vill uppgradera till Data Lake Storage Gen2 kan du behöva ändra storlek på filer i Data Lake Storage Gen1 så att de är mindre än 5 TB.

* Om du använder ett verktyg som inte kopierar ACL: er eller om du inte vill kopiera över ACL: er så kommer du behöver ange ACL: er på målet manuellt på den översta nivån som är lämpliga. Du kan göra det genom att använda Storage Explorer. Kontrollera att dessa ACL: er är standard-ACL: er så att de filer och mappar som du kopierar du över ärver dem.

* I Data Lake Storage Gen1 är den högsta nivån som du kan ange ACL: er i roten för kontot. I Data Lake Storage Gen2 är den högsta nivån som du kan ange ACL: er dock i rotmappen i ett filsystem, inte hela kontot. Om du vill använda standard-ACL: er på kontonivå, måste du därför att duplicera de över alla filsystem i ditt Data Lake Storage Gen2-konto.

* Filen namngivningsbegränsningar skiljer sig mellan de två systemen för lagring. Dessa skillnader särskilt om när du kopierar från Data Lake Storage Gen2 till Data Lake Storage Gen1 eftersom det senare har mer begränsad begränsningar.

### <a name="application-upgrade"></a>Programuppgradering

När du vill skapa program på Data Lake Storage Gen1 eller Data Lake Storage Gen2 måste du först välja en lämplig programmeringsgränssnitt. När du anropar en API för det gränssnittet får att tillhandahålla lämpliga URI: N och rätt autentiseringsuppgifter. En representation av dessa tre delar, API, URI, och hur autentiseringsuppgifter tillhandahålls, skiljer sig åt mellan Data Lake Storage Gen1 och Data Lake Storage Gen2.So som en del av uppgradera programmet, måste du mappa dessa tre konstruktioner på rätt sätt.

#### <a name="uri-changes"></a>URI-ändringar

Den huvudsakliga uppgiften här är att översätta URI: er som har ett prefix på `adl://` i URI: er som har en `abfss://` prefix.

URI-schemat för Data Lake Storage Gen1 nämns [här](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-store) i detalj, men i stora drag, är det *adl://mydatalakestore.azuredatalakestore.net/\<filsökväg\>.*

URI-schemat för att komma åt Data Lake Storage Gen2 filer förklaras [här](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md) i detalj, men i stora drag, är det `abfss://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.widows.net/<PATH>`.

Du behöver gå igenom dina befintliga program och se till att du har ändrat URI: er på rätt sätt för att peka till Data Lake Storage Gen2 sådana. Dessutom måste du lägga till rätt autentiseringsuppgifter. Slutligen måste hur du dra tillbaka de ursprungliga program och Ersätt med det nya programmet justeras nära till din strategi för övergripande uppgradering.

#### <a name="custom-applications"></a>Anpassade program

Beroende på gränssnitt som används i ditt program med Data Lake Storage Gen1, behöver du ändra det om du vill anpassa det till Data Lake Storage Gen2.

##### <a name="rest-apis"></a>REST API:er

Om programmet använder Data Lake Storage REST API: er, måste du ändra ditt program att använda Data Lake Storage Gen2 REST API: erna. Länkar finns i *programmeringsgränssnitt* avsnittet.

##### <a name="sdks"></a>SDK:er

Som kallas ut i den *utvärdera din uppgraderingsberedskap* avsnittet SDK: er är inte tillgänglig för tillfället. Om du vill ha port över dina program till Data Lake Storage Gen2 rekommenderar vi att du väntar stödda SDK: erna ska vara tillgängliga.

##### <a name="powershell"></a>PowerShell

Som anropas i utvärdera uppgraderingsberedskap-avsnittet, är PowerShell-stöd inte tillgänglig för dataplanet.

Du kan ersätta management plan commandlets med lämpliga dem i Data Lake Storage Gen2. Länkar finns i *programmeringsgränssnitt* avsnittet.

##### <a name="cli"></a>CLI

Som kallas i *utvärdera din uppgraderingsberedskap* avsnittet CLI-stöd är inte tillgänglig för dataplanet.

Du kan ersätta kommandon för hantering av plan med lämpliga dem i Data Lake Storage Gen2. Länkar finns i *programmeringsgränssnitt* avsnittet.

### <a name="analytics-frameworks-upgrade"></a>Uppgradera Analytics ramverk

Om ditt program skapar metadata om data i arkivet som explicit fil- och mappsökvägar, måste du utföra ytterligare åtgärder när du uppgraderar du lagra data/meta-data. Detta gäller särskilt för analytics ramverk som Azure HDInsight osv. Databricks, som vanligtvis skapar katalogdata på store-data.

Analytics-ramverk fungerar med data och metadata som lagras i remote-Arkiv som Data Lake Storage Gen1 och Gen2. Så i teorin motorerna kan vara tillfälliga och göras tillgänglig endast när jobb behöver köra mot lagrade data.

Men för att optimera prestanda kan analytics-ramverk Skapa explicita referenser till de filer och mappar som lagras i arkivet för fjärråtkomst och sedan skapa ett cacheminne för att lagra dem. URI för remote data ändras, till exempel ett kluster som lagrar data i Data Lake Storage Gen1 tidigare och nu vill lagra i Data Lake Storage Gen2 är URI för samma kopierade innehåll olika. Så när data och metadata har uppgraderat cacheminnen för dessa motorer också behöva uppdateras eller initieras igen

Som en del av planeringsprocessen måste du identifiera ditt program och ta reda på hur metadata-information kan startas så att den pekar till data som lagras nu på Data Lake Storage Gen2. Nedan finns anvisningar om hur ofta antagen analytics ramverk som hjälper dig med sina uppgradering.

#### <a name="azure-databricks"></a>Azure Databricks

Stegen varierar beroende på vilken uppgradera strategi som du väljer. Det aktuella avsnittet förutsätter att du har valt ”liv-and-shift”-strategi. Dessutom förväntas i Databricks-arbetsytan som används för åtkomst till data i ett Data Lake Storage Gen1 konto att arbeta med de data som kopieras över till Gen2 för Data Lake Storage-konto.

Kontrollera först att du har skapat kontot Gen2 och sedan kopieras över data och metadata från Gen1 till Gen2 med hjälp av ett lämpligt verktyg. Dessa verktyg framhävs [Data uppgradera](#data-upgrade) i den här guiden.

Sedan [uppgradera](https://docs.azuredatabricks.net/user-guide/clusters/index.html) ditt befintliga Databricks-kluster att börja använda Databricks runtime 5.1 eller senare, som ska ha stöd för Data Lake Storage Gen2.

Stegen baseras därefter på hur den befintliga Databricks-arbetsytan har åtkomst till data i Data Lake Storage Gen1-konto. Det kan göras antingen genom att anropa adl: / / URI: er [direkt](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html#access-azure-data-lake-store-directly) från bärbara datorer eller via [monteringspunkter](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html#mount-azure-data-lake-store-with-dbfs).

Om du ansluter direkt från bärbara datorer genom att tillhandahålla fullständig adl: / / URI: er, måste du gå igenom varje anteckningsboken och ändra konfigurationen för att komma åt motsvarande Data Lake Storage Gen2 URI.

Framöver kommer måste du konfigurera om den så att den pekar till Gen2 för Data Lake Storage-konto. Inga fler ändringar krävs och de bärbara datorerna ska kunna fungera som tidigare.

Om du använder någon av de andra strategierna för uppgraderingen kan skapa du en variant av ovanstående steg som uppfyller dina krav.

### <a name="azure-ecosystem-upgrade"></a>Azure-ekosystemet uppgradering

Var och en av de verktyg och tjänster som anropas i [Azure-ekosystemet](#azure-ecosystem) i den här guiden måste konfigureras för att arbeta med Data Lake Storage Gen2.

Först måste se till att det finns integrering med Data Lake Storage Gen2.

Sedan elementen som beskrivs ovan (till exempel: URI och autentiseringsuppgifter) måste ändras. Du kan ändra den befintliga instansen som fungerar med Data Lake Storage Gen1 eller du kan skapa en ny instans som skulle fungera med Data Lake Storage Gen2.

### <a name="partner-ecosystem-upgrade"></a>Partner ekosystem uppgradering

Kontakta den partner som tillhandahåller komponenten och verktyg för att se till att de kan arbeta med Data Lake Storage Gen2. 

## <a name="performing-the-upgrade"></a>Utför uppgraderingen

### <a name="pre-upgrade"></a>Före uppgradering

Som en del av detta, skulle du har gått igenom de *utvärdera din uppgraderingsberedskap* avsnittet och [planering för uppgradering](#planning-for-an-upgrade) avsnitt i den här guiden har du fått all information som behövs och du har Skapa en plan som uppfyller dina behov. Förmodligen har du en testning aktivitet under den här fasen.

### <a name="in-upgrade"></a>I uppgraderingen

Beroende på vilken strategi du väljer och komplexiteten i din lösning, den här fasen kan vara en kort eller en utökad där det finns flera arbetsbelastningar som väntar på att stegvis flyttas till Data Lake Storage Gen2. Det här är den viktigaste delen av uppgraderingen.

### <a name="post-upgrade"></a>Efter uppgraderingen

När du är klar med åtgärden övergången, kommer att omfatta noggrann kontroll det sista steget. Detta skulle inkludera men inte begränsas till verifiera data har kopierats över på ett tillförlitligt sätt, verifiera ACL: er har ställts in korrekt, verifiera slutpunkt till slutpunkt-pipelines fungerar som de ska osv. När kontrollerna har slutförts, kan du nu inaktivera dina gamla pipelines, ta bort källa Gen1 för Data Lake Storage-konton och gå full hastighet på dina lösningar baserade på Data Lake Storage Gen2.

## <a name="conclusion"></a>Sammanfattning

Vägledningen i det här dokumentet bör har hjälpt dig att uppgradera din lösning för att använda Data Lake Storage Gen2. 

Om du har fler frågor, eller om du har feedback, kommentarer nedan eller ge feedback i den [Azure Feedbackforum](https://feedback.azure.com/forums/327234-data-lake).
