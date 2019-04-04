---
title: Aktivera diagnostik i Azure Cloud Services med hjälp av PowerShell | Microsoft Docs
description: Lär dig hur du aktiverar diagnostik för cloud services med hjälp av PowerShell
services: cloud-services
documentationcenter: .net
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 66e08754-8639-4022-ae18-4237749ba17d
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 09/06/2016
ms.author: jeconnoc
ms.openlocfilehash: b20fa7a1f43369cde85c2535637eec7ceb1d3c29
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/04/2019
ms.locfileid: "58918339"
---
# <a name="enable-diagnostics-in-azure-cloud-services-using-powershell"></a>Aktivera diagnostik i Azure Cloud Services med hjälp av PowerShell
Du kan samla in diagnostikdata som Programloggar, prestandaräknare osv från en molntjänst med Azure Diagnostics-tillägget. Den här artikeln beskriver hur du aktiverar Azure Diagnostics-tillägget för en molntjänst med hjälp av PowerShell.  Se [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview) för kraven för den här artikeln.

## <a name="enable-diagnostics-extension-as-part-of-deploying-a-cloud-service"></a>Aktivera diagnostiktillägget som en del av distributionen av en molntjänst
Den här metoden kan användas för kontinuerlig integrering typ av scenarier, där du kan aktivera diagnostiktillägget som en del av distributionen av Molntjänsten. När du skapar en ny molntjänst-distribution kan du aktivera diagnostics-tillägg genom att skicka in den *ExtensionConfiguration* parametern till den [New AzureDeployment](/powershell/module/servicemanagement/azure/new-azuredeployment?view=azuresmps-3.7.0) cmdlet. Den *ExtensionConfiguration* parametern tar en rad diagnostikkonfigurationer som kan skapas med hjälp av den [New AzureServiceDiagnosticsExtensionConfig](/powershell/module/servicemanagement/azure/new-azureservicediagnosticsextensionconfig?view=azuresmps-3.7.0) cmdlet.

I följande exempel visas hur du kan aktivera diagnostik för en molntjänst med en WebRole och WorkerRole, var och en med en annan diagnostik-konfiguration.

```powershell
$service_name = "MyService"
$service_package = "CloudService.cspkg"
$service_config = "ServiceConfiguration.Cloud.cscfg"
$webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml"
$workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath

New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration @($webrole_diagconfig,$workerrole_diagconfig)
```

Om konfigurationsfilen diagnostik anger en `StorageAccount` element med ett lagringskontonamn och sedan den `New-AzureServiceDiagnosticsExtensionConfig` cmdleten ska använda det lagringskontot automatiskt. Lagringskontot måste finnas i samma prenumeration som Molntjänsten som distribueras för detta ska fungera.

Från Azure SDK 2.6 och uppåt tillägget konfigurationsfilerna som genereras av MSBuild publicera innehåller utdata lagringskontonamn baserat på diagnostik configuration strängen som anges i tjänstekonfigurationsfilen (.cscfg). Skriptet nedan visar hur du parsa tillägget configuration-filer från publicera target utdata och konfigurera diagnostiktillägget för varje roll vid distribution av Molntjänsten.

```powershell
$service_name = "MyService"
$service_package = "C:\build\output\CloudService.cspkg"
$service_config = "C:\build\output\ServiceConfiguration.Cloud.cscfg"

#Find the Extensions path based on service configuration file
$extensionsSearchPath = Join-Path -Path (Split-Path -Parent $service_config) -ChildPath "Extensions"

$diagnosticsExtensions = Get-ChildItem -Path $extensionsSearchPath -Filter "PaaSDiagnostics.*.PubConfig.xml"
$diagnosticsConfigurations = @()
foreach ($extPath in $diagnosticsExtensions)
{
    #Find the RoleName based on file naming convention PaaSDiagnostics.<RoleName>.PubConfig.xml
    $roleName = ""
    $roles = $extPath -split ".",0,"simplematch"
    if ($roles -is [system.array] -and $roles.Length -gt 1)
    {
        $roleName = $roles[1]
        $x = 2
        while ($x -le $roles.Length)
            {
               if ($roles[$x] -ne "PubConfig")
                {
                    $roleName = $roleName + "." + $roles[$x]
                }
                else
                {
                    break
                }
                $x++
            }
        $fullExtPath = Join-Path -path $extensionsSearchPath -ChildPath $extPath
        $diagnosticsconfig = New-AzureServiceDiagnosticsExtensionConfig -Role $roleName -DiagnosticsConfigurationPath $fullExtPath
        $diagnosticsConfigurations += $diagnosticsconfig
    }
}
New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration $diagnosticsConfigurations
```

