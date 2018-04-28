---
title: Tillägg för virtuell dator och funktioner för Windows i Azure | Microsoft Docs
description: Lär dig vilka tillägg som är tillgängliga för virtuella Azure-datorer, grupperade efter vad de tillhandahålla och förbättra.
services: virtual-machines-windows
documentationcenter: ''
author: danielsollondon
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 999d63ee-890e-432e-9391-25b3fc6cde28
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/06/2017
ms.author: danis
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 09eb2e723be80fd623ad53c9bda8271ef8846a0f
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="virtual-machine-extensions-and-features-for-windows"></a>Tillägg för virtuell dator och funktioner i Windows

Tillägg för virtuell dator i Azure är små program som innehåller efter distributionen konfiguration och automatisering av uppgifter på virtuella Azure-datorer. Om en virtuell dator kräver installation av programvara, anti-virusskydd eller Docker-konfiguration, kan en VM-tillägget användas utföra dessa uppgifter. Azure VM-tillägg kan köras med hjälp av Azure CLI, PowerShell, Azure Resource Manager-mallar och Azure-portalen. Tillägg kan levereras tillsammans med en ny distribution av virtuella datorer eller köra mot alla befintliga system.

Det här dokumentet innehåller en översikt över virtuella tillägg, krav för att använda tillägg för virtuell dator och vägledning om hur du identifiera, hantera och ta bort tillägg för virtuell dator. Det här dokumentet innehåller allmänna informationen eftersom många VM-tillägg är tillgängligt, var och en med en potentiellt unik konfiguration. Tillägget-specifik information finns i varje dokument specifika för enskilda tillägg.

## <a name="use-cases-and-samples"></a>Användningsfall och exempel

Det finns många olika Virtuella Azure-tillägg, var och en med en specifik användningsfall. Några exempel på användningsområden är:

- Använd PowerShell önskade tillstånd konfigurationer till en virtuell dator med hjälp av DSC-tillägg för Windows. Mer information finns i [tillägg för konfiguration av Azure önskade tillstånd](extensions-dsc-overview.md).
- Konfigurera övervakning av virtuella datorer med hjälp av Microsoft Monitoring Agent VM-tillägget. Mer information finns i [ansluta Azure virtuella datorer till logganalys](../../log-analytics/log-analytics-azure-vm-extension.md).
- Konfigurera övervakning av Azure-infrastrukturen med filnamnstillägget Datadog. Mer information finns i [Datadog blogg](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/).
- Konfigurera en virtuell Azure-dator med hjälp av Chef. Mer information finns i [automatisera Azure distribution av virtuella datorer med Chef](chef-automation.md).

Förutom processpecifika tillägg är ett tillägg för anpassat skript tillgängligt för både Windows och Linux virtuella datorer. Tillägget för anpassat skript för Windows kan alla PowerShell.skript ska köras på en virtuell dator. Detta är användbart när du skapar Azure-distributioner som kräver konfiguration efter vilken interna Azure verktygsuppsättning kan ge. Mer information finns i [tillägget Windows VM anpassade skript](extensions-customscript.md).


## <a name="prerequisites"></a>Förutsättningar

Varje tillägg för virtuell dator kan ha en egen uppsättning krav. Docker VM-tillägget har till exempel en förutsättning för en Linux-fördelning som stöds. Krav för enskilda tillägg beskrivs i dokumentationen för tillägget-specifika.

### <a name="azure-vm-agent"></a>Virtuell Azure-datoragent
Virtuella Azure-agenten hanterar interaktionen mellan en virtuell Azure-dator och Azure-infrastrukturkontrollanten. Den Virtuella datoragenten är ansvarig för många funktioner för att distribuera och hantera virtuella Azure-datorer, inklusive kör VM-tillägg. Virtuella Azure-agenten är förinstallerat på Azure Marketplace-bilder och kan installeras på operativsystem som stöds.

Information om operativsystem som stöds och installationsanvisningar finns [virtuella Azure-datorn agent](agent-user-guide.md).

## <a name="discover-vm-extensions"></a>Identifiera VM-tillägg
Det finns många olika VM-tillägg för användning med virtuella Azure-datorer. Kör följande kommando om du vill se en fullständig lista med Azure Resource Manager PowerShell-modulen. Se till att ange önskad plats när du kör det här kommandot.

