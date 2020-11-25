---
title: Azures serie konsol för SysRq-och NMI-anrop | Microsoft Docs
description: Använda en serie konsol för SysRq-och NMI-anrop i Azure Virtual Machines.
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/14/2018
ms.author: alsin
ms.openlocfilehash: 545399e1d7941351ce861ac98d995d5e57006ea1
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96022861"
---
# <a name="use-the-azure-serial-console-for-sysrq-and-nmi-calls"></a>Använda Azures serie konsol för SysRq-och NMI-anrop

## <a name="system-request-sysrq"></a>System förfrågan (SysRq)
En SysRq är en sekvens med nycklar som förstås av Linux-operativ systemets kernel, som kan utlösa en uppsättning fördefinierade åtgärder. Dessa kommandon används ofta när fel sökning eller återställning av virtuella datorer inte kan utföras via traditionell administration (till exempel om den virtuella datorn inte svarar). Genom att använda funktionen SysRq i Azures serie konsol kommer du att efterlikna hur du trycker på den SysRq nyckeln och de tecken som anges på ett fysiskt tangent bord.

När SysRq-sekvensen har levererats styr kernel-konfigurationen hur systemet svarar. Information om hur du aktiverar och inaktiverar SysRq finns i *SysRq admin guide* [text](https://aka.ms/kernelorgsysreqdoc)  |  [markdown](https://aka.ms/linuxsysrq).

Du kan använda Azures serie konsol för att skicka en SysRq till en virtuell Azure-dator med hjälp av tangent bords ikonen i kommando fältet som visas nedan.

![Skärm bild av Azures serie konsol. Tangent bords ikonen är markerad och dess meny är synlig. Menyn innehåller ett kommando objekt för att skicka SysRq.](../media/virtual-machines-serial-console/virtual-machine-serial-console-command-menu.jpg)

Om du väljer "skicka SysRq kommando" öppnas en dialog ruta som tillhandahåller vanliga alternativ för SysRq eller accepterar en sekvens med SysRq-kommandon som anges i dialog rutan.  Detta gör att serier med SysRq kan utföra en hög nivå åtgärd, till exempel en säker omstart med hjälp av: `REISUB` .

![Skärm bild av dialog rutan Skicka SysRq kommando till gäst. Alternativet för att ange kommandon är markerat och kommando rutan innehåller REISUB.](../media/virtual-machines-serial-console/virtual-machine-serial-console-sysreq_UI.png)

Det går inte att använda kommandot SysRq på virtuella datorer som har stoppats eller vars kernel inte är i ett tillstånd som inte svarar. (till exempel en kernel-panik).

### <a name="enable-sysrq"></a>Aktivera SysRq
Som beskrivs i *Administratörs hand boken för SysRq* ovan kan SysRq konfigureras så att alla, ingen eller endast vissa kommandon är tillgängliga. Du kan aktivera alla SysRq-kommandon med hjälp av steget nedan, men det kommer inte att överleva en omstart:
```
echo "1" >/proc/sys/kernel/sysrq
```
Om du vill göra SysReq-konfigurationen beständig kan du göra följande för att aktivera alla SysRq-kommandon
1. Lägger till den här raden till */etc/sysctl.conf* <br>
    `kernel.sysrq = 1`
1. Starta om eller uppdatera sysctl genom att köra <br>
    `sysctl -p`

### <a name="command-keys"></a>Kommando nycklar
I administratörs hand boken för SysRq ovan:

|Kommando| Funktion
| ------| ----------- |
|``b``  |   Startar om systemet direkt utan att synkronisera eller demontera diskarna.
|``c``  |   Utför en system krasch med en NULL-pekare-referens. En crashdump görs om den har kon figurer ATS.
|``d``  |   Visar alla lås som är lagrade.
|``e``  |   Skicka en SIGTERM till alla processer, förutom init.
|``f``  |   Anropar OOM-Killer för att avsluta en minnes HOG process, men panik inte om inget kan avlivas.
|``g``  |   Används av kgdb (kernel-felsökning)
|``h``  |   Visar hjälp (någon annan nyckel än de som anges här visas också hjälp, men ``h`` är lätt att komma ihåg:-)
|``i``  |    Skicka en SIGKILL till alla processer, förutom init.
|``j``  |    Tvinga fram "Tina bara det"-fil system som frysts av FIFREEZE IOCTL.
|``k``  |    Säker åtkomst nyckel (SAK) omsorg alla program i den aktuella virtuella konsolen. Obs! se viktiga kommentarer nedan i avsnittet SAK.
|``l``  |    Visar en stack-och bakklarning för alla aktiva processorer.
|``m``  |    Dumpar aktuell minnes information till konsolen.
|``n``  |    Används för att göra RT-uppgifter snyggt
|``o``  |    Stänger av systemet (om det är konfigurerat och stöds).
|``p``  |    Kommer att dumpa de aktuella registren och flaggorna till konsolen.
|``q``  |    Dumpar per CPU-lista över alla väpnade hrtimers (men inte vanliga timer_list timers) och detaljerad information om alla clockevent-enheter.
|``r``  |    Stänger av Keyboard RAW-läge och anger det till XLATE.
|``s``  |    Kommer att försöka synkronisera alla monterade fil system.
|``t``  |    Dumpar en lista över aktuella aktiviteter och deras information till-konsolen.
|``u``  |    Försöker att montera om alla monterade fil system i skrivskyddat läge.
|``v``  |    Framtvinga återställning av framebuffer-konsolen
|``v``  |    Orsakar ETM buffer-dump [ARM-Specific]
|``w``  |    Dumpar aktiviteter som är i ett avbrotts läge (blockerat).
|``x``  |    Används av xmon-gränssnittet på PPC/powerpc-plattformar. Visa globala PMU-register på sparc64. Dumpa alla TLB-poster på MIPS.
|``y``  |    Visa globala processor register [SPARC-64-/regionsspecifika]
|``z``  |    Dumpa ftrace-bufferten
|``0``-``9`` | Anger logg nivån för konsolen och styr vilka kernel-meddelanden som ska skrivas ut till konsolen. (till ``0`` exempel göra det så att endast nödfalls meddelanden som Panic eller hoppsans skulle göra det till konsolen.)

### <a name="distribution-specific-documentation"></a>Distribution – detaljerad dokumentation ###
Information om distributions information om SysRq och hur du konfigurerar Linux för att skapa en kraschdump när den får ett SysRq "krasch"-kommando finns i länkarna nedan:

#### <a name="ubuntu"></a>Ubuntu ####
 - [Kernel-krasch dump](https://help.ubuntu.com/lts/serverguide/kernel-crash-dump.html)

#### <a name="red-hat"></a>Red Hat ####
- [Vad är SysRq-funktionen och hur använder jag den?](https://access.redhat.com/articles/231663)
- [Använda SysRq-funktionen för att samla in information från en RHEL-Server](https://access.redhat.com/solutions/2023)

#### <a name="suse"></a>SUSE ####
- [Konfigurera kernel Core dump-avbildning](https://www.suse.com/support/kb/doc/?id=3374462)

#### <a name="coreos"></a>CoreOS ####
- [Samla in krasch loggar](https://coreos.com/os/docs/latest/collecting-crash-logs.html)

## <a name="non-maskable-interrupt-nmi"></a>Icke-Maskbart avbrott (NMI)
Ett icke-maskbart avbrott (NMI) är utformat för att skapa en signal som program varan på en virtuell dator inte kommer att ignorera. Historiskt sett har NMIs använts för att övervaka maskin varu problem på system som krävde specifika svars tider.  Idag använder programmerare programmerare och system administratörer ofta NMI som en mekanism för att felsöka eller felsöka system som inte svarar.

Du kan använda serie konsolen för att skicka en NMI till en virtuell Azure-dator med hjälp av tangent bords ikonen i kommando fältet som visas nedan. När NMI har levererats kontrollerar konfigurationen för den virtuella datorn hur systemet svarar.  Linux-operativsystem kan konfigureras för att krascha och skapa en minnesdump operativ systemet tar emot en NMI.

![Skärm bild av serie konsolen. Tangent bords ikonen är markerad och dess meny är synlig. Menyn innehåller ett avbrotts objekt som inte går att maskera.](../media/virtual-machines-serial-console/virtual-machine-serial-console-command-menu.jpg) <br>

### <a name="enable-nmi"></a>Aktivera NMI
För Linux-system som stöder sysctl för att konfigurera kernel-parametrar kan du aktivera en panik när du tar emot den här NMI genom att använda följande:
1. Lägger till den här raden till */etc/sysctl.conf* <br>
    `kernel.panic_on_unrecovered_nmi=1`
1. Starta om eller uppdatera sysctl genom att köra <br>
    `sysctl -p`

Mer information om konfigurationer i Linux-kernel, inklusive `unknown_nmi_panic` , `panic_on_io_nmi` och `panic_on_unrecovered_nmi` , finns i: [dokumentation för/proc/sys/kernel/*](https://www.kernel.org/doc/Documentation/sysctl/kernel.txt). Information om distributions information om NMI och hur du konfigurerar Linux för att skapa en kraschdump när den får en NMI finns i länkarna nedan:

### <a name="ubuntu"></a>Ubuntu
 - [Kernel-krasch dump](https://help.ubuntu.com/lts/serverguide/kernel-crash-dump.html)

### <a name="red-hat"></a>Red Hat
 - [Vad är en NMI och vad kan jag använda det till?](https://access.redhat.com/solutions/4127)
 - [Hur konfigurerar jag mitt system att krascha när NMI-växeln pushas?](https://access.redhat.com/solutions/125103)
 - [Administratörs guide för krasch dumpning](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/pdf/kernel_crash_dump_guide/kernel-crash-dump-guide.pdf)

### <a name="suse"></a>SUSE
- [Konfigurera kernel Core dump-avbildning](https://www.suse.com/support/kb/doc/?id=3374462)

### <a name="coreos"></a>CoreOS
- [Samla in krasch loggar](https://coreos.com/os/docs/latest/collecting-crash-logs.html)

## <a name="next-steps"></a>Nästa steg
* Sidan med information om Linux-dokumentation för den viktigaste konsolen finns [här](serial-console-linux.md).
* Använd en serie konsol för att starta i [grub och ange enanvändarläge](serial-console-grub-single-user-mode.md)
* Serie konsolen är också tillgänglig för virtuella [Windows](serial-console-windows.md) -datorer
* Läs mer om [startdiagnostik](boot-diagnostics.md)