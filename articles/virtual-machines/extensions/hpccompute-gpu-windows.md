---
title: NVIDIA GPU-drivrutinstillägg - Virtuella virtuella Azure Windows-datorer
description: Microsoft Azure-tillägg för installation av NVIDIA GPU-drivrutiner på virtuella datorer i N-serien som kör Windows.
services: virtual-machines-windows
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/09/2019
ms.author: akjosh
ms.openlocfilehash: c388f433327b5328483f10fbef637a6fdfd08832
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250548"
---
# <a name="nvidia-gpu-driver-extension-for-windows"></a>NVIDIA GPU-drivrutinstillägg för Windows

## <a name="overview"></a>Översikt

Det här tillägget installerar NVIDIA GPU-drivrutiner på virtuella datorer i Windows N-serien. Beroende på vm-familjen installeras CUDA- eller GRID-drivrutiner. När du installerar [NVIDIA-drivrutiner](https://go.microsoft.com/fwlink/?linkid=874330)med det här tillägget accepterar och godkänner du villkoren i licensavtalet för slutanvändare från NVIDIA . Under installationsprocessen kan den virtuella datorn startas om för att slutföra drivrutinskonfigurationen.

Instruktioner om manuell installation av drivrutinerna och de aktuella versionerna som stöds finns [här](https://docs.microsoft.com/azure/virtual-machines/windows/n-series-driver-setup).
En förlängning är också tillgänglig för att installera NVIDIA GPU drivrutiner på [Linux N-serien virtuella datorer](hpccompute-gpu-linux.md).

## <a name="prerequisites"></a>Krav

### <a name="operating-system"></a>Operativsystem

Det här tillägget stöder följande OS:

| Distribution | Version |
|---|---|
| Windows 10 | Kärna |
| Windows Server 2016 | Kärna |
| Windows Server 2012 R2 | Kärna |

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
    "type": "NvidiaGpuDriverWindows",
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
| typ | NvidiaGpuDriverWindows | sträng |
| typHandlerVersion | 1.2 | int |


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
    "type": "NvidiaGpuDriverWindows",
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
    -ExtensionName "NvidiaGpuDriverWindows" `
    -ExtensionType "NvidiaGpuDriverWindows" `
    -TypeHandlerVersion 1.2 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
az vm extension set `
  --resource-group myResourceGroup `
  --vm-name myVM `
  --name NvidiaGpuDriverWindows `
  --publisher Microsoft.HpcCompute `
  --version 1.2 `
  --settings '{ `
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

Utdata för tilläggskörning loggas till följande katalog:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.HpcCompute.NvidiaGpuDriverMicrosoft\
```

### <a name="error-codes"></a>Felkoder

| Felkod | Betydelse | Möjliga åtgärder |
| :---: | --- | --- |
| 0 | Åtgärden lyckades |
| 1 | Operationen lyckades. Omstart krävs. |
| 100 | Åtgärden stöds inte eller kunde inte slutföras. | Möjliga orsaker: PowerShell-versionen stöds inte, VM-storlek är inte en virtuell dator i N-serien, felhämtning av data. Kontrollera loggfilerna för att fastställa orsaken till felet. |
| 240, 840 | Timeout för operation. | Försök igen. |
| -1 | Ett undantag inträffade. | Kontrollera loggfilerna för att fastställa orsaken till undantaget. |
| -5x | Åtgärden avbröts på grund av väntande omstart. | Starta om den virtuella datorn. Installationen fortsätter efter omstart. Avinstallationen bör anropas manuellt. |


### <a name="support"></a>Support

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experterna på [MSDN Azure- och Stack Overflow-forumen](https://azure.microsoft.com/support/community/). Du kan också arkivera en Azure-supportincident. Gå till [Azure-supportwebbplatsen](https://azure.microsoft.com/support/options/) och välj Hämta support. Information om hur du använder Azure Support finns i [vanliga frågor och svar om Microsoft Azure-support](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Nästa steg
Mer information om tillägg finns i [Tillägg och funktioner för virtuella datorer för Windows](features-windows.md).

Mer information om virtuella datorer i N-serien finns i [GPU-optimerade storlekar för virtuella datorer](../windows/sizes-gpu.md).
