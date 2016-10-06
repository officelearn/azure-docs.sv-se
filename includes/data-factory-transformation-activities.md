Azure Data Factory stöder följande transformeringsaktiviteter som kan läggas till enskilt, eller kopplade till en annan aktivitet, i pipelines.

Datatransformeringsaktivitet |  Compute-miljö 
:----------------------- | :--------------------
[Hive](../articles/data-factory/data-factory-hive-activity.md) | HDInsight [Hadoop] 
[Pig](../articles/data-factory/data-factory-pig-activity.md) | HDInsight [Hadoop]  
[MapReduce](../articles/data-factory/data-factory-map-reduce.md) | HDInsight [Hadoop]  
[Hadoop Streaming](../articles/data-factory/data-factory-hadoop-streaming-activity.md) | HDInsight [Hadoop]
[Machine Learning-aktiviteter: batchkörning och resursuppdatering](../articles/data-factory/data-factory-azure-ml-batch-execution-activity.md) | Azure VM 
[Lagrad procedur](../articles/data-factory/data-factory-stored-proc-activity.md) | Azure SQL, Azure SQL Data Warehouse eller SQL Server |
[Data Lake Analytics U-SQL](../articles/data-factory/data-factory-usql-activity.md) | Azure Data Lake Analytics 
[DotNet](../articles/data-factory/data-factory-use-custom-activities.md) | HDInsight [Hadoop] eller Azure Batch
   
> [AZURE.NOTE] 
> Du kan använda MapReduce-aktiviteten för att köra Spark-program i ditt HDInsight Spark-kluster. Mer information finns i [Invoke Spark programs from Azure Data Factory](../articles/data-factory/data-factory-spark.md) (Anropa Spark-program från Azure Data Factory).
> Du kan skapa en anpassad aktivitet som kör R-skript i ditt HDInsight-kluster med R installerat. Se [Run R Script using Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample) (Köra R-skript med Azure Data Factory).

<!--HONumber=Sep16_HO4-->


