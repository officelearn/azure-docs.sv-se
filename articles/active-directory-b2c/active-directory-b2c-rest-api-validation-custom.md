---
title: REST API-anspråk utbyten som verifiering i Azure Active Directory B2C | Microsoft Docs
description: Ett ämne på Azure Active Directory B2C anpassade principer.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/24/2017
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: b08c5e6f2bc7d7970c47e14db84f4172e92eb820
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55203665"
---
# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-validation-on-user-input"></a>Genomgång: Integrera REST API anspråk Utbytena i din Azure AD B2C-användarresa som verifiering på indata från användaren

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den identitet upplevelse Framework (IEF) som ligger till grund Azure Active Directory B2C (Azure AD B2C) gör det möjligt för identity-utvecklare att integrera en interaktion med ett RESTful-API i en användarresa.  

I slutet av den här genomgången kommer du att kunna skapa en Azure AD B2C-användarresa som interagerar med RESTful-tjänster.

IEF skickar data i anspråk och tar emot data i anspråk. Interaktion med API:

- Kan utformas som en REST API-anspråksutbytet eller som en verifieringsprofil som sker i orchestration-steg.
- Vanligtvis verifierar indata från användaren. Om värdet från användaren avvisas, kan användaren försöka igen att ange ett giltigt värde med möjlighet att returnera ett felmeddelande.

Du kan också utforma interaktion som ett orchestration-steg. Mer information finns i [genomgång: Integrera REST API anspråk Utbytena i din Azure AD B2C-användarresa som ett orchestration-steg](active-directory-b2c-rest-api-step-custom.md).

Exempelvis den verifiering profil använder vi profilen redigera användarresa i pack startfil ProfileEdit.xml.

Vi kan kontrollera att namnet anges av användaren i profilredigering inte är en del av en undantagslista.

## <a name="prerequisites"></a>Förutsättningar

- En Azure AD B2C-klient som konfigurerats för att slutföra ett lokalt konto registrerings-registreringen/inloggning, enligt beskrivningen i [komma igång](active-directory-b2c-get-started-custom.md).
- En REST API-slutpunkt för att interagera med. Den här genomgången ska vi har konfigurerat en demo-webbplatsen som heter [WingTipGames](https://wingtipgamesb2c.azurewebsites.net/) med en REST API-tjänst.

## <a name="step-1-prepare-the-rest-api-function"></a>Steg 1: Förbereda REST API-funktion

> [!NOTE]
> Installationen av REST API-funktioner inte omfattas av den här artikeln. [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-reference) tillhandahåller en utmärkt toolkit för att skapa RESTful-tjänster i molnet.

Vi har skapat en Azure-funktion som tar emot ett anspråk som det förväntar sig som `playerTag`. Funktionen kontrollerar om det här kravet finns. Du kan komma åt fullständig Azure function-koden i [GitHub](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/AzureFunctionsSamples).

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

IEF förväntar sig den `userMessage` anspråk som Azure function returnerar. Det här anspråket visas igen som en sträng till användaren om verifieringen misslyckas, till exempel när statusen 409 konflikt returneras i föregående exempel.

## <a name="step-2-configure-the-restful-api-claims-exchange-as-a-technical-profile-in-your-trustframeworkextensionsxml-file"></a>Steg 2: Konfigurera exchange för RESTful-API-anspråk som en tekniska profil i filen TrustFrameworkExtensions.xml

Tekniska profilen är fullständig konfiguration av exchange som önskas med RESTful-tjänst. Öppna filen TrustFrameworkExtensions.xml och Lägg till följande XML-kodstycke i den `<ClaimsProviders>` element.

> [!NOTE]
> I följande XML, RESTful-providern `Version=1.0.0.0` beskrivs som protokoll. Överväg att som den funktion som kommer att interagera med den externa tjänsten. <!-- TODO: A full definition of the schema can be found...link to RESTful Provider schema definition>-->

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

Den `InputClaims` elementet definierar vilka anspråk som skickas från IEF till REST-tjänst. I det här exemplet innehållet i anspråket `givenName` kommer att skickas till REST-tjänst som `playerTag`. I det här exemplet på IEF förväntar sig inte anspråk tillbaka. I stället väntar det på svar från REST-tjänst och åtgärder baserat på de statuskoder som den tar emot.

## <a name="step-3-include-the-restful-service-claims-exchange-in-self-asserted-technical-profile-where-you-want-to-validate-the-user-input"></a>Steg 3: Inkludera anspråksutbytet RESTful-tjänst i självkontrollerad tekniska profil där du vill verifiera användardata

Den vanligaste tillämpningen stegets verifiering är i interaktionen med en användare. All interaktion där användaren förväntas som indata är *lokal verifieringsvillkor tekniska profiler*. I det här exemplet vi lägger till verifieringen av den tekniska profilen Klientportal Asserted ProfileUpdate. Det här är tekniska profil som förlitande part (RP) principfilen `Profile Edit` använder.

Lägga till anspråksutbytet självkontrollerad tekniska profil:

1. Öppna filen TrustFrameworkBase.xml och Sök efter `<TechnicalProfile Id="SelfAsserted-ProfileUpdate">`.
2. Granska konfigurationen av den här tekniska profilen. Se hur exchange med användaren som har definierats som anspråk som blir ombedd av användaren (inkommande anspråk) och anspråk som kommer att förväntas från självkontrollerad providern (utgående anspråk).
3. Sök efter `TechnicalProfileReferenceId="SelfAsserted-ProfileUpdate`, och Lägg märke till att den här profilen har anropats som orchestration-steg 5 i `<UserJourney Id="ProfileEdit">`.

## <a name="step-4-upload-and-test-the-profile-edit-rp-policy-file"></a>Steg 4: Ladda upp och testa principfilen som profilen redigera RP

1. Ladda upp den nya versionen av filen TrustFrameworkExtensions.xml.
2. Använd **kör nu** att testa profilen redigera RP-principfil.
3. Testa verifieringen genom att ange någon av de befintliga (till exempel mcvinny) i den **Förnamn** fält. Om allt är korrekt konfigurerad, bör du få ett meddelande som meddelar användaren att taggen player används redan.

## <a name="next-steps"></a>Nästa steg

[Ändra profil redigerings- och registreringen för att samla in ytterligare information från användarna](active-directory-b2c-create-custom-attributes-profile-edit-custom.md)

[Genomgång: Integrera REST API anspråk Utbytena i din Azure AD B2C-användarresa som ett orchestration-steg](active-directory-b2c-rest-api-step-custom.md)
