---
title: Uppgradera Azure Data Lake Storage från gen1 till Gen2
description: Uppgradera Azure Data Lake Storage från gen1 till Gen2.
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.date: 11/19/2019
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.reviewer: rugopala
ms.openlocfilehash: 41074561b4805fef1889bd889b625e1a59d57d91
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327865"
---
# <a name="upgrade-azure-data-lake-storage-from-gen1-to-gen2"></a>Uppgradera Azure Data Lake Storage från gen1 till Gen2

Om du använder Azure Data Lake Storage Gen1 i dina Big data Analytics-lösningar hjälper den här guiden dig att uppgradera lösningarna till att använda Azure Data Lake Storage Gen2. Du kan använda det här dokumentet för att utvärdera de beroenden som din lösning har på Data Lake Storage Gen1. Den här guiden visar också hur du planerar och utför uppgraderingen.

Vi hjälper dig genom följande uppgifter:

: heavy_check_mark: utvärdera din uppgraderingsberedskap

: heavy_check_mark: Planera för en uppgradering

: heavy_check_mark: utför uppgraderingen

## <a name="assess-your-upgrade-readiness"></a>Utvärdera din uppgraderingsberedskap

Vårt mål är att alla funktioner som finns i Data Lake Storage Gen1 ska göras tillgänglig i Data Lake Storage Gen2. Hur dessa funktioner visas t.ex. i SDK, CLI osv, kan variera mellan Data Lake Storage Gen1 och Gen2 för Data Lake Storage. Program och tjänster som fungerar med Data Lake Storage Gen1 måste fungerar på samma sätt med Data Lake Storage Gen2. Slutligen några av funktionerna visas inte i Data Lake Storage Gen2 direkt. När de blir tillgängliga kommer vi presentera dem i det här dokumentet.

Dessa nästa avsnitt hjälper dig att bestämma hur du bäst att uppgradera till Data Lake Storage Gen2 och det kan vara bäst att göra detta.

### <a name="data-lake-storage-gen1-solution-components"></a>Data Lake Storage Gen1 lösningskomponenter

Mest sannolikt när du använder Data Lake Storage Gen1 Analyslösningar eller pipelines, finns det många ytterligare tekniker som du använder för att uppnå övergripande från slutpunkt till slutpunkt-funktioner. I den här [artikeln](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-data-scenarios) beskrivs olika komponenter i det data flöde som omfattar inmatning, bearbetning och användning av data.

Det finns dessutom övergripande komponenter att etablera, hantera och övervaka dessa komponenter. Var och en av komponenterna fungerar med Data Lake Storage Gen1 med hjälp av ett gränssnitt som passar bäst för dem. När du planerar att uppgradera din lösning till Data Lake Storage Gen2 måste du känna till de gränssnitt som används. Du måste först uppgradera såväl den hanteringsgränssnitt som gränssnitt för data eftersom varje gränssnitt har olika krav.

![Lösningskomponenter för data Lake-lagring](./media/data-lake-storage-upgrade/solution-components.png)

**Bild 1** ovan visar funktions komponenterna som du ser i de flesta analys lösningar.

**Bild 2** visar ett exempel på hur dessa komponenter kommer att implementeras med hjälp av en speciell teknik.

Lagrings funktionerna i **figur 1** tillhandahålls av data Lake Storage gen1 (**bild 2**). Observera hur de olika komponenterna i dataflödet interagera med Data Lake Storage Gen1 med hjälp av REST API: er eller SDK för Java. Tänk också på hur de övergripande funktioner komponenterna samverkar med Data Lake Storage Gen1. Etablerings komponenten använder Azure-resursfiler, medan övervaknings komponenten som använder Azure Monitor loggar använder sig av drift data som kommer från Data Lake Storage Gen1.

Om du vill uppgradera en lösning från att använda Data Lake Storage Gen1 till Data Lake Storage Gen2, måste du kopiera data och metadata, nytt koppla dataflöden och sedan alla komponenter behöver för att kunna arbeta med Data Lake Storage Gen2.

I avsnitten nedan innehåller information om hur du kan fatta bättre beslut:

: heavy_check_mark: plattformsfunktioner

: heavy_check_mark: programmeringsgränssnitt

: heavy_check_mark: Azure-ekosystemet

: heavy_check_mark: partnerekosystem

: heavy_check_mark: teknisk information

