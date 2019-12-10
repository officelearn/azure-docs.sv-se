---
title: Komponenter i Start paketet för anpassad princip
titleSuffix: Azure AD B2C
description: En översikt över principerna i Azure Active Directory B2C anpassad princip start paket.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 7d49bd9af80b1bb9bd86466269b14ba0a47181e0
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2019
ms.locfileid: "74948189"
---
# <a name="understanding-the-custom-policies-of-the-azure-ad-b2c-custom-policy-starter-pack"></a>Förstå de anpassade principerna för Azure AD B2C anpassad princip start paket

I det här avsnittet visas alla kärn element i den B2C_1A_base principen som medföljer **Start paketet** och som används för att skapa egna principer genom arv av *B2C_1A_base_extensionss principen*.

Detta är särskilt avsett för de redan definierade anspråks typerna, anspråks omvandlingar, innehålls definitioner, anspråks leverantörer med teknisk profil (er) och kärn användar resan.

> [!IMPORTANT]
> Microsoft lämnar inga garantier, vare sig uttryckliga eller underförstådda, vad gäller den information som anges nedan. Ändringar kan införas när som helst, före GA-tid, vid GA-tid eller efter.

Både dina egna principer och B2C_1A_base_extensions-principen kan åsidosätta dessa definitioner och utöka den här överordnade principen genom att tillhandahålla ytterligare dem efter behov.

De viktigaste elementen i *B2C_1A_bases principen* är anspråks typer, anspråks omvandlingar och innehålls definitioner. Dessa element kan vara mottagliga för att refereras till i dina egna principer samt i *B2C_1A_base_extensions principen*.

## <a name="claims-schemas"></a>Anspråks scheman

Anspråks scheman är indelade i tre delar:

1.  Ett första avsnitt som visar de minsta anspråk som krävs för att användar resan ska fungera korrekt.
2.  Ett andra avsnitt som visar de anspråk som krävs för parametrar för frågesträng och andra särskilda parametrar som ska skickas till andra anspråks leverantörer, särskilt login.microsoftonline.com för autentisering. **Ändra inte de här anspråken**.
3.  Och slutligen ett tredje avsnitt som visar ytterligare, valfria anspråk som kan samlas in från användaren, lagras i katalogen och skickas i token under inloggningen. Ny anspråks typ som ska samlas in från användaren och/eller skickas i token kan läggas till i det här avsnittet.

> [!IMPORTANT]
> Anspråks schema innehåller begränsningar för vissa anspråk, t. ex. lösen ord och användar namn. Principen för förtroende Framework (TF) behandlar Azure AD som alla andra anspråks leverantörer och alla dess begränsningar är fördelade i den anpassade principen. En princip kan ändras för att lägga till fler begränsningar eller använda en annan anspråks leverantör för lagring av autentiseringsuppgifter som kommer att ha egna begränsningar.

Tillgängliga anspråks typer visas nedan.

### <a name="claims-that-are-required-for-the-user-journeys"></a>Anspråk som krävs för användar resan

Följande anspråk krävs för att användar resan ska fungera korrekt:

| Anspråks typ | Beskrivning |
|-------------|-------------|
| *UserId* | Användarnamn |
| *signInName* | Inloggnings namn |
| *tenantId* | Klient-ID för användarobjektet i Azure AD B2C |
| *objectId* | Objekt identifierare (ID) för användarobjektet i Azure AD B2C |
| *Lösenord* | Lösenord |
| *newPassword* | |
| *reenterPassword* | |
| *passwordPolicies* | Lösen ords principer som används av Azure AD B2C för att fastställa lösen ords säkerhet, förfallo datum osv. |
| *Build* | |
| *alternativeSecurityId* | |
| *identityProvider* | |
| *displayName* | |
| *strongAuthenticationPhoneNumber* | Användarens telefonnummer |
| *Verified.strongAuthenticationPhoneNumber* | |
| *åtkomst* | E-postadress som kan användas för att kontakta användaren |
| *signInNamesInfo.emailAddress* | E-postadress som användaren kan använda för att logga in |
| *otherMails* | E-postadresser som kan användas för att kontakta användaren |
| *userPrincipalName* | Användar namn som lagrats i Azure AD B2C |
| *upnUserName* | Användar namn för att skapa User Principal Name |
| *mailNickName* | Användarens e-Nick namn som det lagras i Azure AD B2C |
| *newUser* | |
| *körd-SelfAsserted-indatamängd* | Anspråk som anger om attribut har samlats in från användaren |
| *executed-PhoneFactor-Input* | Anspråk som anger om ett nytt telefonnummer samlats in från användaren |
| *authenticationSource* | Anger om användaren autentiserades hos social identitetsprovider, login.microsoftonline.com eller lokalt konto |

