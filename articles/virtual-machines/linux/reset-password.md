---
title: Så här återställer du den lokala Linux lösenord på Azure Virtual Machines | Microsoft Docs
description: Lägger till stegen för att återställa det lokala Linux-lösenordet på Azure VM
services: virtual-machines-linux
documentationcenter: ''
author: Deland-Han
manager: cshepard
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/03/2017
ms.author: delhan
ms.openlocfilehash: b9182ec2a974de06c2bd45928b9964f253653bf6
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2018
ms.locfileid: "27578370"
---
# <a name="how-to-reset-local-linux-password-on-azure-vms"></a>Så här återställer du den lokala Linux lösenord på Azure Virtual Machines

Den här artikeln innehåller flera metoder för att återställa lösenord för lokala Linux virtuella dator (VM). Om användarkontot har upphört att gälla eller om du vill skapa ett nytt konto, kan du använda följande metoder för att skapa ett nytt konto för lokal administratör och få åtkomst till den virtuella datorn igen.

## <a name="symptoms"></a>Symtom

Du kan inte logga in till den virtuella datorn och du får ett meddelande som anger att det lösenord som du använde är felaktig. Du kan dessutom använda VMAgent för att återställa lösenordet på Azure Portal. 

## <a name="manual-password-reset-procedure"></a>Proceduren för manuell återställning av lösenord

1.  Ta bort den virtuella datorn och hålla anslutna diskar.

2.  Koppla OS-enhet som en datadisk till en annan temporal virtuell dator på samma plats.

3.  Kör följande SSH-kommando på den temporala virtuella datorn blir en användare.


    ~~~~
    sudo su
    ~~~~

4.  Kör **fdisk -l** eller titta på systemloggar för att hitta nyligen ansluten disk. Hitta enhetsnamnet att montera. Sedan temporal VM titta på i relevanta loggfilen.

    ~~~~
    grep SCSI /var/log/kern.log (ubuntu)
    grep SCSI /var/log/messages (centos, suse, oracle)
    ~~~~

    Följande är exempel på utdata grep-kommandot:

    ~~~~
    kernel: [ 9707.100572] sd 3:0:0:0: [sdc] Attached SCSI disk
    ~~~~

5.  Skapa en monteringspunkt kallas **tempmount**.

    ~~~~
    mkdir /tempmount
    ~~~~

6.  Montera OS-disken på monteringspunkten. Du behöver vanligtvis montera sdc1 eller sdc2. Detta beror på värd partitionen i katalogen/etc från datordisken brutna.

    ~~~~
    mount /dev/sdc1 /tempmount
    ~~~~

7.  Utför en säkerhetskopiering innan du gör några ändringar:

    ~~~~
    cp /etc/passwd /etc/passwd_orig    
    cp /etc/shadow /etc/shadow_orig    
    cp /tempmount/etc/passwd /etc/passwd
    cp /tempmount/etc/shadow /etc/shadow 
    cp /tempmount/etc/passwd /tempmount/etc/passwd_orig
    cp /tempmount/etc/shadow /tempmount/etc/shadow_orig
    ~~~~

8.  Återställa användarlösenord som du behöver:

    ~~~~
    passwd <<USER>> 
    ~~~~

9.  Flytta de ändrade filerna till rätt plats på den brutna datorns disk.

    ~~~~
    cp /etc/passwd /tempmount/etc/passwd
    cp /etc/shadow /tempmount/etc/shadow
    cp /etc/passwd_orig /etc/passwd
    cp /etc/shadow_orig /etc/shadow
    ~~~~

10. Gå tillbaka till roten och demontera disken.

    ~~~~
    cd /
    umount /tempmount
    ~~~~

11. Koppla bort disk från hanteringsportalen.

12. Skapa den virtuella datorn.

## <a name="next-steps"></a>Nästa steg

* [Felsöka Azure VM genom att koppla OS-disken till en annan virtuell dator i Azure](http://social.technet.microsoft.com/wiki/contents/articles/18710.troubleshoot-azure-vm-by-attaching-os-disk-to-another-azure-vm.aspx)

* [Azure CLI: Hur du tar bort och distribuera en virtuell dator från VHD](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/azure-cli-how-to-delete-and-re-deploy-a-vm-from-vhd/)
