---
title: Översikt över Azure Disk Storage för virtuella Windows-datorer
description: Översikt över Azure-hanterade diskar, som hanterar lagringskonton för dig när du använder virtuella Azure Windows-datorer
author: roygara
ms.service: virtual-machines-windows
ms.topic: overview
ms.date: 12/02/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 7b2c2c1289a40d63b2f396ee59000c3aedb14c3d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "75460039"
---
# <a name="introduction-to-azure-managed-disks"></a>Introduktion till Azure Managed Disks

Azure-hanterade diskar är lagringsvolymer på blocknivå som hanteras av Azure och används med Virtuella Azure-datorer. Hanterade diskar är som en fysisk disk i en lokal server men virtualiserad. Med hanterade diskar behöver du bara ange diskstorlek, disktyp och etablera disken. När du har etablerat disken hanterar Azure resten.

De tillgängliga typerna av diskar är ultradiskar, SSD-enheter (Premium Solid State Drives), standard-SSD-enheter och vanliga hårddiskar (HDD). Information om varje enskild disktyp finns i [Välj en disktyp för virtuella IaaS-datorer](disks-types.md).

[!INCLUDE [virtual-machines-managed-disks-overview.md](../../../includes/virtual-machines-managed-disks-overview.md)]

> [!div class="nextstepaction"]
> [Välja en disktyp för virtuella IaaS-datorer](disks-types.md)
