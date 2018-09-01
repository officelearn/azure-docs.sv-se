---
title: Integrera REST API anspråk Utbytena i din Azure Active Directory B2C-användarresa | Microsoft Docs
description: Integrera REST API anspråk Utbytena i din Azure AD B2C-användarresa som verifiering av indata från användaren.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/30/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: e3d938c4464fc5141b97f85220bf096920e17d00
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/31/2018
ms.locfileid: "43339601"
---
# <a name="integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-validation-of-user-input"></a>Integrera REST API anspråk Utbytena i din Azure AD B2C-användarresa som verifiering av indata från användaren

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Med Identitetsramverk, som ligger till grund för Azure Active Directory B2C (Azure AD B2C) som du kan integrera med en RESTful-API i en användarresa. I den här genomgången får du lära dig hur Azure AD B2C interagerar med .NET Framework RESTful-tjänster (webb-API).

## <a name="introduction"></a>Introduktion
Med hjälp av Azure AD B2C kan du lägga till egen affärslogik en användarresa genom att anropa ditt eget RESTful-tjänst. Den Identitetsramverk skickar data till RESTful-tjänst i ett *inkommande anspråk* samling och tar emot data från RESTful i en *utgående anspråk* samling. Med RESTful-tjänst-integrering kan du:

* **Verifiera användarens indata**: den här åtgärden förhindrar att felaktigt beständig lagring till Azure AD. Om värdet från användaren inte är giltigt, returnerar RESTful-tjänst ett felmeddelande som anger att användaren anger en post. Du kan till exempel kontrollera att den e-postadress som anges av användaren finns i din kunddatabas.
* **Skriv över inkommande anspråk**: om en användare anger det första namnet i gemener och versaler bokstäverna kan du formatera namnet med bara den första bokstaven med versaler.
* **Utöka användardata genom att ytterligare integrera med företagets line-of-business-program**: Your RESTful-tjänst kan ta emot användarens e-postadress, fråga kundens databas och returnera användarens lojalitet talet till Azure AD B2C. Avkastningen anspråk kan lagras i användarens Azure AD-konto utvärderas i nästa *Orchestration-steg*, eller ingår i åtkomsttoken.
* **Kör anpassad affärslogik**: du kan skicka push-meddelanden, uppdatera företagets databaser, kör en process för migrering av användare, hantera behörigheter, granska databaser och utföra andra åtgärder.

Du kan utforma integrering med RESTful-tjänster på följande sätt:

* **Teknisk verifieringsprofil**: anropet till RESTful-tjänst som sker inom den tekniska profilen verifieringen av den angivna tekniska profilen. Den tekniska profilen verifiering verifierar data från användare innan användarresa flyttar framåt. Med den tekniska profilen verifiering kan du:
   * Skicka inkommande anspråk.
   * Validera de inkommande anspråken och utlösa anpassade felmeddelanden.
   * Skicka tillbaka utgående anspråk.

* **Exchange-anspråk**: den här designen liknar den tekniska profilen verifiering, men det sker inom ett orchestration-steg. Den här definitionen är begränsat till:
   * Skicka inkommande anspråk.
   * Skicka tillbaka utgående anspråk.

## <a name="restful-walkthrough"></a>RESTful genomgång
I den här genomgången kan du utveckla en .NET Framework webb-API som verifierar indata från användaren och innehåller lojalitet flera användare. Till exempel ditt program kan använda för att bevilja åtkomst till *Platina fördelar* baserat på antalet lojalitet.

Översikt:
* Utveckla RESTful-tjänst (.NET Framework webb-API).
* Använda RESTful-tjänst i användarresan.
* Skicka inkommande anspråk och läsa dem i din kod.
* Verifiera användarens förnamn.
* Skicka tillbaka ett lojalitet tal. 
* Lägg till lojalitet numret till en JSON Web Token (JWT).

## <a name="prerequisites"></a>Förutsättningar
Utför stegen i den [komma igång med anpassade principer](active-directory-b2c-get-started-custom.md) artikeln.

## <a name="step-1-create-an-aspnet-web-api"></a>Steg 1: Skapa ett ASP.NET webb-API

1. Skapa ett projekt i Visual Studio genom att välja **filen** > **New** > **projekt**.

2. I den **nytt projekt** väljer **Visual C#** > **Web** > **ASP.NET-Webbtillämpningsprogram (.NET Framework)**.

3. I den **namn** skriver ett namn för programmet (till exempel *Contoso.AADB2C.API*), och välj sedan **OK**.

    ![Skapa nya visual studio-projekt](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-create-project.png)

