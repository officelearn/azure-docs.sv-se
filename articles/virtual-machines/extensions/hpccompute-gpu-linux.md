---
title: NVIDIA GPU-drivrutinstillägg - Virtuella virtuella Azure Linux-datorer
description: Microsoft Azure Extension för installation av NVIDIA GPU-drivrutiner på virtuella datorer i N-serien som kör Linux.
services: virtual-machines-linux
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/11/2019
ms.author: akjosh
ms.openlocfilehash: e7f6653043d46925d6a4c35eedaf81224ea6c36d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415792"
---
# <a name="nvidia-gpu-driver-extension-for-linux"></a>NVIDIA GPU Drivrutinstillägg för Linux

## <a name="overview"></a>Översikt

Detta tillägg installerar NVIDIA GPU-drivrutiner på Linux N-serien virtuella datorer. Beroende på vm-familjen installeras CUDA- eller GRID-drivrutiner. När du installerar [NVIDIA-drivrutiner](https://go.microsoft.com/fwlink/?linkid=874330)med det här tillägget accepterar och godkänner du villkoren i licensavtalet för slutanvändare från NVIDIA . Under installationsprocessen kan den virtuella datorn startas om för att slutföra drivrutinskonfigurationen.

Instruktioner om manuell installation av drivrutinerna och de aktuella versionerna som stöds finns [här](
https://docs.microsoft.com/azure/virtual-machines/linux/n-series-driver-setup).
Ett tillägg är också tillgängligt för att installera NVIDIA [GPU-drivrutiner på virtuella datorer i Windows N-serien](hpccompute-gpu-windows.md).

## <a name="prerequisites"></a>Krav

### <a name="operating-system"></a>Operativsystem

Det här tillägget stöder följande OS-distributioner, beroende på drivrutinsstöd för specifik OS-version.

| Distribution | Version |
|---|---|
| Linux: Ubuntu | 16.04 LTS, 18.04 LTS |
| Linux: Red Hat Enterprise Linux | 7.3, 7.4, 7.5, 7.6 |
| Linux: CentOS | 7.3, 7.4, 7.5, 7.6 |

### <a name="internet-connectivity"></a>Internetanslutning

Microsoft Azure Extension för NVIDIA GPU-drivrutiner kräver att måldatorn är ansluten till internet och har åtkomst.

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

| Namn | Värde / Exempel | Datatyp |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| utgivare | Microsoft.HpcCompute | sträng |
| typ | NvidiaGpuDriverLinux | sträng |
| typHandlerVersion | 1.2 | int |

### <a name="settings"></a>Inställningar

Alla inställningar är valfria. Standardbeteendet är att inte uppdatera kärnan om det inte behövs för drivrutinsinstallation, installera den senaste drivrutinen som stöds och CUDA-verktygslådan (beroende på vad som är tillämpligt).

| Namn | Beskrivning | Standardvärde | Giltiga värden | Datatyp |
| ---- | ---- | ---- | ---- | ---- |
| updateOS | Uppdatera kärnan även om det inte krävs för drivrutinsinstallation | false | SANT, FALSKT | boolean |
| driverVersion | NV: GRID-drivrutinsversion<br> NC/ND: CUDA verktygslåda version. De senaste drivrutinerna för den valda CUDA installeras automatiskt. | senaste | RUTNÄT: "430.30", "418.70", "410.92", "410.71", "390.75", "390.57", "390.42",<br> CUDA: "10.0.130", "9.2.88", "9.1.85" | sträng |
| installCUDA | Installera CUDA-verktygslådan. Endast relevant för virtuella datorer i NC/ND-serien. | true | SANT, FALSKT | boolean |


## <a name="deployment"></a>Distribution


### <a name="azure-resource-manager-template"></a>Azure Resource Manager-mall 

Azure VM-tillägg kan distribueras med Azure Resource Manager-mallar. Mallar är idealiska när du distribuerar en eller flera virtuella datorer som kräver konfiguration efter distribution.

JSON-konfigurationen för ett tillägg för virtuella datorer kan kapslas inuti resursen för den virtuella datorn eller placeras på rot- eller toppnivå i en Resource Manager JSON-mall. Placeringen av JSON-konfigurationen påverkar värdet för resursnamnet och resurstypen. Mer information finns i [Ange namn och typ för underordnade resurser](../../azure-resource-manager/resource-manager-template-child-resource.md). 

I följande exempel förutsätts att tillägget är kapslat i resursen för den virtuella datorn. När tilläggsresursen kapslas placeras JSON i objektet för `"resources": []` den virtuella datorn.

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

I följande exempel speglas ovanstående Azure Resource Manager och PowerShell-exempel och lägger också till anpassade inställningar som ett exempel för installation av drivrutiner som inte är standard. Specifikt uppdaterar den OS-kärnan och installerar en specifik CUDA-verktygslådasversionsdrivrutin.

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name NvidiaGpuDriverLinux \
  --publisher Microsoft.HpcCompute \
  --version 1.2 \
  --settings '{ \
    "updateOS": true, \
    "driverVersion": "9.1.85", \
  }'
```

## <a name="troubleshoot-and-support"></a>Felsöka och support

### <a name="troubleshoot"></a>Felsöka

Data om tillståndet för tilläggsdistributioner kan hämtas från Azure-portalen och med hjälp av Azure PowerShell och Azure CLI. Om du vill se distributionstillståndet för tillägg för en viss virtuell dator kör du följande kommando.

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Utdata för tilläggskörning loggas till följande fil:

```bash
/var/log/azure/nvidia-vmext-status
```

### <a name="exit-codes"></a>Avsluta koder

| Slutkod | Betydelse | Möjliga åtgärder |
| :---: | --- | --- |
| 0 | Åtgärden lyckades |
| 1 | Felaktig användning av tillägg | Kontrollera utdatalogg för körning |
| 10 | Linux Integration Services för Hyper-V och Azure är inte tillgängliga eller installerade | Kontrollera uteffekten av lspci |
| 11 | NVIDIA GPU hittades inte på den här VM-storleken | Använda en [vm-storlek och ett operativsystem som stöds](../linux/n-series-driver-setup.md) |
| 12 | Bilderbjudande stöds inte |
| 13 | Vm-storlek stöds inte | Använda en virtuell dator i N-serien för att distribuera |
| 14 | Åtgärden misslyckades | Kontrollera utdatalogg för körning |


### <a name="support"></a>Support

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experterna på [MSDN Azure- och Stack Overflow-forumen](https://azure.microsoft.com/support/community/). Du kan också arkivera en Azure-supportincident. Gå till [Azure-supportwebbplatsen](https://azure.microsoft.com/support/options/) och välj Hämta support. Information om hur du använder Azure Support finns i [vanliga frågor och svar om Microsoft Azure-support](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Nästa steg
Mer information om tillägg finns i [Tillägg och funktioner för virtuella datorer för Linux](features-linux.md).

Mer information om virtuella datorer i N-serien finns i [GPU-optimerade storlekar för virtuella datorer](../linux/sizes-gpu.md).
