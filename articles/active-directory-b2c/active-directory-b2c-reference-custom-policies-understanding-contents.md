---
title: Förstå anpassade principer för start-paket i Azure Active Directory B2C | Microsoft Docs
description: Ett ämne på Azure Active Directory B2C anpassade principer.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: ebcd7a677acde12558b0f566bce9172a0d00233b
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37442482"
---
# <a name="understanding-the-custom-policies-of-the-azure-ad-b2c-custom-policy-starter-pack"></a>Förstå anpassade principer för Azure AD B2C anpassad princip-startpaket

Det här avsnittet innehåller alla kärnelement policyns B2C_1A_base som medföljer den **startpaket** och som används för att skapa dina egna principer genom arv av den *B2C_1A_base_extensions princip* .

Därför måste fokuserar den särskilt på redan definierade anspråkstyper, anspråksomvandlingar, innehållsdefinitioner, Anspråksproviders med sin tekniska profil(er) och utbildning för core-användare.

> [!IMPORTANT]
> Microsoft lämnar inga garantier, uttryckliga eller underförstådda, avseende informationen nedan. Ändringar kan införas när som helst före GA tiden på tillgänglig för allmänheten eller.

Både dina egna principer och principen B2C_1A_base_extensions kan åsidosätta dessa definitioner och utöka den här överordnade principen genom att tillhandahålla ytterligare dem efter behov.

Grundelementen i den *B2C_1A_base princip* är anspråkstyper och anspråksomvandlingar innehållsdefinitioner. De här elementen kan sårbara för att referera till i dina egna principer samt som i den *B2C_1A_base_extensions princip*.

## <a name="claims-schemas"></a>Anspråk scheman

Detta anspråk scheman är uppdelad i tre avsnitt:

1.  En första avsnitt som visar en lista över de minsta anspråk som krävs för användaren resor ska fungera korrekt.
2.  Ett andra avsnitt som visar anspråk som krävs för frågesträngsparametrar och andra särskilda parametrar som ska skickas till andra anspråksleverantörer, särskilt login.microsoftonline.com för autentisering. **Ändra inte de här anspråken**.
3.  Och slutligen ett tredje avsnitt som visar en lista över ytterligare, valfria anspråk som kan samlas in från användaren, lagras i katalogen och skickas i token när de loggar in. Ny typ av anspråk till att samlas in från användaren och/eller skickas i token kan läggas till i det här avsnittet.

> [!IMPORTANT]
> Anspråk schemat innehåller begränsningar för vissa anspråk som lösenord och användarnamn. Lita på Framework (TF) principen behandlar Azure AD som någon annan anspråksleverantör av och dess begränsningar utformas i den anpassade principen. En princip kan ändras om du vill lägga till fler begränsningar eller Använd en annan anspråksprovider för lagring av autentiseringsuppgifter som har sin egen begränsningar.

De tillgängliga anspråkstyper som listas nedan.

### <a name="claims-that-are-required-for-the-user-journeys"></a>Anspråk som krävs för användaren-utbildning

Följande anspråk krävs för användaren resor ska fungera korrekt:

| Typ av anspråk | Beskrivning |
|-------------|-------------|
| *Användar-ID* | Användarnamn |
| *signInName* | Logga in namn |
| *TenantId* | Klient-ID (ID) för användarobjektet i Azure AD B2C |
| *objekt-ID* | Objekt-ID (ID) för användarobjektet i Azure AD B2C |
| *Lösenord* | Lösenord |
| *nytt lösenord* | |
| *reenterPassword* | |
| *passwordPolicies* | Lösenordsprinciper som används av Azure AD B2C för att fastställa lösenordssäkerhet, förfallodatum, osv. |
| *Sub* | |
| *alternativeSecurityId* | |
| *identityProvider* | |
| *displayName* | |
| *strongAuthenticationPhoneNumber* | Användarens telefonnummer |
| *Verified.strongAuthenticationPhoneNumber* | |
| *E-post* | E-postadress som kan användas för att kontakta användaren |
| *signInNamesInfo.emailAddress* | E-postadress som användaren kan använda för att logga in |
| *otherMails* | E-postadresser som kan användas för att kontakta användaren |
| *userPrincipalName* | Användarnamnet som lagras i Azure AD B2C |
| *upnUserName* | Användarnamn för att skapa användarens huvudnamn |
| *mailNickName* | Användarens e-post-smeknamn som lagras i Azure AD B2C |
| *ny användare* | |
| *körs SelfAsserted-inmatning* | Anspråk som anger om attribut har samlats in från användaren |
| *körs PhoneFactor-inmatning* | Anspråk som anger om ett nytt telefonnummer har samlats in från användaren |
| *authenticationSource* | Anger om användaren har autentiserats på Social identitetsprovider, login.microsoftonline.com eller lokalt konto |

