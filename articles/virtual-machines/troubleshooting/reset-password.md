---
title: Återställa lokala Linux-lösenord på virtuella Azure-datorer | Microsoft Docs
description: Införa stegen för att återställa det lokala Linux-lösenordet på den virtuella Azure-datorn
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
ms.openlocfilehash: c6bfd5b9ff3626593916533f27c5c2755cebcb13
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "87028489"
---
# <a name="how-to-reset-local-linux-password-on-azure-vms"></a>Återställa lokala Linux-lösenord i virtuella Azure-datorer

Den här artikeln beskriver flera metoder för att återställa lokala virtuella Linux-lösenord (Virtual Machine). Om användar kontot har upphört att gälla eller om du bara vill skapa ett nytt konto kan du använda följande metoder för att skapa ett nytt lokalt administratörs konto och få åtkomst till den virtuella datorn igen.

## <a name="symptoms"></a>Symtom

Du kan inte logga in på den virtuella datorn och du får ett meddelande som anger att det lösen ord som du använde är felaktigt. Dessutom kan du inte använda VMAgent för att återställa lösen ordet på Azure Portal.

## <a name="manual-password-reset-procedure"></a>Procedur för manuell lösen ords återställning

> [!NOTE]
> Följande steg gäller inte för den virtuella datorn med en ohanterad disk.

1. Ta en ögonblicks bild för OS-disken för den berörda virtuella datorn, skapa en disk från ögonblicks bilden och Anslut sedan disken till en Felsök virtuell dator. Mer information finns i [Felsöka en virtuell Windows-dator genom att koppla OS-disken till en virtuell återställnings dator med hjälp av Azure Portal](troubleshoot-recovery-disks-portal-linux.md).

2. Anslut till den virtuella fel söknings datorn med hjälp av fjärr skrivbord.

3.  Kör följande SSH-kommando på den virtuella datorn för fel sökning för att bli en super-användare.

    ```bash
    sudo su
    ```

4.  Kör **fdisk-l** eller titta på system loggarna för att hitta den nyligen anslutna disken. Leta upp enhets namnet som ska monteras. Leta sedan upp den aktuella logg filen på den temporala virtuella datorn.

    ```bash
    grep SCSI /var/log/kern.log (ubuntu)
    grep SCSI /var/log/messages (centos, suse, oracle)
    ```

    Följande är exempel på en utmatning från grep-kommandot:

    ```bash
    kernel: [ 9707.100572] sd 3:0:0:0: [sdc] Attached SCSI disk
    ```

5.  Skapa en monterings punkt med namnet **tempmount**.

    ```bash
    mkdir /tempmount
    ```

6.  Montera OS-disken på monterings punkten. Du behöver vanligt vis montera *sdc1* eller *sdc2*. Detta beror på värd partitionen i *volymen/etc* -katalogen från den skadade dator disken.

    ```bash
    mount /dev/sdc1 /tempmount
    ```

7.  Skapa kopior av filerna för kärn autentiseringsuppgifter innan du gör några ändringar:

    ```bash
    cp /etc/passwd /etc/passwd_orig    
    cp /etc/shadow /etc/shadow_orig    
    cp /tempmount/etc/passwd /etc/passwd
    cp /tempmount/etc/shadow /etc/shadow 
    cp /tempmount/etc/passwd /tempmount/etc/passwd_orig
    cp /tempmount/etc/shadow /tempmount/etc/shadow_orig
    ```

8.  Återställ användarens lösen ord som du behöver:

    ```bash
    passwd <<USER>> 
    ```

9.  Flytta de ändrade filerna till rätt plats på den skadade datorns disk.

    ```bash
    cp /etc/passwd /tempmount/etc/passwd
    cp /etc/shadow /tempmount/etc/shadow
    cp /etc/passwd_orig /etc/passwd
    cp /etc/shadow_orig /etc/shadow
    ```

10. Gå tillbaka till roten och demontera disken.

    ```bash
    cd /
    umount /tempmount
    ```

11. I Azure Portal kopplar du bort disken från den virtuella fel söknings datorn.

12. [Ändra OS-disken för den berörda virtuella datorn](troubleshoot-recovery-disks-portal-linux.md#swap-the-os-disk-for-the-vm).

## <a name="next-steps"></a>Nästa steg

* [Felsöka virtuell Azure-dator genom att koppla OS-disk till en annan virtuell Azure-dator](https://social.technet.microsoft.com/wiki/contents/articles/18710.troubleshoot-azure-vm-by-attaching-os-disk-to-another-azure-vm.aspx)

* [Azure CLI: så här tar du bort och distribuerar om en virtuell dator från en virtuell hård disk](/archive/blogs/linuxonazure/azure-cli-how-to-delete-and-re-deploy-a-vm-from-vhd)
