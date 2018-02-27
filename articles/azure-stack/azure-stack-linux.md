---
title: "Lägga till Linux-avbildningar i Azure-stacken"
description: "Lär dig hur du lägger till Linux-avbildningar till Azure-stacken."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 6e6f9ca3b314ee2f58d8007e7ddc93ddd213e361
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/24/2018
---
# <a name="add-linux-images-to-azure-stack"></a>Lägga till Linux-avbildningar i Azure-stacken

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Du kan distribuera virtuella Linux-datorer (VM) på Azure-stacken genom att lägga till en Linux-baserade avbildning på Azure Marketplace för stacken. Det enklaste sättet att lägga till en Linux-avbildning i Azure-stacken är via Marketplace-hantering. Dessa avbildningar har förberett och testats för kompatibilitet med Azure-stacken.

## <a name="marketplace-management"></a>Marketplace Management

Använd procedurerna i följande artikel för att ladda ned Linux bilder från Azure Marketplace. Välj de Linux-avbildningar som du vill erbjuda användare på Azure-stacken.

[Hämta marketplace-objekt från Azure till Azure-stacken](azure-stack-download-azure-marketplace-item.md).

## <a name="prepare-your-own-image"></a>Förbereda en egen avbildning

Du kan förbereda en egen avbildning för Linux med någon av följande anvisningar:

   * [CentOS-baserade distributioner](../virtual-machines/linux/create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Debian Linux](../virtual-machines/linux/debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [SLES & openSUSE](../virtual-machines/linux/suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Ubuntu](../virtual-machines/linux/create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

1. Hämta och installera den [Azure Linux-agenten](https://github.com/Azure/WALinuxAgent/).

    Azure Linux-agenten version 2.2.2 eller högre krävs för att etablera din Linux VM på Azure-stacken och vissa versioner inte fungerar (2.2.12 och 2.2.13 exempel). Många av de distributioner som visas tidigare innehåller redan en version av agenten (vanligtvis kallar `WALinuxAgent` eller `walinuxagent`). Om du skapar en anpassad avbildning måste du installera agenten manuellt. Den installerade versionen kan fastställas från paketnamnet eller genom att köra `/usr/sbin/waagent -version` på den virtuella datorn.

    Följ anvisningarna nedan för att installera Azure Linux-agenten manuellt-

   a. Hämta den senaste Azure Linux-agenten från först [GitHub](https://github.com/Azure/WALinuxAgent/releases), exempel:

            # wget https://github.com/Azure/WALinuxAgent/archive/v2.2.21.tar.gz
   b. Packa upp Azure-agenten:

            # tar -vzxf v2.2.21.tar.gz
   c. Installera python-installationsverktyg

        **Debian / Ubuntu**

            # sudo apt-get update
            # sudo apt-get install python-setuptools

        **Ubuntu 16.04+**

            # sudo apt-get install python3-setuptools

        **RHEL / CentOS / Oracle Linux**

            # sudo yum install python-setuptools
   d. Installera Azure-agenten:

            # cd WALinuxAgent-2.2.21
            # sudo python3 setup.py install --register-service

     System med Python 2.x och Python 3.x installerat sida-vid-sida kan behöva köra följande kommando:

         sudo python3 setup.py install --register-service
     Mer information finns i Azure Linux-agenten [viktigt](https://github.com/Azure/WALinuxAgent/blob/master/README.md).
2. [Lägga till avbildningen till Marketplace](azure-stack-add-vm-image.md). Se till att den `OSType` parametern anges till `Linux`.
3. När du har lagt till avbildningen till Marketplace, Marketplace-objektet har skapats och användarna kan distribuera en virtuell Linux-dator.

## <a name="next-steps"></a>Nästa steg
[Översikt över erbjuda tjänster i Azure-stacken](azure-stack-offer-services-overview.md)
