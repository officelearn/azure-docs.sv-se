---
title: "Azure Active Directory B2C: REST API-anspråk utbyte som ett orchestration-steg | Microsoft Docs"
description: "Ett ämne på Azure Active Directory B2C anpassade principer som integreras med en API"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: mtillman
editor: rojasja
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/24/2017
ms.author: joroja
ms.openlocfilehash: 3e4f0bccf02c0332663a746d4ed8e5234c51f54e
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-an-orchestration-step"></a>Genomgång: Integrera utbyte av REST API-anspråk i din Azure AD B2C användaren resa som ett orchestration-steg

Den identitet upplevelse Framework (IEF) för Azure Active Directory B2C det (Azure AD B2C) gör det möjligt för identitet utvecklare att integrera en interaktion med en RESTful-API i en resa för användaren.  

I slutet av den här genomgången kommer du att kunna skapa en Azure AD B2C användaren resa som interagerar med RESTful-tjänster.

IEF skickar data i anspråk och tar emot data tillbaka i anspråk. REST API anspråk exchange:

- Kan utformas som en orchestration-steg.
- Kan utlösa en extern åtgärd. Det kan till exempel logga en händelse i en extern databas.
- Kan användas för att hämta ett värde och sedan lagra den i databasen.

Du kan använda mottagna anspråk senare ändra flödet av körningen.

Du kan också utforma interaktionen som en verifieringsprofil. Mer information finns i [genomgång: integrera REST API-anspråk utbyte i din Azure AD B2C användaren resa som autentiserades på indata från användaren](active-directory-b2c-rest-api-validation-custom.md).

Scenariot är att när användaren utför en profil-redigering, vi vill:

1. Leta upp användaren i ett externt system.
2. Hämta den stad där användaren har registrerats.
3. Returnera attributet till programmet som ett anspråk.

## <a name="prerequisites"></a>Krav

- En Azure AD B2C-klient som konfigurerats för att slutföra ett lokalt konto sign-upp/inloggning, enligt beskrivningen i [komma igång](active-directory-b2c-get-started-custom.md).
- En REST API-slutpunkt kan interagera med. Den här genomgången använder en enkel Azure funktionen app webhook som ett exempel.
- *Rekommenderade*: Slutför den [REST API-anspråk exchange genomgång som en verifiering steg](active-directory-b2c-rest-api-validation-custom.md).

## <a name="step-1-prepare-the-rest-api-function"></a>Steg 1: Förbered REST API-funktion

> [!NOTE]
> Installationen av REST API-funktioner som ligger utanför omfånget för den här artikeln. [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-reference) ger en utmärkt toolkit för att skapa RESTful-tjänster i molnet.

Vi har konfigurerat en Azure-funktion som tar emot ett anspråk som kallas `email`, och returnerar sedan anspråket `city` med tilldelade värdet för `Redmond`. Exemplet Azure funktionen finns på [GitHub](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/AzureFunctionsSamples).

Den `userMessage` anspråk som returnerar funktionen Azure är valfri i den här kontexten och IEF ignoreras den. Du kan eventuellt använda den som ett meddelande skickas till programmet och visas för användaren senare.

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

En funktionsapp i Azure-gör det enkelt att hämta funktions-URL som innehåller identifierare för specifik funktion. I det här fallet URL-Adressen är: https://wingtipb2cfuncs.azurewebsites.net/api/LookUpLoyaltyWebHook?code=MQuG7BIE3eXBaCZ/YCfY1SHabm55HEphpNLmh1OP3hdfHkvI2QwPrw==. Du kan använda den för att testa.

## <a name="step-2-configure-the-restful-api-claims-exchange-as-a-technical-profile-in-your-trustframeworextensionsxml-file"></a>Steg 2: Konfigurera exchange för RESTful-API-anspråk som en teknisk profil i filen TrustFrameworExtensions.xml

En teknisk profil är den fullständiga konfigurationen av exchange önskad med RESTful-tjänsten. Öppna filen TrustFrameworkExtensions.xml och Lägg till följande XML-kodstycke i den `<ClaimsProvider>` element.

> [!NOTE]
> I följande XML RESTful-providern `Version=1.0.0.0` beskrivs som protokoll. Överväg att som den funktion som ska interagera med externa-tjänsten. <!-- TODO: A full definition of the schema can be found...link to RESTful Provider schema definition>-->

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

