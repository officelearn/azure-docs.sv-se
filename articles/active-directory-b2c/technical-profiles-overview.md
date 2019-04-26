---
title: Om tekniska profiler i anpassade principer för Azure Active Directory B2C | Microsoft Docs
description: Lär dig mer om hur tekniska profiler som används i en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: ff54b76e8a2de752eaafdb1eb3a98fa95293f106
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60418122"
---
# <a name="about-technical-profiles-in-azure-active-directory-b2c-custom-policies"></a>Om tekniska profiler i Azure Active Directory B2C anpassade principer

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Tekniska profilen ger ett ramverk med en inbyggd mekanism för att kommunicera med annan typ av parter med en anpassad princip i Azure Active Directory (Azure AD) B2C. Tekniska profiler används för att kommunicera med din Azure AD B2C-klientorganisation, att skapa en användare eller läsa en användarprofil. Tekniska profilen kan vara självkontrollerad att aktivera användaren interagerar med. Till exempel samla in användarens autentiseringsuppgifter för inloggning och sedan återge registreringssidan eller sidan för återställning av lösenord. 

## <a name="type-of-technical-profiles"></a>Typ av tekniska profiler

Tekniska profilen gör det möjligt för dessa typer av scenarier:

- [Azure Active Directory](active-directory-technical-profile.md) -har stöd för hantering för Azure Active Directory B2C-användare.
- [JWT tokenutfärdare](jwt-issuer-technical-profile.md) -genererar en JWT-token som returneras till förlitande part-programmet. 
- **Phone faktor providern** -multifaktorautentisering.
- [OAuth1](oauth1-technical-profile.md) -Federation med alla identitetsleverantör för OAuth 1.0-protokollet.
- [OAuth2](oauth2-technical-profile.md) -Federation med alla identitetsleverantör för OAuth 2.0-protokollet.
- [OpenIdConnect](openid-connect-technical-profile.md) -Federation med alla OpenId Connect-protokollet identitetsprovider.
- [Omvandling av anspråk](claims-transformation-technical-profile.md) – anrop utdata anspråksomvandlingar att manipulera anspråk värden, pröva kravets eller ange standardvärden för en uppsättning utgående anspråk.
- [RESTful-providern](restful-technical-profile.md) -anrop till REST API-tjänster, t.ex. verifiera indata från användaren, utöka användardata eller integrera med line-of-business-program.
- [SAML2](saml-technical-profile.md) -Federation med alla protokollet SAML-identitetsprovider.
- [Lokal verifieringsvillkor](self-asserted-technical-profile.md) -interagera med användaren. Till exempel samla in användarens autentiseringsuppgifter för inloggning, återge registreringssidan eller återställning av lösenord.
- **WsFed** -Federation med alla WsFed protokollet identitetsprovider. 
- [Sessionshantering](active-directory-b2c-reference-sso-custom.md) – hantera olika typer av sessioner. 
- **Application insights**

## <a name="technical-profile-flow"></a>Tekniska profilen flöde

Alla typer av tekniska profiler delar samma begrepp. Du skickar inkommande anspråk, kör anspråkstransformering och kommunicera med den konfigurerade parten, till exempel en identitetsprovider, REST API eller Azure AD directory services. När processen är klar den tekniska profilen returnerar utdataanspråk och köra anspråkstransformering för utdata. Följande diagram visar hur omvandlingar och mappningar som refereras till i den tekniska profilen bearbetas. Oavsett den part som den tekniska profilen interagerar med, när du har några anspråk omvandling körs lagras direkt utdataanspråk från den tekniska profilen i uppsättningen anspråk. 

![Tekniska profilen flöde](./media/technical-profiles-overview/technical-profile-idp-saml-flow.png)
 
1. **InputClaimsTransformation** -inkommande anspråk på varje indata [omvandling av anspråk](claimstransformations.md) plockas upp från uppsättningen anspråk och efter körningen utdata anspråk sätts igen i uppsättningen anspråk. Utgående anspråk på ett inkommande anspråk omvandlingen kan vara inkommande anspråk på en efterföljande inkommande anspråk omvandling.
2. **InputClaims** – anspråk plockas upp från uppsättningen anspråk och används för den tekniska profilen. Till exempel en [lokal verifieringsvillkor tekniska profilen](self-asserted-technical-profile.md) används som indata-anspråk att fylla utdataanspråk som användaren tillhandahåller. En REST API-tekniska profilen använder de inkommande anspråken för att skicka indataparametrar REST API-slutpunkten. Azure Active Directory använder inkommande anspråk som en unik identifierare för att läsa, uppdatera eller ta bort ett konto.
3. **Tekniska profilen körning** -den tekniska profilen utbyter anspråk med den konfigurera parten. Exempel:
    - Omdirigera användaren till identitetsleverantören att slutföra inloggningen. Efter inloggningen lyckas, användaren returnerar tillbaka och fortsätter den tekniska profilen-körningen.
    - Anropa en REST-API när du skickar parametrar som InputClaims och hämta information som OutputClaims.
    - Skapa eller uppdatera användarkontot.
    - Skickar och verifierar textmeddelandet MFA.
