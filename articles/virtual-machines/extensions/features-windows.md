---
title: Azure VM-tillägg och funktioner för Windows
description: Läs om vilka tillägg som är tillgängliga för virtuella Azure-datorer, grupperade efter vad de tillhandahåller eller förbättrar.
services: virtual-machines-windows
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 999d63ee-890e-432e-9391-25b3fc6cde28
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/30/2018
ms.author: akjosh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cd6439bf1b1f52b8e63819e8e519fc4971d1bc2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066843"
---
# <a name="virtual-machine-extensions-and-features-for-windows"></a>Tillägg och funktioner för virtuella datorer för Windows

Azure-tillägg för virtuella datorer är små program som ger konfigurations- och automationsuppgifter på virtuella Azure-datorer efter distribution. Om en virtuell dator till exempel behöver programvaruinstallation, antivirusskydd eller körning av ett skript på den kan ett VM-tillägg användas. Azure VM-tillägg kan köras med Azure CLI, PowerShell, Azure Resource Manager-mallar och Azure-portalen. Tillägg kan paketeras med en ny VM-distribution eller köras mot valfritt befintligt system.

Den här artikeln innehåller en översikt över VM-tillägg, förutsättningar för att använda Azure VM-tillägg och vägledning om hur du identifierar, hanterar och tar bort VM-tillägg. Den här artikeln innehåller allmän information eftersom många VM-tillägg är tillgängliga, var och en med en potentiellt unik konfiguration. Tilläggsspecifika detaljer finns i varje dokument som är specifikt för det enskilda tillägget.

 

## <a name="use-cases-and-samples"></a>Användningsfall och prover

Flera olika Azure VM-tillägg är tillgängliga, var och en med ett specifikt användningsfall. Några exempel är:

- Använd PowerShell-konfigurationer för önskat tillstånd på en virtuell dator med DSC-tillägget för Windows. Mer information finns i [Azure Desired State configuration extension](dsc-overview.md).
- Konfigurera övervakning av en virtuell dator med tillägget Log Analytics Agent VM. Mer information finns i [Ansluta virtuella Azure-datorer till Azure Monitor-loggar](../../log-analytics/log-analytics-azure-vm-extension.md).
- Konfigurera en virtuell Azure-dator med hjälp av Chef. Mer information finns i [Automatisera Azure VM-distribution med Chef](../../chef/chef-automation.md).
- Konfigurera övervakning av din Azure-infrastruktur med Datadog-tillägget. Mer information finns i [Datadog-bloggen](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/).


Förutom processspecifika tillägg är ett anpassat skripttillägg tillgängligt för både virtuella Windows- och Linux-datorer. Tillägget Anpassat skript för Windows gör att alla PowerShell-skript kan köras på en virtuell dator. Anpassade skript är användbara för att utforma Azure-distributioner som kräver konfiguration utöver vad inbyggda Azure-verktyg kan ge. Mer information finns i [Windows VM Custom Script extension](custom-script-windows.md).

## <a name="prerequisites"></a>Krav

För att hantera tillägget på den virtuella datorn måste Azure Windows Agent installerad. Vissa enskilda tillägg har förutsättningar, till exempel åtkomst till resurser eller beroenden.

### <a name="azure-vm-agent"></a>Virtuell Azure-datoragent

Azure VM-agenten hanterar interaktioner mellan en Azure VM och Azure-infrastrukturstyrenheten. VM-agenten ansvarar för många funktionella aspekter av distribution och hantering av virtuella Azure-datorer, inklusive att köra VM-tillägg. Azure VM-agenten är förinstallerad på Azure Marketplace-avbildningar och kan installeras manuellt på operativsystem som stöds. Azure VM-agenten för Windows kallas Windows Guest Agent.

Information om operativsystem och installationsinstruktioner som stöds finns i [Azures virtuella datoragent](agent-windows.md).

#### <a name="supported-agent-versions"></a>Agentversioner som stöds

