---
title: Kontinuerlig integrering och leverans i Azure Data Factory
description: Lär dig hur du använder kontinuerlig integrering och leverans för att flytta Data Factory pipelines från en miljö (utveckling, testning, produktion) till en annan.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
manager: jroth
ms.topic: conceptual
ms.date: 02/12/2020
ms.openlocfilehash: dc0da82447b5df0735b16f46298a2f473ee61ea0
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/14/2020
ms.locfileid: "79371383"
---
# <a name="continuous-integration-and-delivery-in-azure-data-factory"></a>Kontinuerlig integrering och leverans i Azure Data Factory

## <a name="overview"></a>Översikt

Kontinuerlig integrering är en metod för att testa varje ändring av kodbasen automatiskt och så tidigt som möjligt. Kontinuerlig leverans följer testerna som sker under kontinuerlig integrering och skickar ändringar till ett mellanlagrings-eller produktions system.

I Azure Data Factory innebär kontinuerlig integrering och leverans (CI/CD) flytt av Data Factory pipelines från en miljö (utveckling, testning, produktion) till en annan. Du kan använda Data Factory UX-integrering med Azure Resource Manager mallar för att göra CI/CD.

I Data Factory UX kan du skapa en Resource Manager-mall från den nedrullningsbara menyn **arm-mall** . När du väljer **Exportera arm-mall**genererar portalen Resource Manager-mallen för data fabriken och en konfigurations fil som innehåller alla anslutnings strängar och andra parametrar. Sedan skapar du en konfigurations fil för varje miljö (utveckling, test, produktion). Den huvudsakliga Resource Manager-mallfilen är densamma för alla miljöer.

För en nio minuters introduktion till den här funktionen och en demonstration, titta på den här videon:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-integration-and-deployment-using-Azure-Data-Factory/player]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cicd-lifecycle"></a>CI/CD-livscykel

Nedan visas ett exempel på en översikt över CI/CD-livscykeln i en Azure-datafabrik som är konfigurerad med Azure databaser git. Mer information om hur du konfigurerar en git-lagringsplats finns i [käll kontroll i Azure Data Factory](source-control.md).

1.  En utvecklings data fabrik skapas och konfigureras med Azure databaser git. Alla utvecklare måste ha behörighet att redigera Data Factory resurser som pipelines och data uppsättningar.

1.  När utvecklare gör ändringar i sina funktions grenar, så att de kan felsöka sina pipeliner med de senaste ändringarna. Mer information om hur du felsöker en pipeline-körning finns i [iterativ utveckling och fel sökning med Azure Data Factory](iterative-development-debugging.md).

1.  När utvecklarna är nöjd med sina ändringar, skapar de en pull-begäran från sina funktions grenar till huvud-eller samarbets grenen för att få sina ändringar granskade av peer-datorer.

1.  När en pull-begäran har godkänts och ändringar slås samman i huvud grenen, kan ändringarna publiceras i utvecklings fabriken.

1.  När teamet är redo att distribuera ändringarna till test fabriken och sedan till produktions fabriken, exporterar teamet Resource Manager-mallen från huvud grenen.

1.  Den exporterade Resource Manager-mallen distribueras med olika parameterstyrda filer till test fabriken och produktions fabriken.

## <a name="create-a-resource-manager-template-for-each-environment"></a>Skapa en Resource Manager-mall för varje miljö

1. I listan **arm-mall** väljer du **Exportera arm** -mall för att exportera Resource Manager-mallen för din data fabrik i utvecklings miljön.

   ![Exportera en Resource Manager-mall](media/continuous-integration-deployment/continuous-integration-image1.png)

1. I test-och produktions data fabrikerna väljer du **Importera arm-mall**. Den här åtgärden tar dig till Azure Portal, där du kan importera den exporterade mallen. Välj **skapa en egen mall i redigeraren** för att öppna Principeditorn i Resource Manager.

   ![Bygg en egen mall](media/continuous-integration-deployment/custom-deployment-build-your-own-template.png) 

1. Välj **Läs in fil**och välj sedan den genererade Resource Manager-mallen. Detta är den **arm_template. JSON** -fil som finns i. zip-filen som exporterades i steg 1.

   ![Redigera mall](media/continuous-integration-deployment/custom-deployment-edit-template.png)

1. I avsnittet inställningar anger du konfigurations värden, t. ex. länkade tjänst uppgifter. När du är klar väljer du **köp** för att distribuera Resource Manager-mallen.

   ![Avsnittet Inställningar](media/continuous-integration-deployment/continuous-integration-image5.png)

### <a name="connection-strings"></a>Anslutningssträngar

Information om hur du konfigurerar anslutnings strängar finns i Connector-artikeln. Till exempel, för Azure SQL Database, se [Kopiera data till eller från Azure SQL Database med Azure Data Factory](connector-azure-sql-database.md). Om du vill verifiera en anslutnings sträng kan du öppna vyn kod för resursen i Data Factory UX. I kodvyn tas lösen ordet eller konto nyckel delen av anslutnings strängen bort. Öppna kodvyn genom att välja ikonen som marker ATS här:

