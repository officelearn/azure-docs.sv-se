---
title: "Azure Service Fabric - Konfigurera övervakning med OMS Log Analytics | Microsoft Docs"
description: "Lär dig hur du ställer in OMS för att visualisera och analysera händelser för övervakning av Azure Service Fabric-kluster."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 1/17/2017
ms.author: dekapur
ms.openlocfilehash: 53b06c5a1395f34c96d4011366835a920d5c670b
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2018
---
# <a name="set-up-oms-log-analytics-for-a-cluster"></a>Ställ in OMS Log Analytics för ett kluster

Du kan ställa in en OMS-arbetsyta via Azure Resource Manager, PowerShell eller via Azure Marketplace. Om du behåller en uppdaterad Resource Manager-mall för din distribution använder du samma mall för att ställa in din OMS-miljö för framtida användning. Distribuera via Marketplace är enklare om du redan har ett kluster som distribueras med diagnostik aktiverad. I det fall att du inte har prenumerationen åtkomst för kontot som du distribuerar OMS, använda PowerShell eller distribuera via Resource Manager-mallen.

> [!NOTE]
> Du måste ha diagnostik aktiverat för klustret för att visa klustret / platform händelser för att kunna ställa in OMS att kunna övervaka ditt kluster.

## <a name="deploying-oms-using-azure-marketplace"></a>Distribuera OMS med hjälp av Azure Marketplace

Om du vill lägga till en OMS-arbetsyta när du har distribuerat ett kluster, gå till Azure Marketplace (i portalen) och letar efter *”Service Fabric Analytics”.*

1. Klicka på **ny** på den vänstra navigeringsmenyn. 

2. Sök efter *Service Fabric Analytics*. Klicka på den resurs som visas.

3. Klicka på **skapa**

    ![OMS SA analyser i Marketplace](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics.png)

4. Klicka på i fönstret Service Fabric Analytics skapa **Välj en arbetsyta** för den *OMS-arbetsytan* fältet och sedan **skapa en ny arbetsyta**. Fyll i obligatoriska poster – det enda kravet är att prenumerationen för Service Fabric-kluster och OMS-arbetsytan ska vara samma. När posterna har validerats ska OMS-arbetsytan börja distribuera. Detta tar bara några minuter.

5. När du är klar klickar du på **skapa** igen längst ned i fönstret Skapa Service Fabric Analytics. Kontrollera att den nya arbetsytan visas under *OMS-arbetsytan*. Lösningen läggs till på arbetsytan som du nyss skapade.

Om du använder Windows, fortsätter du med följande steg för att koppla samman OMS till lagringskontot där din klusterhändelser lagras. Att aktivera den här upplevelsen korrekt för Linux-kluster är fortfarande pågår. Fram till dess, fortsätter du med att lägga till OMS-agenten i klustret.  

1. Arbetsytan måste fortfarande vara anslutna till diagnostikdata från klustret. Navigera till den resursgrupp som du har skapat lösningen i Service Fabric Analytics. Du bör se en *ServiceFabric (\<nameOfOMSWorkspace\>)*. Klicka på lösningen att navigera till dess översiktssidan från där du kan ändra inställningar för lösning, arbetsytan inställningar, och navigera till OMS-portalen.

2. I den vänstra navigeringsmenyn klickar du på **lagringskonton loggar**under *arbetsytan datakällor*.

3. På den *Storage-konto loggar* klickar du på **Lägg till** högst upp att lägga till ditt kluster loggar till arbetsytan.

4. Klicka i **lagringskonto** att lägga till lämpligt konto som skapats i klustret. Om du använde standardnamnet för storage-konto med namnet *sfdg\<resourceGroupName\>*. Du kan också bekräfta detta genom att kontrollera Azure Resource Manager-mallen som används för att distribuera ditt kluster genom att kontrollera värdet för den `applicationDiagnosticsStorageAccountName`. Du kan också behöva rulla nedåt och klicka på **läsa in mer** om namnet inte visas. Klicka på rätt lagringskontonamnet dig för att välja den.

5. Därefter måste du ange den *datatyp*, som ska anges till **Service Fabric händelser**.

6. Den *källa* automatiskt ska anges till *WADServiceFabric\*EventTable*.

7. Klicka på **OK** att ansluta din arbetsyta till ditt kluster loggar.

    ![Lägg till storage-konto loggar i OMS](media/service-fabric-diagnostics-event-analysis-oms/add-storage-account.png)

Kontot visas nu som en del av din *Storage-konto loggar* i din arbetsyta datakällor.

Du har nu lagt till Service Fabric Analytics-lösningen med den här i en OMS logganalys-arbetsyta som nu är korrekt ansluten till ditt kluster plattform och programmet tabell. Du kan lägga till ytterligare datakällor till arbetsytan på samma sätt.