I varje avsnitt kommer du att kunna fastställa de ”måste-haves” för uppgraderingen. När du är säker på att funktionerna är tillgängliga, eller om du är säker på att det finns rimliga lösningar på plats, går du vidare till avsnittet [Planera en uppgradering](#planning-for-an-upgrade) i den här guiden.

### <a name="platform-capabilities"></a>Plattformsfunktioner

Det här avsnittet beskriver vilka Data Lake Storage Gen1 plattformsfunktioner som är tillgängliga i Data Lake Storage Gen2.

| | Data Lake Storage Gen1 | Data Lake Storage Gen2 - mål | Data Lake Storage Gen2 - tillgänglighetsstatus  |
|-|------------------------|-------------------------------|-----------------------------------------------|
| Dataorganisation| Stöd för data som lagras som mappar och filer | Har stöd för data som lagras som objekt/blobbar samt mappar och filer – [länk](https://docs.microsoft.com/azure/storage/data-lake-storage/namespace) | Har stöd för data som lagras som mappar och fil – *tillgängligt nu* <br><br> Har stöd för data som lagras som objekt/blobbar – *ännu inte tillgänglig* |
| Namnrymd| Hierarkisk | Hierarkisk |  *Tillgängligt nu*  |
| API  | REST-API via HTTPS | REST-API via HTTP/HTTPS| *Tillgängligt nu* |
| API för serversidan| [WebHDFS-kompatibel REST API](https://msdn.microsoft.com/library/azure/mt693424.aspx) | Azure Blob service REST API [Data Lake Storage Gen2 REST API](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2) | Data Lake Storage Gen2 REST API – *tillgängligt nu* <br><br> Azure Blob service REST API – *tillgängligt nu*       |
| Hadoop-klienten för System | Ja ([Azure Data Lake Storage](https://hadoop.apache.org/docs/current/hadoop-azure-datalake/index.html)) | Ja ([ABFS](https://jira.apache.org/jira/browse/HADOOP-15407))  | *Tillgängligt nu*  |  
| Dataåtgärder – auktorisering  | Fil- och nivå POSIX åtkomstkontrollistor (ACL) baserat på Azure Active Directory-identiteter  | Fil-och mappnivå POSIX Access Control listor (ACL: er) baserat på Azure Active Directory identiteter [delnings nyckel](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key) för rollbaserade Access Control[](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac)för konto nivå för att komma åt behållare | *Tillgängligt nu* |
| Dataåtgärder – loggar  | Ja | Ja | *Tillgängligt nu* (för hands version). Se [kända problem](data-lake-storage-known-issues.md).<br><br> Azure Monitoring integration – *ännu inte tillgänglig* |
| Vilande krypteringsdata | Transparent, på serversidan med tjänst-hanterade nycklar och med Kundhanterade nycklar i Azure Key | Transparent, på serversidan med tjänst-hanterade nycklar och med Kundnycklar hanterade nycklar i Azure KeyVault | Tjänst-hanterade nycklar – *tillgängligt nu*<br><br> Kundhanterade nycklar – *tillgängligt nu*  |
| Management-åtgärder (t.ex. Account Create) | [Rollbaserad åtkomst kontroll](https://docs.microsoft.com/azure/role-based-access-control/overview) (RBAC) som tillhandahålls av Azure för konto hantering | [Rollbaserad åtkomst kontroll](https://docs.microsoft.com/azure/role-based-access-control/overview) (RBAC) som tillhandahålls av Azure för konto hantering | *Tillgängligt nu*|
| SDK: er för utvecklare | .NET, Java, Python, Node.js  | .NET, Java, Python, Node.js, C++, Ruby, PHP, Go, Android, iOS| BLOB SDK – *tillgängligt nu*. Azure Data Lake Storage Gen2 SDK – *inte tillgängligt ännu*  |
| |Optimerad prestanda för arbetsbelastningar för parallella analyser. Högt dataflöde och IOPS. | Optimerad prestanda för arbetsbelastningar för parallella analyser. Högt dataflöde och IOPS. | *Tillgängligt nu* |
| Stöd för Virtual Network (VNet)  | [Använda Virtual Network-integrering](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-network-security)  | [Använda tjänst slut punkten för Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | *Tillgängligt nu* |
| Storleksbegränsningar | Inga gränser för kontostorlekar, filstorlekar eller antal filer | Inga gränser för kontostorlekar eller många filer. Filstorleken är begränsad till 5TB. | *Tillgängligt nu*|
| GEO-redundans| Lokalt redundant (LRS) | Lokalt redundant (LRS) zon redundant (ZRS) Geo-redundant (GRS) Read-Access Geo-redundant (RA-GRS) finns [här](https://docs.microsoft.com/azure/storage/common/storage-redundancy) för mer information| *Tillgängligt nu* |
| Regional tillgänglighet | Se [här](https://azure.microsoft.com/regions/) | Alla [Azure-regioner](https://azure.microsoft.com/global-infrastructure/regions/)                                                                                                                                                                                                                                                                                                                                       | *Tillgängligt nu*                                                                                                                           |
| Pris                                       | Se [prissättning](https://azure.microsoft.com/pricing/details/data-lake-store/)                                                                            | Se [prissättning](https://azure.microsoft.com/pricing/details/storage/data-lake/)                                                                                                                                                                                                                                                                                                                                         |                                                                                                                                           |
| Serviceavtal finns                            | [Se SLA](https://azure.microsoft.com/support/legal/sla/data-lake-store/v1_0/)                                                                   | [Se SLA](https://azure.microsoft.com/support/legal/sla/storage/v1_3/)                                                                                                                                                                                                                                                                                                                                                | *Tillgängligt nu*                                                                                                                           |
| Databearbetning                             | Förfallodatum för filer                                                                                                                                        | Principer för livscykel                                                                                                                                                                                                                                                                                                                                                                                                          | Livs cykel principer *finns nu* (för hands version). Se [kända problem](data-lake-storage-known-issues.md).                                                                                                                     |

### <a name="programming-interfaces"></a>Programmeringsgränssnitt

Den här tabellen beskriver som API som är tillgängliga för dina anpassade program. SDK-stöd för ACL-och katalog nivå åtgärder stöds inte ännu.

|  API: n                           |  Data Lake Storage Gen1                                                                                                                                                                                                                                                                                                   | Tillgänglighet för Data Lake Storage Gen2 - med autentisering med delad nyckel | Tillgänglighet för Data Lake Storage Gen2 - med OAuth-autentisering                                                                                                  |
|----------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| .NET SDK                  | [Länk](https://docs.microsoft.com/dotnet/api/overview/azure/datalakestore/management?view=azure-dotnet) | *Tillgängligt nu* | *Tillgängligt nu* |
| Java SDK                | [Länk](https://docs.microsoft.com/java/api/overview/azure/datalakestore/management)                                                                                                                                                                                                                                     | *Tillgängligt nu*                                                      | *Tillgängligt nu*                                     |
| Node.js                | [Länka](https://www.npmjs.com/package/azure-arm-datalake-store)                                                                                                                                                                                                                                                                | *Tillgängligt nu*                                                     | *Tillgängligt nu*                                                                                           |
| Python SDK                   | [Länka](https://docs.microsoft.com/python/api/overview/azure/datalakestore/management?view=azure-python)                                                                                                                                                                                                                 | *Tillgängligt nu*                                                      | *Tillgängligt nu*                                        |
| REST-API                 | [Länk](https://docs.microsoft.com/rest/api/datalakestore/accounts)                                                                                                                                                                                                                                                      | *Tillgängligt nu*                                                      | *Tillgängligt nu*                                                                                                                                               |
| PowerShell | [Länk](https://docs.microsoft.com/powershell/module/az.datalakestore)                                                                                                                                                                                                                        | *Tillgängligt nu* |
| CLI                 | [Länk](https://docs.microsoft.com/cli/azure/dls/account?view=azure-cli-latest)                                                                                                                                                                                                                                          | *Tillgängligt nu*                                                      | *Tillgängligt nu*                                                               |
| Azure Resource Manager-mallar - hantering             | [Template1](https://azure.microsoft.com/resources/templates/101-data-lake-store-no-encryption/)  [Template2](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-adls/)  [Template3](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-key-vault/)  | *Tillgängligt nu*                                                      | *Tillgängligt nu*                                          |

### <a name="azure-ecosystem"></a>Azure-ekosystemet

När du använder Data Lake Storage Gen1, kan du använda olika Microsoft-tjänster och produkter i pipelines slutpunkt till slutpunkt. Arbeta med Data Lake Storage Gen1 direkt eller indirekt dessa produkter och tjänster. Den här tabellen visas en lista över tjänsterna vi har ändrat för att arbeta med Data Lake Storage Gen1 och visar vilka som är kompatibel med Data Lake Storage Gen2.

| **Område**             | **Tillgänglighet för Data Lake Storage Gen1**                                                                                                                                    | **Tillgänglighet för Data Lake Storage Gen2 – med autentisering med delad nyckel**                                                                                                           | **Tillgänglighet för Data Lake Storage Gen2 – med OAuth**                                                                                        |
|----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|
| Analytics-ramverk  | [Apache Hadoop](https://hadoop.apache.org/docs/current/hadoop-azure-datalake/index.html)                                                                                       | *Tillgängligt nu*                                                                                                                                                              | *Tillgängligt nu*                                                                                                                                 |
|                      | [HDInsight](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-hdinsight-hadoop-use-portal)                                                               | [Hdinsight](https://docs.microsoft.com/azure/storage/data-lake-storage/quickstart-create-connect-hdi-cluster) 3,6 – *tillgängligt nu* HDInsight 4,0 – *ännu inte tillgängligt*      | HDInsight 3,6 ESP – *tillgängligt nu* <br><br>  HDInsight 4,0 ESP – *ännu inte tillgängligt*                                                                 |
|                      | Databricks Runtime 3.1 och senare                                                                                                                                               | [Databricks Runtime 5,1 och senare](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html#azure-data-lake-storage-gen2) *– tillgängligt nu* | [Databricks Runtime 5,1 och senare](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html#azure-data-lake-storage-gen2) – *tillgängligt nu*                                                                                              |
|                      | [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store)                                            | *Stöds inte*                                                                                                                                                              | [Länk](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql?view=sql-server-2017) *tillgänglig nu* |
| Dataintegrering     | [Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-store)                                                                                | [Version 2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage) – *tillgänglig nu* version 1 – *stöds inte*                               | [Version 2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage) – *tillgänglig nu* <br><br> Version 1 – *stöds inte*  |
|                      | [AdlCopy](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)                                                                 | *Stöds inte*                                                                                                                                                              | *Stöds inte*                                                                                                                                 |
|                      | [SQL Server Integration Services](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager?view=sql-server-2017) | *Tillgängligt nu*                                                                                                                                                          | *Tillgängligt nu*                                                                                                                             |
|                      | [Data Catalog](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-with-data-catalog)                                                                       | *Ännu inte tillgängligt*                                                                                                                                                          | *Ännu inte tillgängligt*                                                                                                                             |
|                      | [Logic Apps](https://docs.microsoft.com/connectors/azuredatalake/)                                                                                                      | *Tillgängligt nu*                                                                                                                                                          | *Tillgängligt nu*                                                                                                                             |
| IoT                  | [Event Hubs – avbildning](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-archive-eventhub-capture)                                                       | *Tillgängligt nu*                                                                                                                                                          | *Tillgängligt nu*                                                                                                                             |
|                      | [Stream Analytics](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-stream-analytics)                                                                   | *Tillgängligt nu*                                                                                                                                                          | *Tillgängligt nu*                                                                                                                             |
| Förbrukning          | [Power BI Desktop](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-power-bi)                                                                           | *Tillgängligt nu*                                                                                                                                                          | *Tillgängligt nu*                                                                                                                             |
|                      | [Excel](https://techcommunity.microsoft.com/t5/Excel-Blog/Announcing-the-Azure-Data-Lake-Store-Connector-in-Excel/ba-p/91677)                                                 | *Ännu inte tillgängligt*                                                                                                                                                          | *Ännu inte tillgängligt*                                                                                                                             |
|                      | [Analysis Services](https://blogs.msdn.microsoft.com/analysisservices/2017/09/05/using-azure-analysis-services-on-top-of-azure-data-lake-storage/)                            | *Ännu inte tillgängligt*                                                                                                                                                          | *Ännu inte tillgängligt*                                                                                                                             |
| Produktivitet         | [Azure Portal](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal)                                                                      | *Stöds inte*                                                                                                                                                              | Konto hantering *– tillgängligt nu* <br><br>Data åtgärder *–*  *ännu inte tillgängligt*                                                                   |
|                      | [Data Lake verktyg för Visual Studio](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-data-lake-tools-install)                                   | *Ännu inte tillgängligt*                                                                                                                                                          | *Ännu inte tillgängligt*                                                                                                                             |
|                      | [Azure Storage Explorer](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-in-storage-explorer)                                                          | *Tillgängligt nu*                                                                                                                                                              | *Tillgängligt nu*                                                                                                                                 |
|                      | [Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=usqlextpublisher.usql-vscode-ext)                                                                     | *Ännu inte tillgängligt*                                                                                                                                                          | *Ännu inte tillgängligt*                                                                                                                             |

### <a name="partner-ecosystem"></a>Partnerekosystem

Den här tabellen visas en lista över tjänster från tredje part och produkter som har ändrats för att arbeta med Data Lake Storage Gen1. Den visar även vilka som är kompatibel med Data Lake Storage Gen2.

| Område            | Partner  | Produkt/tjänst  | Tillgänglighet för Data Lake Storage Gen1                                                                                                                                               | Tillgänglighet för Data Lake Storage Gen2 – med autentisering med delad nyckel                                                   | Tillgänglighet för Data Lake Storage Gen2 – med Oauth                                                             |
|---------------------|--------------|----------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------|
| Analytics-ramverk | Cloudera     | CDH                  | [Länk](https://www.cloudera.com/documentation/enterprise/5-11-x/topics/admin_adls_config.html)                                                                                            | *Ännu inte tillgängligt*                                                                                                  | [Länk](https://www.cloudera.com/documentation/enterprise/latest/topics/admin_adls2_config.html)                                                                                                  |
|                     | Cloudera     | Altus                | [Länk](https://www.cloudera.com/more/news-and-blogs/press-releases/2017-09-27-cloudera-introduces-altus-data-engineering-microsoft-azure-hybrid-multi-cloud-data-analytic-workflows.html) | *Stöds inte*                                                                                                                  | *Ännu inte tillgängligt*                                                                                                  |
|                     | HortonWorks  | HDP 3.0              | [Länka](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.3/bk_cloud-data-access/content/adls-get-started.html)                                                                       | *Ännu inte tillgängligt*                                                                                                  | *Ännu inte tillgängligt*                                                                                                  |
|                     | Qubole       |                      | [Länk](https://www.qubole.com/blog/big-data-analytics-microsoft-azure-data-lake-store-qubole/)                                                                                            | *Ännu inte tillgängligt*                                                                                                  | *Ännu inte tillgängligt*                                                                                                  |
| ETL                 | StreamSets   |                      | [Länk](https://streamsets.com/blog/ingest-data-azure)                                                                                                                                     | *Ännu inte tillgängligt*                                                                                                  | *Ännu inte tillgängligt*                                                                                                  |
|                     | Informatica  |                      | [Länk](https://kb.informatica.com/proddocs/Product%20Documentation/6/IC_Spring2017_MicrosoftAzureDataLakeStoreV2ConnectorGuide_en.pdf)                                                    | *Ännu inte tillgängligt*                                                                                                  | *Ännu inte tillgängligt*                                                                                                  |
|                     | Attunity     |                      | [Länk](https://www.attunity.com/company/press-releases/microsoft-and-attunity-announce-strategic-partnership-for-data-migration/)                                                         | *Ännu inte tillgängligt*                                                                                                  | *Ännu inte tillgängligt*                                                                                                  |
|                     | Alteryx      |                      | [Länk](https://help.alteryx.com/2018.2/DataSources/AzureDataLake.htm)                                                                                                                     | *Ännu inte tillgängligt*                                                                                                  | *Ännu inte tillgängligt*                                                                                                  |
|                     | ImanisData   |                      | [Länk](https://www.imanisdata.com/expansion-azure-support-azure-data-lake-store-integration/)                                                                                             | *Ännu inte tillgängligt*                                                                                                  | *Ännu inte tillgängligt*                                                                                                  |
|                     | WANdisco     |                      | [Länk](https://azure.microsoft.com/blog/globally-replicated-data-lakes-with-livedata-using-wandisco-on-azure/)                                                                      | [Länk](https://azure.microsoft.com/blog/globally-replicated-data-lakes-with-livedata-using-wandisco-on-azure/) | [Länk](https://azure.microsoft.com/blog/globally-replicated-data-lakes-with-livedata-using-wandisco-on-azure/) |

### <a name="operational-information"></a>Funktionsinformation

Data Lake Storage Gen1 skickar specifik information och data till andra tjänster som hjälper dig att operationalisera dina pipelines. Den här tabellen visar tillgängligheten för motsvarande stöd i Data Lake Storage Gen2.

| Typ av data                                                                                       | Tillgänglighet för Data Lake Storage Gen1                                                                 | Tillgänglighet för Data Lake Storage Gen2                                                                                               |
|--------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------|
| Faktureringsdata - mätare som skickas till commerce team för fakturering och sedan görs tillgängliga för kunder  | *Tillgängligt nu*                                                                                             | *Tillgängligt nu*                                                                                                                           |
| Aktivitetsloggar                                                                                          | [Länk](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs#audit-logs)   | En-off-begäran för loggar för en speciell varaktighet med support ärende – *tillgängligt nu* Azure Monitoring integration – *ännu inte tillgänglig* |
| Diagnostikloggar                                                                                        | [Länk](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs#request-logs) | *Tillgängligt nu* (för hands version). Se [kända problem](data-lake-storage-known-issues.md). <br>Azure Monitoring-integrering – *ännu inte tillgänglig* |
| Mått                                                                                                | *Stöds inte*                                                                                               | *Tillgänglig nu-* [länk](https://docs.microsoft.com/azure/storage/common/storage-metrics-in-azure-monitor)                          |

## <a name="planning-for-an-upgrade"></a>Planera för en uppgradering

Det här avsnittet förutsätter att du har granskat avsnittet [utvärdera ditt uppgraderings beredskap](#assess-your-upgrade-readiness) i den här guiden och att alla dina beroenden är uppfyllda. Om det finns funktioner som är fortfarande är inte tillgänglig i Data Lake Storage Gen2, Fortsätt bara om du känner till motsvarande lösningar om detta. Följande avsnitt innehåller vägledning i hur du kan planera inför uppgraderingen av dina pipelines. Att utföra den faktiska uppgraderingen beskrivs i avsnittet [utföra uppgradering](#performing-the-upgrade) i den här guiden.

### <a name="upgrade-strategy"></a>Uppgradera strategi

Den viktigaste delen av uppgraderingen är att bestämma vilken strategi. Det här beslutet avgör alternativ som är tillgängliga.

I den här tabellen listas några välkända strategier som har använts för att migrera databaser, Hadoop-kluster osv. Vi antar liknande strategier i vår vägledning och anpassar dem till vår kontext.

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

Den övergripande strategin som du använder för att utföra uppgraderingen (beskrivs i avsnittet om [uppgraderings strategi](#upgrade-strategy) i den här hand boken) avgör vilka verktyg du kan använda för din data uppgradering. De verktyg som anges nedan är baserade på aktuell information och är förslag. 

#### <a name="tools-guidance"></a>Verktyg vägledning

| Strategi                       | Verktyg                                                                                                             | Experter                                                                                                                             | Överväganden                                                                                                                                                                                                                                                                                                                |
|------------------------------------|-----------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Lyft-och-Shift**                 | [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2-from-gen1) | Hanterad molntjänst                                                                                                                | Både data och ACL: er kan kopieras över för närvarande.                                                                                                                                                                                                                                                                      |
|                                    | [Distcp](https://hadoop.apache.org/docs/r1.2.1/distcp.html)                                                           | Välkända Hadoop-angivna verktyget behörigheter d.v.s. ACL: er kan kopieras till det här verktyget                                                   | Kräver ett kluster som kan ansluta till både Data Lake Storage Gen1 och Gen2 på samma gång.                                                                                                                                                                                   |
| **Kopiera en gång och kopiera stegvis** | Azure Data Factory                                                                                                    | Hanterad molntjänst                                                                                                                | Både data och ACL: er kan kopieras över för närvarande. För att stödja inkrementell kopiering i ADF, behöver data ordnas i en time series-sätt. Det kortaste intervallet mellan stegvisa kopior är [15 minuter](https://docs.microsoft.com/azure/data-factory/how-to-create-tumbling-window-trigger). |
| **Parallellt införande**              | [WANdisco](https://docs.wandisco.com/bigdata/wdfusion/adls/)                                                           | Stöd för replikeringsgruppen om med en ren Hadoop-miljö ansluten till Azure Data Lake Storage har stöd för dubbelriktad replikering | Om du inte använder en ren Hadoop-miljö kan finnas det en fördröjning i replikeringen.                                                                                                                                                                                                                                                  |

Observera att det finns tredje parter som kan hantera Data Lake Storage Gen1 för att Data Lake Storage Gen2 uppgraderingen utan att du behöver använda kopierings verktygen ovan för data/meta-data (till exempel: [Cloudera](https://blog.cloudera.com/blog/2017/08/use-amazon-s3-with-cloudera-bdr/)). De tillhandahåller en ”affär”-upplevelse som utför migrering av data samt migrering av arbetsbelastning. Du kan behöva utföra en uppgradering på out-of-band för verktyg som finns utanför sina ekosystem.

#### <a name="considerations"></a>Överväganden

* Du behöver manuellt skapa Gen2 för Data Lake Storage-konto innan du börjar någon del av uppgraderingen, eftersom de aktuella riktlinjerna inte inkluderar en automatisk Gen2 konto process baserat på din kontoinformation för Gen1. Se till att du jämför processerna för att skapa konton för [gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal) och [Gen2](https://docs.microsoft.com/azure/storage/data-lake-storage/quickstart-create-account).

* Data Lake Storage Gen2 filer endast stöd för upp till 5 TB i storlek. Om du vill uppgradera till Data Lake Storage Gen2 kan du behöva ändra storlek på filerna i Data Lake Storage Gen1 så att de är mindre än 5 TB i storlek.

* Om du använder ett verktyg som inte kopierar ACL: er eller om du inte vill kopiera över ACL: er så kommer du behöver ange ACL: er på målet manuellt på den översta nivån som är lämpliga. Du kan göra det genom att använda Storage Explorer. Kontrollera att dessa ACL: er är standard-ACL: er så att de filer och mappar som du kopierar du över ärver dem.

* I Data Lake Storage Gen1 är den högsta nivån som du kan ange ACL: er i roten för kontot. I Data Lake Storage Gen2 är det dock högsta nivån som du kan ställa in ACL: er i rotmappen i en behållare, inte hela kontot. Om du vill använda standard-ACL: er på kontonivå, måste du därför att duplicera de över alla filsystem i ditt Data Lake Storage Gen2-konto.

* Filen namngivningsbegränsningar skiljer sig mellan de två systemen för lagring. Dessa skillnader särskilt om när du kopierar från Data Lake Storage Gen2 till Data Lake Storage Gen1 eftersom det senare har mer begränsad begränsningar.

### <a name="application-upgrade"></a>Programuppgradering

När du vill skapa program på Data Lake Storage Gen1 eller Data Lake Storage Gen2 måste du först välja en lämplig programmeringsgränssnitt. När du anropar en API för det gränssnittet får att tillhandahålla lämpliga URI: N och rätt autentiseringsuppgifter. En representation av dessa tre delar, API, URI, och hur autentiseringsuppgifter tillhandahålls, skiljer sig åt mellan Data Lake Storage Gen1 och Data Lake Storage Gen2.So som en del av uppgradera programmet, måste du mappa dessa tre konstruktioner på rätt sätt.

#### <a name="uri-changes"></a>URI-ändringar

Huvud uppgiften här är att översätta URI: er som har prefixet `adl://` till URI: er som har ett `abfss://`-prefix.

URI-schemat för Data Lake Storage Gen1 anges [här](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-store) i detalj, men det är *adl://mydatalakestore.azuredatalakestore.net/\<FILE_PATH\>.*

URI-schemat för att komma åt Data Lake Storage Gen2-filer förklaras [här](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md) i detalj, men är i stort sett `abfss://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`.

Du behöver gå igenom dina befintliga program och se till att du har ändrat URI: er på rätt sätt för att peka till Data Lake Storage Gen2 sådana. Dessutom måste du lägga till rätt autentiseringsuppgifter. Slutligen måste hur du dra tillbaka de ursprungliga program och Ersätt med det nya programmet justeras nära till din strategi för övergripande uppgradering.

#### <a name="custom-applications"></a>Anpassade program

Beroende på gränssnitt som används i ditt program med Data Lake Storage Gen1, behöver du ändra det om du vill anpassa det till Data Lake Storage Gen2.

##### <a name="rest-apis"></a>REST-API:er

Om programmet använder Data Lake Storage REST API: er, måste du ändra ditt program att använda Data Lake Storage Gen2 REST API: erna. Det finns länkar i avsnittet *Programming Interfaces* .

##### <a name="sdks"></a>SDK:er

Som det visas i avsnittet *utvärdera ditt uppgraderings beredskap* är SDK: er för närvarande inte tillgängliga. Om du vill att en port över dina program ska Data Lake Storage Gen2, rekommenderar vi att du väntar på att SDK: er som stöds är tillgängliga.

##### <a name="powershell"></a>PowerShell

Som anropas i utvärdera uppgraderingsberedskap-avsnittet, är PowerShell-stöd inte tillgänglig för dataplanet.

Du kan ersätta management plan commandlets med lämpliga dem i Data Lake Storage Gen2. Det finns länkar i avsnittet *Programming Interfaces* .

##### <a name="cli"></a>CLI

Som det visas i avsnittet *utvärdera ditt uppgraderings beredskap* är CLI-stödet för närvarande inte tillgängligt för data planet.

Du kan ersätta kommandon för hantering av plan med lämpliga dem i Data Lake Storage Gen2. Det finns länkar i avsnittet *Programming Interfaces* .

### <a name="analytics-frameworks-upgrade"></a>Uppgradera Analytics ramverk

Om ditt program skapar metadata om data i arkivet som explicit fil- och mappsökvägar, måste du utföra ytterligare åtgärder när du uppgraderar du lagra data/meta-data. Detta gäller särskilt för analytics ramverk som Azure HDInsight osv. Databricks, som vanligtvis skapar katalogdata på store-data.

Analytics-ramverk fungerar med data och metadata som lagras i remote-Arkiv som Data Lake Storage Gen1 och Gen2. Så i teorin motorerna kan vara tillfälliga och göras tillgänglig endast när jobb behöver köra mot lagrade data.

Men för att optimera prestanda kan analytics-ramverk Skapa explicita referenser till de filer och mappar som lagras i arkivet för fjärråtkomst och sedan skapa ett cacheminne för att lagra dem. URI för remote data ändras, till exempel ett kluster som lagrar data i Data Lake Storage Gen1 tidigare och nu vill lagra i Data Lake Storage Gen2 är URI för samma kopierade innehåll olika. Så när data och metadata har uppgraderat cacheminnen för dessa motorer också behöva uppdateras eller initieras igen

Som en del av planeringsprocessen måste du identifiera ditt program och ta reda på hur metadata-information kan startas så att den pekar till data som lagras nu på Data Lake Storage Gen2. Nedan finns anvisningar om hur ofta antagen analytics ramverk som hjälper dig med sina uppgradering.

#### <a name="azure-databricks"></a>Azure Databricks

Stegen varierar beroende på vilken uppgradera strategi som du väljer. Det aktuella avsnittet förutsätter att du har valt ”liv-and-shift”-strategi. Dessutom förväntas i Databricks-arbetsytan som används för åtkomst till data i ett Data Lake Storage Gen1 konto att arbeta med de data som kopieras över till Gen2 för Data Lake Storage-konto.

Kontrollera först att du har skapat kontot Gen2 och sedan kopieras över data och metadata från Gen1 till Gen2 med hjälp av ett lämpligt verktyg. Dessa verktyg kallas i avsnittet [data uppgradering](#data-upgrade) i den här guiden.

[Uppgradera](https://docs.azuredatabricks.net/user-guide/clusters/index.html) sedan ditt befintliga Databricks-kluster för att börja använda Databricks runtime 5,1 eller högre, vilket bör stödja data Lake Storage Gen2.

Stegen baseras därefter på hur den befintliga Databricks-arbetsytan har åtkomst till data i Data Lake Storage Gen1-konto. Det kan göras antingen genom att anropa adl://-URI: er [direkt](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html#access-azure-data-lake-store-directly) från antecknings böcker eller via [mountpoints](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html#mount-azure-data-lake-store-with-dbfs).

Om du ansluter direkt från bärbara datorer genom att tillhandahålla fullständig adl: / / URI: er, måste du gå igenom varje anteckningsboken och ändra konfigurationen för att komma åt motsvarande Data Lake Storage Gen2 URI.

Framöver kommer måste du konfigurera om den så att den pekar till Gen2 för Data Lake Storage-konto. Inga fler ändringar krävs och de bärbara datorerna ska kunna fungera som tidigare.

Om du använder någon av de andra strategierna för uppgraderingen kan skapa du en variant av ovanstående steg som uppfyller dina krav.

### <a name="azure-ecosystem-upgrade"></a>Azure-ekosystemet uppgradering

Vart och ett av verktygen och tjänsterna i avsnittet [Azure eko system](#azure-ecosystem) i den här hand boken måste konfigureras för att fungera med data Lake Storage Gen2.

Först måste se till att det finns integrering med Data Lake Storage Gen2.

Sedan elementen som beskrivs ovan (till exempel: URI och autentiseringsuppgifter), måste ändras. Du kan ändra den befintliga instansen som fungerar med Data Lake Storage Gen1 eller du kan skapa en ny instans som skulle fungera med Data Lake Storage Gen2.

### <a name="partner-ecosystem-upgrade"></a>Partner ekosystem uppgradering

Kontakta den partner som tillhandahåller komponenten och verktyg för att se till att de kan arbeta med Data Lake Storage Gen2. 

## <a name="performing-the-upgrade"></a>Utför uppgraderingen

### <a name="pre-upgrade"></a>Före uppgradering

Som en del av detta skulle du ha gått igenom avsnittet *utvärdera uppgraderings beredskap* och avsnittet [Planera för en uppgradering](#planning-for-an-upgrade) i den här guiden. du har fått all nödvändig information och du har skapat en plan som passar dina behov. Förmodligen har du en testning aktivitet under den här fasen.

### <a name="in-upgrade"></a>I uppgraderingen

Beroende på vilken strategi du väljer och komplexiteten i din lösning, den här fasen kan vara en kort eller en utökad där det finns flera arbetsbelastningar som väntar på att stegvis flyttas till Data Lake Storage Gen2. Det här är den viktigaste delen av uppgraderingen.

### <a name="post-upgrade"></a>Efter uppgraderingen

När du är klar med åtgärden övergången, kommer att omfatta noggrann kontroll det sista steget. Detta omfattar men är inte begränsat till att verifiera data har kopierats på ett tillförlitligt sätt, kontrol lera att ACL: er har ställts in korrekt, verifiera att pipelinen från slut punkt till slut punkt fungerar korrekt osv. När verifieringen har slutförts kan du nu stänga av dina gamla pipeliner, ta bort dina käll Data Lake Storage Gen1s konton och gå med i full hastighet på Data Lake Storage Gen2-baserade lösningar.

## <a name="conclusion"></a>Sammanfattning

Vägledningen i det här dokumentet bör har hjälpt dig att uppgradera din lösning för att använda Data Lake Storage Gen2. 

Om du har fler frågor, eller om du har feedback, anger du kommentarer nedan eller ger feedback i [Azure feedback-forumet](https://feedback.azure.com/forums/327234-data-lake).
