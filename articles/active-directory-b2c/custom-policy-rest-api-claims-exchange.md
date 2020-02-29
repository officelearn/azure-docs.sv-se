---
title: REST API Claims-börser – Azure Active Directory B2C
description: Lägg till REST API åberopade anspråk till anpassade principer i Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 351b41f45fb84384ec0193f8e3130347d0b19401
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/29/2020
ms.locfileid: "78189097"
---
# <a name="add-rest-api-claims-exchanges-to-custom-policies-in-azure-active-directory-b2c"></a>Lägg till REST API åberopade anspråk till anpassade principer i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Du kan lägga till interaktion med ett RESTful-API till dina [anpassade principer](custom-policy-overview.md) i Azure Active Directory B2C (Azure AD B2C). Den här artikeln visar hur du skapar en Azure AD B2C användar resa som samverkar med RESTful-tjänster.

Interaktionen innehåller ett anspråk utbyte av information mellan REST API anspråk och Azure AD B2C. Anspråk byten har följande egenskaper:

- Kan utformas som ett Orchestration-steg.
- Kan utlösa en extern åtgärd. Den kan till exempel logga en händelse i en extern databas.
- Kan användas för att hämta ett värde och sedan lagra det i användar databasen.
- Kan ändra körnings flödet.

Scenariot som representeras i den här artikeln innehåller följande åtgärder:

1. Leta upp användaren i ett externt system.
2. Hämta staden där användaren är registrerad.
3. Returnera attributet till programmet som ett anspråk.

## <a name="prerequisites"></a>Förutsättningar

- Slutför stegen i [Kom igång med anpassade principer](custom-policy-get-started.md).
- En REST API slut punkt att interagera med. I den här artikeln används en enkel Azure-funktion som exempel. Information om hur du skapar Azure-funktionen finns i [skapa din första funktion i Azure Portal](../azure-functions/functions-create-first-azure-function.md).

## <a name="prepare-the-api"></a>Förbered API

I det här avsnittet förbereder du Azure-funktionen för att ta emot ett värde för `email`och returnerar sedan värdet för `city` som kan användas av Azure AD B2C som ett anspråk.

Ändra filen Run. CSX för den Azure-funktion som du skapade för att använda följande kod:

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Newtonsoft.Json;

public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
{
  log.LogInformation("C# HTTP trigger function processed a request.");
  string email = req.Query["email"];
  string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
  dynamic data = JsonConvert.DeserializeObject(requestBody);
  email = email ?? data?.email;

  return email != null
    ? (ActionResult)new OkObjectResult(
      new ResponseContent
      {
        version = "1.0.0",
        status = (int) HttpStatusCode.OK,
        city = "Redmond"
      })
      : new BadRequestObjectResult("Please pass an email on the query string or in the request body");
}

public class ResponseContent
{
    public string version { get; set; }
    public int status { get; set; }
    public string city {get; set; }
}
```

## <a name="configure-the-claims-exchange"></a>Konfigurera anspråk utbyte

En teknisk profil innehåller konfigurationen för anspråks utbytet.

Öppna filen *TrustFrameworkExtensions. XML* och Lägg till följande **ClaimsProvider** XML-element inuti **ClaimsProviders** -elementet.

```XML
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AzureFunctions-WebHook">
      <DisplayName>Azure Function Web Hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://myfunction.azurewebsites.net/api/HttpTrigger1?code=bAZ4lLy//ZHZxmncM8rI7AgjQsrMKmVXBpP0vd9smOzdXDDUIaLljA==</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
        <Item Key="AuthenticationType">None</Item>
        <!-- REMOVE the following line in production environments -->
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="email" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="city" PartnerClaimType="city" />
      </OutputClaims>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

**InputClaims** -elementet definierar de anspråk som skickas till rest-tjänsten. I det här exemplet skickas värdet för anspråks `givenName` till REST-tjänsten som anspråks `email`. **OutputClaims** -elementet definierar de anspråk som förväntas från rest-tjänsten.

Kommentarerna ovan `AuthenticationType` och `AllowInsecureAuthInProduction` anger vilka ändringar du ska göra när du flyttar till en produktions miljö. Information om hur du skyddar dina RESTful-API: er för produktion finns i [skydda RESTful-API: er med grundläggande autentisering](secure-rest-api-dotnet-basic-auth.md) och [säkra RESTful-API: er med certifikatbaserad autentisering](secure-rest-api-dotnet-certificate-auth.md).

## <a name="add-the-claim-definition"></a>Lägg till anspråks definitionen

Lägg till en definition för `city` inuti **BuildingBlocks** -elementet. Du kan hitta det här elementet i början av TrustFrameworkExtensions. XML-filen.

```XML
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="city">
      <DisplayName>City</DisplayName>
      <DataType>string</DataType>
      <UserHelpText>Your city</UserHelpText>
      <UserInputType>TextBox</UserInputType>
    </ClaimType>
  </ClaimsSchema>
</BuildingBlocks>
```

## <a name="add-an-orchestration-step"></a>Lägg till ett Orchestration-steg

