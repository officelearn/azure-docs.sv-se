---
title: Skydda en vilsam tjänst i din Azure AD B2C
titleSuffix: Azure AD B2C
description: Skydda dina anpassade REST API-anspråksutbyten i din Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5a80c6e3bd8cf647590ed757c042ef3301e27b4a
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743508"
---
# <a name="secure-your-restful-services"></a>Säkra dina RESTful-tjänster 

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

När du integrerar ett REST API i en Azure AD B2C-användarresa måste du skydda din REST API-slutpunkt med autentisering. Detta säkerställer att endast tjänster som har rätt autentiseringsuppgifter, till exempel Azure AD B2C, kan ringa till slutpunkten för REST API.

Lär dig hur du integrerar ett REST API i din Azure AD B2C-användarresa i [validera användarindata](custom-policy-rest-api-claims-validation.md) och [Lägg till REST API-anspråksutbyten i anpassade principer.](custom-policy-rest-api-claims-exchange.md)

I den här artikeln beskrivs hur du skyddar REST-API:et med antingen HTTP Basic, klientcertifikat eller OAuth2-autentisering. 

## <a name="prerequisites"></a>Krav

Gör stegen i någon av följande "Hur" guider:

- [Integrera REST API-anspråksutbyten i din Azure AD B2C-användares färd för att validera användarindata](custom-policy-rest-api-claims-validation.md).
- [Lägga till REST API-anspråksutbyten i anpassade principer](custom-policy-rest-api-claims-exchange.md)

## <a name="http-basic-authentication"></a>HTTP grundläggande autentisering

