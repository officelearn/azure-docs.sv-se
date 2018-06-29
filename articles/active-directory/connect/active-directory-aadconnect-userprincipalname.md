---
title: Azure AD UserPrincipalName population
description: Följande dokument beskriver hur attributet UserPrincipalName är ifylld.
author: billmath
ms.component: hybrid
ms.author: billmath
ms.date: 06/26/2018
ms.topic: article
ms.workload: identity
ms.service: active-Directory
manager: mtillman
ms.openlocfilehash: 6b3fddcdf6ff9c35d5932b9b83da02f60f9e081e
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37064056"
---
# <a name="azure-ad-userprincipalname-population"></a>Azure AD UserPrincipalName population

Den här artikeln beskriver hur attributet UserPrincipalName fylls i Azure Active Directory (AD Azure).
Värdet för attributet UserPrincipalName är Azure AD-användarnamnet för användarkonton.

## <a name="upn-terminology"></a>UPN-terminologi
Följande termer används i den här artikeln:

|Period|Beskrivning|
|-----|-----|
|Första domänen|Standarddomän (onmicrosoft.com) i Azure AD-klient. Till exempel contoso.onmicrosoft.com.|
|Microsoft Online routning Emailadress (MOERA)|Azure AD beräknar MOERA från Azure AD MailNickName attribut och inledande Azure AD-domän som &lt;MailNickName&gt;&#64;&lt;initiala domänen&gt;.|
|Attribut för lokala mailNickName|Ett attribut i Active Directory, vars värde representerar alias för en användare i en Exchange-organisation.|
|Lokal e-postattribut|Ett attribut i Active Directory, värdet som representerar den e-postadressen för en användare|
|Primär SMTP-adress|Den primära e-postadressen för Exchange mottagande objekt. Till exempel SMTP:user\@contoso.com.|
|Alternativt inloggnings-ID|En lokal attribut än UserPrincipalName, t.ex e-attribut som används för inloggning.|

