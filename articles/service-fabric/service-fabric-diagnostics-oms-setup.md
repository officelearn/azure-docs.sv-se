---
title: Azure Service Fabric - Konfigurera övervakning med Log Analytics | Microsoft Docs
description: Lär dig hur du ställer in Log Analytics för att visualisera och analysera händelser att övervaka dina Azure Service Fabric-kluster.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 4/03/2018
ms.author: srrengar
ms.openlocfilehash: af94e3270493f6967c4f8c484170751c098bf181
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="set-up-log-analytics-for-a-cluster"></a>Ställ in Log Analytics för ett kluster

Logganalys är vår rekommendation att övervaka klusterhändelser. Du kan ställa in logganalys-arbetsytan via Azure Resource Manager, PowerShell eller Azure Marketplace. Om du sparar en uppdaterad Resource Manager-mall för distributionen för framtida användning kan använda samma mall för att ställa in logganalys-miljö. Distribution via Marketplace är enklare om du redan har ett kluster som distribueras med diagnostik aktiverad. Om du inte har prenumerationen behörighet för kontot som du distribuerar till, distribuera med hjälp av PowerShell eller Resource Manager-mallen.

> [!NOTE]
> Om du vill konfigurera logganalys att övervaka klustret som du behöver ha diagnostik aktiverad för att visa händelser för kluster- eller plattform-nivå. Referera till [hur du ställer in diagnostik i Windows-kluster](service-fabric-diagnostics-event-aggregation-wad.md) och [hur du ställer in diagnostik i Linux-kluster](service-fabric-diagnostics-event-aggregation-lad.md) mer

## <a name="deploy-a-log-analytics-workspace-by-using-azure-marketplace"></a>Distribuera en logganalys-arbetsytan med hjälp av Azure Marketplace

Om du vill lägga till logganalys-arbetsytan när du har distribuerat ett kluster, gå till Azure Marketplace i portalen och Sök efter **Service Fabric Analytics**. Detta är en anpassad lösning för Service Fabric-distributioner som innehåller data som är specifika för Service Fabric. I den här processen skapar du både arbetsytan (sammanställning av underliggande kluster) och lösningen (instrumentpanelen för att visa insikter).

1. Välj **ny** på den vänstra navigeringsmenyn. 

2. Sök efter **Service Fabric Analytics**. Välj den resurs som visas.

3. Välj **Skapa**.

    ![OMS SA analyser i Marketplace](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics.png)

4. I fönstret Service Fabric Analytics skapa Välj **Välj en arbetsyta** för den **OMS-arbetsytan** fältet och sedan **skapa en ny arbetsyta**. Fyll i posterna som krävs. Det enda kravet är att prenumerationen för Service Fabric-kluster och arbetsytan är samma. När posterna har validerats börjar distribuera din arbetsyta. Distributionen tar bara några minuter.

5. När du är klar väljer **skapa** igen längst ned i fönstret Skapa Service Fabric Analytics. Kontrollera att den nya arbetsytan visas under **OMS-arbetsytan**. Den här åtgärden lägger till lösningen arbetsytans.

Om du använder Windows, fortsätter du med följande steg för att ansluta OMS till lagringskontot där din klusterhändelser lagras. 

>[!NOTE]
>Aktivera upplevelsen för Linux-kluster är inte tillgänglig ännu. 

### <a name="connect-the-log-analytics-workspace-to-your-cluster"></a>Ansluta Log Analytics-arbetsyta i klustret 

1. Arbetsytan måste vara anslutna till diagnostikdata från klustret. Gå till den resursgrupp som du skapade Service Fabric Analytics-lösning. Välj **ServiceFabric\<nameOfWorkspace\>**  och gå till dess översiktssidan. Därifrån kan du ändra inställningar för lösning, arbetsytan inställningar, och åtkomst till OMS-portalen.

2. På den vänstra navigeringsmenyn under **arbetsytan datakällor**väljer **lagringskonton loggar**.

