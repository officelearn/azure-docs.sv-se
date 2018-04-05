---
title: Skapa en Azure Batch-pool händelse | Microsoft Docs
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
ms.openlocfilehash: bf7dfc2600c3d94faeb8d03561f6f2b30a0ee2d2
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
# <a name="pool-create-event"></a>Händelse för skapande av pool

 Denna händelse genereras när en pool som har skapats. Innehållet i loggen visar allmän information om poolen. Observera att om mål för poolen är större än 0 compute-noder, start för poolen storleksändringar följer omedelbart efter den här händelsen.

 I följande exempel visar innehållet i en pool skapa en händelse för en pool som skapats med hjälp av egenskapen CloudServiceConfiguration.

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
|vmSize|Sträng|Storleken på de virtuella datorerna i poolen. Alla virtuella datorer i en pool har samma storlek. <br/><br/> Information om tillgängliga storlekar för virtuella datorer för molntjänster pooler (pooler som skapats med cloudServiceConfiguration), finns i [storlekar för molntjänster](http://azure.microsoft.com/documentation/articles/cloud-services-sizes-specs/). Batch stöder alla Cloud Services VM-storlekar utom `ExtraSmall`.<br/><br/> Information om tillgängliga VM storlekar för pooler med bilder från Marketplace för virtuella datorer (pooler som har skapats med virtualMachineConfiguration) finns [storlekar för virtuella datorer](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-sizes/) (Linux) eller [storlekar för virtuella datorer](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/) (Windows). Batch har stöd för alla storlekar för virtuella datorer i Azure utom `STANDARD_A0` och de med Premium Storage (`STANDARD_GS`, `STANDARD_DS`- och `STANDARD_DSV2`-serien).|
|[cloudServiceConfiguration](#bk_csconf)|Komplex typ|Molnet tjänstkonfigurationen för poolen.|
|[virtualMachineConfiguration](#bk_vmconf)|Komplex typ|Konfigurationen av virtuella datorn för poolen.|
|[networkConfiguration](#bk_netconf)|Komplex typ|Nätverkskonfiguration för poolen.|
|resizeTimeout|Tid|Tidsgräns för fördelning av beräkningsnoder i poolen som angetts för den senaste åtgärden Ändra storlek på poolen.  (Den inledande storleken när poolen skapats räknas som en ändring av storlek.)|
|targetDedicated|Int32|Antal compute-noder som har begärts för poolen.|
|enableAutoScale|Booleskt|Anger om justerar poolstorleken automatiskt med tiden.|
|enableInterNodeCommunication|Booleskt|Anger om poolen har ställts in för direkt kommunikation mellan noder.|
|isAutoPool|Booleskt|Speficies om poolen har skapats via ett jobb AutoPool mekanism.|
|maxTasksPerNode|Int32|Det maximala antalet aktiviteter som kan köras samtidigt på en enda beräkningsnod i poolen.|
|vmFillType|Sträng|Definierar hur Batch-tjänsten distribuerar uppgifter mellan beräkningsnoder i poolen. Giltiga värden är spridda eller Pack.|

###  <a name="bk_csconf"></a> cloudServiceConfiguration

|Elementnamn|Typ|Anteckningar|
|------------------|----------|-----------|
|osFamily|Sträng|Azure gäst-OS-familjen installeras på de virtuella datorerna i poolen.<br /><br /> Möjliga värden:<br /><br /> **2** – OS-familjen 2, som motsvarar Windows Server 2008 R2 SP1.<br /><br /> **3** – OS-familjen 3, som motsvarar Windows Server 2012.<br /><br /> **4** – OS-familjen 4 som motsvarar Windows Server 2012 R2.<br /><br /> Mer information finns i [Azure versioner för gästoperativsystem](https://azure.microsoft.com/documentation/articles/cloud-services-guestos-update-matrix/#releases).|
|targetOSVersion|Sträng|Azure gäst-OS-version som ska installeras på de virtuella datorerna i poolen.<br /><br /> Standardvärdet är **\*** som anger den senaste versionen för operativsystemet för den angivna familjen.<br /><br /> Andra tillåtna värden finns [Azure versioner för gästoperativsystem](https://azure.microsoft.com/documentation/articles/cloud-services-guestos-update-matrix/#releases).|

###  <a name="bk_vmconf"></a> virtualMachineConfiguration

|Elementnamn|Typ|Anteckningar|
|------------------|----------|-----------|
|[imageReference](#bk_imgref)|Komplex typ|Anger information om plattform eller Marketplace-avbildningen som ska användas.|
|nodeAgentSKUId|Sträng|SKU av Batch-nod-agenten har etablerats på Beräkningsnoden.|
|[windowsConfiguration](#bk_winconf)|Komplex typ|Anger inställningar för Windows-operativsystemet på den virtuella datorn. Den här egenskapen får inte anges om imageReference refererar till en Linux OS-avbildningen.|

###  <a name="bk_imgref"></a> imageReference

|Elementnamn|Typ|Anteckningar|
|------------------|----------|-----------|
|publisher|Sträng|Utgivaren av avbildningen.|
|Erbjudande|Sträng|Erbjudande för bilden.|
|sku|Sträng|SKU för bilden.|
|version|Sträng|Version för avbildningen.|

###  <a name="bk_winconf"></a> windowsConfiguration

|Elementnamn|Typ|Anteckningar|
|------------------|----------|-----------|
|enableAutomaticUpdates|Boolesk|Anger om den virtuella datorn är aktiverat för automatiska uppdateringar. Standardvärdet är true om den här egenskapen inte anges.|

###  <a name="bk_netconf"></a> networkConfiguration

|Elementnamn|Typ|Anteckningar|
|------------------|--------------|----------|
|subnetId|Sträng|Anger resurs-ID för undernätet där poolens beräkningsnoder skapas.|
