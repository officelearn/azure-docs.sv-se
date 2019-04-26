---
title: Definiera en RESTful-tekniska profilen i en anpassad princip i Azure Active Directory B2C | Microsoft Docs
description: Definiera en RESTful-tekniska profilen i en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 7ff14af756a55ccc6bbf40dd39d49c5168f4af1f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60418310"
---
# <a name="define-a-restful-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definiera en RESTful-tekniska profilen i en anpassad princip för Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory (Azure AD) B2C har stöd för ditt eget RESTful-tjänst. Azure AD B2C skickar data till RESTful-tjänst i ett inkommande anspråk samling och tar emot data i en samling för utgående anspråk. Med RESTful-tjänst-integrering kan du:

- **Verifiera användarens indata** – förhindrar att felaktigt bestående i Azure AD B2C. Om värdet från användaren inte är giltigt, returnerar RESTful-tjänst ett felmeddelande som anger att användaren anger en post. Du kan till exempel kontrollera att den e-postadress som anges av användaren finns i din kunddatabas.
- **Skriv över inkommande anspråk** – gör att du kan formatera om värdena i inkommande anspråk. Om en användare anger det första namnet i alla gemener eller versaler kan formatera du namnet med bara den första bokstaven med versaler.
- **Utöka användardata** – gör det möjligt att ytterligare integrera med företagets line-of-business-program. RESTful-tjänst kan till exempel ta emot användarens e-postadress, fråga kundens databas och returnera användarens lojalitet talet till Azure AD B2C. Returnerar anspråk kan lagras, utvärderas i nästa Orchestration-steg eller ingår i åtkomsttoken.
- **Kör anpassad affärslogik** – gör det möjligt att skicka push-meddelanden, uppdatera företagets databaser, kör en process för migrering av användare, hantera behörigheter och granska databaser och utföra andra åtgärder.

Din princip kan skicka inkommande anspråk till REST-API. REST API kan också returnera utdataanspråk som du kan använda senare i din princip eller den kan ge ett felmeddelande. Du kan utforma integrering med RESTful-tjänster på följande sätt:

- **Teknisk verifieringsprofil** – en teknisk verifieringsprofil anropar RESTful-tjänst. Den tekniska profilen verifiering verifierar data från användare innan användarresa fortsätter. Med den tekniska profilen verifiering, ett felmeddelande visas på en självkontrollerad sida och returneras i utgående anspråk.
- **Exchange-anspråk** -ett anrop görs till RESTful-tjänsten via en orchestration-steg. I det här scenariot finns det inget användargränssnitt att visas ett felmeddelande. Om REST-API returnerar ett fel, omdirigeras användaren till förlitande part programmet med ett felmeddelande.

## <a name="protocol"></a>Protokoll

Den **namn** attributet för den **protokollet** element måste anges till `Proprietary`. Den **hanteraren** attributet måste innehålla det fullständigt kvalificerade namnet på protokoll hanteraren sammansättningen som används av Azure AD B2C: `Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`.

I följande exempel visas en RESTful tekniska profil:

```XML
<TechnicalProfile Id="REST-UserMembershipValidator">
  <DisplayName>Validate user input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...    
```

## <a name="input-claims"></a>Inkommande anspråk

Den **InputClaims** elementet innehåller en lista över anspråk ska skickas till REST API. Du kan också mappa namnet på din ansökan till det namn som definierats i REST-API. Följande exempel visas mappningen mellan din princip och REST-API. Den **givenName** anspråk skickas till REST-API som **firstName**, medan **efternamn** skickas som **lastName**. Den **e-post** anspråk är inställd på är.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="email" />
  <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
  <InputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
</InputClaims>
```

Den **InputClaimsTransformations** element kan innehålla en uppsättning **InputClaimsTransformation** element som används för att ändra de inkommande anspråken eller generera nya innan du skickar till REST API.

## <a name="output-claims"></a>Utdataanspråk

Den **OutputClaims** elementet innehåller en lista över anspråk som returneras av REST API. Du kan behöva matchar namnet på anspråk som definierats i din princip att namnet som definierats i REST-API. Du kan även inkludera anspråk som inte returneras av identitetsprovider REST-API som du ställer in den `DefaultValue` attribut.

Den **OutputClaimsTransformations** element kan innehålla en uppsättning **OutputClaimsTransformation** element som används för att ändra de utgående anspråk eller generera nya.

I följande exempel visar de anspråk som returneras av REST-API:

- Den **MembershipId** anspråk som är mappad till den **loyaltyNumber** anspråk namn.

Den tekniska profilen returnerar också anspråk som inte returnerade poskytovatelem identity: 

- Den **loyaltyNumberIsNew** anspråk som har angetts till standardvärdet `true`.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="MembershipId" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumberIsNew" DefaultValue="true" />
</OutputClaims>
```

## <a name="metadata"></a>Metadata

