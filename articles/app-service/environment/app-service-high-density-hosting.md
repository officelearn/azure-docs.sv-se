---
title: "Hög densitet värd i Azure App Service | Microsoft Docs"
description: "Hög densitet värd i Azure App Service"
author: btardif
manager: erikre
editor: 
services: app-service\web
documentationcenter: 
ms.assetid: a903cb78-4927-47b0-8427-56412c4e3e64
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 06/12/2017
ms.author: byvinyal
ms.openlocfilehash: e6595c9f49e3b6303ad96c37d4ee5ebea37ce829
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="high-density-hosting-on-azure-app-service"></a>Hög densitet värd i Azure App Service
När du använder App Service, frikopplas programmet från den kapacitet som tilldelas av två begrepp:

* **Program:** representerar appen och dess runtime-konfiguration. Den innehåller till exempel versioner av .NET som körningen ska läsas in, app-inställningar.
* **Programtjänstplanen:** definierar egenskaperna för kapacitet, tillgängliga funktioner och ort för programmet. Egenskaper kan till exempel vara stora (fyra kärnor)-dator, fyra instanser, Premium-funktioner i östra USA.

En app alltid är kopplad till en App Service-plan, men en apptjänstplan kan ge kapacitet till en eller flera appar.

Därför ger plattformen flexibilitet att isolera en enda app eller har flera appar som delar resurser genom att dela en App Service-plan.

När flera appar delar en apptjänstplan, kör en instans av appen på varje förekomst av den App Service-planen.

## <a name="per-app-scaling"></a>Per app skalning
*Per app skalning* är en funktion som kan aktiveras på nivån för App Service-plan och sedan används per program.

Per app skalas skalning appen oberoende av App Service-plan som är värd för den. På så sätt kan en App Service-plan kan skalas till 10 instanser, men en app kan ställas in att använda bara fem.

   >[!NOTE]
   >Per app skalning är bara tillgängligt för **Premium** SKU App Service-planer
   >

### <a name="per-app-scaling-using-powershell"></a>Per app skalning med PowerShell

Du kan skapa en plan som konfigurerats som en *Per app skalning* plan genom att passera i den ```-perSiteScaling $true``` attribut till den ```New-AzureRmAppServicePlan``` cmdleten igen

```
New-AzureRmAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan `
                            -Location $Location `
                            -Tier Premium -WorkerSize Small `
                            -NumberofWorkers 5 -PerSiteScaling $true
```

Om du vill uppdatera en befintlig programtjänstplan att använda den här funktionen: 

- Hämta mål-plan```Get-AzureRmAppServicePlan```
- ändra egenskapen lokalt```$newASP.PerSiteScaling = $true```
- skicka ändringarna till azure```Set-AzureRmAppServicePlan``` 

```
# Get the new App Service Plan and modify the "PerSiteScaling" property.
$newASP = Get-AzureRmAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan
$newASP

#Modify the local copy to use "PerSiteScaling" property.
$newASP.PerSiteScaling = $true
$newASP
    
#Post updated app service plan back to azure
Set-AzureRmAppServicePlan $newASP
```

Vi behöver konfigurera antalet instanser som appen kan använda i app service-plan på app-nivå.

Appen är begränsad till två instanser oavsett hur många instanser underliggande apptjänstplan skalas ut till i exemplet nedan.

```
# Get the app we want to configure to use "PerSiteScaling"
$newapp = Get-AzureRmWebApp -ResourceGroupName $ResourceGroup -Name $webapp
    
# Modify the NumberOfWorkers setting to the desired value.
$newapp.SiteConfig.NumberOfWorkers = 2
    
# Post updated app back to azure
Set-AzureRmWebApp $newapp
```

> [!IMPORTANT]
> $newapp. SiteConfig.NumberOfWorkers är olika formulär $newapp. MaxNumberOfWorkers. Per app använder skalning $newapp. SiteConfig.NumberOfWorkers till bestämmer skala egenskaper för appen.

### <a name="per-app-scaling-using-azure-resource-manager"></a>Per app skalning med Azure Resource Manager

Följande *Azure Resource Manager-mall* skapar:

- En apptjänstplan skalas ut till 10 instanser
- en app som är konfigurerad att skala upp till högst fem instanser.

Programtjänstplanen inställningen i **PerSiteScaling** egenskap till true ```"perSiteScaling": true```. Appen inställningen i **antal arbetare** att använda till 5 ```"properties": { "numberOfWorkers": "5" }```.

```
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

## <a name="recommended-configuration-for-high-density-hosting"></a>Rekommenderad konfiguration för hög densitet värd
Är en funktion som är aktiverad i både globala Azure-regioner och Apptjänstmiljöer per app skalning. Den rekommenderade strategin är dock att använda Apptjänstmiljöer för att dra nytta av de avancerade funktionerna och större pooler med kapacitet.  

Följ dessa steg om du vill konfigurera hög densitet som värd för dina appar:

1. Konfigurera Apptjänst-miljön och välj en arbetspool som är dedikerad till hög densitet värd scenariot.
1. Skapa en enkel App Service-plan och skala och använda den tillgängliga kapaciteten i poolen worker.
1. Ställ in flaggan PerSiteScaling på true för App Service-plan.
1. Nya appar skapas och tilldelas till den App Service-planen med de **numberOfWorkers** egenskapen **1**. Med den här konfigurationen ger högsta densiteten möjliga i den här arbetspool.
1. Antalet arbetare kan konfigureras oberoende per app för att bevilja ytterligare resurser som behövs. Exempel:
    - En hög användning app kan ange **numberOfWorkers** till **3** har flera bearbetningskapacitet för appen. 
    - Använd låg appar skulle ange **numberOfWorkers** till **1**.

## <a name="next-steps"></a>Nästa steg

- [Azure App Service-planer djupgående översikt](../azure-web-sites-web-hosting-plans-in-depth-overview.md)
- [Introduktion till App Service-miljöer](app-service-app-service-environment-intro.md)