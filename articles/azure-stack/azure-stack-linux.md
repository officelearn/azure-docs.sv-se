---
title: Lägga till Linux-avbildningar i Azure Stack
description: Lär dig hur du lägger till Linux-avbildningar till Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 11/16/2018
ms.openlocfilehash: 8e30edcc7a600088693de57264665f5ffff3842a
ms.sourcegitcommit: f863ed1ba25ef3ec32bd188c28153044124cacbc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/15/2019
ms.locfileid: "56300745"
---
# <a name="add-linux-images-to-azure-stack"></a>Lägga till Linux-avbildningar i Azure Stack

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

Du kan distribuera virtuella Linux-datorer (VM) på Azure Stack genom att lägga till en Linux-baserad avbildning i Azure Stack Marketplace. Det enklaste sättet att lägga till en Linux-avbildning till Azure Stack är via hantering av Marketplace. Dessa avbildningar har förberett och testats för kompatibilitet med Azure Stack.

## <a name="marketplace-management"></a>Marketplace Management

Om du vill ladda ned Linux-avbildningar från Azure Marketplace, Följ stegen i den [hämta marketplace-objekt från Azure till Azure Stack](azure-stack-download-azure-marketplace-item.md) artikeln. Välj Linux-avbildningar som du vill erbjuda användare på Azure Stack. 

Observera att det finns frekventa uppdateringar till dessa bilder, så kontrollera Marketplace Management ofta för att hålla dig uppdaterad.

## <a name="prepare-your-own-image"></a>Förbereda en egen avbildning

Om möjligt hämta avbildningar via Marketplace-hantering som har förberetts och testats för Azure Stack.

Azure Linux-agent (kallas vanligtvis `WALinuxAgent` eller `walinuxagent`) är obligatoriska och inte alla versioner av agentarbetet på Azure Stack. Du bör använda version 2.2.20 eller senare om du skapar en egen avbildning. Observera att [cloud-init](https://cloud-init.io/) stöds inte på Azure Stack just nu.

Du kan förbereda din egen Linux-avbildning med följande anvisningar:

* [CentOS-baserade distributioner](../virtual-machines/linux/create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Debian Linux](../virtual-machines/linux/debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Red Hat Enterprise Linux](azure-stack-redhat-create-upload-vhd.md)
* [SLES & openSUSE](../virtual-machines/linux/suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Ubuntu Server](../virtual-machines/linux/create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="add-your-image-to-the-marketplace"></a>Lägg till din avbildning på Marketplace

Följ [lägga till bilden i Marketplace](azure-stack-add-vm-image.md). Se till att den `OSType` parametern är inställd på `Linux`.

När du har lagt till bilden i Marketplace, ett Marketplace-objekt skapas och användare kan distribuera en Linux-dator.

## <a name="next-steps"></a>Nästa steg

Se följande artiklar för mer information:

- [Hämta marketplace-objekt från Azure till Azure Stack](azure-stack-download-azure-marketplace-item.md)
- [Översikt över Azure Stack Marketplace](azure-stack-marketplace.md)
