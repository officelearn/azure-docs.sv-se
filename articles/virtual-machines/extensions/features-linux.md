---
title: Azure VM-tillägg och funktioner för Linux
description: Läs om vilka tillägg som är tillgängliga för virtuella Azure-datorer, grupperade efter vad de tillhandahåller eller förbättrar.
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 52f5d0ec-8f75-49e7-9e15-88d46b420e63
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/30/2018
ms.author: akjosh
ms.openlocfilehash: 67df46742be52b03bd91af19654fbfac5df29646
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250522"
---
# <a name="virtual-machine-extensions-and-features-for-linux"></a>Tillägg och funktioner för virtuella datorer för Linux

Azure-tillägg för virtuella datorer är små program som ger konfigurations- och automationsuppgifter på virtuella Azure-datorer efter distribution. Om en virtuell dator till exempel behöver programvaruinstallation, antivirusskydd eller körning av ett skript på den kan ett VM-tillägg användas. Azure VM-tillägg kan köras med Azure CLI, PowerShell, Azure Resource Manager-mallar och Azure-portalen. Tillägg kan paketeras med en ny VM-distribution eller köras mot valfritt befintligt system.

Den här artikeln innehåller en översikt över VM-tillägg, förutsättningar för att använda Azure VM-tillägg och vägledning om hur du identifierar, hanterar och tar bort VM-tillägg. Den här artikeln innehåller allmän information eftersom många VM-tillägg är tillgängliga, var och en med en potentiellt unik konfiguration. Tilläggsspecifika detaljer finns i varje dokument som är specifikt för det enskilda tillägget.

## <a name="use-cases-and-samples"></a>Användningsfall och prover

Flera olika Azure VM-tillägg är tillgängliga, var och en med ett specifikt användningsfall. Några exempel är:

