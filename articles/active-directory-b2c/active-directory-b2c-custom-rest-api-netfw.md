---
title: 'Azure Active Directory B2C: Integrera utbyten av REST API anspråk i din Azure AD B2C användaren resa som verifiering av indata från användaren'
description: Integrera utbyten av REST API anspråk i din Azure AD B2C användaren resa som verifiering av indata från användaren.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 09/30/2017
ms.author: davidmu
ms.openlocfilehash: c4a530b3d6b4f6d4f5ec6087adcee839422dd01d
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
---
# <a name="integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-validation-of-user-input"></a>Integrera utbyte av REST API-anspråk i din Azure AD B2C användaren resa som verifiering av indata från användaren

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Med Identity upplevelse Framework, som ligger till grund för Azure Active Directory B2C (Azure AD B2C) som du kan integrera med en RESTful-API i en resa för användaren. I den här genomgången får lära du dig hur Azure AD B2C samverkar med .NET Framework RESTful-tjänster (webb-API).

## <a name="introduction"></a>Introduktion
Med hjälp av Azure AD B2C kan du lägga till egna affärslogiken för en användare resa genom att anropa tjänsten RESTful. Identitet upplevelse Framework skickar data till RESTful-tjänst i ett *inkommande anspråk* samling och tar emot data från RESTful i en *utgående anspråk* samling. Med RESTful-tjänst-integration kan du:

* **Validera inkommande användardata**: den här åtgärden förhindrar att felaktiga data beständighet till Azure AD. Om värdet från användaren inte är giltigt, returnerar ett felmeddelande som användaren uppmanas att ange någon post RESTful-tjänst. Du kan till exempel kontrollera att e-postadressen som anges av användaren finns i kundens databasen.
* **Skriv över inkommande anspråk**: om en användare anger förnamnet i gemener och versaler bokstäverna kan du formatera namn med bara den första bokstaven versal.
* **Utöka användardata genom att ytterligare integrera med företagets av branschspecifika program**: din RESTful-tjänst kan ta emot användarens e-postadress, frågar kundens databas och returnera användarens förmåner numret till Azure AD B2C. Returnerar anspråk som kan lagras i användarens Azure AD-kontot som utvärderas i nästa *Orchestration-steg*, eller ingår i den åtkomst-token.
* **Kör anpassad affärslogik**: du kan skicka push-meddelanden, uppdatera företagets databaser, kör en process för migrering av användare, hantera behörigheter, gransknings-och databaser och utföra andra åtgärder.

Du kan utforma integrering med RESTful-tjänster på följande sätt:

* **Tekniska verifieringsprofil**: anropet till tjänsten RESTful sker inom tekniska verifieringsprofil för den angivna tekniska profilen. Tekniska verifieringsprofil verifierar användardefinierade data innan användaren transporten flyttas framåt. Med teknisk verifieringsprofil kan du:
   * Skicka inkommande anspråk.
   * Validera de inkommande anspråken och utlösa anpassade felmeddelanden.
   * Skicka tillbaka utgående anspråk.

* **Exchange-anspråk**: den här designen liknar tekniska verifieringsprofil, men det sker inom ett orchestration-steg. Den här definitionen är begränsad till:
   * Skicka inkommande anspråk.
   * Skicka tillbaka utgående anspråk.

## <a name="restful-walkthrough"></a>RESTful genomgång
I den här genomgången kan du utveckla ett .NET Framework webb-API som validerar användarens indata och innehåller förmåner många användare. Programmet kan till exempel bevilja åtkomst till *Platina fördelar* baserat på antalet förmåner.

Översikt:
* Utveckla RESTful-tjänst (.NET Framework webb-API).
* Använda tjänsten RESTful i användaren transporten.
* Skicka inkommande anspråk och läsa dem i din kod.
* Verifiera användarens förnamn.
* Skicka tillbaka ett förmåner tal. 
* Lägg till förmåner numret till en JSON-Webbtoken (JWT).

## <a name="prerequisites"></a>Förutsättningar
Utför stegen i den [komma igång med anpassade principer](active-directory-b2c-get-started-custom.md) artikel.

## <a name="step-1-create-an-aspnet-web-api"></a>Steg 1: Skapa ett ASP.NET webb-API

1. Skapa ett projekt i Visual Studio genom att välja **filen** > **ny** > **projekt**.

2. I den **nytt projekt** väljer **Visual C#** > **Web** > **ASP.NET-webbprogram (.NET Framework)**.

3. I den **namn** Skriv ett namn för programmet (till exempel *Contoso.AADB2C.API*), och välj sedan **OK**.

    ![Skapa nya visual studio-projekt](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-create-project.png)

4. I den **nytt ASP.NET-webbprogram** väljer en **Web API** eller **Azure API-app** mall.

    ![Välj mall för webb-API](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-select-web-api.png)

