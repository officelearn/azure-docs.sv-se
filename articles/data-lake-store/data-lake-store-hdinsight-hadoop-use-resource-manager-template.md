---
title: "Använda Azure-mallar för att skapa HDInsight och Data Lake Store | Microsoft Docs"
description: "Använd Azure Resource Manager-mallar för att skapa och använda HDInsight-kluster med Azure Data Lake Store"
services: data-lake-store,hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 8ef8152f-2121-461e-956c-51c55144919d
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: 8c5afc96cc8101345f00b5d435e9f393d22672de
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2017
---
# <a name="create-an-hdinsight-cluster-with-data-lake-store-using-azure-resource-manager-template"></a>Skapa ett HDInsight-kluster med Data Lake Store med Azure Resource Manager-mall
> [!div class="op_single_selector"]
> * [Använda portalen](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Med hjälp av PowerShell (för standardlagring)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Med hjälp av PowerShell (för ytterligare lagringsutrymme)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Med Resource Manager](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Lär dig hur du konfigurerar ett HDInsight-kluster med Azure Data Lake Store med hjälp av Azure PowerShell **som ytterligare lagringsutrymme**.

För stöds klustertyper användas Data Lake Store som standardlagring eller ytterligare storage-konto. När du använder Data Lake Store som ytterligare lagringsutrymme standardkontot för lagring för kluster kommer fortfarande att Azure Storage BLOB (WASB) och kluster-relaterade-filer (till exempel loggar osv.) är fortfarande skrivs till standardlagring medan de data som du vill bearbeta kan lagras i ett Data Lake Store-konto. Med Data Lake Store som ett ytterligare storage-konto inte påverkar prestanda eller ge möjlighet att läsa eller skriva till lagring från klustret.

## <a name="using-data-lake-store-for-hdinsight-cluster-storage"></a>Med hjälp av Data Lake Store för HDInsight klusterlagring

Här följer några viktiga överväganden när för att använda HDInsight med Data Lake Store:

* Alternativet för att skapa HDInsight-kluster med åtkomst till Data Lake Store som standard lagringsutrymmet är tillgängligt för HDInsight version 3.5 och 3,6.

* Alternativet för att skapa HDInsight-kluster med åtkomst till Data Lake Store som ytterligare lagringsutrymmet är tillgängligt för HDInsight version 3.2, 3.4, 3.5 och 3,6.

I den här artikeln etablera vi ett Hadoop-kluster med Data Lake Store som ytterligare lagringsutrymme. Anvisningar om hur du skapar ett Hadoop-kluster med Data Lake Store som standardlagring finns [skapar ett HDInsight-kluster med Data Lake Store med hjälp av Azure Portal](data-lake-store-hdinsight-hadoop-use-portal.md).

## <a name="prerequisites"></a>Förutsättningar
Innan du påbörjar de här självstudierna måste du ha:

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Installera Azure PowerShell 1.0 eller senare**. Se [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).
* **Azure Active Directory Service Principal**. Stegen i den här kursen ger instruktioner för hur du skapar ett huvudnamn för tjänsten i Azure AD. Du måste dock vara en Azure AD-administratör för att kunna skapa ett huvudnamn för tjänsten. Om du är administratör för Azure AD kan du hoppa över det här kravet och fortsätta med guiden.

    **Om du inte är en Azure AD-administratör**, kommer du inte kunna utföra stegen som krävs för att skapa ett huvudnamn för tjänsten. I sådana fall måste din Azure AD-administratör först skapa ett huvudnamn för tjänsten innan du kan skapa ett HDInsight-kluster med Data Lake Store. Dessutom tjänstens huvudnamn måste skapas med hjälp av ett certifikat, enligt beskrivningen i [skapa ett huvudnamn för tjänsten med certifikat](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-certificate-from-certificate-authority).

## <a name="create-an-hdinsight-cluster-with-azure-data-lake-store"></a>Skapa ett HDInsight-kluster med Azure Data Lake Store
Resource Manager-mallen och krav för att använda mallen finns på GitHub på [distribuera ett HDInsight Linux-kluster med nya Data Lake Store](https://github.com/Azure/azure-quickstart-templates/tree/master/201-hdinsight-datalake-store-azure-storage). Följ anvisningarna i den här länken för att skapa ett HDInsight-kluster med Azure Data Lake Store som det extra lagringsutrymmet.

Anvisningarna i länken ovan kräver PowerShell. Innan du börjar med dessa instruktioner, kontrollera att du loggar in på ditt Azure-konto. Öppna ett nytt Azure PowerShell-fönster på skrivbordet och ange följande kodavsnitt. När du uppmanas att logga in, se till att logga in som en av prenumerationens admininistratörer/ägare:

```
# Log in to your Azure account
Login-AzureRmAccount

# List all the subscriptions associated to your account
Get-AzureRmSubscription

# Select a subscription
Set-AzureRmContext -SubscriptionId <subscription ID>
```

## <a name="upload-sample-data-to-the-azure-data-lake-store"></a>Ladda upp exempeldata till Azure Data Lake Store
Resource Manager-mallen skapar ett nytt Data Lake Store-konto och associerar den med HDInsight-klustret. Du måste ladda upp exempeldata till Data Lake Store. Du behöver den här informationen senare i guiden för att köra jobb från ett HDInsight-kluster som har åtkomst till data i Data Lake Store. Instruktioner om hur du överför data finns [överföra en fil till din Data Lake Store](data-lake-store-get-started-portal.md#uploaddata). Om du behöver exempeldata att ladda upp, kan du hämta mappen **Ambulansdata** från [Azure Data Lake Git-lagringsplatsen](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData).

## <a name="set-relevant-acls-on-the-sample-data"></a>Ange relevant ACL: er för exempeldata
Om du vill kontrollera att du laddar upp exempeldata är tillgänglig från HDInsight-kluster, måste du kontrollera att Azure AD-program som används för att upprätta mellan HDInsight-kluster och Data Lake Store har åtkomst till filen/mappen du försöker komma åt. Utför följande steg om du vill göra detta.

1. Hitta namnet på Azure AD-program som är associerat med HDInsight-kluster och Data Lake Store. Klicka på ett sätt att söka efter namnet på är att öppna bladet för HDInsight-kluster som du skapat med Resource Manager-mall i **kluster-AAD-identitet** fliken och Sök efter värdet för **tjänstens huvudnamn visningsnamn**.
2. Nu kan ge åtkomst till Azure AD-program på filen/mappen som du vill komma åt från HDInsight-klustret. Om du vill ange rätt åtkomstkontrollistor på filen/mappen i Data Lake Store, se [skydda data i Data Lake Store](data-lake-store-secure-data.md#filepermissions).

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-the-data-lake-store"></a>Kör testjobb i HDInsight-klustret för att använda Data Lake Store
När du har konfigurerat ett HDInsight-kluster, kan du köra testjobb på klustret för att testa att HDInsight-klustret har åtkomst till Data Lake Store. Om du vill göra det, kommer vi kör ett Hive-jobb som skapar en tabell med exempeldata som du tidigare har överförts till din Data Lake Store.

I det här avsnittet kommer du att SSH till ett kluster i HDInsight Linux och kör den en exempelfråga Hive. Om du använder en Windows-klient, bör du använda **PuTTY**, som kan hämtas från [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Läs mer om hur du använder PuTTY [använda SSH med Linux-baserade Hadoop i HDInsight från Windows ](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

1. När du är ansluten, startar du Hive-CLI med hjälp av följande kommando:

   ```
   hive
   ```
2. Med hjälp av CLI, ange följande instruktioner för att skapa en ny tabell med namnet **fordon** med exempeldata i Data Lake Store:

   ```
   DROP TABLE vehicles;
   CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://<mydatalakestore>.azuredatalakestore.net:443/';
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


## <a name="access-data-lake-store-using-hdfs-commands"></a>Åtkomst till Data Lake Store med hjälp av HDFS-kommandon
När du har konfigurerat HDInsight-klustret för att använda Data Lake Store kan använda du HDFS-gränssnittskommandon tillgång till store.

I det här avsnittet kommer du att SSH i Linux ett HDInsight-kluster och kör HDFS-kommandon. Om du använder en Windows-klient, bör du använda **PuTTY**, som kan hämtas från [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Läs mer om hur du använder PuTTY [använda SSH med Linux-baserade Hadoop i HDInsight från Windows ](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

När du är ansluten, Använd kommandot filesystem HDFS för att visa filer i Data Lake Store.

```
hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/
```

Du bör nu se filen som du tidigare har överförts till Data Lake Store.

```
15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
Found 1 items
-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder
```

Du kan också använda den `hdfs dfs -put` kommando för att överföra filer till Data Lake Store och sedan använda `hdfs dfs -ls` att kontrollera om filerna som har laddats upp.


## <a name="next-steps"></a>Nästa steg
* [Kopiera data från Azure Storage-Blobbar till Data Lake Store](data-lake-store-copy-data-wasb-distcp.md)
* [Använd Data Lake Store med Azure HDInsight-kluster](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
