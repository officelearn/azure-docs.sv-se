---
title: Azure Seriekonsol för GRUB och enanvändarläge | Microsoft Docs
description: Med hjälp av Seriekonsol för grub på Azure virtuella datorer.
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
ms.openlocfilehash: e3745efdd0d0ea159afcda177c306f5865ac2aad
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2018
ms.locfileid: "46366842"
---
# <a name="use-serial-console-to-access-grub-and-single-user-mode"></a>Använd Seriekonsol för att komma åt GRUB och enanvändarläge
GRUB är GRand Unified startprogrammet. Från GRUB kan du ändra din startkonfiguration starta i enanvändarläge, bland annat. 

Enanvändarläge är en minimal miljö med minimal funktioner. Det kan vara praktiskt för att undersöka problem, filsystem problem eller nätverksproblem. Färre tjänsterna kan köras i bakgrunden och, beroende på är ett filsystem kan inte ens att automatiskt montera.

Enanvändarläge är också användbart i situationer där den virtuella datorn endast kan konfigureras för att acceptera SSH-nycklar för inloggning. I det här fallet kan du att kunna använda enanvändarläge för att skapa ett konto med lösenordsautentisering. 

Om du vill ange enanvändarläge, behöver du ange GRUB när den virtuella datorn startas och ändra startkonfigurationen i GRUB. Detta kan göras med den virtuella datorn från seriell konsolen.

## <a name="general-grub-access"></a>Allmän GRUB-åtkomst
För att komma åt GRUB, behöver du starta om den virtuella datorn samtidigt som det öppna bladet Seriell konsol. Vissa distributioner kräver tangentbordsinmatning att visa GRUB, medan andra automatiskt visa GRUB under några sekunder och tillåter indata från användaren tangentbord annullera timeout-värdet. 

Du vill kontrollera att GRUB är aktiverat på den virtuella datorn för att få åtkomst till enanvändarläge. Beroende på din distribution, kan det finnas vissa installationen fungerar för att kontrollera att GRUB är aktiverat. Distribution-specifik information finns nedan.

### <a name="reboot-your-vm-to-access-grub-in-serial-console"></a>Starta om den virtuella datorn för att komma åt GRUB i Seriekonsol
Starta om den virtuella datorn med bladet Seriell konsol är öppen kan göras med en SysRq `'b'` kommandot om [SysRq](./serial-console-nmi-sysrq.md) är aktiverad, eller genom att klicka på omstarten knappen i bladet översikt (öppna den virtuella datorn i en ny webbläsarflik ska startas om utan att stänga bladet Seriell konsol). Följ distribution-specifika anvisningarna nedan för att lära dig vad som händer GRUB när du startar om.

