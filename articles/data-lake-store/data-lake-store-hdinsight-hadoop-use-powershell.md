---
title: 'PowerShell: Azure HDInsight-kluster med Azure Data Lake Storage Gen1 som tilläggslagring | Microsoft-dokument'
description: Lär dig hur du använder Azure PowerShell för att konfigurera ett HDInsight-kluster med Azure Data Lake Storage Gen1 som ytterligare lagringsutrymme.
services: data-lake-store,hdinsight
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 164ada5a-222e-4be2-bd32-e51dbe993bc0
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 4cd61619e0417ab1db8d8413872b2dff1c904fc1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970130"
---
# <a name="use-azure-powershell-to-create-an-hdinsight-cluster-with-azure-data-lake-storage-gen1-as-additional-storage"></a>Använda Azure PowerShell för att skapa ett HDInsight-kluster med Azure Data Lake Storage Gen1 (som ytterligare lagringsutrymme)

> [!div class="op_single_selector"]
> * [Använda Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Använda PowerShell (för standardlagring)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Använda PowerShell (för ytterligare lagringsutrymme)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Använda Resurshanteraren](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Lär dig hur du använder Azure PowerShell för att konfigurera ett HDInsight-kluster med Azure Data Lake Storage Gen1, **som ytterligare lagring**. Instruktioner om hur du skapar ett HDInsight-kluster med Data Lake Storage Gen1 som standardlagring finns i [Skapa ett HDInsight-kluster med Data Lake Storage Gen1 som standardlagring](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md).

> [!NOTE]
> Om du ska använda Data Lake Storage Gen1 som ytterligare lagring för HDInsight-kluster rekommenderar vi starkt att du gör detta medan du skapar klustret enligt beskrivningen i den här artikeln. Att lägga till Data Lake Storage Gen1 som ytterligare lagring i ett befintligt HDInsight-kluster är en komplicerad process som är utsatt för fel.
>

För klustertyper som stöds kan Data Lake Storage Gen1 användas som standardlagrings- eller ytterligare lagringskonto. När Data Lake Storage Gen1 används som ytterligare lagring skrivs fortfarande Azure Storage Blobs (Azure Storage Blobs) och de klusterrelaterade filerna (t.ex. loggar osv.) fortfarande till standardlagringen, medan de data som du vill kan lagras i ett Data Lake Storage Gen1-konto. Att använda Data Lake Storage Gen1 som ett ytterligare lagringskonto påverkar inte prestanda eller möjlighet att läsa/skriva till lagringen från klustret.

## <a name="using-data-lake-storage-gen1-for-hdinsight-cluster-storage"></a>Använda DataSjölagring Gen1 för HDInsight-klusterlagring

Här är några viktiga överväganden för att använda HDInsight med Data Lake Storage Gen1:

* Möjlighet att skapa HDInsight-kluster med åtkomst till Data Lake Storage Gen1 när ytterligare lagringsutrymme är tillgängligt för HDInsight-versionerna 3.2, 3.4, 3.5 och 3.6.

Att konfigurera HDInsight så att det fungerar med Data Lake Storage Gen1 med PowerShell innebär följande steg:

* Skapa ett Data Lake Storage Gen1-konto
* Konfigurera autentisering för rollbaserad åtkomst till Data Lake Storage Gen1
* Skapa HDInsight-kluster med autentisering till Data Lake Storage Gen1
* Köra ett testjobb i klustret

## <a name="prerequisites"></a>Krav

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Innan du påbörjar de här självstudierna måste du ha:

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Installera Azure PowerShell 1.0 eller senare**. Se [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).
* **Windows SDK**. Du kan installera det [härifrån](https://dev.windows.com/en-us/downloads). Du använder detta för att skapa ett säkerhetscertifikat.
* **Huvudnamn för Azure Active Directory-tjänsten**. Steg i den här självstudien innehåller instruktioner om hur du skapar ett tjänsthuvudnamn i Azure AD. Du måste dock vara Azure AD-administratör för att kunna skapa ett tjänsthuvudnamn. Om du är Azure AD-administratör kan du hoppa över den här förutsättningen och fortsätta med självstudien.

    **Om du inte är Azure AD-administratör**kan du inte utföra de steg som krävs för att skapa ett tjänsthuvudnamn. I så fall måste din Azure AD-administratör först skapa ett tjänsthuvudnamn innan du kan skapa ett HDInsight-kluster med Data Lake Storage Gen1. Tjänstens huvudnamn måste också skapas med hjälp av ett certifikat, enligt beskrivningen på [Skapa ett huvudnamn för tjänsten med certifikat](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority).

## <a name="create-a-data-lake-storage-gen1-account"></a>Skapa ett Data Lake Storage Gen1-konto
Följ dessa steg för att skapa ett Data Lake Storage Gen1-konto.

1. Öppna ett nytt Azure PowerShell-fönster från skrivbordet och ange följande kodavsnitt. När du uppmanas att logga in kontrollerar du att du loggar in som en av prenumerationsadministratören/ägaren:

        # Log in to your Azure account
        Connect-AzAccount

        # List all the subscriptions associated to your account
        Get-AzSubscription

        # Select a subscription
        Set-AzContext -SubscriptionId <subscription ID>

        # Register for Data Lake Storage Gen1
        Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

   > [!NOTE]
   > Om du får ett `Register-AzResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid` fel som liknar när du registrerar resursleverantören Data Lake Storage Gen1 är det möjligt att din prenumeration inte är vitlistad för Data Lake Storage Gen1. Se till att du aktiverar din Azure-prenumeration för Data Lake Storage Gen1 genom att följa dessa [instruktioner](data-lake-store-get-started-portal.md).
   >
   >
2. Ett Data Lake Storage Gen1-konto är associerat med en Azure Resource Group. Börja med att skapa en Azure-resursgrupp.

        $resourceGroupName = "<your new resource group name>"
        New-AzResourceGroup -Name $resourceGroupName -Location "East US 2"

    Utdata bör se ut så här:

        ResourceGroupName : hdiadlgrp
        Location          : eastus2
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp

3. Skapa ett Data Lake Storage Gen1-konto. Det kontonamn som du anger får bara innehålla gemener och siffror.

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

5. Ladda upp några exempeldata till Data Lake Storage Gen1. Vi använder detta senare i den här artikeln för att kontrollera att data är tillgängliga från ett HDInsight-kluster. Om du behöver exempeldata att ladda upp, kan du hämta mappen **Ambulansdata** från [Azure Data Lake Git-lagringsplatsen](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData).

        $myrootdir = "/"
        Import-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name -Path "C:\<path to data>\vehicle1_09142014.csv" -Destination $myrootdir\vehicle1_09142014.csv


## <a name="set-up-authentication-for-role-based-access-to-data-lake-storage-gen1"></a>Konfigurera autentisering för rollbaserad åtkomst till Data Lake Storage Gen1

Varje Azure-prenumeration är associerad med en Azure Active Directory. Användare och tjänster som får åtkomst till resurser för prenumerationen med hjälp av Azure-portalen eller Azure Resource Manager-API:et måste först autentisera med det Azure Active Directory. Åtkomst beviljas till Azure-prenumerationer och tjänster genom att tilldela dem rätt roll på en Azure-resurs.  För tjänster identifierar ett tjänsthuvudnamn tjänsten i Azure Active Directory (AAD). Det här avsnittet illustrerar hur du beviljar en programtjänst, till exempel HDInsight, åtkomst till en Azure-resurs (Data Lake Storage Gen1-kontot som du skapade tidigare) genom att skapa ett tjänsthuvudnamn för programmet och tilldela roller till det via Azure PowerShell.

Om du vill konfigurera Active Directory-autentisering för datasjölagring gen1 måste du utföra följande uppgifter.

* Skapa ett självsignerat certifikat
* Skapa ett program i Azure Active Directory och ett tjänsthuvudnamn

### <a name="create-a-self-signed-certificate"></a>Skapa ett självsignerat certifikat

Kontrollera att du har [Windows SDK](https://dev.windows.com/en-us/downloads) installerat innan du fortsätter med stegen i det här avsnittet. Du måste också ha skapat en katalog, till exempel **C:\mycertdir**, där certifikatet ska skapas.

1. Från PowerShell-fönstret navigerar du till den plats där `C:\Program Files (x86)\Windows Kits\10\bin\x86` du installerade Windows SDK (vanligtvis, och använder [verktyget MakeCert][makecert] för att skapa ett självsignerat certifikat och en privat nyckel. Använd följande kommandon.

        $certificateFileDir = "<my certificate directory>"
        cd $certificateFileDir

        makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -r -len 2048

    Du uppmanas att ange lösenordet för den privata nyckeln. När kommandot har utförts bör du se en **CertFile.cer** och **mykey.pvk** i certifikatkatalogen som du angav.
2. Använd [verktyget Pvk2Pfx][pvk2pfx] för att konvertera filerna .pvk och .cer som MakeCert skapade till en PFX-fil. Kör följande kommando.

        pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>

    Ange lösenordet för den privata nyckel som du angav tidigare när du uppmanas att ange lösenordet för den privata nyckel. Det värde som du anger för parametern **-po** är lösenordet som är associerat med PFX-filen. När kommandot har slutförts bör du också se en CertFile.pfx i certifikatkatalogen som du angav.

### <a name="create-an-azure-active-directory-and-a-service-principal"></a>Skapa en Azure Active Directory och ett tjänsthuvudnamn

I det här avsnittet utför du stegen för att skapa ett tjänsthuvudnamn för ett Azure Active Directory-program, tilldela en roll till tjänstens huvudnamn och autentisera som tjänstens huvudnamn genom att tillhandahålla ett certifikat. Kör följande kommandon för att skapa ett program i Azure Active Directory.

1. Klistra in följande cmdlets i PowerShell-konsolfönstret. Kontrollera att värdet du anger för egenskapen **-DisplayName** är unikt. Dessutom är värdena för **-HomePage** och **-IdentiferUris platshållarvärden** och verifieras inte.

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
2. Skapa ett tjänsthuvudnamn med hjälp av program-ID: et.

        $servicePrincipal = New-AzADServicePrincipal -ApplicationId $applicationId

        $objectId = $servicePrincipal.Id
3. Ge tjänstens huvudåtkomst till mappen Data Lake Storage Gen1 och filen som du kommer åt från HDInsight-klustret. Kodavsnittet nedan ger åtkomst till roten för datasjölagringsgenm1-kontot (där du kopierade exempeldatafilen) och själva filen.

        Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path / -AceType User -Id $objectId -Permissions All
        Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path /vehicle1_09142014.csv -AceType User -Id $objectId -Permissions All

## <a name="create-an-hdinsight-linux-cluster-with-data-lake-storage-gen1-as-additional-storage"></a>Skapa ett HDInsight Linux-kluster med Data Lake Storage Gen1 som ytterligare lagringsutrymme

I det här avsnittet skapar vi ett HDInsight Hadoop Linux-kluster med Data Lake Storage Gen1 som ytterligare lagring. För den här versionen måste HDInsight-klustret och Data Lake Storage Gen1-kontot vara på samma plats.

1. Börja med att hämta prenumerationens klient-ID. Du kommer att behöva det senare.

        $tenantID = (Get-AzContext).Tenant.TenantId
2. För den här versionen kan Data Lake Storage Gen1 endast användas som ytterligare lagring för klustret för ett Hadoop-kluster. Standardlagringen är fortfarande Azure storage blobbar (WASB). Så vi skapar först lagringskontot och lagringsbehållarna som krävs för klustret.

        # Create an Azure storage account
        $location = "East US 2"
        $storageAccountName = "<StorageAccountName>"   # Provide a Storage account name

        New-AzStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -Location $location -Type Standard_GRS

        # Create an Azure Blob Storage container
        $containerName = "<ContainerName>"              # Provide a container name
        $storageAccountKey = (Get-AzStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
        $destContext = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        New-AzStorageContainer -Name $containerName -Context $destContext
3. Skapa HDInsight-klustret. Använd följande cmdlets.

        # Set these variables
        $clusterName = $containerName                   # As a best practice, have the same name for the cluster and container
        $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
        $httpCredentials = Get-Credential
        $sshCredentials = Get-Credential

        New-AzHDInsightCluster -ClusterName $clusterName -ResourceGroupName $resourceGroupName -HttpCredential $httpCredentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop -Version "3.4" -OSType Linux -SshCredential $sshCredentials -ObjectID $objectId -AadTenantId $tenantID -CertificateFilePath $certificateFilePath -CertificatePassword $password

    När cmdleten har slutförts bör du se en utdata med klusterinformationen.


## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-the-data-lake-storage-gen1-account"></a>Kör testjobb i HDInsight-klustret för att använda datasjölagringsgenm1-kontot
När du har konfigurerat ett HDInsight-kluster kan du köra testjobb i klustret för att testa att HDInsight-klustret kan komma åt Data Lake Storage Gen1. För att göra det kör vi ett exempel på Hive-jobb som skapar en tabell med hjälp av exempeldata som du laddade upp tidigare till ditt Data Lake Storage Gen1-konto.

I det här avsnittet kommer du SSH i HDInsight Linux-klustret som du skapade och kör en exempel hive-fråga.

* Om du använder en Windows-klient till SSH i klustret läser [du Använda SSH med Linux-baserade Hadoop på HDInsight från Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Om du använder en Linux-klient till SSH i klustret läser [du Använda SSH med Linux-baserad Hadoop på HDInsight från Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)

1. När du har anslutit startar du Hive CLI med följande kommando:

        hive
2. Med CLI anger du följande satser för att skapa en ny tabell med namnet **fordon** med hjälp av exempeldata i Data Lake Storage Gen1:

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

## <a name="access-data-lake-storage-gen1-using-hdfs-commands"></a>Komma åt DataSjölagring Gen1 med HDFS-kommandon
När du har konfigurerat HDInsight-klustret för att använda Data Lake Storage Gen1 kan du använda HDFS-skalkommandona för att komma åt arkivet.

I det här avsnittet kommer du SSH i HDInsight Linux-klustret du skapade och kör HDFS-kommandona.

* Om du använder en Windows-klient till SSH i klustret läser [du Använda SSH med Linux-baserade Hadoop på HDInsight från Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Om du använder en Linux-klient till SSH i klustret läser [du Använda SSH med Linux-baserad Hadoop på HDInsight från Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)

När du har anslutit använder du följande HDFS-filsystemkommando för att lista filerna i Data Lake Storage Gen1-kontot.

    hdfs dfs -ls adl://<Data Lake Storage Gen1 account name>.azuredatalakestore.net:443/

Detta bör lista filen som du laddade upp tidigare till Data Lake Storage Gen1.

    15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
    Found 1 items
    -rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestoragegen1.azuredatalakestore.net:443/mynewfolder

Du kan också `hdfs dfs -put` använda kommandot för att överföra vissa filer `hdfs dfs -ls` till Data Lake Storage Gen1 och sedan använda för att kontrollera om filerna har överförts.

## <a name="see-also"></a>Se även
* [Använda DataSjölagringgend1 med Azure HDInsight-kluster](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
* [Portal: Skapa ett HDInsight-kluster för att använda DataSjölagring Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
