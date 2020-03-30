---
title: Renderingsprogram - Azure Batch
description: Det är möjligt att använda alla renderingsprogram med Azure Batch. Azure Marketplace VM-avbildningar är dock tillgängliga med vanliga program förinstallerade.
services: batch
ms.service: batch
author: LauraBrenner
ms.author: labrenne
ms.date: 09/19/2019
ms.topic: conceptual
ms.openlocfilehash: 77672534b2aad993a44e9b637fbed58df8610e97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022995"
---
# <a name="pre-installed-applications-on-rendering-vm-images"></a>Förinstallerade program på rendering av VM-avbildningar

Det är möjligt att använda alla renderingsprogram med Azure Batch. Azure Marketplace VM-avbildningar är dock tillgängliga med vanliga program förinstallerade.

I förekommande fall är pay-per-use-licensiering tillgänglig för de förinstallerade renderingsprogrammen. När en batchpool skapas kan de nödvändiga programmen anges och både kostnaden för virtuell dator och program debiteras per minut. Programpriser visas på [prissidan för Azure Batch](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering).

Vissa program stöder bara Windows, men de flesta stöds på både Windows och Linux.

## <a name="applications-on-centos-7-rendering-images"></a>Program på CentOS 7 renderingsbilder

Följande lista gäller CentOS 7.6, version 1.1.6 renderingsbilder.

* Autodesk Maya I/O 2017 uppdatering 5 (version 201708032230)
* Autodesk Maya I/O 2018 Uppdatering 2 (cut 201711281015)
* Autodesk Maya I/O 2019 Uppdatering 1
* Autodesk Arnold för Maya 2017 (Arnold version 5.3.1.1) MtoA-3.2.1.1-2017
* Autodesk Arnold för Maya 2018 (Arnold version 5.3.1.1) MtoA-3.2.1.1-2018
* Autodesk Arnold för Maya 2019 (Arnold version 5.3.1.1) MtoA-3.2.1.1-2019
* Chaos Group V-Ray för Maya 2017 (version 3.60.04)
* Chaos Group V-Ray för Maya 2018 (version 3.60.04)
* Blender (2.68)
* Mixer (2,8)

## <a name="applications-on-latest-windows-server-2016-rendering-images"></a>Program på de senaste återgivningsavbildningarna i Windows Server 2016

Följande lista gäller för Windows Server 2016, version 1.3.8-återgivningsbilder.

* Autodesk Maya I/O 2017 uppdatering 5 (version 17.4.5459)
* Autodesk Maya I/O 2018 Uppdatering 6 (version 18.4.0.7622)
* Autodesk Maya I/O 2019
* Autodesk Maya 3ds I/O 2018 uppdatering 4 (version 20.4.0.4254)
* Autodesk 3ds Max I/O 2019 Uppdatering 1 (version 21.2.0.2219)
* Autodesk 3ds Max I/O 2020 Uppdatering 2
* Autodesk Arnold för Maya 2017 (Arnold version 5.3.0.2) MtoA-3.2.0.2-2017
* Autodesk Arnold för Maya 2018 (Arnold version 5.3.0.2) MtoA-3.2.0.2-2018
* Autodesk Arnold för Maya 2019 (Arnold version 5.3.0.2) MtoA-3.2.0.2-2019
* Autodesk Arnold för 3ds Max 2018 (Arnold version 5.3.0.2)(version 1.2.926)
* Autodesk Arnold för 3ds Max 2019 (Arnold version 5.3.0.2)(version 1.2.926)
* Autodesk Arnold för 3ds Max 2020 (Arnold version 5.3.0.2)(version 1.2.926)
* Chaos Group V-Ray för Maya 2017 (version 4.12.01)
* Chaos Group V-Ray för Maya 2018 (version 4.12.01)
* Chaos Group V-Ray för Maya 2019 (version 4.04.03)
* Chaos Group V-Ray för 3ds Max 2018 (version 4.20.01)
* Chaos Group V-Ray för 3ds Max 2019 (version 4.20.01)
* Chaos Group V-Ray för 3ds Max 2020 (version 4.20.01)
* Blender (2.79)
* Mixer (2,80)
* AZ 10 (PÅ ANDRA)

> [!IMPORTANT]
> Om du vill köra V-Ray med Maya utanför `vrayses.exe` [Azure Batch-tilläggsmallarna](https://github.com/Azure/batch-extension-templates)börjar du innan du kör renderingen. Om du vill starta vrayses.exe utanför mallarna `%MAYA_2017%\vray\bin\vrayses.exe"`kan du använda följande kommando .
>
> En till exempel se startuppgiften för [Maya- och V-Ray-mallen](https://github.com/Azure/batch-extension-templates/blob/master/templates/maya/render-vray-windows/pool.template.json) på GitHub.

## <a name="applications-on-previous-windows-server-2016-rendering-images"></a>Program på tidigare Windows Server 2016-renderingsavbildningar

Följande lista gäller för Windows Server 2016, version 1.3.7-återgivningsbilder.

* Autodesk Maya I/O 2017 uppdatering 5 (version 17.4.5459)
* Autodesk Maya I/O 2018 Uppdatering 4 (version 18.4.0.7622)
* Autodesk 3ds Max I/O 2019 Uppdatering 1 (version 21.2.0.2219)
* Autodesk Maya 3ds I/O 2018 uppdatering 4 (version 20.4.0.4254)
* Autodesk Arnold för Maya 2017 (Arnold version 5.2.0.1) MtoA-3.1.0.1-2017
* Autodesk Arnold för Maya 2018 (Arnold version 5.2.0.1) MtoA-3.1.0.1-2018
* Autodesk Arnold för 3ds Max 2018 (Arnold version 5.0.2.4)(version 1.2.926)
* Autodesk Arnold för 3ds Max 2019 (Arnold version 5.0.2.4)(version 1.2.926)
* Chaos Group V-Ray för Maya 2018 (version 3.52.03)
* Chaos Group V-Ray för 3ds Max 2018 (version 3.60.02)
* Chaos Group V-Ray för Maya 2019 (version 3.52.03)
* Chaos Group V-Ray för 3ds Max 2019 (version 4.10.01)
* Blender (2.79)

> [!NOTE]
> Chaos Group V-Ray för 3ds Max 2019 (version 4.10.01) introducerar bryta förändringar i V-ray. Om du vill använda den tidigare versionen (version 3.60.02) använder du Windows Server 2016, version 1.3.2-renderingsnoder.

## <a name="next-steps"></a>Nästa steg

Om du vill använda avbildningarna för återgivning av virtuella datorer måste de anges i poolkonfigurationen när en pool skapas. se [batchpoolen för rendering](https://docs.microsoft.com/azure/batch/batch-rendering-functionality#batch-pools).
