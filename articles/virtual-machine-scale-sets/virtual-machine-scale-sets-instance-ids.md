---
title: 'Förstå instans-ID: N för virtuella datorer för Azure VM-skalningsuppsättning | Microsoft Docs'
description: 'Förstå VM-skaluppsättning instans-ID: N för Azure VM-skalningsuppsättning'
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: e229664e-ee4e-4f12-9d2e-a4f456989e5d
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: manayar
ms.openlocfilehash: 5d92aa78b132b661af3cdc53b47c0340a0758dd8
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54855320"
---
# <a name="understand-instance-ids-for-azure-vm-scale-set-vms"></a>Förstå VM-skaluppsättning instans-ID: N för Azure VM-skalningsuppsättning
Den här artikeln beskriver instans-ID: N för skalningsuppsättningar och de olika sätt som de Surface-enheter.

## <a name="scale-set-instance-ids"></a>Instans-ID: N för skalningsuppsättningen

Varje virtuell dator i en skalningsuppsättning hämtar ett instans-ID som unikt identifierar den. Den här instansen ID används i skalningsuppsättningen API: er för att utföra åtgärder på en specifik virtuell dator i skalningsuppsättningen ange. Du kan till exempel ange en specifik instans-ID för att återställa avbildningen av när du använder återställa avbildningen av API:

REST API: `POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/reimage?api-version={apiVersion}` (Mer information finns i den [REST API-dokumentation](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/reimage))

PowerShell: `Set-AzureRmVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId} -Reimage` (Mer information finns i den [Powershell-dokumentationen](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmssvm))

CLI: `az vmss reimage -g {resourceGroupName} -n {vmScaleSetName} --instance-id {instanceId}` (Mer information finns i den [CLI-dokumentationen](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_reimage)).

Du kan hämta listan över instans-ID: N genom att lista alla instanser i en skalningsuppsättning:

REST API: `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualMachines?api-version={apiVersion}` (Mer information finns i den [REST API-dokumentation](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesetvms/list))

PowerShell: `Get-AzureRmVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName}` (Mer information finns i den [Powershell-dokumentationen](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmssvm))

CLI: `az vmss list-instances -g {resourceGroupName} -n {vmScaleSetName}` (Mer information finns i den [CLI-dokumentationen](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest)).

Du kan också använda [resources.azure.com](https://resources.azure.com) eller [Azure SDK: er](https://azure.microsoft.com/downloads/) lista de virtuella datorerna i en skalningsuppsättning.

Hur utdata är beroende av de alternativ som du anger i kommandot, men här är några exempel på utdata från CLI:

```
$ az vmss show -g {resourceGroupName} -n {vmScaleSetName}
[
  {
    "instanceId": "85",
    "latestModelApplied": true,
    "location": "westus",
    "name": "nsgvmss_85",
    .
    .
    .
```

Som du ser är ”instanceId”-egenskapen bara ett decimaltal. Instans-ID: N kan återanvändas för nya instanser när gamla instanser tas bort.

>[!NOTE]
> Det finns **ingen garanti** på sätt instans-ID är tilldelade till de virtuella datorerna i skalningsuppsättningen. De kan verka sekventiellt ökande ibland, men detta är inte alltid fallet. Inte att skapa ett beroende på specifika sätt där instans-ID: N är tilldelade till de virtuella datorerna.

## <a name="scale-set-vm-names"></a>Namn på VM-skalningsuppsättning

I exempel på utdata ovan finns även en ”name” för den virtuella datorn. Det här namnet har formatet ”{skala-set-name} _ {instans-id}”. Det här namnet är det som du ser i Azure-portalen när du listar instanser i en skalningsuppsättning:

![](./media/virtual-machine-scale-sets-instance-ids/vmssInstances.png)

{Instans-id}-del av namnet är samma decimaltal som egenskapen ”instanceId” som beskrivits tidigare.

## <a name="instance-metadata-vm-name"></a>Metadata för VM-instansnamn

Om du frågar den [instans metadata](../virtual-machines/windows/instance-metadata-service.md) i en skalningsuppsättning virtuell dator kan du se ett ”name” i utdata:

```
{
  "compute": {
    "location": "westus",
    "name": "nsgvmss_85",
    .
    .
    .
```

Det här namnet är samma som namnet som beskrivits tidigare.

## <a name="scale-set-vm-computer-name"></a>Datornamn för VM-skalningsuppsättning

Varje virtuell dator i en skalningsuppsättning också hämtar ett datornamn som har tilldelats. Den här datornamn är värdnamnet för den virtuella datorn i den [Azure DNS-namnmatchning i det virtuella nätverket](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md). Den här datornamn är i formatet ”{computer-name-prefix}{base-36-instance-id}”.

{Base-36-instans-id} är i [basera 36](https://en.wikipedia.org/wiki/Base36) och är alltid sex siffror långt. Om grundläggande 36 representation av hur många tar färre än sex siffror, läggs {base-36-instans-id} nollor så att de blir sex siffror långt. Till exempel har en instans med {datornamnprefix} ”nsgvmss” och instans-ID 85 datorn namnet ”nsgvmss00002D”.

>[!NOTE]
> Prefixet för datornamnet är en egenskap för skalningsuppsättningen som du kan ange, så det kan vara detsamma som namnet på skalan själva.