- Använd PowerShell-konfigurationer för önskat tillstånd på en virtuell dator med DSC-tillägget för Linux. Mer information finns i [Azure Desired State configuration extension](https://github.com/Azure/azure-linux-extensions/tree/master/DSC).
- Konfigurera övervakning av en virtuell dator med tillägget Microsoft Monitoring Agent VM. Mer information finns i [Så här övervakar du en Virtuell Linux-dator](../linux/tutorial-monitoring.md).
- Konfigurera övervakning av din Azure-infrastruktur med tillägget Chef eller Datadog. Mer information finns i [chefdokumenten eller](https://docs.chef.io/azure_portal.html) [datadogbloggen](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/).

Förutom processspecifika tillägg är ett anpassat skripttillägg tillgängligt för både virtuella Windows- och Linux-datorer. Custom Script-tillägget för Linux gör att alla Bash-skript kan köras på en virtuell dator. Anpassade skript är användbara för att utforma Azure-distributioner som kräver konfiguration utöver vad inbyggda Azure-verktyg kan ge. Mer information finns i [Linux VM Custom Script extension](custom-script-linux.md).

## <a name="prerequisites"></a>Krav

För att hantera tillägget på den virtuella datorn behöver du Azure Linux Agent installerad. Vissa enskilda tillägg har förutsättningar, till exempel åtkomst till resurser eller beroenden.

### <a name="azure-vm-agent"></a>Virtuell Azure-datoragent

Azure VM-agenten hanterar interaktioner mellan en Azure VM och Azure-infrastrukturstyrenheten. VM-agenten ansvarar för många funktionella aspekter av distribution och hantering av virtuella Azure-datorer, inklusive att köra VM-tillägg. Azure VM-agenten är förinstallerad på Azure Marketplace-avbildningar och kan installeras manuellt på operativsystem som stöds. Azure VM-agenten för Linux kallas Linux-agenten.

Information om operativsystem och installationsinstruktioner som stöds finns i [Azures virtuella datoragent](agent-linux.md).

#### <a name="supported-agent-versions"></a>Agentversioner som stöds

För att ge bästa möjliga upplevelse, det finns minimiversioner av agenten. Mer information finns i [den här artikeln](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

#### <a name="supported-oses"></a>OSes som stöds

Linux-agenten körs på flera OSes, men tilläggsramverket har en gräns för de OSes som tillägg. Mer information finns i [den här artikeln](https://support.microsoft.com/en-us/help/4078134/azure-extension-supported-operating-systems
).

Vissa tillägg stöds inte i alla operativsystem och kan avge *felkod 51, "Os som inte stöds".* Kontrollera den enskilda tilläggsdokumentationen för support.

#### <a name="network-access"></a>Nätverksåtkomst

Tilläggspaket hämtas från Azure Storage-tilläggsdatabasen och tilläggsstatusöverföringar bokförs i Azure Storage. Om du använder den version av agenter som [stöds](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) behöver du inte tillåta åtkomst till Azure Storage i regionen VIRTUELL, som kan använda agenten för att omdirigera kommunikationen till Azure fabric-styrenheten för agentkommunikation. Om du använder en version som inte stöds av agenten måste du tillåta utgående åtkomst till Azure-lagring i den regionen från den virtuella datorn.

> [!IMPORTANT]
> Om du har blockerat åtkomst till *168.63.129.16* med gästbrandväggen misslyckas tilläggen oavsett ovanstående.

Agenter kan endast användas för att hämta tilläggspaket och rapporteringsstatus. Om till exempel en tilläggsinstallation behöver hämta ett skript från GitHub (Anpassat skript) eller behöver åtkomst till Azure Storage (Azure Backup), måste ytterligare brandväggs-/nätverkssäkerhetsgruppportar öppnas. Olika tillägg har olika krav, eftersom de är applikationer i sin egen rätt. För tillägg som kräver åtkomst till Azure Storage kan du tillåta åtkomst med Azure NSG-tjänsttaggar för [lagring](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

För att omdirigera agent trafikbegäranden har Linux-agenten proxyserver stöd. Det här proxyserverstödet gäller dock inte tillägg. Du måste konfigurera varje enskilt tillägg så att det fungerar med en proxy.

## <a name="discover-vm-extensions"></a>Upptäck VM-tillägg

Det finns många olika VM-tillägg för användning med virtuella Azure-datorer. Om du vill se en fullständig lista använder du [bildlistan för virtuell vm-tillägg](/cli/azure/vm/extension/image#az-vm-extension-image-list). I följande exempel visas alla tillgängliga tillägg på *westus-platsen:*

```azurecli
az vm extension image list --location westus --output table
```

## <a name="run-vm-extensions"></a>Kör VM-tillägg

Azure VM-tillägg körs på befintliga virtuella datorer, vilket är användbart när du behöver göra konfigurationsändringar eller återställa anslutningen på en redan distribuerad virtuell dator. VM-tillägg kan också levereras med Azure Resource Manager-malldistributioner. Genom att använda tillägg med Resource Manager-mallar kan virtuella Azure-datorer distribueras och konfigureras utan åtgärder efter distributionen.

Följande metoder kan användas för att köra ett tillägg mot en befintlig virtuell dator.

### <a name="azure-cli"></a>Azure CLI

Azure VM-tillägg kan köras mot en befintlig virtuell dator med kommandot [az vm-tilläggsuppsättning.](/cli/azure/vm/extension#az-vm-extension-set) I följande exempel körs tillägget Anpassat skript mot en virtuell dator med namnet *myVM* i en resursgrupp med namnet *myResourceGroup*. Ersätt exempelresursgruppens namn, VM-namn och\/skript som ska köras (https: /raw.githubusercontent.com/me/project/hello.sh) med din egen information. 

```azurecli
az vm extension set `
  --resource-group myResourceGroup `
  --vm-name myVM `
  --name customScript `
  --publisher Microsoft.Azure.Extensions `
  --settings '{"fileUris": ["https://raw.githubusercontent.com/me/project/hello.sh"],"commandToExecute": "./hello.sh"}'
```

När tillägget körs korrekt liknar utdata följande exempel:

```bash
info:    Executing command vm extension set
+ Looking up the VM "myVM"
+ Installing extension "CustomScript", VM: "mvVM"
info:    vm extension set command OK
```

### <a name="azure-portal"></a>Azure Portal

VM-tillägg kan tillämpas på en befintlig virtuell dator via Azure-portalen. Välj den virtuella datorn i portalen, välj **Tillägg**och välj sedan **Lägg till**. Välj det tillägg du vill använda i listan över tillgängliga tillägg och följ instruktionerna i guiden.

Följande avbildning visar installationen av tillägget Linux Custom Script från Azure-portalen:

![Installera anpassat skripttillägg](./media/features-linux/installscriptextensionlinux.png)

### <a name="azure-resource-manager-templates"></a>Azure Resource Manager-mallar

VM-tillägg kan läggas till i en Azure Resource Manager-mall och köras med distributionen av mallen. När du distribuerar ett tillägg med en mall kan du skapa fullständigt konfigurerade Azure-distributioner. Följande JSON hämtas till exempel från en Resource Manager-mall som distribuerar en uppsättning belastningsbalanserade virtuella datorer och en Azure SQL-databas och installerar sedan ett .NET Core-program på varje virtuell dator. Vm-tillägget tar hand om programvaruinstallationen.

Mer information finns i den fullständiga [Resource Manager-mallen](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
    }
}
```

Mer information om hur du skapar Resource Manager-mallar finns i [Skapa Azure Resource Manager-mallar](../windows/template-description.md#extensions).

## <a name="secure-vm-extension-data"></a>Data för säker vm-tillägg

När du kör ett vm-tillägg kan det vara nödvändigt att inkludera känslig information som autentiseringsuppgifter, lagringskontonamn och åtkomstnycklar för lagringskonto. Många VM-tillägg innehåller en skyddad konfiguration som krypterar data och bara dekrypterar den inuti måldatorn. Varje tillägg har ett specifikt schema för skyddad konfiguration och var och en beskrivs i tilläggsspecifik dokumentation.

I följande exempel visas en instans av tillägget Anpassat skript för Linux. Kommandot som ska köras innehåller en uppsättning autentiseringsuppgifter. I det här exemplet är kommandot för att köra inte krypterat:

```json
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
      ],
      "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
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
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
      ]
    },
    "protectedSettings": {
      "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
  }
}
```

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

#### <a name="agent-updates"></a>Agent uppdateringar

Linux VM-agenten innehåller *etableringsagentkod* och *extension-hanteringskod* i ett paket som inte kan separeras. Du kan inaktivera *etableringsagenten* när du vill etablera på Azure med hjälp av cloud-init. Det gör du genom [att använda cloud-init](../linux/using-cloud-init.md).

Versioner av ombud som stöds kan använda automatiska uppdateringar. Den enda kod som kan uppdateras är *koden för tilläggshantering*, inte etableringskoden. *Etableringsagentkoden* körs en gång-kod.

*Koden för tilläggshantering* ansvarar för att kommunicera med Azure-infrastrukturen och hantera vm-tilläggsåtgärder som installationer, rapporteringsstatus, uppdatera enskilda tillägg och ta bort dem. Uppdateringar innehåller säkerhetskorrigeringar, buggfixar och förbättringar av *koden för förlängningshantering*.

När agenten installeras skapas en överordnad demon. Den här föräldern ger sedan upphov till en underordnad process som används för att hantera tillägg. Om en uppdatering är tillgänglig för agenten hämtas den, den överordnade stoppar den underordnade processen, uppgraderar den och startar sedan om den. Om det skulle vara problem med uppdateringen återställs den överordnade processen till den tidigare underordnade versionen.

Den överordnade processen kan inte uppdateras automatiskt. Den överordnade kan bara uppdateras med en distributionspaketuppdatering.

Kontrollera vilken version du kör `waagent` genom att kontrollera följande:

```bash
waagent --version
```

Utdata ser ut ungefär så här:

```bash
WALinuxAgent-2.2.17 running on ubuntu 16.04
Python: 3.5.2
Goal state agent: 2.2.18
```

I föregående exempelutdata är den överordnade versionen eller "paketut distribuerad version" *WALinuxAgent-2.2.17*

"Måltillståndsagenten" är den automatiska uppdateringsversionen.

Vi rekommenderar starkt att du alltid har automatisk uppdatering för [agenten, AutoUpdate.Enabled=y](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent). Att inte ha detta aktiverat innebär att du måste hålla manuellt uppdatera agenten, och inte få bugg och säkerhetskorrigeringar.

#### <a name="extension-updates"></a>Tilläggsuppdateringar

När en tilläggsuppdatering är tillgänglig hämtar och uppgraderar Linux Agent tillägget. Automatiska tilläggsuppdateringar är antingen *Minor* eller *Hotfix*. Du kan välja eller välja bort tillägg *Mindre* uppdateringar när du etablerar tillägget. I följande exempel visas hur du automatiskt uppgraderar delversioner i en Resource Manager-mall med *autoUpgradeMinorVersion": true":*

```json
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
        ]
    },
```

För att få de senaste felkorrigeringarna för mindre versionen rekommenderar vi starkt att du alltid väljer automatisk uppdatering i dina tilläggsdistributioner. Snabbkorrigeringsuppdateringar som innehåller säkerhets- eller nyckelfelskorrigeringar kan inte väljas bort.

### <a name="how-to-identify-extension-updates"></a>Identifiera tilläggsuppdateringar

#### <a name="identifying-if-the-extension-is-set-with-autoupgrademinorversion-on-a-vm"></a>Identifiera om tillägget är inställt med autoUpgradeMinorVersion på en virtuell dator

Du kan se från vm-modellen om tillägget har etablerats med autoUpgradeMinorVersion. Om du vill kontrollera använder du [az vm-visning](/cli/azure/vm#az-vm-show) och anger resursgruppen och VM-namnet enligt följande:

```azurecli
az vm show --resource-group myResourceGroup --name myVM
```

Följande exempelutdata visar att *autoUpgradeMinorVersion* är inställt på *true:*

```json
  "resources": [
    {
      "autoUpgradeMinorVersion": true,
      "forceUpdateTag": null,
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM/extensions/CustomScriptExtension",
```

#### <a name="identifying-when-an-autoupgrademinorversion-occurred"></a>Identifiera när en autoUpgradeMinorVersion inträffade

Om du vill se när en uppdatering av tillägget inträffade granskar du agentloggarna på den virtuella datorn på */var/log/waagent.log*.

I exemplet nedan hade den virtuella datorn *Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9025* installerat. En snabbkorrigering var tillgänglig för *Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027:*

```bash
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Expected handler state: enabled
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Decide which version to use
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Use version: 2.3.9027
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Current handler state is: NotInstalled
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Download extension package
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Unpack extension package
INFO Event: name=Microsoft.OSTCExtensions.LinuxDiagnostic, op=Download, message=Download succeeded
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Initialize extension directory
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Update settings file: 0.settings
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9025] Disable extension.
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9025] Launch command:diagnostic.py -disable
...
INFO Event: name=Microsoft.OSTCExtensions.LinuxDiagnostic, op=Disable, message=Launch command succeeded: diagnostic.py -disable
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Update extension.
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Launch command:diagnostic.py -update
2017/08/14 20:21:57 LinuxAzureDiagnostic started to handle.
```

## <a name="agent-permissions"></a>Agentbehörigheter

Agenten måste köras som *root*för att kunna utföra sina uppgifter .

## <a name="troubleshoot-vm-extensions"></a>Felsöka VM-tillägg

Varje VM-tillägg kan ha felsökningssteg som är specifika för tillägget. När du till exempel använder tillägget Anpassat skript finns information om skriptkörning lokalt på den virtuella datorn där tillägget kördes. Alla tilläggsspecifika felsökningssteg beskrivs i tilläggsspecifik dokumentation.

Följande felsökningssteg gäller för alla VM-tillägg.

1. För att kontrollera Linux Agent Log, titta på aktiviteten när ditt tillägg var etableras i */ var / log /waagent.log*

2. Mer information finns i */var/log/azure/extensionName\<>*

3. Kontrollera felsökningsavsnitt för tilläggsspecifik dokumentation för felkoder, kända problem etc.

3. Titta på systemloggarna. Kontrollera om det finns andra åtgärder som kan ha stört tillägget, till exempel en tidskrävande installation av ett annat program som krävde exklusiv pakethanterare.

### <a name="common-reasons-for-extension-failures"></a>Vanliga orsaker till tilläggsfel

1. Tillägg har 20 minuter att köra (undantag är CustomScript-tillägg, chef och DSC som har 90 minuter). Om distributionen överskrider den här tiden markeras den som en timeout. Orsaken till detta kan bero på virtuella resursdämmor, andra vm-konfigurationer/startaktiviteter som förbrukar stora mängder resurser medan tillägget försöker etablera.

2. Minimikraven är inte uppfyllda. Vissa tillägg har beroenden av virtuella SKU:er, till exempel HPC-avbildningar. Tillägg kan kräva vissa åtkomstkrav för nätverk, till exempel kommunikation med Azure Storage eller offentliga tjänster. Andra exempel kan vara åtkomst till paketdatabaser, på diskutrymme eller säkerhetsbegränsningar.

3. Exklusiv pakethanterare. I vissa fall kan du stöta på en tidskrävande VM-konfiguration och tilläggsinstallation i konflikt, där de båda behöver exklusiv åtkomst till pakethanteraren.

### <a name="view-extension-status"></a>Visa tilläggsstatus

När ett VM-tillägg har körts mot en virtuell dator använder du [az vm get-instance-view](/cli/azure/vm#az-vm-get-instance-view) för att returnera tilläggsstatus enligt följande:

```azurecli
az vm get-instance-view \
    --resource-group rgName \
    --name myVM \
    --query "instanceView.extensions"
```

Utdata liknar följande exempelutdata:

```bash
  {
    "name": "customScript",
    "statuses": [
      {
        "code": "ProvisioningState/failed/0",
        "displayStatus": "Provisioning failed",
        "level": "Error",
        "message": "Enable failed: failed to execute command: command terminated with exit status=127\n[stdout]\n\n[stderr]\n/bin/sh: 1: ech: not found\n",
        "time": null
      }
    ],
    "substatuses": null,
    "type": "Microsoft.Azure.Extensions.customScript",
    "typeHandlerVersion": "2.0.6"
  }
```

Status för körning av tillägg finns också i Azure-portalen. Om du vill visa status för ett tillägg väljer du den virtuella datorn, väljer **Tillägg**och väljer sedan önskat tillägg.

### <a name="rerun-a-vm-extension"></a>Köra ett vm-tillägg igen

Det kan finnas fall där ett vm-tillägg måste köras igen. Du kan köra ett tillägg igen genom att ta bort det och sedan köra tillägget igen med valfri körningsmetod. Om du vill ta bort ett tillägg använder du [az vm-tillägget ta bort](/cli/azure/vm/extension#az-vm-extension-delete) enligt följande:

```azurecli
az vm extension delete \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name customScript
```

Du kan också ta bort ett tillägg i Azure-portalen på följande sätt:

1. Välj en virtuell dator.
2. Välj **Tillägg**.
3. Välj önskat tillägg.
4. Välj **Avinstallera**.

## <a name="common-vm-extension-reference"></a>Gemensam referens för vm-tillägg

| Tilläggsnamn | Beskrivning | Mer information |
| --- | --- | --- |
| Anpassat skripttillägg för Linux |Köra skript mot en virtuell Azure-dator |[Anpassat skripttillägg för Linux](custom-script-linux.md) |
| VM Access-tillägg |Återfå åtkomsten till en virtuell Azure-dator |[VM Access-tillägg](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) |
| Azure Diagnostics-tillägg |Hantera Azure Diagnostics |[Azure Diagnostics-tillägg](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Azure VM Access-tillägg |Hantera användare och autentiseringsuppgifter |[VM-åtkomsttillägg för Linux](https://azure.microsoft.com/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |

## <a name="next-steps"></a>Nästa steg

Mer information om vm-tillägg finns i [Azures översikt över virtuella datorer och funktioner](overview.md).
