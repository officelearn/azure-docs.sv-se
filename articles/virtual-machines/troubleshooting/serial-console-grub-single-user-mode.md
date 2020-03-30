---
title: Azure Serial Console för GRUB och enanvändarläge | Microsoft-dokument
description: I den här artikeln beskrivs hur du använder Serial Console för GRUB i virtuella Azure-datorer.
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
ms.openlocfilehash: 06cb3fe5d551ddfc95fcbd37cd9620adebd825c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "70883922"
---
# <a name="use-serial-console-to-access-grub-and-single-user-mode"></a>Använd seriell konsol för att komma åt GRUB- och enanvändarläge
GRand Unified Bootloader (GRUB) är förmodligen det första du ser när du startar en virtuell dator (VM). Eftersom det visas innan operativsystemet har startat är GRUB inte tillgängligt via SSH. I GRUB kan du ändra din startkonfiguration för att starta upp i enanvändarläge, bland annat.

Enanvändarläget är en minimal miljö med minimal funktionalitet. Det kan vara användbart för att undersöka startproblem, filsystemproblem eller nätverksproblem. Färre tjänster kan köras i bakgrunden och beroende på runlevel, ett filsystem kanske inte ens monteras automatiskt.

Enanvändarläget är också användbart i situationer där den virtuella datorn kan konfigureras för att bara acceptera SSH-nycklar för inloggning. I det här fallet kanske du kan använda enanvändarläge för att skapa ett konto med lösenordsautentisering. 

> [!NOTE]
> Med tjänsten Serial Console tillåts endast användare med *deltagarnivå* eller högre behörigheter för att komma åt seriell konsol för en virtuell dator.

Om du vill gå in i enanvändarläge anger du GRUB när den virtuella datorn startar och ändrar startkonfigurationen i GRUB. Se detaljerade instruktioner för att komma in grub i nästa avsnitt. I allmänhet, om den virtuella datorn har konfigurerats för att visa GRUB, kan du använda omstartsknappen i den virtuella datorns seriell konsol för att starta om den virtuella datorn och visa GRUB.

![Knappen Omstart av Linux-seriekonsolen](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-bar.png)

## <a name="general-grub-access"></a>Allmän GRUB-åtkomst
Om du vill komma åt GRUB startar du om den virtuella datorn medan fönstret Seriell konsol är öppet. Vissa distributioner kräver tangentbordsinmatning för att visa GRUB, och andra visar automatiskt GRUB i några sekunder för att tillåta användarinmatning av tangentbordet för att avbryta timeouten.

