---
title: Så här återställer du lokalt Linux-lösenord på virtuella Azure-datorer | Microsoft-dokument
description: Introducera stegen för att återställa det lokala Linux-lösenordet på Azure VM
services: virtual-machines-linux
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 08/20/2019
ms.author: delhan
ms.openlocfilehash: 83751538efe4f3d3af5928caa04b265b6c867442
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71153578"
---
# <a name="how-to-reset-local-linux-password-on-azure-vms"></a>Återställa lokala Linux-lösenord i virtuella Azure-datorer

Den här artikeln introducerar flera metoder för att återställa lokala Linux Virtual Machine (VM) lösenord. Om användarkontot har upphört att gälla eller om du bara vill skapa ett nytt konto kan du använda följande metoder för att skapa ett nytt lokalt administratörskonto och få åtkomst till den virtuella datorn igen.

## <a name="symptoms"></a>Symtom

Du kan inte logga in på den virtuella datorn och du får ett meddelande som anger att lösenordet som du använde är felaktigt. Dessutom kan du inte använda VMAgent för att återställa ditt lösenord på Azure-portalen.

## <a name="manual-password-reset-procedure"></a>Manuell procedur för återställning av lösenord

> [!NOTE]
> Följande steg gäller inte för den virtuella datorn med ohanterat disk.

1. Ta en ögonblicksbild för OS-disken för den berörda virtuella datorn, skapa en disk från ögonblicksbilden och bifoga sedan disken till en felsöka virtuell dator. Mer information finns i [Felsöka en Virtuell Windows-dator genom att koppla OS-disken till en återställnings-VM med Azure-portalen](troubleshoot-recovery-disks-portal-linux.md).

2. Anslut till den virtuella felsökningsdatorn med fjärrskrivbord.

3.  Kör följande SSH-kommando på felsöka virtuella datorer för att bli en superanvändare.

    ```bash
    sudo su
    ```

4.  Kör **fdisk -l** eller titta på systemloggar för att hitta den nyligen anslutna disken. Leta reda på enhetsnamnet som ska monteras. Titta sedan i den relevanta loggfilen på den tidsmässiga virtuella datorn.

    ```bash
    grep SCSI /var/log/kern.log (ubuntu)
    grep SCSI /var/log/messages (centos, suse, oracle)
    ```

    Följande är exempel på utdata från kommandot grep:

    ```bash
    kernel: [ 9707.100572] sd 3:0:0:0: [sdc] Attached SCSI disk
    ```

5.  Skapa en monteringspunkt som kallas **tempmount**.

    ```bash
    mkdir /tempmount
    ```

6.  Montera OS-disken på monteringspunkten. Du behöver vanligtvis montera *sdc1* eller *sdc2*. Detta beror på värdpartitionen i */etc-katalogen* från den trasiga maskindisken.

    ```bash
    mount /dev/sdc1 /tempmount
    ```

7.  Skapa kopior av kärnautentiseringsuppgifterna innan du gör några ändringar:

    ```bash
    cp /etc/passwd /etc/passwd_orig    
    cp /etc/shadow /etc/shadow_orig    
    cp /tempmount/etc/passwd /etc/passwd
    cp /tempmount/etc/shadow /etc/shadow 
    cp /tempmount/etc/passwd /tempmount/etc/passwd_orig
    cp /tempmount/etc/shadow /tempmount/etc/shadow_orig
    ```

8.  Återställ användarens lösenord som du behöver:

    ```bash
    passwd <<USER>> 
    ```

9.  Flytta de ändrade filerna till rätt plats på den trasiga datorns disk.

    ```bash
    cp /etc/passwd /tempmount/etc/passwd
    cp /etc/shadow /tempmount/etc/shadow
    cp /etc/passwd_orig /etc/passwd
    cp /etc/shadow_orig /etc/shadow
    ```

10. Gå tillbaka till roten och avmontera disken.

    ```bash
    cd /
    umount /tempmount
    ```

11. I Azure-portalen kan du koppla från disken från den virtuella felsökningsdatorn.

12. [Ändra OS-disken för den berörda virtuella datorn](troubleshoot-recovery-disks-portal-linux.md#swap-the-os-disk-for-the-vm).

## <a name="next-steps"></a>Nästa steg

* [Felsöka Azure VM genom att koppla OS-disk till en annan Virtuell Azure-dator](https://social.technet.microsoft.com/wiki/contents/articles/18710.troubleshoot-azure-vm-by-attaching-os-disk-to-another-azure-vm.aspx)

* [Azure CLI: Så här tar du bort och distribuerar om en virtuell dator från VHD](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/azure-cli-how-to-delete-and-re-deploy-a-vm-from-vhd/)