Visual Studio Online använder ett liknande tillvägagångssätt för automatiserade distributioner av Cloud Services med diagnostics-tillägg. Se [publicera AzureCloudDeployment.ps1](https://github.com/Microsoft/vso-agent-tasks/blob/master/Tasks/AzureCloudPowerShellDeployment/Publish-AzureCloudDeployment.ps1) exempel.

Om ingen `StorageAccount` har angetts i diagnostikkonfigurationen måste du skicka in den *StorageAccountName* parameter till cmdleten. Om den *StorageAccountName* parametern anges, kommer cmdleten använder alltid det lagringskonto som anges i parametern och inte det som har angetts i konfigurationsfilen för diagnostik.

Om lagringskontot för gatewaydiagnostik är i en annan prenumeration från Molntjänsten, måste du explicit skicka i den *StorageAccountName* och *StorageAccountKey* parametrar för cmdleten. Den *StorageAccountKey* parametern behövs inte när diagnostiklagringskonto är i samma prenumeration, vilket cmdleten automatiskt kan fråga efter och ange nyckelvärdet när du aktiverar diagnostics-tillägg. Men om lagringskonto för startdiagnostik finns i en annan prenumeration och sedan cmdlet: en kanske inte kan hämta nyckel automatiskt och du måste uttryckligen ange nyckel via den *StorageAccountKey* parametern.

```powershell
$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
```

## <a name="enable-diagnostics-extension-on-an-existing-cloud-service"></a>Aktivera diagnostiktillägg i en befintlig molntjänst
Du kan använda den [Set-AzureServiceDiagnosticsExtension](/powershell/module/servicemanagement/azure/set-azureservicediagnosticsextension?view=azuresmps-3.7.0) cmdlet för att aktivera eller uppdatera konfigurationen för diagnostik på en molnbaserad tjänst som körs redan.

[!INCLUDE [cloud-services-wad-warning](../../includes/cloud-services-wad-warning.md)]

```powershell
$service_name = "MyService"
$webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml"
$workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath

Set-AzureServiceDiagnosticsExtension -DiagnosticsConfiguration @($webrole_diagconfig,$workerrole_diagconfig) -ServiceName $service_name
```

## <a name="get-current-diagnostics-extension-configuration"></a>Hämta den aktuella konfigurationen för diagnostiktillägg
Använd den [Get-AzureServiceDiagnosticsExtension](/powershell/module/servicemanagement/azure/get-azureservicediagnosticsextension?view=azuresmps-3.7.0) cmdlet för att hämta den aktuella diagnostikkonfigurationen för en tjänst i molnet.

```powershell
Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

## <a name="remove-diagnostics-extension"></a>Ta bort diagnostiktillägg
Inaktivera diagnostik på en molnbaserad tjänst som du kan använda den [Remove-AzureServiceDiagnosticsExtension](/powershell/module/servicemanagement/azure/remove-azureservicediagnosticsextension?view=azuresmps-3.7.0) cmdlet.

```powershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

Om du har aktiverat diagnostiktillägget med *Set-AzureServiceDiagnosticsExtension* eller *New-AzureServiceDiagnosticsExtensionConfig* utan den *rollen*parametern och du kan ta bort tillägget med hjälp av *Remove-AzureServiceDiagnosticsExtension* utan den *rollen* parametern. Om den *rollen* parametern användes för att aktivera tillägget och det måste också användas när du tar bort tillägget.

Så här tar du bort diagnostiktillägget från varje enskild roll:

```powershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"
```

## <a name="next-steps"></a>Nästa steg
* Mer information om hur du använder Azure-diagnostik och andra metoder för att felsöka problem finns i [hur du aktiverar diagnostik i Azure Cloud Services och virtuella datorer](cloud-services-dotnet-diagnostics.md).
* Den [diagnostik konfigurationsschema](/azure/azure-monitor/platform/diagnostics-extension-schema-1dot2) beskriver de olika alternativen för diagnostiktillägget gäller xml-konfigurationer.
* Läs hur du aktiverar diagnostics-tillägg för virtuella datorer i [skapa en virtuell Windows-dator med övervakning och diagnostik med Azure Resource Manager-mall](../virtual-machines/windows/extensions-diagnostics-template.md)
