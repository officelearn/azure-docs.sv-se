---
title: "Azure Active Directory B2C: Förstå anpassade principer för starter pack | Microsoft Docs"
description: "Ett ämne på Azure Active Directory B2C anpassade principer"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: rojasja
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/25/2017
ms.author: joroja
ms.openlocfilehash: 9847bcfcc139a769847678c1cca6a8b9c3a30e93
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="understanding-the-custom-policies-of-the-azure-ad-b2c-custom-policy-starter-pack"></a>Så här fungerar på Azure AD B2C anpassad princip startpaket anpassade principer

Det här avsnittet innehåller alla kärnor element i B2C_1A_base principen som medföljer den **startpaket** och som utnyttjas för att skapa dina egna principer genom arv av den *B2C_1A_base_extensions princip* .

Därför fokuserar den särskilt på redan definierad anspråkstyper, anspråksomvandlingar, definitioner av innehåll, Anspråksproviders med deras tekniska eller profilerna och core användaren resor.

> [!IMPORTANT]
> Microsoft lämnar inga garantier, uttryckliga eller underförstådda, avseende informationen nedan. Ändringar kan införas när som helst före GA tiden GA tidpunkt eller efter.

Både dina egna principer och principen B2C_1A_base_extensions kan åsidosätta dessa definitioner och utöka överordnade principen genom att tillhandahålla ytterligare dem efter behov.

Grundelementen i den *B2C_1A_base princip* är anspråkstyper och anspråksomvandlingar innehåll definitioner. De här elementen kan mottagliga refereras i dina egna principer samt som i den *B2C_1A_base_extensions princip*.

## <a name="claims-schemas"></a>Anspråk scheman

Detta anspråk scheman är indelat i tre delar:

1.  Ett första avsnitt som visar minsta anspråk som krävs för användaren resor ska fungera korrekt.
2.  Ett andra avsnitt som visar anspråk krävs för frågan string-parametrar och andra särskilda parametrar som ska skickas till andra anspråksleverantörer, särskilt login.microsoftonline.com för autentisering. **Ändra inte de här anspråken**.
3.  Och slutligen ett tredje avsnitt som visar en lista över ytterligare och valfria anspråk som kan samlas in från användare, lagras i katalogen och skickas i token under inloggningen. Ny typ av anspråk till samlas in från användaren och skickas i token som kan läggas till i det här avsnittet.

> [!IMPORTANT]
> Anspråk schemat innehåller begränsningar för vissa anspråk som lösenord och användarnamn. Principen förtroende Framework (TF) behandlar Azure AD som andra anspråksprovider och dess begränsningar utformas i premium-principen. En princip kan ändras om du vill lägga till fler begränsningar eller Använd en annan anspråksprovider för lagring av autentiseringsuppgifter som har sin egen begränsningar.

Nedan visas de tillgängliga anspråkstyper.

### <a name="claims-that-are-required-for-the-user-journeys"></a>Anspråk som krävs för användaren resor

Följande anspråk krävs för användaren resor ska fungera korrekt:

| Typ av anspråk | Beskrivning |
|-------------|-------------|
| *Användar-ID* | Användarnamn |
| *signInName* | Logga in namn |
| *klient-ID* | Klient ID-Numret för användarobjektet i Azure AD B2C Premium |
| *objekt-ID* | Objekt-ID (ID) för användarobjektet i Azure AD B2C Premium |
| *lösenord* | Lösenord |
| *nytt lösenord* | |
| *reenterPassword* | |
| *passwordPolicies* | Lösenordsprinciper som används av Azure AD B2C Premium för att fastställa lösenordssäkerhet, upphör att gälla, osv. |
| *Sub* | |
| *alternativeSecurityId* | |
| *identityProvider* | |
| *Visningsnamn* | |
| *strongAuthenticationPhoneNumber* | Användarens telefonnummer |
| *Verified.strongAuthenticationPhoneNumber* | |
| *e-post* | E-postadress som kan användas för att kontakta användaren |
| *signInNamesInfo.emailAddress* | E-postadress som användaren kan använda för att logga in |
| *otherMails* | E-postadresser som kan användas för att kontakta användaren |
| *userPrincipalName* | Användarnamnet som lagras i Azure AD B2C-Premium |
| *upnUserName* | Användarnamn för att skapa användarens huvudnamn |
| *mailNickName* | Användarens e-smeknamn som lagras i Azure AD B2C-Premium |
| *ny användare* | |
| *köra SelfAsserted-inmatning* | Anspråk som anger om attribut samlades in från användaren |
| *köra PhoneFactor-inmatning* | Anspråk som anger om ett nytt telefonnummer samlats in från användaren |
| *authenticationSource* | Anger om användaren har autentiserats på sociala identitetsleverantör, login.microsoftonline.com eller lokalt konto |

