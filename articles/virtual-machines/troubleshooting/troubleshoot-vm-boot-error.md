---
title: Start fel för virtuell Azure-dator
description: Det gick inte att starta den virtuella datorn eftersom den virtuella datorn angav en räddnings konsol
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: ''
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 08/28/2019
ms.author: tiag
ms.openlocfilehash: 14bf7b289f3d03dd1437a18b0b5bd35fdec567ea
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2019
ms.locfileid: "70143315"
---
# <a name="vm-boot-error"></a>Fel vid start av virtuell dator

Vi har identifierat att din virtuella dator (VM) angav en räddnings konsol. Problemet uppstår när din virtuella Linux-dator hade kernel-ändringar som nyligen lagts till som en kernel-uppgradering och inte längre startar på rätt sätt på grund av kernel-fel under start processen. När Start inläsaren försöker hitta Linux-kärnan under start processen och den lämnar start kontrollen till den, kommer den virtuella datorn att gå in i en räddnings konsol när leveransen Miss lyckas.

Om du upptäcker att du inte kan ansluta till en virtuell dator i framtiden kan du Visa en skärm bild av den virtuella datorn med hjälp av startdiagnostik-bladet i Azure Portal. Det här kan hjälpa dig att diagnostisera problemet och fastställa om ett liknande startfel är orsaken.

## <a name="recommended-steps"></a>Rekommenderade åtgärder

Följ stegen nedan beroende på vilket fel du får:

### <a name="error---unknown-filesystem"></a>Fel-okänt fil system

* Om du får felet **Okänt fil**system kan det här felet bero på ett skadat fil system på startpartitionen eller en felaktig kernel-konfiguration.

   * För fil Systems problem följer du stegen i artikeln [Linux-återställning: Det går inte att använda SSH till virtuella Linux-datorer på grund av fel](https://blogs.msdn.microsoft.com/linuxonazure/2016/09/13/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck-inodes/)i fil systemet (fsck, noder i procent).
   * För kernel-problem följer du stegen i artikeln [Linux-återställning: Åtgärda problem som inte kan startas manuellt och som rör](https://blogs.msdn.microsoft.com/linuxonazure/2016/10/09/linux-recovery-manually-fixing-non-boot-issues-related-to-kernel-problems/)kernel- [problem eller Linux-återställning: Åtgärda icke-startproblem som rör kernel-problem med](https://blogs.msdn.microsoft.com/linuxonazure/2016/10/09/linux-recovery-fixing-non-boot-issues-related-to-kernel-problems-using-chroot/)chroot.
   
### <a name="error---file-not-found"></a>Fel-filen hittades inte

* Om du får fel **meddelandet 15: Filen hittades inte eller så gick det** inte att hitta den första RAM-disken eller så går det **inte att hitta initrd/initramfs-filen**.

    * För den saknade filen `/boot/grub2/grub.cfg` eller `initrd/initramfs` Fortsätt med följande process:

    1. Se `/etc/default/grub` till att de finns och har rätt/önskade inställningar. Om du inte vet vilka inställningar som är standardinställningar kan du kontrol lera med en fungerande virtuell dator.

    2. Kör sedan följande kommando för att återskapa konfigurationen:`grub2-mkconfig -o /boot/grub2/grub.cfg`

   * `/boot/grub/menu.lst`Om den saknade filen är är det här felet för äldre OS-versioner (**RHEL 6. x**, **CentOS 6. x** och **Ubuntu 14,04**) så att kommandona kan skjuta upp. Du måste sätta igång en gammal Server och testa för att se till att rätt kommandon tillhandahålls.

### <a name="error---no-such-partition"></a>Fel-ingen sådan partition

* Om du får ett fel meddelande om att det **inte finns någon sådan partition**, se till [fall scenario: "ingen sådan partition"-fel vid försök att starta den virtuella datorn efter försök att utöka operativ system enheten](https://blogs.technet.microsoft.com/shwetanayak/2017/03/12/case-scenario-no-such-partition-error-while-trying-to-start-the-vm-after-attempting-to-extend-the-os-drive/).

### <a name="error---grubcfg-file-not-found"></a>Fel-grub. cfg-filen hittades inte

* Om du får fel meddelandet Det gick **inte att hitta/Boot/grub2/grub.cfg**följer du stegen nedan.

    * För den saknade filen `/boot/grub2/grub.cfg` eller `initrd/initramfs` Fortsätt med följande process:

    1. Se `/etc/default/grub` till att de finns och har rätt/önskade inställningar. Om du inte vet vilka inställningar som är standardinställningar kan du kontrol lera med en fungerande virtuell dator.

    2. Kör sedan följande kommando för att återskapa konfigurationen: `grub2-mkconfig -o /boot/grub2/grub.cfg`.

   * `/boot/grub/menu.lst`Om den saknade filen är är det här felet för äldre OS-versioner (**RHEL 6. x**, **CentOS 6. x** och **Ubuntu 14,04**) så att kommandona kan skjuta upp. Skapa en gammal Server och testa den för att se till att rätt kommandon tillhandahålls.

## <a name="next-steps"></a>Nästa steg

* [Översikt över Azure Virtual Machine agent](../extensions/agent-windows.md)
* [Tillägg och funktioner för virtuella datorer för Windows](../extensions/features-windows.md)

