---
title: Använda HDFS CLI med Azure Data Lake Storage Gen2
description: Introduktion till HDFS CLI för Data Lake Storage Gen2
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.subservice: data-lake-storage-gen2
ms.reviewer: artek
ms.openlocfilehash: 1d5313f3f0fff128dd09f9c9857b7dd9921ea4f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "69992209"
---
# <a name="using-the-hdfs-cli-with-data-lake-storage-gen2"></a>Använda HDFS CLI med Data Lake Storage Gen2

Du kan komma åt och hantera data i ditt lagringskonto med hjälp av ett kommandoradsgränssnitt på samma sätt som med ett [HADoop Distributed File System (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Den här artikeln innehåller några exempel som hjälper dig att komma igång.

HDInsight ger åtkomst till den distribuerade behållaren som är lokalt kopplad till beräkningsnoderna. Du kan komma åt den här behållaren med hjälp av skalet som direkt interagerar med HDFS och andra filsystem som Hadoop stöder.

Mer information om HDFS CLI finns i den [officiella dokumentationen](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html) och [HDFS-behörighetsguiden](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)

>[!NOTE]
>Om du använder Azure Databricks i stället för HDInsight och vill interagera med dina data med hjälp av ett kommandoradsgränssnitt kan du använda Databricks CLI för att interagera med databricks filsystemet. Se [Databricks CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html).

## <a name="use-the-hdfs-cli-with-an-hdinsight-hadoop-cluster-on-linux"></a>Använd HDFS CLI med ett HDInsight Hadoop-kluster på Linux

Upprätta först [fjärråtkomst till tjänster](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-information#remote-access-to-services). Om du väljer [SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) skulle exemplet PowerShell-koden se ut så här:

```powershell
#Connect to the cluster via SSH.
ssh sshuser@clustername-ssh.azurehdinsight.net
#Execute basic HDFS commands. Display the hierarchy.
hdfs dfs -ls /
#Create a sample directory.
hdfs dfs -mkdir /samplefolder
```
Anslutningssträngen finns i avsnittet "SSH + Cluster login" i HDInsight-klusterbladet i Azure-portalen. SSH-autentiseringsuppgifter angavs när klustret skapades.

>[!IMPORTANT]
>HDInsight-klusterfaktureringen startar när ett kluster har skapats och stoppas när klustret tas bort. Debiteringen görs i förväg per minut, så du ska alltid ta bort ditt kluster när det inte används. Mer information om hur du tar bort ett kluster finns i vår [artikel i ämnet](../../hdinsight/hdinsight-delete-cluster.md). Data som lagras i ett lagringskonto med Data Lake Storage Gen2 är dock kvar även efter att ett HDInsight-kluster har tagits bort.

## <a name="create-a-container"></a>Skapa en container

    hdfs dfs -D "fs.azure.createRemoteFileSystemDuringInitialization=true" -ls abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/

* Ersätt `<container-name>` platshållaren med det namn som du vill ge behållaren.

* Ersätt platshållaren `<storage-account-name>` med namnet på ditt lagringskonto.

## <a name="get-a-list-of-files-or-directories"></a>Hämta en lista över filer eller kataloger

    hdfs dfs -ls <path>

Ersätt `<path>` platshållaren med URI-urin för behållar- eller behållarmappen.

Exempel: `hdfs dfs -ls abfs://my-file-system@mystorageaccount.dfs.core.windows.net/my-directory-name`

## <a name="create-a-directory"></a>Skapa en katalog

    hdfs dfs -mkdir [-p] <path>

Ersätt `<path>` platshållaren med rotbehållarens namn eller en mapp i behållaren.

Exempel: `hdfs dfs -mkdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/`

## <a name="delete-a-file-or-directory"></a>Ta bort en fil eller katalog

    hdfs dfs -rm <path>

Ersätt `<path>` platshållaren med URI:n för den fil eller mapp som du vill ta bort.

Exempel: `hdfs dfs -rmdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/my-directory-name/my-file-name`

## <a name="display-the-access-control-lists-acls-of-files-and-directories"></a>Visa åtkomstkontrollistorna (ACL: er) för filer och kataloger

    hdfs dfs -getfacl [-R] <path>

Exempel:

`hdfs dfs -getfacl -R /dir`

Se [getfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#getfacl)

## <a name="set-acls-of-files-and-directories"></a>Ange ACL:er för filer och kataloger

    hdfs dfs -setfacl [-R] [-b|-k -m|-x <acl_spec> <path>]|[--set <acl_spec> <path>]

Exempel:

`hdfs dfs -setfacl -m user:hadoop:rw- /file`

Se [setfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#setfacl)

## <a name="change-the-owner-of-files"></a>Ändra ägare av filer

    hdfs dfs -chown [-R] <new_owner>:<users_group> <URI>

Se [chown](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chown)

## <a name="change-group-association-of-files"></a>Ändra gruppassociation av filer

    hdfs dfs -chgrp [-R] <group> <URI>

Se [chgrp](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chgrp)

## <a name="change-the-permissions-of-files"></a>Ändra behörigheter för filer

    hdfs dfs -chmod [-R] <mode> <URI>

Se [chmod](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chmod)

Du kan visa hela listan med kommandon på [Apache Hadoop 2.4.1 File System Shell Guide](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html) Webbplats.

## <a name="next-steps"></a>Nästa steg

* [Använda ett Azure Data Lake Storage Gen2-kompatibelt konto i Azure Databricks](./data-lake-storage-quickstart-create-databricks-account.md)

* [Läs mer om åtkomstkontrollistor för filer och kataloger](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)
