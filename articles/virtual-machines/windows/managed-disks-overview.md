---
title: Översikt över Azure-disklagring Managed disk för virtuella Windows-datorer | Microsoft Docs
description: Översikt över Azure Managed disks, som hanterar lagrings kontona åt dig när du använder virtuella Azure-datorer i Windows
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
ms.sourcegitcommit: 57a7d4f67635212f5bf0c56e58fd87c8ec366f2c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/22/2019
ms.locfileid: "64725814"
---
# <a name="introduction-to-azure-managed-disks"></a>Introduktion till Azure Managed disks

En Azure-hanterad disk är en virtuell hård disk (VHD). Du kan tänka på det som en fysisk disk på en lokal server, men virtualiserad. Azure Managed disks lagras som Page BLOB-objekt, som är ett slumpmässigt IO-lagringssystem i Azure. Vi kallar en hanterad disk hanterad eftersom det är en abstraktion över Page blobbar, BLOB-behållare och Azure Storage-konton. Med hanterade diskar behöver du bara etablera disken och Azure tar hand om resten.

När du väljer att använda Azure Managed disks med dina arbets belastningar, skapar och hanterar Azure disken åt dig. De tillgängliga disk typerna är otroligt solid state-hårddiskar (SSD) (för hands version), Premium SSD, Standard SSD och standard hård diskar (HDD). Mer information om varje enskild disk typ finns i [Välj en disktyp för virtuella IaaS-datorer](disks-types.md).

[!INCLUDE [virtual-machines-managed-disks-overview.md](../../../includes/virtual-machines-managed-disks-overview.md)]

> [!div class="nextstepaction"]
> [Välj en disktyp för virtuella IaaS-datorer](disks-types.md)