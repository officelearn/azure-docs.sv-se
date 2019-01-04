---
title: REST API-anspråk utbyten som en orkestreringssteg i Azure Active Directory B2C | Microsoft Docs
description: Ett ämne på Azure Active Directory B2C anpassade principer som kan integreras med ett API.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/24/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 5102f2b43819c279d0087754b29a616812e5a5f2
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2018
ms.locfileid: "53556568"
---
# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-an-orchestration-step"></a>Steg-för-steg-beskrivning: Integrera REST API anspråk Utbytena i din Azure AD B2C-användarresa som ett orchestration-steg

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den identitet upplevelse Framework (IEF) som ligger till grund Azure Active Directory B2C (Azure AD B2C) gör det möjligt för identity-utvecklare att integrera en interaktion med ett RESTful-API i en användarresa.  

I slutet av den här genomgången kommer du att kunna skapa en Azure AD B2C-användarresa som interagerar med RESTful-tjänster.

IEF skickar data i anspråk och tar emot data i anspråk. Anspråksutbytet som REST-API:

- Kan utformas som ett orchestration-steg.
- Kan utlösa en extern åtgärd. Det kan exempelvis logga en händelse i en extern databas.
- Kan användas för att hämta ett värde och sedan lagra den i databasen.

Du kan använda de mottagna anspråk senare ändra flödet av körningen.

Du kan också utforma interaktion som en profil. Mer information finns i [genomgång: Integrera REST API anspråk Utbytena i din Azure AD B2C-användarresa som verifiering på indata från användaren](active-directory-b2c-rest-api-validation-custom.md).

Scenariot är att när en användare utför en profilredigering, vi vill:

1. Leta upp användaren i ett externt system.
2. Hämta staden där användaren har registrerats.
3. Returnera attributet för programmet som ett anspråk.

## <a name="prerequisites"></a>Förutsättningar

- En Azure AD B2C-klient som konfigurerats för att slutföra ett lokalt konto registrerings-registreringen/inloggning, enligt beskrivningen i [komma igång](active-directory-b2c-get-started-custom.md).
- En REST API-slutpunkt för att interagera med. Den här genomgången använder en enkel Azure-funktion app webhook som ett exempel.
- *Rekommenderade*: Slutför den [REST API-anspråk exchange genomgång som en validerngssteg](active-directory-b2c-rest-api-validation-custom.md).

## <a name="step-1-prepare-the-rest-api-function"></a>Steg 1: Förbereda REST API-funktion

> [!NOTE]
> Installationen av REST API-funktioner inte omfattas av den här artikeln. [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-reference) tillhandahåller en utmärkt toolkit för att skapa RESTful-tjänster i molnet.

Vi har konfigurerat en Azure-funktion som tar emot ett anspråk som kallas `email`, och returnerar sedan anspråket `city` med det tilldelade värdet för `Redmond`. Exemplet Azure-funktion som finns på [GitHub](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/AzureFunctionsSamples).

Den `userMessage` anspråk som Azure-funktionen returnerar är valfri i den här kontexten och IEF ignorerar den. Du kan eventuellt använda den som ett meddelande skickas till programmet och visas för användaren senare.

```csharp
if (requestContentAsJObject.email == null)
{
    return request.CreateResponse(HttpStatusCode.BadRequest);
}

var email = ((string) requestContentAsJObject.email).ToLower();

return request.CreateResponse<ResponseContent>(
    HttpStatusCode.OK,
    new ResponseContent
    {
        version = "1.0.0",
        status = (int) HttpStatusCode.OK,
        userMessage = "User Found",
        city = "Redmond"
    },
    new JsonMediaTypeFormatter(),
    "application/json");
```

En funktionsapp i Azure gör det enkelt att hämta funktions-URL som innehåller ID för specifik funktion. I det här fallet URL: en är: https://wingtipb2cfuncs.azurewebsites.net/api/LookUpLoyaltyWebHook?code=MQuG7BIE3eXBaCZ/YCfY1SHabm55HEphpNLmh1OP3hdfHkvI2QwPrw==. Du kan använda den för att testa.

## <a name="step-2-configure-the-restful-api-claims-exchange-as-a-technical-profile-in-your-trustframeworextensionsxml-file"></a>Steg 2: Konfigurera exchange för RESTful-API-anspråk som en tekniska profil i filen TrustFrameworExtensions.xml

Tekniska profilen är fullständig konfiguration av exchange som önskas med RESTful-tjänst. Öppna filen TrustFrameworkExtensions.xml och Lägg till följande XML-kodstycke i den `<ClaimsProvider>` element.

> [!NOTE]
> I följande XML, RESTful-providern `Version=1.0.0.0` beskrivs som protokoll. Överväg att som den funktion som kommer att interagera med den externa tjänsten. <!-- TODO: A full definition of the schema can be found...link to RESTful Provider schema definition>-->