3. På den **Storage-konto loggar** väljer **Lägg till** högst upp att lägga till ditt kluster loggar till arbetsytan.

4. Välj **lagringskonto** att lägga till lämpligt konto som skapats i klustret. Om du använde standardnamnet för lagringskontot är **sfdg\<resourceGroupName\>**. Du kan också bekräfta detta med hjälp av Azure Resource Manager-mallen som används för att distribuera ditt kluster genom att kontrollera det värde som används för **applicationDiagnosticsStorageAccountName**. Om namnet inte visas, bläddra nedåt och välj **läsa in mer**. Välj namnet på lagringskontot.

5. Ange vilken datatyp. Ange det till **Service Fabric händelser**.

6. Kontrollera att källan är automatiskt **WADServiceFabric\*EventTable**.

7. Välj **OK** att ansluta din arbetsyta till ditt kluster loggar.

    ![Lägg till storage-konto loggar i OMS](media/service-fabric-diagnostics-event-analysis-oms/add-storage-account.png)

Kontot visas nu som en del av ditt lagringskonto loggar i din arbetsyta datakällor.

Du har lagt till Service Fabric Analytics-lösning i en OMS logganalys-arbetsyta som nu är korrekt ansluten till ditt kluster plattform och programmet tabell. Du kan lägga till ytterligare datakällor till arbetsytan på samma sätt.


## <a name="deploy-oms-by-using-a-resource-manager-template"></a>Distribuera OMS med hjälp av en Resource Manager-mall

När du distribuerar ett kluster med hjälp av en Resource Manager-mall mallen skapar en ny OMS-arbetsyta, lägger till Service Fabric-lösningen till arbetsytan och konfigurerar den för att läsa data från lämplig storage-tabeller.

