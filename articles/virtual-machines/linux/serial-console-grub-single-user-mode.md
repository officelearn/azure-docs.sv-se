---
title: Azure Seriekonsol för GRUB och enanvändarläge | Microsoft Docs
description: Med hjälp av Seriekonsol för grub på Azure virtuella datorer.
services: virtual-machines-linux
documentationcenter: ''
author: alsin
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
ms.openlocfilehash: 059cb0cbc7e62af16dbf95693be421feebcc1ee0
ms.sourcegitcommit: 76797c962fa04d8af9a7b9153eaa042cf74b2699
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/21/2018
ms.locfileid: "42061727"
---
# <a name="use-serial-console-to-access-grub-and-single-user-mode"></a>Använd Seriekonsol för att komma åt GRUB och enanvändarläge
Enanvändarläge är en minimal miljö med minimal funktioner. Det kan vara användbara för att undersöka problem eller nätverksproblem som färre tjänsterna kan köras i bakgrunden och, beroende på är ett filsystem kan inte ens att automatiskt montera. Detta är användbart att undersöka situationer, till exempel ett skadat filsystem, en bruten fstab eller nätverksanslutning (felaktig iptables-konfiguration).

Vissa distributioner förlorar automatiskt du i enanvändarläge eller nödläge om den virtuella datorn inte att starta. Andra, men kräver ytterligare konfiguration innan de kan släpper du i enanvändarläge eller nödläge läge automatiskt.

Du vill kontrollera att GRUB är aktiverat på den virtuella datorn för att få åtkomst till enanvändarläge. Beroende på din distribution, kan det finnas vissa installationen fungerar för att kontrollera att GRUB är aktiverat. 


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