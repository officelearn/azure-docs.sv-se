---
title: Definiera en RESTful teknisk profil i en anpassad princip
titleSuffix: Azure AD B2C
description: Definiera en RESTful teknisk profil i en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 410f413fc8450c0ee33c3ca95e860a3e8de34107
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332614"
---
# <a name="define-a-restful-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definiera en RESTful teknisk profil i en azure Active Directory B2C-anpassad princip

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) ger stöd för att integrera din egen RESTful-tjänst. Azure AD B2C skickar data till RESTful-tjänsten i en insamling av indataanspråk och tar emot data tillbaka i en insamling av utdataanspråk. Mer information finns [i Integrera REST API-anspråksutbyten i din anpassade princip för Azure AD B2C](custom-policy-rest-api-intro.md).  

## <a name="protocol"></a>Protokoll

**Attributet Name** för **protokollelementet** måste `Proprietary`anges till . **Hantattributet** måste innehålla det fullständigt kvalificerade namnet på protokollhanterarsammansättningen `Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`som används av Azure AD B2C: .

I följande exempel visas en restful teknisk profil:

```XML
<TechnicalProfile Id="REST-UserMembershipValidator">
  <DisplayName>Validate user input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="input-claims"></a>Ingående anspråk

Elementet InputClaims innehåller en lista över anspråk som ska skickas till REST API.The **InputClaims** elementet contains a list of claims to send to the REST API. Du kan också mappa namnet på ditt anspråk till namnet som definieras i REST API. I följande exempel visas mappningen mellan principen och REST API. Anspråket **givenName** skickas till REST API som **förnamn**, medan **efternamn** skickas som **efternamn**. **E-postanspråket** är inställt som det är.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="email" />
  <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
  <InputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
</InputClaims>
```

Elementet **InputClaimsTransformations** kan innehålla en samling **InputClaimsTransformation-element** som används för att ändra indataanspråken eller generera nya innan de skickas till REST API.The InputClaimsTransformations element may contain a collection of InputClaimsTransformation elements that are used to modify the input claims or generate new one before sending to the REST API.

## <a name="send-a-json-payload"></a>Skicka en JSON-nyttolast

Med den tekniska PROFILEN FÖR REST API kan du skicka en komplex JSON-nyttolast till en slutpunkt.

Så här skickar du en komplex JSON-nyttolast:

1. Bygg din JSON-nyttolast med GenerateJson-anspråkstransformationen. [GenerateJson](json-transformations.md)
1. I den tekniska PROFILEN FÖR REST API:
    1. Lägg till en omvandling av `GenerateJson` indataanspråk med en referens till anspråksomvandlingen.
    1. Ange `SendClaimsIn` alternativet metadata till`body`
    1. Ange `ClaimUsedForRequestPayload` metadataalternativet till namnet på anspråket som innehåller JSON-nyttolasten.
    1. I indataanspråket lägger du till en hänvisning till det ingående anspråk som innehåller JSON-nyttolasten.

I följande `TechnicalProfile` exempel skickas ett verifieringsmeddelande med hjälp av en e-posttjänst från tredje part (i det här fallet SendGrid).

```XML
<TechnicalProfile Id="SendGrid">
  <DisplayName>Use SendGrid's email API to send the code the the user</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://api.sendgrid.com/v3/mail/send</Item>
    <Item Key="AuthenticationType">Bearer</Item>
    <Item Key="SendClaimsIn">Body</Item>
    <Item Key="ClaimUsedForRequestPayload">sendGridReqBody</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_SendGridApiKey" />
  </CryptographicKeys>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="GenerateSendGridRequestBody" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="sendGridReqBody" />
  </InputClaims>
</TechnicalProfile>
```

## <a name="output-claims"></a>Utgående fordringar

**Elementet OutputClaims** innehåller en lista över anspråk som returneras av REST API. Du kan behöva mappa namnet på anspråket som definieras i din princip till det namn som definieras i REST API. Du kan också inkludera anspråk som inte returneras av REST API-identitetsprovidern, så länge du anger `DefaultValue` attributet.

