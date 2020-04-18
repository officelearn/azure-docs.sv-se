---
title: Kontinuerlig integrering och leverans i Azure Data Factory
description: Lär dig hur du använder kontinuerlig integrering och leverans för att flytta Data Factory-pipelines från en miljö (utveckling, test, produktion) till en annan.
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
ms.openlocfilehash: 3e6612b30dd8ae6716c0f87687e77c5961275ea5
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606572"
---
# <a name="continuous-integration-and-delivery-in-azure-data-factory"></a>Kontinuerlig integrering och leverans i Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>Översikt

Kontinuerlig integrering är praxis att testa varje ändring som görs i din kodbas automatiskt och så tidigt som möjligt.Kontinuerlig leverans följer de tester som sker under kontinuerlig integrering och driver ändringar till ett mellanlagrings- eller produktionssystem.

I Azure Data Factory innebär kontinuerlig integrering och leverans (CI/CD) att datafabrikspipelor flyttas från en miljö (utveckling, test, produktion) till en annan. Du kan använda Data Factory UX-integrering med Azure Resource Manager-mallar för att göra CI/CD.

I Data Factory UX kan du generera en Resource Manager-mall från **listrutan ARM-mall.** När du väljer **Exportera ARM-mall**genererar portalen Resource Manager-mallen för datafabriken och en konfigurationsfil som innehåller alla anslutningssträngar och andra parametrar. Sedan skapar du en konfigurationsfil för varje miljö (utveckling, test, produktion). Mallfilen för huvudresurshanteraren förblir densamma för alla miljöer.

För en nio minuters introduktion till den här funktionen och en demonstration, titta på den här videon:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-integration-and-deployment-using-Azure-Data-Factory/player]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cicd-lifecycle"></a>CI/CD-livscykel

Nedan följer en exempelöversikt över CI/CD-livscykeln i en Azure-datafabrik som är konfigurerad med Azure Repos Git. Mer information om hur du konfigurerar en Git-databas finns [i Källkontroll i Azure Data Factory](source-control.md).

1.  En utvecklingsdatafabrik skapas och konfigureras med Azure Repos Git. Alla utvecklare bör ha behörighet att skapa Data Factory-resurser som pipelines och datauppsättningar.

1.  När utvecklarna gör ändringar i sina funktionsgrenar felsöker de sina pipeline-körningar med sina senaste ändringar. Mer information om hur du felsöker en pipeline-körning finns i [Iterativ utveckling och felsökning med Azure Data Factory](iterative-development-debugging.md).

1.  När utvecklarna är nöjda med sina ändringar skapar de en pull-begäran från sin funktionsgren till huvud- eller samarbetsgrenen för att få sina ändringar granskade av peer-datorer.

1.  När en pull-begäran har godkänts och ändringar slås samman i huvudgrenen kan ändringarna publiceras i utvecklingsfabriken.

1.  När teamet är redo att distribuera ändringarna till testfabriken och sedan till produktionsfabriken exporterar teamet resurshanteraren-mallen från huvudgrenen.

1.  Mallen exporterad Resurshanteraren distribueras med olika parameterfiler till testfabriken och produktionsfabriken.

## <a name="create-a-resource-manager-template-for-each-environment"></a>Skapa en Resource Manager-mall för varje miljö

1. I listan **ARM-mall** väljer du **Exportera ARM-mall** för att exportera resurshanterarens mall för datafabriken i utvecklingsmiljön.

   ![Exportera en Resource Manager-mall](media/continuous-integration-deployment/continuous-integration-image1.png)

1. I test- och produktionsdatafabrikerna väljer du **Importera ARM-mall**. Den här åtgärden tar dig till Azure-portalen, där du kan importera den exporterade mallen. Välj **Skapa en egen mall i redigeraren** för att öppna Mallredigeraren för Resurshanteraren.

   ![Skapa din egen mall](media/continuous-integration-deployment/custom-deployment-build-your-own-template.png) 

1. Välj **Läs in fil**och välj sedan den genererade Resource Manager-mallen. Det här är **filen arm_template.json** som finns i zip-filen som exporteras i steg 1.

   ![Redigera mall](media/continuous-integration-deployment/custom-deployment-edit-template.png)

