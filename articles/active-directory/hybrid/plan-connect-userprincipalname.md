---
title: Ifyllnad av UserPrincipalName för Azure AD
description: Följande dokument beskriver så här fylls UserPrincipalName-attribut.
author: billmath
ms.subservice: hybrid
ms.author: billmath
ms.date: 06/26/2018
ms.topic: conceptual
ms.workload: identity
ms.service: active-Directory
manager: daveba
ms.openlocfilehash: 4270aa6feed7574c2e74ca885c6e150eda3b0fad
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55494536"
---
# <a name="azure-ad-userprincipalname-population"></a>Ifyllnad av UserPrincipalName för Azure AD

Den här artikeln beskrivs hur UserPrincipalName-attribut fylls i Azure Active Directory (AD Azure).
Värdet för attributet UserPrincipalName är Azure AD-användarnamn för användarkonton.

## <a name="upn-terminology"></a>UPN-terminologi
De följande termer som används i den här artikeln:

|Period|Beskrivning|
|-----|-----|
|Initial domän|Standarddomän (onmicrosoft.com) i Azure AD-klient. Exempel: contoso.onmicrosoft.com.|
|Microsoft Online-routning epostadress (MOERA)|Azure AD beräknar MOERA från Azure AD MailNickName-attributet och första Azure AD-domän som &lt;MailNickName&gt;&#64;&lt;initial domän&gt;.|
|Den lokala mailNickName-attributet|Ett attribut i Active Directory, där värdet representerar alias för en användare i en Exchange-organisation.|
|Lokala e-postattribut|Ett attribut i Active Directory, vars värde representerar den e-postadressen för en användare|
|Primär SMTP-adress|Primär e-postadress för ett mottagarens Exchange-objekt. Till exempel SMTP:user\@contoso.com.|
|Alternativa inloggnings-ID|En lokal attribut än UserPrincipalName, till exempel e-attribut som används för att logga in.|

