---
title: Ställ in logga in med LinkedIn-konto med hjälp av anpassade principer – Azure Active Directory B2C | Microsoft Docs
description: Ställ in logga in med ett LinkedIn-konto i Azure Active Directory B2C med anpassade principer.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 5bf1126a7015e668f3770835535b81c93d01cbda
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60387085"
---
# <a name="set-up-sign-in-with-a-linkedin-account-using-custom-policies-in-azure-active-directory-b2c"></a>Ställ in logga in med ett LinkedIn-konto med hjälp av anpassade principer i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den här artikeln visar hur du aktiverar inloggning för användare från en LinkedIn-konto med hjälp av [anpassade principer](active-directory-b2c-overview-custom.md) i Azure Active Directory (Azure AD) B2C.

## <a name="prerequisites"></a>Nödvändiga komponenter

- Utför stegen i [Kom igång med anpassade principer i Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).
- Om du inte redan har en LinkedIn-konto, skapa en på den [LinkedIn registreringssidan](https://www.linkedin.com/start/join).
- Ett LinkedIn-program måste du ange en 80 X 80 pixel logotyp för att representera ditt program.

## <a name="create-an-application"></a>Skapa ett program

Om du vill använda LinkedIn som en identitetsprovider i Azure AD B2C måste du skapa ett LinkedIn-program.

1. Logga in på den [LinkedIn programhantering](https://www.linkedin.com/secure/developer?newapp=) webbplatsen med autentiseringsuppgifterna för ditt LinkedIn-konto.
2. Välj **skapa program**.
3. Ange din **företagsnamn**, en **programnamn**, och en **Programbeskrivning**.
4. Ladda upp den **Programlogotyp** som du skapade.
5. Välj en **användningen av** i listan.
6. För **Webbadress**, ange `https://your-tenant.b2clogin.com`.  Ersätt `your-tenant` med namnet på din Azure AD B2C-klient. Till exempel contoso.b2clogin.com.
7. Ange din **företags-e-** adress och **Företagstelefon** tal.
8. Längst ned på sidan läser och godkänner användningsvillkoren och välj sedan **skicka**.
9. Välj **autentisering**, och registrera den **klient-ID** och **Klienthemlighet** värden som ska användas senare.
10. I **behörighet omdirigerings-URL: er**, ange `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/oauth2/authresp`. Ersätt `your-tenant` med namnet på din klient. Du måste använda gemener när du anger ditt klientnamn även om klienten har definierats med versaler i Azure AD B2C. 
11. Välj **Uppdatera**.
12. Välj **inställningar**, ändra den **programstatus** till **Live**, och välj sedan **uppdatering**.

## <a name="create-a-policy-key"></a>Skapa en principnyckel

Du behöver lagra klienthemlighet som tidigare registrerades i din Azure AD B2C-klient.

1. Logga in på [Azure Portal](https://portal.azure.com/).
2. Se till att du använder den katalog som innehåller din Azure AD B2C-klientorganisation genom att klicka på **katalog- och prenumerationsfiltret** på den översta menyn och välja katalogen som innehåller din klientorganisation.
3. Välj **Alla tjänster** på menyn uppe till vänster i Azure Portal. Sök sedan efter och välj **Azure AD B2C**.
4. På sidan Översikt väljer **Identitetsramverk**.
5. Välj **Principnycklar** och välj sedan **Lägg till**.
6. För **alternativ**, Välj `Manual`.
7. Ange en **namn** för principnyckeln. Till exempel `LinkedInSecret`. Prefixet `B2C_1A_` läggs automatiskt till namnet på din nyckel.
8. I **hemlighet**, ange dina klienthemlighet som du antecknade tidigare.
9. För **nyckelanvändning**väljer `Signature`.
10. Klicka på **Skapa**.

## <a name="add-a-claims-provider"></a>Lägg till en anspråksprovider

Om du vill att användarna ska logga in med LinkedIn-konto, måste du definiera kontot som en anspråksprovider som Azure AD B2C kan kommunicera med via en slutpunkt. Slutpunkten som innehåller en uppsättning anspråk som används av Azure AD B2C för att verifiera att en viss användare har autentiserats. 

Du kan definiera en LinkedIn-konto som en anspråksprovider genom att lägga till den **ClaimsProviders** elementet i tilläggsfilen av din princip.

1. Öppna den *TrustFrameworkExtensions.xml*.
2. Hitta den **ClaimsProviders** element. Om det inte finns, lägger du till det under rotelementet.
3. Lägga till en ny **ClaimsProvider** på följande sätt:

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
            <Item Key="external_user_identity_claim_id">id</Item>
            <Item Key="BearerTokenTransmissionMethod">AuthorizationHeader</Item>
            <Item Key="ResolveJsonPathsInJsonTokens">true</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="client_id">Your LinkedIn application client ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_LinkedInSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="id" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="linkedin.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
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

4. Ersätt värdet för **client_id** med klient-ID som du antecknade tidigare.
5. Spara filen.

### <a name="add-the-claims-transformations"></a>Lägg till anspråksomvandlingar

Den tekniska profilen LinkedIn kräver den **ExtractGivenNameFromLinkedInResponse** och **ExtractSurNameFromLinkedInResponse** anspråk transformationer som ska läggas till i listan över ClaimsTransformations. Om du inte har en **ClaimsTransformations** elementet har definierats i filen, lägga till överordnad XML-element som visas nedan. Anspråksomvandlingar även behovet av en ny Anspråkstyp definierats med namnet **nullStringClaim**. 

Den **BuildingBlocks** elementet ska läggas till i den övre delen av filen. Se den *TrustframeworkBase.xml* som exempel.

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
### <a name="upload-the-extension-file-for-verification"></a>Ladda upp tilläggsfilen med för verifiering

Nu bör har du konfigurerat principen så att Azure AD B2C vet att kommunicera med ditt LinkedIn-konto. Försök att överföra tilläggsfilen i din princip bara för att bekräfta att den inte har några problem hittills.

1. På den **anpassade principer** sidan i din Azure AD B2C-klient väljer **ladda upp principen**.
2. Aktivera **Skriv över principen om den finns**, och sedan bläddra till och markera den *TrustFrameworkExtensions.xml* fil.
3. Klicka på **Överför**.

## <a name="register-the-claims-provider"></a>Registrera anspråksprovidern

Nu identitetsprovidern har ställts in, men det finns inte i någon av skärmarna registrering eller inloggning. Du skapar en dubblett av en befintlig mall för användarresa så att den blir tillgänglig, och ändra den så att den har också LinkedIn-identitetsprovider.

1. Öppna den *TrustFrameworkBase.xml* filen från startpaket.
2. Hitta och kopiera hela innehållet i den **UserJourney** element som innehåller `Id="SignUpOrSignIn"`.
3. Öppna den *TrustFrameworkExtensions.xml* och hitta den **UserJourneys** element. Om elementet inte finns kan du lägga till en.
4. Klistra in hela innehållet i den **UserJourney** element som du kopierade som underordnad till den **UserJourneys** element.
5. Byt namn på ID för användarresa. Till exempel `SignUpSignInLinkedIn`.

### <a name="display-the-button"></a>Visa knappen

Den **ClaimsProviderSelection** element är detsamma som en knapp med identity-providern på en skärm för registrering eller inloggning. Om du lägger till en **ClaimsProviderSelection** element för ett LinkedIn-konto, en ny knapp visas när en användare finns på sidan.

1. Hitta den **OrchestrationStep** element som innehåller `Order="1"` i användarresan som du skapade.
2. Under **ClaimsProviderSelects**, lägger du till följande element. Ange värdet för **TargetClaimsExchangeId** till ett lämpligt värde, till exempel `LinkedInExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Länka knappen till en åtgärd

Nu när du har en knapp på plats kan behöva du länka den till en åtgärd. Åtgärden, i det här fallet är för Azure AD B2C att kommunicera med ett LinkedIn-konto för att ta emot en token.

1. Hitta den **OrchestrationStep** som innehåller `Order="2"` i användarresan.
2. Lägg till följande **ClaimsExchange** element att se till att du använder samma värde för det ID som du använde för **TargetClaimsExchangeId**:

    ```XML
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAUTH" />
    ```
    
    Uppdatera värdet för **TechnicalProfileReferenceId** -ID: t för den tekniska profilen du skapade tidigare. Till exempel `LinkedIn-OAUTH`.

3. Spara den *TrustFrameworkExtensions.xml* fil och ladda upp den igen för att bekräfta.

## <a name="create-an-azure-ad-b2c-application"></a>Skapa ett Azure AD B2C-program

Kommunikation med Azure AD B2c sker via ett program som du skapar i din klient. Det här avsnittet innehåller valfria steg som du kan utföra för att skapa ett testprogram om du inte redan gjort det.

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Kontrollera att du använder den katalog som innehåller din Azure AD B2C-klient. Välj den **katalog- och prenumerationsfilter** på den översta menyn och välj den katalog som innehåller din klient.
3. Välj **Alla tjänster** på menyn uppe till vänster i Azure Portal. Sök sedan efter och välj **Azure AD B2C**.
4. Välj **Program** och därefter **Lägg till**.
5. Ange ett namn för programmet, till exempel *testapp1*.
6. För **Webbapp / webb-API**väljer `Yes`, och ange sedan `https://jwt.ms` för den **svars-URL**.
7. Klicka på **Skapa**.

## <a name="update-and-test-the-relying-party-file"></a>Uppdatera och testa filen förlitande part

Uppdatera filen för förlitande part (RP) som initierar användarresa som du skapade.

1. Skapa en kopia av *SignUpOrSignIn.xml* i din arbetskatalog och Byt namn på den. Exempel: Byt namn på den till *SignUpSignInLinkedIn.xml*.
2. Öppna den nya filen och uppdatera värdet för den **PolicyId** attributet för **TrustFrameworkPolicy** med ett unikt värde. Till exempel `SignUpSignInLinkedIn`.
3. Uppdatera värdet för **PublicPolicyUri** med URI: N för principen. Exempel:`http://contoso.com/B2C_1A_signup_signin_linkedin`
4. Uppdatera värdet för den **ReferenceId** attributet i **DefaultUserJourney** att matcha ID för den nya användarresa som du skapade (SignUpSignLinkedIn).
5. Spara dina ändringar, överföra filen och välj sedan den nya principen i listan.
6. Se till att Azure AD B2C-program som du skapat är markerad i den **Välj program** fältet och sedan testa den genom att klicka på **kör nu**.


## <a name="register-the-claims-provider"></a>Registrera anspråksprovidern

Nu identitetsprovidern har ställts in, men det finns inte i någon av skärmarna registrering eller inloggning. Du skapar en dubblett av en befintlig mall för användarresa så att den blir tillgänglig, och ändra den så att den har också LinkedIn-identitetsprovider.

1. Öppna den *TrustFrameworkBase.xml* filen från startpaket.
2. Hitta och kopiera hela innehållet i den **UserJourney** element som innehåller `Id="SignUpOrSignIn"`.
3. Öppna den *TrustFrameworkExtensions.xml* och hitta den **UserJourneys** element. Om elementet inte finns kan du lägga till en.
4. Klistra in hela innehållet i den **UserJourney** element som du kopierade som underordnad till den **UserJourneys** element.
5. Byt namn på ID för användarresa. Till exempel `SignUpSignInLinkedIn`.

### <a name="display-the-button"></a>Visa knappen

Den **ClaimsProviderSelection** element är detsamma som en knapp med identity-providern på en skärm för registrering eller inloggning. Om du lägger till en **ClaimsProviderSelection** element för ett LinkedIn-konto, en ny knapp visas när en användare finns på sidan.

1. Hitta den **OrchestrationStep** element som innehåller `Order="1"` i användarresan som du skapade.
2. Under **ClaimsProviderSelects**, lägger du till följande element. Ange värdet för **TargetClaimsExchangeId** till ett lämpligt värde, till exempel `LinkedInExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Länka knappen till en åtgärd

Nu när du har en knapp på plats kan behöva du länka den till en åtgärd. Åtgärden, i det här fallet är för Azure AD B2C att kommunicera med ett LinkedIn-konto för att ta emot en token.

1. Hitta den **OrchestrationStep** som innehåller `Order="2"` i användarresan.
2. Lägg till följande **ClaimsExchange** element att se till att du använder samma värde för det ID som du använde för **TargetClaimsExchangeId**:

    ```XML
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAUTH" />
    ```
    
    Uppdatera värdet för **TechnicalProfileReferenceId** -ID: t för den tekniska profilen du skapade tidigare. Till exempel `LinkedIn-OAUTH`.

3. Spara den *TrustFrameworkExtensions.xml* fil och ladda upp den igen för att bekräfta.

## <a name="create-an-azure-ad-b2c-application"></a>Skapa ett Azure AD B2C-program

Kommunikation med Azure AD B2c sker via ett program som du skapar i din klient. Det här avsnittet innehåller valfria steg som du kan utföra för att skapa ett testprogram om du inte redan gjort det.

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Kontrollera att du använder den katalog som innehåller din Azure AD B2C-klient. Välj den **katalog- och prenumerationsfilter** på den översta menyn och välj den katalog som innehåller din klient.
3. Välj **Alla tjänster** på menyn uppe till vänster i Azure Portal. Sök sedan efter och välj **Azure AD B2C**.
4. Välj **Program** och därefter **Lägg till**.
5. Ange ett namn för programmet, till exempel *testapp1*.
6. För **Webbapp / webb-API**väljer `Yes`, och ange sedan `https://jwt.ms` för den **svars-URL**.
7. Klicka på **Skapa**.

## <a name="update-and-test-the-relying-party-file"></a>Uppdatera och testa filen förlitande part

Uppdatera filen för förlitande part (RP) som initierar användarresa som du skapade.

1. Skapa en kopia av *SignUpOrSignIn.xml* i din arbetskatalog och Byt namn på den. Exempel: Byt namn på den till *SignUpSignInLinkedIn.xml*.
2. Öppna den nya filen och uppdatera värdet för den **PolicyId** attributet för **TrustFrameworkPolicy** med ett unikt värde. Till exempel `SignUpSignInLinkedIn`.
3. Uppdatera värdet för **PublicPolicyUri** med URI: N för principen. Exempel:`http://contoso.com/B2C_1A_signup_signin_linkedin`
4. Uppdatera värdet för den **ReferenceId** attributet i **DefaultUserJourney** att matcha ID för den nya användarresa som du skapade (SignUpSignLinkedIn).
5. Spara dina ändringar, överföra filen och välj sedan den nya principen i listan.
6. Se till att Azure AD B2C-program som du skapat är markerad i den **Välj program** fältet och sedan testa den genom att klicka på **kör nu**.


## <a name="migration-from-v10-to-v20"></a>Migrering från v1.0 till v2.0

LinkedIn nyligen [uppdaterat sina API: er från v1.0 till v2.0](https://engineering.linkedin.com/blog/2018/12/developer-program-updates). Om du vill migrera din befintliga konfiguration för den nya konfigurationen, Använd informationen i följande avsnitt för att uppdatera elementen i den tekniska profilen.

### <a name="replace-items-in-the-metadata"></a>Ersätta dem i Metadata

I den befintliga **Metadata** elementet i den **TechnicalProfile**, uppdatera följande **objekt** element från:

```XML
<Item Key="ClaimsEndpoint">https://api.linkedin.com/v1/people/~:(id,first-name,last-name,email-address,headline)</Item>
<Item Key="scope">r_emailaddress r_basicprofile</Item>
```

Till:

```XML
<Item Key="ClaimsEndpoint">https://api.linkedin.com/v2/me</Item>
<Item Key="scope">r_emailaddress r_liteprofile</Item>
```

### <a name="add-items-to-the-metadata"></a>Lägg till objekt i Metadata

I den **Metadata** av den **TechnicalProfile**, Lägg till följande **objekt** element:

```XML
<Item Key="external_user_identity_claim_id">id</Item>
<Item Key="BearerTokenTransmissionMethod">AuthorizationHeader</Item>
<Item Key="ResolveJsonPathsInJsonTokens">true</Item>
```

### <a name="update-the-outputclaims"></a>Uppdatera OutputClaims

I den befintliga **OutputClaims** av den **TechnicalProfile**, uppdatera följande **OutputClaim** element från:

```XML
<OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
<OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
```

Till:

```XML
<OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName.localized" />
<OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName.localized" />
```

### <a name="add-new-outputclaimstransformation-elements"></a>Lägga till nya OutputClaimsTransformation element

I den **OutputClaimsTransformations** av den **TechnicalProfile**, Lägg till följande **OutputClaimsTransformation** element:

```XML
<OutputClaimsTransformation ReferenceId="ExtractGivenNameFromLinkedInResponse" />
<OutputClaimsTransformation ReferenceId="ExtractSurNameFromLinkedInResponse" />
```

### <a name="define-the-new-claims-transformations-and-claim-type"></a>Definiera nya anspråksomvandlingar och Anspråkstyp

Du lagt till nya anspråksomvandlingar som måste definieras i det sista steget. För att definiera de anspråk transformeringar, lägga till dem i listan över **ClaimsTransformations**. Om du inte har en **ClaimsTransformations** elementet har definierats i filen, lägga till överordnad XML-element som visas nedan. Anspråksomvandlingar även behovet av en ny Anspråkstyp definierats med namnet **nullStringClaim**. 

Den **BuildingBlocks** elementet ska läggas till i den övre delen av filen. Se den *TrustframeworkBase.xml* som exempel.

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

### <a name="obtain-an-email-address"></a>Hämta en e-postadress

Som en del av LinkedIn-migrering från v1.0 till v2.0 krävs ett extra anrop till en annan API för att hämta e-postadressen. Om du vill få e-postadress under registreringen, gör du följande:

1. Har Azure AD B2C federera med LinkedIn så att användarna loggar in. När det händer så skickas den åtkomst-token från LinkedIn till Azure AD B2C.
2. Spara LinkedIn-åtkomsttoken i ett anspråk. [Se anvisningarna här](idp-pass-through-custom.md).
3. Anropa en Azure-funktion och skicka funktionen åtkomsttoken som samlats in i föregående steg. [Se anvisningarna här](active-directory-b2c-rest-api-step-custom.md)
    1. Azure-funktionen ska ta åtkomsttoken och göra ett anrop till LinkedIn-API (`https://api.linkedin.com/v2/emailAddress?q=members&projection=(elements*(handle~))`).
    2. Azure-funktionen tar svaret och analyserar den e-postadressen.
    3. E-postadressen returneras till principen.
4. E-postadressen lagras i anspråket e-post och användarresa fortsätter på.

Det är valfritt att hämta e-postadressen från LinkedIn under registreringen. Om du väljer att inte få e-postmeddelandet, krävs för användaren att manuellt ange den e-postadressen och verifiera den.
