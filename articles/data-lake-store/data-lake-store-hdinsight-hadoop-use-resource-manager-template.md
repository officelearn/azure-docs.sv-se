---
title: Använd Azure-mallar för att skapa HDInsight med Azure Data Lake Storage Gen1 | Microsoft Docs
description: Använd Azure Resource Manager-mallar för att skapa och använda HDInsight-kluster med Azure Data Lake Storage Gen1
services: data-lake-store,hdinsight
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 8ef8152f-2121-461e-956c-51c55144919d
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: b09ca2cc358107c5f95fe3426351d380380db3c2
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "58880142"
---
# <a name="create-an-hdinsight-cluster-with-azure-data-lake-storage-gen1-using-azure-resource-manager-template"></a>Skapa ett HDInsight-kluster med Azure Data Lake Storage Gen1 med hjälp av Azure Resource Manager-mall
> [!div class="op_single_selector"]
> * [Använda portalen](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Med hjälp av PowerShell (för standardlagring)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Med hjälp av PowerShell (för ytterligare lagring)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Med Resource Manager](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Lär dig hur du använder Azure PowerShell för att konfigurera ett HDInsight-kluster med Azure Data Lake Storage Gen1 **som ytterligare lagringsutrymme**.

Data Lake Storage Gen1 kan användas som standardlagring eller ytterligare storage-konto för stöds klustertyper. När Data Lake Storage Gen1 används som ytterligare lagringsutrymme, standardkontot för lagring för klustren kommer fortfarande att Azure Storage BLOB-objekt (WASB) och de klusterrelaterade filerna (till exempel loggar, osv.) skrivs fortfarande till standardlagring, samtidigt som de data som du vill processen kan lagras i ett Data Lake Storage Gen1-konto. Med Data Lake Storage Gen1 som ett ytterligare lagringskonto påverkar inte prestanda eller möjligheten att läsa eller skriva till lagring från klustret.

## <a name="using-data-lake-storage-gen1-for-hdinsight-cluster-storage"></a>Med hjälp av Data Lake Storage Gen1 för HDInsight-klusterlagring

Här följer några viktiga överväganden för användning av HDInsight med Data Lake Storage Gen1:

* Alternativet för att skapa HDInsight-kluster med åtkomst till Data Lake Storage Gen1 som standardlagring är tillgänglig för HDInsight version 3.5 och 3.6.

* Alternativet för att skapa HDInsight-kluster med åtkomst till Data Lake Storage Gen1 som ytterligare lagring är tillgänglig för HDInsight version 3.2, 3.4, 3.5 och 3.6.

I den här artikeln har etablera vi ett Hadoop-kluster med Data Lake Storage Gen1 som ytterligare lagringsutrymme. Anvisningar för hur du skapar ett Hadoop-kluster med Data Lake Storage Gen1 som standardlagringsutrymme finns i [skapa ett HDInsight-kluster med Data Lake Storage Gen1 med hjälp av Azure Portal](data-lake-store-hdinsight-hadoop-use-portal.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Innan du påbörjar de här självstudierna måste du ha:

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Installera Azure PowerShell 1.0 eller senare**. Se [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).
* **Azure Active Directory-tjänstobjekt**. I den här självstudiekursen innehåller instruktioner om hur du skapar ett huvudnamn för tjänsten i Azure AD. Du måste vara en Azure AD-administratör för att kunna skapa ett huvudnamn för tjänsten. Om du är en Azure AD-administratör kan du hoppa över det här kravet och fortsätter med självstudien.

    **Om du inte är en Azure AD-administratör**, du kommer inte att kunna utföra stegen som krävs för att skapa ett huvudnamn för tjänsten. I detta fall är skapa Azure AD-administratören först ett huvudnamn för tjänsten innan du kan skapa ett HDInsight-kluster med Data Lake Storage Gen1. Dessutom tjänstens huvudnamn måste skapas med hjälp av ett certifikat, enligt beskrivningen i [skapa tjänstens huvudnamn med certifikat](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority).

## <a name="create-an-hdinsight-cluster-with-data-lake-storage-gen1"></a>Skapa ett HDInsight-kluster med Data Lake Storage Gen1
Resource Manager-mallen och krav för att använda mallen, är tillgängliga på GitHub på [distribuera ett HDInsight Linux-kluster med nya Data Lake Storage Gen1](https://github.com/Azure/azure-quickstart-templates/tree/master/201-hdinsight-datalake-store-azure-storage). Följ anvisningarna i den här länken för att skapa ett HDInsight-kluster med Data Lake Storage Gen1 som ytterligare lagringsutrymme.

Anvisningarna i länken ovan kräver PowerShell. Innan du börjar med dessa anvisningar måste du kontrollera att du loggar in på ditt Azure-konto. Öppna ett nytt Azure PowerShell-fönster på skrivbordet och ange följande kodavsnitt. När du uppmanas att logga in, kontrollera att du loggar in som en av prenumerationens Administratörer/ägare:

```
# Log in to your Azure account
Connect-AzAccount

# List all the subscriptions associated to your account
Get-AzSubscription

# Select a subscription
Set-AzContext -SubscriptionId <subscription ID>
```

Mallen distribuerar dessa typer av resurser:

* [Microsoft.DataLakeStore/accounts](/azure/templates/microsoft.datalakestore/accounts)
* [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft.HDInsight/clusters](/azure/templates/microsoft.hdinsight/clusters)

## <a name="upload-sample-data-to-data-lake-storage-gen1"></a>Ladda upp exempeldata till Data Lake Storage Gen1
Resource Manager-mallen skapar ett nytt Data Lake Storage Gen1-konto och associerar det med HDInsight-klustret. Du måste nu ladda upp exempeldata till Data Lake Storage Gen1. Du behöver dessa data senare i guiden för att köra jobb från ett HDInsight-kluster som har åtkomst till data i Data Lake Storage Gen1-konto. Anvisningar för hur du överför data finns i [överföra en fil till ditt Data Lake Storage Gen1 konto](data-lake-store-get-started-portal.md#uploaddata). Om du behöver exempeldata att ladda upp, kan du hämta mappen **Ambulansdata** från [Azure Data Lake Git-lagringsplatsen](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData).

## <a name="set-relevant-acls-on-the-sample-data"></a>Ange relevant ACL: er för exempeldata
Om du vill kontrollera att du laddar upp exempeldata är tillgänglig från HDInsight-kluster, måste du kontrollera att Azure AD-programmet som används för att upprätta identitet mellan HDInsight-kluster och Data Lake Storage Gen1 har åtkomst till filen/mappen du vill åtkomst. Utför följande steg om du vill göra detta.

1. Hitta namnet på Azure AD-programmet som är associerad med HDInsight-klustret och Gen1 för Data Lake Storage-kontot. Ett sätt att Sök efter namnet på är att öppna bladet HDInsight-kluster som du skapade med hjälp av Resource Manager-mall klickar du på den **kluster AAD-identitet** fliken och leta efter värdet för **visningsnamn för tjänstens huvudnamn**.
2. Nu ger åtkomst till den här Azure AD-program på filen/mappen som du vill komma åt från HDInsight-kluster. Om du vill ange rätt ACL: er på filen/mappen i Data Lake Storage Gen1 Se [skydda data i Data Lake Storage Gen1](data-lake-store-secure-data.md#filepermissions).

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-data-lake-storage-gen1"></a>Kör testjobb på HDInsight-kluster för att använda Data Lake Storage Gen1
När du har konfigurerat ett HDInsight-kluster, kan du köra testjobb på klustret för att testa att HDInsight-klustret har åtkomst till Data Lake Storage Gen1. Om du vill göra det kör vi ett Hive-jobb som skapar en tabell med exempeldata som du tidigare överförde till ditt Data Lake Storage Gen1-konto.

I det här avsnittet kommer du att SSH till en HDInsight Linux-kluster och kör den en exempelfråga för Hive. Om du använder en Windows-klient, bör du använda **PuTTY**, som kan laddas ned från [ https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html ](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Läs mer om hur du använder PuTTY [använda SSH med Linux-baserat Hadoop i HDInsight från Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

1. När du är ansluten, startar du Hive-CLI med hjälp av följande kommando:

   ```
   hive
   ```
2. Med hjälp av CLI anger du följande instruktioner för att skapa en ny tabell med namnet **fordon** med exempeldata i Data Lake Storage Gen1:

   ```
   DROP TABLE vehicles;
   CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://<mydatalakestoragegen1>.azuredatalakestore.net:443/';
   SELECT * FROM vehicles LIMIT 10;
   ```

   Du bör se utdata som liknar följande:

   ```
   1,1,2014-09-14 00:00:03,46.81006,-92.08174,51,S,1
   1,2,2014-09-14 00:00:06,46.81006,-92.08174,13,NE,1
   1,3,2014-09-14 00:00:09,46.81006,-92.08174,48,NE,1
   1,4,2014-09-14 00:00:12,46.81006,-92.08174,30,W,1
   1,5,2014-09-14 00:00:15,46.81006,-92.08174,47,S,1
   1,6,2014-09-14 00:00:18,46.81006,-92.08174,9,S,1
   1,7,2014-09-14 00:00:21,46.81006,-92.08174,53,N,1
   1,8,2014-09-14 00:00:24,46.81006,-92.08174,63,SW,1
   1,9,2014-09-14 00:00:27,46.81006,-92.08174,4,NE,1
   1,10,2014-09-14 00:00:30,46.81006,-92.08174,31,N,1
   ```


## <a name="access-data-lake-storage-gen1-using-hdfs-commands"></a>Få åtkomst till Data Lake Storage Gen1 med hjälp av HDFS-kommandon
När du har konfigurerat HDInsight-kluster för att använda Data Lake Storage Gen1 kan använda du HDFS shell-kommandon för åtkomst till store.

I det här avsnittet kommer du att SSH till ett HDInsight Linux-kluster och köra kommandona HDFS. Om du använder en Windows-klient, bör du använda **PuTTY**, som kan laddas ned från [ https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html ](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Läs mer om hur du använder PuTTY [använda SSH med Linux-baserat Hadoop i HDInsight från Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

När du är ansluten, Använd kommandot filsystem HDFS för att visa filer i Data Lake Storage Gen1-konto.

```
hdfs dfs -ls adl://<Data Lake Storage Gen1 account name>.azuredatalakestore.net:443/
```

Du bör nu se den fil som du tidigare laddade upp till Data Lake Storage Gen1.

```
15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
Found 1 items
-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestoragegen1.azuredatalakestore.net:443/mynewfolder
```

Du kan också använda den `hdfs dfs -put` kommando för att ladda upp några filer till Data Lake Storage Gen1 och sedan använda `hdfs dfs -ls` att verifiera om filerna har laddats upp.


## <a name="next-steps"></a>Nästa steg
* [Kopiera data från Azure Storage BLOB till Data Lake Storage Gen1](data-lake-store-copy-data-wasb-distcp.md)
* [Använda Data Lake Storage Gen1 med Azure HDInsight-kluster](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
