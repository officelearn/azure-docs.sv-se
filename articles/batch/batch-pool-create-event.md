---
title: Skapa händelse för Azure Batch pool | Microsoft Docs
description: Referens för skapande händelse för batch-pool.
services: batch
author: laurenhughes
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: 3f7b20cb915e0a969c6a400c07a094943f7f3a1b
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2019
ms.locfileid: "70258430"
---
# <a name="pool-create-event"></a>Händelse för skapande av pool

 Den här händelsen genereras när en pool har skapats. Loggens innehåll kommer att visa allmän information om poolen. Observera att om mål storleken för poolen är större än 0 Compute-noder, kommer en storleks start händelse för poolen att följas omedelbart efter den här händelsen.

 I följande exempel visas bröd texten i en pool för att skapa en pool för en pool `CloudServiceConfiguration` som skapats med egenskapen.

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

|Element|type|Anteckningar|
|-------------|----------|-----------|
|`id`|Sträng|Poolens ID.|
|`displayName`|Sträng|Visnings namnet för poolen.|
|`vmSize`|Sträng|Storleken på de virtuella datorerna i poolen. Alla virtuella datorer i en pool har samma storlek. <br/><br/> Information om tillgängliga storlekar för virtuella datorer för Cloud Services pooler (pooler som skapats med cloudServiceConfiguration) finns i [storlekar för Cloud Services](https://azure.microsoft.com/documentation/articles/cloud-services-sizes-specs/). Batch har stöd för alla Cloud Services VM `ExtraSmall`-storlekar utom.<br/><br/> Information om tillgängliga VM-storlekar för pooler med hjälp av avbildningar från Virtual Machines Marketplace (pooler som skapats med virtualMachineConfiguration) finns i [storlekar för Virtual Machines](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-sizes/) (Linux) eller [storlekar för Virtual Machines](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/) (Windows). Batch har stöd för alla storlekar för virtuella datorer i Azure utom `STANDARD_A0` och de med Premium Storage (`STANDARD_GS`, `STANDARD_DS`- och `STANDARD_DSV2`-serien).|
|`imageType`|Sträng|Avbildningens distributions metod. Värden som stöds `virtualMachineConfiguration` är eller`cloudServiceConfiguration`|
|[`cloudServiceConfiguration`](#bk_csconf)|Komplex typ|Moln tjänst konfigurationen för poolen.|
|[`virtualMachineConfiguration`](#bk_vmconf)|Komplex typ|Konfigurationen för den virtuella datorn för poolen.|
|[`networkConfiguration`](#bk_netconf)|Komplex typ|Nätverks konfigurationen för poolen.|
|`resizeTimeout`|Time|Tids gränsen för allokering av datornoder till poolen som har angetts för den senaste återställningen i poolen.  (Den första storleken när poolen skapas räknas som en storleks ändring.)|
|`targetDedicatedNodes`|Int32|Antalet dedikerade datornoder som har begärts för poolen.|
|`targetLowPriorityNodes`|Int32|Antalet Compute-noder med låg prioritet som begärs för poolen.|
|`enableAutoScale`|Bool|Anger om poolens storlek justeras automatiskt över tid.|
|`enableInterNodeCommunication`|Bool|Anger om poolen har kon figurer ATS för direkt kommunikation mellan noder.|
|`isAutoPool`|Bool|Anger om poolen har skapats via en jobbs mekanism för autopool.|
|`maxTasksPerNode`|Int32|Det maximala antalet uppgifter som kan köras samtidigt på en enda beräknings nod i poolen.|
|`vmFillType`|Sträng|Definierar hur batch-tjänsten distribuerar aktiviteter mellan Compute-noder i poolen. Giltiga värden är spridning eller paket.|

###  <a name="bk_csconf"></a>cloudServiceConfiguration

|Elementnamn|type|Anteckningar|
|------------------|----------|-----------|
|`osFamily`|Sträng|Azure-gästens OS-familj som ska installeras på de virtuella datorerna i poolen.<br /><br /> Möjliga värden är:<br /><br /> **2** – OS-serien 2, som motsvarar Windows Server 2008 R2 SP1.<br /><br /> **3** – OS Family 3, motsvarar Windows Server 2012.<br /><br /> **4** – OS Family 4, motsvarande Windows Server 2012 R2.<br /><br /> Mer information finns i [Azure Guest OS-versioner](https://azure.microsoft.com/documentation/articles/cloud-services-guestos-update-matrix/#releases).|
|`targetOSVersion`|Sträng|Azure-gästens OS-version som ska installeras på de virtuella datorerna i poolen.<br /><br /> Standardvärdet är **\*** som anger den senaste versionen av operativ systemet för den angivna familjen.<br /><br /> För andra tillåtna värden, se [Azure-gästens OS-versioner](https://azure.microsoft.com/documentation/articles/cloud-services-guestos-update-matrix/#releases).|

###  <a name="bk_vmconf"></a>virtualMachineConfiguration

|Elementnamn|type|Anteckningar|
|------------------|----------|-----------|
|[`imageReference`](#bk_imgref)|Komplex typ|Anger information om den plattform eller Marketplace-avbildning som ska användas.|
|`nodeAgentId`|Sträng|SKU: n för batch-nodens agent som har allokerats på Compute-noden.|
|[`windowsConfiguration`](#bk_winconf)|Komplex typ|Anger inställningar för operativ systemet Windows på den virtuella datorn. Den här egenskapen får inte anges om imageReference refererar till en Linux OS-avbildning.|

###  <a name="bk_imgref"></a>imageReference

|Elementnamn|type|Anteckningar|
|------------------|----------|-----------|
|`publisher`|Sträng|Avbildningens utgivare.|
|`offer`|Sträng|Bildens erbjudande.|
|`sku`|Sträng|Avbildningens SKU.|
|`version`|Sträng|Avbildningens version.|

###  <a name="bk_winconf"></a>windowsConfiguration

|Elementnamn|type|Anteckningar|
|------------------|----------|-----------|
|`enableAutomaticUpdates`|Boolesk|Anger om den virtuella datorn är aktive rad för automatiska uppdateringar. Om den här egenskapen inte anges är standardvärdet sant.|

###  <a name="bk_netconf"></a>networkConfiguration

|Elementnamn|type|Anteckningar|
|------------------|--------------|----------|
|`subnetId`|Sträng|Anger resurs-ID för under nätet där poolens Compute-noder skapas.|
