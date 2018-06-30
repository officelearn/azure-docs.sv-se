---
title: Felsök felkoder för Azure MFA NPS-tillägg | Microsoft Docs
description: Få hjälp med att lösa problem med NPS-tillägget för Azure Multi-Factor Authentication
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 07/14/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.openlocfilehash: 5f25213d8d1fbc95aa419c86ca5b780f345952ed
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37130207"
---
# <a name="resolve-error-messages-from-the-nps-extension-for-azure-multi-factor-authentication"></a>Lösa felmeddelanden från NPS-tillägg för Azure Multi-Factor Authentication

Om det uppstår problem med NPS-tillägget för Azure Multi-Factor Authentication, kan du använda den här artikeln för att nå en lösning snabbare. 

## <a name="troubleshooting-steps-for-common-errors"></a>Felsökning av vanliga fel

| Felkod | Felsökningsanvisningar |
| ---------- | --------------------- |
| **CONTACT_SUPPORT** | [Kontakta supporten](#contact-microsoft-support), och nämnt lista över steg för att samla in loggar. Ange så mycket information som du kan om vad som hänt innan felet, inklusive klient-id och användarens huvudnamn (UPN). |
| **CLIENT_CERT_INSTALL_ERROR** | Det kan finnas ett problem med hur klientcertifikatet installerades eller som är associerade med din klient. Följ instruktionerna i [felsökning MFA NPS-tillägget](howto-mfa-nps-extension.md#troubleshooting) att undersöka problem med klient-certifikat. |
| **ESTS_TOKEN_ERROR** | Följ instruktionerna i [felsökning MFA NPS-tillägget](howto-mfa-nps-extension.md#troubleshooting) för att undersöka klienten cert och ADAL token problem. |
| **HTTPS_COMMUNICATION_ERROR** | NPS-servern kan inte ta emot svar från Azure MFA. Kontrollera att dina brandväggar är öppet åt två håll för trafik till och från https://adnotifications.windowsazure.com |
| **HTTP_CONNECT_ERROR** | Kontrollera att du kan nå på den server som kör NPS-tillägget https://adnotifications.windowsazure.com och https://login.microsoftonline.com/. Om dessa platser inte ladda, felsöka anslutning på den servern. |
| **REGISTRY_CONFIG_ERROR** | En nyckel saknas i registret för programmet, vilket kan bero på att den [PowerShell-skript](howto-mfa-nps-extension.md#install-the-nps-extension) inte kör efter installationen. Felmeddelandet ska innehålla nyckeln som saknas. Kontrollera att du har nyckeln under HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa. |
| **REQUEST_FORMAT_ERROR** <br> RADIUS-begäran saknar det obligatoriska attributet för Radius-userName\Identifier. Kontrollera att NPS får RADIUS-begäranden | Det här felet visar vanligtvis ett installationsproblem. NPS-tillägget måste vara installerad i NPS-servrar som kan ta emot RADIUS-begäranden. NPS-servrar som är installerade som beroenden för tjänster som Fjärrskrivbordsgateway och RRAS inte ta emot radius-begäranden. NPS-tillägg fungerar inte när du installerar över sådana installationer och fel eftersom det inte går att läsa information från autentiseringsbegäran. |
| **REQUEST_MISSING_CODE** | Kontrollera att lösenordet krypteringsprotokollet mellan NPS- och NAS stöder sekundära autentiseringsmetoden som du använder. **PAP** stöder alla autentiseringsmetoder i Azure MFA i molnet: telefonsamtal, enkelriktade SMS, mobilappavisering och Verifieringskod för mobila appar. **CHAPv2** och **EAP** stöd för telefonsamtal och mobilappavisering. |
| **USERNAME_CANONICALIZATION_ERROR** | Kontrollera att användaren är tillgänglig i din lokala Active Directory-instans och att NPS-tjänsten har behörighet att få åtkomst till katalogen. Om du använder förtroenden mellan skogar, [supporten](#contact-microsoft-support) för ytterligare hjälp. |


   

### <a name="alternate-login-id-errors"></a>Alternativt inloggnings-ID-fel

| Felkod | Felmeddelande | Felsökningsanvisningar |
| ---------- | ------------- | --------------------- |
| **ALTERNATE_LOGIN_ID_ERROR** | Fel: userObjectSid sökning misslyckades | Kontrollera att användaren finns i din lokala Active Directory-instans. Om du använder förtroenden mellan skogar, [supporten](#contact-microsoft-support) för ytterligare hjälp. |
| **ALTERNATE_LOGIN_ID_ERROR** | Fel: Det gick inte att alternativa LoginId sökning | Kontrollera att LDAP_ALTERNATE_LOGINID_ATTRIBUTE är en [giltigt active directory-attributet](https://msdn.microsoft.com/library/ms675090(v=vs.85).aspx). <br><br> Om LDAP_FORCE_GLOBAL_CATALOG har angetts till True, eller LDAP_LOOKUP_FORESTS har konfigurerats med ett tomt värde, kontrollera att du har konfigurerat en Global katalog och att attributet AlternateLoginId har lagts till den. <br><br> Om LDAP_LOOKUP_FORESTS är konfigurerad med ett tomt värde, kan du kontrollera att värdet är korrekt. Om det finns fler än en skogsnamnet, måste namnen vara avgränsade med semikolon, inte blanksteg. <br><br> Om stegen inte löser problemet, [supporten](#contact-microsoft-support) mer hjälp. |
| **ALTERNATE_LOGIN_ID_ERROR** | Fel: Alternativ LoginId värdet är tomt | Kontrollera att attributet AlternateLoginId har konfigurerats för användaren. |


## <a name="errors-your-users-may-encounter"></a>Fel kan uppstå när dina användare

| Felkod | Felmeddelande | Felsökningsanvisningar |
| ---------- | ------------- | --------------------- |
| **AccessDenied** | Anroparen klienten har inte behörighet att utföra autentiseringen för användaren | Kontrollera om klientdomänen och domänen för användarens huvudnamn (UPN) är samma. Till exempel se till att user@contoso.com försöker autentisera till Contoso-klienten. UPN representerar en giltig användare för klienten i Azure. |
| **AuthenticationMethodNotConfigured** | Angiven autentiseringsmetod har inte konfigurerats för användaren | Behörighet att lägga till eller verifiera sina verifieringsmetoderna enligt anvisningarna i [hantera inställningar för tvåstegsverifiering](end-user/current/multi-factor-authentication-end-user-manage-settings.md). |
| **AuthenticationMethodNotSupported** | Angiven autentiseringsmetod stöds inte. | Samla in alla loggfiler som innehåller det här felet och [supporten](#contact-microsoft-support). Ange användarnamnet och det sekundära verifieringsmetod som utlöste felet när du kontaktar supporten. |
| **BecAccessDenied** | MSODS Bec-anropet returnerade åtkomst nekad, förmodligen användarnamnet har inte definierats i klienten | Användaren finns i Active Directory lokalt men har synkroniserats inte till Azure AD med AD Connect. Eller användaren saknas för klienten. Lägga till användaren i Azure AD och ha dem lägga till sina verifieringsmetoderna enligt anvisningarna i [hantera inställningar för tvåstegsverifiering](end-user/current/multi-factor-authentication-end-user-manage-settings.md). |
| **InvalidFormat** eller **StrongAuthenticationServiceInvalidParameter** | Telefonnumret är i ett okänt format | Låt användaren korrigera deras telefonnummer för verifiering. |
| **InvalidSession** | Den angivna sessionen är ogiltig eller har gått ut | Sessionen har trätt i mer än tre minuter att slutföra. Kontrollera att användaren att ange verifieringskoden eller svara på appmeddelande inom tre minuter för inledande autentiseringsförfrågan. Om detta inte löser problemet, kontrollerar du att det inte finns några nätverksfördröjningar mellan klienten, NAS-Server, NPS-Server och Azure MFA-slutpunkten.  |
| **NoDefaultAuthenticationMethodIsConfigured** | Ingen standardautentiseringsmetod har konfigurerats för användaren | Behörighet att lägga till eller verifiera sina verifieringsmetoderna enligt anvisningarna i [hantera inställningar för tvåstegsverifiering](end-user/current/multi-factor-authentication-end-user-manage-settings.md). Kontrollera att användaren har valt en standardmetoden för autentisering och konfigurerats som metod för sitt konto. |
| **OathCodePinIncorrect** | Fel kod och angiven PIN-kod. | Det här felet förväntas inte i NPS-tillägget. Om användaren påträffar, [supporten](#contact-microsoft-support) för hjälp med felsökning. |
| **ProofDataNotFound** | Bevis data har inte konfigurerats för den angivna autentiseringsmetoden. | Låt användaren prova ett annat verifieringsmetod eller Lägg till en ny verifieringsmetoderna enligt anvisningarna i [hantera inställningar för tvåstegsverifiering](end-user/current/multi-factor-authentication-end-user-manage-settings.md). Om användaren fortfarande att se det här felet när du har bekräftat att deras verifieringsmetod är korrekt konfigurerade, [supporten](#contact-microsoft-support). |
| **SMSAuthFailedWrongCodePinEntered** | Fel kod och angiven PIN-kod. (OneWaySMS) | Det här felet förväntas inte i NPS-tillägget. Om användaren påträffar, [supporten](#contact-microsoft-support) för hjälp med felsökning. |
| **TenantIsBlocked** | Klienten är blockerad | [Kontakta supporten](#contact-microsoft-support) med katalog-ID från Azure AD-egenskapssidan i Azure-portalen. |
| **UserNotFound** | Det gick inte att hitta den angivna användaren | Klienten inte längre visas som aktiv i Azure AD. Kontrollera att din prenumeration är aktiv och att du har de nödvändiga första part appar. Kontrollera också att klienten i certifikatets ämne är som förväntat och cert fortfarande är giltig och registrerad under tjänstens huvudnamn. |

## <a name="messages-your-users-may-encounter-that-arent-errors"></a>Meddelandena som användarna kan stöta på som inte fel

Ibland kan kan användarna få meddelanden från Multi-Factor Authentication eftersom deras autentiseringsbegäran misslyckades. Dessa är inte fel i produkten av konfigurationen, men är avsiktlig varningar som förklarar varför begäran om autentisering nekades.

| Felkod | Felmeddelande | Rekommenderade åtgärder | 
| ---------- | ------------- | ----------------- |
| **OathCodeIncorrect** | Fel kod entered\OATH kod felaktig | Användaren har angett fel kod inte ett fel. | Användaren angav fel kod. Låt användaren prova igen genom att begära en ny kod eller logga in igen. | 
| **SMSAuthFailedMaxAllowedCodeRetryReached** | Högsta tillåtna kod försök nå | Användaren inte verifiering utmaning för många gånger. Beroende på dina inställningar, kan de behöva avblockerad av en administratör nu.  |
| **SMSAuthFailedWrongCodeEntered** | Fel kod anges/Text meddelandet OTP felaktigt | Användaren angav fel kod. Låt användaren prova igen genom att begära en ny kod eller logga in igen. |

## <a name="errors-that-require-support"></a>Fel som kräver stöd

Om det uppstår något av dessa fel, rekommenderar vi att du [supporten](#contact-microsoft-support) diagnostiska hjälp. Det finns ingen standarduppsättning steg som kan åtgärda dessa fel. När du kontaktar supporten, se till att ta så mycket information som möjligt om de steg som ledde till ett fel och klient-information.

| Felkod | Felmeddelande |
| ---------- | ------------- |
| **InvalidParameter** | Begäran får inte vara null |
| **InvalidParameter** | ObjectId får inte vara null eller tomt för ReplicationScope:{0} |
| **InvalidParameter** | Längden på CompanyName \{0} \ är längre än den tillåtna maxlängden {1} |
| **InvalidParameter** | UserPrincipalName får inte vara null eller tomt |
| **InvalidParameter** | Den angivna TenantId har inte rätt format |
| **InvalidParameter** | Sessions-ID får inte vara null eller tomt |
| **InvalidParameter** | Det gick inte att matcha alla ProofData från begäran eller Msods. ProofData är okänd |
| **InternalError** |  |
| **OathCodePinIncorrect** |  |
| **VersionNotSupported** |  |
| **MFAPinNotSetup** |  |

## <a name="next-steps"></a>Nästa steg

### <a name="troubleshoot-user-accounts"></a>Felsöka användarkonton

Om användarna är [har problem med tvåstegsverifiering](end-user/current/multi-factor-authentication-end-user-troubleshoot.md), hjälpa dem själv diagnosticera problem. 

### <a name="contact-microsoft-support"></a>Kontakta Microsoft-supporten

Om du behöver ytterligare hjälp kan du kontakta supportpersonalen via [stöd för Azure Multi-Factor Authentication-servern](https://support.microsoft.com/oas/default.aspx?prid=14947). När du kontaktar oss är det bra om du kan ange så mycket information om problemet som möjligt. Information som du kan ange innehåller sidan där du såg fel, den specifika felkoden specifika sessions-ID, ID för den användare som såg felet, loggar och felsökningsloggar.

Använd följande steg på NPS-servern för att samla in felsökningsloggar för stöd för diagnostik:

1. Öppna Registereditorn och gå till HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa set **VERBOSE_LOG** till **SANT**
2. Öppna en administratörskommandotolk och kör följande kommandon:

   ```
   Mkdir c:\NPS
   Cd NPS
   netsh trace start Scenario=NetConnection capture=yes tracefile=c:\NPS\nettrace.etl
   logman create trace "NPSExtension" -ow -o c:\NPS\NPSExtension.etl -p {7237ED00-E119-430B-AB0F-C63360C8EE81} 0xffffffffffffffff 0xff -nb 16 16 -bs 1024 -mode Circular -f bincirc -max 4096 -ets
   logman update trace "NPSExtension" -p {EC2E6D3A-C958-4C76-8EA4-0262520886FF} 0xffffffffffffffff 0xff -ets
   ```

3. Återskapa problemet

4. Stoppa spårning med följande kommandon:

   ```
   logman stop "NPSExtension" -ets
   netsh trace stop
   wevtutil epl AuthNOptCh C:\NPS\%computername%_AuthNOptCh.evtx
   wevtutil epl AuthZOptCh C:\NPS\%computername%_AuthZOptCh.evtx
   wevtutil epl AuthZAdminCh C:\NPS\%computername%_AuthZAdminCh.evtx
   Start .
   ```

5. Öppna Registereditorn och gå till HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa set **VERBOSE_LOG** till **FALSKT**
6. ZIP-innehållet i mappen C:\NPS och bifoga den komprimerade filen i supportärende.


