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
ms.date: 08/14/2019
ms.openlocfilehash: 7c5c1e91e97087bf28b03629659e5194f67c22b3
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680033"
---
# <a name="continuous-integration-and-delivery-cicd-in-azure-data-factory"></a>Kontinuerlig integrering och leverans (CI/CD) i Azure Data Factory

## <a name="overview"></a>Översikt

Kontinuerlig integrering är en metod för att testa varje ändring som görs i kodbasen automatiskt och så tidigt som möjligt. Kontinuerlig leverans följer testerna som sker under kontinuerlig integrering och skickar ändringar till ett mellanlagrings-eller produktions system.

I Azure Data Factory innebär kontinuerlig integrering & leverans att flytta Data Factory pipelines från en miljö (utveckling, testning, produktion) till en annan. Om du vill utföra kontinuerlig integration & leverans kan du använda Data Factory UX-integrering med Azure Resource Manager mallar. Data Factory UX kan generera en Resource Manager-mall från List rutan **arm-mall** . När du väljer **Exportera arm-mall**genererar portalen Resource Manager-mallen för data fabriken och en konfigurations fil som innehåller alla dina anslutnings strängar och andra parametrar. Sedan har du skapat en konfigurations fil för varje miljö (utveckling, test, produktion). Den huvudsakliga Resource Manager-mallfilen är densamma för alla miljöer.

För en nio minuters introduktion och demonstration av den här funktionen, se följande videoklipp:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-integration-and-deployment-using-Azure-Data-Factory/player]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="continuous-integration-lifecycle"></a>Livs cykel för kontinuerlig integrering

Nedan visas ett exempel på en översikt över kontinuerlig integrering och leverans livs cykel i en Azure-datafabrik som kon figurer ATS med Azure databaser git. Mer information om hur du konfigurerar en git-lagringsplats finns i [käll kontroll i Azure Data Factory](source-control.md).

1.  En utvecklings data fabrik skapas och konfigureras med Azure databaser git där alla utvecklare har behörighet att redigera Data Factory resurser som pipelines och data uppsättningar.

1.  När utvecklare gör ändringar i sin funktions gren, så att de kan felsöka sina pipeliner med de senaste ändringarna. Mer information om hur du felsöker en pipeline-körning finns i [iterativ utveckling och fel sökning med Azure Data Factory](iterative-development-debugging.md).

1.  När utvecklarna är nöjd med sina ändringar, skapar de en pull-begäran från sina funktions grenar till huvud-eller samarbets grenen för att få sina ändringar granskade av peer-datorer.

1.  När pull-begäran har godkänts och ändringar slås samman i huvud grenen kan de publicera till utvecklings fabriken.

1.  När teamet är redo att distribuera ändringarna till test fabriken och sedan till produktions fabriken, exporterar de Resource Manager-mallen från huvud grenen.

1.  Den exporterade Resource Manager-mallen distribueras med olika parameterstyrda filer till test fabriken och produktions fabriken.

## <a name="create-a-resource-manager-template-for-each-environment"></a>Skapa en Resource Manager-mall för varje miljö

I list rutan **arm-mall** väljer du **Exportera arm** -mall för att exportera Resource Manager-mallen för din data fabrik i utvecklings miljön.

![](media/continuous-integration-deployment/continuous-integration-image1.png)

I test-och produktions data fabrikerna väljer du **Importera arm-mall**. Den här åtgärden tar dig till Azure Portal, där du kan importera den exporterade mallen. Välj **skapa en egen mall i redigeraren** för att öppna Principeditorn i Resource Manager.

![](media/continuous-integration-deployment/continuous-integration-image3.png) 

Klicka på **Läs in fil** och välj den genererade Resource Manager-mallen.

![](media/continuous-integration-deployment/continuous-integration-image4.png)

I fönstret inställningar anger du konfigurations värden, till exempel autentiseringsuppgifter för länkad tjänst. När du är klar klickar du på **köp** för att distribuera Resource Manager-mallen.

![](media/continuous-integration-deployment/continuous-integration-image5.png)

### <a name="connection-strings"></a>Anslutningssträngar

Information om hur du konfigurerar anslutnings strängar finns i varje kopplings artikel. Till exempel, för Azure SQL Database, se [Kopiera data till eller från Azure SQL Database med Azure Data Factory](connector-azure-sql-database.md). Om du vill verifiera en anslutnings sträng kan du öppna vyn kod för resursen i Data Factory UX. I kodvyn tas lösen ordet eller konto nyckel delen av anslutnings strängen bort. Öppna kodvyn genom att välja ikonen som är markerad i följande skärm bild.