Det finns många användnings fall där REST API-anropet kan användas som ett Orchestration-steg. Som ett Orchestration-steg kan det användas som en uppdatering av ett externt system när en användare har slutfört en aktivitet, t. ex. första registreringen eller som en profil uppdatering för att synkronisera information. I det här fallet används den för att utöka den information som ges till programmet efter profil redigeringen.

Lägg till ett steg i profilen redigera användar resa. När användaren har autentiserats (Orchestration steg 1-4 i följande XML) och användaren har angett den uppdaterade profil informationen (steg 5). Kopiera profilen redigera användar resans XML-kod från *TrustFrameworkBase. XML* -filen till din *TrustFrameworkExtensions. XML-* fil i **UserJourneys** -elementet. Gör sedan ändringen som steg 6.

```XML
<OrchestrationStep Order="6" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-WebHook" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Den sista XML-koden för användar resan bör se ut som i det här exemplet:

```XML
<UserJourney Id="ProfileEdit">
  <OrchestrationSteps>
    <OrchestrationStep Order="1" Type="ClaimsProviderSelection" ContentDefinitionReferenceId="api.idpselections">
      <ClaimsProviderSelections>
        <ClaimsProviderSelection TargetClaimsExchangeId="FacebookExchange" />
        <ClaimsProviderSelection TargetClaimsExchangeId="LocalAccountSigninEmailExchange" />
      </ClaimsProviderSelections>
    </OrchestrationStep>
    <OrchestrationStep Order="2" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
        <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <OrchestrationStep Order="3" Type="ClaimsExchange">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>authenticationSource</Value>
          <Value>localAccountAuthentication</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <ClaimsExchanges>
        <ClaimsExchange Id="AADUserRead" TechnicalProfileReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <OrchestrationStep Order="4" Type="ClaimsExchange">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>authenticationSource</Value>
          <Value>socialIdpAuthentication</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <ClaimsExchanges>
        <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <OrchestrationStep Order="5" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="B2CUserProfileUpdateExchange" TechnicalProfileReferenceId="SelfAsserted-ProfileUpdate" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <!-- Add a step 6 to the user journey before the JWT token is created-->
    <OrchestrationStep Order="6" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-WebHook" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
  </OrchestrationSteps>
  <ClientDefinition ReferenceId="DefaultWeb" />
</UserJourney>
```

## <a name="add-the-claim"></a>Lägg till anspråket

Redigera filen *ProfileEdit. XML* och lägg till `<OutputClaim ClaimTypeReferenceId="city" />` i **OutputClaims** -elementet.

När du har lagt till det nya anspråket ser den tekniska profilen ut som i det här exemplet:

```XML
<TechnicalProfile Id="PolicyProfile">
  <DisplayName>PolicyProfile</DisplayName>
  <Protocol Name="OpenIdConnect" />
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
    <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
    <OutputClaim ClaimTypeReferenceId="city" />
  </OutputClaims>
  <SubjectNamingInfo ClaimType="sub" />
</TechnicalProfile>
```

## <a name="upload-your-changes-and-test"></a>Ladda upp dina ändringar och testa

1. Valfritt Spara den befintliga versionen (genom att ladda ned) filerna innan du fortsätter.
2. Ladda upp filen *TrustFrameworkExtensions. XML* och *ProfileEdit. XML* och välj att skriva över den befintliga filen.
3. Välj **B2C_1A_ProfileEdit**.
4. För **Välj program** på sidan Översikt i den anpassade principen väljer du det webb program som heter *webapp1* som du tidigare har registrerat. Se till att **svars-URL: en** är `https://jwt.ms`.
4. Välj **Kör nu**. Logga in med autentiseringsuppgifterna för ditt konto och klicka på **Fortsätt**.

Om allt är korrekt konfigurerat, inkluderar token det nya anspråket `city`med värdet `Redmond`.

```JSON
{
  "exp": 1493053292,
  "nbf": 1493049692,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "sub": "a58e7c6c-7535-4074-93da-b0023fbaf3ac",
  "aud": "4e87c1dd-e5f5-4ac8-8368-bc6a98751b8b",
  "acr": "b2c_1a_profileedit",
  "nonce": "defaultNonce",
  "iat": 1493049692,
  "auth_time": 1493049692,
  "city": "Redmond"
}
```

## <a name="next-steps"></a>Nästa steg

Du kan också utforma interaktionen som en validerings profil. Mer information finns i [genom gång: integrera REST API Claims-utbyten i Azure AD B2C användar resa som verifiering vid](custom-policy-rest-api-claims-validation.md)användarindata.

[Ändra profil redigeraren om du vill samla in ytterligare information från dina användare](custom-policy-custom-attributes.md)

[Referens: RESTful teknisk profil](restful-technical-profile.md)

Information om hur du skyddar dina API: er finns i följande artiklar:

* [Skydda ditt RESTful-API med grundläggande autentisering (användar namn och lösen ord)](secure-rest-api-dotnet-basic-auth.md)
* [Skydda ditt RESTful-API med klient certifikat](secure-rest-api-dotnet-certificate-auth.md)