För att ge bästa möjliga upplevelse, det finns minimiversioner av agenten. Mer information finns i [den här artikeln](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

#### <a name="supported-oses"></a>OSes som stöds

Windows Guest-agenten körs på flera OSes, men tilläggsramverket har en gräns för de OSes som tillägg. Mer information finns i [den här artikeln](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems
).

Vissa tillägg stöds inte i alla operativsystem och kan avge *felkod 51, "Os som inte stöds".* Kontrollera den enskilda tilläggsdokumentationen för support.

#### <a name="network-access"></a>Nätverksåtkomst

Tilläggspaket hämtas från Azure Storage-tilläggsdatabasen och tilläggsstatusöverföringar bokförs i Azure Storage. Om du använder den version av agenter som [stöds](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) behöver du inte tillåta åtkomst till Azure Storage i regionen VIRTUELL, eftersom agenten kan använda för att omdirigera kommunikationen till Azure fabric-styrenheten för agentkommunikation (HostGAPlugin-funktionen via den privilegierade kanalen på privat IP [168.63.129.16](https://docs.microsoft.com/azure/virtual-network/what-is-ip-address-168-63-129-16)). Om du använder en version som inte stöds av agenten måste du tillåta utgående åtkomst till Azure-lagring i den regionen från den virtuella datorn.

> [!IMPORTANT]
> Om du har blockerat åtkomsten till *168.63.129.16* med gästbrandväggen eller med en proxy, misslyckas tilläggen oavsett ovanstående. Portar 80, 443 och 32526 krävs.

Agenter kan endast användas för att hämta tilläggspaket och rapporteringsstatus. Om till exempel en tilläggsinstallation behöver hämta ett skript från GitHub (Anpassat skript) eller behöver åtkomst till Azure Storage (Azure Backup), måste ytterligare brandväggs-/nätverkssäkerhetsgruppportar öppnas. Olika tillägg har olika krav, eftersom de är applikationer i sin egen rätt. För tillägg som kräver åtkomst till Azure Storage eller Azure Active Directory kan du tillåta åtkomst med [Azure NSG-tjänsttaggar](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) till lagring eller AzureActiveDirectory.

Windows Guest Agent har inte proxyserver stöd för dig att omdirigera agent trafikförfrågningar genom, vilket innebär att Windows Guest Agent kommer att förlita sig på din anpassade proxy (om du har en) för att komma åt resurser på Internet eller på värden via IP 168.63.129.16.

## <a name="discover-vm-extensions"></a>Upptäck VM-tillägg

Det finns många olika VM-tillägg för användning med virtuella Azure-datorer. Om du vill se en fullständig lista använder du [Get-AzVMExtensionImage](https://docs.microsoft.com/powershell/module/az.compute/get-azvmextensionimage). I följande exempel visas alla tillgängliga tillägg på *WestUS-platsen:*

```powershell
Get-AzVmImagePublisher -Location "WestUS" | `
Get-AzVMExtensionImageType | `
Get-AzVMExtensionImage | Select Type, Version
```

## <a name="run-vm-extensions"></a>Kör VM-tillägg

Azure VM-tillägg körs på befintliga virtuella datorer, vilket är användbart när du behöver göra konfigurationsändringar eller återställa anslutningen på en redan distribuerad virtuell dator. VM-tillägg kan också levereras med Azure Resource Manager-malldistributioner. Genom att använda tillägg med Resource Manager-mallar kan virtuella Azure-datorer distribueras och konfigureras utan åtgärder efter distributionen.

Följande metoder kan användas för att köra ett tillägg mot en befintlig virtuell dator.

### <a name="powershell"></a>PowerShell

Det finns flera PowerShell-kommandon för att köra enskilda tillägg. Om du vill visa en lista använder du [Hämta kommando](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/get-command) och filter på *Tillägg:*

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

I följande exempel används tillägget Anpassat skript för att hämta ett skript från en GitHub-databas till den virtuella måldatorn och sedan köra skriptet. Mer information om tillägget Anpassat skript finns i [Översikt över anpassat skripttillägg](custom-script-windows.md).

```powershell
Set-AzVMCustomScriptExtension -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" -Name "myCustomScript" `
    -FileUri "https://raw.githubusercontent.com/neilpeterson/nepeters-azure-templates/master/windows-custom-script-simple/support-scripts/Create-File.ps1" `
    -Run "Create-File.ps1" -Location "West US"
```

I följande exempel används vm Access-tillägget för att återställa det administrativa lösenordet för en Windows-virtuell dator till ett tillfälligt lösenord. Mer information om vm Access-tillägget finns [i Återställa fjärrskrivbordstjänsten i en Windows VM](../windows/reset-rdp.md). När du har kört detta bör du återställa lösenordet vid första inloggningen:

```powershell
$cred=Get-Credential

Set-AzVMAccessExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myVMAccess" `
    -Location WestUS -UserName $cred.GetNetworkCredential().Username `
    -Password $cred.GetNetworkCredential().Password -typeHandlerVersion "2.0"
```

Kommandot `Set-AzVMExtension` kan användas för att starta alla VM-tillägg. Mer information finns i [referensen Set-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension).


### <a name="azure-portal"></a>Azure Portal

VM-tillägg kan tillämpas på en befintlig virtuell dator via Azure-portalen. Välj den virtuella datorn i portalen, välj **Tillägg**och välj sedan **Lägg till**. Välj det tillägg du vill använda i listan över tillgängliga tillägg och följ instruktionerna i guiden.

I följande exempel visas installationen av Tillägget Microsoft Antimalware från Azure-portalen:

![Installera tillägget för skadlig kod](./media/features-windows/installantimalwareextension.png)

### <a name="azure-resource-manager-templates"></a>Azure Resource Manager-mallar

VM-tillägg kan läggas till i en Azure Resource Manager-mall och köras med distributionen av mallen. När du distribuerar ett tillägg med en mall kan du skapa fullständigt konfigurerade Azure-distributioner. Följande JSON hämtas till exempel från en Resource Manager-mall som distribuerar en uppsättning belastningsbalanserade virtuella datorer och en Azure SQL-databas och installerar sedan ett .NET Core-program på varje virtuell dator. Vm-tillägget tar hand om programvaruinstallationen.

Mer information finns i den [fullständiga Resource Manager-mallen](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows).

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

Mer information om hur du skapar Resource Manager-mallar finns i [Skapa Azure Resource Manager-mallar med Windows VM-tillägg](../windows/template-description.md#extensions).

## <a name="secure-vm-extension-data"></a>Data för säker vm-tillägg

När du kör ett vm-tillägg kan det vara nödvändigt att inkludera känslig information som autentiseringsuppgifter, lagringskontonamn och åtkomstnycklar för lagringskonto. Många VM-tillägg innehåller en skyddad konfiguration som krypterar data och bara dekrypterar den inuti måldatorn. Varje tillägg har ett specifikt schema för skyddad konfiguration och var och en beskrivs i tilläggsspecifik dokumentation.

I följande exempel visas en förekomst av tillägget Anpassat skript för Windows. Kommandot som ska köras innehåller en uppsättning autentiseringsuppgifter. I det här exemplet är kommandot för att köra inte krypterat:

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

Om du flyttar **kommandot för att köra** egenskapen till den **skyddade** konfigurationen skyddas körningssträngen, vilket visas i följande exempel:

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

På en virtuell Azure IaaS-dator som använder tillägg kan du i certifikatkonsolen se certifikat som har ämnet **_Windows Azure CRP-certifikatgenerator_**. På en virtuell virtuell dator med klassiskt fjärrskrivbord har dessa certifikat ämnesnamnet **_Windows Azure Service Management for Extensions_**.

Dessa certifikat skyddar kommunikationen mellan den virtuella datorn och dess värd under överföringen av skyddade inställningar (lösenord, andra autentiseringsuppgifter) som används i tillägg. Certifikaten skapas av Azure-infrastrukturstyrenheten och skickas till VM-agenten. Om du stoppar och startar den virtuella datorn varje dag kan ett nytt certifikat skapas av infrastrukturstyrenheten. Certifikatet lagras i datorns personalcertifikatarkiv. Dessa certifikat kan tas bort. VM-agenten återskapar certifikat om det behövs.

### <a name="how-do-agents-and-extensions-get-updated"></a>Hur uppdateras agenter och tillägg?

Agenterna och tilläggen delar samma uppdateringsmekanism. Vissa uppdateringar kräver inga ytterligare brandväggsregler.

När en uppdatering är tillgänglig installeras den bara på den virtuella datorn när det sker en ändring av tillägg och andra vm-modelländringar, till exempel:

- Datadiskar
- Tillägg
- Behållare för startdiagnostik
- Gäst OS hemligheter
- Storlek på virtuell dator
- Nätverksprofil

Utgivare gör uppdateringar tillgängliga för regioner vid olika tidpunkter, så det är möjligt att du kan ha virtuella datorer i olika regioner i olika versioner.

#### <a name="listing-extensions-deployed-to-a-vm"></a>Lista tillägg som distribuerats till en virtuell dator

```powershell
$vm = Get-AzVM -ResourceGroupName "myResourceGroup" -VMName "myVM"
$vm.Extensions | select Publisher, VirtualMachineExtensionType, TypeHandlerVersion
```

```powershell
Publisher             VirtualMachineExtensionType          TypeHandlerVersion
---------             ---------------------------          ------------------
Microsoft.Compute     CustomScriptExtension                1.9
```

#### <a name="agent-updates"></a>Agent uppdateringar

Windows-gästagenten innehåller endast *kod för tilläggshantering*, *Windows-etableringskoden* är separat. Du kan avinstallera Windows-gästagenten. Du kan inte inaktivera den automatiska uppdateringen av Fönstergästagenten.

*Koden för tilläggshantering* ansvarar för att kommunicera med Azure-infrastrukturen och hantera vm-tilläggsåtgärder som installationer, rapporteringsstatus, uppdatera enskilda tillägg och ta bort dem. Uppdateringar innehåller säkerhetskorrigeringar, buggfixar och förbättringar av *koden för förlängningshantering*.

Mer om hur du kontrollerar vilken version du kör finns i [Identifiera installerade Windows Guest Agent](agent-windows.md#detect-the-vm-agent).

#### <a name="extension-updates"></a>Tilläggsuppdateringar

När en tilläggsuppdatering är tillgänglig hämtar och uppgraderar Windows Guest Agent tillägget. Automatiska tilläggsuppdateringar är antingen *Minor* eller *Hotfix*. Du kan välja eller välja bort tillägg *Mindre* uppdateringar när du etablerar tillägget. I följande exempel visas hur du automatiskt uppgraderar delversioner i en Resource Manager-mall med *autoUpgradeMinorVersion": true":*

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

För att få de senaste felkorrigeringarna för mindre versionen rekommenderar vi starkt att du alltid väljer automatisk uppdatering i dina tilläggsdistributioner. Snabbkorrigeringsuppdateringar som innehåller säkerhets- eller nyckelfelskorrigeringar kan inte väljas bort.

### <a name="how-to-identify-extension-updates"></a>Identifiera tilläggsuppdateringar

#### <a name="identifying-if-the-extension-is-set-with-autoupgrademinorversion-on-a-vm"></a>Identifiera om tillägget är inställt med autoUpgradeMinorVersion på en virtuell dator

Du kan se från vm-modellen om tillägget har etablerats med autoUpgradeMinorVersion. Kontrollera genom att använda [Get-AzVm](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) och ange resursgruppen och VM-namnet enligt följande:

```powerShell
 $vm = Get-AzVm -ResourceGroupName "myResourceGroup" -VMName "myVM"
 $vm.Extensions
```

Följande exempelutdata visar att *autoUpgradeMinorVersion* är inställt på *true:*

```powershell
ForceUpdateTag              :
Publisher                   : Microsoft.Compute
VirtualMachineExtensionType : CustomScriptExtension
TypeHandlerVersion          : 1.9
AutoUpgradeMinorVersion     : True
```

#### <a name="identifying-when-an-autoupgrademinorversion-occurred"></a>Identifiera när en autoUpgradeMinorVersion inträffade

Om du vill se när en uppdatering av tillägget inträffade läser du agentloggarna på den virtuella datorn på *C:\WindowsAzure\Logs\WaAppAgent.log*

I följande exempel hade den virtuella datorn *Microsoft.Compute.CustomScriptExtension 1.8* installerat. En snabbkorrigering var tillgänglig för version *1.9:*

```powershell
[INFO]  Getting plugin locations for plugin 'Microsoft.Compute.CustomScriptExtension'. Current Version: '1.8', Requested Version: '1.9'
[INFO]  Auto-Upgrade mode. Highest public version for plugin 'Microsoft.Compute.CustomScriptExtension' with requested version: '1.9', is: '1.9'
```

## <a name="agent-permissions"></a>Agentbehörigheter

För att kunna utföra sina uppgifter måste agenten köras som *lokalt system*.

## <a name="troubleshoot-vm-extensions"></a>Felsöka VM-tillägg

Varje VM-tillägg kan ha felsökningssteg som är specifika för tillägget. När du till exempel använder tillägget Anpassat skript finns information om skriptkörning lokalt på den virtuella datorn där tillägget kördes. Alla tilläggsspecifika felsökningssteg beskrivs i tilläggsspecifik dokumentation.

Följande felsökningssteg gäller för alla VM-tillägg.

1. Om du vill kontrollera Loggen för Windows-gästagenter tittar du på aktiviteten när tillägget etablerades i *C:\WindowsAzure\Logs\WaAppAgent.log*

2. Mer information finns i tilläggsloggarna *c:\WindowsAzure\Logs\Plugins\<extensionName>*

3. Kontrollera tilläggsspecifika felsökningsavsnitt för felkoder, kända problem etc.

4. Titta på systemloggarna. Kontrollera om det finns andra åtgärder som kan ha stört tillägget, till exempel en tidskrävande installation av ett annat program som krävde exklusiv pakethanterare.

### <a name="common-reasons-for-extension-failures"></a>Vanliga orsaker till tilläggsfel

1. Tillägg har 20 minuter att köra (undantag är CustomScript-tillägg, chef och DSC som har 90 minuter). Om distributionen överskrider den här tiden markeras den som en timeout. Orsaken till detta kan bero på virtuella resursdämmor, andra vm-konfigurationer/startaktiviteter som förbrukar stora mängder resurser medan tillägget försöker etablera.

2. Minimikraven är inte uppfyllda. Vissa tillägg har beroenden av virtuella SKU:er, till exempel HPC-avbildningar. Tillägg kan kräva vissa åtkomstkrav för nätverk, till exempel kommunikation med Azure Storage eller offentliga tjänster. Andra exempel kan vara åtkomst till paketdatabaser, på diskutrymme eller säkerhetsbegränsningar.

3. Exklusiv pakethanterare. I vissa fall kan du stöta på en tidskrävande VM-konfiguration och tilläggsinstallation i konflikt, där de båda behöver exklusiv åtkomst till pakethanteraren.

### <a name="view-extension-status"></a>Visa tilläggsstatus

När ett VM-tillägg har körts mot en virtuell dator använder du [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) för att returnera tilläggsstatus. *Understatus[0]* visar att tilläggets etablering lyckades, vilket innebär att den lyckades distribueras till den virtuella datorn, men körningen av tillägget inuti den virtuella datorn misslyckades, *Understatuses[1]*.

```powershell
Get-AzVM -ResourceGroupName "myResourceGroup" -VMName "myVM" -Status
```

Utdata liknar följande exempelutdata:

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

Status för körning av tillägg finns också i Azure-portalen. Om du vill visa status för ett tillägg väljer du den virtuella datorn, väljer **Tillägg**och väljer sedan önskat tillägg.

### <a name="rerun-vm-extensions"></a>Kör VM-tillägg igen

Det kan finnas fall där ett vm-tillägg måste köras igen. Du kan köra ett tillägg igen genom att ta bort det och sedan köra tillägget igen med valfri körningsmetod. Om du vill ta bort ett tillägg använder du [Ta bort AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/Remove-AzVMExtension) enligt följande:

```powershell
Remove-AzVMExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myExtensionName"
```

Du kan också ta bort ett tillägg i Azure-portalen på följande sätt:

1. Välj en virtuell dator.
2. Välj **Tillägg**.
3. Välj önskat tillägg.
4. Välj **Avinstallera**.

## <a name="common-vm-extensions-reference"></a>Referens för vanliga VM-tillägg
| Tilläggsnamn | Beskrivning | Mer information |
| --- | --- | --- |
| Anpassat skripttillägg för Windows |Köra skript mot en virtuell Azure-dator |[Anpassat skripttillägg för Windows](custom-script-windows.md) |
| DSC-tillägg för Windows |PowerShell DSC-tillägg (önskat tillståndskonfiguration) |[DSC-tillägg för Windows](dsc-overview.md) |
| Azure Diagnostics-tillägg |Hantera Azure Diagnostics |[Azure Diagnostics-tillägg](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Azure VM-åtkomsttillägg |Hantera användare och autentiseringsuppgifter |[VM-åtkomsttillägg för Linux](https://azure.microsoft.com/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |

## <a name="next-steps"></a>Nästa steg

Mer information om vm-tillägg finns i [Azures översikt över virtuella datorer och funktioner](overview.md).
