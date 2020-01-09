---
title: Azures serie konsol för Windows | Microsoft Docs
description: Dubbelriktad serie konsol för Azure Virtual Machines och Virtual Machine Scale Sets.
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
ms.openlocfilehash: 87ccb1c4995337b385f685797980a9fc3962bc6f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75451242"
---
# <a name="azure-serial-console-for-windows"></a>Azures serie konsol för Windows

Serie konsolen i Azure Portal ger åtkomst till en textbaserad konsol för virtuella Windows-datorer och instanser av skalnings uppsättningar för virtuella datorer. Den här seriella anslutningen ansluter till den seriella portarna för den virtuella datorn eller den virtuella datorns skalnings uppsättning och ger åtkomst till den oberoende av nätverket eller operativ systemets tillstånd. Det går bara att komma åt serie konsolen med hjälp av Azure Portal och tillåts bara för de användare som har åtkomst rollen deltagare eller högre till den virtuella datorn eller skalnings uppsättningen för den virtuella datorn.

Serie konsolen fungerar på samma sätt för virtuella datorer och instanser av skalnings uppsättningar för virtuella datorer. I det här dokumentet kommer alla omnämnanden för virtuella datorer att implicit inkludera skalnings uppsättnings instanser för virtuella datorer om inget annat anges.

Dokumentation om Azure-konsolen för Linux finns i [Azures serie konsol för Linux](serial-console-linux.md).

> [!NOTE]
> Serie konsolen är allmänt tillgänglig i globala Azure-regioner och i offentlig för hands version i Azure Government. Den är ännu inte tillgänglig i Azure Kina-molnet.


## <a name="prerequisites"></a>Krav

* Den virtuella datorn eller den virtuella datorns skalnings uppsättnings instans måste använda distributions modellen för resurs hantering. Klassiska distributioner stöds inte.

- Ditt konto som använder en serie konsol måste ha [rollen virtuell dator deltagare](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) för den virtuella datorn och lagrings kontot för [startdiagnostik](boot-diagnostics.md)

- Den virtuella datorn eller den virtuella datorns skalnings uppsättnings instans måste ha en lösenordsbaserad användare. Du kan skapa en med den [Återställ lösenord](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) funktion av access-tillägg för virtuell dator. Välj **Återställ lösenord** från den **Support och felsökning** avsnittet.

