---
title: Använda DataSjölagringgen1 med Hadoop i Azure HDInsight
description: Lär dig hur du frågar data från Azure Data Lake Storage Gen1 och lagrar resultat av din analys.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 03/01/2020
ms.openlocfilehash: 3e7e5919a3f862f5cad243654972683d1879c4ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78251077"
---
# <a name="use-data-lake-storage-gen1-with-azure-hdinsight-clusters"></a>Använda DataSjölagringgend1 med Azure HDInsight-kluster

> [!Note]
> Distribuera nya HDInsight-kluster med [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md) för förbättrad prestanda och nya funktioner.

Om du vill analysera data i HDInsight-klustret kan du lagra data antingen i [Azure Storage,](../storage/common/storage-introduction.md) [Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md)eller [Azure Data Lake Storage Gen 2](../storage/blobs/data-lake-storage-introduction.md). Med alla lagringsalternativ kan du på ett säkert sätt ta bort HDInsight-kluster som används för beräkning utan att förlora användardata.

I den här artikeln får du lära dig hur Data Lake Storage Gen1 fungerar med HDInsight-kluster. Om du vill lära dig hur Azure Storage fungerar med HDInsight-kluster kan du läsa [Use Azure Storage with Azure HDInsight clusters](hdinsight-hadoop-use-blob-storage.md) (Använda Azure Storage med Azure HDInsight-kluster). Mer information om hur du skapar ett HDInsight-kluster finns i [Skapa Apache Hadoop-kluster i HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

> [!NOTE]  
> Data Lake Storage Gen1 nås alltid via en säker `adls` kanal, så det finns inget filsystemschemanamn. Du använder alltid `adl`.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="availability-for-hdinsight-clusters"></a>Tillgänglighet för HDInsight-kluster

Apache Hadoop stöder en uppfattning om standardfilsystemet. Standardfilsystemet kräver att ett standardschema och en utfärdare används. Det kan också användas för att matcha relativa sökvägar. Under hdinsight-klusterprocessen kan du ange en blob-behållare i Azure Storage som standardfilsystem, eller med HDInsight 3.5 och nyare versioner, du kan välja antingen Azure Storage eller Azure Data Lake Storage Gen1 som standardfilsystem med en några få undantag. Observera att klustret och lagringskontot måste vara värd i samma region.

HDInsight-kluster kan använda DataSjölagring gen1 på två sätt:

* Som standardlagring
* Som ytterligare lagringsutrymme med Azure Storage Blob som standardlagringsutrymme.

Från och med nu är det bara några av HDInsight-klustertyperna/-versionerna som stöder användning av Data Lake Storage Gen1 som standardlagrings- och flerlagringskonton:

| Typ av HDInsight-kluster | DataSjölagringGen1 som standardlagring | Data Lake Storage Gen1 som ytterligare lagring| Anteckningar |
|------------------------|------------------------------------|---------------------------------------|------|
| HDInsight version 4.0 | Inga | Inga |ADLS Gen1 stöds inte med HDInsight 4.0 |
| HDInsight version 3.6 | Ja | Ja | Förutom HBase|
| HDInsight version 3.5 | Ja | Ja | Förutom HBase|
| HDInsight version 3.4 | Inga | Ja | |
| HDInsight version 3.3 | Inga | Inga | |
| HDInsight version 3.2 | Inga | Ja | |
| Storm | | |Du kan använda Data Lake Storage Gen1 för att skriva data från en Storm-topologi. Du kan också använda Data Lake Storage som referensdata som sedan kan läsas av en Storm-topologi.|

> [!WARNING]  
> HDInsight HBase stöds inte med Azure Data Lake Storage Gen1

Att använda Data Lake Storage Gen1 som ett ytterligare lagringskonto påverkar inte prestanda eller möjlighet att läsa eller skriva till Azure-lagring från klustret.

## <a name="use-data-lake-storage-gen1-as-default-storage"></a>Använda DataSjölagringsgenm1 som standardlagring

När HDInsight distribueras med Data Lake Storage Gen1 som standardlagring `adl://mydatalakestore/<cluster_root_path>/`lagras `<cluster_root_path>` de klusterrelaterade filerna i , där är namnet på en mapp som du skapar i DataSjölagring. Genom att ange en rotsökväg för varje kluster kan du använda samma datasjölagringskonto för mer än ett kluster. Så du kan ha en konfiguration enligt följande:

* Cluster1 kan använda sökvägen `adl://mydatalakestore/cluster1storage`
* Cluster2 kan använda sökvägen `adl://mydatalakestore/cluster2storage`

Observera att båda klustren använder samma Data Lake Storage Gen1-konto **mydatalakestore**. Varje kluster har åtkomst till sitt eget rotfilsystem i Data Lake Storage. I synnerhet i Azure-portaldistributionen uppmanas du att använda ett mappnamn som **/kluster/\<klusternamn >** för rotsökvägen.

Om du vill kunna använda DataSjölagring gen1 som standardlagring måste du ge tjänsten huvudnamn åtkomst till följande sökvägar:

* Kontoroten Data Lake Storage Gen1.  Till exempel: adl://mydatalakestore/.
* Mappen för alla klustermappar.  Till exempel: adl://mydatalakestore/clusters.
* Mappen för klustret.  Till exempel: adl://mydatalakestore/clusters/cluster1storage.

Mer information om hur du skapar tjänstens huvudnamn och bevilja åtkomst finns i Konfigurera åtkomst till datasjölagring.

### <a name="extracting-a-certificate-from-azure-keyvault-for-use-in-cluster-creation"></a>Extrahera ett certifikat från Azure Keyvault för användning i klusterskapande

Om du vill konfigurera Azure Data Lake Storage Gen1 som standardlagring för ett nytt kluster och certifikatet för tjänstens huvudnamn lagras i Azure Key Vault, finns det några ytterligare steg som krävs för att konvertera certifikatet till rätt format. Följande kodavsnitt visar hur du utför konverteringen.

Hämta först certifikatet från Key `SecretValueText`Vault och extrahera .

```powershell
$certPassword = Read-Host "Enter Certificate Password"
$cert = (Get-AzureKeyVaultSecret -VaultName 'MY-KEY-VAULT' -Name 'MY-SECRET-NAME')
$certValue = [System.Convert]::FromBase64String($cert.SecretValueText)
```

Konvertera sedan `SecretValueText` till ett certifikat.

```powershell
$certObject = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList $certValue,$null,"Exportable, PersistKeySet"
$certBytes = $certObject.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword.SecretValueText);
$identityCertificate = [System.Convert]::ToBase64String($certBytes)
```

Sedan kan du `$identityCertificate` använda för att distribuera ett nytt kluster som i följande utdrag:

```powershell
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $pathToArmTemplate `
    -identityCertificate $identityCertificate `
    -identityCertificatePassword $certPassword.SecretValueText `
    -clusterName  $clusterName `
    -clusterLoginPassword $SSHpassword `
    -sshPassword $SSHpassword `
    -servicePrincipalApplicationId $application.ApplicationId
```

## <a name="use-data-lake-storage-gen1-as-additional-storage"></a>Använda DataSjölagring Gen1 som ytterligare lagring

Du kan också använda Data Lake Storage Gen1 som ytterligare lagring för klustret. I sådana fall kan klusterstandardlagringen antingen vara en Azure Storage Blob eller ett Data Lake Storage-konto. Om du kör HDInsight-jobb mot data som lagras i DataSjölagring som ytterligare lagring måste du använda den fullständigt kvalificerade sökvägen till filerna. Ett exempel:

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

Observera att det inte finns någon **cluster_root_path** i URL-adressen nu. Det beror på att Data Lake Storage inte är en standardlagring i det här fallet så allt du behöver göra är att ange sökvägen till filerna.

För att kunna använda en Data Lake Storage Gen1 som ytterligare lagring behöver du bara ge tjänsten huvudnamn åtkomst till de sökvägar där dina filer lagras.  Ett exempel:

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

Mer information om hur du skapar tjänstens huvudnamn och bevilja åtkomst finns i Konfigurera åtkomst till datasjölagring.

## <a name="use-more-than-one-data-lake-storage-accounts"></a>Använda mer än ett datasjölagringskonton

Lägga till ett Data Lake Storage-konto som ytterligare och lägga till mer än ett Data Lake Storage-konton åstadkoms genom att ge HDInsight-klustret behörighet för data i en malm mer Data Lake Storage-konton. Se Konfigurera åtkomst till datasjölagring.

## <a name="configure-data-lake-storage-access"></a>Konfigurera åtkomst till lagring av datasjö

Om du vill konfigurera datasjölagringsåtkomst från ditt HDInsight-kluster måste du ha ett Azure Active Directory (Azure AD) -tjänsthuvudnamn. Det är bara Azure AD-administratörer som kan vara tjänstens huvudnamn. Tjänstens huvudnamn måste skapas med ett certifikat. Mer information finns i [Snabbstart: Konfigurera kluster i HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md) och [Create service principal with self-signed-certificate](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate) (Skapa tjänstens huvudnamn med självsignerat certifikat).

> [!NOTE]  
> Om du ska använda Azure Data Lake Storage Gen1 som ytterligare lagring för HDInsight-kluster rekommenderar vi starkt att du gör detta medan du skapar klustret enligt beskrivningen i den här artikeln. Att lägga till Azure Data Lake Storage Gen1 som ytterligare lagring i ett befintligt HDInsight-kluster är inte ett scenario som stöds.

Mer information om grunderna i åtkomstkontrollmodellen för Data Lake Storage Gen1 finns [i Åtkomstkontroll i Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md).

## <a name="access-files-from-the-cluster"></a>Åtkomst till filer från klustret

Det finns flera sätt att komma åt filerna i DataSjölagring från ett HDInsight-kluster.

* **Via det fullständiga namnet**. Med den här metoden kan du ange den fullständiga sökvägen till filen som du vill öppna.

    ```
    adl://<data_lake_account>.azuredatalakestore.net/<cluster_root_path>/<file_path>
    ```

* **Via det förkortade sökvägsformatet**. Med den här metoden ersätter du sökvägen upp till klusterroten med:

    ```
    adl:///<file path>
    ```

* **Med den relativa sökvägen**. Med den här metoden anger du bara den relativa sökvägen till den fil som du vill öppna.

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>Exempel på dataåtkomst

Exempel baseras på en [ssh-anslutning](./hdinsight-hadoop-linux-use-ssh-unix.md) till huvudnoden för klustret. Exemplen använder alla tre URI-scheman. Ersätt `DATALAKEACCOUNT` `CLUSTERNAME` och med relevanta värden.

#### <a name="a-few-hdfs-commands"></a>Några hdfs-kommandon

1. Skapa en enkel fil på lokal lagring.

    ```bash
    touch testFile.txt
    ```

1. Skapa kataloger för klusterlagring.

    ```bash
    hdfs dfs -mkdir adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/sampledata1/
    hdfs dfs -mkdir adl:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. Kopiera data från lokal lagring till klusterlagring.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt adl:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt /sampledata3/
    ```

1. Lista kataloginnehåll på klusterlagring.

    ```bash
    hdfs dfs -ls adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/sampledata1/
    hdfs dfs -ls adl:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

#### <a name="creating-a-hive-table"></a>Skapa en Hive-tabell

Tre filplatser visas för belysande ändamål. För faktisk körning använder du `LOCATION` bara en av posterna.

```hql
DROP TABLE myTable;
CREATE EXTERNAL TABLE myTable (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE
LOCATION 'adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/example/data/';
LOCATION 'adl:///example/data/';
LOCATION '/example/data/';
```

## <a name="identify-storage-path-from-ambari"></a>Identifiera lagringssökväg från Ambari

Om du vill identifiera den fullständiga sökvägen till det konfigurerade `fs.defaultFS` standardarkivet navigerar du till > **HDFS-konfigurationer** och anger indatarutan för filtret. **HDFS**

## <a name="create-hdinsight-clusters-with-access-to-data-lake-storage-gen1"></a>Skapa HDInsight-kluster med åtkomst till Data Lake Storage Gen1

Använd följande länkar för detaljerade instruktioner om hur du skapar HDInsight-kluster med åtkomst till Data Lake Storage Gen1.

* [Använda Portal](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
* [Använda PowerShell (med Data Lake Storage Gen1 som standardlagring)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [Använda PowerShell (med Data Lake Storage Gen1 som ytterligare lagringsutrymme)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Använda Azure-mallar](../data-lake-store/data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

## <a name="refresh-the-hdinsight-certificate-for-data-lake-storage-gen1-access"></a>Uppdatera HDInsight-certifikatet för Data Lake Storage Gen1-åtkomst

I följande exempel läser PowerShell-koden ett certifikat från en lokal fil eller Azure Key Vault och uppdaterar ditt HDInsight-kluster med det nya certifikatet för att komma åt Azure Data Lake Storage Gen1. Ange ditt eget HDInsight-klusternamn, resursgruppsnamn, prenumerations-ID, app-ID, lokal sökväg till certifikatet. Skriv in lösenordet när du uppmanas att göra det.

```powershell-interactive
$clusterName = '<clustername>'
$resourceGroupName = '<resourcegroupname>'
$subscriptionId = '01234567-8a6c-43bc-83d3-6b318c6c7305'
$appId = '01234567-e100-4118-8ba6-c25834f4e938'
$addNewCertKeyCredential = $true
$certFilePath = 'C:\localfolder\adls.pfx'
$KeyVaultName = "my-key-vault-name"
$KeyVaultSecretName = "my-key-vault-secret-name"
$certPassword = Read-Host "Enter Certificate Password"
# certSource
# 0 - create self signed cert
# 1 - read cert from file path
# 2 - read cert from key vault
$certSource = 0

Login-AzAccount
Select-AzSubscription -SubscriptionId $subscriptionId

if($certSource -eq 0)
{
    Write-Host "Generating new SelfSigned certificate"

    $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=hdinsightAdlsCert" -KeySpec KeyExchange
    $certBytes = $cert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword);
    $certString = [System.Convert]::ToBase64String($certBytes)
}
elseif($certSource -eq 1)
{

    Write-Host "Reading the cert file from path $certFilePath"

    $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($certFilePath, $certPassword)
    $certString = [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes($certFilePath))
}
elseif($certSource -eq 2)
{

    Write-Host "Reading the cert file from Azure Key Vault $KeyVaultName"

    $cert = (Get-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName)
    $certValue = [System.Convert]::FromBase64String($cert.SecretValueText)
    $certObject = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList $certValue, $null,"Exportable, PersistKeySet"

    $certBytes = $certObject.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword.SecretValueText);

    $certString =[System.Convert]::ToBase64String($certBytes)
}

