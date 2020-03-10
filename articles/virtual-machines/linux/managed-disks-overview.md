---
title: Översikt över disk lagring
description: Översikt över Azure Managed disks, som hanterar lagrings konton åt dig när du använder virtuella datorer.
author: roygara
ms.service: virtual-machines
ms.topic: overview
ms.date: 12/02/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: edcde9c5192791e88da57d158bab3e79b171bfc8
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2020
ms.locfileid: "78944766"
---
# <a name="introduction-to-azure-managed-disks"></a>Introduktion till Azure Managed disks

Azure Managed disks är lagrings volymer på Block nivå som hanteras av Azure och används med Azure Virtual Machines. Hanterade diskar liknar en fysisk disk på en lokal server, men virtualiseras. Med hanterade diskar behöver du bara ange disk storlek, disk typ och etablera disken. När du har etablerat disken hanterar Azure resten.

De tillgängliga disk typerna är Ultra disks, Premium-hårddiskar (solid-state-hårddiskar), standard-SSD och standard hård diskar (HDD). Information om varje enskild disk typ finns i [Välj en disktyp för virtuella IaaS-datorer](disks-types.md).

[!INCLUDE [virtual-machines-managed-disks-overview.md](../../../includes/virtual-machines-managed-disks-overview.md)]

> [!div class="nextstepaction"]
> [Välj en disktyp för virtuella IaaS-datorer](disks-types.md)