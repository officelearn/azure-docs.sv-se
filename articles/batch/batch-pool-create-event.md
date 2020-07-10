---
title: Händelse för att skapa Azure Batch pool
description: Referens för skapa händelse för batch-pool, som genereras när en pool har skapats. Loggens innehåll kommer att visa allmän information om poolen.
ms.topic: reference
ms.date: 04/20/2017
ms.openlocfilehash: eee512bbeed223269c43bde77435fbff2b67b533
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2020
ms.locfileid: "86147320"
---
# <a name="pool-create-event"></a>Händelse för skapande av pool

 Den här händelsen genereras när en pool har skapats. Loggens innehåll kommer att visa allmän information om poolen. Observera att om mål storleken för poolen är större än 0 Compute-noder, kommer en storleks start händelse för poolen att följas omedelbart efter den här händelsen.

 I följande exempel visas bröd texten i en pool för att skapa en pool för en pool som skapats med `CloudServiceConfiguration` egenskapen.

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
|`id`|Sträng|Poolens ID.|
|`displayName`|Sträng|Visnings namnet för poolen.|
|`vmSize`|Sträng|Storleken på de virtuella datorerna i poolen. Alla virtuella datorer i en pool har samma storlek. <br/><br/> Information om tillgängliga storlekar för virtuella datorer för Cloud Services pooler (pooler som skapats med cloudServiceConfiguration) finns i [storlekar för Cloud Services](../cloud-services/cloud-services-sizes-specs.md). Batch har stöd för alla Cloud Services VM-storlekar utom `ExtraSmall` .<br/><br/> Information om tillgängliga VM-storlekar för pooler med hjälp av avbildningar från Virtual Machines Marketplace (pooler som skapats med virtualMachineConfiguration) finns i [storlekar för Virtual Machines](../virtual-machines/linux/sizes.md?toc=%2Fazure%2Fvirtual-machines%2Flinux%2Ftoc.json) (Linux) eller [storlekar för Virtual Machines](../virtual-machines/windows/sizes.md?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json) (Windows). Batch har stöd för alla storlekar för virtuella datorer i Azure utom `STANDARD_A0` och de med Premium Storage (`STANDARD_GS`, `STANDARD_DS`- och `STANDARD_DSV2`-serien).|
|`imageType`|Sträng|Avbildningens distributions metod. Värden som stöds är `virtualMachineConfiguration` eller`cloudServiceConfiguration`|
|[`cloudServiceConfiguration`](#bk_csconf)|Komplex typ|Moln tjänst konfigurationen för poolen.|
|[`virtualMachineConfiguration`](#bk_vmconf)|Komplex typ|Konfigurationen för den virtuella datorn för poolen.|
|[`networkConfiguration`](#bk_netconf)|Komplex typ|Nätverks konfigurationen för poolen.|
|`resizeTimeout`|Tid|Tids gränsen för allokering av datornoder till poolen som har angetts för den senaste återställningen i poolen.  (Den första storleken när poolen skapas räknas som en storleks ändring.)|
|`targetDedicatedNodes`|Int32|Antalet dedikerade datornoder som har begärts för poolen.|
|`targetLowPriorityNodes`|Int32|Antalet Compute-noder med låg prioritet som begärs för poolen.|
|`enableAutoScale`|Bool|Anger om poolens storlek justeras automatiskt över tid.|
|`enableInterNodeCommunication`|Bool|Anger om poolen har kon figurer ATS för direkt kommunikation mellan noder.|
|`isAutoPool`|Bool|Anger om poolen har skapats via en jobbs mekanism för autopool.|
|`maxTasksPerNode`|Int32|Det maximala antalet uppgifter som kan köras samtidigt på en enda beräknings nod i poolen.|
|`vmFillType`|Sträng|Definierar hur batch-tjänsten distribuerar aktiviteter mellan Compute-noder i poolen. Giltiga värden är spridning eller paket.|

###  <a name="cloudserviceconfiguration"></a><a name="bk_csconf"></a>cloudServiceConfiguration

|Elementnamn|Typ|Anteckningar|
|------------------|----------|-----------|
|`osFamily`|Sträng|Azure-gästens OS-familj som ska installeras på de virtuella datorerna i poolen.<br /><br /> Möjliga värden:<br /><br /> **2** – OS-serien 2, som motsvarar Windows Server 2008 R2 SP1.<br /><br /> **3** – OS Family 3, motsvarar Windows Server 2012.<br /><br /> **4** – OS Family 4, motsvarande Windows Server 2012 R2.<br /><br /> Mer information finns i [Azure Guest OS-versioner](../cloud-services/cloud-services-guestos-update-matrix.md#releases).|
|`targetOSVersion`|Sträng|Azure-gästens OS-version som ska installeras på de virtuella datorerna i poolen.<br /><br /> Standardvärdet är **\*** som anger den senaste versionen av operativ systemet för den angivna familjen.<br /><br /> För andra tillåtna värden, se [Azure-gästens OS-versioner](../cloud-services/cloud-services-guestos-update-matrix.md#releases).|

###  <a name="virtualmachineconfiguration"></a><a name="bk_vmconf"></a>virtualMachineConfiguration

|Elementnamn|Typ|Anteckningar|
|------------------|----------|-----------|
|[`imageReference`](#bk_imgref)|Komplex typ|Anger information om den plattform eller Marketplace-avbildning som ska användas.|
|`nodeAgentId`|Sträng|SKU: n för batch-nodens agent som har allokerats på Compute-noden.|
|[`windowsConfiguration`](#bk_winconf)|Komplex typ|Anger inställningar för operativ systemet Windows på den virtuella datorn. Den här egenskapen får inte anges om imageReference refererar till en Linux OS-avbildning.|

###  <a name="imagereference"></a><a name="bk_imgref"></a>imageReference

|Elementnamn|Typ|Anteckningar|
|------------------|----------|-----------|
|`publisher`|Sträng|Avbildningens utgivare.|
|`offer`|Sträng|Bildens erbjudande.|
|`sku`|Sträng|Avbildningens SKU.|
|`version`|Sträng|Avbildningens version.|

###  <a name="windowsconfiguration"></a><a name="bk_winconf"></a>windowsConfiguration

|Elementnamn|Typ|Anteckningar|
|------------------|----------|-----------|
|`enableAutomaticUpdates`|Boolesk|Anger om den virtuella datorn är aktive rad för automatiska uppdateringar. Om den här egenskapen inte anges är standardvärdet sant.|

###  <a name="networkconfiguration"></a><a name="bk_netconf"></a>networkConfiguration

|Elementnamn|Typ|Anteckningar|
|------------------|--------------|----------|
|`subnetId`|Sträng|Anger resurs-ID för under nätet där poolens Compute-noder skapas.|