```XML
<ClaimsProvider>
    <DisplayName>REST APIs</DisplayName>
    <TechnicalProfiles>
        <TechnicalProfile Id="AzureFunctions-LookUpLoyaltyWebHook">
            <DisplayName>Check LookUpLoyalty Web Hook Azure Function</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <Metadata>
                <Item Key="ServiceUrl">https://wingtipb2cfuncs.azurewebsites.net/api/LookUpLoyaltyWebHook?code=MQuG7BIE3eXBaCZ/YCfY1SHabm55HEphpNLmh1OP3hdfHkvI2QwPrw==</Item>
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

Den `<InputClaims>` elementet definierar vilka anspråk som skickas från IEF till REST-tjänst. I det här exemplet innehållet i anspråket `givenName` kommer att skickas till REST-tjänst som anspråket `email`.  

Den `<OutputClaims>` elementet definierar vilka anspråk som förväntas innehålla IEF från REST-tjänst. Oavsett hur många anspråk som tas emot i IEF kommer att använda dem som anges här. I det här exemplet emot ett anspråk som `city` mappas till en IEF anspråk som kallas `city`.

## <a name="step-3-add-the-new-claim-city-to-the-schema-of-your-trustframeworkextensionsxml-file"></a>Steg 3: Lägg till nytt anspråk `city` schemat för din TrustFrameworkExtensions.xml-fil

Anspråket `city` inte ännu har definierats var som helst i vår schemat. Lägg därför till en definition i elementet `<BuildingBlocks>`. Du hittar det här elementet i början av filen TrustFrameworkExtensions.xml.

```XML
<BuildingBlocks>
    <!--The claimtype city must be added to the TrustFrameworkPolicy-->
    <!-- You can add new claims in the BASE file Section III, or in the extensions file-->
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

## <a name="step-4-include-the-rest-service-claims-exchange-as-an-orchestration-step-in-your-profile-edit-user-journey-in-trustframeworkextensionsxml"></a>Steg 4: Inkludera anspråksutbytet som REST-tjänsten som en orkestrering steg i din profil redigera användarresan i TrustFrameworkExtensions.xml

Lägga till ett steg för att profilen redigera användarresa, när användaren har autentiserats (orchestration-steg 1-4 i följande XML) och användaren har tillhandahållit uppdaterade profilinformation (steg 5).

> [!NOTE]
> Det finns många användningsområden där REST API-anrop kan användas som ett orchestration-steg. Som ett orchestration-steg kan användas den som en uppdatering till ett externt system när en användare har genomgått en aktivitet, som registrering för första gången eller som en Profiluppdatering av för att synkronisera informationen. I det här fallet används det att utöka informationen till programmet när profilen har redigerat.

Kopiera profilen Redigera användare resa XML-kod från filen TrustFrameworkBase.xml i TrustFrameworkExtensions.xml-filen i den `<UserJourneys>` element. Sedan göra ändringen under steg 6.

```XML
<OrchestrationStep Order="6" Type="ClaimsExchange">
    <ClaimsExchanges>
        <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-LookUpLoyaltyWebHook" />
    </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> Om ordningen som inte matchar din version, kontrollerar du infoga kod som ett steg före den `ClaimsExchange` typ `SendClaims`.

Sista XML för användarresan bör se ut så här:

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

## <a name="step-5-add-the-claim-city-to-your-relying-party-policy-file-so-the-claim-is-sent-to-your-application"></a>Steg 5: Lägg till anspråket `city` till den förlitande parten princip-fil så att anspråket skickas till ditt program

Redigera filen ProfileEdit.xml förlitande part (RP) och ändra den `<TechnicalProfile Id="PolicyProfile">` element att lägga till följande: `<OutputClaim ClaimTypeReferenceId="city" />`.

När du lägger till nytt anspråk den tekniska profilen ser ut så här:

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

## <a name="step-6-upload-your-changes-and-test"></a>Steg 6: Överföra dina ändringar och testa

Skriv över de befintliga versionerna av principen.

1.  (Valfritt:) Spara den befintliga versionen (genom att ladda ned) i din tillägg fil innan du fortsätter. Om du vill behålla inledande komplexiteten med låg, rekommenderar vi att du inte ladda upp flera versioner av tilläggsfilen.
2.  (Valfritt:) Byt namn på den nya versionen av princip-ID för att redigera filen genom att ändra `PolicyId="B2C_1A_TrustFrameworkProfileEdit"`.
3.  Ladda upp tilläggsfilen.
4.  Ladda upp principen redigera RP-fil.
5.  Använd **kör nu** att testa principen. Granska den token som IEF tillbaka till programmet.

Om allt är korrekt konfigurerad, token tas nytt anspråk `city`, med värdet `Redmond`.

```JSON
{
  "exp": 1493053292,
  "nbf": 1493049692,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "sub": "a58e7c6c-7535-4074-93da-b0023fbaf3ac",
  "aud": "4e87c1dd-e5f5-4ac8-8368-bc6a98751b8b",
  "acr": "b2c_1a_trustframeworkprofileedit",
  "nonce": "defaultNonce",
  "iat": 1493049692,
  "auth_time": 1493049692,
  "city": "Redmond"
}
```

## <a name="next-steps"></a>Nästa steg

[Använd en REST API som ett steg för verifiering](active-directory-b2c-rest-api-validation-custom.md)

[Ändra profilredigering för att samla in ytterligare information från användarna](active-directory-b2c-create-custom-attributes-profile-edit-custom.md)