![Öppna kodvyn för att se anslutnings strängen](media/continuous-integration-deployment/continuous-integration-codeview.png)

## <a name="automate-continuous-integration-with-azure-pipelines-releases"></a>Automatisera kontinuerlig integrering med Azure pipelines-versioner

Nedan finns en guide för att konfigurera en Azure pipelines-lansering som automatiserar distributionen av en data fabrik till flera miljöer.

![Diagram över kontinuerlig integrering med Azure-pipelines](media/continuous-integration-deployment/continuous-integration-image12.png)

### <a name="requirements"></a>Krav

-   En Azure-prenumeration som är länkad till Team Foundation Server eller Azure-databaser med hjälp av [Azure Resource Manager tjänstens slut punkt](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-rm).

-   En Data Factory som kon figurer ATS med Azure databaser git-integrering.

-   En [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) som innehåller hemligheterna för varje miljö.

### <a name="set-up-an-azure-pipelines-release"></a>Konfigurera en version av Azure pipelines

1.  Öppna det projekt som kon figurer ATS med din Data Factory i [användar upplevelsen för Azure-DevOps](https://dev.azure.com/).

1.  Klicka på **pipelines** på vänster sida av sidan och välj sedan **versioner**.

    ![](media/continuous-integration-deployment/continuous-integration-image6.png)

1.  Välj **ny pipeline** eller om du har befintliga pipeliner, **nya**och **nya lanserings pipeliner**.

1.  Välj den **tomma jobb** mal len.

    ![](media/continuous-integration-deployment/continuous-integration-image13.png)

1.  I fältet **scen namn** anger du namnet på din miljö.

1.  Välj **Lägg till en artefakt**och välj samma databas som kon figurer ats med Data Factory. Välj `adf_publish` som standard gren med den senaste standard versionen.

    ![](media/continuous-integration-deployment/continuous-integration-image7.png)

1.  Lägg till en Azure Resource Manager distributions uppgift:

    a.  I vyn fas klickar du på länken **Visa steg uppgifter** .

    ![](media/continuous-integration-deployment/continuous-integration-image14.png)

    b.  Skapa en ny uppgift. Sök efter **Azure Resource Group-distribution**och klicka på **Lägg till**.

    c.  I distributions aktiviteten väljer du prenumeration, resurs grupp och plats för mål Data Factory och anger autentiseringsuppgifter om det behövs.

    d.  I list rutan Åtgärd väljer du **skapa eller uppdatera resurs grupp**.

    e.  Välj **...** i fältet **mall** . Bläddra till Azure Resource Manager mall skapa via steget **Importera arm-mall** i [skapa en Resource Manager-mall för varje miljö](continuous-integration-deployment.md#create-a-resource-manager-template-for-each-environment). Leta efter den här filen i mappen `<FactoryName>` i grenen `adf_publish`.

    f.  Välj **...** i **fältet mallparametrar.** för att välja parameter filen. Välj rätt fil beroende på om du har skapat en kopia eller om du använder standard filen *ARMTemplateParametersForFactory. JSON*.

    g.  Välj **...** bredvid fältet **Åsidosätt mallparametrar** och fyll i informationen för mål Data Factory. För autentiseringsuppgifter som kommer från Key Vault anger du det hemliga namnet mellan dubbla citat tecken. Om hemlighetens namn till exempel är `cred1`anger du `"$(cred1)"`för värdet.

    ![](media/continuous-integration-deployment/continuous-integration-image9.png)

    h. Välj **stegvis** distributions läge.

    > [!WARNING]
    > Om du väljer **fullständigt** distributions läge kan befintliga resurser tas bort, inklusive alla resurser i mål resurs gruppen som inte har definierats i Resource Manager-mallen.

1.  Spara versions pipelinen.

1. Om du vill utlösa en version klickar du på **Skapa version**

![](media/continuous-integration-deployment/continuous-integration-image10.png)

### <a name="get-secrets-from-azure-key-vault"></a>Hämta hemligheter från Azure Key Vault

Om du har hemligheter att skicka i en Azure Resource Manager-mall rekommenderar vi att du använder Azure Key Vault med Azure pipelines-versionen.

Det finns två sätt att hantera hemligheter:

1.  Lägg till filen hemligheter till Parameters. Mer information finns i [använda Azure Key Vault för att skicka ett säkert parameter värde under distributionen](../azure-resource-manager/resource-manager-keyvault-parameter.md).

    -   Skapa en kopia av parameter filen som överförs till publicerings grenen och ange värden för de parametrar som du vill hämta från Key Vault med följande format:

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

    -   När du använder den här metoden hämtas hemligheten automatiskt från nyckel valvet.

    -   Parameter filen måste också finnas i publicerings grenen.

1.  Lägg till en [Azure Key Vault aktivitet](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-key-vault) före Azure Resource Manager distributionen som beskrivs i föregående avsnitt:

    -   Välj fliken **aktiviteter** , skapa en ny uppgift, sök efter **Azure Key Vault** och Lägg till den.

    -   I Key Vault aktivitet väljer du den prenumeration där du skapade nyckel valvet, anger autentiseringsuppgifter om det behövs och väljer sedan nyckel valvet.

    ![](media/continuous-integration-deployment/continuous-integration-image8.png)

#### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>Bevilja behörighet till Azure pipelines-agenten

Azure Key Vault aktiviteten kan Miss lyckas med ett nekat åtkomst fel om rätt behörigheter saknas. Hämta loggarna för versionen och leta upp filen `.ps1` med kommandot för att ge behörighet till Azure pipelines-agenten. Du kan köra kommandot direkt, eller så kan du kopiera ägar-ID: t från filen och lägga till åtkomst principen manuellt i Azure Portal. **Hämta** och **lista** är de lägsta behörigheter som krävs.

### <a name="update-active-triggers"></a>Uppdatera aktiva utlösare

Distributionen kan inte utföras om du försöker uppdatera aktiva utlösare. Om du vill uppdatera aktiva utlösare måste du stoppa dem manuellt och starta dem efter distributionen. Du kan göra detta via en Azure PowerShell-uppgift.

1.  Lägg till en **Azure PowerShell** -uppgift på fliken aktiviteter i versionen.

1.  Välj **Azure Resource Manager** som Anslutnings typ och välj din prenumeration.

1.  Välj **infogat skript** som skript typ och ange sedan din kod. I följande exempel stoppas utlösarna:

    ```powershell
    $triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

    ![](media/continuous-integration-deployment/continuous-integration-image11.png)

Du kan följa liknande steg (med funktionen `Start-AzDataFactoryV2Trigger`) för att starta om utlösarna efter distributionen.

> [!IMPORTANT]
> I kontinuerlig integrering och distributions scenarier måste Integration Runtime typen i olika miljöer vara densamma. Om du till exempel har en *egen värd* integration Runtime (IR) i utvecklings miljön, måste samma IR vara av typen *egen värd* i andra miljöer, till exempel test och produktion också. Om du däremot delar integrerings körningar över flera steg, måste du konfigurera integration runtime som *länkad egen värd* i alla miljöer, till exempel utveckling, testning och produktion.

#### <a name="sample-prepostdeployment-script"></a>Exempel på regler-skript

Nedan visas ett exempel skript för att stoppa utlösare innan distribution och omstart av utlösare efteråt. Skriptet innehåller också kod för att ta bort resurser som har tagits bort. Om du vill installera den senaste versionen av Azure PowerShell, se [installera Azure PowerShell på Windows med PowerShellGet](https://docs.microsoft.com/powershell/azure/install-az-ps).

```powershell
param
(
    [parameter(Mandatory = $false)] [String] $rootFolder,
    [parameter(Mandatory = $false)] [String] $armTemplate,
    [parameter(Mandatory = $false)] [String] $ResourceGroupName,
    [parameter(Mandatory = $false)] [String] $DataFactoryName,
    [parameter(Mandatory = $false)] [Bool] $predeployment=$true,
    [parameter(Mandatory = $false)] [Bool] $deleteDeployment=$false
)

$templateJson = Get-Content $armTemplate | ConvertFrom-Json
$resources = $templateJson.resources

#Triggers 
Write-Host "Getting triggers"
$triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
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
    $pipelinesADF = Get-AzDataFactoryV2Pipeline -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $pipelinesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/pipelines" }
    $pipelinesNames = $pipelinesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedpipelines = $pipelinesADF | Where-Object { $pipelinesNames -notcontains $_.Name }
    #datasets
    Write-Host "Getting datasets"
    $datasetsADF = Get-AzDataFactoryV2Dataset -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $datasetsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/datasets" }
    $datasetsNames = $datasetsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataset = $datasetsADF | Where-Object { $datasetsNames -notcontains $_.Name }
    #linkedservices
    Write-Host "Getting linked services"
    $linkedservicesADF = Get-AzDataFactoryV2LinkedService -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
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

    #Start Active triggers - After cleanup efforts
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

Om du under dessa omständigheter vill åsidosätta standard mal len Parameterisering skapar du en fil med namnet *arm-Template-Parameters-definition. JSON* i rot katalogen för lagrings platsen. Fil namnet måste matcha exakt. Data Factory försöker läsa filen från den gren som du för närvarande är ansluten till i Azure Data Factory portalen, inte bara från samarbets grenen. Du kan skapa eller redigera filen från en privat gren, där du kan testa dina ändringar med hjälp av **export arm-mallen** i användar gränssnittet. Sedan kan du slå samman filen till samarbets grenen. Om ingen fil hittas används standard mal len.


### <a name="syntax-of-a-custom-parameters-file"></a>Syntax för en anpassad parameter fil

Här följer några rikt linjer som du kan använda när du skapar filen med anpassade parametrar. Filen består av ett avsnitt för varje entitetstyp: utlösare, pipeline, länkad tjänst, data uppsättning, integration Runtime och så vidare.
* Ange sökvägen till egenskapen under den relevanta entitetstypen.
* När du anger ett egenskaps namn till\*, anger du att du vill Parameterisera alla egenskaper under den (endast till den första nivån, inte rekursivt). Du kan också ange undantag för detta.
* När du anger värdet för en egenskap som en sträng anger du att du vill Parameterisera egenskapen. Använd formatet `<action>:<name>:<stype>`.
   *  `<action>` kan vara något av följande tecken:
      * `=` innebär att det aktuella värdet är standardvärdet för parametern.
      * `-` innebär att inte behålla standardvärdet för parametern.
      * `|` är ett specialfall för hemligheter från Azure Key Vault för anslutnings strängar eller nycklar.
   * `<name>` är namnet på parametern. Om det är tomt tar det med namnet på egenskapen. Om värdet börjar med ett `-`-värde är namnet förkortat. `AzureStorage1_properties_typeProperties_connectionString` skulle till exempel kortas till `AzureStorage1_connectionString`.
   * `<stype>` är typ av parameter. Om `<stype>` är tomt är standard typen `string`. Värden som stöds: `string`, `bool`, `number`, `object` och `securestring`.
* När du anger en matris i definitions filen anger du att den matchande egenskapen i mallen är en matris. Data Factory itererar igenom alla objekt i matrisen med hjälp av definitionen som anges i matrisens Integration Runtime-objekt. Det andra objektet, en sträng, blir namnet på egenskapen, som används som namn för parametern för varje iteration.
* Det går inte att ha en definition som är unik för en resurs instans. Alla definitioner gäller för alla resurser av den typen.
* Som standard är alla säkra strängar, till exempel Key Vault hemligheter och säkra strängar, till exempel anslutnings strängar, nycklar och tokens, parameterstyrda.
 
### <a name="sample-parameterization-template"></a>Exempel på Parameterisering-mall

Nedan visas ett exempel på hur en Parameterisering-mall kan se ut:

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
Nedan visas en förklaring av hur ovanstående mall är konstruerad, uppdelad efter resurs typ.

#### <a name="pipelines"></a>Pipelines
    
* Alla egenskaper i Path-aktiviteterna/typeProperties/waitTimeInSeconds är parameterstyrda. Alla aktiviteter i en pipeline som har en kod nivå egenskap med namnet `waitTimeInSeconds` (till exempel `Wait`-aktivitet) är parameterstyrda som ett tal med ett standard namn. Men det finns inget standardvärde i Resource Manager-mallen. Det är en obligatorisk Indatatyp under distributionen av Resource Manager.
* På samma sätt har en egenskap som kallas `headers` (till exempel i en `Web`-aktivitet) en parameter med typen `object` (JObject). Det har ett standardvärde, vilket är samma värde som i käll fabriken.

#### <a name="integrationruntimes"></a>IntegrationRuntimes

* Alla egenskaper under sökvägen `typeProperties` är parameterstyrda med respektive standardvärden. Det finns till exempel två egenskaper under **IntegrationRuntimes** typ egenskaper: `computeProperties` och `ssisProperties`. Båda egenskaps typerna skapas med deras respektive standardvärden och typer (objekt).

#### <a name="triggers"></a>Utlösare

* Under `typeProperties` är två egenskaper parameterstyrda. Den första är `maxConcurrency`, som har angetts som standardvärde och är av typen`string`. Den har standard parameter namnet `<entityName>_properties_typeProperties_maxConcurrency`.
* Egenskapen `recurrence` är också parametriserad. Under den här nivån anges alla egenskaper på den nivån som parameterstyrda som strängar, med standardvärden och parameter namn. Ett undantag är egenskapen `interval`, som är parameterstyrda som en siffer typ och med parameter namnet suffixet med `<entityName>_properties_typeProperties_recurrence_triggerSuffix`. På samma sätt är egenskapen `freq` en sträng och är parameterstyrda som en sträng. Men egenskapen `freq` är parameterstyrda utan ett standardvärde. Namnet är kortare och suffixet. Till exempel `<entityName>_freq`.

#### <a name="linkedservices"></a>LinkedServices

* Länkade tjänster är unika. Eftersom länkade tjänster och data uppsättningar har en mängd olika typer, kan du ange en typ bestämd anpassning. I det här exemplet tillämpas alla länkade tjänster av typen `AzureDataLakeStore`, en särskild mall, och för alla andra (via \*) används en annan mall.
* Egenskapen `connectionString` är parameterstyrda som ett `securestring`-värde, det har inget standardvärde och det har ett förkortat parameter namn som har suffix `connectionString`.
* Egenskapen `secretAccessKey` sker som ett `AzureKeyVaultSecret` (till exempel i en `AmazonS3` länkad tjänst). Den är automatiskt parameterstyrda som en Azure Key Vault hemlighet och hämtas från det konfigurerade nyckel valvet. Du kan också Parameterisera själva nyckel valvet.

#### <a name="datasets"></a>Datauppsättningar

* Även om typ specifik anpassning är tillgänglig för data uppsättningar kan du ange konfiguration utan att uttryckligen ha en konfiguration på \*nivå. I exemplet ovan är alla data uppsättnings egenskaper under `typeProperties` parameterstyrda.

### <a name="default-parameterization-template"></a>Standard Parameterisering-mall

Nedan visas den aktuella standard Parameterisering-mallen. Om du bara behöver lägga till en eller flera parametrar kan det vara bra att redigera det direkt eftersom du inte kommer att förlora den befintliga Parameterisering-strukturen.

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
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

Nedan visas ett exempel på hur du lägger till ett enda värde i standard mal len Parameterisering. Vi vill bara lägga till ett befintligt Databricks-Interactive Cluster-ID för en Databricks-länkad tjänst till parameter filen. Observera att filen nedan är samma som ovanstående fil, förutom `existingClusterId` som ingår i fältet egenskaper för `Microsoft.DataFactory/factories/linkedServices`.

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
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

Om du har ställt in kontinuerlig integrering och distribution (CI/CD) för dina data fabriker kan du köra begränsningar för Azure Resource Manager mal len när fabriken växer större. Ett exempel på en gräns är det maximala antalet resurser i en Resource Manager-mall. För att kunna hantera stora fabriker, tillsammans med att skapa en fullständig Resource Manager-mall för en fabrik, skapar Data Factory nu länkade Resource Manager-mallar. Med den här funktionen är hela fabriks nytto lasten uppdelad i flera filer så att du inte kan använda gränserna.

Om du har konfigurerat git skapas och sparas de länkade mallarna tillsammans med de fullständiga Resource Manager-mallarna i `adf_publish`-grenen under en ny mapp med namnet `linkedTemplates`.

![Mapp för länkade Resource Manager-mallar](media/continuous-integration-deployment/linked-resource-manager-templates.png)

De länkade Resource Manager-mallarna har vanligt vis en huvud mal len och en uppsättning underordnade mallar som är länkade till huvud servern. Den överordnade mallen kallas `ArmTemplate_master.json`, och underordnade mallar får namnet med mönstret `ArmTemplate_0.json`, `ArmTemplate_1.json` och så vidare. Om du vill använda länkade mallar i stället för den fullständiga Resource Manager-mallen uppdaterar du CI/CD-aktiviteten så att den pekar på `ArmTemplate_master.json` i stället för `ArmTemplateForFactory.json` (fullständig Resource Manager-mall). Resource Manager kräver också att du överför de länkade mallarna till ett lagrings konto så att de kan nås av Azure under distributionen. Mer information finns i [distribuera länkade arm-mallar med VSTS](https://blogs.msdn.microsoft.com/najib/2018/04/22/deploying-linked-arm-templates-with-vsts/).

Kom ihåg att lägga till Data Factory skript i CI/CD-pipeline innan och efter distributions aktiviteten.

Om du inte har git konfigurerat är de länkade mallarna tillgängliga via gesten **Exportera arm-mall** .

## <a name="hot-fix-production-branch"></a>Snabb korrigering av produktions gren

Om du distribuerar en fabrik till produktion och inser att det finns en bugg som behöver åtgärdas direkt, men du inte kan distribuera den aktuella samarbets grenen, kan du behöva distribuera en snabb korrigering. Den här metoden kallas snabb korrigerings teknik eller QFE. 

1.  I Azure DevOps går du till den version som distribuerades till produktionen och hittade den senaste incheckning som har distribuerats.

2.  Hämta bekräftelse-ID för samarbets grenen från bekräftelse meddelandet.

3.  Skapa en ny gren för snabb korrigering från detta genomförande.

4.  Gå till Azure Data Factory UX och växla till den här grenen.

5.  Åtgärda felet med hjälp av Azure Data Factory UX. Testa dina ändringar.

6.  När korrigeringen har verifierats klickar du på **Exportera arm-mall** för att hämta Resource Manager-mallen för snabb korrigering.

7.  Checka in den här versionen manuellt i adf_publish-grenen.

8.  Om du har konfigurerat din versions pipeline till att automatiskt utlösa baserat på adf_publish-incheckningar startar en ny version automatiskt. Annars måste du köa en version manuellt.

9.  Distribuera snabb korrigerings versionen till test-och produktions fabrikerna. Den här versionen innehåller föregående produktions nytto Last plus den åtgärd som gjorts i steg 5.

10. Lägg till ändringarna från snabb korrigeringen till utvecklings grenen så att senare versioner inte kan köras i samma fel.

## <a name="best-practices-for-cicd"></a>Metod tips för CI/CD

Om du använder git-integrering med din data fabrik och du har en CI/CD-pipeline som flyttar dina ändringar från utveckling till test och sedan till produktion, rekommenderar vi följande bästa praxis:

-   **Git-integrering**. Du behöver bara konfigurera din utvecklings data fabrik med git-integrering. Ändringar av test och produktion distribueras via CI/CD och behöver inte git-integrering.

-   **Data Factory CI/CD-skript**. Före distributions steget i Resource Manager i CI/CD krävs vissa uppgifter som att stoppa/starta utlösare och rensa. Vi rekommenderar att du använder PowerShell-skript före och efter distributionen. Mer information finns i [Uppdatera aktiva utlösare](#update-active-triggers). 

-   **Integrerings körningar och delning**. Integrerings körningar ändras inte ofta och liknar varandra i alla steg i CI/CD. Därför förväntar Data Factory att du har samma namn och samma typ av integrerings körningar i alla stadier av CI/CD. Om du vill dela integrerings körningar i alla stadier bör du överväga att använda en ternär fabrik precis för att innehålla de delade integrerings körningarna. Du kan använda den här delade fabriken i alla dina miljöer som en länkad integration runtime-typ.

-   **Key Vault**. När du använder Azure Key Vaultbaserade länkade tjänster kan du dra nytta av det ytterligare genom att hålla separata nyckel valv för olika miljöer. Du kan också konfigurera separata behörighets nivåer för var och en av dem. Till exempel kanske du inte vill att dina team medlemmar ska ha behörighet till produktions hemligheter. Om du följer den här metoden rekommenderar vi att du behåller samma hemliga namn i alla steg. Om du behåller samma namn behöver du inte ändra dina Resource Manager-mallar i CI/CD-miljöer eftersom det enda som ändras är nyckel valvets namn, vilket är en av parametrarna i Resource Manager-mallen.

## <a name="unsupported-features"></a>Funktioner som inte stöds

- Som design tillåter ADF _inte_ körsbär-plockning eller selektiv publicering av resurser. Publiceringar tar med **alla** ändringar som gjorts i data fabriken

    - Data Factory-entiteter är beroende av varandra, t. ex. utlösare är beroende av pipeliner, pipelines är beroende av data uppsättningar och andra pipeliner osv. Selektiv publicering av en del av resurser _kan_ leda till oväntade beteenden och fel
    - I sällsynta fall där selektiv publicering krävs kan det vara en snabb korrigering. Mer information finns i avsnittet om [snabb korrigering av produktions gren](#hot-fix-production-branch)

-   Det går inte att publicera från privata grenar

-   Från och med nu kan du inte vara värd för projekt på BitBucket