Den `<InputClaims>` elementet definierar vilka anspråk som skickas från IEF REST-tjänst. I det här exemplet innehållet i anspråket `givenName` kommer att skickas till REST-tjänst som anspråket `email`.  

Den `<OutputClaims>` elementet definierar vilka anspråk som IEF ska förvänta sig från REST-tjänst. Oavsett vilket antal anspråk som tas emot av IEF kommer att använda dem som anges här. I det här exemplet ett anspråk som tas emot som `city` kommer att mappas till en IEF anspråk som kallas `city`.

## <a name="step-3-add-the-new-claim-city-to-the-schema-of-your-trustframeworkextensionsxml-file"></a>Steg 3: Lägg till nytt anspråk `city` i schemat för TrustFrameworkExtensions.xml-fil

Anspråket `city` är ännu inte definierats var som helst i vår schemat. Lägg därför till en definition i elementet `<BuildingBlocks>`. Du hittar det här elementet i början av filen TrustFrameworkExtensions.xml.

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

## <a name="step-4-include-the-rest-service-claims-exchange-as-an-orchestration-step-in-your-profile-edit-user-journey-in-trustframeworkextensionsxml"></a>Steg 4: Ta REST service anspråk exchange som en orchestration-steg i din profil Redigera användare resa i TrustFrameworkExtensions.xml

Lägga till ett steg till profilen Redigera användare resan, efter att användaren har autentiserats (orchestration-steg 1 – 4 i följande XML) och användaren har angett informationen uppdaterade profilen (steg 5).

> [!NOTE]
> Det finns många användningsområden där REST API-anrop kan användas som ett orchestration-steg. Som ett orchestration-steg kan den användas som en uppdatering för ett externt system när en användare har slutfört en uppgift som första gången registrering eller som en uppdatering av spårningsprofil för att synkronisera informationen. I det här fallet används det att utöka informationen till programmet när profilen har redigerat.

Kopiera profilen Redigera användare resa XML-koden från filen TrustFrameworkBase.xml i filen TrustFrameworkExtensions.xml inuti den `<UserJourneys>` element. Gör ändringar i steg 6.

```XML
<OrchestrationStep Order="6" Type="ClaimsExchange">
    <ClaimsExchanges>
        <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-LookUpLoyaltyWebHook" />
    </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> Om ordern inte överensstämmer med din version, kontrollerar du att du infogar koden som steg innan den `ClaimsExchange` typen `SendClaims`.

Sista XML för transporten användare bör se ut så här:

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

## <a name="step-5-add-the-claim-city-to-your-relying-party-policy-file-so-the-claim-is-sent-to-your-application"></a>Steg 5: Lägg till anspråket `city` till den förlitande parten princip filen så att begäran skickas till ditt program

Redigera filen ProfileEdit.xml förlitande part (RP) och ändra den `<TechnicalProfile Id="PolicyProfile">` element att lägga till följande: `<OutputClaim ClaimTypeReferenceId="city" />`.

När du lägger till nya anspråk, tekniska profilen ser ut så här:

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

1.  (Valfritt:) Spara den befintliga versionen (genom att ladda ned) av tilläggsfilen innan du fortsätter. Om du vill behålla inledande komplexitet låg, rekommenderar vi att du inte överföra flera versioner av tilläggsfilen.
2.  (Valfritt:) Byt namn på den nya versionen av princip-ID för att redigera filen genom att ändra `PolicyId="B2C_1A_TrustFrameworkProfileEdit"`.
3.  Ladda upp tilläggsfilen.
4.  Överför principfilen redigera RP.
5.  Använd **kör nu** att testa principen. Granska den token som IEF returnerar till programmet.

Om allt är korrekt konfigurerat, token tas nytt anspråk `city`, med värdet `Redmond`.

```JSON
{
  "exp": 1493053292,
  "nbf": 1493049692,
  "ver": "1.0",
  "iss": "https://login.microsoftonline.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
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

[Använd en REST-API som en verifiering steg](active-directory-b2c-rest-api-validation-custom.md)

[Ändra profil Redigera för att samla in ytterligare information från användarna](active-directory-b2c-create-custom-attributes-profile-edit-custom.md)
