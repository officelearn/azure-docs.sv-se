---
title: Använda Azure-mallar för att skapa HDInsight med Azure Data Lake Storage Gen1 | Microsoft-dokument
description: Använda Azure Resource Manager-mallar för att skapa och använda HDInsight-kluster med Azure Data Lake Storage Gen1
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "66161371"
---
# <a name="create-an-hdinsight-cluster-with-azure-data-lake-storage-gen1-using-azure-resource-manager-template"></a>Skapa ett HDInsight-kluster med Azure Data Lake Storage Gen1 med azure Resource Manager-mall
> [!div class="op_single_selector"]
> * [Använda Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Använda PowerShell (för standardlagring)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Använda PowerShell (för ytterligare lagringsutrymme)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Använda Resurshanteraren](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Lär dig hur du använder Azure PowerShell för att konfigurera ett HDInsight-kluster med Azure Data Lake Storage Gen1, **som ytterligare lagring**.

För klustertyper som stöds kan DataSjölagring Gen1 användas som standardlagrings- eller ytterligare lagringskonto. När Data Lake Storage Gen1 används som ytterligare lagring skrivs fortfarande Azure Storage Blobs (Azure Storage Blobs) och de klusterrelaterade filerna (t.ex. loggar osv.) fortfarande till standardlagringen, medan de data som du vill kan lagras i ett Data Lake Storage Gen1-konto. Att använda Data Lake Storage Gen1 som ett ytterligare lagringskonto påverkar inte prestanda eller möjlighet att läsa/skriva till lagringen från klustret.

## <a name="using-data-lake-storage-gen1-for-hdinsight-cluster-storage"></a>Använda DataSjölagring Gen1 för HDInsight-klusterlagring

Här är några viktiga överväganden för att använda HDInsight med Data Lake Storage Gen1:

* Möjlighet att skapa HDInsight-kluster med åtkomst till Data Lake Storage Gen1 som standardlagring är tillgängligt för HDInsight version 3.5 och 3.6.

* Möjlighet att skapa HDInsight-kluster med åtkomst till Data Lake Storage Gen1 när ytterligare lagringsutrymme är tillgängligt för HDInsight-versionerna 3.2, 3.4, 3.5 och 3.6.

I den här artikeln etablerar vi ett Hadoop-kluster med Data Lake Storage Gen1 som ytterligare lagring. Instruktioner om hur du skapar ett Hadoop-kluster med Data Lake Storage Gen1 som standardlagring finns i [Skapa ett HDInsight-kluster med Data Lake Storage Gen1 med Azure Portal](data-lake-store-hdinsight-hadoop-use-portal.md).

## <a name="prerequisites"></a>Krav

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Innan du påbörjar de här självstudierna måste du ha:

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Installera Azure PowerShell 1.0 eller senare**. Se [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).
* **Huvudnamn för Azure Active Directory-tjänsten**. Steg i den här självstudien innehåller instruktioner om hur du skapar ett tjänsthuvudnamn i Azure AD. Du måste dock vara Azure AD-administratör för att kunna skapa ett tjänsthuvudnamn. Om du är Azure AD-administratör kan du hoppa över den här förutsättningen och fortsätta med självstudien.

    **Om du inte är Azure AD-administratör**kan du inte utföra de steg som krävs för att skapa ett tjänsthuvudnamn. I så fall måste din Azure AD-administratör först skapa ett tjänsthuvudnamn innan du kan skapa ett HDInsight-kluster med Data Lake Storage Gen1. Tjänstens huvudnamn måste också skapas med hjälp av ett certifikat, enligt beskrivningen på [Skapa ett huvudnamn för tjänsten med certifikat](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority).

## <a name="create-an-hdinsight-cluster-with-data-lake-storage-gen1"></a>Skapa ett HDInsight-kluster med Data Lake Storage Gen1
Resource Manager-mallen och förutsättningarna för att använda mallen är tillgängliga på GitHub vid [Distribuera ett HDInsight Linux-kluster med ny Data Lake Storage Gen1](https://github.com/Azure/azure-quickstart-templates/tree/master/201-hdinsight-datalake-store-azure-storage). Följ instruktionerna på den här länken för att skapa ett HDInsight-kluster med Data Lake Storage Gen1 som ytterligare lagring.

Instruktionerna på länken som nämns ovan kräver PowerShell. Innan du börjar med dessa instruktioner, se till att du loggar in på ditt Azure-konto. Öppna ett nytt Azure PowerShell-fönster från skrivbordet och ange följande utdrag. När du uppmanas att logga in kontrollerar du att du loggar in som en av prenumerationsadministratörerna/ägaren:

```
# Log in to your Azure account
Connect-AzAccount

# List all the subscriptions associated to your account
Get-AzSubscription

# Select a subscription
Set-AzContext -SubscriptionId <subscription ID>
```

Mallen distribuerar dessa resurstyper:

* [Microsoft.DataLakeStore/konton](/azure/templates/microsoft.datalakestore/accounts)
* [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft.HDInsight/kluster](/azure/templates/microsoft.hdinsight/clusters)

## <a name="upload-sample-data-to-data-lake-storage-gen1"></a>Ladda upp exempeldata till Data Lake Storage Gen1
Resource Manager-mallen skapar ett nytt Data Lake Storage Gen1-konto och associerar det med HDInsight-klustret. Du måste nu ladda upp några exempeldata till Data Lake Storage Gen1. Du behöver dessa data senare i självstudien för att köra jobb från ett HDInsight-kluster som kommer åt data i Data Lake Storage Gen1-kontot. Instruktioner om hur du laddar upp data finns i [Ladda upp en fil till ditt Data Lake Storage Gen1-konto](data-lake-store-get-started-portal.md#uploaddata). Om du behöver exempeldata att ladda upp, kan du hämta mappen **Ambulansdata** från [Azure Data Lake Git-lagringsplatsen](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData).

## <a name="set-relevant-acls-on-the-sample-data"></a>Ange relevanta åtkomstkontrollistor för exempeldata
Om du vill vara säker på att exempeldata som du överför är tillgängliga från HDInsight-klustret måste du se till att Azure AD-programmet som används för att fastställa identitet mellan HDInsight-klustret och Data Lake Storage Gen1 har åtkomst till filen/mappen som du försöker komma åt. Gör följande om du vill göra detta.

1. Hitta namnet på Azure AD-programmet som är associerat med HDInsight-klustret och Data Lake Storage Gen1-kontot. Ett sätt att leta efter namnet är att öppna det HDInsight-klusterblad som du skapade med hjälp av resource manager-mallen, klicka på fliken **KlusterAAD-identitet** och leta efter värdet för **Tjänstens huvudnamn .**
2. Nu ger åtkomst till det här Azure AD-programmet i filen/mappen som du vill komma åt från HDInsight-klustret. Information om hur du anger rätt ACL:er för filen/mappen i DataSjölagring gen1 finns [i Skydda data i DataSjölagring gen1](data-lake-store-secure-data.md#filepermissions).

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-data-lake-storage-gen1"></a>Kör testjobb i HDInsight-klustret för att använda Data Lake Storage Gen1
När du har konfigurerat ett HDInsight-kluster kan du köra testjobb i klustret för att testa att HDInsight-klustret kan komma åt Data Lake Storage Gen1. För att göra det kör vi ett exempel på Hive-jobb som skapar en tabell med hjälp av exempeldata som du laddade upp tidigare till ditt Data Lake Storage Gen1-konto.

I det här avsnittet kommer du SSH i ett HDInsight Linux-kluster och kör ett exempel Hive fråga. Om du använder en Windows-klient rekommenderar vi att du använder [https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) **PuTTY**, som kan hämtas från .

Mer information om hur du använder PuTTY finns i [Använda SSH med Linux-baserade Hadoop på HDInsight från Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

1. När du har anslutit startar du Hive CLI med följande kommando:

   ```
   hive
   ```
2. Med CLI anger du följande satser för att skapa en ny tabell med namnet **fordon** med hjälp av exempeldata i Data Lake Storage Gen1:

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


## <a name="access-data-lake-storage-gen1-using-hdfs-commands"></a>Komma åt DataSjölagring Gen1 med HDFS-kommandon
När du har konfigurerat HDInsight-klustret för att använda Data Lake Storage Gen1 kan du använda HDFS-skalkommandona för att komma åt arkivet.

I det här avsnittet kommer du SSH i en HDInsight Linux-kluster och köra HDFS kommandon. Om du använder en Windows-klient rekommenderar vi att du använder [https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) **PuTTY**, som kan hämtas från .

Mer information om hur du använder PuTTY finns i [Använda SSH med Linux-baserade Hadoop på HDInsight från Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

När du har anslutit använder du följande HDFS-filsystemkommando för att lista filerna i Data Lake Storage Gen1-kontot.

```
hdfs dfs -ls adl://<Data Lake Storage Gen1 account name>.azuredatalakestore.net:443/
```

Detta bör lista filen som du laddade upp tidigare till Data Lake Storage Gen1.

```
15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
Found 1 items
-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestoragegen1.azuredatalakestore.net:443/mynewfolder
```

Du kan också `hdfs dfs -put` använda kommandot för att överföra vissa filer `hdfs dfs -ls` till Data Lake Storage Gen1 och sedan använda för att kontrollera om filerna har överförts.


## <a name="next-steps"></a>Nästa steg
* [Kopiera data från Azure Storage Blobbar till Data Lake Storage Gen1](data-lake-store-copy-data-wasb-distcp.md)
* [Använda DataSjölagringgend1 med Azure HDInsight-kluster](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