### <a name="claims-required-for-query-string-parameters-and-other-special-parameters"></a>Anspråk som krävs för frågan string-parametrar och andra särskilda parametrar

Följande anspråk krävs för att vidarebefordra särskilda parametrar (inklusive vissa sträng frågeparametrar) till andra anspråksleverantörer:

| Typ av anspråk | Beskrivning |
|-------------|-------------|
| *nux* | Särskilda parameter som skickades för lokalt konto autentisering för login.microsoftonline.com |
| *NCA* | Särskilda parameter som skickades för lokalt konto autentisering för login.microsoftonline.com |
| *kommandotolk* | Särskilda parameter som skickades för lokalt konto autentisering för login.microsoftonline.com |
| *mkt* | Särskilda parameter som skickades för lokalt konto autentisering för login.microsoftonline.com |
| *LC* | Särskilda parameter som skickades för lokalt konto autentisering för login.microsoftonline.com |
| *grant_type* | Särskilda parameter som skickades för lokalt konto autentisering för login.microsoftonline.com |
| *omfång* | Särskilda parameter som skickades för lokalt konto autentisering för login.microsoftonline.com |
| *client_id* | Särskilda parameter som skickades för lokalt konto autentisering för login.microsoftonline.com |
| *objectIdFromSession* | Parametern som angetts av standardprovidern för sessionen management att indikera att objekt-id har hämtats från en SSO-session |
| *isActiveMFASession* | Parametern som angetts av MFA sessionshantering som visar att användaren har en aktiv session MFA |

### <a name="additional-optional-claims-that-can-be-collected"></a>Ytterligare (valfritt) anspråk som kan samlas in

Följande anspråk är ytterligare anspråk som kan samlas in från användarna, lagras i katalogen och skickas i token. Enligt innan kan ytterligare anspråk läggas till i listan.

| Typ av anspråk | Beskrivning |
|-------------|-------------|
| *givenName* | Användarens förnamn (även kallat Förnamn) |
| *Efternamn* | Användarens efternamn (även kallat namn eller efternamn) |
| *Extension_picture* | Användarens bild från sociala |

## <a name="claim-transformations"></a>Anspråksomvandlingar

Nedan visas de tillgängliga anspråksomvandlingar.

| Anspråksomvandling av | Beskrivning |
|----------------------|-------------|
| *CreateOtherMailsFromEmail* | |
| *CreateRandomUPNUserName* | |
| *CreateUserPrincipalName* | |
| *CreateSubjectClaimFromObjectID* | |
| *CreateSubjectClaimFromAlternativeSecurityId* | |
| *CreateAlternativeSecurityId* | |

## <a name="content-definitions"></a>Definitioner för innehåll

Det här avsnittet beskrivs de innehåll definitionerna redan deklarerats i den *B2C_1A_base* princip. Dessa definitioner av innehåll är sårbara för refererar till, åsidosätts och utökad som behövs i dina egna principer samt som i den *B2C_1A_base_extensions* princip.

| Anspråksleverantör | Beskrivning |
|-----------------|-------------|
| *Facebook* | |
| *Logga in lokalt konto* | |
| *PhoneFactor* | |
| *Azure Active Directory* | |
| *Self vars* | |
| *Lokalt konto* | |
| *Sessionshantering* | |
| *Trustframework principmodulen* | |
| *TechnicalProfiles* | |
| *Token utfärdare* | |

## <a name="technical-profiles"></a>Tekniska profiler

