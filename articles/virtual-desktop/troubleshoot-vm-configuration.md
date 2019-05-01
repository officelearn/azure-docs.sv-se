---
title: Skapa en klient och värden poolen i Windows virtuellt skrivbord – Azure
description: Så här att lösa problem när du konfigurerar en klient och sessioner värden virtuell dator (VM) i en miljö med virtuella Windows-skrivbordet.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshoot
ms.date: 04/08/2019
ms.author: v-chjenk
ms.openlocfilehash: 38d59fb20776470cb683f2a2146838bb217addf7
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64928129"
---
# <a name="tenant-and-host-pool-creation"></a>Klient- och skapa en pool

Använd den här artikeln för att felsöka problem när du konfigurerar virtuella Windows-skrivbordet session värden virtuella datorer (VM).

## <a name="provide-feedback"></a>Ge feedback

Vi inte är för närvarande tar supportärenden när virtuella Windows-skrivbordet är i förhandsversionen. Gå till den [Windows Desktop Tech-Community virtuella](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) att diskutera virtuellt skrivbord i Windows-tjänsten med produktteamet och aktiva community-medlemmar.

## <a name="vms-are-not-joined-to-the-domain"></a>Virtuella datorer är inte anslutna till domänen

Följ dessa instruktioner om du har problem med att ansluta virtuella datorer till domänen.

