---
title: "Förstå instans-ID: N för virtuella datorer för skaluppsättning för virtuell dator i Azure | Microsoft Docs"
description: "Förstå instans-ID: N för Azure VM scale ange virtuella datorer"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: e229664e-ee4e-4f12-9d2e-a4f456989e5d
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: negat
ms.openlocfilehash: 3a43dc86f1fb53dfde4bce3938faaa30e18f5a6d
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2018
---
# <a name="understand-instance-ids-for-azure-vm-scale-set-vms"></a>Förstå instans-ID: N för Azure VM scale ange virtuella datorer
Den här artikeln beskriver instans-ID: N för skalningsuppsättningar och de olika sätt som de yta.

## <a name="scale-set-instance-ids"></a>Skaluppsättning för instans-ID: N

Varje virtuell dator i en skaluppsättning hämtar en instans-ID som unikt identifierar den. Den här instansen ID används i API: er för skaluppsättning för att utföra åtgärder på en specifik VM i skalan anges. Du kan till exempel ange en specifik instans-ID för att återställa när du använder avbildningsåterställning API:

REST-API: `POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/reimage?api-version={apiVersion}` (Mer information finns i [REST API-dokumentation](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/reimage))

PowerShell: `Set-AzureRmVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId} -Reimage` (Mer information finns i [Powershell dokumentationen](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmssvm))

CLI: `az vmss reimage -g {resourceGroupName} -n {vmScaleSetName} --instance-id {instanceId}` (Mer information finns i [CLI dokumentationen](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_reimage)).

Du kan hämta lista över instans-ID genom att lista alla instanser i en skaluppsättning:

REST-API: `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualMachines?api-version={apiVersion}` (Mer information finns i [REST API-dokumentation](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesetvms/list))

PowerShell: `Get-AzureRmVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName}` (Mer information finns i [Powershell dokumentationen](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmssvm))

CLI: `az vmss list-instances -g {resourceGroupName} -n {vmScaleSetName}` (Mer information finns i [CLI dokumentationen](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_list_instances)).

Du kan också använda [resources.azure.com](https://resources.azure.com) eller [Azure SDK](https://azure.microsoft.com/downloads/) att visa de virtuella datorerna i en skaluppsättning.

Hur utdata beror på de alternativ som du anger i kommandot, men här är några exempel på utdata från CLI:

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
> Det finns **ingen garanti** på instansen sätt ID: N tilldelas till de virtuella datorerna i skaluppsättning. De kan verka sekventiellt ökande ibland, men detta är inte alltid fallet. Ta inte ett beroende på hur specifika där instans-ID: N tilldelas till de virtuella datorerna.

## <a name="scale-set-vm-names"></a>Skaluppsättning för VM-namn

I exemplen på utdata ovan har också ”name” för den virtuella datorn. Det här namnet har formen ”{skala-set-name} _ {instans-id}”. Det här namnet är det som du ser i Azure-portalen när du listar instanser i en skaluppsättning:

![](./media/virtual-machine-scale-sets-instance-ids/vmssInstances.png)

{Instans-id} del av namnet är samma decimaltal som egenskapen ”instanceId” som beskrivits tidigare.

## <a name="instance-metadata-vm-name"></a>Metadata för VM-instansnamn

Om du frågar den [instans metadata](../virtual-machines/windows/instance-metadata-service.md) från i en skaluppsättning för virtuell dator visas ett ”namn” i utdata:

```
{
  "compute": {
    "location": "westus",
    "name": "nsgvmss_85",
    .
    .
    .
```

Det här namnet är detsamma som namnet som beskrivits tidigare.

## <a name="scale-set-vm-computer-name"></a>Skaluppsättning för VM-datornamn

Varje virtuell dator i en skala som också hämtar ett datornamn som är kopplade till den. Den här datornamn är värdnamnet på den virtuella datorn i den [Azure-tillhandahållna DNS-namnmatchning i det virtuella nätverket](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md). Den här datornamn är i formatet ”{computer-name-prefix}{base-36-instance-id}”.

{Base-36-instans-id} är i [basera 36](https://en.wikipedia.org/wiki/Base36) och alltid är sex tecken långt. Om den grundläggande 36 representationen av antalet tar fler än fem siffror, läggs {base-36-instans-id} nollor så att den sex siffror långt. Till exempel har en instans med {--prefixet} ”nsgvmss” och instans-ID 85 datorn namnet ”nsgvmss00002D”.

>[!NOTE]
> Prefixet är en egenskap av scale set modell som du kan ange så att den kan skilja sig från namnet på skalan sig själv.