För att kunna komma åt enanvändarläge vill du se till att GRUB är aktiverat på din virtuella dator. Beroende på din distribution kan vissa installationsarbete vara nödvändiga för att säkerställa att GRUB är aktiverat. Distribuera specifik information finns i nästa avsnitt och vår [Support för Linux på Azure-sidan.](https://blogs.msdn.microsoft.com/linuxonazure/2018/10/23/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/)

### <a name="restart-your-vm-to-access-grub-in-serial-console"></a>Starta om den virtuella datorn för att komma åt GRUB i seriekonsolen
Du kan starta om den virtuella datorn i Seriekonsolen genom att hovra över knappen **Starta om** och sedan välja Starta om den **virtuella datorn**. Ett meddelande om omstarten visas längst ned i fönstret.

Du kan också starta om den virtuella datorn genom att köra ett SysRq "b"-kommando om [SysRq](./serial-console-nmi-sysrq.md) är aktiverat. Mer information om vad du kan förvänta dig av GRUB när du startar om finns i de distributionsspecifika instruktionerna i nästa avsnitt.

![Linux Serial Console omstart](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-ubuntu.gif)

## <a name="general-single-user-mode-access"></a>Allmän åtkomst i enanvändarläge
Du kan behöva manuell åtkomst till enanvändarläge när du inte har konfigurerat ett konto med lösenordsautentisering. Ändra GRUB-konfigurationen för att manuellt gå in i enanvändarläge. När du har gjort detta läser du avsnittet "Använd enanvändarläge för att återställa eller lägga till ett lösenord" för ytterligare instruktioner.

Om den virtuella datorn inte kan starta, kan distributioner ofta automatiskt släppa dig i enanvändarläge eller nödläge. Andra distributioner kräver dock ytterligare inställningar, till exempel att ställa in ett rotlösenord, innan de kan släppa dig i enanvändar- eller nödläge automatiskt.

### <a name="use-single-user-mode-to-reset-or-add-a-password"></a>Använda enanvändarläge för att återställa eller lägga till ett lösenord
När du har varit i enanvändarläge lägger du till en ny användare med sudo-behörigheter genom att göra följande:
1. Kör `useradd <username>` för att lägga till en användare.
1. Kör `sudo usermod -a -G sudo <username>` för att bevilja de nya användarrotprivilegier.
1. Används `passwd <username>` för att ange lösenordet för den nya användaren. Du kan sedan logga in som ny användare.


## <a name="access-for-red-hat-enterprise-linux-rhel"></a>Tillgång för Red Hat Enterprise Linux (RHEL)
Om RHEL inte kan starta normalt, tappar det dig i enanvändarläge automatiskt. Men om du inte har konfigurerat root-åtkomst för enanvändarläge har du inget rotlösenord och kan inte logga in. Det finns en lösning (se avsnittet "Ange enanvändarläge manuellt i RHEL", men vi föreslår att du konfigurerar root-åtkomst från början.

### <a name="grub-access-in-rhel"></a>GRUB-åtkomst i RHEL
RHEL levereras med GRUB aktiverat ur lådan. Om du vill ange GRUB `sudo reboot`startar du om den virtuella datorn genom att köra och trycker sedan på valfri tangent. GRUB-fönstret ska visas. Om det inte är det, se till att följande`/etc/default/grub`rader finns i din GRUB-fil ( ):

**För RHEL 8**

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
> Red Hat tillhandahåller också dokumentation för uppstart i räddningsläge, nödläge eller felsökningsläge och för återställning av rotlösenordet. Instruktioner finns i [Redigering av Terminal-menyn under uppstart](https://aka.ms/rhel7grubterminal).

### <a name="set-up-root-access-for-single-user-mode-in-rhel"></a>Konfigurera rotåtkomst för enanvändarläge i RHEL
Rotanvändaren är inaktiverad som standard. Enanvändarläge i RHEL kräver att rotanvändaren aktiveras. Om du behöver aktivera enanvändarläge använder du följande instruktioner:

1. Logga in på Red Hat-systemet via SSH.
1. Växla till rot.
1. Aktivera lösenordet för rotanvändaren genom att göra följande:
    * Kör `passwd root` (ange ett starkt rotlösenord).
1. Se till att rotanvändaren bara kan logga in via ttyS0 genom att göra följande:  
    a. Kör `edit /etc/ssh/sshd_config`och se till att PermitRootLogIn är inställt på `no`.  
    b. Kör `edit /etc/securetty file` för att tillåta inloggning endast via ttyS0.

Nu, om systemet startar i enanvändarläge, kan du logga in med rotlösenordet.

Alternativt, för RHEL 7.4+ eller 6.9+, för att aktivera enanvändarläge i GRUB-uppmaningarna, se [Starta i enanvändarläge](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/installation_guide/s1-rescuemode-booting-single).

### <a name="manually-enter-single-user-mode-in-rhel"></a>Ange enanvändarläge manuellt i RHEL
Om du har ställt in GRUB- och root-åtkomst med hjälp av föregående instruktioner kan du ange enanvändarläge genom att göra följande:

1. Om du vill ange GRUB trycker du på Esc när du startar om den virtuella datorn.
1. I GRUB trycker du på E för att redigera det operativsystem som du vill starta upp i. Operativsystemet visas vanligtvis på den första raden.
1. Hitta kärnlinjen. I Azure börjar det med *linux16*.
1. Tryck på Ctrl+E för att gå till slutet av raden.
1. I slutet av raden lägger du till *systemd.unit=rescue.target*.
    
    Den här åtgärden startar dig i enanvändarläge. Om du vill använda nödläge lägger du till *systemd.unit=emergency.target* i slutet av raden (i stället för *systemd.unit=rescue.target*).

1. Tryck på Ctrl+X för att avsluta och starta om med de tillämpade inställningarna.

   Du uppmanas att ange administratörslösenordet innan du kan ange enanvändarläge. Det här lösenordet är det som du skapade i föregående instruktioner.

    ![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-enter-emergency-shell.gif)

### <a name="enter-single-user-mode-without-root-account-enabled-in-rhel"></a>Ange enanvändarläge utan rotkonto aktiverat i RHEL
Om du inte har aktiverat rotanvändaren genom att följa de tidigare instruktionerna kan du fortfarande återställa rotlösenordet genom att göra följande:

> [!NOTE]
> Om du använder SELinux ska du följa de ytterligare steg som beskrivs i [Red Hat-dokumentationen](https://aka.ms/rhel7grubterminal)när du återställer rotlösenordet .

1. Om du vill ange GRUB trycker du på Esc när du startar om den virtuella datorn.

1. I GRUB trycker du på E för att redigera det operativsystem som du vill starta upp i. Operativsystemet visas vanligtvis på den första raden.
1. Hitta kärnlinjen. I Azure börjar det med *linux16*.
1. I slutet av raden lägger du till *rd.break* i slutet av raden. Lämna ett mellanslag mellan kärnlinjen och *rd.break*.

    Den här åtgärden avbryter startprocessen `initramfs` innan `systemd`kontrollen skickas från till , enligt beskrivningen i [Red Hat-dokumentationen](https://aka.ms/rhel7rootpassword).
1. Tryck på Ctrl+X för att avsluta och starta om med de tillämpade inställningarna.

   När du har startat om hamnar du i nödläge med ett skrivskyddat filsystem. 
   
1. I skalet `mount -o remount,rw /sysroot` anger du att rotfilsystemet ska återmonteras med läs-/skrivbehörighet.
1. När du har startat upp `chroot /sysroot` i enanvändarläge går du in för att byta till fängelset. `sysroot`
1. Du är nu rotad. Du kan återställa ditt rotlösenord genom att ange `passwd` och sedan använda föregående instruktioner för att gå in i enanvändarläge. 
1. När du är klar `reboot -f` anger du att starta om.

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-emergency-mount-no-root.gif)

> [!NOTE]
> Om du går igenom ovanstående instruktioner hamnar du i nödskalet så `fstab`att du även kan utföra uppgifter som redigering . Vi föreslår dock vanligtvis att du återställer ditt rotlösenord och använder det för att gå in i enanvändarläge.

## <a name="access-for-centos"></a>Tillgång för CentOS
Ungefär som Red Hat Enterprise Linux kräver enanvändarläget i CentOS GRUB och rotanvändaren aktiveras.

### <a name="grub-access-in-centos"></a>GRUB-åtkomst i CentOS
CentOS levereras med GRUB aktiverat ur lådan. Om du vill ange GRUB `sudo reboot`startar du om den virtuella datorn genom att ange och trycker sedan på valfri tangent. Den här åtgärden visar GRUB-fönstret.

### <a name="single-user-mode-in-centos"></a>Enanvändarläge i CentOS
Om du vill aktivera enanvändarläge i CentOS följer du de tidigare instruktionerna för RHEL.

## <a name="access-for-ubuntu"></a>Tillgång för Ubuntu
Ubuntu-bilder kräver inte ett root-lösenord. Om systemet startar i enanvändarläge kan du använda det utan ytterligare autentiseringsuppgifter.

### <a name="grub-access-in-ubuntu"></a>GRUB-åtkomst i Ubuntu
Om du vill komma åt GRUB håller du ned Esc medan den virtuella datorn startar.

Som standard kanske Ubuntu-bilder inte automatiskt visar GRUB-fönstret. Du kan ändra inställningen genom att göra följande:
1. Öppna filen */etc/default/grub.d/50-cloudimg-settings.cfg* i en textredigerare.

1. Ändra `GRUB_TIMEOUT` värdet till ett värde som inte är noll.
1. Öppna */etc/default/grub*i en textredigerare.
1. Kommentera ut `GRUB_HIDDEN_TIMEOUT=1` linjen.
1. Se till att `GRUB_TIMEOUT_STYLE=menu` det finns en linje.
1. Kör `sudo update-grub`.

### <a name="single-user-mode-in-ubuntu"></a>Enanvändarläge i Ubuntu
Om Ubuntu inte kan starta normalt, tappar det dig i enanvändarläge automatiskt. Så här går du in i enanvändarläge manuellt:

1. I GRUB trycker du på E för att redigera startposten (Ubuntu-posten).
1. Leta efter den linje som börjar med *Linux*, och sedan leta efter *ro*.
1. Lägg *till en gång* efter *ro*, se till att det finns ett utrymme före och efter *enstaka*.
1. Tryck på Ctrl+X för att starta om med dessa inställningar och gå in i enanvändarläge.

### <a name="use-grub-to-invoke-bash-in-ubuntu"></a>Använd GRUB att åberopa bash i Ubuntu
När du har provat föregående instruktioner kan det finnas en situation (till exempel ett glömt rotlösenord) där du fortfarande inte kan komma åt enanvändarläge i din Ubuntu-virtuella dator. Du kan också tala `/bin/bash` om för kärnan att köras som init, snarare än systemet init. Denna åtgärd ger dig en bash skal och möjliggör systemunderhåll. Använd följande instruktioner:

1. I GRUB trycker du på E för att redigera startposten (Ubuntu-posten).

1. Leta efter den linje som börjar med *Linux*, och sedan leta efter *ro*.
1. Byt *ut ro* mot *rw init=/bin/bash*.

    Den här åtgärden monterar filsystemet som `/bin/bash` läs-skriv och använder som init-process.
1. Tryck på Ctrl+X för att starta om med dessa inställningar.

## <a name="access-for-coreos"></a>Åtkomst för CoreOS
Enanvändarläge i CoreOS kräver att GRUB aktiveras.

### <a name="grub-access-in-coreos"></a>GRUB-åtkomst i CoreOS
För att komma åt GRUB, tryck på valfri tangent medan din virtuella dator startar.

### <a name="single-user-mode-in-coreos"></a>Enanvändarläge i CoreOS
Om CoreOS inte kan starta normalt, tappar det dig i enanvändarläge automatiskt. Så här går du in i enanvändarläge manuellt:

1. I GRUB trycker du på E för att redigera startposten.

1. Leta efter den linje som börjar med *linux $*. Det bör finnas två instanser av linjen, varje inkapslad i en annan *om ... annars* klausul.
1. Lägg *till coreos.autologin=ttyS0* till slutet av varje *linux$-linje.*
1. Tryck på Ctrl+X för att starta om med dessa inställningar och gå in i enanvändarläge.

## <a name="access-for-suse-sles"></a>Åtkomst för SUSE SLES
Nyare bilder av SLES 12 SP3+ ger åtkomst via seriekonsolen om systemet startar i nödläge.

### <a name="grub-access-in-suse-sles"></a>GRUB-åtkomst i SUSE SLES
GRUB-åtkomst i SLES kräver en bootloader-konfiguration via YaST. Så här skapar du konfigurationen:

1. Använd SSH för att logga in på `sudo yast bootloader`din SLES VM och kör sedan . Tryck på Tabb, tryck på Retur och använd sedan piltangenterna för att navigera genom menyn.

1. Gå till **Kernel-parametrar**och markera sedan kryssrutan **Använd seriell konsol.**
1. Lägg `serial --unit=0 --speed=9600 --parity=no` till i **konsolargumenten.**
1. Tryck på F10 för att spara inställningarna och avsluta.
1. För att komma in grub, starta om din virtuella dator och tryck på valfri tangent under startsekvensen för att hålla GRUB-fönstret visas.

    Standardtidsgränsen för GRUB är **1s**. Du kan ändra den `GRUB_TIMEOUT` här inställningen genom att ändra variabeln i filen */etc/default/grub.*

![Initiera starthanterarens konfiguration](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-sles-yast-grub-config.gif)

### <a name="single-user-mode-in-suse-sles"></a>Enanvändarläge i SUSE SLES
Om SLES inte kan starta normalt, släpps du automatiskt in i nödskanlet. Så här anger du nödskalet manuellt:

1. I GRUB trycker du på E för att redigera startposten (SLES-posten).

1. Leta efter kärnan linje som börjar med *Linux*.
1. Lägg till *systemd.unit=emergency.target* till slutet av kärnraden.
1. Tryck på Ctrl+X för att starta om med dessa inställningar och ange nödskalet.

   > [!NOTE]
   > Den här åtgärden släpper in dig i nödskalet med ett skrivskyddat filsystem. Om du vill redigera filer återmonterar du filsystemet med läs-skrivbehörighet. För att göra `mount -o remount,rw /` det, ange i skalet.

## <a name="access-for-oracle-linux"></a>Åtkomst för Oracle Linux
Ungefär som Red Hat Enterprise Linux kräver enanvändarläget i Oracle Linux GRUB och rotanvändaren aktiveras.

### <a name="grub-access-in-oracle-linux"></a>GRUB-åtkomst i Oracle Linux
Oracle Linux levereras med GRUB aktiverat ur lådan. Om du vill ange GRUB `sudo reboot`startar du om den virtuella datorn genom att köra och trycker sedan på Esc. Den här åtgärden visar GRUB-fönstret. Om GRUB-fönstret inte visas kontrollerar du att `GRUB_TERMINAL` linjens värde innehåller *seriell konsol* (d.v.s. `GRUB_TERMINAL="serial console"`). Bygg om `grub2-mkconfig -o /boot/grub/grub.cfg`GRUB med .

### <a name="single-user-mode-in-oracle-linux"></a>Enanvändarläge i Oracle Linux
Om du vill aktivera enanvändarläge i Oracle Linux följer du de tidigare instruktionerna för RHEL.

## <a name="next-steps"></a>Nästa steg
Mer information om Serial Console finns i:
* [Dokumentation för Linux Serial Console](serial-console-linux.md)
* [Använd Serial Console för att aktivera GRUB i olika distributioner](https://blogs.msdn.microsoft.com/linuxonazure/2018/10/23/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/)
* [Använda serialkonsol för NMI- och SysRq-samtal](serial-console-nmi-sysrq.md)
* [Seriekonsol för virtuella Windows-datorer](serial-console-windows.md)
* [Startdiagnostik](boot-diagnostics.md)
