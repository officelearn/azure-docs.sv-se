---
title: Azures serie konsol för GRUB och enanvändarläge | Microsoft Docs
description: Den här artikeln beskriver hur du använder en serie konsol för GRUB i virtuella Azure-datorer.
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
ms.date: 08/06/2019
ms.author: alsin
ms.openlocfilehash: 5341cc62a7d02c3072df90becf893dec18427ac2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "87439546"
---
# <a name="use-serial-console-to-access-grub-and-single-user-mode"></a>Använd serie konsolen för att komma åt GRUB och enanvändarläge
GRand Unified Starter (GRUB) är förmodligen det första du ser när du startar en virtuell dator (VM). Eftersom det visas innan operativ systemet har startats går det inte att komma åt GRUB via SSH. I GRUB kan du ändra start konfigurationen till att starta i enanvändarläge, bland annat.

Enanvändarläge är en minimal miljö med minimal funktionalitet. Det kan vara användbart för att undersöka start problem, problem med fil systemet eller nätverks problem. Färre tjänster kan köras i bakgrunden och, beroende på runlevel, kan ett fil system inte ens monteras automatiskt.

Enanvändarläge är också användbart i situationer där din virtuella dator kan konfigureras för att endast godkänna SSH-nycklar för inloggning. I det här fallet kanske du kan använda enanvändarläge för att skapa ett konto med lösenordsautentisering.

> [!NOTE]
> Tjänsten för seriella konsoler tillåter endast användare med *deltagar* nivå eller högre behörighet att komma åt den seriella konsolen för en virtuell dator.

Om du vill ange enanvändarläge anger du GRUB när den virtuella datorn startas och ändrar Start konfigurationen i GRUB. Se detaljerade instruktioner för att ange GRUB i nästa avsnitt. I allmänhet, om din virtuella dator har kon figurer ATS för att Visa GRUB, kan du använda knappen starta om i den virtuella datorns serie konsol för att starta om den virtuella datorn och Visa GRUB.

![Knappen starta om den seriella konsolen för Linux](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-bar.png)

## <a name="general-grub-access"></a>Allmän GRUB-åtkomst
Du kommer åt GRUB genom att starta om den virtuella datorn när serie konsol fönstret är öppet. Vissa distributioner kräver tangent bords indata för att Visa GRUB, och andra visar GRUB automatiskt i några sekunder så att användaren kan avbryta tids indatan.

För att kunna komma åt enanvändarläge, vill du se till att GRUB är aktiverat på den virtuella datorn. Beroende på din distribution kan vissa inställningar vara nödvändiga för att se till att GRUB har Aktiver ATS. Information om distribution finns i nästa avsnitt.

### <a name="restart-your-vm-to-access-grub-in-serial-console"></a>Starta om den virtuella datorn för att få åtkomst till GRUB i serie konsolen
Du kan starta om den virtuella datorn i serie konsolen genom att hovra över knappen **starta om** och sedan välja **starta om virtuell dator**. Ett meddelande om omstarten visas längst ned i fönstret.

Du kan också starta om den virtuella datorn genom att köra ett SysRq "b"-kommando om [SysRq](./serial-console-nmi-sysrq.md) är aktiverat. Information om vad som förväntas från GRUB när du startar om finns i de distributions anvisningar som beskrivs i nästa avsnitt.

![Starta om den seriella konsolen för Linux](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-ubuntu.gif)

## <a name="general-single-user-mode-access"></a>Allmän åtkomst till enanvändarläge
Du kan behöva manuellt åtkomst till enanvändarläge när du inte har konfigurerat ett konto med lösenordsautentisering. Ändra GRUB-konfigurationen för att manuellt ange enanvändarläge. När du har gjort det går du till avsnittet "Använd enanvändarläge för att återställa eller lägga till ett lösen ord" för ytterligare instruktioner.

