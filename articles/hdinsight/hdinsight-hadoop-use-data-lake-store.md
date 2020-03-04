---
title: Använda Data Lake Storage Gen1 med Hadoop i Azure HDInsight
description: Lär dig hur du frågar efter data från Azure Data Lake Storage Gen1 och lagrar resultatet av din analys.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 03/01/2020
ms.openlocfilehash: 3e7e5919a3f862f5cad243654972683d1879c4ba
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2020
ms.locfileid: "78251077"
---
# <a name="use-data-lake-storage-gen1-with-azure-hdinsight-clusters"></a>Använda Data Lake Storage Gen1 med Azure HDInsight-kluster

> [!Note]
> Distribuera nya HDInsight-kluster med hjälp av [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md) för bättre prestanda och nya funktioner.

För att analysera data i HDInsight-klustret kan du lagra data antingen i [Azure Storage](../storage/common/storage-introduction.md), [Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md)eller [Azure Data Lake Storage gen 2](../storage/blobs/data-lake-storage-introduction.md). Med alla lagrings alternativ kan du på ett säkert sätt ta bort HDInsight-kluster som används för beräkning utan att förlora användar data.

I den här artikeln får du lära dig hur Data Lake Storage Gen1 fungerar med HDInsight-kluster. Om du vill lära dig hur Azure Storage fungerar med HDInsight-kluster kan du läsa [Use Azure Storage with Azure HDInsight clusters](hdinsight-hadoop-use-blob-storage.md) (Använda Azure Storage med Azure HDInsight-kluster). Mer information om hur du skapar ett HDInsight-kluster finns i [skapa Apache Hadoop kluster i HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

> [!NOTE]  
> Data Lake Storage Gen1 alltid nås via en säker kanal, så det finns inget `adls` fil Systems schema namn. Du använder alltid `adl`.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="availability-for-hdinsight-clusters"></a>Tillgänglighet för HDInsight-kluster

Apache Hadoop stöder en begreppet standard fil system. Standardfilsystemet kräver att ett standardschema och en utfärdare används. Det kan också användas för att matcha relativa sökvägar. När du skapar HDInsight-kluster kan du ange en BLOB-behållare i Azure Storage som standard fil system, eller med HDInsight 3,5 och nyare versioner, kan du välja antingen Azure Storage eller Azure Data Lake Storage Gen1 som standard fil system med en några undantag. Observera att klustret och lagrings kontot måste finnas i samma region.

HDInsight-kluster kan använda Data Lake Storage Gen1 på två sätt:

* Som standardlagring
* Som ytterligare lagringsutrymme med Azure Storage Blob som standardlagringsutrymme.

Från och med nu kan endast vissa av HDInsight-kluster typerna/-versionerna användas med Data Lake Storage Gen1 som standard lagring och ytterligare lagrings konton:

| Typ av HDInsight-kluster | Data Lake Storage Gen1 som standard lagring | Data Lake Storage Gen1 som ytterligare lagrings utrymme| Anteckningar |
|------------------------|------------------------------------|---------------------------------------|------|
| HDInsight version 4,0 | Nej | Nej |ADLS Gen1 stöds inte med HDInsight 4,0 |
| HDInsight version 3.6 | Ja | Ja | Förutom HBase|
| HDInsight version 3.5 | Ja | Ja | Förutom HBase|
| HDInsight version 3.4 | Nej | Ja | |
| HDInsight version 3.3 | Nej | Nej | |
| HDInsight version 3.2 | Nej | Ja | |
| Storm | | |Du kan använda Data Lake Storage Gen1 för att skriva data från en Storm-topologi. Du kan också använda Data Lake Storage för referens data som sedan kan läsas av en Storm-topologi.|

> [!WARNING]  
> HDInsight-HBase stöds inte med Azure Data Lake Storage Gen1

Att använda Data Lake Storage Gen1 som ett ytterligare lagrings konto påverkar inte prestanda eller möjligheten att läsa eller skriva till Azure Storage från klustret.

## <a name="use-data-lake-storage-gen1-as-default-storage"></a>Använd Data Lake Storage Gen1 som standard lagring

När HDInsight distribueras med Data Lake Storage Gen1 som standard lagring, lagras de klusterbaserade filerna i `adl://mydatalakestore/<cluster_root_path>/`, där `<cluster_root_path>` är namnet på en mapp som du skapar i Data Lake Storage. Genom att ange en rot Sök väg för varje kluster kan du använda samma Data Lake Storage konto för mer än ett kluster. Så du kan ha en konfiguration enligt följande:

* Cluster1 kan använda sökvägen `adl://mydatalakestore/cluster1storage`
* Cluster2 kan använda sökvägen `adl://mydatalakestore/cluster2storage`

Observera att båda klustren använder samma Data Lake Storage Gen1-konto **mydatalakestore**. Varje kluster har åtkomst till ett eget rot fil system i Data Lake Storage. I synnerhet i Azure-portaldistributionen uppmanas du att använda ett mappnamn som **/kluster/\<klusternamn >** för rotsökvägen.

För att kunna använda Data Lake Storage Gen1 som standard lagring måste du ge tjänstens huvud namn åtkomst till följande sökvägar:

* Data Lake Storage Gen1 konto roten.  Till exempel: adl://mydatalakestore/.
* Mappen för alla klustermappar.  Till exempel: adl://mydatalakestore/clusters.
* Mappen för klustret.  Till exempel: adl://mydatalakestore/clusters/cluster1storage.

Mer information om hur du skapar tjänstens huvud namn och beviljar åtkomst finns i Konfigurera Data Lake Storage åtkomst.

### <a name="extracting-a-certificate-from-azure-keyvault-for-use-in-cluster-creation"></a>Extrahera ett certifikat från Azure-valv för användning i skapande av kluster

Om du vill konfigurera Azure Data Lake Storage Gen1 som standard lagring för ett nytt kluster och certifikatet för tjänstens huvud namn lagras i Azure Key Vault, finns det några ytterligare steg som krävs för att konvertera certifikatet till rätt format. Följande kodfragment visar hur du utför konverteringen.

Hämta först certifikatet från Key Vault och extrahera `SecretValueText`.

```powershell
$certPassword = Read-Host "Enter Certificate Password"
$cert = (Get-AzureKeyVaultSecret -VaultName 'MY-KEY-VAULT' -Name 'MY-SECRET-NAME')
$certValue = [System.Convert]::FromBase64String($cert.SecretValueText)
```

Sedan konverterar du `SecretValueText` till ett certifikat.

```powershell
$certObject = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList $certValue,$null,"Exportable, PersistKeySet"
$certBytes = $certObject.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword.SecretValueText);
$identityCertificate = [System.Convert]::ToBase64String($certBytes)
```

Sedan kan du använda `$identityCertificate` för att distribuera ett nytt kluster som i följande kodfragment:

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

## <a name="use-data-lake-storage-gen1-as-additional-storage"></a>Använd Data Lake Storage Gen1 som ytterligare lagrings utrymme

Du kan också använda Data Lake Storage Gen1 som ytterligare lagring för klustret. I sådana fall kan klustrets standard lagring antingen vara ett Azure Storage Blob eller ett Data Lake Storage konto. Om du kör HDInsight-jobb mot data som lagras i Data Lake Storage som ytterligare lagrings utrymme måste du använda den fullständigt kvalificerade sökvägen till filerna. Exempel:

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

Observera att det inte finns någon **cluster_root_path** i URL-adressen nu. Det beror på att Data Lake Storage inte är ett standard lagrings utrymme i det här fallet, så allt du behöver göra är att ange sökvägen till filerna.

För att kunna använda en Data Lake Storage Gen1 som ytterligare lagrings utrymme behöver du bara ge tjänstens huvud namn åtkomst till Sök vägarna där filerna lagras.  Exempel:

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

Mer information om hur du skapar tjänstens huvud namn och beviljar åtkomst finns i Konfigurera Data Lake Storage åtkomst.

## <a name="use-more-than-one-data-lake-storage-accounts"></a>Använd fler än ett Data Lake Storage konton

Genom att lägga till ett Data Lake Storage konto som ytterligare och lägga till mer än ett Data Lake Storage-konto utförs genom att ge HDInsight-klustret behörighet till data i ett eller flera Data Lake Storage-konton. Se Konfigurera Data Lake Storage åtkomst.

## <a name="configure-data-lake-storage-access"></a>Konfigurera Data Lake Storage åtkomst

Du måste ha ett huvud namn för Azure Active Directory (Azure AD) för att kunna konfigurera Data Lake Storage åtkomst från HDInsight-klustret. Det är bara Azure AD-administratörer som kan vara tjänstens huvudnamn. Tjänstens huvudnamn måste skapas med ett certifikat. Mer information finns i [Snabbstart: Konfigurera kluster i HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md) och [Create service principal with self-signed-certificate](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate) (Skapa tjänstens huvudnamn med självsignerat certifikat).

> [!NOTE]  
> Om du ska använda Azure Data Lake Storage Gen1 som ytterligare lagring för HDInsight-kluster rekommenderar vi starkt att du gör detta när du skapar klustret enligt beskrivningen i den här artikeln. Att lägga till Azure Data Lake Storage Gen1 som ytterligare lagrings utrymme i ett befintligt HDInsight-kluster är inte ett scenario som stöds.

Mer information om grunderna i åtkomst kontroll modellen för Data Lake Storage Gen1 finns [i åtkomst kontroll i Azure Data Lake Storage gen1](../data-lake-store/data-lake-store-access-control.md).

## <a name="access-files-from-the-cluster"></a>Åtkomst till filer från klustret

Det finns flera sätt som du kan använda för att komma åt filerna i Data Lake Storage från ett HDInsight-kluster.

* **Via det fullständiga namnet**. Med den här metoden kan du ange den fullständiga sökvägen till filen som du vill öppna.

    ```
    adl://<data_lake_account>.azuredatalakestore.net/<cluster_root_path>/<file_path>
    ```

* **Via det förkortade sökvägsformatet**. Med den här metoden ersätter du sökvägen upp till kluster roten med:

    ```
    adl:///<file path>
    ```

* **Med den relativa sökvägen**. Med den här metoden anger du bara den relativa sökvägen till den fil som du vill öppna.

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>Exempel på data åtkomst

Exempel baseras på en [ssh-anslutning](./hdinsight-hadoop-linux-use-ssh-unix.md) till klustrets huvud nod. I exemplen används alla tre URI-scheman. Ersätt `DATALAKEACCOUNT` och `CLUSTERNAME` med relevanta värden.

#### <a name="a-few-hdfs-commands"></a>Några HDFS-kommandon

1. Skapa en enkel fil på lokal lagrings plats.

    ```bash
    touch testFile.txt
    ```

1. Skapa kataloger i kluster lagringen.

    ```bash
    hdfs dfs -mkdir adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/sampledata1/
    hdfs dfs -mkdir adl:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. Kopiera data från lokal lagring till kluster lagring.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt adl:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt /sampledata3/
    ```

1. Lista katalog innehåll i kluster lagringen.

    ```bash
    hdfs dfs -ls adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/sampledata1/
    hdfs dfs -ls adl:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

#### <a name="creating-a-hive-table"></a>Skapa en Hive-tabell

Tre fil platser visas i syfte att illustrera. För faktisk körning använder du endast en av `LOCATION` posterna.

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

## <a name="identify-storage-path-from-ambari"></a>Identifiera lagrings Sök väg från Ambari

Om du vill identifiera den fullständiga sökvägen till det konfigurerade standard arkivet navigerar du till **HDFS** > **configs** och anger `fs.defaultFS` i rutan Filter indatamängd.

## <a name="create-hdinsight-clusters-with-access-to-data-lake-storage-gen1"></a>Skapa HDInsight-kluster med åtkomst till Data Lake Storage Gen1

Använd följande länkar om du vill ha detaljerade instruktioner om hur du skapar HDInsight-kluster med åtkomst till Data Lake Storage Gen1.

* [Använda portalen](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
* [Använda PowerShell (med Data Lake Storage Gen1 som standard lagring)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [Använda PowerShell (med Data Lake Storage Gen1 som ytterligare lagrings utrymme)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Använda Azure-mallar](../data-lake-store/data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

## <a name="refresh-the-hdinsight-certificate-for-data-lake-storage-gen1-access"></a>Uppdatera HDInsight-certifikatet för Data Lake Storage Gen1 åtkomst

Följande exempel på PowerShell-kod läser in ett certifikat från en lokal fil eller Azure Key Vault och uppdaterar ditt HDInsight-kluster med det nya certifikatet för att få åtkomst till Azure Data Lake Storage Gen1. Ange ett eget HDInsight-kluster namn, resurs grupp namn, prenumerations-ID, app-ID, lokal sökväg till certifikatet. Ange lösen ordet när du uppmanas till det.

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
* [Skapa ett HDInsight-kluster om du vill använda Data Lake Storage Gen1 med hjälp av Azure PowerShell](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Ladda upp data till HDInsight](hdinsight-upload-data.md)
* [Använda Apache Hive med HDInsight](hadoop/hdinsight-use-hive.md)
* [Använd Azure Storage signaturer för delad åtkomst för att begränsa åtkomsten till data med HDInsight](hdinsight-storage-sharedaccesssignature-permissions.md)
* [Självstudie: extrahera, transformera och läsa in data med hjälp av interaktiv fråga i Azure HDInsight](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
