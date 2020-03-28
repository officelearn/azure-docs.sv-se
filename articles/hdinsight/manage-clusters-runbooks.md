---
title: 'Självstudiekurs: Använda Azure Automation-runbooks för att skapa kluster - Azure HDInsight'
description: Lär dig hur du skapar och tar bort Azure HDInsight-kluster med skript som körs i molnet med hjälp av Azure Automation-runbooks.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 12/27/2019
ms.openlocfilehash: 05c0aaf6cc33442fa4f36eb38eb0d6d593fc6c1f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75553515"
---
# <a name="tutorial-create-azure-hdinsight-clusters-with-azure-automation"></a>Självstudiekurs: Skapa Azure HDInsight-kluster med Azure Automation

Med Azure Automation kan du skapa skript som körs i molnet och hantera Azure-resurser på begäran eller baserat på ett schema. I den här artikeln beskrivs hur du skapar PowerShell-runbooks för att skapa och ta bort Azure HDInsight-kluster.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Installera moduler som behövs för att interagera med HDInsight.
> * Skapa och lagra autentiseringsuppgifter som behövs när klustret skapas.
> * Skapa en ny Azure Automation-runbook för att skapa ett HDInsight-kluster.

Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Krav

* Ett befintligt [Azure Automation-konto](../automation/automation-quickstart-create-account.md).
* Ett befintligt [Azure Storage-konto](../storage/common/storage-account-create.md), som kommer att användas som klusterlagring.

## <a name="install-hdinsight-modules"></a>Installera HDInsight-moduler

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj dina Azure Automation-konton.
1. Välj **galleriet Moduler** under **Delade resurser**.
1. Skriv **AzureRM.Profile** i rutan och tryck enter för att söka. Välj tillgängligt sökresultat.
1. Välj **Importera**på skärmen **AzureRM.profile** . Markera rutan för att uppdatera Azure-moduler och välj sedan **OK**.

    ![importera AzureRM.profile-modul](./media/manage-clusters-runbooks/import-azurermprofile-module.png)

1. Gå tillbaka till modulernas galleri genom att välja **galleriet Moduler** under **Delade resurser**.
1. Skriv **HDInsight**. Välj **AzureRM.HDInsight**.

    ![bläddra i HDInsight-moduler](./media/manage-clusters-runbooks/browse-modules-hdinsight.png)

1. På panelen **AzureRM.HDInsight** väljer du **Importera** och **OK**.

    ![importera AzureRM.HDInsight-modul](./media/manage-clusters-runbooks/import-azurermhdinsight-module.png)

## <a name="create-credentials"></a>Skapa autentiseringsuppgifter

1. Under **Delade resurser**väljer du **Autentiseringsuppgifter**.
1. Välj **Lägg till en autentiseringstillstånd**.
1. Ange nödvändig information på panelen **Ny autentiseringsuppgifter.** Den här autentiseringsinformationen är att lagra klusterlösenordet, vilket gör att du kan logga in på Ambari.

    | Egenskap | Värde |
    | --- | --- |
    | Namn | `cluster-password` |
    | Användarnamn | `admin` |
    | lösenord | `SECURE_PASSWORD` |
    | Bekräfta lösenord | `SECURE_PASSWORD` |

1. Välj **Skapa**.
1. Upprepa samma process för en ny `ssh-password` autentiseringsdatabas med användarnamn `sshuser` och ett lösenord som du väljer. Välj **Skapa**. Den här autentiseringsdatabasen är att lagra SSH-lösenordet för klustret.

    ![skapa autentiseringsuppgift](./media/manage-clusters-runbooks/create-credentials.png)

## <a name="create-a-runbook-to-create-a-cluster"></a>Skapa en runbook för att skapa ett kluster

1. Välj **Runbooks** under **Process Automation**.
1. Välj **Skapa en runbook**.
1. Ange ett namn på runbooken på panelen Skapa `hdinsight-cluster-create`en **runbook,** till exempel . Välj **Powershell** i listrutan **Runbook-typ.**
1. Välj **Skapa**.

    ![skapa runbook](./media/manage-clusters-runbooks/create-runbook.png)

