---
title: "Lägga till Linux-avbildningar i Azure-stacken"
description: "Lär dig hur du lägger till Linux-avbildningar till Azure-stacken."
services: azure-stack
documentationcenter: 
author: anjayajodha
manager: byronr
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 9/25/2017
ms.author: anajod
ms.openlocfilehash: a8763c01cba4e5a9eaa3b7842b627d6eb9661a95
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="add-linux-images-to-azure-stack"></a>Lägga till Linux-avbildningar i Azure-stacken

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit* 

Du kan distribuera virtuella Linux-datorer i Azure-stacken genom att lägga till en Linux-baserade avbildning på Azure Marketplace för stacken. Det enklaste sättet att lägga till en Linux-avbildning i Azure-stacken är via Marketplace-hantering.

## <a name="marketplace-management"></a>Marketplace-hantering

Använd procedurerna i följande artikel för att ladda ned Linux bilder från Azure Marketplace. Välj de Linux-avbildningar som du vill erbjuda användare på Azure-stacken.

[Hämta marketplace-objekt från Azure till Azure-stacken](azure-stack-download-azure-marketplace-item.md).

## <a name="download-an-image"></a>Hämta en avbildning

Du kan ladda ned och extrahera Azure Stack-kompatibla Linux bilder med hjälp av följande länkar:


   * [Bitnami](https://bitnami.com/azure-stack)
   * [CentOS](http://olstacks.cloudapp.net/latest/)
   * [CoreOS](https://stable.release.core-os.net/amd64-usr/current/coreos_production_azure_image.vhd.bz2)
   * [SuSE](https://download.suse.com/Download?buildid=VCFi7y7MsFQ~)
   * [Ubuntu 14.04 LTS](https://partner-images.canonical.com/azure/azure_stack/) / [Ubuntu 16.04 LTS](http://cloud-images.ubuntu.com/releases/xenial/release/ubuntu-16.04-server-cloudimg-amd64-disk1.vhd.zip)

1. Extrahera VHD-avbildning om det behövs och [lägga till avbildningen till Marketplace](azure-stack-add-vm-image.md). Se till att den `OSType` parametern anges till `Linux`.
2. När du har lagt till avbildningen till Marketplace, Marketplace-objektet har skapats och användarna kan distribuera en virtuell Linux-dator.

## <a name="prepare-your-own-image"></a>Förbereda en egen avbildning

Du kan förbereda en egen avbildning för Linux med någon av följande anvisningar:
   
   * [CentOS-baserade distributioner](../virtual-machines/linux/create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Debian Linux](../virtual-machines/linux/debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Oracle Linux](../virtual-machines/linux/oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Red Hat Enterprise Linux](../virtual-machines/linux/redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [SLES & openSUSE](../virtual-machines/linux/suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Ubuntu](../virtual-machines/linux/create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

1. Hämta och installera den [Azure Linux-agenten](https://github.com/Azure/WALinuxAgent/).
   
    Azure Linux-agenten version 2.1.3 eller högre krävs för att etablera din Linux VM på Azure-stacken. Många av de distributioner som visas tidigare innehåller redan den här versionen av agenten eller högre som ett paket i sina databaser (vanligtvis kallar `WALinuxAgent` eller `walinuxagent`). Men om versionen av Azure-agenten är mindre än 2.1.3 (till exempel 2.0.18 eller nedre), måste du installera agenten manuellt. Den installerade versionen kan fastställas från paketnamnet eller genom att köra `/usr/sbin/waagent -version` på den virtuella datorn.
   
    Följ anvisningarna nedan för att installera Azure Linux-agenten manuellt-
   
   a. Hämta den senaste Azure Linux-agenten från först [GitHub](https://github.com/Azure/WALinuxAgent/releases), exempel:
     
            # wget https://github.com/Azure/WALinuxAgent/archive/v2.2.16.tar.gz
   b. Packa upp Azure-agenten:
     
            # tar -vzxf v2.2.16.tar.gz
   c. Installera python-installationsverktyg
     
        **Debian / Ubuntu**
     
            # sudo apt-get update
            # sudo apt-get install python-setuptools
     
        **Ubuntu 16.04+**
     
            # sudo apt-get install python3-setuptools
     
        **RHEL / CentOS / Oracle Linux**
     
            # sudo yum install python-setuptools
   d. Installera Azure-agenten:
     
            # cd WALinuxAgent-2.2.16
            # sudo python3 setup.py install --register-service
     
     System med Python 2.x och Python 3.x installerat sida-vid-sida kan behöva köra följande kommando:
     
         sudo python3 setup.py install --register-service
     Mer information finns i Azure Linux-agenten [viktigt](https://github.com/Azure/WALinuxAgent/blob/master/README.md).
2. [Lägga till avbildningen till Marketplace](azure-stack-add-vm-image.md). Se till att den `OSType` parametern anges till `Linux`.
3. När du har lagt till avbildningen till Marketplace, Marketplace-objektet har skapats och användarna kan distribuera en virtuell Linux-dator.

## <a name="next-steps"></a>Nästa steg
[Översikt över erbjuda tjänster i Azure-stacken](azure-stack-offer-services-overview.md)

