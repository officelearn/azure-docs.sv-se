---
title: Begränsa åtkomst med signaturer för delad åtkomst - Azure HDInsight
description: Lär dig hur du använder signaturer för delad åtkomst för att begränsa HDInsight åtkomst till data som lagras i Azure storage-blobbar.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: hrasheed
ms.openlocfilehash: 7f7f6fe31afe35d9ccfd6ee33617bd7e4fbe46b7
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2019
ms.locfileid: "65409550"
---
# <a name="use-azure-storage-shared-access-signatures-to-restrict-access-to-data-in-hdinsight"></a>Använd Azure Storage signaturer för delad åtkomst för att begränsa åtkomsten till data i HDInsight

HDInsight har fullständig åtkomst till data i Azure Storage-konton som är associerade med klustret. Du kan använda signaturer för delad åtkomst för blob-behållaren för att begränsa åtkomsten till data. Signaturer för delad åtkomst (SAS) är en funktion i Azure storage-konton där du kan begränsa åtkomsten till data. Till exempel konfigurera skrivskyddad åtkomst till data.

> [!IMPORTANT]  
> Överväg att använda domänanslutna HDInsight för en lösning som använder Apache Ranger. Mer information finns i den [konfigurera domänanslutna HDInsight](./domain-joined/apache-domain-joined-configure.md) dokumentet.

> [!WARNING]  
> HDInsight måste ha fullständig åtkomst till standardlagringen för klustret.

## <a name="prerequisites"></a>Nödvändiga komponenter

* En Azure-prenumeration.

* En SSH-klient. Mer information finns i [Ansluta till HDInsight (Apache Hadoop) med hjälp av SSH](./hdinsight-hadoop-linux-use-ssh-unix.md).

* En befintlig [lagringsbehållare](../storage/blobs/storage-quickstart-blobs-portal.md).  

