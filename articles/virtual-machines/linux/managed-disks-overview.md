---
title: Översikt över Azure-disklagring Managed disks för virtuella Linux-datorer | Microsoft Docs
description: Översikt över Azure Managed disks, som hanterar lagrings konton åt dig när du använder virtuella Linux-datorer
author: roygara
ms.service: virtual-machines-linux
ms.topic: overview
ms.date: 11/06/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: de6ac88d5619266d049f0dc5bd4b54d674f00df7
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2019
ms.locfileid: "73796266"
---
# <a name="introduction-to-azure-managed-disks"></a>Introduktion till Azure Managed disks

En Azure-hanterad disk är en virtuell hård disk (VHD). Du kan tänka på det som en fysisk disk på en lokal server, men virtualiserad. Azure Managed disks lagras som Page BLOB-objekt, som är ett slumpmässigt IO-lagringssystem i Azure. Vi kallar en hanterad disk hanterad eftersom det är en abstraktion över Page blobbar, BLOB-behållare och Azure Storage-konton. Med hanterade diskar behöver du bara etablera disken och Azure tar hand om resten.

När du väljer att använda Azure Managed disks med dina arbets belastningar, skapar och hanterar Azure disken åt dig. De tillgängliga disk typerna är Ultra disk, Premium solid state-hårddisk (SSD), Standard SSD och standard hård disk (HDD). Mer information om varje enskild disk typ finns i [Välj en disktyp för virtuella IaaS-datorer](disks-types.md).

[!INCLUDE [virtual-machines-managed-disks-overview.md](../../../includes/virtual-machines-managed-disks-overview.md)]

> [!div class="nextstepaction"]
> [Välj en disktyp för virtuella IaaS-datorer](disks-types.md)
