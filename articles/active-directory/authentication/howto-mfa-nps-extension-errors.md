---
title: Felsöka Azure MFA NPS-tillägg – Azure Active Directory
description: Få hjälp med att lösa problem med NPS-tillägget för Azure Multi-Factor Authentication
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 330363a78aa9b642f4794cee40bbf040d3484b4b
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80653721"
---
# <a name="resolve-error-messages-from-the-nps-extension-for-azure-multi-factor-authentication"></a>Åtgärda felmeddelanden från NPS-tillägget för Azure Multi-Factor Authentication

Om du stöter på fel med NPS-tillägget för Azure Multi-Factor Authentication använder du den här artikeln för att nå en lösning snabbare. NPS-tilläggsloggar finns i Loggboken under **Anpassade vyer** > **Serverroller** > **Nätverksprincip och Åtkomsttjänster** på servern där NPS-tillägget är installerat.

## <a name="troubleshooting-steps-for-common-errors"></a>Felsökningssteg för vanliga fel

| Felkod | Felsökningsanvisningar |
| ---------- | --------------------- |
| **CONTACT_SUPPORT** | [Kontakta supporten](#contact-microsoft-support)och ange listan med steg för att samla in loggar. Ange så mycket information du kan om vad som hände före felet, inklusive klient-ID och användarnamn (UPN). |
| **CLIENT_CERT_INSTALL_ERROR** | Det kan finnas ett problem med hur klientcertifikatet installerades eller associerades med din klient. Följ instruktionerna i [Felsökning av MFA NPS-tillägget](howto-mfa-nps-extension.md#troubleshooting) för att undersöka problem med klientcert. |
| **ESTS_TOKEN_ERROR** | Följ instruktionerna i [Felsökning av MFA NPS-tillägget](howto-mfa-nps-extension.md#troubleshooting) för att undersöka klientcertifikat och ADAL-tokenproblem. |
| **HTTPS_COMMUNICATION_ERROR** | NPS-servern kan inte ta emot svar från Azure MFA. Kontrollera att brandväggarna är öppna varannanriktat för trafik till och frånhttps://adnotifications.windowsazure.com |
| **HTTP_CONNECT_ERROR** | Kontrollera att du kan nå `https://adnotifications.windowsazure.com` och `https://login.microsoftonline.com/`. Om dessa platser inte läses in felsöker du anslutningen på den servern. |
| **NPS-tillägg för Azure MFA:** <br> NPS-tillägg för Azure MFA utför endast sekundär auth för Radius-begäranden i AccessAccept-tillstånd. Begäran mottagen för användarens användarnamn med svarstillstånd AccessReject, ignorera begäran. | Det här felet återspeglar vanligtvis ett autentiseringsfel i AD eller att NPS-servern inte kan ta emot svar från Azure AD. Kontrollera att brandväggarna är öppna varannanriktat `https://adnotifications.windowsazure.com` för `https://login.microsoftonline.com` trafik till och från och med portarna 80 och 443. Det är också viktigt att kontrollera att inställningen är inställd på "kontrollåtkomst via NPS-nätverksprincipen" på uppringningsfliken i behörigheterna för nätverksåtkomst i Nätverksåtkomst. Det här felet kan också utlösa om användaren inte har tilldelats en licens. |
| **REGISTRY_CONFIG_ERROR** | En nyckel saknas i registret för programmet, vilket kan bero på att [PowerShell-skriptet](howto-mfa-nps-extension.md#install-the-nps-extension) inte kördes efter installationen. Felmeddelandet bör innehålla den saknade nyckeln. Kontrollera att du har nyckeln under HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa. |
| **REQUEST_FORMAT_ERROR** <br> Radius Request saknas obligatoriskt Radius userName\Identifier-attribut. Kontrollera att NPS tar emot RADIUS-begäranden | Det här felet återspeglar vanligtvis ett installationsproblem. NPS-tillägget måste installeras i NPS-servrar som kan ta emot RADIUS-begäranden. NPS-servrar som är installerade som beroenden för tjänster som RDG och RRAS tar inte emot radiebegäranden. NPS Extension fungerar inte när det installeras över sådana installationer och fel ut eftersom det inte kan läsa detaljer från autentiseringsbegäran. |
| **REQUEST_MISSING_CODE** | Kontrollera att lösenordskrypteringsprotokollet mellan NPS- och NAS-servrarna stöder den sekundära autentiseringsmetod som du använder. **PAP** stöder alla autentiseringsmetoder för Azure MFA i molnet: telefonsamtal, enkelriktad textmeddelande, mobilappmeddelanden och verifieringskod för mobilappar. **CHAPV2** och **EAP** stöder telefonsamtal och mobilapp anmälan. |
| **USERNAME_CANONICALIZATION_ERROR** | Kontrollera att användaren finns i den lokala Active Directory-instansen och att NPS-tjänsten har behörighet att komma åt katalogen. Om du använder gränsöverskridande förtroenden [kontaktar du supporten](#contact-microsoft-support) för ytterligare hjälp. |

### <a name="alternate-login-id-errors"></a>Alternativa inloggnings-ID-fel

| Felkod | Felmeddelande | Felsökningsanvisningar |
| ---------- | ------------- | --------------------- |
| **ALTERNATE_LOGIN_ID_ERROR** | Fel: userObjectSid-sökning misslyckades | Kontrollera att användaren finns i den lokala Active Directory-instansen. Om du använder gränsöverskridande förtroenden [kontaktar du supporten](#contact-microsoft-support) för ytterligare hjälp. |
| **ALTERNATE_LOGIN_ID_ERROR** | Fel: Alternate LoginId-sökning misslyckades | Kontrollera att LDAP_ALTERNATE_LOGINID_ATTRIBUTE är inställt på ett [giltigt active directory-attribut](https://msdn.microsoft.com/library/ms675090(v=vs.85).aspx). <br><br> Om LDAP_FORCE_GLOBAL_CATALOG är inställt på True eller om LDAP_LOOKUP_FORESTS är konfigurerat med ett icke-tomt värde kontrollerar du att du har konfigurerat en global katalog och att attributet AlternateLoginId läggs till i den. <br><br> Om LDAP_LOOKUP_FORESTS är konfigurerat med ett icke-tomt värde kontrollerar du att värdet är korrekt. Om det finns mer än ett skogsnamn måste namnen separeras med semikolon, inte mellanslag. <br><br> Om de här stegen inte löser problemet [kontaktar du supporten](#contact-microsoft-support) för mer hjälp. |
| **ALTERNATE_LOGIN_ID_ERROR** | Fel: Alternativt LoginId-värde är tomt | Kontrollera att Attributet AlternateLoginId är konfigurerat för användaren. |

## <a name="errors-your-users-may-encounter"></a>Fel som användarna kan stöta på

| Felkod | Felmeddelande | Felsökningsanvisningar |
| ---------- | ------------- | --------------------- |
| **ÅtkomstDyd** | Anroparens klient har inte behörighet att utföra autentisering för användaren | Kontrollera om klientdomänen och domänen för användarens huvudnamn (UPN) är desamma. Kontrollera till exempel user@contoso.com att den försöker autentisera till Contoso-klienten. UPN representerar en giltig användare för klienten i Azure. |
| **AuthenticationMethodNotKonfigurerad** | Den angivna autentiseringsmetoden har inte konfigurerats för användaren | Låt användaren lägga till eller verifiera sina verifieringsmetoder enligt instruktionerna i [Hantera dina inställningar för tvåstegsverifiering](../user-help/multi-factor-authentication-end-user-manage-settings.md). |
| **AuthenticationMethodNotSupported** | Den angivna autentiseringsmetoden stöds inte. | Samla in alla dina loggar som innehåller det här felet och [kontakta supporten](#contact-microsoft-support). När du kontaktar supporten anger du användarnamnet och den sekundära verifieringsmetoden som utlöste felet. |
| **BecAccessDenied** | MSODS Bec-anropet nekade åtkomst nekad, förmodligen användarnamnet inte definieras i klienten | Användaren finns i Active Directory lokalt men synkroniseras inte till Azure AD av AD Connect. Eller så saknas användaren för klienten. Lägg till användaren i Azure AD och be dem lägga till sina verifieringsmetoder enligt instruktionerna i [Hantera dina inställningar för tvåstegsverifiering](../user-help/multi-factor-authentication-end-user-manage-settings.md). |
| **InvalidFormat** eller **StrongAuthenticationServiceInvalidParameter** | Telefonnumret är i ett oigenkännligt format | Be användaren korrigera sina verifieringstelefonnummer. |
| **Ogiltigsession** | Den angivna sessionen är ogiltig eller kan ha upphört att gälla | Sessionen har tagit mer än tre minuter att slutföra. Kontrollera att användaren anger verifieringskoden eller svarar på appaviseringen inom tre minuter efter att autentiseringsbegäran initierats. Om det inte löser problemet kontrollerar du att det inte finns några nätverksavgångar mellan klient, NAS Server, NPS Server och Azure MFA-slutpunkten.  |
| **NoDefaultAuthenticationMethodIsKonfigurerad** | Ingen standardautentiseringsmetod har konfigurerats för användaren | Låt användaren lägga till eller verifiera sina verifieringsmetoder enligt instruktionerna i [Hantera dina inställningar för tvåstegsverifiering](../user-help/multi-factor-authentication-end-user-manage-settings.md). Kontrollera att användaren har valt en standardautentiseringsmetod och konfigurerade den metoden för sitt konto. |
| **OathCodePinInkorrigering** | Fel kod och stift anges. | Det här felet förväntas inte i NPS-tillägget. Om användaren stöter på detta [kontaktar du supporten](#contact-microsoft-support) för felsökning. |
| **ProofDataNotFound** | Bevisdata har inte konfigurerats för den angivna autentiseringsmetoden. | Låt användaren prova en annan verifieringsmetod eller lägga till en ny verifieringsmetod enligt instruktionerna i [Hantera inställningarna för tvåstegsverifiering](../user-help/multi-factor-authentication-end-user-manage-settings.md). Om användaren fortsätter att se det här felet efter att du har bekräftat att deras verifieringsmetod har konfigurerats korrekt [kontaktar du supporten](#contact-microsoft-support). |
| **SMSAuthFailedWrongCodePin Inmatad** | Fel kod och stift anges. (OneWaySMS) | Det här felet förväntas inte i NPS-tillägget. Om användaren stöter på detta [kontaktar du supporten](#contact-microsoft-support) för felsökning. |
| **Klientisblockerad** | Klienten är blockerad | [Kontakta support](#contact-microsoft-support) med katalog-ID från sidan Azure AD-egenskaper i Azure-portalen. |
| **AnvändareNotFound** | Det gick inte att hitta den angivna användaren | Klienten visas inte längre som aktiv i Azure AD. Kontrollera att din prenumeration är aktiv och att du har de nödvändiga förstapartsapparna. Kontrollera också att klienten i certifikatämnet är som förväntat och att certifikatet fortfarande är giltigt och registrerat under tjänstens huvudnamn. |

## <a name="messages-your-users-may-encounter-that-arent-errors"></a>Meddelanden som användarna kan stöta på som inte är fel

Ibland kan användarna få meddelanden från multifaktorautentisering eftersom deras autentiseringsbegäran misslyckades. Det här är inga fel i konfigurationsprodukten, men är avsiktliga varningar som förklarar varför en autentiseringsbegäran nekades.

| Felkod | Felmeddelande | Rekommenderade åtgärder | 
| ---------- | ------------- | ----------------- |
| **OathCodeIncorrect** | Fel kod har angetts\OATH-kod felaktig | Användaren angav fel kod. Be dem försöka igen genom att begära en ny kod eller logga in igen. | 
| **SMSAuthFailedMaxAllowedCodeRetryReached** | Maximalt tillåtet återförsök för kod | Användaren misslyckades verifieringsutmaningen för många gånger. Beroende på dina inställningar kan de behöva hävas av en administratör nu.  |
| **SMSAuthFailedWrongCode Inmatad** | Fel kod inmatats/2-sms felaktigt | Användaren angav fel kod. Be dem försöka igen genom att begära en ny kod eller logga in igen. |

## <a name="errors-that-require-support"></a>Fel som kräver stöd

Om du stöter på något av dessa fel rekommenderar vi att du [kontaktar supporten](#contact-microsoft-support) för diagnostikhjälp. Det finns ingen standarduppsättning steg som kan åtgärda dessa fel. När du kontaktar supporten måste du inkludera så mycket information som möjligt om de steg som ledde till ett fel och din klientinformation.

| Felkod | Felmeddelande |
| ---------- | ------------- |
| **Ogiltigparameter** | Begäran får inte vara null |
| **Ogiltigparameter** | ObjectId får inte vara null eller tom för ReplicationScope:{0} |
| **Ogiltigparameter** | Längden på CompanyName \{0}\ är längre än den högsta tillåtna längden{1} |
| **Ogiltigparameter** | UserPrincipalName får inte vara null eller tomt |
| **Ogiltigparameter** | Den angivna TenantId är inte i korrekt format |
| **Ogiltigparameter** | SessionId får inte vara null eller tom |
| **Ogiltigparameter** | Det gick inte att lösa några ProofData från begäran eller Msods. ProofData är inte känd |
| **InternalError** |  |
| **OathCodePinInkorrigering** |  |
| **VersionInte Stöds** |  |
| **MFAPinNotSetup** |  |

## <a name="next-steps"></a>Nästa steg

### <a name="troubleshoot-user-accounts"></a>Felsöka användarkonton

Om användarna [har problem med tvåstegsverifiering](../user-help/multi-factor-authentication-end-user-troubleshoot.md)hjälper du dem att själv diagnostisera problem.

### <a name="health-check-script"></a>Skript för hälsokontroll

[Hälsokontrollskriptet för Azure MFA NPS Extension](https://docs.microsoft.com/samples/azure-samples/azure-mfa-nps-extension-health-check/azure-mfa-nps-extension-health-check/) utför en grundläggande hälsokontroll vid felsökning av NPS-tillägget. Kör skriptet och välj alternativ 3.

### <a name="contact-microsoft-support"></a>Kontakta Microsoft-supporten

Om du behöver ytterligare hjälp kontaktar du en supporttekniker via [Azure Multi-Factor Authentication Server](https://support.microsoft.com/oas/default.aspx?prid=14947)support . När du kontaktar oss är det användbart om du kan inkludera så mycket information om ditt problem som möjligt. Information som du kan ange inkluderar sidan där du såg felet, den specifika felkoden, det specifika sessions-ID: t, ID:t för den användare som såg felet och felsökningsloggar.

Så här samlar du in felsökningsloggar för supportdiagnostik genom att använda följande steg på NPS-servern:

1. Öppna Registereditorn och bläddra till HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa som **VERBOSE_LOG** **till SANT**
2. Öppna en kommandotolk för administratörer och kör följande kommandon:

   ```
   Mkdir c:\NPS
   Cd NPS
   netsh trace start Scenario=NetConnection capture=yes tracefile=c:\NPS\nettrace.etl
   logman create trace "NPSExtension" -ow -o c:\NPS\NPSExtension.etl -p {7237ED00-E119-430B-AB0F-C63360C8EE81} 0xffffffffffffffff 0xff -nb 16 16 -bs 1024 -mode Circular -f bincirc -max 4096 -ets
   logman update trace "NPSExtension" -p {EC2E6D3A-C958-4C76-8EA4-0262520886FF} 0xffffffffffffffff 0xff -ets
   ```

3. Återskapa problemet

4. Stoppa spårningen med följande kommandon:

   ```
   logman stop "NPSExtension" -ets
   netsh trace stop
   wevtutil epl AuthNOptCh C:\NPS\%computername%_AuthNOptCh.evtx
   wevtutil epl AuthZOptCh C:\NPS\%computername%_AuthZOptCh.evtx
   wevtutil epl AuthZAdminCh C:\NPS\%computername%_AuthZAdminCh.evtx
   Start .
   ```

5. Öppna Registereditorn och bläddra till HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa som **VERBOSE_LOG** till **FALSE**
6. Zip innehållet i mappen C:\NPS och bifoga den zippade filen till supportärelet.
