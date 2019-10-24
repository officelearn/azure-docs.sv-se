---
title: Uppgradera dina Big data Analytics-lösningar från Azure Data Lake Storage Gen1 till Azure Data Lake Storage Gen2
description: Uppgradera din lösning för att använda Azure Data Lake Storage Gen2
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.date: 02/07/2019
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.reviewer: rugopala
ms.openlocfilehash: 27d752b8ff7eafbb92930b19e17890ace8a90b85
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/22/2019
ms.locfileid: "72750433"
---
# <a name="upgrade-your-big-data-analytics-solutions-from-azure-data-lake-storage-gen1-to-azure-data-lake-storage-gen2"></a>Uppgradera dina Big data Analytics-lösningar från Azure Data Lake Storage Gen1 till Azure Data Lake Storage Gen2

Om du använder Azure Data Lake Storage Gen1 i dina Big data Analytics-lösningar hjälper den här guiden dig att uppgradera lösningarna till att använda Azure Data Lake Storage Gen2. Du kan använda det här dokumentet för att utvärdera de beroenden som din lösning har på Data Lake Storage Gen1. Den här guiden visar också hur du planerar och genomför uppgraderingen.

Vi hjälper dig med följande uppgifter:

: heavy_check_mark: Bedöm din uppgraderings beredskap

: heavy_check_mark: planera för en uppgradering

: heavy_check_mark: utför uppgraderingen

## <a name="assess-your-upgrade-readiness"></a>Utvärdera uppgraderings beredskap

Vårt mål är att alla funktioner som finns i Data Lake Storage Gen1 kommer att göras tillgängliga i Data Lake Storage Gen2. Hur dessa funktioner exponeras, t. ex. i SDK: n, kan vara olika mellan Data Lake Storage Gen1 och Data Lake Storage Gen2. Program och tjänster som fungerar med Data Lake Storage Gen1 behöver kunna fungera på samma sätt med Data Lake Storage Gen2. Slutligen är vissa av funktionerna inte tillgängliga i Data Lake Storage Gen2 direkt. När de blir tillgängliga meddelar vi dem i det här dokumentet.

I följande avsnitt får du hjälp med att bestämma hur du ska uppgradera till Data Lake Storage Gen2 och när det är mest viktigt att göra det.

### <a name="data-lake-storage-gen1-solution-components"></a>Komponenter för Data Lake Storage Gen1 lösning

Det vanligaste är att när du använder Data Lake Storage Gen1 i dina analys lösningar eller pipeliner finns det många ytterligare tekniker som du kan använda för att uppnå de övergripande funktionerna från slut punkt till slut punkt. I den här [artikeln](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-data-scenarios) beskrivs olika komponenter i det data flöde som omfattar inmatning, bearbetning och användning av data.

Dessutom finns det Ban oberoende komponenter för att etablera, hantera och övervaka dessa komponenter. Var och en av komponenterna fungerar med Data Lake Storage Gen1 med hjälp av ett gränssnitt som passar bäst för dem. När du planerar att uppgradera din lösning till Data Lake Storage Gen2 måste du vara medveten om de gränssnitt som används. Du måste uppgradera både hanterings gränssnitten och data gränssnitt eftersom varje gränssnitt har särskilda krav.

![Komponenter för Data Lake Storage lösning](./media/data-lake-storage-upgrade/solution-components.png)

**Bild 1** ovan visar funktions komponenterna som du ser i de flesta analys lösningar.

**Bild 2** visar ett exempel på hur dessa komponenter kommer att implementeras med hjälp av en speciell teknik.

Lagrings funktionerna i **figur 1** tillhandahålls av data Lake Storage gen1 (**bild 2**). Observera hur de olika komponenterna i data flödet interagerar med Data Lake Storage Gen1 med hjälp av REST API: er eller Java SDK. Observera också hur funktionerna för att korsa överstyckningen fungerar med Data Lake Storage Gen1. Etablerings komponenten använder Azure-resursfiler, medan övervaknings komponenten som använder Azure Monitor loggar använder sig av drift data som kommer från Data Lake Storage Gen1.

Om du vill uppgradera en lösning från att använda Data Lake Storage Gen1 för att Data Lake Storage Gen2 måste du kopiera data och meta-data, återskapa data strömmarna och sedan måste alla komponenter kunna arbeta med Data Lake Storage Gen2.

Avsnitten nedan innehåller information som hjälper dig att fatta bättre beslut:

: heavy_check_mark: plattforms funktioner

: heavy_check_mark: programmerings gränssnitt

: heavy_check_mark: Azure-eko system

: heavy_check_mark: partner eko system

: heavy_check_mark: drift information