4. I den **nytt ASP.NET-webbprogram** väljer en **webb-API** eller **Azure API-app** mall.

    ![Välj mall för webb-API](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-select-web-api.png)

5. Se till att autentiseringen är inställd på **ingen autentisering**.

6. Klicka på **OK** för att skapa projektet.

## <a name="step-2-prepare-the-rest-api-endpoint"></a>Steg 2: Förbereda REST API-slutpunkt

### <a name="step-21-add-data-models"></a>Steg 2.1: Lägg till datamodeller
Modellerna representerar de inkommande anspråken och utgående anspråk data i dina RESTful-tjänst. Din kod läser indata med deserialisering av inkommande anspråk modellen från en JSON-sträng till ett C#-objekt (din modell). ASP.NET-webb-API deserializes automatiskt utgående anspråk modellen tillbaka till JSON och sedan skriver den serialiserade informationen och i brödtexten för HTTP-svarsmeddelande. 

Skapa en modell som representerar inkommande anspråk genom att göra följande:

1. Om Solution Explorer inte redan är öppen väljer **visa** > **Solution Explorer**. 
2. I Solution Explorer högerklickar du på mappen **Modeller**, välj **Lägg till** och sedan **Klass**.

    ![Lägg till modell](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-model.png)

3. Namnge klassen `InputClaimsModel`, och Lägg sedan till följande egenskaper för att den `InputClaimsModel` klass:

    ```csharp
    namespace Contoso.AADB2C.API.Models
    {
        public class InputClaimsModel
        {
            public string email { get; set; }
            public string firstName { get; set; }
            public string lastName { get; set; }
        }
    }
    ```

4. Skapa en ny modell `OutputClaimsModel`, och Lägg sedan till följande egenskaper för att den `OutputClaimsModel` klass:

    ```csharp
    namespace Contoso.AADB2C.API.Models
    {
        public class OutputClaimsModel
        {
            public string loyaltyNumber { get; set; }
        }
    }
    ```

5. Skapa en mer modell, `B2CResponseContent`, som du använder för att utlösa verifieringsfel för inkommande meddelanden. Lägg till följande egenskaper för att den `B2CResponseContent` klassen anger referenser som saknas och spara filen:

    ```csharp
    namespace Contoso.AADB2C.API.Models
    {
        public class B2CResponseContent
        {
            public string version { get; set; }
            public int status { get; set; }
            public string userMessage { get; set; }

            public B2CResponseContent(string message, HttpStatusCode status)
            {
                this.userMessage = message;
                this.status = (int)status;
                this.version = Assembly.GetExecutingAssembly().GetName().Version.ToString();
            }    
        }
    }
    ```

### <a name="step-22-add-a-controller"></a>Steg 2.2: Lägga till en kontrollant
I webb-API, en _controller_ är ett objekt som hanterar HTTP-begäranden. Kontrollanten returnerar utdata anspråk eller om det första namnet inte är giltig, utlöser en konflikt HTTP-felmeddelande.

1. I Solution Explorer högerklickar du på mappen **Styrenheter**. Välj sedan **Lägg till** och sedan **Styrenhet**.

    ![Lägg till ny kontrollant](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-1.png)

2. I den **Lägg till Kodskelett** väljer **Web API-styrenhet – tom**, och välj sedan **Lägg till**.

    ![Välj webb-API 2 styrenhet – tom](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-2.png)

3. I den **Lägg till styrenhet** fönstret namn kontrollanten **IdentityController**, och välj sedan **Lägg till**.

    ![Skriv domänkontrollantens namn](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-3.png)

    Ställningarna skapar en fil med namnet *IdentityController.cs* i den *domänkontrollanter* mapp.

4. Om den *IdentityController.cs* filen är inte öppen redan, dubbelklicka på den och Ersätt Koden i filen med följande kod:

    ```csharp
    using Contoso.AADB2C.API.Models;
    using Newtonsoft.Json;
    using System;
    using System.NET;
    using System.Web.Http;

    namespace Contoso.AADB2C.API.Controllers
    {
        public class IdentityController: ApiController
        {
            [HttpPost]
            public IHttpActionResult SignUp()
            {
                // If no data came in, then return
                if (this.Request.Content == null) throw new Exception();

                // Read the input claims from the request body
                string input = Request.Content.ReadAsStringAsync().Result;

                // Check the input content value
                if (string.IsNullOrEmpty(input))
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Request content is empty", HttpStatusCode.Conflict));
                }

                // Convert the input string into an InputClaimsModel object
                InputClaimsModel inputClaims = JsonConvert.DeserializeObject(input, typeof(InputClaimsModel)) as InputClaimsModel;

                if (inputClaims == null)
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Can not deserialize input claims", HttpStatusCode.Conflict));
                }

                // Run an input validation
                if (inputClaims.firstName.ToLower() == "test")
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Test name is not valid, please provide a valid name", HttpStatusCode.Conflict));
                }

                // Create an output claims object and set the loyalty number with a random value
                OutputClaimsModel outputClaims = new OutputClaimsModel();
                outputClaims.loyaltyNumber = new Random().Next(100, 1000).ToString();

                // Return the output claim(s)
                return Ok(outputClaims);
            }
        }
    }
    ```