Du kan använda och ändra [exempel mallen](https://github.com/krnese/azure-quickstart-templates/tree/master/service-fabric-oms) som uppfyller dina krav.

Gör följande ändringar:
1. Lägg till `omsWorkspaceName` och `omsRegion` till parametrarna genom att lägga till följande kodavsnitt de parametrar som definierats i din *template.json* fil. Du kan ändra standardinställningarna som du vill. Lägg även till två nya parametrar i din *parameters.json* filen för att definiera deras värden för resurs-distribution:
    
    ```json
    "omsWorkspacename": {
        "type": "string",
        "defaultValue": "sfomsworkspace",
        "metadata": {
            "description": "Name of your OMS Log Analytics Workspace"
        }
    },
    "omsRegion": {
        "type": "string",
        "defaultValue": "East US",
        "allowedValues": [
            "West Europe",
            "East US",
            "Southeast Asia"
        ],
        "metadata": {
            "description": "Specify the Azure Region for your OMS workspace"
        }
    }
    ```

    Den `omsRegion` värden måste överensstämma med en specifik uppsättning värden. Välj det som är närmast distributionen av klustret.

2. Om du skickar programmet loggar till OMS först bekräfta att den `applicationDiagnosticsStorageAccountType` och `applicationDiagnosticsStorageAccountName` ingår som parametrar i mallen. Om de inte ingår, lägga till dem i avsnittet variables och redigera deras värden efter behov. Du kan även inkludera dem som parametrar genom att följa formatet föregående.

    ```json
    "applicationDiagnosticsStorageAccountType": "Standard_LRS",
    "applicationDiagnosticsStorageAccountName": "[toLower(concat('oms', uniqueString(resourceGroup().id), '3' ))]"
    ```

3. Lägg till Service Fabric OMS-lösning i din mall variabler:

    ```json
    "solution": "[Concat('ServiceFabric', '(', parameters('omsWorkspacename'), ')')]",
    "solutionName": "ServiceFabric"
    ```

4. Lägg till följande i slutet av avsnittet dina resurser efter där Service Fabric-klusterresursen har deklarerats:

    ```json
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[parameters('omsWorkspacename')]",
        "type": "Microsoft.OperationalInsights/workspaces",
        "properties": {
            "sku": {
                "name": "Free"
            }
        },
        "resources": [
            {
                "apiVersion": "2015-11-01-preview",
                "name": "[concat(parameters('applicationDiagnosticsStorageAccountName'),parameters('omsWorkspacename'))]",
                "type": "storageinsightconfigs",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]",
                    "[concat('Microsoft.Storage/storageAccounts/', parameters('applicationDiagnosticsStorageAccountName'))]"
                ],
                "properties": {
                    "containers": [ ],
                    "tables": [
                        "WADServiceFabric*EventTable",
                        "WADWindowsEventLogsTable",
                        "WADETWEventTable"
                    ],
                    "storageAccount": {
                        "id": "[resourceId('Microsoft.Storage/storageaccounts/', parameters('applicationDiagnosticsStorageAccountName'))]",
                        "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-06-15').key1]"
                    }
                }
            }
        ]
    },
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[variables('solution')]",
        "type": "Microsoft.OperationsManagement/solutions",
        "id": "[resourceId('Microsoft.OperationsManagement/solutions/', variables('solution'))]",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('OMSWorkspacename'))]"
        ],
        "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
        },
        "plan": {
            "name": "[variables('solution')]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('solutionName'))]",
            "promotionCode": ""
        }
    }
    ```
    
    > [!NOTE]
    > Om du har lagt till den `applicationDiagnosticsStorageAccountName` som en variabel, se till att alla referenser till den för att ändra `variables('applicationDiagnosticsStorageAccountName')` i stället för `parameters('applicationDiagnosticsStorageAccountName')`.

5. Distribuera mallen som en Resource Manager-uppgradering av klustret med hjälp av den `New-AzureRmResourceGroupDeployment` API i AzureRM PowerShell-modulen. Ett kommando är:

    ```powershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName "sfcluster1" -TemplateFile "<path>\template.json" -TemplateParameterFile "<path>\parameters.json"
    ``` 

    Azure Resource Manager upptäcker att det här kommandot är en uppdatering för en befintlig resurs. Endast bearbetas ändringar mellan mallen föra den befintliga distributionen och den nya mallen som.

## <a name="deploy-oms-by-using-azure-powershell"></a>Distribuera OMS med hjälp av Azure PowerShell

Du kan också distribuera din OMS logganalys resurs via PowerShell med hjälp av den `New-AzureRmOperationalInsightsWorkspace` kommando. Kontrollera att du har installerat om du vill använda den här metoden [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-5.1.1). Använd det här skriptet för att skapa en ny OMS logganalys-arbetsyta och lägga till Service Fabric-lösningen: 

```PowerShell

$SubscriptionName = "<Name of your subscription>"
$ResourceGroup = "<Resource group name>"
$Location = "<Resource group location>"
$WorkspaceName = "<OMS Log Analytics workspace name>"
$solution = "ServiceFabric"

# Log in to Azure and access the correct subscription
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId $SubID 

# Create the resource group if needed
try {
    Get-AzureRmResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzureRmResourceGroup -Name $ResourceGroup -Location $Location
}

New-AzureRmOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup
Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true

```

När du är klar följer du stegen i föregående avsnitt för att ansluta OMS logganalys till lämplig storage-konto.

Du kan också lägga till andra lösningar eller göra andra ändringar till OMS-arbetsyta med hjälp av PowerShell. Läs mer i [hantera Log Analytics med hjälp av PowerShell](../log-analytics/log-analytics-powershell-workspace-configuration.md).

## <a name="next-steps"></a>Nästa steg
* [Distribuera OMS-agenten](service-fabric-diagnostics-oms-agent.md) till noderna att samla in prestandaräknare och samla in docker statistik och loggfiler för behållarna
* Hämta bekantat med den [logga sökning och hämtning av](../log-analytics/log-analytics-log-searches.md) funktioner som erbjuds som en del av logganalys
* [Använd vyn Designer för att skapa anpassade vyer i logganalys](../log-analytics/log-analytics-view-designer.md)
