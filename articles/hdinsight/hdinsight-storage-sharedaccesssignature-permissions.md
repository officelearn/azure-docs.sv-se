---
title: Begränsa åtkomsten med signaturer för delad åtkomst – Azure HDInsight
description: Lär dig hur du använder signaturer för delad åtkomst för att begränsa HDInsight-åtkomst till data som lagras i Azure-lagringsblobar.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/14/2020
ms.openlocfilehash: d68f7dc6368c2b3de7f26f2946c5fb47237a820d
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81313935"
---
# <a name="use-azure-storage-shared-access-signatures-to-restrict-access-to-data-in-hdinsight"></a>Använda signaturer för delad åtkomst i Azure Storage för att begränsa åtkomsten till data med HDInsight

HDInsight har fullständig åtkomst till data i Azure Storage-konton som är associerade med klustret. Du kan använda signaturer för delad åtkomst i blob-behållaren för att begränsa åtkomsten till data. SAS (Shared Access Signatures) är en funktion i Azure-lagringskonton som gör att du kan begränsa åtkomsten till data. Till exempel ger skrivskyddad åtkomst till data.

> [!IMPORTANT]  
> En lösning som använder Apache Ranger kan du använda domänanslutna HDInsight. Mer information finns i [dokumentet Konfigurera domänansluten HDInsight.](./domain-joined/apache-domain-joined-configure.md)

> [!WARNING]  
> HDInsight måste ha fullständig åtkomst till standardlagringen för klustret.

## <a name="prerequisites"></a>Krav

* En SSH-klient. Mer information finns i [Ansluta till HDInsight (Apache Hadoop) med hjälp av SSH](./hdinsight-hadoop-linux-use-ssh-unix.md).

* En befintlig [lagringsbehållare](../storage/blobs/storage-quickstart-blobs-portal.md).  