Om den virtuella datorn inte kan starta, släpps ofta distributioner automatiskt i enanvändarläge eller i nödfalls läge. Andra distributioner kräver dock ytterligare konfiguration, till exempel att konfigurera ett rot lösen ord, innan de kan släppa dig i enanvändarläge eller nöd läge automatiskt.

### <a name="use-single-user-mode-to-reset-or-add-a-password"></a>Använd enanvändarläge för att återställa eller lägga till ett lösen ord
När du är i enanvändarläge lägger du till en ny användare med sudo-behörighet genom att göra följande:
1. Kör `useradd <username>` för att lägga till en användare.
1. Kör `sudo usermod -a -G sudo <username>` för att ge den nya användar rot behörigheten.
1. Används `passwd <username>` för att ange lösen ordet för den nya användaren. Sedan kan du logga in som den nya användaren.


## <a name="access-for-red-hat-enterprise-linux-rhel"></a>Åtkomst för Red Hat Enterprise Linux (RHEL)
Om RHEL inte kan starta normalt hamnar du i enanvändarläge automatiskt. Men om du inte har konfigurerat rot åtkomst för enanvändarläge, har du inget rot lösen ord och kan inte logga in. Det finns en lösning (se avsnittet "ange manuellt läge för enkel användare i RHEL"), men vi rekommenderar att du konfigurerar rot åtkomst initialt.

### <a name="grub-access-in-rhel"></a>GRUB-åtkomst i RHEL
RHEL levereras med GRUB aktive rad. Om du vill ange GRUB startar du om den virtuella datorn genom att köra `sudo reboot` och trycker sedan på valfri tangent. Fönstret GRUB visas. Om den inte är det kontrollerar du att följande rader finns i din GRUB-fil ( `/etc/default/grub` ):

**För RHEL 8**

>[!NOTE]
> Red Hat rekommenderar att du använder grubby för att konfigurera kommando rads parametrar för kernel i RHEL 8 +. Det går för närvarande inte att uppdatera grub-timeout och Terminal-parametrar med grubby. Om du vill ändra uppdatering av argumentet GRUB_CMDLINE_LINUX för alla start poster kör du `grubby --update-kernel=ALL --args="console=ttyS0,115200 console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"` . Mer information finns [här](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/managing_monitoring_and_updating_the_kernel/configuring-kernel-command-line-parameters_managing-monitoring-and-updating-the-kernel).

```
GRUB_TIMEOUT=5
GRUB_TERMINAL="serial console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
```

**För RHEL 7**

```
GRUB_TIMEOUT=5
GRUB_TERMINAL_OUTPUT="serial console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300 net.ifnames=0"
```