* Startdiagnostik måste vara aktiverat för den virtuella datorns [](boot-diagnostics.md) skalnings uppsättning instans för virtuell dator.

    ![Inställningarna för startdiagnostik](../media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

## <a name="enable-serial-console-functionality-for-windows-server"></a>Aktivera funktioner för seriell konsol för Windows Server

> [!NOTE]
> Om du inte ser något i serie konsolen ser du till att startdiagnostik är aktiverat på den virtuella datorn eller skalnings uppsättningen för den virtuella datorn.

### <a name="enable-the-serial-console-in-custom-or-older-images"></a>Aktivera seriekonsolen i anpassade eller äldre bilder
Nya Windows Server-avbildningar på Azure har SAC ( [Special administrations konsol](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) ) aktiverat som standard. SAC stöds på server-versioner av Windows men är inte tillgängligt i klientversioner (till exempel Windows 10, Windows 8 eller Windows 7).

För äldre Windows Server-avbildningar (som skapats före februari 2018), kan du automatiskt aktivera seriekonsolen via Azure portal körningskommandot funktionen. I Azure Portal väljer du **Kör kommando**och väljer sedan kommandot med namnet **EnableEMS** i listan.

![Kör kommandolista](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-runcommand.png)

Om du vill aktivera seriell konsol manuellt för virtuella Windows-datorer och skalnings uppsättningar för virtuella datorer som skapats före februari 2018, följer du dessa steg:

1. Ansluta till din Windows-dator med hjälp av fjärrskrivbord
1. Kör följande kommandon från en administrativ kommandotolk:
    - `bcdedit /ems {current} on`
    - `bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200`
1. Starta om systemet för SAC-konsolen ska vara aktiverat.

    ![SAC-konsolen](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

Om det behövs kan SAC aktiveras offline samt:

1. Ansluta windows-disken som du vill ha SAC som konfigurerats som en datadisk till den befintliga virtuella datorn.

1. Kör följande kommandon från en administrativ kommandotolk:
   - `bcdedit /store <mountedvolume>\boot\bcd /ems {default} on`
   - `bcdedit /store <mountedvolume>\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

#### <a name="how-do-i-know-if-sac-is-enabled"></a>Hur vet jag om SAC är aktiverad?

Om [SAC](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) är inte aktiverad, seriekonsolen visas SAC-prompten. Hälsoinformation för virtuell dator visas i vissa fall och i andra fall är det tomt. Om du använder en Windows Server-avbildning som skapats före februari 2018 aktiveras SAC förmodligen inte.

### <a name="enable-the-windows-boot-menu-in-the-serial-console"></a>Aktivera Windows-startmenyn i seriekonsolen

Om du vill aktivera Windows boot loader anvisningarna för att visa i seriekonsolen kan du lägga till följande ytterligare alternativ boot configuration data. Mer information finns i [bcdedit](https://docs.microsoft.com/windows-hardware/drivers/devtest/bcdedit--set).

1. Anslut till en virtuell Windows-dator eller en virtuell dators skalnings uppsättnings instans med hjälp av fjärr skrivbord.

1. Kör följande kommandon från en administrativ kommandotolk:
   - `bcdedit /set {bootmgr} displaybootmenu yes`
   - `bcdedit /set {bootmgr} timeout 10`
   - `bcdedit /set {bootmgr} bootems yes`

1. Starta om datorn för Start-menyn är aktiverat

> [!NOTE]
> Timeout-värdet som du anger för startmenyn manager att visa påverkar din OS-starttiden. Om du tror 10 sekunder timeout-värdet är för kort eller för lång kan du ange den till ett annat värde.

## <a name="use-serial-console"></a>Använd serie konsol

### <a name="use-cmd-or-powershell-in-serial-console"></a>Använda CMD eller PowerShell i serie konsolen

1. Anslut till seriekonsol. Om du har anslutit, prompten är **SAC >** :

    ![Ansluta till SAC](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect-sac.png)

1.  Ange `cmd` att skapa en kanal som har en CMD-instans.

1.  Ange `ch -si 1` eller tryck på `<esc>+<tab>` gen vägs tangenter för att växla till den kanal som kör CMD-instansen.

1.  Tryck på **RETUR**, och ange sedan autentiseringsuppgifter med administrativ behörighet.

1.  När du har angett giltiga autentiseringsuppgifter, öppnas CMD-instans.

1.  Om du vill starta en PowerShell-instans, ange `PowerShell` i CMD-instans och tryck sedan på **RETUR**.

    ![Öppna PowerShell-instans](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-powershell.png)

### <a name="use-the-serial-console-for-nmi-calls"></a>Använd seriekonsolen för NMI-anrop
Ett icke-maskable avbrott (NMI) är utformad för att skapa en signal som programvara på en virtuell dator inte ignorera. Historiskt sett använts NMIs för att övervaka maskinvarufel på system som krävs för specifika svarstider. Idag använder programmerare programmerare och system administratörer ofta NMI som en mekanism för att felsöka eller felsöka system som inte svarar.

Seriell konsol kan användas för att skicka en NMI till en Azure virtuell dator med hjälp av tangentbordsikonen i kommandofältet. När NMI är på plats, ska konfigurationen av virtuella datorn styra hur systemet svarar. Windows kan konfigureras att krascher och skapa en minnesdump när du tar emot en NMI.

![Skicka NMI](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-nmi.png) <br>

Information om hur du konfigurerar Windows för att skapa en kraschdumpfil när den får en NMI finns i [hur du skapar en kraschdumpfil med hjälp av en NMI](https://support.microsoft.com/help/927069/how-to-generate-a-complete-crash-dump-file-or-a-kernel-crash-dump-file).

### <a name="use-function-keys-in-serial-console"></a>Använda funktionstangenterna i seriekonsol
Funktionstangenter är aktiverade för användning för seriekonsolen i virtuella Windows-datorer. F8 i listrutan seriekonsolen underlättar för att enkelt ange avancerade inställningar för Start-menyn, men Seriell konsol är kompatibel med alla andra funktionstangenter. Du kan behöva trycka på **Fn** + **F1** (eller F2, F3 osv.) på tangent bordet, beroende på vilken dator du använder för att använda en serie konsol från.

### <a name="use-wsl-in-serial-console"></a>Använd WSL i seriekonsol
Windows-undersystem för Linux (WSL) har aktiverats för Windows Server 2019 eller senare, så det är också möjligt att aktivera WSL för användning i seriekonsolen om du kör Windows Server 2019 eller senare. Detta kan vara fördelaktigt för användare som även har tidigare erfarenhet av Linux-kommandon. Anvisningar för att aktivera WSL för Windows Server finns i den [installationsguide](https://docs.microsoft.com/windows/wsl/install-on-server).

### <a name="restart-your-windows-vmvirtual-machine-scale-set-instance-within-serial-console"></a>Starta om din instans av skalnings uppsättningen för virtuella Windows-datorer/virtuella datorer i serie konsolen
Du kan starta en omstart i serie konsolen genom att gå till ström knappen och klicka på "starta om virtuell dator". Detta initierar en omstart av datorn och ett meddelande visas i Azure Portal om omstarten.

Detta är användbart i situationer där du kanske vill komma åt Start menyn utan att lämna serie konsol upplevelsen.

![Starta om Windows seriell konsol](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-windows.gif)

## <a name="disable-the-serial-console"></a>Inaktivera serie konsolen
Som standard har alla prenumerationer åtkomst till seriell konsol. Du kan inaktivera serie konsolen antingen på prenumerations nivå eller skal uppsättnings nivå för virtuell dator/virtuell dator. Detaljerade anvisningar finns [i Aktivera och inaktivera Azures serie konsol](./serial-console-enable-disable.md).

## <a name="serial-console-security"></a>Seriell konsol-säkerhet

### <a name="access-security"></a>Åtkomstsäkerhet
Åtkomst till seriekonsol är begränsad till användare som har en åtkomstroll av [virtuell Datordeltagare](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) eller högre för att den virtuella datorn. Om din Azure Active Directory-klient kräver multifaktorautentisering (MFA) och åtkomst till seriell konsol måste också MFA att Seriell konsol åtkomst sker via det [Azure-portalen](https://portal.azure.com).

### <a name="channel-security"></a>Kanalsäkerhet
Alla data som skickas fram och tillbaka krypteras under överföringen.

### <a name="audit-logs"></a>Granskningsloggar
All åtkomst till seriekonsol för tillfället är inloggad på [startdiagnostik](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) loggarna för den virtuella datorn. Åtkomst till de här loggarna ägs och kontrolleras av administratören för Azure virtuella datorer.

> [!CAUTION]
> Inga lösenord för åtkomst för konsolen loggas. Men om kommandon körs i konsolen innehåller eller utgående lösenord, hemligheter, användarnamn eller någon annan form av personligt identifierbar information (PII), skrivs de till diagnostikloggar för VM-start. De skrivs tillsammans med alla andra synlig text, som en del av implementeringen av Seriell konsol rullar tillbaka funktion. Dessa loggar är cirkulär och endast personer med läsbehörighet till lagringskontot för diagnostik ha åtkomst till dem. Vi rekommenderar dock följa bästa praxis för att använda Remote Desktop för allt som kan omfatta hemligheter och/eller personligt identifierbar information.

### <a name="concurrent-usage"></a>Samtidig användning
Om en användare är ansluten till seriekonsol och en annan användare begär har åtkomst till den samma virtuella datorn, kommer att kopplas från den första användaren och den andra användaren som är anslutna till samma session.

> [!CAUTION]
> Det innebär att en användare som är frånkopplad inte kommer att loggas ut. Möjligheten att genomdriva en utloggning vid från koppling (med SIGHUP eller liknande mekanism) finns fortfarande i översikten. För Windows finns en automatisk tidsgräns har aktiverats i SAC; Du kan konfigurera terminal timeout-inställningen för Linux.

## <a name="accessibility"></a>Hjälpmedel
Hjälpmedel är viktiga fokus för Azure seriekonsol. Därför har vi sett att seriekonsolen är tillgänglig för det visuella objektet och ha tagit reda på försämrad, samt för personer som inte kanske kan använda en mus.

### <a name="keyboard-navigation"></a>Tangentbordsnavigering
Använd den **fliken** på tangentbordet för att navigera i gränssnittet Seriell konsol från Azure-portalen. Din plats kommer vara markerad på skärmen. Om du vill lämna seriekonsolfönstret fokus trycker du på **Ctrl**+**F6** på tangentbordet.

### <a name="use-the-serial-console-with-a-screen-reader"></a>Använda seriekonsolen med en skärmläsare
Seriekonsolen har inbyggt stöd för Skärmläsaren. Navigera med en skärmläsare aktiveras kan alternativ text för knappen är markerade som ska läsas upp av Skärmläsaren.

## <a name="common-scenarios-for-accessing-the-serial-console"></a>Vanliga scenarier för att komma åt seriekonsolen

Scenario          | Åtgärder i seriekonsolen
:------------------|:-----------------------------------------
Felaktig brandväggsregler | Åtkomst till seriella konsolen och åtgärda Windows brandväggsregler.
Filsystem skadade/kontroll | Komma åt seriekonsolen och Återställ filsystemet.
Problem med RDP-konfigurationen | Komma åt seriekonsolen och ändra inställningarna. Mer information finns i den [RDP-dokumentationen](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-allow-access).
Nätverket låsa system | Komma åt seriekonsolen från Azure portal för att hantera systemet. Vissa nätverkskommandon finns i [Windows-kommandon: CMD och PowerShell](serial-console-cmd-ps-commands.md).
Interagera med startprogrammet | Åtkomst BCD via seriekonsolen. Mer information finns i [aktivera Windows-startmenyn i seriekonsolen](#enable-the-windows-boot-menu-in-the-serial-console).

## <a name="known-issues"></a>Kända problem
Vi är medvetna om några problem med serie konsolen och den virtuella datorns operativ system. Här är en lista över de här problemen och stegen för att minska för virtuella Windows-datorer. Dessa problem och begränsningar gäller för både virtuella datorer och instanser av skalnings uppsättningar för virtuella datorer. Om de inte matchar det fel du ser kan du läsa fel i vanliga fel med seriell konsol tjänsten i [vanliga](./serial-console-errors.md)fel i serie konsolen.

Problem                             |   Åtgärd
:---------------------------------|:--------------------------------------------|
Att trycka på **RETUR** när anslutningen popup-meddelandet inte orsakar en uppmaning som ska visas. | Mer information finns i [Hitting ange ingenting](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). Det här felet kan inträffa om du kör en anpassad virtuell dator, förstärkt installation eller Start-config som medför att Windows inte kan ansluta ordentligt till den seriella porten. Det här felet uppstår också om du kör en virtuell Windows 10-dator, eftersom endast virtuella Windows Server-datorer har kon figurer ATS för att ha EMS aktiverat.
Endast hälsoinformation visas när du ansluter till en virtuell Windows-dator| Det här felet uppstår om den särskilda administrations konsolen inte har Aktiver ATS för Windows-avbildningen. Se [aktivera seriekonsolen i anpassade eller äldre bilder](#enable-the-serial-console-in-custom-or-older-images) för instruktioner om hur du manuellt Aktivera SAC på din virtuella Windows-dator. Mer information finns i [Windows hälsotillstånd signaler](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md).
SAC tar inte upp hela det seriella konsol utrymmet i webbläsaren | Detta är ett känt problem med Windows och termin Ale mula torn. Vi spårar det här problemet med båda teamen men för närvarande finns det ingen minskning.
Det går inte att skriva på SAC fråga om kernel-felsökning är aktiverad. | RDP till den virtuella datorn och köra `bcdedit /debug {current} off` från en upphöjd kommandotolk. Om du kan inte använda RDP du i stället kan koppla OS-disken till en annan virtuell Azure-dator och ändra den när ansluten som en datadisk genom att köra `bcdedit /store <drive letter of data disk>:\boot\bcd /debug <identifier> off`, växlar tillbaka disken.
Klistra in i PowerShell i SAC resulterar i ett tredje tecken om det ursprungliga innehållet hade ett upprepade tecken. | En lösning kan köra `Remove-Module PSReadLine` att ta bort modulen PSReadLine från den aktuella sessionen. Den här åtgärden kommer inte att ta bort eller avinstallera modulen.
Vissa tangentbord indata producerar utdata som onormalt SAC (till exempel **[A**, **[3 ~** ). | [VT100](https://aka.ms/vtsequences) escape-sekvenser stöds inte av SAC-prompten.
Klistra in lång sträng fungerar inte. | Seriekonsolen begränsar längden på strängar som klistras in i terminalen för att 2048 tecken för att förhindra överbelastning serieport bandbredd.

## <a name="frequently-asked-questions"></a>Vanliga frågor

**F. Hur gör jag för att skicka feedback?**

A. Ge feedback genom att skapa ett GitHub-ärende på https://aka.ms/serialconsolefeedback. Du kan också (mindre rekommenderas), kan du skicka feedback via azserialhelp@microsoft.com eller i kategorin virtuell dator för https://feedback.azure.com.

**F. har serie konsolen stöd för kopiera/klistra in?**

A. Ja. Använd **Ctrl**+**SKIFT**+**C** och **Ctrl**+**SKIFT** + **V** kopiera och klistra in i terminalen.

**F. Vem kan aktivera eller inaktivera serie konsolen för min prenumeration?**

A. Om du vill aktivera eller inaktivera seriekonsolen på en prenumeration hela-nivå, måste du ha skrivbehörighet till prenumerationen. Roller som har behörighet att skriva vara administratörer eller ägare. Anpassade roller kan också ha skrivbehörighet.

**F. Vem har åtkomst till serie konsolen för min virtuella dator?**

A. Du måste ha rollen virtuell Datordeltagare eller högre för en virtuell dator att få åtkomst till den Virtuella datorns Seriell konsol.

**F. min serie konsol visar inte något, vad gör jag?**

A. Avbildningen är antagligen felkonfigurerad för seriell konsolåtkomst. Information om hur du konfigurerar din avbildning om du vill aktivera seriekonsolen finns i [aktivera seriekonsolen i anpassade eller äldre bilder](#enable-the-serial-console-in-custom-or-older-images).

**F. är serie konsolen tillgänglig för skalnings uppsättningar för virtuella datorer?**

A. Ja det är det! Se [serie konsolen för Virtual Machine Scale Sets](./serial-console-overview.md#serial-console-for-virtual-machine-scale-sets)

## <a name="next-steps"></a>Nästa steg
* En detaljerad guide till CMD- och PowerShell-kommandon som du kan använda i Windows-SAC finns [Windows-kommandon: CMD och PowerShell](serial-console-cmd-ps-commands.md).
* Seriekonsolen är också tillgängligt för [Linux](serial-console-linux.md) virtuella datorer.
* Läs mer om [startdiagnostik](boot-diagnostics.md).
