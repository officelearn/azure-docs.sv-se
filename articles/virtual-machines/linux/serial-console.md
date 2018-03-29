---
title: Virtuella Azure-datorn seriekonsolen | Microsoft Docs
description: Dubbelriktad seriekonsolen för virtuella Azure-datorer.
services: virtual-machines-linux
documentationcenter: ''
author: harijay
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/05/2018
ms.author: harijay
ms.openlocfilehash: b7d6e48a6f34472bc38947fd70e850b1c3bf6f8a
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="virtual-machine-serial-console-preview"></a>Virtuella seriekonsolen (förhandsgranskning) 


Seriekonsolen för virtuell dator i Azure ger tillgång till en textbaserad konsol för Linux och Windows-datorer. Den här seriell anslutning är att COM1 seriell port för den virtuella datorn och ger åtkomst till den virtuella datorn och inte är relaterade till nätverk för virtuella datorer / operativsystem systemtillstånd. Åtkomst till seriekonsol för en virtuell dator kan gör du för närvarande endast via Azure-portalen och tillåts endast för de användare som har VM-deltagare eller senare åtkomst till den virtuella datorn. 

> [!Note] 
> Förhandsgranskningar görs tillgängliga för dig under förutsättning att du godkänner användningsvillkoren. Mer information finns i [Microsoft Azure kompletterande användningsvillkor för Microsoft Azure-förhandsgranskningar.] (https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Tjänsten är för närvarande **förhandsversion** och åtkomst till seriekonsol för virtuella datorer är tillgängligt för globala Azure-regioner. Nu är seriekonsolen inte tillgängliga Azure Government, Azure Tyskland och Azure Kina molnet.


## <a name="prerequisites"></a>Förutsättningar 

* Virtuell dator måste ha [starta diagnostik](boot-diagnostics.md) aktiverad 
* Det konto som använder seriekonsolen måste ha [deltagarrollen](../../active-directory/role-based-access-built-in-roles.md) för den virtuella datorn och [starta diagnostik](boot-diagnostics.md) storage-konto. 
* Inställningar som är specifika för Linux distro finns [åtkomst till seriekonsolen för Linux](#accessing-serial-console-for-linux)


## <a name="open-the-serial-console"></a>Öppna seriekonsolen
seriekonsolen för virtuella datorer är endast tillgänglig via [Azure-portalen](https://portal.azure.com). Nedan följer du stegen för att komma åt seriekonsolen för virtuella datorer via portalen 

  1. Öppna Azure-portalen
  2. Välj virtuella datorer i den vänstra menyn.
  3. Klicka på den virtuella datorn i listan. På översiktssidan för den virtuella datorn öppnas.
  4. Rulla stöd + avsnittet om felsökning och klicka på alternativet för seriekonsolen (förhandsversion). Nya rutan med seriekonsolen öppnas och anslutningen.

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)


> [!NOTE] 
> Seriekonsolen kräver en lokal användare med ett lösenord som har konfigurerats. Virtuella datorer som endast är konfigurerade med offentliga SSH-nyckeln har inte åtkomst till seriekonsolen för tillfället. Så här skapar du en lokal användare med lösenord [tillägg för virtuell dator åtkomst](https://docs.microsoft.com/azure/virtual-machines/linux/using-vmaccess-extension) och skapa lokala användare med lösenord.

### <a name="disable-feature"></a>Inaktivera funktionen
Funktionen seriekonsolen kan inaktiveras för specifika virtuella datorer genom att inaktivera inställningen för den virtuella datorn starta diagnostik.

## <a name="serial-console-security"></a>Seriekonsolen säkerhet 

### <a name="access-security"></a>Åtkomstsäkerhet 
Åtkomst till seriekonsol är begränsad till användare som har [VM deltagare](../../active-directory/role-based-access-built-in-roles.md#virtual-machine-contributor) eller senare åtkomst till den virtuella datorn. Om din AAD-klient kräver Multi-Factor Authentication sedan åtkomst till seriekonsolen måste också MFA eftersom dess åtkomst [Azure-portalen](https://portal.azure.com).

### <a name="channel-security"></a>Kanalsäkerhet
Alla data skickas tillbaka och tillbaka är krypterad under överföringen.

### <a name="audit-logs"></a>Granskningsloggar
All åtkomst till seriekonsolen för närvarande är inloggad på [starta diagnostik](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) loggar för den virtuella datorn. Åtkomst till dessa loggar ägs och styrs av administratören för Azure virtuella datorer.  

>[!CAUTION] 
När inga lösenord för konsolen loggas om kommandon i konsolen innehåller eller utgående lösenord, hemligheter, användarnamn eller andra former av personligt identifierbar Information (PII), skrivs de till startdiagnostikinställningar för virtuell dator loggar, tillsammans med alla andra synliga text, som en del av implementeringen av den seriekonsolen scrollback funktioner. Dessa loggar är cirkulär och bara personer med läsbehörighet till diagnostik storage-konto har åtkomst till dem, men vi rekommenderar följande bästa praxis och använder SSH-konsolen till något som kan innebära hemligheter och/eller personligt identifierbar information. 

### <a name="concurrent-usage"></a>Samtidig användning
Om en användare är ansluten till seriekonsol och en annan användare har begär åtkomst till att samma virtuella dator, kommer att kopplas från den första användaren och andra användare som är anslutna på ett sätt som den första användaren Ständiga eller ut ur den fysiska konsolen och en ny användaren placerad.

>[!CAUTION] 
Det innebär att den användare som kopplas inte kommer att loggas! Möjlighet att Påtvinga en logga ut vid frånkoppling (via SIGHUP eller liknande mekanism) är fortfarande Översikt. För Windows finns en automatisk tidsgräns har aktiverats i SAC, men för Linux du kan konfigurera terminal timeout-inställningen. För att göra det lägger du till `export TMOUT=600` i .bash_profile eller .profile för användaren du logga in i konsolen med, för att tidsgränsen för sessionen efter 10 minuter.

### <a name="disable-feature"></a>Inaktivera funktionen
Funktionen seriekonsolen kan inaktiveras för specifika virtuella datorer genom att inaktivera inställningen för den virtuella datorn starta diagnostik.

## <a name="common-scenarios-for-accessing-serial-console"></a>Vanliga scenarier för att komma åt seriekonsol 
Scenario          | Åtgärder i seriekonsol                |  OS-tillämplighet 
:------------------|:-----------------------------------------|:------------------
Skadad FSTAB fil | Ange nyckel för att fortsätta och åtgärda fstab fil med en textredigerare. Se [hur du löser problem med fstab](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors) | Linux 
Felaktig brandväggsregler | Komma åt seriekonsolen och åtgärda iptables eller regler för Windows-brandväggen | Linux/Windows 
Filsystem skadade/kontroll | Komma åt seriekonsolen och återställa filsystem | Linux/Windows 
SSH/RDP konfigurationsproblem | Komma åt seriekonsolen och ändra inställningar | Linux/Windows 
Nätverket Lås system| Åtkomst seriekonsolen via portalen för att hantera system | Linux/Windows 
Interaktion med startprogrammet | Åtkomst GRUB/BCD via seriekonsolen | Linux/Windows 

## <a name="accessing-serial-console-for-linux"></a>Åtkomst till seriekonsolen för Linux
Gästoperativsystemet måste konfigureras för att läsa och skriva konsolmeddelanden till den seriella porten för seriekonsolen ska fungera korrekt. De flesta [godkända Azure Linux-distributioner](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) har seriekonsolen konfigureras som standard. Genom att klicka på portalen på avsnittet seriekonsolen ger åtkomst till konsolen. 

### <a name="access-for-redhat"></a>Åtkomst för RedHat 
RedHat bilder på Azure har konsolåtkomst aktiverad som standard. Enanvändarläge i Red Hat kräver rotanvändaren aktiveras, vilket är inaktiverat som standard. Om du behöver aktivera enanvändarläge, använda på följande sätt:

1. Logga in på Red Hat-systemet via SSH
2. Aktivera lösenord för rotanvändaren 
 * `passwd root` (Ange ett starkt rotlösenordet)
3. Kontrollera rotanvändaren kan bara logga in via ttyS0
 * `edit /etc/ssh/sshd_config` och kontrollera PermitRootLog i är inga
 * `edit /etc/securetty file` att endast tillåta logga in via ttyS0 

Nu om systemet startar i enanvändarläge kan du logga in via rotlösenordet.

Alternativt RHEL 7.4 + eller 6,9 + kan du aktivera enanvändarläge i GRUB uppmanar finns instruktioner [här](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/installation_guide/s1-rescuemode-booting-single)

### <a name="access-for-ubuntu"></a>Åtkomst för Ubuntu 
Ubuntu bilder på Azure har konsolåtkomst aktiverad som standard. Du kan öppna utan ytterligare autentiseringsuppgifter om systemet startar i enanvändarläge. 

### <a name="access-for-coreos"></a>Åtkomst för virtuell CoreOS
Virtuell CoreOS-avbildningar som är tillgängliga på Azure har konsolåtkomst aktiverad som standard. Om nödvändiga system kan startas i enanvändarläge via ändra GRUB parametrar och lägga till `coreos.autologin=ttyS0` skulle göra core användaren till loggfilen och tillgänglig i seriekonsol. 

### <a name="access-for-suse"></a>Åtkomst för SUSE
SLES bilder på Azure har konsolåtkomst aktiverad som standard. Om du använder äldre versioner av SLES på Azure, följer du de [KB-artikel](https://www.novell.com/support/kb/doc.php?id=3456486) att aktivera seriekonsol. Nyare avbildningar för SLES 12 SP3 + kan också åtkomst via seriekonsolen om systemet startar i nödläge.

### <a name="access-for-centos"></a>Åtkomst för CentOS
CentOS bilder på Azure har konsolåtkomst aktiverad som standard. För enanvändarläge, följ anvisningar som liknar Red Hat bilder ovan. 

### <a name="access-for-oracle-linux"></a>Åtkomst för Oracle Linux
Oracle Linux bilder som finns på Azure har konsolåtkomst aktiverad som standard. För enanvändarläge, följ anvisningar som liknar Red Hat bilder ovan.

### <a name="access-for-custom-linux-image"></a>Åtkomst för anpassad Linux-avbildning
Aktivera konsolåtkomst i /etc/inittab att köra en terminal på ttyS0 om du vill aktivera seriekonsolen för den anpassade Linux VM-avbildningen. Nedan visas ett exempel för att lägga till det i filen inittab 

`S0:12345:respawn:/sbin/agetty -L 115200 console vt102` 

## <a name="errors"></a>Fel
De flesta felen är tillfälliga slag och försöker igen anslutningsadressen dessa. Tabellen nedan visar en lista över fel och minskning 

Fel                            |   Åtgärd 
:---------------------------------|:--------------------------------------------|
Det gick inte att hämta startdiagnostikinställningar för '<VMNAME>'. Se till att inga startdiagnostikinställningar har aktiverats för den här virtuella datorn om du vill använda seriekonsolen. | Kontrollera att den virtuella datorn har [starta diagnostik](boot-diagnostics.md) aktiverat. 
Den virtuella datorn är i ett stoppat frigjord tillstånd. Starta den virtuella datorn och försök seriekonsolen anslutningen. | Virtuell dator måste vara i startat tillstånd för att komma åt seriekonsolen
Du har inte behörighet att använda den här virtuella datorn seriekonsolen. Kontrollera att du har minst rollbehörigheter för VM-deltagare.| Seriell åtkomst kräver vissa behörighet att komma åt. Se [åtkomstkrav](#prerequisites) information
Det gick inte att fastställa resursgruppen för start diagnostiklagringskonto '<STORAGEACCOUNTNAME>'. Kontrollera att inga startdiagnostikinställningar har aktiverats för den här virtuella datorn och du har åtkomst till det här lagringskontot. | Seriell åtkomst kräver vissa behörighet att komma åt. Se [åtkomstkrav](#prerequisites) information

## <a name="known-issues"></a>Kända problem 
Eftersom vi är fortfarande i preview steg för seriekonsolen åtkomst, vi gå igenom några kända problem, nedan är listan över dem med möjliga lösningar 

Problem                           |   Åtgärd 
:---------------------------------|:--------------------------------------------|
Det finns inget alternativ med virtuella scale set instans seriekonsol |  Åtkomst till seriekonsol för virtual machine scale set instanser stöds inte vid tidpunkten för förhandsgranskning.
Träffa ange när anslutningen banderoll inte visar en logg i Kommandotolken | [Träffa ange händer ingenting](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md)


## <a name="frequently-asked-questions"></a>Vanliga frågor och svar 
**FRÅGOR. Hur skickar jag feedback?**

A. Ge feedback som ett problem genom att gå till https://aka.ms/serialconsolefeedback. Du kan också mindre (primära) skicka feedback via azserialhelp@microsoft.com eller i den virtuella dator kategorin http://feedback.azure.com

**Q.I får ett felmeddelande ”befintliga konsolen har motstridiga OS-typen” Windows ”med den begärda OS-typen av Linux?**

A. Detta är ett känt problem att åtgärda det genom att helt enkelt öppna [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) i bash-läge och försök igen.

**FRÅGOR. Jag kan inte komma åt seriekonsolen var kan jag filen ett supportärende?**

A. Den här förhandsgranskningsfunktionen omfattas via Azure villkoren. Stöd för detta hanteras bör via kanaler som nämns ovan. 

## <a name="next-steps"></a>Nästa steg
* Den seriekonsolen är också tillgängligt för [Windows](../windows/serial-console.md) virtuella datorer
* Lär dig mer om [bootdiagnostics](boot-diagnostics.md)