---
title: Azure Disk Storage managed disk översikt för Windows-datorer | Microsoft Docs
description: Översikt över Azure-hanterade diskar, som hanterar storage-konton åt dig när du använder virtuella Azure Windows-datorer
services: virtual-machines-windows,storage
author: roygara
ms.service: virtual-machines-windows
ms.workload: storage
ms.tgt_pltfrm: vm-windows
ms.topic: overview
ms.date: 04/22/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: fb1ee8556935b141dfee6a18c96ecafb476aa584
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60761835"
---
# <a name="introduction-to-azure-managed-disks"></a>Introduktion till Azure hanterade diskar

En Azure-hanterad disk är en virtuell hårddisk (VHD). Du kan se det som en fysisk disk i en lokal server men virtualiserade. Azure-hanterade diskar lagras som sidblobar som är ett slumpmässigt i/o-lagringsobjekt i Azure. Vi kallar en hanterad disk som ”hanterad” eftersom det är en abstraktion över page blobs, blob-behållare och Azure storage-konton. Allt du behöver göra är att etablera disken med hanterade diskar och Azure tar hand om resten.

När du väljer att använda Azure hanterade diskar med dina arbetsbelastningar, Azure skapar och hanterar disken åt dig. Tillgängliga typer av diskar är Ultra Solid tillstånd-hårddiskar (SSD) (förhandsversion), Premium SSD, Standard SSD och Standard hårddiskar (HDD). Läs mer om varje enskild disk-typ, [väljer en disktyp för virtuella IaaS-datorer](disks-types.md).

[!INCLUDE [virtual-machines-managed-disks-overview.md](../../../includes/virtual-machines-managed-disks-overview.md)]

> [!div class="nextstepaction"]
> [Välj en disktyp för virtuella IaaS-datorer](disks-types.md)