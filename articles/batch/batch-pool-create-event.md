---
title: Händelse för skapande av Azure Batch-pool | Microsoft Docs
description: Referens för Batch-pool Skapa händelse.
services: batch
author: dlepow
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: danlep
ms.openlocfilehash: f8c0adf96d027f58a35dbe570f1b19c311cd84b9
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51246522"
---
# <a name="pool-create-event"></a>Händelse för skapande av pool

 Den här händelsen genereras när poolen har skapats. Innehållet i loggen visas allmän information om poolen. Observera att om mål för poolen är större än 0 compute-noder, en Starthändelse för storleksändring av pool följer direkt efter den här händelsen.

 I följande exempel visar innehållet i en pool Skapa händelse för en pool som skapats med hjälp av egenskapen CloudServiceConfiguration.

```
{
    "id": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Small",
    "cloudServiceConfiguration": {
        "osFamily": "3",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "resizeTimeout": "300000",
    "targetDedicated": 2,
    "maxTasksPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoScale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

|Element|Typ|Anteckningar|
|-------------|----------|-----------|
|id|Sträng|Id för poolen.|
|displayName|Sträng|Visningsnamnet för poolen.|
|vmSize|Sträng|Storleken på de virtuella datorerna i poolen. Alla virtuella datorer i en pool har samma storlek. <br/><br/> Mer information om tillgängliga storlekar för virtuella datorer för Cloud Services pooler (pooler som skapats med cloudServiceConfiguration), finns i [storlekar för Cloud Services](https://azure.microsoft.com/documentation/articles/cloud-services-sizes-specs/). Batch stöder alla Cloud Services VM-storlekar utom `ExtraSmall`.<br/><br/> Information om tillgängliga VM för pooler med hjälp av avbildningar från Marketplace för virtuella datorer (pooler som skapats med virtualMachineConfiguration) finns i [storlekar för virtuella datorer](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-sizes/) (Linux) eller [storlekar för virtuella Datorer](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/) (Windows). Batch har stöd för alla storlekar för virtuella datorer i Azure utom `STANDARD_A0` och de med Premium Storage (`STANDARD_GS`, `STANDARD_DS`- och `STANDARD_DSV2`-serien).|
|[cloudServiceConfiguration](#bk_csconf)|Komplex typ|Konfigurationen för Molntjänsten för poolen.|
|[virtualMachineConfiguration](#bk_vmconf)|Komplex typ|Konfigurationen av virtuell dator för poolen.|
|[networkConfiguration](#bk_netconf)|Komplex typ|Nätverkskonfiguration för poolen.|
|resizeTimeout|Tid|Tidsgränsen för allokering av beräkningsnoder i poolen som angetts för den senaste storleksändringen på poolen.  (Den initiala storlek när poolen skapas om du vill räknas som en storleksändring.)|
|targetDedicated|Int32|Antalet beräkningsnoder som har begärts för poolen.|
|enableAutoScale|Bool|Anger om poolstorleken automatiskt justerar över tid.|
|enableInterNodeCommunication|Bool|Anger om poolen har ställts in för direktkommunikation mellan noder.|
|isAutoPool|Bool|Speficies om poolen har skapats via ett jobb AutoPool mekanism.|
|maxTasksPerNode|Int32|Högsta antal aktiviteter som kan köras samtidigt på en enda beräkningsnod i poolen.|
|vmFillType|Sträng|Definierar hur Batch-tjänsten distribuerar uppgifter mellan beräkningsnoder i poolen. Giltiga värden är fördelade eller Pack.|

###  <a name="bk_csconf"></a> cloudServiceConfiguration

|Elementnamn|Typ|Anteckningar|
|------------------|----------|-----------|
|osFamily|Sträng|Azures gäst-OS-familj som ska installeras på de virtuella datorerna i poolen.<br /><br /> Möjliga värden:<br /><br /> **2** – OS-familjen 2, som motsvarar Windows Server 2008 R2 SP1.<br /><br /> **3** – OS-familjen 3, motsvarande till Windows Server 2012.<br /><br /> **4** – OS-familjen 4, motsvarande till Windows Server 2012 R2.<br /><br /> Mer information finns i [Azure Gästoperativsystemversioner](https://azure.microsoft.com/documentation/articles/cloud-services-guestos-update-matrix/#releases).|
|targetOSVersion|Sträng|Azures gäst-OS-version som ska installeras på de virtuella datorerna i poolen.<br /><br /> Standardvärdet är **\*** som anger den senaste Operativsystemversionen för den angivna familjen.<br /><br /> Andra tillåtna värden finns i [Azure Gästoperativsystemversioner](https://azure.microsoft.com/documentation/articles/cloud-services-guestos-update-matrix/#releases).|

###  <a name="bk_vmconf"></a> virtualMachineConfiguration

|Elementnamn|Typ|Anteckningar|
|------------------|----------|-----------|
|[imageReference](#bk_imgref)|Komplex typ|Anger information om plattform eller Marketplace-avbildningen som ska användas.|
|nodeAgentSKUId|Sträng|SKU: N för Batch-nodagenten som etablerats på Beräkningsnoden.|
|[windowsConfiguration](#bk_winconf)|Komplex typ|Anger inställningar för Windows-operativsystemet på den virtuella datorn. Den här egenskapen får inte anges om imageReference refererar till en Linux OS-avbildning.|

###  <a name="bk_imgref"></a> imageReference

|Elementnamn|Typ|Anteckningar|
|------------------|----------|-----------|
|utgivare|Sträng|Utgivaren av avbildningen.|
|erbjudande|Sträng|Erbjudande för avbildningen.|
|sku|Sträng|SKU för avbildningen.|
|version|Sträng|Version för avbildningen.|

###  <a name="bk_winconf"></a> windowsConfiguration

|Elementnamn|Typ|Anteckningar|
|------------------|----------|-----------|
|enableAutomaticUpdates|Boolesk|Anger om den virtuella datorn har aktiverats för automatiska uppdateringar. Om den här egenskapen inte anges är standardvärdet är sant.|

###  <a name="bk_netconf"></a> networkConfiguration

|Elementnamn|Typ|Anteckningar|
|------------------|--------------|----------|
|subnetId|Sträng|Anger resursidentifieraren för undernätet där poolens beräkningsnoder skapas.|