```powershell
Get-AzureRmVmImagePublisher -Location WestUS | `
Get-AzureRmVMExtensionImageType | `
Get-AzureRmVMExtensionImage | Select Type, Version
```

## <a name="run-vm-extensions"></a>Kör VM-tillägg

Tillägg för virtuell Azure-dator kan köras på den befintliga virtuella datorer, vilket är användbart när du behöver göra konfigurationsändringar eller återställa anslutningen på en redan distribuerad virtuell dator. VM-tillägg kan också tillsammans med Azure Resource Manager mall-distributioner. Genom att använda tillägg med Resource Manager-mallar kan aktivera du virtuella Azure-datorer ska kunna distribueras och konfigurerats utan att behöva åtgärder efter distributionen.

Följande metoder kan användas för att köra ett tillägg mot en befintlig virtuell dator.

### <a name="powershell"></a>PowerShell

Det finns flera PowerShell-kommandon för att köra enskilda tillägg. Kör följande PowerShell-kommandon för att visa en lista.

```powershell
get-command Set-AzureRM*Extension* -Module AzureRM.Compute
```

Detta ger utdata som liknar följande:

```powershell
CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Set-AzureRmVMAccessExtension                       2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMADDomainExtension                     2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMAEMExtension                          2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMBackupExtension                       2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMBginfoExtension                       2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMChefExtension                         2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMCustomScriptExtension                 2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMDiagnosticsExtension                  2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMDiskEncryptionExtension               2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMDscExtension                          2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMExtension                             2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMSqlServerExtension                    2.2.0      AzureRM.Compute
```

I följande exempel använder tillägget för anpassat skript för att hämta ett skript från en GitHub-databas på den virtuella måldatorn och kör skriptet. Mer information om tillägget för anpassat skript finns [översikt över tillägget för anpassat skript](extensions-customscript.md).

```powershell
Set-AzureRmVMCustomScriptExtension -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" -Name "myCustomScript" `
    -FileUri "https://raw.githubusercontent.com/neilpeterson/nepeters-azure-templates/master/windows-custom-script-simple/support-scripts/Create-File.ps1" `
    -Run "Create-File.ps1" -Location "West US"
```

I det här exemplet används tillägget för virtuell dator åtkomst för att återställa lösenordet för administratörer för en virtuell dator i Windows. Mer information om tillägget för virtuell dator åtkomst finns [Återställ Fjärrskrivbordstjänsten i en Windows VM](reset-rdp.md).

```powershell
$cred=Get-Credential

Set-AzureRmVMAccessExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myVMAccess" `
    -Location WestUS -UserName $cred.GetNetworkCredential().Username `
    -Password $cred.GetNetworkCredential().Password -typeHandlerVersion "2.0"
```