![Öppna kodvyn för att se anslutnings strängen](media/continuous-integration-deployment/continuous-integration-codeview.png)

## <a name="automate-continuous-integration-by-using-azure-pipelines-releases"></a>Automatisera kontinuerlig integrering med hjälp av Azure pipelines-versioner

Nedan följer en guide för hur du konfigurerar en version av en Azure-pipeline, som automatiserar distributionen av en data fabrik till flera miljöer.

![Diagram över kontinuerlig integrering med Azure-pipelines](media/continuous-integration-deployment/continuous-integration-image12.png)

### <a name="requirements"></a>Krav

-   En Azure-prenumeration som är länkad till Visual Studio Team Foundation Server eller Azure-databaser som använder [Azure Resource Manager tjänstens slut punkt](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-resource-manager).

-   En data fabrik som kon figurer ATS med Azure databaser git-integrering.

-   Ett [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) som innehåller hemligheterna för varje miljö.

### <a name="set-up-an-azure-pipelines-release"></a>Konfigurera en version av Azure pipelines

1.  Öppna det projekt som har kon figurer ATS med din data fabrik i [Azure-DevOps](https://dev.azure.com/).

1.  Välj **pipelines**på vänster sida av sidan och välj sedan **versioner**.

    ![Välj pipeliner, versioner](media/continuous-integration-deployment/continuous-integration-image6.png)

1.  Välj **ny pipeline**eller, om du har befintliga pipeliner, väljer du **ny** och sedan **ny versions pipeline**.

1.  Välj den **tomma jobb** mal len.

    ![Välj tomt jobb](media/continuous-integration-deployment/continuous-integration-image13.png)

1.  I rutan **scen namn** anger du namnet på din miljö.

1.  Välj **Lägg till artefakt**och välj sedan den databas som kon figurer ATS med din data fabrik. Välj **adf_publish** för **standard grenen**. Välj **senaste från standard gren**för **standard versionen**.

    ![Lägg till en artefakt](media/continuous-integration-deployment/continuous-integration-image7.png)

1.  Lägg till en Azure Resource Manager distributions uppgift:

    a.  I vyn fas väljer du **Visa fas aktiviteter**.

    ![Vyn fas](media/continuous-integration-deployment/continuous-integration-image14.png)

    b.  Skapa en ny uppgift. Sök efter **Azure Resource Group-distribution**och välj sedan **Lägg till**.

    c.  I distributions aktiviteten väljer du prenumeration, resurs grupp och plats för mål data fabriken. Ange autentiseringsuppgifter om det behövs.

    d.  I listan **åtgärd** väljer du **skapa eller uppdatera resurs grupp**.

    e.  Välj knappen med tre punkter ( **...** ) bredvid rutan **mall** . Bläddra till Azure Resource Manager mall som du skapade med hjälp av **Importera arm-mall** i avsnittet [skapa en Resource Manager-mall för varje miljö](continuous-integration-deployment.md#create-a-resource-manager-template-for-each-environment) i den här artikeln. Leta efter den här filen i mappen <FactoryName> i adf_publish grenen.

    f.  Välj **...** bredvid rutan **mallparametrar** för att välja parameter filen. Vilken fil du väljer beror på om du har skapat en kopia eller använder standard filen ARMTemplateParametersForFactory. JSON.

    g.  Välj **...** bredvid rutan **Åsidosätt mallparametrar** och ange informationen för mål data fabriken. För autentiseringsuppgifter som kommer från Azure Key Vault anger du hemlighetens namn mellan dubbla citat tecken. Om t. ex. hemlighetens namn är cred1 anger du **"$ (cred1)"** för det här värdet.

    h. Välj **stegvis** för **distributions läget**.

    > [!WARNING]
    > Om du väljer **Slutför** för **distributions läget**kan befintliga resurser tas bort, inklusive alla resurser i mål resurs gruppen som inte har definierats i Resource Manager-mallen.

    ![Data Factory Prod. distribution](media/continuous-integration-deployment/continuous-integration-image9.png)

1.  Spara versions pipelinen.

1. Om du vill utlösa en version väljer du **Skapa version**.

   ![Välj Skapa version](media/continuous-integration-deployment/continuous-integration-image10.png)

> [!IMPORTANT]
> I CI/CD-scenarier måste integrerings körnings typen (IR) i olika miljöer vara densamma. Om du till exempel har en lokal IR-anslutning i utvecklings miljön, måste samma IR också vara av typen egen värd i andra miljöer, till exempel test och produktion. Om du däremot delar integrerings körningar över flera steg, måste du konfigurera integration runtime som länkad egen värd i alla miljöer, till exempel utveckling, testning och produktion.

### <a name="get-secrets-from-azure-key-vault"></a>Hämta hemligheter från Azure Key Vault

Om du har hemligheter som ska skickas i en Azure Resource Manager-mall rekommenderar vi att du använder Azure Key Vault med Azure pipelines-versionen.

Det finns två sätt att hantera hemligheter:

1.  Lägg till filen hemligheter till Parameters. Mer information finns i [använda Azure Key Vault för att skicka ett säkert parameter värde under distributionen](../azure-resource-manager/templates/key-vault-parameter.md).

    Skapa en kopia av parameter filen som överförs till publicerings grenen. Ange värdena för de parametrar som du vill hämta från Key Vault med det här formatet:

    ```json
    {
        "parameters": {
            "azureSqlReportingDbPassword": {
                "reference": {
                    "keyVault": {
                        "id": "/subscriptions/<subId>/resourceGroups/<resourcegroupId> /providers/Microsoft.KeyVault/vaults/<vault-name> "
                    },
                    "secretName": " < secret - name > "
                }
            }
        }
    }
    ```

    När du använder den här metoden hämtas hemligheten automatiskt från nyckel valvet.

    Parameter filen måste också finnas i publicerings grenen.

1. Lägg till en [Azure Key Vault aktivitet](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-key-vault) före Azure Resource Manager distributions aktivitet som beskrivs i föregående avsnitt:

    1.  På fliken **aktiviteter** skapar du en ny uppgift. Sök efter **Azure Key Vault** och Lägg till det.

    1.  I Key Vault aktiviteten väljer du den prenumeration där du skapade nyckel valvet. Ange autentiseringsuppgifter om det behövs och välj sedan nyckel valvet.

    ![Lägg till en Key Vault uppgift](media/continuous-integration-deployment/continuous-integration-image8.png)

#### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>Bevilja behörighet till Azure pipelines-agenten

Azure Key Vault aktiviteten kan Miss Miss kan ett fel meddelande om nekad åtkomst om rätt behörigheter inte har angetts. Hämta loggarna för versionen och leta upp den. ps1-fil som innehåller kommandot för att ge behörighet till Azure pipelines-agenten. Du kan köra kommandot direkt. Du kan också kopiera ägar-ID: t från filen och lägga till åtkomst principen manuellt i Azure Portal. `Get` och `List` är de lägsta behörigheter som krävs.

### <a name="update-active-triggers"></a>Uppdatera aktiva utlösare

Distributionen kan inte utföras om du försöker uppdatera aktiva utlösare. Om du vill uppdatera aktiva utlösare måste du stoppa dem manuellt och sedan starta om dem efter distributionen. Du kan göra detta med hjälp av en Azure PowerShell uppgift:

1.  Lägg till en **Azure PowerShell** aktivitet på fliken **aktiviteter** i versionen. Välj aktivitet version 4. *. 

1.  Välj den prenumeration som din fabrik är i.

1.  Välj **skript fil Sök väg** som skript typ. Detta kräver att du sparar ditt PowerShell-skript i din lagrings plats. Följande PowerShell-skript kan användas för att stoppa utlösare:

    ```powershell
    $triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

Du kan slutföra liknande steg (med funktionen `Start-AzDataFactoryV2Trigger`) för att starta om utlösarna efter distributionen.

### <a name="sample-pre--and-post-deployment-script"></a>Exempel skript för för-och efter distribution

Följande exempel skript kan användas för att stoppa utlösare före distribution och starta om dem efteråt. Skriptet innehåller också kod för att ta bort resurser som har tagits bort. Spara skriptet i en Azure DevOps git-lagringsplats och referera till den via en Azure PowerShell aktivitet som använder version 4. *.

När du kör ett skript för för distribution måste du ange en variant av följande parametrar i fältet **skript argument** .

`-armTemplate "$(System.DefaultWorkingDirectory)/<your-arm-template-location>" -ResourceGroupName <your-resource-group-name> -DataFactoryName <your-data-factory-name>  -predeployment $true -deleteDeployment $false`


När du kör ett skript efter distribution måste du ange en variant av följande parametrar i fältet **skript argument** .

`-armTemplate "$(System.DefaultWorkingDirectory)/<your-arm-template-location>" -ResourceGroupName <your-resource-group-name> -DataFactoryName <your-data-factory-name>  -predeployment $false -deleteDeployment $true`

    ![Azure PowerShell task](media/continuous-integration-deployment/continuous-integration-image11.png)

Här är det skript som kan användas för för-och-distribution. IT-konton för borttagna resurser och resurs referenser.

```powershell
param
(
    [parameter(Mandatory = $false)] [String] $armTemplate,
    [parameter(Mandatory = $false)] [String] $ResourceGroupName,
    [parameter(Mandatory = $false)] [String] $DataFactoryName,
    [parameter(Mandatory = $false)] [Bool] $predeployment=$true,
    [parameter(Mandatory = $false)] [Bool] $deleteDeployment=$false
)

function getPipelineDependencies {
    param([System.Object] $activity)
    if ($activity.Pipeline) {
        return @($activity.Pipeline.ReferenceName)
    } elseif ($activity.Activities) {
        $result = @()
        return $activity.Activities | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
    } elseif ($activity.ifFalseActivities -or $activity.ifTrueActivities) {
        $result = @()
        $activity.ifFalseActivities | Where-Object {$_ -ne $null} | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        $activity.ifTrueActivities | Where-Object {$_ -ne $null} | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
    } elseif ($activity.defaultActivities) {
        $result = @()
        $activity.defaultActivities | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        if ($activity.cases) {
            $activity.cases | ForEach-Object{ $_.activities } | ForEach-Object{$result += getPipelineDependencies -activity $_ }
        }
        return $result
    }
}

function pipelineSortUtil {
    param([Microsoft.Azure.Commands.DataFactoryV2.Models.PSPipeline]$pipeline,
    [Hashtable] $pipelineNameResourceDict,
    [Hashtable] $visited,
    [System.Collections.Stack] $sortedList)
    if ($visited[$pipeline.Name] -eq $true) {
        return;
    }
    $visited[$pipeline.Name] = $true;
    $pipeline.Activities | ForEach-Object{ getPipelineDependencies -activity $_ -pipelineNameResourceDict $pipelineNameResourceDict}  | ForEach-Object{
        pipelineSortUtil -pipeline $pipelineNameResourceDict[$_] -pipelineNameResourceDict $pipelineNameResourceDict -visited $visited -sortedList $sortedList
    }
    $sortedList.Push($pipeline)

}

function Get-SortedPipelines {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $pipelines = Get-AzDataFactoryV2Pipeline -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $ppDict = @{}
    $visited = @{}
    $stack = new-object System.Collections.Stack
    $pipelines | ForEach-Object{ $ppDict[$_.Name] = $_ }
    $pipelines | ForEach-Object{ pipelineSortUtil -pipeline $_ -pipelineNameResourceDict $ppDict -visited $visited -sortedList $stack }
    $sortedList = new-object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSPipeline]
    
    while ($stack.Count -gt 0) {
        $sortedList.Add($stack.Pop())
    }
    $sortedList
}

function triggerSortUtil {
    param([Microsoft.Azure.Commands.DataFactoryV2.Models.PSTrigger]$trigger,
    [Hashtable] $triggerNameResourceDict,
    [Hashtable] $visited,
    [System.Collections.Stack] $sortedList)
    if ($visited[$trigger.Name] -eq $true) {
        return;
    }
    $visited[$trigger.Name] = $true;
    $trigger.Properties.DependsOn | Where-Object {$_ -and $_.ReferenceTrigger} | ForEach-Object{
        triggerSortUtil -trigger $triggerNameResourceDict[$_.ReferenceTrigger.ReferenceName] -triggerNameResourceDict $triggerNameResourceDict -visited $visited -sortedList $sortedList
    }
    $sortedList.Push($trigger)
}

function Get-SortedTriggers {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $triggers = Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
    $triggerDict = @{}
    $visited = @{}
    $stack = new-object System.Collections.Stack
    $triggers | ForEach-Object{ $triggerDict[$_.Name] = $_ }
    $triggers | ForEach-Object{ triggerSortUtil -trigger $_ -triggerNameResourceDict $triggerDict -visited $visited -sortedList $stack }
    $sortedList = new-object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSTrigger]
    
    while ($stack.Count -gt 0) {
        $sortedList.Add($stack.Pop())
    }
    $sortedList
}

