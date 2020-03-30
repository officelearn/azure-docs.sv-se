---
title: Skalning per app för värdskap med hög densitet
description: Skala appar oberoende av App Service-abonnemangen och optimera de utskalade instanserna i planen.
author: btardif
ms.assetid: a903cb78-4927-47b0-8427-56412c4e3e64
ms.topic: article
ms.date: 05/13/2019
ms.author: byvinyal
ms.custom: seodec18
ms.openlocfilehash: f1ca4958fe2608d0c040ef5b93827a7e71a4151c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74672354"
---
# <a name="high-density-hosting-on-azure-app-service-using-per-app-scaling"></a>Värdskap med hög densitet på Azure App Service med skalning per app

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

När du använder App Service kan du skala dina appar genom att skala [apptjänstplanen](overview-hosting-plans.md) som de körs på. När flera appar körs i samma App Service-plan körs alla appar i planen varje utskalad instans.

*Per appskalning* kan aktiveras på apptjänstplansnivå för att tillåta skalning av en app oberoende av apptjänstplanen som är värd för den. På så sätt kan en App Service-plan skalas till 10 instanser, men en app kan ställas in så att den bara använder fem.

> [!NOTE]
> Per appskalning är endast tillgängligt för prisnivåer **standard,** **Premium,** **Premium V2** och **Isolerade.**
>

Appar allokeras till tillgänglig App Service-plan med hjälp av en metod för bästa möjliga arbete för en jämn distribution mellan instanser. En jämn distribution garanteras inte, men plattformen ser till att två instanser av samma app inte finns på samma App Service-planinstans.

Plattformen är inte beroende av mått för att besluta om arbetsallokering. Program balanseras endast om när instanser läggs till eller tas bort från App Service-planen.

## <a name="per-app-scaling-using-powershell"></a>Per appskalning med PowerShell

Skapa en plan med skalning per ```-PerSiteScaling $true``` app genom ```New-AzAppServicePlan``` att skicka parametern till cmdleten.

```powershell
New-AzAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan `
                            -Location $Location `
                            -Tier Premium -WorkerSize Small `
                            -NumberofWorkers 5 -PerSiteScaling $true
```

Aktivera skalning per app med en befintlig appserviceplan genom att skicka in parametern `-PerSiteScaling $true` till cmdleten. ```Set-AzAppServicePlan```

```powershell
# Enable per-app scaling for the App Service Plan using the "PerSiteScaling" parameter.
Set-AzAppServicePlan -ResourceGroupName $ResourceGroup `
   -Name $AppServicePlan -PerSiteScaling $true
```

Konfigurera antalet instanser som appen kan använda i App Service-planen på appnivå.

I exemplet nedan är appen begränsad till två instanser oavsett hur många instanser den underliggande apptjänstplanen skalas ut till.

```powershell
# Get the app we want to configure to use "PerSiteScaling"
$newapp = Get-AzWebApp -ResourceGroupName $ResourceGroup -Name $webapp

# Modify the NumberOfWorkers setting to the desired value.
$newapp.SiteConfig.NumberOfWorkers = 2

# Post updated app back to azure
Set-AzWebApp $newapp
```

> [!IMPORTANT]
> `$newapp.SiteConfig.NumberOfWorkers`skiljer sig `$newapp.MaxNumberOfWorkers`från . Skalning per app `$newapp.SiteConfig.NumberOfWorkers` använder för att bestämma appens skalegenskaper.

## <a name="per-app-scaling-using-azure-resource-manager"></a>Skalning per app med Azure Resource Manager

Följande Azure Resource Manager-mall skapar:

- En apptjänstplan som skalas ut till 10 instanser
- en app som är konfigurerad för att skalas till högst fem instanser.

App serviceplanen ställer in egenskapen **PerSiteScaling** på true `"perSiteScaling": true`. Appen anger **antalet arbetare som** ska användas `"properties": { "numberOfWorkers": "5" }`till 5 .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters":{
        "appServicePlanName": { "type": "string" },
        "appName": { "type": "string" }
        },
    "resources": [
    {
        "comments": "App Service Plan with per site perSiteScaling = true",
        "type": "Microsoft.Web/serverFarms",
        "sku": {
            "name": "P1",
            "tier": "Premium",
            "size": "P1",
            "family": "P",
            "capacity": 10
            },
        "name": "[parameters('appServicePlanName')]",
        "apiVersion": "2015-08-01",
        "location": "West US",
        "properties": {
            "name": "[parameters('appServicePlanName')]",
            "perSiteScaling": true
        }
    },
    {
        "type": "Microsoft.Web/sites",
        "name": "[parameters('appName')]",
        "apiVersion": "2015-08-01-preview",
        "location": "West US",
        "dependsOn": [ "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" ],
        "properties": { "serverFarmId": "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" },
        "resources": [ {
                "comments": "",
                "type": "config",
                "name": "web",
                "apiVersion": "2015-08-01",
                "location": "West US",
                "dependsOn": [ "[resourceId('Microsoft.Web/Sites', parameters('appName'))]" ],
                "properties": { "numberOfWorkers": "5" }
            } ]
        }]
}
```

## <a name="recommended-configuration-for-high-density-hosting"></a>Rekommenderad konfiguration för högdensitetshosting

Per appskalning är en funktion som är aktiverad i både globala Azure-regioner och [App Service-miljöer](environment/app-service-app-service-environment-intro.md). Den rekommenderade strategin är dock att använda App Service-miljöer för att dra nytta av deras avancerade funktioner och den större appserviceplankapaciteten.  

Så här konfigurerar du värdskap med hög densitet för dina appar:

1. Ange en App Service-plan som högdensitetsplan och skala ut den till önskad kapacitet.
1. Ange `PerSiteScaling` flaggan som true på App Service-planen.
1. Nya appar skapas och tilldelas apptjänstplanen med egenskapen **numberOfWorkers** set **1**.
   - Om du använder den här konfigurationen får du högsta möjliga densitet.
1. Antalet arbetare kan konfigureras oberoende av dem per app för att bevilja ytterligare resurser efter behov. Ett exempel:
   - En app med hög användning kan ange **antalArbetare till** **3** för att få mer bearbetningskapacitet för den appen.
   - Appar med låg användning anger **antaletArbetare** till **1**.

## <a name="next-steps"></a>Nästa steg

- [Översikt över Azure App Service-abonnemang på djupet](overview-hosting-plans.md)
- [Introduktion till App Service-miljöer](environment/app-service-app-service-environment-intro.md)