| Attribut | Obligatoriskt | Beskrivning |
| --------- | -------- | ----------- |
| ServiceUrl | Ja | URL till REST API-slutpunkt. | 
| AuthenticationType | Ja | Vilken typ av autentisering som utförs av RESTful anspråksprovidern. Möjliga värden: `None`, `Basic`, eller `ClientCertificate`. Den `None` värdet anger att REST-API inte är anonyma. Den `Basic` värdet anger att REST API skyddas med grundläggande HTTP-autentisering. Endast verifierad användare, inklusive Azure AD B2C kan komma åt ditt API. Den `ClientCertificate` (rekommenderas) värdet anger att REST-API begränsar åtkomst med hjälp av autentisering av klientcertifikat. Endast de tjänster som har rätt certifikat, såsom Azure AD B2C kan komma åt tjänsten. | 
| SendClaimsIn | Nej | Anger hur de inkommande anspråken skickas till RESTful anspråksprovidern. Möjliga värden: `Body` (standard), `Form`, `Header`, eller `QueryString`. Den `Body` värdet är den inkommande anspråk som skickas i begärandetexten i JSON-format. Den `Form` värdet är den inkommande anspråk som skickas i begärandetexten i ett et-tecken ' och ' avgränsade nyckelvärdet format. Den `Header` värdet är den inkommande anspråk som skickas i rubriken. Den `QueryString` värdet är den inkommande anspråk som skickas i frågesträngen begäran. | 
| ClaimsFormat | Nej | Anger formatet för utdataanspråk. Möjliga värden: `Body` (standard), `Form`, `Header`, eller `QueryString`. Den `Body` värdet är utdata-anspråket som skickas i begärandetexten i JSON-format. Den `Form` värdet är utdata-anspråket som skickas i begärandetexten i ett et-tecken ' och ' avgränsade nyckelvärdet format. Den `Header` värdet är utdata-anspråket som skickas i rubriken. Den `QueryString` värdet är utdata-anspråket som skickas i frågesträngen begäran. | 
| DebugMode | Nej | Kör den tekniska profilen i felsökningsläge. I felsökningsläge och kan REST-API returnera mer information. Se meddelandeavsnittet returnerar fel. | 

## <a name="cryptographic-keys"></a>Krypteringsnycklar

Om typ av autentisering är inställt på `None`, **CryptographicKeys** elementet används inte.

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

Om typ av autentisering är inställt på `Basic`, **CryptographicKeys** elementet innehåller följande attribut:

| Attribut | Obligatoriskt | Beskrivning |
| --------- | -------- | ----------- |
| BasicAuthenticationUsername | Ja | Det användarnamn som används för att autentisera. | 
| BasicAuthenticationPassword | Ja | Det lösenord som används för att autentisera. |

I följande exempel visas en tekniska profilen med basic-autentisering:

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

Om typ av autentisering är inställt på `ClientCertificate`, **CryptographicKeys** elementet innehåller följande attribut:

| Attribut | Obligatoriskt | Beskrivning |
| --------- | -------- | ----------- |
| ClientCertificate | Ja | X509 certifikat (RSA nyckeluppsättning) som ska användas för att autentisera. | 

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

## <a name="returning-error-message"></a>Returnerar ett felmeddelande

REST-API kan behöva returnera ett felmeddelande som ”användaren inte hittades i CRM-systemet”. Ett fel inträffar, REST-API ska returnera felmeddelandet HTTP 409 (konflikt svarsstatuskod) med följande attribut:

| Attribut | Obligatoriskt | Beskrivning |
| --------- | -------- | ----------- |
| version | Ja | 1.0.0 | 
| status | Ja | 409 | 
| Kod | Nej | Felkoden från leverantören RESTful-slutpunkt, vilket är visas när `DebugMode` är aktiverat. | 
| requestId | Nej | En identifierare för begäran från leverantören RESTful-slutpunkt, vilket är visas när `DebugMode` är aktiverat. | 
| userMessage | Ja | Ett felmeddelande som visas för användaren. | 
| developerMessage | Nej | Utförlig beskrivning av problemet och hur du åtgärdar det, vilket är visas när `DebugMode` är aktiverat. | 
| moreInfo | Nej | En URI som pekar på ytterligare information som visas när `DebugMode` är aktiverat. | 

I följande exempel visar ett REST-API som returnerar ett felmeddelande som formaterats i JSON:

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

I följande exempel visas en C#-klass som returnerar ett felmeddelande visas:

```C#
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

## <a name="examples"></a>Exempel:
- [Integrera REST API anspråk Utbytena i din Azure AD B2C-användarresa som verifiering av indata från användaren](active-directory-b2c-custom-rest-api-netfw.md) 
- [Skydda RESTful-tjänster med hjälp av grundläggande HTTP-autentisering](active-directory-b2c-custom-rest-api-netfw-secure-basic.md)
- [Skydda RESTful-tjänst med hjälp av klientcertifikat](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)
- [Genomgång: Integrera REST API anspråk Utbytena i din Azure AD B2C-användarresa som verifiering på indata från användaren](active-directory-b2c-rest-api-validation-custom.md)

 














