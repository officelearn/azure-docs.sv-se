---
title: Ifyllnad av UserPrincipalName för Azure AD
description: I följande dokument beskrivs hur attributet UserPrincipalName fylls i.
author: billmath
ms.subservice: hybrid
ms.author: billmath
ms.date: 06/26/2018
ms.topic: conceptual
ms.workload: identity
ms.service: active-directory
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: c198b329f07c5c7459f25165b2dc0a3bfa032276
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60382133"
---
# <a name="azure-ad-userprincipalname-population"></a>Ifyllnad av UserPrincipalName för Azure AD

I den här artikeln beskrivs hur attributet UserPrincipalName fylls i i Azure Active Directory (Azure AD).
Attributevärdet UserPrincipalName är Azure AD-användarnamnet för användarkontona.

## <a name="upn-terminology"></a>UPN-terminologi
Följande terminologi används i den här artikeln:

|Period|Beskrivning|
|-----|-----|
|Inledande domän|Standarddomänen (onmicrosoft.com) i Azure AD-klienten. Till exempel contoso.onmicrosoft.com.|
|Microsoft Online-adress för e-postroutning (MOERA)|Azure AD beräknar MOERA från Azure AD AdNickName-attributet&gt; och Azure &lt;AD-startdomänen som &lt;MailNickName&#64;ursprungliga domänen&gt;.|
|Lokalt mailNickName-attribut|Ett attribut i Active Directory, vars värde representerar alias för en användare i en Exchange-organisation.|
|Lokalt e-postattribut|Ett attribut i Active Directory, vars värde representerar en användares e-postadress|
|Primär SMTP-adress|Den primära e-postadressen för ett Exchange-mottagarobjekt. SmTP:user\@contoso.com till exempel.|
|Alternativt inloggnings-ID|Ett lokalt attribut än UserPrincipalName, till exempel e-postattribut, som används för inloggning.|