**Elementet OutputClaimsTransformations** kan innehålla en samling **OutputClaimsTransformation-element** som används för att ändra utdataanspråken eller generera nya.

I följande exempel visas anspråket som returneras av REST API:

- **Den MembershipId** anspråk som är mappad till **loyaltyNumber** anspråksnamn.

Den tekniska profilen returnerar också anspråk som inte returneras av identitetsleverantören:

- **LoyaltyNumberIsNew-anspråk** som har ett `true`standardvärde inställt på .

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="MembershipId" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumberIsNew" DefaultValue="true" />
</OutputClaims>
```

## <a name="metadata"></a>Metadata

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| ServiceUrl (av serviceUrl) | Ja | URL:en för REST API-slutpunkten. |
| AuthenticationType (autentiseringstyp) | Ja | Den typ av autentisering som utförs av RESTful-anspråksprovidern. Möjliga `None`värden: `Basic` `Bearer`, `ClientCertificate`, eller . Värdet `None` anger att REST API inte är anonymt. Värdet `Basic` anger att REST API är skyddat med HTTP grundläggande autentisering. Endast verifierade användare, inklusive Azure AD B2C, kan komma åt ditt API. Värdet `ClientCertificate` (rekommenderas) anger att REST API begränsar åtkomsten med hjälp av klientcertifikatautentisering. Endast tjänster som har lämpliga certifikat, till exempel Azure AD B2C, kan komma åt ditt API. Värdet `Bearer` anger att REST API begränsar åtkomsten med klient-OAuth2-innehavartoken. |
| AllowInsecureAuthInProduction| Inga| Anger om `AuthenticationType` kan anges `none` i produktionsmiljön`DeploymentMode` ( i [TrustFrameworkPolicy](trustframeworkpolicy.md) är inställt på `Production`eller inte angivet). Möjliga värden: sant eller falskt (standard). |
| SendClaimsIn | Inga | Anger hur indataanspråken skickas till leverantören av RESTful-anspråk. Möjliga `Body` värden: `Form`(standard), , `Header`eller `QueryString`. Värdet `Body` är det indataanspråk som skickas i begärandetexten i JSON-format. Värdet `Form` är det indataanspråk som skickas i begärandetexten i ett et-tecken -&-avskilt nyckelvärdeformat. Värdet `Header` är det indataanspråk som skickas i begäranden. Värdet `QueryString` är det indataanspråk som skickas i frågesträngen för begäran. DE HTTP-verb som anropas av var och en är följande:<br /><ul><li>`Body`: POST</li><li>`Form`: POST</li><li>`Header`: FÅ</li><li>`QueryString`: FÅ</li></ul> |
| ClaimsFormat (Skadeformat) | Inga | Används för närvarande, kan ignoreras. |
| ClaimUsedForRequestPayload| Inga | Namnet på ett stränganspråk som innehåller nyttolasten som ska skickas till REST API. |
| FelsökMode | Inga | Kör den tekniska profilen i felsökningsläge. Möjliga värden: `true` `false` , eller (standard). I felsökningsläge kan REST API returnera mer information. Se avsnittet [Returnera felmeddelande.](#returning-error-message) |
| InkluderaClaimResolvingInClaimsHandling  | Inga | För indata- och utdataanspråk anger om [anspråksresolution](claim-resolver-overview.md) ingår i den tekniska profilen. Möjliga värden: `true` `false`  , eller (standard). Om du vill använda en anspråksmatchningsmatchare `true`i den tekniska profilen ställer du in den på . |
| LösJsonPathsInJsonTokens  | Inga | Anger om den tekniska profilen löser JSON-sökvägar. Möjliga värden: `true` `false` , eller (standard). Använd dessa metadata för att läsa data från ett kapslat JSON-element. I ett [OutputClaim](technicalprofiles.md#outputclaims) `PartnerClaimType` anger du det JSON-banelement som du vill mata ut. Till exempel: `firstName.localized` `data.0.to.0.email`eller .|
| AnvändClaimAsBearerToken| Inga| Namnet på anspråket som innehåller innehavartoken.|

## <a name="cryptographic-keys"></a>Krypteringsnycklar

Om typen av autentisering `None`är inställd på används inte elementet **CryptographicKeys.**

```XML
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
</TechnicalProfile>
```

Om typen av autentisering `Basic`är inställd på innehåller elementet **CryptographicKeys** följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| GrundläggandeautentiseringAnvändare | Ja | Användarnamnet som används för att autentisera. |
| BasicAuthenticationPassword | Ja | Lösenordet som används för att autentisera. |

I följande exempel visas en teknisk profil med grundläggande autentisering:

```XML
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">Basic</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_B2cRestClientId" />
    <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_B2cRestClientSecret" />
  </CryptographicKeys>
