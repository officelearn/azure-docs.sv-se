---
title: Azure-dator från seriell konsol | Microsoft Docs
description: Dubbelriktad seriekonsol för virtuella Azure-datorer.
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
ms.openlocfilehash: 00a776131321500386b507f45ea84817b08147f7
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/06/2018
ms.locfileid: "37867869"
---
# <a name="virtual-machine-serial-console-preview"></a>Seriekonsol för virtuella datorer (förhandsversion) 


Seriekonsol för virtuella datorer på Azure tillhandahåller åtkomst till en textbaserad konsol för Linux och Windows-datorer. Den här seriell anslutning är att COM1 seriell port på den virtuella datorn och ger åtkomst till den virtuella datorn och inte är relaterade till nätverk för virtuella datorer / drift systemtillstånd. Åtkomst till seriekonsol för en virtuell dator kan klar för närvarande endast via Azure-portalen och tillåts endast för de användare som har VM-deltagare eller senare åtkomst till den virtuella datorn. 

> [!Note] 
> Förhandsversioner görs tillgängliga för dig på villkor att du godkänner användningsvillkoren. Mer information finns i [Microsoft Azure kompletterande användningsvillkor för förhandsversioner av Microsoft Azure.] (https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Den här tjänsten är för närvarande i **förhandsversion** och åtkomst till seriekonsol för virtuella datorer är tillgänglig för globala Azure-regioner. I det här läget är seriell konsol inte tillgängliga Azure Government, Azure Tyskland och Azure Kina-molnet.


## <a name="prerequisites"></a>Förutsättningar 

* Du måste använda resource management-distributionsmodellen. Klassiska distributioner stöds inte. 
* Virtuell dator måste ha [startdiagnostik](boot-diagnostics.md) aktiverat 
* Det konto som använder seriekonsolen måste ha [deltagarrollen](../../role-based-access-control/built-in-roles.md) för den virtuella datorn och [startdiagnostik](boot-diagnostics.md) storage-konto. 
* Inställningar som är specifika för Linux-distribution, se [åtkomst till seriekonsolen för Linux](#accessing-serial-console-for-linux)


## <a name="open-the-serial-console"></a>Öppna seriekonsolen
seriekonsol för virtuella datorer bara kan nås via [Azure-portalen](https://portal.azure.com). Nedan visas stegen för att komma åt seriekonsol för virtuella datorer via portalen 

  1. Öppna Azure portal
  2. Välj virtuella datorer i den vänstra menyn.
  3. Klicka på den virtuella datorn i listan. Översiktssidan för den virtuella datorn öppnas.
  4. Rulla ned till avsnittet om Support och felsökning och klicka på alternativet för seriekonsol (förhandsversion). Ett nytt fönster med seriell konsol öppnas och starta anslutningen.

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)


> [!NOTE] 
> Seriell konsol kräver en lokal användare med ett lösenord som har konfigurerats. Virtuella datorer som endast konfigurerats med offentlig SSH-nyckel har inte åtkomst till seriekonsol för tillfället. Så här skapar du en lokal användare med lösenord [VM Access-tillägg](https://docs.microsoft.com/azure/virtual-machines/linux/using-vmaccess-extension) och skapa lokala användare med lösenord.

### <a name="disable-feature"></a>Inaktivera funktionen
Seriell konsol-funktionen kan inaktiveras för specifika virtuella datorer genom att inaktivera inställningen för den virtuella datorn boot diagnostics.

## <a name="serial-console-security"></a>Seriell konsol-säkerhet 

### <a name="access-security"></a>Åtkomstsäkerhet 
Åtkomst till seriell konsol är begränsat till användare som har [VM deltagare](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) eller över åtkomst till den virtuella datorn. Om din AAD-klient kräver Multi-Factor Authentication och sedan åtkomst till seriell konsol måste också MFA eftersom dess åtkomst sker via [Azure-portalen](https://portal.azure.com).

### <a name="channel-security"></a>Kanalsäkerhet
Alla data skickas tillbaka och krypteras tillbaka under överföringen.

### <a name="audit-logs"></a>Granskningsloggar
All åtkomst till seriekonsol för tillfället är inloggad på [startdiagnostik](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) loggarna för den virtuella datorn. Åtkomst till de här loggarna ägs och kontrolleras av administratören för Azure virtuella datorer.  

>[!CAUTION] 
Även om inga lösenord för åtkomst för konsolen loggas om kommandon körs i konsolen innehåller eller utgående lösenord, hemligheter, användarnamn eller någon annan form av personligt identifierbar Information (PII), skrivs de till startdiagnostik för virtuell dator loggar, tillsammans med alla andra synlig text, som en del av implementeringen av Seriell konsol scrollback funktioner. Dessa loggar är cirkulär och endast personer med läsbehörighet till lagringskontot för diagnostik har åtkomst till dem, men vi rekommenderar att du följer bästa praxis för att använda SSH-konsolen för allt som kan omfatta hemligheter och/eller personligt identifierbar information. 

### <a name="concurrent-usage"></a>Samtidig användning
Om en användare är ansluten till seriell konsol och en annan användare begär har åtkomst till den samma virtuella datorn, kommer att kopplas från den första användaren och den andra användaren som är anslutna på ett sätt som den första användaren ställa upp och låta den fysiska konsolen och en ny användare som sitter.

>[!CAUTION] 
Det innebär att den användare som kopplas inte kommer att loggas ut! Möjlighet att framtvinga en utloggning vid frånkoppling (via SIGHUP eller liknande mekanism) är fortfarande i översikten. För Windows finns en automatisk tidsgräns har aktiverats i SAC, men för Linux du kan konfigurera terminal timeoutinställning. För att göra detta helt enkelt till `export TMOUT=600` i din .bash_profile eller .profile för användaren du logga in i konsolen med, för att tidsgränsen för sessionen efter 10 minuter.

### <a name="disable-feature"></a>Inaktivera funktionen
Seriell konsol-funktionen kan inaktiveras för specifika virtuella datorer genom att inaktivera inställningen för den virtuella datorn boot diagnostics.

## <a name="common-scenarios-for-accessing-serial-console"></a>Vanliga scenarier för att komma åt seriekonsol 
Scenario          | Åtgärder i seriekonsol                |  OS-tillämplighet 
:------------------|:-----------------------------------------|:------------------
Bruten FSTAB-filen | Ange nyckel för att fortsätta och åtgärda fstab-filen med hjälp av en textredigerare. Se [åtgärda fstab-problem](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors) | Linux 
Felaktig brandväggsregler | Få åtkomst till seriell konsol och åtgärda iptables eller regler för Windows-brandväggen | Linux/Windows 
Filsystem skadade/kontroll | Få åtkomst till seriell konsol och återställa filsystem | Linux/Windows 
Problem med SSH/RDP-konfigurationen | Åtkomst från seriell konsol och ändra inställningar | Linux/Windows 
Nätverket låsa system| Åtkomst från seriell konsol via portalen för att hantera system | Linux/Windows 
Interagera med startprogrammet | Åtkomst GRUB/BCD via seriekonsolen | Linux/Windows 

## <a name="accessing-serial-console-for-linux"></a>Åtkomst till seriell konsol för Linux
Gästoperativsystemet måste konfigureras för att läsa och skriva konsolmeddelanden att den seriella porten för seriekonsol för att fungera korrekt. De flesta [godkända Azure Linux-distributioner](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) har seriekonsolen konfigureras som standard. Genom att klicka på portalen i avsnittet seriekonsolen ger åtkomst till konsolen. 

### <a name="access-for-red-hat"></a>Åtkomst för Red Hat 
Red Hat-bilder som är tillgängliga på Azure har åtkomst till konsolen aktiverad som standard. Enanvändarläge i Red Hat kräver rotanvändaren är aktiverat som standard är inaktiverad. Om du har ett behov av att aktivera enanvändarläge, Använd följande instruktioner:

1. Logga in på Red Hat-systemet via SSH
2. Aktivera lösenord för rotanvändaren 
 * `passwd root` (Ange ett starkt rotlösenord)
3. Se till att rotanvändaren kan bara logga in via ttyS0
 * `edit /etc/ssh/sshd_config` och se till att PermitRootLog i har angetts till ingen
 * `edit /etc/securetty file` att endast tillåta inloggningen via ttyS0 

Nu om systemet startas i enanvändarläge kan du logga in via rotlösenordet.

Alternativt RHEL 7.4 + eller 6,9 +-du kan aktivera enanvändarläge i GRUB uppmanar finns i instruktionerna [här](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/installation_guide/s1-rescuemode-booting-single)

### <a name="access-for-ubuntu"></a>Åtkomst för Ubuntu 
Ubuntu-avbildningar som är tillgängliga på Azure har åtkomst till konsolen aktiverad som standard. Om systemet startar i enanvändarläge kan du komma åt utan ytterligare autentiseringsuppgifter. 

### <a name="access-for-coreos"></a>Åtkomst för CoreOS
CoreOS-avbildningarna i Azure har åtkomst till konsolen aktiverad som standard. Om nödvändiga system kan startas i enanvändarläge via ändra GRUB parametrar och lägga till `coreos.autologin=ttyS0` skulle gör core användare in i och är tillgänglig i Seriell konsol. 

### <a name="access-for-suse"></a>Åtkomst för SUSE
SLES-avbildningarna i Azure har åtkomst till konsolen aktiverad som standard. Om du använder äldre versioner av SLES på Azure, följer du de [KB-artikel](https://www.novell.com/support/kb/doc.php?id=3456486) att aktivera Seriell konsol. Nyare avbildningar av SLES 12 SP3 + kan också åtkomst via seriekonsolen om systemet startar i nödläge.

### <a name="access-for-centos"></a>Åtkomst för CentOS
CentOS-avbildningarna i Azure har åtkomst till konsolen aktiverad som standard. Enanvändarläge, följa anvisningar som liknar Red Hat-bilder ovan. 

### <a name="access-for-oracle-linux"></a>Åtkomst för Oracle Linux
Oracle Linux-avbildningar på Azure har åtkomst till konsolen aktiverad som standard. Enanvändarläge, följa anvisningar som liknar Red Hat-bilder ovan.

### <a name="access-for-custom-linux-image"></a>Åtkomst för anpassade Linux-avbildning
Aktivera åtkomst till konsolen i /etc/inittab att köra en terminal på ttyS0 om du vill aktivera seriekonsol för en anpassad Linux VM-avbildning. Nedan visas ett exempel att lägga till det i filen inittab 

`S0:12345:respawn:/sbin/agetty -L 115200 console vt102` 

## <a name="errors"></a>Fel
De flesta felen är tillfälliga i karaktär och sedan försöka anslutningsadressen dessa. Tabellen nedan visar en lista över fel och minskning 

Fel                            |   Åtgärd 
:---------------------------------|:--------------------------------------------|
Det gick inte att hämta inställningarna för startdiagnostik för '<VMNAME>'. Se till att startdiagnostik har aktiverats för den här virtuella datorn om du vill använda seriekonsolen. | Kontrollera att den virtuella datorn har [startdiagnostik](boot-diagnostics.md) aktiverat. 
Den virtuella datorn är i tillståndet stoppad frigjord. Starta den virtuella datorn och försök Seriell konsol-anslutning. | Virtuell dator måste vara i tillståndet igång för att komma åt seriekonsolen
Du har inte behörighet att använda den här virtuella datorn seriekonsolen. Kontrollera att du har minst deltagarbehörigheter för virtuell dator.| Seriell konsolåtkomst kräver vissa behörighet att komma åt. Se [åtkomstbehörigheter](#prerequisites) information
Det går inte att fastställa resursgruppen för startdiagnostiklagringskonto '<STORAGEACCOUNTNAME>'. Kontrollera att startdiagnostik har aktiverats för den här virtuella datorn och du har åtkomst till det här lagringskontot. | Seriell konsolåtkomst kräver vissa behörighet att komma åt. Se [åtkomstbehörigheter](#prerequisites) information

## <a name="known-issues"></a>Kända problem 
Eftersom vi är fortfarande i förhandsstadium för seriell konsolåtkomst, vi arbetar dig igenom några kända problem, nedan är en lista över dessa med möjliga lösningar 

Problem                           |   Åtgärd 
:---------------------------------|:--------------------------------------------|
Det finns inget alternativ med VM scale set-instans från seriell konsol |  Åtkomst till seriekonsol för VM-skalningsuppsättningsinstanser stöds inte vid tidpunkten för förhandsversionen.
Träffa ange när anslutningen popup-meddelandet inte visas en logg i Kommandotolken | [Träffa ange gör ingenting](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md)


## <a name="frequently-asked-questions"></a>Vanliga frågor och svar 
**FRÅGOR OCH. Hur kan jag skicka feedback?**

A. Ge feedback som ett problem genom att gå till https://aka.ms/serialconsolefeedback. Du kan också mindre (rekommenderas) skicka feedback via azserialhelp@microsoft.com eller i kategori för virtuell dator av http://feedback.azure.com

**Q.I får ett felmeddelande ”befintliga konsolen har operativsystemtypen” Windows ”med den begärda OS-typ av Linux?**

A. Det här är ett känt problem att åtgärda det genom att helt enkelt öppna [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) i bash-läge och försök igen.

**FRÅGOR OCH. Jag kan inte komma åt seriekonsolen var kan jag registrera ett supportärende?**

A. Den här förhandsversionsfunktionen täcks via Azure-förhandsversioner. Stöd för detta hanteras bäst via kanaler som nämns ovan. 

## <a name="next-steps"></a>Nästa steg
* Seriekonsolen är också tillgängligt för [Windows](../windows/serial-console.md) virtuella datorer
* Läs mer om [bootdiagnostics](boot-diagnostics.md)