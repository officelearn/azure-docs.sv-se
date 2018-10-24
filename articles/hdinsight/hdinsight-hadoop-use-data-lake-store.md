---
title: Använd Data Lake Store med Hadoop i Azure HDInsight
description: Lär dig mer om hur du frågar efter data från Azure Data Lake Store och lagrar resultatet av dina analyser.
services: hdinsight,storage
author: jasonwhowell
ms.author: jasonh
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 07/23/2018
ms.openlocfilehash: d205a46c672523e029816b573742d991de79b2ae
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49956739"
---
# <a name="use-data-lake-store-with-azure-hdinsight-clusters"></a>Använda Data Lake Store med Azure HDInsight-kluster

När du ska analysera data i HDInsight-kluster kan du lagra data i antingen [Azure Storage](../storage/common/storage-introduction.md), [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md) eller bådadera. Båda lagringsalternativen låter dig ta bort HDInsight-kluster som används för beräkning utan att förlora användardata.

I den här artikeln får du lära dig hur Data Lake Store fungerar med HDInsight-kluster. Om du vill lära dig hur Azure Storage fungerar med HDInsight-kluster kan du läsa [Use Azure Storage with Azure HDInsight clusters](hdinsight-hadoop-use-blob-storage.md) (Använda Azure Storage med Azure HDInsight-kluster). Mer information om hur du skapar ett HDInsight-kluster finns i [Create Hadoop clusters in HDInsight](hdinsight-hadoop-provision-linux-clusters.md) (Skapa Hadoop-kluster i HDInsight).

> [!NOTE]
> Data Lake Store alltid är tillgängligt via en säker kanal så det finns inget `adls`-filsystemsschemanamn. Du använder alltid `adl`.
> 


## <a name="availability-for-hdinsight-clusters"></a>Tillgänglighet för HDInsight-kluster

Hadoop stöder begreppet standardfilsystem. Standardfilsystemet kräver att ett standardschema och en utfärdare används. Det kan också användas för att matcha relativa sökvägar. Du kan ange en blobcontainer i Azure Storage som standardfilsystem när du skapar HDInsight-kluster. Med HDInsight 3.5 eller senare kan du välja antingen Azure Storage eller Azure Data Lake Store som standardfilsystem, med några undantag. 

HDInsight-kluster kan använda Data Lake Store på två sätt:

* Som standardlagring
* Som ytterligare lagringsutrymme med Azure Storage Blob som standardlagringsutrymme.

Från och med nu har endast vissa typer/versioner av HDInsight-kluster stöd för Data Lake Store som konton för standardlagring och ytterligare lagring:

| Typ av HDInsight-kluster | Data Lake Store som standardlagring | Data Lake Store som ytterligare lagring| Anteckningar |
|------------------------|------------------------------------|---------------------------------------|------|
| HDInsight version 3.6 | Ja | Ja | |
| HDInsight version 3.5 | Ja | Ja | Förutom HBase|
| HDInsight version 3.4 | Nej | Ja | |
| HDInsight version 3.3 | Nej | Nej | |
| HDInsight version 3.2 | Nej | Ja | |
| Storm | | |Du kan använda Data Lake Store till att skriva data från en Storm-topologi. Du kan också använda Data Lake Store för referensdata som sedan kan läsas av en Storm-topologi.|

När du använder Data Lake Store som ett ytterligare lagringskonto påverkas inte prestanda eller möjligheten att läsa eller skriva till Azure-lagring från klustret.


## <a name="use-data-lake-store-as-default-storage"></a>Använda Data Lake Store som standardlagring

När HDInsight distribueras med Azure Data Lake Store som standardlagringsutrymme lagras klusterrelaterade filer i Data Lake Store på följande plats:

    adl://mydatalakestore/<cluster_root_path>/

där `<cluster_root_path>` är namnet på en mapp du skapar i Data Lake Store. Du kan använda samma Data Lake Store-konto för flera kluster genom att ange en rotsökväg för varje kluster. Så du kan ha en konfiguration enligt följande:

* Cluster1 kan använda sökvägen `adl://mydatalakestore/cluster1storage`
* Cluster2 kan använda sökvägen `adl://mydatalakestore/cluster2storage`

Observera att samma Data Lake Store-konto används för båda klustren **mydatalakestore**. Varje kluster har åtkomst till sitt egen rotfilsystem i Data Lake Store. I synnerhet i Azure-portaldistributionen uppmanas du att använda ett mappnamn som **/kluster/\<klusternamn >** för rotsökvägen.

