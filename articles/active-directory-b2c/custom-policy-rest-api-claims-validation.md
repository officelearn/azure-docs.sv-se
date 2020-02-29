---
title: REST API anspråks utbyten som verifiering
titleSuffix: Azure AD B2C
description: En genom gång för att skapa en Azure AD B2C användar resa som samverkar med RESTful-tjänster.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7100498d99068941bcd7ca48b6cbcaa271fbb095
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/29/2020
ms.locfileid: "78189080"
---
# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-validation-on-user-input"></a>Genom gång: integrera REST API Claims-utbyten i Azure AD B2C användar resa som verifiering vid användarindata

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

IEF (Identity Experience Framework) Azure Active Directory B2C (Azure AD B2C) gör det möjligt för identitets utvecklaren att integrera en interaktion med ett RESTful-API i en användar resa.

I slutet av den här genom gången kommer du att kunna skapa en Azure AD B2C användar resa som interagerar med RESTful-tjänster.

IEF skickar data i anspråk och tar emot data tillbaka i anspråk. Interaktionen med API: et:

- Kan utformas som en REST API anspråk eller som en validerings profil, vilket sker i ett Orchestration-steg.
- Validerar vanligt vis indata från användaren. Om värdet från användaren avvisas kan användaren försöka att ange ett giltigt värde med möjlighet att returnera ett fel meddelande.

Du kan också utforma interaktionen som ett Orchestration-steg. Mer information finns i [genom gång: integrera REST API Claims-utbyten i Azure AD B2C användar resa som ett Dirigerings steg](custom-policy-rest-api-claims-exchange.md).

För exempel på validerings profil använder vi profil redigera användar resa i Start paket filen ProfileEdit. xml.

Vi kan kontrol lera att namnet som anges av användaren i profil redigering inte ingår i en undantags lista.

## <a name="prerequisites"></a>Förutsättningar

