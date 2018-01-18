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
ms.date: 10/31/2017
ms.author: dekapur
ms.openlocfilehash: 61182668b2677f19edbb736505d4892150890fed
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2018
---
# <a name="set-up-oms-log-analytics-for-a-cluster"></a>Ställ in OMS Log Analytics för ett kluster

Du kan ställa in en OMS-arbetsyta via Azure Resource Manager eller från Azure Marketplace. Använd tidigare när du vill behålla en mall för distributionen för framtida användning. Distribuera via Marketplace är enklare om du redan har ett kluster som distribueras med diagnostik aktiverad.

> [!NOTE]
> Du måste ha diagnostik aktiverat för klustret för att visa klustret / platform händelser för att kunna ställa in OMS att kunna övervaka ditt kluster.

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
                "name": "[concat(variables('applicationDiagnosticsStorageAccountName'),parameters('omsWorkspacename'))]",
                "type": "storageinsightconfigs",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]",
                    "[concat('Microsoft.Storage/storageAccounts/', variables('applicationDiagnosticsStorageAccountName'))]"
                ],
                "properties": {
                    "containers": [ ],
                    "tables": [
                        "WADServiceFabric*EventTable",
                        "WADWindowsEventLogsTable",
                        "WADETWEventTable"
                    ],
                    "storageAccount": {
                        "id": "[resourceId('Microsoft.Storage/storageaccounts/', variables('applicationDiagnosticsStorageAccountName'))]",
                        "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('applicationDiagnosticsStorageAccountName')),'2015-06-15').key1]"
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

5. Distribuera mallen som en Resource Manager-uppgradering av klustret. Detta görs med hjälp av den `New-AzureRmResourceGroupDeployment` API i AzureRM PowerShell-modulen. Ett kommando är:

    ```powershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName "sfcluster1" -TemplateFile "<path>\template.json" -TemplateParameterFile "<path>\parameters.json"
    ``` 

    Azure Resource Manager kommer att kunna identifiera att det här är en uppdatering för en befintlig resurs. Endast bearbetas ändringar mellan mallen föra den befintliga distributionen och den nya mallen som.

## <a name="deploying-oms-using-azure-marketplace"></a>Distribuera OMS med hjälp av Azure Marketplace

Om du vill lägga till en OMS-arbetsyta när du har distribuerat ett kluster, gå till Azure Marketplace (i portalen) och letar efter *”Service Fabric Analytics”*.

1. Klicka på **ny** på den vänstra navigeringsmenyn. 

2. Sök efter *Service Fabric Analytics*. Klicka på den resurs som visas.

3. Klicka på **skapa**

    ![OMS SA analyser i Marketplace](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics.png)

4. Klicka på i fönstret Service Fabric Analytics skapa **Välj en arbetsyta** för den *OMS-arbetsytan* fältet och sedan **skapa en ny arbetsyta**. Fyll i obligatoriska poster – det enda kravet är att prenumerationen för Service Fabric-kluster och OMS-arbetsytan ska vara samma. När posterna har validerats startar OMS-arbetsytan att distribuera. Detta tar bara några minuter.

5. När du är klar klickar du på **skapa** igen längst ned i fönstret Skapa Service Fabric Analytics. Kontrollera att den nya arbetsytan visas under *OMS-arbetsytan*. Detta lägger till lösningen till arbetsytan som du nyss skapade.

6. Arbetsytan måste fortfarande vara anslutna till diagnostikdata från klustret. Navigera till den resursgrupp som du har skapat lösningen i Service Fabric Analytics. Du bör se en *ServiceFabric (\<nameOfOMSWorkspace\>)*. Klicka på lösningen att navigera till dess översiktssidan från där du kan ändra inställningar för lösning, arbetsytan inställningar, och navigera till OMS-portalen.

7. I den vänstra navigeringsmenyn klickar du på **lagringskonton loggar**under *arbetsytan datakällor*.

8. På den *Storage-konto loggar* klickar du på **Lägg till** högst upp att lägga till ditt kluster loggar till arbetsytan.

9. Klicka i **lagringskonto** att lägga till lämpligt konto som skapats i klustret. Om du använde standardnamnet för storage-konto med namnet *sfdg\<resourceGroupName\>*. Du kan också bekräfta detta genom att kontrollera Azure Resource Manager-mallen som används för att distribuera ditt kluster genom att kontrollera värdet för den `applicationDiagnosticsStorageAccountName`. Du kan också behöva rulla nedåt och klicka på **läsa in mer** om namnet inte visas. Klicka på rätt lagringskontonamnet dig för att välja den.

10. Därefter måste du ange den *datatyp*, som ska anges till **Service Fabric händelser**.

11. Den *källa* automatiskt ska anges till *WADServiceFabric\*EventTable*.

12. Klicka på **OK** att ansluta din arbetsyta till ditt kluster loggar.

    ![Lägg till storage-konto loggar i OMS](media/service-fabric-diagnostics-event-analysis-oms/add-storage-account.png)

Kontot visas nu som en del av din *Storage-konto loggar* i din arbetsyta datakällor.

Du har nu lagt till Service Fabric Analytics-lösningen med den här i en OMS logganalys-arbetsyta som nu är korrekt ansluten till ditt kluster plattform och programmet tabell. Du kan lägga till ytterligare datakällor till arbetsytan på samma sätt.

## <a name="next-steps"></a>Nästa steg
* [Distribuera OMS-agenten](service-fabric-diagnostics-oms-agent.md) till noderna att samla in prestandaräknare och samla in docker statistik och loggfiler för behållarna
* Hämta bekantat med den [logga sökning och hämtning av](../log-analytics/log-analytics-log-searches.md) funktioner som erbjuds som en del av logganalys
* [Använd vyn Designer för att skapa anpassade vyer i logganalys](../log-analytics/log-analytics-view-designer.md)
