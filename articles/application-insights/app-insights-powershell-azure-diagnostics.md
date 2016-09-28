<properties
    pageTitle="Skicka Azure Diagnostics-data till Application Insights med hjälp av PowerShell | Microsoft Azure"
    description="Automatisera överföringen av Azure Diagnostics-data till Application Insights."
    services="application-insights"
    documentationCenter=".net"
    authors="sbtron"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="11/17/2015"
    ms.author="awills"/>


# Skicka Azure Diagnostics-data till Application Insights med hjälp av PowerShell

[Microsoft Azure](https://azure.com) kan [konfigureras att skicka Azure Diagnostics-data](app-insights-azure-diagnostics.md) till [Visual Studio Application Insights](app-insights-overview.md). Diagnostiken gäller Azure Cloud Services och virtuella datorer i Azure. De kompletterar telemetrin som du skickar inifrån appen med hjälp av Application Insights SDK. Som en del av automatiseringen av processen för att skapa nya resurser i Azure kan du konfigurera diagnostik med hjälp av PowerShell.

## Aktivera diagnostiktillägget som en del av distributionen av en molntjänst

`New-AzureDeployment`-cmdleten har en parameter, `ExtensionConfiguration`, som stöder en rad diagnostikkonfigurationer. Dessa kan skapas med hjälp av cmdleten `New-AzureServiceDiagnosticsExtensionConfig`. Till exempel:

```ps

    $service_package = "CloudService.cspkg"
    $service_config = "ServiceConfiguration.Cloud.cscfg"
    $diagnostics_storagename = "myservicediagnostics"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

    $primary_storagekey = (Get-AzureStorageKey `
     -StorageAccountName "$diagnostics_storagename").Primary
    $storage_context = New-AzureStorageContext `
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

## Aktivera diagnostiktillägg i en befintlig molntjänst

I en befintlig tjänst använder du `Set-AzureServiceDiagnosticsExtension`.

```ps
 
    $service_name = "MyService"
    $diagnostics_storagename = "myservicediagnostics"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"
    $primary_storagekey = (Get-AzureStorageKey `
         -StorageAccountName "$diagnostics_storagename").Primary
    $storage_context = New-AzureStorageContext `
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

## Hämta den aktuella konfigurationen för diagnostiktillägg

```ps

    Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```


## Ta bort diagnostiktillägg

```ps

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

Om du aktiverade diagnostiktillägget med `Set-AzureServiceDiagnosticsExtension` eller `New-AzureServiceDiagnosticsExtensionConfig` utan rollparametern så kan du ta bort tillägget med hjälp av `Remove-AzureServiceDiagnosticsExtension` utan rollparametern. Om rollparametern användes när du aktiverade tillägget måste den också användas när du tar bort tillägget.

Så här tar du bort diagnostiktillägget från varje enskild roll:

```ps

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"
```


## Se även

* [Övervaka Azure Cloud Services-appar med Application Insights](app-insights-cloudservices.md)
* [Skicka Azure Diagnostics-data till Application Insights](app-insights-azure-diagnostics.md)
* [Automatisera konfigurationen av aviseringar](app-insights-powershell-alerts.md)




<!--HONumber=Sep16_HO3-->


