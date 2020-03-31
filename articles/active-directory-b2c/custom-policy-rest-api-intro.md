---
title: REST API hävdar utbyten i B2C-anpassad princip
titleSuffix: Azure AD B2C
description: En introduktion till att skapa en Azure AD B2C-användarresa som interagerar med RESTful-tjänster.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6a6cc8e5931f3e29c242f51a6e062441953228ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337420"
---
# <a name="integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-custom-policy"></a>Integrera REST API-anspråksutbyten i din azure AD B2C-anpassade princip

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Identity Experience Framework, som ligger till grund för Azure Active Directory B2C (Azure AD B2C), kan integreras med RESTful API:er i en användarresa. Den här artikeln visar hur du skapar en användarresa som interagerar med en RESTful-tjänst med hjälp av en [RESTful teknisk profil](https://identitydivision.visualstudio.com/defaultcollection/Identity%20CXP/_git/GTP?path=%2Fyoelh%2Fdocs%2Frest-api%2Frestful-technical-profile.md&version=GBmaster).

Med Hjälp av Azure AD B2C kan du lägga till din egen affärslogik i en användarresa genom att ringa din egen RESTful-tjänst. Identity Experience Framework kan skicka och ta emot data från din RESTful-tjänst för att utbyta anspråk. Du kan till exempel:

- **Validera indata för användaren**. Du kan till exempel kontrollera att den e-postadress som användaren har angett finns i kundens databas och om inte, ett fel.
- **Bearbeta anspråk**. Om en användare anger sitt förnamn i alla gemener eller versaler kan REST API formatera namnet med endast den första bokstaven med stora bokstäver och returnera det till Azure AD B2C.
- **Berika användardata genom att ytterligare integrera med företagslinjeprogram**. Din RESTful-tjänst kan ta emot användarens e-postadress, fråga kundens databas och returnera användarens förmånsnummer till Azure AD B2C. Sedan kan returanspråk lagras i användarens Azure AD-konto, utvärderas i nästa orchestration-steg eller inkluderas i åtkomsttoken.
- **Kör anpassad affärslogik**. Du kan skicka push-meddelanden, uppdatera företagsdatabaser, köra en migreringsprocess för användare, hantera behörigheter, granska databaser och utföra andra arbetsflöden.

![Diagram över ett RESTful-tjänsteanspråksutbyte](media/custom-policy-rest-api-intro/restful-service-claims-exchange.png)

## <a name="calling-a-restful-service"></a>Ringa en RESTful-tjänst

Interaktionen innehåller ett anspråksutbyte av information mellan REST API-anspråk och Azure AD B2C. Du kan utforma integreringen med RESTful-tjänsterna på följande sätt:

- **Teknisk profil för validering**. Anropet till RESTful-tjänsten sker inom en [teknisk valideringsprofil](validation-technical-profile.md) för den angivna [självsäkra tekniska profilen](self-asserted-technical-profile.md)eller en [kontrollkontroll](display-control-verification.md) av en [bildskärm.](display-controls.md) Den tekniska profilen för validering validerar data som tillhandahålls av användaren innan användarens färd går framåt. Med den tekniska profilen för validering kan du:

  - Skicka anspråk till ditt REST API.
  - Validera anspråk och kasta anpassade felmeddelanden som visas för användaren.
  - Skicka tillbaka anspråk från REST API till efterföljande orchestration steg.

- **Utbyte av fordringar**. Ett direkt anspråksutbyte kan konfigureras genom att anropa en teknisk REST API-profil direkt från ett orchestration-steg för en [användarresa](userjourneys.md). Denna definition är begränsad till:

  - Skicka anspråk till ditt REST API.
  - Validera anspråk och skicka anpassade felmeddelanden som returneras till programmet.
  - Skicka tillbaka anspråk från REST API till efterföljande orchestration steg.

Du kan lägga till ett REST API-anrop när som helst i användarfärden som definieras av en anpassad princip. Du kan till exempel anropa ett REST API:

- Under inloggningen, strax innan Azure AD B2C validerar autentiseringsuppgifterna.
- Omedelbart efter inloggning.
- Innan Azure AD B2C skapar ett nytt konto i katalogen.
- När Azure AD B2C har skapat ett nytt konto i katalogen.
- Innan Azure AD B2C utfärdar en åtkomsttoken.

![Insamling av teknisk profil för validering](media/custom-policy-rest-api-intro/validation-technical-profile.png)

## <a name="sending-data"></a>Skicka data

I den [tekniska PROFILEN RESTful](restful-technical-profile.md)innehåller elementet `InputClaims` en lista över anspråk att skicka till din RESTful-tjänst. Du kan mappa namnet på ditt anspråk till namnet som definierats i tjänsten RESTful, ange ett standardvärde och använda [anspråkslösare](claim-resolver-overview.md).

Du kan konfigurera hur indataanspråken skickas till RESTful-anspråksprovidern med attributet SendClaimsIn. Möjliga värden är:

- **Brödtext**, skickat i http post-begärandetexten i JSON-format.
- **Formulär**, skickas i http post-begärandetexten i ett et-tecken -&-avskilt nyckelvärdeformat.
- **Header**, skickas i HTTP GET-begäranden.
- **QueryString**, skickas i HTTP GET-begäranfrågesträngen.

När body-alternativet är konfigurerat kan du skicka en komplex JSON-nyttolast till en slutpunkt med rest API-api.When the **Body** option is configured, the REST API technical profile allows you to send a complex JSON payload to an endpoint. Mer information finns i [Skicka en JSON-nyttolast](restful-technical-profile.md#send-a-json-payload).

## <a name="receiving-data"></a>Ta emot data

Elementet i den tekniska `OutputClaims` [RESTful-profilen](restful-technical-profile.md) innehåller en lista över anspråk som returneras av REST API.The elementet of the RESTful technical profile contains a list of claims returned by the REST API. Du kan behöva mappa namnet på anspråket som definieras i din princip till det namn som definieras i REST API. Du kan också inkludera anspråk som inte returneras av REST API-identitetsprovidern, så länge du anger attributet DefaultValue.

De utgående anspråk som tolkas av RESTful fordringar leverantören förväntar sig alltid att tolka en platt JSON Body svar, såsom:

```json
{
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "loyaltyNumber":  1234
}
```

Utdataanspråken ska se ut så här:

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" />
</OutputClaims>
```

Om du vill tolka ett kapslat JSON-brödtextsvar ställer du in Metadata för ResolveJsonPathsInJsonTokens till true. I utdataanspråket ställer du in PartnerClaimType på det JSON-sökvägselement som du vill mata ut.

```json
"contacts": [
  {
    "id": "MAINCONTACT_1",
    "person": {
      "name": "Emily Smith",
      "loyaltyNumber":  1234,
      "emails": [
        {
          "id": "EMAIL_1",
          "type": "WORK",
          "email": "email@domain.com"
        }
      ]
    }
  }
],
```


Utdataanspråken ska se ut så här:

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="contacts.0.person.name" />
  <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="contacts.0.person.emails.0.email" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="contacts.0.person.loyaltyNumber" />
</OutputClaims>
```

## <a name="security-considerations"></a>Säkerhetsöverväganden

Du måste skydda slutpunkten för REST API så att endast autentiserade klienter kan kommunicera med den. REST API måste använda en HTTPS-slutpunkt. Ange AuthenticationType-metadata till någon av följande autentiseringsmetoder:

- **Klientcertifikat** begränsar åtkomsten med hjälp av autentisering av klientcertifikat. Endast tjänster som har rätt certifikat kan komma åt ditt API. Du lagrar klientcertifikatet i en Azure AD B2C-principnyckel. Läs mer om hur du [skyddar din RESTful-tjänst med hjälp av klientcertifikat](secure-rest-api.md#https-client-certificate-authentication).
- **Basic** skyddar REST API med HTTP grundläggande autentisering. Endast verifierade användare, inklusive Azure AD B2C, kan komma åt ditt API. Användarnamnet och lösenordet lagras i Azure AD B2C-principnycklar. Lär dig hur du [skyddar dina RESTful-tjänster med hjälp av HTTP Basic-autentisering](secure-rest-api.md#http-basic-authentication).
- **Bärare** begränsar åtkomsten med hjälp av en klient OAuth2-åtkomsttoken. Åtkomsttoken lagras i en Azure AD B2C-principnyckel. Läs mer om hur du [säkrar din RESTful-tjänst med hjälp av Innehavartoken](secure-rest-api.md#oauth2-bearer-authentication).

## <a name="rest-api-platform"></a>REST API-plattform
Din REST API kan baseras på alla plattformar och skriven på alla programmeringsspråk, så länge det är säkert och kan skicka och ta emot anspråk som anges i [RESTful teknisk profil](restful-technical-profile.md).

## <a name="localize-the-rest-api"></a>Lokalisera REST API
I en RESTful teknisk profil kanske du vill skicka den aktuella sessionens språk/språk och vid behov skapa ett lokaliserat felmeddelande. Med [anspråksmatcharen](claim-resolver-overview.md)kan du skicka ett kontextuellt anspråk, till exempel användarspråket. I följande exempel visas en restful teknisk profil som visar det här scenariot.

```XML
<TechnicalProfile Id="REST-ValidateUserData">
  <DisplayName>Validate user input data</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app.azurewebsites.net/api/identity</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
    <Item Key="IncludeClaimResolvingInClaimsHandling">true</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userLanguage" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="emailAddress" />
  </InputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

## <a name="handling-error-messages"></a>Hantera felmeddelanden

REST API kan behöva returnera ett felmeddelande, till exempel "Användaren hittades inte i CRM-systemet.", Om ett fel uppstår ska REST API returnera ett HTTP 409-felmeddelande (statuskod för konfliktsvar). Mer information finns i den [tekniska restful-profilen](https://identitydivision.visualstudio.com/defaultcollection/Identity%20CXP/_git/GTP?path=%2Fyoelh%2Fdocs%2Frest-api%2Frestful-technical-profile.md&version=GBmaster&anchor=returning-error-message).

Detta kan endast uppnås genom att anropa en TEKNISK REST API-profil från en teknisk valideringsprofil. Detta gör det möjligt för användaren att korrigera data på sidan och köra valideringen igen vid sidinlämning.

Ett HTTP 409-svar krävs för att förhindra bearbetning av efterföljande valideringstekniska profiler i det här orchestration-steget.

Om du refererar till en teknisk REST API-profil direkt från en användarresa omdirigeras användaren tillbaka till det förlitande partprogrammet med det relevanta felmeddelandet.

## <a name="publishing-your-rest-api"></a>Publicera REST API

Begäran till din REST API-tjänst kommer från Azure AD B2C-servrar. REST API-tjänsten måste publiceras till en allmänt tillgänglig HTTPS-slutpunkt. REST API-anrop kommer från en AZURE-datacenter-IP-adress.

Utforma REST API-tjänsten och dess underliggande komponenter (till exempel databasen och filsystemet) så att de är mycket tillgängliga.

## <a name="next-steps"></a>Nästa steg

Se följande artiklar för exempel på hur du använder en RESTful teknisk profil:

- [Genomgång: Integrera REST API-anspråksutbyten i din Azure AD B2C-användarresa som validering av användarindata](custom-policy-rest-api-claims-validation.md)
- [Genomgång: Lägg till REST API-anspråksutbyten i anpassade principer i Azure Active Directory B2C](custom-policy-rest-api-claims-validation.md)
- [Skydda dina REST API-tjänster](secure-rest-api.md)
- [Referens: RESTful teknisk profil](restful-technical-profile.md)