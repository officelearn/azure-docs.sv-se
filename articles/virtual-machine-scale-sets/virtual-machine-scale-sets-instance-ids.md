---
title: Förstå instans-ID:er för virtuella azure vm-skalningsuppsättning virtuella datorer
description: Förstå instans-ID:er för Azure VM-skalning anger virtuella datorer och de olika sätt som de kommer på.
author: mimckitt
tags: azure-resource-manager
ms.assetid: e229664e-ee4e-4f12-9d2e-a4f456989e5d
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 02/22/2018
ms.author: mimckitt
ms.openlocfilehash: aa8b88bde4b6f8f02f6c9c81d0742d0dede761ac
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273695"
---
# <a name="understand-instance-ids-for-azure-vm-scale-set-vms"></a>Förstå instans-ID:er för virtuella azure vm-skalningsuppsättning virtuella datorer
I den här artikeln beskrivs instans-ID:er för skalningsuppsättningar och olika sätt att visa.

## <a name="scale-set-instance-ids"></a>Skala ange instans-ID:er

Varje virtuell dator i en skalningsuppsättning får ett instans-ID som unikt identifierar det. Det här instans-ID:et används i skalningsuppsättningens API:er för att utföra åtgärder på en viss virtuell dator i skalningsuppsättningen. Du kan till exempel ange ett specifikt instans-ID som ska publiceras igen när API:et för reimage ska användas:

REST API: `POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/reimage?api-version={apiVersion}` (mer information finns i [REST API-dokumentationen](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/reimage))

Powershell: `Set-AzVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId} -Reimage` (mer information finns i [Powershell-dokumentationen](https://docs.microsoft.com/powershell/module/az.compute/set-azvmssvm))

CLI: `az vmss reimage -g {resourceGroupName} -n {vmScaleSetName} --instance-id {instanceId}` (för mer information, se [CLI-dokumentationen](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest)).

Du kan hämta listan över instans-ID:er genom att ange alla instanser i en skalningsuppsättning:

REST API: `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualMachines?api-version={apiVersion}` (mer information finns i [REST API-dokumentationen](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesetvms/list))

Powershell: `Get-AzVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName}` (mer information finns i [Powershell-dokumentationen](https://docs.microsoft.com/powershell/module/az.compute/get-azvmssvm))

CLI: `az vmss list-instances -g {resourceGroupName} -n {vmScaleSetName}` (för mer information, se [CLI-dokumentationen](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest)).

Du kan också använda [resources.azure.com](https://resources.azure.com) eller [Azure SDK:er](https://azure.microsoft.com/downloads/) för att lista de virtuella datorerna i en skalningsuppsättning.

Den exakta presentationen av utdata beror på vilka alternativ du anger för kommandot, men här är några exempelutdata från CLI:

```azurecli
az vmss show -g {resourceGroupName} -n {vmScaleSetName}
```

```output
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

Som du kan se är egenskapen "instanceId" bara ett decimaltal. Instans-ID:erna kan återanvändas för nya instanser när gamla instanser har tagits bort.

>[!NOTE]
> Det finns **ingen garanti** för hur instans-ID:er tilldelas de virtuella datorerna i skalningsuppsättningen. De kan tyckas sekventiellt öka ibland, men detta är inte alltid fallet. Ta inte ett beroende av det specifika sätt på vilket instans-ID:n tilldelas de virtuella datorerna.

## <a name="scale-set-vm-names"></a>Skala ange VM-namn

I exempelutdata ovan finns det också ett "namn" för den virtuella datorn. Det här namnet har formen {scale-set-name}_{instance-id}. Det här namnet är det namn som visas i Azure-portalen när du listar instanser i en skalningsuppsättning:

![](./media/virtual-machine-scale-sets-instance-ids/vmssInstances.png)

Den {instance-id} delen av namnet är samma decimaltal som egenskapen "instanceId" som diskuterats tidigare.

## <a name="instance-metadata-vm-name"></a>VM-namn för instans metadata

Om du frågar [instansmetadata](../virtual-machines/windows/instance-metadata-service.md) från en skalningsuppsättning vm visas ett "namn" i utdata:

```output
{
  "compute": {
    "location": "westus",
    "name": "nsgvmss_85",
    .
    .
    .
```

Det här namnet är samma som namnet som diskuterats tidigare.

## <a name="scale-set-vm-computer-name"></a>Datornamn för skala uppsättning vm

Varje virtuell dator i en skalningsuppsättning får också ett datornamn tilldelat till den. Det här datornamnet är värdnamnet för den virtuella datorn i [DNS-namnmatchningen som tillhandahålls](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)av Azure i det virtuella nätverket . Det här datornamnet är av formuläret {computer-name-prefix}{base-36-instance-id}.

{base-36-instance-id} finns i [bas 36](https://en.wikipedia.org/wiki/Base36) och är alltid sex siffror långa. Om basen 36-representationen av talet tar färre än sex siffror, är {base-36-instance-id} vadderad med nollor för att göra det sex siffror i längd. En instans med {computer-name-prefix} "nsgvmss" och instans-ID 85 har till exempel datornamnet "nsgvmss00002D".

>[!NOTE]
> Datornamnprefixet är en egenskap för den skaluppsättningsmodell som du kan ange, så att den kan skilja sig från själva skalningsuppsättningsnamnet.
