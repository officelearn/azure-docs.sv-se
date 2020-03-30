---
title: REST API-anspråksutbyten - Azure Active Directory B2C
description: Lägg till REST API-anspråksutbyten i anpassade principer i Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6316165ba08d055be1186995e2fe2ad5a0079fb7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80330728"
---
# <a name="walkthrough-add-rest-api-claims-exchanges-to-custom-policies-in-azure-active-directory-b2c"></a>Genomgång: Lägg till REST API-anspråksutbyten i anpassade principer i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) gör det möjligt för identitetsutvecklare att integrera en interaktion med ett RESTful API i en användarresa. I slutet av den här genomgången kan du skapa en Azure AD B2C-användarresa som interagerar med [RESTful-tjänster](custom-policy-rest-api-intro.md).

I det här fallet berikar vi användarens tokendata genom att integrera med ett företags arbetsflöde för affärsområden. Under registrering eller inloggning med lokalt eller federerat konto anropar Azure AD B2C ett REST API för att hämta användarens utökade profildata från en fjärrdatakälla. I det här exemplet skickar Azure AD B2C användarens unika identifierare, objectId. REST API returnerar sedan användarens kontosaldo (ett slumptal). Använd det här exemplet som utgångspunkt för att integrera med ditt eget CRM-system, marknadsföringsdatabas eller något affärsarbetsflöde.

Du kan också utforma interaktionen som en teknisk valideringsprofil. Detta är lämpligt när REST API kommer att validera data på skärmen och returnera fordringar. Mer information finns i [Genomgång: Integrera REST API-anspråksutbyten i din Azure AD B2C-användarresa för att validera användarindata](custom-policy-rest-api-claims-validation.md).

## <a name="prerequisites"></a>Krav

- Slutför stegen i [Komma igång med anpassade principer](custom-policy-get-started.md). Du bör ha en fungerande anpassad princip för registrering och inloggning med lokala konton.
- Lär dig hur du [integrerar REST API-anspråksutbyten i din anpassade princip för Azure AD B2C](custom-policy-rest-api-intro.md).

## <a name="prepare-a-rest-api-endpoint"></a>Förbereda en REST API-slutpunkt

För den här genomgången bör du ha ett REST API som validerar om en användares Azure AD B2C-objectId är registrerat i backend-systemet. Om rest-API:et registreras returneras användarkontosaldot. Annars registrerar REST API det nya kontot i katalogen `50.00`och returnerar startsaldot .

Följande JSON-kod illustrerar de data som Azure AD B2C skickar till din REST API-slutpunkt. 

```json
{
    "objectId": "User objectId",
    "language": "Current UI language"
}
```

När REST API validerar data måste det returnera en HTTP 200 (Ok), med följande JSON-data:

