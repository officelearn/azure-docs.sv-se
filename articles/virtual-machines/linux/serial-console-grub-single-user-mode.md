---
title: Azures serie konsol för GRUB och enanvändarläge
description: Använda en serie konsol för grub i virtuella Azure-datorer.
services: virtual-machines-linux
author: asinn826
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure-services
ms.date: 08/14/2018
ms.author: alsin
ms.openlocfilehash: 2aa7110ab4e52fdc5c3804bd27be5f41081fb435
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "81758501"
---
# <a name="use-serial-console-to-access-grub-and-single-user-mode"></a>Använda seriekonsolen för att komma åt GRUB och enanvändarläge
GRUB är GRand Unified-startprogrammet. Från GRUB kan du ändra start konfigurationen till att starta i enanvändarläge, bland annat.

Enanvändarläge är en minimal miljö med minimal funktionalitet. Det kan vara användbart för att undersöka start problem, fil Systems problem eller nätverks problem. Färre tjänster kan köras i bakgrunden och, beroende på runlevel, kan ett fil system inte ens monteras automatiskt.

Enanvändarläge är också användbart i situationer där din virtuella dator bara kan konfigureras för att godkänna SSH-nycklar för inloggning. I det här fallet kanske du kan använda enanvändarläge för att skapa ett konto med lösenordsautentisering.

Om du vill ange enanvändarläge måste du ange GRUB när den virtuella datorn startas och ändra start konfigurationen i GRUB. Detta kan göras med den virtuella datorns serie konsol.

## <a name="general-grub-access"></a>Allmän GRUB-åtkomst
För att få åtkomst till GRUB måste du starta om den virtuella datorn och låta serie konsol bladet vara öppet. Vissa distributioner kräver tangent bords indata för att Visa GRUB, medan andra automatiskt kommer att Visa GRUB under några sekunder och tillåta att användaren anger indata från tangent bordet för att avbryta tids gränsen. 

Du måste se till att GRUB har Aktiver ATS på den virtuella datorn för att kunna komma åt ett enskilt användarläge. Beroende på din distribution kan det hända att vissa inställningar fungerar för att säkerställa att GRUB är aktiverat. Distribution information finns nedan.

### <a name="reboot-your-vm-to-access-grub-in-serial-console"></a>Starta om den virtuella datorn för att få åtkomst till GRUB i serie konsolen
Det går att starta om den virtuella datorn med det öppna bladet för serie konsolen genom att använda ett SysRq `'b'` -kommando om [SysRq](./serial-console-nmi-sysrq.md) är aktiverat eller genom att klicka på knappen starta om i översikts bladet (öppna den virtuella datorn i en ny flik i webbläsaren om du vill starta om utan att stänga bladet för serie konsolen). Följ de distribution anvisningarna nedan om du vill veta vad du kan förväntar dig från GRUB när du startar om.

## <a name="general-single-user-mode-access"></a>Åtkomst till läget allmän enkel användare
Det kan krävas manuell åtkomst till enanvändarläge i situationer där du inte har konfigurerat ett konto med lösenordsautentisering. Du måste ändra GRUB-konfigurationen för att manuellt ange enanvändarläge. När du har gjort detta läser du Använd enanvändarläge för att återställa eller lägga till ett lösen ord för ytterligare instruktioner.

I de fall där den virtuella datorn inte kan starta, kommer distributioner ofta att släppa dig i enanvändarläge eller i nödfalls läge. Andra kräver dock ytterligare konfiguration innan de kan släppa dig i enanvändarläge eller nöd läge automatiskt (till exempel att konfigurera ett rot lösen ord).

### <a name="use-single-user-mode-to-reset-or-add-a-password"></a>Använd enanvändarläge för att återställa eller lägga till ett lösen ord
När du är i enanvändarläge gör du följande för att lägga till en ny användare med sudo-behörigheter:
1. Kör `useradd <username>` för att lägga till en användare
1. Kör `sudo usermod -a -G sudo <username>` för att ge den nya användar rot behörigheten
1. Används `passwd <username>` för att ange lösen ordet för den nya användaren. Du kommer sedan att kunna logga in som den nya användaren