1. I inställningsavsnittet anger du konfigurationsvärdena, till exempel länkade tjänstautentiseringsuppgifter. När du är klar väljer du **Inköp** för att distribuera Resource Manager-mallen.

   ![Avsnittet Inställningar](media/continuous-integration-deployment/continuous-integration-image5.png)

### <a name="connection-strings"></a>Anslutningssträngar

Information om hur du konfigurerar anslutningssträngar finns i kopplingens artikel. För Azure SQL Database finns till exempel [Kopiera data till eller från Azure SQL Database med hjälp av Azure Data Factory](connector-azure-sql-database.md). Om du vill verifiera en anslutningssträng kan du öppna kodvyn för resursen i Data Factory UX. I kodvyn tas lösenords- eller kontonyckeldelen av anslutningssträngen bort. Om du vill öppna kodvyn markerar du ikonen som är markerad här:

![Öppna kodvyn för att se anslutningssträngen](media/continuous-integration-deployment/continuous-integration-codeview.png)

## <a name="automate-continuous-integration-by-using-azure-pipelines-releases"></a>Automatisera kontinuerlig integrering med hjälp av Azure Pipelines-versioner

Nedan följer en guide för att konfigurera en Azure Pipelines-version, som automatiserar distributionen av en datafabrik till flera miljöer.

![Diagram över kontinuerlig integrering med Azure Pipelines](media/continuous-integration-deployment/continuous-integration-image12.png)

### <a name="requirements"></a>Krav

-   En Azure-prenumeration som är länkad till Visual Studio Team Foundation Server eller Azure Repos som använder slutpunkten för [Azure Resource Manager-tjänsten](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-resource-manager).

-   En datafabrik som konfigurerats med Azure Repos Git-integrering.