</TechnicalProfile>
```

Om typen av autentisering `ClientCertificate`är inställd på innehåller elementet **CryptographicKeys** följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| ClientCertificate (Klientcertifiera) | Ja | X509-certifikatet (RSA-nyckeluppsättning) som ska användas för att autentisera. |

```XML
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">ClientCertificate</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_B2cRestClientCertificate" />
  </CryptographicKeys>
</TechnicalProfile>
```

Om typen av autentisering `Bearer`är inställd på innehåller elementet **CryptographicKeys** följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| BärareAutentiseringToken | Inga | Oauth 2.0-innehavartoken. |

```XML
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">Bearer</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_B2cRestClientAccessToken" />
  </CryptographicKeys>
</TechnicalProfile>
```

## <a name="returning-error-message"></a>Returnera felmeddelande

REST API kan behöva returnera ett felmeddelande, till exempel "Användaren hittades inte i CRM-systemet". Om ett fel inträffar ska REST API returnera ett HTTP 4xx-felmeddelande, till exempel 400 (felaktig begäran) eller 409 (konflikt) svarsstatuskod. Svarstexten innehåller felmeddelande formaterad i JSON:

```JSON
{
  "version": "1.0.0",
  "status": 409,
  "code": "API12345",
  "requestId": "50f0bd91-2ff4-4b8f-828f-00f170519ddb",
  "userMessage": "Message for the user",
  "developerMessage": "Verbose description of problem and how to fix it.",
  "moreInfo": "https://restapi/error/API12345/moreinfo"
}
```

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| version | Ja | Din REST API-version. Till exempel: 1.0.1 |
| status | Ja | Måste vara 409 |
| kod | Inga | En felkod från DEN RESTful slutpunktsleverantören, som visas när `DebugMode` den är aktiverad. |
| Id | Inga | En begärandeidentifierare från DEN RESTful slutpunktsleverantören, som visas när `DebugMode` den är aktiverad. |
| användareMessage | Ja | Ett felmeddelande som visas för användaren. |
| utvecklareMessage | Inga | Den utförliga beskrivningen av problemet och hur du åtgärdar det, som visas när `DebugMode` är aktiverad. |
| merInfo | Inga | En URI som pekar på ytterligare information, som visas när `DebugMode` är aktiverad. |


I följande exempel visas en C#-klass som returnerar ett felmeddelande:

```csharp
public class ResponseContent
{
  public string version { get; set; }
  public int status { get; set; }
  public string code { get; set; }
  public string userMessage { get; set; }
  public string developerMessage { get; set; }
  public string requestId { get; set; }
  public string moreInfo { get; set; }
}
```

## <a name="next-steps"></a>Nästa steg

Se följande artiklar för exempel på hur du använder en RESTful teknisk profil:

- [Integrera REST API-anspråksutbyten i din azure AD B2C-anpassade princip](custom-policy-rest-api-intro.md)
- [Genomgång: Integrera REST API-anspråksutbyten i din Azure AD B2C-användarresa som validering av användarindata](custom-policy-rest-api-claims-validation.md)
- [Genomgång: Lägg till REST API-anspråksutbyten i anpassade principer i Azure Active Directory B2C](custom-policy-rest-api-claims-validation.md)
- [Skydda dina REST API-tjänster](secure-rest-api.md)