4. **ValidationTechnicalProfiles** – för en [självkontrollerade tekniska profilen](self-asserted-technical-profile.md), kan du anropa indata [teknisk verifieringsprofil](validation-technical-profile.md). Den tekniska profilen verifiering verifierar data profileras av användaren och returnerar ett felmeddelande eller Ok, med eller utan utdataanspråk. Innan Azure AD B2C skapar ett nytt konto, kontrollerar det till exempel om användaren redan finns i katalogtjänsterna. Du kan anropa en REST API-tekniska profilen för att lägga till egen affärslogik.<p>Omfånget för utdataanspråk av en profil för tekniska är begränsad till den tekniska profilen som anropar den tekniska profilen för verifiering och andra tekniska profiler verifiering under samma tekniska profilen. Om du vill använda utgående anspråk i nästa orchestration-steg som du behöver lägga till utdataanspråk till den tekniska profilen som anropar den tekniska profilen för verifiering.
5. **OutputClaims** -anspråk som returneras till anspråk egenskapsuppsättning. Du kan använda dessa anspråk i nästa steg i orkestreringar eller anspråksomvandlingar för utdata.
6. **OutputClaimsTransformations** -inkommande anspråk på varje utdata [omvandling av anspråk](claimstransformations.md) plockas upp från uppsättningen anspråk. Utdataanspråk för den tekniska profilen från föregående steg kan vara inkommande anspråk på en anspråkstransformering för utdata. Efter körningen placeras utdataanspråk i uppsättningen anspråk. Utgående anspråk på en utdata anspråkstransformering kan också vara inkommande anspråk på en efterföljande utdata anspråkstransformering.
7. **Enkel inloggning (SSO) sessionshantering** - [SSO sessionshantering](active-directory-b2c-reference-sso-custom.md) styr interaktion med en användare när användaren redan har autentiserats. Administratören kan till exempel styra om valet av identitetsleverantörer ska visas eller om lokala kontoinformation måste anges igen.

Tekniska profilen kan ärvas från en annan tekniska profil ändra inställningar eller lägga till nya funktioner.  Den **IncludeTechnicalProfile** element är en referens till den grundläggande tekniska profilen som en tekniska profilen härleds.  

Till exempel den **AAD-UserReadUsingAlternativeSecurityId-NoError** tekniska profilen innehåller den **AAD-UserReadUsingAlternativeSecurityId**. Den här tekniska profilen anger den **RaiseErrorIfClaimsPrincipalDoesNotExist** metadataobjektet till `true`, och genererar ett fel om en sociala kontot inte finns i katalogen. **AAD-UserReadUsingAlternativeSecurityId-NoError** åsidosätter det här beteendet och inaktiverar ett felmeddelande om användaren inte fanns.

```XML
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId-NoError">
  <Metadata>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">false</Item>
  </Metadata>
  <IncludeTechnicalProfile ReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
</TechnicalProfile>
``` 

**AAD-UserReadUsingAlternativeSecurityId** innehåller den `AAD-Common` tekniska profilen.

```XML
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">Read</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">User does not exist. Please sign up before you can sign in.</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId" PartnerClaimType="alternativeSecurityId" Required="true" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surname" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

Båda **AAD-UserReadUsingAlternativeSecurityId-NoError** och **AAD-UserReadUsingAlternativeSecurityId** inte anger de nödvändiga **protokollet** elementet eftersom det anges i den **AAD-gemensamma** tekniska profilen.

```XML
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
</TechnicalProfile>
```

Tekniska profilen kan inkludera eller ärver ett annat tekniska profilen, som kan innehålla en annan. Det finns ingen gräns för hur många nivåer. Beroende på företagets behov användarresan kan anropa **AAD-UserReadUsingAlternativeSecurityId** som genererar ett fel om ett socialt konto inte finns, eller  **AAD-UserReadUsingAlternativeSecurityId-NoError** som inte returnerar ett fel.