-   Ett [Azure-nyckelvalv](https://azure.microsoft.com/services/key-vault/) som innehåller hemligheterna för varje miljö.

### <a name="set-up-an-azure-pipelines-release"></a>Konfigurera en Azure Pipelines-version

1.  Öppna projektet som konfigureras med datafabriken i [Azure DevOps.](https://dev.azure.com/)

1.  Till vänster på sidan väljer du **Pipelines**och väljer sedan **Releases**.

    ![Välj pipelines, versioner](media/continuous-integration-deployment/continuous-integration-image6.png)

1.  Välj **Ny pipeline**eller, om du har befintliga pipelines, välj **Ny** och sedan **Ny versionspipeline**.

1.  Välj mallen **Tomt jobb.**

    ![Välj Tomt jobb](media/continuous-integration-deployment/continuous-integration-image13.png)

1.  Ange namnet på miljön i rutan **Scennamn.**

1.  Välj **Lägg till artefakt**och välj sedan den databas som konfigurerats med datafabriken. Välj **adf_publish** för **standardgrenen**. För **standardversionen**väljer du **Senaste från standardgrenen**.

    ![Lägg till en artefakt](media/continuous-integration-deployment/continuous-integration-image7.png)

1.  Lägga till en distributionsaktivitet för Azure Resource Manager:

    a.  Välj **Visa fasaktiviteter**i scenvyn .

    ![Scenvy](media/continuous-integration-deployment/continuous-integration-image14.png)

    b.  Skapa en ny uppgift. Sök efter **Azure Resource Group Deployment**och välj sedan Lägg **till**.

    c.  I distributionsaktiviteten väljer du prenumeration, resursgrupp och plats för måldatafabriken. Ange autentiseringsuppgifter om det behövs.

    d.  Välj Skapa eller **uppdatera resursgrupp** **i** åtgärdslistan .

    e.  Välj ellipsknappen (**...**) bredvid rutan **Mall.** Bläddra efter azure Resource Manager-mallen som du har skapat med hjälp av **Importera ARM-mall** i [mallen Skapa en Resurshanteraren för varje miljöavsnitt](continuous-integration-deployment.md#create-a-resource-manager-template-for-each-environment) i den här artikeln. Leta efter den <FactoryName> här filen i mappen för adf_publish grenen.

    f.  Välj **...** bredvid rutan **Mallparametrar** för att välja parameterfilen. Vilken fil du väljer beror på om du har skapat en kopia eller använder standardfilen ARMTemplateParametersForFactory.json.

    g.  Välj **...** bredvid rutan **Åsidosätt mallparametrar** och ange informationen för måldatafabriken. För autentiseringsuppgifter som kommer från Azure Key Vault anger du hemlighetens namn mellan dubbla citattecken. Om hemlighetens namn till exempel är cred1 anger du **"$(cred1)"** för det här värdet.

    h. Välj **Inkrementellt** för **distributionsläget**.

    > [!WARNING]
    > Om du väljer **Slutför** för **distributionsläget**kan befintliga resurser tas bort, inklusive alla resurser i målresursgruppen som inte har definierats i resource manager-mallen.

    ![Distribution av datafabriksprod](media/continuous-integration-deployment/continuous-integration-image9.png)

1.  Spara releasepipelinen.

1. Om du vill utlösa en version väljer du **Skapa version**.

   ![Välj Skapa version](media/continuous-integration-deployment/continuous-integration-image10.png)

> [!IMPORTANT]
> I CI/CD-scenarier måste iR-typen (Integration runtime) i olika miljöer vara densamma. Om du till exempel har en självvärd i utvecklingsmiljön måste samma IR också vara av typen självvärd i andra miljöer, till exempel test och produktion. Om du delar integrationskörningar i flera steg måste du konfigurera integrationskörningarna som länkade självvärderade i alla miljöer, till exempel utveckling, test och produktion.

### <a name="get-secrets-from-azure-key-vault"></a>Få hemligheter från Azure Key Vault

Om du har hemligheter att skicka i en Azure Resource Manager-mall rekommenderar vi att du använder Azure Key Vault med Azure Pipelines-versionen.

Det finns två sätt att hantera hemligheter:

1.  Lägg till hemligheterna i parameterfilen. Mer information finns i [Använda Azure Key Vault för att skicka säkert parametervärde under distributionen](../azure-resource-manager/templates/key-vault-parameter.md).

    Skapa en kopia av parameterfilen som överförs till publiceringsgrenen. Ange värden för de parametrar som du vill hämta från Key Vault med det här formatet:

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

    När du använder den här metoden hämtas hemligheten automatiskt från nyckelvalvet.

    Parameterfilen måste också finnas i publiceringsgrenen.

1. Lägg till en [Azure Key Vault-aktivitet](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-key-vault) före distributionsaktiviteten för Azure Resource Manager som beskrivs i föregående avsnitt:

    1.  Skapa en ny uppgift på fliken **Uppgifter.** Sök efter **Azure Key Vault** och lägg till det.

    1.  I key vault-uppgiften väljer du den prenumeration där du skapade nyckelvalvet. Ange autentiseringsuppgifter om det behövs och välj sedan nyckelvalvet.

    ![Lägga till en key vault-uppgift](media/continuous-integration-deployment/continuous-integration-image8.png)

#### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>Bevilja behörigheter till Azure Pipelines-agenten

Azure Key Vault-aktiviteten kan misslyckas med ett åtkomst nekat fel om rätt behörigheter inte har angetts. Hämta loggarna för versionen och leta reda på PS1-filen som innehåller kommandot för att ge behörighet till Azure Pipelines-agenten. Du kan köra kommandot direkt. Du kan också kopiera huvud-ID:t från filen och lägga till åtkomstprincipen manuellt i Azure-portalen. `Get`och `List` är de minsta behörigheter som krävs.

### <a name="update-active-triggers"></a>Uppdatera aktiva utlösare

Distributionen kan misslyckas om du försöker uppdatera aktiva utlösare. Om du vill uppdatera aktiva utlösare måste du stoppa dem manuellt och sedan starta om dem efter distributionen. Du kan göra detta med hjälp av en Azure PowerShell-uppgift:

1.  Lägg till en **Azure PowerShell-uppgift** på fliken **Uppgifter** i versionen. Välj uppgiftsversion 4.*. 

1.  Välj den prenumeration som fabriken är i.

1.  Välj **Script File Path** som skripttyp. Detta kräver att du sparar PowerShell-skriptet i databasen. Följande PowerShell-skript kan användas för att stoppa utlösare:

    ```powershell
    $triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

Du kan slutföra liknande `Start-AzDataFactoryV2Trigger` steg (med funktionen) för att starta om utlösarna efter distributionen.

### <a name="sample-pre--and-post-deployment-script"></a>Exempel på skript före och efter distribution

Följande exempelskript kan användas för att stoppa utlösare innan distributionen och starta om dem efteråt. Skriptet innehåller också kod för att ta bort resurser som har tagits bort. Spara skriptet i en Azure DevOps git-databas och referera till det via en Azure PowerShell-uppgift med version 4.*.

När du kör ett skript före distribution måste du ange en variant av följande parametrar i fältet **Skriptargument.**

`-armTemplate "$(System.DefaultWorkingDirectory)/<your-arm-template-location>" -ResourceGroupName <your-resource-group-name> -DataFactoryName <your-data-factory-name>  -predeployment $true -deleteDeployment $false`


När du kör ett skript efter distributionen måste du ange en variant av följande parametrar i fältet **Skriptargument.**

`-armTemplate "$(System.DefaultWorkingDirectory)/<your-arm-template-location>" -ResourceGroupName <your-resource-group-name> -DataFactoryName <your-data-factory-name>  -predeployment $false -deleteDeployment $true`

![Azure PowerShell-uppgift](media/continuous-integration-deployment/continuous-integration-image11.png)

Här är skriptet som kan användas för före och efter distributionen. Den står för borttagna resurser och resursreferenser.

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
        Remove-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force
    $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_
    while ($status.Status -ne "Disabled"){
            Start-Sleep -s 15
            $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_
    }
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
        Add-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force
    $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_
    while ($status.Status -ne "Enabled"){
            Start-Sleep -s 15
            $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_
    }
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
```

## <a name="use-custom-parameters-with-the-resource-manager-template"></a>Använda anpassade parametrar med Resource Manager-mallen

Om du är i GIT-läge kan du åsidosätta standardegenskaperna i Resurshanterarens mall för att ange egenskaper som är parameteriserade i mallen och egenskaper som är hårdkodade. Du kanske vill åsidosätta standardparametimeringsmallen i följande scenarier:

* Du använder automatisk CI/CD och vill ändra vissa egenskaper under Resurshanterarens distribution, men egenskaperna är inte parameteriserade som standard.
* Fabriken är så stor att standardmallen För Resurshanteraren är ogiltig eftersom den har fler än de högsta tillåtna parametrarna (256).

Under dessa förhållanden, för att åsidosätta standardparameteriseringsmallen, skapa en fil med namnet **arm-template-parameters-definition.json** i mappen som anges som rotmapp för datafabrikens git-integrering. Du måste använda det exakta filnamnet. Data Factory läser den här filen från vilken gren du för närvarande befinner dig i Azure Data Factory-portalen, inte bara från samarbetsgrenen. Du kan skapa eller redigera filen från en privat gren, där du kan testa ändringarna genom att välja **Exportera ARM-mall** i användargränssnittet. Du kan sedan sammanfoga filen till samarbetsgrenen. Om ingen fil hittas används standardmallen.

> [!NOTE]
> En anpassad parameteriseringsmall ändrar inte arm-mallparametergränsen på 256. Det låter dig välja och minska antalet parameteriserade egenskaper.

### <a name="syntax-of-a-custom-parameters-file"></a>Syntax för en anpassad parameterfil

Följande är några riktlinjer att följa när du skapar den anpassade parameterfilen. Filen består av ett avsnitt för varje entitetstyp: utlösare, pipeline, länkad tjänst, datauppsättning, integrationskörning och så vidare.
* Ange egenskapssökvägen under relevant entitetstyp.
* Ange ett egenskapsnamn så att `*` det anges att du vill parameterisera alla egenskaper under det (bara ner till den första nivån, inte rekursivt). Du kan också ange undantag från den här konfigurationen.
* Om du anger värdet för en egenskap som en sträng betyder det att du vill parametriera egenskapen. Använd formatet `<action>:<name>:<stype>`.
   *  `<action>` kan vara ett av följande tecken:
      * `=` innebär att behålla det aktuella värdet som standardvärde för parametern.
      * `-` betyder att inte behålla standardvärdet för parametern.
      * `|` är ett specialfall för hemligheter från Azure Key Vault för anslutningssträngar eller nycklar.
   * `<name>` är namnet på parametern. Om den är tom får den namnet på egenskapen. Om värdet börjar `-` med ett tecken förkortas namnet. Till exempel `AzureStorage1_properties_typeProperties_connectionString` skulle förkortas `AzureStorage1_connectionString`till .
   * `<stype>` är typen av parameter. Om `<stype>` är tom är `string`standardtypen . Värden som `string` `bool`stöds: `object`, `securestring`, `number`, och .
* Om du anger en matris i definitionsfilen betyder det att den matchande egenskapen i mallen är en matris. Data Factory itererar igenom alla objekt i matrisen med hjälp av den definition som anges i matrisens integrationskörningsobjekt. Det andra objektet, en sträng, blir namnet på egenskapen, som används som namn för parametern för varje iteration.
* En definition kan inte vara specifik för en resursinstans. Alla definitioner gäller för alla resurser av den typen.
* Som standard parametriiseras alla säkra strängar, till exempel Key Vault-hemligheter, och säkra strängar, till exempel anslutningssträngar, nycklar och token.
 
### <a name="sample-parameterization-template"></a>Exempel på parameteriseringsmall

Här är ett exempel på hur en parameteriseringsmall kan se ut:

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
Här är en förklaring av hur den föregående mallen är konstruerad, uppdelad efter resurstyp.

#### <a name="pipelines"></a>Pipelines
    
* Alla egenskaper i `activities/typeProperties/waitTimeInSeconds` sökvägen är parameteriserade. Alla aktiviteter i en pipeline som har `waitTimeInSeconds` en egenskap `Wait` på kodnivå med namnet (till exempel aktiviteten) parameteriseras som ett tal med ett standardnamn. Men det har inget standardvärde i mallen Resurshanteraren. Det blir en obligatorisk indata under Resurshanteraren-distributionen.
* På samma sätt `headers` parametrieras en `Web` egenskap som anropas `object` (till exempel i en aktivitet) med typen (JObject). Den har ett standardvärde, vilket är samma värde som källfabriken.

#### <a name="integrationruntimes"></a>IntegrationRuntimes

* Alla egenskaper under `typeProperties` sökvägen parametriseras med sina respektive standardvärden. Det finns till exempel `IntegrationRuntimes` två egenskaper `computeProperties` `ssisProperties`under typegenskaper: och . Båda egenskapstyperna skapas med sina respektive standardvärden och standardtyper (Objekt).

#### <a name="triggers"></a>Utlösare

* Under `typeProperties`parametrin är två egenskaper parameteriserade. Den första `maxConcurrency`är , som har angetts ha ett`string`standardvärde och är av typen . Den har standardparameternamnet `<entityName>_properties_typeProperties_maxConcurrency`.
* Egenskapen `recurrence` är också parameteriserad. Under den anges alla egenskaper på den nivån som parameteriserades som strängar, med standardvärden och parameternamn. Ett undantag `interval` är egenskapen, som `number`är parameteriserad som typ . Parameternamnet är suffixed `<entityName>_properties_typeProperties_recurrence_triggerSuffix`med . På samma `freq` sätt är egenskapen en sträng och är parameteriserad som en sträng. Egenskapen `freq` är dock parameteriserad utan standardvärde. Namnet förkortas och suffixeras. Till exempel `<entityName>_freq`.

#### <a name="linkedservices"></a>LinkedServices

* Länkade tjänster är unika. Eftersom länkade tjänster och datauppsättningar har ett brett spektrum av typer kan du tillhandahålla typspecifik anpassning. I det här exemplet används `AzureDataLakeStore`en specifik mall för alla länkade tjänster av typen. För alla andra `*`(via), en annan mall kommer att tillämpas.
* Egenskapen `connectionString` kommer att parameteriseras som ett `securestring` värde. Det kommer inte att ha ett standardvärde. Det kommer att ha en förkortad parameter namn `connectionString`som är suffixed med .
* Fastigheten `secretAccessKey` råkar vara `AzureKeyVaultSecret` en (till exempel i en Amazon S3 länkad tjänst). Den är automatiskt parameteriserad som en Azure Key Vault hemlighet och hämtas från den konfigurerade nyckel valv. Du kan också parametriera själva nyckelvalvet.

#### <a name="datasets"></a>Datauppsättningar

* Även om typspecifik anpassning är tillgänglig för datauppsättningar kan \*du tillhandahålla konfiguration utan att uttryckligen ha en konfiguration på -nivå. I föregående exempel parametrieras alla `typeProperties` datauppsättningsegenskaper under.

### <a name="default-parameterization-template"></a>Standardparametimeringsmall

Följande är den aktuella standardparametriiseringsmallen. Om du bara behöver lägga till några parametrar kan det vara en bra idé att redigera mallen direkt eftersom du inte förlorar den befintliga parameterstrukturn.

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

I följande exempel visas hur du lägger till ett enda värde i standardparametimeringsmallen. Vi vill bara lägga till ett befintligt Azure Databricks interaktivt kluster-ID för en Databricks-länkad tjänst till parameterfilen. Observera att den här filen är samma som `existingClusterId` den föregående filen `Microsoft.DataFactory/factories/linkedServices`förutom tillägg av under egenskapsfältet i .

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

## <a name="linked-resource-manager-templates"></a>Mallar för länkade resurshanteraren

Om du har konfigurerat CI/CD för dina datafabriker kan du överskrida mallgränserna för Azure Resource Manager när din fabrik blir större. En gräns är till exempel det maximala antalet resurser i en Resource Manager-mall. För att rymma stora fabriker samtidigt som den fullständiga Resource Manager-mallen för en fabrik genereras, data factory, nu länkade Resource Manager-mallar. Med den här funktionen är hela fabriksnyttolasten uppdelad i flera filer så att du inte begränsas av gränserna.

Om du har konfigurerat Git genereras och sparas de länkade mallarna tillsammans med de fullständiga Resource Manager-mallarna i adf_publish grenen i en ny mapp som kallas linkedTemplates:

![Mapp med länkade resurshanteraren-mallar](media/continuous-integration-deployment/linked-resource-manager-templates.png)

Mallarna för länkade Resurshanteraren består vanligtvis av en huvudmall och en uppsättning underordnade mallar som är länkade till bakgrunden. Den överordnade mallen kallas ArmTemplate_master.json och underordnade mallar namnges med mönstret ArmTemplate_0.json, ArmTemplate_1.json och så vidare. 

Om du vill använda länkade mallar i stället för den fullständiga Resource Manager-mallen uppdaterar du CI/CD-aktiviteten så att den pekar på ArmTemplate_master.json i stället för ArmTemplateForFactory.json (den fullständiga Resource Manager-mallen). Resource Manager kräver också att du laddar upp länkade mallar till ett lagringskonto så att Azure kan komma åt dem under distributionen. Mer information finns i [Distribuera länkade Resource Manager-mallar med VSTS](https://blogs.msdn.microsoft.com/najib/2018/04/22/deploying-linked-arm-templates-with-vsts/).

Kom ihåg att lägga till Data Factory-skripten i CI/CD-pipelinen före och efter distributionsuppgiften.

Om du inte har konfigurerat Git kan du komma åt de länkade mallarna via **Export ARM-mallen** i **ARM-malllistan.**

## <a name="hotfix-production-branch"></a>Produktionsgren för snabbkorrigering

Om du distribuerar en fabrik till produktion och inser att det finns ett fel som måste åtgärdas direkt, men du inte kan distribuera den aktuella samarbetsgrenen, kan du behöva distribuera en snabbkorrigering. Den här metoden kallas quick-fix engineering eller QFE.

1.    Gå till versionen som distribuerades till produktion i Azure DevOps. Hitta det senaste åtagandet som har distribuerats.

2.    Från meddelandet commit får du commit-ID:et för samarbetsgrenen.

3.    Skapa en ny snabbkorrigeringsgren från den commit.

4.    Gå till Azure Data Factory UX och växla till snabbkorrigeringsgrenen.

5.    Lös felet genom att använda Azure Data Factory UX. Testa dina ändringar.

6.    När korrigeringen har verifierats väljer du **Exportera ARM-mall** för att hämta snabbkorrigeringsresurshanterarens mall.

7.    Kontrollera den här versionen manuellt i adf_publish grenen.

8.    Om du har konfigurerat versionspipelinen för att automatiskt utlösa baserat på adf_publish incheckningar startar en ny version automatiskt. Annars köar du manuellt en version.

9.    Distribuera snabbkorrigeringsversionen till test- och produktionsfabrikerna. Den här versionen innehåller den tidigare produktionsnyttolasten plus den korrigering som du gjorde i steg 5.

10.    Lägg till ändringarna från snabbkorrigeringen i utvecklingsgrenen så att senare versioner inte innehåller samma fel.

## <a name="best-practices-for-cicd"></a>Metodtips för CI/CD

Om du använder Git-integrering med din datafabrik och har en CI/CD-pipeline som flyttar dina ändringar från utveckling till test och sedan till produktion rekommenderar vi följande metodtips:

-   **Git integration**. Du behöver bara konfigurera din utvecklingsdatafabrik med Git-integrering. Ändringar i test och produktion distribueras via CI/CD och behöver inte Git-integrering.

-   **Ci/CD-skript för datafabrik.** Innan resurshanterarens distributionssteg i CI/CD måste du slutföra vissa uppgifter, till exempel stoppa och starta om utlösare och utföra rensning. Vi rekommenderar att du använder PowerShell-skript före och efter distributionen. Mer information finns i [Uppdatera aktiva utlösare](#update-active-triggers).

-   **Integrationskörningar och delning**. Integrationskörningtider ändras inte ofta och är likartade i alla steg i DIN CI/CD. Så Data Factory förväntar sig att du har samma namn och typ av integration runtime i alla stadier av CI / CD. Om du vill dela integrationskörningar i alla steg bör du överväga att använda en ternära fabrik bara för att innehålla de delade integrationskörningarna. Du kan använda den här delade fabriken i alla miljöer som en länkad integrationskörningstyp.

-   **Nyckelvalvet**. När du använder länkade tjänster baserat på Azure Key Vault kan du dra nytta av dem ytterligare genom att hålla separata nyckelvalv för olika miljöer. Du kan också konfigurera separata behörighetsnivåer för varje nyckelvalv. Du kanske till exempel inte vill att gruppmedlemmarna ska ha behörighet till produktionshemligheter. Om du följer den här metoden rekommenderar vi att du behåller samma hemliga namn i alla steg. Om du behåller samma namn behöver du inte ändra Resource Manager-mallarna i CI/CD-miljöer eftersom det enda som ändras är nyckelvalvets namn, som är en av Resource Manager-mallparametrarna.

## <a name="unsupported-features"></a>Funktioner som inte stöds

- Data Factory tillåter inte att resurser plockas russinen eller selektiv publicering av resurser. Publiceringar kommer att innehålla alla ändringar som gjorts i datafabriken.

    - Data fabrik entiteter beror på varandra. Utlösare beror till exempel på pipelines och pipelines är beroende av datauppsättningar och andra pipelines. Selektiv publicering av en delmängd av resurser kan leda till oväntade beteenden och fel.
    - I sällsynta fall när du behöver selektiv publicering bör du överväga att använda en snabbkorrigering. Mer information finns i [Produktionsgren för snabbkorrigering](#hotfix-production-branch).

-   Du kan inte publicera från privata grenar.

-   Du kan för närvarande inte vara värd för projekt på Bitbucket.