## <a name="what-is-userprincipalname"></a>Vad är UserPrincipalName?
UserPrincipalName är ett attribut som är ett inloggningsnamn i Internet-stil för en användare baserat på Internet-standarden [RFC 822](https://www.ietf.org/rfc/rfc0822.txt). 

### <a name="upn-format"></a>UPN-format
Ett UPN består av ett UPN-prefix (användarkontonamnet) och ett UPN-suffix (ett DNS-domännamn). Prefixet sammanfogas med suffixet\@med symbolen " " . Till exempel "någon\@example.com". Ett UPN-nummer måste vara unikt bland alla huvudobjekt i en katalogskog. 

## <a name="upn-in-azure-ad"></a>UPN i Azure AD 
UPN används av Azure AD för att tillåta användare att logga in.  Det UPN som en användare kan använda beror på om domänen har verifierats eller inte.  Om domänen har verifierats tillåts en användare med det suffixet att logga in på Azure AD.  

Attributet synkroniseras av Azure AD Connect.  Under installationen kan du visa de domäner som har verifierats och de som inte har.

   ![Overifierade domäner](./media/plan-connect-userprincipalname/unverifieddomain.png) 

## <a name="alternate-login-id"></a>Alternativt inloggnings-ID
I vissa miljöer kanske slutanvändarna bara är medvetna om sin e-postadress och inte sin UPN.  Användningen av e-postadress kan bero på en företagspolicy eller ett lokalt affärsprogramberoende.

Med alternativt inloggnings-ID kan du konfigurera en inloggningsupplevelse där användare kan logga in med ett annat attribut än sitt UPN, till exempel e-post.

För att aktivera alternativt inloggnings-ID med Azure AD behövs inga ytterligare konfigurationssteg när du använder Azure AD Connect. Alternativ-ID kan konfigureras i guiden. Se Azure AD-inloggningskonfiguration för dina användare under avsnittet Synkronisera. Under listrutan **Användarnamn** väljer du attributet för Alternativt inloggnings-ID.

![Overifierade domäner](./media/plan-connect-userprincipalname/altloginid.png)  

Mer information finns i [Konfigurera alternativt inloggnings-ID](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) och [Azure AD-inloggningskonfiguration](how-to-connect-install-custom.md#azure-ad-sign-in-configuration)

## <a name="non-verified-upn-suffix"></a>Icke-verifierat UPN-suffix
Om det lokala UserPrincipalName-attributet/alternativa inloggnings-ID-suffixet inte verifieras med Azure AD-klientorganisation, anges attributet Azure AD UserPrincipalName till MOERA. Azure AD beräknar MOERA från Attributet Azure AD MailNickName och Azure AD-startdomänen som &lt;MailNickName&gt;&#64;&lt;ursprungliga domänen&gt;.

## <a name="verified-upn-suffix"></a>Verifierat UPN-suffix
Om det lokala UserPrincipalName-attributet/alternativa inloggnings-ID-suffixet verifieras med Azure AD-klienten, kommer attributet Azure AD UserPrincipalName att vara detsamma som det lokala UserPrincipalName-attributet/alternativt inloggnings-ID-värdet.

## <a name="azure-ad-mailnickname-attribute-value-calculation"></a>Azure AD MailNickName attributvärde beräkning
Eftersom attributet Azure AD UserPrincipalName kan ställas in på MOERA är det viktigt att förstå hur attributet Azure AD MailNickName, som är MOERA-prefixet, beräknas.

När ett användarobjekt synkroniseras med en Azure AD-klient för första gången kontrollerar Azure AD följande objekt i den angivna ordningen och anger attributevärdet MailNickName till den första befintliga:

- Lokalt mailNickName-attribut
- Prefix för primär SMTP-adress
- Prefix för lokalt e-postattribut
- Prefix för lokalt userPrincipalName-attribut/Alternativt inloggnings-ID
- Prefix för sekundär smtp-adress

När uppdateringarna till ett användarobjekt synkroniseras med Azure AD-klienten uppdaterar Azure AD endast attributevärdet MailNickName om det finns en uppdatering av det lokala mailNickName-attributvärdet.

>[!IMPORTANT]
>Azure AD beräknar om attributet UserPrincipalName endast om en uppdatering av det lokala UserPrincipalName-attributet/alternativt inloggnings-ID synkroniseras med Azure AD-klienten. 
>
>När Azure AD beräknar om attributet UserPrincipalName beräknas även MOERA om. 

## <a name="upn-scenarios"></a>UPN-scenarier
Följande är exempel på scenarier för hur UPN beräknas baserat på det givna scenariot.

### <a name="scenario-1-non-verified-upn-suffix--initial-synchronization"></a>Scenario 1: Icke-verifierat UPN-suffix – inledande synkronisering

![Scenario1](./media/plan-connect-userprincipalname/example1.png)

Lokalt användarobjekt:
- mailNickName &lt;: inte inställt&gt;
- proxyAdresser :SMTP:us1@contoso.com{ }
- Mail:us2@contoso.com
- userPrincipalName us3@contoso.com: '

Synkroniserade användarobjektet med Azure AD-klient för första gången
- Ange Azure AD MailNickName-attributet till primär SMTP-adressprefix.
- Ange MOERA &lt;till&gt; MailNickName &lt;&#64;&gt;ursprungliga domänen .
- Ange attributet Azure AD UserPrincipalName till MOERA.

Användarobjekt för Azure AD-klient:
- MailNickName : us1           
- UserPrincipalName :us1@contoso.onmicrosoft.com


### <a name="scenario-2-non-verified-upn-suffix--set-on-premises-mailnickname-attribute"></a>Scenario 2: Icke-verifierat UPN-suffix – ange lokalt mailNickName-attribut

![Scenario2](./media/plan-connect-userprincipalname/example2.png)

Lokalt användarobjekt:
- mailNickName : us4
- proxyAdresser :SMTP:us1@contoso.com{ }
- Mail:us2@contoso.com
- userPrincipalName :us3@contoso.com

Synkronisera uppdatering på lokalt mailNickName-attribut till Azure AD-klient
- Uppdatera Azure AD MailNickName-attributet med lokalt mailNickName-attribut.
- Eftersom det inte finns någon uppdatering av attributet lokalt userPrincipalName, görs ingen ändring av attributet Azure AD UserPrincipalName.

Användarobjekt för Azure AD-klient:
- MailNickName : us4
- UserPrincipalName :us1@contoso.onmicrosoft.com

### <a name="scenario-3-non-verified-upn-suffix--update-on-premises-userprincipalname-attribute"></a>Scenario 3: Icke-verifierat UPN-suffix – uppdatera lokalt userPrincipalName-attribut

![Scenario3](./media/plan-connect-userprincipalname/example3.png)

Lokalt användarobjekt:
- mailNickName : us4
- proxyAdresser :SMTP:us1@contoso.com{ }
- Mail:us2@contoso.com
- userPrincipalName :us5@contoso.com

Synkronisera uppdatering på lokalt userPrincipalName-attribut till Azure AD-klient
- Uppdatera på lokala userPrincipalName-attribut utlöser omberäkning av ATTRIBUTEt MOERA och Azure AD UserPrincipalName.
- Ange MOERA &lt;till&gt; MailNickName &lt;&#64;&gt;ursprungliga domänen .
- Ange attributet Azure AD UserPrincipalName till MOERA.

Användarobjekt för Azure AD-klient:
- MailNickName : us4
- UserPrincipalName :us4@contoso.onmicrosoft.com

### <a name="scenario-4-non-verified-upn-suffix--update-primary-smtp-address-and-on-premises-mail-attribute"></a>Scenario 4: Icke-verifierat UPN-suffix – uppdatera primär SMTP-adress och lokalt e-postattribut

![Scenario4](./media/plan-connect-userprincipalname/example4.png)

Lokalt användarobjekt:
- mailNickName : us4
- proxyAdresser :SMTP:us6@contoso.com{ }
- Mail:us7@contoso.com
- userPrincipalName :us5@contoso.com

Synkronisera uppdatering på lokalt e-postattribut och primär SMTP-adress till Azure AD-klient
- Efter den första synkroniseringen av användarobjektet påverkar uppdateringar av det lokala e-postattributet och den primära SMTP-adressen inte Azure AD MailNickName eller Attributet UserPrincipalName.

Användarobjekt för Azure AD-klient:
- MailNickName : us4
- UserPrincipalName :us4@contoso.onmicrosoft.com

### <a name="scenario-5-verified-upn-suffix--update-on-premises-userprincipalname-attribute-suffix"></a>Scenario 5: Verifierat UPN-suffix – uppdatera lokalt attributsuffix för userPrincipalName

![Scenario5](./media/plan-connect-userprincipalname/example5.png)

Lokalt användarobjekt:
- mailNickName : us4
- proxyAdresser :SMTP:us6@contoso.com{ }
- Mail:us7@contoso.com
- userPrincipalName :us5@verified.contoso.com

Synkronisera uppdatering på lokalt userPrincipalName-attribut till Azure AD-klienten
- Uppdatera på lokala userPrincipalName-attribut utlöser omberäkning av Azure AD UserPrincipalName-attribut.
- Ange Azure AD UserPrincipalName-attributet till lokalt userPrincipalName-attribut eftersom UPN-suffixet verifieras med Azure AD-klienten.

Användarobjekt för Azure AD-klient:
- MailNickName : us4     
- UserPrincipalName :us5@verified.contoso.com

## <a name="next-steps"></a>Efterföljande moment
- [Integrerar dina lokala kataloger med Azure Active Directory](whatis-hybrid-identity.md)
- [Anpassad installation av Azure AD Connect](how-to-connect-install-custom.md)
