---
title: Azure Seriekonsol för SysRq och NMI samtal | Microsoft Docs
description: Använda Seriekonsol för för SysRq och NMI anropar i Azure virtual machines.
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/14/2018
ms.author: alsin
ms.openlocfilehash: 7129525f4d33648caa6c9e4594b0a0489254418a
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2018
ms.locfileid: "44379825"
---
# <a name="use-serial-console-for-sysrq-and-nmi-calls"></a>Använd Seriekonsol för SysRq och NMI anrop

## <a name="system-request-sysrq"></a>Systembegäran (SysRq)
En SysRq är en sekvens av nycklar som tolkas av Linux-åtgärden system kerneln, som kan utlösa en uppsättning fördefinierade åtgärder. Dessa kommandon används ofta vid felsökning av virtuell dator eller återställning inte kan utföras via traditionella administration (till exempel om den virtuella datorn låser sig). Med hjälp av Azure Seriekonsol funktionen SysRq efterliknar trycker på SysRq-nyckeln och tecken som anges på ett fysiskt tangentbord.

När de SysRq levereras, ska kernel-konfigurationen styra hur systemet svarar. Information om aktivering och inaktivering av SysRq finns i den *SysRq Adminhandbok* [text](https://aka.ms/kernelorgsysreqdoc) | [markdown](https://aka.ms/linuxsysrq).  

Azure Seriekonsol kan användas för att skicka en SysRq till en Azure virtuell dator med hjälp av tangentbordsikonen i kommandofältet visas nedan.

![](../media/virtual-machines-serial-console/virtual-machine-serial-console-command-menu.jpg)

När du väljer ”Skicka SysRq kommandot” öppnas en dialogruta som ska innehålla vanliga SysRq alternativ eller acceptera en sekvens med SysRq kommandon som anges i dialogrutan.  Detta gör att för serie SysRq: er för att utföra en avancerad åtgärd, till exempel en säker omstart med hjälp av: `REISUB`.

![](../media/virtual-machines-serial-console/virtual-machine-serial-console-sysreq_UI.png)

Kommandot SysRq kan inte användas på virtuella datorer som har stoppats eller vars kerneln är i ett tillstånd som inte svarar. (till exempel en kernel panic-meddelande).

### <a name="enable-sysrq"></a>Aktivera SysRq 
Enligt beskrivningen i den *SysRq administratörshandboken* ovan, SysRq kan konfigureras så att alla, none eller bara vissa kommandon är tillgängliga. Du kan aktivera alla SysRq kommandon med hjälp av steg nedan, men det kommer inte att överleva en omstart:
```
echo "1" >/proc/sys/kernel/sysrq
```
Om du vill göra SysReq konfigurationen beständiga, kan du göra följande för att aktivera alla SysRq kommandon
1. Att lägga till den här raden till */etc/sysctl.conf* <br>
    `kernel.sysrq = 1`
1. Starta om eller uppdatera sysctl genom att köra <br>
    `sysctl -p`

### <a name="command-keys"></a>Kommandot nycklar 
Administrationshandboken för SysRq ovan:

|Kommando| Funktion
| ------| ----------- |
|``b``  |   Kommer omedelbart att starta om systemet utan synkronisering eller demontera diskarna.
|``c``  |   Utför en systemkrasch genom en NULL-pekare avreferera. En crashdump ska vidtas om konfigurerat.
|``d``  |   Visar alla lås som hålls kvar.
|``e``  |   Skicka en sigterm-signal till alla processer, förutom init.
|``f``  |   Anropar ooma borttagare för att avsluta en process för minne hog, men få inte panik om ingenting kan avslutas.
|``g``  |   Används av kgdb (kernel-felsökare)
|``h``  |   Visar hjälp (andra nyckel än de som anges här visas också hjälp, men ``h`` är lätt att komma ihåg :-)
|``i``  |    Skicka en SIGKILL till alla processer, förutom init.
|``j``  |    Forcerar ”bara tina det” - filsystem som är låsta av FIFREEZE ioctl.
|``k``  |    Säker åtkomst till nyckeln (SAK) stoppar alla program på den aktuella virtuella konsolen. Obs: Se viktiga kommentarer nedan under SAK.
|``l``  |    Visar en stack backtrace för alla aktiva processorer.
|``m``  |    Kommer dumpa aktuella minne information till konsolen.
|``n``  |    Används för att göra RT uppgifter bra kan
|``o``  |    Stängs av systemet (om konfigurerad och stöds).
|``p``  |    Kommer dumpa aktuella register och flaggor till konsolen.
|``q``  |    Kommer dumpa per CPU-listor över alla väpnade hrtimers (men inte vanlig timer_list timers) och detaljerad information om alla clockevent enheter.
|``r``  |    Inaktiverar tangentbord raw läge och ställer in den på XLATE.
|``s``  |    Försöker att synkronisera alla monterade filsystem.
|``t``  |    Kommer dumpa en lista över aktuella uppgifter och deras information till konsolen.
|``u``  |    Försöker montera om alla monterade filsystem skrivskyddad.
|``v``  |    Kernelpaketet återställer framebuffer-konsolen
|``v``  |    Orsaker ETM buffert dump [ARM-specifika]
|``w``  |    Dumpar uppgifter som är i tillståndet för avbrottsfri (blockerade).
|``x``  |    Används av xmon-gränssnittet på ppc/powerpc plattformar. Visa global PMU registrerar på sparc64. Dumpa alla TLB poster på MIPS.
|``y``  |    Visa globala CPU registrerar [SPARC-64-specifika]
|``z``  |    Dumpa ftrace bufferten
|``0``-``9`` | Anger loggningsnivån konsolen styra vilka kernelmeddelanden skrivs till konsolen. (``0``för exemplet gör det så att endast vid akutfall meddelanden som panic eller OOPSes gör det till din konsol.)

### <a name="distribution-specific-documentation"></a>Distribution-specifika dokumentation ###
Distribution-specifika dokumentation på SysRq och stegen för att konfigurera Linux att skapa en kraschdumpfil när den får en SysRq ”krascha” kommandot finns i länkarna nedan:

#### <a name="ubuntu"></a>Ubuntu ####
 - [Kernel-kraschdump](https://help.ubuntu.com/lts/serverguide/kernel-crash-dump.html)

#### <a name="red-hat"></a>Red Hat ####
- [Vad är funktionen SysRq och hur använder jag det?](https://access.redhat.com/articles/231663)
- [Hur du använder funktionen SysRq att samla in information från en RHEL-server](https://access.redhat.com/solutions/2023)

#### <a name="suse"></a>SUSE ####
- [Konfigurera kernel core dump avbilda](https://www.suse.com/support/kb/doc/?id=3374462)

#### <a name="coreos"></a>CoreOS ####
- [Samla in loggar för krascher](https://coreos.com/os/docs/latest/collecting-crash-logs.html)

## <a name="non-maskable-interrupt-nmi"></a>Icke-Maskable Interrupt (NMI) 
Ett icke-maskable avbrott (NMI) är utformad för att skapa en signal som inte kommer att ignorera programvara på en virtuell dator. Historiskt sett använts NMIs för att övervaka maskinvarufel på system som krävs för specifika svarstider.  Idag, programmerare och systemadministratörer använder ofta NMI som en mekanism för att felsöka eller felsöka system som har hängt.

Seriekonsol kan användas för att skicka en NMI till en Azure virtuell dator med hjälp av tangentbordsikonen i kommandofältet visas nedan. När NMI levereras, ska konfigurationen av virtuella datorn styra hur systemet svarar.  Linux-operativsystem kan konfigureras kraschar och skapa en minnesdump operativsystemet tar emot en NMI.

![](../media/virtual-machines-serial-console/virtual-machine-serial-console-command-menu.jpg) <br>

Linux-system som stöder sysctl för att konfigurera kernel parametrar, kan du aktivera en panik när du tar emot den här NMI med hjälp av följande:
1. Att lägga till den här raden till */etc/sysctl.conf* <br>
    `kernel.panic_on_unrecovered_nmi=1`
1. Starta om eller uppdatera sysctl genom att köra <br>
    `sysctl -p`

Mer information om Linux-kernel inställningar, till exempel `unknown_nmi_panic`, `panic_on_io_nmi`, och `panic_on_unrecovered_nmi`, se: [dokumentationen för/proc/sys/kernel / *](https://www.kernel.org/doc/Documentation/sysctl/kernel.txt). Distribution-specifika dokumentation på NMI och stegen för att konfigurera Linux att skapa en kraschdumpfil när den får en NMI finns i länkarna nedan:
 
### <a name="ubuntu"></a>Ubuntu 
 - [Kernel-kraschdump](https://help.ubuntu.com/lts/serverguide/kernel-crash-dump.html)

### <a name="red-hat"></a>Red Hat 
 - [Vad är en NMI och vad kan jag använda det för?](https://access.redhat.com/solutions/4127)
 - [Hur konfigurerar jag mitt system kraschar när NMI växel skickas?](https://access.redhat.com/solutions/125103)
 - [Krascha dumpa administratörshandboken](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/pdf/kernel_crash_dump_guide/kernel-crash-dump-guide.pdf)

### <a name="suse"></a>SUSE 
- [Konfigurera kernel core dump avbilda](https://www.suse.com/support/kb/doc/?id=3374462)

### <a name="coreos"></a>CoreOS 
- [Samla in loggar för krascher](https://coreos.com/os/docs/latest/collecting-crash-logs.html)

## <a name="next-steps"></a>Nästa steg
* Huvudsidan för seriella konsolen Linux-dokumentation finns [här](serial-console.md).
* Använd Seriekonsol för att starta i [GRUB och ange enanvändarläge](serial-console-grub-single-user-mode.md)
* Seriekonsolen är också tillgängligt för [Windows](../windows/serial-console.md) virtuella datorer
* Läs mer om [startdiagnostik](boot-diagnostics.md)