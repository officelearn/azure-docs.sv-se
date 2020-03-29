---
title: Aktivera diagnostik i Azure Cloud Services med PowerShell | Microsoft-dokument
description: Lär dig hur du aktiverar diagnostik för molntjänster med PowerShell
services: cloud-services
documentationcenter: .net
author: tgore03
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.date: 09/06/2016
ms.author: tagore
ms.openlocfilehash: 76cdffed813fd182980b36f848e0ae42f3226539
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75386552"
---
# <a name="enable-diagnostics-in-azure-cloud-services-using-powershell"></a>Aktivera diagnostik i Azure Cloud Services med PowerShell
Du kan samla in diagnostikdata som programloggar, prestandaräknare etc. från en molntjänst med azure diagnostics-tillägget. I den här artikeln beskrivs hur du aktiverar Azure Diagnostics-tillägget för en molntjänst med PowerShell.  Se [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview) för de förutsättningar som krävs för den här artikeln.

## <a name="enable-diagnostics-extension-as-part-of-deploying-a-cloud-service"></a>Aktivera diagnostiktillägget som en del av distributionen av en molntjänst
Den här metoden är tillämplig på kontinuerlig integrering typ av scenarier, där diagnostiktillägget kan aktiveras som en del av distributionen av molntjänsten. När du skapar en ny Molntjänstdistribution kan du aktivera diagnostiktillägget genom att skicka in parametern *ExtensionConfiguration* till cmdleten [New-AzureDeployment.](/powershell/module/servicemanagement/azure/new-azuredeployment?view=azuresmps-3.7.0) Parametern *ExtensionConfiguration* tar en matris med diagnostikkonfigurationer som kan skapas med hjälp av [cmdleten New-AzureServiceDiagnosticsExtensionConfig.](/powershell/module/servicemanagement/azure/new-azureservicediagnosticsextensionconfig?view=azuresmps-3.7.0)

I följande exempel visas hur du kan aktivera diagnostik för en molntjänst med en WebRole och WorkerRole, som alla har en annan diagnostikkonfiguration.

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

Om konfigurationsfilen för diagnostik `StorageAccount` anger ett element med `New-AzureServiceDiagnosticsExtensionConfig` ett lagringskontonamn används det cmdlet automatiskt det lagringskontot. För att detta ska fungera måste lagringskontot vara i samma prenumeration som molntjänsten som distribueras.

Från Azure SDK 2.6 och framåt kommer de tilläggskonfigurationsfiler som genereras av MSBuild-publiceringsmålutdata att innehålla lagringskontonamnet baserat på konfigurationssträngen för diagnostik som anges i tjänstkonfigurationsfilen (.cscfg). Skriptet nedan visar hur du tolkar konfigurationsfilerna för tillägg från publiceringsmålutdata och konfigurerar diagnostiktillägg för varje roll när du distribuerar molntjänsten.

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

Visual Studio Online använder en liknande metod för automatisk distribution av molntjänster med diagnostiktillägget. Se [Publicera-AzureCloudDeployment.ps1](https://github.com/Microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureCloudPowerShellDeploymentV1/Publish-AzureCloudDeployment.ps1) för ett komplett exempel.

Om `StorageAccount` inget har angetts i diagnostikkonfigurationen måste du skicka in parametern *StorageAccountName* till cmdlet. Om parametern *StorageAccountName* har angetts använder cmdlet alltid det lagringskonto som anges i parametern och inte det som anges i konfigurationsfilen för diagnostik.

Om diagnostiklagringskontot finns i en annan prenumeration än molntjänsten måste du uttryckligen skicka in parametrarna *StorageAccountName* och *StorageAccountKey* till cmdleten. *Parametern StorageAccountKey* behövs inte när diagnostiklagringskontot finns i samma prenumeration, eftersom cmdleten automatiskt kan fråga och ange nyckelvärdet när diagnostiktillägget aktiveras. Men om diagnostiklagringskontot finns i en annan prenumeration kanske cmdleten inte kan hämta nyckeln automatiskt och du måste uttryckligen ange nyckeln via parametern *StorageAccountKey.*

```powershell
$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
```

## <a name="enable-diagnostics-extension-on-an-existing-cloud-service"></a>Aktivera diagnostiktillägg i en befintlig molntjänst
Du kan använda [cmdleten Set-AzureServiceDiagnosticsExtension](/powershell/module/servicemanagement/azure/set-azureservicediagnosticsextension?view=azuresmps-3.7.0) för att aktivera eller uppdatera diagnostikkonfigurationen på en molntjänst som redan körs.

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
Använd [cmdleten Get-AzureServiceDiagnosticsExtension](/powershell/module/servicemanagement/azure/get-azureservicediagnosticsextension?view=azuresmps-3.7.0) för att hämta den aktuella diagnostikkonfigurationen för en molntjänst.

```powershell
Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

## <a name="remove-diagnostics-extension"></a>Ta bort diagnostiktillägg
Om du vill inaktivera diagnostik på en molntjänst kan du använda [cmdleten Remove-AzureServiceDiagnosticsExtension.](/powershell/module/servicemanagement/azure/remove-azureservicediagnosticsextension?view=azuresmps-3.7.0)

```powershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

Om du har aktiverat diagnostiktillägget med hjälp av *Antingen Set-AzureServiceDiagnosticsExtension* eller *New-AzureServiceDiagnosticsExtensionConfig* utan rollparametern, kan du ta bort tillägget med *Ta bort AzureServiceDiagnosticsExtension* utan *parametern Roll.* *Role* Om *parametern Roll* användes när tillägget aktiverades måste den också användas när tillägget tas bort.

Så här tar du bort diagnostiktillägget från varje enskild roll:

```powershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"
```

## <a name="next-steps"></a>Efterföljande moment
* Mer information om hur du använder Azure-diagnostik och andra tekniker för att felsöka problem finns [i Aktivera diagnostik i Azure Cloud Services och Virtual Machines](cloud-services-dotnet-diagnostics.md).
* [Konfigurationsschema för diagnostik](/azure/azure-monitor/platform/diagnostics-extension-schema-1dot3) förklarar de olika xml-konfigurationsalternativen för diagnostiktillägget.
* Mer information om hur du aktiverar diagnostiktillägget för virtuella datorer finns i [Skapa en virtuell Windows-dator med övervakning och diagnostik med Azure Resource Manager-mall](../virtual-machines/windows/extensions-diagnostics-template.md)



