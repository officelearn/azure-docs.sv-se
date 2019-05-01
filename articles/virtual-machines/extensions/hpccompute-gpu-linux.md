---
title: NVIDIA GPU-drivrutinen tillägg – virtuella Azure Linux-datorer | Microsoft Docs
description: Microsoft Azure-tillägg för att installera NVIDIA GPU-drivrutiner på N-serien compute virtuella datorer som kör Linux.
services: virtual-machines-linux
documentationcenter: ''
author: vermagit
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/11/2019
ms.author: roiyz
ms.openlocfilehash: 5a184c72da8af0d451902a164c8b71a94a01883f
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64683179"
---
# <a name="nvidia-gpu-driver-extension-for-linux"></a>NVIDIA GPU-drivrutinen tillägg för Linux

## <a name="overview"></a>Översikt

Det här tillägget installerar NVIDIA GPU-drivrutiner på Linux N-serien virtuella datorer. Beroende på VM-familjen installerar tillägget CUDA- eller NÄTVERKSBASERADE drivrutiner. När du installerar NVIDIA drivrutinerna med hjälp av det här tillägget du accepterar och samtycker till villkoren i den [NVIDIA licensavtalet](https://go.microsoft.com/fwlink/?linkid=874330). Under installationen, kan den virtuella datorn startas om för att slutföra installationen för drivrutinen.

Ett tillägg kan även installera NVIDIA GPU-drivrutiner på [Windows virtuella datorer i N-serien](hpccompute-gpu-windows.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

### <a name="operating-system"></a>Operativsystem

Det här tillägget stöder följande OS-distributioner, beroende på drivrutinstöd för specifika OS-version.

| Distribution | Version |
|---|---|
| Linux: Ubuntu | 16.04 LTS, 18.04 LTS |
| Linux: Red Hat Enterprise Linux | 7.3, 7.4, 7.5, 7.6 |
| Linux: CentOS | 7.3, 7.4, 7.5, 7.6 |

### <a name="internet-connectivity"></a>Internetanslutning

Microsoft Azure-tillägget för NVIDIA GPU-drivrutiner kräver att den Virtuella måldatorn är ansluten till internet och har åtkomst.

## <a name="extension-schema"></a>Tilläggsschema

Följande JSON visar schemat för tillägget.

```json
{
  "name": "<myExtensionName>",
  "type": "extensions",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <myVM>)]"
  ],
  "properties": {
    "publisher": "Microsoft.HpcCompute",
    "type": "NvidiaGpuDriverLinux",
    "typeHandlerVersion": "1.2",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="properties"></a>Egenskaper

| Namn | Värdet / exempel | Datatyp |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| utgivare | Microsoft.HpcCompute | string |
| typ | NvidiaGpuDriverLinux | string |
| typeHandlerVersion | 1.2 | int |

### <a name="settings"></a>Inställningar

Alla inställningar är valfria. Standardinställningen är att inte uppdatera kernel om det inte krävs för installation av drivrutiner, installera den senaste stödda drivrutinen och CUDA-toolkit (som tillämpligt).

| Namn | Beskrivning | Standardvärde | Giltiga värden | Datatyp |
| ---- | ---- | ---- | ---- | ---- |
| updateOS | Uppdatera kernel även om det inte krävs för installation av drivrutiner | false | SANT, FALSKT | boolesk |
| driverVersion | NV: RUTNÄTET drivrutinsversion<br> NC/ND: CUDA toolkit-version. De senaste drivrutinerna för valt CUDA installeras automatiskt. | senaste | GRID: "418.70", "410.92", "410.71", "390.75", "390.57", "390.42"<br> CUDA: "10.0.130", "9.2.88", "9.1.85" | string |
| installCUDA | Installera CUDA toolkit. Detta gäller endast för NC/ND-serien virtuella datorer. | true | SANT, FALSKT | boolesk |


## <a name="deployment"></a>Distribution


### <a name="azure-resource-manager-template"></a>Azure Resource Manager-mall 

Azure VM-tillägg kan distribueras med Azure Resource Manager-mallar. Mallar är perfekt när du distribuerar en eller flera virtuella datorer som kräver konfiguration efter distribution.

JSON-konfiguration för tillägg för virtuell dator kan kapslas i resursen för virtuella datorer eller placeras i roten eller översta nivån i en Resource Manager JSON-mall. Placeringen av JSON-konfigurationen påverkar värdet för resursnamn och typ. Mer information finns i [ange namn och typ för underordnade resurser](../../azure-resource-manager/resource-manager-template-child-resource.md). 

I följande exempel förutsätter att tillägget är kapslade i den virtuella datorresursen. När kapsla tillägget resursen JSON placeras i den `"resources": []` objekt av den virtuella datorn.

```json
{
  "name": "myExtensionName",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', myVM)]"
  ],
  "properties": {
    "publisher": "Microsoft.HpcCompute",
    "type": "NvidiaGpuDriverLinux",
    "typeHandlerVersion": "1.2",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="powershell"></a>PowerShell

```powershell
Set-AzVMExtension
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Location "southcentralus" `
    -Publisher "Microsoft.HpcCompute" `
    -ExtensionName "NvidiaGpuDriverLinux" `
    -ExtensionType "NvidiaGpuDriverLinux" `
    -TypeHandlerVersion 1.2 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Azure CLI

I följande exempel speglar ovanstående exempel för Azure Resource Manager och PowerShell och även lägger till anpassade inställningar som ett exempel på icke-standard drivrutinsinstallation. Mer specifikt den uppdaterar OS-kernel och installerar en specifik CUDA toolkit version drivrutin.

```azurecli
az vm extension set `
  --resource-group myResourceGroup `
  --vm-name myVM `
  --name NvidiaGpuDriverLinux `
  --publisher Microsoft.HpcCompute `
  --version 1.2 `
  --settings '{ `
    "updateOS": true, `
    "driverVersion": "9.1.85", `
  }'
```

## <a name="troubleshoot-and-support"></a>Felsökning och support

### <a name="troubleshoot"></a>Felsöka

Data om tillståndet för distributioner av tillägget kan hämtas från Azure-portalen och med hjälp av Azure PowerShell och Azure CLI. Om du vill se distributionsstatusen för tillägg för en viss virtuell dator, kör du följande kommando.

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Tillägget utförande-utdatan loggas till följande fil:

```bash
/var/log/azure/nvidia-vmext-status
```

### <a name="exit-codes"></a>Slutkoder för aktiviteter

| Slutkod | Betydelse | Möjlig åtgärd |
| :---: | --- | --- |
| 0 | Åtgärden lyckades |
| 1 | Felaktig användning av tillägget | Kontrollera körningsloggen för utdata |
| 10 | Linux Integration-tjänster för Hyper-V och Azure saknas eller är installerade | Kontrollera resultatet av lspci |
| 11 | NVIDIA GPU hittades inte på den här VM-storlek | Använd en [VM-storlek och operativsystem som stöds](../linux/n-series-driver-setup.md) |
| 12 | Bild-erbjudande som inte stöds |
| 13 | VM-storlek som inte stöds | Använda en virtuell dator i N-serien för att distribuera |
| 14 | Åtgärden misslyckades | Kontrollera körningsloggen för utdata |


### <a name="support"></a>Support

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experter på den [Azure för MSDN och Stack Overflow-forum](https://azure.microsoft.com/support/community/). Alternativt kan du arkivera en Azure-support-incident. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och väljer Get support. Information om hur du använder Azure-supporten finns i [vanliga frågor om Microsoft Azure-support](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Nästa steg
Mer information om tillägg finns i [virtuella datorer, tillägg och funktioner i Linux](features-linux.md).

Läs mer om virtuella datorer i N-serien, [GPU-optimerad storlekar för virtuella datorer](../linux/sizes-gpu.md).
