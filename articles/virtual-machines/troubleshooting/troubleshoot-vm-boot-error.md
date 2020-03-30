---
title: Linux VM startar till Grub Rescue
description: Virtuell dator kunde inte starta eftersom den virtuella datorn gick in i en räddningskonsol
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
ms.openlocfilehash: c24a840716841d04537ac5b77bcaf26fca4b78cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77561957"
---
# <a name="linux-vm-boots-to-grub-rescue"></a>Linux VM startar till Grub Rescue

Vi har identifierat att din virtuella dator (VM) gick in i en räddningskonsol. Problemet uppstår när din Linux-vm hade kärnändringar tillämpas nyligen, till exempel en kärnuppgradering, och inte längre startar upp ordentligt på grund av kärnfel under startprocessen. Under startprocessen, när starthanteraren försöker hitta Linux-kärnan och lämna av startkontrollen till den, går den virtuella datorn in i en räddningskonsol när handoffen misslyckas.

Om du upptäcker att du inte kan ansluta till en virtuell dator i framtiden kan du visa en skärmbild av din virtuella dator med hjälp av bladet boot diagnostics i Azure-portalen. Det här kan hjälpa dig att diagnostisera problemet och fastställa om ett liknande startfel är orsaken.

## <a name="recommended-steps"></a>Rekommenderade åtgärder

Följ begränsningsstegen nedan beroende på felet du får:

### <a name="error---unknown-filesystem"></a>Fel - Okänt filsystem

* Om du får felet **Okänt filsystem**kan det här felet bero på att ett filsystem är fel på startpartitionen eller en felaktig kärnkonfiguration.

   * För filsystemproblem följer du stegen i artikeln [Linux Recovery: Kan inte SSH till Linux VM på grund av filsystemfel (fsck, inodes)](https://blogs.msdn.microsoft.com/linuxonazure/2016/09/13/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck-inodes/).
   * Om du vill ha problem med kärnan följer du stegen i artikeln [Linux Recovery: Manuellt åtgärda problem som inte är startrelaterade till kernelproblem](https://blogs.msdn.microsoft.com/linuxonazure/2016/10/09/linux-recovery-manually-fixing-non-boot-issues-related-to-kernel-problems/)eller [Linux-återställning: Åtgärda problem som inte är startrelaterade till kernelproblem med chroot](https://blogs.msdn.microsoft.com/linuxonazure/2016/10/09/linux-recovery-fixing-non-boot-issues-related-to-kernel-problems-using-chroot/).
   
### <a name="error---file-not-found"></a>Fel - Filen hittades inte

* Om du får felet **Fel 15: Filen hittades inte eller inledande RAM-disk** eller **initrd/initramfs-fil hittades**följer du stegen nedan.

    * För den `/boot/grub2/grub.cfg` saknade `initrd/initramfs` filen eller fortsätt med följande process:

    1. Se `/etc/default/grub` till att det finns och har rätt/önskade inställningar. Om du inte vet vilka standardinställningarna är kan du kontrollera med en fungerande virtuell dator.

    2. Kör sedan följande kommando för att återskapa konfigurationen:`grub2-mkconfig -o /boot/grub2/grub.cfg`

   * Om den saknade `/boot/grub/menu.lst`filen är , är detta fel för äldre OS-versioner (**RHEL 6.x**, **Centos 6.x** och **Ubuntu 14.04**) så kommandona kan skilja sig åt. Du måste snurra upp en gammal server och testa för att säkerställa att rätt kommandon tillhandahålls.

### <a name="error---no-such-partition"></a>Fel - Ingen sådan partition

* Om du får felet **Ingen sådan partition,** se [ärendescenario: "ingen sådan partition" fel när du försöker starta den virtuella datorn efter att ha försökt att utöka OS-enheten](https://blogs.technet.microsoft.com/shwetanayak/2017/03/12/case-scenario-no-such-partition-error-while-trying-to-start-the-vm-after-attempting-to-extend-the-os-drive/).

### <a name="error---grubcfg-file-not-found"></a>Fel - grub.cfg-filen hittades inte

* Om du får felet **/boot/grub2/grub.cfg filen hittades,** följ stegen nedan.

    * För den `/boot/grub2/grub.cfg` saknade `initrd/initramfs` filen eller fortsätt med följande process:

    1. Se `/etc/default/grub` till att det finns och har rätt/önskade inställningar. Om du inte vet vilka standardinställningarna är kan du kontrollera med en fungerande virtuell dator.

    2. Kör sedan följande kommando för att `grub2-mkconfig -o /boot/grub2/grub.cfg`återskapa dess konfiguration: .

   * Om den saknade `/boot/grub/menu.lst`filen är , är detta fel för äldre OS-versioner (**RHEL 6.x**, **Centos 6.x** och **Ubuntu 14.04**) så kommandona kan skjuta upp. Snurra upp en gammal server och testa den för att säkerställa att rätt kommandon tillhandahålls.

## <a name="next-steps"></a>Nästa steg

* [Översikt över Azure Virtual Machine Agent](../extensions/agent-windows.md)
* [Tillägg och funktioner för virtuella datorer för Windows](../extensions/features-windows.md)