```json
{
    "balance": "760.50"
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
<ClaimType Id="balance">
  <DisplayName>Your Balance</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="userLanguage">
  <DisplayName>User UI language (used by REST API to return localized error messages)</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

## <a name="configure-the-restful-api-technical-profile"></a>Konfigurera den tekniska PROFILEN för RESTful API 

En [vilsam teknisk profil](restful-technical-profile.md) ger stöd för samspel med din egen RESTful-tjänst. Azure AD B2C skickar data till RESTful-tjänsten i `InputClaims` `OutputClaims` en samling och tar emot data tillbaka i en samling. Hitta **elementet ClaimsProviders** i <em>**`TrustFrameworkExtensions.xml`**</em> filen och lägg till en ny anspråksleverantör enligt följande:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
        <Item Key="AuthenticationType">None</Item>
        <!-- REMOVE the following line in production environments -->
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <!-- Claims sent to your REST API -->
        <InputClaim ClaimTypeReferenceId="objectId" />
        <InputClaim ClaimTypeReferenceId="userLanguage" PartnerClaimType="lang" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
      </InputClaims>
      <OutputClaims>
        <!-- Claims parsed from your REST API -->
        <OutputClaim ClaimTypeReferenceId="balance" />
      </OutputClaims>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

I det här `userLanguage` exemplet skickas rest-tjänsten `lang` som inom JSON-nyttolasten. Värdet för `userLanguage` anspråket innehåller det aktuella användarspråks-ID:t. Mer information finns i [anspråkslösare](claim-resolver-overview.md).

Kommentarerna ovan `AuthenticationType` `AllowInsecureAuthInProduction` och ange ändringar som du bör göra när du flyttar till en produktionsmiljö. Mer information om hur du skyddar dina RESTful API:er för produktion finns i [Secure RESTful API](secure-rest-api.md).

## <a name="add-an-orchestration-step"></a>Lägga till ett orchestration-steg

[Användarfärder](userjourneys.md) anger explicita sökvägar genom vilka en princip tillåter ett förlitande part-program att hämta önskade anspråk för en användare. En användarresa representeras som en orchestration-sekvens som måste följas upp för en lyckad transaktion. Du kan lägga till eller subtrahera orkestreringssteg. I det här fallet lägger du till ett nytt orchestration-steg som används för att öka informationen som lämnas till programmet efter att användaren har registrerat sig eller loggat in via REST API-anropet.

1. Öppna principfilens basfil. Till exempel <em> `SocialAndLocalAccounts/` </em>.
1. Sök efter `<UserJourneys>` elementet. Kopiera hela elementet och ta sedan bort det.
1. Öppna filen för tillägg i principen. Till exempel <em> `SocialAndLocalAccounts/` </em>.
1. Klistra `<UserJourneys>` in filen i tilläggsfilen efter `<ClaimsProviders>` elementets.
1. Leta `<UserJourney Id="SignUpOrSignIn">`reda på och lägg till följande orchestration steg före den sista.

    ```XML
    <OrchestrationStep Order="7" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="RESTGetProfile" TechnicalProfileReferenceId="REST-GetProfile" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ```

1. Refactor det sista orchestration `Order` steget `8`genom att ändra till . De två sista orchestration stegen bör se ut så här:

    ```XML
    <OrchestrationStep Order="7" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="RESTGetProfile" TechnicalProfileReferenceId="REST-GetProfile" />
      </ClaimsExchanges>
    </OrchestrationStep>

    <OrchestrationStep Order="8" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
    ```

1. Upprepa de två sista stegen för **användarresorna ProfileEdit** och **PasswordReset.**


## <a name="include-a-claim-in-the-token"></a>Inkludera ett anspråk i token 

Om du `balance` vill returnera anspråket tillbaka till det <em> `SocialAndLocalAccounts/` </em> förlitande part-programmet lägger du till ett utdataanspråk i filen. Om du lägger till ett utdataanspråk uppstår anspråket i token efter en lyckad användarresa och skickas till programmet. Ändra det tekniska profilelementet i `balance` avsnittet förlitande part för att lägga till som utdataanspråk.
 
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
      <OutputClaim ClaimTypeReferenceId="balance" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

Upprepa det här steget för användarfärderna **ProfileEdit.xml**och **PasswordReset.xml.**

Spara de filer du har ändrat: *TrustFrameworkBase.xml*och *TrustFrameworkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml*och *PasswordReset.xml*. 

## <a name="test-the-custom-policy"></a>Testa den anpassade principen

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Kontrollera att du använder katalogen som innehåller din Azure AD-klient genom att välja **katalog + prenumerationsfilter** i den övre menyn och välja den katalog som innehåller din Azure AD-klientorganisation.
1. Välj **Alla tjänster** i det övre vänstra hörnet på Azure-portalen och sök sedan efter och välj **Appregistreringar**.
1. Välj **Identity Experience Framework**.
1. Välj **Ladda upp anpassad princip**och ladda sedan upp de principfiler som du har ändrat: *TrustFrameworkBase.xml*och *TrustFrameworkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml*och *PasswordReset.xml*. 
1. Välj den registrerings- eller inloggningsprincip som du har laddat upp och klicka på knappen **Kör nu.**
1. Du bör kunna registrera dig med en e-postadress eller ett Facebook-konto.
1. Token som skickas tillbaka till `balance` din ansökan innehåller anspråket.

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1584961516,
  "nbf": 1584957916,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1584957916,
  "auth_time": 1584957916,
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "balance": "202.75"
  ...
}
```

## <a name="next-steps"></a>Nästa steg


## <a name="next-steps"></a>Nästa steg

Mer information om hur du skyddar API:erna finns i följande artiklar:

- [Genomgång: Integrera REST API-anspråksutbyten i din Azure AD B2C-användarresa som ett orchestration-steg](custom-policy-rest-api-claims-exchange.md)
- [Skydda ditt RESTful API](secure-rest-api.md)
- [Referens: RESTful teknisk profil](restful-technical-profile.md)