### <a name="claims-required-for-query-string-parameters-and-other-special-parameters"></a>Anspråk som krävs för parametrar för frågesträng och andra särskilda parametrar

Följande anspråk krävs för att överföra särskilda parametrar (inklusive vissa parametrar för frågesträng) till andra anspråks leverantörer:

| Anspråks typ | Beskrivning |
|-------------|-------------|
| *nux* | Speciell parameter som skickades för autentisering med lokalt konto till login.microsoftonline.com |
| *NCA* | Speciell parameter som skickades för autentisering med lokalt konto till login.microsoftonline.com |
| *visas* | Speciell parameter som skickades för autentisering med lokalt konto till login.microsoftonline.com |
| *mkt* | Speciell parameter som skickades för autentisering med lokalt konto till login.microsoftonline.com |
| *lc* | Speciell parameter som skickades för autentisering med lokalt konto till login.microsoftonline.com |
| *grant_type* | Speciell parameter som skickades för autentisering med lokalt konto till login.microsoftonline.com |
| *utrymme* | Speciell parameter som skickades för autentisering med lokalt konto till login.microsoftonline.com |
| *client_id* | Speciell parameter som skickades för autentisering med lokalt konto till login.microsoftonline.com |
| *objectIdFromSession* | Parameter som tillhandahålls av standardprovidern för hantering av sessioner för att ange att objekt-ID: t har hämtats från en SSO-session |
| *isActiveMFASession* | Parameter som tillhandahålls av MFA-sessionen hantering för att indikera att användaren har en aktiv MFA-session |

### <a name="additional-optional-claims-that-can-be-collected"></a>Ytterligare (valfria) anspråk som kan samlas in

Följande anspråk är ytterligare anspråk som kan samlas in från användarna, lagras i katalogen och skickas i token. Som beskrivs tidigare kan ytterligare anspråk läggas till i den här listan.

| Anspråks typ | Beskrivning |
|-------------|-------------|
| *givenName* | Användarens förnamn (kallas även förnamnet) |
| *efter namn* | Användarens efter namn (även kallat familje namn eller efter namn) |
| *Extension_picture* | Användarens bild från sociala |

## <a name="claim-transformations"></a>Anspråks omvandlingar

Tillgängliga anspråks omvandlingar visas nedan.

| Anspråks omvandling | Beskrivning |
|----------------------|-------------|
| *CreateOtherMailsFromEmail* | |
| *CreateRandomUPNUserName* | |
| *CreateUserPrincipalName* | |
| *CreateSubjectClaimFromObjectID* | |
| *CreateSubjectClaimFromAlternativeSecurityId* | |
| *CreateAlternativeSecurityId* | |

## <a name="content-definitions"></a>Innehålls definitioner

I det här avsnittet beskrivs de innehålls definitioner som redan har deklarerats i *B2C_1A_bases* principen. Dessa innehålls definitioner är utsatta för att refereras, åsidosättas och/eller utökas efter behov i dina egna principer samt i *B2C_1A_base_extensions* principen.

| Anspråksleverantör | Beskrivning |
|-----------------|-------------|
| *Facebook* | |
| *Inloggning för lokalt konto* | |
| *PhoneFactor* | |
| *Azure Active Directory* | |
| *Self-kontrollerad* | |
| *Lokalt konto* | |
| *Sessionshantering* | |
| *Trustframework-princip motor* | |
| *TechnicalProfiles* | |
| *Token utfärdare* | |

## <a name="technical-profiles"></a>Tekniska profiler

