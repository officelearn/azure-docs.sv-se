---
title: Azure Seriekonsol för Windows | Microsoft Docs
description: Dubbelriktad Seriekonsol för virtuella Azure-datorer och VM-Skalningsuppsättningar.
services: virtual-machines-windows
documentationcenter: ''
author: asinn826
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 5/1/2019
ms.author: harijay
ms.openlocfilehash: c6611c75e61f7e381efd2e437b8281cc70601215
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65141064"
---
# <a name="azure-serial-console-for-windows"></a>Azure Seriekonsol för Windows

Seriekonsol i Azure portal ger åtkomst till en textbaserad konsol för Windows-datorer (VM) och VM-skalningsuppsättningen (virtual machine scale Sets)-instanser. Den här seriell anslutning ansluter till den seriella porten COM1 för den virtuella datorn eller scale set instansen för virtuella datorer, att ge åtkomst till den oberoende av tillståndet nätverk eller operativsystem. Seriell konsol kan bara användas med hjälp av Azure-portalen och är tillåts endast för de användare som har en roll för åtkomst av deltagare eller högre till skalningsuppsättningen för virtuell dator eller virtuell dator.

Seriell konsol fungerar på samma sätt för virtuella datorer och VM-skaluppsättningsinstanser. I det här dokumentet innehåller alla omnämnanden till virtuella datorer implicit VM-skalningsuppsättningsinstanser om inte annat anges.

Seriell konsol dokumentation för virtuella Linux-datorer och VM scale Sets finns i [Azure Seriekonsol för Linux](serial-console-linux.md).

> [!NOTE]
> Seriekonsolen är allmänt tillgängligt i globala Azure-regioner. Det finns inte ännu i Azure government eller Azure Kina moln.


## <a name="prerequisites"></a>Nödvändiga komponenter

* Din virtuella dator eller virtuell dator skalningsuppsättningens datorinstans måste använda resource management-distributionsmodellen. Klassiska distributioner stöds inte.

- Kontot som använder Seriell konsol måste ha den [rollen virtuell Datordeltagare](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) för den virtuella datorn och [startdiagnostik](boot-diagnostics.md) storage-konto

- Din virtuella dator eller virtuell dator skalningsuppsättningens datorinstans måste ha en lösenordsbaserad användare. Du kan skapa en med den [Återställ lösenord](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) funktion av access-tillägg för virtuell dator. Välj **Återställ lösenord** från den **Support och felsökning** avsnittet.

