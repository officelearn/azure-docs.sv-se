---
title: Lägga till Linux-avbildningar i Azure-stacken
description: Lär dig hur du lägger till Linux-avbildningar till Azure-stacken.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 64a860bc925b9c7499363c1fe39d03df88a9a51d
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
ms.locfileid: "33935727"
---
# <a name="add-linux-images-to-azure-stack"></a>Lägga till Linux-avbildningar i Azure-stacken

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Du kan distribuera virtuella Linux-datorer (VM) på Azure-stacken genom att lägga till en Linux-baserade avbildning på Azure Marketplace för stacken. Det enklaste sättet att lägga till en Linux-avbildning i Azure-stacken är via Marketplace-hantering. Dessa avbildningar har förberett och testats för kompatibilitet med Azure-stacken.

## <a name="marketplace-management"></a>Marketplace-hantering

Använd procedurerna i följande artikel för att ladda ned Linux bilder från Azure Marketplace. Välj de Linux-avbildningar som du vill erbjuda användare på Azure-stacken. 

[Hämta marketplace-objekt från Azure till Azure-stacken](azure-stack-download-azure-marketplace-item.md).

Observera att det finns många uppdateringar till dessa bilder, så kontrollera Marketplace Management ofta för att hålla dig uppdaterad.

## <a name="prepare-your-own-image"></a>Förbereda en egen avbildning

 Hämta avbildningarna som är tillgängligt via Marketplace Management som har förberetts och testats för Azure-Stack om möjligt. 
 
 Azure Linux-agenten (vanligtvis kallar `WALinuxAgent` eller `walinuxagent`) krävs, och inte alla versioner av agenten fungerar i Azure-stacken. Du bör använda version 2.2.18 eller senare om du skapar en egen avbildning. Observera att [moln init](https://cloud-init.io/) stöds inte på Azure-stacken just nu.

 Du kan förbereda dina egna Linux-avbildning med hjälp av följande anvisningar:

   * [CentOS-baserade distributioner](../virtual-machines/linux/create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Debian Linux](../virtual-machines/linux/debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Red Hat Enterprise Linux](azure-stack-redhat-create-upload-vhd.md)
   * [SLES & openSUSE](../virtual-machines/linux/suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Ubuntu Server](../virtual-machines/linux/create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

    
## <a name="add-your-image-to-the-marketplace"></a>Lägga till bilden till marketplace
 
Följ [lägga till avbildningen till Marketplace](azure-stack-add-vm-image.md). Se till att den `OSType` parametern anges till `Linux`.

När du har lagt till avbildningen till Marketplace, Marketplace-objektet har skapats och användarna kan distribuera en virtuell Linux-dator.
