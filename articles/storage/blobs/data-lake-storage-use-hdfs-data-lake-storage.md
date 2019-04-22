---
title: Med hjälp av HDFS-CLI med Azure Data Lake Storage Gen2
description: Introduktion till HDFS CLI för Data Lake Storage Gen2
services: storage
author: artemuwka
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: artek
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: d1c9eff08a7b9cc50ccdca4ce798ac4d0f3d35f2
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59678028"
---
# <a name="using-the-hdfs-cli-with-data-lake-storage-gen2"></a>Med hjälp av HDFS-CLI med Data Lake Storage Gen2

Du kan komma åt och hantera data i ditt storage-konto med hjälp av ett kommandoradsgränssnitt precis som med en [Hadoop Distributed File System (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Den här artikeln innehåller några exempel som hjälper dig att komma igång.

HDInsight ger tillgång till det distribuerade filsystemet som är lokalt anslutet till beräkningsnoderna. Du kan komma åt detta filsystem med hjälp av det gränssnitt som interagerar direkt med med HDFS och andra filsystem som har stöd för Hadoop.

Mer information om HDFS CLI finns i den [officiella dokumentationen](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html) och [Guide för HDFS-behörigheter](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)

>[!NOTE]
>Om du använder Azure Databricks i stället för HDInsight och du vill interagera med dina data med hjälp av ett kommandoradsgränssnitt, kan du använda Databricks CLI för att interagera med Databricks-filsystemet. Se [Databricks CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html).

## <a name="use-the-hdfs-cli-with-an-hdinsight-hadoop-cluster-on-linux"></a>Använd HDFS-CLI med ett HDInsight Hadoop-kluster på Linux

Först måste upprätta [fjärråtkomst till tjänster](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-information#remote-access-to-services). Om du väljer [SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) exempelkoden PowerShell skulle se ut så här:

```powershell
#Connect to the cluster via SSH.
ssh sshuser@clustername-ssh.azurehdinsight.net
#Execute basic HDFS commands. Display the hierarchy.
hdfs dfs -ls /
#Create a sample directory.
hdfs dfs -mkdir /samplefolder
```
Anslutningssträngen kan hittas på den ”SSH- och logga in” på bladet för HDInsight-kluster i Azure-portalen. SSH-autentiseringsuppgifter angavs vid tidpunkten för klustret skapas.

>[!IMPORTANT]
>HDInsight-kluster debiteringen börjar när ett kluster har skapats och stoppas när klustret tas bort. Debiteringen görs i förväg per minut, så du ska alltid ta bort ditt kluster när det inte används. Information om hur du tar bort ett kluster finns i vår [artikeln på ämnet](../../hdinsight/hdinsight-delete-cluster.md). Data som lagras i ett lagringskonto med Data Lake Storage Gen2 aktiverat kvarstår dock även efter ett HDInsight-klustret tas bort.

## <a name="create-a-file-system"></a>Skapa ett filsystem

    hdfs dfs -D "fs.azure.createRemoteFileSystemDuringInitialization=true" -ls abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/

* Ersätt platshållaren `<file-system-name>` med det namn som du vill ge filsystemet.

* Ersätt platshållaren `<storage-account-name>` med namnet på ditt lagringskonto.

## <a name="get-a-list-of-files-or-directories"></a>Hämta en lista över filer eller kataloger

    hdfs dfs -ls <path>

Ersätt den `<path>` med URI: N för filsystemet eller filens systemmapp.

Exempel: `hdfs dfs -ls abfs://my-file-system@mystorageaccount.dfs.core.windows.net/my-directory-name`

## <a name="create-a-directory"></a>Skapa en katalog

    hdfs dfs -mkdir [-p] <path>

Ersätt den `<path>` med rot filsystemets namn eller en mapp i ditt filsystem.

Exempel: `hdfs dfs -mkdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/`

## <a name="delete-a-file-or-directory"></a>Ta bort en fil eller katalog

    hdfs dfs -rm <path>

Ersätt den `<path>` med URI för filen eller mappen som du vill ta bort.

Exempel: `hdfs dfs -rmdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/my-directory-name/my-file-name`

## <a name="display-the-access-control-lists-acls-of-files-and-directories"></a>Visa den åtkomstkontrollistor (ACL) på filer och kataloger

    hdfs dfs -getfacl [-R] <path>

Exempel:

`hdfs dfs -getfacl -R /dir`

Se [getfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#getfacl)

## <a name="set-acls-of-files-and-directories"></a>Ange ACL: er för filer och kataloger

    hdfs dfs -setfacl [-R] [-b|-k -m|-x <acl_spec> <path>]|[--set <acl_spec> <path>]

Exempel:

`hdfs dfs -setfacl -m user:hadoop:rw- /file`

Se [setfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#setfacl)

## <a name="change-the-owner-of-files"></a>Ändra ägaren till filer

    hdfs dfs -chown [-R] <new_owner>:<users_group> <URI>

Se [ändra](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chown)

## <a name="change-group-association-of-files"></a>Ändra koppling av filer

    hdfs dfs -chgrp [-R] <group> <URI>

Se [chgrp](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chgrp)

## <a name="change-the-permissions-of-files"></a>Ändra behörigheterna för filer

    hdfs dfs -chmod [-R] <mode> <URI>

Se [chmod](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chmod)

Du kan visa den fullständiga listan med kommandon på den [Apache Hadoop 2.4.1 File System Shell Guide](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html) webbplats.

## <a name="next-steps"></a>Nästa steg

* [Använda ett Azure Data Lake Storage Gen2 kompatibla konto i Azure Databricks](./data-lake-storage-quickstart-create-databricks-account.md)

* [Lär dig mer om åtkomstkontrollistor för filer och kataloger](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)
