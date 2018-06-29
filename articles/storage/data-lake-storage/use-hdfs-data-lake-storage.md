---
title: Med hjälp av HDFS-CLI med Azure Data Lake lagring Gen2 förhandsgranskning
description: Introduktion till HDFS CLI för Data Lake lagring Gen2 Preview
services: storage
documentationcenter: ''
author: artemuwka
manager: jahogg
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: artek
ms.component: data-lake-storage-gen2
ms.openlocfilehash: 75fb07120c78c45d422ee5017eac0afcf0e80859
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37060826"
---
# <a name="using-the-hdfs-cli-with-data-lake-storage-gen2"></a>Med Data Lake lagring Gen2 HDFS-CLI

Azure Data Lake lagring Gen2 Preview kan du hantera och komma åt data, precis som med en [Hadoop Distributed File System (HDFS)](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Om du har ett HDInsight-kluster ansluten eller köra ett Apache Spark-jobb med hjälp av Azure Databricks för att utföra analyser på data som lagras i Azure Data Lake lagring Gen2 kan du använda kommandoradsgränssnittet (CLI) att hämta och ändra inlästa data. Resten av artikeln beskrivs de alternativ som du har medan [Azure Storage-teamet arbetar med att lägga till stöd för Azure Lagringsutforskaren och Azure-portalen](https://azure.microsoft.com/roadmap/) -roliga!

## <a name="hdfs-cli-with-hdinsight"></a>HDFS CLI med HDInsight

HDInsight ger tillgång till det distribuerade filsystemet som är lokalt anslutet till beräkningsnoderna. Detta filsystem kan nås med hjälp av gränssnittet som interagerar direkt med HDFS och andra filsystem som har stöd för Hadoop. Nedan visas vanliga kommandon och länkar till användbara resurser.

>[!IMPORTANT]
>Debiteringen för HDInsight-klustret börjar när ett kluster skapas och stoppas när klustret tas bort. Faktureringen är proportionerligt per minut, så du bör alltid ta bort klustret när den inte längre används (Lär dig hur du [tar bort ett kluster](../../hdinsight/hdinsight-delete-cluster.md)). Data som lagras i Azure Data Lake lagring Gen2 kvarstår dock även efter ett HDInsight-kluster har tagits bort.

Hämta en lista över filer eller kataloger:

    hdfs dfs -ls <args>
Att skapa en katalog:

    hdfs dfs -mkdir [-p] <paths>
Ta bort en fil eller katalog:

    hdfs dfs -rm [-skipTrash] URI [URI ...]


Låt oss nu ta HDInsight Hadoop-kluster på Linux som ett exempel. Om du vill använda HDFS CLI, måste du först fastställa [fjärråtkomst till tjänster](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-information#remote-access-to-services). Om du väljer [SSH](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) exempelkoden PowerShell skulle se ut så här:
```PowerShell
#Connect to the cluster via SSH.
ssh sshuser@clustername-ssh.azurehdinsight.net
#Execute basic HDFS commands. Display the hierarchy.
hdfs dfs -ls /
#Create a sample directory.
hdfs dfs -mkdir /samplefolder
```

Anslutningssträngen kan hittas på den ”SSH + klustret inloggning” avsnittet av bladet HDInsight-kluster i Azure-portalen. SSH-autentiseringsuppgifter angavs när klustret skapas.

Mer information om HDFS CLI finns i [officiella dokumentation](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html) och [HDFS behörigheter guiden](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html).

## <a name="hdfs-cli-with-azure-databricks"></a>HDFS CLI med Azure Databricks

Databricks ger ett enkelt att använda CLI byggda på Databricks REST API. Öppen källkod projektet finns på [GitHub](https://github.com/databricks/databricks-cli). Nedan visas vanliga kommandon.

Hämta en lista över filer eller kataloger:

    dbfs ls [-l]
Att skapa en katalog:

    dbfs mkdirs
Ta bort en fil:

    dbfs rm [-r]

Ett annat sätt att interagera med Databricks är bärbara datorer. När en bärbar dator har ett primärt språk, kan du blanda språk genom att ange språk magiskt kommandot % språk i början av en cell. Mer specifikt kan % v du köra shell kod i din bärbara dator liknande sätt som i exemplet med HDInsight tidigare i den här artikeln.

Hämta en lista över filer eller kataloger:

    %sh ls <args>
Att skapa en katalog:

    %sh mkdir [-p] <paths>
Ta bort en fil eller katalog:

    %sh rm [-skipTrash] URI [URI ...]

När du har startat Spark-kluster i Azure Databricks, ska du skapa en ny anteckningsbok. Så här ser anteckningsboken exempelskript:

    #Execute basic HDFS commands invoking the shell. Display the hierarchy.
    %sh ls /
    #Create a sample directory.
    %sh mkdir /samplefolder

Mer information om Databricks CLI finns i [officiella dokumentation](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html). Läs mer på bärbara datorer i den [anteckningsböcker](https://docs.azuredatabricks.net/user-guide/notebooks/index.html) avsnittet i dokumentationen.

## <a name="next-steps"></a>Nästa steg

- [Skapa ett HDInsight-kluster med Azure Data Lake lagring Gen2](./quickstart-create-connect-hdi-cluster.md)
- [Använda ett Azure Data Lake lagring Gen2 kompatibla konto i Azure Databricks](./quickstart-create-databricks-account.md) 