Den `Set-AzureRmVMExtension` kommando kan användas för att starta alla VM-tillägg. Mer information finns i [Set AzureRmVMExtension referens](https://msdn.microsoft.com/library/mt603745.aspx).


### <a name="azure-portal"></a>Azure Portal

Ett VM-tillägg kan tillämpas på en befintlig virtuell dator via Azure-portalen. Om du vill göra det, väljer du den virtuella datorn som du vill använda, Välj **tillägg**, och klicka på **Lägg till**. Detta visar en lista över tillgängliga tillägg. Välj den du vill använda och följ stegen i guiden.

Följande bild visar installationen av tillägget Microsoft Antimalware från Azure-portalen.

![Installera tillägg för program mot skadlig kod](./media/extensions-features/installantimalwareextension.png)

### <a name="azure-resource-manager-templates"></a>Azure Resource Manager-mallar

VM-tillägg kan läggas till i en Azure Resource Manager-mall och utförs med distributionen av mallen. Distribuera tillägg med en mall är användbart för att skapa helt konfigurerade Azure-distributioner. Till exempel hämtas följande JSON från en Resource Manager-mall som distribuerar en uppsättning belastningsutjämnade virtuella datorer och en Azure SQL database och installerar sedan en .NET Core-programmet på varje virtuell dator. Tillägg för virtuell dator hand tar om installationen av programmet.

Mer information finns i [fullständig Resource Manager-mall](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows).

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.4",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

Mer information finns i [redigera Azure Resource Manager-mallar med Windows VM-tillägg](template-description.md#extensions).

## <a name="secure-vm-extension-data"></a>Säkra data för VM-tillägg

När du kör en VM-tillägget måste vara det nödvändigt att inkludera känslig information, till exempel autentiseringsuppgifter, lagringskontonamn och åtkomst för lagringskontonycklar. Många VM-tillägg innehåller en skyddad konfiguration som krypterar data och dekrypterar dem bara inuti den virtuella måldatorn. Varje tillägg har en specifik skyddade konfigurationsschema som ska anges i tillägget specifika-dokumentationen.

I följande exempel visas en instans av tillägget för anpassat skript för Windows. Observera att kommandot ska köras innehåller en uppsättning autentiseringsuppgifter. I det här exemplet krypteras inte kommandot att köras.


```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.4",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ],
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

Säker körning av strängen genom att flytta den **kommando att köra** egenskapen till den **skyddade** konfiguration.

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.4",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

## <a name="troubleshoot-vm-extensions"></a>Felsökning av VM-tillägg

Varje VM-tillägg kan ha specifika felsökningsanvisningar. Exempelvis när du använder tillägget för anpassat skript finns körning skriptdetaljer lokalt på den virtuella datorn där tillägget kördes. Tillägget-specifika felsökning beskrivs i dokumentationen för tillägget-specifika.

Följande felsökningssteg gäller för alla tillägg för virtuell dator.

### <a name="view-extension-status"></a>Visa status för tillägg

När ett tillägg för virtuell dator har körts mot en virtuell dator, kan du använda följande PowerShell-kommando Tilläggsstatus ska returneras. Ersätt exempel parameternamn med egna värden. Den `Name` parameter tar namnet på filnamnstillägget vid körning.

```PowerShell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

Det ser ut som följande utdata:

```json
ResourceGroupName       : myResourceGroup
VMName                  : myVM
Name                    : myExtensionName
Location                : westus
Etag                    : null
Publisher               : Microsoft.Azure.Extensions
ExtensionType           : DockerExtension
TypeHandlerVersion      : 1.0
Id                      : /subscriptions/mySubscriptionIS/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM/extensions/myExtensionName
PublicSettings          :
ProtectedSettings       :
ProvisioningState       : Succeeded
Statuses                :
SubStatuses             :
AutoUpgradeMinorVersion : False
ForceUpdateTag          :
```

Tillståndets status är körningen kan också finnas i Azure-portalen. Om du vill visa status för ett tillägg markerar du den virtuella datorn, väljer **tillägg**, och välj önskad tillägget.

### <a name="rerun-vm-extensions"></a>Kör VM-tillägg

Det kan finnas fall där tillägget för virtuell dator behöver köras igen. Du kan göra detta genom att ta bort tillägget och sedan köra tillägget med en körning metod du föredrar. Kör följande kommando för att ta bort ett tillägg med Azure PowerShell-modulen. Ersätt exempel parameternamn med egna värden.

```powershell
Remove-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

Ett tillägg kan också tas bort med hjälp av Azure portal. Gör så här:

1. Välj en virtuell dator.
2. Välj **tillägg**.
3. Välj önskad tillägg.
4. Välj **avinstallera**.

## <a name="common-vm-extensions-reference"></a>Benämningen för VM-tillägg
| Tilläggsnamn | Beskrivning | Mer information |
| --- | --- | --- |
| Tillägget för anpassat skript för Windows |Kör skript mot en virtuell Azure-dator |[Tillägget för anpassat skript för Windows](extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| DSC-tillägg för Windows |PowerShell DSC (Desired State Configuration)-tillägg |[DSC-tillägg för Windows](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Azure Diagnostics-tillägg |Hantera Azure-diagnostik |[Azure Diagnostics-tillägg](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Tillägget för Azure VM-åtkomst |Hantera användare och autentiseringsuppgifter |[Tillägg för virtuell dator åtkomst för Linux](https://azure.microsoft.com/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |
