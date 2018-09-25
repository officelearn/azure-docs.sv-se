# [Dokumentation om Data Lake Storage Gen1](index.md)
# [Växla till dokumentation om Data Lake Storage Gen2 (förhandsversion)](https://docs.microsoft.com/azure/storage/data-lake-storage/introduction)

# Översikt
## [Översikt över Data Lake Storage Gen1](data-lake-store-overview.md)
## [Jämför med Azure Storage](data-lake-store-comparison-with-blob-storage.md)
## [Bearbeta stordata](data-lake-store-data-scenarios.md)
## [Arbeta med program med öppen källkod](data-lake-store-compatible-oss-other-applications.md)
## [Bästa praxis](data-lake-store-best-practices.md)

# Kom igång
## [Använda Azure Portal](data-lake-store-get-started-portal.md)
## [Använda Azure PowerShell](data-lake-store-get-started-powershell.md)
## [Använda Azure CLI](data-lake-store-get-started-cli-2.0.md)

# Gör så här för att
## Läs in och flytta data
### [Använda Azure Data Factory](../data-factory/load-azure-data-lake-store.md)
### [Använda Storage Explorer](data-lake-store-in-storage-explorer.md)
### [Använda AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
### [Använda DistCp](data-lake-store-copy-data-wasb-distcp.md)
### [Använda Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
### [Ladda upp data från offlinekällor](data-lake-store-offline-bulk-data-upload.md)
### [Migrera Data Lake Store Gen1 mellan olika regioner](data-lake-store-migration-cross-region.md)

## Skydda data
### [Säkerhetsöversikt](data-lake-store-security-overview.md)
### [Åtkomstkontroll](data-lake-store-access-control.md)
### [Att säkra lagrade data](data-lake-store-secure-data.md)
### [Kryptering](data-lake-store-encryption.md)

## Autentisera med Data Lake Storage Gen1
### [Autentiseringsalternativ](data-lakes-store-authentication-using-azure-active-directory.md)
### [Slutanvändarautentisering](data-lake-store-end-user-authenticate-using-active-directory.md)
#### [Använda Java](data-lake-store-end-user-authenticate-java-sdk.md)
#### [Använda .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
#### [Använda REST-API](data-lake-store-end-user-authenticate-rest-api.md)
#### [Använda Python](data-lake-store-end-user-authenticate-python.md)
### [Tjänst-till-tjänst-autentisering](data-lake-store-service-to-service-authenticate-using-active-directory.md)
#### [Använda Java](data-lake-store-service-to-service-authenticate-java.md)
#### [Använda .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
#### [Använda REST-API](data-lake-store-service-to-service-authenticate-rest-api.md)
#### [Använda Python](data-lake-store-service-to-service-authenticate-python.md)

## Arbeta med Data Lake Storage Gen1
### Åtgärder för kontohantering
#### [Använda .NET SDK](data-lake-store-get-started-net-sdk.md)
#### [Använda REST-API](data-lake-store-get-started-rest-api.md)
#### [Använda Python](data-lake-store-get-started-python.md)
### Åtgärder för filsystem
#### [Använda .NET SDK](data-lake-store-data-operations-net-sdk.md)
#### [Använda Java SDK](data-lake-store-get-started-java-sdk.md)
#### [Använda REST-API](data-lake-store-data-operations-rest-api.md)
#### [Använda Python](data-lake-store-data-operations-python.md)

## Prestanda
### [Översikt](data-lake-store-performance-tuning-guidance.md)
### [Använda Azure PowerShell](data-lake-store-performance-tuning-powershell.md)
### [Använda Spark på HDInsight](data-lake-store-performance-tuning-spark.md)
### [Använda Hive på HDInsight](data-lake-store-performance-tuning-hive.md)
### [Använda på HDInsight](data-lake-store-performance-tuning-mapreduce.md)
### [Använda Storm på HDInsight](data-lake-store-performance-tuning-storm.md)

## Integrera med Azure-tjänster
### Med HDInsight
#### [Använda Azure Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
#### [Använda Azure PowerShell (standardlagring)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
#### [Använda Azure PowerShell (ytterligare lagring)](data-lake-store-hdinsight-hadoop-use-powershell.md)
#### [Använda Azure-mall](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
### [Åtkomst från virtuella datorer i Azure VNET](data-lake-store-connectivity-from-vnets.md)
### [Använd tillsammans med Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
### [Använd med Azure Event Hubs](data-lake-store-archive-eventhub-capture.md)
### [Använd med Data Factory](../data-factory/load-azure-data-lake-store.md)
### [Använd med Stream Analytics](data-lake-store-stream-analytics.md)
### [Använd med Power BI](data-lake-store-power-bi.md)
### [Använd med Data Catalog](data-lake-store-with-data-catalog.md)
### [Använd med PolyBase i SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md)
### [Använd med SQL Server Integration Services](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager)
### [Fler Azure-integreringsalternativ](data-lake-store-integrate-with-other-services.md)

## Hantera
### [Få åtkomst till diagnostikloggar](data-lake-store-diagnostic-logs.md)
### [Planera för hög tillgänglighet](data-lake-store-disaster-recovery-guidance.md)

# Referens
## [Kodexempel](https://azure.microsoft.com/resources/samples/?service=data-lake-store)
## [Azure PowerShell](/powershell/module/azurerm.datalakestore)
## [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)
## [Java](/java/api/com.microsoft.azure.datalake.store)
## [Node.js](https://www.npmjs.com/package/azure-arm-datalake-store)
## [Python (kontohantering)](https://docs.microsoft.com/python/api/azure.mgmt.datalake.store?view=azure-python)
## [Python (filsystemshantering)](http://azure-datalake-store.readthedocs.io/en/latest)
## [REST](/rest/api/datalakestore)
## [Azure CLI](https://docs.microsoft.com/cli/azure/dls)

# Resurser
## [Azure-översikt](https://azure.microsoft.com/roadmap/)
## [Data Lake Store-blogg](https://blogs.msdn.microsoft.com/azuredatalake/)
## [Ge feedback på UserVoice](https://feedback.azure.com/forums/327234-data-lake)
## [MSDN-forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDataLake)
## [Prissättning](https://azure.microsoft.com/pricing/details/data-lake-store/)
## [Priskalkylator](https://azure.microsoft.com/pricing/calculator/)
## [Tjänstuppdateringar](https://azure.microsoft.com/updates/?product=data-lake-store)
## [Stack Overflow-forum](http://stackoverflow.com/questions/tagged/azure-data-lake)
## [Videoklipp](https://azure.microsoft.com/documentation/videos/index/?services=data-lake-store)
