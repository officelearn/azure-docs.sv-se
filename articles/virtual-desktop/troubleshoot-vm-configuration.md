---
title: Felsöka windows virtual desktop-sessionsvärd – Azure
description: Så här löser du problem när du konfigurerar om Windows Virtual Desktop-session värd virtuella datorer.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 12/03/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: c7d9a5d576ceec301eba7436c1e0af34412ae854
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127597"
---
# <a name="session-host-virtual-machine-configuration"></a>Session för konfiguration av virtuell värddator

Använd den här artikeln för att felsöka problem som du har när du konfigurerar Windows Virtual Desktop-sessionen är värd för virtuella datorer .

## <a name="provide-feedback"></a>Ge feedback

Besök [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) för att diskutera Windows Virtual Desktop-tjänsten med produktteamet och aktiva communitymedlemmar.

## <a name="vms-are-not-joined-to-the-domain"></a>Virtuella datorer är inte anslutna till domänen

Följ de här instruktionerna om du har problem med att ansluta virtuella datorer till domänen.

- Gå med i den virtuella datorn manuellt med hjälp av processen i [Anslut en virtuell Windows Server-dator till en hanterad domän](../active-directory-domain-services/join-windows-vm.md) eller använda [domänanslutningsmallen](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).
- Försök att pinga domännamnet från kommandoraden på den virtuella datorn.
- Granska listan över felmeddelanden om domänkoppling i [Felsöka felmeddelanden om domänkoppling](https://social.technet.microsoft.com/wiki/contents/articles/1935.troubleshooting-domain-join-error-messages.aspx).

### <a name="error-incorrect-credentials"></a>Fel: Felaktiga autentiseringsuppgifter

**Orsak:** Det gjordes ett stavfel när autentiseringsuppgifterna angavs i azure Resource Manager-mallgränssnittet.

**Rättad:** Vidta en av följande åtgärder för att lösa.

- Lägg till de virtuella datorerna manuellt i en domän.
- Distribuera om mallen när autentiseringsuppgifterna har bekräftats. Se [Skapa en värdpool med PowerShell](create-host-pools-powershell.md).
- Ansluta virtuella datorer till en domän med en mall med [Ansluter en befintlig Windows VM till AD-domän](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).

### <a name="error-timeout-waiting-for-user-input"></a>Fel: Timeout väntar på användarindata

**Orsak:** Kontot som används för att slutföra domänanslutningen kan ha MFA (Multi Factor Authentication).

**Rättad:** Vidta en av följande åtgärder för att lösa.

- Ta tillfälligt bort MFA för kontot.
- Använd ett tjänstkonto.

### <a name="error-the-account-used-during-provisioning-doesnt-have-permissions-to-complete-the-operation"></a>Fel: Kontot som används vid etablering har inte behörighet att slutföra åtgärden

**Orsak:** Kontot som används har inte behörighet att ansluta virtuella datorer till domänen på grund av efterlevnad och regler.

**Rättad:** Vidta en av följande åtgärder för att lösa.

- Använd ett konto som är medlem i gruppen Administratör.
- Bevilja nödvändiga behörigheter till kontot som används.

### <a name="error-domain-name-doesnt-resolve"></a>Fel: Domännamnet löser inte

**Orsak 1:** Virtuella datorer finns i ett virtuellt nätverk som inte är associerat med det virtuella nätverket (VNET) där domänen finns.

**Åtgärda 1:** Skapa VNET-peering mellan det virtuella nätverket där virtuella datorer har etablerats och det virtuella nätverk där domänkontrollanten (DC) körs. Se [Skapa en virtuell nätverks peering - Resource Manager, olika prenumerationer](../virtual-network/create-peering-different-subscriptions.md).

**Orsak 2:** När du använder Azure Active Directory Domain Services (Azure AD DS) har det virtuella nätverket inte sina DNS-serverinställningar uppdaterade för att peka på de hanterade domänkontrollanterna.

**Åtgärda 2:** Information om hur du uppdaterar DNS-inställningarna för det virtuella nätverket som innehåller Azure AD DS finns i [Uppdatera DNS-inställningar för det virtuella Azure-nätverket](../active-directory-domain-services/tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network).

**Orsak 3:** Nätverksgränssnittets DNS-serverinställningar pekar inte på rätt DNS-server i det virtuella nätverket.

**Åtgärda 3:** Vidta någon av följande åtgärder för att lösa, i följande steg i [Ändra DNS-servrar].
- Ändra nätverksgränssnittets DNS-serverinställningar till **Anpassad** med stegen från [Ändra DNS-servrar](../virtual-network/virtual-network-network-interface.md#change-dns-servers) och ange dns-servrarnas privata IP-adresser i det virtuella nätverket.
- Ändra nätverksgränssnittets DNS-serverinställningar till **Inherit från virtuellt nätverk** med stegen från Ändra [DNS-servrar](../virtual-network/virtual-network-network-interface.md#change-dns-servers)och ändra sedan det virtuella nätverkets DNS-serverinställningar med stegen från [Ändra DNS-servrar](../virtual-network/manage-virtual-network.md#change-dns-servers).

## <a name="windows-virtual-desktop-agent-and-windows-virtual-desktop-boot-loader-are-not-installed"></a>Windows Virtual Desktop Agent och Windows Virtual Desktop Boot Loader är inte installerade

Det rekommenderade sättet att etablera virtuella datorer är att använda Azure Resource Manager **Skapa och etablera Windows Virtual Desktop värdpool** mall. Mallen installerar automatiskt Starthanteraren för Windows Virtual Desktop Agent och Windows Virtual Desktop Agent.

Följ dessa instruktioner för att bekräfta att komponenterna är installerade och för att kontrollera om det finns felmeddelanden.

1. Kontrollera att de två komponenterna installeras genom att checka in**Programs** > **Program och funktioner**på **Kontrollpanelen** > . Om **Windows Virtual Desktop Agent** och Windows Virtual Desktop Agent Boot **Loader** inte är synliga installeras de inte på den virtuella datorn.
2. Öppna **Utforskaren** och navigera till **C:\Windows\Temp\ScriptLog.log**. Om filen saknas anger den att PowerShell DSC som installerade de två komponenterna inte kunde köras i säkerhetskontexten.
3. Om filen **C:\Windows\Temp\ScriptLog.log** finns öppnar du den och söker efter felmeddelanden.

### <a name="error-windows-virtual-desktop-agent-and-windows-virtual-desktop-agent-boot-loader-are-missing-cwindowstempscriptloglog-is-also-missing"></a>Fel: Windows Virtual Desktop Agent och Windows Virtual Desktop Agent Boot Loader saknas. C:\Windows\Temp\ScriptLog.log saknas också

**Orsak 1:** Autentiseringsuppgifter som angavs under indata för Azure Resource Manager-mallen var felaktiga eller behörigheter var otillräckliga.

**Åtgärda 1:** Lägg manuellt till de saknade komponenterna i de virtuella datorerna med [Skapa en värdpool med PowerShell](create-host-pools-powershell.md).

**Orsak 2:** PowerShell DSC kunde starta och köra men kunde inte slutföra eftersom det inte kan logga in på Windows Virtual Desktop och hämta nödvändig information.

**Åtgärda 2:** Bekräfta objekten i följande lista.

- Kontrollera att kontot inte har MFA.
- Bekräfta att klientnamnet är korrekt och att klienten finns i Windows Virtual Desktop.
- Bekräfta att kontot har minst rds-deltagarebehörigheter.

### <a name="error-authentication-failed-error-in-cwindowstempscriptloglog"></a>Fel: Autentiseringen misslyckades, fel i C:\Windows\Temp\ScriptLog.log

**Orsak:** PowerShell DSC kunde köra men kunde inte ansluta till Windows Virtual Desktop.

**Rättad:** Bekräfta objekten i följande lista.

- Registrera de virtuella datorerna manuellt med tjänsten Windows Virtual Desktop.
- Bekräfta att konto som används för anslutning till Windows Virtual Desktop har behörigheter för klienten att skapa värdpooler.
- Bekräfta att kontot inte har MFA.

## <a name="windows-virtual-desktop-agent-is-not-registering-with-the-windows-virtual-desktop-service"></a>Windows Virtual Desktop Agent registrerar sig inte med tjänsten Windows Virtual Desktop

När Windows Virtual Desktop Agent först installeras på virtuella datorer för sessionsvärdar (antingen manuellt eller via Azure Resource Manager-mallen och PowerShell DSC), innehåller den en registreringstoken. I följande avsnitt beskrivs felsökningsproblem som gäller för Windows Virtual Desktop Agent och token.

### <a name="error-the-status-filed-in-get-rdssessionhost-cmdlet-shows-status-as-unavailable"></a>Fel: Statusen som arkiverats i Get-RdsSessionHost cmdlet visar status som otillgänglig

![Cmdlet Get-RdsSessionHost visar status som ej tillgänglig.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**Orsak:** Agenten kan inte uppdatera sig själv till en ny version.

**Rättad:** Följ dessa instruktioner för att uppdatera agenten manuellt.

1. Hämta en ny version av agenten på den virtuella datorn för sessionsvärd.
2. Starta Aktivitetshanteraren och stoppa tjänsten RDAgentBootLoader på servicefliken.
3. Kör installationsprogrammet för den nya versionen av Windows Virtual Desktop Agent.
4. När du uppmanas att ange registreringstoken tar du bort INVALID_TOKEN och trycker på nästa (en ny token krävs inte).
5. Slutför installationsguiden.
6. Öppna Aktivitetshanteraren och starta tjänsten RDAgentBootLoader.

## <a name="error--windows-virtual-desktop-agent-registry-entry-isregistered-shows-a-value-of-0"></a>Fel: Registerposten för Windows Virtual Desktop Agent IsRegistered visar värdet 0

**Orsak:** Registreringstoken har upphört att gälla eller har genererats med utgångsvärdet 999999.

**Rättad:** Följ dessa instruktioner för att åtgärda agentregistrets fel.

1. Om det redan finns en registreringstoken tar du bort den med Remove-RDSRegistrationInfo.
2. Generera ny token med Rds-NewRegistrationInfo.
3. Bekräfta att parametern -ExpriationHours är inställd på 72 (maxvärdet är 99999).

### <a name="error-windows-virtual-desktop-agent-isnt-reporting-a-heartbeat-when-running-get-rdssessionhost"></a>Fel: Windows Virtual Desktop-agent rapporterar inte ett pulsslag när get-rdsSessionhost körs

**Orsak 1:** RDAgentBootLoader-tjänsten har stoppats.

**Åtgärda 1:** Starta Aktivitetshanteraren och starta tjänsten om fliken Tjänst rapporterar en stoppad status för TJÄNSTEN RDAgentBootLoader.

**Orsak 2:** Port 443 kan vara stängd.

**Åtgärda 2:** Följ dessa instruktioner för att öppna port 443.

1. Bekräfta att port 443 är öppen genom att hämta PSPing-verktyget från [Sysinternal-verktyg](/sysinternals/downloads/psping/).
2. Installera PSPing på den virtuella datorn för sessionen där agenten körs.
3. Öppna kommandotolken som administratör och utfärda kommandot nedan:

    ```cmd
    psping rdbroker.wvdselfhost.microsoft.com:443
    ```

4. Bekräfta att PSPing fått information tillbaka från RDBroker:

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

## <a name="troubleshooting-issues-with-the-windows-virtual-desktop-side-by-side-stack"></a>Felsöka problem med Windows Virtual Desktop sida vid sida stack

Windows Virtual Desktop sida vid sida-stack installeras automatiskt med Windows Server 2019. Använd Microsoft Installer (MSI) för att installera stacken sida vid sida på Microsoft Windows Server 2016 eller Windows Server 2012 R2. För Microsoft Windows 10 är Windows Virtual Desktop sida vid sida-stacken aktiverad med **enablesxstackrs.ps1**.

Det finns tre huvudsakliga sätt som stacken installeras eller aktiveras sida vid sida på virtuella datorer i sessionsvärdpoolen:

- Med Azure Resource Manager **Skapa och etablera nya Windows Virtual Desktop värdpool** mall
- Genom att inkluderas och aktiveras på huvudbilden
- Installeras eller aktiveras manuellt på varje virtuell dator (eller med tillägg/PowerShell)

Om du har problem med Windows Virtual Desktop sida vid sida-stack skriver du **qwinsta-kommandot** från kommandotolken för att bekräfta att stacken är installerad eller aktiverad sida vid sida.

**Qwinstas** utdata **listar rdp-sxs** i utdata om stacken sida vid sida är installerad och aktiverad.

![Stacken sida vid sida installerad eller aktiverad med qwinsta listad som rdp-sxs i utdata.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

Undersök registerposterna nedan och bekräfta att deras värden matchar. Om registernycklar saknas eller om värdena inte stämmer överens följer du instruktionerna i [Skapa en värdpool med PowerShell](create-host-pools-powershell.md) om hur du installerar om stacken sida vid sida.

```registry
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\WinStations\rds-sxs\"fEnableWinstation":DWORD=1

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\ClusterSettings\"SessionDirectoryListener":rdp-sxs
```

### <a name="error-o_reverse_connect_stack_failure"></a>Fel: O_REVERSE_CONNECT_STACK_FAILURE

![O_REVERSE_CONNECT_STACK_FAILURE felkod.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**Orsak:** Stacken sida vid sida är inte installerad på den virtuella datorn för sessionsvärd.

**Rättad:** Följ dessa instruktioner för att installera stacken sida vid sida på den virtuella datorn för sessionsvärd.

1. Använd RDP (Remote Desktop Protocol) för att komma direkt in i den virtuella datorn för sessionsvärd som lokal administratör.
2. Hämta och importera [Windows Virtual Desktop PowerShell-modulen](/powershell/windows-virtual-desktop/overview/) som ska användas i PowerShell-sessionen om du inte redan har gjort det, kör sedan den här cmdleten för att logga in på ditt konto:

    ```powershell
    Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
    ```

3. Installera stacken sida vid sida med [Skapa en värdpool med PowerShell](create-host-pools-powershell.md).

## <a name="how-to-fix-a-windows-virtual-desktop-side-by-side-stack-that-malfunctions"></a>Så här åtgärdar du en Windows Virtual Desktop sida vid sida-stack som inte fungerar

Det finns kända omständigheter som kan orsaka att stacken sida vid sida inte fungerar:

- Inte följer rätt ordning på stegen för att aktivera den sida vid sida stacken
- Automatisk uppdatering till Windows 10 Förbättrad Mångsidig Skiva (EVD)
- Saknas rollen Värd för fjärrskrivbordssession (RDSH)
- Löpning enablesxsstackrc.ps1 flera gånger
- Köra enablesxsstackrc.ps1 i ett konto som inte har lokala administratörsbehörighet

Instruktionerna i det här avsnittet kan hjälpa dig att avinstallera Windows Virtual Desktop sida vid sida stacken. När du har avinstallerat stacken sida vid sida går du till "Registrera den virtuella datorn med värdpoolen för Windows Virtual Desktop" i [Skapa en värdpool med PowerShell](create-host-pools-powershell.md) för att installera om stacken sida vid sida.

Den virtuella datorn som används för att köra reparation måste finnas i samma undernät och domän som den virtuella datorn med den felaktiga stacken sida vid sida.

Följ dessa instruktioner för att köra reparation från samma undernät och domän:

1. Anslut med rdp (Standard Remote Desktop Protocol) till den virtuella datorn där korrigeringen ska tillämpas.
2. Ladda ner PsExec från https://docs.microsoft.com/sysinternals/downloads/psexec.
3. Packa upp den nedladdade filen.
4. Starta kommandotolken som lokal administratör.
5. Navigera till mappen där PsExec packades upp.
6. Från kommandotolken använder du följande kommando:

    ```cmd
            psexec.exe \\<VMname> cmd
    ```

    >[!Note]
    >VMname är datornamnet på den virtuella datorn med den felaktiga stacken sida vid sida.

7. Acceptera Licensavtalet för PsExec genom att klicka på Godkänn.

    ![Skärmdump av programvarulicensavtal.](media/SoftwareLicenseTerms.png)

    >[!Note]
    >Den här dialogrutan visas bara första gången PsExec körs.

8. När kommandotolkssessionen har öppnats på den virtuella datorn med den felaktiga stacken sida vid sida kör du qwinsta och bekräftar att en post med namnet rdp-sxs är tillgänglig. Om inte, finns inte en stack sida vid sida på den virtuella datorn så problemet är inte bundet till stacken sida vid sida.

    ![Kommandotolken administratör](media/AdministratorCommandPrompt.png)

9. Kör följande kommando, som listar Microsoft-komponenter som är installerade på den virtuella datorn med den felaktiga stacken sida vid sida.

    ```cmd
        wmic product get name
    ```

10. Kör kommandot nedan med produktnamn från steget ovan.

    ```cmd
        wmic product where name="<Remote Desktop Services Infrastructure Agent>" call uninstall
    ```

11. Avinstallera alla produkter som börjar med "Fjärrskrivbord".

12. När alla Windows Virtual Desktop-komponenter har avinstallerats följer du instruktionerna för operativsystemet:

13. Om operativsystemet är Windows Server startar du om den virtuella datorn som hade den felaktiga stacken sida vid sida (antingen med Azure-portalen eller från PsExec-verktyget).

Om ditt operativsystem är Microsoft Windows 10 fortsätter du med instruktionerna nedan:

14. Från den virtuella datorn som kör PsExec öppnar du Utforskaren och kopierar disablesxsstackrc.ps1 till den virtuella datorns systemenhet med den krånglade stacken sida vid sida.

    ```cmd
        \\<VMname>\c$\
    ```

    >[!NOTE]
    >VMname är datornamnet på den virtuella datorn med den felaktiga stacken sida vid sida.

15. Den rekommenderade processen: starta PowerShell från PsExec-verktyget och navigera till mappen från föregående steg och kör disablesxsstackrc.ps1. Du kan också köra följande cmdlets:

    ```PowerShell
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\ClusterSettings" -Name "SessionDirectoryListener" -Force
    Remove-Item -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" -Recurse -Force
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations" -Name "ReverseConnectionListener" -Force
    ```

16. När cmdlets är klar, starta om den virtuella datorn med den felaktiga sida vid sida stacken.

## <a name="remote-desktop-licensing-mode-isnt-configured"></a>Licensieringsläget för fjärrskrivbord är inte konfigurerat

Om du loggar in på Windows 10 Enterprise multisession med ett administrativt konto kan du få ett meddelande om att "Licensieringsläget för fjärrskrivbord är inte konfigurerat, Fjärrskrivbordstjänster slutar fungera i X dagar. På servern För anslutningsutjämning använder du Serverhanteraren för att ange licensieringsläget för fjärrskrivbord."

Om tidsgränsen går ut visas ett felmeddelande med följande: "Fjärrsessionen kopplades från eftersom det inte finns några klientåtkomstlicenser för fjärrskrivbord för den här datorn.",

Om något av dessa meddelanden visas innebär det att avbildningen inte har de senaste Windows-uppdateringarna installerade eller att du ställer in licensieringsläget för fjärrskrivbord via grupprincipen. Följ stegen i nästa avsnitt för att kontrollera grupprincipinställningen, identifiera versionen av Windows 10 Enterprise multisession och installera motsvarande uppdatering.  

>[!NOTE]
>Windows Virtual Desktop kräver bara en CAL-licens (RDS Client Access License) när värdpoolen innehåller Windows Server-sessionsvärdar. Mer information om hur du konfigurerar en RDS CAL finns i [Licensiera din RDS-distribution med klientåtkomstlicenser](/windows-server/remote/remote-desktop-services/rds-client-access-license/).

### <a name="disable-the-remote-desktop-licensing-mode-group-policy-setting"></a>Inaktivera gruppprincipinställningen för licensieringsläge för fjärrskrivbord

Kontrollera gruppprincipinställningen genom att öppna redigeraren för grupprinciper i den virtuella datorn och navigera till **administrativa mallar** > **Windows Components** > **Remote Desktop Services** > **Remote Desktop Session Host** > **Licensing** > **Ange licensieringsläge för fjärrskrivbord**. Om grupprincipinställningen är **Aktiverad**ändrar du den till **Inaktiverad**. Om den redan är inaktiverad, lämna den som den är.

>[!NOTE]
>Om du anger grupprincip via domänen inaktiverar du den här inställningen för principer som är inriktade på dessa virtuella datorer med flera flera sessioner i Windows 10 Enterprise.

### <a name="identify-which-version-of-windows-10-enterprise-multi-session-youre-using"></a>Identifiera vilken version av Windows 10 Enterprise multi-session du använder

Så här kontrollerar du vilken version av Windows 10 Enterprise multisession du har:

1. Logga in med ditt administratörskonto.
2. Skriv "Om" i sökfältet bredvid Start-menyn.
3. Välj **Om din dator**.
4. Kontrollera numret bredvid "Version". Numret ska vara antingen "1809" eller "1903", som visas i följande bild.

    ![En skärmbild av fönstret Windows-specifikationer. Versionsnumret är markerat i blått.](media/windows-specifications.png)

Nu när du känner till versionsnumret går du vidare till det relevanta avsnittet.

### <a name="version-1809"></a>Version 1809

Om versionsnumret säger "1809" installerar du [uppdateringen KB4516077](https://support.microsoft.com/help/4516077).

### <a name="version-1903"></a>Version 1903

Distribuera om värdoperativsystemet med den senaste versionen av Windows 10, version 1903-avbildningen från Azure Gallery.

## <a name="next-steps"></a>Nästa steg

- En översikt över felsökning av Windows Virtual Desktop och eskaleringsspåren finns i [Felsökningsöversikt, feedback och support](troubleshoot-set-up-overview.md).
- Information om hur du felsöker problem när du skapar en klient- och värdpool i en Windows Virtual Desktop-miljö finns i [Skapandet av klient- och värdpooler](troubleshoot-set-up-issues.md).
- Mer om du vill felsöka problem när du konfigurerar en virtuell dator (VM) i Windows Virtual Desktop finns i [Konfigurationen för virtuell dator för session värd](troubleshoot-vm-configuration.md).
- Mer om du vill felsöka problem med Windows Virtual Desktop-klientanslutningar finns i [Windows Virtual Desktop-tjänstanslutningar](troubleshoot-service-connection.md).
- Om du vill felsöka problem med fjärrskrivbordsklienter finns i [Felsöka klienten för fjärrskrivbord](troubleshoot-client.md)
- Mer om du vill felsöka problem när du använder PowerShell med Virtuellt Windows-skrivbord finns i [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md).
- Mer information om tjänsten finns i [Windows Virtual Desktop-miljö](environment-setup.md).
- Information om hur du går igenom en felsökningsguide finns i [Självstudiekurs: Felsöka Resource Manager-malldistributioner](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Mer information om granskningsåtgärder finns i [Granskningsåtgärder med Resurshanteraren](../azure-resource-manager/management/view-activity-logs.md).
- Mer information om åtgärder för att fastställa fel under distributionen finns i [Visa distributionsåtgärder](../azure-resource-manager/templates/deployment-history.md).
