---
title: Azure Serial Console proaktiv GRUB-konfiguration| Microsoft-dokument
description: Konfigurera GRUB över olika distributioner som tillåter åtkomst till en användare och återställningsläge i virtuella Azure-datorer.
services: virtual-machines-linux
documentationcenter: ''
author: mimckitt
manager: vashan
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/10/2019
ms.author: mimckitt
ms.openlocfilehash: 573bd0797e63fc512e59b0e0882c718e4569111c
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262901"
---
# <a name="proactively-ensuring-you-have-access-to-grub-and-sysrq-could-save-you-lots-of-down-time"></a>Proaktivt se till att du har tillgång till GRUB och sysrq kan spara massor av ner tid

Att ha tillgång till seriekonsolen och GRUB kommer att förbättra återställningstiderna för din IaaS Linux Virtual Machine i de flesta fall. GRUB erbjuder återställningsalternativ som annars skulle ta längre tid att återställa din virtuella dator. 


Orsakerna till att utföra en vm-återställning är många och kan hänföras till scenarier som:

   - Korrupta filsystem/kernel/MBR (Master Boot Record)
   - Misslyckade kärnuppgraderingar
   - Felaktiga GRUB-kärnparametrar
   - Felaktiga fstab-konfigurationer
   - Brandväggskonfigurationer
   - Glömt lösenord
   - Mangled sshd konfigurationer filer
   - Nätverkskonfigurationer

 Många andra scenarier som beskrivs [här](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux#common-scenarios-for-accessing-the-serial-console)

Kontrollera att du kan komma åt GRUB och seriekonsolen på dina virtuella datorer som distribueras i Azure. 

Om du inte har tidigare i Serial Console läser du [den här länken](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux/).

> [!TIP]
> Se till att du tar säkerhetskopior av filer innan du gör ändringar

Titta på den här videon nedan för att se hur du snabbt kan återställa din Linux VM när du har tillgång till GRUB

[Återställa Linux VM-video](https://youtu.be/KevOc3d_SG4)

Det finns ett antal metoder för att hjälpa återställning av virtuella Linux-datorer. I en molnmiljö har den här processen varit utmanande.
Framsteg görs kontinuerligt med verktyg och funktioner för att säkerställa att tjänsterna återvinns snabbt.

Med Azure Serial Console kan du interagera med din Virtuella Linux-dator som om du var på ett systems konsol.

Du kan manipulera många konfigurationsfiler, inklusive hur kärnan startar. 

De mer erfarna Linux/Unix-systemadministratörerna kommer att uppskatta de **enanvändar-** och **nödlägen** som är tillgängliga via Azure Serial Console som gör diskswap- och VM-borttagning för många återställningsscenarier överflödiga.

Återställningsmetoden beror på problemet som uppstår, till exempel kan ett förlorat eller felplacerat lösenord återställas via Azure-portalalternativ -> **Återställ lösenord**. Funktionen **Återställ lösenord** kallas ett tillägg och kommunicerar med Linux Guest Agent.

Andra tillägg som Custom Script finns tillgängliga men dessa alternativ kräver att Linux **waagent** vara upp och i ett hälsosamt tillstånd som inte alltid är fallet.

![agentstatus](./media/virtual-machines-serial-console/agent-status.png)


Att se till att du har åtkomst till Azure Serial Console och GRUB innebär att en lösenordsändring eller en felaktig konfiguration kan rättas till på några minuter i stället för timmar. Du kan även tvinga den virtuella datorn att starta från en alternativ kärna om du har flera kärnor på disken i det scenario där din primära kärna blir skadad.

![flera kärnor](./media/virtual-machines-serial-console/more-kernel.png)

## <a name="suggested-order-of-recovery-methods"></a>Föreslagen ordning för återställningsmetoder:

- Seriell konsol i Azure

- Disk Swap - kan automatiseras med antingen:

   - [Power Shell-återställningsskript](https://github.com/Azure/azure-support-scripts/tree/master/VMRecovery/ResourceManager)
   - [bash Recovery Skript](https://github.com/sribs/azure-support-scripts)

- Äldre metod

## <a name="disk-swap-video"></a>Video för diskbyte:

Om du inte har tillgång till GRUB titta på [den här](https://youtu.be/m5t0GZ5oGAc) videon och se, hur du enkelt kan automatisera disken swap förfarande för att återställa din VM

## <a name="challenges"></a>Utmaningar:

Alla virtuella Linux Azure-datorer är inte konfigurerade som standard för GRUB-åtkomst och inte heller är de alla konfigurerade för att avbrytas med sysrq-kommandona. Vissa äldre distributioner, till exempel SLES 11, har inte konfigurerats för att visa inloggningsfråga i Azure Serial Console

I den här artikeln kommer vi att granska olika Linux-distributioner och dokumentkonfigurationer om hur du gör GRUB tillgängligt.




## <a name="how-to-configure-linux-vm-to-accept-sysrq-keys"></a>Konfigurera Linux VM för att acceptera SysRq-nycklar
Sysrq-nyckeln är aktiverad på vissa nyare Linux-distributioner som standard, men på andra kan den konfigureras för att acceptera värden endast för vissa SysRq-funktioner.
På äldre distributioner kan det vara inaktiverat helt.

SysRq-funktionen är användbar för att starta om en kraschad eller hängd virtuell dator direkt från Azure Serial Console, också användbart för att få tillgång till GRUB-menyn, alternativt starta om en virtuell dator från ett annat portalfönster eller ssh-session kan släppa din nuvarande konsolanslutning vilket utgående GRUB-timeout till vilka används för att visa GRUB-menyn.
Den virtuella datorn måste konfigureras för att acceptera ett värde av 1 för kernel-parametern, vilket aktiverar alla funktioner i sysrq eller 128, vilket möjliggör omstart/poweroff


[Aktivera sysrq-video](https://youtu.be/0doqFRrHz_Mc)


Om du vill konfigurera den virtuella datorn så att den accepterar en omstart via SysRq-kommandon på Azure-portalen måste du ange ett värde på 1 för kernel-parametern kernel.sysrq

Lägg till en post i filen **sysctl.conf** för att den här konfigurationen ska kunna sparas

`echo kernel.sysrq = 1 >> /etc/sysctl.conf`

Så här konfigurerar du kernel-parametern dynamiskt

`sysctl -w kernel.sysrq=1`

Om du inte har **root-åtkomst** eller sudo är bruten, kommer det inte att vara möjligt att konfigurera sysrq från en skalfråga.

Du kan aktivera sysrq i det här scenariot med hjälp av Azure-portalen. Den här metoden kan vara till nytta om **sudoers.d/waagent-filen** har brutits eller har tagits bort.

Med hjälp av Azure portal Operations -> Run Command -> RunShellScript-funktionen kräver waagent-processen vara felfri kan du sedan injicera det här kommandot för att aktivera sysrq

`sysctl -w kernel.sysrq=1 ; echo kernel.sysrq = 1 >> /etc/sysctl.conf`

Som visas ![här: aktivera sysrq2](./media/virtual-machines-serial-console/enabling-sysrq-2.png)

När du är klar kan du sedan försöka komma åt **sysrq** och bör se att en omstart är möjlig.

![aktivera sysrq3](./media/virtual-machines-serial-console/enabling-sysrq-3.png)

Välj **Starta om** och skicka kommandot **SysRq**

![aktivera sysrq4](./media/virtual-machines-serial-console/enabling-sysrq-4.png)

Systemet bör logga ett återställningsmeddelande som detta

![aktivera sysrq5](./media/virtual-machines-serial-console/enabling-sysrq-5.png)


## <a name="ubuntu-grub-configuration"></a>Ubuntu GRUB-konfiguration

Som standard bör du kunna komma åt GRUB genom att hålla ned **Esc-tangenten** under vm-start, om GRUB-menyn inte visas kan du tvinga och hålla GRUB-menyn på skärmen i Azure Serial Console med hjälp av något av dessa alternativ.

**Alternativ 1** - Tvingar GRUB att visas på skärmen 

Uppdatera filen /etc/default/grub.d/50-cloudimg-settings.cfg för att hålla GRUB-menyn på skärmen för den angivna TIMEOUT.
Du behöver inte träffa **Esc** eftersom GRUB visas omedelbart

```
GRUB_TIMEOUT=0

change to

GRUB_TIMEOUT=5
```

**Alternativ 2** - Gör det möjligt för **Esc** att tryckas in innan du startar

Liknande beteende kan upplevas genom att göra ändringar i filen / etc / default / grub och observera en 3-sekunders timeout för att träffa **Esc**


Kommentera ut dessa två rader:

```
#GRUB_HIDDEN_TIMEOUT=0
#GRUB_HIDDEN_TIMEOUT_QUIET=true
```
och lägg till den här raden:

```
GRUB_TIMEOUT_STYLE=countdown
```


## <a name="ubuntu-1204"></a>Ubuntu 12\.04

Ubuntu 12.04 ger tillgång till seriell konsol men erbjuder inte möjligheten att interagera. En **inloggning:** prompten visas inte


För 12,04 för att få en **inloggning:** fråga:
1. Skapa en fil som heter /etc/init/ttyS0.conf som innehåller följande text:

    ```
    # ttyS0 - getty
    #
    # This service maintains a getty on ttyS0 from the point the system is
    # started until it is shut down again.
    start on stopped rc RUNLEVEL=[12345]
    stop on runlevel [!12345]
    
    respawn
    exec /sbin/getty -L 115200 ttyS0 vt102
    ```    

2. Be uppkomlingen att starta getty     
    ```
    sudo start ttyS0
    ```
 
De inställningar som krävs för att konfigurera seriell konsol för Ubuntu-versioner finns [här](https://help.ubuntu.com/community/SerialConsoleHowto)

## <a name="ubuntu-recovery-mode"></a>Återställningsläge för Ubuntu

Ytterligare återställnings- och rensningsalternativ är tillgängliga för Ubuntu via GRUB, men dessa inställningar är endast tillgängliga om du konfigurerar kärnparametrar därefter.
Om du inte konfigurerar den här kernel-startparametern skulle återställningsmenyn skickas till Azure Diagnostics och inte till Azure Serial Console.
Du kan få tillgång till Ubuntu Recovery Menu genom att följa dessa steg:

Avbryt BOOT-processen och få tillgång till GRUB-menyn

Välj Avancerade alternativ för Ubuntu och tryck på retur

![ubunturec1 (ubunturec1)](./media/virtual-machines-serial-console/ubunturec1.png)

Välj den linje som visas *(återställningsläge)* tryck inte enter men tryck på "e"

![ubunturec2 (ubunturec2)](./media/virtual-machines-serial-console/ubunturec2.png)

Leta reda på den linje som ska läsa in kärnan och ersätta den sista **parameternoden** med destination som **console=ttyS0**

```
linux /boot/vmlinuz-4.15.0-1023-azure root=UUID=21b294f1-25bd-4265-9c4e-d6e4aeb57e97 ro recovery nomodeset

change to

linux /boot/vmlinuz-4.15.0-1023-azure root=UUID=21b294f1-25bd-4265-9c4e-d6e4aeb57e97 ro recovery console=ttyS0
```

![ubunturec3 (ubunturec3)](./media/virtual-machines-serial-console/ubunturec3.png)

Tryck på **Ctrl-x** för att starta och läsa in kärnan.
Om allt går bra kommer du att se dessa ytterligare alternativ, som kan hjälpa till att utföra andra återställningsalternativ

![ubunturec4 (ubunturec4)](./media/virtual-machines-serial-console/ubunturec4.png)


## <a name="red-hat-grub-configuration"></a>Red Hat GRUB konfiguration

## <a name="red-hat-74-grub-configuration"></a>Red Hat\.7\+ 4 GRUB konfiguration
Standardkonfigurationen för /etc/default/grub på dessa versioner är tillräckligt konfigurerad

```
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL="serial console"
GRUB_SERIAL_COMMAND="serial"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
GRUB_DISABLE_RECOVERY="true"
```

Aktivera SysRq-tangenten

```
sysctl -w kernel.sysrq=1;echo kernel.sysrq = 1 >> /etc/sysctl.conf;sysctl -a | grep -i sysrq
```

## <a name="red-hat-72-and-73-grub-configuration"></a>Red Hat\.7 2\.och 7 3 GRUB konfiguration
Filen för att ändra är / etc / standard / grub - en standard config ser ut så här exemplet:

```
GRUB_TIMEOUT=1
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL_OUTPUT="console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
GRUB_DISABLE_RECOVERY="true"
```

Ändra följande rader i /etc/default/grub

```
GRUB_TIMEOUT=1 

to

GRUB_TIMEOUT=5
```


```
GRUB_TERMINAL_OUTPUT="console"

to

GRUB_TERMINAL="serial console"
```

Lägg också till den här raden:

```
GRUB_SERIAL_COMMAND=”serial –speed=115200 –unit=0 –word=8 –parity=no –stop=1″
```

/etc/default/grub bör nu se ut ungefär som det här exemplet:

```
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL="serial console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
GRUB_DISABLE_RECOVERY="true"
```
 
Slutför och uppdatera grub-konfiguration med

`grub2-mkconfig -o /boot/grub2/grub.cfg`

Ange parametern SysRq-kärna:

`sysctl -w kernel.sysrq = 1;echo kernel.sysrq = 1 >> /etc/sysctl.conf;sysctl -a | grep -i sysrq`

Du kan alternativt konfigurera GRUB och SysRq med en enda rad antingen i skalet eller via körkommandot. Säkerhetskopiera dina filer innan du kör det här kommandot:


`cp /etc/default/grub /etc/default/grub.bak; sed -i 's/GRUB_TIMEOUT=1/GRUB_TIMEOUT=5/g' /etc/default/grub; sed -i 's/GRUB_TERMINAL_OUTPUT="console"/GRUB_TERMINAL="serial console"/g' /etc/default/grub; echo "GRUB_SERIAL_COMMAND=\"serial --speed=115200 --unit=0 --word=8 --parity=no --stop=1\"" >> /etc/default/grub;grub2-mkconfig -o /boot/grub2/grub.cfg;sysctl -w kernel.sysrq=1;echo kernel.sysrq = 1 /etc/sysctl.conf;sysctl -a | grep -i sysrq`


## <a name="red-hat-6x-grub-configuration"></a>Red Hat\.6 x GRUB konfiguration
Filen för att ändra är / boot / grub/grub.conf. Värdet `timeout` avgör hur länge GRUB visas.

```
#boot=/dev/vda
default=0
timeout=15
splashimage=(hd0,0)/grub/splash.xpm.gz
#hiddenmenu
serial --unit=0 --speed=9600
terminal serial
terminal --timeout=5 serial console
```


Den sista raden *terminal --timeout = 5 seriell konsol* kommer att ytterligare öka **GRUB** timeout genom att lägga till en uppmaning på 5 sekunder visar **Tryck på valfri tangent för att fortsätta.**

![rh6-1](./media/virtual-machines-serial-console/rh6-1.png)

GRUB-menyn ska visas på skärmen för den konfigurerade timeout=15 utan att du behöver trycka på Esc. Se till att klicka i konsolen i webbläsaren för att göra aktiv menyn och välj önskad kärna

![rh6-2](./media/virtual-machines-serial-console/rh6-2.png)

## <a name="suse"></a>Suse

## <a name="sles-12-sp1"></a>SLES 12 sp1
Antingen använda yast bootloader enligt den officiella [docs](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-grub-single-user-mode#grub-access-in-suse-sles)

Eller lägga till / ändra till / etc / standard / grub följande parametrar:

```
GRUB_TERMINAL=serial
GRUB_TIMEOUT=5
GRUB_SERIAL_COMMAND="serial --unit=0 --speed=9600 --parity=no"

```
Kontrollera att ttys0 används i GRUB_CMDLINE_LINUX eller GRUB_CMDLINE_LINUX_DEFAULT

```
GRUB_CMDLINE_LINUX_DEFAULT="console=ttyS0,9600n"
```

Återskapa grub.cfg

`grub2-mkconfig -o /boot/grub2/grub.cfg`


## <a name="sles-11-sp4"></a>SLES 11 SP4 
Seriekonsolen visas och visar startmeddelanden men visar ingen **inloggning:** fråga

Öppna en ssh-session i den virtuella datorn och uppdatera filen **/etc/inittab** genom att avkommentera den här raden:

```
#S0:12345:respawn:/sbin/agetty -L 9600 ttyS0 vt102
```

Nästa kör kommandot 

`telinit q`

För att aktivera GRUB bör följande ändringar göras i /boot/grub/menu.lst 

```
timeout 5
serial --unit=0 --speed=9600 --parity=no
terminal --timeout=5 serial console

root (hd0,0)
kernel /boot/vmlinuz-3.0.101-108.74-default root=/dev/disk/by-uuid/ab6b62bb--
1a8c-45eb-96b1-1fbc535b9265 disk=/dev/sda  USE_BY_UUID_DEVICE_NAMES=1 earlyprinttk=ttyS0 console=ttyS0 rootdelay=300  showopts vga=0x314
```

 Den här konfigurationen aktiverar meddelandet **Tryck på valfri tangent** för att fortsätta att visas på konsolen i 5 sekunder 

Det kommer då att visa GRUB-menyn i ytterligare 5 sekunder - genom att trycka på nedåtpilen kommer du att avbryta räknaren och välja en kärna som du vill starta antingen lägga till **sökordssingeln** för enanvändarläge som kräver root lösenord som ska ställas in.

Om du rapporterar kommandot **init=/bin/bash** läses kärnan in men init-programmet ersätts av ett bash-skal.

Du kommer att få tillgång till ett skal utan att behöva ange ett lösenord. Du kan sedan fortsätta att uppdatera lösenord för Linux-konton eller göra andra konfigurationsändringar.


## <a name="force-the-kernel-to-a-bash-prompt"></a>Tvinga kärnan till en bash-prompt
Med tillgång till GRUB kan du avbryta initieringsprocessen denna interaktion är användbart för många återställningsprocedurer.
Om du inte har root lösenord och en användare kräver att du har en rot lösenord, kan du starta kärnan ersätter init-programmet med en bash prompt - detta avbrott kan uppnås genom att lägga till init = / bin / bash till kärnan startlinjen

![bash1 (på en)](./media/virtual-machines-serial-console/bash1.png)

Sätt tillbaka filsystemet RW för /(rot) med kommandot

`mount -o remount,rw /`

![bash2 (på en)](./media/virtual-machines-serial-console/bash2.png)


Nu kan du utföra root lösenordsändring eller många andra Linux-konfigurationsändringar

![bash3 (på en)](./media/virtual-machines-serial-console/bash3.png)

Starta om den virtuella datorn med 

`/sbin/reboot -f`




## <a name="single-user-mode"></a>Läget En användare

Alternativt kan du behöva komma åt den virtuella datorn i enanvändar- eller nödläge. Välj den kärna som du vill starta eller avbryta med piltangenterna.
Ange önskat läge genom att lägga till nyckelordet **single** eller **1** till kärnstartlinjen. På RHEL-system kan du också lägga till **rd.break**.

Mer information om hur du kommer åt enanvändarläge finns i [det här dokumentet](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-grub-single-user-mode#general-single-user-mode-access) 


![single_user_ubuntu](./media/virtual-machines-serial-console/single-user-ubuntu.png)


## <a name="next-steps"></a>Nästa steg
Läs mer om [Azure Serial Console]( https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux)
