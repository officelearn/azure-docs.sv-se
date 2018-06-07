---
title: 'PowerShell: Azure HDInsight-kluster med Data Lake Store som tilläggslagring | Microsoft Docs'
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
ms.openlocfilehash: a68e6a076bb27626c100594de011cf07ff361768
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34627390"
---
# <a name="use-azure-powershell-to-create-an-hdinsight-cluster-with-data-lake-store-as-additional-storage"></a>Använda Azure PowerShell för att skapa ett HDInsight-kluster med Data Lake Store (som ytterligare lagringsutrymme)

> [!div class="op_single_selector"]
> * [Använda portalen](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Med hjälp av PowerShell (för standardlagring)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Med hjälp av PowerShell (för ytterligare lagringsutrymme)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Med Resource Manager](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Lär dig hur du konfigurerar ett HDInsight-kluster med Azure Data Lake Store med hjälp av Azure PowerShell **som ytterligare lagringsutrymme**. Anvisningar för hur du skapar ett HDInsight-kluster med Azure Data Lake Store som standardlagring finns [skapar ett HDInsight-kluster med Data Lake Store som standardlagring](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md).

> [!NOTE]
> Om du ska använda Azure Data Lake Store som ytterligare lagringsutrymme för HDInsight-kluster rekommenderar vi starkt att du gör detta när du skapar klustret, enligt beskrivningen i den här artikeln. Att lägga till Azure Data Lake Store som ytterligare lagringsutrymme för ett befintligt HDInsight-kluster är en komplicerad process där det är lätt att göra fel.
>

Data Lake Store kan användas som en standardlagring eller ytterligare storage-konto för stöds klustertyper. När du använder Data Lake Store som ytterligare lagringsutrymme standardkontot för lagring för kluster kommer fortfarande att Azure Storage BLOB (WASB) och kluster-relaterade-filer (till exempel loggar osv.) är fortfarande skrivs till standardlagring medan de data som du vill bearbeta kan lagras i ett Data Lake Store-konto. Med Data Lake Store som ett ytterligare storage-konto inte påverkar prestanda eller ge möjlighet att läsa eller skriva till lagring från klustret.

## <a name="using-data-lake-store-for-hdinsight-cluster-storage"></a>Med hjälp av Data Lake Store för HDInsight klusterlagring

Här följer några viktiga överväganden när för att använda HDInsight med Data Lake Store:

* Alternativet för att skapa HDInsight-kluster med åtkomst till Data Lake Store som ytterligare lagringsutrymmet är tillgängligt för HDInsight version 3.2, 3.4, 3.5 och 3,6.

Konfigurera HDInsight för att arbeta med Data Lake Store omfattar med hjälp av PowerShell följande steg:

* Skapa ett Azure Data Lake Store
* Konfigurera autentisering för rollbaserad åtkomst till Data Lake Store
* Skapa HDInsight-kluster med autentisering till Data Lake Store
* Köra ett testjobb på klustret

## <a name="prerequisites"></a>Förutsättningar
Innan du påbörjar de här självstudierna måste du ha:

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Installera Azure PowerShell 1.0 eller senare**. Se [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).
* **Windows SDK**. Du kan installera det [härifrån](https://dev.windows.com/en-us/downloads). Du kan använda den för att skapa ett säkerhetscertifikat.
* **Azure Active Directory Service Principal**. Stegen i den här kursen ger instruktioner för hur du skapar ett huvudnamn för tjänsten i Azure AD. Du måste dock vara en Azure AD-administratör för att kunna skapa ett huvudnamn för tjänsten. Om du är administratör för Azure AD kan du hoppa över det här kravet och fortsätta med guiden.

    **Om du inte är en Azure AD-administratör**, kommer du inte kunna utföra stegen som krävs för att skapa ett huvudnamn för tjänsten. I sådana fall måste din Azure AD-administratör först skapa ett huvudnamn för tjänsten innan du kan skapa ett HDInsight-kluster med Data Lake Store. Dessutom tjänstens huvudnamn måste skapas med hjälp av ett certifikat, enligt beskrivningen i [skapa ett huvudnamn för tjänsten med certifikat](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-certificate-from-certificate-authority).

## <a name="create-an-azure-data-lake-store"></a>Skapa ett Azure Data Lake Store
Följ dessa steg om du vill skapa ett Data Lake Store.

1. Öppna ett nytt Azure PowerShell-fönster på skrivbordet och ange följande fragment. När du uppmanas att logga in, se till att logga in som en administratör/prenumerationsägaren:

        # Log in to your Azure account
        Connect-AzureRmAccount

        # List all the subscriptions associated to your account
        Get-AzureRmSubscription

        # Select a subscription
        Set-AzureRmContext -SubscriptionId <subscription ID>

        # Register for Data Lake Store
        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

   > [!NOTE]
   > Om du får ett felmeddelande liknande `Register-AzureRmResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid` när du registrerar Data Lake Store-resursprovidern är det möjligt att din prenumeration inte är godkända för Azure Data Lake Store. Kontrollera att du aktiverar din Azure-prenumeration för Data Lake Store public preview genom att följa dessa [instruktioner](data-lake-store-get-started-portal.md).
   >
   >
2. Ett Azure Data Lake Store-konto är kopplat till en resursgrupp i Azure. Börja med att skapa en Azure-resursgrupp.

        $resourceGroupName = "<your new resource group name>"
        New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

    Du bör se utdata som liknar detta:

        ResourceGroupName : hdiadlgrp
        Location          : eastus2
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp

3. Skapa ett Azure Data Lake Store-konto. Namnet på kontot du anger får bara innehålla gemena bokstäver och siffror.

        $dataLakeStoreName = "<your new Data Lake Store name>"
        New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

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

5. Ladda upp exempeldata till Azure Data Lake. Vi använder det senare i den här artikeln för att verifiera att data är tillgängliga från ett HDInsight-kluster. Om du behöver exempeldata att ladda upp, kan du hämta mappen **Ambulansdata** från [Azure Data Lake Git-lagringsplatsen](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData).

        $myrootdir = "/"
        Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "C:\<path to data>\vehicle1_09142014.csv" -Destination $myrootdir\vehicle1_09142014.csv


## <a name="set-up-authentication-for-role-based-access-to-data-lake-store"></a>Konfigurera autentisering för rollbaserad åtkomst till Data Lake Store

Varje Azure-prenumeration är associerad med ett Azure Active Directory. Användare och tjänster som har åtkomst till resurserna i prenumerationen med Azure-portalen eller Azure Resource Manager API måste först autentisera med den Azure Active Directory. Åtkomst till Azure-prenumerationer och tjänster genom att tilldela dem sedan rätt roll för en Azure-resurs.  För tjänster identifierar ett huvudnamn för tjänsten tjänsten i Azure Active Directory (AAD). Det här avsnittet beskriver hur du ger en programtjänst som HDInsight, åtkomst till en Azure-resurs (det Azure Data Lake Store-konto du skapade tidigare) genom att skapa ett huvudnamn för tjänsten för programmet och tilldela roller till som via Azure PowerShell.

Om du vill konfigurera Active Directory-autentisering för Azure Data Lake måste du utföra följande uppgifter.

* Skapa ett självsignerat certifikat
* Skapa ett program i Azure Active Directory och ett huvudnamn för tjänsten

### <a name="create-a-self-signed-certificate"></a>Skapa ett självsignerat certifikat

Kontrollera att du har [Windows SDK](https://dev.windows.com/en-us/downloads) installerad innan du fortsätter med instruktionerna i det här avsnittet. Måste du också skapa en katalog som **C:\mycertdir**, där certifikatet ska skapas.

1. Navigera till den plats där du har installerat Windows SDK från PowerShell-fönster (normalt `C:\Program Files (x86)\Windows Kits\10\bin\x86` och använda den [MakeCert] [ makecert] verktyg för att skapa ett självsignerat certifikat och en privat nyckel. Använd följande kommandon.

        $certificateFileDir = "<my certificate directory>"
        cd $certificateFileDir

        makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -r -len 2048

    Du uppmanas att ange lösenordet för privata nyckeln. När kommandot har körs, bör du se en **CertFile.cer** och **mykey.pvk** i katalogen certifikat som du har angett.
2. Använd den [Pvk2Pfx] [ pvk2pfx] verktyg för att konvertera .pvk och .cer-filer som skapats av MakeCert till en .pfx-fil. Kör följande kommando.

        pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>

    När du uppmanas ange privata nyckel lösenordet du angav tidigare. Värdet som du anger för den **IO -** parameter är det lösenord som är associerad med den .pfx-fil. Du bör också se en CertFile.pfx i katalogen certifikat som du angav när kommandot har slutförts.

### <a name="create-an-azure-active-directory-and-a-service-principal"></a>Skapa ett Azure Active Directory och ett huvudnamn för tjänsten

I det här avsnittet kan du utföra stegen för att skapa en tjänstens huvudnamn för ett Azure Active Directory-program, tilldela en roll till tjänstens huvudnamn och autentisera sig som tjänstens huvudnamn genom att tillhandahålla ett certifikat. Kör följande kommandon för att skapa ett program i Azure Active Directory.

1. Klistra in följande cmdlets i PowerShell-konsolfönster. Kontrollera att värdet som du anger för den **- DisplayName** egenskapen är unika. Värden för **- webbsida** och **- IdentiferUris** är platshållarvärdena och kan inte verifieras.

        $certificateFilePath = "$certificateFileDir\CertFile.pfx"

        $password = Read-Host -Prompt "Enter the password" # This is the password you specified for the .pfx file

        $certificatePFX = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($certificateFilePath, $password)

        $rawCertificateData = $certificatePFX.GetRawCertData()

        $credential = [System.Convert]::ToBase64String($rawCertificateData)

        $application = New-AzureRmADApplication `
            -DisplayName "HDIADL" `
            -HomePage "https://contoso.com" `
            -IdentifierUris "https://mycontoso.com" `
            -CertValue $credential  `
            -StartDate $certificatePFX.NotBefore  `
            -EndDate $certificatePFX.NotAfter

        $applicationId = $application.ApplicationId
2. Skapa ett huvudnamn för tjänsten med hjälp av program-ID.

        $servicePrincipal = New-AzureRmADServicePrincipal -ApplicationId $applicationId

        $objectId = $servicePrincipal.Id
3. Ge service principal åtkomst till Data Lake Store-mapp och fil som du kommer åt från HDInsight-klustret. Kodfragmentet nedan ger åtkomst till roten på Data Lake Store-konto (dit du kopierade Exempeldatafilen), och själva filen.

        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path / -AceType User -Id $objectId -Permissions All
        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path /vehicle1_09142014.csv -AceType User -Id $objectId -Permissions All

## <a name="create-an-hdinsight-linux-cluster-with-data-lake-store-as-additional-storage"></a>Skapa ett kluster i HDInsight Linux med Data Lake Store som ytterligare lagringsutrymme

I det här avsnittet skapar vi ett HDInsight Hadoop Linux-kluster med Data Lake Store som ytterligare lagringsutrymme. HDInsight-klustret och Data Lake Store måste vara på samma plats för den här versionen.

1. Börja med att hämta prenumerationen klient-ID. Du behöver som senare.

        $tenantID = (Get-AzureRmContext).Tenant.TenantId
2. Den här versionen kan för Hadoop-kluster Data Lake Store endast användas som ett ytterligare lagringsutrymme för klustret. Standard-lagringen kommer fortfarande att Azure storage-blobbar (WASB). Så måste vi först skapa storage-konto och behållare som krävs för klustret.

        # Create an Azure storage account
        $location = "East US 2"
        $storageAccountName = "<StorageAcccountName>"   # Provide a Storage account name

        New-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -Location $location -Type Standard_GRS

        # Create an Azure Blob Storage container
        $containerName = "<ContainerName>"              # Provide a container name
        $storageAccountKey = (Get-AzureRmStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        New-AzureStorageContainer -Name $containerName -Context $destContext
3. Skapa HDInsight-klustret. Använd följande cmdlets.

        # Set these variables
        $clusterName = $containerName                   # As a best practice, have the same name for the cluster and container
        $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
        $httpCredentials = Get-Credential
        $sshCredentials = Get-Credential

        New-AzureRmHDInsightCluster -ClusterName $clusterName -ResourceGroupName $resourceGroupName -HttpCredential $httpCredentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop -Version "3.4" -OSType Linux -SshCredential $sshCredentials -ObjectID $objectId -AadTenantId $tenantID -CertificateFilePath $certificateFilePath -CertificatePassword $password

    Du bör se utdata visar information om kluster efter cmdleten har slutförts.


## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-the-data-lake-store"></a>Kör testjobb i HDInsight-klustret för att använda Data Lake Store
När du har konfigurerat ett HDInsight-kluster, kan du köra testjobb på klustret för att testa att HDInsight-klustret har åtkomst till Data Lake Store. Om du vill göra det, kommer vi kör ett Hive-jobb som skapar en tabell med exempeldata som du tidigare har överförts till din Data Lake Store.

I det här avsnittet kommer du att SSH i HDInsight Linux-kluster du skapat och kör den en exempelfråga Hive.

* Om du använder en Windows-klient till SSH till klustret finns [använda SSH med Linux-baserade Hadoop i HDInsight från Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Om du använder en Linux-klient till SSH till klustret finns [använda SSH med Linux-baserade Hadoop i HDInsight från Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)

1. När du är ansluten, startar du Hive-CLI med hjälp av följande kommando:

        hive
2. Med hjälp av CLI, ange följande instruktioner för att skapa en ny tabell med namnet **fordon** med exempeldata i Data Lake Store:

        DROP TABLE vehicles;
        CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://<mydatalakestore>.azuredatalakestore.net:443/';
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

## <a name="access-data-lake-store-using-hdfs-commands"></a>Åtkomst till Data Lake Store med hjälp av HDFS-kommandon
När du har konfigurerat HDInsight-klustret för att använda Data Lake Store kan använda du HDFS-gränssnittskommandon tillgång till store.

I det här avsnittet kommer du att SSH i HDInsight Linux-kluster du skapat och kör HDFS-kommandon.

* Om du använder en Windows-klient till SSH till klustret finns [använda SSH med Linux-baserade Hadoop i HDInsight från Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Om du använder en Linux-klient till SSH till klustret finns [använda SSH med Linux-baserade Hadoop i HDInsight från Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)

När du är ansluten, Använd kommandot filesystem HDFS för att visa filer i Data Lake Store.

    hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/

Du bör nu se filen som du tidigare har överförts till Data Lake Store.

    15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
    Found 1 items
    -rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder

Du kan också använda den `hdfs dfs -put` kommando för att överföra filer till Data Lake Store och sedan använda `hdfs dfs -ls` att kontrollera om filerna som har laddats upp.

## <a name="see-also"></a>Se även
* [Använd Data Lake Store med Azure HDInsight-kluster](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
* [Portalen: Skapa ett HDInsight-kluster om du vill använda Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
