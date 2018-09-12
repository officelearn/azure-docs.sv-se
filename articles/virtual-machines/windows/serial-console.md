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
ms.openlocfilehash: 1bb6e464b748f2558cec35a95554bb3e08b667f0
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2018
ms.locfileid: "44378337"
---
# <a name="virtual-machine-serial-console-preview"></a>Virtual Machine Serial Console (förhandsversion) 


Virtual Machine Serial Console i Azure ger åtkomst till en textbaserad konsol för Windows-datorer. Den här seriell anslutning är att den seriella porten COM1 för den virtuella datorn, som ger tillgång till den virtuella datorn som är oberoende av en virtuell dators nätverks- eller operating system-tillstånd. Åtkomsten till seriekonsol för en virtuell dator för närvarande kan endast göras via Azure portal och tillåts endast för de användare som har VM-deltagare eller senare åtkomst till den virtuella datorn. 

Seriell konsol dokumentation för virtuella Linux-datorer [Klicka här](../linux/serial-console.md).

> [!Note] 
> Förhandsversioner görs tillgängliga för dig på villkor att du godkänner användningsvillkoren. Mer information finns i [Microsoft Azure kompletterande användningsvillkor för förhandsversioner av Microsoft Azure.] (https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Den här tjänsten är för närvarande i **förhandsversion** och åtkomst till seriekonsol för virtuella datorer är tillgänglig för globala Azure-regioner. I det här läget är seriell konsol inte tillgängliga Azure Government, Azure Tyskland och Azure Kina-molnet.

 

## <a name="prerequisites"></a>Förutsättningar 

* Du måste använda resource management-distributionsmodellen. Klassiska distributioner stöds inte. 
* Virtuell dator måste ha [startdiagnostik](boot-diagnostics.md) aktiverat 

    ![](../media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

* Det konto som använder seriekonsolen måste ha [deltagarrollen](../../role-based-access-control/built-in-roles.md) för den virtuella datorn och [startdiagnostik](boot-diagnostics.md) storage-konto. 
* Den virtuella datorn som du är acessing Seriell konsol måste också ha ett konto för lösenordsbaserad. Du kan skapa en med den [Återställ lösenord](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) funktionerna i VM access-tillägg - se skärmbilden nedan.

    ![](../media/virtual-machines-serial-console/virtual-machine-serial-console-reset-password.png)

## <a name="get-started-with-serial-console"></a>Kom igång med Seriekonsol
Seriekonsol för virtuella datorer bara kan nås via [Azure-portalen](https://portal.azure.com). Nedan följer du stegen för att komma åt seriekonsol för virtuella datorer via portalen.

  1. Öppna Azure portal
  2. Välj virtuella datorer i den vänstra menyn.
  3. Klicka på den virtuella datorn i listan. Översiktssidan för den virtuella datorn öppnas.
  4. Rulla ned till avsnittet om Support och felsökning och klicka på alternativet seriekonsol (förhandsversion). Ett nytt fönster med seriell konsol öppnas och starta anslutningen.

![](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

## <a name="enable-serial-console-in-custom-or-older-images"></a>Aktivera Seriekonsolen i anpassade eller äldre bilder
Nyare Windows Server-avbildningar på Azure har [särskilda administrationskonsolen](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) (SAC) aktiverad som standard. SAC stöds på server-versioner av Windows men är inte tillgänglig på klientversioner (till exempel Windows 10, Windows 8 eller Windows 7). Om du vill aktivera seriekonsol för Windows-datorer som skapats före februari 2018, använder du följande steg: 

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

### <a name="how-do-i-know-if-sac-is-enabled"></a>Hur vet jag om SAC är aktiverad?

Om [SAC] (https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) aktiveras inte seriekonsolen visas inte SAC-prompten. Hälsoinformation för virtuell dator visas i vissa fall och i andra fall är det tomt. Om du använder en Windows Server-avbildning som skapats före februari 2018 kommer SAC troligen inte att aktivera.

## <a name="enable-the-windows-boot-menu-in-serial-console"></a>Aktivera Windows-startmenyn i Seriekonsol 

Om du måste aktivera Windows boot loader uppmanas du för att visa i seriekonsolen, du kan lägga till följande ytterligare alternativ till dina boot configuration data. Se [bcdedit](https://docs.microsoft.com/windows-hardware/drivers/devtest/bcdedit--set) för mer information

1. Ansluta till din Windows-dator via fjärrskrivbord
2. Kör följande kommandon från en administrativ kommandotolk 
* `bcdedit /set {bootmgr} displaybootmenu yes`
* `bcdedit /set {bootmgr} timeout 5`
* `bcdedit /set {bootmgr} bootems yes`
3. Starta om datorn för Start-menyn är aktiverat

## <a name="use-serial-console-for-nmi-calls-in-windows-vms"></a>Använd Seriekonsol för NMI anrop i Windows-datorer
Ett icke-maskable avbrott (NMI) är utformad för att skapa en signal som inte kommer att ignorera programvara på en virtuell dator. Historiskt sett använts NMIs för att övervaka maskinvarufel på system som krävs för specifika svarstider.  Idag, programmerare och systemadministratörer använder ofta NMI som en mekanism för att felsöka eller felsöka system som har hängt.

Seriekonsol kan användas för att skicka en NMI till en Azure virtuell dator med hjälp av tangentbordsikonen i kommandofältet visas nedan. När NMI levereras, ska konfigurationen av virtuella datorn styra hur systemet svarar. Windows kan konfigureras att krascher och skapa en minnesdump när du tar emot en NMI.

![](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-nmi.png) <br>

Information om hur du konfigurerar Windows för att skapa en kraschdumpfil när den får en NMI finns: [hur du skapar en fullständig kraschdumpfil eller en kernel-kraschdumpfil genom att använda en NMI på en Windows-dator](https://support.microsoft.com/en-us/help/927069/how-to-generate-a-complete-crash-dump-file-or-a-kernel-crash-dump-file)

## <a name="disable-serial-console"></a>Inaktivera Seriekonsol
Som standard har alla prenumerationer seriell konsolåtkomst är aktiverad för alla virtuella datorer. Du kan inaktivera seriekonsolen på prenumerationsnivån eller VM-nivå. 

> [!Note] 
> För att aktivera eller inaktivera seriekonsol för en prenumeration, måste du ha skrivbehörighet till prenumerationen. Detta omfattar, men är inte begränsat till administratörer eller ägare. Anpassade roller kan också ha skrivbehörighet.

### <a name="subscription-level-disable"></a>Prenumerationsnivå inaktivera
Seriell konsol kan inaktiveras för en hel prenumeration genom att via den [inaktivera konsolen REST API-anrop](https://aka.ms/disableserialconsoleapi). Du kan använda ”prova” funktionerna som är tillgängliga på sidan API-dokumentationen om du inaktiverar och aktiverar Seriekonsol för en prenumeration. Ange din `subscriptionId`, ”standard” i den `default` fältet och klicka på Kör. Azure CLI-kommandon är inte tillgängliga ännu och anländer vid ett senare tillfälle. [Testa REST API-anrop här](https://aka.ms/disableserialconsoleapi).

![](../media/virtual-machines-serial-console/virtual-machine-serial-console-rest-api-try-it.png)

Du kan också använda uppsättningen kommandon nedan i Cloud Shell (bash-kommandon visas) för att inaktivera, aktivera och visa inaktiverat status för seriell konsol för en prenumeration. 

* Hämta inaktiverad status för seriell konsol för en prenumeration:
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"')) 

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s | jq .properties
    ```
* Inaktivera seriekonsol för en prenumeration:
    ```azurecli-interactive 
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"')) 

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl -X POST "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default/disableConsole?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s -H "Content-Length: 0"
    ```
* Så här aktiverar Seriell konsol för en prenumeration:
    ```azurecli-interactive
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

## <a name="common-scenarios-for-accessing-serial-console"></a>Vanliga scenarier för att komma åt seriekonsol 
Scenario          | Åtgärder i seriekonsol                
:------------------|:-----------------------------------------
Felaktig brandväggsregler | Åtkomst till seriella konsolen och åtgärda Windows brandväggsregler. 
Filsystem skadade/kontroll | Få åtkomst till seriell konsol och Återställ filsystem. 
Problem med RDP-konfigurationen | Komma åt Seriell konsol och ändra inställningarna. Gå till den [RDP-dokumentationen](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-allow-access) att komma igång.
Nätverket låsa system| Åtkomst seriekonsolen via portalen för att hantera system. Vissa nätverkskommandon finns i den [seriella konsolen CMD och PowerShell-dokumentationen](./serial-console-cmd-ps-commands.md). 
Interagera med startprogrammet | Åtkomst BCD via seriekonsolen. Gå till [aktiverar startmenyn ska visas i seriekonsolen](#enabling-boot-menu-to-show-in-the-serial-console) att komma igång. 

## <a name="accessibility"></a>Hjälpmedel
Hjälpmedel är viktiga fokus för Azure seriekonsol. Därför har vi sett att seriekonsolen är tillgänglig för de med visuella objekt och hörselnedsättning samt personer som inte kanske kan använda en mus.

### <a name="keyboard-navigation"></a>Tangentbordsnavigering
Använd den `tab` på tangentbordet för att flytta runt Seriell konsol-gränssnittet i Azure-portalen. Din plats kommer vara markerad på skärmen. Om du vill lämna fokus på bladet för seriell konsol, trycker du på `Ctrl + F6` på tangentbordet.

### <a name="use-serial-console-with-a-screen-reader"></a>Använda Seriell konsol med en skärmläsare
Seriell konsol levereras med stöd för Skärmläsaren inbyggda. Navigera med en skärmläsare aktiveras kan alternativ text för knappen är markerade som ska läsas upp av Skärmläsaren.

## <a name="errors"></a>Fel
De flesta felen är tillfälliga i karaktär och sedan försöka anslutningsadressen dessa. Tabellen nedan visar en lista över fel och åtgärder

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
Träffa ange när anslutningen popup-meddelandet inte visas en logg i Kommandotolken | Finns på följande sida: [Hitting ange ingenting](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). Detta kan inträffa om du använder en anpassad virtuell dator härdade installation eller GRUB konfiguration som causers Windows för att kunna ansluta ordentligt till den seriella porten.
Endast hälsoinformation visas när du ansluter till en virtuell Windows-dator| Detta kommer att visas om den särskilda administrationskonsolen inte har aktiverats för din Windows-avbildning. Se [åtkomst Seriell konsol för Windows](#access-serial-console-for-windows) för instruktioner om hur du manuellt Aktivera SAC på din virtuella Windows-dator. Mer information finns på [Windows hälsotillstånd signaler](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md).
Det går inte att skriva vid SAC fråga om kernel-felsökning är aktiverad | RDP till den virtuella datorn och köra `bcdedit /debug {current} off` från en upphöjd kommandotolk. Om du kan inte använda RDP du i stället kan koppla OS-disken till en annan virtuell Azure-dator och ändra den när ansluten som en disk med `bcdedit /store <drive letter of data disk>:\boot\bcd /debug <identifier> off`, sedan växla tillbaka disken.
Klistra in i PowerShell i SAC resulterar i ett tredje tecken om ursprungliga innehållet hade ett upprepade tecken | En lösning är att ta bort modulen PSReadLine från den aktuella sessionen. Kör `Remove-Module PSReadLine` att ta bort modulen PSReadLine från den aktuella sessionen – det här inte ta bort eller avinstallera modulen.
Vissa tangentbord indata producerar utdata som onormalt SAC (t.ex. `[A`, `[3~`) | [VT100](https://aka.ms/vtsequences) escape-sekvenser stöds inte av SAC-prompten.
Ett ”förbjuden”-svar påträffades vid åtkomst till den här Virtuella datorns lagringskonto för startdiagnostik. | Kontrollera att startdiagnostik inte har en brandvägg för kontot. Ett lagringskonto för tillgänglig startdiagnostik är nödvändigt för seriekonsolen ska fungera.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar 
**FRÅGOR OCH. Hur kan jag skicka feedback?**

A. Ge feedback som ett problem genom att gå till https://aka.ms/serialconsolefeedback. Du kan också mindre (rekommenderas) skicka feedback via azserialhelp@microsoft.com eller i kategori för virtuell dator av http://feedback.azure.com

**FRÅGOR OCH. Seriell konsol som har stöd för kopiera/klistra in?**

A. Ja sker. Använd Ctrl + Skift + C och Ctrl + Skift + V för att kopiera och klistra in i terminalen.

**FRÅGOR OCH. Vem kan aktivera eller inaktivera seriekonsol för min prenumeration?**

A. För att aktivera eller inaktivera Seriell konsol på en prenumeration hela-nivå, måste du ha skrivbehörighet till prenumerationen. Roller som har behörighet att skriva omfattar, men är inte begränsad till administratörer eller ägare. Anpassade roller kan också ha skrivbehörighet.

**FRÅGOR OCH. Vem som kan komma åt seriekonsol för min virtuella dator?**

A. Du måste ha deltagarbehörighet inom nivå eller högre till en virtuell dator för att komma åt den Virtuella datorns Seriell konsol. 

**FRÅGOR OCH. Min seriekonsolen inte visas allt, hur gör jag?**

A. Avbildningen är antagligen felkonfigurerad för seriell konsolåtkomst. Se [aktiverar Seriekonsol i anpassade eller äldre bilder](#Enable-Serial-Console-in-custom-or-older-images) mer information om hur du konfigurerar din avbildning om du vill aktivera Seriell konsol.

**FRÅGOR OCH. Är seriell konsol för Virtual Machine Scale Sets?**

A. Åtkomst till seriekonsol för VM-skalningsuppsättningsinstanser stöds inte för tillfället.

## <a name="next-steps"></a>Nästa steg
* En detaljerad guide till CMD- och PowerShell-kommandon som du kan använda i Windows-SAC klickar du på [här](serial-console-cmd-ps-commands.md).
* Seriekonsolen är också tillgängligt för [Linux](../linux/serial-console.md) virtuella datorer.
* Läs mer om [startdiagnostik](boot-diagnostics.md).
