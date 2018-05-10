---
title: Azure VM-tillägg och funktioner för Windows | Microsoft Docs
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
ms.date: 03/30/2018
ms.author: danis
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 88852fe7843e24fde50749e2f994bcfeb596305d
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="virtual-machine-extensions-and-features-for-windows"></a>Tillägg för virtuell dator och funktioner i Windows

Virtuell Azure-dator (VM)-tillägg är små program som innehåller efter distributionen konfiguration och automatisering av uppgifter på Azure Virtual Machines. Om exempelvis en virtuell dator kräver Programvaruinstallation, antivirus skydd, eller om du vill köra ett skript inuti den VM-tillägget kan användas. Azure VM-tillägg kan köras med Azure CLI, PowerShell, Azure Resource Manager-mallar och Azure-portalen. Tillägg kan levereras tillsammans med en ny VM-distribution eller köra mot alla befintliga system.

Den här artikeln innehåller en översikt över krav för att använda Azure VM-tillägg på VM-tillägg och anvisningar om hur du identifierar, hantera och ta bort VM-tillägg. Den här artikeln innehåller allmänna informationen eftersom många VM-tillägg är tillgängligt, var och en med en potentiellt unik konfiguration. Tillägget-specifik information finns i varje dokument specifika för enskilda tillägg.

## <a name="use-cases-and-samples"></a>Användningsfall och exempel

Det finns flera olika Virtuella Azure-tillägg, var och en med en specifik användningsfall. Några exempel är:

- Använd PowerShell önskade tillstånd konfigurationer på en virtuell dator med DSC-tillägg för Windows. Mer information finns i [tillägg för konfiguration av Azure önskade tillstånd](dsc-overview.md).
- Konfigurera övervakning av en virtuell dator med Microsoft Monitoring Agent VM-tillägget. Mer information finns i [ansluta virtuella Azure-datorer till logganalys](../../log-analytics/log-analytics-azure-vm-extension.md).
- Konfigurera en virtuell dator i Azure med hjälp av Chef. Mer information finns i [automatisera Azure VM-distribution med Chef](../windows/chef-automation.md).
- Konfigurera övervakning av Azure-infrastrukturen med filnamnstillägget Datadog. Mer information finns i [Datadog blogg](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/).


Förutom processpecifika tillägg är ett tillägg för anpassat skript tillgängligt för både Windows och Linux virtuella datorer. Tillägget för anpassat skript för Windows kan alla PowerShell.skript kan köras på en virtuell dator. Anpassade skript är användbara för att utforma Azure-distributioner som kräver konfiguration efter vilken interna Azure verktygsuppsättning kan ge. Mer information finns i [tillägget Windows VM anpassade skript](custom-script-windows.md).

## <a name="prerequisites"></a>Förutsättningar

För att hantera tillägg på den virtuella datorn måste Azure Linux-agenten installerad. Vissa enskilda tillägg har förutsättningar, till exempel åtkomst till resurser eller beroenden.

### <a name="azure-vm-agent"></a>Virtuell Azure-datoragent

Virtuella Azure-agenten hanterar samverkan mellan en virtuell dator i Azure och Azure-infrastrukturkontrollanten. Den Virtuella datoragenten är ansvarig för många funktioner för att distribuera och hantera virtuella Azure-datorer, inklusive kör VM-tillägg. Virtuella Azure-agenten är förinstallerat på Azure Marketplace-bilder och kan installeras manuellt på operativsystem som stöds. Azure VM-agenten för Windows kallas Windows gästagenten.

Information om operativsystem som stöds och installationsanvisningar finns [virtuella Azure-datorn agent](agent-windows.md).

#### <a name="supported-agent-versions"></a>Agent som stöds versioner

