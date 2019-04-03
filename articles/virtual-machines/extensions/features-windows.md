---
title: Azure VM-tillägg och funktioner för Windows | Microsoft Docs
description: Lär dig vilka tillägg som finns tillgängliga för Azure-datorer, grupperade efter vad de tillhandahåller eller förbättra.
services: virtual-machines-windows
documentationcenter: ''
author: roiyz-msft
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
ms.author: roiyz
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ce13f053c2adee6a9a347a4162b60cc6d6b40eda
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2019
ms.locfileid: "58849762"
---
# <a name="virtual-machine-extensions-and-features-for-windows"></a>Virtuella datorer, tillägg och funktioner för Windows

Tillägg för Azure-dator (VM) är små program som ger efter distributionen konfiguration och automatisering av uppgifter på Azure Virtual Machines. Exempel: om en virtuell dator kräver Programvaruinstallation, antivirusskydd, eller för att köra ett skript inuti den kan ett VM-tillägg användas. Azure VM-tillägg kan köras med Azure CLI, PowerShell, Azure Resource Manager-mallar och Azure-portalen. Tillägg kan levereras tillsammans med en ny VM-distribution eller kör mot alla befintliga system.

Den här artikeln innehåller en översikt över VM-tillägg, krav för att använda Azure VM-tillägg och anvisningar för hur du identifiera, hantera och ta bort VM-tillägg. Den här artikeln innehåller allmänna informationen eftersom många VM-tillägg är tillgängligt, var och en med en potentiellt unika konfigurationer. Tillägget-specifik information finns i varje dokument specifika för enskilda tillägget.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="use-cases-and-samples"></a>Användningsfall och exempel

Flera olika Azure VM-tillägg som finns tillgängliga, var och en med en specifik användningsfall. Några exempel är:

- Använd PowerShell Desired State konfigurationer på en virtuell dator med DSC-tillägget för Windows. Mer information finns i [Azure Desired State configuration-tillägget](dsc-overview.md).
- Konfigurera övervakning av en virtuell dator med Microsoft Monitoring Agent-VM-tillägget. Mer information finns i [ansluta virtuella Azure-datorer till Azure Monitor-loggar](../../log-analytics/log-analytics-azure-vm-extension.md).
- Konfigurera en Azure-dator med Chef. Mer information finns i [automatisera Azure VM-distribution med Chef](../windows/chef-automation.md).
- Konfigurera övervakning av Azure-infrastrukturen med Datadog-tillägget. Mer information finns i den [Datadog blogg](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/).


Förutom process-specifika tillägg finns ett anpassat skripttillägg för både Windows och Linux-datorer. Tillägget för anpassat skript för Windows kan alla PowerShell.skript som ska köras på en virtuell dator. Anpassade skript är användbara för att utforma Azure-distributioner som kräver konfigurering, förutom vilka interna Azure-verktyg kan ge. Mer information finns i [anpassade skripttillägg för Windows-VM](custom-script-windows.md).

## <a name="prerequisites"></a>Förutsättningar

För att hantera tillägg på den virtuella datorn, måste du Azure Windows-agenten installerad. Vissa enskilda tillägg har förutsättningar, till exempel åtkomst till resurser eller beroenden.

### <a name="azure-vm-agent"></a>Virtuell Azure-datoragent

Azure VM-agenten hanterar samverkan mellan en Azure-dator och Azure-infrastrukturkontrollanten. VM-agenten är ansvarig för många funktionella aspekter av distribution och hantering av virtuella datorer i Azure, inklusive köra VM-tillägg. Azure VM-agenten är förinstallerade på Azure Marketplace-avbildningar och kan installeras manuellt på operativsystem som stöds. Azure VM-agenten för Windows kallas för Windows-gästagenten.

Information om operativsystem som stöds och installationsanvisningar finns i [Azure VM agent](agent-windows.md).

#### <a name="supported-agent-versions"></a>Versioner av agent som stöds