## <a name="access-for-red-hat-enterprise-linux-rhel"></a>Åtkomst för Red Hat Enterprise Linux (RHEL)
RHEL kommer att släppa dig i enanvändarläge automatiskt om det inte går att starta normalt. Men om du inte har konfigurerat rot åtkomst för enanvändarläge, kommer du inte att ha ett rot lösen ord och kommer inte att kunna logga in. Det finns en lösning (se "ange manuellt läge för enkel användare" nedan), men förslaget är att konfigurera rot åtkomst initialt.

### <a name="grub-access-in-rhel"></a>GRUB-åtkomst i RHEL
RHEL levereras med GRUB aktive rad. Starta om den virtuella datorn med `sudo reboot` och tryck på valfri tangent för att ange grub. Skärmen GRUB visas.

> Obs! Red Hat innehåller också dokumentation för att starta i räddnings läge, nöd läge, fel söknings läge och återställa rot lösen ordet. [Klicka här för att komma åt den](https://aka.ms/rhel7grubterminal).

### <a name="set-up-root-access-for-single-user-mode-in-rhel"></a>Konfigurera rot åtkomst för ett enskilt användarläge i RHEL
Single-User-läget i RHEL kräver att rot användaren är aktive rad, vilket är inaktiverat som standard. Om du behöver aktivera enanvändarläge använder du följande instruktioner:

1. Logga in på Red Hat-systemet via SSH
1. Växla till rot
1. Aktivera lösen ord för rot användare 
    * `passwd root`(ange ett starkt rot lösen ord)
1. Se till att rot användaren bara kan logga in via ttyS0
    * `edit /etc/ssh/sshd_config`och se till att PermitRootLogIn är inställt på Nej
    * `edit /etc/securetty file`för att endast tillåta inloggning via ttyS0 

Nu när systemet startas i enanvändarläge kan du logga in via rot lösen ordet.

Alternativt för RHEL 7.4 + eller 6,9 + du kan aktivera enanvändarläge i GRUB-prompten, se anvisningarna [här](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/installation_guide/s1-rescuemode-booting-single)

### <a name="manually-enter-single-user-mode-in-rhel"></a>Ange ett enskilt användarläge manuellt i RHEL
Om du har konfigurerat GRUB och rot åtkomst med instruktionerna ovan kan du ange enanvändarläge med följande instruktioner:

1. Tryck på ESC när du startar om den virtuella datorn för att ange GRUB
1. I GRUB trycker du på "e" för att redigera det valda operativ systemet som du vill starta i (vanligt vis den första raden)
1. Hitta kernel-linjen – i Azure kommer detta att börja med`linux16`
1. Tryck på CTRL + E för att gå till slutet av raden
1. Lägg till följande i slutet av raden:`systemd.unit=rescue.target`
    * Detta kommer att starta dig i enanvändarläge. Om du vill använda nöd läget lägger du till `systemd.unit=emergency.target` i slutet av raden i stället för`systemd.unit=rescue.target`
1. Tryck på CTRL + X för att avsluta och starta om med de tillämpade inställningarna
1. Du uppmanas att ange administratörs lösen ord innan du kan ange enanvändarläge. det här är samma lösen ord som du skapade i anvisningarna ovan    

    ![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-enter-emergency-shell.gif)

### <a name="enter-single-user-mode-without-root-account-enabled-in-rhel"></a>Ange ett enskilt användarläge utan att rot kontot har Aktiver ATS i RHEL
Om du inte går igenom stegen ovan för att aktivera rot användaren kan du fortfarande återställa rot lösen ordet. Använd följande instruktioner:

> Obs! Om du använder SELinux bör du se till att du har vidtagit de ytterligare stegen som beskrivs i Red Hat-dokumentationen [här](https://aka.ms/rhel7grubterminal) när du återställer rot lösen ordet.

1. Tryck på ESC när du startar om den virtuella datorn för att ange GRUB
1. I GRUB trycker du på "e" för att redigera det valda operativ systemet som du vill starta i (vanligt vis den första raden)
1. Hitta kernel-linjen – i Azure kommer detta att börja med`linux16`
1. Lägg till `rd.break` i slutet av raden och se till att det finns ett blank steg före `rd.break` (se exemplet nedan)
    - Detta avbryter start processen innan kontrollen skickas från `initramfs` till `systemd` , enligt beskrivningen i Red Hat-dokumentationen [här](https://aka.ms/rhel7rootpassword).
1. Tryck på CTRL + X för att avsluta och starta om med de tillämpade inställningarna
1. När du har startat kommer du att släppas i nödfalls läge med ett skrivskyddat fil system. Sätt in `mount -o remount,rw /sysroot` i gränssnittet för att montera om rot fil systemet med Läs-/Skriv behörighet
1. När du startar i enanvändarläge skriver du in `chroot /sysroot` för att växla till `sysroot` jailbrokad
1. Du är nu rot. Du kan återställa rot lösen ordet med `passwd` och sedan använda anvisningarna ovan för att ange enanvändarläge. Skriv `reboot -f` för att starta om när du är klar.

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-emergency-mount-no-root.gif)

> Obs! genom att följa anvisningarna ovan går du till nödsamtal, så att du kan utföra uppgifter som till exempel redigering `fstab` . Men det allmänt accepterade förslaget är att återställa rot lösen ordet och använda det för att ange enanvändarläge. 


## <a name="access-for-centos"></a>Åtkomst för CentOS
I likhet med Red Hat Enterprise Linux kräver enanvändarläge i CentOS GRUB och rot användaren är aktive rad. 

### <a name="grub-access-in-centos"></a>GRUB-åtkomst i CentOS
CentOS levereras med GRUB aktive rad. Starta om den virtuella datorn med `sudo reboot` och tryck på valfri tangent för att ange grub. Skärmen GRUB visas.

### <a name="single-user-mode-in-centos"></a>Läge för enkel användare i CentOS
Följ anvisningarna för RHEL ovan för att aktivera enanvändarläge i CentOS.

## <a name="access-for-ubuntu"></a>Åtkomst för Ubuntu 
Ubuntu-avbildningar kräver inte ett rot lösen ord. Om systemet startar i enanvändarläge kan du använda det utan ytterligare autentiseringsuppgifter. 

### <a name="grub-access-in-ubuntu"></a>GRUB-åtkomst i Ubuntu
För att få åtkomst till GRUB trycker du på och håller ned ESC medan den virtuella datorn startas. 

Som standard kommer Ubuntu-bilder inte att Visa GRUB-skärmen automatiskt. Detta kan ändras med följande instruktioner:
1. Öppna `/etc/default/grub.d/50-cloudimg-settings.cfg` i valfri text redigerare
1. Ändra `GRUB_TIMEOUT` värdet till ett värde som inte är noll
1. Öppna `/etc/default/grub` i valfri text redigerare
1. Kommentera ut `GRUB_HIDDEN_TIMEOUT=1` raden
1. Kör `sudo update-grub`

### <a name="single-user-mode-in-ubuntu"></a>Läge för enkel användare i Ubuntu
Ubuntu kommer att släppa dig i enanvändarläge automatiskt om det inte går att starta normalt. Använd följande instruktioner för att manuellt ange enanvändarläge:

1. Från GRUB trycker du på "e" för att redigera start posten (Ubuntu-posten)
1. Leta efter raden som börjar med `linux` och Sök sedan efter`ro`
1. Lägg till `single` efter `ro` , och se till att det finns ett blank steg före och efter`single`
1. Tryck på CTRL + X för att starta om med de här inställningarna och ange enanvändarläge

## <a name="access-for-coreos"></a>Åtkomst för kärnor
Läget enanvändarläge kräver att GRUB är aktiverat. 

### <a name="grub-access-in-coreos"></a>GRUB åtkomst i Core
För att få åtkomst till GRUB trycker du på valfri tangent när den virtuella datorn startas.

### <a name="single-user-mode-in-coreos"></a>Läge för enkel användare i kärna
Core-metoden släpper dig i enanvändarläge automatiskt om det inte går att starta normalt. Använd följande instruktioner för att manuellt ange enanvändarläge:
1. Från GRUB trycker du på "e" för att redigera start posten
1. Leta efter raden som börjar med `linux$` . Det ska finnas 2, kapslade i olika if/else-satser
1. Lägg `coreos.autologin=ttyS0` till i slutet av båda `linux$` raderna
1. Tryck på CTRL + X för att starta om med de här inställningarna och ange enanvändarläge

## <a name="access-for-suse-sles"></a>Åtkomst för SUSE SLES
Nyare avbildningar av SLES 12 SP3 + Tillåt åtkomst via serie konsolen om systemet startar i nödfalls läge. 

### <a name="grub-access-in-suse-sles"></a>GRUB-åtkomst i SUSE SLES
GRUB-åtkomst i SLES kräver att Start programmet konfigureras via YaST. Det gör du genom att följa dessa anvisningar:

1. Använd SSH i din virtuella SLES-dator och kör `sudo yast bootloader` . Använd `tab` nyckeln, `enter` nyckeln och piltangenterna för att navigera i menyn. 
1. Navigera till `Kernel Parameters` och kontrol lera `Use serial console` . 
1. Lägg till `serial --unit=0 --speed=9600 --parity=no` i konsol argumenten

1. Tryck på F10 för att spara dina inställningar och avsluta
1. Om du vill ange GRUB startar du om den virtuella datorn och trycker på valfri tangent under startsekvensen för att göra GRUB kvar på skärmen
    - Standard tids gränsen för GRUB är 1. Du kan ändra detta genom att ändra `GRUB_TIMEOUT` variabeln i`/etc/default/grub`

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-sles-yast-grub-config.gif)

### <a name="single-user-mode-in-suse-sles"></a>Läge för enkel användare i SUSE SLES
Du kommer automatiskt att tas bort från nödfall om SLES inte kan starta normalt. Använd följande instruktioner för att manuellt ange ett larm gränssnitt:

1. Från GRUB trycker du på "e" för att redigera start posten (SLES-posten)
1. Sök efter den kernel-linje som den kommer att börja med`linux`
1. Lägg till `systemd.unit=emergency.target` i slutet av raden
1. Tryck på CTRL + X för att starta om med de här inställningarna och ange ett larm gränssnitt
   > Observera att du kommer att släppa i nödfalls gränssnitt med ett _skrivskyddat_ fil system. Om du vill göra ändringar i några filer måste du montera fil systemet med Läs-och Skriv behörighet. Det gör du genom att ange `mount -o remount,rw /` i gränssnittet

## <a name="access-for-oracle-linux"></a>Åtkomst för Oracle Linux
Till skillnad från Red Hat Enterprise Linux kräver enanvändarläge i Oracle Linux GRUB och rot användaren måste vara aktive rad. 

### <a name="grub-access-in-oracle-linux"></a>GRUB åtkomst i Oracle Linux
Oracle Linux levereras med GRUB aktive rad. Om du vill ange GRUB startar du om den virtuella datorn med `sudo reboot` och trycker på ESC. Skärmen GRUB visas.

### <a name="single-user-mode-in-oracle-linux"></a>Läge för enkel användare i Oracle Linux
Följ anvisningarna för RHEL ovan för att aktivera enanvändarläge i Oracle Linux.

## <a name="next-steps"></a>Nästa steg
* Sidan med information om Linux-dokumentation för den viktigaste konsolen finns [här](serial-console.md).
* Använda en serie konsol för [NMI-och SysRq-anrop](serial-console-nmi-sysrq.md)
* Serie konsolen är också tillgänglig för virtuella [Windows](../windows/serial-console.md) -datorer
* Läs mer om [startdiagnostik](boot-diagnostics.md)