## <a name="what-is-userprincipalname"></a>Vad är UserPrincipalName?
UserPrincipalName är ett attribut som är ett inloggningsnamn för Internet-format för en användare baserat på Internet-standard [RFC 822](https://www.ietf.org/rfc/rfc0822.txt). 

### <a name="upn-format"></a>UPN-format
Ett UPN består av ett prefix för UPN (användarens kontonamn) och ett UPN-suffix (ett DNS-domännamn). Prefixet är ansluten med suffixet med hjälp av den ”\@” symbol. Till exempel ”någon\@example.com”. Ett UPN måste vara unikt bland alla säkerhetsobjekt inom en directory-skog. 

## <a name="upn-in-azure-ad"></a>UPN i Azure AD 
UPN-namnet används av Azure AD så att användarna kan logga in.  Det UPN som en användare kan använda beror på om domänen har verifierats.  Om domänen har verifierats och sedan en användare med det suffixet kommer att kunna logga in på Azure AD.  

Attributet är synkroniserad med Azure AD Connect.  Under installationen kan du visa de domäner som har verifierats och de som inte har.

   ![Overifierade domäner](./media/plan-connect-userprincipalname/unverifieddomain.png) 

## <a name="alternate-login-id"></a>Alternativa inloggnings-ID
I vissa miljöer kan slutanvändarna bara känna till sin e-postadress och inte sina UPN.  Användning av e-postadress kan bero på en företagspolicy eller ett lokalt line-of-business programberoenden.

Alternativa inloggnings-ID kan du konfigurera en inloggning där användare kan logga in med ett attribut än sina UPN, till exempel e-post.

Om du vill aktivera alternativa inloggnings-ID med Azure AD, krävs ingen ytterligare konfigurationer steg när du använder Azure AD Connect. Alternativt ID kan konfigureras i guiden. Se Azure AD-inloggningen konfiguration för användarna under synkroniseringsavsnittet. Under den **User Principal Name** listrutan, väljer du attributet för alternativa inloggnings-ID.

![Overifierade domäner](./media/plan-connect-userprincipalname/altloginid.png)  

Mer information finns i [konfigurera alternativt inloggnings-ID](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) och [inloggningskonfiguration för Azure AD](how-to-connect-install-custom.md#azure-ad-sign-in-configuration)

## <a name="non-verified-upn-suffix"></a>Icke-kontrollerad UPN-Suffix
Om det lokala UserPrincipalName attribut/alternativ inloggning ID suffixet inte verifieras med Azure AD-klient, anges Azure AD UserPrincipalName attributvärdet till MOERA. Azure AD beräknar MOERA från Azure AD MailNickName-attributet och första Azure AD-domän som &lt;MailNickName&gt;&#64;&lt;initial domän&gt;.

## <a name="verified-upn-suffix"></a>Verifierade UPN-suffix
Om lokalt UserPrincipalName-attribut/alternativa verifieras inloggnings-ID: T suffix med Azure AD-klient och sedan Azure AD UserPrincipalName attribut-värde ska vara samma som lokalt UserPrincipalName-attribut/alternativ inloggning ID-värde.

## <a name="azure-ad-mailnickname-attribute-value-calculation"></a>Azure AD-MailNickName-attributet värdeberäkning
Eftersom Azure AD UserPrincipalName-attributvärdet kan anges till MOERA, är det viktigt att förstå hur Azure AD MailNickName attribut-värde som är prefixet MOERA, beräknas.

När ett användarobjekt synkroniseras till en Azure AD-klient för första gången, kontrolleras följande i angiven ordning Azure AD och anger MailNickName-attributvärdet till den första befintliga:

- Den lokala mailNickName-attributet
- Prefixet för primär SMTP-adress
- Prefixet för lokala e-postattribut
- Prefixet för lokalt userPrincipalName-attribut/alternativa inloggnings-ID
- Prefixet för sekundära smtp-adress

När uppdateringarna till ett användarobjekt synkroniseras till Azure AD-klient, Azure AD uppdaterar värdet för MailNickName-attributet endast om det finns en uppdatering av det lokala mailNickName-attributvärdet.

>[!IMPORTANT]
>Azure AD beräknar om värdet för attributet UserPrincipalName endast om en uppdatering av lokalt UserPrincipalName-attribut/alternativ inloggning ID-värde som synkroniseras till Azure AD-klienten. 
>
>När Azure AD beräknar om attributet UserPrincipalName, uppdateras också MOERA. 

## <a name="upn-scenarios"></a>UPN-scenarier
Här följer några exempelscenarier på hur UPN beräknas baserat på det aktuella scenariot.

### <a name="scenario-1-non-verified-upn-suffix--initial-synchronization"></a>Scenario 1: Icke-kontrollerat UPN-suffixet – den första synkroniseringen

![Scenario1](./media/plan-connect-userprincipalname/example1.png)

Lokala användarobjektet:
- mailNickName: &lt;inte har angetts&gt;
- proxyAddresses: {SMTP:us1@contoso.com}
- e-post: us2@contoso.com
- userPrincipalName : us3@contoso.com`

Synkroniseras användarobjektet med Azure AD-klient för första gången
- Ange Azure AD MailNickName-attributet till primära SMTP-adressprefix.
- Inställd MOERA &lt;MailNickName&gt;&#64;&lt;initial domän&gt;.
- Ange Azure AD UserPrincipalName-attribut till MOERA.

Användarobjektet med Azure AD-klient:
- MailNickName: us1           
- UserPrincipalName : us1@contoso.onmicrosoft.com


### <a name="scenario-2-non-verified-upn-suffix--set-on-premises-mailnickname-attribute"></a>Scenario 2: Icke-kontrollerat UPN-suffixet – angett den lokala mailNickName-attributet

![Scenario2](./media/plan-connect-userprincipalname/example2.png)

Lokala användarobjektet:
- mailNickName: us4
- proxyAddresses: {SMTP:us1@contoso.com}
- e-post: us2@contoso.com
- userPrincipalName : us3@contoso.com

Synkronisera uppdateringen på den lokala mailNickName-attributet till Azure AD-klient
- Uppdatera Azure AD MailNickName-attributet med lokala mailNickName-attributet.
- Eftersom det finns ingen uppdatering till lokalt userPrincipalName-attribut, finns det ingen förändring av Azure AD UserPrincipalName-attribut.

Användarobjektet med Azure AD-klient:
- mailNickName: us4
- UserPrincipalName : us1@contoso.onmicrosoft.com

### <a name="scenario-3-non-verified-upn-suffix--update-on-premises-userprincipalname-attribute"></a>Scenario 3: Icke-kontrollerat UPN-suffixet – uppdatera lokala userPrincipalName-attribut

![Scenario3](./media/plan-connect-userprincipalname/example3.png)

Lokala användarobjektet:
- mailNickName: us4
- proxyAddresses: {SMTP:us1@contoso.com}
- e-post: us2@contoso.com
- userPrincipalName : us5@contoso.com

Synkronisera uppdateringen på lokalt userPrincipalName-attribut till Azure AD-klient
- Uppdatering av lokalt userPrincipalName-attribut utlöser omberäkning av MOERA och Azure AD UserPrincipalName-attribut.
- Inställd MOERA &lt;MailNickName&gt;&#64;&lt;initial domän&gt;.
- Ange Azure AD UserPrincipalName-attribut till MOERA.

Användarobjektet med Azure AD-klient:
- mailNickName: us4
- UserPrincipalName : us4@contoso.onmicrosoft.com

### <a name="scenario-4-non-verified-upn-suffix--update-primary-smtp-address-and-on-premises-mail-attribute"></a>Scenario 4: Icke-kontrollerat UPN-suffixet – uppdatering primär SMTP-adress och en lokal e-attribut

![Scenario4](./media/plan-connect-userprincipalname/example4.png)

Lokala användarobjektet:
- mailNickName: us4
- proxyAddresses: {SMTP:us6@contoso.com}
- e-post: us7@contoso.com
- userPrincipalName : us5@contoso.com

Synkronisera uppdateringen på en lokal e-postattribut och primär SMTP-adress till Azure AD-klient
- När den första synkroniseringen för användarobjektet, uppdaterar till lokalt e-attributet och den primära SMTP-adressen påverkar inte Azure AD-MailNickName eller UserPrincipalName-attribut.

Användarobjektet med Azure AD-klient:
- mailNickName: us4
- UserPrincipalName : us4@contoso.onmicrosoft.com

### <a name="scenario-5-verified-upn-suffix--update-on-premises-userprincipalname-attribute-suffix"></a>Scenario 5: Verifierade UPN-suffixet – uppdatera lokala suffix för userPrincipalName-attribut

![Scenario5](./media/plan-connect-userprincipalname/example5.png)

Lokala användarobjektet:
- mailNickName: us4
- proxyAddresses: {SMTP:us6@contoso.com}
- e-post: us7@contoso.com
- userPrincipalName : us5@verified.contoso.com

Synkronisera uppdateringen på lokalt userPrincipalName-attribut till Azure AD-klient
- Uppdatera på lokalt userPrincipalName-attribut utlösare omberäkning av Azure AD UserPrincipalName-attribut.
- Ange Azure AD UserPrincipalName-attribut till lokalt userPrincipalName-attribut som UPN-suffixet har verifierats med Azure AD-klient.

Användarobjektet med Azure AD-klient:
- mailNickName: us4     
- UserPrincipalName : us5@verified.contoso.com

## <a name="next-steps"></a>Nästa steg
- [Integrerar dina lokala kataloger med Azure Active Directory](whatis-hybrid-identity.md)
- [Anpassad installation av Azure AD Connect](how-to-connect-install-custom.md)
