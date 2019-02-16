---
title: Azure Disk Storage managed disk översikt för virtuella Linux-datorer | Microsoft Docs
description: Översikt över Azure-hanterade diskar, som hanterar storage-konton åt dig när du använder virtuella Linux-datorer
services: virtual-machines-linux,storage
author: roygara
ms.service: virtual-machines-linux
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 02/11/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: d8394121019338b53c87f7229d0ccf52fbdf8f21
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/16/2019
ms.locfileid: "56327035"
---
# <a name="introduction-to-azure-managed-disks"></a>Introduktion till Azure hanterade diskar

En Azure-hanterad disk är en virtuell hårddisk (VHD). Du kan se det som en fysisk disk i en lokal server men virtualiserade. Azure-hanterade diskar lagras som sidblobar som är ett slumpmässigt i/o-lagringsobjekt i Azure. Vi kallar en hanterad disk som ”hanterad” eftersom det är en abstraktion över page blobs, blob-behållare och Azure storage-konton. Allt du behöver göra är att etablera disken med hanterade diskar och Azure tar hand om resten.

När du väljer att använda Azure hanterade diskar med dina arbetsbelastningar, Azure skapar och hanterar disken åt dig. Tillgängliga typer av diskar är ultra diskar (förhandsversion), premium solid state-hårddiskar (SSD), standard SSD- och standard-hårddiskar (HDD). Läs mer om varje enskild disk-typ, [väljer en disktyp för virtuella IaaS-datorer](disks-types.md).

[!INCLUDE [virtual-machines-managed-disks-overview.md](../../../includes/virtual-machines-managed-disks-overview.md)]

> [!div class="nextstepaction"]
> [Välj en disktyp för virtuella IaaS-datorer](disks-types.md)