function Get-SortedLinkedServices {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $linkedServices = Get-AzDataFactoryV2LinkedService -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
    $LinkedServiceHasDependencies = @('HDInsightLinkedService', 'HDInsightOnDemandLinkedService', 'AzureBatchLinkedService')
    $Akv = 'AzureKeyVaultLinkedService'
    $HighOrderList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]
    $RegularList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]
    $AkvList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]

    $linkedServices | ForEach-Object {
        if ($_.Properties.GetType().Name -in $LinkedServiceHasDependencies) {
            $HighOrderList.Add($_)
        }
        elseif ($_.Properties.GetType().Name -eq $Akv) {
            $AkvList.Add($_)
        }
        else {
            $RegularList.Add($_)
        }
    }

    $SortedList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]($HighOrderList.Count + $RegularList.Count + $AkvList.Count)
    $SortedList.AddRange($HighOrderList)
    $SortedList.AddRange($RegularList)
    $SortedList.AddRange($AkvList)
    $SortedList
}

$templateJson = Get-Content $armTemplate | ConvertFrom-Json
$resources = $templateJson.resources

#Triggers 
Write-Host "Getting triggers"
$triggersADF = Get-SortedTriggers -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
$triggersTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/triggers" }
$triggerNames = $triggersTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
$activeTriggerNames = $triggersTemplate | Where-Object { $_.properties.runtimeState -eq "Started" -and ($_.properties.pipelines.Count -gt 0 -or $_.properties.pipeline.pipelineReference -ne $null)} | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
$deletedtriggers = $triggersADF | Where-Object { $triggerNames -notcontains $_.Name }
$triggerstostop = $triggerNames | where { ($triggersADF | Select-Object name).name -contains $_ }

