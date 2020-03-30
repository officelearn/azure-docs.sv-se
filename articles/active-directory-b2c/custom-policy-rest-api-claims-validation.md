---
title: REST API hävdar utbyten som validering
titleSuffix: Azure AD B2C
description: En genomgång för att skapa en Azure AD B2C-användarresa som interagerar med RESTful-tjänster.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a4902e96cd41a02953b6686b5d52d7912b27809f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80330825"
---
# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-to-validate-user-input"></a>Genomgång: Integrera REST API-anspråksutbyten i din Azure AD B2C-användarresa för att validera användarindata

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Identity Experience Framework (IEF) som ligger till grund för Azure Active Directory B2C (Azure AD B2C) gör det möjligt för identitetsutvecklare att integrera en interaktion med ett RESTful API i en användarresa.  I slutet av den här genomgången kan du skapa en Azure AD B2C-användarresa som interagerar med [RESTful-tjänster](custom-policy-rest-api-intro.md) för att validera användarindata.

I det här fallet lägger vi till möjligheten för användare att ange ett förmånsnummer på azure AD B2C-registreringssidan. Vi validerar om den här kombinationen av e-post och förmånsnummer mappas till en kampanjkod genom att skicka dessa data till ett REST API. Om REST API hittar en kampanjkod för den här användaren returneras den till Azure AD B2C. Slutligen infogas kampanjkoden i tokenanspråken för att programmet ska förbrukas.

Du kan också utforma interaktionen som ett orchestration-steg. Detta är lämpligt när REST API inte kommer att validera data på skärmen, och alltid returnera fordringar. Mer information finns i [Genomgång: Integrera REST API-anspråksutbyten i din Azure AD B2C-användarresa som ett orchestration-steg](custom-policy-rest-api-claims-exchange.md).

## <a name="prerequisites"></a>Krav

- Slutför stegen i [Komma igång med anpassade principer](custom-policy-get-started.md). Du bör ha en fungerande anpassad princip för registrering och inloggning med lokala konton.
- Lär dig hur du [integrerar REST API-anspråksutbyten i din anpassade princip för Azure AD B2C](custom-policy-rest-api-intro.md).

## <a name="prepare-a-rest-api-endpoint"></a>Förbereda en REST API-slutpunkt

För den här genomgången bör du ha ett REST API som validerar om en e-postadress är registrerad i ditt serverdelssystem med ett förmåns-ID. Om REST API-API:et är registrerat ska det returnera en registreringskampanjkod som kunden kan använda för att köpa varor i din ansökan. I annat fall ska REST API:et returnera ett HTTP 409-felmeddelande: "Lojalitets-ID '{loyalty ID} är inte associerat med e-postadressen {email}.".

Följande JSON-kod illustrerar de data som Azure AD B2C skickar till din REST API-slutpunkt. 

```json
{
    "email": "User email address",
    "language": "Current UI language",
    "loyaltyId": "User loyalty ID"
}
```

När REST API validerar data måste det returnera en HTTP 200 (Ok), med följande JSON-data:

```json
{
    "promoCode": "24534"
}
```

Om valideringen misslyckades måste REST API returnera en HTTP 409 (Konflikt), med `userMessage` JSON-elementet. IEF förväntar `userMessage` sig påståendet att REST API returnerar. Det här anspråket visas som en sträng för användaren om valideringen misslyckas.

```json
{
    "version": "1.0.1",
    "status": 409,
    "userMessage": "LoyaltyId ID '1234' is not associated with 'david@contoso.com' email address."
}
```