HTTP-grundläggande autentisering definieras i [RFC 2617](https://tools.ietf.org/html/rfc2617). Grundläggande autentisering fungerar på följande sätt: Azure AD B2C skickar en HTTP-begäran med klientautentiseringsuppgifterna i auktoriseringshuvudet. Autentiseringsuppgifterna är formaterade som den base64-kodade strängen "name:password".  

### <a name="add-rest-api-username-and-password-policy-keys"></a>Lägg till REST API-användarnamn och lösenordsprincipnycklar

Om du vill konfigurera en teknisk REST API-profil med HTTP Basic-autentisering skapar du följande kryptografiska nycklar för att lagra användarnamn och lösenord:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. Kontrollera att du använder katalogen som innehåller din Azure AD B2C-klient. Välj **katalog + prenumerationsfilter** i den övre menyn och välj din Azure AD B2C-katalog.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. På sidan Översikt väljer du **Identity Experience Framework**.
1. Välj **Principnycklar**och välj sedan **Lägg till**.
1. För **Alternativ**väljer du **Manuell**.
1. För **Namn**skriver du **RestApiUsername**.
    Prefixet *B2C_1A_* kan läggas till automatiskt.
1. Ange rest-API-användarnamnet i rutan **Hemligt.**
1. För **nyckelanvändning**väljer du **Kryptering**.
1. Välj **Skapa**.
1. Välj **Principnycklar** igen.
1. Välj **Lägg till**.
1. För **Alternativ**väljer du **Manuell**.
1. För **Namn**skriver du **RestApiPassword**.
    Prefixet *B2C_1A_* kan läggas till automatiskt.
1. Ange REST API-lösenordet i rutan **Hemligt.**
1. För **nyckelanvändning**väljer du **Kryptering**.
1. Välj **Skapa**.

### <a name="configure-your-rest-api-technical-profile-to-use-http-basic-authentication"></a>Konfigurera den tekniska profilen för REST API för att använda HTTP Basic-autentisering

När du har skapat de nödvändiga nycklarna konfigurerar du metadata för REST API-tekniska profil för att referera till autentiseringsuppgifterna.

1. Öppna principfilen för tilläggsprincipen i arbetskatalogen (TrustFrameworkExtensions.xml).
1. Sök efter den tekniska PROFILEN för REST API. Till `REST-ValidateProfile`exempel `REST-GetProfile`, eller .
1. Leta `<Metadata>` reda på elementet.
1. Ändra *AuthenticationType* `Basic`till .
1. Ändra *AllowInsecureAuthInProduction* `false`till .
1. Lägg till `</Metadata>` följande XML-kodavsnitt direkt efter stängningselementet:
    ```xml
    <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_RestApiUsername" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_RestApiPassword" />
    </CryptographicKeys>
    ```

Följande är ett exempel på en RESTful teknisk profil som konfigurerats med HTTP grundläggande autentisering:

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
        <Item Key="AuthenticationType">Basic</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_RestApiUsername" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_RestApiPassword" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="https-client-certificate-authentication"></a>Autentisering av HTTPS-klientcertifikat

Klientcertifikatautentisering är en ömsesidig certifikatbaserad autentisering, där klienten, Azure AD B2C, tillhandahåller sitt klientcertifikat till servern för att bevisa sin identitet. Detta sker som en del av SSL-handskakningen. Endast tjänster som har rätt certifikat, till exempel Azure AD B2C, kan komma åt din REST API-tjänst. Klientcertifikatet är ett digitalt X.509-certifikat. I produktionsmiljöer måste den signeras av en certifikatutfärdare.

### <a name="prepare-a-self-signed-certificate-optional"></a>Förbereda ett självsignerat certifikat (valfritt)

Om du inte redan har ett certifikat för icke-produktionsmiljöer kan du använda ett självsignerat certifikat. På Windows kan du använda PowerShells [cmdlet New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) för att generera ett certifikat.

1. Kör det här PowerShell-kommandot för att generera ett självsignerat certifikat. Ändra `-Subject` argumentet efter behov för ditt program och Azure AD B2C-klientnamn. Du kan också `-NotAfter` justera datumet för att ange ett annat förfallodatum för certifikatet.
    ```PowerShell
    New-SelfSignedCertificate `
        -KeyExportPolicy Exportable `
        -Subject "CN=yourappname.yourtenant.onmicrosoft.com" `
        -KeyAlgorithm RSA `
        -KeyLength 2048 `
        -KeyUsage DigitalSignature `
        -NotAfter (Get-Date).AddMonths(12) `
        -CertStoreLocation "Cert:\CurrentUser\My"
    ```    
1. Öppna **Hantera användarcertifikat** > **Aktuella personliga** > **Personal** > **användarcertifikat** > *yourappname.yourtenant.onmicrosoft.com*.
1. Markera certifikatet > **Åtgärd** > **Alla aktiviteter** > **Exportera**.
1. Välj **Ja** > **Nästa** > **Ja, exportera den privata nyckeln** > **Nästa**.
1. Acceptera standardvärdena för **Exportfilformat**.
1. Ange ett lösenord för certifikatet.

### <a name="add-a-client-certificate-policy-key"></a>Lägga till en klientcertifikatprincipnyckel

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. Kontrollera att du använder katalogen som innehåller din Azure AD B2C-klient. Välj **katalog + prenumerationsfilter** i den övre menyn och välj din Azure AD B2C-katalog.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. På sidan Översikt väljer du **Identity Experience Framework**.
1. Välj **Principnycklar**och välj sedan **Lägg till**.
1. Välj **Ladda upp**i rutan **Alternativ** .
1. Skriv **RestApiClientCertificate**i rutan **Namn** .
    Prefixet *B2C_1A_* läggs till automatiskt.
1. I rutan **Filöverföring** väljer du certifikatets PFX-fil med en privat nyckel.
1. Skriv lösenordet för certifikatet i rutan **Lösenord.**
1. Välj **Skapa**.

### <a name="configure-your-rest-api-technical-profile-to-use-client-certificate-authentication"></a>Konfigurera den tekniska profilen för REST API för att använda autentisering av klientcertifikat

När du har skapat den nödvändiga nyckeln konfigurerar du metadata för REST API-tekniska profil för att referera till klientcertifikatet.

1. Öppna principfilen för tilläggsprincipen i arbetskatalogen (TrustFrameworkExtensions.xml).
1. Sök efter den tekniska PROFILEN för REST API. Till `REST-ValidateProfile`exempel `REST-GetProfile`, eller .
1. Leta `<Metadata>` reda på elementet.
1. Ändra *AuthenticationType* `ClientCertificate`till .
1. Ändra *AllowInsecureAuthInProduction* `false`till .
1. Lägg till `</Metadata>` följande XML-kodavsnitt direkt efter stängningselementet:
    ```xml
    <CryptographicKeys>
       <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_RestApiClientCertificate" />
    </CryptographicKeys>
    ```

Följande är ett exempel på en RESTful teknisk profil som konfigurerats med ett HTTP-klientcertifikat:

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
        <Item Key="AuthenticationType">ClientCertificate</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_RestApiClientCertificate" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="oauth2-bearer-authentication"></a>OAuth2-bärares autentisering 

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

Autentisering av innehavartoken definieras i [OAuth2.0 Auktoriseringsram: Bärare tokenanvändning (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). I innehavartokenautentisering skickar Azure AD B2C en HTTP-begäran med en token i auktoriseringshuvudet.

```http
Authorization: Bearer <token>
```

En innehavartoken är en ogenomskinlig sträng. Det kan vara en JWT-åtkomsttoken eller någon sträng som REST API förväntar sig att Azure AD B2C ska skicka in auktoriseringshuvudet. Azure AD B2C stöder följande typer:

- **Bärare token**. För att kunna skicka innehavartoken i den vilsamma tekniska profilen måste din policy först hämta innehavartoken och sedan använda den i den tekniska PROFILEN RESTful.  
- **Statisk bärare token**. Använd den här metoden när REST API-åtkomsten utfärdar en långsiktig åtkomsttoken. Om du vill använda en statisk bärare token, skapa en principnyckel och göra en referens från RESTful teknisk profil till din principnyckel. 


## <a name="using-oauth2-bearer"></a>Använda OAuth2 Bearer  

Följande steg visar hur du använder klientautentiseringsuppgifter för att hämta en innehavartoken och skicka den till auktoriseringshuvudet för REST API-anrop.  

### <a name="define-a-claim-to-store-the-bearer-token"></a>Definiera ett anspråk för att lagra innehavartoken

Ett anspråk ger tillfällig lagring av data under en Azure AD B2C-principkörning. [Skadeschemat](claimsschema.md) är den plats där du deklarerar dina anspråk. Åtkomsttoken måste lagras i ett anspråk som ska användas senare. 

1. Öppna filen för tillägg i principen. Till exempel <em> `SocialAndLocalAccounts/` </em>.
1. Sök efter elementet [BuildingBlocks.](buildingblocks.md) Om elementet inte finns lägger du till det.
1. Leta reda på elementet [ClaimsSchema.](claimsschema.md) Om elementet inte finns lägger du till det.
1. Lägg till stadsbärarenToken i **elementet ClaimsSchema.**  

```xml
<ClaimType Id="bearerToken">
  <DisplayName>bearer token</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

### <a name="acquiring-an-access-token"></a>Skaffa en åtkomsttoken 

Du kan hämta en åtkomsttoken på ett av flera sätt: genom att hämta den [från en federerad identitetsprovider](idp-pass-through-custom.md)genom att anropa ett REST-API som returnerar en åtkomsttoken, med hjälp av ett [ROPC-flöde](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth-ropc)eller genom att använda [klientautentiseringsflödet](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow).  

I följande exempel används en teknisk REST API-profil för att göra en begäran till slutpunkten för Azure AD-token med hjälp av klientautentiseringsuppgifterna som skickas som HTTP-grundläggande autentisering. Information om hur du konfigurerar detta i Azure AD finns i [Microsofts identitetsplattform och flödet för OAuth 2.0-klientautentiseringsuppgifter](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow). Du kan behöva ändra detta för att samverka med din identitetsleverantör. 

För ServiceUrl ersätter du ditt klientnamn med namnet på din Azure AD-klientorganisation. Se [restful teknisk profilreferens](restful-technical-profile.md) för alla tillgängliga alternativ.

```xml
<TechnicalProfile Id="SecureREST-AccessToken">
  <DisplayName></DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://login.microsoftonline.com/your-tenant-name.microsoft.com/oauth2/v2.0/token</Item>
    <Item Key="AuthenticationType">Basic</Item>
     <Item Key="SendClaimsIn">Form</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_SecureRESTClientId" />
    <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_SecureRESTClientSecret" />
  </CryptographicKeys>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="grant_type" DefaultValue="client_credentials" />
    <InputClaim ClaimTypeReferenceId="scope" DefaultValue="https://secureb2cfunction.azurewebsites.net/.default" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="bearerToken" PartnerClaimType="access_token" />
  </OutputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

### <a name="change-the-rest-technical-profile-to-use-bearer-token-authentication"></a>Ändra den tekniska REST-profilen för att använda bäraretokenautentisering

Om du vill ha stöd för autentisering av innehavartoken i din anpassade princip ändrar du den tekniska PROFILEN för REST API med följande:

1. Öppna principfilen *TrustFrameworkExtensions.xml* i arbetskatalogen.
1. Sök efter `<TechnicalProfile>` noden `Id="REST-API-SignUp"`som innehåller .
1. Leta `<Metadata>` reda på elementet.
1. Ändra *AuthenticationType* till *Bärare*enligt följande:
    ```xml
    <Item Key="AuthenticationType">Bearer</Item>
    ```
1. Ändra eller lägg till *UseClaimAsBearerToken* till *bearerToken*enligt följande. *InnehavarenToken* är namnet på påståendet att innehavartoken kommer att hämtas `SecureREST-AccessToken`från (utdataanspråket från ).

    ```xml
    <Item Key="UseClaimAsBearerToken">bearerToken</Item>
    ```
    
1. Se till att du lägger till anspråk som används ovan som ett indataanspråk:

    ```xml
    <InputClaim ClaimTypeReferenceId="bearerToken"/>
    ```    

När du har lagt till ovanstående utdrag bör den tekniska profilen se ut så här:

```XML
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="UseClaimAsBearerToken">bearerToken</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="bearerToken"/>
      </InputClaims>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="using-a-static-oauth2-bearer"></a>Använda en statisk OAuth2-bärare 

### <a name="add-the-oauth2-bearer-token-policy-key"></a>Lägg till principnyckeln för OAuth2-innehavarsymbol

Skapa en principnyckel för att lagra innehavartokenvärdet.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. Kontrollera att du använder katalogen som innehåller din Azure AD B2C-klient. Välj **katalog + prenumerationsfilter** i den övre menyn och välj din Azure AD B2C-katalog.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. På sidan Översikt väljer du **Identity Experience Framework**.
1. Välj **Principnycklar**och välj sedan **Lägg till**.
1. För **Alternativ** `Manual`väljer du .
1. Ange ett **namn** för principnyckeln. Till exempel `RestApiBearerToken`. Prefixet `B2C_1A_` läggs automatiskt till i namnet på nyckeln.
1. I **Secret**anger du din klienthemlighet som du tidigare spelat in.
1. För **nyckelanvändning** `Encryption`väljer du .
1. Välj **Skapa**.

### <a name="configure-your-rest-api-technical-profile-to-use-the-bearer-token-policy-key"></a>Konfigurera den tekniska REST API-profilen så att den använder principnyckeln för innehavartoken

När du har skapat den nödvändiga nyckeln konfigurerar du metadata för REST API-tekniska profil för att referera till innehavartoken.

1. Öppna principfilen för tilläggsprincipen i arbetskatalogen (TrustFrameworkExtensions.xml).
1. Sök efter den tekniska PROFILEN för REST API. Till `REST-ValidateProfile`exempel `REST-GetProfile`, eller .
1. Leta `<Metadata>` reda på elementet.
1. Ändra *AuthenticationType* `Bearer`till .
1. Ändra *AllowInsecureAuthInProduction* `false`till .
1. Lägg till `</Metadata>` följande XML-kodavsnitt direkt efter stängningselementet:
    ```xml
    <CryptographicKeys>
       <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_RestApiBearerToken" />
    </CryptographicKeys>
    ```

Följande är ett exempel på en RESTful teknisk profil som konfigurerats med bärare token autentisering:

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
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_RestApiBearerToken" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="next-steps"></a>Nästa steg

- Läs mer om det [vilsamma tekniska profilelementet](restful-technical-profile.md) i IEF-referensen. 