### <a name="claims-required-for-query-string-parameters-and-other-special-parameters"></a>Anspråk som krävs för frågesträngsparametrar och andra särskilda parametrar

Följande anspråk krävs för att skicka vidare särskilda parametrar (inklusive några frågesträngsparametrar) till andra anspråksleverantörer:

| Typ av anspråk | Beskrivning |
|-------------|-------------|
| *nux* | Särskilda parameter som överförs för lokalt konto för autentisering till login.microsoftonline.com |
| *NCA* | Särskilda parameter som överförs för lokalt konto för autentisering till login.microsoftonline.com |
| *fråga* | Särskilda parameter som överförs för lokalt konto för autentisering till login.microsoftonline.com |
| *mkt* | Särskilda parameter som överförs för lokalt konto för autentisering till login.microsoftonline.com |
| *LC* | Särskilda parameter som överförs för lokalt konto för autentisering till login.microsoftonline.com |
| *_typ av beviljande* | Särskilda parameter som överförs för lokalt konto för autentisering till login.microsoftonline.com |
| *Omfång* | Särskilda parameter som överförs för lokalt konto för autentisering till login.microsoftonline.com |
| *client_id* | Särskilda parameter som överförs för lokalt konto för autentisering till login.microsoftonline.com |
| *objectIdFromSession* | Parametern som tillhandahålls av standardprovidern för sessionen management att indikera att objekt-ID har hämtats från en SSO-session |
| *isActiveMFASession* | Parametern som tillhandahålls av MFA-sessionshantering som visar att användaren har en aktiv MFA-session |

### <a name="additional-optional-claims-that-can-be-collected"></a>Ytterligare (valfritt) anspråk som kan samlas in

Följande anspråk är ytterligare anspråk som kan samlas in från användarna, lagras i katalogen och skickas i token. Enligt beskrivningen innan kan ytterligare anspråk läggas till i listan.

| Typ av anspråk | Beskrivning |
|-------------|-------------|
| *givenName* | Användarens förnamn (även kallat Förnamn) |
| *Efternamn* | Användarens efternamn (även kallat Familjenamn eller efternamn) |
| *Extension_picture* | Användarens bild från sociala |

## <a name="claim-transformations"></a>Anspråksomvandlingar

Tillgängliga anspråksomvandlingar listas nedan.

| Anspråksomvandling | Beskrivning |
|----------------------|-------------|
| *CreateOtherMailsFromEmail* | |
| *CreateRandomUPNUserName* | |
| *CreateUserPrincipalName* | |
| *CreateSubjectClaimFromObjectID* | |
| *CreateSubjectClaimFromAlternativeSecurityId* | |
| *CreateAlternativeSecurityId* | |

## <a name="content-definitions"></a>Innehållsdefinitioner

Det här avsnittet beskrivs innehållsdefinitioner som har redan deklarerats i den *B2C_1A_base* princip. Dessa innehållsdefinitioner kan vara utsatta för att referera till, åsidosätts och/eller utökade efter behov i dina egna principer samt som i den *B2C_1A_base_extensions* princip.

| Anspråksleverantör | Beskrivning |
|-----------------|-------------|
| *Facebook* | |
| *Inloggning från lokalt konto* | |
| *PhoneFactor* | |
| *Azure Active Directory* | |
| *Self verifieringsvillkor* | |
| *Lokalt konto* | |
| *Sessionshantering* | |
| *Motorn för Trustframework-princip* | |
| *TechnicalProfiles* | |
| *Tokenutfärdare* | |

## <a name="technical-profiles"></a>Tekniska profiler

