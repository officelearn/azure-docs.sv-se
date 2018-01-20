---
title: "Tillägg för virtuell dator och funktioner för Linux | Microsoft Docs"
description: "Lär dig vilka tillägg som är tillgängliga för virtuella Azure-datorer, grupperade efter vad de tillhandahålla och förbättra."
services: virtual-machines-linux
documentationcenter: 
author: danielsollondon
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 52f5d0ec-8f75-49e7-9e15-88d46b420e63
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: danis
ms.openlocfilehash: 2797085d1e675f0e6608b6192736f939952ed98b
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2018
---
# <a name="virtual-machine-extensions-and-features-for-linux"></a>Tillägg för virtuell dator och funktioner för Linux

Tillägg för virtuell dator i Azure är små program som innehåller efter distributionen konfiguration och automatisering av uppgifter på virtuella Azure-datorer. Om en virtuell dator kräver installation av programvara, anti-virusskydd eller Docker-konfiguration, kan en VM-tillägget användas utföra dessa uppgifter. Azure VM-tillägg kan köras med hjälp av Azure CLI, PowerShell, Azure Resource Manager-mallar och Azure-portalen. Tillägg kan levereras tillsammans med en ny distribution av virtuella datorer eller köra mot alla befintliga system.

Det här dokumentet innehåller en översikt över krav för att använda Azure VM-tillägg på VM-tillägg och anvisningar om hur du identifierar, hantera och ta bort VM-tillägg. Det här dokumentet innehåller allmänna informationen eftersom många VM-tillägg är tillgängligt, var och en med en potentiellt unik konfiguration. Tillägget-specifik information finns i varje dokument specifika för enskilda tillägg.

## <a name="use-cases-and-samples"></a>Användningsfall och exempel

Det finns flera olika Virtuella Azure-tillägg, var och en med en specifik användningsfall. Några exempel är:

