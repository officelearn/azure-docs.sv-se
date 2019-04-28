---
title: Använd Data Lake Storage Gen1 med Hadoop i Azure HDInsight
description: Lär dig att läsa data från Azure Data Lake Storage Gen1 och för att lagra resultatet av dina analyser.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 11/06/2018
ms.openlocfilehash: 067bdcfc496fc986ae87620b7d57d7cad3a0f734
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766975"
---
# <a name="use-data-lake-storage-gen1-with-azure-hdinsight-clusters"></a>Använda Data Lake Storage Gen1 med Azure HDInsight-kluster

> [!Note] 
> Distribuera nya HDInsight-kluster med hjälp av [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md) för optimala prestanda och nya funktioner.

Om du vill analysera data i HDInsight-kluster kan du lagra data antingen i [Azure Storage](../storage/common/storage-introduction.md), [Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md) eller [Azure Data Lake Storage Gen 2](../storage/blobs/data-lake-storage-introduction.md). Alla lagringsalternativ för kan du ta bort HDInsight-kluster som används för beräkning utan att förlora användardata.

I den här artikeln får du lära dig hur Data Lake Storage Gen1 fungerar med HDInsight-kluster. Om du vill lära dig hur Azure Storage fungerar med HDInsight-kluster kan du läsa [Use Azure Storage with Azure HDInsight clusters](hdinsight-hadoop-use-blob-storage.md) (Använda Azure Storage med Azure HDInsight-kluster). Mer information om hur du skapar ett HDInsight-kluster finns i [skapa Apache Hadoop-kluster i HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

> [!NOTE]  
> Data Lake Storage Gen1 alltid är tillgängligt via en säker kanal så det finns inga `adls` filsystemsschemanamn. Du använder alltid `adl`.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="availability-for-hdinsight-clusters"></a>Tillgänglighet för HDInsight-kluster

Apache Hadoop stöder begreppet standardfilsystem. Standardfilsystemet kräver att ett standardschema och en utfärdare används. Det kan också användas för att matcha relativa sökvägar. Under skapandeprocessen för HDInsight-kluster kan du ange en blobbehållare i Azure Storage som standardfilsystemet eller med HDInsight 3.5 och senare versioner kan du välja antingen Azure Storage eller Azure Data Lake Storage Gen1 som standardfilsystem med ett få undantag. 

HDInsight-kluster kan använda Data Lake Storage Gen1 på två sätt:

* Som standardlagring
* Som ytterligare lagringsutrymme med Azure Storage Blob som standardlagringsutrymme.

Från och med nu, bara en del av HDInsight klusterstöd typer/versioner med Data Lake Storage Gen1 som standardlagring och ytterligare lagringskonton:

| Typ av HDInsight-kluster | Data Lake Storage Gen1 som standardlagring | Data Lake Storage Gen1 som ytterligare lagringsutrymme| Anteckningar |
|------------------------|------------------------------------|---------------------------------------|------|
| HDInsight version 4.0 | Nej | Nej |ADLS Gen1 stöds inte med HDInsight 4.0 |
| HDInsight version 3.6 | Ja | Ja | Förutom HBase|
| HDInsight version 3.5 | Ja | Ja | Förutom HBase|
| HDInsight version 3.4 | Nej | Ja | |
| HDInsight version 3.3 | Nej | Nej | |
| HDInsight version 3.2 | Nej | Ja | |
| Storm | | |Du kan använda Data Lake Storage Gen1 för att skriva data från en Storm-topologi. Du kan också använda Data Lake Storage för referensdata som sedan kan läsas av en Storm-topologi.|

> [!WARNING]  
> HDInsight HBase stöds inte med Azure Data Lake Storage Gen1

Med Data Lake Storage Gen1 som ett ytterligare lagringskonto påverkas inte prestanda eller möjligheten att läsa eller skriva till Azure-lagring från klustret.

## <a name="use-data-lake-storage-gen1-as-default-storage"></a>Använda Data Lake Storage Gen1 som standardlagring

När HDInsight distribueras med Data Lake Storage Gen1 som standardlagringsutrymme lagras klusterrelaterade filer i `adl://mydatalakestore/<cluster_root_path>/`, där `<cluster_root_path>` är namnet på en mapp som du skapar i Data Lake Storage. Du kan använda samma Data Lake Storage-konto för flera kluster genom att ange en rotsökväg för varje kluster. Så du kan ha en konfiguration enligt följande:

* Cluster1 kan använda sökvägen `adl://mydatalakestore/cluster1storage`
* Cluster2 kan använda sökvägen `adl://mydatalakestore/cluster2storage`

Observera att båda klustren använder samma konto som Data Lake Storage Gen1 **mydatalakestore**. Varje kluster har åtkomst till sitt egen rotfilsystem i Data Lake Storage. I synnerhet i Azure-portaldistributionen uppmanas du att använda ett mappnamn som **/kluster/\<klusternamn >** för rotsökvägen.

Om du vill kunna använda Data Lake Storage Gen1 som standardlagring måste du ge tjänstens huvudnamn åtkomst till följande sökvägar:

- Data Lake Storage Gen1 konto roten.  Till exempel: adl://mydatalakestore/.
- Mappen för alla klustermappar.  Till exempel: adl://mydatalakestore/clusters.
- Mappen för klustret.  Till exempel: adl://mydatalakestore/clusters/cluster1storage.

Mer information för att skapa tjänstens huvudnamn och bevilja åtkomst finns i Konfigurera Data Lake Storage åtkomst.

### <a name="extracting-a-certificate-from-azure-keyvault-for-use-in-cluster-creation"></a>Extrahera ett certifikat från Azure Keyvault för användning i klustret har skapats

Om du vill ställa in Azure Data Lake Storage Gen1 som din standardlagring för ett nytt kluster och certifikatet för tjänstens huvudnamn lagras i Azure Key Vault, det finns några ytterligare steg som krävs för att konvertera certifikatet till rätt format. I följande kodavsnitt visar hur du kan utföra konverteringen.

Först hämta certifikatet från Key Vault och extrahera den `SecretValueText`.

```powershell
$certPassword = Read-Host "Enter Certificate Password"
$cert = (Get-AzureKeyVaultSecret -VaultName 'MY-KEY-VAULT' -Name 'MY-SECRET-NAME')
$certValue = [System.Convert]::FromBase64String($cert.SecretValueText)
```

Sedan konvertera den `SecretValueText` till ett certifikat.

```powershell
$certObject = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList $certValue,$null,"Exportable, PersistKeySet"
$certBytes = $certObject.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword.SecretValueText);
$identityCertificate = [System.Convert]::ToBase64String($certBytes)
```

Du kan använda den `$identityCertificate` att distribuera ett nytt kluster som i följande kodavsnitt:

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

## <a name="use-data-lake-storage-gen1-as-additional-storage"></a>Använda Data Lake Storage Gen1 som ytterligare lagringsutrymme

Du kan använda Data Lake Storage Gen1 som ytterligare lagringsutrymme för klustret. I sådana fall kan Standardlagringsutrymmet för klustret antingen vara en Azure Storage Blob eller ett Data Lake Storage-konto. Om du kör HDInsight-jobb mot data som lagras i Data Lake Storage som ytterligare lagringsutrymme måste du använda den fullständiga sökvägen till filerna. Exempel:

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

Observera att det inte finns någon **cluster_root_path** i URL-adressen nu. Det beror på att Data Lake Storage inte är ett standardlagringsutrymme i det här fallet så allt du behöver göra är att ange sökvägen till filerna.

Om du vill kunna använda ett Data Lake Storage Gen1 som ytterligare lagringsutrymme behöver du bara ge tjänstens huvudnamn åtkomst till de sökvägar där filerna lagras.  Exempel:

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

Mer information för att skapa tjänstens huvudnamn och bevilja åtkomst finns i Konfigurera Data Lake Storage åtkomst.


## <a name="use-more-than-one-data-lake-storage-accounts"></a>Använda fler än ett Data Lake Storage-konton

Lägger till ett Data Lake Storage-konto som ytterligare och lägga till fler än en Data Lake Storage-konto genom att ge behörighet för HDInsight-kluster på data i ett eller flera Data Lake Storage-konton. Se Konfigurera Data Lake Storage åtkomst.

## <a name="configure-data-lake-storage-access"></a>Konfigurera åtkomst till Data Lake Storage

Om du vill konfigurera åtkomst till Data Lake Storage från HDInsight-kluster, måste du ha ett Azure Active directory (Azure AD)-tjänstobjekt. Det är bara Azure AD-administratörer som kan vara tjänstens huvudnamn. Tjänstens huvudnamn måste skapas med ett certifikat. Mer information finns i [ Snabbstart: Konfigurera kluster i HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md), och [skapa tjänstens huvudnamn med klientportal signed certifikat](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).

> [!NOTE]  
> Om du ska använda Azure Data Lake Storage Gen1 som ytterligare lagringsutrymme för HDInsight-kluster, rekommenderar vi starkt att du gör detta när du skapar klustret enligt beskrivningen i den här artikeln. Att lägga till Azure Data Lake Storage Gen1 som ytterligare lagringsutrymme i ett befintligt HDInsight-kluster är inte ett scenario som stöds.
>

## <a name="access-files-from-the-cluster"></a>Åtkomst till filer från klustret

Det finns flera sätt du kan komma åt filer i Data Lake Storage från ett HDInsight-kluster.

* **Via det fullständiga namnet**. Med den här metoden kan du ange den fullständiga sökvägen till filen som du vill öppna.

        adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/<file_path>

* **Via det förkortade sökvägsformatet**. Med den här metoden ersätts sökvägen fram till klusterroten med adl: ///. Så i exemplet ovan kan du ersätta `adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/` med `adl:///`.

        adl:///<file path>

* **Med den relativa sökvägen**. Med den här metoden anger du bara den relativa sökvägen till den fil som du vill öppna. Till exempel, om den fullständiga sökvägen till filen är:

        adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/example/data/sample.log

    Du kan komma åt samma sample.log-fil via den här relativa sökvägen i stället.

        /example/data/sample.log

## <a name="create-hdinsight-clusters-with-access-to-data-lake-storage-gen1"></a>Skapa HDInsight-kluster med åtkomst till Data Lake Storage Gen1

Använd följande länkar om du detaljerade anvisningar om hur du skapar HDInsight-kluster med åtkomst till Data Lake Storage Gen1.

* [Använda portalen](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
* [Använda PowerShell (med Data Lake Storage Gen1 som standardlagringsutrymme)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [Använda PowerShell (med Data Lake Storage Gen1 som ytterligare lagringsutrymme)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Använda Azure-mallar](../data-lake-store/data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

## <a name="refresh-the-hdinsight-certificate-for-data-lake-storage-gen1-access"></a>Uppdatera HDInsight-certifikatet för Data Lake Storage Gen1 åtkomst

I följande exempel PowerShell-kod läser ett certifikat från en lokal fil eller Azure Key Vault och uppdaterar ditt HDInsight-kluster med det nya certifikatet till åtkomst till Azure Data Lake Storage Gen1. Ange dina egna HDInsight-klusternamnet och resursgruppens namn, prenumerations-ID, app-ID, lokal sökväg till certifikatet. Ange lösenordet när du uppmanas till detta.

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

Login-AzAccount
Select-AzSubscription -SubscriptionId $subscriptionId

if($addNewCertKeyCredential)
{
    Write-Host "Creating new KeyCredential for the app"
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    New-AzADAppCredential -ApplicationId $appId -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
    Write-Host "Waiting for 30 seconds for the permissions to get propagated"
    Start-Sleep -s 30
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

* [Kom igång med Azure HDInsight][hdinsight-get-started]
* [Snabbstart: Konfigurera kluster i HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
* [Skapa ett HDInsight-kluster om du vill använda Data Lake Storage Gen1 med Azure PowerShell](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Överföra data till HDInsight][hdinsight-upload-data]
* [Använda Apache Hive med HDInsight][hdinsight-use-hive]
* [Använda Apache Hive med HDInsight][hdinsight-use-pig]
* [Använda signaturer för delad åtkomst i Azure Storage för att begränsa åtkomsten till data med HDInsight][hdinsight-use-sas]

[hdinsight-use-sas]: hdinsight-storage-sharedaccesssignature-permissions.md
[powershell-install]: /powershell/azureps-cmdlets-docs
[hdinsight-creation]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md

[blob-storage-restAPI]: https://msdn.microsoft.com/library/windowsazure/dd135733.aspx
[azure-storage-create]:../storage/common/storage-create-storage-account.md

[img-hdi-powershell-blobcommands]: ./media/hdinsight-hadoop-use-blob-storage/HDI.PowerShell.BlobCommands.png
[img-hdi-quick-create]: ./media/hdinsight-hadoop-use-blob-storage/HDI.QuickCreateCluster.png
[img-hdi-custom-create-storage-account]: ./media/hdinsight-hadoop-use-blob-storage/HDI.CustomCreateStorageAccount.png  
