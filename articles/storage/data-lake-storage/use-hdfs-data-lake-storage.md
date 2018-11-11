---
title: Med hjälp av HDFS-CLI med Azure Data Lake Storage Gen2 förhandsversion
description: Introduktion till HDFS CLI för Data Lake Storage Gen2 förhandsversion
services: storage
author: artemuwka
ms.service: storage
ms.topic: article
ms.date: 06/27/2018
ms.author: artek
ms.component: data-lake-storage-gen2
ms.openlocfilehash: c5f11cbb12b727f5f308d7a71c51706fa8ec373f
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2018
ms.locfileid: "51277094"
---
# <a name="using-the-hdfs-cli-with-data-lake-storage-gen2"></a>Med hjälp av HDFS-CLI med Data Lake Storage Gen2

Azure Data Lake Storage Gen2 förhandsversion kan du hantera och komma åt data precis som med en [Hadoop Distributed File System (HDFS)](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Om du har ett HDInsight-kluster-ansluten eller kör ett Apache Spark-jobb med Azure Databricks för att utföra analyser på data som lagras i ett Azure Storage-konto kan använda du kommandoradsgränssnittet (CLI) att hämta och ändra inlästa data.

## <a name="hdfs-cli-with-hdinsight"></a>HDFS CLI med HDInsight

HDInsight ger tillgång till det distribuerade filsystemet som är lokalt anslutet till beräkningsnoderna. Detta filsystem kan nås genom att använda det gränssnitt som interagerar direkt med med HDFS och andra filsystem som har stöd för Hadoop. Nedan visas vanliga kommandon och länkar till användbara resurser.

>[!IMPORTANT]
>HDInsight-kluster debiteringen börjar när ett kluster har skapats och stoppas när klustret tas bort. Debiteringen görs i förväg per minut, så du ska alltid ta bort ditt kluster när det inte används. Information om hur du tar bort ett kluster finns i vår [artikeln på ämnet](../../hdinsight/hdinsight-delete-cluster.md). Data som lagras i ett lagringskonto med Data Lake Storage Gen2 aktiverat kvarstår dock även efter ett HDInsight-klustret tas bort.

Hämta en lista över filer eller kataloger:

    hdfs dfs -ls <args>
Skapa en katalog:

    hdfs dfs -mkdir [-p] <paths>
Ta bort en fil eller katalog:

    hdfs dfs -rm [-skipTrash] URI [URI ...]


Låt oss nu ta HDInsight Hadoop-kluster på Linux som ett exempel. Om du vill använda CLI HDFS, måste du först upprätta [fjärråtkomst till tjänster](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-information#remote-access-to-services). Om du väljer [SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) exempelkoden PowerShell skulle se ut så här:
```PowerShell
#Connect to the cluster via SSH.
ssh sshuser@clustername-ssh.azurehdinsight.net
#Execute basic HDFS commands. Display the hierarchy.
hdfs dfs -ls /
#Create a sample directory.
hdfs dfs -mkdir /samplefolder
```

Anslutningssträngen kan hittas på den ”SSH- och logga in” på bladet för HDInsight-kluster i Azure-portalen. SSH-autentiseringsuppgifter angavs vid tidpunkten för klustret skapas.

Mer information om HDFS CLI finns i den [officiella dokumentationen](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html) och [Guide för HDFS-behörigheter](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html).

## <a name="hdfs-cli-with-azure-databricks"></a>HDFS CLI med Azure Databricks

Databricks ger en enkel att använda CLI som bygger på Databricks REST API. Projekt för öppen källkod finns på [GitHub](https://github.com/databricks/databricks-cli). Nedan visas vanliga kommandon.

Hämta en lista över filer eller kataloger:

    dbfs ls [-l]
Skapa en katalog:

    dbfs mkdirs
Ta bort en fil:

    dbfs rm [-r]

Ett annat sätt att interagera med Databricks är anteckningsböcker. När en bärbar dator har ett primärt språk, kan du blanda språk genom att ange språket magiska språkkommandot % språk i början av en cell. Mer specifikt kan % sh du köra shell kod i anteckningsboken nästan samma sätt som i exemplet för HDInsight tidigare i den här artikeln.

Hämta en lista över filer eller kataloger:

    %sh ls <args>
Skapa en katalog:

    %sh mkdir [-p] <paths>
Ta bort en fil eller katalog:

    %sh rm [-skipTrash] URI [URI ...]

När du har startat Spark-kluster i Azure Databricks, ska du skapa en ny anteckningsbok. Exempelskript för notebook ska se ut så här:

    #Execute basic HDFS commands invoking the shell. Display the hierarchy.
    %sh ls /
    #Create a sample directory.
    %sh mkdir /samplefolder

Mer information om Databricks CLI finns i den [officiella dokumentationen](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html). Läs mer på bärbara datorer, den [anteckningsböcker](https://docs.azuredatabricks.net/user-guide/notebooks/index.html) i dokumentationen för.

## <a name="next-steps"></a>Nästa steg

- [Skapa ett HDInsight-kluster med Azure Data Lake Storage Gen2](./quickstart-create-connect-hdi-cluster.md)
- [Använda ett Azure Data Lake Storage Gen2 kompatibla konto i Azure Databricks](./quickstart-create-databricks-account.md) 