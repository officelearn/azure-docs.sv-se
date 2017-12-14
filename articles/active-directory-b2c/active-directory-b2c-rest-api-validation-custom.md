---
title: "Azure Active Directory B2C: REST API-anspråk utbyte som verifiering | Microsoft Docs"
description: "Ett ämne på Azure Active Directory B2C anpassade principer"
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
ms.openlocfilehash: dfd33a9ecdce7b21f58660fb39a5f2d7b4ce6f43
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-validation-on-user-input"></a>Genomgång: Integrera utbyte av REST API-anspråk i din Azure AD B2C användaren resa som autentiserades på indata från användaren

Den identitet upplevelse Framework (IEF) för Azure Active Directory B2C det (Azure AD B2C) gör det möjligt för identitet utvecklare att integrera en interaktion med en RESTful-API i en resa för användaren.  

I slutet av den här genomgången kommer du att kunna skapa en Azure AD B2C användaren resa som interagerar med RESTful-tjänster.

IEF skickar data i anspråk och tar emot data tillbaka i anspråk. Interaktion med API:

- Kan utformas som en REST API anspråk exchange eller verifieringsprofil som görs i ett orchestration-steg.
- Vanligtvis verifierar indata från användaren. Om värdet från användaren avvisas kan användaren försöka igen att ange ett giltigt värde med möjlighet att returnera ett felmeddelande.

Du kan också utforma interaktionen som ett orchestration-steg. Mer information finns i [genomgång: integrera REST API-anspråk utbyte i din Azure AD B2C användaren resa som ett orchestration-steg](active-directory-b2c-rest-api-step-custom.md).

Exempelvis den verifiering profil använder vi profil Redigera användare resa i pack startfil ProfileEdit.xml.

Vi kan kontrollera att namnet som användaren i profilen Redigera inte är en del av en undantagslista.

## <a name="prerequisites"></a>Krav

- En Azure AD B2C-klient som konfigurerats för att slutföra ett lokalt konto sign-upp/inloggning, enligt beskrivningen i [komma igång](active-directory-b2c-get-started-custom.md).
- En REST API-slutpunkt kan interagera med. Den här genomgången vi har konfigurerat en demo-webbplatsen som heter [WingTipGames](https://wingtipgamesb2c.azurewebsites.net/) med en REST API-tjänst.

## <a name="step-1-prepare-the-rest-api-function"></a>Steg 1: Förbered REST API-funktion

> [!NOTE]
> Installationen av REST API-funktioner som ligger utanför omfånget för den här artikeln. [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-reference) ger en utmärkt toolkit för att skapa RESTful-tjänster i molnet.

Vi har skapat en Azure-funktion som tar emot ett anspråk som den förväntas som `playerTag`. Funktionen verifierar om detta anspråk finns. Du har åtkomst till fullständig Azure Funktionskoden i [GitHub](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/AzureFunctionsSamples).

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

IEF förväntar sig den `userMessage` anspråk som returnerar funktionen Azure. Det här anspråket visas som en sträng till användaren om valideringen misslyckas, till exempel när status 409 konflikt returneras i föregående exempel.

## <a name="step-2-configure-the-restful-api-claims-exchange-as-a-technical-profile-in-your-trustframeworkextensionsxml-file"></a>Steg 2: Konfigurera exchange för RESTful-API-anspråk som en teknisk profil i filen TrustFrameworkExtensions.xml

En teknisk profil är den fullständiga konfigurationen av exchange önskad med RESTful-tjänsten. Öppna filen TrustFrameworkExtensions.xml och Lägg till följande XML-kodstycke i den `<ClaimsProviders>` element.

> [!NOTE]
> I följande XML RESTful-providern `Version=1.0.0.0` beskrivs som protokoll. Överväg att som den funktion som ska interagera med externa-tjänsten. <!-- TODO: A full definition of the schema can be found...link to RESTful Provider schema definition>-->

```xml
<ClaimsProvider>
    <DisplayName>REST APIs</DisplayName>
    <TechnicalProfiles>
        <TechnicalProfile Id="AzureFunctions-CheckPlayerTagWebHook">
            <DisplayName>Check Player Tag Web Hook Azure Function</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <Metadata>
                <Item Key="ServiceUrl">https://wingtipb2cfuncs.azurewebsites.net/api/CheckPlayerTagWebHook?code=L/05YRSpojU0nECzM4Tp3LjBiA2ZGh3kTwwp1OVV7m0SelnvlRVLCg==</Item>
                <Item Key="AuthenticationType">None</Item>
                <Item Key="SendClaimsIn">Body</Item>
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

Den `InputClaims` elementet definierar vilka anspråk som skickas från IEF REST-tjänst. I det här exemplet innehållet i anspråket `givenName` kommer att skickas till REST-tjänst som `playerTag`. I detta exempel förväntar på IEF sig inte anspråk tillbaka. I stället väntar på svar från REST-tjänst och åtgärder baserat på statuskoder som tas emot.

## <a name="step-3-include-the-restful-service-claims-exchange-in-self-asserted-technical-profile-where-you-want-to-validate-the-user-input"></a>Steg 3: Inkludera RESTful-tjänst anspråk exchange i själva uppgavs tekniska profil där du vill verifiera användardata

De vanligaste används steget verifiering i interaktion med användaren. All interaktion där användaren förväntas ge indata är *själva vars tekniska profiler*. I det här exemplet ska vi lägga till valideringen oberoende Asserted ProfileUpdate tekniska profilen. Det här är tekniska profil som förlitande part (RP) principfilen `Profile Edit` använder.

Lägga till anspråk exchange till själva uppgavs tekniska profilen:

1. Öppna filen TrustFrameworkBase.xml och Sök efter `<TechnicalProfile Id="SelfAsserted-ProfileUpdate">`.
2. Granska konfigurationen av den här tekniska profilen. Se hur exchange med användaren har definierats som anspråk som blir ombedd av användaren (inkommande anspråk) och anspråk som förväntas från själva uppgavs providern (utgående anspråk).
3. Sök efter `TechnicalProfileReferenceId="SelfAsserted-ProfileUpdate`, och Lägg märke till att den här profilen anropas som orchestration-steg 4 i `<UserJourney Id="ProfileEdit">`.

## <a name="step-4-upload-and-test-the-profile-edit-rp-policy-file"></a>Steg 4: Överför och testa redigera RP princip profilfilen

1. Ladda upp den nya versionen av filen TrustFrameworkExtensions.xml.
2. Använd **kör nu** att testa profilen redigera RP principfilen.
3. Testa verifieringen genom att tillhandahålla en av de befintliga (till exempel mcvinny) i den **Förnamn** fältet. Om allt är korrekt konfigurerat, kan du bör få ett meddelande som meddelar användaren att taggen player används redan.

## <a name="next-steps"></a>Nästa steg

[Ändra profil redigera och användaren registrering för att samla in ytterligare information från användarna](active-directory-b2c-create-custom-attributes-profile-edit-custom.md)

[Genomgång: Integrera utbyte av REST API-anspråk i din Azure AD B2C användaren resa som ett orchestration-steg](active-directory-b2c-rest-api-step-custom.md)
