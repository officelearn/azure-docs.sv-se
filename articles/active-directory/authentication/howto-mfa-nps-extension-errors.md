---
title: Felsök felkoder för Azure MFA NPS-tillägget | Microsoft Docs
description: Få hjälp med att lösa problem med NPS-tillägget för Azure Multi-Factor Authentication
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c831a5a939e946d7189e4d35eaf051508e904a4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "57999521"
---
# <a name="resolve-error-messages-from-the-nps-extension-for-azure-multi-factor-authentication"></a>Åtgärda felmeddelanden från NPS-tillägget för Azure Multi-Factor Authentication

Om det uppstår fel med NPS-tillägget för Azure Multi-Factor Authentication, kan du använda den här artikeln för att nå en lösning snabbare. NPS-tillägget loggar finns i Loggboken under **Vyhanteraren** > **serverroller** > **nätverkspolicy och åtkomsttjänster** på den servern där NPS-tillägget är installerad.

## <a name="troubleshooting-steps-for-common-errors"></a>Felsökning av vanliga fel

| Felkod | Felsökningsanvisningar |
| ---------- | --------------------- |
| **CONTACT_SUPPORT** | [Kontakta supporten](#contact-microsoft-support), och nämner lista över steg för att samla in loggar. Ange så mycket information som du kan om vad som hände före felet, inklusive klient-id och användarens huvudnamn (UPN). |
| **CLIENT_CERT_INSTALL_ERROR** | Det kan finnas ett problem med hur klientcertifikatet installerades eller som är associerade med din klient. Följ instruktionerna i [felsökning MFA NPS-tillägget](howto-mfa-nps-extension.md#troubleshooting) att undersöka problem med certifikat-klienten. |
| **ESTS_TOKEN_ERROR** | Följ instruktionerna i [felsökning MFA NPS-tillägget](howto-mfa-nps-extension.md#troubleshooting) för att undersöka klientcertifikatet och ADAL token problem. |
| **HTTPS_COMMUNICATION_ERROR** | NPS-servern kan inte ta emot svar från Azure MFA. Kontrollera att dina brandväggar är öppen i två riktningar för trafik till och från https://adnotifications.windowsazure.com |
| **HTTP_CONNECT_ERROR** | På den server som kör NPS-tillägget, kontrollerar du att du kan nå https://adnotifications.windowsazure.com och https://login.microsoftonline.com/. Om dessa platser inte läsa in, felsöka anslutning på den servern. |
| **NPS-tillägget för Azure MFA:** <br> NPS-tillägget för Azure MFA utför endast sekundär autentisering för Radius-förfrågningar i AccessAccept tillstånd. Begäran mottagen för användarnamnet för användaren med svar tillstånd AccessReject, ignorerar begäran. | Det här felet återspeglar vanligtvis ett autentiseringsfel i AD eller som NPS-servern inte kan ta emot svar från Azure AD. Kontrollera att dina brandväggar är öppen i två riktningar för trafik till och från https://adnotifications.windowsazure.com och https://login.microsoftonline.com med hjälp av portarna 80 och 443. Det är också viktigt att kontrollera att inställningen anges till ”styra åtkomst genom NPS-nätverksprinciper” på fliken DIAL-IN åtkomstbehörighet för nätverket. |
| **REGISTRY_CONFIG_ERROR** | En nyckel saknas i registret för programmet, vilket kan bero på att den [PowerShell-skript](howto-mfa-nps-extension.md#install-the-nps-extension) inte kör efter installationen. Felmeddelandet ska innehålla nyckeln som saknas. Kontrollera att du har nyckeln under HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa. |
| **REQUEST_FORMAT_ERROR** <br> RADIUS-begäran saknar det obligatoriska attributet för Radius-userName\Identifier. Kontrollera att NPS tar emot RADIUS-begäranden | Det här felet återspeglar vanligtvis ett installationsproblem. NPS-tillägget måste installeras i NPS-servrar som kan ta emot RADIUS-förfrågningar. NPS-servrar som är installerade som beroenden för tjänster som RDG och RRAS inte ta emot radius-förfrågningar. NPS-tillägget fungerar inte när du installerar över sådana installationer och fel ut eftersom det inte går att läsa information från autentiseringsbegäran. |
| **REQUEST_MISSING_CODE** | Kontrollera att lösenordet krypteringsprotokollet mellan NPS- och NAS-servrarna har stöd för den sekundära autentiseringsmetoden som du använder. **PAP** har stöd för alla autentiseringsmetoder av Azure MFA i molnet: telefonsamtal, enkelriktad SMS, mobilapp och mobilapp-Verifieringskod. **CHAPv2** och **EAP** stöd för telefonsamtal och mobilapp. |
| **USERNAME_CANONICALIZATION_ERROR** | Kontrollera att användaren finns i din lokala Active Directory-instans och att NPS-tjänsten har behörighet att komma åt katalogen. Om du använder förtroenderelationer mellan skogar, [supporten](#contact-microsoft-support) för ytterligare hjälp. |

### <a name="alternate-login-id-errors"></a>Alternativa inloggnings-ID fel

| Felkod | Felmeddelande | Felsökningsanvisningar |
| ---------- | ------------- | --------------------- |
| **ALTERNATE_LOGIN_ID_ERROR** | Fel: userObjectSid sökningen misslyckades | Kontrollera att användaren finns i din lokala Active Directory-instans. Om du använder förtroenderelationer mellan skogar, [supporten](#contact-microsoft-support) för ytterligare hjälp. |
| **ALTERNATE_LOGIN_ID_ERROR** | Fel: Alternativa LoginId sökningen misslyckades | Kontrollera att LDAP_ALTERNATE_LOGINID_ATTRIBUTE har angetts till en [giltigt active directory-attributet](https://msdn.microsoft.com/library/ms675090(v=vs.85).aspx). <br><br> Om LDAP_FORCE_GLOBAL_CATALOG har angetts till True, eller LDAP_LOOKUP_FORESTS har konfigurerats med en icke-tomma värden, kontrollera att du har konfigurerat en Global katalog och att attributet AlternateLoginId har lagts till den. <br><br> Om LDAP_LOOKUP_FORESTS konfigureras med en icke-tomma värden, kontrollerar du att värdet är korrekt. Om det finns fler än en skogsnamn, måste namnen avgränsade med semikolon, inte blanksteg. <br><br> Om de här stegen inte löser problemet, [supporten](#contact-microsoft-support) för mer hjälp. |
| **ALTERNATE_LOGIN_ID_ERROR** | Fel: Alternativt LoginId värde är tomt | Kontrollera att attributet AlternateLoginId har konfigurerats för användaren. |

## <a name="errors-your-users-may-encounter"></a>Fel kan uppstå när dina användare

| Felkod | Felmeddelande | Felsökningsanvisningar |
| ---------- | ------------- | --------------------- |
| **AccessDenied** | Anroparen klienten har inte behörighet att utföra autentisering för användaren | Kontrollera om klientdomänen och domänen för användarens huvudnamn (UPN) är lika. Till exempel se till att user@contoso.com försöker autentisera till Contoso-klienten. UPN-namnet representerar en giltig användare för klienten i Azure. |
| **AuthenticationMethodNotConfigured** | Den angivna autentiseringsmetoden har inte konfigurerats för användaren | Få användaren att lägga till eller verifiera sina verifieringsmetoder enligt anvisningarna i [hantera dina inställningar för tvåstegsverifiering](../user-help/multi-factor-authentication-end-user-manage-settings.md). |
| **AuthenticationMethodNotSupported** | Angivna autentiseringsmetod stöds inte. | Samla in alla loggar som innehåller det här felet och [supporten](#contact-microsoft-support). När du kontaktar supporten, anger du användarnamnet och den sekundära autentiseringsmetoden som utlöste felet. |
| **BecAccessDenied** | MSODS Bec anropet returnerade åtkomst nekad, förmodligen användarnamnet har inte definierats i klienten | Användaren finns i Active Directory lokalt men har synkroniserats inte till Azure AD genom att AD Connect. Eller användaren saknas för klienten. Lägga till användaren i Azure AD och ber dem lägga till sina verifieringsmetoder enligt anvisningarna i [hantera dina inställningar för tvåstegsverifiering](../user-help/multi-factor-authentication-end-user-manage-settings.md). |
| **InvalidFormat** eller **StrongAuthenticationServiceInvalidParameter** | Telefonnumret är i ett okänt format | Få användaren att korrigera sina telefonnummer för verifiering. |
| **InvalidSession** | Den angivna sessionen är ogiltig eller har gått ut | Sessionen har tagit mer än tre minuter att slutföra. Kontrollera att användaren att ange verifieringskoden eller svara på appmeddelande inom tre minuter med att starta autentiseringsbegäran. Om detta inte löser problemet, kontrollerar du att det finns inga nätverksfördröjningar mellan klienten, NAS-Server, NPS-Server och Azure MFA-slutpunkten.  |
| **NoDefaultAuthenticationMethodIsConfigured** | Inga standardmetoden för autentisering har konfigurerats för användaren | Få användaren att lägga till eller verifiera sina verifieringsmetoder enligt anvisningarna i [hantera dina inställningar för tvåstegsverifiering](../user-help/multi-factor-authentication-end-user-manage-settings.md). Kontrollera att användaren har valt en standardmetoden för autentisering och konfigurerat den metoden för sitt konto. |
| **OathCodePinIncorrect** | Fel kod och angiven PIN-kod. | Det här felet är inte förväntas i NPS-tillägget. Om dina användare påträffar det [supporten](#contact-microsoft-support) för hjälp med felsökning. |
| **ProofDataNotFound** | Funktionstest data har inte angetts för den angivna autentiseringsmetoden. | Låt användaren prova en annan verifieringsmetod eller lägga till en ny verifieringsmetoder enligt anvisningarna i [hantera dina inställningar för tvåstegsverifiering](../user-help/multi-factor-authentication-end-user-manage-settings.md). Om användaren fortfarande ser det här felet när du har bekräftat att deras verifieringsmetod är korrekt konfigurerad, [supporten](#contact-microsoft-support). |
| **SMSAuthFailedWrongCodePinEntered** | Fel kod och angiven PIN-kod. (OneWaySMS) | Det här felet är inte förväntas i NPS-tillägget. Om dina användare påträffar det [supporten](#contact-microsoft-support) för hjälp med felsökning. |
| **TenantIsBlocked** | Klienten är blockerad | [Kontakta supporten](#contact-microsoft-support) med katalog-ID från Azure AD-sidan för egenskaper i Azure-portalen. |
| **UserNotFound** | Det gick inte att hitta den angivna användaren | Klienten är inte längre visas som aktiv i Azure AD. Kontrollera att din prenumeration är aktiv och att du har de nödvändiga första parts appar. Kontrollera också att innehavaren i certifikatets ämne är som förväntat och certifikatet fortfarande är giltig och registrerad under tjänstens huvudnamn. |

## <a name="messages-your-users-may-encounter-that-arent-errors"></a>Meddelanden som användarna kan stöta på som inte är fel

Ibland kan kan dina användare få meddelanden från Multi-Factor Authentication eftersom deras autentiseringsbegäran misslyckades. De är inte fel i produkten av konfigurationen, men är avsiktlig varningar förklarar varför en autentiseringsbegäran nekades.

| Felkod | Felmeddelande | Rekommenderade åtgärder | 
| ---------- | ------------- | ----------------- |
| **OathCodeIncorrect** | Fel kod entered\OATH felaktig kod | Användaren angav fel kod. Låt användaren prova igen genom att begära en ny kod eller logga in igen. | 
| **SMSAuthFailedMaxAllowedCodeRetryReached** | Högsta tillåtna kod försök har nåtts | Användaren inte verifieringen utmaningen för många gånger. Beroende på dina inställningar kan behöva de vara avblockerad av en administratör nu.  |
| **SMSAuthFailedWrongCodeEntered** | Fel kod har angett/Text Message OTP felaktigt | Användaren angav fel kod. Låt användaren prova igen genom att begära en ny kod eller logga in igen. |

## <a name="errors-that-require-support"></a>Fel som kräver stöd

Om det uppstår något av dessa fel, rekommenderar vi att du [supporten](#contact-microsoft-support) diagnostiska hjälp. Det finns ingen standarduppsättning steg som kan lösa de här felen. När du kontaktar supporten, se till att inkludera som mycket information som möjligt om de steg som ledde till ett fel och klientorganisationens information.

| Felkod | Felmeddelande |
| ---------- | ------------- |
| **InvalidParameter** | Begäran får inte vara null |
| **InvalidParameter** | Objekt-ID får inte vara null eller tom för ReplicationScope:{0} |
| **InvalidParameter** | Längden på CompanyName \{0} \ är längre än maxlängden {1} |
| **InvalidParameter** | UserPrincipalName får inte vara null eller tomt |
| **InvalidParameter** | Den angivna TenantId är inte i rätt format |
| **InvalidParameter** | Sessions-ID får inte vara null eller tomt |
| **InvalidParameter** | Det gick inte att matcha alla ProofData från begäran eller Msods. ProofData är okänd |
| **InternalError** |  |
| **OathCodePinIncorrect** |  |
| **VersionNotSupported** |  |
| **MFAPinNotSetup** |  |

## <a name="next-steps"></a>Nästa steg

### <a name="troubleshoot-user-accounts"></a>Felsöka användarkonton

Om dina användare finns [har du problem med tvåstegsverifiering](../user-help/multi-factor-authentication-end-user-troubleshoot.md), hjälpa dem själv diagnostisera problem.

### <a name="contact-microsoft-support"></a>Kontakta Microsoft-supporten

Om du behöver ytterligare hjälp kan du kontakta en supporttekniker via [support för Azure Multi-Factor Authentication Server](https://support.microsoft.com/oas/default.aspx?prid=14947). När du kontaktar oss, är det bra om du kan inkludera så mycket information om problemet som möjligt. Information som du kan ange innehåller sidan där du såg felet, den specifika felkoden specifika sessions-ID, ID för den användare som såg felet, loggar och felsökningsloggar.

Använd följande steg för att samla in felsökningsloggar support diagnostik på NPS-servern:

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
6. ZIP-innehållet i mappen C:\NPS och bifoga den komprimerade filen till support-ärende.