## <a name="step-3-publish-the-project-to-azure"></a>Steg 3: Publicera projektet på Azure
1. I Solution Explorer högerklickar du på den **Contoso.AADB2C.API** projektet och välj sedan **publicera**.

    ![Publicera till Microsoft Azure App Service](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-1.png)

2. I den **publicera** väljer **Microsoft Azure App Service**, och välj sedan **publicera**.

    ![Skapa nya Microsoft Azure App Service](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-2.png)

    Den **skapa App Service** öppnas. I den, kan du skapa alla Azure-resurser för att köra ASP.NET-webbapp i Azure.

    > [!NOTE]
    >Mer information om hur du publicerar finns i [skapa en ASP.NET-webbapp i Azure](https://docs.microsoft.com/azure/app-service-web/app-service-web-get-started-dotnet#publish-to-azure).

3. I den **Webbappnamnet** skriver du ett unikt appnamn (giltiga tecken är a – z, 0-9 och bindestreck (-). URL: en för webbappen är http://<app_name>.azurewebsites.NET, där *app_name* är namnet på din webbapp. Du kan godkänna namnet som genereras automatiskt och som är unikt.

    ![Ange egenskaper för App Service](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-3.png)

4. Om du vill börja skapa Azure-resurser, Välj **skapa**.  
    När ASP.NET-webbappen har skapats, guiden publicerar den till Azure och sedan startar appen i standardwebbläsaren.

6. Kopiera webbappens URL.

## <a name="step-4-add-the-new-loyaltynumber-claim-to-the-schema-of-your-trustframeworkextensionsxml-file"></a>Steg 4: Lägg till den nya `loyaltyNumber` anspråk i schemat för din TrustFrameworkExtensions.xml-fil
Den `loyaltyNumber` anspråk ännu inte har definierats i vår schemat. Lägg till en definition i den `<BuildingBlocks>` element som du hittar i början av den *TrustFrameworkExtensions.xml* fil.

```xml
<BuildingBlocks>
    <ClaimsSchema>
        <ClaimType Id="loyaltyNumber">
            <DisplayName>loyaltyNumber</DisplayName>
            <DataType>string</DataType>
            <UserHelpText>Customer loyalty number</UserHelpText>
        </ClaimType>
    </ClaimsSchema>
</BuildingBlocks>
```

## <a name="step-5-add-a-claims-provider"></a>Steg 5: Lägga till en anspråksprovider 
Varje anspråksprovidern måste ha en eller flera tekniska profiler som avgör de slutpunkter och protokoll som behövs för att kommunicera med anspråksprovidern. 

En anspråksprovider kan ha flera tekniska profiler för olika anledningar. Flera tekniska profiler kan exempelvis definieras eftersom anspråksprovidern stöder flera protokoll, slutpunkter kan ha olika funktioner eller versioner kan innehålla anspråk som har en mängd olika förtroendenivåer. Det kan vara att frigöra känsliga anspråk i en användarresa men inte i en annan. 

Följande XML-kodstycke innehåller en anspråk providern nod med två tekniska profiler:

* **Tekniska Id = ”REST-API-SignUp”**: definierar RESTful-tjänst. 
   * `Proprietary` beskrivs som protokoll för en RESTful-baserade provider. 
   * `InputClaims` definierar vilka anspråk som skickas från Azure AD B2C till REST-tjänst. 

   I det här exemplet innehållet i anspråket `givenName` skickar till REST-tjänst som `firstName`, innehållet i anspråket `surname` skickar till REST-tjänst som `lastName`, och `email` skickar skick. Den `OutputClaims` elementet definierar vilka anspråk som hämtas från RESTful-tjänst tillbaka till Azure AD B2C.

* **Tekniska Id = ”LocalAccountSignUpWithLogonEmail”**: lägger till en profil för tekniska i en befintlig tekniska profil (definieras i basprincipen). Under registreringen vägen anropar den tekniska profilen verifiering föregående tekniska profil. Om tjänsten RESTful returnerar ett HTTP-fel 409 (en konflikt fel), visas ett felmeddelande för användaren. 

Leta upp den `<ClaimsProviders>` nod, och Lägg sedan till följande XML-kodstycke under den `<ClaimsProviders>` nod:

```xml
<ClaimsProvider>
    <DisplayName>REST APIs</DisplayName>
    <TechnicalProfiles>
    
    <!-- Custom Restful service -->
    <TechnicalProfile Id="REST-API-SignUp">
        <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
        <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
        <Metadata>
        <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
        <Item Key="AuthenticationType">None</Item>
        <Item Key="SendClaimsIn">Body</Item>
        </Metadata>
        <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" />
        <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
        <InputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
        </InputClaims>
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="loyaltyNumber" />
        </OutputClaims>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>

<!-- Change LocalAccountSignUpWithLogonEmail technical profile to support your validation technical profile -->
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="loyaltyNumber" />
        </OutputClaims>
        <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="REST-API-SignUp" />
        </ValidationTechnicalProfiles>
    </TechnicalProfile>

    </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="step-6-add-the-loyaltynumber-claim-to-your-relying-party-policy-file-so-the-claim-is-sent-to-your-application"></a>Steg 6: Lägg till den `loyaltyNumber` anspråk till din förlitande part principfil därför anspråket skickas till ditt program
Redigera din *SignUpOrSignIn.xml* förlitande part (RP) filen och ändra tekniska-Id = ”PolicyProfile”-element för att lägga till följande: `<OutputClaim ClaimTypeReferenceId="loyaltyNumber" />`.

När du lägger till nytt anspråk förlitande part-koden ser ut så här:

```xml
<RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect" />
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" DefaultValue="" />
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
    </RelyingParty>
</TrustFrameworkPolicy>
```

## <a name="step-7-upload-the-policy-to-your-tenant"></a>Steg 7: Ladda upp principen till din klient

1. I den [Azure-portalen](https://portal.azure.com), växla till den [kontext som din Azure AD B2C-klient](active-directory-b2c-navigate-to-b2c-context.md), och öppna sedan **Azure AD B2C**.

2. Välj **Identitetsramverk**.

3. Öppna **alla principer**. 

4. Välj **överföra princip**.

5. Välj den **Skriv över principen om den finns** markerar du kryssrutan.

6. Överföra filen TrustFrameworkExtensions.xml och se till att den godkänns vid.

7. Upprepa föregående steg med SignUpOrSignIn.xml-fil.

## <a name="step-8-test-the-custom-policy-by-using-run-now"></a>Steg 8: Testa den anpassade principen med hjälp av kör nu
1. Välj **Azure AD B2C-inställningar**, och gå till **Identitetsramverk**.

    > [!NOTE]
    > **Kör nu** kräver minst ett program att vara förväg registrerade på klienten. Läs hur du registrerar program i Azure AD B2C [börjar](active-directory-b2c-get-started.md) artikel eller [programregistrering](active-directory-b2c-app-registration.md) artikeln.

2. Öppna **B2C_1A_signup_signin**, den förlitande part (RP) anpassa princip som du överförde och väljer sedan **kör nu**.

    ![Fönstret B2C_1A_signup_signin](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-run.png)

3. Testa processen genom att skriva **Test** i den **Förnamn** box.  
    Azure AD B2C visar ett felmeddelande visas överst i fönstret.

    ![Testa din princip](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-test.png)

4.  I den **Förnamn** skriver ett namn (andra än ”Test”).  
    Azure AD B2C registrerar sig användaren och skickar sedan en loyaltyNumber till ditt program. Observera antalet i det här JWT.

```
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1507125903,
  "nbf": 1507122303,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1507122303,
  "auth_time": 1507122303,
  "loyaltyNumber": "290",
  "given_name": "Emily",
  "emails": ["B2cdemo@outlook.com"]
}
```

## <a name="optional-download-the-complete-policy-files-and-code"></a>(Valfritt) Ladda ned fullständig principfiler och kod
* När du har slutfört den [Kom igång med anpassade principer](active-directory-b2c-get-started-custom.md) genomgången ska vi rekommenderar att du skapar ditt scenario genom att använda din egen anpassade principfiler. För referens har vi samlat [exempel principfiler](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw).
* Du kan hämta den fullständiga koden från [exempel Visual Studio-lösning för referens](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/).
    
## <a name="next-steps"></a>Nästa steg
* [Skydda ditt RESTful-API med grundläggande autentisering (användarnamn och lösenord)](active-directory-b2c-custom-rest-api-netfw-secure-basic.md)
* [Skydda ditt RESTful-API med klientcertifikat](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)