- Ansluta den virtuella datorn manuellt med hjälp av processen i [ansluta en Windows Server-dator till en hanterad domän](https://docs.microsoft.com/azure/active-directory-domain-services/Active-directory-ds-admin-guide-join-windows-vm-portal) eller med hjälp av den [domän join mallen](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).
- Försök att pinga domännamnet från kommandoraden på den virtuella datorn.
- Granska listan över domän join felmeddelanden i [felsökning domän Join-felmeddelanden](https://social.technet.microsoft.com/wiki/contents/articles/1935.troubleshooting-domain-join-error-messages.aspx).

### <a name="error-incorrect-credentials"></a>Fel: Felaktiga autentiseringsuppgifter

**Orsak:** Det uppstod ett skrivfel när autentiseringsuppgifterna har angetts i mallen gränssnittet åtgärdar Azure Resource Manager.

**Fix:** Följ dessa instruktioner för att korrigera autentiseringsuppgifterna.

1. Lägga till de virtuella datorerna manuellt till en domän.
2. Distribuera om när autentiseringsuppgifterna har bekräftats. Se [skapar du en värd-pool med PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell).
3. Ansluta virtuella datorer till en domän med en mall med [ansluter till en befintlig Windows virtuell dator till AD-domänen](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).

### <a name="error-timeout-waiting-for-user-input"></a>Fel: Tidsgränsen som väntar på indata från användaren

**Orsak:** Det konto som används för att slutföra domänanslutningen kan ha multifaktorautentisering (MFA).

**Fix:** Följ dessa instruktioner för att slutföra domänanslutningen.

1. Ta bort MFA tillfälligt för kontot.
2. Använd ett tjänstkonto.

### <a name="error-the-account-used-during-provisioning-doesnt-have-permissions-to-complete-the-operation"></a>Fel: Det konto som används vid etableringen har inte behörighet att slutföra åtgärden

**Orsak:** Kontot som används har inte behörighet att ansluta till virtuella datorer till domänen på grund av efterlevnad och -förordningar.

**Fix:** Följ dessa instruktioner.

1. Använd ett konto som är medlem i gruppen Administratörer.
2. Bevilja behörighet till kontot som används.

### <a name="error-domain-name-doesnt-resolve"></a>Fel: Domännamnet inte går att lösa

**Orsak 1:** Virtuella datorer finns i en resursgrupp som inte är associerat med det virtuella nätverket (VNET) där domänen finns.

**Fix 1:** Skapa VNET-peering mellan det virtuella nätverket där virtuella datorer har etablerats och det virtuella nätverket där domänkontrollanten (DC) körs. Se [skapa vnet-peering - Resource Manager, olika prenumerationer](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions).

**Orsak 2:** När du använder AadService (AADS), har DNS-poster inte ställts in.

**Fix 2:** Om du vill ange domäntjänster, se [aktivera Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-dns).

## <a name="windows-virtual-desktop-agent-and-windows-virtual-desktop-boot-loader-are-not-installed"></a>Agenten för virtuella skrivbord i Windows och Windows-startprogrammet för virtuella skrivbordet är inte installerade

Det rekommenderade sättet att etablera virtuella datorer använder Azure Resource Manager **skapa och etablera virtuella Windows-skrivbordet vara värd för poolen** mall. Mallen installerar automatiskt agenten för virtuella skrivbord i Windows och Windows Virtual Desktop agenten startprogrammet.

Följ instruktionerna för att bekräfta komponenterna är installerade och att söka efter felmeddelanden.

1. Bekräfta att de båda komponenterna är installerade genom att kontrollera **Kontrollpanelen** > **program** > **program och funktioner**. Om **Windows Virtual Desktop agenten** och **Windows Virtual Desktop agenten Boot Loader** är inte synliga så att de inte är installerade på den virtuella datorn.
2. Öppna **Utforskaren** och gå till **C:\Windows\Temp\scriptlogs.log**. Om filen saknas, anger det att den PowerShell DSC som installerade de båda komponenterna inte är kan köras i den säkerhetskontext som tillhandahålls.
3. Om filen **C:\Windows\Temp\scriptlogs.log** är presentera, öppna den och kontrollera om felmeddelanden.

### <a name="error-windows-virtual-desktop-agent-and-windows-virtual-desktop-agent-boot-loader-are-missing-cwindowstempscriptlogslog-is-also-missing"></a>Fel: Agenten för virtuella skrivbord i Windows och Windows Virtual Desktop agenten Boot Loader saknas. C:\Windows\Temp\scriptlogs.log till också saknas

**Orsak 1:** Autentiseringsuppgifter som angetts vid inmatningen för Azure Resource Manager-mallen är felaktiga eller behörigheter har inte tillräckligt med.

**Fix 1:** Lägga till komponenter som saknas manuellt till de virtuella datorerna med [skapar du en värd-pool med PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell).

**Orsak 2:** PowerShell DSC kunde starta och köra men kunde inte slutföras eftersom den inte kan logga in på virtuella Windows-skrivbordet och hämta nödvändig information.

**Fix 2:** Bekräfta objekten i listan nedan.

- Kontrollera att den inte har MFA.
- Bekräfta att innehavarens namn är korrekt och klienten finns i virtuella Windows-skrivbordet.
- Bekräfta att kontot har minst deltagarbehörighet för Fjärrskrivbordstjänster.

### <a name="error-authentication-failed-error-in-cwindowstempscriptlogslog"></a>Fel: Autentiseringen misslyckades, fel i C:\Windows\Temp\scriptlogs.log

**Orsak:** PowerShell DSC kunde köra men kunde inte ansluta till virtuella Windows-skrivbordet.

**Fix:** Bekräfta objekten i listan nedan.

- Registrera manuellt de virtuella datorerna med virtuella skrivbord i Windows-tjänsten.
- Bekräfta att kontot som används för att ansluta till virtuella Windows-skrivbordet har behörighet för innehavaren att skapa pooler för värden.
- Bekräfta konto inte har MFA.

## <a name="windows-virtual-desktop-agent-is-not-registering-with-the-windows-virtual-desktop-service"></a>Windows-agenten för virtuella skrivbord registreras inte med virtuella skrivbord i Windows-tjänsten

När Windows Virtual Desktop agenten installeras på sessionen vara värd för virtuella datorer (antingen manuellt eller via Azure Resource Manager-mall och PowerShell DSC), den innehåller en registreringstoken. Följande avsnitt handlar om felsökning av problem gäller för Windows-agenten för virtuella skrivbord och token.

### <a name="error-the-status-filed-in-get-rdssessionhost-cmdlet-shows-status-as-unavailable"></a>Fel: Arkiverat i Get-RdsSessionHost cmdlet visas status status är inte tillgänglig

![Get-RdsSessionHost cmdlet visar status som är inte tillgänglig.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**Orsak:** Agenten har inte att uppdatera sig själv till en ny version.

**Fix:** Följ dessa instruktioner för att uppdatera agenten manuellt.

1. Hämta en ny version av agenten på den Virtuella värddatorn i sessionen.
2. Starta Aktivitetshanteraren och stoppa tjänsten RDAgentBootLoader på fliken Service.
3. Kör installationsprogrammet för den nya versionen av Windows-agenten för virtuella skrivbord.
4. När du uppmanas att ange fcm, ta bort posten INVALID_TOKEN och tryck på Nästa (krävs inte en ny token).
5. Slutför installationsguiden.
6. Öppna Aktivitetshanteraren och starta tjänsten RDAgentBootLoader.

## <a name="error--windows-virtual-desktop-agent-registry-entry-isregistered-shows-a-value-of-0"></a>Fel:  Agenten för virtuella skrivbord i Windows-registerposten IsRegistered visar ett värde av 0

**Orsak:** Registreringstoken har upphört att gälla eller har skapats med värdet för förfallotid för 999999.

**Fix:** Följ dessa instruktioner för att åtgärda felet för registret för agenten.

1. Om det finns redan en registreringstoken, tar du bort den med Remove-RDSRegistrationInfo.
2. Generera ny token med Rds-NewRegistrationInfo.
3. Kontrollera att parametern - ExpriationHours har angetts till 72 (högsta värdet är 99999).

### <a name="error-windows-virtual-desktop-agent-isnt-reporting-a-heartbeat-when-running-get-rdssessionhost"></a>Fel: Virtuellt skrivbord i Windows-agenten rapporterar inte ett pulsslag när du kör Get-RdsSessionHost

**Orsak 1:** RDAgentBootLoader tjänsten har stoppats.

**Fix 1:** Starta Aktivitetshanteraren och om fliken tjänst rapporterar statusen stoppad för RDAgentBootLoader-tjänsten, starta tjänsten.

**Orsak 2:** Port 443 kan stängas.

**Fix 2:** Följ dessa instruktioner för att öppna port 443.

1. Kontrollera att port 443 är öppen genom att ladda ned verktyget PSPing från [Sysinternal verktyg](https://docs.microsoft.com/sysinternals/downloads/psping).
2. Installera PSPing på värd för fjärrskrivbordssession virtuell dator där agenten körs.
3. Öppna Kommandotolken som administratör och utfärda kommandot nedan:

    ```cmd
    psping rdbroker.wvdselfhost.microsoft.com:443
    ```

4. Bekräfta informationen PSPing togs emot från RDBroker:

    ```
    PsPing v2.10 - PsPing - ping, latency, bandwidth measurement utility
    Copyright (C) 2012-2016 Mark Russinovich
    Sysinternals - www.sysinternals.com
    TCP connect to 13.77.160.237:443:
    5 iterations (warmup 1) ping test:
    Connecting to 13.77.160.237:443 (warmup): from 172.20.17.140:60649: 2.00ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60650: 3.83ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60652: 2.21ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60653: 2.14ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60654: 2.12ms
    TCP connect statistics for 13.77.160.237:443:
    Sent = 4, Received = 4, Lost = 0 (0% loss),
    Minimum = 2.12ms, Maximum = 3.83ms, Average = 2.58ms
    ```

## <a name="troubleshooting-issues-with-the-windows-virtual-desktop-side-by-side-stack"></a>Felsökning av problem med virtuella Windows-skrivbordet sida-vid-sida-stack

Den virtuella Windows-skrivbordet sida-vid-sida-stacken installeras automatiskt med Windows Server 2019. Använd Microsoft Installer (MSI) för att installera sida-vid-sida-stacken på Microsoft Windows Server 2016 eller Windows Server 2012 R2. För Microsoft Windows 10, Windows virtuellt skrivbord sida-vid-sida-stacken har aktiverats med **enablesxstackrs.ps1**.

Det finns tre sätt sida-vid-sida-stack hämtar installerad eller aktiverad på sessionen värd pool med virtuella datorer:

- Med Azure Resource Manager **skapa och etablera nya virtuella Windows-skrivbordet värd poolen** mall
- Genom att ingår och aktiverat på huvudavbildningen
- Installerad eller aktiverad manuellt på varje virtuell dator (eller med tillägg/PowerShell)

Om du har problem med den virtuella Windows-skrivbordet sida-vid-sida-stacken, skriver den **qwinsta** från Kommandotolken för att bekräfta att den sida-vid-sida-stacken är installerad eller aktiverad.

Utdata från **qwinsta** visar en lista över **rdp-sxs** i utdata om sida-vid-sida-stacken är installerat och aktiverat.

![Sida-vid-sida-stack installerad eller aktiverad med qwinsta som listas som rdp-sxs i utdata.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

Granska registerposterna nedan och bekräfta att det matchar deras värden. Om registernycklar saknas eller är felaktig matchning av värden, följ instruktionerna i [skapar du en värd-pool med PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell) om hur du installerar om den sida-vid-sida-stacken.

```registry
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\WinStations\rds-sxs\"fEnableWinstation":DWORD=1

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\ClusterSettings\"SessionDirectoryListener":rdp-sxs
```

### <a name="error-oreverseconnectstackfailure"></a>Fel: O_REVERSE_CONNECT_STACK_FAILURE

![O_REVERSE_CONNECT_STACK_FAILURE felkod.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**Orsak:** Den sida-vid-sida-stacken har inte installerats på den Virtuella värddatorn i sessionen.

**Fix:** Följ dessa instruktioner för att installera sida-vid-sida-stacken på den Virtuella värddatorn i sessionen.

1. Använda Remote Desktop Protocol (RDP) direkt till värden för fjärrskrivbordssession VM som lokal administratör.
2. Hämta och importera [Windows Virtual Desktop PowerShell-modulen](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) ska användas i PowerShell-sessionen om du inte redan har gjort.
3. Installera sida-vid-sida-stack med [skapar du en värd-pool med PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell).

## <a name="how-to-fix-a-windows-virtual-desktop-side-by-side-stack-that-malfunctions"></a>Hur du löser en stack för virtuella Windows-skrivbordet sida-vid-sida som inte fungerar

Det finns kända förhållanden som kan orsaka sida-vid-sida-stack inte fungerar korrekt:

- Följer inte rätt ordningen på stegen för att aktivera sida-vid-sida-stack
- Automatisk uppdatering för Windows 10 förbättrad mångsidig skiva (EVD)
- Remote värd för fjärrskrivbordssession (RDSH)-rollen som saknas
- Kör enablesxsstackrc.ps1 flera gånger
- Kör enablesxsstackrc.ps1 i ett konto som har inte lokala administratörsrättigheter

Med hjälp av anvisningarna i det här avsnittet kan du avinstallera den virtuella Windows-skrivbordet sida-vid-sida-stacken. När du har avinstallerat den sida-vid-sida-stacken, går du till ”registrera den virtuella datorn med Windows virtuella skrivbord värd pool” [skapar du en värd-pool med PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell) att installera om den sida-vid-sida-stacken.

Den virtuella datorn som används för att köra åtgärder måste finnas på samma undernät och domän som den virtuella datorn med den felaktiga sida-vid-sida-stacken.

Följ dessa instruktioner för att köra reparation från samma undernät och domän:

1. Ansluta med standard Remote Desktop Protocol (RDP) till den virtuella datorn från där korrigering kommer att tillämpas.
2. Hämta PsExec från https://docs.microsoft.com/sysinternals/downloads/psexec.
3. Packa upp den hämta filen.
4. Starta Kommandotolken som lokal administratör.
5. Gå till mappen där PsExec var uppzippade.
6. Använd följande kommando från Kommandotolken:

    ```cmd
            psexec.exe \\<VMname> cmd
    ```

    >[!Note]
    >VMname är datornamnet för den virtuella datorn med den felaktiga sida-vid-sida-stacken.

7. Godkänn licensavtalet PsExec genom att klicka på Godkänn.

    ![Software license agreement skärmbild.](media/SoftwareLicenseTerms.png)

    >[!Note]
    >Den här dialogrutan visas bara första gången PsExec körs.

8. När kommandotolk-session öppnas på den virtuella datorn med felaktig sida-vid-sida-stack kör qwinsta och bekräfta att det finns en post med namnet rdp-sxs. Om inte, en sida-vid-sida-stack inte på den virtuella datorn så att problemet inte är kopplat till den sida-vid-sida-stacken.

    ![Administratörskommandotolk](media/AdministratorCommandPrompt.png)

9. Kör följande kommando, som visar en lista över Microsoft-komponenter som är installerade på den virtuella datorn med den felaktiga sida-vid-sida-stacken.

    ```cmd
        wmic product get name
    ```

10. Kör kommandot nedan med produktnamn från ovanstående steg.

    ```cmd
        wmic product where name="<Remote Desktop Services Infrastructure Agent>" call uninstall
    ```

11. Avinstallera alla produkter som börjar med ”Remote Desktop”.

12. När alla virtuella skrivbordet i Windows-komponenter har avinstallerats, följer du anvisningarna för ditt operativsystem:

13. Om operativsystemet är Windows Server, startar du om den virtuella datorn som hade felaktig sida-vid-sida-stack (antingen med Azure-portalen eller från PsExec-verktyg).

Om operativsystemet Microsoft Windows 10 Fortsätt med instruktionerna nedan:

14. Från den virtuella datorn som kör PsExec, öppna Utforskaren och kopiera disablesxsstackrc.ps1 till den virtuella datorn med fel sida-vid-sida-stack systemenhet.

    ```cmd
        \\<VMname>\c$\
    ```

    >[!NOTE]
    >VMname är datornamnet för den virtuella datorn med den felaktiga sida-vid-sida-stacken.

15. Rekommenderad metod: från PsExec-verktyg, starta PowerShell och navigera till mappen från föregående steg och kör disablesxsstackrc.ps1. Du kan också köra följande cmdlets:

    ```PowerShell
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\ClusterSettings" -Name "SessionDirectoryListener" -Force
    Remove-Item -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" -Recurse -Force
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations" -Name "ReverseConnectionListener" -Force
    ```

16. När cmdletarna som är klar körs, starta om den virtuella datorn med den felaktiga sida-vid-sida-stacken.

## <a name="next-steps"></a>Nästa steg

- En översikt om hur du felsöker virtuella Windows-skrivbordet och Eskalering spårar finns [översikt, feedback och support](troubleshoot-set-up-overview.md).
- Felsökning av problem när du skapar en pool med klient- och värden i en miljö med virtuella Windows-skrivbordet beskrivs [klient och värden lagringspoolen skapa](troubleshoot-set-up-issues.md).
- Felsökning av problem när du konfigurerar en virtuell dator (VM) i virtuella Windows-skrivbordet beskrivs [Session Värdkonfiguration för virtuell dator](troubleshoot-vm-configuration.md).
- Felsökning av problem med virtuella skrivbord i Windows-klientanslutningar beskrivs [Remote Desktop-klientanslutningar](troubleshoot-client-connection.md).
- När du felsöker problem när du använder PowerShell med virtuella Windows-skrivbordet, se [Windows PowerShell för virtuella skrivbord](troubleshoot-powershell.md).
- Läs mer om förhandsversionen av tjänsten i [Windows Desktop förhandsversionsmiljön](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
- Om du vill gå igenom en självstudiekurs om felsökning finns i [självstudien: Felsöka malldistributioner för Resource Manager-](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot).
- Läs om granskning åtgärder i [granskningsåtgärder med Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Mer information om åtgärder för att avgöra felen under distributionen, se [visa distributionsåtgärder](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).