---
title: 'Azure AD Connect: Felsöka problem med Azure AD-anslutning | Microsoft-dokument'
description: I artikeln beskrivs felsÃ¶kning av anslutningsproblem med Azure AD Connect.
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
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 72dbb404d1b4d3618909e0233f332d2f98b51516
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80049722"
---
# <a name="troubleshoot-azure-ad-connectivity"></a>Felsöka Azure AD-anslutning
I den här artikeln beskrivs hur anslutningen mellan Azure AD Connect och Azure AD fungerar och hur du felsöker anslutningsproblem. Dessa problem är mest sannolikt att ses i en miljö med en proxyserver.

## <a name="troubleshoot-connectivity-issues-in-the-installation-wizard"></a>Felsöka anslutningsproblem i installationsguiden
Azure AD Connect använder modern autentisering (med ADAL-biblioteket) för autentisering. Installationsguiden och själva synkroniseringsmotorn kräver att machine.config konfigureras korrekt eftersom dessa två är .NET-program.

I den här artikeln visar vi hur Fabrikam ansluter till Azure AD via dess proxy. Proxyservern heter fabrikamproxy och använder port 8080.

Först måste vi se till att [**machine.config**](how-to-connect-install-prerequisites.md#connectivity) är korrekt konfigurerad.  
![maskinkonfig](./media/tshoot-connect-connectivity/machineconfig.png)

> [!NOTE]
> I vissa bloggar som inte kommer från Microsoft dokumenteras att ändringar ska göras i stället för miiserver.exe.config. Den här filen skrivs dock över på varje uppgradering så även om den fungerar under den första installationen slutar systemet att fungera vid första uppgraderingen. Därför är rekommendationen att uppdatera machine.config istället.
>
>

Proxyservern måste också ha de webbadresser som krävs öppna. Den officiella listan är dokumenterad i [Office 365-url:er och IP-adressintervall](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

Av dessa webbadresser är följande tabell det absolut minsta minimum för att kunna ansluta till Azure AD alls. Den här listan innehåller inga valfria funktioner, till exempel återställning av lösenord eller Azure AD Connect Health. Det dokumenteras här för att hjälpa till vid felsökning för den första konfigurationen.

| URL | Port | Beskrivning |
| --- | --- | --- |
| mscrl.microsoft.com |HTTP/80 |Används för att hämta CRL-listor. |
| \*.verisign.com |HTTP/80 |Används för att hämta CRL-listor. |
| \*.entrust.net |HTTP/80 |Används för att hämta CRL-listor för MFA. |
| \*.windows.net |HTTPS/443 |Används för att logga in på Azure AD. |
| secure.aadcdn.microsoftonline-p.com |HTTPS/443 |Används för MFA. |
| \*.microsoftonline.com |HTTPS/443 |Används för att konfigurera din Azure AD-katalog och importera/exportera data. |

## <a name="errors-in-the-wizard"></a>Fel i guiden
Installationsguiden använder två olika säkerhetskontexter. På sidan **Anslut till Azure AD**använder den inloggade användaren. På sidan **Konfigurera**ändras den till [kontot som kör tjänsten för synkroniseringsmotorn](reference-connect-accounts-permissions.md#adsync-service-account). Om det finns ett problem visas det troligen redan på sidan **Anslut till Azure AD** i guiden eftersom proxykonfigurationen är global.

Följande problem är de vanligaste felen som du stöter på i installationsguiden.

### <a name="the-installation-wizard-has-not-been-correctly-configured"></a>Installationsguiden har inte konfigurerats korrekt
Det här felet visas när själva guiden inte kan nå proxyn.  
![nomachineconfig](./media/tshoot-connect-connectivity/nomachineconfig.png)

* Om det här felet visas kontrollerar du att [machine.config](how-to-connect-install-prerequisites.md#connectivity) har konfigurerats korrekt.
* Om det ser korrekt ut följer du stegen i [Verifiera proxyanslutning](#verify-proxy-connectivity) för att se om problemet finns även utanför guiden.

### <a name="a-microsoft-account-is-used"></a>Ett Microsoft-konto används
Om du använder ett **Microsoft-konto** i stället för ett **skol- eller organisationskonto** visas ett allmänt fel.  
![Ett Microsoft-konto används](./media/tshoot-connect-connectivity/unknownerror.png)

### <a name="the-mfa-endpoint-cannot-be-reached"></a>MFA-slutpunkten kan inte nås
Det här felet visas **https://secure.aadcdn.microsoftonline-p.com** om slutpunkten inte kan nås och din globala administratör har MFA aktiverat.  
![nomachineconfig](./media/tshoot-connect-connectivity/nomicrosoftonlinep.png)

* Om det här felet visas kontrollerar du att **slutpunkten secure.aadcdn.microsoftonline-p.com** har lagts till i proxyn.

### <a name="the-password-cannot-be-verified"></a>Det går inte att verifiera lösenordet
Om installationsguiden lyckas ansluta till Azure AD, men själva lösenordet inte kan verifieras visas det här felet:  
![Felaktigt lösenord.](./media/tshoot-connect-connectivity/badpassword.png)

* Är lösenordet ett tillfälligt lösenord och måste ändras? Är det verkligen rätt lösenord? Försök att logga `https://login.microsoftonline.com` in på (på en annan dator än Azure AD Connect-servern) och verifiera att kontot kan kan kan tas ut.

### <a name="verify-proxy-connectivity"></a>Verifiera proxyanslutning
Om du vill kontrollera om Azure AD Connect-servern har faktisk anslutning till Proxy och Internet använder du vissa PowerShell för att se om proxyn tillåter webbbegäranden eller inte. I en PowerShell-prompt kör du `Invoke-WebRequest -Uri https://adminwebservice.microsoftonline.com/ProvisioningService.svc`. (Tekniskt sett är `https://login.microsoftonline.com` det första samtalet till och denna URI fungerar också, men den andra URI är snabbare att svara.)

PowerShell använder konfigurationen i machine.config för att kontakta proxyn. Inställningarna i winhttp/netsh bör inte påverka dessa cmdlets.

Om proxyn är korrekt konfigurerad bör du ![få en lyckad status: proxy200](./media/tshoot-connect-connectivity/invokewebrequest200.png)

Om du **inte får kontakt med fjärrservern**försöker PowerShell ringa ett direktsamtal utan att använda proxyn eller DNS är inte korrekt konfigurerat. Kontrollera att **filen machine.config** är korrekt konfigurerad.
![det går inte att ansluta](./media/tshoot-connect-connectivity/invokewebrequestunable.png)

Om proxyn inte är korrekt konfigurerad visas ![ett felmeddelande: proxy200](./media/tshoot-connect-connectivity/invokewebrequest403.png)
![proxy407](./media/tshoot-connect-connectivity/invokewebrequest407.png)

| Fel | Feltext | Kommentar |
| --- | --- | --- |
| 403 |Förbjudet |Proxyn har inte öppnats för den begärda webbadressen. Gå tillbaka till proxykonfigurationen och kontrollera att [webbadresserna](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) har öppnats. |
| 407 |Proxyautentisering krävs |Proxyservern krävde en inloggning och ingen angavs. Om proxyservern kräver autentisering måste du se till att den här inställningen har konfigurerats i machine.config. Kontrollera också att du använder domänkonton för användaren som kör guiden och för tjänstkontot. |

### <a name="proxy-idle-timeout-setting"></a>Inställningar för timeout för proxytidsutlös
När Azure AD Connect skickar en exportbegäran till Azure AD kan Det ta upp till 5 minuter innan Azure AD behandlar begäran innan du skapar ett svar. Detta kan inträffa särskilt om det finns ett antal gruppobjekt med stora gruppmedlemskap som ingår i samma exportbegäran. Kontrollera att timeouten för proxy-inaktiv är konfigurerad för att vara större än 5 minuter. Annars kan återkommande anslutningsproblem med Azure AD observeras på Azure AD Connect-servern.

## <a name="the-communication-pattern-between-azure-ad-connect-and-azure-ad"></a>Kommunikationsmönstret mellan Azure AD Connect och Azure AD
Om du har följt alla dessa föregående steg och fortfarande inte kan ansluta kan du nu börja titta på nätverksloggar. Det här avsnittet dokumenterar ett normalt och framgångsrikt anslutningsmönster. Det är också lista gemensamma röda sillar som kan ignoreras när du läser nätverksloggar.

* Det finns `https://dc.services.visualstudio.com`samtal till . Det krävs inte att den här webbadressen är öppen i proxyn för att installationen ska lyckas och dessa anrop kan ignoreras.
* Du ser att dns-upplösningen visar de faktiska värdarna som ska finnas i DNS-namnutrymmet nsatc.net och andra namnområden som inte finns under microsoftonline.com. Det finns dock inga webbtjänstbegäranden på de faktiska servernamnen och du behöver inte lägga till dessa url:er i proxyn.
* Slutpunkterna adminwebservice och provisioningapi är identifieringslutpunkter och används för att hitta den faktiska slutpunkten som ska användas. Dessa slutpunkter är olika beroende på din region.

### <a name="reference-proxy-logs"></a>Referensproxy loggar
Här är en dump från en faktisk proxylogg och installationsguiden sidan från där den togs (dubblettposter till samma slutpunkt har tagits bort). Det här avsnittet kan användas som referens för dina egna proxy- och nätverksloggar. De faktiska slutpunkterna kan vara olika i din miljö (särskilt webbadresserna i *kursiv stil).*

**Anslut till Azure AD**

| Tid | URL |
| --- | --- |
| 1/11/2016 8:31 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:31 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:32 |connect://*bba800-ankare*.microsoftonline.com:443 |
| 1/11/2016 8:32 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:33 |connect://provisioningapi.microsoftonline.com:443 |
| 1/11/2016 8:33 |connect://*bwsc02-reläet*.microsoftonline.com:443 |

**Konfigurera**

| Tid | URL |
| --- | --- |
| 1/11/2016 8:43 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:43 |connect://*bba800-ankare*.microsoftonline.com:443 |
| 1/11/2016 8:43 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://*bba900-ankare*.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://*bba800-ankare*.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:46 |connect://provisioningapi.microsoftonline.com:443 |
| 1/11/2016 8:46 |connect://*bwsc02-reläet*.microsoftonline.com:443 |

**Första synkronisering**

| Tid | URL |
| --- | --- |
| 1/11/2016 8:48 |connect://login.windows.net:443 |
| 1/11/2016 8:49 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:49 |connect://*bba900-ankare*.microsoftonline.com:443 |
| 1/11/2016 8:49 |connect://*bba800-ankare*.microsoftonline.com:443 |

## <a name="authentication-errors"></a>Autentiseringsfel
Det här avsnittet innehåller fel som kan returneras från ADAL (autentiseringsbiblioteket som används av Azure AD Connect) och PowerShell. Felet förklaras bör hjälpa dig att förstå dina nästa steg.

### <a name="invalid-grant"></a>Ogiltigt bidrag
Ogiltigt användarnamn eller lösenord. Mer information finns i [Lösenordet kan inte verifieras](#the-password-cannot-be-verified).

### <a name="unknown-user-type"></a>Okänd användartyp
Det går inte att hitta eller matcha din Azure AD-katalog. Kanske du försöker logga in med ett användarnamn i en overifierad domän?

### <a name="user-realm-discovery-failed"></a>Identifiering av användar sfär misslyckades
Problem med nätverks- eller proxykonfiguration. Det går inte att nå nätverket. Se [Felsöka anslutningsproblem i installationsguiden](#troubleshoot-connectivity-issues-in-the-installation-wizard).

### <a name="user-password-expired"></a>Användarlösenordet har upphört att gälla
Dina autentiseringsuppgifter har upphört att gälla. Ändra ditt lösenord.

### <a name="authorization-failure"></a>Auktoriseringsfel
Det gick inte att auktorisera användaren att utföra åtgärder i Azure AD.

### <a name="authentication-canceled"></a>Autentiseringen avbröts
Utmaningen med multifaktorautentisering avbröts.

<div id="connect-msolservice-failed">
<!--
  Empty div just to act as an alias for the "Connect To MS Online Failed" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="connect-to-ms-online-failed"></a>Det gick inte att ansluta till MS Online
Autentiseringen lyckades, men Azure AD PowerShell har ett autentiseringsproblem.

<div id="get-msoluserrole-failed">
<!--
  Empty div just to act as an alias for the "Azure AD Global Admin Role Needed" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="azure-ad-global-admin-role-needed"></a>Azure AD Global Admin-roll behövs
Användaren autentiserades. Användaren har dock inte tilldelats global administratörsroll. Så här kan du tilldela användaren [global administratörsroll.](../users-groups-roles/directory-assign-admin-roles.md) 

<div id="privileged-identity-management">
<!--
  Empty div just to act as an alias for the "Privileged Identity Management Enabled" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="privileged-identity-management-enabled"></a>Privilegierad identitetshantering aktiverad
Autentiseringen lyckades. Privilegierad identitetshantering har aktiverats och du är för närvarande inte global administratör. Mer information finns i [Privilegierad identitetshantering](../privileged-identity-management/pim-getting-started.md).

<div id="get-msolcompanyinformation-failed">
<!--
  Empty div just to act as an alias for the "Company Information Unavailable" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="company-information-unavailable"></a>Företagsinformation är inte tillgänglig
Autentiseringen lyckades. Det gick inte att hämta företagsinformation från Azure AD.

<div id="get-msoldomain-failed">
<!--
  Empty div just to act as an alias for the "Domain Information Unavailable" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="domain-information-unavailable"></a>Domäninformation är inte tillgänglig
Autentiseringen lyckades. Det gick inte att hämta domäninformation från Azure AD.

### <a name="unspecified-authentication-failure"></a>Ospecificerat autentiseringsfel
Visas som oväntat fel i installationsguiden. Kan inträffa om du försöker använda ett **Microsoft-konto** i stället för ett **skol- eller organisationskonto**.

## <a name="troubleshooting-steps-for-previous-releases"></a>Felsökningssteg för tidigare versioner.
Med utgåvor som börjar med byggnummer 1.1.105.0 (släpptes i februari 2016) drogs inloggningsassistenten tillbaka. Det här avsnittet och konfigurationen bör inte längre krävas, men bevaras som referens.

För att en inloggningsassistenten ska fungera måste winhttp konfigureras. Denna konfiguration kan göras med [**netsh**](how-to-connect-install-prerequisites.md#connectivity).  
![Netsh](./media/tshoot-connect-connectivity/netsh.png)

### <a name="the-sign-in-assistant-has-not-been-correctly-configured"></a>Inloggningsassistenten har inte konfigurerats korrekt
Det här felet visas när inloggningsassistenten inte kan nå proxyn eller om proxyn inte tillåter begäran.
![nonetsh](./media/tshoot-connect-connectivity/nonetsh.png)

* Om det här felet visas kan du titta på proxykonfigurationen i [netsh](how-to-connect-install-prerequisites.md#connectivity) och kontrollera att det är korrekt.
  ![netshshow (på nytt)](./media/tshoot-connect-connectivity/netshshow.png)
* Om det ser korrekt ut följer du stegen i [Verifiera proxyanslutning](#verify-proxy-connectivity) för att se om problemet finns även utanför guiden.

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