Inställningen av REST API-slutpunkten ligger utanför den här artikelns omfattning. Vi har skapat ett [Azure Functions-exempel.](https://docs.microsoft.com/azure/azure-functions/functions-reference) Du kan komma åt den fullständiga Azure-funktionskoden på [GitHub](https://github.com/azure-ad-b2c/rest-api/tree/master/source-code/azure-function).

## <a name="define-claims"></a>Definiera anspråk

Ett anspråk ger tillfällig lagring av data under en Azure AD B2C-principkörning. Du kan deklarera anspråk i avsnittet [anspråksschema.](claimsschema.md) 

1. Öppna filen för tillägg i principen. Till exempel <em> `SocialAndLocalAccounts/` </em>.
1. Sök efter elementet [BuildingBlocks.](buildingblocks.md) Om elementet inte finns lägger du till det.
1. Leta reda på elementet [ClaimsSchema.](claimsschema.md) Om elementet inte finns lägger du till det.
1. Lägg till följande anspråk i elementet **ClaimsSchema.**  

```xml
<ClaimType Id="loyaltyId">
  <DisplayName>Your loyalty ID</DisplayName>
  <DataType>string</DataType>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
<ClaimType Id="promoCode">
  <DisplayName>Your promo code</DisplayName>
  <DataType>string</DataType>
  <UserInputType>Paragraph</UserInputType>
</ClaimType>
  <ClaimType Id="userLanguage">
  <DisplayName>User UI language (used by REST API to return localized error messages)</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

## <a name="configure-the-restful-api-technical-profile"></a>Konfigurera den tekniska PROFILEN för RESTful API 

En [vilsam teknisk profil](restful-technical-profile.md) ger stöd för gränssnitt till din egen RESTful-tjänst. Azure AD B2C skickar data till RESTful-tjänsten i `InputClaims` `OutputClaims` en samling och tar emot data tillbaka i en samling. Hitta elementet **ClaimsProviders** och lägg till en ny anspråksleverantör enligt följande:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-ValidateProfile">
      <DisplayName>Check loyaltyId Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/ValidateProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
        <Item Key="AuthenticationType">None</Item>
        <!-- REMOVE the following line in production environments -->
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <!-- Claims sent to your REST API -->
        <InputClaim ClaimTypeReferenceId="loyaltyId" />
        <InputClaim ClaimTypeReferenceId="email" />
        <InputClaim ClaimTypeReferenceId="userLanguage" PartnerClaimType="lang" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
      </InputClaims>
      <OutputClaims>
        <!-- Claims parsed from your REST API -->
        <OutputClaim ClaimTypeReferenceId="promoCode" />
      </OutputClaims>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

I det här `userLanguage` exemplet skickas rest-tjänsten `lang` som inom JSON-nyttolasten. Värdet för `userLanguage` anspråket innehåller det aktuella användarspråks-ID:t. Mer information finns i [anspråkslösare](claim-resolver-overview.md).

Kommentarerna ovan `AuthenticationType` `AllowInsecureAuthInProduction` och ange ändringar som du bör göra när du flyttar till en produktionsmiljö. Mer information om hur du skyddar dina RESTful API:er för produktion finns i [Secure RESTful API](secure-rest-api.md).

## <a name="validate-the-user-input"></a>Validera indata för användaren

Om du vill ha användarens förmånsnummer under registreringen måste du tillåta användaren att ange dessa data på skärmen. Lägg till anspråk på **loyaltyId-utdata** på registreringssidan genom att lägga `OutputClaims` till det i det befintliga avsnittet registrera teknisk profil element. Ange hela listan över utdataanspråk för att styra den ordning som anspråken visas på skärmen.  

Lägg till den tekniska profilreferensen för validering i `REST-ValidateProfile`den tekniska profilen för registrering, som anropar . Den nya tekniska profilen för validering läggs `<ValidationTechnicalProfiles>` till överst i samlingen som definieras i basprincipen. Det här problemet innebär att Azure AD B2C först efter lyckad validering går vidare för att skapa kontot i katalogen.   

1. Leta reda på elementet **ClaimsProviders.** Lägg till en ny anspråksleverantör enligt följande:

    ```xml
    <ClaimsProvider>
      <DisplayName>Local Account</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="newPassword" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="displayName"/>
            <OutputClaim ClaimTypeReferenceId="givenName"/>
            <OutputClaim ClaimTypeReferenceId="surName"/>
            <!-- Required to present the text box to collect the data from the user -->
            <OutputClaim ClaimTypeReferenceId="loyaltyId"/>
            <!-- Required to pass the promoCode returned from "REST-ValidateProfile" 
            to subsequent orchestration steps and token issuance-->
            <OutputClaim ClaimTypeReferenceId="promoCode" />
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="REST-ValidateProfile" />
          </ValidationTechnicalProfiles>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    <ClaimsProvider>
      <DisplayName>Self Asserted</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SelfAsserted-Social">
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="email" />
          </InputClaims>
            <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="email" />
            <OutputClaim ClaimTypeReferenceId="displayName"/>
            <OutputClaim ClaimTypeReferenceId="givenName"/>
            <OutputClaim ClaimTypeReferenceId="surname"/>
            <!-- Required to present the text box to collect the data from the user -->
            <OutputClaim ClaimTypeReferenceId="loyaltyId"/>
            <!-- Required to pass the promoCode returned from "REST-ValidateProfile" 
            to subsequent orchestration steps and token issuance-->
            <OutputClaim ClaimTypeReferenceId="promoCode" />
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="REST-ValidateProfile"/>
          </ValidationTechnicalProfiles>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

## <a name="include-a-claim-in-the-token"></a>Inkludera ett anspråk i token 

Om du vill returnera anspråket för kampanjkod tillbaka till <em> `SocialAndLocalAccounts/` </em> det förlitande part-programmet lägger du till ett utdataanspråk i filen. Utdataanspråket gör att anspråket kan läggas till i token efter en lyckad användarresa och skickas till programmet. Ändra det tekniska profilelementet i avsnittet `promoCode` förlitande part för att lägga till utdataanspråket.
 
```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
      <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      <OutputClaim ClaimTypeReferenceId="promoCode" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

## <a name="test-the-custom-policy"></a>Testa den anpassade principen

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Kontrollera att du använder katalogen som innehåller din Azure AD-klient genom att välja **katalog + prenumerationsfilter** i den övre menyn och välja den katalog som innehåller din Azure AD-klientorganisation.
1. Välj **Alla tjänster** i det övre vänstra hörnet på Azure-portalen och sök sedan efter och välj **Appregistreringar**.
1. Välj **Identity Experience Framework**.
1. Välj **Ladda upp anpassad princip**och ladda sedan upp de principfiler som du har ändrat: *TrustFrameworkExtensions.xml*och *SignUpOrSignin.xml*. 
1. Välj den registrerings- eller inloggningsprincip som du har laddat upp och klicka på knappen **Kör nu.**
1. Du bör kunna registrera dig med en e-postadress.
1. Klicka på länken **Registrera dig nu.**
1. Skriv 1234 i **ditt förmåns-ID**och klicka på **Fortsätt**. Nu bör du få ett felmeddelande om validering.
1. Ändra till ett annat värde och klicka på **Fortsätt**.
1. Token som skickas tillbaka till `promoCode` din ansökan innehåller anspråket.

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1584295703,
  "nbf": 1584292103,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1584292103,
  "auth_time": 1584292103,
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "promoCode": "84362"
  ...
}
```

## <a name="next-steps"></a>Nästa steg

Mer information om hur du skyddar API:erna finns i följande artiklar:

- [Genomgång: Integrera REST API-anspråksutbyten i din Azure AD B2C-användarresa som ett orchestration-steg](custom-policy-rest-api-claims-exchange.md)
- [Skydda ditt RESTful API](secure-rest-api.md)
- [Referens: RESTful teknisk profil](restful-technical-profile.md)