if ($predeployment -eq $true) {
    #Stop all triggers
    Write-Host "Stopping deployed triggers"
    $triggerstostop | ForEach-Object { 
        Write-host "Disabling trigger " $_
        Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
else {
    #Deleted resources
    #pipelines
    Write-Host "Getting pipelines"
    $pipelinesADF = Get-SortedPipelines -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $pipelinesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/pipelines" }
    $pipelinesNames = $pipelinesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedpipelines = $pipelinesADF | Where-Object { $pipelinesNames -notcontains $_.Name }
    #dataflows
    $dataflowsADF = Get-AzDataFactoryV2DataFlow -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $dataflowsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/dataflows" }
    $dataflowsNames = $dataflowsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataflow = $dataflowsADF | Where-Object { $dataflowsNames -notcontains $_.Name }
    #datasets
    Write-Host "Getting datasets"
    $datasetsADF = Get-AzDataFactoryV2Dataset -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $datasetsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/datasets" }
    $datasetsNames = $datasetsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataset = $datasetsADF | Where-Object { $datasetsNames -notcontains $_.Name }
    #linkedservices
    Write-Host "Getting linked services"
    $linkedservicesADF = Get-SortedLinkedServices -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $linkedservicesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/linkedservices" }
    $linkedservicesNames = $linkedservicesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedlinkedservices = $linkedservicesADF | Where-Object { $linkedservicesNames -notcontains $_.Name }
    #Integrationruntimes
    Write-Host "Getting integration runtimes"
    $integrationruntimesADF = Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $integrationruntimesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/integrationruntimes" }
    $integrationruntimesNames = $integrationruntimesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedintegrationruntimes = $integrationruntimesADF | Where-Object { $integrationruntimesNames -notcontains $_.Name }

    #Delete resources
    Write-Host "Deleting triggers"
    $deletedtriggers | ForEach-Object { 
        Write-Host "Deleting trigger "  $_.Name
        $trig = Get-AzDataFactoryV2Trigger -name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
        if ($trig.RuntimeState -eq "Started") {
            Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force 
        }
        Remove-AzDataFactoryV2Trigger -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting pipelines"
    $deletedpipelines | ForEach-Object { 
        Write-Host "Deleting pipeline " $_.Name
        Remove-AzDataFactoryV2Pipeline -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting dataflows"
    $deleteddataflow | ForEach-Object { 
        Write-Host "Deleting dataflow " $_.Name
        Remove-AzDataFactoryV2DataFlow -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting datasets"
    $deleteddataset | ForEach-Object { 
        Write-Host "Deleting dataset " $_.Name
        Remove-AzDataFactoryV2Dataset -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting linked services"
    $deletedlinkedservices | ForEach-Object { 
        Write-Host "Deleting Linked Service " $_.Name
        Remove-AzDataFactoryV2LinkedService -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting integration runtimes"
    $deletedintegrationruntimes | ForEach-Object { 
        Write-Host "Deleting integration runtime " $_.Name
        Remove-AzDataFactoryV2IntegrationRuntime -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }

    if ($deleteDeployment -eq $true) {
        Write-Host "Deleting ARM deployment ... under resource group: " $ResourceGroupName
        $deployments = Get-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName
        $deploymentsToConsider = $deployments | Where { $_.DeploymentName -like "ArmTemplate_master*" -or $_.DeploymentName -like "ArmTemplateForFactory*" } | Sort-Object -Property Timestamp -Descending
        $deploymentName = $deploymentsToConsider[0].DeploymentName

       Write-Host "Deployment to be deleted: " $deploymentName
        $deploymentOperations = Get-AzResourceGroupDeploymentOperation -DeploymentName $deploymentName -ResourceGroupName $ResourceGroupName
        $deploymentsToDelete = $deploymentOperations | Where { $_.properties.targetResource.id -like "*Microsoft.Resources/deployments*" }

        $deploymentsToDelete | ForEach-Object { 
            Write-host "Deleting inner deployment: " $_.properties.targetResource.id
            Remove-AzResourceGroupDeployment -Id $_.properties.targetResource.id
        }
        Write-Host "Deleting deployment: " $deploymentName
        Remove-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName -Name $deploymentName
    }

    #Start active triggers - after cleanup efforts
    Write-Host "Starting active triggers"
    $activeTriggerNames | ForEach-Object { 
        Write-host "Enabling trigger " $_
        Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
```

## <a name="use-custom-parameters-with-the-resource-manager-template"></a>Använd anpassade parametrar med Resource Manager-mallen

Om du är i GIT-läge kan du åsidosätta standard egenskaperna i Resource Manager-mallen för att ange egenskaper som är parameterstyrda i mallen och de egenskaper som är hårdkodade. Du kanske vill åsidosätta standard mal len för parameterisering i följande scenarier:

* Du använder automatiserad CI/CD och du vill ändra vissa egenskaper under distributionen av Resource Manager, men egenskaperna är inte parameterstyrda som standard.
* Fabriken är så stor att Resource Manager-standardmallen är ogiltig eftersom den har fler än det högsta tillåtna antalet parametrar (256).

Om du under dessa omständigheter vill åsidosätta standard mal len Parameterisering skapar du en fil med namnet **arm-Template-Parameters-definition. JSON** i mappen som anges som rotmapp för git-integreringen för Data Factory. Du måste använda det exakta fil namnet. Data Factory läser filen från den gren som du är närvarande på i Azure Data Factory portalen, inte bara från samarbets grenen. Du kan skapa eller redigera filen från en privat gren, där du kan testa dina ändringar genom att välja **Exportera arm-mall** i användar gränssnittet. Sedan kan du slå samman filen till samarbets grenen. Om ingen fil hittas används standard mal len.

> [!NOTE]
> En anpassad Parameterisering-mall ändrar inte gränsen för ARM-mallparameter på 256. Du kan välja och minska antalet parameter egenskaper.

### <a name="syntax-of-a-custom-parameters-file"></a>Syntax för en anpassad parameter fil

Nedan följer några rikt linjer som du följer när du skapar filen med anpassade parametrar. Filen består av ett avsnitt för varje entitetstyp: utlösare, pipeline, länkad tjänst, data uppsättning, integration Runtime och så vidare.
* Ange sökvägen till egenskapen under den relevanta entitetstypen.
* Om du anger ett egenskaps namn som `*` anger du att du vill Parameterisera alla egenskaper under den (endast ned till den första nivån, inte rekursivt). Du kan också ange undantag för den här konfigurationen.
* Att ange värdet för en egenskap som en sträng anger att du vill Parameterisera egenskapen. Använd formatet `<action>:<name>:<stype>`.
   *  `<action>` kan vara något av följande tecken:
      * `=` innebär att det aktuella värdet är standardvärdet för parametern.
      * `-` innebär att inte behålla standardvärdet för parametern.
      * `|` är ett specialfall för hemligheter från Azure Key Vault för anslutnings strängar eller nycklar.
   * `<name>` är namnet på parametern. Om det är tomt tar det med namnet på egenskapen. Om värdet börjar med ett `-`-kort, förkortas namnet. `AzureStorage1_properties_typeProperties_connectionString` skulle till exempel kortas till `AzureStorage1_connectionString`.
   * `<stype>` är typ av parameter. Om `<stype>` är tomt är standard typen `string`. Värden som stöds: `string`, `bool`, `number`, `object`och `securestring`.
* Att ange en matris i definitions filen anger att den matchande egenskapen i mallen är en matris. Data Factory itererar igenom alla objekt i matrisen med hjälp av definitionen som anges i integration runtime-objektet i matrisen. Det andra objektet, en sträng, blir namnet på egenskapen, som används som namn för parametern för varje iteration.
* En definition kan inte vara unik för en resurs instans. Alla definitioner gäller för alla resurser av den typen.
* Som standard är alla säkra strängar, som Key Vault hemligheter och säkra strängar, som anslutnings strängar, nycklar och tokens, parameterstyrda.
 
### <a name="sample-parameterization-template"></a>Exempel på Parameterisering-mall

Här är ett exempel på hur en Parameterisering-mall kan se ut så här:

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
        "properties": {
            "activities": [{
                "typeProperties": {
                    "waitTimeInSeconds": "-::number",
                    "headers": "=::object"
                }
            }]
        }
    },
    "Microsoft.DataFactory/factories/integrationRuntimes": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "typeProperties": {
                "recurrence": {
                    "*": "=",
                    "interval": "=:triggerSuffix:number",
                    "frequency": "=:-freq"
                },
                "maxConcurrency": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "secretAccessKey": "|"
                }
            }
        },
        "AzureDataLakeStore": {
            "properties": {
                "typeProperties": {
                    "dataLakeStoreUri": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    }
}
```
Här är en förklaring av hur föregående mall skapas, uppdelat efter resurs typ.

#### <a name="pipelines"></a>Pipelines
    
* Alla egenskaper i sökvägen `activities/typeProperties/waitTimeInSeconds` är parameterstyrda. Alla aktiviteter i en pipeline som har en kod nivå egenskap med namnet `waitTimeInSeconds` (till exempel `Wait` aktivitet) är parameterstyrda som ett tal med ett standard namn. Men det finns inget standardvärde i Resource Manager-mallen. Det är en obligatorisk Indatatyp under distributionen av Resource Manager.
* På samma sätt har en egenskap som kallas `headers` (till exempel i en `Web` aktivitet) en parameter med typen `object` (JObject). Det har ett standardvärde, vilket är samma värde som käll fabriken.

#### <a name="integrationruntimes"></a>IntegrationRuntimes

* Alla egenskaper under Sök vägs `typeProperties` är parameterstyrda med respektive standardvärden. Det finns till exempel två egenskaper under `IntegrationRuntimes` typ egenskaper: `computeProperties` och `ssisProperties`. Båda egenskaps typerna skapas med deras respektive standardvärden och typer (objekt).

#### <a name="triggers"></a>Utlösare

* Under `typeProperties`är två egenskaper parameterstyrda. Den första är `maxConcurrency`, som har angetts som standardvärde och är av typen`string`. Den har standard parameter namnet `<entityName>_properties_typeProperties_maxConcurrency`.
* Egenskapen `recurrence` är också parametriserad. Under den här nivån anges alla egenskaper på den nivån som parameterstyrda som strängar, med standardvärden och parameter namn. Ett undantag är `interval`-egenskapen, som är parameterstyrda som typ `number`. Parameter namnet är suffixet med `<entityName>_properties_typeProperties_recurrence_triggerSuffix`. På samma sätt är egenskapen `freq` en sträng och är parameterstyrda som en sträng. Men egenskapen `freq` är parameterstyrda utan ett standardvärde. Namnet är kortare och suffixet. Till exempel `<entityName>_freq`.

#### <a name="linkedservices"></a>LinkedServices

* Länkade tjänster är unika. Eftersom länkade tjänster och data uppsättningar har en mängd olika typer, kan du ange en typ bestämd anpassning. I det här exemplet används en speciell mall för alla länkade tjänster av typen `AzureDataLakeStore`. För alla andra (via `*`) kommer en annan mall att tillämpas.
* Egenskapen `connectionString` är parameterstyrda som ett `securestring` värde. Det har inget standardvärde. Det kommer att ha ett förkortat parameter namn som är suffix med `connectionString`.
* Egenskapen `secretAccessKey` sker som ett `AzureKeyVaultSecret` (till exempel i en länkad Amazon S3-tjänst). Den är automatiskt parameterstyrda som en Azure Key Vault hemlighet och hämtas från det konfigurerade nyckel valvet. Du kan också Parameterisera själva nyckel valvet.

#### <a name="datasets"></a>Datauppsättningar

* Även om typ specifik anpassning är tillgänglig för data uppsättningar kan du ange konfiguration utan att uttryckligen ha en konfiguration på \*nivå. I föregående exempel är alla data uppsättnings egenskaper under `typeProperties` parameterstyrda.

### <a name="default-parameterization-template"></a>Standard Parameterisering-mall

Följande är den aktuella standard Parameterisering-mallen. Om du bara behöver lägga till några få parametrar kan det vara en bra idé att redigera mallen direkt eftersom du inte förlorar den befintliga Parameterisering-strukturen.

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/dataflows": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }

        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
                    "poolName": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "aadResourceId": "=",
                    "connectionString": "|:-connectionString:secureString"
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }}
}
```

I följande exempel visas hur du lägger till ett enda värde i standard mal len Parameterisering. Vi vill bara lägga till ett befintligt Azure Databricks interaktiva kluster-ID: t för en länkad Databricks-tjänst till parameter filen. Observera att den här filen är samma som föregående fil, förutom att du kan lägga till `existingClusterId` under fältet egenskaper för `Microsoft.DataFactory/factories/linkedServices`.

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/dataflows": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }
 
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
            "poolName": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "aadResourceId": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "existingClusterId": "-"
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }}
}
```

## <a name="linked-resource-manager-templates"></a>Länkade Resource Manager-mallar

Om du har konfigurerat CI/CD för dina data fabriker kan du överskrida gränserna för Azure Resource Manager mal len när fabriken växer större. Till exempel är en gräns det maximala antalet resurser i en Resource Manager-mall. För att kunna hantera stora fabriker samtidigt som du skapar en fullständig Resource Manager-mall för en fabrik, skapar Data Factory nu länkade Resource Manager-mallar. Med den här funktionen delas hela fabriks nytto lasten upp i flera filer så att du inte begränsas av gränserna.

Om du har konfigurerat git skapas och sparas de länkade mallarna tillsammans med de fullständiga Resource Manager-mallarna i adf_publish grenen i en ny mapp med namnet linkedTemplates:

![Mapp för länkade Resource Manager-mallar](media/continuous-integration-deployment/linked-resource-manager-templates.png)

De länkade Resource Manager-mallarna består vanligt vis av en huvud mal len och en uppsättning underordnade mallar som är länkade till huvud servern. Den överordnade mallen kallas ArmTemplate_master. JSON och underordnade mallar får namnet med mönstret ArmTemplate_0. JSON, ArmTemplate_1. JSON och så vidare. 

Om du vill använda länkade mallar i stället för den fullständiga Resource Manager-mallen uppdaterar du CI/CD-aktiviteten så att den pekar på ArmTemplate_master. json i stället för ArmTemplateForFactory. JSON (fullständig Resource Manager-mall). Resource Manager kräver också att du överför de länkade mallarna till ett lagrings konto så att Azure kan komma åt dem under distributionen. Mer information finns i [distribuera länkade Resource Manager-mallar med VSTS](https://blogs.msdn.microsoft.com/najib/2018/04/22/deploying-linked-arm-templates-with-vsts/).

Kom ihåg att lägga till Data Factory skript i CI/CD-pipeline innan och efter distributions aktiviteten.

Om du inte har git konfigurerat kan du komma åt de länkade mallarna via **export arm-mallen** i listan **arm-mall** .

## <a name="hotfix-production-branch"></a>Produktions gren för snabb korrigeringar

Om du distribuerar en fabrik till produktion och inser att det finns en bugg som måste åtgärdas direkt, men du inte kan distribuera den aktuella samarbets grenen, kan du behöva distribuera en snabb korrigering. Den här metoden kallas snabb korrigerings teknik eller QFE.

1.  I Azure DevOps går du till den version som distribuerades till produktionen. Hitta den senaste incheckning som har distribuerats.

2.  Hämta genomförande-ID: t för samarbets grenen från bekräftelse meddelandet.

3.  Skapa en ny gren för snabb korrigeringar från det genomförandet.

4.  Gå till Azure Data Factory UX och växla till snabb korrigerings grenen.

5.  Åtgärda felet genom att använda Azure Data Factory UX. Testa dina ändringar.

6.  När korrigeringen har verifierats väljer du **Exportera arm-mall** för att hämta mallen för snabb korrigeringar för Resource Manager.

7.  Kontrol lera den här versionen manuellt i adf_publish grenen.

8.  Om du har konfigurerat din versions pipeline så att den automatiskt utlöses baserat på adf_publish incheckningar startar en ny version automatiskt. Annars måste du köa en version manuellt.

9.  Distribuera snabb korrigerings versionen till test-och produktions fabrikerna. Den här versionen innehåller föregående produktions nytto Last plus den korrigering som du gjorde i steg 5.

10. Lägg till ändringarna från snabb korrigeringen till utvecklings grenen så att senare versioner inte tar med samma fel.

## <a name="best-practices-for-cicd"></a>Metod tips för CI/CD

Om du använder git-integrering med din data fabrik och har en CI/CD-pipeline som flyttar dina ändringar från utveckling till test och sedan till produktion, rekommenderar vi följande metod tips:

-   **Git-integrering**. Du behöver bara konfigurera din utvecklings data fabrik med git-integrering. Ändringar av test och produktion distribueras via CI/CD och kräver inte git-integrering.

-   **Data Factory CI/CD-skript**. Innan du utför distributions steget i Resource Manager i CI/CD måste du slutföra vissa åtgärder, t. ex. stoppa och starta om utlösare och rensning. Vi rekommenderar att du använder PowerShell-skript före och efter distributionen. Mer information finns i [Uppdatera aktiva utlösare](#update-active-triggers).

-   **Integrerings körningar och delning**. Integrerings körningar ändras inte ofta och liknar varandra i alla steg i CI/CD. Så Data Factory förväntar dig att du har samma namn och typ av integration runtime i alla stadier av CI/CD. Om du vill dela integrerings körningar i alla faser bör du överväga att använda en ternär fabrik som bara innehåller de delade integrerings körningarna. Du kan använda den här delade fabriken i alla dina miljöer som en länkad integration runtime-typ.

-   **Key Vault**. När du använder länkade tjänster baserat på Azure Key Vault kan du dra nytta av dem ytterligare genom att hålla separata nyckel valv för olika miljöer. Du kan också konfigurera separata behörighets nivåer för varje nyckel valv. Till exempel kanske du inte vill att dina team medlemmar ska ha behörighet till produktions hemligheter. Om du följer den här metoden rekommenderar vi att du behåller samma hemliga namn i alla steg. Om du behåller samma namn behöver du inte ändra dina Resource Manager-mallar i CI/CD-miljöer eftersom det enda som ändras är nyckel valvets namn, vilket är en av parametrarna för Resource Manager-mallen.

## <a name="unsupported-features"></a>Funktioner som inte stöds

- Enligt design tillåter Data Factory inte körsbär-plockning av incheckningar eller selektiv publicering av resurser. Publiceringar tar med alla ändringar som gjorts i data fabriken.

    - Data Factory-entiteter är beroende av varandra. Utlösare är exempelvis beroende av pipeliner och pipeliner beror på data uppsättningar och andra pipeliner. Selektiv publicering av en del av resurser kan leda till oväntade beteenden och fel.
    - Vid sällsynta tillfällen när du behöver selektiv publicering bör du överväga att använda en snabb korrigering. Mer information finns i avsnittet om [snabb korrigeringar för produktions gren](#hotfix-production-branch).

-   Du kan inte publicera från privata grenar.

-   Du kan för närvarande inte vara värd för projekt på Bitbucket.