Det här avsnittet beskrivs de tekniska profiler som redan deklarerats per anspråksleverantör i den *B2C_1A_base* princip. Dessa tekniska profiler kan vara utsatta för att ytterligare refereras, åsidosätts och/eller utökade efter behov i dina egna principer samt som i den *B2C_1A_base_extensions* princip.

### <a name="technical-profiles-for-facebook"></a>Tekniska profiler för Facebook

| Tekniska profilen | Beskrivning |
|-------------------|-------------|
| *Facebook-OAUTH* | |

### <a name="technical-profiles-for-local-account-signin"></a>Tekniska profiler för inloggning från lokalt konto

| Tekniska profilen | Beskrivning |
|-------------------|-------------|
| *Inloggning utan interaktivitet* | |

### <a name="technical-profiles-for-phone-factor"></a>Tekniska profiler för Phonefactor

| Tekniska profilen | Beskrivning |
|-------------------|-------------|
| *PhoneFactor-indata* | |
| *PhoneFactor-InputOrVerify* | |
| *Kontrollera PhoneFactor* | |

### <a name="technical-profiles-for-azure-active-directory"></a>Tekniska profiler för Azure Active Directory

| Tekniska profilen | Beskrivning |
|-------------------|-------------|
| *AAD – vanligt* | Tekniska profil inkluderas med de andra AAD-xxx tekniska profilerna |
| *AAD-UserWriteUsingAlternativeSecurityId* | Tekniska profilen för sociala inloggningar |
| *AAD-UserReadUsingAlternativeSecurityId* | Tekniska profilen för sociala inloggningar |
| *AAD-UserReadUsingAlternativeSecurityId-NoError* | Tekniska profilen för sociala inloggningar |
| *AAD-UserWritePasswordUsingLogonEmail* | Tekniska profilen för lokala konton |
| *AAD-UserReadUsingEmailAddress* | Tekniska profilen för lokala konton |
| *AAD-UserWriteProfileUsingObjectId* | Tekniska profilen för att uppdatera användarpost med objekt-ID |
| *AAD-UserWritePhoneNumberUsingObjectId* | Tekniska profilen för att uppdatera användarpost med objekt-ID |
| *AAD-UserWritePasswordUsingObjectId* | Tekniska profilen för att uppdatera användarpost med objekt-ID |
| *AAD-UserReadUsingObjectId* | Tekniska profilen används för att läsa data när användaren autentiseras |

### <a name="technical-profiles-for-self-asserted"></a>Tekniska profiler för Self verifieringsvillkor

| Tekniska profilen | Beskrivning |
|-------------------|-------------|
| *SelfAsserted Social* | |
| *SelfAsserted ProfileUpdate* | |

### <a name="technical-profiles-for-local-account"></a>Tekniska profiler för lokalt konto

| Tekniska profilen | Beskrivning |
|-------------------|-------------|
| *LocalAccountSignUpWithLogonEmail* | |

### <a name="technical-profiles-for-session-management"></a>Tekniska profiler för sessionshantering

| Tekniska profilen | Beskrivning |
|-------------------|-------------|
| *SM-Noop* | |
| *SM-AAD* | |
| *SM-SocialSignup* | Profilnamnet används disambiguate AAD session mellan logga in och logga in |
| *SM-SocialLogin* | |
| *SM-MFA* | |

### <a name="technical-profiles-for-the-trust-framework-policy-engine"></a>Tekniska profiler för förtroende framework-principmodulen

För närvarande inga tekniska profiler har definierats för den **Trustframework-princip motorn TechnicalProfiles** anspråksleverantör.

### <a name="technical-profiles-for-token-issuer"></a>Tekniska profiler för tokenutfärdare

| Tekniska profilen | Beskrivning |
|-------------------|-------------|
| *JwtIssuer* | |

## <a name="user-journeys"></a>Användaren resor

Det här avsnittet visar de användare körningar som har redan deklarerats i den *B2C_1A_base* princip. Dessa användare transporter kan vara utsatta för att ytterligare refereras, åsidosätts och/eller utökade efter behov i dina egna principer samt som i den *B2C_1A_base_extensions* princip.

| Användarresa | Beskrivning |
|--------------|-------------|
| *Registrera dig* | |
| *Inloggning från* | |
| *SignUpOrSignIn* | |
| *EditProfile* | |
| *PasswordReset* | |
