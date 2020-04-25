---
title: Översikt över disk lagring
description: Översikt över Azure Managed disks, som hanterar lagrings konton åt dig när du använder virtuella datorer.
author: roygara
ms.service: virtual-machines
ms.topic: overview
ms.date: 04/24/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: c66324841989580712d893ada9121ae40f5161a3
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2020
ms.locfileid: "82147776"
---
# <a name="introduction-to-azure-managed-disks"></a>Introduktion till Azure Managed Disks

Azure Managed disks är lagrings volymer på Block nivå som hanteras av Azure och används med Azure Virtual Machines. Hanterade diskar liknar en fysisk disk på en lokal server, men virtualiseras. Med hanterade diskar behöver du bara ange disk storlek, disk typ och etablera disken. När du har etablerat disken hanterar Azure resten.

De tillgängliga disk typerna är Ultra disks, Premium-hårddiskar (solid-state-hårddiskar), standard-SSD och standard hård diskar (HDD). Information om varje enskild disk typ finns i [Välj en disktyp för virtuella IaaS-datorer](disks-types.md).

[!INCLUDE [virtual-machines-managed-disks-overview.md](../../../includes/virtual-machines-managed-disks-overview.md)]

> [!div class="nextstepaction"]
> [Välja en disktyp för virtuella IaaS-datorer](disks-types.md)