if($addNewCertKeyCredential)
{
    Write-Host "Creating new KeyCredential for the app"
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    New-AzADAppCredential -ApplicationId $appId -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
    Write-Host "Waiting for 7 minutes for the permissions to get propagated"
    Start-Sleep -s 420 #7 minutes
}

Write-Host "Updating the certificate on HDInsight cluster..."

Invoke-AzResourceAction `
    -ResourceGroupName $resourceGroupName `
    -ResourceType 'Microsoft.HDInsight/clusters' `
    -ResourceName $clusterName `
    -ApiVersion '2015-03-01-preview' `
    -Action 'updateclusteridentitycertificate' `
    -Parameters @{ ApplicationId = $appId; Certificate = $certString; CertificatePassword = $certPassword.ToString() } `
    -Force
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du använder HDFS-kompatibla Azure Data Lake Storage Gen1 med HDInsight. Du kan skapa skalbara, långsiktiga lösningar för arkivering av insamlade data samt HDInsight för att få tillgång till informationen i lagrade strukturerade och ostrukturerade data.

Mer information finns i:

* [Kom igång med Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Snabbstart: Konfigurera kluster i HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
* [Skapa ett HDInsight-kluster för att använda Data Lake Storage Gen1 med Azure PowerShell](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Överföra data till HDInsight](hdinsight-upload-data.md)
* [Använda Apache Hive med HDInsight](hadoop/hdinsight-use-hive.md)
* [Använda signaturer för delad åtkomst i Azure Storage för att begränsa åtkomsten till data med HDInsight](hdinsight-storage-sharedaccesssignature-permissions.md)
* [Självstudiekurs: Extrahera, transformera och läsa in data med interaktiv fråga i Azure HDInsight](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
