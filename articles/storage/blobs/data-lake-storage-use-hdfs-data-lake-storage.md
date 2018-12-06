---
title: Med hjälp av HDFS-CLI med Azure Data Lake Storage Gen2 förhandsversion
description: Introduktion till HDFS CLI för Data Lake Storage Gen2 förhandsversion
services: storage
author: artemuwka
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: artek
ms.component: data-lake-storage-gen2
ms.openlocfilehash: b4485e234e19e93a852895c80775b8aadc7a15ce
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52975405"
---
# <a name="using-the-hdfs-cli-with-data-lake-storage-gen2"></a>Med hjälp av HDFS-CLI med Data Lake Storage Gen2

Azure Data Lake Storage Gen2 förhandsversion kan du hantera och komma åt data precis som med en [Hadoop Distributed File System (HDFS)](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Om du har ett HDInsight-kluster-ansluten eller kör ett Apache Spark-jobb med Azure Databricks för att utföra analyser på data som lagras i ett Azure Storage-konto kan använda du kommandoradsgränssnittet (CLI) att hämta och ändra inlästa data.

## <a name="hdfs-cli-with-hdinsight"></a>HDFS CLI med HDInsight

HDInsight ger tillgång till det distribuerade filsystemet som är lokalt anslutet till beräkningsnoderna. Detta filsystem kan nås genom att använda det gränssnitt som interagerar direkt med med HDFS och andra filsystem som har stöd för Hadoop. Nedan visas vanliga kommandon och länkar till användbara resurser.

>[!IMPORTANT]
>HDInsight-kluster debiteringen börjar när ett kluster har skapats och stoppas när klustret tas bort. Debiteringen görs i förväg per minut, så du ska alltid ta bort ditt kluster när det inte används. Information om hur du tar bort ett kluster finns i vår [artikeln på ämnet](../../hdinsight/hdinsight-delete-cluster.md). Data som lagras i ett lagringskonto med Data Lake Storage Gen2 aktiverat kvarstår dock även efter ett HDInsight-klustret tas bort.

### <a name="get-a-list-of-files-or-directories"></a>Hämta en lista över filer eller kataloger

    hdfs dfs -ls <args>

### <a name="create-a-directory"></a>Skapa en katalog

    hdfs dfs -mkdir [-p] <paths>

### <a name="delete-a-file-or-a-directory"></a>Ta bort en fil eller katalog

    hdfs dfs -rm [-skipTrash] URI [URI ...]

### <a name="use-the-hdfs-cli-with-an-hdinsight-hadoop-cluster-on-linux"></a>Använd HDFS-CLI med ett HDInsight Hadoop-kluster på Linux

Först måste upprätta [fjärråtkomst till tjänster](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-information#remote-access-to-services). Om du väljer [SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) exempelkoden PowerShell skulle se ut så här:

```PowerShell
#Connect to the cluster via SSH.
ssh sshuser@clustername-ssh.azurehdinsight.net
#Execute basic HDFS commands. Display the hierarchy.
hdfs dfs -ls /
#Create a sample directory.
hdfs dfs -mkdir /samplefolder
```
Anslutningssträngen kan hittas på den ”SSH- och logga in” på bladet för HDInsight-kluster i Azure-portalen. SSH-autentiseringsuppgifter angavs vid tidpunkten för klustret skapas.

Mer information om HDFS CLI finns i den [officiella dokumentationen](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html) och [Guide för HDFS-behörigheter](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html). Läs mer om ACL: er i Databricks i den [hemligheter CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#secrets-cli). 

## <a name="hdfs-cli-with-azure-databricks"></a>HDFS CLI med Azure Databricks

Databricks ger en enkel att använda CLI som bygger på Databricks REST API. Projekt för öppen källkod finns på [GitHub](https://github.com/databricks/databricks-cli). Nedan visas vanliga kommandon.

### <a name="get-a-list-of-files-or-directories"></a>Hämta en lista över filer eller kataloger

    dbfs ls [-l]

### <a name="create-a-directory"></a>Skapa en katalog

    dbfs mkdirs

### <a name="delete-a-file"></a>Ta bort en fil

    dbfs rm [-r]

Ett annat sätt att interagera med Databricks är anteckningsböcker. När en bärbar dator har ett primärt språk, kan du blanda språk genom att ange språket magiska språkkommandot % språk i början av en cell. Mer specifikt kan % sh du köra shell kod i anteckningsboken nästan samma sätt som i exemplet för HDInsight tidigare i den här artikeln.

### <a name="get-a-list-of-files-or-directories"></a>Hämta en lista över filer eller kataloger

    %sh ls <args>

### <a name="create-a-directory"></a>Skapa en katalog

    %sh mkdir [-p] <paths>

### <a name="delete-a-file-or-a-directory"></a>Ta bort en fil eller katalog

    %sh rm [-skipTrash] URI [URI ...]

När du har startat Spark-kluster i Azure Databricks, ska du skapa en ny anteckningsbok. Exempelskript för notebook ska se ut så här:

    #Execute basic HDFS commands invoking the shell. Display the hierarchy.
    %sh ls /
    #Create a sample directory.
    %sh mkdir /samplefolder
    #Get the ACL of the newly created directory.
    hdfs dfs -getfacl /samplefolder

Mer information om Databricks CLI finns i den [officiella dokumentationen](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html). Läs mer på bärbara datorer, den [anteckningsböcker](https://docs.azuredatabricks.net/user-guide/notebooks/index.html) i dokumentationen för.

## <a name="set-file-and-directory-level-permissions"></a>Ange fil- och serverbehörigheter

Du ställa in och få behörigheter på fil- och katalog. Här är några kommandon för att komma igång. 

Mer information om fil- och behörigheter på kolumnnivå för filsystemet Azure Data Lake Gen2 finns [åtkomstkontroll i Azure Data Lake Storage Gen2](storage-data-lake-storage-access-control.md).

### <a name="display-the-access-control-lists-acls-of-files-and-directories"></a>Visa den åtkomstkontrollistor (ACL) på filer och kataloger

    hdfs dfs -getfacl [-R] <path>

Exempel:

`hdfs dfs -getfacl -R /dir`

Se [getfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#getfacl)

### <a name="set-acls-of-files-and-directories"></a>Ange ACL: er för filer och kataloger

    hdfs dfs -setfacl [-R] [-b|-k -m|-x <acl_spec> <path>]|[--set <acl_spec> <path>]

Exempel:

`hdfs dfs -setfacl -m user:hadoop:rw- /file`

Se [setfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#setfacl)

### <a name="change-the-owner-of-files"></a>Ändra ägaren till filer

    hdfs dfs -chown [-R] <new_owner>:<users_group> <URI>

Se [ändra](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chown)

### <a name="change-group-association-of-files"></a>Ändra koppling av filer

    hdfs dfs -chgrp [-R] <group> <URI>

Se [chgrp](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chgrp)

### <a name="change-the-permissions-of-files"></a>Ändra behörigheterna för filer

    hdfs dfs -chmod [-R] <mode> <URI>

Se [chmod](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chmod)

Du kan visa den fullständiga listan med kommandon på den [Apache Hadoop 2.4.1 File System Shell Guide](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html) webbplats.

## <a name="next-steps"></a>Nästa steg

[Använda ett Azure Data Lake Storage Gen2 kompatibla konto i Azure Databricks](./data-lake-storage-quickstart-create-databricks-account.md) 