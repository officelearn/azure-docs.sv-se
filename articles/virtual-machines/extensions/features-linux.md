---
title: Azure VM-tillägg och funktioner i Linux | Microsoft Docs
description: Lär dig vilka tillägg som finns tillgängliga för Azure-datorer, grupperade efter vad de tillhandahåller eller förbättra.
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
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
ms.author: roiyz
ms.openlocfilehash: 720b8584c201826e55099ad31667478b6aa57b27
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50024531"
---
# <a name="virtual-machine-extensions-and-features-for-linux"></a>Virtuella datorer, tillägg och funktioner i Linux

Tillägg för Azure-dator (VM) är små program som ger efter distributionen konfiguration och automatisering av uppgifter på Azure Virtual Machines. Exempel: om en virtuell dator kräver Programvaruinstallation, antivirusskydd, eller för att köra ett skript inuti den kan ett VM-tillägg användas. Azure VM-tillägg kan köras med Azure CLI, PowerShell, Azure Resource Manager-mallar och Azure-portalen. Tillägg kan levereras tillsammans med en ny VM-distribution eller kör mot alla befintliga system.

Den här artikeln innehåller en översikt över VM-tillägg, krav för att använda Azure VM-tillägg och anvisningar för hur du identifiera, hantera och ta bort VM-tillägg. Den här artikeln innehåller allmänna informationen eftersom många VM-tillägg är tillgängligt, var och en med en potentiellt unika konfigurationer. Tillägget-specifik information finns i varje dokument specifika för enskilda tillägget.

## <a name="use-cases-and-samples"></a>Användningsfall och exempel

Flera olika Azure VM-tillägg som finns tillgängliga, var och en med en specifik användningsfall. Några exempel är:

- Använd PowerShell Desired State konfigurationer på en virtuell dator med DSC-tillägget för Linux. Mer information finns i [Azure Desired State configuration-tillägget](https://github.com/Azure/azure-linux-extensions/tree/master/DSC).
- Konfigurera övervakning av en virtuell dator med Microsoft Monitoring Agent-VM-tillägget. Mer information finns i [så här övervakar du en Linux VM](../linux/tutorial-monitoring.md).
- Konfigurera övervakning av Azure-infrastrukturen med Chef eller Datadog. Mer information finns i den [Chef docs](https://docs.chef.io/azure_portal.html) eller [Datadog blogg](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/).

Förutom process-specifika tillägg finns ett anpassat skripttillägg för både Windows och Linux-datorer. Tillägget för anpassat skript för Linux kan valfritt Bash-skript som ska köras på en virtuell dator. Anpassade skript är användbara för att utforma Azure-distributioner som kräver konfigurering, förutom vilka interna Azure-verktyg kan ge. Mer information finns i [tillägget för anpassat skript för Linux VM](custom-script-linux.md).

## <a name="prerequisites"></a>Förutsättningar

För att hantera tillägg på den virtuella datorn, måste du Azure Linux Agent installerad. Vissa enskilda tillägg har förutsättningar, till exempel åtkomst till resurser eller beroenden.

### <a name="azure-vm-agent"></a>Virtuell Azure-datoragent

Azure VM-agenten hanterar samverkan mellan en Azure-dator och Azure-infrastrukturkontrollanten. VM-agenten är ansvarig för många funktionella aspekter av distribution och hantering av virtuella datorer i Azure, inklusive köra VM-tillägg. Azure VM-agenten är förinstallerade på Azure Marketplace-avbildningar och kan installeras manuellt på operativsystem som stöds. Azure VM-agenten för Linux är känt som Linux-agenten.

Information om operativsystem som stöds och installationsanvisningar finns i [Azure VM agent](agent-linux.md).

#### <a name="supported-agent-versions"></a>Versioner av agent som stöds

Det finns minimiversioner av agenten för att tillhandahålla den bästa möjliga upplevelsen. Mer information finns i [den här artikeln](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

#### <a name="supported-oses"></a>Stödda OS

Linux-agenten körs på flera operativsystem, men ramen tillägg har en gräns för operativsystem som tillägg. Mer information finns i [den här artikeln](https://support.microsoft.com/en-us/help/4078134/azure-extension-supported-operating-systems
).

Vissa tillägg stöds inte i alla operativsystem och kan skapa *felkod 51, 'Operativsystmet'*. Dokumentationen enskilda tillägg för support.

#### <a name="network-access"></a>Nätverksåtkomst

Tilläggspaket laddas ned från Azure Storage-tilläggscentrallagret och tillägget status uppladdningar skickas till Azure Storage. Om du använder [stöds](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) version av agenterna kan du inte behöver att tillåta åtkomst till Azure Storage i regionen VM kan använda agenten för att omdirigera kommunikationen till Azure-infrastrukturkontrollanten för agenten kommunikation. Om du har en version som inte stöds av agenten kan behöva du tillåter utgående åtkomst till Azure storage i regionen från den virtuella datorn.

> [!IMPORTANT]
> Om du har blockerat åtkomsten till *168.63.129.1* med gästdatorns brandvägg, sedan tillägg misslyckas oavsett ovan.

Agenter kan bara användas för att ladda ned tilläggspaket och Rapporteringsstatus. Till exempel om en installation av tillägget måste hämta ett skript från GitHub (anpassade skript) eller behöver åtkomst till Azure Storage (Azure Backup), sedan ytterligare brandvägg/Network Security Group portar måste du öppna. Olika tillägg har olika krav, eftersom de är program självständigt. För tillägg som kräver åtkomst till Azure Storage, kan du tillåta åtkomst med hjälp av Azure NSG-Tjänsttaggar för [Storage](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

För att omdirigera trafik agentbegäranden har Linux-agenten stöd för proxyserver. Dock gäller inte den här stöd för proxyserver tillägg. Du måste konfigurera varje enskilda tillägg ska fungera med en proxy.

## <a name="discover-vm-extensions"></a>Identifiera VM-tillägg

Det finns många olika VM-tillägg för användning med virtuella Azure-datorer. Om du vill se en fullständig lista, använda [az vm-tillägget bildlista](/cli/azure/vm/extension/image#az-vm-extension-image-list). I följande exempel visar en lista över alla tillgängliga tillägg i den *westus* plats:

```azurecli
az vm extension image list --location westus --output table
```

## <a name="run-vm-extensions"></a>Kör VM-tillägg

Azure VM-tillägg som kör på befintliga virtuella datorer, vilket är användbart när du behöver göra konfigurationsändringar eller återställa anslutningen i en redan distribuerad virtuell dator. VM-tillägg kan också tillsammans med Azure Resource Manager malldistributioner. Genom att använda tillägg med Resource Manager-mallar, kan virtuella Azure-datorer distribueras och konfigurerats utan inblandning av efter distributionen.

Följande metoder kan användas för att köra ett tillägg mot en befintlig virtuell dator.

### <a name="azure-cli"></a>Azure CLI

Azure VM-tillägg kan köras mot en befintlig virtuell dator med den [az vm-tilläggsuppsättningen](/cli/azure/vm/extension#az-vm-extension-set) kommando. Följande exempel kör tillägget för anpassat skript mot en virtuell dator med namnet *myVM* i en resursgrupp med namnet *myResourceGroup*:

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

VM-tillägg kan tillämpas på en befintlig virtuell dator via Azure portal. Välj den virtuella datorn i portalen, väljer **tillägg**och välj sedan **Lägg till**. Välj de tillägg du vill använda från listan över tillgängliga tillägg och följ instruktionerna i guiden.

Följande bild visar installationen av tillägget för Linux anpassat skript från Azure portal:

![Installera tillägget för anpassat skript](./media/features-linux/installscriptextensionlinux.png)

### <a name="azure-resource-manager-templates"></a>Azure Resource Manager-mallar

VM-tillägg kan läggas till i en Azure Resource Manager-mall och körs med distributionen av mallen. När du distribuerar ett tillägg med en mall kan skapa du helt konfigurerade Azure-distributioner. Till exempel hämtas följande JSON från en Resource Manager-mall som distribuerar en uppsättning belastningsutjämnade virtuella datorer och en Azure SQL database och sedan installerar ett program med .NET Core på varje virtuell dator. VM-tillägget hand tar om programvaran ska installeras.

Mer information finns i fullständiga [Resource Manager-mall](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).

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

Mer information om hur du skapar Resource Manager-mallar finns i [redigera Azure Resource Manager-mallar](../windows/template-description.md#extensions).

## <a name="secure-vm-extension-data"></a>Skydda data för VM-tillägg

När du kör ett VM-tillägg, kan det vara nödvändigt att inkludera känslig information, till exempel autentiseringsuppgifter, lagringskontonamn och åtkomstnycklarna för lagringskontot. Många VM-tillägg innehåller en skyddad konfiguration som krypterar data och dekrypterar dem bara inuti den Virtuella måldatorn. Varje tillägg har en specifik skyddad konfigurationsschema och varje beskrivs i dokumentationen för specifika tillägg.

I följande exempel visas en instans av tillägget för anpassat skript för Linux. Kommandot körs innehåller en uppsättning autentiseringsuppgifter. I det här exemplet krypteras inte kommandot som ska köras:

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

Flytta den **kommando för att köra** egenskap enligt den **skyddade** konfigurationen skyddar körning-strängen som du ser i följande exempel:

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

Dela samma uppdateringsmekanism agenter och tillägg. Vissa uppdateringar kräver inte ytterligare brandväggsregler.

När en uppdatering är tillgänglig endast installeras på den virtuella datorn när det finns en ändring av tillägg och andra Virtuella datormodellen ändringar som:

- Datadiskar
- Tillägg
- Boot diagnostics behållare
- Gäst-OS-hemligheter
- Storlek på virtuell dator
- Nätverksprofil

Utgivare göra uppdateringar tillgängliga för regioner vid olika tidpunkter, så det är möjligt kan du har virtuella datorer i olika regioner i olika versioner.

#### <a name="agent-updates"></a>Agentuppdateringar

VM-agenten för Linux innehåller *etablering agenten kod* och *tillägget hantering av kod* i ett paket som inte kan delas upp. Du kan inaktivera den *etablering agenten* när du vill etablera på Azure med cloud-init. För att göra detta, se [med cloud-init](../linux/using-cloud-init.md).

Versioner av agenter som stöds kan använda automatiska uppdateringar. Är den enda kod som kan uppdateras den *tillägget hantering av kod*, inte etablering koden. Den *etablering agenten kod* är kod körs en gång.

Den *tillägget hantering av kod* ansvarar för kommunicerar med Azure-infrastrukturen och hantera åtgärder för VM-tillägg som installerar rapporterar status, uppdaterar enskilda tillägg och ta bort dem. Uppdateringar innehålla säkerhetskorrigeringar, felkorrigeringar och förbättringar av den *tillägget hantering av kod*.

När agenten är installerad, skapas en överordnad daemon. Den här överordnade tio sedan en underordnad process som används för att hantera tillägg. Om en uppdatering är tillgänglig för agenten, det har laddats ner, överordnat stoppar den underordnade processen, uppgraderas den och sedan startar om den. Det bör finnas ett problem med uppdateringen, återställer den överordnade processen till den tidigare versionen av underordnade.

Den överordnade processen får inte vara uppdateras automatiskt. Överordnat kan bara uppdateras efter en distribution som Paketuppdatering.

Om du vill kontrollera vilken version du kör, kontrollera den `waagent` på följande sätt:

```bash
waagent --version
```

Utdata ser ut ungefär så här:

```bash
WALinuxAgent-2.2.17 running on ubuntu 16.04
Python: 3.5.2
Goal state agent: 2.2.18
```

I föregående Exempelutdata överordnade eller 'paketet distribueras versionen ”är *WALinuxAgent 2.2.17*

'Målet tillstånd agent' är den uppdaterade versionen som automatiskt.

Vi rekommenderar starkt att du alltid har automatisk uppdatering av agenten [AutoUpdate.Enabled=y](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent). Inte med det aktivera innebär att du behöver uppdatera agenten manuellt och att hämta inte korrigeringar av fel och säkerhet.

#### <a name="extension-updates"></a>Uppdateringar för tillägget

När det finns en uppdatering av tillägget, laddar ned Linux-agenten och uppgraderar tillägget. Tillägget för automatiska uppdateringar är antingen *mindre* eller *snabbkorrigering*. Du kan anmäla eller avanmäla dig från tillägg *mindre* uppdateras när du etablerar tillägget. I följande exempel visas hur man automatiskt uppgraderar delversioner i Resource Manager-mall med *autoUpgradeMinorVersion ”: true”,*:

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

Vi rekommenderar att du alltid välja automatisk uppdatering i distributionen tillägget för att få de senaste versionen mindre felkorrigeringar. Hotfix-uppdateringar som säkerhet eller nyckel felkorrigeringar går inte att välja.

### <a name="how-to-identify-extension-updates"></a>Så här identifierar du tillägget uppdateringar

#### <a name="identifying-if-the-extension-is-set-with-autoupgrademinorversion-on-a-vm"></a>Identifiera om tillägget är inställd med autoUpgradeMinorVersion på en virtuell dator

Du kan se från den Virtuella datormodellen om tillägget etablerades med 'autoUpgradeMinorVersion'. Du kan kontrollera genom att använda [az vm show](/cli/azure/vm#az-vm-show) och ange resursgrupp och virtuell dator namn på följande sätt:

```azurecli
az vm show --resource-group myResourceGroup --name myVM
```

Följande Exempelutdata visar att *autoUpgradeMinorVersion* är inställd på *SANT*:

```json
  "resources": [
    {
      "autoUpgradeMinorVersion": true,
      "forceUpdateTag": null,
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM/extensions/CustomScriptExtension",
```

#### <a name="identifying-when-an-autoupgrademinorversion-occurred"></a>Identifiera när en autoUpgradeMinorVersion inträffade

Om du vill se när en uppdatering av tillägget uppstod granska agenten loggar in på den virtuella datorn senare */var/log/waagent.log*.

I exemplet nedan visas den virtuella datorn hade *Microsoft.OSTCExtensions.LinuxDiagnostic 2.3.9025* installerad. Det fanns en snabbkorrigering till *Microsoft.OSTCExtensions.LinuxDiagnostic 2.3.9027*:

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

## <a name="agent-permissions"></a>Agent-behörigheter

För att utföra sina uppgifter, agenten måste köras som *rot*.

## <a name="troubleshoot-vm-extensions"></a>Felsöka VM-tillägg

Varje VM-tillägg kan ha felsökningssteg specifika till tillägget. Till exempel när du använder tillägget för anpassat skript, finns information om skriptkörning lokalt på den virtuella datorn där tillägget kördes. Tillägget-specifika felsökning beskrivs i dokumentationen för specifika tillägg.

Följande felsökningssteg gäller för alla VM-tillägg.

1. Om du vill kontrollera loggen för Linux-agenten, titta på aktiviteten när ditt tillägg etablerades i */var/log/waagent.log*

2. Kontrollera faktiska tillägget programloggarna för mer information finns i   */var/logga/azure /<extensionName>*

3. Dokumentationen tillägget-specifika felsökning avsnitt för felkoder, kända problem osv.

3. Titta på systemloggar. Sök efter andra åtgärder som kan ha påverkas tillägg, till exempel en tidskrävande installation av ett annat program som krävs för exklusiv package manager-åtkomst.

### <a name="common-reasons-for-extension-failures"></a>Vanliga orsaker till datortillägg

1. Tillägg har 20 minuter att köra (undantag är CustomScript-tillägg, Chef och DSC som har 90 minuter). Om distributionen överskrider den här tiden, markeras den som en tidsgräns. Orsaken till detta kan bero på otillräckliga resurser virtuella datorer, andra VM-konfigurationer/starta upp aktiviteter som använder hög mängder resurs när tillägget försöker etablera.

2. Minsta kraven uppfylls inte. Vissa tillägg vara beroende VM SKU: er som HPC-bilder. Tillägg kan kräva viss nätverkskraven åtkomst, till exempel kommunikation till Azure Storage eller offentliga tjänster. Andra exempel kan vara åtkomst till paketet lagringsplatser, slut på diskutrymme eller säkerhetsbegränsningar.

3. Exklusiva package manager-åtkomst. I vissa fall kan det uppstå en tidskrävande VM-konfiguration och installation av tillägg som är i konflikt, där de båda behöver exklusiv åtkomst till package manager.

### <a name="view-extension-status"></a>Visa status för tillägg

När en VM-tillägget har körts mot en virtuell dator kan du använda [az vm get-instance-view](/cli/azure/vm#az-vm-get-instance-view) att returnera tilläggets status på följande sätt:

```azurecli
az vm get-instance-view \
    --resource-group rgName \
    --name myVM \
    --query "instanceView.extensions"
```

Utdata liknar följande Exempelutdata:

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

Körningsstatus för tillägg finns också i Azure-portalen. Om du vill visa status för ett tillägg markerar du den virtuella datorn, väljer **tillägg**, markerar du önskade tillägget.

### <a name="rerun-a-vm-extension"></a>Kör ett VM-tillägg

Det kan finnas fall där ett VM-tillägg måste köras igen. Du kan köra ett tillägg genom att ta bort den och sedan köra tillägget med en körning metod för ditt val. Ta bort ett tillägg med [az vm-tillägget delete](/cli/azure/vm/extension#az-vm-extension-delete) på följande sätt:

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

## <a name="common-vm-extension-reference"></a>Vanliga tilläggsreferensen för virtuell dator

| Namn på tillägg | Beskrivning | Mer information |
| --- | --- | --- |
| Tillägget för anpassat skript för Linux |Köra skript mot en Azure virtuell dator |[Tillägget för anpassat skript för Linux](custom-script-linux.md) |
| VM Access-tillägg |Få åtkomst till en Azure virtuell dator |[VM Access-tillägg](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) |
| Azure Diagnostics-tillägg |Hantera Azure-diagnostik |[Azure Diagnostics-tillägg](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Azure VM Access-tillägg |Hantera användare och autentiseringsuppgifter |[VM Access-tillägg för Linux](https://azure.microsoft.com/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |

## <a name="next-steps"></a>Nästa steg

Mer information om VM-tillägg finns i [Azure VM-tillägg och funktioner för översikt över](overview.md).