## <a name="general-single-user-mode-access"></a>Allmän enanvändarläge åtkomst
Manuell åtkomst till enanvändarläge kan behövas i situationer där du inte har konfigurerat ett konto med autentisering med lösenord. Du behöver ändra GRUB-konfigurationen för att manuellt ange enanvändarläge. När du har gjort det, se [Använd läget för enskild användare kan återställa eller lägger till ett lösenord](#-Use-Single-User-Mode-to-reset-or-add-a-password) för ytterligare instruktioner.

I fall där den virtuella datorn är det går inte att starta kommer distributioner ofta automatiskt släpper du i enanvändarläge eller nödläge. Andra, men kräver ytterligare konfiguration innan de kan släpper du i enanvändarläge eller nödläge läge automatiskt (till exempel ställa in ett rotlösenord).

### <a name="use-single-user-mode-to-reset-or-add-a-password"></a>Använd enanvändarläge för att återställa eller lägger till ett lösenord
När du är i enanvändarläge kan du göra följande för att lägga till en ny användare med sudo-behörighet:
1. Kör `useradd <username>` lägga till en användare
1. Kör `sudo usermod -a -G sudo <username>` och ge den nya användaren rotprivilegier
1. Använd `passwd <username>` ställa in lösenordet för den nya användaren. Du kommer sedan att kunna logga in som den nya användaren

## <a name="access-for-red-hat-enterprise-linux-rhel"></a>Åtkomst för Red Hat Enterprise Linux (RHEL)
RHEL förlorar du i enanvändarläge automatiskt om den inte kan starta på vanligt sätt. Om du inte har konfigurerat rotåtkomst för enanvändarläge du kommer inte att ha ett rotlösenord och går inte att logga in. Det finns en lösning (se ”manuellt ange enanvändarläge” nedan), men förslaget är att ställa in rotåtkomst från början.

### <a name="grub-access-in-rhel"></a>GRUB-åtkomst i RHEL
RHEL levereras med GRUB aktiverad direkt ur lådan. Du anger GRUB genom att starta om den virtuella datorn med `sudo reboot` och tryck på valfri tangent. GRUB-skärmen visas visas.

> Obs: Red Hat innehåller också dokumentationen för start i Räddade läge, nödfall läge, felsökningsläge och återställa rotlösenordet. [Klicka här för att komma åt den](https://aka.ms/rhel7grubterminal).

### <a name="set-up-root-access-for-single-user-mode-in-rhel"></a>Konfigurera rotåtkomst för enanvändarläge i RHEL
Enanvändarläge i RHEL kräver rotanvändaren är aktiverat som standard är inaktiverad. Om du har ett behov av att aktivera enanvändarläge, Använd följande instruktioner:

1. Logga in på Red Hat-systemet via SSH
1. Växla till rot
1. Aktivera lösenord för rotanvändaren 
    * `passwd root` (Ange ett starkt rotlösenord)
1. Se till att rotanvändaren kan bara logga in via ttyS0
    * `edit /etc/ssh/sshd_config` och se till att PermitRootLogIn har angetts till ingen
    * `edit /etc/securetty file` att endast tillåta inloggningen via ttyS0 

Nu om systemet startas i enanvändarläge kan du logga in via rotlösenordet.

Alternativt RHEL 7.4 + eller 6,9 +-du kan aktivera enanvändarläge i GRUB uppmanar finns i instruktionerna [här](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/installation_guide/s1-rescuemode-booting-single)

### <a name="manually-enter-single-user-mode-in-rhel"></a>Manuellt ange enanvändarläge i RHEL
Om du har ställt in GRUB nätverksenheter och rotcertifikatutfärdarcertifikat komma åt med anvisningarna ovan så du kan ange enanvändarläge med följande instruktioner:

1. Tryck på 'Esc ”när du startar om den virtuella datorn om du vill ange GRUB
1. I GRUB, trycker du på ”e” om du vill redigera den valda OS som du vill starta i (vanligtvis den första raden)
1. Hitta raden kernel - i Azure, startas med `linux16`
1. Tryck på Ctrl + E för att gå till slutet av raden
1. Lägg till följande i slutet av raden: `systemd.unit=rescue.target`
    * Detta startar du i enanvändarläge. Om du vill använda nödläge lägger du till `systemd.unit=emergency.target` i slutet av raden i stället för `systemd.unit=rescue.target`
1. Tryck på Ctrl + X om du vill avbryta och starta om med inställningarna som används
1. Du uppmanas att administratörslösenordet innan du kan ange enanvändarläge – detta är samma lösenord som du skapade i anvisningarna ovan    

    ![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-enter-emergency-shell.gif)

### <a name="enter-single-user-mode-without-root-account-enabled-in-rhel"></a>Ange enanvändarläge utan rotkontot aktiverat i RHEL
Om du inte går igenom stegen ovan för att aktivera rotanvändaren kan återställa du fortfarande rotlösenordet. Använd följande instruktioner:

> Obs: Om du använder SELinux, kontrollera att du har vidtagit de ytterligare stegen som beskrivs i dokumentationen för Red Hat [här](https://aka.ms/rhel7grubterminal) när de återställer rotlösenordet.

1. Tryck på 'Esc ”när du startar om den virtuella datorn om du vill ange GRUB
1. I GRUB, trycker du på ”e” om du vill redigera den valda OS som du vill starta i (vanligtvis den första raden)
1. Hitta raden kernel - i Azure, startas med `linux16`
1. Lägg till `rd.break` i slutet av raden att se till att det är ett blanksteg före `rd.break` (se exemplet nedan)
    - Detta kommer att avbryta startprocessen innan kontrollen skickas från `initramfs` till `systemd`, enligt beskrivningen i dokumentationen för Red Hat [här](https://aka.ms/rhel7rootpassword).
1. Tryck på Ctrl + X om du vill avbryta och starta om med inställningarna som används
1. När du startar du kommer att tas bort i nödläge med ett skrivskyddade filsystem. Ange `mount -o remount,rw /sysroot` i gränssnittet för att montera filsystemet rot med Läs-/ skrivbehörigheter
1. När du startar i enanvändarläge, skriva i `chroot /sysroot` byter du till den `sysroot` upplåsta
1. Du kan nu rot. Du kan återställa rotlösenordet med `passwd` och använder anvisningarna ovan för att ange enanvändarläge. Typ `reboot -f` ska startas om när du är klar.

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-emergency-mount-no-root.gif)

> Obs: Kör via anvisningarna ovan förlorar du i nödfall shell, så du kan också utföra uppgifter som att redigera `fstab`. Allmänt vedertagna förslag är dock att återställa rotlösenordet och använda den för att ange enanvändarläge. 


## <a name="access-for-centos"></a>Åtkomst för CentOS
Som Red Hat Enterprise Linux kräver enanvändarläge i CentOS mycket GRUB och rotanvändaren är aktiverat. 

### <a name="grub-access-in-centos"></a>GRUB-åtkomst i CentOS
CentOS levereras med GRUB aktiverad direkt ur lådan. Du anger GRUB genom att starta om den virtuella datorn med `sudo reboot` och tryck på valfri tangent. GRUB-skärmen visas visas.

### <a name="single-user-mode-in-centos"></a>Enanvändarläge i CentOS
Följ instruktionerna för RHEL ovan för att aktivera enanvändarläge i CentOS.

## <a name="access-for-ubuntu"></a>Åtkomst för Ubuntu 
Ubuntu-avbildningar kräver inte ett rotlösenord. Du kan använda det utan ytterligare autentiseringsuppgifter om systemet startas i enanvändarläge. 

### <a name="grub-access-in-ubuntu"></a>GRUB-åtkomst i Ubuntu
Tryck och håll ner 'Esc' medan den virtuella datorn startar upp för att komma åt GRUB. 

Som standard visas Ubuntu-avbildningar automatiskt inte GRUB-skärmen. Detta kan ändras med följande instruktioner:
1. Öppna `/etc/default/grub.d/50-cloudimg-settings.cfg` i en textredigerare valfri
1. Ändra den `GRUB_TIMEOUT` värdet till ett annat värde än noll
1. Öppna `/etc/default/grub` i en textredigerare valfri
1. Kommentera ut den `GRUB_HIDDEN_TIMEOUT=1` rad
1. Kör `sudo update-grub`

### <a name="single-user-mode-in-ubuntu"></a>Enanvändarläge i Ubuntu
Ubuntu förlorar du i enanvändarläge automatiskt om den inte kan starta på vanligt sätt. Använd följande instruktioner för att ange enanvändarläge manuellt:

1. Från GRUB, trycker du på ”e” så här redigerar du din startpost (Ubuntu-post)
1. Leta efter den rad som börjar med `linux`, leta efter `ro`
1. Lägg till `single` när `ro`, att se till att det finns ett blanksteg före och efter `single`
1. Tryck på Ctrl + X för att starta om med de här inställningarna och ange enanvändarläge

## <a name="access-for-coreos"></a>Åtkomst för CoreOS
Enanvändarläge i CoreOS kräver GRUB aktiveras. 

### <a name="grub-access-in-coreos"></a>GRUB-åtkomst i CoreOS
Du kommer åt GRUB genom att trycka på valfri tangent när den virtuella datorn startas.

### <a name="single-user-mode-in-coreos"></a>Enanvändarläge i CoreOS
CoreOS förlorar du i enanvändarläge automatiskt om den inte kan starta på vanligt sätt. Använd följande instruktioner för att ange enanvändarläge manuellt:
1. Från GRUB, trycker du på ”e” om du vill redigera din startpost
1. Leta efter den rad som börjar med `linux$`. Det bör finnas 2, inkapslade i olika if/else satser
1. Lägg till `coreos.autologin=ttyS0` i slutet av både `linux$` rader
1. Tryck på Ctrl + X för att starta om med de här inställningarna och ange enanvändarläge

## <a name="access-for-suse-sles"></a>Åtkomst för SUSE SLES
Nyare avbildningar av SLES 12 SP3 + Tillåt åtkomst via seriekonsolen om systemet startar i nödläge. 

### <a name="grub-access-in-suse-sles"></a>GRUB-åtkomst i SUSE SLES
GRUB-åtkomst i SLES kräver startprogrammet konfiguration via YaST. Om du vill göra detta måste du följa instruktionerna nedan:

1. SSH till din SLES VM och kör `sudo yast bootloader`. Använd den `tab` nyckel, `enter` nyckel och piltangenterna för att gå via menyn. 
1. Gå till `Kernel Parameters`, och kontrollera `Use serial console`. 
1. Lägg till `serial --unit=0 --speed=9600 --parity=no` till konsolen argument

1. Tryck på F10 för att spara dina inställningar och avsluta
1. Om du vill ange GRUB, starta om den virtuella datorn och tryck på valfri tangent startsekvens att göra GRUB stanna kvar på skärmen
    - Standardvärdet för timeout för GRUB är 1s. Du kan ändra detta genom att ändra den `GRUB_TIMEOUT` variabel i `/etc/default/grub`

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-sles-yast-grub-config.gif)

### <a name="single-user-mode-in-suse-sles"></a>Enanvändarläge i SUSE SLES
Du tas automatiskt bort i nödfall shell om SLES inte kan starta på vanligt sätt. Om du vill ange manuellt i nödfall gränssnittet, Använd följande instruktioner:

1. Från GRUB, trycker du på ”e” så här redigerar du din startpost (SLES-post)
1. Leta efter raden kernel den börjar med `linux`
1. Lägg till `systemd.unit=emergency.target` i slutet av raden
1. Tryck på Ctrl + X för att starta om med de här inställningarna och ange vid akutfall shell
> Observera att du kommer att tas bort i nödfall shell med en _skrivskyddad_ filsystem. Om du vill göra alla ändringar i alla filer, kommer du behöva montera filsystemet med läs-och skrivbehörighet. Gör detta genom att ange `mount -o remount,rw /` i gränssnittet

## <a name="access-for-oracle-linux"></a>Åtkomst för Oracle Linux
Som Red Hat Enterprise Linux kräver enanvändarläge i Oracle Linux mycket GRUB och rotanvändaren är aktiverat. 

### <a name="grub-access-in-oracle-linux"></a>GRUB-åtkomst i Oracle Linux
Oracle Linux levereras med GRUB aktiverad direkt ur lådan. Du anger GRUB genom att starta om den virtuella datorn med `sudo reboot` och tryck på 'Esc'. GRUB-skärmen visas visas.

### <a name="single-user-mode-in-oracle-linux"></a>Enanvändarläge i Oracle Linux
Följ instruktionerna för RHEL ovan för att aktivera enanvändarläge i Oracle Linux.

## <a name="next-steps"></a>Nästa steg
* Sidan huvudkonsolen seriell Linux-dokumentation finns [här](serial-console.md).
* Använd Seriekonsol för [NMI och SysRq anrop](serial-console-nmi-sysrq.md)
* Seriekonsolen är också tillgängligt för [Windows](../windows/serial-console.md) virtuella datorer
* Läs mer om [startdiagnostik](boot-diagnostics.md)