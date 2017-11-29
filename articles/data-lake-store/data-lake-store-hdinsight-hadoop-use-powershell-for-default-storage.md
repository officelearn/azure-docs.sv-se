---
title: "Skapa HDInsight-kluster med Data Lake Store som standardlagring med hjälp av PowerShell | Microsoft Docs"
description: "Använda Azure PowerShell för att skapa och använda HDInsight-kluster med Azure Data Lake Store"
services: data-lake-store,hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 8917af15-8e37-46cf-87ad-4e6d5d67ecdb
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: 2f1793c2de2b68a8b155ada73044c6bc36882612
ms.sourcegitcommit: 651a6fa44431814a42407ef0df49ca0159db5b02
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2017
---
# <a name="create-hdinsight-clusters-with-data-lake-store-as-default-storage-by-using-powershell"></a>Skapa HDInsight-kluster med Data Lake Store som standardlagring med hjälp av PowerShell
> [!div class="op_single_selector"]
> * [Använda Azure Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Använd PowerShell (för standardlagring)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Använd PowerShell (för ytterligare lagringsutrymme)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Använd Resource Manager](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

Lär dig hur du konfigurerar Azure HDInsight-kluster med Azure Data Lake Store, som standardlagring med hjälp av Azure PowerShell. Anvisningar om hur du skapar ett HDInsight-kluster med Data Lake Store som ytterligare lagringsutrymme finns [skapar ett HDInsight-kluster med Data Lake Store som ytterligare lagringsutrymme](data-lake-store-hdinsight-hadoop-use-powershell.md).

Här följer några viktiga överväganden när för att använda HDInsight med Data Lake Store:

* Alternativet för att skapa HDInsight-kluster med åtkomst till Data Lake Store som standardlagring är tillgänglig för HDInsight version 3.5 och 3,6.

* Alternativet för att skapa HDInsight-kluster med åtkomst till Data Lake Store eftersom standardlagring *inte tillgänglig* för HDInsight Premium-kluster.

Följ instruktionerna i följande fem avsnitt om du vill konfigurera HDInsight för att arbeta med Data Lake Store med hjälp av PowerShell.

## <a name="prerequisites"></a>Krav
Innan du påbörjar den här självstudien måste du kontrollera att du uppfyller följande krav:

* **En Azure-prenumeration**: Gå till [hämta kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1.0 eller senare**: se [hur du installerar och konfigurerar PowerShell](/powershell/azure/overview).
* **Windows Software Development Kit (SDK)**: Om du vill installera Windows SDK, gå till [laddar ned och verktyg för Windows 10](https://dev.windows.com/en-us/downloads). SDK: N används för att skapa ett säkerhetscertifikat.
* **Azure Active Directory-tjänstens huvudnamn**: den här självstudiekursen beskrivs hur du skapar ett huvudnamn för tjänsten i Azure Active Directory (AD Azure). Du måste dock vara en Azure AD-administratör om du vill skapa ett huvudnamn för tjänsten. Om du är administratör kan du gå vidare med självstudiekursen hoppa över det här kravet.

    >[!NOTE]
    >Du kan skapa en tjänst huvudnamn endast om du är administratör för Azure AD. Azure AD-administratör skapa en tjänst huvudnamn innan du kan skapa ett HDInsight-kluster med Data Lake Store. Tjänstens huvudnamn måste skapas med ett certifikat som beskrivs i [skapa ett huvudnamn för tjänsten med certifikat](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-certificate-from-certificate-authority).
    >

## <a name="create-a-data-lake-store-account"></a>Skapa ett Data Lake Store-konto
Om du vill skapa ett Data Lake Store-konto, gör du följande:

1. Öppna ett PowerShell-fönster på skrivbordet och ange sedan fragmenten nedan. När du uppmanas att logga in, logga in som en prenumerationsadministratörer eller ägare. 

        # Sign in to your Azure account
        Login-AzureRmAccount

        # List all the subscriptions associated to your account
        Get-AzureRmSubscription

        # Select a subscription
        Set-AzureRmContext -SubscriptionId <subscription ID>

        # Register for Data Lake Store
        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

    > [!NOTE]
    > Om du registerresursleverantören Data Lake Store och får ett felmeddelande liknande `Register-AzureRmResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid`, prenumerationen kanske inte är godkända för Data Lake Store. Om du vill aktivera din Azure-prenumeration för Data Lake Store förhandsversion följer du anvisningarna i [Kom igång med Azure Data Lake Store med hjälp av Azure portal](data-lake-store-get-started-portal.md).
    >

2. Ett Data Lake Store-konto är kopplat till en Azure-resursgrupp. Börja med att skapa en resursgrupp.

        $resourceGroupName = "<your new resource group name>"
        New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

    Du bör se utdata som liknar detta:

        ResourceGroupName : hdiadlgrp
        Location          : eastus2
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp

3. Skapa ett Data Lake Store-konto. Namnet på kontot du anger måste innehålla endast små bokstäver och siffror.

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

4. Med hjälp av Data Lake Store som standardlagring måste du ange en rotsökväg som kopieras filerna klusterspecifika när klustret skapas. Så här skapar du en rotsökväg, vilket är **/kluster/hdiadlcluster** i fragment, använder du följande cmdlets:

        $myrootdir = "/"
        New-AzureRmDataLakeStoreItem -Folder -AccountName $dataLakeStoreName -Path $myrootdir/clusters/hdiadlcluster


## <a name="set-up-authentication-for-role-based-access-to-data-lake-store"></a>Konfigurera autentisering för rollbaserad åtkomst till Data Lake Store
Alla Azure-prenumerationer är associerade med en Azure AD-entitet. Användare och tjänster som har åtkomst till prenumerationsresurser med hjälp av Azure-portalen eller Azure Resource Manager API måste först autentisera med Azure AD. Åtkomst till Azure-prenumerationer och tjänster genom att tilldela dem sedan rätt roll för en Azure-resurs. För tjänster identifierar en tjänst som tjänsten i Azure AD.

Det här avsnittet beskriver hur du ger en tjänst för programmet, till exempel HDInsight, åtkomst till en Azure-resurs (Data Lake Store-kontot som du skapade tidigare). Det gör du genom att skapa en tjänst huvudnamn för programmet och tilldela roller till den via PowerShell.

Om du vill konfigurera Active Directory-autentisering för Azure Data Lake utför du uppgifterna i följande två avsnitt.

### <a name="create-a-self-signed-certificate"></a>Skapa ett självsignerat certifikat
Kontrollera att du har [Windows SDK](https://dev.windows.com/en-us/downloads) installerad innan du fortsätter med instruktionerna i det här avsnittet. Måste du också skapa en katalog som *C:\mycertdir*, där du kan skapa certifikatet.

1. Gå till den plats där du har installerat Windows SDK från PowerShell-fönster (vanligtvis *C:\Program Files (x86) \Windows Kits\10\bin\x86*) och använda den [MakeCert] [ makecert] för att skapa ett självsignerat certifikat och en privat nyckel. Använd följande kommandon:

        $certificateFileDir = "<my certificate directory>"
        cd $certificateFileDir

        makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -r -len 2048

    Du uppmanas att ange lösenordet för privata nyckeln. Efter att kommandot har körts, bör du se **CertFile.cer** och **mykey.pvk** i katalogen certifikat som du angav.
2. Använd den [Pvk2Pfx] [ pvk2pfx] verktyg för att konvertera .pvk och .cer-filer som skapats av MakeCert till en .pfx-fil. Kör följande kommando:

        pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>

    När du uppmanas ange privata nyckel som du angav tidigare. Värdet som du anger för den **IO -** parameter är det lösenord som är associerad med den .pfx-fil. När kommandot har slutförts, bör du också se en **CertFile.pfx** i katalogen certifikat som du angav.

### <a name="create-an-azure-ad-and-a-service-principal"></a>Skapa en Azure AD och ett huvudnamn för tjänsten
I det här avsnittet, skapa ett huvudnamn för tjänsten för Azure AD-program, tilldela en roll till tjänstens huvudnamn och autentisera sig som tjänstens huvudnamn genom att tillhandahålla ett certifikat. Om du vill skapa ett program i Azure AD, kör du följande kommandon:

1. Klistra in följande cmdlets i PowerShell-konsolfönster. Se till att värdet som du anger för den **- DisplayName** egenskapen är unika. Värdena för **- webbsida** och **- IdentiferUris** är platshållarvärdena och kan inte verifieras.

        $certificateFilePath = "$certificateFileDir\CertFile.pfx"

        $password = Read-Host –Prompt "Enter the password" # This is the password you specified for the .pfx file

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
3. Ge service principal åtkomst till Data Lake Store-roten och alla mappar i rotsökvägen som du angav tidigare. Använd följande cmdlets:

        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path / -AceType User -Id $objectId -Permissions All
        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path /clusters -AceType User -Id $objectId -Permissions All
        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path /clusters/hdiadlcluster -AceType User -Id $objectId -Permissions All

## <a name="create-an-hdinsight-linux-cluster-with-data-lake-store-as-the-default-storage"></a>Skapa ett kluster i HDInsight Linux med Data Lake Store som standardlagring

I det här avsnittet skapar du ett HDInsight Hadoop Linux-kluster med Data Lake Store som standardlagring. HDInsight-kluster och Data Lake Store måste vara på samma plats för den här versionen.

1. Hämta klient-ID för prenumerationen och spara den för senare användning.

        $tenantID = (Get-AzureRmContext).Tenant.TenantId

2. Skapa HDInsight-kluster med hjälp av följande cmdlets:

        # Set these variables

        $location = "East US 2"
        $storageAccountName = $dataLakeStoreName                       # Data Lake Store account name
        $storageRootPath = "<Storage root path you specified earlier>" # E.g. /clusters/hdiadlcluster
        $clusterName = "<unique cluster name>"
        $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
        $httpCredentials = Get-Credential
        $sshCredentials = Get-Credential

        New-AzureRmHDInsightCluster `
               -ClusterType Hadoop `
               -OSType Linux `
               -ClusterSizeInNodes $clusterNodes `
               -ResourceGroupName $resourceGroupName `
               -ClusterName $clusterName `
               -HttpCredential $httpCredentials `
               -Location $location `
               -DefaultStorageAccountType AzureDataLakeStore `
               -DefaultStorageAccountName "$storageAccountName.azuredatalakestore.net" `
               -DefaultStorageRootPath $storageRootPath `
               -Version "3.6" `
               -SshCredential $sshCredentials `
               -AadTenantId $tenantId `
               -ObjectId $objectId `
               -CertificateFilePath $certificateFilePath `
               -CertificatePassword $password

    Du bör se utdata som visar information om kluster när cmdleten har slutförts.

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-data-lake-store"></a>Kör testjobb i HDInsight-klustret för att använda Data Lake Store
När du har konfigurerat ett HDInsight-kluster, kan du köra testjobb på den så att den kan komma åt Data Lake Store. Gör du genom att köra ett exempel Hive-jobb för att skapa en tabell som använder exempeldata som redan finns i Data Lake Store på  *<cluster root>/example/data/sample.log*.

Du upprättar en anslutning för SSH (Secure Shell) i HDInsight Linux-kluster som du skapade i det här avsnittet och kör sedan en exempelfråga Hive.

* Om du använder en Windows-klient för att göra en SSH-anslutning till klustret finns [använda SSH med Linux-baserade Hadoop i HDInsight från Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Om du använder en Linux-klient för att göra en SSH-anslutning till klustret finns [använda SSH med Linux-baserade Hadoop i HDInsight från Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. När du har gjort anslutningen, startar du Hive-kommandoradsgränssnittet (CLI) med hjälp av följande kommando:

        hive
2. Använd CLI för att ange följande instruktioner om du vill skapa en ny tabell med namnet **fordon** med exempeldata i Data Lake Store:

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'adl:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Du bör se utdata på konsolen SSH.

    >[!NOTE]
    >Sökvägen till exempeldata i föregående CREATE TABLE-kommando är `adl:///example/data/`, där `adl:///` är roten till klustret. Följande exempel för rot-kluster som har angetts i den här självstudiekursen kommandot är `adl://hdiadlstore.azuredatalakestore.net/clusters/hdiadlcluster`. Du kan använda kortare alternativ, eller så kan du ange den fullständiga sökvägen till kluster-roten.
    >

## <a name="access-data-lake-store-by-using-hdfs-commands"></a>Åtkomst till Data Lake Store med hjälp av HDFS-kommandon
När du har konfigurerat HDInsight-klustret för att använda Data Lake Store kan använda du Hadoop Distributed File System (HDFS)-gränssnittskommandon tillgång till store.

Du gör en SSH-anslutning i HDInsight Linux-kluster som du skapade i det här avsnittet och kör sedan HDFS-kommandon.

* Om du använder en Windows-klient för att göra en SSH-anslutning till klustret finns [använda SSH med Linux-baserade Hadoop i HDInsight från Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Om du använder en Linux-klient för att göra en SSH-anslutning till klustret finns [använda SSH med Linux-baserade Hadoop i HDInsight från Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

När du har gjort anslutningen kan du visa filer i Data Lake Store med hjälp av följande kommando för HDFS file system.

    hdfs dfs -ls adl:///

Du kan också använda den `hdfs dfs -put` kommando för att överföra filer till Data Lake Store och sedan använda `hdfs dfs -ls` att kontrollera om filerna som har laddats upp.

## <a name="see-also"></a>Se även
* [Använd Data Lake Store med Azure HDInsight-kluster](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
* [Azure-portalen: skapa ett HDInsight-kluster om du vill använda Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
