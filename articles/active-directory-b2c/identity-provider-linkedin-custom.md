---
title: Konfigurera inloggning med ett LinkedIn-konto med hjälp av anpassade principer
titleSuffix: Azure AD B2C
description: Konfigurera inloggning med ett LinkedIn-konto i Azure Active Directory B2C med hjälp av anpassade principer.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 80bd1b65d04ea49fc742033e1850d95a85021c9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188179"
---
# <a name="set-up-sign-in-with-a-linkedin-account-using-custom-policies-in-azure-active-directory-b2c"></a>Konfigurera inloggning med ett LinkedIn-konto med hjälp av anpassade principer i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den här artikeln visar hur du aktiverar inloggning för användare från ett LinkedIn-konto med hjälp av [anpassade principer](custom-policy-overview.md) i Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Krav

- Slutför stegen i [Komma igång med anpassade principer i Azure Active Directory B2C](custom-policy-get-started.md).
- LinkedIn-konto - Om du inte redan har ett, [skapa ett konto](https://www.linkedin.com/start/join).
- LinkedIn-sida - Du behöver en [LinkedIn-sida](https://www.linkedin.com/help/linkedin/answer/710/creating-a-linkedin-company-page) för att associera med Det LinkedIn-program som du skapar i nästa avsnitt.

## <a name="create-an-application"></a>Skapa ett program

Om du vill använda LinkedIn som identitetsprovider i Azure AD B2C måste du skapa ett LinkedIn-program.

### <a name="create-app"></a>Skapa app

1. Logga in på [LinkedIn-programhanteringswebbplatsen](https://www.linkedin.com/secure/developer?newapp=) med dina LinkedIn-kontouppgifter.
1. Välj **Skapa app**.
1. Ange ett **appnamn**.
1. Ange ett **företagsnamn** som motsvarar ett LinkedIn-sidnamn. Skapa en LinkedIn-sida om du inte redan har en.
1. (Valfritt) Ange en **webbadress för sekretesspolicy**. Det måste vara en giltig URL, men behöver inte vara en slutpunkt som kan nås.
1. Ange ett **företagsmeddelande**.
1. Ladda upp en **applogotypbild.** Logotypbilden måste vara fyrkantig och dess mått måste vara minst 100 x 100 pixlar.
1. Lämna standardinställningarna i avsnittet **Produkter.**
1. Granska informationen som presenteras i **juridiska termer**. Om du godkänner villkoren markerar du kryssrutan.
1. Välj **Skapa app**.

### <a name="configure-auth"></a>Konfigurera autentisering

1. Välj fliken **Auth.**
1. Registrera **klient-ID**.
1. Avslöja och spela in **klienthemligheten**.
1. Lägg till följande **omdirigerings-URL**under **OAuth 2.0.** Ersätt `your-tenant` med namnet på din klient. Använd **alla gemener** för klientnamnet även om det definieras med versaler i Azure AD B2C.

    `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/oauth2/authresp`

## <a name="create-a-policy-key"></a>Skapa en principnyckel

Du måste lagra klienthemligheten som du tidigare har registrerat i din Azure AD B2C-klientorganisation.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Kontrollera att du använder katalogen som innehåller din Azure AD B2C-klient. Välj **katalog + prenumerationsfilter** i den övre menyn och välj den katalog som innehåller din klient.
3. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
4. På sidan Översikt väljer du **Identity Experience Framework**.
5. Välj **Principnycklar** och välj sedan **Lägg till**.
6. För **Alternativ** `Manual`väljer du .
7. Ange ett **namn** för principnyckeln. Till exempel `LinkedInSecret`. Prefixet *B2C_1A_* läggs till automatiskt i namnet på nyckeln.
8. I **Secret**anger du klienthemligheten som du tidigare spelat in.
9. För **nyckelanvändning** `Signature`väljer du .
10. Klicka på **Skapa**.

## <a name="add-a-claims-provider"></a>Lägga till en anspråksleverantör

Om du vill att användare ska logga in med ett LinkedIn-konto måste du definiera kontot som en anspråksleverantör som Azure AD B2C kan kommunicera med via en slutpunkt. Slutpunkten innehåller en uppsättning anspråk som används av Azure AD B2C för att verifiera att en viss användare har autentiserats.

Definiera ett LinkedIn-konto som en anspråksleverantör genom att lägga till det i elementet **ClaimsProviders** i principets tilläggsfil.

1. Öppna filen *SocialAndLocalAccounts/**TrustFrameworkExtensions.xml*** i redigeraren. Den här filen finns i [startpaketet för anpassade princip som][starter-pack] du hämtade som en del av en av förutsättningarna.
1. Leta reda på elementet **ClaimsProviders.** Om den inte finns lägger du till den under rotelementet.
1. Lägg till en ny **ClaimsProvider** enligt följande:

    ```xml
    <ClaimsProvider>
      <Domain>linkedin.com</Domain>
      <DisplayName>LinkedIn</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LinkedIn-OAUTH">
          <DisplayName>LinkedIn</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">linkedin</Item>
            <Item Key="authorization_endpoint">https://www.linkedin.com/oauth/v2/authorization</Item>
            <Item Key="AccessTokenEndpoint">https://www.linkedin.com/oauth/v2/accessToken</Item>
            <Item Key="ClaimsEndpoint">https://api.linkedin.com/v2/me</Item>
            <Item Key="scope">r_emailaddress r_liteprofile</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="external_user_identity_claim_id">id</Item>
            <Item Key="BearerTokenTransmissionMethod">AuthorizationHeader</Item>
            <Item Key="ResolveJsonPathsInJsonTokens">true</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="client_id">Your LinkedIn application client ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_LinkedInSecret" />
          </CryptographicKeys>
          <InputClaims />
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="id" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName.localized" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName.localized" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="linkedin.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="ExtractGivenNameFromLinkedInResponse" />
            <OutputClaimsTransformation ReferenceId="ExtractSurNameFromLinkedInResponse" />
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Ersätt värdet **för client_id** med klient-ID för LinkedIn-programmet som du tidigare har registrerat.
1. Spara filen.

### <a name="add-the-claims-transformations"></a>Lägg till anspråksomvandlingarna

Den tekniska profilen LinkedIn kräver att **anspråkstransformationerna ExtractGivenNameFromLinkedInResponse** och **ExtractSurNameFromLinkedInResponse** läggs till i listan över claimstransformationer. Om du inte har ett **ClaimsTransformations-element** som definierats i filen lägger du till de överordnade XML-elementen enligt nedan. Anspråksomvandlingarna behöver också en ny anspråkstyp som definieras med namnet **nullStringClaim**.

Lägg till **elementet BuildingBlocks** högst upp i filen *TrustFrameworkExtensions.xml.* Se *TrustFrameworkBase.xml* för ett exempel.

```XML
<BuildingBlocks>
  <ClaimsSchema>
    <!-- Claim type needed for LinkedIn claims transformations -->
    <ClaimType Id="nullStringClaim">
      <DisplayName>nullClaim</DisplayName>
      <DataType>string</DataType>
      <AdminHelpText>A policy claim to store output values from ClaimsTransformations that aren't useful. This claim should not be used in TechnicalProfiles.</AdminHelpText>
      <UserHelpText>A policy claim to store output values from ClaimsTransformations that aren't useful. This claim should not be used in TechnicalProfiles.</UserHelpText>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <!-- Claim transformations needed for LinkedIn technical profile -->
    <ClaimsTransformation Id="ExtractGivenNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="ExtractSurNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="surname" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="surname" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

### <a name="upload-the-extension-file-for-verification"></a>Ladda upp tilläggsfilen för verifiering

Du har nu konfigurerat en princip så att Azure AD B2C vet hur du kommunicerar med ditt LinkedIn-konto. Prova att ladda upp förlängningsfilen för din policy för att bekräfta att den inte har några problem hittills.

1. På sidan **Anpassade principer** i din Azure AD B2C-klient väljer du **Överför princip**.
2. Aktivera **Skriv över principen om den finns**och bläddra sedan till och välj filen *TrustFrameworkExtensions.xml.*
3. Klicka på **Överför**.

## <a name="register-the-claims-provider"></a>Registrera anspråksleverantören

Nu har identitetsprovidern konfigurerats, men den är inte tillgänglig på någon av inloggnings- eller inloggningsskärmarna. Om du vill göra den tillgänglig skapar du en dubblett av en befintlig mallanvändarresa och ändrar den så att den också har LinkedIn-identitetsprovidern.

1. Öppna filen *TrustFrameworkBase.xml* i startpaketet.
2. Leta reda på och kopiera hela innehållet `Id="SignUpOrSignIn"`i **UserJourney-elementet** som innehåller .
3. Öppna *TrustFrameworkExtensions.xml* och leta reda på **Elementet UserJourneys.** Om elementet inte finns lägger du till ett.
4. Klistra in hela innehållet i **UserJourney-elementet** som du kopierade som underordnade **elementet UserJourneys.**
5. Byt namn på ID för användarens färd. Till exempel `SignUpSignInLinkedIn`.

### <a name="display-the-button"></a>Visa knappen

Elementet **ClaimsProviderSelection** är analogt med en identitetsleverantörsknapp på en inloggnings- eller inloggningsskärm. Om du lägger till ett **ClaimsProviderSelection-element** för ett LinkedIn-konto visas en ny knapp när en användare hamnar på sidan.

1. Leta reda på **orchestrationStep-elementet** som ingår `Order="1"` i användarfärden som du skapade.
2. Lägg till följande element under **ClaimsProviderSelections.** Ange värdet för **TargetClaimsExchangeId** till ett `LinkedInExchange`lämpligt värde, till exempel:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Länka knappen till en åtgärd

Nu när du har en knapp på plats måste du länka den till en åtgärd. Åtgärden, i det här fallet, är för Azure AD B2C att kommunicera med ett LinkedIn-konto för att ta emot en token.

1. Hitta **OrchestrationStep** som `Order="2"` ingår i användarfärden.
2. Lägg till följande **ClaimsExchange-element** och se till att du använder samma värde för ID:et som du använde för **TargetClaimsExchangeId:**

    ```XML
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAUTH" />
    ```

    Uppdatera värdet för **TechnicalProfileReferenceId** till ID för den tekniska profil som du skapade tidigare. Till exempel `LinkedIn-OAUTH`.

3. Spara *filen TrustFrameworkExtensions.xml* och ladda upp den igen för verifiering.

## <a name="create-an-azure-ad-b2c-application"></a>Skapa ett Azure AD B2C-program

Kommunikation med Azure AD B2C sker via ett program som du registrerar i din B2C-klientorganisation. I det här avsnittet visas valfria steg som du kan slutföra för att skapa ett testprogram om du inte redan har gjort det.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Uppdatera och testa den förlitande partfilen

Uppdatera den förlitande partens (RP) fil som initierar användarens färd som du skapade.

1. Gör en kopia av *SignUpOrSignIn.xml* i arbetskatalogen och byt namn på den. Byt till exempel namn på den till *SignUpSignInLinkedIn.xml*.
2. Öppna den nya filen och uppdatera värdet för **Attributet PolicyId** för **TrustFrameworkPolicy** med ett unikt värde. Till exempel `SignUpSignInLinkedIn`.
3. Uppdatera värdet för **PublicPolicyUri** med URI för principen. Exempel:`http://contoso.com/B2C_1A_signup_signin_linkedin`
4. Uppdatera värdet för **Attributet ReferenceId** i **DefaultUserJourney** så att det matchar ID:t för den nya användarresan som du skapade (SignUpSignLinkedIn).
5. Spara ändringarna, ladda upp filen och välj sedan den nya principen i listan.
6. Kontrollera att Azure AD B2C-program som du har skapat har valts i fältet **Välj program** och testa det sedan genom att klicka på **Kör nu**.

## <a name="migration-from-v10-to-v20"></a>Migrering från v1.0 till v2.0

LinkedIn uppdaterade nyligen [sina API:er från v1.0 till v2.0](https://engineering.linkedin.com/blog/2018/12/developer-program-updates). Om du vill migrera den befintliga konfigurationen till den nya konfigurationen använder du informationen i följande avsnitt för att uppdatera elementen i den tekniska profilen.

### <a name="replace-items-in-the-metadata"></a>Ersätta objekt i metadata

Uppdatera följande **objektelement** i det befintliga **metadataelementet** i **TechnicalProfile:**

```XML
<Item Key="ClaimsEndpoint">https://api.linkedin.com/v1/people/~:(id,first-name,last-name,email-address,headline)</Item>
<Item Key="scope">r_emailaddress r_basicprofile</Item>
```

Till:

```XML
<Item Key="ClaimsEndpoint">https://api.linkedin.com/v2/me</Item>
<Item Key="scope">r_emailaddress r_liteprofile</Item>
```

### <a name="add-items-to-the-metadata"></a>Lägga till objekt i metadata

Lägg till följande **objektelement** i **Metadata** för **TechnicalProfile:**

```XML
<Item Key="external_user_identity_claim_id">id</Item>
<Item Key="BearerTokenTransmissionMethod">AuthorizationHeader</Item>
<Item Key="ResolveJsonPathsInJsonTokens">true</Item>
```

### <a name="update-the-outputclaims"></a>Uppdatera OutputClaims

I de befintliga **OutputClaims** av **TechnicalProfile**uppdaterar du följande **OutputClaim-element** från:

```XML
<OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
<OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
```

Till:

```XML
<OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName.localized" />
<OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName.localized" />
```

### <a name="add-new-outputclaimstransformation-elements"></a>Lägga till nya OutputClaimsTransformation-element

I **Utdataknãmtransformationerna** av **TechnicalProfile**lägger du till följande **OutputClaimsTransformation-element:**

```XML
<OutputClaimsTransformation ReferenceId="ExtractGivenNameFromLinkedInResponse" />
<OutputClaimsTransformation ReferenceId="ExtractSurNameFromLinkedInResponse" />
```

### <a name="define-the-new-claims-transformations-and-claim-type"></a>Definiera nya anspråksomvandlingar och anspråkstyp

I det sista steget har du lagt till nya anspråksomvandlingar som måste definieras. Om du vill definiera anspråksomvandlingarna lägger du till dem i listan över **claimstransformationer**. Om du inte har ett **ClaimsTransformations-element** som definierats i filen lägger du till de överordnade XML-elementen enligt nedan. Anspråksomvandlingarna behöver också en ny anspråkstyp som definieras med namnet **nullStringClaim**.

Elementet **BuildingBlocks** ska läggas till högst upp i filen. Se *TrustframeworkBase.xml* som ett exempel.

```XML
<BuildingBlocks>
  <ClaimsSchema>
    <!-- Claim type needed for LinkedIn claims transformations -->
    <ClaimType Id="nullStringClaim">
      <DisplayName>nullClaim</DisplayName>
      <DataType>string</DataType>
      <AdminHelpText>A policy claim to store unuseful output values from ClaimsTransformations. This claim should not be used in a TechnicalProfiles.</AdminHelpText>
      <UserHelpText>A policy claim to store unuseful output values from ClaimsTransformations. This claim should not be used in a TechnicalProfiles.</UserHelpText>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <!-- Claim transformations needed for LinkedIn technical profile -->
    <ClaimsTransformation Id="ExtractGivenNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="ExtractSurNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="surname" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="surname" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

### <a name="obtain-an-email-address"></a>Skaffa en e-postadress

Som en del av LinkedIn-migreringen från v1.0 till v2.0 krävs ytterligare ett anrop till ett annat API för att få e-postadressen. Om du behöver skaffa e-postadressen under registreringen gör du följande:

1. Följ stegen ovan för att låta Azure AD B2C federera med LinkedIn så att användaren kan logga in. Som en del av federationen tar Azure AD B2C emot åtkomsttoken för LinkedIn.
2. Spara LinkedIn-åtkomsttoken i ett anspråk. [Se instruktionerna här](idp-pass-through-custom.md).
3. Lägg till följande anspråksprovider som gör `/emailAddress` begäran till LinkedIns API. För att godkänna den här begäran behöver du LinkedIn-åtkomsttoken.

    ```XML
    <ClaimsProvider>
      <DisplayName>REST APIs</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="API-LinkedInEmail">
          <DisplayName>Get LinkedIn email</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
              <Item Key="ServiceUrl">https://api.linkedin.com/v2/emailAddress?q=members&amp;projection=(elements*(handle~))</Item>
              <Item Key="AuthenticationType">Bearer</Item>
              <Item Key="UseClaimAsBearerToken">identityProviderAccessToken</Item>
              <Item Key="SendClaimsIn">Url</Item>
              <Item Key="ResolveJsonPathsInJsonTokens">true</Item>
          </Metadata>
          <InputClaims>
              <InputClaim ClaimTypeReferenceId="identityProviderAccessToken" />
          </InputClaims>
          <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="elements[0].handle~.emailAddress" />
          </OutputClaims>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. Lägg till följande orchestration-steg i användarfärden, så att API-anspråksprovidern utlöses när en användare loggar in med LinkedIn. Se till att `Order` uppdatera numret på rätt sätt. Lägg till det här steget direkt efter orchestration-steget som utlöser den tekniska profilen LinkedIn.

    ```XML
    <!-- Extra step for LinkedIn to get the email -->
    <OrchestrationStep Order="3" Type="ClaimsExchange">
      <Preconditions>
        <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
          <Value>identityProvider</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="false">
          <Value>identityProvider</Value>
          <Value>linkedin.com</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <ClaimsExchanges>
        <ClaimsExchange Id="GetEmail" TechnicalProfileReferenceId="API-LinkedInEmail" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ```

Det är valfritt att få e-postadressen från LinkedIn under registreringen. Om du väljer att inte skaffa e-postmeddelandet från LinkedIn men behöver ett under registreringen måste användaren ange e-postadressen manuellt och validera den.

Ett fullständigt exempel på en princip som använder LinkedIn-identitetsprovidern finns i [Startpaketet för anpassad princip](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/linkedin-identity-provider).

<!-- Links - EXTERNAL -->
[starter-pack]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
