---
title: Azure Serial Console för Windows | Microsoft-dokument
description: Dubbelriktad seriell konsol för virtuella Azure-datorer och skaluppsättningar för virtuella datorer.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267006"
---
# <a name="azure-serial-console-for-windows"></a>Azure Serial Console för Windows

Seriekonsolen i Azure-portalen ger åtkomst till en textbaserad konsol för Windows virtuella datorer (VIRTUELLA datorer) och skaluppsättningar för virtuella datorer. Den här seriella anslutningen ansluter till COM1-seriell port för den virtuella datorn eller den virtuella datorns skalningsuppsättningsinstans, vilket ger åtkomst till den oberoende av nätverks- eller operativsystemtillstånd. Seriell konsol kan endast nås med hjälp av Azure-portalen och är endast tillåten för de användare som har en åtkomstroll som Deltagare eller högre till den virtuella datorn eller skalan för virtuella datorer.

Seriekonsolen fungerar på samma sätt för virtuella datorer och skalningsuppsättningsinstanser för virtuella datorer och virtuella datorer. I det här dokumentet innehåller alla omnämnanden till virtuella datorer implicit infallna instanser av skalningsuppsättningar för virtuella datorer om inte annat anges.

Dokumentation för seriell konsol för Linux finns i [Azure Serial Console för Linux](serial-console-linux.md).

> [!NOTE]
> Seriekonsolen är vanligtvis tillgänglig i globala Azure-regioner och i offentlig förhandsversion i Azure Government. Det är ännu inte tillgängligt i Azure China-molnet.


## <a name="prerequisites"></a>Krav

* Den virtuella datorn eller skalningsgruppsinstansen för virtuella datorer måste använda distributionsmodellen för resurshantering. Klassiska distributioner stöds inte.

- Ditt konto som använder seriell konsol måste ha rollen Deltagare i [virtuell dator](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) för den virtuella datorn och [lagringskontot för startdiagnostik](boot-diagnostics.md)

- Den virtuella datorn eller den virtuella datorns skalningsgruppsinstans måste ha en lösenordsbaserad användare. Du kan skapa en med [återställningslösenordsfunktionen](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) för vm-åtkomsttillägget. Välj **Återställ lösenord** i avsnittet Support + **felsökning.**