## <a name="deploying-oms-using-a-resource-manager-template"></a>Distribuera OMS med hjälp av en Resource Manager-mall

När du distribuerar ett kluster med en Resource Manager-mall mallen ska skapa en ny OMS-arbetsyta, lägga till Service Fabric-lösningen i den och konfigurera den för att läsa data från lämplig storage-tabeller.

[Här](https://azure.microsoft.com/resources/templates/service-fabric-oms/) är en exempelmall som du kan använda och ändra enligt kraven. Flera mallar som ger olika alternativ för hur du konfigurerar en OMS-arbetsyta finns på [Service Fabric och OMS mallar](https://azure.microsoft.com/resources/templates/?term=service+fabric+OMS).

De viktigaste ändringarna är följande:

1. Lägg till `omsWorkspaceName` och `omsRegion` till parametrarna. Det innebär att lägga till följande kodavsnitt i de parametrar som definierats i din *template.json* fil. Du kan ändra standardinställningarna som du vill. Du bör också lägga till två nya parametrar i din *parameters.json* att definiera värden för resurs-distribution:
    
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

    Den `omsRegion` värden måste överensstämma med en specifik uppsättning värden. Du bör välja det som är närmast distributionen av klustret.

2. Om du kommer att skicka loggar programmet till OMS, kontrollerar du att den `applicationDiagnosticsStorageAccountType` och `applicationDiagnosticsStorageAccountName` ingår som parametrar i mallen. Om de inte kan läggas till avsnittet variables t.ex och redigera deras värden efter behov. Du kan även inkludera dem som parametrar, om du vill att följa formatet ovan.

    ```json
    "applicationDiagnosticsStorageAccountType": "Standard_LRS",
    "applicationDiagnosticsStorageAccountName": "[toLower(concat('oms', uniqueString(resourceGroup().id), '3' ))]"
    ```

3. Lägg till Service Fabric OMS-lösning i din mall variabler:

    ```json
    "solution": "[Concat('ServiceFabric', '(', parameters('omsWorkspacename'), ')')]",
    "solutionName": "ServiceFabric"
    ```

4. Lägger till följande i slutet av dina resurser i avsnittet efter där Service Fabric-klusterresursen har deklarerats.

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

5. Distribuera mallen som en Resource Manager-uppgradering av klustret. Detta görs med hjälp av den `New-AzureRmResourceGroupDeployment` API i AzureRM PowerShell-modulen. Ett kommando är:

    ```powershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName "sfcluster1" -TemplateFile "<path>\template.json" -TemplateParameterFile "<path>\parameters.json"
    ``` 

    Azure Resource Manager kommer att kunna identifiera att det här är en uppdatering för en befintlig resurs. Endast bearbetas ändringar mellan mallen föra den befintliga distributionen och den nya mallen som.

## <a name="deploying-oms-using-azure-powershell"></a>Distribuera OMS med hjälp av Azure PowerShell

Du kan också distribuera din OMS logganalys resurs via PowerShell. Detta uppnås med hjälp av den `New-AzureRmOperationalInsightsWorkspace` kommando. Kontrollera att du har installerat för att kunna göra detta [Azure Powershell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-5.1.1). Använd det här skriptet för att skapa en ny OMS logganalys-arbetsyta och lägga till Service Fabric-lösningen: 

```ps

$SubscriptionName = "<Name of your subscription>"
$ResourceGroup = "<Resource group name>"
$Location = "<Resource group location>"
$WorkspaceName = "<OMS Log Analytics workspace name>"
$solution = "ServiceFabric"

# Log in to Azure and access the correct subscription
Login-AzureRmAccount
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

När detta är slutfört om klustret är ett Windows-kluster, följer du stegen i avsnittet ovan för att koppla samman OMS logganalys till lämplig storage-konto.

Du kan också lägga till andra lösningar eller göra andra ändringar i din OMS-arbetsyta med hjälp av PowerShell. Du kan läsa mer om det finns [hantera Log Analytics med hjälp av PowerShell](../log-analytics/log-analytics-powershell-workspace-configuration.md)

## <a name="next-steps"></a>Nästa steg
* [Distribuera OMS-agenten](service-fabric-diagnostics-oms-agent.md) till noderna att samla in prestandaräknare och samla in docker statistik och loggfiler för behållarna
* Hämta bekantat med den [logga sökning och hämtning av](../log-analytics/log-analytics-log-searches.md) funktioner som erbjuds som en del av logganalys
* [Använd vyn Designer för att skapa anpassade vyer i logganalys](../log-analytics/log-analytics-view-designer.md)
