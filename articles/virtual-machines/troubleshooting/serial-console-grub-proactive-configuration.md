---
title: Konfiguration av proaktiva GRUB i Azures seriell konsol | Microsoft Docs
description: Konfigurera GRUB över olika distributioner som tillåter åtkomst till enkel användare och återställnings läge på virtuella Azure-datorer.
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
ms.openlocfilehash: c48ef0321ece2e7e0ffcdfcb8c0907c5f839e738
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2020
ms.locfileid: "87831370"
---
# <a name="proactively-ensuring-you-have-access-to-grub-and-sysrq-could-save-you-lots-of-down-time"></a>Att se till att du har åtkomst till GRUB och SysRq kan spara mycket tid

Att ha åtkomst till serie konsolen och GRUB kommer att förbättra återställnings tiden för din virtuella IaaS Linux-dator i de flesta fall. GRUB erbjuder återställnings alternativ som annars skulle ta längre tid att återställa den virtuella datorn. 


Anledningen till att utföra en VM-återställning är många och kan användas för scenarier som:

   - Skadade fil system/kernel/MBR (Master Boot Record)
   - Misslyckade kernel-uppgraderingar
   - Felaktiga kernel-parametrar för GRUB
   - Felaktiga fstab-konfigurationer
   - Brand Väggs konfigurationer
   - Förlorat lösen ord
   - Mangled sshd-konfigurationsfiler
   - Nätverkskonfigurationer

 Många andra scenarier som beskrivs [här](./serial-console-linux.md#common-scenarios-for-accessing-the-serial-console)

Kontrol lera att du har åtkomst till GRUB och Seriell konsol på dina virtuella datorer som distribuerats i Azure. 

Om du är nybörjare på serie konsolen kan du se [den här länken](./serial-console-linux.md).

> [!TIP]
> Se till att du tar säkerhets kopior av filer innan du gör några ändringar

Titta på den här videon nedan och se hur du snabbt kan återställa din virtuella Linux-dator när du har åtkomst till GRUB

[Återställ Linux VM-video](https://youtu.be/KevOc3d_SG4)

Det finns ett antal metoder för att hjälpa till att återställa virtuella Linux-datorer. Den här processen har varit utmanande i en moln miljö.
Förloppet görs kontinuerligt till verktyg och funktioner för att säkerställa att tjänsterna återställs snabbt.

Med Azures serie konsol kan du interagera med din virtuella Linux-dator som om du var i ett Systems-konsol.

Du kan ändra många konfigurationsfiler, inklusive hur kerneln ska starta. 

De mer erfarna Linux/UNIX sys-administratörer kommer att uppskatta de **enskilda användare** och **nöd lägen** som är tillgängliga via Azures serie konsol för att göra disk växling och virtuell dator borttagning för många återställnings scenarier redundanta.

Återställnings metoden beror på problemet, till exempel om ett förlorat eller fel placerat lösen ord kan återställas via Azure Portal alternativ-> **Återställ lösen ord**. Funktionen **Återställ lösen ord** kallas för ett tillägg och kommunicerar med Linux-gästens agent.

Andra tillägg som anpassade skript är tillgängliga men de här alternativen kräver att Linux- **waagent** är upp och i ett felfritt tillstånd som inte alltid är fallet.

![Agent status](./media/virtual-machines-serial-console/agent-status.png)


Att se till att du har åtkomst till Azures serie konsol och GRUB innebär att en ändring av lösen ord eller en felaktig konfiguration kan åtgärdas på några minuter i stället för timmar. Du kan även tvinga den virtuella datorn att starta från en alternativ kernel om du har flera kärnor på disk i det scenario där din primära kernel skadas.

![flera kärnor](./media/virtual-machines-serial-console/more-kernel.png)

## <a name="suggested-order-of-recovery-methods"></a>Föreslagen ordning för återställnings metoder:

- Azures serie konsol

- Disk växling – kan automatiseras med hjälp av något av följande:

   - [PowerShell-återställnings skript](https://github.com/Azure/azure-support-scripts/tree/master/VMRecovery/ResourceManager)
   - [bash-återställnings skript](https://github.com/sribs/azure-support-scripts)

- Äldre metod

## <a name="disk-swap-video"></a>Video om disk växling:

Om du inte har åtkomst till GRUB titta på [den här](https://youtu.be/m5t0GZ5oGAc) videon och se hur du enkelt kan automatisera disk växlings proceduren för att återställa den virtuella datorn

## <a name="challenges"></a>Särskilda

Alla virtuella Linux Azure-datorer har inte kon figurer ATS som standard för GRUB-åtkomst och inte heller att de är konfigurerade för att avbrytas med SysRq-kommandona. Vissa äldre distributioner, till exempel SLES 11, är inte konfigurerade att Visa inloggnings meddelanden i Azures serie konsol

I den här artikeln granskar vi olika Linux-distributioner och dokument konfigurationer för att göra GRUB tillgängliga.




## <a name="how-to-configure-linux-vm-to-accept-sysrq-keys"></a>Så här konfigurerar du virtuella Linux-datorer så att de accepterar SysRq-nycklar
SysRq-nyckeln är aktive rad på vissa nyare Linux-distributioner som standard, även om andra inte kan konfigureras för att acceptera värden för vissa SysRq-funktioner.
På äldre distributioner kan det vara inaktiverat helt.

SysRq-funktionen är användbar för att starta om en kraschad eller icke-svarande virtuell dator direkt från Azures serie konsol, vilket också är användbart för att få åtkomst till GRUB-menyn. Alternativt kan du också starta om en virtuell dator från ett annat Portal fönster eller ssh-session, så att du kan släppa den aktuella konsol anslutningen, vilket innebär att GRUB tids gränser som används för att Visa GRUB-
Den virtuella datorn måste vara konfigurerad för att godkänna värdet 1 för kernel-parametern, som aktiverar alla funktioner i SysRq eller 128, vilket tillåter omstart/avstängnings läge


[Aktivera SysRq-video](https://youtu.be/0doqFRrHz_Mc)


Om du vill konfigurera den virtuella datorn så att den accepterar en omstart via SysRq-kommandon på Azure Portal måste du ange värdet 1 för kernel-parametern kernel.sysRQ

Lägg till en post i filen **sysctl. conf** för den här konfigurationen om du vill spara en omstart.

`echo kernel.sysrq = 1 >> /etc/sysctl.conf`

Konfigurera kernel-parametern dynamiskt

`sysctl -w kernel.sysrq=1`

Om du inte har **rot** åtkomst eller om sudo är bruten är det inte möjligt att konfigurera SysRq från en Shell-prompt.

Du kan aktivera SysRq i det här scenariot med hjälp av Azure Portal. Den här metoden kan vara fördelaktig om filen **sudoers. d/waagent** har blivit bruten eller har tagits bort.

Med hjälp av kommandot Azure Portal åtgärder-> Kör kommando > RunShellScript, måste waagent-processen vara felfri. sedan kan du mata in det här kommandot för att aktivera SysRq

`sysctl -w kernel.sysrq=1 ; echo kernel.sysrq = 1 >> /etc/sysctl.conf`

Som du ser här: ![ Aktivera sysrq2](./media/virtual-machines-serial-console/enabling-sysrq-2.png)

När du är klar kan du försöka komma åt **SysRq** och se att en omstart är möjlig.

![Aktivera sysrq3](./media/virtual-machines-serial-console/enabling-sysrq-3.png)

Välj **starta om** och **Skicka SysRq** -kommando

![Aktivera sysrq4](./media/virtual-machines-serial-console/enabling-sysrq-4.png)

Systemet ska logga ett återställnings meddelande som detta

![Aktivera sysrq5](./media/virtual-machines-serial-console/enabling-sysrq-5.png)


## <a name="ubuntu-grub-configuration"></a>Ubuntu GRUB-konfiguration

Som standard ska du kunna komma åt GRUB genom att hålla ned **ESC** -nyckeln under VM-starten, om grub-menyn inte visas kan du tvinga och behålla grub-menyn på skärmen i Azures serie konsol genom att använda något av dessa alternativ.

**Alternativ 1** – tvingar grub att visas på skärmen 

Uppdatera filen/etc/default/grub.d/50-cloudimg-Settings.cfg och behåll menyn GRUB på skärmen för den angivna tids gränsen.
Du behöver inte trycka på **ESC** när grub visas direkt

```
GRUB_TIMEOUT=0

change to

GRUB_TIMEOUT=5
```

**Alternativ 2** – tillåter att **ESC** trycks ned innan du startar

Liknande beteenden kan uppstå genom att göra ändringar i filen/etc/default/grub och se att det går att trycka på en tids gräns på 3 sekunder för att trycka på **ESC**


Kommentera följande två rader:

```
#GRUB_HIDDEN_TIMEOUT=0
#GRUB_HIDDEN_TIMEOUT_QUIET=true
```
och Lägg till den här raden:

```
GRUB_TIMEOUT_STYLE=countdown
```


## <a name="ubuntu-1204"></a>Ubuntu 12 \. 04

Ubuntu 12,04 ger åtkomst till serie konsolen men ger inte möjlighet att interagera. En **inloggning:** prompten visas inte


För att 12,04 ska få en **inloggning:** prompt:
1. Skapa en fil med namnet/etc/init/ttyS0.conf som innehåller följande text:

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

2. Be om att starta om Getty     
    ```
    sudo start ttyS0
    ```
 
De inställningar som krävs för att konfigurera en serie konsol för Ubuntu-versioner hittar du [här](https://help.ubuntu.com/community/SerialConsoleHowto)

## <a name="ubuntu-recovery-mode"></a>Återställnings läge för Ubuntu

Ytterligare återställnings-och rensnings alternativ är tillgängliga för Ubuntu via GRUB men de här inställningarna är bara tillgängliga om du konfigurerar kernel-parametrar på motsvarande sätt.
Om du inte konfigurerar den här kernel Boot-parametern tvingas återställnings menyn att skickas till Azure-diagnostik och inte till Azures serie konsol.
Du kan få åtkomst till Ubuntu-återställnings menyn genom att följa dessa steg:

Avbryta start processen och komma åt GRUB-menyn

Välj avancerade alternativ för Ubuntu och tryck på RETUR

![ubunturec1](./media/virtual-machines-serial-console/ubunturec1.png)

Välj den rad som visar *(återställnings läge)* tryck inte på RETUR men tryck på "e"

![ubunturec2](./media/virtual-machines-serial-console/ubunturec2.png)

Leta upp den rad som laddar kärnan och ersätt den sista parametern **nomodeset** med destination som **console = ttyS0**

```
linux /boot/vmlinuz-4.15.0-1023-azure root=UUID=21b294f1-25bd-4265-9c4e-d6e4aeb57e97 ro recovery nomodeset

change to

linux /boot/vmlinuz-4.15.0-1023-azure root=UUID=21b294f1-25bd-4265-9c4e-d6e4aeb57e97 ro recovery console=ttyS0
```

![ubunturec3](./media/virtual-machines-serial-console/ubunturec3.png)

Tryck på **CTRL-x** för att starta och läsa in kärnan.
Om alla går bra ser du dessa ytterligare alternativ, som kan hjälpa dig att utföra andra återställnings alternativ

![ubunturec4](./media/virtual-machines-serial-console/ubunturec4.png)


## <a name="red-hat-grub-configuration"></a>Konfiguration av Red Hat-GRUB

## <a name="red-hat-74-grub-configuration"></a>Konfiguration av Red Hat 7 \. 4- \+ grub
Standard konfigurationen för/etc/default/grub i dessa versioner är korrekt konfigurerad

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

Aktivera SysRq-nyckeln

```
sysctl -w kernel.sysrq=1;echo kernel.sysrq = 1 >> /etc/sysctl.conf;sysctl -a | grep -i sysrq
```

## <a name="red-hat-72-and-73-grub-configuration"></a>Konfiguration av Red Hat 7 \. 2 och 7 \. 3 grub
Filen som ska ändras är/etc/default/grub – en standard konfiguration ser ut som i det här exemplet:

```
GRUB_TIMEOUT=1
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL_OUTPUT="console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
GRUB_DISABLE_RECOVERY="true"
```

Ändra följande rader i/etc/default/grub

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

/etc/default/grub bör nu se ut ungefär som i det här exemplet:

```
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL="serial console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
GRUB_DISABLE_RECOVERY="true"
```
 
Slutför och uppdatera grub-konfigurationen med

`grub2-mkconfig -o /boot/grub2/grub.cfg`

Ange kernel-parametern SysRq:

`sysctl -w kernel.sysrq = 1;echo kernel.sysrq = 1 >> /etc/sysctl.conf;sysctl -a | grep -i sysrq`

Du kan också konfigurera GRUB och SysRq med hjälp av en enda rad, antingen i gränssnittet eller via kommandot kör. Säkerhetskopiera filerna innan du kör det här kommandot:


`cp /etc/default/grub /etc/default/grub.bak; sed -i 's/GRUB_TIMEOUT=1/GRUB_TIMEOUT=5/g' /etc/default/grub; sed -i 's/GRUB_TERMINAL_OUTPUT="console"/GRUB_TERMINAL="serial console"/g' /etc/default/grub; echo "GRUB_SERIAL_COMMAND=\"serial --speed=115200 --unit=0 --word=8 --parity=no --stop=1\"" >> /etc/default/grub;grub2-mkconfig -o /boot/grub2/grub.cfg;sysctl -w kernel.sysrq=1;echo kernel.sysrq = 1 /etc/sysctl.conf;sysctl -a | grep -i sysrq`


## <a name="red-hat-6x-grub-configuration"></a>Konfiguration av Red Hat 6 \. x-grub
Filen som ska ändras är/boot/grub/grub.conf. `timeout`Värdet avgör hur lång grub som visas för.

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


Den sista raden *Terminal – timeout = 5 i serie konsolen* ökar **grub** -tids gränsen ytterligare genom att lägga till ett meddelande om 5 sekunder som visar **Tryck på valfri tangent för att fortsätta.**

![RH6-1](./media/virtual-machines-serial-console/rh6-1.png)

GRUB-menyn ska visas på skärmen för den konfigurerade tids gränsen = 15 utan att du behöver trycka på ESC. Se till att klicka i-konsolen i webbläsaren för att aktivera menyn och välja önskad kernel

![RH6-2](./media/virtual-machines-serial-console/rh6-2.png)

## <a name="suse"></a>SuSE

## <a name="sles-12-sp1"></a>SLES 12 SP1
Använd antingen YaST-startprogrammet som enligt officiella [dokument](./serial-console-grub-single-user-mode.md#grub-access-in-suse-sles)

Eller Lägg till/ändra för att/etc/default/grub följande parametrar:

```
GRUB_TERMINAL=serial
GRUB_TIMEOUT=5
GRUB_SERIAL_COMMAND="serial --unit=0 --speed=9600 --parity=no"

```
Kontrol lera att ttyS0 används i GRUB_CMDLINE_LINUX eller GRUB_CMDLINE_LINUX_DEFAULT

```
GRUB_CMDLINE_LINUX_DEFAULT="console=ttyS0,9600n"
```

Återskapa grub. cfg

`grub2-mkconfig -o /boot/grub2/grub.cfg`


## <a name="sles-11-sp4"></a>SLES 11 SP4 
Serie konsolen visas och visar start meddelanden, men visar inte någon **inloggning:** prompt

Öppna en SSH-session i den virtuella datorn och uppdatera filen **/etc/inittab** genom att ångra kommentaren till den här raden:

```
#S0:12345:respawn:/sbin/agetty -L 9600 ttyS0 vt102
```

Kör kommandot på nästa 

`telinit q`

Om du vill aktivera GRUB bör du göra följande ändringar i/boot/grub/menu.lst 

```
timeout 5
serial --unit=0 --speed=9600 --parity=no
terminal --timeout=5 serial console

root (hd0,0)
kernel /boot/vmlinuz-3.0.101-108.74-default root=/dev/disk/by-uuid/ab6b62bb--
1a8c-45eb-96b1-1fbc535b9265 disk=/dev/sda  USE_BY_UUID_DEVICE_NAMES=1 earlyprinttk=ttyS0 console=ttyS0 rootdelay=300  showopts vga=0x314
```

 Med den här konfigurationen kan meddelandet **trycka på valfri tangent för att fortsätta** visas i konsolen i 5 sekunder 

Sedan visas GRUB-menyn med ytterligare 5 sekunder – genom att trycka på nedåtpilen avbryts räknaren och välj en kernel som du vill starta genom att antingen lägga till nyckelordet **Single** för Single User-läge som kräver att rot lösen ordet anges.

Om du lägger till kommandot **init =/bin/bash** läses kerneln, men säkerställer att programmet init ersätts av ett bash-gränssnitt.

Du får åtkomst till ett gränssnitt utan att behöva ange ett lösen ord. Sedan kan du fortsätta med att uppdatera lösen ordet för Linux-konton eller göra andra konfigurations ändringar.


## <a name="force-the-kernel-to-a-bash-prompt"></a>Tvinga kärnan till en bash-prompt
När du har åtkomst till GRUB kan du avbryta initierings processen som den här interaktionen är användbar för många återställnings procedurer.
Om du inte har rot lösen ordet och en enskild användare kräver att du har ett rot lösen ord, kan du starta kärnan och ersätta programmet init med en bash-prompt – detta avbrott kan uppnås genom att lägga till init =/bin/bash på kernel-startlinjen

![bash1](./media/virtual-machines-serial-console/bash1.png)

Montera om/(root) fil system RW med kommandot

`mount -o remount,rw /`

![bash2](./media/virtual-machines-serial-console/bash2.png)


Nu kan du utföra ändringar i rot lösen ordet eller många andra ändringar i Linux-konfigurationen

![bash3](./media/virtual-machines-serial-console/bash3.png)

Starta om den virtuella datorn med 

`/sbin/reboot -f`




## <a name="single-user-mode"></a>Läge för enkel användare

Alternativt kan du behöva komma åt den virtuella datorn i en enskild användare eller nöd läge. Välj den kernel som du vill starta eller avbryta med piltangenterna.
Ange önskat läge genom att lägga till nyckelordet **Single** eller **1** på kernelns start rad. Du kan också lägga till **Rd. Break**i RHEL-system.

Mer information om hur du kommer åt enanvändarläge finns i [det här dokumentet](./serial-console-grub-single-user-mode.md#general-single-user-mode-access) 


![single_user_ubuntu](./media/virtual-machines-serial-console/single-user-ubuntu.png)


## <a name="next-steps"></a>Nästa steg
Läs mer om [Azures serie konsol]( ./serial-console-linux.md)