---
title: Renderingsprogram – Azure Batch
description: Förinstallerade Batch rendering-program
services: batch
ms.service: batch
author: laurenhughes
ms.author: lahugh
ms.date: 03/26/2018
ms.topic: conceptual
ms.openlocfilehash: 84b2ca30f1ccd49e18e2f9d42133f8672d3f8ad6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60776177"
---
# <a name="pre-installed-applications-on-rendering-vm-images"></a>Förinstallerade program på VM-avbildningar för återgivning

Det är möjligt att använda alla återgivning-program med Azure Batch. Azure Marketplace-VM-avbildningar är dock tillgängliga med vanliga program som redan är installerat.

Om tillämpligt, är där du betalar per användning licensiering tillgänglig för de förinstallerade renderingsprogram. När en Batch-pool har skapats för de nödvändiga programmen kan anges och båda kostnaden för virtuella datorer och program kommer att debiteras per minut. Programmet priserna anges på den [Azure Batch prissättningssidan](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering).

Vissa program har endast stöd för Windows, men de flesta stöds på både Windows och Linux.

## <a name="applications-on-centos-7-rendering-images"></a>Program på CentOS 7 rendering avbildningar

* Autodesk Maya I/O 2017 uppdatering 5 (version 201708032230)
* Autodesk Maya i/o 2018 uppdatering 2 (klipp ut 201711281015)
* Autodesk Arnold för Maya 2017 (Arnold version 5.0.1.1) MtoA-2.0.1.1-2017
* Autodesk Arnold för Maya 2018 (Arnold version 5.0.1.4) MtoA-2.1.0.3-2018
* Chaos Group V-Ray för Maya 2017 (version 3.60.04)
* Chaos Group V-Ray för Maya 2018 (version 3.60.04)
* Blender (2.68)

## <a name="applications-on-latest-windows-server-2016-rendering-images"></a>Program på senaste avbildningar för Windows Server 2016-återgivning

Listan nedan gäller för Windows Server 2016, version 1.3.4 rendering avbildningar.

* Autodesk Maya I/O 2017 uppdatering 5 (version 17.4.5459)
* Autodesk Maya i/o 2018 uppdatering 4 (version 18.4.0.7622)
* Autodesk 3ds Max-i/o 2019 uppdatering 1 (version 21.2.0.2219)
* Autodesk Maya 3ds I/O 2018 uppdatering 4 (version 20.4.0.4254)
* Autodesk Arnold för Maya 2017 (Arnold version 5.2.0.1) MtoA-3.1.0.1-2017
* Autodesk Arnold för Maya 2018 (Arnold version 5.2.0.1) MtoA-3.1.0.1-2018
* Autodesk Arnold för 3ds Max (Arnold version 5.0.2.4)(version 1.2.926)
* Chaos Group V-Ray för Maya 2018 (version 3.52.03)
* Chaos Group V-Ray för 3ds Max 2018 (version 3.60.02)
* Chaos Group V-Ray för Maya 2019 (version 3.52.03)
* Chaos Group V-Ray för 3ds Max 2019 (version 4.10.01)
* Blender (2.79)

> [!NOTE]
> Chaos Group V-Ray för 3ds Max 2019 (version 4.10.01) introducerar ändringar i V-ray. Använd Windows Server 2016, version 1.3.2 rendering noder om du vill använda den tidigare versionen (version 3.60.02).

## <a name="applications-on-previous-windows-server-2016-rendering-images"></a>Program i tidigare bilder för Windows Server 2016-återgivning

Listan nedan gäller för Windows Server 2016, version 1.3.2 rendering avbildningar.

* Autodesk Maya I/O 2017 uppdatering 5 (version 17.4.5459)
* Autodesk Maya i/o 2018 uppdatering 4 (version 18.4.0.7622)  
* Autodesk 3ds Max-i/o 2019 uppdatering 1 (version 21.2.0.2219)
* Autodesk Maya 3ds I/O 2018 uppdatering 4 (version 20.4.0.4254)
* Autodesk Arnold för Maya 2017 (Arnold version 5.2.0.1) MtoA-3.1.0.1-2017
* Autodesk Arnold för Maya 2018 (Arnold version 5.2.0.1) MtoA-3.1.0.1-2018
* Autodesk Arnold för 3ds Max (Arnold version 5.0.2.4)(version 1.2.926)
* Chaos Group V-Ray för Maya 2019 (version 3.52.03)
* Chaos Group V-Ray för 3ds Max 2018 (version 3.60.02)
* Blender (2.79)

## <a name="next-steps"></a>Nästa steg

Om du vill använda rendering VM-avbildningar som de behöver anges i poolkonfigurationen när poolen skapas; se den [Batch-pool funktioner för rendering](https://docs.microsoft.com/azure/batch/batch-rendering-functionality#batch-pools).