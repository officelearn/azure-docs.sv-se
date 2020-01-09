---
title: Azure-disklagring översikt för virtuella Linux-datorer
description: Översikt över Azure Managed disks, som hanterar lagrings konton åt dig när du använder virtuella Linux-datorer
author: roygara
ms.service: virtual-machines-linux
ms.topic: overview
ms.date: 12/02/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 2b25da174399b092fe821a46b235d7a2bd14572b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75355887"
---
# <a name="introduction-to-azure-managed-disks"></a>Introduktion till Azure Managed disks

Azure Managed disks är lagrings volymer på Block nivå som hanteras av Azure och används med Azure Virtual Machines. Hanterade diskar liknar en fysisk disk på en lokal server, men virtualiseras. Med hanterade diskar behöver du bara ange disk storlek, disk typ och etablera disken. När du har etablerat disken hanterar Azure resten.

De tillgängliga disk typerna är Ultra disks, Premium-hårddiskar (solid-state-hårddiskar), standard-SSD och standard hård diskar (HDD). Information om varje enskild disk typ finns i [Välj en disktyp för virtuella IaaS-datorer](disks-types.md).

[!INCLUDE [virtual-machines-managed-disks-overview.md](../../../includes/virtual-machines-managed-disks-overview.md)]

> [!div class="nextstepaction"]
> [Välj en disktyp för virtuella IaaS-datorer](disks-types.md)