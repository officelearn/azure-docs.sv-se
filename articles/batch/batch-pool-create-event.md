---
title: Skapa händelse för att skapa Azure Batch-pool
description: Referens för händelsen Skapa batch-pool, som skickas ut när en pool har skapats. Innehållet i loggen kommer att visa allmän information om poolen.
services: batch
author: LauraBrenner
manager: evansma
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: labrenne
ms.openlocfilehash: dea025b274278aa5fed2900c95b4a274541ffef9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022197"
---
# <a name="pool-create-event"></a>Händelse för skapande av pool

 Den här händelsen avges när en pool har skapats. Innehållet i loggen kommer att visa allmän information om poolen. Observera att om målstorleken för poolen är större än 0 beräkningsnoder, följer en pool storleksstarthändelse omedelbart efter den här händelsen.

 I följande exempel visas brödtexten för en pool `CloudServiceConfiguration` skapa händelse för en pool som skapats med hjälp av egenskapen.

```
{
    "id": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Standard_F1s",
    "imageType": "VirtualMachineConfiguration",
    "cloudServiceConfiguration": {
        "osFamily": "3",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "virtualMachineConfiguration": {
          "imageReference": {
            "publisher": " ",
            "offer": " ",
            "sku": " ",
            "version": " "
          },
          "nodeAgentId": " "
        },
    "resizeTimeout": "300000",
    "targetDedicatedNodes": 2,
    "targetLowPriorityNodes": 2,
    "maxTasksPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoScale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

|Element|Typ|Anteckningar|
|-------------|----------|-----------|
|`id`|String|Id för poolen.|
|`displayName`|String|Poolens visningsnamn.|
|`vmSize`|String|Storleken på de virtuella datorerna i poolen. Alla virtuella datorer i en pool har samma storlek. <br/><br/> Information om tillgängliga storlekar för virtuella datorer för Molntjänstpooler (pooler som skapats med cloudServiceConfiguration) finns i [Storlekar för molntjänster](https://azure.microsoft.com/documentation/articles/cloud-services-sizes-specs/). Batch stöder alla VM-storlekar `ExtraSmall`för molntjänster utom .<br/><br/> Information om tillgängliga VM-storlekar för pooler som använder avbildningar från Marketplace för virtuella datorer (pooler som skapats med virtualMachineConfiguration) finns [i Storlekar för virtuella datorer](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-sizes/) (Linux) eller storlekar för virtuella [datorer](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/) (Windows). Batch har stöd för alla storlekar för virtuella datorer i Azure utom `STANDARD_A0` och de med Premium Storage (`STANDARD_GS`, `STANDARD_DS`- och `STANDARD_DSV2`-serien).|
|`imageType`|String|Distributionsmetoden för avbildningen. Värden som `virtualMachineConfiguration` stöds är eller`cloudServiceConfiguration`|
|[`cloudServiceConfiguration`](#bk_csconf)|Komplex typ|Molntjänstkonfigurationen för poolen.|
|[`virtualMachineConfiguration`](#bk_vmconf)|Komplex typ|Konfigurationen för den virtuella datorn för poolen.|
|[`networkConfiguration`](#bk_netconf)|Komplex typ|Nätverkskonfigurationen för poolen.|
|`resizeTimeout`|Tid|Tidsgränsen för allokering av beräkningsnoder till poolen som angetts för den senaste storleksåtgärden på poolen.  (Den första storleksändringen när poolen skapas räknas som en storleksändring.)|
|`targetDedicatedNodes`|Int32|Antalet dedikerade beräkningsnoder som begärs för poolen.|
|`targetLowPriorityNodes`|Int32|Antalet beräkningsnoder med låg prioritet som begärs för poolen.|
|`enableAutoScale`|Bool|Anger om poolstorleken justeras automatiskt med tiden.|
|`enableInterNodeCommunication`|Bool|Anger om poolen har ställts in för direkt kommunikation mellan noder.|
|`isAutoPool`|Bool|Anger om poolen skapades via ett jobbs Automatiska poolmekanism.|
|`maxTasksPerNode`|Int32|Det maximala antalet aktiviteter som kan köras samtidigt på en enda beräkningsnod i poolen.|
|`vmFillType`|String|Definierar hur batch-tjänsten distribuerar uppgifter mellan beräkningsnoder i poolen. Giltiga värden är Spread eller Pack.|

###  <a name="cloudserviceconfiguration"></a><a name="bk_csconf"></a>cloudServiceConfiguration

|Elementnamn|Typ|Anteckningar|
|------------------|----------|-----------|
|`osFamily`|String|Azure Guest OS-familjen som ska installeras på de virtuella datorerna i poolen.<br /><br /> Möjliga värden:<br /><br /> **2** – OS Family 2, motsvarande Windows Server 2008 R2 SP1.<br /><br /> **3** – OS Family 3, motsvarande Windows Server 2012.<br /><br /> **4** – OS Family 4, motsvarande Windows Server 2012 R2.<br /><br /> Mer information finns i [Azure Guest OS Releases](https://azure.microsoft.com/documentation/articles/cloud-services-guestos-update-matrix/#releases).|
|`targetOSVersion`|String|Azure Guest OS-versionen som ska installeras på de virtuella datorerna i poolen.<br /><br /> Standardvärdet är **\*** vilket anger den senaste operativsystemversionen för den angivna familjen.<br /><br /> Andra tillåtna värden finns i [Azure Guest OS Releases](https://azure.microsoft.com/documentation/articles/cloud-services-guestos-update-matrix/#releases).|

###  <a name="virtualmachineconfiguration"></a><a name="bk_vmconf"></a>virtualMachineKonfigurering

|Elementnamn|Typ|Anteckningar|
|------------------|----------|-----------|
|[`imageReference`](#bk_imgref)|Komplex typ|Anger information om plattformen eller Marketplace-avbildningen som ska användas.|
|`nodeAgentId`|String|SKU:n för batchnodagenten som etablerats på beräkningsnoden.|
|[`windowsConfiguration`](#bk_winconf)|Komplex typ|Anger Windows-operativsysteminställningar på den virtuella datorn. Den här egenskapen får inte anges om avbildningen refererar till en Linux OS-avbildning.|

###  <a name="imagereference"></a><a name="bk_imgref"></a>bildReferens

|Elementnamn|Typ|Anteckningar|
|------------------|----------|-----------|
|`publisher`|String|Utgivaren av bilden.|
|`offer`|String|Erbjudandet om bilden.|
|`sku`|String|Bildens SKU.|
|`version`|String|Versionen av bilden.|

###  <a name="windowsconfiguration"></a><a name="bk_winconf"></a>windowsKonfigurering

|Elementnamn|Typ|Anteckningar|
|------------------|----------|-----------|
|`enableAutomaticUpdates`|Boolean|Anger om den virtuella datorn är aktiverad för automatiska uppdateringar. Om den här egenskapen inte anges är standardvärdet sant.|

###  <a name="networkconfiguration"></a><a name="bk_netconf"></a>nätverkskonfiguration

|Elementnamn|Typ|Anteckningar|
|------------------|--------------|----------|
|`subnetId`|String|Anger resursidentifieraren för det undernät där poolens beräkningsnoder skapas.|
