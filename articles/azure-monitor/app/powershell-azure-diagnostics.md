---
title: Ställa in Application Insights i en Azure med hjälp av PowerShell | Microsoft Docs
description: Automatisera konfiguration av Azure-diagnostik för att skicka pipe-data till Application Insights.
ms.topic: conceptual
ms.date: 08/06/2019
ms.openlocfilehash: da1796c8af5b9463d8223615f4b0629ba65eb3e8
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77669811"
---
# <a name="using-powershell-to-set-up-application-insights-for-azure-cloud-services"></a>Konfigurera Application Insights för Azure-Cloud Services med hjälp av PowerShell

[Microsoft Azure](https://azure.com) kan [konfigureras att skicka Azure Diagnostics-data](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md) till [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md). Diagnostiken gäller Azure Cloud Services och virtuella datorer i Azure. De kompletterar telemetrin som du skickar inifrån appen med hjälp av Application Insights SDK. Som en del av automatiseringen av processen för att skapa nya resurser i Azure kan du konfigurera diagnostik med hjälp av PowerShell.

## <a name="azure-template"></a>Azure-mall
Om webbappen finns i Azure och du skapar dina resurser med hjälp av en Azure Resource Manager-mall kan du konfigurera Application Insights genom att lägga till följande till resursnoden:

    {
      resources: [
        /* Create Application Insights resource */
        {
          "apiVersion": "2015-05-01",
          "type": "microsoft.insights/components",
          "name": "nameOfAIAppResource",
          "location": "centralus",
          "kind": "web",
          "properties": { "ApplicationId": "nameOfAIAppResource" },
          "dependsOn": [
            "[concat('Microsoft.Web/sites/', myWebAppName)]"
          ]
        }
       ]
     } 

* `nameOfAIAppResource` – ett namn för Application Insights-resursen
* `myWebAppName`-webbappens ID

## <a name="enable-diagnostics-extension-as-part-of-deploying-a-cloud-service"></a>Aktivera diagnostiktillägget som en del av distributionen av en molntjänst
`New-AzureDeployment`-cmdleten har en parameter, `ExtensionConfiguration`, som stöder en rad diagnostikkonfigurationer. Dessa kan skapas med hjälp av cmdleten `New-AzureServiceDiagnosticsExtensionConfig`. Några exempel:

```ps

    $service_package = "CloudService.cspkg"
    $service_config = "ServiceConfiguration.Cloud.cscfg"
    $diagnostics_storagename = "myservicediagnostics"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

    $primary_storagekey = (Get-AzStorageKey `
     -StorageAccountName "$diagnostics_storagename").Primary
    $storage_context = New-AzStorageContext `
       -StorageAccountName $diagnostics_storagename `
       -StorageAccountKey $primary_storagekey

    $webrole_diagconfig = `
     New-AzureServiceDiagnosticsExtensionConfig `
      -Role "WebRole" -Storage_context $storageContext `
      -DiagnosticsConfigurationPath $webrole_diagconfigpath
    $workerrole_diagconfig = `
     New-AzureServiceDiagnosticsExtensionConfig `
      -Role "WorkerRole" `
      -StorageContext $storage_context `
      -DiagnosticsConfigurationPath $workerrole_diagconfigpath

    New-AzureDeployment `
      -ServiceName $service_name `
      -Slot Production `
      -Package $service_package `
      -Configuration $service_config `
      -ExtensionConfiguration @($webrole_diagconfig,$workerrole_diagconfig)

``` 

## <a name="enable-diagnostics-extension-on-an-existing-cloud-service"></a>Aktivera diagnostiktillägg i en befintlig molntjänst
I en befintlig tjänst använder du `Set-AzureServiceDiagnosticsExtension`.

```ps

    $service_name = "MyService"
    $diagnostics_storagename = "myservicediagnostics"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"
    $primary_storagekey = (Get-AzStorageKey `
         -StorageAccountName "$diagnostics_storagename").Primary
    $storage_context = New-AzStorageContext `
        -StorageAccountName $diagnostics_storagename `
        -StorageAccountKey $primary_storagekey

    Set-AzureServiceDiagnosticsExtension `
        -StorageContext $storage_context `
        -DiagnosticsConfigurationPath $webrole_diagconfigpath `
        -ServiceName $service_name `
        -Slot Production `
        -Role "WebRole" 
    Set-AzureServiceDiagnosticsExtension `
        -StorageContext $storage_context `
        -DiagnosticsConfigurationPath $workerrole_diagconfigpath `
        -ServiceName $service_name `
        -Slot Production `
        -Role "WorkerRole"
```

## <a name="get-current-diagnostics-extension-configuration"></a>Hämta den aktuella konfigurationen för diagnostiktillägg
```ps

    Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```


## <a name="remove-diagnostics-extension"></a>Ta bort diagnostiktillägg
```ps

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

Om du aktiverade diagnostiktillägget med `Set-AzureServiceDiagnosticsExtension` eller `New-AzureServiceDiagnosticsExtensionConfig` utan rollparametern så kan du ta bort tillägget med hjälp av `Remove-AzureServiceDiagnosticsExtension` utan rollparametern. Om rollparametern användes när du aktiverade tillägget måste den också användas när du tar bort tillägget.

Så här tar du bort diagnostiktillägget från varje enskild roll:

```ps

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"
```


## <a name="see-also"></a>Se även
* [Övervaka Azure Cloud Services-appar med Application Insights](../../azure-monitor/app/cloudservices.md)
* [Skicka Azure Diagnostics-data till Application Insights](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md)
* [Automatisera konfigurationen av aviseringar](powershell-alerts.md)