I det här avsnittet beskrivs de tekniska profiler som redan har deklarerats per anspråks leverantör i principen för *B2C_1A_base* . De här tekniska profilerna är utsatta för ytterligare referenser, åsidosätts och/eller utökas efter behov i dina egna principer samt i *B2C_1A_base_extensions* -principen.

### <a name="technical-profiles-for-facebook"></a>Tekniska profiler för Facebook

| Teknisk profil | Beskrivning |
|-------------------|-------------|
| *Facebook – OAUTH* | |

### <a name="technical-profiles-for-local-account-signin"></a>Teknisk profil för inloggning med lokalt konto

| Teknisk profil | Beskrivning |
|-------------------|-------------|
| *Inloggning-ej interaktiv* | |

### <a name="technical-profiles-for-phone-factor"></a>Tekniska profiler för telefon faktor

| Teknisk profil | Beskrivning |
|-------------------|-------------|
| *PhoneFactor-Input* | |
| *PhoneFactor-InputOrVerify* | |
| *PhoneFactor-Verify* | |

### <a name="technical-profiles-for-azure-active-directory"></a>Tekniska profiler för Azure Active Directory

| Teknisk profil | Beskrivning |
|-------------------|-------------|
| *AAD-common* | Teknisk profil som ingår i de andra AAD-XXX-tekniska profilerna |
| *AAD-UserWriteUsingAlternativeSecurityId* | Teknisk profil för sociala inloggningar |
| *AAD-UserReadUsingAlternativeSecurityId* | Teknisk profil för sociala inloggningar |
| *AAD-UserReadUsingAlternativeSecurityId-noerror* | Teknisk profil för sociala inloggningar |
| *AAD-UserWritePasswordUsingLogonEmail* | Teknisk profil för lokala konton |
| *AAD-UserReadUsingEmailAddress* | Teknisk profil för lokala konton |
| *AAD-UserWriteProfileUsingObjectId* | Teknisk profil för uppdatering av användar post med objectId |
| *AAD-UserWritePhoneNumberUsingObjectId* | Teknisk profil för uppdatering av användar post med objectId |
| *AAD-UserWritePasswordUsingObjectId* | Teknisk profil för uppdatering av användar post med objectId |
| *AAD-UserReadUsingObjectId* | Teknisk profil används för att läsa data när användaren autentiseras |

### <a name="technical-profiles-for-self-asserted"></a>Tekniska profiler för självkontrollerad

| Teknisk profil | Beskrivning |
|-------------------|-------------|
| *SelfAsserted-Social* | |
| *SelfAsserted-ProfileUpdate* | |

### <a name="technical-profiles-for-local-account"></a>Tekniska profiler för lokalt konto

| Teknisk profil | Beskrivning |
|-------------------|-------------|
| *LocalAccountSignUpWithLogonEmail* | |

### <a name="technical-profiles-for-session-management"></a>Tekniska profiler för hantering av sessioner

| Teknisk profil | Beskrivning |
|-------------------|-------------|
| *SM-Noop* | |
| *SM-AAD* | |
| *SM-SocialSignup* | Profil namnet används för att disambiguate AAD-sessionen mellan registrera dig och logga in |
| *SM-SocialLogin* | |
| *SM-MFA* | |

### <a name="technical-profiles-for-the-trust-framework-policy-engine"></a>Tekniska profiler för förtroende Ramverks princip motorn

För närvarande har inga tekniska profiler definierats för **Trustframework-TechnicalProfiles för princip motor** .

### <a name="technical-profiles-for-token-issuer"></a>Tekniska profiler för token Issuer

| Teknisk profil | Beskrivning |
|-------------------|-------------|
| *JwtIssuer* | |

## <a name="user-journeys"></a>Användarresor

I det här avsnittet beskrivs användar resan som redan har deklarerats i *B2C_1A_base* principen. Dessa användar resor är utsatta för ytterligare referenser, åsidosätts och/eller utökas efter behov i dina egna principer samt i *B2C_1A_base_extensions* principen.

| Användar resa | Beskrivning |
|--------------|-------------|
| *SignUp* | |
| *Inloggning* | |
| *SignUpOrSignIn* | |
| *EditProfile* | |
| *PasswordReset original* | |
