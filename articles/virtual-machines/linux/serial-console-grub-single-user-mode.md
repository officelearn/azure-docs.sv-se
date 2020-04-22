---
title: Azure Serial Console för GRUB- och enanvändarläge
description: Använda seriekonsol för grub i virtuella Azure-datorer.
services: virtual-machines-linux
author: asinn826
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure-services
ms.date: 08/14/2018
ms.author: alsin
ms.openlocfilehash: 2aa7110ab4e52fdc5c3804bd27be5f41081fb435
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758501"
---
# <a name="use-serial-console-to-access-grub-and-single-user-mode"></a>Använda seriekonsolen för att komma åt GRUB och enanvändarläge
GRUB är GRand Unified Bootloader. Från GRUB kan du ändra din startkonfiguration för att starta upp till enanvändarläge, bland annat.

Enanvändarläget är en minimal miljö med minimal funktionalitet. Det kan vara användbart för att undersöka startproblem, filsystemproblem eller nätverksproblem. Färre tjänster kan köras i bakgrunden, och beroende på runlevel kan det hända att ett filsystem inte ens monteras automatiskt.

Enanvändarläget är också användbart i situationer där den virtuella datorn endast kan konfigureras för att acceptera SSH-nycklar för att logga in. I det här fallet kanske du kan använda enanvändarläge för att skapa ett konto med lösenordsautentisering.

För att gå in i enanvändarläge måste du ange GRUB när den virtuella datorn startar upp och ändrar startkonfigurationen i GRUB. Detta kan göras med den virtuella datorns seriekonsol.

## <a name="general-grub-access"></a>Allmän GRUB-åtkomst
För att komma åt GRUB måste du starta om den virtuella datorn samtidigt som seriekonsolbladet är öppet. Vissa distributioner kräver tangentbordsinmatning för att visa GRUB, medan andra automatiskt visar GRUB i några sekunder och tillåter användarens tangentbordsinmatning för att avbryta timeouten. 

Du kommer att vilja se till att GRUB är aktiverat på din virtuella dator för att kunna komma åt enanvändarläge. Beroende på din distribution kan det finnas en del installationsarbete för att säkerställa att GRUB är aktiverat. Distro-specifik information finns nedan.

### <a name="reboot-your-vm-to-access-grub-in-serial-console"></a>Starta om den virtuella datorn för att komma åt GRUB i seriekonsolen
Starta om den virtuella datorn med det seriella konsolbladet öppet kan göras med ett SysRq-kommando `'b'` om [SysRq](./serial-console-nmi-sysrq.md) är aktiverat, eller genom att klicka på knappen Starta om i översiktsbladet (öppna den virtuella datorn i en ny webbläsarflik för att starta om utan att stänga seriekonsolbladet). Följ de distro-specifika instruktionerna nedan för att lära dig vad som väntar från GRUB när du startar om.

## <a name="general-single-user-mode-access"></a>Allmän åtkomst i enanvändarläge
Manuell åtkomst till enanvändarläge kan behövas i situationer där du inte har konfigurerat ett konto med lösenordsautentisering. Du måste ändra GRUB-konfigurationen för att manuellt gå in i enanvändarläge. När du har gjort detta läser du Använda enanvändarläge för att återställa eller lägga till ett lösenord för ytterligare instruktioner.

I de fall den virtuella datorn inte kan starta, kommer distributioner ofta automatiskt släppa dig i enanvändarläge eller nödläge. Andra kräver dock ytterligare inställningar innan de kan släppa dig i enanvändar- eller nödläge automatiskt (till exempel ställa in ett rotlösenord).

### <a name="use-single-user-mode-to-reset-or-add-a-password"></a>Använda enanvändarläge för att återställa eller lägga till ett lösenord
När du är i enanvändarläge gör du följande för att lägga till en ny användare med sudo-behörighet:
1. Kör `useradd <username>` för att lägga till en användare
1. Kör `sudo usermod -a -G sudo <username>` för att bevilja de nya användarrotprivilegier
1. Används `passwd <username>` för att ange lösenordet för den nya användaren. Då kan du logga in som ny användare