Det här avsnittet visar profilerna tekniska redan deklarerats per anspråksleverantör i den *B2C_1A_base* princip. Dessa tekniska profiler är sårbara för att ytterligare refererar till, åsidosätts och/eller utökad som behövs i dina egna principer samt som i den *B2C_1A_base_extensions* princip.

### <a name="technical-profiles-for-facebook"></a>Tekniska profiler för Facebook

| Tekniska profil | Beskrivning |
|-------------------|-------------|
| *Facebook-OAUTH* | |

### <a name="technical-profiles-for-local-account-signin"></a>Tekniska profiler för lokal inloggning för kontot

| Tekniska profil | Beskrivning |
|-------------------|-------------|
| *Logga in utan interaktivitet* | |

### <a name="technical-profiles-for-phone-factor"></a>Tekniska profiler för Phonefactor

| Tekniska profil | Beskrivning |
|-------------------|-------------|
| *PhoneFactor-indata* | |
| *PhoneFactor-InputOrVerify* | |
| *PhoneFactor-verifiera* | |

### <a name="technical-profiles-for-azure-active-directory"></a>Tekniska profiler för Azure Active Directory

| Tekniska profil | Beskrivning |
|-------------------|-------------|
| *AAD-gemensamma* | Tekniska profil ingår som de andra AAD-xxx tekniska profilerna |
| *AAD-UserWriteUsingAlternativeSecurityId* | Tekniska profil för sociala inloggningar |
| *AAD-UserReadUsingAlternativeSecurityId* | Tekniska profil för sociala inloggningar |
| *AAD-UserReadUsingAlternativeSecurityId-NoError* | Tekniska profil för sociala inloggningar |
| *AAD-UserWritePasswordUsingLogonEmail* | Tekniska profil för lokala konton |
| *AAD-UserReadUsingEmailAddress* | Tekniska profil för lokala konton |
| *AAD-UserWriteProfileUsingObjectId* | Tekniska profil för att uppdatera användarpost med objekt-ID |
| *AAD-UserWritePhoneNumberUsingObjectId* | Tekniska profil för att uppdatera användarpost med objekt-ID |
| *AAD-UserWritePasswordUsingObjectId* | Tekniska profil för att uppdatera användarpost med objekt-ID |
| *AAD-UserReadUsingObjectId* | Tekniska profilen används för att läsa data när användaren autentiseras |

### <a name="technical-profiles-for-self-asserted"></a>Tekniska profiler för Self vars

| Tekniska profil | Beskrivning |
|-------------------|-------------|
| *SelfAsserted Social* | |
| *SelfAsserted ProfileUpdate* | |

### <a name="technical-profiles-for-local-account"></a>Tekniska profiler för lokala konton

| Tekniska profil | Beskrivning |
|-------------------|-------------|
| *LocalAccountSignUpWithLogonEmail* | |

### <a name="technical-profiles-for-session-management"></a>Tekniska profiler för sessionshantering

| Tekniska profil | Beskrivning |
|-------------------|-------------|
| *SM-Noop* | |
| *SM-AAD* | |
| *SM-SocialSignup* | Profilnamnet används för att undvika tvetydigheten AAD session mellan logga in och logga in |
| *SM-SocialLogin* | |
| *SM-MFA* | |

### <a name="technical-profiles-for-trustframework-policy-engine-technicalprofiles"></a>Tekniska profiler för Trustframework princip motorn TechnicalProfiles

För närvarande inga tekniska profiler har definierats för den **Trustframework princip motorn TechnicalProfiles** anspråksleverantör.

### <a name="technical-profiles-for-token-issuer"></a>Tekniska profiler för tokenutfärdare

| Tekniska profil | Beskrivning |
|-------------------|-------------|
| *JwtIssuer* | |

## <a name="user-journeys"></a>Användaren resor

Det här avsnittet visar de användare körningar redan deklarerats i den *B2C_1A_base* princip. Dessa användare resor är sårbara för att ytterligare refererar till, åsidosätts och/eller utökad som behövs i dina egna principer samt som i den *B2C_1A_base_extensions* princip.

| Användaren resa | Beskrivning |
|--------------|-------------|
| *Registreringen* | |
| *Logga in* | |
| *SignUpOrSignIn* | |
| *EditProfile* | |
| *PasswordReset* | |
