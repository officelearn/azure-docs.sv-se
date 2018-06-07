---
title: 'Azure AD Connect: Felsöka anslutningsproblem | Microsoft Docs'
description: Beskriver hur du felsöker problem med nätverksanslutningen med Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 3aa41bb5-6fcb-49da-9747-e7a3bd780e64
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 2c12630deb45fd057537c42157d88fdeef22d18b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34593021"
---
# <a name="troubleshoot-connectivity-issues-with-azure-ad-connect"></a>Felsökning av anslutningsproblem med Azure AD Connect
Den här artikeln förklarar hur anslutning mellan Azure AD Connect och AD Azure fungerar och hur du felsöker problem med nätverksanslutningen. Dessa problem är stor sannolikhet kommer att visas i en miljö med en proxyserver.

## <a name="troubleshoot-connectivity-issues-in-the-installation-wizard"></a>Felsökning av anslutningsproblem i installationsguiden
Azure AD Connect använder Modern autentisering (med hjälp av ADAL-biblioteket) för autentisering. Installationsguiden och rätt Synkroniseringsmotorn kräver machine.config konfigureras korrekt eftersom dessa två .NET-program.

I den här artikeln visar vi hur Fabrikam ansluter till Azure AD via dess proxy. Proxyservern heter fabrikamproxy och använder port 8080.