5. Se till att autentiseringen har angetts till **ingen autentisering**.

6. Välj **OK** att skapa projektet.

## <a name="step-2-prepare-the-rest-api-endpoint"></a>Steg 2: Förbered REST API-slutpunkt

### <a name="step-21-add-data-models"></a>Steg 2.1: Lägg till datamodeller
Modeller representerar de inkommande anspråken och utgående anspråk data i RESTful-tjänst. Koden läser indata med avserialisering av inkommande anspråk modellen från en JSON-sträng till ett C#-objekt (din modell). ASP.NET web API deserializes automatiskt utgående anspråk modellen tillbaka till JSON och skriver sedan serialiserade data till i brödtexten för HTTP-svarsmeddelandet. 

Skapa en modell som representerar inkommande anspråk genom att göra följande:

1. Om Solution Explorer inte redan är öppen väljer **visa** > **Solution Explorer**. 
2. I Solution Explorer högerklickar du på den **modeller** mapp, Välj **Lägg till**, och välj sedan **klassen**.

    ![Lägg till modell](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-model.png)

3. Klassen namnet `InputClaimsModel`, och Lägg sedan till följande egenskaper för att den `InputClaimsModel` klass:

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

5. Skapa en mer modell `B2CResponseContent`, där du vill utlösa indata-validering felmeddelanden. Lägg till följande egenskaper för att den `B2CResponseContent` klassen anger referenser som saknas och sedan spara filen:

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

### <a name="step-22-add-a-controller"></a>Steg 2.2: Lägg till en domänkontrollant
I webb-API, en _domänkontrollant_ är ett objekt som hanterar HTTP-begäranden. Returnerar domänkontrollanten utgående anspråk eller om det första namnet inte är giltigt, genererar ett felmeddelande för HTTP-konflikt.

1. I Solution Explorer högerklickar du på den **domänkontrollanter** mapp, Välj **Lägg till**, och välj sedan **domänkontrollant**.

    ![Lägga till nya styrenhet](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-1.png)

2. I den **Lägg till Kodskelett** väljer **Web API-styrenhet – tom**, och välj sedan **Lägg till**.

    ![Välj Web API 2 styrenhet – tom](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-2.png)

3. I den **Lägg till styrenhet** och namn på kontrollanten **IdentityController**, och välj sedan **Lägg till**.

    ![Skriv domänkontrollantens namn](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-3.png)

    Den scaffold-teknik som skapar en fil med namnet *IdentityController.cs* i den *domänkontrollanter* mapp.

4. Om den *IdentityController.cs* filen inte är öppen redan, dubbelklicka på den och Ersätt Koden i filen med följande kod:

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

## <a name="step-3-publish-the-project-to-azure"></a>Steg 3: Publicera projektet till Azure
1. I Solution Explorer högerklickar du på den **Contoso.AADB2C.API** projektet och välj sedan **publicera**.

    ![Publicera till Microsoft Azure App Service](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-1.png)

