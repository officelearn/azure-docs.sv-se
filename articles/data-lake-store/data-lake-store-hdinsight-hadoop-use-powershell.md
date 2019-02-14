---
title: 'PowerShell: Azure HDInsight-kluster med Azure Data Lake Storage Gen1 som tilläggslagring | Microsoft Docs'
services: data-lake-store,hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 164ada5a-222e-4be2-bd32-e51dbe993bc0
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: 4187557ef9a38f55465547f83d7bc4c3bcad9ba7
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56236945"
---
# <a name="use-azure-powershell-to-create-an-hdinsight-cluster-with-azure-data-lake-storage-gen1-as-additional-storage"></a>Använda Azure PowerShell för att skapa ett HDInsight-kluster med Azure Data Lake Storage Gen1 (som ytterligare lagringsutrymme)

> [!div class="op_single_selector"]
> * [Använda portalen](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Med hjälp av PowerShell (för standardlagring)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Med hjälp av PowerShell (för ytterligare lagring)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Med Resource Manager](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Lär dig hur du använder Azure PowerShell för att konfigurera ett HDInsight-kluster med Azure Data Lake Storage Gen1 **som ytterligare lagringsutrymme**. Anvisningar för hur du skapar ett HDInsight-kluster med Data Lake Storage Gen1 som standardlagringsutrymme finns i [skapa ett HDInsight-kluster med Data Lake Storage Gen1 som standardlagringsutrymme](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md).

> [!NOTE]
> Om du tänker använda Data Lake Storage Gen1 som ytterligare lagringsutrymme för HDInsight-kluster rekommenderar vi starkt att du gör detta när du skapar klustret enligt beskrivningen i den här artikeln. Att lägga till Data Lake Storage Gen1 som ytterligare lagringsutrymme i ett befintligt HDInsight kluster är en komplicerad process där känslig för fel.
>

Data Lake Storage Gen1 kan användas som en standardlagring eller ytterligare storage-konto för stöds klustertyper. När Data Lake Storage Gen1 används som ytterligare lagringsutrymme, standardkontot för lagring för klustren kommer fortfarande att Azure Storage BLOB-objekt (WASB) och de klusterrelaterade filerna (till exempel loggar, osv.) skrivs fortfarande till standardlagring, samtidigt som de data som du vill processen kan lagras i ett Data Lake Storage Gen1-konto. Med Data Lake Storage Gen1 som ett ytterligare lagringskonto påverkar inte prestanda eller möjligheten att läsa eller skriva till lagring från klustret.

## <a name="using-data-lake-storage-gen1-for-hdinsight-cluster-storage"></a>Med hjälp av Data Lake Storage Gen1 för HDInsight-klusterlagring

Här följer några viktiga överväganden för användning av HDInsight med Data Lake Storage Gen1:

* Alternativet för att skapa HDInsight-kluster med åtkomst till Data Lake Storage Gen1 som ytterligare lagring är tillgänglig för HDInsight version 3.2, 3.4, 3.5 och 3.6.

Konfigurera HDInsight för att arbeta med Data Lake Storage Gen1 omfattar med hjälp av PowerShell följande steg:

* Skapa ett Data Lake Storage Gen1-konto
* Konfigurera autentisering för rollbaserad åtkomst till Data Lake Storage Gen1
* Skapa HDInsight-kluster med autentisering till Data Lake Storage Gen1
* Kör ett testjobb på klustret

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Innan du påbörjar de här självstudierna måste du ha:

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Installera Azure PowerShell 1.0 eller senare**. Se [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).
* **Windows SDK**. Du kan installera det [härifrån](https://dev.windows.com/en-us/downloads). Du kan använda detta för att skapa ett säkerhetscertifikat.
* **Azure Active Directory-tjänstobjekt**. I den här självstudiekursen innehåller instruktioner om hur du skapar ett huvudnamn för tjänsten i Azure AD. Du måste vara en Azure AD-administratör för att kunna skapa ett huvudnamn för tjänsten. Om du är en Azure AD-administratör kan du hoppa över det här kravet och fortsätter med självstudien.

    **Om du inte är en Azure AD-administratör**, du kommer inte att kunna utföra stegen som krävs för att skapa ett huvudnamn för tjänsten. I detta fall är skapa Azure AD-administratören först ett huvudnamn för tjänsten innan du kan skapa ett HDInsight-kluster med Data Lake Storage Gen1. Dessutom tjänstens huvudnamn måste skapas med hjälp av ett certifikat, enligt beskrivningen i [skapa tjänstens huvudnamn med certifikat](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority).

## <a name="create-a-data-lake-storage-gen1-account"></a>Skapa ett Data Lake Storage Gen1-konto
Följ dessa steg om du vill skapa ett Data Lake Storage Gen1-konto.

1. Öppna ett nytt Azure PowerShell-fönster på skrivbordet och ange följande fragment. När du uppmanas att logga in, kontrollera att du loggar in som en av prenumerationens administratör/ägare:

        # Log in to your Azure account
        Connect-AzAccount

        # List all the subscriptions associated to your account
        Get-AzSubscription

        # Select a subscription
        Set-AzContext -SubscriptionId <subscription ID>

        # Register for Data Lake Storage Gen1
        Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

   > [!NOTE]
   > Om du får ett felmeddelande som liknar `Register-AzResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid` när du registrerar resursprovidern Data Lake Storage Gen1, är det möjligt att prenumerationen inte är godkänd för Data Lake Storage Gen1. Kontrollera att du aktivera din Azure-prenumeration för Data Lake Storage Gen1 genom att följa de här [instruktioner](data-lake-store-get-started-portal.md).
   >
   >
2. Ett Data Lake Storage Gen1-konto är kopplat till en Azure-resursgrupp. Börja med att skapa en Azure-resursgrupp.

        $resourceGroupName = "<your new resource group name>"
        New-AzResourceGroup -Name $resourceGroupName -Location "East US 2"

    Du bör se utdata som liknar detta:

        ResourceGroupName : hdiadlgrp
        Location          : eastus2
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp

3. Skapa ett Data Lake Storage Gen1-konto. Kontonamnet som du anger får bara innehålla gemena bokstäver och siffror.

        $dataLakeStorageGen1Name = "<your new Data Lake Storage Gen1 account name>"
        New-AzDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStorageGen1Name -Location "East US 2"

    Du bör se utdata som liknar följande:

        ...
        ProvisioningState           : Succeeded
        State                       : Active
        CreationTime                : 5/5/2017 10:53:56 PM
        EncryptionState             : Enabled
        ...
        LastModifiedTime            : 5/5/2017 10:53:56 PM
        Endpoint                    : hdiadlstore.azuredatalakestore.net
        DefaultGroup                :
        Id                          : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp/providers/Microsoft.DataLakeStore/accounts/hdiadlstore
        Name                        : hdiadlstore
        Type                        : Microsoft.DataLakeStore/accounts
        Location                    : East US 2
        Tags                        : {}

5. Ladda upp exempeldata till Data Lake Storage Gen1. Vi använder det senare i den här artikeln för att verifiera att data kan nås från ett HDInsight-kluster. Om du behöver exempeldata att ladda upp, kan du hämta mappen **Ambulansdata** från [Azure Data Lake Git-lagringsplatsen](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData).

        $myrootdir = "/"
        Import-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name -Path "C:\<path to data>\vehicle1_09142014.csv" -Destination $myrootdir\vehicle1_09142014.csv


## <a name="set-up-authentication-for-role-based-access-to-data-lake-storage-gen1"></a>Konfigurera autentisering för rollbaserad åtkomst till Data Lake Storage Gen1

Alla Azure-prenumerationer är associerade med en Azure Active Directory. Användare och tjänster som har åtkomst till resurser i prenumerationen med Azure portal eller Azure Resource Manager API: et måste autentiseras mot den Azure Active Directory. Komma åt Azure-prenumerationer och tjänster genom att tilldela dem till rätt roll på en Azure-resurs.  För tjänster identifierar ett huvudnamn för tjänsten tjänsten i Azure Active Directory (AAD). Det här avsnittet beskriver hur du ger ett programtjänst, till exempel HDInsight, åtkomst till en Azure-resurs (Data Lake Storage Gen1 kontot som du skapade tidigare) genom att skapa ett tjänsthuvudnamn för programmet och tilldela roller till som via Azure PowerShell.

Om du vill konfigurera Active Directory-autentisering för Data Lake Storage Gen1, måste du utföra följande uppgifter.

* Skapa ett självsignerat certifikat
* Skapa ett program i Azure Active Directory och ett huvudnamn för tjänsten

### <a name="create-a-self-signed-certificate"></a>Skapa ett självsignerat certifikat

Kontrollera att du har [Windows SDK](https://dev.windows.com/en-us/downloads) installerad innan du fortsätter med stegen i det här avsnittet. Du måste har också skapat en katalog som **C:\mycertdir**, där certifikatet ska skapas.

1. Navigera till den plats där du har installerat Windows SDK från PowerShell-fönster (vanligtvis `C:\Program Files (x86)\Windows Kits\10\bin\x86` och använda den [MakeCert] [ makecert] verktyget för att skapa ett självsignerat certifikat och en privat nyckel. Använd följande kommandon.

        $certificateFileDir = "<my certificate directory>"
        cd $certificateFileDir

        makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -r -len 2048

    Du uppmanas att ange lösenordet för privata nyckeln. När kommandot har körs, bör du se en **CertFile.cer** och **mykey.pvk** i katalogen certifikat som du har angett.
2. Använd den [Pvk2Pfx] [ pvk2pfx] verktyg för att konvertera .pvk och .cer-filer som skapats av MakeCert till en .pfx-fil. Kör följande kommando.

        pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>

    När du uppmanas ange privata nyckel lösenordet du angav tidigare. Värdet som du anger för den **-IO** parametern är det lösenord som är associerad med den .pfx-fil. När kommandot har slutförts bör du också se en CertFile.pfx i katalogen certifikat som du har angett.

### <a name="create-an-azure-active-directory-and-a-service-principal"></a>Skapa en Azure Active Directory och ett huvudnamn för tjänsten

I det här avsnittet ska utföra du stegen för att skapa tjänstens huvudnamn för ett Azure Active Directory-program, tilldela en roll till tjänstens huvudnamn och autentisera sig som tjänstens huvudnamn genom att tillhandahålla ett certifikat. Kör följande kommandon för att skapa ett program i Azure Active Directory.

1. Klistra in följande cmdletar i PowerShell-konsolfönster. Kontrollera att värdet som du anger för den **- DisplayName** egenskapen är unika. Dessutom värden för **- startsidan** och **- IdentiferUris** är platshållarvärden och inte verifieras.

        $certificateFilePath = "$certificateFileDir\CertFile.pfx"

        $password = Read-Host -Prompt "Enter the password" # This is the password you specified for the .pfx file

        $certificatePFX = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($certificateFilePath, $password)

        $rawCertificateData = $certificatePFX.GetRawCertData()

        $credential = [System.Convert]::ToBase64String($rawCertificateData)

        $application = New-AzADApplication `
            -DisplayName "HDIADL" `
            -HomePage "https://contoso.com" `
            -IdentifierUris "https://mycontoso.com" `
            -CertValue $credential  `
            -StartDate $certificatePFX.NotBefore  `
            -EndDate $certificatePFX.NotAfter

        $applicationId = $application.ApplicationId
2. Skapa ett huvudnamn för tjänsten med hjälp av program-ID.

        $servicePrincipal = New-AzADServicePrincipal -ApplicationId $applicationId

        $objectId = $servicePrincipal.Id
3. Ge tjänstens huvudnamn åtkomst till Data Lake Storage Gen1 mappen och filen som du kommer åt från HDInsight-klustret. Kodfragmentet nedan ger åtkomst till roten för Data Lake Storage Gen1 kontot (dit du kopierade Exempeldatafilen), och själva filen.

        Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path / -AceType User -Id $objectId -Permissions All
        Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path /vehicle1_09142014.csv -AceType User -Id $objectId -Permissions All

## <a name="create-an-hdinsight-linux-cluster-with-data-lake-storage-gen1-as-additional-storage"></a>Skapa ett HDInsight Linux-kluster med Data Lake Storage Gen1 som ytterligare lagringsutrymme

I det här avsnittet ska skapa vi ett HDInsight Hadoop Linux-kluster med Data Lake Storage Gen1 som ytterligare lagringsutrymme. Den här versionen är måste HDInsight-kluster och Gen1 för Data Lake Storage-konto vara på samma plats.

1. Börja med att hämta prenumerationen klient-ID. Du kommer att behöva som senare.

        $tenantID = (Get-AzContext).Tenant.TenantId
2. Den här versionen är kan för ett Hadoop-kluster, Data Lake Storage Gen1 endast användas som ett ytterligare lagringsutrymme för klustret. Standardlagring kommer fortfarande att Azure storage BLOB (WASB). Så måste vi först skapa storage-konto och storage-behållare som krävs för klustret.

        # Create an Azure storage account
        $location = "East US 2"
        $storageAccountName = "<StorageAccountName>"   # Provide a Storage account name

        New-AzStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -Location $location -Type Standard_GRS

        # Create an Azure Blob Storage container
        $containerName = "<ContainerName>"              # Provide a container name
        $storageAccountKey = (Get-AzStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
        $destContext = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        New-AzStorageContainer -Name $containerName -Context $destContext
3. Skapa HDInsight-kluster. Använd följande cmdlets.

        # Set these variables
        $clusterName = $containerName                   # As a best practice, have the same name for the cluster and container
        $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
        $httpCredentials = Get-Credential
        $sshCredentials = Get-Credential

        New-AzHDInsightCluster -ClusterName $clusterName -ResourceGroupName $resourceGroupName -HttpCredential $httpCredentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop -Version "3.4" -OSType Linux -SshCredential $sshCredentials -ObjectID $objectId -AadTenantId $tenantID -CertificateFilePath $certificateFilePath -CertificatePassword $password

    När cmdleten har slutförts bör du se utdata med information om kluster.


## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-the-data-lake-storage-gen1-account"></a>Kör testjobb på HDInsight-kluster för att använda Data Lake Storage Gen1-konto
När du har konfigurerat ett HDInsight-kluster, kan du köra testjobb på klustret för att testa att HDInsight-klustret har åtkomst till Data Lake Storage Gen1. Om du vill göra det kör vi ett Hive-jobb som skapar en tabell med exempeldata som du tidigare överförde till ditt Data Lake Storage Gen1-konto.

I det här avsnittet kommer du att SSH till HDInsight Linux-klustret du skapade och kör den en exempelfråga för Hive.

* Om du använder en Windows-klient för SSH till klustret, se [använda SSH med Linux-baserat Hadoop i HDInsight från Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Om du använder en Linux-klient för SSH till klustret, se [använda SSH med Linux-baserat Hadoop i HDInsight från Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)

1. När du är ansluten, startar du Hive-CLI med hjälp av följande kommando:

        hive
2. Med hjälp av CLI anger du följande instruktioner för att skapa en ny tabell med namnet **fordon** med exempeldata i Data Lake Storage Gen1:

        DROP TABLE vehicles;
        CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://<mydatalakestoragegen1>.azuredatalakestore.net:443/';
        SELECT * FROM vehicles LIMIT 10;

    Du bör se utdata som liknar följande:

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

## <a name="access-data-lake-storage-gen1-using-hdfs-commands"></a>Få åtkomst till Data Lake Storage Gen1 med hjälp av HDFS-kommandon
När du har konfigurerat HDInsight-kluster för att använda Data Lake Storage Gen1 kan använda du HDFS shell-kommandon för åtkomst till store.

I det här avsnittet kommer du att SSH till HDInsight Linux-klustret du skapade och kör kommandon för HDFS.

* Om du använder en Windows-klient för SSH till klustret, se [använda SSH med Linux-baserat Hadoop i HDInsight från Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Om du använder en Linux-klient för SSH till klustret, se [använda SSH med Linux-baserat Hadoop i HDInsight från Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)

När du är ansluten, Använd kommandot filsystem HDFS för att visa filer i Data Lake Storage Gen1-konto.

    hdfs dfs -ls adl://<Data Lake Storage Gen1 account name>.azuredatalakestore.net:443/

Du bör nu se den fil som du tidigare laddade upp till Data Lake Storage Gen1.

    15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
    Found 1 items
    -rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestoragegen1.azuredatalakestore.net:443/mynewfolder

Du kan också använda den `hdfs dfs -put` kommando för att ladda upp några filer till Data Lake Storage Gen1 och sedan använda `hdfs dfs -ls` att verifiera om filerna har laddats upp.

## <a name="see-also"></a>Se även
* [Använda Data Lake Storage Gen1 med Azure HDInsight-kluster](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
* [Portal: Skapa ett HDInsight-kluster om du vill använda Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
