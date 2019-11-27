---
title: Felsöka fjärr skrivbord Windows Virtual Desktop – Azure
description: Så här löser du problem när du konfigurerar klient anslutningar i en Windows-klient för virtuella skriv bord.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/08/2019
ms.author: helohr
ms.openlocfilehash: 9fcc65768db3029461a5823034336bc883379292
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227677"
---
# <a name="remote-desktop-client-connections"></a>Anslutningar för fjärrskrivbordsklienten

Använd den här artikeln för att lösa problem med klient anslutningar för virtuella Windows-datorer.

## <a name="provide-feedback"></a>Ge feedback

Besök [Windows-Tech-communityn för Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) för att diskutera Windows Virtual Desktop-tjänsten med produkt teamet och aktiva community-medlemmar.

## <a name="you-cant-open-a-web-client"></a>Du kan inte öppna en webb klient

Bekräfta att det finns en Internet anslutning genom att öppna en annan webbplats; till exempel [www.Bing.com](https://www.bing.com).

Använd **nslookup** för att bekräfta att DNS kan matcha FQDN:

```cmd
nslookup rdweb.wvd.microsoft.com
```

Försök att ansluta till en annan klient, t. ex. fjärr skrivbords klienten för Windows 7 eller Windows 10, och kontrol lera om du kan öppna webb klienten.

### <a name="error-opening-another-site-fails"></a>Fel: det går inte att öppna en annan webbplats

**Orsak:** Nätverks problem och/eller avbrott.

**KORRIGERA:** Kontakta nätverks supporten.

### <a name="error-nslookup-cannot-resolve-the-name"></a>Fel: det går inte att matcha namnet med nslookup

**Orsak:** Nätverks problem och/eller avbrott.

**KORRIGERA:** Kontakta nätverks support

### <a name="error-you-cant-connect-but-other-clients-can-connect"></a>Fel: du kan inte ansluta men andra klienter kan ansluta

**Orsak:** Webbläsaren fungerar inte som förväntat och har slutat fungera.

**KORRIGERA:** Följ dessa anvisningar för att felsöka webbläsaren.

1. Starta om webbläsaren.
2. Rensa webbläsarens cookies. Se [hur du tar bort cookie-filer i Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
3. Rensa webbläsarens cache. Se [Rensa webbläsarens cacheminne för webbläsaren](https://binged.it/2RKyfdU).
4. Öppna webbläsare i privat läge.

## <a name="web-client-stops-responding-or-disconnects"></a>Webb klienten slutar svara eller kopplas från

Försök att ansluta med en annan webbläsare eller klient.

### <a name="error-other-browsers-and-clients-also-malfunction-or-fail-to-open"></a>Fel: andra webbläsare och klienter fungerar inte heller eller kan inte öppnas

**Orsak:** Problem med nätverk och/eller operativ system eller drift avbrott

**KORRIGERA:** Kontakta support team.

## <a name="web-client-keeps-prompting-for-credentials"></a>Webb klienten kommer att uppmanas att ange autentiseringsuppgifter

Följ dessa instruktioner om webb klienten ska fråga om autentiseringsuppgifter.

1. Webb adressen till webb klienten är korrekt.
2. Bekräfta att autentiseringsuppgifterna för den virtuella Windows-skrivbordet är knutna till URL: en.
3. Rensa webbläsarens cookies. Se [hur du tar bort cookie-filer i Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
4. Rensa webbläsarens cache. Se [Rensa webbläsarens cacheminne för webbläsaren](https://binged.it/2RKyfdU).
5. Öppna webbläsare i privat läge.

## <a name="remote-desktop-client-for-windows-7-or-windows-10-stops-responding-or-cannot-be-opened"></a>Fjärr skrivbords klienten för Windows 7 eller Windows 10 slutar svara eller kan inte öppnas

Använd följande PowerShell-cmdlets för att rensa out-of-band-klientens register (out-of-band).

```PowerShell
Remove-ItemProperty 'HKCU:\Software\Microsoft\Terminal Server Client\Default' - Name FeedURLs

#Remove RdClientRadc registry key
Remove-Item 'HKCU:\Software\Microsoft\RdClientRadc' -Recurse

#Remove all files under %appdata%\RdClientRadc
Remove-Item C:\Users\pavithir\AppData\Roaming\RdClientRadc\* -Recurse
```

Navigera till **%AppData%\RdClientRadc** och ta bort allt innehåll.

Avinstallera och installera om fjärr skrivbords klienten för Windows 7 och Windows 10.

## <a name="troubleshooting-end-user-connectivity"></a>Felsöka slut användar anslutning

Ibland kan användare komma åt sina flöden och lokala resurser, men fortfarande ha konfigurations-, tillgänglighets-eller prestanda problem som hindrar dem från att komma åt fjär resurser. I dessa fall får användaren meddelanden som liknar dessa:

![Anslutning till fjärrskrivbord fel meddelande.](media/eb76b666808bddb611448dfb621152ce.png)

![Det går inte att ansluta till gateway-fel meddelandet.](media/a8fbb9910d4672147335550affe58481.png)

Följ dessa allmänna fel söknings anvisningar för fel koder för klient anslutningar.

1. Bekräfta användar namn och tidpunkt när problem uppstod.
2. Öppna **PowerShell** och upprätta en anslutning till den virtuella Windows-klient där problemet rapporterades.
3. Bekräfta anslutningen till rätt klient med **Get-RdsTenant.**
4. Använd cmdletarna **Get-RdsHostPool** och **Get-RdsSessionHost** för att kontrol lera att fel sökning utförs på rätt adresspool.
5. Kör kommandot nedan för att hämta en lista över alla misslyckade aktiviteter av typen anslutning under den angivna tids perioden:

    ```PowerShell
     Get-RdsDiagnosticActivities -TenantName <TenantName> -username <UPN> -StartTime
     "11/21/2018 1:07:03 PM" -EndTime "11/21/2018 1:27:03 PM" -Outcome Failure -ActivityType Connection
    ```

6. Använd **ActivityId** från föregående cmdlet-utdata och kör kommandot nedan:

    ```PowerShell
    (Get-RdsDiagnosticActivities -TenantName <TenantName> -ActivityId <ActivityId> -Detailed).Errors
    ```

7. Kommandot ger utdata som liknar de utdata som visas nedan. Använd **ErrorCodeSymbolic** och **errormessage** för att felsöka rotor saken.

    ```PowerShell
    ErrorSource       : <Source>
    ErrorOperation    : <Operation>
    ErrorCode         : <Error code>
    ErrorCodeSymbolic : <Error code string>
    ErrorMessage      : <Error code message>
    ErrorInternal     : <Internal for the OS>
    ReportedBy        : <Reported by component>
    Time              : <Timestampt>
    ```

### <a name="error-o_add_user_to_group_failed--failed-to-add-user--username-to-group--remote-desktop-users-reason-win32error_no_such_member"></a>Fel: O_ADD_USER_TO_GROUP_FAILED/det gick inte att lägga till användare = ≤ användar namn ≥ till grupp = fjärr skrivbords användare. Orsak: Win32. ERROR_NO_SUCH_MEMBER

**Orsak:** Den virtuella datorn har inte anslutits till domänen där användar objekt är.

**KORRIGERA:** Lägg till den virtuella datorn i rätt domän. Se [Anslut en virtuell Windows Server-dator till en hanterad domän](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-join-windows-vm-portal).

### <a name="error-nslookup-cannot-resolve-the-name"></a>Fel: det går inte att matcha namnet med nslookup

**Orsak:** Nätverks problem eller avbrott.

**KORRIGERA:** Kontakta nätverks support

### <a name="error-connectionfailedclientprotocolerror"></a>Fel: ConnectionFailedClientProtocolError

**Orsak:** Virtuella datorer som användaren försöker ansluta till är inte domänanslutna.

**KORRIGERA:** Anslut alla virtuella datorer som ingår i en adresspool till domänkontrollanten.

### <a name="error-connectionfailedusersidinformationmismatch"></a>Fel: ConnectionFailedUserSIDInformationMismatch
**Orsak:** SID från användarens Azure Active Directory (AD)-token matchar inte SID som returnerades av domänkontrollanten vid försök att aktivera användaren för fjärran sluten inloggning. Det här felet uppstår vanligt vis när du försöker logga in på en Azure Active Directory Domain Services (Azure AD DS)-miljö med en användare som ursprungligen har varit källad från en Windows Server AD.

**KORRIGERA:** Det här scenariot stöds inte för tillfället. Endast användare som har ursprung i Azure Active Directory kan logga in på virtuella Windows-datorer med virtuella skriv bord som är anslutna till Azure AD DS.

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>Användaren ansluter men inget visas (ingen feed)

En användare kan starta fjärr skrivbords klienter och kan autentisera, men användaren ser inga ikoner i webb identifierings flödet.

Bekräfta att användaren som rapporterar problemen har tilldelats program grupper genom att använda den här kommando raden:

```PowerShell
Get-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname>
```

Bekräfta att användaren loggar in med rätt autentiseringsuppgifter.

Om webb klienten används kontrollerar du att det inte finns några problem med cachelagrade autentiseringsuppgifter.

## <a name="next-steps"></a>Nästa steg

- En översikt över fel sökning av virtuella Windows-datorer och eskalerade spår finns i [fel söknings översikt, feedback och support](troubleshoot-set-up-overview.md).
- Information om hur du felsöker problem när du skapar en klient och en adresspool i en Windows Virtual Desktop-miljö finns i [skapa innehavare och skapa värdar för pooler](troubleshoot-set-up-issues.md).
- Information om hur du felsöker problem när du konfigurerar en virtuell dator (VM) i Windows Virtual Desktop finns i [konfiguration av Session Host-dator](troubleshoot-vm-configuration.md).
- Information om hur du felsöker problem när du använder PowerShell med Windows Virtual Desktop finns i [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md).
- Information om hur du går igenom en fel söknings kurs finns i [Självstudier: Felsöka distributioner av Resource Manager-mallar](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot).