2. I den **publicera** väljer **Microsoft Azure App Service**, och välj sedan **publicera**.

    ![Skapa ny Microsoft Azure App Service](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-2.png)

    Den **skapa App Service** öppnas. I det skapar du alla nödvändiga Azure-resurser för att köra ASP.NET-webbapp i Azure.

    > [!NOTE]
    >Mer information om hur du publicerar finns [skapa en ASP.NET-webbapp i Azure](https://docs.microsoft.com/azure/app-service-web/app-service-web-get-started-dotnet#publish-to-azure).

3. I den **Webbprogramnamnet** Skriv ett unikt appnamn (giltiga tecken är a-z, 0-9 och bindestreck (-). URL: en för webbappen är http://<app_name>.azurewebsites.NET, där *programnamn* är namnet på ditt webbprogram. Du kan godkänna namnet som genereras automatiskt och som är unikt.

    ![Ange egenskaper för Apptjänst](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-3.png)

4. Om du vill skapa Azure-resurser, Välj **skapa**.  
    När ASP.NET-webbprogram har skapats, guiden publicerar till Azure och startar appen i standardwebbläsaren.

6. Kopiera webbappens URL.

## <a name="step-4-add-the-new-loyaltynumber-claim-to-the-schema-of-your-trustframeworkextensionsxml-file"></a>Steg 4: Lägg till de nya `loyaltyNumber` anspråk i schemat för TrustFrameworkExtensions.xml-fil
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

## <a name="step-5-add-a-claims-provider"></a>Steg 5: Lägg till en anspråksprovider 
Varje anspråksprovider måste ha en eller flera tekniska profiler som avgör slutpunkter och protokoll som behövs för att kommunicera med anspråksprovidern. 

En anspråksprovider kan ha flera tekniska profiler för olika skäl. Till exempel definieras flera tekniska profiler eftersom anspråksprovidern stöder flera protokoll, slutpunkter kan ha olika funktioner eller versioner kan innehålla anspråk som har flera olika förtroendenivåer. Det kan vara godtagbar att släppa känsliga anspråk i en användare resa men inte i en annan. 

Följande XML-kodstycke innehåller en nod för providern av anspråk med två tekniska profiler:

* **TechnicalProfile Id = ”REST-API-SignUp”**: definierar RESTful-tjänst. 
   * `Proprietary` beskrivs som protokoll för en RESTful-baserade provider. 
   * `InputClaims` definierar vilka anspråk som skickas från Azure AD B2C REST-tjänst. 

   I det här exemplet innehållet anspråkets `givenName` skickar till REST-tjänst som `firstName`, innehållet i anspråket `surname` skickar till REST-tjänst som `lastName`, och `email` skickar eftersom. Den `OutputClaims` elementet definierar vilka anspråk som hämtas från RESTful-tjänst tillbaka till Azure AD B2C.

* **TechnicalProfile Id = ”LocalAccountSignUpWithLogonEmail”**: lägger till en profil för validering av teknisk i en befintlig tekniska profil (definieras i grundläggande princip). Under registreringen transporten anropar tekniska verifieringsprofil föregående tekniska profilen. Om tjänsten RESTful returnerar ett HTTP-fel 409 (en konflikt fel), visas ett felmeddelande för användaren. 

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

## <a name="step-6-add-the-loyaltynumber-claim-to-your-relying-party-policy-file-so-the-claim-is-sent-to-your-application"></a>Steg 6: Lägg till den `loyaltyNumber` anspråk till den förlitande part princip-filen så att begäran skickas till ditt program
Redigera din *SignUpOrSignIn.xml* förlitande part (RP) filen och ändra TechnicalProfile-Id = ”PolicyProfile” element för att lägga till följande: `<OutputClaim ClaimTypeReferenceId="loyaltyNumber" />`.

När du lägger till nya anspråk, förlitande part koden ser ut så här:

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

## <a name="step-7-upload-the-policy-to-your-tenant"></a>Steg 7: Överför principen till din klient

1. I den [Azure-portalen](https://portal.azure.com), växla till den [kontext för din Azure AD B2C-klient](active-directory-b2c-navigate-to-b2c-context.md), och sedan öppna **Azure AD B2C**.

2. Välj **identitet upplevelse Framework**.

3. Öppna **alla principer**. 

4. Välj **överföra princip**.

5. Välj den **skriva över principen om den finns** kryssrutan.

6. Överföra filen TrustFrameworkExtensions.xml och se till att den har klarat valideringen.

7. Upprepa det föregående steget med filen SignUpOrSignIn.xml.

## <a name="step-8-test-the-custom-policy-by-using-run-now"></a>Steg 8: Testa den anpassade principen genom att använda Kör nu
1. Välj **Azure AD B2C inställningar**, och gå till **identitet upplevelse Framework**.

    > [!NOTE]
    > **Kör nu** kräver minst ett program till preregistered för innehavaren. Om du vill lära dig mer om att registrera program, finns i Azure AD B2C [Kom igång](active-directory-b2c-get-started.md) artikel eller [appregistrering](active-directory-b2c-app-registration.md) artikel.

2. Öppna **B2C_1A_signup_signin**, förlitande part (RP) anpassade principer som du överfört och välj sedan **kör nu**.

    ![Fönstret B2C_1A_signup_signin](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-run.png)

3. Testa processen genom att skriva **Test** i den **Förnamn** rutan.  
    Azure AD B2C visar ett felmeddelande visas överst i fönstret.

    ![Testa din princip](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-test.png)

4.  I den **Förnamn** Skriv ett namn (andra än ”Test”).  
    Azure AD B2C registrerar sig användaren och skickar sedan en loyaltyNumber till ditt program. Observera antalet i den här JWT.

```
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1507125903,
  "nbf": 1507122303,
  "ver": "1.0",
  "iss": "https://login.microsoftonline.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
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

## <a name="optional-download-the-complete-policy-files-and-code"></a>(Valfritt) Hämta fullständig principfiler och kod
* När du har slutfört den [komma igång med anpassade principer](active-directory-b2c-get-started-custom.md) genomgång, rekommenderar vi att du skapar ditt scenario genom att använda en anpassad princip för filerna. Referens, har vi angett [exempel principfiler](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw).
* Du kan hämta den fullständiga koden från [exempel Visual Studio-lösning för referens](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/).
    
## <a name="next-steps"></a>Nästa steg
* [Skydda din RESTful-API med grundläggande autentisering (användarnamn och lösenord)](active-directory-b2c-custom-rest-api-netfw-secure-basic.md)
* [Skydda din RESTful-API med klientcertifikat](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)
