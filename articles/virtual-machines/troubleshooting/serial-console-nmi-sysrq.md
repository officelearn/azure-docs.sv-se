---
title: Azure Serial Console för SysRq- och NMI-samtal | Microsoft-dokument
description: Använda Serial Console för SysRq- och NMI-anrop i virtuella Azure-datorer.
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
ms.openlocfilehash: 3ad68438f5fc015b6a9150d67485b90a095f1a4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250093"
---
# <a name="use-serial-console-for-sysrq-and-nmi-calls"></a>Använda seriekonsolen för SysRq- och NMI-anrop

## <a name="system-request-sysrq"></a>Systembegäran (SysRq)
En SysRq är en sekvens av nycklar som förstås av Linux-operativsystemkärnan, som kan utlösa en uppsättning fördefinierade åtgärder. Dessa kommandon används ofta när felsökning eller återställning av virtuella datorer inte kan utföras via traditionell administration (till exempel om den virtuella datorn inte svarar). Om du använder SysRq-funktionen i Azure Serial Console kan du efterlikna att trycka på SysRq-tangenten och tecken som anges på ett fysiskt tangentbord.

När SysRq-sekvensen har levererats styr kärnkonfigurationen hur systemet svarar. Information om hur du aktiverar och inaktiverar SysRq finns i | [textmarkdown](https://aka.ms/linuxsysrq)för *SysRq Admin Guide* [text](https://aka.ms/kernelorgsysreqdoc).

Azure Serial Console kan användas för att skicka en SysRq till en virtuell Azure-dator med hjälp av tangentbordsikonen i kommandofältet nedan.

![](../media/virtual-machines-serial-console/virtual-machine-serial-console-command-menu.jpg)

Om du väljer "Skicka SysRq Command" öppnas en dialogruta som ger gemensamma SysRq-alternativ eller accepterar en sekvens av SysRq-kommandon som anges i dialogrutan.  Detta gör det möjligt för serien SysRq's att utföra en `REISUB`hög nivå operation såsom en säker omstart med: .

![](../media/virtual-machines-serial-console/virtual-machine-serial-console-sysreq_UI.png)

SysRq-kommandot kan inte användas på virtuella datorer som stoppas eller vars kärna är i ett icke-responsivt tillstånd. (till exempel en kärna panik).

### <a name="enable-sysrq"></a>Aktivera SysRq
Som beskrivs i *SysRq Admin Guide* ovan kan SysRq konfigureras så att alla, inga eller bara vissa kommandon är tillgängliga. Du kan aktivera alla SysRq-kommandon med steget nedan, men det kommer inte att överleva en omstart:
```
echo "1" >/proc/sys/kernel/sysrq
```
Om du vill göra SysReq-konfigurationen beständig kan du göra följande för att aktivera alla SysRq-kommandon
1. Lägga till denna rad till */etc/sysctl.conf* <br>
    `kernel.sysrq = 1`
1. Starta om eller uppdatera sysctl genom att köra <br>
    `sysctl -p`

### <a name="command-keys"></a>Kommandonycklar
Från SysRq Admin Guide ovan:

|Kommando| Funktion
| ------| ----------- |
|``b``  |   Kommer omedelbart att starta om systemet utan att synkronisera eller avmontera dina diskar.
|``c``  |   Kommer att utföra en systemkrasch med en NULL-pekare avreference. En crashdump tas om den konfigureras.
|``d``  |   Visar alla lås som hålls.
|``e``  |   Skicka en SIGTERM till alla processer, utom init.
|``f``  |   Kommer att ringa oom mördaren att döda ett minne svin process, men inte panik om ingenting kan dödas.
|``g``  |   Används av kgdb (kernel debugger)
|``h``  |   Kommer att visa hjälp (någon annan nyckel än de ``h`` som anges här kommer också att visa hjälp, men är lätt att komma ihåg :-)
|``i``  |    Skicka en SIGKILL till alla processer, utom init.
|``j``  |    Med våld "Bara tina upp det" - filsystem fryst av FIFREEZE ioctl.
|``k``  |    Säker åtkomstnyckel (SAK) Dödar alla program på den aktuella virtuella konsolen. OBS: Se viktiga kommentarer nedan i SAK avsnitt.
|``l``  |    Visar en stackbacktrace för alla aktiva processorer.
|``m``  |    Kommer att dumpa aktuell minnesinformation till konsolen.
|``n``  |    Används för att göra RT-uppgifter trevliga
|``o``  |    Kommer att stänga av ditt system (om konfigurerat och stöds).
|``p``  |    Kommer att dumpa de aktuella registren och flaggorna till konsolen.
|``q``  |    Kommer att dumpa per CPU-listor över alla beväpnade hrtimers (men inte vanliga timer_list timers) och detaljerad information om alla clockevent enheter.
|``r``  |    Stänger av tangentbordsråläge och ställer in det på XLATE.
|``s``  |    Kommer att försöka synkronisera alla monterade filsystem.
|``t``  |    Kommer att dumpa en lista över aktuella uppgifter och deras information till konsolen.
|``u``  |    Kommer att försöka montera alla monterade filsystem skrivskyddade.
|``v``  |    Återställer rambuffertkonsolen med kraft
|``v``  |    Orsakar ETM-buffertdump [ARM-specifik]
|``w``  |    Dumpar aktiviteter som är i avbrottsfri (blockerad) tillstånd.
|``x``  |    Används av xmon gränssnitt på ppc / powerpc plattformar. Visa globala PMU-register på sparc64. Dumpa alla TLB-poster på MIPS.
|``y``  |    Visa globala CPU-register [SPARC-64 specifik]
|``z``  |    Dumpa ftrace-bufferten
|``0``-``9`` | Ställer in konsolloggnivån och styr vilka kärnmeddelanden som ska skrivas ut på konsolen. (,``0``till exempel skulle göra det så att endast nödmeddelanden som PANIC eller OOPSes skulle göra det till din konsol.)

### <a name="distribution-specific-documentation"></a>Distributionsspecifik dokumentation ###
För distributionsspecifik dokumentation om SysRq och steg för att konfigurera Linux för att skapa en kraschdump när den tar emot ett SysRq "Crash"-kommando finns i länkarna nedan:

#### <a name="ubuntu"></a>Ubuntu ####
 - [Kärnkraschdump](https://help.ubuntu.com/lts/serverguide/kernel-crash-dump.html)

#### <a name="red-hat"></a>Red Hat ####
- [Vad är SysRq-anläggningen och hur använder jag den?](https://access.redhat.com/articles/231663)
- [Så här använder du SysRq-funktionen för att samla in information från en RHEL-server](https://access.redhat.com/solutions/2023)

#### <a name="suse"></a>SUSE ####
- [Konfigurera insamling av kärnkärna dump](https://www.suse.com/support/kb/doc/?id=3374462)

#### <a name="coreos"></a>CoreOS ####
- [Samla kraschloggar](https://coreos.com/os/docs/latest/collecting-crash-logs.html)

## <a name="non-maskable-interrupt-nmi"></a>Icke-maskerbart avbrott (NMI)
Ett icke-maskerbart avbrott (NMI) är utformat för att skapa en signal som programvara på en virtuell dator inte ignorerar. Historiskt sett har NMIs använts för att övervaka maskinvaruproblem på system som krävde specifika svarstider.  Idag använder programmerare och systemadministratörer ofta NMI som en mekanism för att felsöka eller felsöka system som inte svarar.

Seriekonsolen kan användas för att skicka en NMI till en virtuell Azure-dator med hjälp av tangentbordsikonen i kommandofältet nedan. När NMI har levererats styr konfigurationen för den virtuella datorn hur systemet svarar.  Linux-operativsystem kan konfigureras för att krascha och skapa en minnesdump operativsystemet tar emot en NMI.

![](../media/virtual-machines-serial-console/virtual-machine-serial-console-command-menu.jpg) <br>

### <a name="enable-nmi"></a>Aktivera NMI
För Linux-system som stöder sysctl för att konfigurera kärnparametrar kan du aktivera panik när du tar emot denna NMI med hjälp av följande:
1. Lägga till denna rad till */etc/sysctl.conf* <br>
    `kernel.panic_on_unrecovered_nmi=1`
1. Starta om eller uppdatera sysctl genom att köra <br>
    `sysctl -p`

Mer information om Linux-kärnkonfigurationer, inklusive `unknown_nmi_panic` `panic_on_io_nmi`, och `panic_on_unrecovered_nmi`finns i: Dokumentation för [/proc/sys/kernel/*](https://www.kernel.org/doc/Documentation/sysctl/kernel.txt). För distributionsspecifik dokumentation om NMI och steg för att konfigurera Linux för att skapa en kraschdump när den tar emot en NMI, se länkarna nedan:

### <a name="ubuntu"></a>Ubuntu
 - [Kärnkraschdump](https://help.ubuntu.com/lts/serverguide/kernel-crash-dump.html)

### <a name="red-hat"></a>Red Hat
 - [Vad är en NMI och vad kan jag använda den till?](https://access.redhat.com/solutions/4127)
 - [Hur kan jag konfigurera mitt system så att det kraschar när NMI-växeln trycks in?](https://access.redhat.com/solutions/125103)
 - [Adminguide för kraschdump](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/pdf/kernel_crash_dump_guide/kernel-crash-dump-guide.pdf)

### <a name="suse"></a>SUSE
- [Konfigurera insamling av kärnkärna dump](https://www.suse.com/support/kb/doc/?id=3374462)

### <a name="coreos"></a>CoreOS
- [Samla kraschloggar](https://coreos.com/os/docs/latest/collecting-crash-logs.html)

## <a name="next-steps"></a>Nästa steg
* Den huvudsakliga Serial Console Linux dokumentationssidan finns [här](serial-console-linux.md).
* Använd Serial Console för att starta i [GRUB och gå in i enanvändarläge](serial-console-grub-single-user-mode.md)
* Seriekonsolen är också tillgänglig för [virtuella Windows-datorer](serial-console-windows.md)
* Läs mer om [startdiagnostik](boot-diagnostics.md)