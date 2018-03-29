---
title: Virtuella Azure-datorn seriekonsolen | Microsoft Docs
description: Dubbelriktad seriekonsolen för virtuella Azure-datorer.
services: virtual-machines-windows
documentationcenter: ''
author: harijay
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/05/2018
ms.author: harijay
ms.openlocfilehash: 9c16114b4f8d335dc750b1beef1fb6204ae20e0f
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

## <a name="open-the-serial-console"></a>Öppna seriekonsolen
seriekonsolen för virtuella datorer är endast tillgänglig via [Azure-portalen](https://portal.azure.com). Nedan följer du stegen för att komma åt seriekonsolen för virtuella datorer via portalen 

  1. Öppna Azure-portalen
  2. Välj virtuella datorer i den vänstra menyn.
  3. Klicka på den virtuella datorn i listan. På översiktssidan för den virtuella datorn öppnas.
  4. Rulla stöd + avsnittet om felsökning och klicka på alternativet seriekonsolen (förhandsversion). Nya rutan med seriekonsolen öppnas och anslutningen.

![](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

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
När inga lösenord för konsolen loggas om kommandon i konsolen innehåller eller utgående lösenord, hemligheter, användarnamn eller andra former av personligt identifierbar Information (PII), skrivs de till startdiagnostikinställningar för virtuell dator loggar, tillsammans med alla andra synliga text, som en del av implementeringen av den seriekonsolen scrollback funktioner. Dessa loggar är cirkulär och bara personer med läsbehörighet till diagnostik storage-konto har åtkomst till dem, men vi rekommenderar följande bästa praxis att använda Remote Desktop för något som kan innebära hemligheter och/eller personligt identifierbar information. 

### <a name="concurrent-usage"></a>Samtidig användning
Om en användare är ansluten till seriekonsol och en annan användare har begär åtkomst till att samma virtuella dator, kommer att kopplas från den första användaren och andra användare som är anslutna på ett sätt som den första användaren Ständiga eller ut ur den fysiska konsolen och en ny användaren placerad.

>[!CAUTION] 
Det innebär att den användare som kopplas inte kommer att loggas! Möjlighet att Påtvinga en logga ut vid frånkoppling (via SIGHUP eller liknande mekanism) är fortfarande Översikt. För Windows finns en automatisk tidsgräns har aktiverats i SAC, men för Linux du kan konfigurera terminal timeout-inställningen. 


## <a name="accessing-serial-console-for-windows"></a>Åtkomst till seriekonsolen för Windows 
Senare Windows Server-avbildningar i Azure har [särskilda administrationskonsolen](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) (SAC) aktiverade som standard. SAC stöds på Windows serverversioner, men är inte tillgängligt i klientversioner (t.ex. Windows 10, Windows 8 eller Windows 7). Om du vill aktivera seriekonsolen för Windows-datorer som skapats med hjälp av Feb2018 eller lägre bilder du följande steg: 

1. Ansluta till din Windows-dator via fjärrskrivbord
2. Kör följande kommandon från en administrativ kommandotolk 
* `bcdedit /ems {current} on`
* `bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200`
3. Starta om systemet för SAC-konsolen måste vara aktiverat

![](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

Om nödvändiga SAC kan aktiveras offline 

1. Ansluta windows disken som du vill använda SAC som konfigurerats för som en datadisk till en befintlig virtuell dator. 
2. Kör följande kommandon från en administrativ kommandotolk 
* `bcdedit /store <mountedvolume>\boot\bcd /ems {default} on`
* `bcdedit /store <mountedvolume>\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

### <a name="how-do-i-know-if-sac-is-enabled-or-not"></a>Hur vet jag om SAC är aktiverad eller inte 

Om [SAC] (https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) aktiveras inte seriekonsolen visas inte SAC-prompten. Den kan visa en VM hälsoinformation i vissa fall eller det skulle vara tomt.  

### <a name="enabling-boot-menu-to-show-in-the-serial-console"></a>Aktiverar startmenyn ska visas i seriekonsolen 

Om du behöver aktivera Windows-startprogrammet uppmanas du för att visa i seriekonsolen kan du lägga till följande ytterligare alternativ Windows startprogram.

1. Ansluta till din Windows-dator via fjärrskrivbord
2. Kör följande kommandon från en administrativ kommandotolk 
* `bcdedit /set {bootmgr} displaybootmenu yes`
* `bcdedit /set {bootmgr} timeout 5`
* `bcdedit /set {bootmgr} bootems yes`
3. Starta om datorn för startmenyn aktiveras

> [!NOTE] 
> På den här platsen stöd för funktionen nycklar inte är aktiverat, om du behöver avancerade startalternativ använda bcdedit/set {aktuella} onetimeadvancedoptions på, se [bcdedit](https://docs.microsoft.com/windows-hardware/drivers/devtest/bcdedit--set) mer information

## <a name="common-scenarios-for-accessing-windows-serial-console"></a>Vanliga scenarier för att komma åt Windows seriekonsol 
Scenario          | Åtgärder i seriekonsolen                
:------------------|:----------------------------------------
Felaktig brandväggsregler | Komma åt seriekonsolen och åtgärda iptables eller regler för Windows-brandväggen 
Filsystem skadade/kontroll | Komma åt seriekonsolen och återställa filsystem när du loggar in till SAC kommando
RDP-konfigurationsproblem | Komma åt seriekonsolen och logga in cmd-kanal. Kontrollera hälsotillståndet för Terminal services och starta om vid behov.
Nätverket Lås system| Seriekonsolen för åtkomst och logga in cmd-kanal. Kontrollera status för brandväggen av [netsh](https://docs.microsoft.com/windows-server/networking/technologies/netsh/netsh-contexts) kommandoraden. 

## <a name="errors"></a>Fel
De flesta felen är tillfälliga slag och försöker igen anslutningsadressen dessa. Tabellen nedan visar en lista över fel och minskning 

Fel                            |   Åtgärd 
:---------------------------------|:--------------------------------------------|
Det gick inte att hämta startdiagnostikinställningar för '<VMNAME>'. Se till att inga startdiagnostikinställningar har aktiverats för den här virtuella datorn om du vill använda seriekonsolen. | Kontrollera att den virtuella datorn har [starta diagnostik](boot-diagnostics.md) aktiverat. 
Den virtuella datorn är i ett stoppat frigjord tillstånd. Starta den virtuella datorn och försök seriekonsolen anslutningen. | Virtuell dator måste vara i startat tillstånd för att komma åt seriekonsolen
Du har inte behörighet att använda den här Virtuella seriekonsol. Kontrollera att du har minst rollbehörigheter för VM-deltagare.| Seriell åtkomst kräver vissa behörighet att komma åt. Se [åtkomstkrav](#prerequisites) information
Det gick inte att fastställa resursgruppen för start diagnostiklagringskonto '<STORAGEACCOUNTNAME>'. Kontrollera att inga startdiagnostikinställningar har aktiverats för den här virtuella datorn och du har åtkomst till det här lagringskontot. | Seriell åtkomst kräver vissa behörighet att komma åt. Se [åtkomstkrav](#prerequisites) information

## <a name="known-issues"></a>Kända problem 
Eftersom vi är fortfarande i preview steg för seriekonsolen åtkomst, vi gå igenom några kända problem, nedan är listan över dem med möjliga lösningar 

Problem                           |   Åtgärd 
:---------------------------------|:--------------------------------------------|
Det finns inget alternativ med virtuella scale set instans seriekonsol | Åtkomst till seriekonsol för virtual machine scale set instanser stöds inte vid tidpunkten för förhandsgranskning.
Träffa ange när anslutningen banderoll inte visar en logg i Kommandotolken | [Träffa ange händer ingenting](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md)
Endast hälsoinformation visas när du ansluter till en Windows VM| [Windows hälsa signaler](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md)

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar 
**FRÅGOR. Hur skickar jag feedback?**

A. Ge feedback som ett problem genom att gå till https://aka.ms/serialconsolefeedback. Du kan också mindre (primära) skicka feedback via azserialhelp@microsoft.com eller i den virtuella dator kategorin http://feedback.azure.com 

**FRÅGOR. Ett felmeddelande ”befintliga konsolen har motstridiga OS-typen” Windows ”med den begärda OS-typen av Linux?**

A. Detta är ett känt problem att åtgärda det genom att helt enkelt öppna [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) i bash-läge och försök igen.

**FRÅGOR. Jag kan inte komma åt seriekonsolen var kan jag filen ett supportärende?**

A. Den här förhandsgranskningsfunktionen omfattas via Azure villkoren. Stöd för detta hanteras bör via kanaler som nämns ovan. 

## <a name="next-steps"></a>Nästa steg
* Seriekonsolen är också tillgängligt för [Linux](../linux/serial-console.md) virtuella datorer
* Lär dig mer om [bootdiagnostics](boot-diagnostics.md)