- En Azure AD B2C klient som har kon figurer ATS för att slutföra en lokal konto registrering/inloggning, enligt beskrivningen i [komma igång](custom-policy-get-started.md).
- En REST API slut punkt att interagera med. I den här genom gången har vi konfigurerat en demo webbplats som heter [WingTipGames](https://wingtipgamesb2c.azurewebsites.net/) med en REST API-tjänst.

## <a name="step-1-prepare-the-rest-api-function"></a>Steg 1: Förbered REST API-funktionen

> [!NOTE]
> Installationen av REST API funktioner ligger utanför omfånget för den här artikeln. [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-reference) innehåller ett utmärkt verktyg för att skapa RESTful-tjänster i molnet.

Vi har skapat en Azure-funktion som tar emot ett anspråk som förväntas som `playerTag`. Funktionen validerar om detta påstående finns. Du kan komma åt den fullständiga Azure Function-koden i [GitHub](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/AzureFunctionsSamples).

```csharp
if (requestContentAsJObject.playerTag == null)
{
  return request.CreateResponse(HttpStatusCode.BadRequest);
}

var playerTag = ((string) requestContentAsJObject.playerTag).ToLower();

if (playerTag == "mcvinny" || playerTag == "msgates123" || playerTag == "revcottonmarcus")
{
  return request.CreateResponse<ResponseContent>(
    HttpStatusCode.Conflict,
    new ResponseContent
    {
      version = "1.0.0",
      status = (int) HttpStatusCode.Conflict,
      userMessage = $"The player tag '{requestContentAsJObject.playerTag}' is already used."
    },
    new JsonMediaTypeFormatter(),
    "application/json");
}

return request.CreateResponse(HttpStatusCode.OK);
```

IEF förväntar sig att det `userMessage` anspråk som Azure-funktionen returnerar. Detta anspråk visas som en sträng för användaren om verifieringen Miss lyckas, till exempel när en status för 409-konflikt returneras i föregående exempel.

## <a name="step-2-configure-the-restful-api-claims-exchange-as-a-technical-profile-in-your-trustframeworkextensionsxml-file"></a>Steg 2: Konfigurera RESTful-API-anspråk Exchange som en teknisk profil i din TrustFrameworkExtensions. XML-fil

En teknisk profil är den fullständiga konfigurationen av Exchange som önskas med RESTful-tjänsten. Öppna filen TrustFrameworkExtensions. xml och Lägg till följande XML-kodfragment inuti `<ClaimsProviders>`-elementet.

> [!NOTE]
> I följande XML beskrivs RESTful-providern `Version=1.0.0.0` som protokoll. Tänk på det som den funktion som ska interagera med den externa tjänsten. <!-- TODO: A full definition of the schema can be found...link to RESTful Provider schema definition>-->

```xml
<ClaimsProvider>
    <DisplayName>REST APIs</DisplayName>
    <TechnicalProfiles>
        <TechnicalProfile Id="AzureFunctions-CheckPlayerTagWebHook">
            <DisplayName>Check Player Tag Web Hook Azure Function</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <Metadata>
                <Item Key="ServiceUrl">https://wingtipb2cfuncs.azurewebsites.net/api/CheckPlayerTagWebHook?code=L/05YRSpojU0nECzM4Tp3LjBiA2ZGh3kTwwp1OVV7m0SelnvlRVLCg==</Item>
                <Item Key="SendClaimsIn">Body</Item>
                <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
                <Item Key="AuthenticationType">None</Item>
                <!-- REMOVE the following line in production environments -->
                <Item Key="AllowInsecureAuthInProduction">true</Item>
            </Metadata>
            <InputClaims>
                <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="playerTag" />
            </InputClaims>
            <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
        <TechnicalProfile Id="SelfAsserted-ProfileUpdate">
            <ValidationTechnicalProfiles>
                <ValidationTechnicalProfile ReferenceId="AzureFunctions-CheckPlayerTagWebHook" />
            </ValidationTechnicalProfiles>
        </TechnicalProfile>
    </TechnicalProfiles>
</ClaimsProvider>
```

`InputClaims`-elementet definierar de anspråk som ska skickas från IEF till REST-tjänsten. I det här exemplet skickas innehållet i anspråks `givenName` till REST-tjänsten som `playerTag`. I det här exemplet förväntar sig IEF inte anspråk tillbaka. I stället väntar den på ett svar från REST-tjänsten och fungerar baserat på de status koder som den tar emot.

Kommentarerna ovan `AuthenticationType` och `AllowInsecureAuthInProduction` anger vilka ändringar du ska göra när du flyttar till en produktions miljö. Information om hur du skyddar dina RESTful-API: er för produktion finns i [skydda RESTful-API: er med grundläggande autentisering](secure-rest-api-dotnet-basic-auth.md) och [säkra RESTful-API: er med certifikatbaserad autentisering](secure-rest-api-dotnet-certificate-auth.md).

## <a name="step-3-include-the-restful-service-claims-exchange-in-self-asserted-technical-profile-where-you-want-to-validate-the-user-input"></a>Steg 3: ta med RESTful service anspråk Exchange i självkontrollerad teknisk profil där du vill verifiera användarindata

Den vanligaste användningen av validerings steget är i interaktionen med en användare. Alla interaktioner där användaren förväntas tillhandahålla indata är *självkontrollerade tekniska profiler*. I det här exemplet ska vi lägga till valideringen i den självkontrollerade ProfileUpdate tekniska profilen. Det här är den tekniska profil som den förlitande part (RP)-princip filen `Profile Edit` använder.

Så här lägger du till anspråks utbytet i den självkontrollerade tekniska profilen:

1. Öppna filen TrustFrameworkBase. xml och Sök efter `<TechnicalProfile Id="SelfAsserted-ProfileUpdate">`.
2. Granska konfigurationen av den här tekniska profilen. Observera hur Exchange med användaren definieras som anspråk som kommer att uppmanas till användaren (ingående anspråk) och anspråk som kommer att förväntas från den självkontrollerade providern (utgående anspråk).
3. Sök efter `TechnicalProfileReferenceId="SelfAsserted-ProfileUpdate`och Lägg märke till att den här profilen anropas som dirigerings steg 5 i `<UserJourney Id="ProfileEdit">`.

## <a name="step-4-upload-and-test-the-profile-edit-rp-policy-file"></a>Steg 4: Ladda upp och testa profilen redigera RP-princip filen

1. Ladda upp den nya versionen av filen TrustFrameworkExtensions. xml.
2. Använd **Kör nu** för att testa profilen redigera RP-principagenten.
3. Testa verifieringen genom att ange ett av de befintliga namnen (till exempel mcvinny) i fältet med det **aktuella namnet** . Om allt är korrekt konfigurerat bör du få ett meddelande som meddelar användaren att Player-taggen redan används.

## <a name="next-steps"></a>Nästa steg

[Ändra profil redigering och användar registrering för att samla in ytterligare information från dina användare](custom-policy-custom-attributes.md)

[Genom gång: integrera REST API Claims-utbyten i Azure AD B2C användar resa som ett Orchestration-steg](custom-policy-rest-api-claims-exchange.md)

[Referens: RESTful teknisk profil](restful-technical-profile.md)

Information om hur du skyddar dina API: er finns i följande artiklar:

* [Skydda ditt RESTful-API med grundläggande autentisering (användar namn och lösen ord)](secure-rest-api-dotnet-basic-auth.md)
* [Skydda ditt RESTful-API med klient certifikat](secure-rest-api-dotnet-certificate-auth.md)
