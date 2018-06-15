---
title: Azure VM-tillägg och funktioner för Linux | Microsoft Docs
description: Lär dig vilka tillägg som är tillgängliga för virtuella Azure-datorer, grupperade efter vad de tillhandahålla och förbättra.
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 52f5d0ec-8f75-49e7-9e15-88d46b420e63
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/30/2018
ms.author: danis
ms.openlocfilehash: 760f832bc12bccbf1cce77db25bf60413ad9a36b
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
ms.locfileid: "33942832"
---
# <a name="virtual-machine-extensions-and-features-for-linux"></a>Tillägg för virtuell dator och funktioner för Linux

Virtuell Azure-dator (VM)-tillägg är små program som innehåller efter distributionen konfiguration och automatisering av uppgifter på Azure Virtual Machines. Om exempelvis en virtuell dator kräver Programvaruinstallation, antivirus skydd, eller om du vill köra ett skript inuti den VM-tillägget kan användas. Azure VM-tillägg kan köras med Azure CLI, PowerShell, Azure Resource Manager-mallar och Azure-portalen. Tillägg kan levereras tillsammans med en ny VM-distribution eller köra mot alla befintliga system.

Den här artikeln innehåller en översikt över krav för att använda Azure VM-tillägg på VM-tillägg och anvisningar om hur du identifierar, hantera och ta bort VM-tillägg. Den här artikeln innehåller allmänna informationen eftersom många VM-tillägg är tillgängligt, var och en med en potentiellt unik konfiguration. Tillägget-specifik information finns i varje dokument specifika för enskilda tillägg.

## <a name="use-cases-and-samples"></a>Användningsfall och exempel

Det finns flera olika Virtuella Azure-tillägg, var och en med en specifik användningsfall. Några exempel är:

