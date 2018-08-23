---
title: Azure Virtual Machine Serial Console | Microsoft Docs
description: Dubbelriktad Seriekonsol för virtuella Azure-datorer.
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
ms.date: 08/07/2018
ms.author: harijay
ms.openlocfilehash: 66354db65d5e615780ec49683fbc72f1156ac5e1
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/21/2018
ms.locfileid: "42057117"
---
# <a name="virtual-machine-serial-console-preview"></a>Virtual Machine Serial Console (förhandsversion) 


Virtual Machine Serial Console i Azure ger åtkomst till en textbaserad konsol för Linux och Windows-datorer. Den här seriell anslutning är att COM1 seriell port på den virtuella datorn och ger åtkomst till den virtuella datorn och inte är relaterade till nätverk för virtuella datorer / drift systemtillstånd. Åtkomst till seriekonsol för en virtuell dator kan klar för närvarande endast via Azure-portalen och tillåts endast för de användare som har VM-deltagare eller senare åtkomst till den virtuella datorn. 

Seriell konsol dokumentation för virtuella Linux-datorer [Klicka här](../linux/serial-console.md).

> [!Note] 
> Förhandsversioner görs tillgängliga för dig på villkor att du godkänner användningsvillkoren. Mer information finns i [Microsoft Azure kompletterande användningsvillkor för förhandsversioner av Microsoft Azure.] (https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Den här tjänsten är för närvarande i **förhandsversion** och åtkomst till seriekonsol för virtuella datorer är tillgänglig för globala Azure-regioner. I det här läget är seriell konsol inte tillgängliga Azure Government, Azure Tyskland och Azure Kina-molnet.

 

## <a name="prerequisites"></a>Förutsättningar 

* Du måste använda resource management-distributionsmodellen. Klassiska distributioner stöds inte. 
* Virtuell dator måste ha [startdiagnostik](boot-diagnostics.md) aktiverat 
* Det konto som använder seriekonsolen måste ha [deltagarrollen](../../role-based-access-control/built-in-roles.md) för den virtuella datorn och [startdiagnostik](boot-diagnostics.md) storage-konto. 

## <a name="open-the-serial-console"></a>Öppna Seriekonsolen
Seriekonsol för virtuella datorer bara kan nås via [Azure-portalen](https://portal.azure.com). Nedan visas stegen för att komma åt seriekonsol för virtuella datorer via portalen 

  1. Öppna Azure portal
  2. Välj virtuella datorer i den vänstra menyn.
  3. Klicka på den virtuella datorn i listan. Översiktssidan för den virtuella datorn öppnas.
  4. Rulla ned till avsnittet om Support och felsökning och klicka på alternativet seriekonsol (förhandsversion). Ett nytt fönster med seriell konsol öppnas och starta anslutningen.

![](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

## <a name="disable-serial-console"></a>Inaktivera Seriekonsol
Som standard har alla prenumerationer seriell konsolåtkomst är aktiverad för alla virtuella datorer. Du kan inaktivera seriekonsolen på prenumerationsnivån eller VM-nivå.

### <a name="subscription-level-disable"></a>Prenumerationsnivå inaktivera
Seriell konsol kan inaktiveras för en hel prenumeration genom att via den [inaktivera konsolen REST API-anrop](https://aka.ms/disableserialconsoleapi). Du kan använda ”prova” funktionerna som är tillgängliga på sidan API-dokumentationen om du inaktiverar och aktiverar Seriekonsol för en prenumeration. Ange din `subscriptionId`, ”standard” i den `default` fältet och klicka på Kör. Azure CLI-kommandon är inte tillgängliga ännu och anländer vid ett senare tillfälle. [Testa REST API-anrop här](https://aka.ms/disableserialconsoleapi).

![](../media/virtual-machines-serial-console/virtual-machine-serial-console-rest-api-try-it.png)

Du kan också använda uppsättningen kommandon nedan i Cloud Shell (bash-kommandon visas) för att inaktivera, aktivera och visa inaktiverat status för seriell konsol för en prenumeration. 

* Hämta inaktiverad status för seriell konsol för en prenumeration:
    ```
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"')) 

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s | jq .properties
    ```
* Inaktivera seriekonsol för en prenumeration:
    ```
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"')) 

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl -X POST "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default/disableConsole?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s -H "Content-Length: 0"
    ```
* Så här aktiverar Seriell konsol för en prenumeration:
    ```
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"')) 

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl -X POST "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default/enableConsole?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s -H "Content-Length: 0"
    ```

### <a name="vm-level-disable"></a>Inaktivera för VM-nivå
Seriell konsol kan inaktiveras för specifika virtuella datorer genom att inaktivera inställningen för den virtuella datorn boot diagnostics. Helt enkelt inaktivera startdiagnostik från Azure-portalen och seriekonsolen kommer att inaktiveras för den virtuella datorn.

## <a name="serial-console-security"></a>Seriell konsol-säkerhet 

### <a name="access-security"></a>Åtkomstsäkerhet 
Åtkomst till seriell konsol är begränsat till användare som har [VM deltagare](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) eller över åtkomst till den virtuella datorn. Om din AAD-klient kräver Multi-Factor Authentication och sedan åtkomst till seriell konsol måste också MFA eftersom dess åtkomst sker via [Azure-portalen](https://portal.azure.com).

### <a name="channel-security"></a>Kanalsäkerhet
Alla data som skickas fram och tillbaka krypteras under överföringen.

### <a name="audit-logs"></a>Granskningsloggar
All åtkomst till seriekonsol för tillfället är inloggad på [startdiagnostik](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) loggarna för den virtuella datorn. Åtkomst till de här loggarna ägs och kontrolleras av administratören för Azure virtuella datorer.  

>[!CAUTION] 
Även om inga lösenord för åtkomst för konsolen loggas om kommandon körs i konsolen innehåller eller utgående lösenord, hemligheter, användarnamn eller någon annan form av personligt identifierbar Information (PII), skrivs de till startdiagnostik för virtuell dator loggfiler, tillsammans med alla andra synlig text, som en del av implementeringen av Seriell konsol rullar tillbaka funktioner. Dessa loggar är cirkulär och endast personer med läsbehörighet till lagringskontot för diagnostik har åtkomst till dem, men vi rekommenderar att du följer bästa praxis för att använda Remote Desktop för allt som kan omfatta hemligheter och/eller personligt identifierbar information. 

### <a name="concurrent-usage"></a>Samtidig användning
Om en användare är ansluten till seriell konsol och en annan användare begär har åtkomst till den samma virtuella datorn, kommer att kopplas från den första användaren och den andra användaren som är anslutna på ett sätt som den första användaren ställa upp och låta den fysiska konsolen och en ny användare som sitter.

>[!CAUTION] 
Det innebär att den användare som kopplas inte kommer att loggas ut! Möjlighet att framtvinga en utloggning vid frånkoppling (via SIGHUP eller liknande mekanism) är fortfarande i översikten. För Windows finns en automatisk tidsgräns har aktiverats i SAC, men du kan konfigurera terminal timeoutinställning för Linux. 


## <a name="access-serial-console-for-windows"></a>Åtkomst Seriekonsol för Windows 
Nyare Windows Server-avbildningar på Azure har [särskilda administrationskonsolen](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) (SAC) aktiverad som standard. SAC stöds på server-versioner av Windows men är inte tillgänglig på klientversioner (till exempel Windows 10, Windows 8 eller Windows 7). Om du vill aktivera seriekonsol för Windows-datorer som skapats med hjälp av använder Feb2018 eller lägre avbildningar du följande steg: 

1. Ansluta till din Windows-dator via fjärrskrivbord
2. Kör följande kommandon från en administrativ kommandotolk 
* `bcdedit /ems {current} on`
* `bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200`
3. Starta om systemet för SAC-konsolen ska vara aktiverat

![](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

Om nödvändiga SAC kan aktiveras offline 

1. Ansluta windows-disken som du vill att SAC som konfigurerats för som en datadisk till en befintlig virtuell dator. 
2. Kör följande kommandon från en administrativ kommandotolk 
* `bcdedit /store <mountedvolume>\boot\bcd /ems {default} on`
* `bcdedit /store <mountedvolume>\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

### <a name="how-do-i-know-if-sac-is-enabled-or-not"></a>Hur vet jag om SAC har aktiverats eller inte 

Om [SAC] (https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) aktiveras inte seriekonsolen visas inte SAC-prompten. Det kan visa information om en VM-hälsa i vissa fall eller det skulle vara tomt.  

### <a name="enabling-boot-menu-to-show-in-the-serial-console"></a>Aktivera startmenyn för att visa i seriekonsolen 

Om du måste aktivera Windows boot loader uppmanas du för att visa i seriekonsolen, du kan lägga till följande ytterligare alternativ i startprogrammet för Windows.

1. Ansluta till din Windows-dator via fjärrskrivbord
2. Kör följande kommandon från en administrativ kommandotolk 
* `bcdedit /set {bootmgr} displaybootmenu yes`
* `bcdedit /set {bootmgr} timeout 5`
* `bcdedit /set {bootmgr} bootems yes`
3. Starta om datorn för Start-menyn är aktiverat

> [!NOTE] 
> I den här punkt-stöd för funktionen nycklar inte är aktiverad ser om du behöver avancerade startalternativ använda bcdedit/set {aktuella} onetimeadvancedoptions på [bcdedit](https://docs.microsoft.com/windows-hardware/drivers/devtest/bcdedit--set) för mer information

## <a name="using-serial-console-for-nmi-calls-in-windows-vms"></a>Med Seriekonsol för NMI anropar i virtuella Windows-datorer
Ett icke-maskable avbrott (NMI) är utformad för att skapa en signal som inte kommer att ignorera programvara på en virtuell dator. Historiskt sett använts NMIs för att övervaka maskinvarufel på system som krävs för specifika svarstider.  Idag, programmerare och systemadministratörer använder ofta NMI som en mekanism för att felsöka eller felsöka system som har hängt.

Seriekonsol kan användas för att skicka en NMI till en Azure virtuell dator med hjälp av tangentbordsikonen i kommandofältet visas nedan. När NMI levereras, ska konfigurationen av virtuella datorn styra hur systemet svarar. Windows kan konfigureras att krascher och skapa en minnesdump när du tar emot en NMI.

![](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-nmi.png) <br>

Information om hur du konfigurerar Windows för att skapa en kraschdumpfil när den får en NMI finns: [hur du skapar en fullständig kraschdumpfil eller en kernel-kraschdumpfil genom att använda en NMI på en Windows-dator](https://support.microsoft.com/en-us/help/927069/how-to-generate-a-complete-crash-dump-file-or-a-kernel-crash-dump-file)


## <a name="errors"></a>Fel
De flesta felen är tillfälliga i karaktär och sedan försöka anslutningsadressen dessa. Tabellen nedan visar en lista över fel och minskning 

Fel                            |   Åtgärd 
:---------------------------------|:--------------------------------------------|
Det gick inte att hämta inställningarna för startdiagnostik för '<VMNAME>'. Se till att startdiagnostik har aktiverats för den här virtuella datorn om du vill använda seriekonsolen. | Kontrollera att den virtuella datorn har [startdiagnostik](boot-diagnostics.md) aktiverat. 
Den virtuella datorn är i tillståndet stoppad frigjord. Starta den virtuella datorn och försök Seriell konsol-anslutning. | Virtuell dator måste vara i tillståndet igång för att komma åt seriekonsolen
Du har inte behörighet att använda den här virtuella datorn från seriell konsol. Kontrollera att du har minst deltagarbehörigheter för virtuell dator.| Seriell konsolåtkomst kräver vissa behörighet att komma åt. Se [åtkomstbehörigheter](#prerequisites) information
Det går inte att fastställa resursgruppen för startdiagnostiklagringskonto '<STORAGEACCOUNTNAME>'. Kontrollera att startdiagnostik har aktiverats för den här virtuella datorn och du har åtkomst till det här lagringskontot. | Seriell konsolåtkomst kräver vissa behörighet att komma åt. Se [åtkomstbehörigheter](#prerequisites) information
Ett ”förbjuden”-svar påträffades vid åtkomst till den här Virtuella datorns lagringskonto för startdiagnostik. | Kontrollera att startdiagnostik inte har en brandvägg för kontot. Ett lagringskonto för tillgänglig startdiagnostik är nödvändigt för seriekonsolen ska fungera.
Web socket är stängd eller kunde inte öppnas. | Du kan behöva godkänna `*.console.azure.com`. En mer detaljerad men längre metod är att godkänna den [Microsoft Azure Datacenter IP-intervall](https://www.microsoft.com/en-us/download/details.aspx?id=41653), som ändras relativt regelbundet.

## <a name="known-issues"></a>Kända problem 
Eftersom vi är fortfarande i förhandsstadium för seriell konsolåtkomst, vi arbetar dig igenom några kända problem, nedan är en lista över dessa med möjliga lösningar 

Problem                             |   Åtgärd 
:---------------------------------|:--------------------------------------------|
Det finns inget alternativ med VM scale set-instans från seriell konsol | Åtkomst till seriekonsol för VM-skalningsuppsättningsinstanser stöds inte vid tidpunkten för förhandsversionen.
Träffa ange när anslutningen popup-meddelandet inte visas en logg i Kommandotolken | [Träffa ange gör ingenting](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md)
Endast hälsoinformation visas när du ansluter till en virtuell Windows-dator| [Windows hälsotillstånd signaler](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md)
Det går inte att skriva vid SAC fråga om kernel-felsökning är aktiverad | RDP till den virtuella datorn och köra `bcdedit /debug {current} off` från en upphöjd kommandotolk. Om du kan inte använda RDP du i stället kan koppla OS-disken till en annan virtuell Azure-dator och ändra den när ansluten som en disk med `bcdedit /store <drive letter of data disk>:\boot\bcd /debug <identifier> off`, sedan växla tillbaka disken.
Klistra in i PowerShell i SAC resulterar i ett tredje tecken om ursprungliga innehållet hade ett upprepade tecken | En lösning är att ta bort modulen PSReadLine. `Remove-Module PSReadLine` tar bort modulen PSReadLine från den aktuella sessionen.
Vissa tangentbord indata producerar utdata som onormalt SAC (t.ex. `[A`, `[3~`) | [VT100](https://aka.ms/vtsequences) escape-sekvenser stöds inte av SAC-prompten.
Ett ”förbjuden”-svar påträffades vid åtkomst till den här Virtuella datorns lagringskonto för startdiagnostik. | Kontrollera att startdiagnostik inte har en brandvägg för kontot. Ett lagringskonto för tillgänglig startdiagnostik är nödvändigt för seriekonsolen ska fungera.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar 
**FRÅGOR OCH. Hur kan jag skicka feedback?**

A. Ge feedback som ett problem genom att gå till https://aka.ms/serialconsolefeedback. Du kan också mindre (rekommenderas) skicka feedback via azserialhelp@microsoft.com eller i kategori för virtuell dator av http://feedback.azure.com

**FRÅGOR OCH. Jag kan inte komma åt seriekonsolen var kan jag registrera ett supportärende?**

A. Den här förhandsversionsfunktionen täcks via Azure-förhandsversioner. Stöd för detta hanteras bäst via kanaler som nämns ovan. 

## <a name="next-steps"></a>Nästa steg
* En detaljerad guide till CMD- och PowerShell-kommandon som du kan använda i Windows-SAC klickar du på [här](serial-console-cmd-ps-commands.md).
* Seriekonsolen är också tillgängligt för [Linux](../linux/serial-console.md) virtuella datorer.
* Läs mer om [startdiagnostik](boot-diagnostics.md).