* Om du använder PowerShell behöver du [Az-modulen](https://docs.microsoft.com/powershell/azure/overview).

* Om du vill använda Azure CLI och ännu inte har installerat det läser du [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

* Om du använder [Python](https://www.python.org/downloads/), version 2.7 eller senare.

* Om du använder C#måste Visual Studio vara version 2013 eller högre.

* [URI-schemat](./hdinsight-hadoop-linux-information.md#URI-and-scheme) för ditt lagringskonto. Det här `wasb://` schemat skulle `abfs://` vara för Azure Storage, `adl://` för Azure Data Lake Storage Gen2 eller för Azure Data Lake Storage Gen1. Om säker överföring är aktiverad för Azure `wasbs://`Storage, skulle URI vara . Se även [säker överföring](../storage/common/storage-require-secure-transfer.md).

* Ett befintligt HDInsight-kluster som du vill lägga till en signatur för delad åtkomst i. Om inte, kan du använda Azure PowerShell för att skapa ett kluster och lägga till en signatur för delad åtkomst när klustret skapas.

* Exempelfilerna [https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature)från . Den här databasen innehåller följande objekt:

  * Ett Visual Studio-projekt som kan skapa en lagringsbehållare, lagrad princip och SAS för användning med HDInsight
  * Ett Python-skript som kan skapa en lagringsbehållare, lagrad princip och SAS för användning med HDInsight
  * Ett PowerShell-skript som kan skapa ett HDInsight-kluster och konfigurera det så att det använder SAS. En uppdaterad version används längre nedan.
  * En exempelfil:`hdinsight-dotnet-python-azure-storage-shared-access-signature-master\sampledata\sample.log`

## <a name="shared-access-signatures"></a>Signaturer för delad åtkomst

Det finns två former av signaturer för delad åtkomst:

* `Ad hoc`: Starttid, förfallotid och behörigheter för SAS anges alla i SAS URI.

* `Stored access policy`: En lagrad åtkomstprincip definieras på en resursbehållare, till exempel en blob-behållare. En princip kan användas för att hantera villkor för en eller flera signaturer för delad åtkomst. När du associerar en SAS med en lagrad åtkomstprincip ärver SAS de begränsningar - starttid, utgångstid och behörigheter - som definierats för den lagrade åtkomstprincipen.

Skillnaden mellan de två formulären är viktig för ett nyckelscenario: återkallande. En SAS är en URL, så alla som skaffar SAS kan använda den. Det spelar ingen roll vem som bad om det till att börja med. Om en SAS publiceras offentligt kan den användas av vem som helst i världen. En SAS som distribueras är giltig tills en av fyra saker händer:

1. Den utgångstid som anges på SAS har uppnåtts.

2. Den utgångstid som anges i den lagrade åtkomstprincip som refereras av SAS har uppnåtts. Följande scenarier gör att utgångstiden uppnås:

    * Tidsintervallet har förflutit.
    * Principen för lagrad åtkomst ändras för att ha en förfallotid tidigare. Att ändra förfallotiden är ett sätt att återkalla SAS.

3. Den lagrade åtkomstprincipen som refereras av SAS tas bort, vilket är ett annat sätt att återkalla SAS. Om du återskapar den lagrade åtkomstprincipen med samma namn är alla SAS-token för den tidigare principen giltiga (om utgångstiden för SAS inte har passerat). Om du tänker återkalla SAS måste du använda ett annat namn om du återskapar åtkomstprincipen med en utgångstid i framtiden.

4. Kontonyckeln som användes för att skapa SAS återskapas. Om du återskapar nyckeln kan alla program som använder föregående nyckel misslyckas med autentiseringen. Uppdatera alla komponenter till den nya nyckeln.

> [!IMPORTANT]  
> En URI för delad åtkomstsignatur är associerad med den kontonyckel som används för att skapa signaturen och den associerade principen för lagrad åtkomst (om sådan finns). Om ingen lagrad åtkomstprincip anges är det enda sättet att återkalla en signatur för delad åtkomst att ändra kontonyckeln.

Vi rekommenderar att du alltid använder principer för lagrad åtkomst. När du använder lagrade principer kan du antingen återkalla signaturer eller förlänga utgångsdatumet efter behov. Stegen i det här dokumentet använder lagrade åtkomstprinciper för att generera SAS.

Mer information om signaturer för delad åtkomst finns [i Förstå SAS-modellen](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

## <a name="create-a-stored-policy-and-sas"></a>Skapa en lagrad princip och SAS

Spara SAS-token som produceras i slutet av varje metod. Token kommer att se ut ungefär som följande utdata:

```output
?sv=2018-03-28&sr=c&si=myPolicyPS&sig=NAxefF%2BrR2ubjZtyUtuAvLQgt%2FJIN5aHJMj6OsDwyy4%3D
```

### <a name="using-powershell"></a>Använda PowerShell

Ersätt `RESOURCEGROUP` `STORAGEACCOUNT`, `STORAGECONTAINER` och med lämpliga värden för din befintliga lagringsbehållare. Ändra katalogen `hdinsight-dotnet-python-azure-storage-shared-access-signature-master` till `-File` eller ändra parametern `Set-AzStorageblobcontent`så att den innehåller den absoluta sökvägen för . Ange följande PowerShell-kommando:

```powershell
$resourceGroupName = "RESOURCEGROUP"
$storageAccountName = "STORAGEACCOUNT"
$containerName = "STORAGECONTAINER"
$policy = "myPolicyPS"

# Login to your Azure subscription
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

# Get the access key for the Azure Storage account
$storageAccountKey = (Get-AzStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $storageAccountName)[0].Value

# Create an Azure Storage context
$storageContext = New-AzStorageContext `
                                -StorageAccountName $storageAccountName `
                                -StorageAccountKey $storageAccountKey

# Create a stored access policy for the Azure storage container
New-AzStorageContainerStoredAccessPolicy `
   -Container $containerName `
   -Policy $policy `
   -Permission "rl" `
   -ExpiryTime "12/31/2025 08:00:00" `
   -Context $storageContext

# Get the stored access policy or policies for the Azure storage container
Get-AzStorageContainerStoredAccessPolicy `
    -Container $containerName `
    -Context $storageContext

# Generates an SAS token for the Azure storage container
New-AzStorageContainerSASToken `
    -Name $containerName `
    -Policy $policy `
    -Context $storageContext

<# Removes a stored access policy from the Azure storage container
Remove-AzStorageContainerStoredAccessPolicy `
    -Container $containerName `
    -Policy $policy `
    -Context $storageContext
#>

# upload a file for a later example
Set-AzStorageblobcontent `
    -File "./sampledata/sample.log" `
    -Container $containerName `
    -Blob "samplePS.log" `
    -Context $storageContext
```

### <a name="using-azure-cli"></a>Använda Azure CLI

Användningen av variabler i det här avsnittet baseras på en Windows-miljö. Små variationer kommer att behövas för bash eller andra miljöer.

1. Ersätt `STORAGEACCOUNT`och `STORAGECONTAINER` med lämpliga värden för din befintliga lagringsbehållare.

    ```azurecli
    # set variables
    set AZURE_STORAGE_ACCOUNT=STORAGEACCOUNT
    set AZURE_STORAGE_CONTAINER=STORAGECONTAINER

    #Login
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription SUBSCRIPTION

    # Retrieve the primary key for the storage account
    az storage account keys list --account-name %AZURE_STORAGE_ACCOUNT% --query "[0].{PrimaryKey:value}" --output table
    ```

2. Ange den hämtade primärnyckeln till en variabel för senare användning. Ersätt `PRIMARYKEY` med det hämtade värdet i föregående steg och ange sedan kommandot nedan:

    ```console
    #set variable for primary key
    set AZURE_STORAGE_KEY=PRIMARYKEY
    ```

3. Ändra katalogen `hdinsight-dotnet-python-azure-storage-shared-access-signature-master` till `--file` eller ändra parametern `az storage blob upload`så att den innehåller den absoluta sökvägen för . Kör de återstående kommandona:

    ```azurecli
    # Create stored access policy on the containing object
    az storage container policy create --container-name %AZURE_STORAGE_CONTAINER% --name myPolicyCLI --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT% --expiry 2025-12-31 --permissions rl

    # List stored access policies on a containing object
    az storage container policy list --container-name %AZURE_STORAGE_CONTAINER% --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT%

    # Generate a shared access signature for the container
    az storage container generate-sas --name myPolicyCLI --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT%

    # Reversal
    # az storage container policy delete --container-name %AZURE_STORAGE_CONTAINER% --name myPolicyCLI --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT%

    # upload a file for a later example
    az storage blob upload --container-name %AZURE_STORAGE_CONTAINER% --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT% --name sampleCLI.log --file "./sampledata/sample.log"
    ```

### <a name="using-python"></a>Använda Python

Öppna `SASToken.py` filen och `storage_account_name` `storage_account_key`ersätt `storage_container_name` , och med lämpliga värden för din befintliga lagringsbehållare och kör sedan skriptet.

Du kan behöva `pip install --upgrade azure-storage` köra om felmeddelandet `ImportError: No module named azure.storage`visas .

### <a name="using-c"></a>Använda C\#

1. Öppna lösningen i Visual Studio.

2. Högerklicka på **SASExample-projektet** i Solution Explorer och välj **Egenskaper**.

3. Välj **Inställningar** och lägg till värden för följande poster:

    |Objekt |Beskrivning |
    |---|---|
    |LagringAnslutningssträngning|Anslutningssträngen för det lagringskonto som du vill skapa en lagrad princip och SAS för. Formatet ska `DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey` vara `myaccount` där är namnet på `mykey` ditt lagringskonto och är nyckeln till lagringskontot.|
    |ContainerName|Behållaren i lagringskontot som du vill begränsa åtkomsten till.|
    |SASPolicyName|Namnet som ska användas för den lagrade principen att skapa.|
    |FileToUpload|Sökvägen till en fil som överförs till behållaren.|

4. Kör projektet. Spara SAS-principtoken, lagringskontonamnet och behållarnamnet. Dessa värden används när lagringskontot associeras med ditt HDInsight-kluster.

## <a name="use-the-sas-with-hdinsight"></a>Använda SAS med HDInsight

När du skapar ett HDInsight-kluster måste du ange ett primärt lagringskonto. Du kan också ange ytterligare lagringskonton. Båda dessa metoder för att lägga till lagring kräver fullständig åtkomst till lagringskonton och behållare som används.

Använd en signatur för delad åtkomst för att begränsa behållaråtkomsten. Lägg till en anpassad post i **kärnplatskonfigurationen** för klustret. Du kan lägga till posten när klustret skapas med PowerShell eller efter att klustret har skapats med Ambari.

### <a name="create-a-cluster-that-uses-the-sas"></a>Skapa ett kluster som använder SAS

Ersätt `CLUSTERNAME` `RESOURCEGROUP`, `DEFAULTSTORAGEACCOUNT` `STORAGECONTAINER`, `STORAGEACCOUNT`, `TOKEN` , och med lämpliga värden. Ange PowerShell-kommandon:

```powershell
$clusterName = 'CLUSTERNAME'
$resourceGroupName = 'RESOURCEGROUP'

# Replace with the Azure data center you want to the cluster to live in
$location = 'eastus'

# Replace with the name of the default storage account TO BE CREATED
$defaultStorageAccountName = 'DEFAULTSTORAGEACCOUNT'

# Replace with the name of the SAS container CREATED EARLIER
$SASContainerName = 'STORAGECONTAINER'

# Replace with the name of the SAS storage account CREATED EARLIER
$SASStorageAccountName = 'STORAGEACCOUNT'

# Replace with the SAS token generated earlier
$SASToken = 'TOKEN'

# Default cluster size (# of worker nodes), version, and type
$clusterSizeInNodes = "4"
$clusterVersion = "3.6"
$clusterType = "Hadoop"

# Login to your Azure subscription
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

# Create an Azure Storage account and container
New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName `
    -Location $location `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableHttpsTrafficOnly 1

$defaultStorageAccountKey = (Get-AzStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $defaultStorageAccountName)[0].Value

$defaultStorageContext = New-AzStorageContext `
                                -StorageAccountName $defaultStorageAccountName `
                                -StorageAccountKey $defaultStorageAccountKey

# Create a blob container. This holds the default data store for the cluster.
New-AzStorageContainer `
    -Name $clusterName `
    -Context $defaultStorageContext

# Cluster login is used to secure HTTPS services hosted on the cluster
$httpCredential = Get-Credential `
    -Message "Enter Cluster login credentials" `
    -UserName "admin"

# SSH user is used to remotely connect to the cluster using SSH clients
$sshCredential = Get-Credential `
    -Message "Enter SSH user credentials" `
    -UserName "sshuser"

# Create the configuration for the cluster
$config = New-AzHDInsightClusterConfig

$config = $config | Add-AzHDInsightConfigValue `
    -Spark2Defaults @{} `
    -Core @{"fs.azure.sas.$SASContainerName.$SASStorageAccountName.blob.core.windows.net"=$SASToken}

# Create the HDInsight cluster
New-AzHDInsightCluster `
    -Config $config `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $clusterName `
    -Location $location `
    -ClusterSizeInNodes $clusterSizeInNodes `
    -ClusterType $clusterType `
    -OSType Linux `
    -Version $clusterVersion `
    -HttpCredential $httpCredential `
    -SshCredential $sshCredential `
    -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultStorageContainer $clusterName

<# REVERSAL
Remove-AzHDInsightCluster `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $clusterName

Remove-AzStorageContainer `
    -Name $clusterName `
    -Context $defaultStorageContext

Remove-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName

Remove-AzResourceGroup `
    -Name $resourceGroupName
#>
```

> [!IMPORTANT]  
> När du uppmanas att ange http/s eller SSH-användarnamn och lösenord måste du ange ett lösenord som uppfyller följande kriterier:
>
> * Måste vara minst 10 tecken långt.
> * Måste innehålla minst en siffra.
> * Måste innehålla minst ett icke-alfanumeriskt tecken.
> * Måste innehålla minst en versal eller gemener.

Det tar ett tag för skriptet att slutföras, vanligtvis cirka 15 minuter. När skriptet är klart utan några fel har klustret skapats.

### <a name="use-the-sas-with-an-existing-cluster"></a>Använda SAS med ett befintligt kluster

Om du har ett befintligt kluster kan du lägga till SAS **i kärnplatskonfigurationen** med hjälp av följande steg:

1. Öppna webbgränssnittet för Ambari för ditt kluster. Adressen till den `https://YOURCLUSTERNAME.azurehdinsight.net`här sidan är . När du uppmanas att autentisera till klustret med hjälp av administratörsnamnet (administratören) och lösenordet som du använde när du skapade klustret.

1. Navigera till **HDFS** > **Configs** > **Advanced** > Custom**core-site**.

1. Expandera avsnittet **Anpassad kärna,** rulla till slutet och välj sedan **Lägg till egenskap...**. Använd följande värden för **Nyckel** och **värde:**

    * **Nyckel:**`fs.azure.sas.CONTAINERNAME.STORAGEACCOUNTNAME.blob.core.windows.net`
    * **Värde**: SAS returneras med en av de metoder som tidigare utförts.

    Ersätt `CONTAINERNAME` med det behållarnamn som du använde med C#- eller SAS-programmet. Ersätt `STORAGEACCOUNTNAME` med det lagringskontonamn som du använde.

    Välj **Lägg till** om du vill spara den här nyckeln och värdet

1. Välj knappen **Spara** för att spara konfigurationsändringarna. När du uppmanas att lägga till en beskrivning av ändringen ("lägga till SAS-lagringsåtkomst" till exempel) och välj sedan **Spara**.

    Välj **OK** när ändringarna har slutförts.

   > [!IMPORTANT]  
   > Du måste starta om flera tjänster innan ändringen börjar gälla.

1. En listruta **för Omstart** visas. Välj **Starta om alla som påverkas** i listrutan och bekräfta sedan starta om __alla__.

    Upprepa denna process för **MapReduce2** och **YARN**.

1. När tjänsterna har startats om väljer du var och en och inaktiverar underhållsläge i listrutan **Serviceåtgärder.**

## <a name="test-restricted-access"></a>Testa begränsad åtkomst

Gör så här för att kontrollera att du bara kan läsa och lista objekt på SAS-lagringskontot.

1. Anslut till klustret. Ersätt `CLUSTERNAME` med namnet på klustret och ange följande kommando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Om du vill visa innehållet i behållaren använder du följande kommando från prompten:

    ```bash
    hdfs dfs -ls wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/
    ```

    Ersätt `SASCONTAINER` med namnet på behållaren som skapats för SAS-lagringskontot. Ersätt `SASACCOUNTNAME` med namnet på det lagringskonto som används för SAS.

    Listan innehåller filen som laddades upp när behållaren och SAS skapades.

3. Använd följande kommando för att kontrollera att du kan läsa innehållet i filen. Byt `SASCONTAINER` `SASACCOUNTNAME` ut och som i föregående steg. Ersätt `sample.log` med namnet på filen som visas i föregående kommando:

    ```bash
    hdfs dfs -text wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/sample.log
    ```

    Det här kommandot visar innehållet i filen.

4. Använd följande kommando för att hämta filen till det lokala filsystemet:

    ```bash
    hdfs dfs -get wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/sample.log testfile.txt
    ```

    Det här kommandot hämtar filen till en lokal fil med namnet **testfile.txt**.

5. Använd följande kommando för att överföra den lokala filen till en ny fil med namnet **testupload.txt** i SAS-lagringen:

    ```bash
    hdfs dfs -put testfile.txt wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/testupload.txt
    ```

    Du får ett meddelande som liknar följande text:

        put: java.io.IOException

    Det här felet uppstår eftersom lagringsplatsen endast är läs+list. Använd följande kommando för att placera data på standardlagringen för klustret, vilket är skrivbart:

    ```bash
    hdfs dfs -put testfile.txt wasbs:///testupload.txt
    ```

    Den här gången ska åtgärden slutföras.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du lägger till lagring med begränsad åtkomst till ditt HDInsight-kluster kan du lära dig andra sätt att arbeta med data i klustret:

* [Använda Apache Hive med HDInsight](hadoop/hdinsight-use-hive.md)
* [Använd MapReduce med HDInsight](hadoop/hdinsight-use-mapreduce.md)