> [!NOTE]
> Red Hat innehåller också dokumentation för att starta i undsättnings läge, nöd läge eller fel söknings läge och för att återställa rot lösen ordet. Instruktioner finns i [Redigera Terminal-meny under start](https://aka.ms/rhel7grubterminal).

### <a name="set-up-root-access-for-single-user-mode-in-rhel"></a>Konfigurera rot åtkomst för enanvändarläge i RHEL
Rot användaren är inaktive rad som standard. Single-User-läget i RHEL kräver att rot användaren är aktive rad. Om du behöver aktivera enanvändarläge använder du följande instruktioner:

1. Logga in på Red Hat-systemet via SSH.
1. Växla till roten.
1. Aktivera lösen ordet för rot användaren genom att göra följande:
    * Kör `passwd root` (ange ett starkt rot lösen ord).
1. Se till att rot användaren bara kan logga in via ttyS0 genom att göra följande: a. Kör `edit /etc/ssh/sshd_config` och se till att PermitRootLogIn är inställt på `no` .
    b. Kör `edit /etc/securetty file` endast för att tillåta inloggning via ttyS0.

Nu kan du logga in med rot lösen ordet om systemet startar i enanvändarläge.

Alternativt, för RHEL 7.4 + eller 6,9 +, för att aktivera enanvändarläge i GRUB-prompten, se [starta i enanvändarläge](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/installation_guide/s1-rescuemode-booting-single).

### <a name="manually-enter-single-user-mode-in-rhel"></a>Ange läget för enkel användare manuellt i RHEL
Om du har konfigurerat GRUB och rot åtkomst genom att följa anvisningarna ovan kan du ange läge för enkel användare genom att göra följande:

1. Om du vill ange GRUB trycker du på ESC när du startar om den virtuella datorn.
1. I GRUB trycker du på E för att redigera det operativ system som du vill starta i. Operativ systemet visas vanligt vis på den första raden.
1. Hitta kernel-raden. I Azure börjar den med *linux16*.
1. Tryck på CTRL + E för att gå till slutet av raden.
1. I slutet av raden lägger du till *system. Unit = räddning. Target*.

    Den här åtgärden startar dig i enanvändarläge. Om du vill använda nöd läge, lägger du till *system. Unit = nödfall. Target* i slutet av raden (i stället för *systemed. Unit = räddning. Target*).

1. Tryck på CTRL + X för att avsluta och starta om med de tillämpade inställningarna.

   Du uppmanas att ange administratörs lösen ordet innan du kan ange läget för en enskild användare. Det här lösen ordet är det som du skapade i föregående instruktioner.

    ![Animerad bild som visar ett kommando rads gränssnitt. Användaren väljer en server, letar upp slutet på kernel-raden och anger sedan den angivna texten.](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-enter-emergency-shell.gif)

### <a name="enter-single-user-mode-without-root-account-enabled-in-rhel"></a>Ange läget för en enskild användare utan att rot kontot är aktiverat i RHEL
Om du inte har aktiverat rot användaren genom att följa de tidigare anvisningarna kan du fortfarande återställa rot lösen ordet genom att göra följande:

> [!NOTE]
> Om du använder SELinux, när du återställer rot lösen ordet, måste du följa de ytterligare steg som beskrivs i [Red Hat-dokumentationen](https://aka.ms/rhel7grubterminal).

1. Om du vill ange GRUB trycker du på ESC när du startar om den virtuella datorn.

1. I GRUB trycker du på E för att redigera det operativ system som du vill starta i. Operativ systemet visas vanligt vis på den första raden.
1. Hitta kernel-raden. I Azure börjar den med *linux16*.
1. I slutet av raden lägger du till *Rd. Break* i slutet av raden. Lämna ett blank steg mellan kernel-linjen och *Rd. Break*.

    Den här åtgärden avbryter start processen innan kontrollen skickas från `initramfs` till `systemd` , enligt beskrivningen i [Red Hat-dokumentationen](https://aka.ms/rhel7rootpassword).
1. Tryck på CTRL + X för att avsluta och starta om med de tillämpade inställningarna.

   När du har startat om, släpps du i nödfalls läge med ett skrivskyddat fil system.

1. I gränssnittet anger `mount -o remount,rw /sysroot` du för att montera om rot fil systemet med Läs-/Skriv behörighet.
1. När du har startat i enanvändarläge anger du `chroot /sysroot` för att växla till `sysroot` jailbrokad.
1. Du är nu i roten. Du kan återställa rot lösen ordet genom att ange `passwd` och sedan använda föregående instruktioner för att ange läget för en enskild användare.
1. När du är klar anger du `reboot -f` för att starta om.

![Animerad bild som visar ett kommando rads gränssnitt. Användaren väljer en server, letar upp slutet på kernel-raden och anger de angivna kommandona.](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-emergency-mount-no-root.gif)

> [!NOTE]
> Genom att följa anvisningarna ovan blir du i nödfalls gränssnittet så att du kan utföra åtgärder som till exempel redigering `fstab` . Vi föreslår dock vanligt vis att du återställer rot lösen ordet och använder det för att ange läget för en enskild användare.

## <a name="access-for-centos"></a>Åtkomst för CentOS
Till skillnad från Red Hat Enterprise Linux kräver enanvändarläge i CentOS GRUB och rot användaren är aktive rad.

### <a name="grub-access-in-centos"></a>GRUB-åtkomst i CentOS
CentOS levereras med GRUB aktive rad. Starta om den virtuella datorn genom att ange GRUB, `sudo reboot` och tryck sedan på valfri tangent. Den här åtgärden visar fönstret GRUB.

### <a name="single-user-mode-in-centos"></a>Läge för enkel användare i CentOS
Om du vill aktivera enanvändarläge i CentOS följer du de tidigare anvisningarna för RHEL.

## <a name="access-for-ubuntu"></a>Åtkomst för Ubuntu
Ubuntu-avbildningar kräver inte ett rot lösen ord. Om systemet startar i enanvändarläge kan du använda det utan ytterligare autentiseringsuppgifter.

### <a name="grub-access-in-ubuntu"></a>GRUB-åtkomst i Ubuntu
För att få åtkomst till GRUB trycker du på och håller ned ESC medan den virtuella datorn startas.

Som standard kan Ubuntu-bilder inte automatiskt visa fönstret GRUB. Du kan ändra inställningen genom att göra följande:
1. Öppna filen */etc/default/grub.d/50-cloudimg-Settings.cfg* i en text redigerare.

1. Ändra `GRUB_TIMEOUT` värdet till ett värde som inte är noll.
1. Öppna */etc/default/grub*i en text redigerare.
1. Kommentera ut `GRUB_HIDDEN_TIMEOUT=1` raden.
1. Se till att det finns en `GRUB_TIMEOUT_STYLE=menu` rad.
1. Kör `sudo update-grub`.

### <a name="single-user-mode-in-ubuntu"></a>Läge för enkel användare i Ubuntu
Om Ubuntu inte kan starta normalt hamnar du i enanvändarläge automatiskt. Om du vill ange enanvändarläge manuellt gör du följande:

1. I GRUB trycker du på E för att redigera start posten (Ubuntu-posten).
1. Leta efter raden som börjar med *Linux*och leta sedan efter *ro*.
1. Lägg till ett *enskilt* efter *ro*, och se till att det finns ett mellanslag före och efter *Single*.
1. Tryck på CTRL + X för att starta om med de här inställningarna och ange läge för enkel användare.

### <a name="use-grub-to-invoke-bash-in-ubuntu"></a>Använda GRUB för att anropa bash i Ubuntu
När du har provat föregående anvisningar kan det finnas en situation (till exempel ett bortglömt rot lösen ord) där du fortfarande inte kan komma åt enanvändarläge i din Ubuntu VM. Du kan också instruera kärnan att köra `/bin/bash` som init, i stället för system init. Den här åtgärden ger dig ett bash-gränssnitt och möjliggör system underhåll. Använd följande instruktioner:

1. I GRUB trycker du på E för att redigera start posten (Ubuntu-posten).

1. Leta efter raden som börjar med *Linux*och leta sedan efter *ro*.
1. Ersätt *ro* med *RW init =/bin/bash*.

    Den här åtgärden monterar fil systemet med Läs-och skriv åtgärder och använder `/bin/bash` som initierings process.
1. Tryck på CTRL + X för att starta om med de här inställningarna.

## <a name="access-for-coreos"></a>Åtkomst för kärnor
Enanvändarläge i enanvändarläge kräver att GRUB är aktiverat.

### <a name="grub-access-in-coreos"></a>GRUB åtkomst i Core
Om du vill komma åt GRUB trycker du på valfri tangent medan den virtuella datorn startas.

### <a name="single-user-mode-in-coreos"></a>Läge för enkel användare i Core
Om kärnan inte kan starta normalt hamnar du i enanvändarläge automatiskt. Om du vill ange enanvändarläge manuellt gör du följande:

1. I GRUB trycker du på E för att redigera start posten.

1. Leta efter raden som börjar med *Linux $*. Det måste finnas två instanser av raden, varje inkapslat i en annan *IF... Else* -sats.
1. Lägg till *Core. Autologin = ttyS0* i slutet av varje *Linux $* -rad.
1. Tryck på CTRL + X för att starta om med de här inställningarna och ange läge för enkel användare.

## <a name="access-for-suse-sles"></a>Åtkomst för SUSE SLES
Nyare avbildningar av SLES 12 SP3 + Tillåt åtkomst via serie konsolen om systemet startar i nödfalls läge.

### <a name="grub-access-in-suse-sles"></a>GRUB-åtkomst i SUSE SLES
GRUB-åtkomst i SLES kräver en start programs konfiguration via YaST. Skapa konfigurationen genom att göra följande:

1. Använd SSH för att logga in på din virtuella SLES-dator och kör sedan `sudo yast bootloader` . Tryck på TABB, tryck på RETUR och Använd sedan piltangenterna för att navigera i menyn.

1. Gå till **kernel-parametrar**och markera kryss rutan **Använd serie konsol** .
1. Lägg till `serial --unit=0 --speed=9600 --parity=no` i **konsol** argumenten.
1. Tryck på F10 för att spara inställningarna och avsluta.
1. Om du vill ange GRUB startar du om den virtuella datorn och trycker på valfri tangent under startsekvensen för att hålla fönstret GRUB visas.

    Standard tids gränsen för GRUB är **1**. Du kan ändra den här inställningen genom `GRUB_TIMEOUT` att ändra variabeln i */etc/default/grub* -filen.

![Initierar start program konfigurationen](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-sles-yast-grub-config.gif)

### <a name="single-user-mode-in-suse-sles"></a>Läge för enkel användare i SUSE SLES
Om SLES inte kan starta normalt släpps du automatiskt i nödfalls gränssnittet. Gör så här för att ange nödfalls gränssnittet manuellt:

1. I GRUB trycker du på E för att redigera start posten (SLES-posten).

1. Leta efter den kernel-linje som börjar med *Linux*.
1. Lägg till *systemed. Unit = nödfall. Target* till slutet av kernel-linjen.
1. Tryck på CTRL + X för att starta om med de här inställningarna och ange återställnings gränssnittet.

   > [!NOTE]
   > Den här åtgärden gör dig till nödfalls gränssnittet med ett skrivskyddat fil system. Om du vill redigera filer monterar du fil systemet med Läs-och Skriv behörighet. Det gör du genom att ange `mount -o remount,rw /` i gränssnittet.

## <a name="access-for-oracle-linux"></a>Åtkomst för Oracle Linux
På samma sätt som Red Hat Enterprise Linux är enanvändarläge i Oracle Linux GRUB och rot användaren måste vara aktive rad.

### <a name="grub-access-in-oracle-linux"></a>GRUB åtkomst i Oracle Linux
Oracle Linux levereras med GRUB aktive rad. Om du vill ange GRUB startar du om den virtuella datorn genom att köra `sudo reboot` och trycker sedan på ESC. Den här åtgärden visar fönstret GRUB. Om fönstret GRUB inte visas kontrollerar du att `GRUB_TERMINAL` radens värde innehåller en *serie konsol* (det vill säga `GRUB_TERMINAL="serial console"` ). Återskapa GRUB med `grub2-mkconfig -o /boot/grub/grub.cfg` .

### <a name="single-user-mode-in-oracle-linux"></a>Läge för enkel användare i Oracle Linux
Om du vill aktivera enanvändarläge i Oracle Linux följer du de tidigare anvisningarna för RHEL.

## <a name="next-steps"></a>Nästa steg
Mer information om serie konsolen finns i:
* [Dokumentation om Linux-serienummer](serial-console-linux.md)
* [Använd en serie konsol för att aktivera GRUB i olika distributioner](http://linuxonazure.azurewebsites.net/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/)
* [Använda en serie konsol för NMI-och SysRq-anrop](serial-console-nmi-sysrq.md)
* [Serie konsol för virtuella Windows-datorer](serial-console-windows.md)
* [Startdiagnostik](boot-diagnostics.md)