1. Ange följande kod på skärmen **Redigera PowerShell Runbook** och välj **Publicera:**

    ![publicera runbook](./media/manage-clusters-runbooks/publish-runbook.png)

    ```powershell
    Param
    (
      [Parameter (Mandatory= $true)]
      [String] $subscriptionID,
    
      [Parameter (Mandatory= $true)]
      [String] $resourceGroup,
    
      [Parameter (Mandatory= $true)]
      [String] $storageAccount,
    
      [Parameter (Mandatory= $true)]
      [String] $containerName,
    
      [Parameter (Mandatory= $true)]
      [String] $clusterName
    )
    ### Authenticate to Azure 
    $Conn = Get-AutomationConnection -Name 'AzureRunAsConnection'
    Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
    
    # Set cluster variables
    $storageAccountKey = (Get-AzureRmStorageAccountKey –Name $storageAccount –ResourceGroupName $resourceGroup)[0].value 
    
    # Setting cluster credentials
    
    #Automation credential for Cluster Admin
    $clusterCreds = Get-AutomationPSCredential –Name 'cluster-password'
    
    #Automation credential for user to SSH into cluster
    $sshCreds = Get-AutomationPSCredential –Name 'ssh-password' 
    
    $clusterType = "Hadoop" #Use any supported cluster type (Hadoop, HBase, Storm, etc.)
    $clusterOS = "Linux"
    $clusterWorkerNodes = 3
    $clusterNodeSize = "Standard_D3_v2"
    $location = Get-AzureRmStorageAccount –StorageAccountName $storageAccount –ResourceGroupName $resourceGroup | %{$_.Location}
    
    ### Provision HDInsight cluster
    New-AzureRmHDInsightCluster –ClusterName $clusterName –ResourceGroupName $resourceGroup –Location $location –DefaultStorageAccountName "$storageAccount.blob.core.windows.net" –DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName –ClusterType $clusterType –OSType $clusterOS –Version “3.6” –HttpCredential $clusterCreds –SshCredential $sshCreds –ClusterSizeInNodes $clusterWorkerNodes –HeadNodeSize $clusterNodeSize –WorkerNodeSize $clusterNodeSize
    ```

## <a name="create-a-runbook-to-delete-a-cluster"></a>Skapa en runbook för att ta bort ett kluster

1. Välj **Runbooks** under **Process Automation**.
1. Välj **Skapa en runbook**.
1. Ange ett namn på runbooken på panelen Skapa `hdinsight-cluster-delete`en **runbook,** till exempel . Välj **Powershell** i listrutan **Runbook-typ.**
1. Välj **Skapa**.
1. Ange följande kod på skärmen **Redigera PowerShell Runbook** och välj **Publicera:**

    ```powershell
    Param
    (
      [Parameter (Mandatory= $true)]
      [String] $clusterName
    )
    
    ### Authenticate to Azure 
    $Conn = Get-AutomationConnection -Name 'AzureRunAsConnection'
    Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
    
    Remove-AzureRmHDInsightCluster -ClusterName $clusterName
    ```

## <a name="execute-runbooks"></a>Kör runbooks

### <a name="create-a-cluster"></a>Skapa ett kluster

1. Visa listan över Runbooks för ditt Automation-konto genom att välja **Runbooks** under **Process Automation**.
1. Välj `hdinsight-cluster-create`eller det namn som du använde när du skapade runbooken för klusterskapande.
1. Välj **Start** om du vill köra runbooken omedelbart. Du kan också schemalägga runbooks så att de körs med jämna mellanrum. Se [Schemalägga en runbook i Azure Automation](../automation/shared-resources/schedules.md)
1. Ange de parametrar som krävs för skriptet och välj **OK**. Då skapas ett nytt HDInsight-kluster med det namn som du angav i **parametern CLUSTERNAME.**

    ![köra skapa klusterkörningsbok](./media/manage-clusters-runbooks/execute-create-runbook.png)

### <a name="delete-a-cluster"></a>Ta bort ett kluster

Ta bort klustret `hdinsight-cluster-delete` genom att välja den runbook som du skapade. Välj **Start**, ange parametern **CLUSTERNAME** och sselect **OK**.

## <a name="clean-up-resources"></a>Rensa resurser

När det inte längre behövs tar du bort Det Azure Automation-konto som skapades för att undvika oavsiktliga avgifter. Det gör du genom att navigera till Azure-portalen, välja den resursgrupp där du skapade Azure Automation-kontot, välja Automation-kontot och välj sedan **Ta bort**.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hantera Apache Hadoop-kluster i HDInsight med hjälp av Azure PowerShell](hdinsight-administer-use-powershell.md)