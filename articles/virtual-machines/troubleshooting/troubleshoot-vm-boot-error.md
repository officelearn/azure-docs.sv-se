---
title: Virtuella Linux-datorer startar till grub räddning
description: Det gick inte att starta den virtuella datorn eftersom den virtuella datorn angav en räddnings konsol
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 08/28/2019
ms.author: tiag
ms.openlocfilehash: 7774ea5138e6d77547e386531e573457cd6d2862
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86525950"
---
# <a name="linux-vm-boots-to-grub-rescue"></a>Virtuella Linux-datorer startar till grub räddning

Vi har identifierat att din virtuella dator (VM) angav en räddnings konsol. Problemet uppstår när din virtuella Linux-dator hade kernel-ändringar som nyligen lagts till som en kernel-uppgradering och inte längre startar på rätt sätt på grund av kernel-fel under start processen. När Start inläsaren försöker hitta Linux-kärnan under start processen och den lämnar start kontrollen till den, kommer den virtuella datorn att gå in i en räddnings konsol när leveransen Miss lyckas.

Om du upptäcker att du inte kan ansluta till en virtuell dator i framtiden kan du Visa en skärm bild av den virtuella datorn med hjälp av startdiagnostik-bladet i Azure Portal. Det här kan hjälpa dig att diagnostisera problemet och fastställa om ett liknande startfel är orsaken.

## <a name="recommended-steps"></a>Rekommenderade åtgärder

Följ stegen nedan beroende på vilket fel du får:

### <a name="error---unknown-filesystem"></a>Fel-okänt fil system

* Om du får felet **Okänt fil**system kan det här felet bero på ett skadat fil system på startpartitionen eller en felaktig kernel-konfiguration.

   * För fil Systems problem följer du stegen i artikeln Linux- [återställning: det går inte att använda SSH till Linux-VM på grund av fil system fel (fsck, noder i procent)](/archive/blogs/linuxonazure/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck-inodes).
   * För kernel-problem följer du stegen i artikeln [Linux-återställning: korrigera icke-startproblem som rör kernel-problem](/archive/blogs/linuxonazure/linux-recovery-manually-fixing-non-boot-issues-related-to-kernel-problems)eller Linux- [återställning: åtgärda icke-startproblem som rör kernel-problem med chroot](/archive/blogs/linuxonazure/linux-recovery-fixing-non-boot-issues-related-to-kernel-problems-using-chroot).
   
### <a name="error---file-not-found"></a>Fel-filen hittades inte

* Om du får fel **meddelandet 15: filen hittades inte eller** så går det inte att hitta den första RAM-disken eller så går det **inte att hitta initrd/initramfs-filen**.

    * För den saknade filen `/boot/grub2/grub.cfg` eller `initrd/initramfs` Fortsätt med följande process:

    1. Se till att de `/etc/default/grub` finns och har rätt/önskade inställningar. Om du inte vet vilka inställningar som är standardinställningar kan du kontrol lera med en fungerande virtuell dator.

    2. Kör sedan följande kommando för att återskapa konfigurationen:`grub2-mkconfig -o /boot/grub2/grub.cfg`

   * Om filen saknas är `/boot/grub/menu.lst` det här felet för äldre OS-versioner (**RHEL 6. x**, **CentOS 6. x** och **Ubuntu 14,04**) så att kommandona kan skilja sig. Du måste sätta igång en gammal Server och testa för att se till att rätt kommandon tillhandahålls.

### <a name="error---no-such-partition"></a>Fel-ingen sådan partition

* Om du får ett fel meddelande om att det **inte finns någon sådan partition**, se till [fall scenario: "ingen sådan partition"-fel vid försök att starta den virtuella datorn efter försök att utöka operativ system enheten](/archive/blogs/shwetanayak/case-scenario-no-such-partition-error-while-trying-to-start-the-vm-after-attempting-to-extend-the-os-drive).

### <a name="error---grubcfg-file-not-found"></a>Fel-grub. cfg-filen hittades inte

* Om du får fel meddelandet Det gick **inte att hitta/Boot/grub2/grub.cfg**följer du stegen nedan.

    * För den saknade filen `/boot/grub2/grub.cfg` eller `initrd/initramfs` Fortsätt med följande process:

    1. Se till att de `/etc/default/grub` finns och har rätt/önskade inställningar. Om du inte vet vilka inställningar som är standardinställningar kan du kontrol lera med en fungerande virtuell dator.

    2. Kör sedan följande kommando för att återskapa konfigurationen: `grub2-mkconfig -o /boot/grub2/grub.cfg` .

   * Om den saknade filen är är `/boot/grub/menu.lst` det här felet för äldre OS-versioner (**RHEL 6. x**, **CentOS 6. x** och **Ubuntu 14,04**) så att kommandona kan skjuta upp. Skapa en gammal Server och testa den för att se till att rätt kommandon tillhandahålls.

## <a name="next-steps"></a>Nästa steg

* [Översikt över Azure Virtual Machine agent](../extensions/agent-windows.md)
* [Tillägg och funktioner för virtuella datorer för Windows](../extensions/features-windows.md)
