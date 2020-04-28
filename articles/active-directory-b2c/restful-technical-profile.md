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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80332614"
---
# <a name="define-a-restful-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definiera en RESTful teknisk profil i en Azure Active Directory B2C anpassad princip

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) ger stöd för att integrera din egen RESTful-tjänst. Azure AD B2C skickar data till RESTful-tjänsten i en inloggad anspråks samling och tar emot data tillbaka i en utgående anspråks samling. Mer information finns i [integrera REST API Claims-utbyten i din Azure AD B2C anpassade princip](custom-policy-rest-api-intro.md).  

## <a name="protocol"></a>Protokoll

Namnattributet **för** **protokoll** elementet måste anges till `Proprietary`. Attributet **hanterare** måste innehålla det fullständigt kvalificerade namnet på den protokoll hanterings sammansättning som används av Azure AD B2C: `Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`.

I följande exempel visas en RESTful teknisk profil:

```XML
<TechnicalProfile Id="REST-UserMembershipValidator">
  <DisplayName>Validate user input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="input-claims"></a>Inmatade anspråk

**InputClaims** -elementet innehåller en lista över anspråk att skicka till REST API. Du kan också mappa namnet på anspråket till namnet som definierats i REST API. I följande exempel visas mappningen mellan principen och REST API. **GivenName** -anspråket skickas till REST API som **FirstName** **, medan efter namn skickas** som **LastName**. **E-** postanspråket anges som det är.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="email" />
  <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
  <InputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
</InputClaims>
```

**InputClaimsTransformations** -elementet kan innehålla en samling av **InputClaimsTransformation** -element som används för att ändra de inloggade anspråken eller generera nya innan de skickas till REST API.

## <a name="send-a-json-payload"></a>Skicka en JSON-nyttolast

Med den REST API tekniska profilen kan du skicka en komplex JSON-nyttolast till en slut punkt.

Skicka en komplex JSON-nytto last:

1. Bygg din JSON-nyttolast med [GenerateJson](json-transformations.md) -anspråks omvandlingen.
1. I REST API teknisk profil:
    1. Lägg till en omvandling av inmatade anspråk med `GenerateJson` en referens till omvandlingen av anspråk.
    1. Ange alternativet `SendClaimsIn` för metadata till`body`
    1. Ange alternativet `ClaimUsedForRequestPayload` för metadata till namnet på anspråket som innehåller JSON-nyttolasten.
    1. Lägg till en referens till det inloggade anspråk som innehåller JSON-nyttolasten i det inloggade anspråket.

I följande exempel `TechnicalProfile` skickas ett e-postmeddelande med en e-posttjänst från tredje part (i det här fallet SendGrid).

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

## <a name="output-claims"></a>Utgående anspråk

**OutputClaims** -elementet innehåller en lista över anspråk som returneras av REST API. Du kan behöva mappa namnet på det anspråk som definierats i principen till det namn som definierats i REST API. Du kan också inkludera anspråk som inte returneras av REST API identitets leverantören, så länge som du ställer in `DefaultValue` attributet.

**OutputClaimsTransformations** -elementet kan innehålla en samling av **OutputClaimsTransformation** -element som används för att ändra de utgående anspråken eller skapa nya.

I följande exempel visas anspråk som returneras av REST API:

- **MembershipId** -anspråket som är mappat till namnet på **loyaltyNumber** -anspråket.

Den tekniska profilen returnerar även anspråk som inte returneras av identitets leverantören:

- **LoyaltyNumberIsNew** -anspråket som har ett standardvärde inställt på `true`.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="MembershipId" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumberIsNew" DefaultValue="true" />
</OutputClaims>
```

## <a name="metadata"></a>Metadata

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| ServiceUrl | Ja | URL: en för REST API slut punkten. |
| AuthenticationType | Ja | Den typ av autentisering som utförs av RESTful-anspråks leverantören. Möjliga värden: `None`, `Basic`, `Bearer`eller `ClientCertificate`. `None` Värdet anger att REST API inte är anonymt. `Basic` Värdet anger att REST API skyddas med HTTP Basic-autentisering. Endast verifierade användare, inklusive Azure AD B2C, har åtkomst till ditt API. Värdet `ClientCertificate` (rekommenderas) indikerar att REST API begränsar åtkomsten med hjälp av autentisering av klient certifikat. Endast tjänster som har rätt certifikat, till exempel Azure AD B2C, har åtkomst till ditt API. `Bearer` Värdet anger att REST API begränsar åtkomsten med hjälp av klientens OAuth2 Bearer-token. |
| AllowInsecureAuthInProduction| Inga| `AuthenticationType` Anger om kan anges till `none` i produktions miljön (`DeploymentMode` i [TrustFrameworkPolicy](trustframeworkpolicy.md) är inställt på `Production`eller inte angivet). Möjliga värden: true eller false (standard). |
| SendClaimsIn | Inga | Anger hur inloggade anspråk skickas till RESTful-anspråks leverantören. Möjliga värden: `Body` (standard), `Form`, `Header`, eller `QueryString`. `Body` Värdet är det ingående anspråket som skickas i begär ande texten i JSON-format. `Form` Värdet är det inloggade anspråket som skickas i begär ande texten i ett et-tecken () &. `Header` Värdet är det ingående anspråket som skickas i begär ande huvudet. `QueryString` Värdet är det inloggade anspråk som skickas i frågesträngen för begäran. HTTP-verben som anropas av var och en är följande:<br /><ul><li>`Body`: POST</li><li>`Form`: POST</li><li>`Header`: Hämta</li><li>`QueryString`: Hämta</li></ul> |
| ClaimsFormat | Inga | Används inte för närvarande, kan ignoreras. |
| ClaimUsedForRequestPayload| Inga | Namn på ett sträng anspråk som innehåller nytto lasten som ska skickas till REST API. |
| DebugMode | Inga | Kör den tekniska profilen i fel söknings läge. Möjliga värden: `true`, eller `false` (standard). I fel söknings läge kan REST API returnera mer information. Se avsnittet [returnera fel meddelande](#returning-error-message) . |
| IncludeClaimResolvingInClaimsHandling  | Inga | För indata-och utgående anspråk anges om [anspråks matchning](claim-resolver-overview.md) ingår i den tekniska profilen. Möjliga värden: `true`, eller `false`  (standard). Om du vill använda en anspråks lösare i den tekniska profilen ställer du in den på `true`. |
| ResolveJsonPathsInJsonTokens  | Inga | Anger om den tekniska profilen matchar JSON-sökvägar. Möjliga värden: `true`, eller `false` (standard). Använd dessa metadata för att läsa data från ett kapslat JSON-element. I en [OutputClaim](technicalprofiles.md#outputclaims)anger `PartnerClaimType` du det JSON-sökvägar som du vill mata ut. Till exempel: `firstName.localized`, eller `data.0.to.0.email`.|
| UseClaimAsBearerToken| Inga| Namnet på det anspråk som innehåller Bearer-token.|

## <a name="cryptographic-keys"></a>Kryptografiska nycklar

Om autentiseringstypen har angetts till `None`används inte **CryptographicKeys** -elementet.

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

Om autentiseringstypen har angetts till `Basic`innehåller **CryptographicKeys** -elementet följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| BasicAuthenticationUsername | Ja | Det användar namn som används för att autentisera. |
| BasicAuthenticationPassword | Ja | Det lösen ord som används för att autentisera. |

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

Om autentiseringstypen har angetts till `ClientCertificate`innehåller **CryptographicKeys** -elementet följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Mängden | Ja | X509-certifikatet (RSA-nyckel uppsättning) som ska användas för att autentisera. |

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

Om autentiseringstypen har angetts till `Bearer`innehåller **CryptographicKeys** -elementet följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| BearerAuthenticationToken | Inga | OAuth 2,0 Bearer-token. |

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

## <a name="returning-error-message"></a>Returnerar fel meddelande

REST API kan behöva returnera ett fel meddelande, till exempel "användaren inte kunde hittas i CRM-systemet". Om ett fel inträffar ska REST API returnera ett HTTP-4xx fel meddelande, till exempel, 400 (felaktig begäran) eller 409 (konflikt) svars status kod. Svars texten innehåller ett fel meddelande som är formaterat i JSON:

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
| kod | Inga | En felkod från RESTful-slutpunkt-providern, som visas när `DebugMode` är aktive rad. |
| requestId | Inga | En begärande-ID från RESTful-slutpunkt-providern, som `DebugMode` visas när är aktive rad. |
| userMessage | Ja | Ett fel meddelande som visas för användaren. |
| developerMessage | Inga | Utförlig beskrivning av problemet och hur du åtgärdar det, som visas när `DebugMode` är aktiverat. |
| moreInfo | Inga | En URI som pekar på ytterligare information, som visas när `DebugMode` är aktive rad. |


I följande exempel visas en C#-klass som returnerar ett fel meddelande:

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

I följande artiklar finns exempel på hur du använder en RESTful teknisk profil:

- [Integrera REST API Claims-utbyten i Azure AD B2C anpassad princip](custom-policy-rest-api-intro.md)
- [Genom gång: integrera REST API Claims-utbyten i Azure AD B2C användar resa som validering av användarindata](custom-policy-rest-api-claims-validation.md)
- [Genom gång: Lägg till REST API Claims-utbyten till anpassade principer i Azure Active Directory B2C](custom-policy-rest-api-claims-validation.md)
- [Skydda dina REST API-tjänster](secure-rest-api.md)

