---
title: REST API-anspråk utbyten - Azure Active Directory B2C | Microsoft Docs
description: Lägg till REST API anspråk utbyten i anpassade principer i Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: e705c12782310597ea14d5253aba8b6a1a004e6d
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65952794"
---
# <a name="add-rest-api-claims-exchanges-to-custom-policies-in-azure-active-directory-b2c"></a>Lägg till REST API anspråk utbyten i anpassade principer i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Du kan lägga till interaktion med ett RESTful-API till din [anpassade principer](active-directory-b2c-overview-custom.md) i Azure Active Directory (Azure AD) B2C. Den här artikeln visar hur du skapar en Azure AD B2C-användarresa som interagerar med RESTful-tjänster.

Interaktionen innehåller ett anspråk utbyta information mellan REST API-anspråk och Azure AD B2C. Utbyten av anspråk har följande egenskaper:

- Kan utformas som ett orchestration-steg.
- Kan utlösa en extern åtgärd. Det kan exempelvis logga en händelse i en extern databas.
- Kan användas för att hämta ett värde och sedan lagra den i databasen.
- Kan ändra flödet av körningen. 

Det scenario som representeras i den här artikeln innehåller följande åtgärder:

1. Leta upp användaren i ett externt system.
2. Hämta staden där användaren har registrerats.
3. Returnera attributet för programmet som ett anspråk.

## <a name="prerequisites"></a>Nödvändiga komponenter

- Utför stegen i [Kom igång med anpassade principer](active-directory-b2c-get-started-custom.md).
- En REST API-slutpunkt för att interagera med. Den här artikeln använder en enkel Azure fungerar som ett exempel. För att skapa Azure-funktion, se [skapa din första funktion i Azure-portalen](../azure-functions/functions-create-first-azure-function.md).

## <a name="prepare-the-api"></a>Förbereda API: et

I det här avsnittet ska du förbereda Azure-funktion som tar emot ett värde för `email`, och returnerar sedan värdet för `city` som kan användas av Azure AD B2C som ett anspråk.

Ändra filen run.csx för Azure-funktion som du skapade för att använda följande kod: 

```
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

## <a name="configure-the-claims-exchange"></a>Konfigurera anspråksutbytet

Tekniska profilen innehåller konfigurationen för anspråk för exchange. 

Öppna den *TrustFrameworkExtensions.xml* filen och Lägg till följande XML-element inuti den **ClaimsProvider** element.

```XML
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AzureFunctions-WebHook">
      <DisplayName>Azure Function Web Hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://myfunction.azurewebsites.net/api/HttpTrigger1?code=bAZ4lLy//ZHZxmncM8rI7AgjQsrMKmVXBpP0vd9smOzdXDDUIaLljA==</Item>
        <Item Key="AuthenticationType">None</Item>
        <Item Key="SendClaimsIn">Body</Item>
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

Den **InputClaims** elementet definierar vilka anspråk som skickas till REST-tjänst. I det här exemplet anspråkets värde `givenName` skickas till REST-tjänst som anspråket `email`. Den **OutputClaims** elementet definierar vilka anspråk som förväntas från REST-tjänst.

## <a name="add-the-claim-definition"></a>Lägg till anspråksdefinitionen

Lägg till en definition för `city` inuti den **BuildingBlocks** element. Du hittar det här elementet i början av filen TrustFrameworkExtensions.xml.

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

## <a name="add-an-orchestration-step"></a>Lägg till en orchestration-steg

Det finns många användningsområden där REST API-anrop kan användas som ett orchestration-steg. Som ett orchestration-steg kan användas den som en uppdatering till ett externt system när en användare har genomgått en aktivitet, som registrering för första gången eller som en Profiluppdatering av för att synkronisera informationen. I det här fallet används det att utöka informationen till programmet när profilen har redigerat.

Lägga till ett steg i användarresan för profilen redigera. När användaren har autentiserats (orchestration-steg 1-4 i följande XML) och användaren har tillhandahållit uppdaterade profilinformation (steg 5). Kopiera profilen Redigera användare resa XML-koden från den *TrustFrameworkBase.xml* filen till din *TrustFrameworkExtensions.xml* filen i den **UserJourneys** element. Sedan göra ändringen som steg 6.

```XML
<OrchestrationStep Order="6" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-LookUpLoyaltyWebHook" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Sista XML för användarresan bör se ut som i följande exempel:

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
        <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-LookUpLoyaltyWebHook" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
  </OrchestrationSteps>
  <ClientDefinition ReferenceId="DefaultWeb" />
</UserJourney>
```

## <a name="add-the-claim"></a>Lägg till kravet

Redigera den *ProfileEdit.xml* filen och Lägg till `<OutputClaim ClaimTypeReferenceId="city" />` till den **OutputClaims** element.

När du lägger till nytt anspråk den tekniska profilen ser ut som i följande exempel:

```XML
<DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="city" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
</TechnicalProfile>
```

## <a name="upload-your-changes-and-test"></a>Överföra dina ändringar och testa

1. (Optional:) Spara den befintliga versionen (genom att ladda ned) filer innan du fortsätter.
2. Ladda upp den *TrustFrameworkExtensions.xml* och *ProfileEdit.xml* och välj att skriva över den befintliga filen.
3. Välj **B2C_1A_ProfileEdit**.
4. För **Välj program** på översiktssidan av den anpassade principen väljer du webbprogrammet med namnet *webapp1* som du tidigare har registrerat. Se till att den **svars-URL** är `https://jwt.ms`.
4. Välj **kör nu**. Logga in med autentiseringsuppgifterna för ditt konto och klicka på **Fortsätt**.

Om allt är korrekt konfigurerad, token innehåller nytt anspråk `city`, med värdet `Redmond`.

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

- Du kan också utforma interaktion som en profil. Mer information finns i [genomgång: Integrera REST API anspråk Utbytena i din Azure AD B2C-användarresa som verifiering på indata från användaren](active-directory-b2c-rest-api-validation-custom.md).
- [Ändra profilredigering för att samla in ytterligare information från användarna](active-directory-b2c-create-custom-attributes-profile-edit-custom.md)