* Den virtuella datorn för skalningsuppsättningsinstans för virtuella datorer måste ha [startdiagnostik](boot-diagnostics.md) aktiverad.

    ![Inställningar för startdiagnostik](../media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

## <a name="enable-serial-console-functionality-for-windows-server"></a>Aktivera serialkonsolfunktioner för Windows Server

> [!NOTE]
> Om du inte ser något i seriekonsolen kontrollerar du att startdiagnostik är aktiverat på den virtuella datorn eller skalningsuppsättningen för virtuella datorer.

### <a name="enable-the-serial-console-in-custom-or-older-images"></a>Aktivera seriekonsolen i anpassade eller äldre bilder
Nyare Windows Server-avbildningar på Azure har [SAC (Special Administration Console)](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) aktiverat som standard. SAC stöds i serverversioner av Windows men är inte tillgängligt i klientversioner (till exempel Windows 10, Windows 8 eller Windows 7).

För äldre Windows Server-avbildningar (som skapats före februari 2018) kan du automatiskt aktivera seriekonsolen via Azure-portalens kommandofunktion för körning. I Azure-portalen väljer du **Kommandot Kör**och väljer sedan kommandot **EnableEMS** i listan.

![Kör kommandolista](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-runcommand.png)

Om du vill aktivera seriell konsol för Windows virtuella datorer/skalningsuppsättning för virtuella datorer som skapats före februari 2018 gör du så här:

1. Ansluta till windows virtuella datorn med fjärrskrivbord
1. Kör följande kommandon i en administrativ kommandotolk:
    - `bcdedit /ems {current} on`
    - `bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200`
1. Starta om systemet för att SAC-konsolen ska aktiveras.

    ![SAC-konsol](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

Vid behov kan SAC aktiveras offline också:

1. Bifoga windows-disken som du vill konfigurera SAC för som en datadisk till den befintliga virtuella datorn.

1. Kör följande kommandon i en administrativ kommandotolk:
   - `bcdedit /store <mountedvolume>\boot\bcd /ems {default} on`
   - `bcdedit /store <mountedvolume>\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

#### <a name="how-do-i-know-if-sac-is-enabled"></a>Hur vet jag om SAC är aktiverat?

Om [SAC](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) inte är aktiverat visas inte SAC-prompten i seriekonsolen. I vissa fall visas hälsoinformation för virtuella datorer och i andra fall är den tom. Om du använder en Windows Server-avbildning som skapats före februari 2018 aktiveras förmodligen SAC inte.

### <a name="enable-the-windows-boot-menu-in-the-serial-console"></a>Aktivera Startmenyn för Windows i seriekonsolen

Om du behöver aktivera uppmaningar om windows-starthanterare att visas i seriekonsolen kan du lägga till följande ytterligare alternativ i startkonfigurationsdata. Mer information finns [i bcdedit](https://docs.microsoft.com/windows-hardware/drivers/devtest/bcdedit--set).

1. Anslut till windows VM- eller virtual machine-skalningsuppsättningsinstansen med fjärrskrivbord.

1. Kör följande kommandon i en administrativ kommandotolk:
   - `bcdedit /set {bootmgr} displaybootmenu yes`
   - `bcdedit /set {bootmgr} timeout 10`
   - `bcdedit /set {bootmgr} bootems yes`

1. Starta om systemet för att startmenyn ska aktiveras

> [!NOTE]
> Den timeout som du anger för att starthanteraren-menyn ska visas påverkar starttiden för operativsystemet. Om du tror att timeout-värdet på 10 sekunder är för kort eller för långt anger du det till ett annat värde.

## <a name="use-serial-console"></a>Använda seriell konsol

### <a name="use-cmd-or-powershell-in-serial-console"></a>Använda CMD eller PowerShell i seriekonsol

1. Anslut till seriekonsolen. Om du lyckas ansluta är prompten **SAC>: **

    ![Ansluta till SAC](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect-sac.png)

1.  Ange `cmd` om du vill skapa en kanal med en CMD-förekomst.

1.  Ange `ch -si 1` eller `<esc>+<tab>` tryck på kortkommandon för att växla till den kanal som kör CMD-instansen.

1.  Tryck på **Retur**och ange sedan inloggningsuppgifter med administratörsbehörighet.

1.  När du har angett giltiga autentiseringsuppgifter öppnas CMD-instansen.

1.  Om du vill starta `PowerShell` en PowerShell-instans anger du i CMD-instansen och trycker sedan på **Retur**.

    ![Öppna PowerShell-instans](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-powershell.png)

### <a name="use-the-serial-console-for-nmi-calls"></a>Använda seriekonsolen för NMI-samtal
Ett icke-maskerbart avbrott (NMI) är utformat för att skapa en signal som programvara på en virtuell dator inte ignorerar. Historiskt sett har NMIs använts för att övervaka maskinvaruproblem på system som krävde specifika svarstider. Idag använder programmerare och systemadministratörer ofta NMI som en mekanism för att felsöka eller felsöka system som inte svarar.

Seriekonsolen kan användas för att skicka en NMI till en virtuell Azure-dator med hjälp av tangentbordsikonen i kommandofältet. När NMI har levererats styr konfigurationen för den virtuella datorn hur systemet svarar. Windows kan konfigureras för att krascha och skapa en minnesdumpfil när du tar emot en NMI.

![Skicka NMI](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-nmi.png) <br>

Information om hur du konfigurerar Windows för att skapa en kraschdumpfil när den tar emot en NMI finns i [Så här genererar du en kraschdumpfil med hjälp av en NMI](https://support.microsoft.com/help/927069/how-to-generate-a-complete-crash-dump-file-or-a-kernel-crash-dump-file).

### <a name="use-function-keys-in-serial-console"></a>Använda funktionstangenter i seriekonsolen
Funktionstangenterna är aktiverade för användning för seriell konsol i virtuella datorer i Windows. F8 i seriekonsolen rullgardinsmenyn ger bekvämligheten med att enkelt komma in i menyn Avancerade startinställningar, men seriell konsol är kompatibel med alla andra funktionstangenter. Du kan behöva trycka på **Fn** + **F1** (eller F2, F3, etc.) på tangentbordet beroende på vilken dator du använder seriell konsol från.

### <a name="use-wsl-in-serial-console"></a>Använda WSL i seriekonsol
Windows Subsystem för Linux (WSL) har aktiverats för Windows Server 2019 eller senare, så det är också möjligt att aktivera WSL för användning i seriekonsolen om du kör Windows Server 2019 eller senare. Detta kan vara fördelaktigt för användare som också har en förtrogenhet med Linux-kommandon. Instruktioner för hur du aktiverar WSL för Windows Server finns i [installationsguiden](https://docs.microsoft.com/windows/wsl/install-on-server).

### <a name="restart-your-windows-vmvirtual-machine-scale-set-instance-within-serial-console"></a>Starta om skalningsuppsättningen för Windows VM/virtuell dator i Seriekonsolen
Du kan starta om en omstart i seriekonsolen genom att navigera till strömbrytaren och klicka på "Starta om den virtuella datorn". Detta initierar en omstart av den virtuella datorn och du kommer att se ett meddelande i Azure-portalen om omstarten.

Detta är användbart i situationer där du kanske vill komma åt startmenyn utan att lämna seriekonsolen.

![Omstart av Windows-seriekonsol](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-windows.gif)

## <a name="disable-the-serial-console"></a>Inaktivera seriekonsolen
Som standard har alla prenumerationer seriell konsolåtkomst aktiverad. Du kan inaktivera seriekonsolen på antingen prenumerationsnivå eller vm/virtuell datorskala. Detaljerade instruktioner finns i [Aktivera och inaktivera Azure Serial Console](./serial-console-enable-disable.md).

## <a name="serial-console-security"></a>Seriell konsolsäkerhet

### <a name="access-security"></a>Åtkomstsäkerhet
Åtkomst till seriekonsolen är begränsad till användare som har en åtkomstroll [som deltagare i virtuell dator](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) eller högre till den virtuella datorn. Om din Azure Active Directory-klient kräver MFA (Multifaktorautentisering) behöver åtkomsten till seriekonsolen också MFA eftersom seriekonsolens åtkomst är via [Azure-portalen](https://portal.azure.com).

### <a name="channel-security"></a>Kanalsäkerhet
Alla data som skickas fram och tillbaka krypteras på tråden.

### <a name="audit-logs"></a>Granskningsloggar
All åtkomst till seriekonsolen loggas för närvarande i [startdiagnostikloggarna](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) för den virtuella datorn. Åtkomst till dessa loggar ägs och kontrolleras av Azure-administratören för virtuella datorer.

> [!CAUTION]
> Inga åtkomstlösenord för konsolen loggas. Men om kommandon som körs i konsolen innehåller eller utdatalösenord, hemligheter, användarnamn eller någon annan form av personligt identifierbar information (PII), kommer de att skrivas till VM-startdiagnostikloggarna. De kommer att skrivas tillsammans med all annan synlig text, som en del av genomförandet av den seriella konsolens scroll back-funktion. Dessa loggar är cirkulära och endast personer med läsbehörighet till diagnostiklagringskontot har åtkomst till dem. Vi rekommenderar dock att du följer den bästa metoden med att använda fjärrskrivbordet för allt som kan innebära hemligheter och/eller PII.

### <a name="concurrent-usage"></a>Samtidig användning
Om en användare är ansluten till seriekonsolen och en annan användare har begärt åtkomst till samma virtuella dator kopplas den första användaren från och den andra användaren ansluts till samma session.

> [!CAUTION]
> Det innebär att en användare som är frånkopplad inte loggas ut. Möjligheten att genomdriva en utloggning vid frånkoppling (med hjälp av SIGHUP eller liknande mekanism) finns fortfarande i färdplanen. För Windows finns det en automatisk timeout aktiverad i SAC. för Linux kan du konfigurera terminaltidsgränsen.

## <a name="accessibility"></a>Hjälpmedel
Hjälpmedel är ett viktigt fokus för Azure-seriekonsolen. Därför har vi sett till att seriekonsolen är tillgänglig för syn- och hörselskadade samt personer som kanske inte kan använda en mus.

### <a name="keyboard-navigation"></a>Tangentbordsnavigering
Använd **tabbtangenten** på tangentbordet för att navigera i det seriella konsolgränssnittet från Azure-portalen. Din plats markeras på skärmen. Om du vill lämna fokus i seriekonsolfönstret trycker du på **Ctrl**+**F6** på tangentbordet.

### <a name="use-the-serial-console-with-a-screen-reader"></a>Använda seriekonsolen med en skärmläsare
Den seriella konsolen har skärmläsare stöd inbyggd. Om du navigerar med en skärmläsare påslagen kan alternativtexten för den markerade knappen läsas upp högt av skärmläsaren.

## <a name="common-scenarios-for-accessing-the-serial-console"></a>Vanliga scenarier för åtkomst till seriekonsolen

Scenario          | Åtgärder i seriekonsolen
:------------------|:-----------------------------------------
Felaktiga brandväggsregler | Öppna seriella konsol och åtgärda Windows-brandväggsregler.
Filsystemet skadas/kontrolleras | Öppna seriekonsolen och återställ filsystemet.
Problem med rdp-konfiguration | Öppna seriekonsolen och ändra inställningarna. Mer information finns i [RDP-dokumentationen](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-allow-access).
System för låsning av nätverk | Öppna seriekonsolen från Azure-portalen för att hantera systemet. Vissa nätverkskommandon visas i [Windows-kommandon: CMD och PowerShell](serial-console-cmd-ps-commands.md).
Interagera med starthanterare | Få åtkomst till BCD via seriekonsolen. Mer information finns [i Aktivera Startmenyn för Windows i seriekonsolen](#enable-the-windows-boot-menu-in-the-serial-console).

## <a name="known-issues"></a>Kända problem
Vi är medvetna om vissa problem med seriekonsolen och den virtuella datorns operativsystem. Här är en lista över dessa problem och steg för begränsning för virtuella datorer i Windows. Dessa problem och mildrande åtgärder gäller för både virtuella datorer och skalningsuppsättningsinstanser för virtuella datorer. Om dessa inte matchar felet du ser läser du de vanliga seriella konsolservicefelen vid [vanliga seriella konsolfel](./serial-console-errors.md).

Problem                             |   Åtgärd
:---------------------------------|:--------------------------------------------|
Om du trycker på **Retur** efter att anslutningsbanderollen inte visas visas en inloggningsfråga. | Mer information finns i [Hitting enter gör ingenting](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). Det här felet kan uppstå om du kör en anpassad virtuell dator, en härdad installation eller en startkonfiguration som gör att Windows inte kan anslutas till den seriella porten på rätt sätt. Det här felet uppstår också om du kör en virtuell Windows 10-dator, eftersom endast virtuella Windows Server-datorer är konfigurerade för att ha EMS aktiverat.
Endast hälsoinformation visas vid anslutning till en Windows VM| Det här felet uppstår om konsolen Special Administration inte har aktiverats för Din Windows-avbildning. Se [Aktivera seriekonsolen i anpassade eller äldre avbildningar](#enable-the-serial-console-in-custom-or-older-images) för instruktioner om hur du aktiverar SAC manuellt på din virtuella Windows-dator. Mer information finns i [Hälsosignaler för Windows](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md).
SAC tar inte upp hela Serial Console-området i webbläsaren | Detta är ett känt problem med Windows och terminalemulatorn. Vi spårar det här problemet med båda teamen, men för tillfället finns det ingen begränsning.
Det gick inte att skriva vid SAC-prompten om kernel-felsökning är aktiverat. | RDP till VM `bcdedit /debug {current} off` och köra från en upphöjd kommandotolk. Om du inte kan RDP kan du i stället ansluta OS-disken till en annan `bcdedit /store <drive letter of data disk>:\boot\bcd /debug <identifier> off`Virtuell Azure-dator och ändra den när den är ansluten som en datadisk genom att köra och sedan byta tillbaka disken.
Att klistra in i PowerShell i SAC resulterar i ett tredje tecken om det ursprungliga innehållet hade ett upprepande tecken. | För en lösning kör `Remove-Module PSReadLine` du för att ta bort PSReadLine-modulen från den aktuella sessionen. Den här åtgärden kommer inte att ta bort eller avinstallera modulen.
Vissa tangentbordsinmatningar ger konstiga SAC-utgångar (till exempel **[A**, **[3~**). | [VT100](https://aka.ms/vtsequences) escape-sekvenser stöds inte av SAC-prompten.
Att klistra in långa strängar fungerar inte. | Seriekonsolen begränsar längden på strängar som klistras in i terminalen till 2048 tecken för att förhindra överbelastning av den seriella portens bandbredd.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**F. Hur kan jag skicka feedback?**

A. Ge feedback genom att skapa https://aka.ms/serialconsolefeedbackett GitHub-problem på . Alternativt (mindre att föredra) kan azserialhelp@microsoft.com du skicka feedback https://feedback.azure.comvia eller i kategorin virtuell dator i .

**F. Stöder den seriella konsolen kopiering/inklistring?**

A. Ja. Använd **Ctrl**+**Skift**+**C** och **Ctrl**+**Skift**+**V** för att kopiera och klistra in terminalen.

**F. Vem kan aktivera eller inaktivera seriekonsolen för min prenumeration?**

A. Om du vill aktivera eller inaktivera seriekonsolen på en prenumerationsnivå måste du ha skrivbehörighet till prenumerationen. Roller som har skrivbehörighet omfattar administratörs- eller ägarroller. Anpassade roller kan också ha skrivbehörighet.

**F. Vem kan komma åt seriekonsolen för min virtuella dator?**

A. Du måste ha rollen Deltagare i virtuell dator eller högre för att en virtuell dator ska komma åt den virtuella datorns seriella konsol.

**F. Min seriell konsol visar ingenting, vad gör jag?**

A. Bilden är troligen felkonfigurerad för seriell konsolåtkomst. Information om hur du konfigurerar avbildningen så att den aktiverar seriekonsolen finns [i Aktivera seriekonsolen i anpassade eller äldre bilder](#enable-the-serial-console-in-custom-or-older-images).

**F. Är seriekonsolen tillgänglig för skaluppsättningar för virtuella datorer?**

A. Ja det är det! Se [Seriekonsol för skalningsuppsättningar för virtuella datorer](./serial-console-overview.md#serial-console-for-virtual-machine-scale-sets)

## <a name="next-steps"></a>Nästa steg
* En djupgående guide till CMD- och PowerShell-kommandon som du kan använda i Windows SAC finns i [Windows-kommandon: CMD och PowerShell](serial-console-cmd-ps-commands.md).
* Seriekonsolen är också tillgänglig för [virtuella Linux-datorer.](serial-console-linux.md)
* Läs mer om [startdiagnostik](boot-diagnostics.md).
