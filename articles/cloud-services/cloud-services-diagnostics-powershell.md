---
title: "Aktivera diagnostik i Azure Cloud Services med hjälp av PowerShell | Microsoft Docs"
description: "Lär dig hur du aktiverar diagnostik för molntjänster med hjälp av PowerShell"
services: cloud-services
documentationcenter: .net
author: Thraka
manager: timlt
editor: 
ms.assetid: 66e08754-8639-4022-ae18-4237749ba17d
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 09/06/2016
ms.author: adegeo
ms.openlocfilehash: 8dd9724981860c9cd4ccc443cc2bfdc465811e7c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="enable-diagnostics-in-azure-cloud-services-using-powershell"></a>Aktivera diagnostik i Azure Cloud Services med hjälp av PowerShell
Du kan samla in diagnostikdata som programloggarna, prestandaräknare etc. från en tjänst i molnet med Azure-diagnostik. Den här artikeln beskriver hur du aktiverar tillägget för Azure-diagnostik för en tjänst i molnet med hjälp av PowerShell.  Se [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview) för kraven för den här artikeln.

## <a name="enable-diagnostics-extension-as-part-of-deploying-a-cloud-service"></a>Aktivera diagnostiktillägget som en del av distributionen av en molntjänst
Den här metoden kan användas för kontinuerlig Integrationstyp av scenarier där tillägget diagnostics kan aktiveras som en del av distribution av Molntjänsten. När du skapar en ny molntjänst distribution du kan aktivera diagnostik-tillägg genom att passera i den *ExtensionConfiguration* parametern till den [ny AzureDeployment](/powershell/module/azure/new-azuredeployment?view=azuresmps-3.7.0) cmdlet. Den *ExtensionConfiguration* parameter tar en matris av diagnostik-konfigurationer som kan skapas med hjälp av den [ny AzureServiceDiagnosticsExtensionConfig](/powershell/module/azure/new-azureservicediagnosticsextensionconfig?view=azuresmps-3.7.0) cmdlet.

I följande exempel visas hur du kan aktivera diagnostik för en molnbaserad tjänst med en WebRole och WorkerRole, var och en med en annan diagnostik-konfiguration.

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

Om konfigurationsfilen diagnostik anger en `StorageAccount` element med namnet på ett lagringskonto, sedan `New-AzureServiceDiagnosticsExtensionConfig` cmdlet använder automatiskt det storage-kontot. Lagringskontot måste vara i samma prenumeration som den molntjänst som distribueras för detta ska fungera.

Från Azure SDK 2.6 och framåt tillägget konfigurationsfilerna som genereras av MSBuild publicera innehåller utdata lagringskontots namn baserat på diagnostik configuration strängen som anges i tjänstekonfigurationsfilen (.cscfg). Skriptet nedan visar hur du tolka konfigurationsfiler tillägget från den publish utdatan och konfigurera diagnostik för varje roll vid distribution av Molntjänsten.

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

Visual Studio Online använder ett liknande sätt för automatisk distribution av molntjänster med filnamnstillägget diagnostik. Se [publicera AzureCloudDeployment.ps1](https://github.com/Microsoft/vso-agent-tasks/blob/master/Tasks/AzureCloudPowerShellDeployment/Publish-AzureCloudDeployment.ps1) för en komplett exempel.

Om inget `StorageAccount` har angetts i diagnostik-konfigurationen måste du skicka in den *StorageAccountName* parameter för cmdlet. Om den *StorageAccountName* parametern anges, kommer cmdleten använder alltid storage-konto som har angetts i parametern och inte det som har angetts i konfigurationsfilen diagnostik.

Om diagnostik storage-konto är i en annan prenumeration från Molntjänsten, måste du uttryckligen skicka in den *StorageAccountName* och *StorageAccountKey* parametrar för cmdleten. Den *StorageAccountKey* parametern behövs inte när diagnostiklagringskonto är i samma prenumeration som cmdleten automatiskt kan fråga och ange värdet för nyckeln när du aktiverar tillägget diagnostik. Om lagringskontot för gatewaydiagnostik är i en annan prenumeration sedan cmdleten kanske inte går att hämta nyckeln automatiskt och måste du uttryckligen ange nyckel via den *StorageAccountKey* parameter.

```powershell
$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
```

## <a name="enable-diagnostics-extension-on-an-existing-cloud-service"></a>Aktivera diagnostiktillägg i en befintlig molntjänst
Du kan använda den [Set AzureServiceDiagnosticsExtension](/powershell/module/azure/set-azureservicediagnosticsextension?view=azuresmps-3.7.0) för att aktivera eller uppdatera diagnostik konfigurationen på en molntjänst som redan körs.

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
Använd den [Get-AzureServiceDiagnosticsExtension](/powershell/module/azure/get-azureservicediagnosticsextension?view=azuresmps-3.7.0) för att hämta den aktuella diagnostik-konfigurationen för en tjänst i molnet.

```powershell
Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

## <a name="remove-diagnostics-extension"></a>Ta bort diagnostiktillägg
Inaktivera diagnostik på en molnbaserad tjänst som du kan använda den [ta bort AzureServiceDiagnosticsExtension](/powershell/module/azure/remove-azureservicediagnosticsextension?view=azuresmps-3.7.0) cmdlet.

```powershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

Om du har aktiverat diagnostik-tillägget med hjälp av antingen *Set AzureServiceDiagnosticsExtension* eller *ny AzureServiceDiagnosticsExtensionConfig* utan den *rollen* parameter och du kan ta bort tillägget med hjälp av *ta bort AzureServiceDiagnosticsExtension* utan den *rollen* parameter. Om den *rollen* parametern används när du aktiverar tillägget och den måste användas när du tar bort tillägget.

Så här tar du bort diagnostiktillägget från varje enskild roll:

```powershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"
```

## <a name="next-steps"></a>Nästa steg
* Mer information om hur du använder Azure-diagnostik och andra tekniker för att felsöka problem finns [aktiverar diagnostik i Azure-molntjänster och virtuella datorer](cloud-services-dotnet-diagnostics.md).
* Den [diagnostik Konfigurationsschemat](https://msdn.microsoft.com/library/azure/dn782207.aspx) förklarar alternativen olika XML-konfigurationer för diagnostik-tillägg.
* Information om hur du aktiverar tillägget diagnostik för virtuella datorer finns [skapa en Windows virtuell dator med övervakning och diagnostik med Azure Resource Manager-mall](../virtual-machines/windows/extensions-diagnostics-template.md)