- Använd PowerShell önskade tillstånd konfigurationer till en virtuell dator med hjälp av DSC-tillägg för Linux. Mer information finns i [tillägg för konfiguration av Azure önskade tillstånd](https://github.com/Azure/azure-linux-extensions/tree/master/DSC).
- Konfigurera övervakning av en virtuell dator med Microsoft Monitoring Agent VM-tillägget. Mer information finns i [hur du övervakar en Linux VM](tutorial-monitoring.md).
- Konfigurera övervakning av Azure-infrastrukturen med filnamnstillägget Datadog. Mer information finns i [Datadog blogg](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/).
- Konfigurera en Docker-värd på en virtuell Azure-dator med hjälp av Docker VM-tillägget. Mer information finns i [Docker VM-tillägget](dockerextension.md).

Förutom processpecifika tillägg är ett tillägg för anpassat skript tillgängligt för både Windows och Linux virtuella datorer. Tillägget för anpassat skript för Linux kan alla Bash-skript ska köras på en virtuell dator. Anpassade skript är användbara för att utforma Azure-distributioner som kräver konfiguration efter vilken interna Azure verktygsuppsättning kan ge. Mer information finns i [tillägget för anpassat skript för Linux Virtuella](extensions-customscript.md).


## <a name="prerequisites"></a>Förutsättningar

Varje tillägg för virtuell dator kan ha en egen uppsättning krav. Docker VM-tillägget har till exempel en förutsättning för en Linux-fördelning som stöds. Krav för enskilda tillägg beskrivs i dokumentationen för tillägget-specifika.

### <a name="azure-vm-agent"></a>Virtuell Azure-datoragent

Virtuella Azure-agenten hanterar samverkan mellan en virtuell Azure-dator och Azure-infrastrukturkontrollanten. Den Virtuella datoragenten är ansvarig för många funktioner för att distribuera och hantera virtuella Azure-datorer, inklusive kör VM-tillägg. Virtuella Azure-agenten är förinstallerat på Azure Marketplace-bilder och kan installeras manuellt på operativsystem som stöds.

Information om operativsystem som stöds och installationsanvisningar finns [virtuella Azure-datorn agent](../windows/classic/agents-and-extensions-classic.md).

## <a name="discover-vm-extensions"></a>Identifiera VM-tillägg

Det finns många olika VM-tillägg för användning med virtuella Azure-datorer. Kör följande kommando om du vill se en fullständig lista med Azure CLI, ersätter Exempelplats med önskad plats.

```azurecli
az vm extension image list --location westus -o table
```

## <a name="run-vm-extensions"></a>Kör VM-tillägg

Tillägg för virtuell Azure-dator kan köras på befintliga virtuella datorer som är användbara när du behöver göra konfigurationsändringar eller återställa anslutningen på en redan distribuerad virtuell dator. VM-tillägg kan också tillsammans med Azure Resource Manager mall-distributioner. Genom att använda tillägg med Resource Manager-mallar, virtuella Azure-datorer distribuerats och konfigurerats utan åtgärder från efter distributionen.

Följande metoder kan användas för att köra ett tillägg mot en befintlig virtuell dator.

### <a name="azure-cli"></a>Azure CLI

Tillägg för virtuell Azure-dator kan köras mot en befintlig virtuell dator med hjälp av den `az vm extension set` kommando. Det här exemplet körs tillägget för anpassat skript mot en virtuell dator.

```azurecli
az vm extension set `
  --resource-group exttest `
  --vm-name exttest `
  --name customScript `
  --publisher Microsoft.Azure.Extensions `
  --settings '{"fileUris": ["https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"],"commandToExecute": "./hello.sh"}'
```

Skriptet genererar utdata som liknar följande:

```azurecli
info:    Executing command vm extension set
+ Looking up the VM "myVM"
+ Installing extension "CustomScript", VM: "mvVM"
info:    vm extension set command OK
```

### <a name="azure-portal"></a>Azure Portal

VM-tillägg kan tillämpas på en befintlig virtuell dator via Azure-portalen. Om du vill göra det, markerar du den virtuella datorn, väljer **tillägg**, och klicka på **Lägg till**. Välj de tillägg du vill använda från listan över tillgängliga tillägg och följ instruktionerna i guiden.

Följande bild visar installationen av tillägget för anpassat skript för Linux från Azure-portalen.

![Installera tillägget för anpassat skript](./media/extensions-features/installscriptextensionlinux.png)

### <a name="azure-resource-manager-templates"></a>Azure Resource Manager-mallar

VM-tillägg kan läggas till i en Azure Resource Manager-mall och utförs med distributionen av mallen. När du distribuerar ett tillägg med en mall kan skapa du helt konfigurerade Azure-distributioner. Till exempel hämtas följande JSON från en Resource Manager-mall. Mallen distribuerar en uppsättning belastningsutjämnade virtuella datorer och en Azure SQL database och installerar ett program med .NET Core på varje virtuell dator. Tillägg för virtuell dator hand tar om installationen av programmet.

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

Mer information finns i [redigera Azure Resource Manager-mallar](../windows/template-description.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#extensions).

## <a name="secure-vm-extension-data"></a>Säkra data för VM-tillägg

När du kör en VM-tillägget måste vara det nödvändigt att inkludera känslig information, till exempel autentiseringsuppgifter, lagringskontonamn och åtkomst för lagringskontonycklar. Många VM-tillägg innehåller en skyddad konfiguration som krypterar data och dekrypterar dem bara inuti den virtuella måldatorn. Varje tillägg innehåller en specifik skyddade konfigurationsschema och varje beskrivs i dokumentationen för specifika tillägg.

I följande exempel visas en instans av tillägget för anpassat skript för Linux. Observera att kommandot ska köras innehåller en uppsättning autentiseringsuppgifter. I det här exemplet krypteras inte kommandot att köras.


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

Flytta den **kommando att köra** egenskapen till den **skyddade** konfigurationen skyddar körning av strängen.

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

## <a name="troubleshoot-vm-extensions"></a>Felsökning av VM-tillägg

Varje VM-tillägg kan ha felsökningssteg specifika tillägget. Till exempel när du använder tillägget för anpassat skript finns körning skriptdetaljer lokalt på den virtuella datorn där tillägget kördes. Tillägget-specifika felsökning beskrivs i dokumentationen för tillägget-specifika.

Följande felsökningssteg gäller för alla tillägg för virtuell dator.

### <a name="view-extension-status"></a>Visa status för tillägg

När en virtuell dator tillägget har körts mot en virtuell dator, använder du följande kommando i Azure CLI Tilläggsstatus ska returneras. Ersätt exempel parameternamn med egna värden.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Det ser ut som följande utdata:

```azurecli
AutoUpgradeMinorVersion    Location    Name          ProvisioningState    Publisher                   ResourceGroup      TypeHandlerVersion  VirtualMachineExtensionType
-------------------------  ----------  ------------  -------------------  --------------------------  ---------------  --------------------  -----------------------------
True                       westus      customScript  Succeeded            Microsoft.Azure.Extensions  exttest                             2  customScript
```

Tillståndets status är körningen kan också finnas i Azure-portalen. Om du vill visa status för ett tillägg markerar du den virtuella datorn, väljer **tillägg**, och välj önskad tillägget.

### <a name="rerun-a-vm-extension"></a>Kör en VM-tillägget

Det kan finnas fall där tillägget för virtuell dator behöver köras igen. Du kan köra ett tillägg genom att ta bort den och sedan köra tillägget med en körning metod du föredrar. Kör följande kommando för att ta bort ett tillägg med Azure CLI. Ersätt exempel parameternamn med egna värden.

```azurecli
az vm extension delete --name customScript --resource-group myResourceGroup --vm-name myVM
```

Du kan ta bort ett tillägg med hjälp av följande steg i Azure-portalen:

1. Välj en virtuell dator.
2. Välj **tillägg**.
3. Välj önskad tillägget.
4. Välj **avinstallera**.

## <a name="common-vm-extension-reference"></a>Benämningen för VM-tillägg
| Tilläggsnamn | Beskrivning | Mer information |
| --- | --- | --- |
| Tillägget för anpassat skript för Linux |Kör skript mot en virtuell Azure-dator |[Tillägget för anpassat skript för Linux](extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) |
| Docker-tillägg |Installera Docker-daemon för att stödja Docker fjärrkommandon. |[Docker VM-tillägg](dockerextension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) |
| VM Access-tillägg |Få åtkomst till en virtuell Azure-dator |[VM Access-tillägg](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) |
| Azure Diagnostics-tillägget |Hantera Azure-diagnostik |[Azure Diagnostics-tillägget](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Azure VM Access-tillägg |Hantera användare och autentiseringsuppgifter |[Tillägget för virtuell dator åtkomst för Linux](https://azure.microsoft.com/en-us/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |
