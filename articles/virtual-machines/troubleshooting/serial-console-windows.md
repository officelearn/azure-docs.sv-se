---
title: Azures serie konsol för Windows | Microsoft Docs
description: Dubbelriktad serie konsol för Azure Virtual Machines och Virtual Machine Scale Sets med hjälp av ett Windows-exempel.
services: virtual-machines-windows
documentationcenter: ''
author: asinn826
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 5/1/2019
ms.author: alsin
ms.openlocfilehash: c5c139cb94358d70d1f23b68f2a369adb953da08
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91325988"
---
# <a name="azure-serial-console-for-windows"></a>Azures serie konsol för Windows

Serie konsolen i Azure Portal ger åtkomst till en textbaserad konsol för virtuella Windows-datorer och instanser av skalnings uppsättningar för virtuella datorer. Den här seriella anslutningen ansluter till den seriella portarna för den virtuella datorn eller den virtuella datorns skalnings uppsättning och ger åtkomst till den oberoende av nätverket eller operativ systemets tillstånd. Det går bara att komma åt serie konsolen med hjälp av Azure Portal och tillåts bara för de användare som har åtkomst rollen deltagare eller högre till den virtuella datorn eller skalnings uppsättningen för den virtuella datorn.

Serie konsolen fungerar på samma sätt för virtuella datorer och instanser av skalnings uppsättningar för virtuella datorer. I det här dokumentet kommer alla omnämnanden för virtuella datorer att implicit inkludera skalnings uppsättnings instanser för virtuella datorer om inget annat anges.

Serie konsolen är allmänt tillgänglig i globala Azure-regioner och i offentlig för hands version i Azure Government. Den är ännu inte tillgänglig i Azure Kina-molnet.

Dokumentation om Azure-konsolen för Linux finns i [Azures serie konsol för Linux](serial-console-linux.md).

> [!NOTE]
> Serie konsolen är för närvarande inte kompatibel med ett lagrings konto för Managed Boot Diagnostics. Se till att du använder ett anpassat lagrings konto för att använda en serie konsol.


## <a name="prerequisites"></a>Förutsättningar

* Den virtuella datorn eller den virtuella datorns skalnings uppsättnings instans måste använda distributions modellen för resurs hantering. Klassiska distributioner stöds inte.

- Ditt konto som använder en serie konsol måste ha [rollen virtuell dator deltagare](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) för den virtuella datorn och lagrings kontot för [startdiagnostik](boot-diagnostics.md)

- Den virtuella datorn eller den virtuella datorns skalnings uppsättnings instans måste ha en lösenordsbaserad användare. Du kan skapa en med funktionen [Återställ lösen ord](../extensions/vmaccess.md#reset-password) för VM Access-tillägget. Välj **Återställ lösen ord** i avsnittet **support och fel sökning** .

* Startdiagnostik måste vara aktiverat för den virtuella datorns [boot diagnostics](boot-diagnostics.md) skalnings uppsättning instans för virtuell dator.

    ![Inställningar för startdiagnostik](../media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

## <a name="enable-serial-console-functionality-for-windows-server"></a>Aktivera funktioner för seriell konsol för Windows Server

> [!NOTE]
> Om du inte ser något i serie konsolen ser du till att startdiagnostik är aktiverat på den virtuella datorn eller skalnings uppsättningen för den virtuella datorn.

### <a name="enable-the-serial-console-in-custom-or-older-images"></a>Aktivera serie konsolen i anpassade eller äldre avbildningar
Nya Windows Server-avbildningar på Azure har SAC ( [Special administrations konsol](/previous-versions/windows/it-pro/windows-server-2003/cc787940(v=ws.10)) ) aktiverat som standard. SAC stöds på Server versioner av Windows, men är inte tillgänglig på klient versioner (till exempel Windows 10, Windows 8 eller Windows 7).

För äldre Windows Server-avbildningar (som skapats före februari 2018) kan du automatiskt aktivera den seriella konsolen via kommando funktionen kör i Azure Portal. I Azure Portal väljer du **Kör kommando**och väljer sedan kommandot med namnet **EnableEMS** i listan.

![Kör kommando lista](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-runcommand.png)

Om du vill aktivera seriell konsol manuellt för virtuella Windows-datorer och skalnings uppsättningar för virtuella datorer som skapats före februari 2018, följer du dessa steg:

1. Ansluta till din virtuella Windows-dator med hjälp av fjärr skrivbord
1. Kör följande kommandon från en administrativ kommando tolk:
    - `bcdedit /ems {current} on`, eller `bcdedit /ems '{current}' on` om du använder PowerShell
    - `bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200`
1. Starta om systemet för att aktivera SAC-konsolen.

    ![SAC-konsol](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

Vid behov kan SAC aktive ras även i frånkopplat läge:

1. Anslut Windows-disken som du vill att SAC ska konfigureras som en datadisk till den befintliga virtuella datorn.

1. Kör följande kommandon från en administrativ kommando tolk:
   - `bcdedit /store <mountedvolume>\boot\bcd /ems {default} on`
   - `bcdedit /store <mountedvolume>\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

#### <a name="how-do-i-know-if-sac-is-enabled"></a>Hur gör jag för att vet du om SAC är aktiverat?

Om [SAC](/previous-versions/windows/it-pro/windows-server-2003/cc787940(v=ws.10)) inte är aktive rad visas inte SAC-prompten i serie konsolen. I vissa fall visas information om VM-hälsa och i andra fall är det tomt. Om du använder en Windows Server-avbildning som skapats före februari 2018 kommer SAC förmodligen inte att aktive ras.

### <a name="enable-the-windows-boot-menu-in-the-serial-console"></a>Aktivera Start menyn i Windows i serie konsolen

Om du behöver aktivera Windows Start inläsnings meddelanden för att visa i serie konsolen kan du lägga till följande ytterligare alternativ i Start konfigurations data. Mer information finns i [bcdedit](/windows-hardware/drivers/devtest/bcdedit--set).

1. Anslut till en virtuell Windows-dator eller en virtuell dators skalnings uppsättnings instans med hjälp av fjärr skrivbord.

1. Kör följande kommandon från en administrativ kommando tolk:
   - `bcdedit /set {bootmgr} displaybootmenu yes`
   - `bcdedit /set {bootmgr} timeout 10`
   - `bcdedit /set {bootmgr} bootems yes`

1. Starta om systemet för att Start menyn ska aktive ras

> [!NOTE]
> Den tids gräns som du angav för start hanterarens meny att Visa påverkar start tiden för operativ systemet. Om du tror att tids gränsen på 10 sekunder är för kort eller för lång, anger du ett annat värde.

## <a name="use-serial-console"></a>Använd serie konsol

### <a name="use-cmd-or-powershell-in-serial-console"></a>Använda CMD eller PowerShell i serie konsolen

1. Anslut till serie konsolen. Om du har anslutit är meddelandet **SAC>**:

   ![Anslut till SAC](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect-sac.png)
1. Ange `cmd` om du vill skapa en kanal som har en cmd-instans.

1. Ange `ch -si 1` eller tryck på `<esc>+<tab>` kortkommandon för att växla till den kanal som kör cmd-instansen.

1. Tryck på **RETUR**och ange sedan inloggnings uppgifter med administratörs behörighet.

1. När du har angett giltiga autentiseringsuppgifter öppnas CMD-instansen.

1. Starta en PowerShell-instans genom att ange `PowerShell` i cmd-instansen och tryck på **RETUR**.

   ![Öppna PowerShell-instans](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-powershell.png)

### <a name="use-the-serial-console-for-nmi-calls"></a>Använda serie konsolen för NMI-anrop
Ett icke-maskbart avbrott (NMI) är utformat för att skapa en signal som program varan på en virtuell dator inte kommer att ignorera. Historiskt sett har NMIs använts för att övervaka maskin varu problem på system som krävde specifika svars tider. Idag använder programmerare programmerare och system administratörer ofta NMI som en mekanism för att felsöka eller felsöka system som inte svarar.

Du kan använda serie konsolen för att skicka en NMI till en virtuell Azure-dator med hjälp av tangent bords ikonen i kommando fältet. När NMI har levererats kontrollerar konfigurationen för den virtuella datorn hur systemet svarar. Windows kan konfigureras för att krascha och skapa en minnesdumpfil när en NMI tas emot.

![Skicka NMI](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-nmi.png) <br>

Information om hur du konfigurerar Windows för att skapa en kraschdumpfil när den får en NMI finns i [så här skapar du en kraschdumpfil med hjälp av en NMI](https://support.microsoft.com/help/927069/how-to-generate-a-complete-crash-dump-file-or-a-kernel-crash-dump-file).

### <a name="use-function-keys-in-serial-console"></a>Använda funktions nycklar i serie konsolen
Funktions tangenter har Aktiver ATS för användning av serie konsolen i virtuella Windows-datorer. F8 i list rutan för serie konsolen ger dig möjlighet att enkelt ange menyn avancerade Start Inställningar, men serie konsolen är kompatibel med alla andra funktions nycklar. Du kan behöva trycka på **FN**  +  **F1** (eller F2, F3 osv.) på tangent bordet, beroende på vilken dator du använder för att använda en serie konsol från.

### <a name="use-wsl-in-serial-console"></a>Använda WSL i serie konsolen
Windows-undersystemet för Linux (WSL) har Aktiver ATS för Windows Server 2019 eller senare, så det är också möjligt att aktivera WSL för användning i serie konsolen om du kör Windows Server 2019 eller senare. Det kan vara fördelaktigt för användare som också är bekant med Linux-kommandon. Instruktioner för att aktivera WSL för Windows Server finns i [installations guiden](/windows/wsl/install-on-server)för.

### <a name="restart-your-windows-vmvirtual-machine-scale-set-instance-within-serial-console"></a>Starta om din instans av skalnings uppsättningen för virtuella Windows-datorer/virtuella datorer i serie konsolen
Du kan starta en omstart i serie konsolen genom att gå till ström knappen och klicka på "starta om virtuell dator". Detta initierar en omstart av datorn och ett meddelande visas i Azure Portal om omstarten.

Detta är användbart i situationer där du kanske vill komma åt Start menyn utan att lämna serie konsol upplevelsen.

![Starta om Windows seriell konsol](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-windows.gif)

## <a name="disable-the-serial-console"></a>Inaktivera serie konsolen
Som standard har alla prenumerationer åtkomst till seriell konsol. Du kan inaktivera serie konsolen antingen på prenumerations nivå eller skal uppsättnings nivå för virtuell dator/virtuell dator. Detaljerade anvisningar finns [i Aktivera och inaktivera Azures serie konsol](./serial-console-enable-disable.md).

## <a name="serial-console-security"></a>Seriell konsol säkerhet

### <a name="access-security"></a>Åtkomst säkerhet
Åtkomst till serie konsolen är begränsad till användare som har åtkomst rollen [virtuell dator deltagare](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) eller högre till den virtuella datorn. Om din Azure Active Directory klient kräver Multi-Factor Authentication (MFA) behöver åtkomst till serie konsolen även MFA eftersom den seriella konsolens åtkomst sker via [Azure Portal](https://portal.azure.com).

### <a name="channel-security"></a>Kanal säkerhet
Alla data som skickas fram och tillbaka krypteras i kabeln.

### <a name="audit-logs"></a>Spårningsloggar
All åtkomst till serie konsolen är för närvarande inloggad i [Start](./boot-diagnostics.md) -diagnostikloggar för den virtuella datorn. Åtkomst till dessa loggar ägs och kontrol leras av administratören för den virtuella Azure-datorn.

> [!CAUTION]
> Inga åtkomst lösen ord för konsolen loggas. Men om kommandon som körs i-konsolen innehåller eller skickar lösen ord, hemligheter, användar namn eller någon annan form av personligt identifierbar information (PII), skrivs de till den virtuella datorns startdiagnostik loggar. De skrivs tillsammans med all annan synlig text som en del av implementeringen av den seriella konsolens funktion för att rulla bakåt. Loggarna är cirkulära och endast personer med Läs behörighet till kontot för diagnostik har åtkomst till dem. Vi rekommenderar dock att du följer det bästa syftet med att använda fjärr skrivbord för allt som kan innebära hemligheter och/eller personligt identifierbar information.

### <a name="concurrent-usage"></a>Samtidig användning
Om en användare är ansluten till serie konsolen och en annan användare kan begära åtkomst till samma virtuella dator, kommer den första användaren att kopplas från och den andra användaren som är ansluten till samma session.

> [!CAUTION]
> Det innebär att en användare som är frånkopplad inte kommer att loggas ut. Möjligheten att genomdriva en utloggning vid från koppling (med SIGHUP eller liknande mekanism) finns fortfarande i översikten. För Windows finns en automatisk timeout som är aktive rad i SAC; för Linux kan du konfigurera inställningen för Terminal-timeout.

## <a name="accessibility"></a>Tillgänglighet
Hjälpmedel är en viktig fokus för Azures serie konsol. I detta fall har vi säkerställt att serie konsolen är tillgänglig för personer med nedsatt syn, eller som är svåra att höra, samt personer som kanske inte kan använda musen.

### <a name="keyboard-navigation"></a>Tangentbordsnavigering
Använd **TABB** -tangenten på tangent bordet för att navigera i gränssnittet för den seriella konsolen från Azure Portal. Din plats kommer att vara markerad på skärmen. Tryck på **CTRL** + **F6** på tangent bordet för att lämna fönstret för serie konsolen.

### <a name="use-the-serial-console-with-a-screen-reader"></a>Använda serie konsolen med en skärm läsare
Serie konsolen har stöd för skärm läsare inbyggd. Om du navigerar runt med en skärm läsare aktive rad kan alternativ texten för den markerade knappen läsas högt av skärm läsaren.

## <a name="common-scenarios-for-accessing-the-serial-console"></a>Vanliga scenarier för åtkomst till serie konsolen

Scenario          | Åtgärder i serie konsolen
:------------------|:-----------------------------------------
Felaktiga brand Väggs regler | Gå till serie konsolen och korrigera regler för Windows-brandväggen.
Fil system skadat/kontroll | Få åtkomst till serie konsolen och Återställ fil systemet.
Problem med RDP-konfiguration | Få åtkomst till serie konsolen och ändra inställningarna. Mer information finns i RDP- [dokumentationen](/windows-server/remote/remote-desktop-services/clients/remote-desktop-allow-access).
Nätverks låsnings system | Få åtkomst till serie konsolen från Azure Portal för att hantera systemet. Vissa nätverks kommandon visas i [Windows-kommandon: cmd och PowerShell](serial-console-cmd-ps-commands.md).
Interagera med Start programmet | Öppna BCD via serie konsolen. Mer information finns i [Aktivera Start menyn i Windows i serie konsolen](#enable-the-windows-boot-menu-in-the-serial-console).

## <a name="known-issues"></a>Kända problem
Vi är medvetna om några problem med serie konsolen och den virtuella datorns operativ system. Här är en lista över de här problemen och stegen för att minska för virtuella Windows-datorer. Dessa problem och begränsningar gäller för både virtuella datorer och instanser av skalnings uppsättningar för virtuella datorer. Om de inte matchar det fel du ser kan du läsa fel i vanliga fel med seriell konsol tjänsten i [vanliga](./serial-console-errors.md)fel i serie konsolen.

Problem                             |   Åtgärd
:---------------------------------|:--------------------------------------------|
Om du trycker på **RETUR** efter det att anslutningens banderoll inte leder till att en inloggnings tolk visas. | För mer information, se att [trycka på RETUR gör ingenting](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). Det här felet kan inträffa om du kör en anpassad virtuell dator, en härdad installation eller start konfiguration som gör att Windows inte kan ansluta till den seriella porten korrekt. Det här felet uppstår också om du kör en virtuell Windows 10-dator, eftersom endast virtuella Windows Server-datorer har kon figurer ATS för att ha EMS aktiverat.
Endast hälso information visas när du ansluter till en virtuell Windows-dator| Det här felet uppstår om den särskilda administrations konsolen inte har Aktiver ATS för Windows-avbildningen. Se [Aktivera serie konsolen i anpassade eller äldre avbildningar](#enable-the-serial-console-in-custom-or-older-images) för instruktioner om hur du aktiverar SAC manuellt på din virtuella Windows-dator. Mer information finns i [Windows hälso signaler](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md).
SAC tar inte upp hela det seriella konsol utrymmet i webbläsaren | Detta är ett känt problem med Windows och termin Ale mula torn. Vi spårar det här problemet med båda teamen men för närvarande finns det ingen minskning.
Det går inte att skriva vid SAC-prompt om kernel-felsökning är aktiverat. | RDP till virtuell dator och köra `bcdedit /debug {current} off` från en upphöjd kommando tolk. Om du inte kan använda RDP kan du i stället koppla OS-disken till en annan virtuell Azure-dator och ändra den när den är ansluten som en datadisk genom att köra `bcdedit /store <drive letter of data disk>:\boot\bcd /debug <identifier> off` och sedan växla tillbaka till disken.
När du klistrar in i PowerShell i SAC visas ett tredje steg om det ursprungliga innehållet hade ett upprepande. | Om du vill ha en lösning kan `Remove-Module PSReadLine` du köra för att ta bort PSReadLine-modulen från den aktuella sessionen. Denna åtgärd tar inte bort eller avinstallerar modulen.
Vissa tangent bords inmatningar ger konstiga SAC-utdata (till exempel **[A**, **[3 ~**). | [VT100](https://aka.ms/vtsequences) escape-sekvenser stöds inte av SAC-prompten.
Att klistra in långa strängar fungerar inte. | Serie konsolen begränsar längden på strängar som klistras in i terminalen till 2048 tecken för att förhindra överbelastning av bandbredden för den seriella porten.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**C. Hur gör jag för att skicka feedback?**

A. Ge feedback genom att skapa ett GitHub-problem på https://aka.ms/serialconsolefeedback . Alternativt (mindre önskad) kan du skicka feedback via azserialhelp@microsoft.com eller i kategorin virtuell dator för https://feedback.azure.com .

**C. Har serie konsolen stöd för kopiera/klistra in?**

A. Ja. Använd **CTRL** + **Shift** + **C** och **CTRL** + **Shift** + **V** för att kopiera och klistra in i terminalen.

**C. Vem kan aktivera eller inaktivera serie konsolen för min prenumeration?**

A. Om du vill aktivera eller inaktivera serie konsolen på en prenumerations nivå måste du ha Skriv behörighet till prenumerationen. Roller som har Skriv behörighet inkluderar administratörs-eller ägar roller. Anpassade roller kan också ha Skriv behörighet.

**C. Vem har åtkomst till serie konsolen för min virtuella dator?**

A. Du måste ha rollen virtuell dator deltagare eller högre för att en virtuell dator ska kunna få åtkomst till den virtuella datorns serie konsol.

**C. Min serie konsol visar inte något, vad gör jag?**

A. Avbildningen är troligt vis felkonfigurerad för åtkomst till serie konsol. Information om hur du konfigurerar din avbildning för att aktivera serie konsolen finns i [Aktivera serie konsolen i anpassade eller äldre avbildningar](#enable-the-serial-console-in-custom-or-older-images).

**C. Är serie konsolen tillgänglig för skalnings uppsättningar för virtuella datorer?**

A. Ja det är det! Se [serie konsolen för Virtual Machine Scale Sets](./serial-console-overview.md#serial-console-for-virtual-machine-scale-sets)

## <a name="next-steps"></a>Nästa steg
* En djupgående guide till CMD-och PowerShell-kommandon som du kan använda i Windows SAC finns i [Windows-kommandon: cmd och PowerShell](serial-console-cmd-ps-commands.md).
* Serie konsolen är också tillgänglig för virtuella [Linux](serial-console-linux.md) -datorer.
* Läs mer om [startdiagnostik](boot-diagnostics.md).