För att kunna ge bästa möjliga upplevelse, finns det lägsta versioner av agenten. Mer information finns i [den här artikeln](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

#### <a name="supported-oses"></a>Stödda OS

Windows-gästagenten körs på flera OS men tillägg framework har en gräns för operativsystem som tillägg. Mer information finns i [den här artikeln] (https://support.microsoft.com/en-us/help/4078134/azure-extension-supported-operating-systems ).

Vissa tillägg stöds inte över alla operativsystem och kan skapa *felkod 51, 'stöds inte OS-*. I dokumentationen enskilda tillägg för support.

#### <a name="network-access"></a>Nätverksåtkomst

Tilläggspaket hämtas från databasen för Azure Storage-tillägget och tillägget status överföringar skickas till Azure Storage. Om du använder [stöds](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) versionen av agenter, du behöver inte tillåter åtkomst till Azure Storage i VM-region som agenten kan använda för att dirigera kommunikation till Azure-infrastrukturkontrollanten för agenten kommunikation. Om du är på en version som inte stöds av agenten måste du tillåta utgående åtkomst till Azure-lagring i den regionen från den virtuella datorn.

> [!IMPORTANT]
> Om du har blockerat åtkomsten till *168.63.129.1* med gäst-brandväggen, sedan tillägg misslyckas oavsett ovan.

Agenter kan bara användas för att ladda ned tilläggspaket och Rapporteringsstatus. Till exempel om en installation av tillägget måste hämtas skript från GitHub (anpassade skript) eller behöver åtkomst till Azure Storage (Azure Backup) sedan ytterligare brandvägg/nätverk Säkerhet grupp portar måste öppnas. Olika tillägg har olika krav, eftersom de är program i sin egen rätt. För tillägg som kräver åtkomst till Azure Storage kan du tillåta åtkomst med hjälp av Azure NSG Service taggar för [lagring](https://docs.microsoft.com/en-us/azure/virtual-network/security-overview#service-tags).

Gästagenten Windows har inte proxyserver som stöd att omdirigera trafik agentbegäranden via.

## <a name="discover-vm-extensions"></a>Identifiera VM-tillägg

Det finns många olika VM-tillägg för användning med virtuella Azure-datorer. Om du vill se en fullständig lista över [Get-AzureRmVMExtensionImage](/powershell/module/azurerm.compute/get-azurermvmextensionimage). I följande exempel visar en lista över alla tillgängliga tillägg i den *WestUS* plats:

```powershell
Get-AzureRmVmImagePublisher -Location "WestUS" | `
Get-AzureRmVMExtensionImageType | `
Get-AzureRmVMExtensionImage | Select Type, Version
```

## <a name="run-vm-extensions"></a>Kör VM-tillägg

Azure VM-tillägg som kör på befintliga virtuella datorer, vilket är användbart när du behöver göra konfigurationsändringar eller återställa anslutningen på en redan distribuerad virtuell dator. VM-tillägg kan också tillsammans med Azure Resource Manager mall-distributioner. Genom att använda tillägg med Resource Manager-mallar, virtuella datorer i Azure distribuerats och konfigurerats utan åtgärder från efter distributionen.

Följande metoder kan användas för att köra ett tillägg mot en befintlig virtuell dator.

### <a name="powershell"></a>PowerShell

Det finns flera PowerShell-kommandon för att köra enskilda tillägg. Om du vill se en lista [Get-Command](/powershell/module/microsoft.powershell.core/get-command) och filtrera på *tillägget*:

```powershell
Get-Command Set-AzureRM*Extension* -Module AzureRM.Compute
```

Detta ger utdata som liknar följande:

```powershell
CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Set-AzureRmVMAccessExtension                       4.5.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMADDomainExtension                     4.5.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMAEMExtension                          4.5.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMBackupExtension                       4.5.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMBginfoExtension                       4.5.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMChefExtension                         4.5.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMCustomScriptExtension                 4.5.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMDiagnosticsExtension                  4.5.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMDiskEncryptionExtension               4.5.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMDscExtension                          4.5.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMExtension                             4.5.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMSqlServerExtension                    4.5.0      AzureRM.Compute
Cmdlet          Set-AzureRmVmssDiskEncryptionExtension             4.5.0      AzureRM.Compute
```

I följande exempel använder tillägget för anpassat skript för att hämta ett skript från en GitHub-databas på den virtuella måldatorn och kör skriptet. Mer information om tillägget för anpassat skript finns [översikt över tillägget för anpassat skript](custom-script-windows.md).

```powershell
Set-AzureRmVMCustomScriptExtension -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" -Name "myCustomScript" `
    -FileUri "https://raw.githubusercontent.com/neilpeterson/nepeters-azure-templates/master/windows-custom-script-simple/support-scripts/Create-File.ps1" `
    -Run "Create-File.ps1" -Location "West US"
```

I följande exempel används tillägget för virtuell dator åtkomst för att återställa lösenordet för administratörer av en virtuell Windows-dator till ett tillfälligt lösenord. Mer information om tillägget för virtuell dator åtkomst finns [Återställ Fjärrskrivbordstjänsten i en Windows VM](../windows/reset-rdp.md). När du har kört detta ska du återställa lösenord vid första inloggningen:

```powershell
$cred=Get-Credential

Set-AzureRmVMAccessExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myVMAccess" `
    -Location WestUS -UserName $cred.GetNetworkCredential().Username `
    -Password $cred.GetNetworkCredential().Password -typeHandlerVersion "2.0"
```

Den `Set-AzureRmVMExtension` kommando kan användas för att starta alla VM-tillägg. Mer information finns i [Set AzureRmVMExtension referens](https://msdn.microsoft.com/en-us/library/mt603745.aspx).


### <a name="azure-portal"></a>Azure Portal

VM-tillägg kan tillämpas på en befintlig virtuell dator via Azure-portalen. Välj den virtuella datorn i portalen, Välj **tillägg**och välj **Lägg till**. Välj tillägg du vill använda från listan över tillgängliga tillägg och följ instruktionerna i guiden.

I följande exempel visas att installationen av tillägget Microsoft Antimalware från Azure portal:

![Installera tillägg för program mot skadlig kod](./media/features-windows/installantimalwareextension.png)

### <a name="azure-resource-manager-templates"></a>Azure Resource Manager-mallar

VM-tillägg kan läggas till i en Azure Resource Manager-mall och utförs med distributionen av mallen. När du distribuerar ett tillägg med en mall kan skapa du helt konfigurerade Azure-distributioner. Till exempel följande JSON hämtas från en resurshanterare mall distribuerar en uppsättning belastningsutjämnade virtuella datorer och en Azure SQL database och sedan installerar ett program med .NET Core på varje virtuell dator. Tillägg för virtuell dator hand tar om installationen av programmet.

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
    "typeHandlerVersion": "1.9",
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

Mer information om hur du skapar Resource Manager-mallar finns [redigera Azure Resource Manager-mallar med Windows VM-tillägg](../windows/template-description.md#extensions).

## <a name="secure-vm-extension-data"></a>Säkra data för VM-tillägg

När du kör en VM-tillägget, kan det vara nödvändigt att inkludera känslig information, till exempel autentiseringsuppgifter, lagringskontonamn och åtkomst för lagringskontonycklar. Många VM-tillägg innehåller en skyddad konfiguration som krypterar data och dekrypterar dem endast inuti VM målet. Varje tillägg innehåller en specifik skyddade konfigurationsschema och varje beskrivs i dokumentationen för specifika tillägg.

I följande exempel visas en instans av tillägget för anpassat skript för Windows. Kommandot för att köra innehåller en uppsättning autentiseringsuppgifter. I det här exemplet krypteras inte kommandot att köras:

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
    "typeHandlerVersion": "1.9",
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

Flytta den **kommando att köra** egenskapen till den **skyddade** konfigurationen skyddar körning-strängen som visas i följande exempel:

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
    "typeHandlerVersion": "1.9",
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

### <a name="how-do-agents-and-extensions-get-updated"></a>Hur agenter och tillägg uppdateras?

Dela mekanismen för samma uppdatering av agenter och tillägg. Vissa uppdateringar kräver inte ytterligare brandväggsregler.

När en uppdatering är tillgänglig endast installeras på den virtuella datorn när det finns en ändring av tillägg och andra VM Modelländringar som:

- Datadiskar
- Tillägg
- Starta diagnostik behållare
- Gästoperativsystem hemligheter
- Storlek på virtuell dator
- Nätverksprofil

Utgivare göra uppdateringar tillgängliga för regioner vid olika tidpunkter, så det är möjligt att du kan ha virtuella datorer i olika områden i olika versioner.

#### <a name="listing-extensions-deployed-to-a-vm"></a>Visar en lista över tillägg som distribuerats till en virtuell dator

```powershell
$vm = Get-AzureRmVM -ResourceGroupName "myResourceGroup" -VMName "myVM"
$vm.Extensions | select Publisher, VirtualMachineExtensionType, TypeHandlerVersion
```

```powershell
Publisher             VirtualMachineExtensionType          TypeHandlerVersion
---------             ---------------------------          ------------------
Microsoft.Compute     CustomScriptExtension                1.9
```

#### <a name="agent-updates"></a>Agentuppdateringar

Endast innehåller Windows Gästagenten *tillägget hantera kod*, *Windows etablering kod* är desamma. Du kan avinstallera Windows Gästagenten. Du kan inaktivera automatisk uppdatering av Gästagenten fönster.

Den *tillägget hantera kod* ansvarar för att kommunicera med Azure-strukturen och hanterar åtgärderna för VM-tillägg som installerar reporting status, uppdatering av enskilda tillägg och ta bort dem. Uppdateringar innehåller säkerhetskorrigeringar, felkorrigeringar och förbättringar av *tillägget hantera koden*.

Du kan kontrollera vilken version du kör [identifiera installerade Windows-Gästagenten](agent-windows.md#detect-the-vm-agent).

#### <a name="extension-updates"></a>Tillägget uppdateringar

När det finns en uppdatering för tillägg, hämtar Windows Gästagenten och uppgraderar tillägget. Tillägget automatiska uppdateringar är antingen *mindre* eller *snabbkorrigering*. Du kan välja eller välja att inaktivera tillägg *mindre* uppdateras när du etablerar tillägget. I följande exempel visas hur man automatiskt uppgraderar delversioner i en Resource Manager-mall med *autoUpgradeMinorVersion ”: true'*:

```json
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.9",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ]
    },
```

För att få de senaste mindre viktig felkorrigeringar, rekommenderas att du alltid välja automatisk uppdatering i tillägget-distributioner. Hotfix-uppdateringar som innehåller säkerhets- eller felkorrigeringar går inte att välja.

### <a name="how-to-identify-extension-updates"></a>Så här identifierar du tillägget uppdateringar

#### <a name="identifying-if-the-extension-is-set-with-autoupgrademinorversion-on-a-vm"></a>Identifiera om tillägget anges med autoUpgradeMinorVersion på en virtuell dator

Du kan se från VM-modellen om tillägget har etablerats med 'autoUpgradeMinorVersion'. Använd för att kontrollera [Get-AzureRmVm](/powershell/module/azurerm.compute/get-azurermvm) och ange resursgrupp och VM namn på följande sätt:

```powerShell
 $vm = Get-AzureRmVm -ResourceGroupName "myResourceGroup" -VMName "myVM"
 $vm.Extensions
```

Följande exempel visas som *autoUpgradeMinorVersion* är inställd på *SANT*:

```powershell
ForceUpdateTag              :
Publisher                   : Microsoft.Compute
VirtualMachineExtensionType : CustomScriptExtension
TypeHandlerVersion          : 1.9
AutoUpgradeMinorVersion     : True
```

#### <a name="identifying-when-an-autoupgrademinorversion-occurred"></a>Identifiera när en autoUpgradeMinorVersion inträffade

När en uppdatering av tillägget inträffat, granska agenten loggar in på den virtuella datorn på *C:\WindowsAzure\Logs\WaAppAgent.log*

I följande exempel visas den virtuella datorn hade *Microsoft.Compute.CustomScriptExtension 1.8* installerad. Det fanns en snabbkorrigering till version *1,9*:

```powershell
[INFO]  Getting plugin locations for plugin 'Microsoft.Compute.CustomScriptExtension'. Current Version: '1.8', Requested Version: '1.9'
[INFO]  Auto-Upgrade mode. Highest public version for plugin 'Microsoft.Compute.CustomScriptExtension' with requested version: '1.9', is: '1.9'
```

## <a name="agent-permissions"></a>Behörigheter

Om du vill utföra sina uppgifter agenten måste köras som *lokalt System*.

## <a name="troubleshoot-vm-extensions"></a>Felsökning av VM-tillägg

Varje VM-tillägg kan ha felsökningssteg specifika tillägget. Till exempel när du använder tillägget för anpassat skript finns körning skriptdetaljer lokalt på den virtuella datorn där tillägget kördes. Tillägget-specifika felsökning beskrivs i dokumentationen för tillägget-specifika.

Följande felsökningssteg gäller för alla VM-tillägg.

1. Om du vill kontrollera Windows gäst Agent loggen titta på aktiviteten när din anknytning etablerades i *C:\WindowsAzure\Logs\WaAppAgent.txt*

2. Kontrollera loggarna faktiska tillägget för mer information finns i *C:\WindowsAzure\Logs\Plugins\<tillägg >*

3. Kontrollera tillägget viss dokumentation felsökningsavsnitt innehåller felkoder, kända problem osv.

4. Titta på systemloggarna. Kontrollera om andra åtgärder som kan ha påverkas filnamnstillägg, till exempel en tidskrävande installation av ett annat program som kräver exklusiv package manager åtkomst.

### <a name="common-reasons-for-extension-failures"></a>Vanliga orsaker till tillägget fel

1. Tillägg har 20 minuter att köra (undantag är CustomScript-tillägg, Chef och DSC som har 90 minuter). Om distributionen överskrider den här tiden, är den markerad som en tidsgräns. Orsaken till detta kan bero på otillräckliga resurser virtuella datorer, andra VM konfigurationer/start aktiviteter som använder hög mängder resursen samtidigt tillägget försöker etablera.

2. Lägsta krav inte uppfylls. Vissa tillägg har beroenden på VM-SKU: er, till exempel HPC-avbildningar. Tillägg kan kräva vissa nätverkskrav åtkomst, till exempel kommunikation till Azure Storage eller offentliga tjänster. Andra exempel kan vara åtkomst till paketet databaser, få slut på diskutrymme eller säkerhetsbegränsningar.

3. Exklusiv package manager-åtkomst. I vissa fall kan uppstå det en tidskrävande VM-konfiguration och installation av webbprogramtillägg i konflikt, där båda måste exklusiv åtkomst till package manager.

### <a name="view-extension-status"></a>Visa status för tillägg

När en VM-tillägget har körts mot en VM, använda [Get-AzureRmVM ](/powershell/module/azurerm.compute/get-azurermvm) Tilläggsstatus ska returneras. *Underordnad status [0]* visas som tillägget etableringen har slutförts, vilket innebär att det lyckades distribueras till den virtuella datorn, men inte det gick att köra tillägget inuti den virtuella datorn, *underordnad status [1]*.

```powershell
Get-AzureRmVM -ResourceGroupName "myResourceGroup" -VMName "myVM" -Status
```

Utdata liknar följande exempel utdata:

```powershell
Extensions[0]           :
  Name                  : CustomScriptExtension
  Type                  : Microsoft.Compute.CustomScriptExtension
  TypeHandlerVersion    : 1.9
  Substatuses[0]        :
    Code                : ComponentStatus/StdOut/succeeded
    Level               : Info
    DisplayStatus       : Provisioning succeeded
    Message             : Windows PowerShell \nCopyright (C) Microsoft Corporation. All rights reserved.\n
  Substatuses[1]        :
    Code                : ComponentStatus/StdErr/succeeded
    Level               : Info
    DisplayStatus       : Provisioning succeeded
    Message             : The argument 'cseTest%20Scriptparam1.ps1' to the -File parameter does not exist. Provide the path to an existing '.ps1' file as an argument to the

-File parameter.
  Statuses[0]           :
    Code                : ProvisioningState/failed/-196608
    Level               : Error
    DisplayStatus       : Provisioning failed
    Message             : Finished executing command
```

Tillståndets status är körningen kan också finnas i Azure-portalen. Om du vill visa status för ett tillägg markerar du den virtuella datorn, väljer **tillägg**, markerar du önskade tillägget.

### <a name="rerun-vm-extensions"></a>Kör VM-tillägg

Det kan finnas fall där en VM-tillägget behöver köras igen. Du kan köra ett tillägg genom att ta bort den och sedan köra tillägget med en körning metod du föredrar. Ta bort ett tillägg med [ta bort AzureRmVMExtension](/powershell/module/AzureRM.Compute/Remove-AzureRmVMExtension) på följande sätt:

```powershell
Remove-AzureRmVMExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myExtensionName"
```

Du kan också ta bort ett tillägg i Azure-portalen på följande sätt:

1. Välj en virtuell dator.
2. Välj **tillägg**.
3. Välj önskad tillägget.
4. Välj **avinstallera**.

## <a name="common-vm-extensions-reference"></a>Benämningen för VM-tillägg
| Tilläggsnamn | Beskrivning | Mer information |
| --- | --- | --- |
| Tillägget för anpassat skript för Windows |Kör skript mot en virtuell Azure-dator |[Tillägget för anpassat skript för Windows](custom-script-windows.md) |
| DSC-tillägg för Windows |PowerShell DSC (Desired State Configuration)-tillägg |[DSC-tillägg för Windows](dsc-overview.md) |
| Azure Diagnostics-tillägg |Hantera Azure-diagnostik |[Azure Diagnostics-tillägg](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Tillägget för Azure VM-åtkomst |Hantera användare och autentiseringsuppgifter |[Tillägg för virtuell dator åtkomst för Linux](https://azure.microsoft.com/en-us/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |

## <a name="next-steps"></a>Nästa steg

Mer information om VM-tillägg finns [översikt över funktioner och tillägg av Azure virtuella datorer](overview.md).