## <a name="what-is-userprincipalname"></a>Vad är UserPrincipalName?
UserPrincipalName är ett attribut som är ett inloggningsnamn för Internet-format för en användare baserat på Internet-standard [RFC 822](http://www.ietf.org/rfc/rfc0822.txt). 

### <a name="upn-format"></a>UPN-format
Ett UPN består av ett UPN-prefix (användarkontonamnet) och UPN-suffix (ett DNS-domännamn). Prefixet är ansluten med suffixet med hjälp av den ”\@” symbol. Till exempel ”någon\@example.com”. Ett UPN måste vara unika bland alla säkerhetsobjekt inom en directory-skog. 

## <a name="upn-in-azure-ad"></a>UPN i Azure AD 
UPN används av Azure AD så att användarna kan logga in.  Det UPN som en användare kan använda beror på om domänen har verifierats.  Om domänen har verifierats och sedan en användare med det suffixet kommer att kunna logga in på Azure AD.  

Attributet är synkroniserad med Azure AD Connect.  Du kan visa de domäner som har verifierats och de som inte under installationen.

   ![Overifierade domäner](./media/active-directory-aadconnect-get-started-express/unverifieddomain.png) 

## <a name="alternate-login-id"></a>Alternativt inloggnings-ID
I vissa miljöer kan slutanvändarna bara vara medveten om deras e-postadress och inte deras UPN.  Användning av e-postadress kan bero på en företagspolicy eller ett lokalt line-of-business tillämpningsprogramberoende.

Alternativt inloggnings-ID kan du konfigurera en inloggning där användare kan logga in med ett attribut än sina UPN-namnet, till exempel e-post.

Om du vill aktivera alternativt inloggnings-ID med Azure AD, krävs inga ytterligare konfigurationer som när du använder Azure AD Connect. Alternativt ID kan konfigureras i guiden. Finns i Azure AD-inloggning konfiguration för användarna under synkroniseringsavsnittet. Under den **användarens huvudnamn** listrutan, Välj attributet för alternativa inloggnings-ID.

![Overifierade domäner](./media/active-directory-aadconnect-userprincipalname/altloginid.png)  

Mer information finns i [konfigurera alternativt inloggnings-ID](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) och [inloggningskonfiguration för Azure AD](active-directory-aadconnect-get-started-custom.md#azure-ad-sign-in-configuration)

## <a name="non-verified-upn-suffix"></a>Icke-verifierad UPN-Suffix
Om suffixet lokalt UserPrincipalName attributet/alternativa inloggnings-ID inte har verifierats med Azure AD-klient, ange värdet för attributet UserPrincipalName i Azure AD till MOERA. Azure AD beräknar MOERA från Azure AD MailNickName attribut och inledande Azure AD-domän som &lt;MailNickName&gt;&#64;&lt;initiala domänen&gt;.

## <a name="verified-upn-suffix"></a>Verifierade UPN-suffix
Om lokala UserPrincipalName attributet/alternativa är inloggnings-ID-suffix verifierats med Azure AD-klient attributvärdet Azure AD UserPrincipalName ska vara detsamma som lokalt UserPrincipalName attributet/alternativa inloggnings-ID värdet.

## <a name="azure-ad-mailnickname-attribute-value-calculation"></a>Azure AD MailNickName attributet beräkningen
Eftersom Azure AD UserPrincipalName attributvärdet kan anges till MOERA, är det viktigt att förstå hur Azure AD MailNickName attributvärde är prefixet som MOERA beräknas.

När ett användarobjekt synkroniseras till en Azure AD-klient för första gången, Azure AD kontrolleras följande i angiven ordning och anger MailNickName attribut-värde till den första befintliga:

- Attribut för lokala mailNickName
- Prefix för primära SMTP-adress
- Prefix för lokala e-postattribut
- Prefix för lokala userPrincipalName attributet/alternativa inloggnings-ID
- Prefix för sekundära smtp-adress

När uppdateringar till ett användarobjekt synkroniseras till Azure AD-klient, Azure AD uppdaterar attributvärdet MailNickName endast om det finns en uppdatering till lokala mailNickName attributvärdet.

>[!IMPORTANT]
>Azure AD beräknar värdet för attributet UserPrincipalName endast om en uppdatering till lokalt UserPrincipalName attributet/alternativ inloggnings-ID värde synkroniseras till Azure AD-klient. 
>
>När Azure AD beräknar attributet UserPrincipalName, uppdateras också MOERA. 

## <a name="upn-scenarios"></a>UPN-scenarier
Här följer några exempelscenarier på hur UPN beräknas baserat på det aktuella scenariot.

### <a name="scenario-1-non-verified-upn-suffix--initial-synchronization"></a>Scenario 1: Icke-verifierad UPN-suffixet – inledande synkronisering

![Scenario1](media/active-directory-aadconnect-userprincipalname/example1.png)

Lokala användarobjektet:
- mailNickName: &lt;inte har angetts&gt;
- proxyAddresses: {SMTP:us1@contoso.com}
- e-post: us2@contoso.com
- userPrincipalName: us3@contoso.com'

Synkroniserade användarobjektet till Azure AD-klient för första gången
- Ange Azure AD MailNickName attributet primära SMTP-adressprefix.
- Ange MOERA till &lt;MailNickName&gt;&#64;&lt;initiala domänen&gt;.
- Ange Azure AD UserPrincipalName attributet MOERA.

Azure AD-klient användarobjektet:
- MailNickName: us1           
- UserPrincipalName: us1@contoso.onmicrosoft.com


### <a name="scenario-2-non-verified-upn-suffix--set-on-premises-mailnickname-attribute"></a>Scenario 2: Icke-verifierad UPN-suffixet – ange lokalt mailNickName attribut

![Scenario2](media/active-directory-aadconnect-userprincipalname/example2.png)

Lokala användarobjektet:
- mailNickName: us4
- proxyAddresses: {SMTP:us1@contoso.com}
- e-post: us2@contoso.com
- userPrincipalName: us3@contoso.com

Synkronisera uppdateringen på lokala mailNickName attribut till Azure AD-klient
- Uppdatera Azure AD MailNickName attributet med lokalt mailNickName attribut.
- Eftersom det finns ingen uppdatering till attributet userPrincipalName lokalt, påverkas inte till attributet UserPrincipalName i Azure AD.

Azure AD-klient användarobjektet:
- MailNickName: us4
- UserPrincipalName: us1@contoso.onmicrosoft.com

### <a name="scenario-3-non-verified-upn-suffix--update-on-premises-userprincipalname-attribute"></a>Scenario 3: Icke-verifierad UPN-suffixet – uppdatera lokalt attributet userPrincipalName

![Scenario3](media/active-directory-aadconnect-userprincipalname/example3.png)

Lokala användarobjektet:
- mailNickName: us4
- proxyAddresses: {SMTP:us1@contoso.com}
- e-post: us2@contoso.com
- userPrincipalName: us5@contoso.com

Synkronisera uppdatering på attributet för lokala userPrincipalName till Azure AD-klient
- Uppdatering på attributet för lokala userPrincipalName utlöser omberäkning av MOERA och Azure AD UserPrincipalName.
- Ange MOERA till &lt;MailNickName&gt;&#64;&lt;initiala domänen&gt;.
- Ange Azure AD UserPrincipalName attributet MOERA.

Azure AD-klient användarobjektet:
- MailNickName: us4
- UserPrincipalName: us4@contoso.onmicrosoft.com

### <a name="scenario-4-non-verified-upn-suffix--update-primary-smtp-address-and-on-premises-mail-attribute"></a>Scenario 4: Icke-verifierad UPN-suffixet – uppdatera primära SMTP-adress och lokala e-attribut

![Scenario4](media/active-directory-aadconnect-userprincipalname/example4.png)

Lokala användarobjektet:
- mailNickName: us4
- proxyAddresses: {SMTP:us6@contoso.com}
- e-post: us7@contoso.com
- userPrincipalName: us5@contoso.com

Synkronisera uppdateringen på lokala e-postattribut och primära SMTP-adress till Azure AD-klient
- Efter den första synkroniseringen för användarobjektet, uppdateras till lokal e-attributet och den primära SMTP-adressen påverkar inte Azure AD-MailNickName eller attributet UserPrincipalName.

Azure AD-klient användarobjektet:
- MailNickName: us4
- UserPrincipalName: us4@contoso.onmicrosoft.com

### <a name="scenario-5-verified-upn-suffix--update-on-premises-userprincipalname-attribute-suffix"></a>Scenario 5: Verifierade UPN-suffixet – uppdatera lokalt userPrincipalName attributet suffix

![Scenario5](media/active-directory-aadconnect-userprincipalname/example5.png)

Lokala användarobjektet:
- mailNickName: us4
- proxyAddresses: {SMTP:us6@contoso.com}
- e-post: us7@contoso.com
- serPrincipalName: us5@verified.contoso.com

Synkronisera uppdatering på attributet för lokala userPrincipalName till Azure AD-klient
- Uppdatera på lokala userPrincipalName attributet utlösare omberäkning av attributet UserPrincipalName i Azure AD.
- Ange Azure AD UserPrincipalName attribut till attributet för lokala userPrincipalName som UPN-suffix verifieras med Azure AD-klient.

Azure AD-klient användarobjektet:
- MailNickName: us4     
- UserPrincipalName: us5@verified.contoso.com

## <a name="next-steps"></a>Nästa steg
- [Integrerar dina lokala kataloger med Azure Active Directory](active-directory-aadconnect.md)
- [Anpassad installation av Azure AD Connect](active-directory-aadconnect-get-started-custom.md)
