---
title: 'Azure AD Connect: Felsökning av anslutningsproblem | Microsoft Docs'
description: Beskriver hur du kan felsöka anslutningsproblem med Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 3aa41bb5-6fcb-49da-9747-e7a3bd780e64
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c0afc31bf08a5037d91885bc6a85c6aeaf858825
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57436671"
---
# <a name="troubleshoot-connectivity-issues-with-azure-ad-connect"></a>Felsökning av anslutningsproblem med Azure AD Connect
Den här artikeln förklarar hur anslutningar mellan Azure AD Connect och Azure AD fungerar och hur du felsöker problem med nätverksanslutningen. Dessa problem är mest sannolikt ska synas i en miljö med en proxyserver.

## <a name="troubleshoot-connectivity-issues-in-the-installation-wizard"></a>Felsökning av anslutningsproblem i installationsguiden
Azure AD Connect använder Modern autentisering (med ADAL-biblioteket) för autentisering. Installationsguiden och Synkroniseringsmotorn rätt kräver machine.config konfigureras korrekt eftersom dessa två .NET-program.

I den här artikeln visar vi hur Fabrikam ansluter till Azure AD via dess proxy. Proxyservern heter fabrikamproxy och använder port 8080.

Vi måste först se till att [ **machine.config** ](how-to-connect-install-prerequisites.md#connectivity) är korrekt konfigurerad.  
![machineconfig](./media/tshoot-connect-connectivity/machineconfig.png)

> [!NOTE]
> I vissa icke-Microsoft-bloggar dokumenteras det att ändringar ska göras till miiserver.exe.config i stället. Den här filen är dock över på varje uppgradering även om det fungerar under den inledande installationen kan systemet slutar att fungera vid första uppgradering. Därför är rekommendationen att uppdatera machine.config istället.
>
>

Proxyservern måste också ha de nödvändiga webbadresser som öppnas. Listan över officiella dokumenteras i [Office 365-URL: er och IP-adressintervall](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

I följande tabell är det absoluta minst för att kunna ansluta till Azure AD på alla dessa webbadresser. Den här listan innehåller inte några valfria funktioner, till exempel tillbakaskrivning av lösenord eller Azure AD Connect Health. Den dokumenteras här för att underlätta felsökningen för den inledande konfigurationen.

| URL | Port | Beskrivning |
| --- | --- | --- |
| mscrl.microsoft.com |HTTP/80 |Används för att hämta listor över återkallade certifikat. |
| \*.verisign.com |HTTP/80 |Används för att hämta listor över återkallade certifikat. |
| \*.entrust.net |HTTP/80 |Används för att hämta listor över återkallade certifikat för MFA. |
| \*.windows.net |HTTPS/443 |Används för att logga in på Azure AD. |
| secure.aadcdn.microsoftonline-p.com |HTTPS/443 |Används för MFA. |
| \*.microsoftonline.com |HTTPS/443 |Används för att konfigurera Azure AD-katalogen och importera/exportera data. |

## <a name="errors-in-the-wizard"></a>Fel i guiden
Installationsguiden använder två olika kontexter. På sidan **Anslut till Azure AD**, den använder den för tillfället inloggade användaren. På sidan **konfigurera**, ändras till den [konto som kör tjänsten för Synkroniseringsmotorn](reference-connect-accounts-permissions.md#adsync-service-account). Om det finns ett problem, visas den mest sannolika redan på den **Anslut till Azure AD** sidan i guiden eftersom proxykonfigurationen är globala.

Följande är de vanligaste fel som uppstår i installationsguiden.

### <a name="the-installation-wizard-has-not-been-correctly-configured"></a>Installationsguiden har inte konfigurerats korrekt
Det här felet visas när guiden inte kan nå proxyservern.  
![nomachineconfig](./media/tshoot-connect-connectivity/nomachineconfig.png)

* Om du ser det här felet kan verifiera den [machine.config](how-to-connect-install-prerequisites.md#connectivity) har konfigurerats korrekt.
* Om som verkar vara korrekta, följer du stegen i [Kontrollera proxy-anslutningen](#verify-proxy-connectivity) att se om problemet finns utanför guiden.

### <a name="a-microsoft-account-is-used"></a>Ett Microsoft-konto används
Om du använder en **microsoftkonto** snarare än en **Skol- eller organisation** konto, visas ett allmänt fel.  
![Ett Account används](./media/tshoot-connect-connectivity/unknownerror.png)

### <a name="the-mfa-endpoint-cannot-be-reached"></a>MFA-slutpunkten kan inte nås
Det här felet visas om slutpunkten **https://secure.aadcdn.microsoftonline-p.com** går inte att nå och din globala administratör har aktiverat MFA.  
![nomachineconfig](./media/tshoot-connect-connectivity/nomicrosoftonlinep.png)

* Om du ser det här felet kan du kontrollera att slutpunkten **secure.aadcdn.microsoftonline p.com** har lagts till proxyservern.

### <a name="the-password-cannot-be-verified"></a>Lösenordet kan inte verifieras
Om installationsguiden lyckas ansluta till Azure AD, men själva lösenordet går inte att verifiera du ser detta fel:  
![Felaktigt lösenord.](./media/tshoot-connect-connectivity/badpassword.png)

* Är ett tillfälligt lösenord för lösenordet och måste ändras? Är det faktiskt rätt lösenord? Försök att logga in på https://login.microsoftonline.com (på en annan dator än Azure AD Connect-servern) och kontrollera att konton som kan användas.

### <a name="verify-proxy-connectivity"></a>Kontrollera proxy-anslutningen
Kontrollera om Azure AD Connect-servern har faktiska anslutningarna till proxyservern och Internet genom att använda PowerShell för att se om proxyservern tillåter webbegäranden eller inte. Kör PowerShell-prompten `Invoke-WebRequest -Uri https://adminwebservice.microsoftonline.com/ProvisioningService.svc`. (Tekniskt sett det första anropet är att https://login.microsoftonline.com och den här URI: N fungerar också, men andra URI: N går snabbare att svara.)

PowerShell använder konfigurationen i machine.config för att kontakta proxyservern. Inställningarna i winhttp/netsh bör inte påverka dessa cmdletar.

Om proxyn är korrekt konfigurerad, bör du få en lyckad status: ![proxy200](./media/tshoot-connect-connectivity/invokewebrequest200.png)

Om du får **det går inte att ansluta till fjärrservern**, sedan PowerShell försöker att ringa direkt utan att använda proxyn eller DNS inte är korrekt konfigurerad. Kontrollera att den **machine.config** filen är korrekt konfigurerad.
![unabletoconnect](./media/tshoot-connect-connectivity/invokewebrequestunable.png)

Om proxyn inte är korrekt konfigurerad, du får ett felmeddelande: ![proxy200](./media/tshoot-connect-connectivity/invokewebrequest403.png)
![proxy407](./media/tshoot-connect-connectivity/invokewebrequest407.png)

| Fel | Feltext | Kommentar |
| --- | --- | --- |
| 403 |Förbjudna |Proxyn har inte öppnats för den begärda URL: en. Gå tillbaka till proxykonfigurationen och se till att den [URL: er](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) har öppnats. |
| 407 |Proxyautentisering krävs |Proxyservern måste en inloggning och inget har tillhandahållits. Om proxyservern kräver autentisering, se till att ha den här inställningen konfigurerad i machine.config. Kontrollera också att du använder domänkonton för användaren som kör guiden och för tjänstkontot. |

### <a name="proxy-idle-timeout-setting"></a>Proxyinställningen för timeout för inaktivitet
När Azure AD Connect skickar en begäran om export till Azure AD, kan Azure AD ta upp till 5 minuter att bearbeta begäran innan du genererar ett svar. Detta kan inträffa särskilt om det finns ett antal gruppobjekt med stora gruppmedlemskap som ingår i samma exportbegäran. Se till att tidsgränsen för inaktivitet Proxy har konfigurerats för att vara större än 5 minuter. Tillfälliga anslutningsproblem med Azure AD kan annars observerats på Azure AD Connect-servern.

## <a name="the-communication-pattern-between-azure-ad-connect-and-azure-ad"></a>Kommunikationsmönster för mellan Azure AD Connect och Azure AD
Om du har följt de föregående stegen och fortfarande inte kan ansluta, kan du nu börja titta på loggarna för nätverket. Det här avsnittet är dokumentera arbetsprofilen normalt och lyckad anslutning. Det också en lista med vanliga red herrings som kan ignoreras när du läser loggarna för nätverket.

* Det finns anrop till https://dc.services.visualstudio.com. Du behöver inte ha den här URL: en öppen i proxyn för att installationen ska lyckas, och dessa anrop kan ignoreras.
* Du ser att dns-matchningen visar de faktiska värdarna i DNS-namnet utrymme nsatc.net och andra namnområden inte under microsoftonline.com. Men det finns inte någon webbtjänstbegäranden på de faktiska servernamn och du behöver inte lägga till dessa URL: er till proxyservern.
* Slutpunkter adminwebservice och provisioningapi är identifiering av slutpunkter och används för att hitta den faktiska slutpunkten som ska användas. De här slutpunkterna är olika beroende på region.

### <a name="reference-proxy-logs"></a>Referens för proxy-loggar
Här är en dump från en faktisk proxy-loggen och guidesidan installation från där den togs (dubbla poster till samma slutpunkt har tagits bort). Det här avsnittet kan användas som referens för proxy och nätverket loggarna. De faktiska slutpunkterna kan skilja sig i din miljö (särskilt dessa URL: er i *kursiv*).

**Anslut till Azure AD**

| Tid | URL |
| --- | --- |
| 1/11/2016 8:31 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:31 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:32 |connect://*bba800-anchor*.microsoftonline.com:443 |
| 1/11/2016 8:32 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:33 |connect://provisioningapi.microsoftonline.com:443 |
| 1/11/2016 8:33 |connect://*bwsc02-relay*.microsoftonline.com:443 |

**Konfigurera**

| Tid | URL |
| --- | --- |
| 1/11/2016 8:43 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:43 |connect://*bba800-anchor*.microsoftonline.com:443 |
| 1/11/2016 8:43 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://*bba900-anchor*.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://*bba800-anchor*.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:46 |connect://provisioningapi.microsoftonline.com:443 |
| 1/11/2016 8:46 |connect://*bwsc02-relay*.microsoftonline.com:443 |

**Den inledande synkroniseringen**

| Tid | URL |
| --- | --- |
| 1/11/2016 8:48 |connect://login.windows.net:443 |
| 1/11/2016 8:49 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:49 |connect://*bba900-anchor*.microsoftonline.com:443 |
| 1/11/2016 8:49 |connect://*bba800-anchor*.microsoftonline.com:443 |

## <a name="authentication-errors"></a>Autentiseringsfel
Det här avsnittet beskriver fel som kan returneras från ADAL (autentiseringsbibliotek som används av Azure AD Connect) och PowerShell. Felet förklaras hjälper dig i att förstå din nästa steg.

### <a name="invalid-grant"></a>Ogiltig bevilja
Ogiltigt användarnamn eller lösenord. Mer information finns i [lösenordet går inte att verifiera](#the-password-cannot-be-verified).

### <a name="unknown-user-type"></a>Okänd användartyp
Azure AD-katalogen kan inte hitta eller löst. Kanske du försöker logga in med ett användarnamn i en overifierade domän?

### <a name="user-realm-discovery-failed"></a>Identifiering av startsfär användaren misslyckades
Nätverks- och proxyinställningar konfigurationsproblem. Nätverket kan inte nås. Se [Felsöka anslutningsproblem i installationsguiden](#troubleshoot-connectivity-issues-in-the-installation-wizard).

### <a name="user-password-expired"></a>Användarens lösenord har upphört att gälla
Dina autentiseringsuppgifter har upphört att gälla. Ändra lösenordet.

### <a name="authorization-failure"></a>Auktoriseringen misslyckades
Det gick inte att auktorisera användare att utföra åtgärden i Azure AD.

### <a name="authentication-canceled"></a>Autentisering har avbrutits
Multifaktorautentisering (MFA) utmaningen avbröts.

<div id="connect-msolservice-failed">
<!--
  Empty div just to act as an alias for the "Connect To MS Online Failed" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="connect-to-ms-online-failed"></a>Ansluta till MS Online misslyckades
Autentiseringen lyckades, men Azure AD PowerShell har ett problem med autentisering.

<div id="get-msoluserrole-failed">
<!--
  Empty div just to act as an alias for the "Azure AD Global Admin Role Needed" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="azure-ad-global-admin-role-needed"></a>Rollen som Global administratör i en Azure AD och behövs
Användaren autentiserades har. Men användaren inte har tilldelats rollen som global administratör. Det här är [hur du kan tilldela rollen som global administratör](../users-groups-roles/directory-assign-admin-roles.md) för användaren. 

<div id="privileged-identity-management">
<!--
  Empty div just to act as an alias for the "Privileged Identity Management Enabled" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="privileged-identity-management-enabled"></a>Privileged Identity Management-aktiverade
Autentiseringen lyckades. Privileged identity management har aktiverats och du är för närvarande inte en global administratör. Mer information finns i [Privileged Identity Management](../privileged-identity-management/pim-getting-started.md).

<div id="get-msolcompanyinformation-failed">
<!--
  Empty div just to act as an alias for the "Company Information Unavailable" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="company-information-unavailable"></a>Företagsinformation som är inte tillgänglig
Autentiseringen lyckades. Det gick inte att hämta företagets information från Azure AD.

<div id="get-msoldomain-failed">
<!--
  Empty div just to act as an alias for the "Domain Information Unavailable" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="domain-information-unavailable"></a>Inte tillgänglig domäninformation
Autentiseringen lyckades. Det gick inte att hämta domäninformation från Azure AD.

### <a name="unspecified-authentication-failure"></a>Ospecificerad autentiseringsfel
Visas som ett oväntat fel inträffade i installationsguiden. Kan inträffa om du försöker använda en **Account** snarare än en **school-eller organisationskonto**.

## <a name="troubleshooting-steps-for-previous-releases"></a>Felsökning för tidigare versioner.
Med versioner drogs från och med build-nummer 1.1.105.0 (publicerad februari 2016), inloggningsassistenten tillbaka. Det här avsnittet och konfigurationen som inte längre ska krävs, men sparas som referens.

För den enkel inloggningen i installationsassistenten att fungera, måste winhttp konfigureras. Den här konfigurationen kan göras med [ **netsh**](how-to-connect-install-prerequisites.md#connectivity).  
![netsh](./media/tshoot-connect-connectivity/netsh.png)

### <a name="the-sign-in-assistant-has-not-been-correctly-configured"></a>Inloggningsassistenten har inte konfigurerats korrekt
Det här felet visas när inloggningsassistenten inte går att nå proxyservern eller proxyservern tillåter inte begäran.
![nonetsh](./media/tshoot-connect-connectivity/nonetsh.png)

* Om du ser det här felet kan du titta på proxykonfiguration i [netsh](how-to-connect-install-prerequisites.md#connectivity) och den är korrekt.
  ![netshshow](./media/tshoot-connect-connectivity/netshshow.png)
* Om som verkar vara korrekta, följer du stegen i [Kontrollera proxy-anslutningen](#verify-proxy-connectivity) att se om problemet finns utanför guiden.

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
