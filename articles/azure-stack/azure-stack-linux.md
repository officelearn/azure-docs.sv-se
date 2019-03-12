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
ms.openlocfilehash: 277af6f139e815f46894f5b8df82a1d92ef573d1
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57537141"
---
# <a name="add-linux-images-to-azure-stack"></a>Lägga till Linux-avbildningar i Azure Stack

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

Du kan distribuera virtuella Linux-datorer (VM) på Azure Stack genom att lägga till en Linux-baserad avbildning i Azure Stack Marketplace. Det enklaste sättet att lägga till en Linux-avbildning till Azure Stack är via hantering av Marketplace. Dessa avbildningar har förberett och testats för kompatibilitet med Azure Stack.

## <a name="marketplace-management"></a>Marketplace Management

Om du vill ladda ned Linux-avbildningar från Azure Marketplace, Följ stegen i den [hämta marketplace-objekt från Azure till Azure Stack](azure-stack-download-azure-marketplace-item.md) artikeln. Välj Linux-avbildningar som du vill erbjuda användare på Azure Stack. 

Observera att det finns frekventa uppdateringar till dessa bilder, så kontrollera Marketplace Management ofta för att hålla dig uppdaterad.

## <a name="prepare-your-own-image"></a>Förbereda en egen avbildning

Om möjligt hämta avbildningar via Marketplace-hantering som har förberetts och testats för Azure Stack.

Azure Linux Agent (kallas vanligtvis `WALinuxAgent` eller `walinuxagent`) krävs, och inte alla versioner av agenten fungerar i Azure Stack. Du bör använda den senaste versionen av WALA eller version 2.2.20 om du skapar en egen avbildning. Observera att mellan 2.2.20 och 2.2.35.1 (exklusiva) inte fungerar på Azure Stack. Observera att [cloud-init](https://cloud-init.io/) stöds inte på Azure Stack just nu.

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