* Om du använder PowerShell, måste den [Az modulen](https://docs.microsoft.com/powershell/azure/overview).

* Om du vill använda Azure CLI och du inte har installerat den, se [installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

* Om du använder [Python](https://www.python.org/downloads/), version 2.7 eller senare.

* Om du använder C#, Visual Studio måste vara version 2013 eller senare.

* Den [URI-schema](./hdinsight-hadoop-linux-information.md#URI-and-scheme) för ditt lagringskonto. Detta skulle vara `wasb://` för Azure Storage, `abfs://` för Azure Data Lake Storage Gen2 eller `adl://` för Azure Data Lake Storage Gen1. Om säker överföring har aktiverats för Azure Storage eller Data Lake Storage Gen2, URI: N blir `wasbs://` eller `abfss://`respektive Se även [säker överföring](../storage/common/storage-require-secure-transfer.md).

* Ett befintligt HDInsight-kluster att lägga till en signatur för delad åtkomst till. Om inte, du kan använda Azure PowerShell för att skapa ett kluster och lägga till en signatur för delad åtkomst när klustret skapas.

* I exempel-filer från [ https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature ](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature). Den här lagringsplatsen innehåller följande objekt:

  * Ett Visual Studio-projekt som kan skapa en lagringsbehållare och lagrade princip SAS för användning med HDInsight
  * Ett Python-skript som kan skapa en lagringsbehållare och lagrade princip SAS för användning med HDInsight
  * Ett PowerShell-skript som kan skapa ett HDInsight-kluster och konfigurera den att använda SAS. Används för en uppdaterad version nedan.
  * En exempelfil: `hdinsight-dotnet-python-azure-storage-shared-access-signature-master\sampledata\sample.log`

## <a name="shared-access-signatures"></a>Signaturer för delad åtkomst

Det finns två typer av signaturer för delad åtkomst:

* Ad hoc: Starttid, förfallotid och behörigheter för SAS anges på SAS-URI.

* Lagrad åtkomstprincip: En lagrad åtkomstprincip har definierats för en resurs-behållare, till exempel en blob-behållare. En princip kan användas för att hantera begränsningar för en eller flera signaturer för delad åtkomst. När du kopplar en SAS med en lagrad åtkomstprincip, ärver SAS begränsningarna - starttid, förfallotid och behörigheter - har definierats för lagrad åtkomstprincip.

Skillnaden mellan de två formulär är viktigt för ett viktiga scenario: återkallade certifikat. En SAS är en URL så att alla som erhåller SAS kan använda det, oavsett vem som begärde att. Om en SAS publiceras offentligt, kan den användas av vem som helst i världen. En SAS som distribueras är giltig tills något av följande händer:

1. Förfallotiden som anges för SAS har nåtts.

2. Förfallotiden som anges på lagrad åtkomstprincip som refereras av SAS har nåtts. Följande scenarier orsaka förfallotiden att nå:

    * Tidsintervallet har gått ut.
    * Lagrad åtkomstprincip ändras så att den har en förfallotid tidigare. Ändra förfallotiden är ett sätt att återkalla signaturen för delad åtkomst.

3. Lagrad åtkomstprincip som refereras av SAS tas bort, vilket är ett annat sätt att återkalla signaturen för delad åtkomst. Om du återskapar lagrad åtkomstprincip med samma namn, är alla SAS-token för den föregående principen giltiga (om förfallotiden för SAS inte har passerats). Om du planerar att återkalla signaturen för delad åtkomst, måste du använda ett annat namn om du återskapar åtkomstprincip med en förfallotid i framtiden.

4. Den kontonyckel som används för att skapa SAS återskapas. Återskapar nyckeln gör att alla program som använder den tidigare nyckeln Avbryt autentisering. Uppdatera alla komponenter till den nya nyckeln.

> [!IMPORTANT]  
> En signatur för delad åtkomst URI: N är associerad med den kontonyckel som används för att skapa signaturen och den associerade lagras åtkomstprincip (om sådan finns). Om ingen lagrad åtkomstprincip anges, är det enda sättet att återkalla en signatur för delad åtkomst för att ändra kontonyckeln.

Vi rekommenderar att du alltid använder lagrade åtkomstprinciper. När du använder lagrade principer, kan du återkalla signaturer eller utöka utgångsdatum efter behov. Stegen i det här dokumentet använder lagrade åtkomstprinciper för att generera SAS.

Mer information om signaturer för delad åtkomst finns i [förstå SAS-modellen](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

## <a name="create-a-stored-policy-and-sas"></a>Skapa en princip för lagrade och SAS

Spara SAS-token som skapas i slutet av varje metod. Token ska se ut ungefär så här:

```output
?sv=2018-03-28&sr=c&si=myPolicyPS&sig=NAxefF%2BrR2ubjZtyUtuAvLQgt%2FJIN5aHJMj6OsDwyy4%3D
```

### <a name="using-powershell"></a>Använda PowerShell

Ersätt `RESOURCEGROUP`, `STORAGEACCOUNT`, och `STORAGECONTAINER` med lämpliga värden för din befintliga lagringsbehållare. Gå till katalogen för `hdinsight-dotnet-python-azure-storage-shared-access-signature-master` eller ändra den `-File` parameter som innehåller den absoluta sökvägen för `Set-AzStorageblobcontent`. Ange följande PowerShell-kommando:

```PowerShell
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

Användning av variabler i det här avsnittet baseras på en Windows-miljö. Små variationer krävs för bash eller andra miljöer.

1. Ersätt `STORAGEACCOUNT`, och `STORAGECONTAINER` med lämpliga värden för din befintliga lagringsbehållare.

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

2. Ange den primära nyckeln som hämtades till en variabel för senare användning. Ersätt `PRIMARYKEY` med det hämtade värdet i föregående steg och ange sedan kommandot nedan:

    ```azurecli
    #set variable for primary key
    set AZURE_STORAGE_KEY=PRIMARYKEY
    ```

3. Gå till katalogen för `hdinsight-dotnet-python-azure-storage-shared-access-signature-master` eller ändra den `--file` parameter som innehåller den absoluta sökvägen för `az storage blob upload`. Kör de resterande kommandona:

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

Öppna den `SASToken.py` och Ersätt `storage_account_name`, `storage_account_key`, och `storage_container_name` med lämpliga värden för din befintliga behållaren och kör skriptet.

Du kan behöva köra `pip install --upgrade azure-storage` om du får ett felmeddelande `ImportError: No module named azure.storage`.

### <a name="using-c"></a>Använd C#

1. Öppna lösningen i Visual Studio.

2. I Solution Explorer högerklickar du på den **SASExample** projektet och välj **egenskaper**.

3. Välj **inställningar** och lägga till värden för följande poster:

   * StorageConnectionString: Anslutningssträngen för lagringskontot som du vill skapa en princip för lagrade och SAS för. Formatet ska vara `DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey` där `myaccount` är namnet på ditt lagringskonto och `mykey` är nyckeln till lagringskontot.

   * ContainerName: Behållare i lagringskontot som du vill begränsa åtkomst till.

   * SASPolicyName: Namnet du använder för lagrade principen för att skapa.

   * FileToUpload: Sökväg till en fil som har överförts till behållaren.

4. Kör projektet. Spara SAS-token för principen, lagringskontonamn och behållarnamn. De här värdena används när du associerar storage-konto med ditt HDInsight-kluster.

## <a name="use-the-sas-with-hdinsight"></a>Använda SAS med HDInsight

När du skapar ett HDInsight-kluster, måste du ange en primär lagring-konto och Alternativt kan du ange ytterligare lagringskonton. Båda dessa metoder för att lägga till lagring kräver fullständig åtkomst till lagringskonton och behållare som används.

Om du vill använda en signatur för delad åtkomst för att begränsa åtkomsten till en behållare, lägger du till en anpassad post i **core-site** konfigurationen för klustret. Du kan lägga till posten när klustret skapas med hjälp av PowerShell eller när klustret har skapats med hjälp av Ambari.

### <a name="create-a-cluster-that-uses-the-sas"></a>Skapa ett kluster som använder SAS

Ersätt `CLUSTERNAME`, `RESOURCEGROUP`, `DEFAULTSTORAGEACCOUNT`, `STORAGECONTAINER`, `STORAGEACCOUNT`, och `TOKEN` med lämpliga värden. Ange PowerShell-kommandon:

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

$config = $config | Add-AzHDInsightConfigValues `
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
> När du tillfrågas om HTTP/s eller SSH-användarnamn och lösenord, måste du ange ett lösenord som uppfyller följande kriterier:
>
> * Måste vara minst 10 tecken långt.
> * Måste innehålla minst en siffra.
> * Måste innehålla minst ett icke-alfanumeriska tecken.
> * Måste innehålla minst en versal eller gemen bokstav.

Det tar en stund innan det här skriptet att slutföra, vanligen cirka 15 minuter. När skriptet har slutförts utan fel har klustret skapats.

### <a name="use-the-sas-with-an-existing-cluster"></a>Använda SAS med ett befintligt kluster

Om du har ett befintligt kluster kan du lägga till SAS till den **core-site** konfiguration med hjälp av följande steg:

1. Öppna Ambari-webbgränssnittet för klustret. Adress för den här sidan är `https://YOURCLUSTERNAME.azurehdinsight.net`. När du uppmanas, autentisera till klustret med administratörsnamn (admin) och lösenordet som du använde när du skapar klustret.

2. Från vänster sida av Ambari-webbgränssnittet väljer **HDFS** och välj sedan den **Peeringkonfigurationer** fliken mitt på sidan.

3. Välj den **Avancerat** fliken och rullar tills du hittar den **anpassad core-site** avsnittet.

4. Expandera den **anpassad core-site** avsnittet och rulla till slutet och välj sedan den **Lägg till egenskap...**  länk. Använd följande värden för den **nyckel** och **värdet** fält:

   * **nyckeln**: `fs.azure.sas.CONTAINERNAME.STORAGEACCOUNTNAME.blob.core.windows.net`
   * **Värde**: SAS som returneras av en av de metoder som körts tidigare.

     Ersätt `CONTAINERNAME` med behållaren namnet du använde med den C# eller SAS-program. Ersätt `STORAGEACCOUNTNAME` med namnet du använde.

5. Klicka på den **Lägg till** knappen för att spara den här nyckeln och värdet och klicka sedan på den **spara** för att spara konfigurationsändringarna. När du uppmanas, lägga till en beskrivning av ändringen (”att lägga till SAS-lagringsåtkomst” till exempel) och klicka sedan på **spara**.

    Klicka på **OK** när ändringarna har utförts.

   > [!IMPORTANT]  
   > Du måste starta om flera tjänster innan ändringen börjar gälla.

6. Ambari-webbgränssnittet, Välj **HDFS** i listan till vänster och välj sedan **starta om alla påverkade** från den **tjänståtgärder** listan till höger. När du uppmanas, väljer __bekräfta starta om alla__.

    Upprepa processen för MapReduce2 och YARN.

7. När tjänsterna har startats om, markerar du var och en och inaktivera underhållsläge från den **tjänståtgärder** nedrullningsbar listruta.

## <a name="test-restricted-access"></a>Testa begränsad åtkomst

Använd följande steg för att verifiera att du kan endast Läs- och objekt för SAS-lagringskontot.

1. Ansluta till klustret. Ersätt `CLUSTERNAME` med namnet på klustret och ange följande kommando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Använd följande kommando från prompten för att visa innehållet i behållaren:

    ```bash
    hdfs dfs -ls wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/
    ```

    Ersätt `SASCONTAINER` med namnet på behållaren som skapades för kontot i SAS-lagring. Ersätt `SASACCOUNTNAME` med namnet på lagringskontot som används för signaturen för delad åtkomst.

    Listan innehåller filen när behållaren och SAS har skapats.

3. Använd följande kommando för att kontrollera att du kan läsa innehållet i filen. Ersätt den `SASCONTAINER` och `SASACCOUNTNAME` som i föregående steg. Ersätt `sample.log` med namnet på den fil som visas i föregående kommando:

    ```bash
    hdfs dfs -text wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/sample.log
    ```

    Det här kommandot visar innehållet i filen.

4. Använd följande kommando för att ladda ned filen till det lokala filsystemet:

    ```bash
    hdfs dfs -get wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/sample.log testfile.txt
    ```

    Det här kommandot laddar ned filen till en lokal fil med namnet **testfile.txt**.

5. Använd följande kommando för att ladda upp den lokala filen till en ny fil med namnet **testupload.txt** på SAS-lagring:

    ```bash
    hdfs dfs -put testfile.txt wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/testupload.txt
    ```

    Du får ett meddelande som liknar följande text:

        put: java.io.IOException

    Det här felet uppstår eftersom lagringsplatsen är skrivskyddade + endast lista över. Använd följande kommando för att publicera den på standardlagringen för klustret, som är skrivbar:

    ```bash
    hdfs dfs -put testfile.txt wasbs:///testupload.txt
    ```

    Den här gången bör åtgärden slutföras utan problem.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du lägger till lagring med begränsad åtkomst till ditt HDInsight-kluster kan läsa andra sätt att arbeta med data i ditt kluster:

* [Använda Apache Hive med HDInsight](hadoop/hdinsight-use-hive.md)
* [Använda Apache Pig med HDInsight](hadoop/hdinsight-use-pig.md)
* [Använda MapReduce med HDInsight](hadoop/hdinsight-use-mapreduce.md)

