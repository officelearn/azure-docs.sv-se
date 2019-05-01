---
title: Fjärrskrivbordsklienten anslutningar i Windows virtuellt skrivbord – Azure
description: Så här att lösa problem när du ställer in klientanslutningar i en miljö för virtuella Windows-skrivbordet organisationer.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshoot
ms.date: 04/08/2019
ms.author: v-chjenk
ms.openlocfilehash: c5a67e22c301a2afc73a46a6def9a514426c497f
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64928054"
---
# <a name="remote-desktop-client-connections"></a>Fjärrskrivbordsklienten anslutningar

Använd den här artikeln för att lösa problem med virtuella skrivbord i Windows-klientanslutningar.

## <a name="provide-feedback"></a>Ge feedback

Vi inte är för närvarande tar supportärenden när virtuella Windows-skrivbordet är i förhandsversionen. Gå till den [Windows Desktop Tech-Community virtuella](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) att diskutera virtuellt skrivbord i Windows-tjänsten med produktteamet och aktiva community-medlemmar.

## <a name="you-cant-open-a-web-client"></a>Du kan inte öppna en webbklient

Bekräfta att det finns en internet-anslutning genom att öppna en annan webbplats; till exempel [www.Bing.com](https://www.bing.com).

Använd **nslookup** att bekräfta DNS kan matcha detta FQDN:

    ```cmd
    nslookup rdweb.wvd.microsoft.com
    ```

Vill du försöka ansluta med en annan klient, t.ex. fjärrskrivbord-klienten för Windows 7 eller Windows 10 och kontroll att se om du kan öppna webbklienten.

### <a name="error-opening-another-site-fails"></a>Fel: Öppna en annan plats misslyckas

**Orsak:** Nätverksproblem och/eller avbrott.

**Fix:** Kontakta supporten för nätverket.

### <a name="error-nslookup-cannot-resolve-the-name"></a>Fel: Nslookup matcha inte namnet

**Orsak:** Nätverksproblem och/eller avbrott.

**Fix:** Kontakta supporten för nätverk

### <a name="error-you-cant-connect-but-other-clients-can-connect"></a>Fel: Du kan inte ansluta men andra klienter kan ansluta

**Orsak:** Webbläsaren fungerar inte som förväntat och Stoppad fungerande.

**Fix:** Följ dessa instruktioner för att felsöka webbläsaren.

1. Starta om webbläsaren.
2. Rensa webbläsarcookies. Se [hur du tar bort cookie-filer i Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
3. Rensa webbläsarens cacheminne. Se [rensa webbläsarens cacheminne för din webbläsare](https://binged.it/2RKyfdU).
4. Öppna webbläsaren i privat läge.

## <a name="web-client-stops-responding-or-disconnects"></a>Webbklienten slutar svara eller kopplar

Försök att ansluta med hjälp av en annan webbläsare eller klient.

### <a name="error-other-browsers-and-clients-also-malfunction-or-fail-to-open"></a>Fel: Andra webbläsare och klienter också köras felaktigt eller så öppnas inte

**Orsak:** Nätverk och/eller åtgärden systemproblem eller bortfall

**Fix:** Kontakta supportteam.

## <a name="web-client-keeps-prompting-for-credentials"></a>Webbklienten håller förfrågning om autentiseringsuppgifter

Om webbklienten håller förfrågning om autentiseringsuppgifter, följer du dessa instruktioner.

1. Bekräfta Webbadress för klienten är korrekt.
2. Kontrollera att autentiseringsuppgifterna är för virtuellt skrivbord i Windows-miljö som är kopplad till URL: en.
3. Rensa webbläsarcookies. Se [hur du tar bort cookie-filer i Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
4. Rensa webbläsarens cacheminne. Se [rensa webbläsarens cacheminne för din webbläsare](https://binged.it/2RKyfdU).
5. Öppna webbläsaren i privat läge.

## <a name="remote-desktop-client-for-windows-7-or-windows-10-stops-responding-or-cannot-be-opened"></a>Fjärrskrivbordsklient för Windows 7 eller Windows 10 slutar svara eller kan inte öppnas

Använd följande PowerShell-cmdletar för att rensa out-of-band (OOB) klienten register.

```PowerShell
Remove-ItemProperty 'HKCU:\Software\Microsoft\Terminal Server Client\Default' - Name FeedURLs

#Remove RdClientRadc registry key
Remove-Item 'HKCU:\Software\Microsoft\RdClientRadc' -Recurse

#Remove all files under %appdata%\RdClientRadc
Remove-Item C:\Users\pavithir\AppData\Roaming\RdClientRadc\* -Recurse
```

Gå till **%AppData%\RdClientRadc** och ta bort allt innehåll.

Avinstallera och installera fjärrskrivbord-klienten för Windows 7 och Windows 10.

## <a name="troubleshooting-end-user-connectivity"></a>Felsökning av anslutningsmöjligheter för slutanvändare

Ibland användare kan komma åt sina feed och lokala resurser, men ändå konfiguration, tillgänglighet eller prestandaproblem som hindrar dem från att komma åt fjärresurser. I dessa fall kan användaren får meddelanden liknar dessa:

![Remote Desktop Connection felmeddelande.](media/eb76b666808bddb611448dfb621152ce.png)

![Det går inte att ansluta till gateway-felmeddelandet.](media/a8fbb9910d4672147335550affe58481.png)

De här instruktionerna allmän felsökning för klienten felkoder för anslutningen.

1. Kontrollera användarnamnet och tid när problemet uppstod i samband med.
2. Öppna **PowerShell** och upprätta anslutning till virtuella skrivbord i Windows-klient där problemet har rapporterats.
3. Bekräfta anslutningen till rätt klient med **Get-RdsTenant.**
4. Om det behövs, ange klienten grupp kontext med **Set-RdsContext – TenantGroupt\<TenantGroup\>**.
5. Med hjälp av **Get-RdsHostPool** och **Get-RdsSessionHost** cmdlet: ar, bekräfta att felsöka görs på rätt värden poolen.
6. Kör kommandot nedan för att hämta en lista över alla misslyckade aktiviteter av typen anslutning för det angivna tidsfönstret:

    ```cmd
     Get-RdsDiagnosticActivities -TenantName <TenantName> -username <UPN> -StartTime
     "11/21/2018 1:07:03 PM" -EndTime "11/21/2018 1:27:03 PM" -Outcome Failure -ActivityType Connection
    ```

7. Med hjälp av den **ActivityId** från föregående cmdlet-utdatan, kör du kommandot nedan:

    ```
    (Get-RdsDiagnosticActivities -TenantName $tenant -ActivityId <ActivityId> -Detailed).Errors
    ```

8. Kommandot producerar utdata som liknar de utdata som visas nedan. Använd **ErrorCodeSymbolic** och **ErrorMessage** att felsöka rotorsaken.

    ```
    ErrorSource       : <Source>
    ErrorOperation    : <Operation>
    ErrorCode         : <Error code>
    ErrorCodeSymbolic : <Error code string>
    ErrorMessage      : <Error code message>
    ErrorInternal     : <Internal for the OS>
    ReportedBy        : <Reported by component>
    Time              : <Timestampt>
    ```

### <a name="error-oaddusertogroupfailed--failed-to-add-user--username-to-group--remote-desktop-users-reason-win32errornosuchmember"></a>Fel: O_ADD_USER_TO_GROUP_FAILED / det gick inte att lägga till användare = ≤username≥ till grupp = användare av fjärrskrivbord. Orsak: Win32.ERROR_NO_SUCH_MEMBER

**Orsak:** Virtuell dator ingår inte i den domän där användarobjektet är.

**Fix:** Lägg till virtuell dator till rätt domän. Se [ansluta en Windows Server-dator till en hanterad domän](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-join-windows-vm-portal).

### <a name="error-nslookup-cannot-resolve-the-name"></a>Fel: Nslookup matcha inte namnet

**Orsak:** Nätverks- eller avbrott.

**Fix:** Kontakta supporten för nätverk

### <a name="error-connectionfailedclientprotocolerror"></a>Fel: ConnectionFailedClientProtocolError

**Orsak:** Virtuella datorer som användaren försöker ansluta till är inte ansluten till en domän.

**Fix:** Anslut alla virtuella datorer som ingår i poolen värden till domänkontrollanten.

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>Användare ansluter men inget att visas (inga matning)

En användare kan starta fjärrskrivbordsklienter och kan autentisera, men användaren inte se några ikonerna i webbsökning feed.

Bekräfta att användaren problemen som har tilldelats till programgrupper genom att använda den här kommandoraden:

```cmd
Get-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname>
```

Bekräfta att användaren loggar in med rätt autentiseringsuppgifter.

Om webbklienten används, kontrollerar du att det inte finns några problem med cachelagrade autentiseringsuppgifter.

## <a name="next-steps"></a>Nästa steg

- En översikt om hur du felsöker virtuella Windows-skrivbordet och Eskalering spårar finns [översikt, feedback och support](troubleshoot-set-up-overview.md).
- Felsökning av problem när du skapar en pool med klient- och värden i en miljö med virtuella Windows-skrivbordet beskrivs [klient och värden lagringspoolen skapa](troubleshoot-set-up-issues.md).
- Felsökning av problem när du konfigurerar en virtuell dator (VM) i virtuella Windows-skrivbordet beskrivs [Session Värdkonfiguration för virtuell dator](troubleshoot-vm-configuration.md).
- När du felsöker problem när du använder PowerShell med virtuella Windows-skrivbordet, se [Windows PowerShell för virtuella skrivbord](troubleshoot-powershell.md).
- Läs mer om förhandsversionen av tjänsten i [Windows Desktop förhandsversionsmiljön](https://docs.microsoft.com/azure/virtual-desktop/environment-setup?).
- Om du vill gå igenom en självstudiekurs om felsökning finns i [självstudien: Felsöka malldistributioner för Resource Manager-](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot).
- Läs om granskning åtgärder i [granskningsåtgärder med Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Mer information om åtgärder för att avgöra felen under distributionen, se [visa distributionsåtgärder](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).