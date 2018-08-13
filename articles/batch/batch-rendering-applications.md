---
title: Batch rendering-program
description: Förinstallerade Batch rendering-program
services: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: 28acd1b7275694d38a52f14d2b2c32b79cc8183e
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2018
ms.locfileid: "40034829"
---
# <a name="pre-installed-applications-on-rendering-vm-images"></a>Förinstallerade program på VM-avbildningar för återgivning

Det är möjligt att använda alla återgivning-program med Azure Batch. Azure Marketplace-VM-avbildningar är dock tillgängliga med vanliga program som redan är installerat.

Om tillämpligt, är där du betalar per användning licensiering tillgänglig för de förinstallerade renderingsprogram. När en Batch-pool har skapats för de nödvändiga programmen kan anges och båda kostnaden för virtuella datorer och program kommer att debiteras per minut. Programmet priserna anges på den [Azure Batch prissättningssidan](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering).

Vissa program har endast stöd för Windows, men de flesta stöds på både Windows och Linux.

## <a name="applications-on-centos-7-rendering-nodes"></a>Program på CentOS 7 rendering noder

* Autodesk Maya I/O 2017 uppdatering 5 (version 201708032230)
* Autodesk Maya i/o 2018 uppdatering 2 (klipp ut 201711281015)
* Autodesk Arnold för Maya 2017 (Arnold version 5.0.1.1) MtoA-2.0.1.1-2017
* Autodesk Arnold för Maya 2018 (Arnold version 5.0.1.4) MtoA-2.1.0.3-2018
* Chaos Group V-Ray för Maya 2017 (version 3.60.04)
* Chaos Group V-Ray för Maya 2018 (version 3.60.04)
* Blender (2.68)

## <a name="applications-on-windows-server-2016-rendering-nodes"></a>Program på Windows Server 2016 rendering noder

* Autodesk Maya I/O 2017 uppdatering 5 (version 17.4.5459)
* Autodesk Maya i/o 2018 Update 3 (version 18.3.0.7040)  
* Autodesk 3ds Max-i/o 2019 uppdatering 1 (version 21.10.1314)
* Autodesk Maya 3ds I/O 2018 uppdatering 4 (version 20.4.0.4254)
* Autodesk Arnold för Maya (Arnold version 5.0.1.1) MtoA-2.0.1.1-2017
* Autodesk Arnold för Maya (Arnold version 5.0.1.4) MtoA-2.0.2.3-2018
* Autodesk Arnold för 3ds Max (Arnold version 5.0.2.4)(version 1.2.926)
* Chaos Group V-Ray för Maya (version 3.52.03)
* Chaos Group V-Ray för 3ds Max (version 3.60.02)
* Blender (2.79)

## <a name="next-steps"></a>Nästa steg

Om du vill använda rendering VM-avbildningar som de behöver anges i poolkonfigurationen när poolen skapas; se den [Batch-pool funktioner för rendering](https://docs.microsoft.com/azure/batch/batch-rendering-functionality#batch-pools).