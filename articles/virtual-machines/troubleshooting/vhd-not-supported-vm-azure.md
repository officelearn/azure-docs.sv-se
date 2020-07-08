---
title: VHD stöds inte när du skapar en virtuell dator i Azure | Microsoft Docs
description: Den här artikeln hjälper till att korrigera VHD-fel när du kör en virtuell dator i Microsoft Azure.
services: virtual-machines
documentationCenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
ms.service: virtual-machines
ms.assetid: 5488aba9-c3da-435d-b4a5-63470f455b07
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure
ms.date: 06/29/2020
ms.author: genli
ms.openlocfilehash: ff4822b513ed2aea6a18ba45bffc1d060ee2410e
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85937573"
---
# <a name="vhd-is-not-supported-when-you-create-a-virtual-machine-in-azure"></a>VHD stöds inte när du skapar en virtuell dator i Azure

Den här artikeln hjälper till att korrigera VHD-fel när du kör en virtuell dator i Windows eller Linux.

## <a name="symptoms"></a>Symtom

När du skapar en virtuell dator i Microsoft Azure med hjälp av en uppladdad virtuell hård disk, Miss lyckas distributionen och returnerar följande fel meddelande: 

```
New-AzureRmVM : Long running operation failed with status 'Failed'.
ErrorCode: InvalidVhd
ErrorMessage: The specified cookie value in VHD footer indicates that disk 'diskname' with blob https://xxxxxx.blob.core.windows.net/vhds/samplename.vhd is not a supported VHD. Disk is expected to have cookie value 'conectix'.
```

## <a name="cause"></a>Orsak

Det här problemet uppstår av någon av följande orsaker:

- Den virtuella hårddisken uppfyller inte justeringen på 1 MB (förskjutning). Diskstorleken som stöds bör vara 1 MB * N. Disken bör till exempel vara 102401 MB.
- Den virtuella hård disken är skadad eller stöds inte. 

## <a name="resolution"></a>Lösning

> [!NOTE]
> För att utföra följande korrigering måste kunden utföra de här stegen innan du laddar upp den virtuella hård disken i Azure.

Lös problemet genom att ändra storlek på disken för att följa 1 MB-justering:

- Lös problemet i Windows med hjälp av [PowerShell-cmdleten för att ändra storlek på VHD](https://docs.microsoft.com/powershell/module/hyper-v/resize-vhd). Observera att **ändra storlek – VHD** är inte en Azure PowerShell-cmdlet.

  1. [Installera Hyper-V-rollen på Windows Server](https://docs.microsoft.com/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server)
  1. [Konvertera den virtuella disken till en virtuell hård disk med fast storlek](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image#convert-the-virtual-disk-to-a-fixed-size-vhd)

- Använd [kommandot qemu-img](https://docs.microsoft.com/azure/virtual-machines/linux/create-upload-generic)för att lösa problemet i Linux.

Mer information om hur du skapar och laddar upp en virtuell hård disk för att skapa en virtuell Azure-dator finns i följande artiklar:

- [Ladda upp och skapa en virtuell Linux-dator från en anpassad disk avbildning med hjälp av Azure CLI 1,0](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd)
- [Skapa och överför en Windows Server VHD till Azure](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed)

Fortsatta problem kan tyda på en skadad virtuell hård disk. I den här situationen rekommenderar vi att du bygger upp den virtuella hård disken från grunden.

Mer information finns i följande artiklar:

- [Om Windows VHD](https://docs.microsoft.com/azure/virtual-machines/windows/about-disks-and-vhds#about-vhds)
- [Om Linux VHD](https://docs.microsoft.com/azure/virtual-machines/linux/about-disks-and-vhds#about-vhds)