Det finns minimiversioner av agenten för att tillhandahålla den bästa möjliga upplevelsen. Mer information finns i [den här artikeln](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

#### <a name="supported-oses"></a>Stödda OS

Windows-gästagenten körs på flera operativsystem, men ramen tillägg har en gräns för operativsystem som tillägg. Mer information finns i [den här artikeln](https://support.microsoft.com/en-us/help/4078134/azure-extension-supported-operating-systems
).

Vissa tillägg stöds inte i alla operativsystem och kan skapa *felkod 51, 'Operativsystmet'*. Dokumentationen enskilda tillägg för support.

#### <a name="network-access"></a>Nätverksåtkomst

Tilläggspaket laddas ned från Azure Storage-tilläggscentrallagret och tillägget status uppladdningar skickas till Azure Storage. Om du använder [stöds](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) version av agenterna kan du inte behöver att tillåta åtkomst till Azure Storage i regionen VM kan använda agenten för att omdirigera kommunikationen till Azure-infrastrukturkontrollanten för agenten kommunikation. Om du har en version som inte stöds av agenten kan behöva du tillåter utgående åtkomst till Azure storage i regionen från den virtuella datorn.

> [!IMPORTANT]
> Om du har blockerat åtkomsten till *168.63.129.16* med gästdatorns brandvägg, sedan tillägg misslyckas oavsett ovan.

Agenter kan bara användas för att ladda ned tilläggspaket och Rapporteringsstatus. Till exempel om en installation av tillägget måste hämta ett skript från GitHub (anpassade skript) eller behöver åtkomst till Azure Storage (Azure Backup), sedan ytterligare brandvägg/Network Security Group portar måste du öppna. Olika tillägg har olika krav, eftersom de är program självständigt. För tillägg som kräver åtkomst till Azure Storage, kan du tillåta åtkomst med hjälp av Azure NSG-Tjänsttaggar för [Storage](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

Windows-Gästagenten har inte proxyserver som stöd att omdirigera trafik agentbegäranden via.

## <a name="discover-vm-extensions"></a>Identifiera VM-tillägg

Det finns många olika VM-tillägg för användning med virtuella Azure-datorer. Om du vill se en fullständig lista, använda [Get-AzVMExtensionImage](https://docs.microsoft.com/powershell/module/az.compute/get-azvmextensionimage). I följande exempel visar en lista över alla tillgängliga tillägg i den *WestUS* plats:

```powershell
Get-AzVmImagePublisher -Location "WestUS" | `
Get-AzVMExtensionImageType | `
Get-AzVMExtensionImage | Select Type, Version
```

## <a name="run-vm-extensions"></a>Kör VM-tillägg

Azure VM-tillägg som kör på befintliga virtuella datorer, vilket är användbart när du behöver göra konfigurationsändringar eller återställa anslutningen i en redan distribuerad virtuell dator. VM-tillägg kan också tillsammans med Azure Resource Manager malldistributioner. Genom att använda tillägg med Resource Manager-mallar, kan virtuella Azure-datorer distribueras och konfigurerats utan inblandning av efter distributionen.

Följande metoder kan användas för att köra ett tillägg mot en befintlig virtuell dator.

### <a name="powershell"></a>PowerShell

Det finns flera PowerShell-kommandon för att köra enskilda tillägg. Om du vill se en lista kan du använda [Get-Command](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/get-command) och filtrerar på *tillägget*:

```powershell
Get-Command Set-Az*Extension* -Module Az.Compute
```

Detta ger utdata som liknar följande:

```powershell
CommandType     Name                                          Version    Source
-----------     ----                                          -------    ------
Cmdlet          Set-AzVMAccessExtension                       4.5.0      Az.Compute
Cmdlet          Set-AzVMADDomainExtension                     4.5.0      Az.Compute
Cmdlet          Set-AzVMAEMExtension                          4.5.0      Az.Compute
Cmdlet          Set-AzVMBackupExtension                       4.5.0      Az.Compute
Cmdlet          Set-AzVMBginfoExtension                       4.5.0      Az.Compute
Cmdlet          Set-AzVMChefExtension                         4.5.0      Az.Compute
Cmdlet          Set-AzVMCustomScriptExtension                 4.5.0      Az.Compute
Cmdlet          Set-AzVMDiagnosticsExtension                  4.5.0      Az.Compute
Cmdlet          Set-AzVMDiskEncryptionExtension               4.5.0      Az.Compute
Cmdlet          Set-AzVMDscExtension                          4.5.0      Az.Compute
Cmdlet          Set-AzVMExtension                             4.5.0      Az.Compute
Cmdlet          Set-AzVMSqlServerExtension                    4.5.0      Az.Compute
Cmdlet          Set-AzVmssDiskEncryptionExtension             4.5.0      Az.Compute
```

I följande exempel använder tillägget för anpassat skript för att hämta ett skript från en GitHub-lagringsplatsen till den virtuella måldatorn och kör skriptet. Läs mer på det anpassade skripttillägget [översikt över tillägget för anpassat skript](custom-script-windows.md).

```powershell
Set-AzVMCustomScriptExtension -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" -Name "myCustomScript" `
    -FileUri "https://raw.githubusercontent.com/neilpeterson/nepeters-azure-templates/master/windows-custom-script-simple/support-scripts/Create-File.ps1" `
    -Run "Create-File.ps1" -Location "West US"
```

I följande exempel används VM Access-tillägg för att återställa lösenordet för administratörer av en virtuell Windows-dator till ett tillfälligt lösenord. Mer information om VM Access-tillägg finns i [Återställ Fjärrskrivbordstjänsten i en Windows-VM](../windows/reset-rdp.md). När du har kört det här kan återställa du lösenordet vid första inloggningen:

```powershell
$cred=Get-Credential

Set-AzVMAccessExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myVMAccess" `
    -Location WestUS -UserName $cred.GetNetworkCredential().Username `
    -Password $cred.GetNetworkCredential().Password -typeHandlerVersion "2.0"
```

Den `Set-AzVMExtension` kommando kan användas för att starta alla VM-tillägg. Mer information finns i den [Set-AzVMExtension referens](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension).


### <a name="azure-portal"></a>Azure Portal

VM-tillägg kan tillämpas på en befintlig virtuell dator via Azure portal. Välj den virtuella datorn i portalen, väljer **tillägg**och välj sedan **Lägg till**. Välj de tillägg du vill använda från listan över tillgängliga tillägg och följ instruktionerna i guiden.

I följande exempel visas att installationen av tillägget Microsoft Antimalware från Azure portal:

![Installera tillägget mot skadlig kod](./media/features-windows/installantimalwareextension.png)

### <a name="azure-resource-manager-templates"></a>Azure Resource Manager-mallar

VM-tillägg kan läggas till i en Azure Resource Manager-mall och körs med distributionen av mallen. När du distribuerar ett tillägg med en mall kan skapa du helt konfigurerade Azure-distributioner. Till exempel följande JSON utförs från en resurshanterare mallen distribuerar en uppsättning belastningsutjämnade virtuella datorer och en Azure SQL database och sedan installerar ett program med .NET Core på varje virtuell dator. VM-tillägget hand tar om programvaran ska installeras.

Mer information finns i den [fullständiga Resource Manager-mall](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows).

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

Mer information om hur du skapar Resource Manager-mallar finns i [redigera Azure Resource Manager-mallar med Windows VM-tillägg](../windows/template-description.md#extensions).

## <a name="secure-vm-extension-data"></a>Skydda data för VM-tillägg

När du kör ett VM-tillägg, kan det vara nödvändigt att inkludera känslig information, till exempel autentiseringsuppgifter, lagringskontonamn och åtkomstnycklarna för lagringskontot. Många VM-tillägg innehåller en skyddad konfiguration som krypterar data och dekrypterar dem bara inuti den Virtuella måldatorn. Varje tillägg har en specifik skyddad konfigurationsschema och varje beskrivs i dokumentationen för specifika tillägg.

I följande exempel visar en instans av tillägget för anpassat skript för Windows. Kommandot körs innehåller en uppsättning autentiseringsuppgifter. I det här exemplet krypteras inte kommandot som ska köras:

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

Flytta den **kommando för att köra** egenskap enligt den **skyddade** konfigurationen skyddar körning-strängen som du ser i följande exempel:

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

Dela samma uppdateringsmekanism agenter och tillägg. Vissa uppdateringar kräver inte ytterligare brandväggsregler.

När en uppdatering är tillgänglig endast installeras på den virtuella datorn när det finns en ändring av tillägg och andra Virtuella datormodellen ändringar som:

- Datadiskar
- Tillägg
- Boot diagnostics behållare
- Gäst-OS-hemligheter
- Storlek på virtuell dator
- Nätverksprofil

Utgivare göra uppdateringar tillgängliga för regioner vid olika tidpunkter, så det är möjligt kan du har virtuella datorer i olika regioner i olika versioner.

#### <a name="listing-extensions-deployed-to-a-vm"></a>Visa en lista över tillägg som distribuerats till en virtuell dator

```powershell
$vm = Get-AzVM -ResourceGroupName "myResourceGroup" -VMName "myVM"
$vm.Extensions | select Publisher, VirtualMachineExtensionType, TypeHandlerVersion
```

```powershell
Publisher             VirtualMachineExtensionType          TypeHandlerVersion
---------             ---------------------------          ------------------
Microsoft.Compute     CustomScriptExtension                1.9
```

#### <a name="agent-updates"></a>Agentuppdateringar

Windows-Gästagenten innehåller endast *tillägget hantering av kod*, *Windows etablering kod* är separat. Du kan avinstallera Windows-Gästagenten. Du kan inte inaktivera den automatiska uppdateringen av Gästagenten fönster.

Den *tillägget hantering av kod* ansvarar för kommunicerar med Azure-infrastrukturen och hantera åtgärder för VM-tillägg som installerar rapporterar status, uppdaterar enskilda tillägg och ta bort dem. Uppdateringar innehålla säkerhetskorrigeringar, felkorrigeringar och förbättringar av den *tillägget hantering av kod*.

Du kan kontrollera vilken version du kör [identifiera installerade Windows-Gästagenten](agent-windows.md#detect-the-vm-agent).

#### <a name="extension-updates"></a>Uppdateringar för tillägget

När det finns en uppdatering av tillägget, laddar ned Windows-Gästagenten och uppgraderar tillägget. Tillägget för automatiska uppdateringar är antingen *mindre* eller *snabbkorrigering*. Du kan anmäla eller avanmäla dig från tillägg *mindre* uppdateras när du etablerar tillägget. I följande exempel visas hur man automatiskt uppgraderar delversioner i Resource Manager-mall med *autoUpgradeMinorVersion ”: true”,*:

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

Vi rekommenderar att du alltid välja automatisk uppdatering i distributionen tillägget för att få de senaste versionen mindre felkorrigeringar. Hotfix-uppdateringar som säkerhet eller nyckel felkorrigeringar går inte att välja.

### <a name="how-to-identify-extension-updates"></a>Så här identifierar du tillägget uppdateringar

#### <a name="identifying-if-the-extension-is-set-with-autoupgrademinorversion-on-a-vm"></a>Identifiera om tillägget är inställd med autoUpgradeMinorVersion på en virtuell dator

Du kan se från den Virtuella datormodellen om tillägget etablerades med 'autoUpgradeMinorVersion'. Du kan kontrollera genom att använda [Get-AzVm](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) och ange resursgrupp och virtuell dator namn på följande sätt:

```powerShell
 $vm = Get-AzVm -ResourceGroupName "myResourceGroup" -VMName "myVM"
 $vm.Extensions
```

Följande Exempelutdata visar att *autoUpgradeMinorVersion* är inställd på *SANT*:

```powershell
ForceUpdateTag              :
Publisher                   : Microsoft.Compute
VirtualMachineExtensionType : CustomScriptExtension
TypeHandlerVersion          : 1.9
AutoUpgradeMinorVersion     : True
```

#### <a name="identifying-when-an-autoupgrademinorversion-occurred"></a>Identifiera när en autoUpgradeMinorVersion inträffade

Om du vill se när en uppdatering av tillägget uppstod granska agenten loggar in på den virtuella datorn senare *C:\WindowsAzure\Logs\WaAppAgent.log*

I följande exempel visas den virtuella datorn hade *Microsoft.Compute.CustomScriptExtension 1.8* installerad. Det fanns en snabbkorrigering till version *1.9*:

```powershell
[INFO]  Getting plugin locations for plugin 'Microsoft.Compute.CustomScriptExtension'. Current Version: '1.8', Requested Version: '1.9'
[INFO]  Auto-Upgrade mode. Highest public version for plugin 'Microsoft.Compute.CustomScriptExtension' with requested version: '1.9', is: '1.9'
```

## <a name="agent-permissions"></a>Agent-behörigheter

För att utföra sina uppgifter, agenten måste köras som *lokalt System*.

## <a name="troubleshoot-vm-extensions"></a>Felsöka VM-tillägg

Varje VM-tillägg kan ha felsökningssteg specifika till tillägget. Till exempel när du använder tillägget för anpassat skript, finns information om skriptkörning lokalt på den virtuella datorn där tillägget kördes. Tillägget-specifika felsökning beskrivs i dokumentationen för specifika tillägg.

Följande felsökningssteg gäller för alla VM-tillägg.

1. Om du vill kontrollera Agentloggen för Windows-Gäst, titta på aktiviteten när ditt tillägg etablerades i *C:\WindowsAzure\Logs\WaAppAgent.txt*

2. Kontrollera faktiska tillägget programloggarna för mer information finns i *C:\WindowsAzure\Logs\Plugins\<extensionName >*

3. Kontrollera tillägget dokumentation felsökningsavsnitt för felkoder, kända problem osv.

4. Titta på systemloggar. Sök efter andra åtgärder som kan ha påverkas tillägg, till exempel en tidskrävande installation av ett annat program som krävs för exklusiv package manager-åtkomst.

### <a name="common-reasons-for-extension-failures"></a>Vanliga orsaker till datortillägg

1. Tillägg har 20 minuter att köra (undantag är CustomScript-tillägg, Chef och DSC som har 90 minuter). Om distributionen överskrider den här tiden, markeras den som en tidsgräns. Orsaken till detta kan bero på otillräckliga resurser virtuella datorer, andra VM-konfigurationer/starta upp aktiviteter som använder hög mängder resurs när tillägget försöker etablera.

2. Minsta kraven uppfylls inte. Vissa tillägg vara beroende VM SKU: er som HPC-bilder. Tillägg kan kräva viss nätverkskraven åtkomst, till exempel kommunikation till Azure Storage eller offentliga tjänster. Andra exempel kan vara åtkomst till paketet lagringsplatser, slut på diskutrymme eller säkerhetsbegränsningar.

3. Exklusiva package manager-åtkomst. I vissa fall kan det uppstå en tidskrävande VM-konfiguration och installation av tillägg som är i konflikt, där de båda behöver exklusiv åtkomst till package manager.

### <a name="view-extension-status"></a>Visa status för tillägg

När en VM-tillägget har körts mot en virtuell dator kan du använda [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) Tilläggsstatus ska returneras. *Underordnad status [0]* visar att tillägget etableringen är klar, vilket innebär att lyckas den distribueras till den virtuella datorn, men inte det gick att köra av tillägget på den virtuella datorn, *underordnad status [1]*.

```powershell
Get-AzVM -ResourceGroupName "myResourceGroup" -VMName "myVM" -Status
```

Utdata liknar följande Exempelutdata:

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

Körningsstatus för tillägg finns också i Azure-portalen. Om du vill visa status för ett tillägg markerar du den virtuella datorn, väljer **tillägg**, markerar du önskade tillägget.

### <a name="rerun-vm-extensions"></a>Köra VM-tillägg

Det kan finnas fall där ett VM-tillägg måste köras igen. Du kan köra ett tillägg genom att ta bort den och sedan köra tillägget med en körning metod för ditt val. Ta bort ett tillägg med [Remove-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/Remove-AzVMExtension) på följande sätt:

```powershell
Remove-AzVMExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myExtensionName"
```

Du kan också ta bort ett tillägg i Azure-portalen på följande sätt:

1. Välj en virtuell dator.
2. Välj **tillägg**.
3. Välj önskad tillägget.
4. Välj **avinstallera**.

## <a name="common-vm-extensions-reference"></a>Referens för vanliga VM-tillägg
| Namn på tillägg | Beskrivning | Mer information |
| --- | --- | --- |
| Tillägget för anpassat skript för Windows |Köra skript mot en Azure virtuell dator |[Tillägget för anpassat skript för Windows](custom-script-windows.md) |
| DSC-tillägg för Windows |PowerShell DSC (Desired State Configuration)-tillägg |[DSC-tillägg för Windows](dsc-overview.md) |
| Azure Diagnostics-tillägg |Hantera Azure-diagnostik |[Azure Diagnostics-tillägg](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Azure VM Access-tillägg |Hantera användare och autentiseringsuppgifter |[VM Access-tillägg för Linux](https://azure.microsoft.com/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |

## <a name="next-steps"></a>Nästa steg

Mer information om VM-tillägg finns i [Azure VM-tillägg och funktioner för översikt över](overview.md).
