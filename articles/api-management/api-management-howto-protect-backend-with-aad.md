---
title: Skydda ett webb-API-serverdelen med Azure Active Directory och API-hantering | Microsoft Docs
description: "Lär dig hur du skyddar en webb-API-serverdel med Azure Active Directory och API-hantering."
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2017
ms.author: apimpm
ms.openlocfilehash: b7fc48412799aea0c4bba971102b4912dbb18e05
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="how-to-protect-a-web-api-backend-with-azure-active-directory-and-api-management"></a>Hur du skyddar en webb-API-serverdel med Azure Active Directory och API-hantering

Det här avsnittet visar hur du skapar ett Web API-serverdel och skydda den med hjälp av OAuth 2.0-protokollet med Azure Active Directory och API-hantering.  

## <a name="create-an-azure-ad-directory"></a>Skapa en Azure AD-katalog
Du måste ha en AAD-klient om du vill skydda din Web API-serverdel med Azure Active Directory. Om du vill skapa en AAD-klient, logga in på den [klassiska Azure-portalen](https://manage.windowsazure.com) och på **ny**->**Apptjänster**->**Active Directory**  -> **Directory**->**skapa anpassade**. 

![Azure Active Directory][api-management-create-aad-menu]

I det här exemplet en katalog med namnet **APIMDemo** skapas med en standarddomän med namnet **DemoAPIM.onmicrosoft.com**. 

![Azure Active Directory][api-management-create-aad]

## <a name="create-a-web-api-service-secured-by-azure-active-directory"></a>Skapa en tjänst för webb-API som skyddas av Azure Active Directory
I det här steget skapas en webb-API-serverdel med hjälp av Visual Studio 2013. Skapa Web API backend-projekt i Visual Studio klickar du på **filen**->**ny**->**projekt**, och välj **ASP.NET-webbprogram Programmet** från den **Web** lista över mallar. 

![Visual Studio][api-management-new-web-app]

Klicka på **Web API** från den **markerar du en lista över** att skapa ett Web API-projekt. Om du vill konfigurera Azure Directory Authentication klickar du på **ändra autentisering**.

![Nytt projekt][api-management-new-project]

Klicka på **Organisationskonton**, och ange den **domän** för din AAD-klient. I det här exemplet domänen är **DemoAPIM.onmicrosoft.com**. Domänen för din katalog kan hämtas från den **domäner** för din katalog.

![Domäner][api-management-aad-domains]

Konfigurera önskade inställningar i den **ändra autentisering** dialogrutan och klicka på **OK**.

![Ändra autentisering][api-management-change-authentication]

När du klickar på **OK** Visual Studio kommer att försöka registrera ditt program med Azure AD-katalogen och du uppmanas att logga in med Visual Studio. Logga in med ett administratörskonto för din katalog.

![Logga in till Visual Studio][api-management-sign-in-vidual-studio]

Så här konfigurerar du det här projektet som en Azure Web API du kryssrutan för **värd i molnet** och klicka sedan på **OK**.

![Nytt projekt][api-management-new-project-cloud]

Du kan uppmanas att logga in på Azure och du kan sedan konfigurera webbprogrammet.

![Konfigurera][api-management-configure-web-app]

I det här exemplet, en ny **programtjänstplanen** med namnet **APIMAADDemo** har angetts.

Klicka på **OK** att konfigurera webbprogrammet och skapa projektet.

## <a name="add-the-code-to-the-web-api-project"></a>Lägg till kod Web API-projekt

Webb-API i det här exemplet implementerar en grundläggande Kalkylatorn tjänst med hjälp av en modell och en domänkontrollant. Om du vill lägga till modellen för tjänsten, högerklicka på **modeller** i **Solution Explorer** och välj **Lägg till**, **klassen**. Klassen namnet `CalcInput` och på **Lägg till**.

Lägg till följande `using` uttryck överst i den `CalcInput.cs` filen.

```csharp
using Newtonsoft.Json;
```

Ersätt klassen skapas med följande kod.

```csharp
public class CalcInput
{
    [JsonProperty(PropertyName = "a")]
    public int a;

    [JsonProperty(PropertyName = "b")]
    public int b;
}
```

Högerklicka på **domänkontrollanter** i **Solution Explorer** och välj **Lägg till**->**domänkontrollant**. Välj **Web API 2 styrenhet – tom** och på **Lägg till**. Typen **CalcController** styrenheten namn och på **Lägg till**.

![Lägg till styrenhet][api-management-add-controller]

Lägg till följande `using` uttryck överst i den `CalcController.cs` filen.

```csharp
using System.IO;
using System.Web;
using APIMAADDemo.Models;
```

Ersätt genererade controller-klassen med följande kod. Den här koden implementerar den `Add`, `Subtract`, `Multiply`, och `Divide` operations grundläggande Kalkylatorn API.

```csharp
[Authorize]
public class CalcController : ApiController
{
    [Route("api/add")]
    [HttpGet]
    public HttpResponseMessage GetSum([FromUri]int a, [FromUri]int b)
    {
        string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a + b);
        HttpResponseMessage response = Request.CreateResponse();
        response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
        return response;
    }

    [Route("api/sub")]
    [HttpGet]
    public HttpResponseMessage GetDiff([FromUri]int a, [FromUri]int b)
    {
        string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a - b);
        HttpResponseMessage response = Request.CreateResponse();
        response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
        return response;
    }

    [Route("api/mul")]
    [HttpGet]
    public HttpResponseMessage GetProduct([FromUri]int a, [FromUri]int b)
    {
        string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a * b);
        HttpResponseMessage response = Request.CreateResponse();
        response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
        return response;
    }

    [Route("api/div")]
    [HttpGet]
    public HttpResponseMessage GetDiv([FromUri]int a, [FromUri]int b)
    {
        string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a / b);
        HttpResponseMessage response = Request.CreateResponse();
        response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
        return response;
    }
}
```

Tryck på **F6** att skapa och verifiera lösningen.

## <a name="publish-the-project-to-azure"></a>Publicera projektet på Azure

Om du vill publicera projektet till Azure, högerklicka på den **APIMAADDemo** projektet i Visual Studio och välj **publicera**. Behåll standardinställningarna den **Publicera webbplats** dialogrutan och klicka på **publicera**.

![Webbpublicering][api-management-web-publish]

## <a name="grant-permissions-to-the-azure-ad-backend-service-application"></a>Bevilja behörighet till Azure AD backend-tjänstprogram
Ett nytt program för backend-tjänsten har skapats i Azure AD-katalogen som en del av processen för att konfigurera och publicera för Web API-projekt.

![Program][api-management-aad-backend-app]

Klicka på namnet på tillämpningsprogrammet kan konfigurera behörigheterna som krävs. Navigera till den **konfigurera** fliken och rulla ned till den **behörigheter för andra program** avsnitt. Klicka på den **programbehörigheter** listrutan bredvid **Windows** **Azure Active Directory**, markera kryssrutan för **läsa katalogdata** , och klicka på **spara**.

![Lägg till behörigheter][api-management-aad-add-permissions]

> [!NOTE]
> Om **Windows** **Azure Active Directory** är inte i listan under behörigheter för andra program, klickar du på **lägga till program** och lägga till den i listan.
> 
> 

Anteckna den **App-Id URI** för användning i ett senare skede när en Azure AD-programmet har konfigurerats för API Management developer-portalen.

![URI för App-Id][api-management-aad-sso-uri]

## <a name="import-the-web-api-into-api-management"></a>Importera webb-API till API-hantering
API: er konfigureras från publisher-portalen API som öppnas via Azure Portal. För att nå det klickar du på **Publisher portal** från verktygsfältet för API Management-tjänsten. Om du inte har skapat en instans för API Management-tjänsten finns [skapa en instans för API Management-tjänsten] [ Create an API Management service instance] i den [hantera din första API] [ Manage your first API] kursen.

![Utgivarportalen][api-management-management-console]

Åtgärder kan vara [lagts till API: er manuellt](api-management-howto-add-operations.md), eller kan importeras.

Skapa en fil med namnet `calcapi.json` med följande innehåll och spara den på datorn. Se till att den `host` attributet pekar till din Web API-serverdel. I det här exemplet `"host": "apimaaddemo.azurewebsites.net"` används.

```json
{
  "swagger": "2.0",
  "info": {
    "title": "Calculator",
    "description": "Arithmetics over HTTP!",
    "version": "1.0"
  },
  "host": "apimaaddemo.azurewebsites.net",
  "basePath": "/api",
  "schemes": [
    "http"
  ],
  "paths": {
    "/add?a={a}&b={b}": {
      "get": {
        "description": "Responds with a sum of two numbers.",
        "operationId": "Add two integers",
        "parameters": [
          {
            "name": "a",
            "in": "query",
            "description": "First operand. Default value is <code>51</code>.",
            "required": true,
            "type": "string",
            "default": "51",
            "enum": [
              "51"
            ]
          },
          {
            "name": "b",
            "in": "query",
            "description": "Second operand. Default value is <code>49</code>.",
            "required": true,
            "type": "string",
            "default": "49",
            "enum": [
              "49"
            ]
          }
        ],
        "responses": { }
      }
    },
    "/sub?a={a}&b={b}": {
      "get": {
        "description": "Responds with a difference between two numbers.",
        "operationId": "Subtract two integers",
        "parameters": [
          {
            "name": "a",
            "in": "query",
            "description": "First operand. Default value is <code>100</code>.",
            "required": true,
            "type": "string",
            "default": "100",
            "enum": [
              "100"
            ]
          },
          {
            "name": "b",
            "in": "query",
            "description": "Second operand. Default value is <code>50</code>.",
            "required": true,
            "type": "string",
            "default": "50",
            "enum": [
              "50"
            ]
          }
        ],
        "responses": { }
      }
    },
    "/div?a={a}&b={b}": {
      "get": {
        "description": "Responds with a quotient of two numbers.",
        "operationId": "Divide two integers",
        "parameters": [
          {
            "name": "a",
            "in": "query",
            "description": "First operand. Default value is <code>100</code>.",
            "required": true,
            "type": "string",
            "default": "100",
            "enum": [
              "100"
            ]
          },
          {
            "name": "b",
            "in": "query",
            "description": "Second operand. Default value is <code>20</code>.",
            "required": true,
            "type": "string",
            "default": "20",
            "enum": [
              "20"
            ]
          }
        ],
        "responses": { }
      }
    },
    "/mul?a={a}&b={b}": {
      "get": {
        "description": "Responds with a product of two numbers.",
        "operationId": "Multiply two integers",
        "parameters": [
          {
            "name": "a",
            "in": "query",
            "description": "First operand. Default value is <code>20</code>.",
            "required": true,
            "type": "string",
            "default": "20",
            "enum": [
              "20"
            ]
          },
          {
            "name": "b",
            "in": "query",
            "description": "Second operand. Default value is <code>5</code>.",
            "required": true,
            "type": "string",
            "default": "5",
            "enum": [
              "5"
            ]
          }
        ],
        "responses": { }
      }
    }
  }
}
```

Du importerar kalkylator-API:et genom att klicka på **API:er** på **API Management**-menyn till vänster och sedan på **Importera API**.

![Knappen Importera API][api-management-import-api]

Utför följande steg om du vill konfigurera Kalkylatorn API.

1. Klicka på **från filen**, bläddra till den `calculator.json` fil som du sparade och klicka på den **Swagger** knappen.
2. Typen **beräkna** till den **URL för Web API-suffix** textruta.
3. Klicka i rutan **Produkter (valfritt)** och välj **Starter**.
4. Klicka på **Spara** för att importera API:et.

![Lägga till ett nytt API][api-management-import-new-api]

När du har importerat API:et visas sammanfattningssidan för API:et på utgivarportalen.

## <a name="call-the-api-unsuccessfully-from-the-developer-portal"></a>Anropa API: et fel från developer-portalen
Nu API: et har importerats till API-hantering, men kan ännu inte anropas har från developer-portalen eftersom serverdelstjänsten skyddas med Azure AD-autentisering. 

Klicka på **utvecklarportalen** från upp till höger i portalen utgivare.

![Utvecklarportalen][api-management-developer-portal-menu]

Klicka på **API: er** och klicka på den **Kalkylatorn** API.

![Utvecklarportalen][api-management-dev-portal-apis]

Klicka på **prova**.

![Prova][api-management-dev-portal-try-it]

Klicka på **skicka** och notera svarsstatusen av **401 obehörig**.

![Skicka][api-management-dev-portal-send-401]

Begäran har inte behörighet eftersom serverdelen API skyddas av Azure Active Directory. Innan du anropar API: N i developer måste portal konfigureras för att ge utvecklare som använder OAuth 2.0. Den här processen beskrivs i följande avsnitt.

## <a name="register-the-developer-portal-as-an-aad-application"></a>Registrera developer-portalen som ett AAD-program
Det första steget i att konfigurera developer-portalen för att ge utvecklare som använder OAuth 2.0 är att registrera developer-portalen som ett AAD-program. 

Gå till Azure AD-klient. I det här exemplet väljer **APIMDemo** och navigera till den **program** fliken.

![Nytt program][api-management-aad-new-application-devportal]

Klicka på den **Lägg till** knappen om du vill skapa ett nytt program för Azure Active Directory och välj **Lägg till ett program som min organisation utvecklar**.

![Nytt program][api-management-new-aad-application-menu]

Välj **Web application och/eller webb-API**, ange ett namn och klicka på pilen Nästa. I det här exemplet **APIMDeveloperPortal** används.

![Nytt program][api-management-aad-new-application-devportal-1]

För **inloggnings-URL** anger du URL för API Management-tjänsten och Lägg till `/signin`. I det här exemplet `https://contoso5.portal.azure-api.net/signin` används.

För **App-Id-URL** anger du URL för API Management-tjänsten och lägga till vissa unika tecken. Det kan vara önskade tecken och i det här exemplet `https://contoso5.portal.azure-api.net/dp` används. När den önskade **appegenskaper** är konfigurerad, klicka på kryssmarkeringen för att skapa programmet.

![Nytt program][api-management-aad-new-application-devportal-2]

## <a name="configure-an-api-management-oauth-20-authorization-server"></a>Konfigurera en server för API Management OAuth 2.0-auktorisering
Nästa steg är att konfigurera en server för OAuth 2.0-auktorisering i API-hantering. 

Klicka på **säkerhet** API Management-menyn till vänster klickar du på **OAuth 2.0**, och klicka sedan på **lägga till tillståndet** server.

![Lägg till auktorisering server][api-management-add-authorization-server]

Ange ett namn och en valfri beskrivning i den **namn** och **beskrivning** fält. De här fälten används för att identifiera servern OAuth 2.0 auktorisering i API Management service-instans. I det här exemplet **auktorisering server demo** används. Senare när du anger en OAuth 2.0-server som ska användas för autentisering för ett API som du väljer det här namnet.

För den **klienten URL för registrering** ange ett platshållarvärde som `http://localhost`.  Den **klienten URL för registrering** pekar på den sida som användare kan använda för att skapa och konfigurera sina egna konton för OAuth 2.0-leverantörer som stöder Användarhantering av konton. I det här exemplet användare inte skapa och konfigurera sina egna konton, så att en platshållare används.

![Lägg till auktorisering server][api-management-add-authorization-server-1]

Ange sedan **slutpunkts-URL-auktorisering** och **Token slutpunkts-URL**.

![auktorisering server][api-management-add-authorization-server-1a]

Dessa värden kan hämtas från den **App slutpunkter** sidan i AAD-program som du skapade för developer-portalen. Om du vill komma åt slutpunkterna, navigera till den **konfigurera** för AAD-program och klicka på **visa slutpunkter**.

![Program][api-management-aad-devportal-application]

![Visa slutpunkter][api-management-aad-view-endpoints]

Kopiera den **OAuth 2.0 autentiseringsslutpunkt** och klistrar in det i den **slutpunkts-URL-auktorisering** textruta.

![Lägg till auktorisering server][api-management-add-authorization-server-2]

Kopiera den **OAuth 2.0-token för slutpunkt** och klistrar in det i den **Token slutpunkts-URL** textruta.

![Lägg till auktorisering server][api-management-add-authorization-server-2a]

Förutom klistra in tokenslutpunkten, lägga till en ytterligare brödtext parameter med namnet **resurs** och använda värdet i **App-Id URI** från AAD-program för backend-tjänst som skapades när den Visual Studio-projekt har publicerats.

![URI för App-Id][api-management-aad-sso-uri]

Därefter anger du klientautentiseringsuppgifterna. Det är dessa autentiseringsuppgifter för den resurs som du vill komma åt i det här fallet developer-portalen.

![Klientautentiseringsuppgifter][api-management-client-credentials]

Att hämta den **klient-Id**, navigera till den **konfigurera** för AAD-programmet för developer-portalen och kopiera den **klient-Id**.

Att hämta den **Klienthemlighet** klickar du på den **Markera varaktighet** listrutan i den **nycklar** avsnittet och ange ett intervall. I det här exemplet används 1 år.

![Klient-ID][api-management-aad-client-id]

Klicka på **spara** att spara konfigurationen och visa nyckeln. 

> [!IMPORTANT]
> Anteckna den här nyckeln. När du stänger fönstret Azure Active Directory-konfiguration kan nyckeln inte visas igen.
> 
> 

Kopiera nyckeln till Urklipp, växla tillbaka till publisher-portalen, klistra in nyckeln till den **Klienthemlighet** textruta och klicka på **spara**.

![Lägg till auktorisering server][api-management-add-authorization-server-3]

Direkt efter klientens autentiseringsuppgifter är en kod bevilja för auktorisering. Kopiera den här auktoriseringskod och går tillbaka till Azure AD developer portal programmet Konfigurera och klistra in authorization grant i den **Reply URL** fältet och klickar på **spara** igen.

![Svars-URL][api-management-aad-reply-url]

Nästa steg är att konfigurera behörigheter för developer-portalen AAD-program. Klicka på **programbehörigheter** och markera kryssrutan för **läsa katalogdata**. Klicka på **spara** att spara den här ändringen och klicka sedan på **lägga till program**.

![Lägg till behörigheter][api-management-add-devportal-permissions]

Klicka på sökikonen, typen **APIM** i Starta med rutan, Välj **APIMAADDemo**, och klicka på bockmarkeringen för att spara.

![Lägg till behörigheter][api-management-aad-add-app-permissions]

Klicka på **delegerade behörigheter** för **APIMAADDemo** och markera kryssrutan för **åtkomst APIMAADDemo**, och klicka på **spara**. Detta kan utvecklare portalprogram för åtkomst till backend-tjänst.

![Lägg till behörigheter][api-management-aad-add-delegated-permissions]

## <a name="enable-oauth-20-user-authorization-for-the-calculator-api"></a>Aktivera OAuth 2.0 användarautentiseringen för Kalkylatorn API
OAuth 2.0-server är konfigurerat kan du ange den i säkerhetsinställningarna för ditt API. 

Klicka på **API: er** i den vänstra menyn och klicka på **Kalkylatorn** att visa och konfigurera dess inställningar.

![Kalkylatorn API][api-management-calc-api]

Navigera till den **säkerhet** markerar den **OAuth 2.0** kryssrutan Markera önskad auktorisering servern från den **auktorisering server** listrutan, och klicka på  **Spara**.

![Kalkylatorn API][api-management-enable-aad-calculator]

## <a name="successfully-call-the-calculator-api-from-the-developer-portal"></a>Anropa API: et Kalkylatorn från developer-portalen
OAuth 2.0-tillståndet är konfigurerat på API anropas åtgärderna har från developer center. 

Gå tillbaka till den **lägga till två heltal** Kalkylatorn tjänsten i developer-portalen och klicka på **prova**. Observera att det nya objektet i den **auktorisering** avsnittet motsvarar auktoriserings-server som du just lagt till.

![Kalkylatorn API][api-management-calc-authorization-server]

Välj **Auktoriseringskoden** från auktorisering nedrullningsbara listan och ange autentiseringsuppgifterna för kontot som ska användas. Om du redan har loggat in med kontot kan du inte ombedd.

![Kalkylatorn API][api-management-devportal-authorization-code]

Klicka på **skicka** och notera den **svarsstatusen** av **200 OK** och resultatet av åtgärden i svaret innehållet.

![Kalkylatorn API][api-management-devportal-response]

## <a name="configure-a-desktop-application-to-call-the-api"></a>Konfigurera ett skrivbordsprogram att anropa API: et

Konfigurera en enkel skrivbordsprogram att anropa API: et. Det första steget är att registrera programmet i Azure AD och ge det åtkomst till katalogen och serverdelstjänsten. 

## <a name="configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>Konfigurera en princip för verifiering av JWT för att godkänna begäranden före

Använd den [Validera JWT](api-management-access-restriction-policies.md#ValidateJWT) princip för att auktorisera före begäranden genom att verifiera åtkomsttoken för varje inkommande begäran. Om begäran inte har verifierats av Validera JWT-principen, begäran har blockerats av API-hantering och skickas inte vidare till serverdelen.

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/DemoAPIM.onmicrosoft.com/.well-known/openid-configuration" />
    <required-claims>
        <claim name="aud">
            <value>https://DemoAPIM.NOTonmicrosoft.com/APIMAADDemo</value>
        </claim>
    </required-claims>
</validate-jwt>
```

Mer information finns i [moln omfattar avsnitt 177: flera API Management-funktioner](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) och spola framåt till 13:50. Spola fram till 15:00 för att se de principer som konfigurerats i Redigeraren för grupprinciper och sedan till 18:50 för en demonstration av anropa en funktion från utvecklarportal både med och utan autentiseringstoken som krävs.

## <a name="next-steps"></a>Nästa steg
* Checka ut mer [videor](https://azure.microsoft.com/documentation/videos/index/?services=api-management) om API-hantering.
* Andra sätt att skydda din backend-tjänst finns [ömsesidig autentisering](api-management-howto-mutual-certificates.md).

[api-management-management-console]: ./media/api-management-howto-protect-backend-with-aad/api-management-management-console.png

[api-management-import-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-import-api.png
[api-management-import-new-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-import-new-api.png
[api-management-create-aad-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-create-aad-menu.png
[api-management-create-aad]: ./media/api-management-howto-protect-backend-with-aad/api-management-create-aad.png
[api-management-new-web-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-web-app.png
[api-management-new-project]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-project.png
[api-management-new-project-cloud]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-project-cloud.png
[api-management-change-authentication]: ./media/api-management-howto-protect-backend-with-aad/api-management-change-authentication.png
[api-management-sign-in-vidual-studio]: ./media/api-management-howto-protect-backend-with-aad/api-management-sign-in-vidual-studio.png
[api-management-configure-web-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-configure-web-app.png
[api-management-aad-domains]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-domains.png
[api-management-add-controller]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-controller.png
[api-management-web-publish]: ./media/api-management-howto-protect-backend-with-aad/api-management-web-publish.png
[api-management-aad-backend-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-backend-app.png
[api-management-aad-add-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-permissions.png
[api-management-developer-portal-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-developer-portal-menu.png
[api-management-dev-portal-apis]: ./media/api-management-howto-protect-backend-with-aad/api-management-dev-portal-apis.png
[api-management-dev-portal-try-it]: ./media/api-management-howto-protect-backend-with-aad/api-management-dev-portal-try-it.png
[api-management-dev-portal-send-401]: ./media/api-management-howto-protect-backend-with-aad/api-management-dev-portal-send-401.png
[api-management-aad-new-application-devportal]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-new-application-devportal.png
[api-management-aad-new-application-devportal-1]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-new-application-devportal-1.png
[api-management-aad-new-application-devportal-2]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-new-application-devportal-2.png
[api-management-aad-devportal-application]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-devportal-application.png
[api-management-add-authorization-server]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server.png
[api-management-aad-sso-uri]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-sso-uri.png
[api-management-aad-view-endpoints]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-view-endpoints.png
[api-management-aad-client-id]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-client-id.png
[api-management-add-authorization-server-1]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-1.png
[api-management-add-authorization-server-2]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-2.png
[api-management-add-authorization-server-2a]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-2a.png
[api-management-add-authorization-server-3]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-3.png
[api-management-aad-reply-url]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-reply-url.png
[api-management-add-devportal-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-devportal-permissions.png
[api-management-aad-add-app-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-app-permissions.png
[api-management-aad-add-delegated-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-delegated-permissions.png
[api-management-calc-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-calc-api.png
[api-management-enable-aad-calculator]: ./media/api-management-howto-protect-backend-with-aad/api-management-enable-aad-calculator.png
[api-management-devportal-authorization-code]: ./media/api-management-howto-protect-backend-with-aad/api-management-devportal-authorization-code.png
[api-management-devportal-response]: ./media/api-management-howto-protect-backend-with-aad/api-management-devportal-response.png
[api-management-calc-authorization-server]: ./media/api-management-howto-protect-backend-with-aad/api-management-calc-authorization-server.png
[api-management-add-authorization-server-1a]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-1a.png
[api-management-client-credentials]: ./media/api-management-howto-protect-backend-with-aad/api-management-client-credentials.png
[api-management-new-aad-application-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-aad-application-menu.png

[Create an API Management service instance]: get-started-create-service-instance.md
[Manage your first API]: import-and-publish.md
