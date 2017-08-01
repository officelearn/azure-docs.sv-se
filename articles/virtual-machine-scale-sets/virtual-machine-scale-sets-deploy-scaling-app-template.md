---
title: "Distribuera ett program på en Azure VM-skalningsuppsättning | Microsoft Docs"
description: "Lär dig att distribuera ett enkelt autoskalningsprogram på en VM-skaluppsttning med en Azure Resource Manager-mall."
services: virtual-machine-scale-sets
documentationcenter: 
author: rwike77
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 4/4/2017
ms.author: ryanwi
ms.translationtype: Human Translation
ms.sourcegitcommit: 74f34bdbf5707510c682814716aa0b95c19a5503
ms.openlocfilehash: b2b0bbe2c8e07a9ee2f21983262a948acb90d03d
ms.contentlocale: sv-se
ms.lasthandoff: 06/09/2017


---

# <a name="deploy-an-autoscaling-app-using-a-template"></a>Distribuera en app för automatisk skalning med en mall

[Azure Resource Manager-mallar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#template-deployment) är ett bra sätt att distribuera grupper av relaterade resurser. Den här kursen bygger på [Distribuera en enkel skaluppsättning](virtual-machine-scale-sets-mvss-start.md) och beskriver hur du distribuerar ett enkelt program med autoskalning på en skaluppsättning med en Azure Resource Manager-mall.  Du kan också ställa in autoskalning med PowerShell, CLI eller portalen. Mer information finns i [Översikt för autoskala](virtual-machine-scale-sets-autoscale-overview.md).

## <a name="two-quickstart-templates"></a>Två snabbstartmallar
När du distribuerar en skaluppsättning du kan installera ny programvara på en plattformbild med ett [VM-tillägg](../virtual-machines/virtual-machines-windows-extensions-features.md). Ett VM-tilläggt är ett litet program som innehåller en färdig konfiguration och automatisering av uppgifter på virtuella Azure-datorer, till exempel att distribuera en app. Två olika exempelmallar finns i [Azure/azure-snabbstartsmallar](https://github.com/Azure/azure-quickstart-templates) som visar hur du distribuerar ett autoskalningsprogram på en skaluppsättning som anges med VM-tillägg.

### <a name="python-http-server-on-linux"></a>Python-HTTP-servern på Linux
Exempelmallen [Python HTTP-servern på Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale) distribuerar ett enkelt autoskalningsprogram som körs på en Linux-skaluppsättning.  [Bottle](http://bottlepy.org/docs/dev/), ett Python webwramverk och en enkel HTTP-server distribueras på varje virtuell dator i skaluppsättningen med ett anpassat skript för VM-tillägget. Skaluppsättningen skalar upp när den genomsnittliga CPU-användningen över alla virtuella datorer är större än 60 % och skalar ner när den genomsnittliga CPU-användningen är mindre än 30 %.

Förutom skaluppsättningsresursen deklarerar exempelmallen *azuredeploy.json* också virtuella nätverk, offentlig IP-adress, belastningsutjämnare och resurser för inställning av autoskala.  Mer information om hur du skapar dessa resurser i en mall finns i [Linux-skala med autoskala](virtual-machine-scale-sets-linux-autoscale.md).

I mallen *azuredeploy.json* anger `extensionProfile`-egenskapen för `Microsoft.Compute/virtualMachineScaleSets`-resurs ett anpassat skripttillägg. `fileUris` anger platsen för skript. I det här fallet två filer: *workserver.py*, som definierar en enkel HTTP-server och *installserver.sh*, som installerar Bottle och startar HTTP-servern. `commandToExecute` anger kommandot som ska köras när skaluppsättning har distribuerats.

```json
          "extensionProfile": {
            "extensions": [
              {
                "name": "lapextension",
                "properties": {
                  "publisher": "Microsoft.Azure.Extensions",
                  "type": "CustomScript",
                  "typeHandlerVersion": "2.0",
                  "autoUpgradeMinorVersion": true,
                  "settings": {
                    "fileUris": [
                      "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/installserver.sh",
                      "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/workserver.py"
                    ],
                    "commandToExecute": "bash installserver.sh"
                  }
                }
              }
            ]
          }
```

### <a name="aspnet-mvc-application-on-windows"></a>ASP.NET MVC-appen i Windows
Exempelmallen [ASP.NET MVC-appen i Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale) distribuerar en enkel ASP.NET MVC-app som körs i IIS på Windows-skaluppsättning.  IIS och MVC-appen distribueras med VM-tillägget [PowerShell önskad tillståndskonfiguration (DSC)](virtual-machine-scale-sets-dsc.md).  Skaluppsättningen skalar upp (en VM-instans i taget) när CPU-användning är större än 50 % under 5 minuter. 

Förutom skaluppsättningsresursen deklarerar exempelmallen *azuredeploy.json* också virtuella nätverk, offentlig IP-adress, belastningsutjämnare och resurser för inställning av autoskala. Den här mallen visas också uppgradering av programmet.  Mer information om hur du skapar dessa resurser i en mall finns i [Windows-skala med autoskala](virtual-machine-scale-sets-windows-autoscale.md).

I mallen *azuredeploy.json* anger resursen `extensionProfile` egenskapen för `Microsoft.Compute/virtualMachineScaleSets` ett [önskad tillståndskonfiguration (DSC)](virtual-machine-scale-sets-dsc.md)-tillägg som installerar IIS och en standard-webbapp från ett WebDeploy-paket.  Skriptet *IISInstall.ps1* installerar IIS på den virtuella datorn och finns i mappen *DSC*.  MVC-webbappen finns i mappen *WebDeploy*.  Sökvägar till installationsskriptet och webbprogrammet har definierats i parametrarna `powershelldscZip` och `webDeployPackage` i filen *azuredeploy.parameters.json*. 

```json
          "extensionProfile": {
            "extensions": [
              {
                "name": "Microsoft.Powershell.DSC",
                "properties": {
                  "publisher": "Microsoft.Powershell",
                  "type": "DSC",
                  "typeHandlerVersion": "2.9",
                  "autoUpgradeMinorVersion": true,
                  "forceUpdateTag": "[parameters('powershelldscUpdateTagVersion')]",
                  "settings": {
                    "configuration": {
                      "url": "[variables('powershelldscZipFullPath')]",
                      "script": "IISInstall.ps1",
                      "function": "InstallIIS"
                    },
                    "configurationArguments": {
                      "nodeName": "localhost",
                      "WebDeployPackagePath": "[variables('webDeployPackageFullPath')]"
                    }
                  }
                }
              }
            ]
          }
```

## <a name="deploy-the-template"></a>Distribuera mallen
Det enklaste sättet att distribuera [Python HTTP-servern på Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale) eller mallen [ASP.NET MVC-program på Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale) är att använda knappen **Distribuera till Azure** som finns i readme-filerna i GitHub.  Du kan också använda PowerShell eller Azure CLI för att distribuera exempelmallarna.

### <a name="powershell"></a>PowerShell
Kopiera [Python HTTP-servern på Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale) eller filerna [ASP.NET MVC-appen i Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale) från GitHub-lagringsplatsen till en mapp på den lokala datorn.  Öppna filen *azuredeploy.parameters.json* och uppdatera standardvärdena för parametrarna `vmssName`, `adminUsername` och `adminPassword`. Spara följande PowerShell-skript till *deploy.ps1* i samma mapp som mallen *azuredeploy.json*. För att distribuera exempelmallen, kör skriptet *deploy.ps1* från en PowerShell-kommandotolk.

```powershell
param(
 [Parameter(Mandatory=$True)]
 [string]
 $subscriptionId,

 [Parameter(Mandatory=$True)]
 [string]
 $resourceGroupName,

 [string]
 $resourceGroupLocation,

 [Parameter(Mandatory=$True)]
 [string]
 $deploymentName,

 [string]
 $templateFilePath = "azuredeploy.json",

 [string]
 $parametersFilePath = "azuredeploy.parameters.json"
)

<#
.SYNOPSIS
    Registers RPs
#>
Function RegisterRP {
    Param(
        [string]$ResourceProviderNamespace
    )

    Write-Host "Registering resource provider '$ResourceProviderNamespace'";
    Register-AzureRmResourceProvider -ProviderNamespace $ResourceProviderNamespace;
}

#******************************************************************************
# Script body
# Execution begins here
#******************************************************************************
$ErrorActionPreference = "Stop"

# sign in
Write-Host "Logging in...";
Login-AzureRmAccount;

# select subscription
Write-Host "Selecting subscription '$subscriptionId'";
Select-AzureRmSubscription -SubscriptionID $subscriptionId;

# Register RPs
$resourceProviders = @("microsoft.resources");
if($resourceProviders.length) {
    Write-Host "Registering resource providers"
    foreach($resourceProvider in $resourceProviders) {
        RegisterRP($resourceProvider);
    }
}

#Create or check for existing resource group
$resourceGroup = Get-AzureRmResourceGroup -Name $resourceGroupName -ErrorAction SilentlyContinue
if(!$resourceGroup)
{
    Write-Host "Resource group '$resourceGroupName' does not exist. To create a new resource group, please enter a location.";
    if(!$resourceGroupLocation) {
        $resourceGroupLocation = Read-Host "resourceGroupLocation";
    }
    Write-Host "Creating resource group '$resourceGroupName' in location '$resourceGroupLocation'";
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation
}
else{
    Write-Host "Using existing resource group '$resourceGroupName'";
}

# Start the deployment
Write-Host "Starting deployment...";
if(Test-Path $parametersFilePath) {
    New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath -TemplateParameterFile $parametersFilePath;
} else {
    New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath;
}
```

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]

