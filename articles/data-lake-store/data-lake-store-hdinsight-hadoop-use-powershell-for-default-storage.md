---
title: Skapa HDInsight-kluster med Azure Data Lake Storage Gen1 som standard storage med hjälp av PowerShell | Microsoft Docs
description: Använda Azure PowerShell för att skapa och använda HDInsight-kluster med Azure Data Lake Storage Gen1
services: data-lake-store,hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 8917af15-8e37-46cf-87ad-4e6d5d67ecdb
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: 84bed7031307316545cc8e468196c6b12cde7bb7
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56237069"
---
# <a name="create-hdinsight-clusters-with-azure-data-lake-storage-gen1-as-default-storage-by-using-powershell"></a>Skapa HDInsight-kluster med Azure Data Lake Storage Gen1 som standard storage med hjälp av PowerShell

> [!div class="op_single_selector"]
> * [Använda Azure Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Använda PowerShell (för standardlagring)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Använda PowerShell (för ytterligare lagring)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Använd Resource Manager](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

Lär dig hur du använder Azure PowerShell för att konfigurera Azure HDInsight-kluster med Azure Data Lake Storage Gen1, som standardlagringsutrymme. Anvisningar om hur du skapar ett HDInsight-kluster med Data Lake Storage Gen1 som ytterligare lagringsutrymme finns i [skapa ett HDInsight-kluster med Data Lake Storage Gen1 som ytterligare lagringsutrymme](data-lake-store-hdinsight-hadoop-use-powershell.md).

Här följer några viktiga överväganden för användning av HDInsight med Data Lake Storage Gen1:

* Alternativet för att skapa HDInsight-kluster med åtkomst till Data Lake Storage Gen1 som standardlagringsutrymme är tillgänglig för HDInsight version 3.5 och 3.6.

* Alternativet för att skapa HDInsight-kluster med åtkomst till Data Lake Storage Gen1 skick standardlagring *inte tillgänglig* för HDInsight Premium-kluster.

Följ anvisningarna i nästa fem avsnitt om du vill konfigurera HDInsight för att arbeta med Data Lake Storage Gen1 med hjälp av PowerShell.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Innan du börjar den här självstudien måste du kontrollera att du uppfyller följande krav:

* **En Azure-prenumeration**: Gå till [skaffa Azure kostnadsfria utvärderingsversionen](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1.0 eller senare**: Se [hur du installerar och konfigurerar PowerShell](/powershell/azure/overview).
* **Windows Software Development Kit (SDK)**: Om du vill installera Windows SDK, gå till [laddar ned och verktyg för Windows 10](https://dev.windows.com/downloads). SDK: N används för att skapa ett säkerhetscertifikat.
* **Azure Active Directory-tjänstobjekt**: Den här självstudien beskrivs hur du skapa ett tjänstobjekt i Azure Active Directory (AD Azure). Om du vill skapa ett huvudnamn för tjänsten, måste du vara en Azure AD-administratör. Om du är administratör kan du hoppa över den nödvändiga komponenten och fortsätter med självstudien.

    >[!NOTE]
    >Du kan skapa en tjänst huvudnamn endast om du är en Azure AD-administratör. Azure AD-administratören måste skapa ett tjänstens huvudnamn av innan du kan skapa ett HDInsight-kluster med Data Lake Storage Gen1. Tjänstens huvudnamn måste skapas med ett certifikat, enligt beskrivningen i [skapa tjänstens huvudnamn med certifikat](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority).
    >

## <a name="create-a-data-lake-storage-gen1-account"></a>Skapa ett Data Lake Storage Gen1-konto

Om du vill skapa ett Data Lake Storage Gen1-konto, gör du följande:

1. Öppna ett PowerShell-fönster på skrivbordet och ange fragmenten nedan. När du uppmanas att logga in, logga in som en av prenumerationens administratörer eller ägare. 

        # Sign in to your Azure account
        Connect-AzAccount

        # List all the subscriptions associated to your account
        Get-AzSubscription

        # Select a subscription
        Set-AzContext -SubscriptionId <subscription ID>

        # Register for Data Lake Storage Gen1
        Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

    > [!NOTE]
    > Om du registrerar resursprovidern Data Lake Storage Gen1 och får ett felmeddelande som liknar `Register-AzResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid`, din prenumeration kanske inte är godkänd för Data Lake Storage Gen1. Om du vill aktivera din Azure-prenumeration för Data Lake Storage Gen1, följer du anvisningarna i [Kom igång med Azure Data Lake Storage Gen1 med hjälp av Azure portal](data-lake-store-get-started-portal.md).
    >

2. Ett Data Lake Storage Gen1-konto är kopplat till en Azure-resursgrupp. Börja med att skapa en resursgrupp.

        $resourceGroupName = "<your new resource group name>"
        New-AzResourceGroup -Name $resourceGroupName -Location "East US 2"

    Du bör se utdata som liknar detta:

        ResourceGroupName : hdiadlgrp
        Location          : eastus2
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp

3. Skapa ett Data Lake Storage Gen1-konto. Namnet på kontot du anger måste innehålla endast gemener och siffror.

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

4. Med hjälp av Data Lake Storage Gen1 som standardlagring måste du ange en rotsökväg klusterspecifika filer som kopieras när klustret skapas. Skapa en rotsökväg, vilket är **/kluster/hdiadlcluster** i kodfragmentet använder du följande cmdlets:

        $myrootdir = "/"
        New-AzDataLakeStoreItem -Folder -AccountName $dataLakeStorageGen1Name -Path $myrootdir/clusters/hdiadlcluster


## <a name="set-up-authentication-for-role-based-access-to-data-lake-storage-gen1"></a>Konfigurera autentisering för rollbaserad åtkomst till Data Lake Storage Gen1
Alla Azure-prenumerationer är associerade med en Azure AD-entitet. Användare och tjänster som har åtkomst till prenumerationsresurser med hjälp av Azure portal eller Azure Resource Manager API: et måste först autentisera med Azure AD. Komma åt Azure-prenumerationer och tjänster genom att tilldela dem till rätt roll på en Azure-resurs. För tjänster identifierar ett huvudnamn för tjänsten tjänsten i Azure AD.

Det här avsnittet visar hur du ger en programtjänst, till exempel HDInsight, åtkomst till en Azure-resurs (det Data Lake Storage Gen1 konto som du skapade tidigare). Du kan göra det genom att skapa ett tjänstens huvudnamn för programmet och tilldela roller till den via PowerShell.

Om du vill konfigurera Active Directory-autentisering för Data Lake Storage Gen1 utför du uppgifterna i följande två avsnitt.

### <a name="create-a-self-signed-certificate"></a>Skapa ett självsignerat certifikat
Kontrollera att du har [Windows SDK](https://dev.windows.com/en-us/downloads) installerad innan du fortsätter med stegen i det här avsnittet. Du måste har också skapat en katalog som *C:\mycertdir*, där du skapar certifikatet.

1. Från PowerShell-fönstret går du till den plats där du har installerat Windows SDK (vanligtvis *C:\Program Files (x86) \Windows Kits\10\bin\x86*) och använda den [MakeCert] [ makecert] Verktyg för att skapa ett självsignerat certifikat och en privat nyckel. Använd följande kommandon:

        $certificateFileDir = "<my certificate directory>"
        cd $certificateFileDir

        makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -r -len 2048

    Du uppmanas att ange lösenordet för privata nyckeln. När kommandot körs utan problem, bör du se **CertFile.cer** och **mykey.pvk** i katalogen certifikat som du har angett.
2. Använd den [Pvk2Pfx] [ pvk2pfx] verktyg för att konvertera .pvk och .cer-filer som skapats av MakeCert till en .pfx-fil. Kör följande kommando:

        pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>

    När du uppmanas, anger du den privata nyckeln lösenord som du angav tidigare. Värdet som du anger för den **-IO** parametern är det lösenord som är associerat med den .pfx-fil. När kommandot har slutförts, bör du också se en **CertFile.pfx** i katalogen certifikat som du har angett.

### <a name="create-an-azure-ad-and-a-service-principal"></a>Skapa en Azure AD och ett huvudnamn för tjänsten
I det här avsnittet, skapa ett tjänsthuvudnamn för en Azure AD-program, tilldela en roll till tjänstens huvudnamn och autentisera sig som tjänstens huvudnamn genom att tillhandahålla ett certifikat. Om du vill skapa ett program i Azure AD, kör du följande kommandon:

1. Klistra in följande cmdletar i PowerShell-konsolfönster. Kontrollera att värdet du anger för den **- DisplayName** egenskapen är unika. Värdena för **- startsidan** och **- IdentiferUris** är platshållarvärden och inte verifieras.

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
3. Ge tjänstens huvudnamn åtkomst till Data Lake Storage Gen1 roten och alla mappar i rotsökvägen som du angav tidigare. Använd följande cmdlets:

        Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path / -AceType User -Id $objectId -Permissions All
        Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path /clusters -AceType User -Id $objectId -Permissions All
        Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path /clusters/hdiadlcluster -AceType User -Id $objectId -Permissions All

## <a name="create-an-hdinsight-linux-cluster-with-data-lake-storage-gen1-as-the-default-storage"></a>Skapa ett HDInsight Linux-kluster med Data Lake Storage Gen1 som standardlagring

I det här avsnittet skapar du ett HDInsight Hadoop Linux-kluster med Data Lake Storage Gen1 som standardlagring. Den här versionen är måste HDInsight-kluster och Data Lake Storage Gen1 vara på samma plats.

1. Hämta klient-ID för prenumerationen och lagra den för senare användning.

        $tenantID = (Get-AzContext).Tenant.TenantId

2. Skapa HDInsight-kluster med hjälp av följande cmdlets:

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

    När cmdleten har slutförts, bör du se utdata som visar information om kluster.

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-data-lake-storage-gen1"></a>Kör testjobb på HDInsight-kluster för att använda Data Lake Storage Gen1
När du har konfigurerat ett HDInsight-kluster, kan du köra testjobb på den så att den kan komma åt Data Lake Storage Gen1. Om du vill göra det kör du ett exempel Hive-jobb för att skapa en tabell som använder exempeldata som redan är tillgänglig i Data Lake Storage Gen1 på  *<cluster root>/example/data/sample.log*.

Du upprättar en Secure Shell (SSH)-anslutning till HDInsight Linux-kluster som du skapade i det här avsnittet och kör sedan en exempelfråga för Hive.

* Om du använder en Windows-klient för att göra en SSH-anslutning till klustret, se [använda SSH med Linux-baserat Hadoop i HDInsight från Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Om du använder en Linux-klient för att göra en SSH-anslutning till klustret, se [använda SSH med Linux-baserat Hadoop i HDInsight från Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. När du har gjort anslutningen, börjar du Hive-kommandoradsgränssnittet (CLI) med hjälp av följande kommando:

        hive
2. Använd CLI för att ange följande instruktioner för att skapa en ny tabell med namnet **fordon** med exempeldata i Data Lake Storage Gen1:

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'adl:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Du bör se utdata i SSH-konsolen.

    >[!NOTE]
    >Sökvägen till exempeldata i det föregående kommandot CREATE TABLE är `adl:///example/data/`, där `adl:///` är klusterroten. Följande exempel på klusterroten som anges i den här självstudien kommandot är `adl://hdiadlstore.azuredatalakestore.net/clusters/hdiadlcluster`. Du kan använda kortare alternativ, eller så kan du ange den fullständiga sökvägen till klusterroten.
    >

## <a name="access-data-lake-storage-gen1-by-using-hdfs-commands"></a>Åtkomst till Data Lake Storage Gen1 med hjälp av HDFS-kommandon
När du har konfigurerat HDInsight-kluster för att använda Data Lake Storage Gen1 kan använda du Hadoop Distributed File System (HDFS) shell-kommandon för åtkomst till store.

Du gör en SSH-anslutning till HDInsight Linux-kluster som du skapade i det här avsnittet och kör sedan HDFS-kommandon.

* Om du använder en Windows-klient för att göra en SSH-anslutning till klustret, se [använda SSH med Linux-baserat Hadoop i HDInsight från Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Om du använder en Linux-klient för att göra en SSH-anslutning till klustret, se [använda SSH med Linux-baserat Hadoop i HDInsight från Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

När du har gjort anslutningen du visa filerna i Data Lake Storage Gen1 med hjälp av följande kommando för HDFS file system.

    hdfs dfs -ls adl:///

Du kan också använda den `hdfs dfs -put` kommando för att ladda upp några filer till Data Lake Storage Gen1 och sedan använda `hdfs dfs -ls` att verifiera om filerna har laddats upp.

## <a name="see-also"></a>Se också
* [Använda Data Lake Storage Gen1 med Azure HDInsight-kluster](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
* [Azure-portalen: Skapa ett HDInsight-kluster om du vill använda Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
