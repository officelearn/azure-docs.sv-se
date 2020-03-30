---
title: Skapa HDInsight-kluster med Azure Data Lake Storage Gen1 som standardlagring med PowerShell | Microsoft Dokument"
description: Använda Azure PowerShell för att skapa och använda HDInsight-kluster med Azure Data Lake Storage Gen1
services: data-lake-store,hdinsight
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 8917af15-8e37-46cf-87ad-4e6d5d67ecdb
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: c57a45145d9abc43d0ca79839ea297dfc025db9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "66161417"
---
# <a name="create-hdinsight-clusters-with-azure-data-lake-storage-gen1-as-default-storage-by-using-powershell"></a>Skapa HDInsight-kluster med Azure Data Lake Storage Gen1 som standardlagring med powershell

> [!div class="op_single_selector"]
> * [Använda Azure Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Använda PowerShell (för standardlagring)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Använda PowerShell (för ytterligare lagringsutrymme)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Använda Resurshanteraren](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

Lär dig hur du använder Azure PowerShell för att konfigurera Azure HDInsight-kluster med Azure Data Lake Storage Gen1, som standardlagring. Instruktioner om hur du skapar ett HDInsight-kluster med Data Lake Storage Gen1 som ytterligare lagring finns i [Skapa ett HDInsight-kluster med Data Lake Storage Gen1 som ytterligare lagring](data-lake-store-hdinsight-hadoop-use-powershell.md).

Här är några viktiga överväganden för att använda HDInsight med Data Lake Storage Gen1:

* Alternativet att skapa HDInsight-kluster med åtkomst till Data Lake Storage Gen1 som standardlagring är tillgängligt för HDInsight version 3.5 och 3.6.

* Alternativet att skapa HDInsight-kluster med åtkomst till Data Lake Storage Gen1 som standardlagring är *inte tillgängligt* för HDInsight Premium-kluster.

Om du vill konfigurera HDInsight så att de fungerar med DataSjölagring Gen1 med PowerShell följer du instruktionerna i de kommande fem avsnitten.

## <a name="prerequisites"></a>Krav

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Innan du påbörjar den här självstudien ska du se till att du uppfyller följande krav:

* **En Azure-prenumeration:** Gå till [Hämta kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1.0 eller senare**: Se [Hur du installerar och konfigurerar PowerShell](/powershell/azure/overview).
* **Windows Software Development Kit (SDK):** Om du vill installera Windows SDK går du till [Nedladdningar och verktyg för Windows 10](https://dev.windows.com/downloads). SDK används för att skapa ett säkerhetscertifikat.
* **Huvudnamn för Azure Active Directory-tjänsten**: Den här självstudien beskriver hur du skapar ett tjänsthuvudnamn i Azure Active Directory (Azure AD). Om du vill skapa ett tjänsthuvudnamn måste du dock vara administratör för Azure AD. Om du är administratör kan du hoppa över den här förutsättningen och fortsätta med självstudien.

    >[!NOTE]
    >Du kan bara skapa ett tjänsthuvudnamn om du är Azure AD-administratör. Din Azure AD-administratör måste skapa ett tjänsthuvudnamn innan du kan skapa ett HDInsight-kluster med Data Lake Storage Gen1. Tjänstens huvudnamn måste skapas med ett certifikat enligt beskrivningen i [Skapa ett huvudnamn för tjänsten med certifikat](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority).
    >

## <a name="create-a-data-lake-storage-gen1-account"></a>Skapa ett Data Lake Storage Gen1-konto

Så här skapar du ett Data Lake Storage Gen1-konto:

1. Öppna ett PowerShell-fönster från skrivbordet och ange sedan kodavsnitten nedan. När du uppmanas att logga in loggar du in som en av prenumerationsadministratörerna eller ägarna. 

        # Sign in to your Azure account
        Connect-AzAccount

        # List all the subscriptions associated to your account
        Get-AzSubscription

        # Select a subscription
        Set-AzContext -SubscriptionId <subscription ID>

        # Register for Data Lake Storage Gen1
        Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

    > [!NOTE]
    > Om du registrerar resursleverantören Data Lake Storage Gen1 `Register-AzResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid`och får ett fel som liknar , kanske din prenumeration inte är vitlistad för Data Lake Storage Gen1. Om du vill aktivera din Azure-prenumeration för Data Lake Storage Gen1 följer du instruktionerna i [Komma igång med Azure Data Lake Storage Gen1 med hjälp av Azure-portalen](data-lake-store-get-started-portal.md).
    >

2. Ett Data Lake Storage Gen1-konto är associerat med en Azure-resursgrupp. Börja med att skapa en resursgrupp.

        $resourceGroupName = "<your new resource group name>"
        New-AzResourceGroup -Name $resourceGroupName -Location "East US 2"

    Utdata bör se ut så här:

        ResourceGroupName : hdiadlgrp
        Location          : eastus2
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp

3. Skapa ett Data Lake Storage Gen1-konto. Det kontonamn som du anger får bara innehålla gemener och siffror.

        $dataLakeStorageGen1Name = "<your new Data Lake Storage Gen1 name>"
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

4. Om du använder DataSjölagring gen1 som standardlagring måste du ange en rotsökväg som de klusterspecifika filerna kopieras till när klustret skapas. Om du vill skapa en rotsökväg, som är **/clusters/hdiadlcluster** i kodavsnittet, använder du följande cmdlets:

        $myrootdir = "/"
        New-AzDataLakeStoreItem -Folder -AccountName $dataLakeStorageGen1Name -Path $myrootdir/clusters/hdiadlcluster


## <a name="set-up-authentication-for-role-based-access-to-data-lake-storage-gen1"></a>Konfigurera autentisering för rollbaserad åtkomst till Data Lake Storage Gen1
Varje Azure-prenumeration är associerad med en Azure AD-entitet. Användare och tjänster som får åtkomst till prenumerationsresurser med hjälp av Azure-portalen eller Azure Resource Manager-API:et måste först autentisera med Azure AD. Åtkomst beviljas till Azure-prenumerationer och tjänster genom att tilldela dem rätt roll på en Azure-resurs. För tjänster identifierar ett tjänsthuvudnamn tjänsten i Azure AD.

Det här avsnittet illustrerar hur du beviljar en programtjänst, till exempel HDInsight, åtkomst till en Azure-resurs (Data Lake Storage Gen1-kontot som du skapade tidigare). Du gör det genom att skapa ett tjänsthuvudnamn för programmet och tilldela roller till det via PowerShell.

Om du vill konfigurera Active Directory-autentisering för datasjölagring gen1 utför du uppgifterna i följande två avsnitt.

### <a name="create-a-self-signed-certificate"></a>Skapa ett självsignerat certifikat
Kontrollera att du har [Windows SDK](https://dev.windows.com/en-us/downloads) installerat innan du fortsätter med stegen i det här avsnittet. Du måste också ha skapat en katalog, till exempel *C:\mycertdir*, där du skapar certifikatet.

1. Gå till den plats där du installerade Windows SDK (vanligtvis *C:\Program Files (x86)\Windows Kits\10\bin\x86)* från powershell-fönstret och använd [verktyget MakeCert][makecert] för att skapa ett självsignerat certifikat och en privat nyckel. Använd följande kommandon:

        $certificateFileDir = "<my certificate directory>"
        cd $certificateFileDir

        makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -r -len 2048

    Du uppmanas att ange lösenordet för den privata nyckeln. När kommandot har körts bör du se **CertFile.cer** och **mykey.pvk** i certifikatkatalogen som du angav.
2. Använd [verktyget Pvk2Pfx][pvk2pfx] för att konvertera filerna .pvk och .cer som MakeCert skapade till en PFX-fil. Kör följande kommando:

        pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>

    När du uppmanas att ange lösenordet för den privata nyckel som du angav tidigare. Det värde som du anger för parametern **-po** är lösenordet som är associerat med PFX-filen. När kommandot har slutförts bör du också se en **CertFile.pfx** i certifikatkatalogen som du angav.

### <a name="create-an-azure-ad-and-a-service-principal"></a>Skapa ett Azure AD och ett tjänsthuvudnamn
I det här avsnittet skapar du ett tjänsthuvudnamn för ett Azure AD-program, tilldelar en roll till tjänstens huvudnamn och autentiserar som tjänstens huvudnamn genom att tillhandahålla ett certifikat. Om du vill skapa ett program i Azure AD kör du följande kommandon:

1. Klistra in följande cmdlets i PowerShell-konsolfönstret. Kontrollera att värdet du anger för egenskapen **-DisplayName** är unikt. Värdena för **-HomePage** och **-IdentiferUris** är platshållarvärden och verifieras inte.

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
3. Bevilja tjänstens huvudåtkomst till datasjölagringsgend1-roten och alla mappar i rotsökvägen som du angav tidigare. Använd följande cmdlets:

        Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path / -AceType User -Id $objectId -Permissions All
        Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path /clusters -AceType User -Id $objectId -Permissions All
        Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path /clusters/hdiadlcluster -AceType User -Id $objectId -Permissions All

## <a name="create-an-hdinsight-linux-cluster-with-data-lake-storage-gen1-as-the-default-storage"></a>Skapa ett HDInsight Linux-kluster med Data Lake Storage Gen1 som standardlagring

I det här avsnittet skapar du ett HDInsight Hadoop Linux-kluster med Data Lake Storage Gen1 som standardlagring. För den här versionen måste HDInsight-klustret och Data Lake Storage Gen1 vara på samma plats.

1. Hämta klient-ID:et för prenumeration och lagra det så att det används senare.

        $tenantID = (Get-AzContext).Tenant.TenantId

2. Skapa HDInsight-klustret med hjälp av följande cmdlets:

        # Set these variables

        $location = "East US 2"
        $storageAccountName = $dataLakeStorageGen1Name                         # Data Lake Storage Gen1 account name
        $storageRootPath = "<Storage root path you specified earlier>" # E.g. /clusters/hdiadlcluster
        $clusterName = "<unique cluster name>"
        $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
        $httpCredentials = Get-Credential
        $sshCredentials = Get-Credential

        New-AzHDInsightCluster `
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

    När cmdleten har slutförts bör du se en utdata som listar klusterinformationen.

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-data-lake-storage-gen1"></a>Kör testjobb i HDInsight-klustret för att använda Data Lake Storage Gen1
När du har konfigurerat ett HDInsight-kluster kan du köra testjobb på det för att säkerställa att det kan komma åt Data Lake Storage Gen1. Det gör du genom att köra ett exempel på Hive-jobb för att skapa en tabell som använder exempeldata som redan är tillgängliga i DataSjölagring gen1 vid * \<klusterrot>/exempel/data/sample.log*.

I det här avsnittet gör du en SSH-anslutning (Secure Shell) till HDInsight Linux-klustret som du skapade och kör sedan en exempel-Hive-fråga.

* Om du använder en Windows-klient för att göra en SSH-anslutning till klustret läser [du Använda SSH med Linux-baserade Hadoop på HDInsight från Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Om du använder en Linux-klient för att göra en SSH-anslutning till klustret läser [du Använda SSH med Linux-baserade Hadoop på HDInsight från Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. När du har gjort anslutningen startar du Hive-kommandoradsgränssnittet (CLI) med hjälp av följande kommando:

        hive
2. Använd CLI för att ange följande satser för att skapa en ny tabell med namnet **fordon** med hjälp av exempeldata i Data Lake Storage Gen1:

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'adl:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Du bör se frågeutdata på SSH-konsolen.

    >[!NOTE]
    >Sökvägen till exempeldata i föregående SKAPA `adl:///example/data/`TABELL-kommando är , där `adl:///` är klusterroten. I exemplet med klusterroten som anges i den här `adl://hdiadlstore.azuredatalakestore.net/clusters/hdiadlcluster`självstudien är kommandot . Du kan antingen använda det kortare alternativet eller ange den fullständiga sökvägen till klusterroten.
    >

## <a name="access-data-lake-storage-gen1-by-using-hdfs-commands"></a>Komma åt DataSjölagring Gen1 med hdfs-kommandon
När du har konfigurerat HDInsight-klustret så att datasjölagringgenr1 används kan du använda kommandona för det delade filsystemet (Hadoop Distributed File System) för att komma åt arkivet.

I det här avsnittet gör du en SSH-anslutning till HDInsight Linux-klustret som du skapade och kör sedan HDFS-kommandona.

* Om du använder en Windows-klient för att göra en SSH-anslutning till klustret läser [du Använda SSH med Linux-baserade Hadoop på HDInsight från Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Om du använder en Linux-klient för att göra en SSH-anslutning till klustret läser [du Använda SSH med Linux-baserade Hadoop på HDInsight från Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

När du har gjort anslutningen listar du filerna i Data Lake Storage Gen1 med hjälp av följande HDFS-filsystemkommando.

    hdfs dfs -ls adl:///

Du kan också `hdfs dfs -put` använda kommandot för att överföra vissa filer `hdfs dfs -ls` till Data Lake Storage Gen1 och sedan använda för att kontrollera om filerna har överförts.

## <a name="see-also"></a>Se även
* [Använda DataSjölagringgend1 med Azure HDInsight-kluster](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
* [Azure-portal: Skapa ett HDInsight-kluster för att använda Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