* Den virtuella datorn som du försöker komma åt en seriell konsol måste ha [startdiagnostik](boot-diagnostics.md) aktiverat.

    ![Inställningarna för startdiagnostik](../media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

## <a name="get-started-with-the-serial-console"></a>Kom igång med seriekonsolen
Seriekonsol för virtuella datorer och VM scale Sets är bara tillgänglig via Azure portal:

### <a name="serial-console-for-virtual-machines"></a>Seriekonsol för virtuella datorer
Seriekonsol för virtuella datorer är lika enkelt som att klicka på **seriekonsolen** inom den **Support och felsökning** avsnitt i Azure-portalen.
  1. Öppna [Azure-portalen](https://portal.azure.com).

  1. Gå till **alla resurser** och välj en virtuell dator. Översiktssidan för den virtuella datorn öppnas.

  1. Rulla ned till den **Support och felsökning** och väljer **seriekonsolen**. Ett nytt fönster med seriell konsol öppnas och börjar anslutningen.

### <a name="serial-console-for-virtual-machine-scale-sets"></a>Seriekonsol för Virtual Machine Scale Sets
Seriell konsol är tillgänglig på basis av per instans för skalningsuppsättningar för virtuella datorer. Du måste gå till den enskilda instansen för en skalningsuppsättning för virtuell dator innan du ser den **seriekonsolen** knappen. Kontrollera att du uppdaterar din VM-skalningsuppsättningen för att aktivera startdiagnostik och sedan uppgradera alla instanser till den nya modellen för att komma åt seriekonsolen om din skalningsuppsättning för virtuell dator inte har startdiagnostik aktiverat.
  1. Öppna [Azure-portalen](https://portal.azure.com).

  1. Gå till **alla resurser** och väljer en Virtual Machine Scale Sets. Översiktssidan för VM-skalningsuppsättningen ange öppnas.

  1. Gå till **instanser**

  1. Välj en VM scale set-instans

  1. Från den **Support och felsökning** väljer **seriekonsolen**. Ett nytt fönster med seriell konsol öppnas och börjar anslutningen.

## <a name="enable-serial-console-functionality"></a>Aktivera Seriekonsol-funktioner

> [!NOTE]
> Om du inte ser något i seriekonsolen, se till att startdiagnostik har aktiverats på din skalningsuppsättning för virtuell dator eller virtuell dator.

### <a name="enable-the-serial-console-in-custom-or-older-images"></a>Aktivera seriekonsolen i anpassade eller äldre bilder
Nyare Windows Server-avbildningar på Azure har [särskilda administrationskonsolen](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) (SAC) aktiverad som standard. SAC stöds på server-versioner av Windows men är inte tillgängligt i klientversioner (till exempel Windows 10, Windows 8 eller Windows 7).

För äldre Windows Server-avbildningar (som skapats före februari 2018), kan du automatiskt aktivera seriekonsolen via Azure portal körningskommandot funktionen. I Azure-portalen väljer du **Körningskommando**, välj sedan kommandot med namnet **EnableEMS** i listan.

![Kör kommandolista](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-runcommand.png)

Du kan också för att manuellt Aktivera seriekonsol för Windows-datorer/virtuella datorskaluppsättningen som skapats före februari 2018, Följ dessa steg:

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

1. Ansluta till din Windows-VM eller VM-skalningsuppsättningen instans med hjälp av fjärrskrivbord.

1. Kör följande kommandon från en administrativ kommandotolk:
   - `bcdedit /set {bootmgr} displaybootmenu yes`
   - `bcdedit /set {bootmgr} timeout 10`
   - `bcdedit /set {bootmgr} bootems yes`

1. Starta om datorn för Start-menyn är aktiverat

> [!NOTE]
> Timeout-värdet som du anger för startmenyn manager att visa påverkar din OS-starttiden. Om du tror 10 sekunder timeout-värdet är för kort eller för lång kan du ange den till ett annat värde.

## <a name="use-serial-console"></a>Använda Seriekonsol

### <a name="use-cmd-or-powershell-in-serial-console"></a>Använd CMD / PowerShell på Seriekonsol

1. Anslut till seriekonsol. Om du har anslutit, prompten är **SAC >**:

    ![Ansluta till SAC](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect-sac.png)

1.  Ange `cmd` att skapa en kanal som har en CMD-instans.

1.  Ange `ch -si 1` att växla till den kanal som CMD-instansen körs.

1.  Tryck på **RETUR**, och ange sedan autentiseringsuppgifter med administrativ behörighet.

1.  När du har angett giltiga autentiseringsuppgifter, öppnas CMD-instans.

1.  Om du vill starta en PowerShell-instans, ange `PowerShell` i CMD-instans och tryck sedan på **RETUR**.

    ![Öppna PowerShell-instans](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-powershell.png)

### <a name="use-the-serial-console-for-nmi-calls"></a>Använd seriekonsolen för NMI-anrop
Ett icke-maskable avbrott (NMI) är utformad för att skapa en signal som programvara på en virtuell dator inte ignorera. Historiskt sett använts NMIs för att övervaka maskinvarufel på system som krävs för specifika svarstider. Idag, programmerare och systemadministratörer använder ofta NMI som en mekanism för att felsöka eller felsöka system som inte svarar.

Seriell konsol kan användas för att skicka en NMI till en Azure virtuell dator med hjälp av tangentbordsikonen i kommandofältet. När NMI är på plats, ska konfigurationen av virtuella datorn styra hur systemet svarar. Windows kan konfigureras att krascher och skapa en minnesdump när du tar emot en NMI.

![Skicka icke-maskbart avbrott](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-nmi.png) <br>

Information om hur du konfigurerar Windows för att skapa en kraschdumpfil när den får en NMI finns i [hur du skapar en kraschdumpfil med hjälp av en NMI](https://support.microsoft.com/help/927069/how-to-generate-a-complete-crash-dump-file-or-a-kernel-crash-dump-file).

### <a name="use-function-keys-in-serial-console"></a>Använda funktionstangenterna i seriekonsol
Funktionstangenter är aktiverade för användning för seriekonsolen i virtuella Windows-datorer. F8 i listrutan seriekonsolen underlättar för att enkelt ange avancerade inställningar för Start-menyn, men Seriell konsol är kompatibel med alla andra funktionstangenter. Du måste kanske trycka på **Fn** + **F1** (eller F2, F3, etc.) på tangentbordet beroende på datorn du använder Seriell konsol från.

### <a name="use-wsl-in-serial-console"></a>Använd WSL i seriekonsol
Windows-undersystem för Linux (WSL) har aktiverats för Windows Server 2019 eller senare, så det är också möjligt att aktivera WSL för användning i seriekonsolen om du kör Windows Server 2019 eller senare. Detta kan vara fördelaktigt för användare som även har tidigare erfarenhet av Linux-kommandon. Anvisningar för att aktivera WSL för Windows Server finns i den [installationsguide](https://docs.microsoft.com/windows/wsl/install-on-server).

### <a name="restart-your-windows-vmvirtual-machine-scale-set-instance-within-serial-console"></a>Starta om din Windows virtuell dator/virtuell dator scale set-instans inom Seriekonsol
Du kan initiera en omstart inom seriekonsolen genom att gå till power-knappen och klicka på ”Starta om VM”. Detta initierar en omstart av virtuella datorer och du ser ett meddelande i Azure-portalen om omstarten.

Detta är användbart i situationer där du kanske vill komma åt startmenyn utan att lämna Seriell konsol-upplevelse.

![Starta om Windows-Seriekonsol](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-windows.gif)

## <a name="disable-serial-console"></a>Inaktivera seriekonsol
Som standard har alla prenumerationer seriell konsolåtkomst är aktiverad för alla virtuella datorer. Du kan inaktivera seriekonsolen på prenumerationsnivån eller VM-nivå.

### <a name="vmvirtual-machine-scale-set-level-disable"></a>Virtuell dator/virtuell dator scale set-nivå inaktivera
Seriell konsol kan inaktiveras för en specifik virtuell dator eller virtuell dator skalningsuppsättning genom att inaktivera inställningen boot diagnostics. Inaktivera startdiagnostik från Azure portal för att inaktivera seriekonsolen för den virtuella datorn eller virtuella datorns skalningsuppsättning. Om du använder Seriell konsol på en skalningsuppsättning för virtuell dator, se till att du uppgraderar dina VM-skalningsuppsättningsinstanser till den senaste modellen.

> [!NOTE]
> Om du vill aktivera eller inaktivera seriekonsol för en prenumeration, måste du ha skrivbehörighet till prenumerationen. Dessa behörigheter omfattar, men är inte begränsad till administratörer eller ägare. Anpassade roller kan också ha skrivbehörighet.

### <a name="subscription-level-disable"></a>Prenumerationsnivå inaktivera
Seriell konsol kan inaktiveras för en hel prenumeration via den [inaktivera konsolen REST API-anrop](/rest/api/serialconsole/console/disableconsole). Du kan använda den **prova** funktionen som är tillgängliga på den här API-dokumentationssidan inaktiverar och aktiverar Seriell konsol för en prenumeration. Ange ditt prenumerations-ID för **subscriptionId**, ange ”standard” för **standard**, och välj sedan **kör**. Azure CLI-kommandon är ännu inte tillgängliga.

![Testa REST-API](../media/virtual-machines-serial-console/virtual-machine-serial-console-rest-api-try-it.png)

Du kan också använda följande uppsättning bash-kommandon i Cloud Shell för att inaktivera, aktivera och visa inaktiverad status för seriekonsolen för en prenumeration:

* Hämta inaktiverad status för seriekonsolen för en prenumeration:
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
> Det innebär att en användare som är frånkopplad inte kommer att loggas ut. Möjlighet att framtvinga en utloggning vid frånkoppling (med hjälp av SIGHUP eller liknande mekanism) är fortfarande i översikten. För Windows finns en automatisk tidsgräns har aktiverats i SAC; Du kan konfigurera terminal timeout-inställningen för Linux.

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


## <a name="errors"></a>Fel
Eftersom de flesta felen är tillfälliga kan försöker anslutningen ofta åtgärda dem. I följande tabell visas en lista över fel och åtgärder för både virtuella datorer och VM-skaluppsättningsinstanser.

Fel                            |   Åtgärd
:---------------------------------|:--------------------------------------------|
Det gick inte att hämta inställningarna för startdiagnostik för  *&lt;VMNAME&gt;*. Se till att startdiagnostik har aktiverats för den här virtuella datorn om du vill använda seriekonsolen. | Kontrollera att den virtuella datorn har [startdiagnostik](boot-diagnostics.md) aktiverat.
Den virtuella datorn är i tillståndet stoppad frigjord. Starta den virtuella datorn och försök Seriell konsol-anslutning. | Virtuell dator måste vara i tillståndet igång för att komma åt seriekonsolen
Du har inte behörighet att använda den här virtuella datorn från seriell konsol. Kontrollera att du har minst deltagarbehörigheter för virtuell dator.| Seriell konsolåtkomst kräver vissa behörigheter. Mer information finns i [krav](#prerequisites).
Det går inte att fastställa resursgruppen för startdiagnostiklagringskonto  *&lt;STORAGEACCOUNTNAME&gt;*. Kontrollera att startdiagnostik har aktiverats för den här virtuella datorn och du har åtkomst till det här lagringskontot. | Seriell konsolåtkomst kräver vissa behörigheter. Mer information finns i [krav](#prerequisites).
Ett ”förbjuden”-svar påträffades vid åtkomst till den här Virtuella datorns lagringskonto för startdiagnostik. | Kontrollera att startdiagnostik inte har en brandvägg för kontot. Ett lagringskonto för tillgänglig startdiagnostik är nödvändiga för seriekonsolen ska fungera.
Web socket är stängd eller kunde inte öppnas. | Du kan behöva godkänna `*.console.azure.com`. En mer detaljerad men längre metod är att godkänna den [Microsoft Azure Datacenter IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653), som ändras relativt regelbundet.
Endast hälsoinformation visas när du ansluter till en virtuell Windows-dator| Det här felet uppstår om särskilda administrationskonsolen inte har aktiverats för din Windows-avbildning. Se [aktivera seriekonsolen i anpassade eller äldre bilder](#enable-the-serial-console-in-custom-or-older-images) för instruktioner om hur du manuellt Aktivera SAC på din virtuella Windows-dator. Mer information finns i [Windows hälsotillstånd signaler](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md).

## <a name="known-issues"></a>Kända problem
Vi är medvetna om några problem med seriell konsol. Här är en lista över dessa problem och åtgärder för problemlösning. Dessa problem och åtgärder som gäller för både virtuella datorer och VM-skaluppsättningsinstanser.

Problem                             |   Åtgärd
:---------------------------------|:--------------------------------------------|
Att trycka på **RETUR** när anslutningen popup-meddelandet inte orsakar en uppmaning som ska visas. | Mer information finns i [Hitting ange ingenting](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). Det här felet kan inträffa om du kör en anpassad virtuell dator, förstärkt installation eller Start-config som medför att Windows inte kan ansluta ordentligt till den seriella porten. Det här felet inträffar också om du kör Windows 10-klient VM, eftersom endast Windows Server-datorer är konfigurerade för EMS aktiverat.
Det går inte att skriva på SAC fråga om kernel-felsökning är aktiverad. | RDP till den virtuella datorn och köra `bcdedit /debug {current} off` från en upphöjd kommandotolk. Om du kan inte använda RDP du i stället kan koppla OS-disken till en annan virtuell Azure-dator och ändra den när ansluten som en datadisk genom att köra `bcdedit /store <drive letter of data disk>:\boot\bcd /debug <identifier> off`, växlar tillbaka disken.
Klistra in i PowerShell i SAC resulterar i ett tredje tecken om det ursprungliga innehållet hade ett upprepade tecken. | En lösning kan köra `Remove-Module PSReadLine` att ta bort modulen PSReadLine från den aktuella sessionen. Den här åtgärden kommer inte att ta bort eller avinstallera modulen.
Vissa tangentbord indata producerar utdata som onormalt SAC (till exempel **[A**, **[3 ~**). | [VT100](https://aka.ms/vtsequences) escape-sekvenser stöds inte av SAC-prompten.
Klistra in lång sträng fungerar inte. | Seriekonsolen begränsar längden på strängar som klistras in i terminalen för att 2048 tecken för att förhindra överbelastning serieport bandbredd.
Seriell konsol fungerar inte med en brandvägg för storage-konto. | Seriell konsol avsiktligt fungerar inte med storage-konto brandväggar aktiverad på startdiagnostiklagringskonto.


## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**FRÅGOR OCH. Hur kan jag skicka feedback?**

A. Ge feedback genom att skapa ett GitHub-ärende på https://aka.ms/serialconsolefeedback. Du kan också (mindre rekommenderas), kan du skicka feedback via azserialhelp@microsoft.com eller i kategorin virtuell dator för https://feedback.azure.com.

**FRÅGOR OCH. Stöder seriekonsolen kopiera och klistra in?**

A. Ja. Använd **Ctrl**+**SKIFT**+**C** och **Ctrl**+**SKIFT** + **V** kopiera och klistra in i terminalen.

**FRÅGOR OCH. Vem kan aktivera eller inaktivera seriekonsol för min prenumeration?**

A. Om du vill aktivera eller inaktivera seriekonsolen på en prenumeration hela-nivå, måste du ha skrivbehörighet till prenumerationen. Roller som har behörighet att skriva vara administratörer eller ägare. Anpassade roller kan också ha skrivbehörighet.

**FRÅGOR OCH. Vem som kan komma åt seriekonsolen för min virtuella dator?**

A. Du måste ha rollen virtuell Datordeltagare eller högre för en virtuell dator att få åtkomst till den Virtuella datorns Seriell konsol.

**FRÅGOR OCH. Min seriekonsolen inte visar någonting, vad gör jag?**

A. Avbildningen är antagligen felkonfigurerad för seriell konsolåtkomst. Information om hur du konfigurerar din avbildning om du vill aktivera seriekonsolen finns i [aktivera seriekonsolen i anpassade eller äldre bilder](#enable-the-serial-console-in-custom-or-older-images).

**FRÅGOR OCH. Är seriell konsol som är tillgänglig för VM-skalningsuppsättningar?**

A. För närvarande stöds inte åtkomst till seriekonsol för VM-skalningsuppsättningsinstanser.

## <a name="next-steps"></a>Nästa steg
* En detaljerad guide till CMD- och PowerShell-kommandon som du kan använda i Windows-SAC finns [Windows-kommandon: CMD och PowerShell](serial-console-cmd-ps-commands.md).
* Seriekonsolen är också tillgängligt för [Linux](serial-console-linux.md) virtuella datorer.
* Läs mer om [startdiagnostik](boot-diagnostics.md).