I varje avsnitt kan du kontrol lera att "måste ha" för uppgraderingen. När du är säker på att funktionerna är tillgängliga, eller om du är säker på att det finns rimliga lösningar på plats, går du vidare till avsnittet [Planera en uppgradering](#planning-for-an-upgrade) i den här guiden.

### <a name="platform-capabilities"></a>Plattformsfunktioner

I det här avsnittet beskrivs vilka Data Lake Storage Gen1 plattforms funktioner som för närvarande är tillgängliga i Data Lake Storage Gen2.

| | Data Lake Storage Gen1 | Data Lake Storage Gen2-mål | Data Lake Storage Gen2-tillgänglighets status  |
|-|------------------------|-------------------------------|-----------------------------------------------|
| Data organisation| Har stöd för data som lagras som mappar och filer | Har stöd för data som lagras som objekt/blobbar samt mappar och filer – [länk](https://docs.microsoft.com/azure/storage/data-lake-storage/namespace) | Har stöd för data som lagras som mappar och fil – *tillgängligt nu* <br><br> Har stöd för data som lagras som objekt/blobbar – *ännu inte tillgänglig* |
| Namnområde| Nyckelordshierarki | Nyckelordshierarki |  *Tillgängligt nu*  |
| API  | REST API över HTTPS | REST API över HTTP/HTTPS| *Tillgängligt nu* |
| API för Server Sidan| [WebHDFS-kompatibel REST API](https://msdn.microsoft.com/library/azure/mt693424.aspx) | Azure Blob service REST API [Data Lake Storage Gen2 REST API](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2) | Data Lake Storage Gen2 REST API – *tillgängligt nu* <br><br> Azure Blob service-REST API – *ännu inte tillgänglig*       |
| Hadoop-filsystem-klient | Ja ([Azure Data Lake Storage](https://hadoop.apache.org/docs/current/hadoop-azure-datalake/index.html)) | Ja ([ABFS](https://jira.apache.org/jira/browse/HADOOP-15407))  | *Tillgängligt nu*  |  
| Data åtgärder – auktorisering  | Fil-och mappnivå POSIX Access Control listor (ACL: er) baserat på Azure Active Directory identiteter  | Fil-och mappnivå POSIX Access Control listor (ACL: er) baserat på Azure Active Directory identiteter [delnings nyckel](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key) för rollbaserade Access Control[](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac)för konto nivå för att komma åt behållare | *Tillgängligt nu* |
| Data åtgärder – loggar  | Ja | En-off-begäran för loggar för en speciell varaktighet med support biljetten Azure Monitoring integration | En-off-begäran för loggar för en speciell varaktighet med support ärende – *tillgängligt nu*<br><br> Azure Monitoring integration – *ännu inte tillgänglig* |
| Vilande data kryptering | Transparent, Server sida med service-hanterade nycklar och med Kundhanterade nycklar i Azure-valv | Transparent, Server sida med tjänst hanterade nycklar och med kund nycklar hanterade nycklar i Azure-valv | Tjänst-hanterade nycklar – *tillgängligt nu*<br><br> Kundhanterade nycklar – *tillgängligt nu*  |
| Hanterings åtgärder (t. ex. konto skapande) | [Rollbaserad åtkomst kontroll](https://docs.microsoft.com/azure/role-based-access-control/overview) (RBAC) som tillhandahålls av Azure för konto hantering | [Rollbaserad åtkomst kontroll](https://docs.microsoft.com/azure/role-based-access-control/overview) (RBAC) som tillhandahålls av Azure för konto hantering | *Tillgängligt nu*|
| SDK: er för utvecklare | .NET, Java, python, Node. js  | .NET, Java, python, Node. js, C++, ruby, php, go, Android, iOS| *Ännu inte tillgängligt* |
| |Optimerade prestanda för arbets belastningar med parallell analys. Högt data flöde och IOPS. | Optimerade prestanda för arbets belastningar med parallell analys. Högt data flöde och IOPS. | *Tillgängligt nu* |
| Stöd för Virtual Network (VNet)  | [Använda Virtual Network-integrering](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-network-security)  | [Använda tjänst slut punkten för Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | *Tillgängligt nu* |
| Storleks begränsningar | Inga begränsningar för konto storlekar, fil storlekar eller antal filer | Inga begränsningar för konto storlekar eller antal filer. Fil storleken är begränsad till 5TB. | *Tillgängligt nu*|
| GEO-redundans| Lokalt redundant (LRS) | Lokalt redundant (LRS) zon redundant (ZRS) Geo-redundant (GRS) Read-Access Geo-redundant (RA-GRS) finns [här](https://docs.microsoft.com/azure/storage/common/storage-redundancy) för mer information| *Tillgängligt nu* |
| Regional tillgänglighet | Se [här](https://azure.microsoft.com/regions/) | Alla [Azure-regioner](https://azure.microsoft.com/global-infrastructure/regions/)                                                                                                                                                                                                                                                                                                                                       | *Tillgängligt nu*                                                                                                                           |
| Pris                                       | Se [prissättning](https://azure.microsoft.com/pricing/details/data-lake-store/)                                                                            | Se [prissättning](https://azure.microsoft.com/pricing/details/storage/data-lake/)                                                                                                                                                                                                                                                                                                                                         |                                                                                                                                           |
| Serviceavtal finns                            | [Se SLA](https://azure.microsoft.com/support/legal/sla/data-lake-store/v1_0/)                                                                   | [Se SLA](https://azure.microsoft.com/support/legal/sla/storage/v1_3/)                                                                                                                                                                                                                                                                                                                                                | *Tillgängligt nu*                                                                                                                           |
| Databearbetning                             | Filens förfallo datum                                                                                                                                        | Livs cykel principer                                                                                                                                                                                                                                                                                                                                                                                                          | *Ännu inte tillgängligt*                                                                                                                       |

### <a name="programming-interfaces"></a>Programmeringsgränssnitt

Den här tabellen beskriver vilka API-uppsättningar som är tillgängliga för dina anpassade program. För att göra det lite tydligare har vi avgränsat dessa API-uppsättningar till 2 typer: hanterings-API: er och API: er för fil system

Med hanterings-API: er kan du hantera konton, medan API: er för fil systemet hjälper dig att använda filerna och mapparna.

|  API-uppsättning                           |  Data Lake Storage Gen1                                                                                                                                                                                                                                                                                                   | Tillgänglighet för Data Lake Storage Gen2 – med autentisering med delad nyckel | Tillgänglighet för Data Lake Storage Gen2 – med OAuth-autentisering                                                                                                  |
|----------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| .NET SDK – hantering                  | [Länk](https://docs.microsoft.com/dotnet/api/overview/azure/datalakestore/management?view=azure-dotnet)                                                                                                                                                                                                                 | *Stöds inte*                                                      | *Tillgänglig nu-* [länk](https://docs.microsoft.com/rest/api/storageservices/operations-on-the-account--blob-service-)                                    |
| .NET SDK – fil system                  | [Länk](https://docs.microsoft.com/dotnet/api/overview/azure/datalakestore/client?view=azure-dotnet)                                                                                                                                                                                                                     | *Ännu inte tillgängligt*                                                | *Ännu inte tillgängligt*                                                                                                                                             |
| Java SDK – hantering                  | [Länk](https://docs.microsoft.com/java/api/overview/azure/datalakestore/management)                                                                                                                                                                                                                                     | *Stöds inte*                                                      | *Tillgänglig nu –* [länk](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob?view=azure-java-stable)                                     |
| Java SDK – fil system                  | [Länka](https://docs.microsoft.com/java/api/overview/azure/datalake)                                                                                                                                                                                                                                          | *Ännu inte tillgängligt*                                                | *Ännu inte tillgängligt*                                                                                                                                             |
| Node. js-hantering                   | [Länka](https://www.npmjs.com/package/azure-arm-datalake-store)                                                                                                                                                                                                                                                                 | Stöds inte                                                      | *Tillgänglig nu-* [länk](https://azure.github.io/azure-storage-node/)                                                                                            |
| Node. js-fil system                   | [Länka](https://www.npmjs.com/package/azure-arm-datalake-store)                                                                                                                                                                                                                                                                 | *Ännu inte tillgängligt*                                                | *Ännu inte tillgängligt*                                                                                                                                             |
| Python – hantering                    | [Länka](https://docs.microsoft.com/python/api/overview/azure/datalakestore/management?view=azure-python)                                                                                                                                                                                                                  | *Stöds inte*                                                      | *Tillgänglig nu-* [länk](https://docs.microsoft.com/python/api/overview/azure/storage/management?view=azure-python)                                       |
| Python – fil system                    | [Länk](https://azure-datalake-store.readthedocs.io/en/latest/)                                                                                                                                                                                                                                                                 | *Ännu inte tillgängligt*                                                | *Ännu inte tillgängligt*                                                                                                                                             |
| REST API-hantering                  | [Länk](https://docs.microsoft.com/rest/api/datalakestore/accounts)                                                                                                                                                                                                                                                      | *Stöds inte*                                                      | *Tillgängligt nu –*                                                                                                                                               |
| REST API-fil system                  | [Länk](https://docs.microsoft.com/rest/api/datalakestore/webhdfs-filesystem-apis)                                                                                                                                                                                                                                       | *Tillgängligt nu*                                                    | *Tillgänglig nu-* [länk](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2)                                                      |
| PowerShell – hantering och fil system | [Länk](https://docs.microsoft.com/powershell/module/az.datalakestore)                                                                                                                                                                                                                        | Hantering – stöds inte fil system – *inte tillgängligt ännu*        | Hantering – *tillgänglig nu –* [länk](https://docs.microsoft.com/powershell/module/az.storage) <br><br>Fil system – *inte tillgängligt ännu* |
| CLI – hantering                       | [Länk](https://docs.microsoft.com/cli/azure/dls/account?view=azure-cli-latest)                                                                                                                                                                                                                                          | *Stöds inte*                                                      | *Tillgänglig nu-* [länk](https://docs.microsoft.com/cli/azure/storage?view=azure-cli-latest)                                                              |
| CLI-fil system                       | [Länk](https://docs.microsoft.com/cli/azure/dls/fs?view=azure-cli-latest)                                                                                                                                                                                                                                               | *Ännu inte tillgängligt*                                                | *Ännu inte tillgängligt*                                                                                                                                             |
| Azure Resource Manager mallar – hantering             | [Template1](https://azure.microsoft.com/resources/templates/101-data-lake-store-no-encryption/)  [Template2](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-adls/)  [Template3](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-key-vault/)   | *Stöds inte*                                                      | *Tillgänglig nu-* [länk](https://docs.microsoft.com/azure/templates/microsoft.storage/2018-07-01/storageaccounts)                                         |

### <a name="azure-ecosystem"></a>Azure-eko system

När du använder Data Lake Storage Gen1 kan du använda en mängd olika Microsoft-tjänster och-produkter i dina pipeliner från slut punkt till slut punkt. Dessa tjänster och produkter fungerar med Data Lake Storage Gen1 antingen direkt eller indirekt. I den här tabellen visas en lista över de tjänster som vi har ändrat för att arbeta med Data Lake Storage Gen1 och visar vilka som för närvarande är kompatibla med Data Lake Storage Gen2.

| **Område**             | **Tillgänglighet för Data Lake Storage Gen1**                                                                                                                                    | **Tillgänglighet för Data Lake Storage Gen2 – med autentisering med delad nyckel**                                                                                                           | **Tillgänglighet för Data Lake Storage Gen2 – med OAuth**                                                                                        |
|----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|
| Analys ramverk  | [Apache Hadoop](https://hadoop.apache.org/docs/current/hadoop-azure-datalake/index.html)                                                                                       | *Tillgängligt nu*                                                                                                                                                              | *Tillgängligt nu*                                                                                                                                 |
|                      | [HDInsight](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-hdinsight-hadoop-use-portal)                                                               | [Hdinsight](https://docs.microsoft.com/azure/storage/data-lake-storage/quickstart-create-connect-hdi-cluster) 3,6 – *tillgängligt nu* HDInsight 4,0 – *ännu inte tillgängligt*      | HDInsight 3,6 ESP – *tillgängligt nu* <br><br>  HDInsight 4,0 ESP – *ännu inte tillgängligt*                                                                 |
|                      | Databricks Runtime 3,1 och uppåt                                                                                                                                               | [Databricks Runtime 5,1 och senare](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html#azure-data-lake-storage-gen2) *– tillgängligt nu* | [Databricks Runtime 5,1 och senare](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html#azure-data-lake-storage-gen2) – *tillgängligt nu*                                                                                              |
|                      | [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store)                                            | *Stöds inte*                                                                                                                                                              | [Länk](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql?view=sql-server-2017) *tillgänglig nu* |
| Dataintegration     | [Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-store)                                                                                | [Version 2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage) – *tillgänglig nu* version 1 – *stöds inte*                               | [Version 2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage) – *tillgänglig nu* <br><br> Version 1 – *stöds inte*  |
|                      | [AdlCopy](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)                                                                 | *Stöds inte*                                                                                                                                                              | *Stöds inte*                                                                                                                                 |
|                      | [SQL Server Integration Services](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager?view=sql-server-2017) | *Tillgängligt nu*                                                                                                                                                          | *Tillgängligt nu*                                                                                                                             |
|                      | [Data Catalog](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-with-data-catalog)                                                                       | *Ännu inte tillgängligt*                                                                                                                                                          | *Ännu inte tillgängligt*                                                                                                                             |
|                      | [Logic Apps](https://docs.microsoft.com/connectors/azuredatalake/)                                                                                                      | *Tillgängligt nu*                                                                                                                                                          | *Tillgängligt nu*                                                                                                                             |
| IoT (Internet of Things)                  | [Event Hubs – avbildning](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-archive-eventhub-capture)                                                       | *Tillgängligt nu*                                                                                                                                                          | *Tillgängligt nu*                                                                                                                             |
|                      | [Stream Analytics](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-stream-analytics)                                                                   | *Tillgängligt nu*                                                                                                                                                          | *Tillgängligt nu*                                                                                                                             |
| Förbrukning          | [Power BI Desktop](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-power-bi)                                                                           | *Tillgängligt nu*                                                                                                                                                          | *Tillgängligt nu*                                                                                                                             |
|                      | [Excel](https://techcommunity.microsoft.com/t5/Excel-Blog/Announcing-the-Azure-Data-Lake-Store-Connector-in-Excel/ba-p/91677)                                                 | *Ännu inte tillgängligt*                                                                                                                                                          | *Ännu inte tillgängligt*                                                                                                                             |
|                      | [Analysis Services](https://blogs.msdn.microsoft.com/analysisservices/2017/09/05/using-azure-analysis-services-on-top-of-azure-data-lake-storage/)                            | *Ännu inte tillgängligt*                                                                                                                                                          | *Ännu inte tillgängligt*                                                                                                                             |
| Produktivitet         | [Azure-portalen](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal)                                                                      | *Stöds inte*                                                                                                                                                              | Konto hantering *– tillgängligt nu* <br><br>Data åtgärder *–*  *ännu inte tillgängligt*                                                                   |
|                      | [Data Lake verktyg för Visual Studio](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-data-lake-tools-install)                                   | *Ännu inte tillgängligt*                                                                                                                                                          | *Ännu inte tillgängligt*                                                                                                                             |
|                      | [Azure Storage Explorer](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-in-storage-explorer)                                                          | *Tillgängligt nu*                                                                                                                                                              | *Tillgängligt nu*                                                                                                                                 |
|                      | [Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=usqlextpublisher.usql-vscode-ext)                                                                     | *Ännu inte tillgängligt*                                                                                                                                                          | *Ännu inte tillgängligt*                                                                                                                             |

### <a name="partner-ecosystem"></a>Partner eko system

I den här tabellen visas en lista över tjänster och produkter från tredje part som har ändrats för att fungera med Data Lake Storage Gen1. Den visar även de som för närvarande är kompatibla med Data Lake Storage Gen2.

| Område            | Partner  | Produkt/tjänst  | Tillgänglighet för Data Lake Storage Gen1                                                                                                                                               | Tillgänglighet för Data Lake Storage Gen2 – med autentisering med delad nyckel                                                   | Tillgänglighet för Data Lake Storage Gen2 – med OAuth                                                             |
|---------------------|--------------|----------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------|
| Analys ramverk | Cloudera     | CDH                  | [Länk](https://www.cloudera.com/documentation/enterprise/5-11-x/topics/admin_adls_config.html)                                                                                            | *Ännu inte tillgängligt*                                                                                                  | [Länk](https://www.cloudera.com/documentation/enterprise/latest/topics/admin_adls2_config.html)                                                                                                  |
|                     | Cloudera     | Altus                | [Länk](https://www.cloudera.com/more/news-and-blogs/press-releases/2017-09-27-cloudera-introduces-altus-data-engineering-microsoft-azure-hybrid-multi-cloud-data-analytic-workflows.html) | *Stöds inte*                                                                                                                  | *Ännu inte tillgängligt*                                                                                                  |
|                     | HortonWorks  | HDP 3,0              | [Länka](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.3/bk_cloud-data-access/content/adls-get-started.html)                                                                        | *Ännu inte tillgängligt*                                                                                                  | *Ännu inte tillgängligt*                                                                                                  |
|                     | Qubole       |                      | [Länk](https://www.qubole.com/blog/big-data-analytics-microsoft-azure-data-lake-store-qubole/)                                                                                            | *Ännu inte tillgängligt*                                                                                                  | *Ännu inte tillgängligt*                                                                                                  |
| ETL                 | StreamSets   |                      | [Länk](https://streamsets.com/blog/ingest-data-azure)                                                                                                                                     | *Ännu inte tillgängligt*                                                                                                  | *Ännu inte tillgängligt*                                                                                                  |
|                     | Informatica  |                      | [Länk](https://kb.informatica.com/proddocs/Product%20Documentation/6/IC_Spring2017_MicrosoftAzureDataLakeStoreV2ConnectorGuide_en.pdf)                                                    | *Ännu inte tillgängligt*                                                                                                  | *Ännu inte tillgängligt*                                                                                                  |
|                     | Attunity     |                      | [Länk](https://www.attunity.com/company/press-releases/microsoft-and-attunity-announce-strategic-partnership-for-data-migration/)                                                         | *Ännu inte tillgängligt*                                                                                                  | *Ännu inte tillgängligt*                                                                                                  |
|                     | Alteryx      |                      | [Länk](https://help.alteryx.com/2018.2/DataSources/AzureDataLake.htm)                                                                                                                     | *Ännu inte tillgängligt*                                                                                                  | *Ännu inte tillgängligt*                                                                                                  |
|                     | ImanisData   |                      | [Länk](https://www.imanisdata.com/expansion-azure-support-azure-data-lake-store-integration/)                                                                                             | *Ännu inte tillgängligt*                                                                                                  | *Ännu inte tillgängligt*                                                                                                  |
|                     | WANdisco     |                      | [Länk](https://azure.microsoft.com/blog/globally-replicated-data-lakes-with-livedata-using-wandisco-on-azure/)                                                                      | [Länk](https://azure.microsoft.com/blog/globally-replicated-data-lakes-with-livedata-using-wandisco-on-azure/) | [Länk](https://azure.microsoft.com/blog/globally-replicated-data-lakes-with-livedata-using-wandisco-on-azure/) |

### <a name="operational-information"></a>Drift information

Data Lake Storage Gen1 push-överför speciell information och data till andra tjänster som hjälper dig att operationalisera dina pipeliner. Den här tabellen visar tillgänglighet för motsvarande stöd i Data Lake Storage Gen2.

| Typ av data                                                                                       | Tillgänglighet för Data Lake Storage Gen1                                                                 | Tillgänglighet för Data Lake Storage Gen2                                                                                               |
|--------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------|
| Fakturerings data – mätare som skickas till handels teamet för fakturering och sedan görs tillgängliga för kunder  | *Tillgängligt nu*                                                                                             | *Tillgängligt nu*                                                                                                                           |
| Aktivitetsloggar                                                                                          | [Länk](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs#audit-logs)   | En-off-begäran för loggar för en speciell varaktighet med support ärende – *tillgängligt nu* Azure Monitoring integration – *ännu inte tillgänglig* |
| Diagnostikloggar                                                                                        | [Länk](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs#request-logs) | En-off-begäran för loggar för en speciell varaktighet med support ärende – *tillgängligt nu* Azure Monitoring integration – *ännu inte tillgänglig* |
| Mått                                                                                                | *Stöds inte*                                                                                               | *Tillgänglig nu-* [länk](https://docs.microsoft.com/azure/storage/common/storage-metrics-in-azure-monitor)                          |

## <a name="planning-for-an-upgrade"></a>Planera för en uppgradering

Det här avsnittet förutsätter att du har granskat avsnittet [utvärdera ditt uppgraderings beredskap](#assess-your-upgrade-readiness) i den här guiden och att alla dina beroenden är uppfyllda. Om det finns funktioner som fortfarande inte är tillgängliga i Data Lake Storage Gen2 går du bara vidare om du känner till motsvarande lösning. I följande avsnitt finns anvisningar om hur du kan planera för att uppgradera dina pipeliner. Att utföra den faktiska uppgraderingen beskrivs i avsnittet [utföra uppgradering](#performing-the-upgrade) i den här guiden.

### <a name="upgrade-strategy"></a>Uppgraderings strategi

Den mest kritiska delen av uppgraderingen bestämmer strategin. Det här beslutet avgör vilka alternativ som är tillgängliga för dig.

I den här tabellen listas några välkända strategier som har använts för att migrera databaser, Hadoop-kluster osv. Vi antar liknande strategier i vår vägledning och anpassar dem till vår kontext.

| Strategi                   | Proffs                                                                                  | Nackdelar                                                           | När ska jag använda?                                                                                                                                                                                             |
|--------------------------------|-------------------------------------------------------------------------------------------|--------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Lyft-och-Shift                 | Enklare.                                                                                 | Kräver stillestånds tid för att kopiera över data, flytta jobb och flytta ingress och utgående                                             | För enklare lösningar, där det inte finns flera lösningar som har åtkomst till samma gen1-konto och kan därför flyttas tillsammans på ett snabbt kontrollerat sätt.                                                  |
| Kopiera en gång och kopiera stegvis | Minska nedtid genom att utföra kopiering i bakgrunden medan käll systemet fortfarande är aktivt. | Kräver stillestånds tid för att flytta ingress och utgående.                   | Mängden data som ska kopieras över är stor och drift stoppet som är kopplat till Life-och-Shift är inte acceptabelt. Testning kan krävas med betydande produktions data på mål systemet före över gången. |
| Parallellt införande              | Med minsta stillestånd kan du göra det möjligt för program att migrera efter eget gottfinnande.           | De flesta avancerade sedan tvåvägs synkronisering krävs mellan de två systemen. | För komplexa scenarier där program som bygger på Data Lake Storage Gen1 inte kan start punkt alla samtidigt och måste flyttas över på ett steg.                                                      |

Nedan finns mer information om de steg som ingår i varje strategi. De steg som beskrivs i den här listan är de komponenter som ingår i uppgraderingen. Detta omfattar det övergripande käll systemet, det övergripande mål systemet, ingresss källor för käll systemet, utgående mål för käll systemet och jobb som körs på käll systemet.

De här stegen är inte avsedda att vara för skript. De är avsedda att ange ramverket för hur vi tänker på varje strategi. Vi ger fallstudier för varje strategi när vi ser att de implementeras.

#### <a name="lift-and-shift"></a>Lyft-och-Shift

1. Pausa käll systemet – ingresss källor, jobb, utgående destinationer.
2. Kopiera alla data från käll systemet till mål systemet.
3. Peka alla ingress-källor till mål systemet. Peka på utgående destination från mål systemet.
4. Flytta, ändra och kör alla jobb till mål systemet.
5. Stäng av käll systemet.

#### <a name="copy-once-and-copy-incremental"></a>Kopiera – en gång och en kopia – stegvis

1. Kopiera data från käll systemet till mål systemet.
2. Kopiera över de stegvisa data från käll systemet till mål systemet med jämna mellanrum.
3. Peka på utgående destination från mål systemet.
4. Flytta, ändra, kör alla jobb på mål systemet.
5. Punkt ingressr stegvisa källor till mål systemet efter din bekvämlighet.
6. När alla ingress-källor pekar på mål systemet.
    1. Inaktivera stegvis kopiering.
    2. Stäng av käll systemet.

#### <a name="parallel-adoption"></a>Parallellt införande

1. Konfigurera mål system.
2. Konfigurera en dubbelriktad replikering mellan käll systemet och mål systemet.
3. Punkt ingresss källor stegvis till mål systemet.
4. Flytta, ändra, Kör jobb stegvis till mål systemet.
5. Peka på utgående destinationer stegvis från mål systemet.
6. När alla de ursprungliga ingångs källorna, jobb och utgående mål arbetar med mål systemet, Stäng av käll systemet.

### <a name="data-upgrade"></a>Data uppgradering

Den övergripande strategin som du använder för att utföra uppgraderingen (beskrivs i avsnittet om [uppgraderings strategi](#upgrade-strategy) i den här hand boken) avgör vilka verktyg du kan använda för din data uppgradering. Verktygen i listan nedan baseras på aktuell information och är förslag. 

#### <a name="tools-guidance"></a>Verktygs vägledning

| Strategi                       | Verktyg                                                                                                             | Proffs                                                                                                                             | Överväganden                                                                                                                                                                                                                                                                                                                |
|------------------------------------|-----------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Lyft-och-Shift**                 | [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2-from-gen1) | Hanterad moln tjänst                                                                                                                | Både data och ACL: er kan kopieras över för närvarande.                                                                                                                                                                                                                                                                      |
|                                    | [Distcp](https://hadoop.apache.org/docs/r1.2.1/distcp.html)                                                           | Välkända Hadoop-tillhandahållna verktygs behörigheter, dvs. ACL: er kan kopieras med det här verktyget                                                   | Kräver ett kluster som kan ansluta till både Data Lake Storage Gen1 och Gen2 samtidigt.                                                                                                                                                                                   |
| **Kopiera en gång och kopiera stegvis** | Azure Data Factory                                                                                                    | Hanterad moln tjänst                                                                                                                | Både data och ACL: er kan kopieras över för närvarande. För att stödja stegvis kopiering i ADF måste data ordnas i en tids serie. Det kortaste intervallet mellan stegvisa kopior är [15 minuter](https://docs.microsoft.com/azure/data-factory/how-to-create-tumbling-window-trigger). |
| **Parallellt införande**              | [WANdisco](https://docs.wandisco.com/bigdata/wdfusion/adls/)                                                           | Stöd för konsekvent replikering om du använder en ren Hadoop-miljö som är ansluten till Azure Data Lake Storage, stöder dubbelriktad replikering | Om du inte använder en ren Hadoop-miljö kan det uppstå en fördröjning i replikeringen.                                                                                                                                                                                                                                                  |

Observera att det finns tredje parter som kan hantera Data Lake Storage Gen1 för att Data Lake Storage Gen2 uppgraderingen utan att du behöver använda kopierings verktygen ovan för data/meta-data (till exempel: [Cloudera](https://blog.cloudera.com/blog/2017/08/use-amazon-s3-with-cloudera-bdr/)). De ger en "One-Stop"-upplevelse som utför datamigrering samt migrering av arbets belastning. Du kan behöva utföra en out-of-band-uppgradering för alla verktyg som ligger utanför eko systemet.

#### <a name="considerations"></a>Överväganden

* Innan du påbörjar en del av uppgraderingen måste du först skapa ett Data Lake Storage Gen2-konto, eftersom den aktuella vägledningen inte innehåller någon automatisk Gen2 konto process baserat på din gen1-kontoinformation. Se till att du jämför processerna för att skapa konton för [gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal) och [Gen2](https://docs.microsoft.com/azure/storage/data-lake-storage/quickstart-create-account).

* Data Lake Storage Gen2 stöder bara filer på upp till 5 TB i storlek. Om du vill uppgradera till Data Lake Storage Gen2 kan du behöva ändra storlek på filerna i Data Lake Storage Gen1 så att de är mindre än 5 TB i storlek.

* Om du använder ett verktyg som inte kopierar ACL: er eller om du inte vill kopiera över åtkomst kontrol listorna, måste du ange ACL: erna på målet manuellt på lämplig översta nivå. Du kan göra det med hjälp av Storage Explorer. Se till att dessa ACL: er är standard-ACL: er så att de filer och mappar som du kopierar över ärver dem.

* I Data Lake Storage Gen1 är den högsta nivån du kan ange ACL: er i roten för kontot. I Data Lake Storage Gen2 är det dock högsta nivån som du kan ställa in ACL: er i rotmappen i en behållare, inte hela kontot. Så om du vill ange standard-ACL: er på konto nivå måste du duplicera dem över alla fil system i ditt Data Lake Storage Gen2-konto.

* Fil namns begränsningar skiljer sig mellan de två lagrings systemen. Dessa skillnader gäller särskilt när du kopierar från Data Lake Storage Gen2 till Data Lake Storage Gen1 eftersom det senare har fler begränsade begränsningar.

### <a name="application-upgrade"></a>Programuppgradering

När du behöver bygga program på Data Lake Storage Gen1 eller Data Lake Storage Gen2 måste du först välja ett lämpligt programmerings gränssnitt. När du anropar ett API på det gränssnittet måste du ange rätt URI och lämpliga autentiseringsuppgifter. Representationen av dessa tre element, API: et, URI: n och hur autentiseringsuppgifterna anges, skiljer sig mellan Data Lake Storage Gen1 och Data Lake Storage Gen2.So, som en del av program uppgraderingen, du måste mappa dessa tre konstruktioner på lämpligt sätt.

#### <a name="uri-changes"></a>URI-ändringar

Huvud uppgiften här är att översätta URI: er som har prefixet `adl://` till URI: er som har ett `abfss://`-prefix.

URI-schemat för Data Lake Storage Gen1 anges [här](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-store) i detalj, men det är *ADL://mydatalakestore.azuredatalakestore.net/\<file_path \>.*

URI-schemat för att komma åt Data Lake Storage Gen2-filer förklaras [här](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md) i detalj, men är i stort sett `abfss://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`.

Du måste gå igenom dina befintliga program och se till att du har ändrat de URI: er som är lämpliga för att peka på Data Lake Storage Gen2. Du måste också lägga till lämpliga autentiseringsuppgifter. Slutligen, hur du drar tillbaka de ursprungliga programmen och ersätter med det nya programmet måste du justeras nära din övergripande uppgraderings strategi.

#### <a name="custom-applications"></a>Anpassade program

Beroende på vilket gränssnitt som används i programmet med Data Lake Storage Gen1 måste du ändra det för att anpassa det till Data Lake Storage Gen2.

##### <a name="rest-apis"></a>REST API:er

Om programmet använder Data Lake Storage REST-API: er måste du ändra ditt program så att det använder Data Lake Storage Gen2 REST-API: er. Det finns länkar i avsnittet *Programming Interfaces* .

##### <a name="sdks"></a>SDK:er

Som det visas i avsnittet *utvärdera ditt uppgraderings beredskap* är SDK: er för närvarande inte tillgängliga. Om du vill att en port över dina program ska Data Lake Storage Gen2, rekommenderar vi att du väntar på att SDK: er som stöds är tillgängliga.

##### <a name="powershell"></a>PowerShell

Som det visas i avsnittet utvärdera ditt uppgraderings beredskap är PowerShell-support för närvarande inte tillgängligt för data planet.

Du kan ersätta Management plan-cmdletarna med lämpliga i Data Lake Storage Gen2. Det finns länkar i avsnittet *Programming Interfaces* .

##### <a name="cli"></a>CLI

Som det visas i avsnittet *utvärdera ditt uppgraderings beredskap* är CLI-stödet för närvarande inte tillgängligt för data planet.

Du kan ersätta hanterings plan kommandon med lämpliga i Data Lake Storage Gen2. Det finns länkar i avsnittet *Programming Interfaces* .

### <a name="analytics-frameworks-upgrade"></a>Uppgradering av analys ramverk

Om programmet skapar meta-data om information i arkivet, till exempel explicita fil-och mappsökvägar, måste du utföra ytterligare åtgärder efter uppgraderingen av lagra data/meta-data. Detta gäller särskilt för analys ramverk som Azure HDInsight, Databricks o.s.v., som vanligt vis skapar katalog data på Store-data.

Analys ramverk fungerar med data och meta-data som lagras i fjärrarkiven som Data Lake Storage Gen1 och Gen2. I teorin kan motorerna dock vara tillfälliga och endast hämtas när jobb måste köras mot lagrade data.

För att optimera prestanda kan analys ramverken dock skapa explicita referenser till de filer och mappar som lagras i fjärrlagringsplatsen och sedan skapa ett cacheminne för att hålla dem kvar. Om URI: n för fjärrdatan ändras, till exempel ett kluster som lagrar data i Data Lake Storage Gen1 tidigare och som nu vill lagra i Data Lake Storage Gen2, är URI: n för samma kopierade innehåll olika. När du har uppgraderat data och meta-data måste cacheminnena för dessa motorer också uppdateras eller initieras om

Som en del av planerings processen måste du identifiera ditt program och ta reda på hur meta-datainformation kan initieras igen för att peka på data som nu lagras i Data Lake Storage Gen2. Nedan ges vägledning för vanliga analys ramverk som hjälper dig med sina uppgraderings steg.

#### <a name="azure-databricks"></a>Azure Databricks

Stegen varierar beroende på vilken uppgraderings strategi du väljer. Det aktuella avsnittet förutsätter att du har valt strategin "livs längd och Skift". Dessutom förväntas den befintliga Databricks-arbetsytan som används för att komma åt data i ett Data Lake Storage Gen1 konto arbeta med de data som kopieras till Data Lake Storage Gen2-kontot.

Kontrol lera först att du har skapat Gen2-kontot och kopierat sedan över data och meta från gen1 till Gen2 med hjälp av ett lämpligt verktyg. Dessa verktyg kallas i avsnittet [data uppgradering](#data-upgrade) i den här guiden.

[Uppgradera](https://docs.azuredatabricks.net/user-guide/clusters/index.html) sedan ditt befintliga Databricks-kluster för att börja använda Databricks runtime 5,1 eller högre, vilket bör stödja data Lake Storage Gen2.

Anvisningarna baseras sedan på hur den befintliga Databricks-arbetsytan får åtkomst till data i Data Lake Storage Gen1-kontot. Det kan göras antingen genom att anropa adl://-URI: er [direkt](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html#access-azure-data-lake-store-directly) från antecknings böcker eller via [mountpoints](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html#mount-azure-data-lake-store-with-dbfs).

Om du ansluter direkt från antecknings böcker genom att tillhandahålla fullständiga adl://-URI: er måste du gå igenom varje antecknings bok och ändra konfigurationen för att få åtkomst till motsvarande Data Lake Storage Gen2-URI.

När du går vidare måste du konfigurera om den så att den pekar på Data Lake Storage Gen2 konto. Inga fler ändringar krävs och antecknings böckerna bör kunna fungera som tidigare.

Om du använder någon av de andra uppgraderings strategierna kan du skapa en variant av stegen ovan för att uppfylla dina krav.

### <a name="azure-ecosystem-upgrade"></a>Azure eko system uppgradering

Vart och ett av verktygen och tjänsterna i avsnittet [Azure eko system](#azure-ecosystem) i den här hand boken måste konfigureras för att fungera med data Lake Storage Gen2.

Se först till att det finns en integrering med Data Lake Storage Gen2.

Sedan kommer de element som anropas ovan (till exempel: URI och autentiseringsuppgifter) att ändras. Du kan ändra den befintliga instansen som fungerar med Data Lake Storage Gen1 eller så kan du skapa en ny instans som fungerar med Data Lake Storage Gen2.

### <a name="partner-ecosystem-upgrade"></a>Uppgradering av partner eko system

Arbeta med den partner som tillhandahåller komponenten och verktygen för att säkerställa att de kan arbeta med Data Lake Storage Gen2. 

## <a name="performing-the-upgrade"></a>Utför uppgraderingen

### <a name="pre-upgrade"></a>Före uppgradering

Som en del av detta skulle du ha gått igenom avsnittet *utvärdera uppgraderings beredskap* och avsnittet [Planera för en uppgradering](#planning-for-an-upgrade) i den här guiden. du har fått all nödvändig information och du har skapat en plan som passar dina behov. Du kommer förmodligen att ha en test aktivitet under den här fasen.

### <a name="in-upgrade"></a>Under uppgradering

Beroende på vilken strategi du väljer och hur komplex lösningen är, kan den här fasen vara en kort eller en utökad lösning där det finns flera arbets belastningar som väntar på att ökas stegvis till Data Lake Storage Gen2. Detta är den viktigaste delen av uppgraderingen.

### <a name="post-upgrade"></a>Efter uppgradering

När du är klar med över gångs åtgärden kommer de sista stegen att omfatta noggrann verifiering. Detta omfattar men är inte begränsat till att verifiera data har kopierats på ett tillförlitligt sätt, kontrol lera att ACL: er har ställts in korrekt, verifiera att pipelinen från slut punkt till slut punkt fungerar korrekt osv. När verifieringen har slutförts kan du nu stänga av dina gamla pipeliner, ta bort dina käll Data Lake Storage Gen1s konton och gå med i full hastighet på Data Lake Storage Gen2-baserade lösningar.

## <a name="conclusion"></a>Sammanfattning

De rikt linjer som anges i det här dokumentet bör ha hjälpt dig att uppgradera din lösning för att använda Data Lake Storage Gen2. 

Om du har fler frågor, eller om du har feedback, anger du kommentarer nedan eller ger feedback i [Azure feedback-forumet](https://feedback.azure.com/forums/327234-data-lake).