Vi måste först se till att [ **machine.config** ](active-directory-aadconnect-prerequisites.md#connectivity) är korrekt konfigurerad.  
![machineconfig](./media/active-directory-aadconnect-troubleshoot-connectivity/machineconfig.png)

> [!NOTE]
> I vissa icke-Microsoft-bloggar dokumenteras det att ändringar ska göras till miiserver.exe.config i stället. Den här filen är över på varje uppgradering även om det fungerar under inledande installationen systemet slutar att fungera på första uppgraderingen. Därför vi rekommenderar att uppdatera machine.config i stället.
>
>

Proxyservern måste också ha de URL: erna öppnas. Den officiella förteckningen dokumenteras i [Office 365-URL: er och IP-adressintervall](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

Följande tabell är det absoluta minst för att kunna ansluta till Azure AD alls av dessa webbadresser. Den här listan innehåller inte några valfria funktioner, till exempel tillbakaskrivning av lösenord eller Azure AD Connect Health. Den dokumenteras här för att underlätta felsökningen för den inledande konfigurationen.

| URL | Port | Beskrivning |
| --- | --- | --- |
| mscrl.microsoft.com |HTTP/80 |Används för att hämta listor över återkallade certifikat. |
| \*.verisign.com |HTTP/80 |Används för att hämta listor över återkallade certifikat. |
| \*. entrust.com |HTTP/80 |Används för att hämta listor över återkallade certifikat för MFA. |
| \*.windows.net |HTTPS/443 |Används för att logga in på Azure AD. |
| secure.aadcdn.microsoftonline-p.com |HTTPS/443 |Används för MFA. |
| \*.microsoftonline.com |HTTPS/443 |Används för att konfigurera Azure AD-katalogen och importera och exportera data. |

## <a name="errors-in-the-wizard"></a>Fel i guiden
Installationsguiden använder två olika kontexter. På sidan **Anslut till Azure AD**, som används för närvarande inloggad användare. På sidan **konfigurera**, ändrar till den [konto som kör tjänsten för Synkroniseringsmotorn](active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-account). Om det finns ett problem, visas det förmodligen redan på den **Anslut till Azure AD** sida i guiden eftersom proxykonfigurationen är globala.

Följande är de vanligaste felmeddelanden i installationsguiden.

### <a name="the-installation-wizard-has-not-been-correctly-configured"></a>Installationsguiden har inte konfigurerats korrekt
Det här felet uppstår när guiden inte kan nå proxyservern.  
![nomachineconfig](./media/active-directory-aadconnect-troubleshoot-connectivity/nomachineconfig.png)

* Om du ser det här felet ska du kontrollera den [machine.config](active-directory-aadconnect-prerequisites.md#connectivity) har konfigurerats korrekt.
* Om som verkar vara korrekta, följer du stegen i [proxy verifiera](#verify-proxy-connectivity) att se om problemet finns utanför guiden.

### <a name="a-microsoft-account-is-used"></a>Ett Microsoft-konto används
Om du använder en **Microsoft-konto** snarare än en **Skol- eller organisation** konto, visas ett allmänt fel.  
![Ett Account används](./media/active-directory-aadconnect-troubleshoot-connectivity/unknownerror.png)

### <a name="the-mfa-endpoint-cannot-be-reached"></a>Det går inte att nå slutpunkten för MFA
Det här felet uppstår om slutpunkten **https://secure.aadcdn.microsoftonline-p.com** går inte att nå och den globala administratören har MFA är aktiverat.  
![nomachineconfig](./media/active-directory-aadconnect-troubleshoot-connectivity/nomicrosoftonlinep.png)

* Om du ser detta fel, kontrollera att slutpunkten **secure.aadcdn.microsoftonline p.com** har lagts till proxyservern.

### <a name="the-password-cannot-be-verified"></a>Lösenordet kan inte verifieras
Om installationsguiden lyckas ansluta till Azure AD, men själva lösenordet går inte att verifiera det här felet visas.  
![badpassword](./media/active-directory-aadconnect-troubleshoot-connectivity/badpassword.png)

* Är lösenordet som ett tillfälligt lösenord och måste ändras? Är det faktiska lösenordet? Försök att logga in på https://login.microsoftonline.com (på en annan dator än Azure AD Connect-server) och kontrollera att kontot är användbara.

### <a name="verify-proxy-connectivity"></a>Kontrollera proxy-anslutningen
För att verifiera om Azure AD Connect-servern är ansluten till faktiska med Proxy- och Internet, använder du vissa PowerShell för att se om proxyservern tillåter webbegäranden eller inte. I PowerShell-Kommandotolken kör `Invoke-WebRequest -Uri https://adminwebservice.microsoftonline.com/ProvisioningService.svc`. (Tekniskt det första anropet är att https://login.microsoftonline.com och den här URI fungerar också, men andra URI: N går snabbare att svara.)

PowerShell använder konfigurationen i machine.config för att kontakta proxyservern. Inställningarna i winhttp/netsh bör inte påverkar dessa cmdlets.

Om proxy är korrekt konfigurerad, du får en lyckad status: ![proxy200](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequest200.png)

Om du får **kan inte ansluta till fjärrservern**, sedan PowerShell försöker att ringa direkt utan att använda proxy eller DNS är inte korrekt konfigurerad. Kontrollera att den **machine.config** filen är korrekt konfigurerad.
![unabletoconnect](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequestunable.png)

Om proxy inte är korrekt konfigurerad, du får ett felmeddelande: ![proxy200](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequest403.png)
![proxy407](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequest407.png)

| Fel | Feltext | Kommentar |
| --- | --- | --- |
| 403 |Förbjudna |Proxyn har inte öppnats för begärd URL. Kontrollera igen proxykonfigurationen och kontrollera att den [URL: er](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) har öppnats. |
| 407 |Det krävs proxyautentisering |Proxyservern måste en inloggning och inget har tillhandahållits. Om proxyservern kräver autentisering, se till att ha den här inställningen konfigurerad i machine.config. Kontrollera också att du använder domänkonton för användaren som kör guiden och för tjänstkontot. |

### <a name="proxy-idle-timeout-setting"></a>Tidsgränsen för inaktivitet proxyinställningar
När Azure AD Connect skickar en begäran för export till Azure AD, kan Azure AD ta upp till 5 minuter att bearbeta begäran innan du genererar ett svar. Detta kan inträffa särskilt om det finns ett antal gruppobjekt med stora gruppmedlemskap som ingår i samma begäran för export. Se till att tidsgränsen för inaktivitet Proxy har konfigurerats för att vara större än 5 minuter. Annars kan du sett återkommande anslutningsproblem med Azure AD på Azure AD Connect-servern.

## <a name="the-communication-pattern-between-azure-ad-connect-and-azure-ad"></a>Mönstret för kommunikation mellan Azure AD Connect och Azure AD
Om du har följt de föregående stegen och fortfarande inte kan ansluta, kan du nu starta tittar på nätverket loggar. Det här avsnittet är dokumentera ett normalt och lyckad anslutning mönster. Det också en lista med vanliga red herrings som kan ignoreras när du läser loggar för nätverket.

* Det finns anrop till https://dc.services.visualstudio.com. Krävs inte har den här URL: en öppen i proxy för att installationen ska lyckas och dessa anrop kan ignoreras.
* Du ser att dns-matchning Listar de faktiska värdarna i DNS-namnet utrymme nsatc.net och andra namnområden inte under microsoftonline.com. Men det finns inte någon webbtjänstbegäranden på de faktiska servernamn och du behöver inte lägga till dessa URL: er till proxyservern.
* Slutpunkter adminwebservice och provisioningapi är identifiering slutpunkter och används för att hitta den faktiska slutpunkten ska användas. De här slutpunkterna är olika beroende på region.

### <a name="reference-proxy-logs"></a>Referens för proxy-loggar
Här är en dump från en verklig proxy-loggen och installationssidan från var den togs (dubblettposter till samma slutpunkt har tagits bort). Det här avsnittet kan användas som referens för proxy- och loggarna. De faktiska slutpunkterna kan skilja sig i din miljö (särskilt dessa URL: er i *kursiv*).

**Anslut till Azure AD**

| Tid | URL |
| --- | --- |
| 1/11/2016 8:31 |Connect://login.microsoftonline.com:443 |
| 1/11/2016 8:31 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:32 |ansluta: / /*bba800 fästpunkt*. microsoftonline.com:443 |
| 1/11/2016 8:32 |Connect://login.microsoftonline.com:443 |
| 1/11/2016 8:33 |Connect://provisioningapi.microsoftonline.com:443 |
| 1/11/2016 8:33 |connect://*bwsc02-relay*.microsoftonline.com:443 |

**Konfigurera**

| Tid | URL |
| --- | --- |
| 1/11/2016 8:43 |Connect://login.microsoftonline.com:443 |
| 1/11/2016 8:43 |ansluta: / /*bba800 fästpunkt*. microsoftonline.com:443 |
| 1/11/2016 8:43 |Connect://login.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:44 |ansluta: / /*bba900 fästpunkt*. microsoftonline.com:443 |
| 1/11/2016 8:44 |Connect://login.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:44 |ansluta: / /*bba800 fästpunkt*. microsoftonline.com:443 |
| 1/11/2016 8:44 |Connect://login.microsoftonline.com:443 |
| 1/11/2016 8:46 |Connect://provisioningapi.microsoftonline.com:443 |
| 1/11/2016 8:46 |connect://*bwsc02-relay*.microsoftonline.com:443 |

**Inledande synkronisering**

| Tid | URL |
| --- | --- |
| 1/11/2016 8:48 |Connect://login.Windows.NET:443 |
| 1/11/2016 8:49 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:49 |ansluta: / /*bba900 fästpunkt*. microsoftonline.com:443 |
| 1/11/2016 8:49 |ansluta: / /*bba800 fästpunkt*. microsoftonline.com:443 |

## <a name="authentication-errors"></a>Autentiseringsfel
Det här avsnittet beskriver fel som kan returneras från ADAL (autentiseringsbiblioteket används av Azure AD Connect) och PowerShell. Felet förklaras hjälper dig i Förstå nästa steg.

### <a name="invalid-grant"></a>Ogiltig bevilja
Ogiltigt användarnamn eller lösenord. Mer information finns i [lösenordet går inte att verifiera](#the-password-cannot-be-verified).

### <a name="unknown-user-type"></a>Okänd typ
Azure AD-katalogen kan inte hitta eller matcha. Du kanske försöker logga in med ett användarnamn i en overifierade domän?

### <a name="user-realm-discovery-failed"></a>Identifiering av startsfär användare misslyckades
Nätverks- och proxyinställningar konfigurationsproblem. Kan inte nås i nätverket. Se [felsökning av anslutningsproblem i installationsguiden av](#troubleshoot-connectivity-issues-in-the-installation-wizard).

### <a name="user-password-expired"></a>Användarens lösenord har upphört att gälla
Dina autentiseringsuppgifter har upphört att gälla. Ändra ditt lösenord.

### <a name="authorizationfailure"></a>AuthorizationFailure
Okänt problem.

### <a name="authentication-cancelled"></a>Autentisering avbröts
Multifaktorautentisering (MFA)-anropet avbröts.

### <a name="connecttomsonline"></a>ConnectToMSOnline
Autentiseringen lyckades, men Azure AD PowerShell har problem med autentisering.

### <a name="azurerolemissing"></a>AzureRoleMissing
Autentiseringen lyckades. Du är inte en global administratör.

### <a name="privilegedidentitymanagement"></a>PrivilegedIdentityManagement
Autentiseringen lyckades. Privileged identity management har aktiverats och du för närvarande är inte en global administratör. Mer information finns i [Privileged Identity Management](../active-directory-privileged-identity-management-getting-started.md).

### <a name="companyinfounavailable"></a>CompanyInfoUnavailable
Autentiseringen lyckades. Det gick inte att hämta företagets information från Azure AD.

### <a name="retrievedomains"></a>RetrieveDomains
Autentiseringen lyckades. Det gick inte att hämta domäninformation från Azure AD.

### <a name="unexpected-exception"></a>Oväntat undantag
Visas som ett oväntat fel i installationsguiden. Kan inträffa om du försöker använda en **Account** snarare än en **Skol- eller organisation konto**.

## <a name="troubleshooting-steps-for-previous-releases"></a>Felsökningssteg för tidigare versioner.
Med versioner från och med build-nummer 1.1.105.0 (utgiven februari 2016), de-inloggningsassistenten har dragits tillbaka. Det här avsnittet och konfigurationen bör inte längre behövs, men sparas som referens.

För den enda-inloggningsassistenten ska fungera, konfigureras winhttp. Den här konfigurationen kan göras med [ **netsh**](active-directory-aadconnect-prerequisites.md#connectivity).  
![Netsh](./media/active-directory-aadconnect-troubleshoot-connectivity/netsh.png)

### <a name="the-sign-in-assistant-has-not-been-correctly-configured"></a>Inloggningsassistenten har inte konfigurerats korrekt
Det här felet visas när inloggningsassistenten inte går att nå proxyservern eller proxyservern tillåter inte begäran.
![nonetsh](./media/active-directory-aadconnect-troubleshoot-connectivity/nonetsh.png)

* Om du ser detta fel granskar du proxykonfigurationen i [netsh](active-directory-aadconnect-prerequisites.md#connectivity) och den är korrekt.
  ![netshshow](./media/active-directory-aadconnect-troubleshoot-connectivity/netshshow.png)
* Om som verkar vara korrekta, följer du stegen i [proxy verifiera](#verify-proxy-connectivity) att se om problemet finns utanför guiden.

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md).