- Använd PowerShell önskade tillstånd konfigurationer på en virtuell dator med DSC-tillägg för Linux. Mer information finns i [tillägg för konfiguration av Azure önskade tillstånd](https://github.com/Azure/azure-linux-extensions/tree/master/DSC).
- Konfigurera övervakning av en virtuell dator med Microsoft Monitoring Agent VM-tillägget. Mer information finns i [hur du övervakar en Linux VM](../linux/tutorial-monitoring.md).
- Konfigurera övervakning av Azure-infrastrukturen med filnamnstillägget Chef eller Datadog. Mer information finns i [Chef docs](https://docs.chef.io/azure_portal.html) eller [Datadog blogg](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/).

Förutom processpecifika tillägg är ett tillägg för anpassat skript tillgängligt för både Windows och Linux virtuella datorer. Tillägget för anpassat skript för Linux kan alla Bash-skript kan köras på en virtuell dator. Anpassade skript är användbara för att utforma Azure-distributioner som kräver konfiguration efter vilken interna Azure verktygsuppsättning kan ge. Mer information finns i [tillägget för anpassat skript för Linux Virtuella](custom-script-linux.md).

## <a name="prerequisites"></a>Förutsättningar

För att hantera tillägg på den virtuella datorn måste Azure Linux-agenten installerad. Vissa enskilda tillägg har förutsättningar, till exempel åtkomst till resurser eller beroenden.

### <a name="azure-vm-agent"></a>Virtuell Azure-datoragent

Virtuella Azure-agenten hanterar samverkan mellan en virtuell dator i Azure och Azure-infrastrukturkontrollanten. Den Virtuella datoragenten är ansvarig för många funktioner för att distribuera och hantera virtuella Azure-datorer, inklusive kör VM-tillägg. Virtuella Azure-agenten är förinstallerat på Azure Marketplace-bilder och kan installeras manuellt på operativsystem som stöds. Azure VM-agenten för Linux kallas Linux-agenten.

Information om operativsystem som stöds och installationsanvisningar finns [virtuella Azure-datorn agent](agent-linux.md).

#### <a name="supported-agent-versions"></a>Agent som stöds versioner

För att kunna ge bästa möjliga upplevelse, finns det lägsta versioner av agenten. Mer information finns i [den här artikeln](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

#### <a name="supported-oses"></a>Stödda OS

Linux-agenten körs på flera OS men tillägg framework har en gräns för operativsystem som tillägg. Mer information finns i [den här artikeln] (https://support.microsoft.com/en-us/help/4078134/azure-extension-supported-operating-systems ).

Vissa tillägg stöds inte över alla operativsystem och kan skapa *felkod 51, 'stöds inte OS-*. I dokumentationen enskilda tillägg för support.

#### <a name="network-access"></a>Nätverksåtkomst

Tilläggspaket hämtas från databasen för Azure Storage-tillägget och tillägget status överföringar skickas till Azure Storage. Om du använder [stöds](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) versionen av agenter, du behöver inte tillåter åtkomst till Azure Storage i VM-region som agenten kan använda för att dirigera kommunikation till Azure-infrastrukturkontrollanten för agenten kommunikation. Om du är på en version som inte stöds av agenten måste du tillåta utgående åtkomst till Azure-lagring i den regionen från den virtuella datorn.

> [!IMPORTANT]
> Om du har blockerat åtkomsten till *168.63.129.1* med gäst-brandväggen, sedan tillägg misslyckas oavsett ovan.

Agenter kan bara användas för att ladda ned tilläggspaket och Rapporteringsstatus. Till exempel om en installation av tillägget måste hämtas skript från GitHub (anpassade skript) eller behöver åtkomst till Azure Storage (Azure Backup) sedan ytterligare brandvägg/nätverk Säkerhet grupp portar måste öppnas. Olika tillägg har olika krav, eftersom de är program i sin egen rätt. För tillägg som kräver åtkomst till Azure Storage kan du tillåta åtkomst med hjälp av Azure NSG Service taggar för [lagring](https://docs.microsoft.com/en-us/azure/virtual-network/security-overview#service-tags).

Om du vill dirigera trafik agentbegäranden har Linux-agenten stöd för proxyserver. Dock gäller inte stöd för denna proxyserver tillägg. Du måste konfigurera varje enskilda tillägg ska fungera med en proxy.

## <a name="discover-vm-extensions"></a>Identifiera VM-tillägg

Det finns många olika VM-tillägg för användning med virtuella Azure-datorer. Om du vill se en fullständig lista över [az vm-tillägget bildlista](/cli/azure/vm/extension/image#az-vm-extension-image-list). I följande exempel visar en lista över alla tillgängliga tillägg i den *westus* plats:

```azurecli
az vm extension image list --location westus --output table
```

## <a name="run-vm-extensions"></a>Kör VM-tillägg

Azure VM-tillägg som kör på befintliga virtuella datorer, vilket är användbart när du behöver göra konfigurationsändringar eller återställa anslutningen på en redan distribuerad virtuell dator. VM-tillägg kan också tillsammans med Azure Resource Manager mall-distributioner. Genom att använda tillägg med Resource Manager-mallar, virtuella datorer i Azure distribuerats och konfigurerats utan åtgärder från efter distributionen.

Följande metoder kan användas för att köra ett tillägg mot en befintlig virtuell dator.

### <a name="azure-cli-20"></a>Azure CLI 2.0

Azure VM-tillägg kan köras mot en befintlig virtuell dator med den [az vm-tillägget set](/cli/azure/vm/extension#az-vm-extension-set) kommando. Följande exempel kör tillägget för anpassat skript mot en virtuell dator med namnet *myVM* i en resursgrupp med namnet *myResourceGroup*:

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

VM-tillägg kan tillämpas på en befintlig virtuell dator via Azure-portalen. Välj den virtuella datorn i portalen, Välj **tillägg**och välj **Lägg till**. Välj tillägg du vill använda från listan över tillgängliga tillägg och följ instruktionerna i guiden.

Följande bild visar installationen av tillägget för anpassat skript för Linux från Azure portal:

![Installera tillägget för anpassat skript](./media/features-linux/installscriptextensionlinux.png)

### <a name="azure-resource-manager-templates"></a>Azure Resource Manager-mallar

VM-tillägg kan läggas till i en Azure Resource Manager-mall och utförs med distributionen av mallen. När du distribuerar ett tillägg med en mall kan skapa du helt konfigurerade Azure-distributioner. Till exempel hämtas följande JSON från en Resource Manager-mall som distribuerar en uppsättning belastningsutjämnade virtuella datorer och en Azure SQL database och sedan installerar ett program med .NET Core på varje virtuell dator. Tillägg för virtuell dator hand tar om installationen av programmet.

Mer information finns i hela [Resource Manager-mall](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).

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

Mer information om hur du skapar Resource Manager-mallar finns [redigera Azure Resource Manager-mallar](../windows/template-description.md#extensions).

## <a name="secure-vm-extension-data"></a>Säkra data för VM-tillägg

När du kör en VM-tillägget, kan det vara nödvändigt att inkludera känslig information, till exempel autentiseringsuppgifter, lagringskontonamn och åtkomst för lagringskontonycklar. Många VM-tillägg innehåller en skyddad konfiguration som krypterar data och dekrypterar dem endast inuti VM målet. Varje tillägg innehåller en specifik skyddade konfigurationsschema och varje beskrivs i dokumentationen för specifika tillägg.

I följande exempel visas en instans av tillägget för anpassat skript för Linux. Kommandot för att köra innehåller en uppsättning autentiseringsuppgifter. I det här exemplet krypteras inte kommandot att köras:

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

Flytta den **kommando att köra** egenskapen till den **skyddade** konfigurationen skyddar körning-strängen som visas i följande exempel:

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

#### <a name="agent-updates"></a>Agentuppdateringar

Linux VM-agenten innehåller *etablering Agent kod* och *tillägget hantera koden* i ett paket som inte kan skiljas. Du kan inaktivera den *etablering Agent* när du vill etablera på Azure med hjälp av molnet initiering. För att göra detta, se [med molnet init](../linux/using-cloud-init.md).

Versioner av agenter som stöds kan använda automatiska uppdateringar. Är den enda kod som kan uppdateras den *tillägget hantera kod*, inte etablering koden. Den *etablering Agent kod* är kod som körs en gång.

Den *tillägget hantera kod* ansvarar för att kommunicera med Azure-strukturen och hanterar åtgärderna för VM-tillägg som installerar reporting status, uppdatering av enskilda tillägg och ta bort dem. Uppdateringar innehåller säkerhetskorrigeringar, felkorrigeringar och förbättringar av *tillägget hantera koden*.

När agenten har installerats skapas en överordnad daemon. Den här överordnade skapas sedan en underordnad process som används för att hantera tillägg. Om en uppdatering är tillgänglig för agenten hämtas, överordnat stoppar den underordnade processen, uppgraderas den och sedan startar om den. Det bör finnas problem med uppdateringen, återställs den överordnade processen till den tidigare versionen av underordnade.

Den överordnade processen får inte vara uppdateras automatiskt. Överordnat kan bara uppdateras genom en distro Paketuppdatering.

Om du vill kontrollera vilken version du kör Kontrollera den `waagent` på följande sätt:

```bash
waagent --version
```

Utdata ser ut ungefär så här:

```bash
WALinuxAgent-2.2.17 running on ubuntu 16.04
Python: 3.5.2
Goal state agent: 2.2.18
```

I föregående exempel utdata, överordnade eller 'paketet distribueras version' är *WALinuxAgent 2.2.17*

'Målet tillstånd agent' är den uppdaterade versionen som automatiskt.

Vi rekommenderar att du alltid har automatisk uppdatering för agenten [AutoUpdate.Enabled=y](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/update-agent). Inte med det aktivera innebär du behöver uppdatera agenten manuellt, och att hämta inte programfel och säkerhet korrigeringar.

#### <a name="extension-updates"></a>Tillägget uppdateringar

När det finns en uppdatering för tillägg, hämtar Linux-agenten och uppgraderar tillägget. Tillägget automatiska uppdateringar är antingen *mindre* eller *snabbkorrigering*. Du kan välja eller välja att inaktivera tillägg *mindre* uppdateras när du etablerar tillägget. I följande exempel visas hur man automatiskt uppgraderar delversioner i en Resource Manager-mall med *autoUpgradeMinorVersion ”: true'*:

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

För att få de senaste mindre viktig felkorrigeringar, rekommenderas att du alltid välja automatisk uppdatering i tillägget-distributioner. Hotfix-uppdateringar som innehåller säkerhets- eller felkorrigeringar går inte att välja.

### <a name="how-to-identify-extension-updates"></a>Så här identifierar du tillägget uppdateringar

#### <a name="identifying-if-the-extension-is-set-with-autoupgrademinorversion-on-a-vm"></a>Identifiera om tillägget anges med autoUpgradeMinorVersion på en virtuell dator

Du kan se från VM-modellen om tillägget har etablerats med 'autoUpgradeMinorVersion'. Använd för att kontrollera [az vm visa](/cli/azure/vm#az-vm-show) och ange resursgrupp och VM namn på följande sätt:

```azurecli
az vm show --resource-group myResourceGroup --name myVM
```

Följande exempel visas som *autoUpgradeMinorVersion* är inställd på *SANT*:

```json
  "resources": [
    {
      "autoUpgradeMinorVersion": true,
      "forceUpdateTag": null,
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM/extensions/CustomScriptExtension",
```

#### <a name="identifying-when-an-autoupgrademinorversion-occurred"></a>Identifiera när en autoUpgradeMinorVersion inträffade

När en uppdatering av tillägget inträffat, granska agenten loggar in på den virtuella datorn på */var/log/waagent.log*.

I exemplet nedan VM hade *Microsoft.OSTCExtensions.LinuxDiagnostic 2.3.9025* installerad. Det fanns en snabbkorrigering för *Microsoft.OSTCExtensions.LinuxDiagnostic 2.3.9027*:

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

## <a name="agent-permissions"></a>Behörigheter

Om du vill utföra sina uppgifter agenten måste köras som *rot*.

## <a name="troubleshoot-vm-extensions"></a>Felsökning av VM-tillägg

Varje VM-tillägg kan ha felsökningssteg specifika tillägget. Till exempel när du använder tillägget för anpassat skript finns körning skriptdetaljer lokalt på den virtuella datorn där tillägget kördes. Tillägget-specifika felsökning beskrivs i dokumentationen för tillägget-specifika.

Följande felsökningssteg gäller för alla VM-tillägg.

1. Om du vill kontrollera loggen för Linux-Agent, titta på aktiviteten när din anknytning etablerades i */var/log/waagent.log*

2. Kontrollera loggarna faktiska tillägget för mer information finns i   */var/logga/azure /<extensionName>*

3. Dokumentationen tillägget-specifika felsökning avsnitt felkoder, kända problem osv.

3. Titta på systemloggarna. Kontrollera om andra åtgärder som kan ha påverkas filnamnstillägg, till exempel en tidskrävande installation av ett annat program som kräver exklusiv package manager åtkomst.

### <a name="common-reasons-for-extension-failures"></a>Vanliga orsaker till tillägget fel

1. Tillägg har 20 minuter att köra (undantag är CustomScript-tillägg, Chef och DSC som har 90 minuter). Om distributionen överskrider den här tiden, är den markerad som en tidsgräns. Orsaken till detta kan bero på otillräckliga resurser virtuella datorer, andra VM konfigurationer/start aktiviteter som använder hög mängder resursen samtidigt tillägget försöker etablera.

2. Lägsta krav inte uppfylls. Vissa tillägg har beroenden på VM-SKU: er, till exempel HPC-avbildningar. Tillägg kan kräva vissa nätverkskrav åtkomst, till exempel kommunikation till Azure Storage eller offentliga tjänster. Andra exempel kan vara åtkomst till paketet databaser, få slut på diskutrymme eller säkerhetsbegränsningar.

3. Exklusiv package manager-åtkomst. I vissa fall kan uppstå det en tidskrävande VM-konfiguration och installation av webbprogramtillägg i konflikt, där båda måste exklusiv åtkomst till package manager.

### <a name="view-extension-status"></a>Visa status för tillägg

När en VM-tillägget har körts mot en VM, använda [az vm get-instans view](/cli/azure/vm#az-vm-get-instance-view) att returnera tillståndets status på följande sätt:

```azurecli
az vm get-instance-view \
    --resource-group rgName \
    --name myVM \
    --query "instanceView.extensions"
```

Utdata liknar följande exempel utdata:

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

Tillståndets status är körningen kan också finnas i Azure-portalen. Om du vill visa status för ett tillägg markerar du den virtuella datorn, väljer **tillägg**, markerar du önskade tillägget.

### <a name="rerun-a-vm-extension"></a>Kör en VM-tillägget

Det kan finnas fall där en VM-tillägget behöver köras igen. Du kan köra ett tillägg genom att ta bort den och sedan köra tillägget med en körning metod du föredrar. Ta bort ett tillägg med [az virtuella tillägget Ta bort](/cli/azure/vm/extension#az-vm-extension-delete) på följande sätt:

```azurecli
az vm extension delete \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name customScript
```

Du kan också ta bort ett tillägg i Azure-portalen på följande sätt:

1. Välj en virtuell dator.
2. Välj **tillägg**.
3. Välj önskad tillägget.
4. Välj **avinstallera**.

## <a name="common-vm-extension-reference"></a>Benämningen för VM-tillägg

| Tilläggsnamn | Beskrivning | Mer information |
| --- | --- | --- |
| Tillägget för anpassat skript för Linux |Kör skript mot en virtuell Azure-dator |[Tillägget för anpassat skript för Linux](custom-script-linux.md) |
| VM Access-tillägg |Få åtkomst till en virtuell Azure-dator |[VM Access-tillägg](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) |
| Azure Diagnostics-tillägget |Hantera Azure-diagnostik |[Azure Diagnostics-tillägget](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Azure VM Access-tillägg |Hantera användare och autentiseringsuppgifter |[Tillägget för virtuell dator åtkomst för Linux](https://azure.microsoft.com/en-us/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |

## <a name="next-steps"></a>Nästa steg

Mer information om VM-tillägg finns [översikt över funktioner och tillägg av Azure virtuella datorer](overview.md).