Om du vill kunna använda Data Lake Store som standardlagring måste du ge tjänstens huvudnamn åtkomst till följande sökvägar:

- Roten för Data Lake Store-kontot.  Till exempel: adl://mydatalakestore/.
- Mappen för alla klustermappar.  Till exempel: adl://mydatalakestore/clusters.
- Mappen för klustret.  Till exempel: adl://mydatalakestore/clusters/cluster1storage.

Mer information om att skapa tjänstens huvudnamn och bevilja åtkomst finns i [Konfigurera åtkomst till Data Lake Store](#configure-data-lake-store-access).


## <a name="use-data-lake-store-as-additional-storage"></a>Använda Data Lake Store som ytterligare lagring

Du kan även använda Data Lake Store som ytterligare lagringsutrymme för klustret. I sådana fall kan standardlagringsutrymmet för klustret vara ett konto för antingen Azure Storage Blob eller Data Lake Store. Om du kör HDInsight-jobb mot data som finns lagrade i Data Lake Store som ytterligare lagringsutrymme måste du använda den fullständiga sökvägen till filerna. Exempel:

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

Observera att det inte finns någon **cluster_root_path** i URL-adressen nu. Det beror på att Data Lake Store inte är ett standardlagringsutrymme i det här fallet, så allt du behöver göra är att ange sökvägen till filerna.

För att kunna använda ett Data Lake Store som ytterligare lagringsutrymme behöver du bara ge tjänstens huvudnamn åtkomst till de sökvägar där filerna lagras.  Exempel:

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

Mer information om att skapa tjänstens huvudnamn och bevilja åtkomst finns i [Konfigurera åtkomst till Data Lake Store](#configure-data-lake-store-access).


## <a name="use-more-than-one-data-lake-store-accounts"></a>Använda fler än ett Data Lake Store-konto

Du kan lägga till ett Data Lake Store-konto som ytterligare lagring och lägga till fler än ett Data Lake Store-konto genom att ge HDInsight-klustret behörighet till data i ett eller flera Data Lake Store-konton. Läs mer i [Konfigurera åtkomst till Data Lake Store](#configure-data-lake-store-access).

## <a name="configure-data-lake-store-access"></a>Konfigurera åtkomst till Data Lake Store

Om du vill konfigurera åtkomst till Data Lake Store från ditt HDInsight-kluster måste tjänstens huvudnamn komma från Azure AD (Active Directory). Det är bara Azure AD-administratörer som kan vara tjänstens huvudnamn. Tjänstens huvudnamn måste skapas med ett certifikat. Mer information finns i [Snabbstart: Konfigurera kluster i HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md) och [Create service principal with self-signed-certificate](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate) (Skapa tjänstens huvudnamn med självsignerat certifikat).

> [!NOTE]
> Om du ska använda Azure Data Lake Store som ytterligare lagringsutrymme för HDInsight-kluster rekommenderar vi starkt att du gör detta när du skapar klustret, enligt beskrivningen i den här artikeln. Det finns inget stöd för att lägga till Azure Data Lake Store som ytterligare lagringsutrymme för ett befintligt HDInsight-kluster.
>

## <a name="access-files-from-the-cluster"></a>Åtkomst till filer från klustret

Du kan komma åt filer i Data Lake Store från ett HDInsight-kluster på flera olika sätt.

* **Via det fullständiga namnet**. Med den här metoden kan du ange den fullständiga sökvägen till filen som du vill öppna.

        adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/<file_path>

* **Via det förkortade sökvägsformatet**. Med den här metoden ersätts sökvägen fram till klusterroten med adl: ///. Så i exemplet ovan kan du ersätta `adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/` med `adl:///`.

        adl:///<file path>

* **Med den relativa sökvägen**. Med den här metoden anger du bara den relativa sökvägen till den fil som du vill öppna. Till exempel, om den fullständiga sökvägen till filen är:

        adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/example/data/sample.log

    Du kan komma åt samma sample.log-fil via den här relativa sökvägen i stället.

        /example/data/sample.log

## <a name="create-hdinsight-clusters-with-access-to-data-lake-store"></a>Skapa HDInsight-kluster med åtkomst till Data Lake Store

Använd länkarna nedan om du vill ha mer detaljerade instruktioner om hur du skapar HDInsight-kluster med åtkomst till Data Lake Store.

* [Använda portalen](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
* [Använda PowerShell (med Data Lake Store som standardlagringsutrymme)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [Använda PowerShell (med Data Lake Store som ytterligare lagringsutrymme)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Använda Azure-mallar](../data-lake-store/data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

## <a name="refresh-the-hdinsight-certificate-for-data-lake-store-access"></a>Uppdatera HDInsight-certifikatet för Data Lake Store-åtkomst

I följande exempel PowerShell-kod läser en lokal certifikatfil och uppdaterar ditt HDInsight-kluster med det nya certifikatet till Azure Data Lake Store. Ange dina egna HDInsight-klusternamnet och resursgruppens namn, prenumerations-ID, app-ID, lokal sökväg till certifikatet. Ange lösenordet när du uppmanas till detta.

```powershell-interactive
$clusterName = '<clustername>'
$resourceGroupName = '<resourcegroupname>'
$subscriptionId = '01234567-8a6c-43bc-83d3-6b318c6c7305'
$appId = '01234567-e100-4118-8ba6-c25834f4e938'
$generateSelfSignedCert = $false
$addNewCertKeyCredential = $true
$certFilePath = 'C:\localfolder\adls.pfx'
$certPassword = Read-Host "Enter Certificate Password"

if($generateSelfSignedCert)
{
    Write-Host "Generating new SelfSigned certificate"
    
    $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=hdinsightAdlsCert" -KeySpec KeyExchange
    $certBytes = $cert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword);
    $certString = [System.Convert]::ToBase64String($certBytes)
}
else
{

    Write-Host "Reading the cert file from path $certFilePath"

    $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($certFilePath, $certPassword)
    $certString = [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes($certFilePath))
}

Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId $subscriptionId

if($addNewCertKeyCredential)
{
    Write-Host "Creating new KeyCredential for the app"
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    New-AzureRmADAppCredential -ApplicationId $appId -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
    Write-Host "Waiting for 30 seconds for the permissions to get propagated"
    Start-Sleep -s 30
}

Write-Host "Updating the certificate on HDInsight cluster..."

Invoke-AzureRmResourceAction `
    -ResourceGroupName $resourceGroupName `
    -ResourceType 'Microsoft.HDInsight/clusters' `
    -ResourceName $clusterName `
    -ApiVersion '2015-03-01-preview' `
    -Action 'updateclusteridentitycertificate' `
    -Parameters @{ ApplicationId = $appId; Certificate = $certString; CertificatePassword = $certPassword.ToString() } `
    -Force
```

## <a name="next-steps"></a>Nästa steg
I den här artikeln fick du lära dig hur du använder det HDFS-kompatibla Azure Data Lake Storage med HDInsight. Du kan skapa skalbara, långsiktiga lösningar för arkivering av insamlade data samt HDInsight för att få tillgång till informationen i lagrade strukturerade och ostrukturerade data.

Mer information finns i:

* [Kom igång med Azure HDInsight][hdinsight-get-started]
* [Snabbstart: Konfigurera kluster i HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
* [Skapa ett HDInsight-kluster för att använda Data Lake Store med Azure PowerShell](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Överföra data till HDInsight][hdinsight-upload-data]
* [Använda Hive med HDInsight][hdinsight-use-hive]
* [Använda Pig med HDInsight][hdinsight-use-pig]
* [Använda signaturer för delad åtkomst i Azure Storage för att begränsa åtkomsten till data med HDInsight][hdinsight-use-sas]

[hdinsight-use-sas]: hdinsight-storage-sharedaccesssignature-permissions.md
[powershell-install]: /powershell/azureps-cmdlets-docs
[hdinsight-creation]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md

[blob-storage-restAPI]: http://msdn.microsoft.com/library/windowsazure/dd135733.aspx
[azure-storage-create]:../storage/common/storage-create-storage-account.md

[img-hdi-powershell-blobcommands]: ./media/hdinsight-hadoop-use-blob-storage/HDI.PowerShell.BlobCommands.png
[img-hdi-quick-create]: ./media/hdinsight-hadoop-use-blob-storage/HDI.QuickCreateCluster.png
[img-hdi-custom-create-storage-account]: ./media/hdinsight-hadoop-use-blob-storage/HDI.CustomCreateStorageAccount.png  