## <a name="access-for-red-hat-enterprise-linux-rhel"></a>Tillgång för Red Hat Enterprise Linux (RHEL)
RHEL kommer att släppa dig i enanvändarläge automatiskt om det inte kan starta normalt. Men om du inte har ställt in root-åtkomst för enanvändarläge har du inget rotlösenord och kommer inte att kunna logga in. Det finns en lösning (Se "Manuellt in i enanvändarläge" nedan), men förslaget är att ställa in root-åtkomst från början.

### <a name="grub-access-in-rhel"></a>GRUB-åtkomst i RHEL
RHEL levereras med GRUB aktiverat ur lådan. För att komma in GRUB, starta om din virtuella dator med `sudo reboot` och tryck på valfri tangent. Du kommer att se GRUB skärmen dyker upp.

> Red Hat tillhandahåller också dokumentation för uppstart i räddningsläge, nödläge, felsökningsläge och återställning av rotlösenordet. [Klicka här för att komma till den](https://aka.ms/rhel7grubterminal).

### <a name="set-up-root-access-for-single-user-mode-in-rhel"></a>Konfigurera rotåtkomst för enanvändarläge i RHEL
Enanvändarläge i RHEL kräver att rotanvändaren är aktiverad, vilket är inaktiverat som standard. Om du behöver aktivera enanvändarläge använder du följande instruktioner:

1. Logga in på Red Hat-systemet via SSH
1. Växla till rot
1. Aktivera lösenord för rotanvändare 
    * `passwd root`(ange ett starkt rotlösenord)
1. Se till att rotanvändaren bara kan logga in via ttyS0
    * `edit /etc/ssh/sshd_config`och se till att PermitRootLogIn är inställt på
    * `edit /etc/securetty file`att endast tillåta inloggning via ttyS0 

Nu om systemet startar i enanvändarläge kan du logga in via root lösenord.

Alternativt för RHEL 7.4+ eller 6.9+ kan du aktivera enanvändarläge i GRUB-anvisningarna, se instruktionerna [här](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/installation_guide/s1-rescuemode-booting-single)

### <a name="manually-enter-single-user-mode-in-rhel"></a>Ange manuellt enanvändarläge i RHEL
Om du har ställt in GRUB och root-åtkomst med instruktionerna ovan kan du gå in i enanvändarläge med följande instruktioner:

1. Tryck på "Esc" medan du startar om den virtuella datorn för att komma in grub
1. I GRUB trycker du på "e" för att redigera det valda operativsystemet som du vill starta upp (vanligtvis den första raden)
1. Hitta kärnlinjen - i Azure börjar detta med`linux16`
1. Tryck på Ctrl + E för att gå till slutet av raden
1. Lägg till följande i slutet av raden:`systemd.unit=rescue.target`
    * Detta kommer att starta dig i enanvändarläge. Om du vill använda nödläge lägger du `systemd.unit=emergency.target` till i slutet av raden i stället för`systemd.unit=rescue.target`
1. Tryck på Ctrl + X för att avsluta och starta om med de tillämpade inställningarna
1. Du kommer att bli tillfrågad om administratörslösenordet innan du kan gå in i enanvändarläge - det här är samma lösenord som du skapade i instruktionerna ovan    

    ![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-enter-emergency-shell.gif)

### <a name="enter-single-user-mode-without-root-account-enabled-in-rhel"></a>Ange enanvändarläge utan rotkonto aktiverat i RHEL
Om du inte gick igenom stegen ovan för att aktivera rotanvändaren kan du fortfarande återställa rotlösenordet. Använd följande instruktioner:

> Obs: Om du använder SELinux, se till att du har vidtagit ytterligare åtgärder som beskrivs i Red Hat-dokumentationen [här](https://aka.ms/rhel7grubterminal) när du återställer rotlösenordet.

1. Tryck på "Esc" medan du startar om den virtuella datorn för att komma in grub
1. I GRUB trycker du på "e" för att redigera det valda operativsystemet som du vill starta upp (vanligtvis den första raden)
1. Hitta kärnlinjen - i Azure börjar detta med`linux16`
1. Lägg `rd.break` till i slutet av raden, se `rd.break` till att det finns ett utrymme innan (se exempel nedan)
    - Detta kommer att avbryta startprocessen `initramfs` innan `systemd`kontrollen skickas från till , enligt beskrivningen i Red Hat-dokumentationen [här](https://aka.ms/rhel7rootpassword).
1. Tryck på Ctrl + X för att avsluta och starta om med de tillämpade inställningarna
1. När du startar, kommer du att släppas i nödläge med en skrivskyddad filsystem. Gå `mount -o remount,rw /sysroot` in i skalet för att återmontera rotfilsystemet med läs-/skrivbehörighet
1. När du startar i enanvändarläge skriver du in `chroot /sysroot` för att byta till `sysroot` fängelset
1. Du är nu rot. Du kan återställa ditt `passwd` rotlösenord med och sedan använda instruktionerna ovan för att gå in i enanvändarläge. Skriv `reboot -f` om du vill starta om när du är klar.

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-emergency-mount-no-root.gif)

> Om du går igenom instruktionerna ovan hamnar du i nödskal, så `fstab`att du även kan utföra uppgifter som redigering . Det allmänt accepterade förslaget är dock att återställa ditt rotlösenord och använda det för att gå in i enanvändarläge. 


## <a name="access-for-centos"></a>Tillgång för CentOS
Ungefär som Red Hat Enterprise Linux kräver single user-läget i CentOS GRUB och rotanvändaren aktiveras. 

### <a name="grub-access-in-centos"></a>GRUB-åtkomst i CentOS
CentOS levereras med GRUB aktiverat ur lådan. För att komma in GRUB, starta om din virtuella dator med `sudo reboot` och tryck på valfri tangent. Du kommer att se GRUB skärmen dyker upp.

### <a name="single-user-mode-in-centos"></a>Enanvändarläge i CentOS
Följ instruktionerna för RHEL ovan för att aktivera enanvändarläge i CentOS.

## <a name="access-for-ubuntu"></a>Tillgång för Ubuntu 
Ubuntu-bilder kräver inget root-lösenord. Om systemet startar i enanvändarläge kan du använda det utan ytterligare autentiseringsuppgifter. 

### <a name="grub-access-in-ubuntu"></a>GRUB-åtkomst i Ubuntu
För att komma åt GRUB, tryck och håll "Esc" medan den virtuella datorn startar upp. 

Som standard visar Ubuntu-bilder inte grub-skärmen automatiskt. Detta kan ändras med följande instruktioner:
1. Öppna `/etc/default/grub.d/50-cloudimg-settings.cfg` i valfri textredigerare
1. Ändra `GRUB_TIMEOUT` värdet till ett värde som inte är noll
1. Öppna `/etc/default/grub` i valfri textredigerare
1. Kommentera ut `GRUB_HIDDEN_TIMEOUT=1` raden
1. Kör `sudo update-grub`

### <a name="single-user-mode-in-ubuntu"></a>Enanvändarläge i Ubuntu
Ubuntu kommer att släppa dig i enanvändarläge automatiskt om det inte kan starta normalt. Om du vill gå in i enanvändarläge manuellt använder du följande instruktioner:

1. Från GRUB trycker du på "e" för att redigera startposten (Ubuntu-posten)
1. Leta efter den linje `linux`som börjar med och leta sedan efter`ro`
1. Lägg `single` `ro`till efter , se till att det finns ett utrymme före och efter`single`
1. Tryck på Ctrl + X för att starta om med dessa inställningar och gå in i enanvändarläge

## <a name="access-for-coreos"></a>Åtkomst för CoreOS
Enanvändarläget i CoreOS kräver att GRUB aktiveras. 

### <a name="grub-access-in-coreos"></a>GRUB-åtkomst i CoreOS
För att komma åt GRUB, tryck på valfri tangent när din virtuella dator startar upp.

### <a name="single-user-mode-in-coreos"></a>Enanvändarläge i CoreOS
CoreOS kommer att släppa dig i enanvändarläge automatiskt om det inte kan starta normalt. Om du vill gå in i enanvändarläge manuellt använder du följande instruktioner:
1. Från GRUB trycker du på "e" för att redigera startposten
1. Leta efter den linje `linux$`som börjar med . Det bör finnas 2, inkapslade i olika om/annars-klausuler
1. Lägg `coreos.autologin=ttyS0` till i slutet `linux$` av båda raderna
1. Tryck på Ctrl + X för att starta om med dessa inställningar och gå in i enanvändarläge

## <a name="access-for-suse-sles"></a>Åtkomst för SUSE SLES
Nyare bilder av SLES 12 SP3+ ger åtkomst via seriekonsolen om systemet startar i nödläge. 

### <a name="grub-access-in-suse-sles"></a>GRUB-åtkomst i SUSE SLES
GRUB-åtkomst i SLES kräver bootloader-konfiguration via YaST. För att göra detta, följ dessa instruktioner:

1. ssh i din SLES `sudo yast bootloader`VM och kör . Använd `tab` tangenterna, `enter` tangenterna och piltangenterna för att navigera genom menyn. 
1. Navigera `Kernel Parameters`till och `Use serial console`kontrollera . 
1. Lägg `serial --unit=0 --speed=9600 --parity=no` till i konsolargumenten

1. Tryck på F10 för att spara inställningarna och avsluta
1. För att komma in GRUB, starta om din VM och tryck på valfri tangent under uppstartssekvensen för att göra GRUB stanna på skärmen
    - Standardtidsgränsen för GRUB är 1:a. Du kan ändra detta `GRUB_TIMEOUT` genom att ändra variabeln i`/etc/default/grub`

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-sles-yast-grub-config.gif)

### <a name="single-user-mode-in-suse-sles"></a>Enanvändarläge i SUSE SLES
Du kommer automatiskt att släppas i nödskal om SLES inte kan starta normalt. Använd följande instruktioner om du vill komma in manuellt i nödskalet:

1. Från GRUB trycker du på "e" för att redigera startposten (SLES-posten)
1. Leta efter kärnlinjen det börjar med`linux`
1. Lägg `systemd.unit=emergency.target` till i slutet av raden
1. Tryck på Ctrl + X för att starta om med dessa inställningar och ange nödskal
   > Observera att du kommer att släppas i nödskal med ett _skrivskyddat_ filsystem. Om du vill göra några ändringar i några filer måste du montera om filsystemet med läs-skrivbehörighet. För att göra `mount -o remount,rw /` detta, gå in i skalet

## <a name="access-for-oracle-linux"></a>Åtkomst för Oracle Linux
Ungefär som Red Hat Enterprise Linux, kräver enanvändarläge i Oracle Linux GRUB och rotanvändaren aktiveras. 

### <a name="grub-access-in-oracle-linux"></a>GRUB-åtkomst i Oracle Linux
Oracle Linux levereras med GRUB aktiverat ur lådan. För att komma in GRUB, starta om din virtuella dator med `sudo reboot` och tryck på "Esc". Du kommer att se GRUB skärmen dyker upp.

### <a name="single-user-mode-in-oracle-linux"></a>Enanvändarläge i Oracle Linux
Följ instruktionerna för RHEL ovan för att aktivera enanvändarläge i Oracle Linux.

## <a name="next-steps"></a>Nästa steg
* De viktigaste seriell konsol Linux dokumentationssida finns [här](serial-console.md).
* Använda serialkonsol för [NMI- och SysRq-samtal](serial-console-nmi-sysrq.md)
* Seriekonsolen är också tillgänglig för [virtuella Windows-datorer](../windows/serial-console.md)
* Läs mer om [startdiagnostik](boot-diagnostics.md)
