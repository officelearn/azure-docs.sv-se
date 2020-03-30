---
title: Skydda SPA-serverning med OAuth 2.0 med hjälp av Azure Active Directory B2C och Azure API Management.
description: Skydda ett API med OAuth 2.0 med hjälp av Azure Active Directory B2C, Azure API Management och Easy Auth som ska anropas från ett JavaScript SPA.
services: api-management, azure-ad-b2c, app-service
documentationcenter: ''
author: WillEastbury
manager: alberts
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2020
ms.author: wieastbu
ms.custom: fasttrack-new
ms.openlocfilehash: 55acea360de11c5fcc699d65daf92cf24dfd691d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79475484"
---
# <a name="protect-spa-backend-with-oauth-20-azure-active-directory-b2c-and-azure-api-management"></a>Skydda SPA-serverd med OAuth 2.0, Azure Active Directory B2C och Azure API Management

Det här scenariot visar hur du konfigurerar din Azure API Management-instans för att skydda ett API.
Vi använder OAuth 2.0-protokollet med Azure AD B2C, tillsammans med API Management för att skydda en Azure Functions serverdel med EasyAuth.

## <a name="aims"></a>Syftar till
Vi ska se hur API Management kan användas i ett förenklat scenario med Azure Functions och Azure AD B2C. Du skapar en JavaScript-app (JS) som anropar ett API som loggar in användare med Azure AD B2C. Sedan använder du API Managements policyfunktioner för validering-jwt för att skydda Serverda-API:et.

För försvar på djupet använder vi sedan EasyAuth för att validera token igen i backend-API:et.

## <a name="prerequisites"></a>Krav
Om du vill följa stegen i den här artikeln måste du ha:
* Ett Azure-konto (StorageV2) Allmänt syfte V2-lagring som är värd för den första JS-appen för ensidig sida
* En Azure API Management-instans 
* En tom Azure Function-app (som kör V2 .NET Core-körningen, på en Windows-förbrukningsplan) som värd för det anropade API:et
* En Azure AD B2C-klient, kopplad till en prenumeration 

Även om du i praktiken skulle använda resurser i samma region i produktionsarbetsbelastningar, är distributionsregionen inte viktig för den här artikeln.

## <a name="overview"></a>Översikt
Här är en illustration av de komponenter som används och flödet mellan dem när denna process är klar.
![Komponenter som används och flöde](../api-management/media/howto-protect-backend-frontend-azure-ad-b2c/image-arch.png "Komponenter som används och flöde")

Här är en snabb översikt över stegen:

1. Skapa Azure AD B2C-anrop (Frontend, API Management) och API-program med scope och bevilja API-åtkomst
1. Skapa inloggnings- eller inloggningsprinciper för att tillåta användare att logga in med Azure AD B2C 
1. Konfigurera API-hantering med de nya Azure AD B2C-klient-ID:na och nycklarna för att aktivera OAuth2-användarauktorisering i utvecklarkonsolen
1. Skapa funktions-API:et
1. Konfigurera funktions-API:et för att aktivera EasyAuth med det nya Azure AD B2C-klient-ID:et och nycklar och lås till APIM VIP 
1. Skapa API-definitionen i API Management
1. Konfigurera Oauth2 för API Management API-konfigurationen
1. Konfigurera **CORS-principen** och lägg till **principen validate-jwt** för att validera OAuth-token för varje inkommande begäran
1. Skapa det anropande programmet för att använda API:et
1. Ladda upp JS SPA-provet
1. Konfigurera exempelhs-klientappen med det nya Azure AD B2C-klient-ID:et och -nycklarna 
1. Testa klientprogrammet

## <a name="configure-azure-ad-b2c"></a>Konfigurera Azure AD B2C 
Öppna Azure AD B2C-bladet i portalen och gör följande steg.
1. Välj fliken **Program** 
1. Klicka på knappen Lägg till och skapa tre program för följande ändamål
   * Frontend-klienten.
   * Api:et för serverdfunktion.
   * [Valfritt] API Management-utvecklarportalen (såvida du inte kör Azure API Management på förbrukningsnivån, mer om det här scenariot senare).
1. Ange WebApp / Web API för alla 3 program och ange "Tillåt implicit flöde" till ja för endast Frontend-klienten.
1. Ställ nu in App ID URI, välj något unikt och relevant för den tjänst som skapas.
1. Använd platshållare för svarsadresser för https://localhostnu, till exempel, vi uppdaterar dessa webbadresser senare.
1. Klicka på Skapa och upprepa sedan steg 2-5 för var och en av de tre apparna ovan, spela in AppID URI, namn och program-ID för senare användning för alla tre apparna.
1. Öppna API Management Developer Portal Application från listan över program och välj fliken *Nycklar* (under Allmänt) och klicka sedan på "Generera nyckel" för att generera en auth-nyckel
1. När du klickar spara, spela in nyckeln någonstans säkert för senare användning - observera att denna plats är den enda chansen kommer du att få visa och kopiera denna nyckel.
1. Välj nu fliken *Publicerade scope* (under API Access)
1. Skapa och namnge ett scope för funktions-API:et och registrera scopet och det ifyllda fullständigt scopevärde och klicka sedan på Spara.
   > [!NOTE]
   > Azure AD B2C-scope är effektivt behörigheter inom ditt API som andra program kan begära åtkomst till via API-åtkomstbladet från sina program, effektivt du bara skapade programbehörigheter för ditt anropade API.
1. Öppna de andra två programmen och titta sedan under fliken *API Access.*
1. Ge dem åtkomst till serverd-API-scopet och det standardområde som redan fanns där ("Öppna användarens profil").
1. Generera dem en nyckel vardera genom att välja fliken *Nycklar* under "Allmänt" för att generera en auth-nyckel och spela in dessa nycklar någonstans säkert för senare.

## <a name="create-a-sign-up-or-sign-in-user-flow"></a>Skapa ett användarflöde för "Registrera dig eller Logga in"
1. Återgå till roten (eller översikt) för Azure AD B2C-bladet 
1. Välj sedan "Användarflöden (principer)" och klicka på "Nytt användarflöde"
1. Välj användarflödestypen "Registrera dig och logga in"
1. Ge principen ett namn och spela in den för senare.
1. Sedan under "Identitetsleverantörer", sedan kontrollera "Användar-ID registrera dig" (detta kan säga "E-registrera dig") och klicka på OK. 
1. Under Användarattribut och anspråk klickar du på Visa mer...' Välj sedan de anspråksalternativ som du vill att användarna ska ange och har returnerat i token. Kontrollera åtminstone "Visningsnamn" och "E-postadress" för att samla in och returnera, och klicka på "OK", klicka sedan på "Skapa".
1. Välj den princip som du skapade i listan och klicka sedan på knappen Kör användarflöde.
1. Den här åtgärden öppnar bladet för användarflödet, väljer klientdelsprogrammet och registrerar sedan adressen till den b2clogin.com domän som visas under listrutan för Välj domän.
1. Klicka på länken högst upp för att öppna slutpunkten för "välkänd openid-konfiguration" och registrera authorization_endpoint- och token_endpoint-värden samt värdet på själva länken som den välkända openid-konfigurationsslutpunkten.

   > [!NOTE]
   > Med B2C-principer kan du exponera Azure AD B2C-inloggningsslutpunkterna för att kunna samla in olika datakomponenter och logga in användare på olika sätt. I det här fallet konfigurerade vi en registrerings- eller inloggningsslutpunkt, som exponerade en välkänd konfigurationsslutpunkt, specifikt identifierades vår skapade princip i URL:en av parametern p=.
   > 
   > När detta är gjort - du har nu en funktionell Business to Consumer identitetsplattform som kommer att logga in användare i flera program. 
   > Om du vill kan du klicka på "Kör användarflöde" knappen här (för att gå igenom registreringen eller logga in) och få en känsla för vad den kommer att göra i praktiken, men omdirigeringssteget i slutet kommer att misslyckas eftersom appen ännu inte har distribuerats.

## <a name="build-the-function-api"></a>Skapa funktions-API:et
1. Växla tillbaka till din vanliga Azure AD-klient i Azure-portalen så att vi kan konfigurera objekt i din prenumeration igen 
1. Gå till bladet Funktionsappar i Azure-portalen, öppna din tomma funktionsapp och skapa sedan en ny inportalfunktion för "Webhook + API" via snabbstarten.
1. Klistra in exempelkoden nedanifrån i Run.csx över den befintliga koden som visas.

   ```csharp
   
   using System.Net;
   using Microsoft.AspNetCore.Mvc;
   using Microsoft.Extensions.Primitives;
   
   public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
   {
      log.LogInformation("C# HTTP trigger function processed a request.");
      
      return (ActionResult)new OkObjectResult($"Hello World, time and date are {DateTime.Now.ToString()}");
   }
   
   ```

   > [!NOTE]
   > C # script funktionskod du just klistrat in loggar helt enkelt en rad till funktioner loggar, och returnerar texten "Hello World" med några dynamiska data (datum och tid).

3. Välj "Integrera" från det vänstra bladet och välj sedan "Avancerad redigerare" i det övre högra hörnet av fönstret.
4. Klistra in exempelkoden nedan över den befintliga json.

   ```json
   {
      "bindings": [
       {
        "authLevel": "function",
        "name": "req",
        "type": "httpTrigger",
        "direction": "in",
        "methods": [
           "get"
        ],
        "route": "hello"
       },
       {
         "name": "$return",
         "type": "http",
         "direction": "out"
       }
     ]
   }
   ```

5. Växla tillbaka till fliken HttpTrigger1, klicka på "Hämta funktions-URL" och kopiera sedan webbadressen som visas.

   > [!NOTE]
   > Bindningarna du just skapade berättar helt enkelt funktioner att svara på anonyma http GET-förfrågningar till webbadressen du just kopierade. (Nuhttps://yourfunctionappname.azurewebsites.net/api/hello?code=secretkey) har vi en skalbar serverlös https API, som kan returnera en mycket enkel nyttolast.
   > Du kan nu testa att anropa detta API från en webbläsare med hjälp av webbadressen ovan, du kan också ta bort ?code=secret delen av URL:en och bevisa att Azure Functions returnerar ett 401-fel.

## <a name="configure-and-secure-the-function-api"></a>Konfigurera och skydda funktions-API:et
1. Två extra områden i funktionsappen måste konfigureras (Auth- och nätverksbegränsningar).
1. För det första Låt oss konfigurera Autentisering / auktorisering, &lt;så&gt; klicka på namnet på funktionsappen (bredvid Z-funktionsikonen) för att visa översiktssidan.
1. Välj sedan fliken "Plattformsfunktioner" och välj "Autentisering/auktorisering".
1. Aktivera funktionen Autentisering av apptjänst.
1. Under Autentiseringsleverantörer väljer du "Azure Active Directory" och väljer "Avancerat" i växeln hanteringsläge.
1. Klistra in serverfunktions-API:ets program-ID (från Azure AD B2C i rutan "Klient-ID"
1. Klistra in den välkända slutpunkten för konfiguration med öppen id från registrerings- eller inloggningsprincipen i rutan Utfärdares URL (vi spelade in den här konfigurationen tidigare).
1. Välj OK.
1. Ange åtgärden som ska vidtas när begäran inte autentiseras till "Logga in med Azure Active Directory" och klicka sedan på Spara.

   > [!NOTE]
   > Nu distribueras funktions-API:et och bör kasta 401 svar om rätt nyckel inte anges och returnera data när en giltig begäran presenteras.
   > Du har lagt till ytterligare försvars-djupgående säkerhet i EasyAuth genom att konfigurera alternativet Logga in med Azure AD för att hantera oautentiserade begäranden. Tänk på att detta kommer att ändra obehörigbegäran beteende mellan Backend Funktion App och Frontend SPA som EasyAuth kommer att utfärda en 302 omdirigera till AAD i stället för en 401 Inte auktoriserad svar, kommer vi att rätta detta med hjälp av API Management senare.
   > Vi har fortfarande ingen IP-säkerhet tillämpas, om du har en giltig nyckel och OAuth2 token, kan vem som helst ringa detta från var som helst - helst vill vi tvinga alla förfrågningar att komma via API Management.
   > Om du använder förbrukningsnivån för API Management kan du inte utföra den här låsningen med VIP eftersom det inte finns någon dedikerad statisk IP för den nivån, du måste förlita dig på metoden för att låsa dina API-anrop via den delade hemliga funktionsnyckeln , så steg 11-13 kommer inte att vara möjligt.

1. Stäng bladet "Autentisering/auktorisering" 
1. Välj Nätverk och välj sedan "Åtkomstbegränsningar"
1. Lås sedan av de tillåtna funktionsapp-IP-adresser till API Management-instansen VIP. Denna VIP visas i API-hantering - översikt delen av portalen.
1. Om du vill fortsätta att interagera med funktionsportalen och utföra de valfria stegen nedan bör du lägga till din egen offentliga IP-adress eller CIDR-intervall även här.
1. När det finns en tillåt-post i listan lägger Azure till en implicit neka-regel för att blockera alla andra adresser. 

Du måste lägga till CIDR-formaterade adressblock på IP-begränsningspanelen. När du behöver lägga till en enda adress, till exempel API Management VIP, måste du lägga till den i formatet xx.xx.xx.xx.

   > [!NOTE]
   > Nu ska inte funktions-API:et kunna anropas från någon annanstans än via API-hantering eller din adress.
   
## <a name="import-the-function-app-definition"></a>Importera funktionsappdefinitionen
1. Öppna *API Management-bladet*och öppna sedan *instansen*.
1. Välj API:ernas blad i avsnittet API Management i din instans.
1. Välj "Funktionsapp" i fönstret Lägg till ett nytt API och välj sedan "Full" högst upp i popup-fönstret.
1. Klicka på Bläddra, välj den funktionsapp som du är värd för API:et inuti och klicka på välj.
1. Ge API:et ett namn och en beskrivning av API-hanteringens interna användning och lägg till det i den "obegränsade" produkten.
1. Se till att du registrerar bas-URL:en för senare användning och klicka sedan på skapa.

## <a name="configure-oauth2-for-api-management"></a>Konfigurera Oauth2 för API-hantering

1. Välj sedan bladet Oauth 2.0 på fliken Säkerhet och klicka på Lägg till
1. Ange värden för *visningsnamn* och *beskrivning* för den tillagda Oauth-slutpunkten (dessa värden visas i nästa steg som en Oauth2-slutpunkt).
1. Du kan ange valfritt värde i klientregistreringssidans URL, eftersom det här värdet inte används.
1. Kontrollera typen *Implicit auth* grant och lämna beviljandetypen auktoriseringskod markerad.
1. Flytta till slutpunktsfälten *Auktorisering* och *Token* och ange de värden som du hämtade från det välkända konfigurations xml-dokumentet tidigare.
1. Bläddra nedåt och fyll i en *ytterligare brödtextparameter* som kallas "resurs" med klient-ID:et för serverdelsfunktions-API från Azure AD B2C-appregistreringen
1. Välj "Klientuppgifter", ange klient-ID till utvecklarkonsolappens app-ID – hoppa över det här steget om du använder modellen för förbruknings-API Management.
1. Ange klienthemligheten till nyckeln som du spelade in tidigare - hoppa över det här steget om du använder modellen för hantering av förbruknings-API.Set the Client Secret to the key you recorded earlier - skip this step if using the consumption API Management model.
1. Slutligen, nu registrera redirect_uri av auth kod bidrag från API Management för senare användning.

## <a name="set-up-oauth2-for-your-api"></a>Konfigurera Oauth2 för ditt API
1. Ditt API visas till vänster på portalen under avsnittet Alla API:er, öppna ditt API genom att klicka på det.
1. Välj fliken Inställningar.
1. Uppdatera inställningarna genom att välja "Oauth 2.0" från alternativknappen för användarauktorisering.
1. Välj den Oauth-server som du definierade tidigare.
1. Markera kryssrutan Åsidosätt omfattning och ange det scope som du spelade in för serverd-API-anropet tidigare.

   > [!NOTE]
   > Nu har vi en API Management-instans som vet hur du får åtkomsttoken från Azure AD B2C för att auktorisera begäranden och förstår vår Oauth2 Azure Active Directory B2C-konfiguration.

## <a name="set-up-the-cors-and-validate-jwt-policies"></a>Ställa in **CORS-** och **validate-jwt-principer**

> Följande avsnitt bör följas oavsett den APIM-nivå som används. 

1. Växla tillbaka till designfliken och välj "Alla API:er" och klicka sedan på kodvisningsknappen för att visa principredigeraren.
1. Redigera det inkommande avsnittet och klistra in nedanstående xml så att det lyder som följande.

   ```xml
   <inbound>
      <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
         <openid-config url="https://tenant.b2clogin.com/tenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1_MyDefaultPolicy" />
         <required-claims>
            <claim name="aud">
               <value>your-backend-api-application-client-id</value>
            </claim>
         </required-claims>
      </validate-jwt>
      <cors>
         <allowed-origins>
            <origin>*</origin>
         </allowed-origins>
         <allowed-methods>
           <method>GET</method>
         </allowed-methods>
         <allowed-headers>
            <header>*</header>
         </allowed-headers>
         <expose-headers>
           <header>*</header>
         </expose-headers>
       </cors>
   </inbound>
   ```
1. Redigera url:en openid-config för att matcha din välkända Azure AD B2C-slutpunkt för registrerings- eller inloggningsprincipen.
1. Redigera anspråksvärdet för att matcha det giltiga program-ID:t, även kallat klient-ID för serverd-API-programmet och spara.

   > [!NOTE]
   > Nu kan API-hantering svara på begäranden mellan ursprung till JS SPA-appar, och den kommer att utföra begränsning, hastighetsbegränsning och förvalidering av JWT-auth-token som skickas innan begäran vidarebefordras till funktions-API:et.

   > [!NOTE]
   > Följande avsnitt är valfritt och gäller inte för **förbrukningsnivån,** som inte stöder utvecklarportalen.
   > Om du inte tänker använda utvecklarportalen, eller inte kan använda den eftersom du använder förbrukningsnivån, hoppa över det här steget och hoppa direkt till ["Bygg JavaScript SPA för att använda API".](#build-the-javascript-spa-to-consume-the-api)

## <a name="optional-configure-the-developer-portal"></a>[Valfritt] Konfigurera utvecklarportalen

1. Öppna Azure AD B2C-bladet och navigera till programregistreringen för utvecklarportalen
1. Ange posten Svara på URL till den du noterade nedåt när du konfigurerade redirect_uri av auth-kodbidraget i API Management tidigare.

   Nu när OAuth 2.0-användarauktoriseringen är aktiverad på `Echo API`erhåller Utvecklarkonsolen en åtkomsttoken för användaren innan API:et anropas.

1. Bläddra till alla `Echo API` åtgärder under utvecklarportalen och välj Prova den **för** att ta dig till utvecklarkonsolen.
1. Observera ett nytt objekt i avsnittet **Auktorisering,** som motsvarar den auktoriseringsserver som du just har lagt till.
1. Välj **Auktoriseringskod** i listrutan auktorisering och du uppmanas att logga in på Azure AD-klienten. Om du redan är inloggad med kontot kanske du inte blir tillfrågad.
1. Efter lyckad inloggning `Authorization: Bearer` läggs ett huvud till i begäran, med en åtkomsttoken från Azure AD B2C kodad i Base64. 
1. Välj **Skicka** och du kan anropa API:et.

   > [!NOTE]
   > Nu kan API-hantering hämta token för utvecklarportalen för att testa ditt API och kan förstå dess definition och återge lämplig testsida i utvecklingsportalen.

1. Från översiktsbladet för API Management-portalen klickar du på Utvecklarportalen för att logga in som administratör för API:et.
1. Här kan du och andra utvalda konsumenter av ditt API testa och anropa dem från en konsol.
1. Välj "Produkter" och välj sedan "Obegränsad", välj sedan det API vi skapade tidigare och klicka på "PROVA DET"
1. Ta fram API-prenumerationsnyckeln och kopiera den någonstans säkert tillsammans med url:en för begäran som du behöver senare.
1. Välj också Implicit, från oauth auth-listrutan och du kan behöva autentisera här med en popup.
1. Klicka på "Skicka" och om allt är bra, bör din funktion App svara tillbaka med ett hej meddelande via API-hantering med en 200 OK meddelande och några JSON.

   > [!NOTE]
   > Grattis, du har nu Azure AD B2C, API Management och Azure Functions som arbetar tillsammans för att publicera, skydda och använda ett API. Du kanske har märkt att API:et faktiskt är skyddat två gånger med den här metoden, en gång med API Management Ocp-Subscription-Key Header och en gång med Auktoriseringen: Bearer JWT.
   > Du skulle vara korrekt, eftersom det här exemplet är ett JavaScript-program för ensidig sida använder vi API Management Key endast för hastighetsbegränsande och faktureringssamtal.
   > Den faktiska auktorisering och autentisering hanteras av Azure AD B2C och är inkapslad i JWT, som valideras två gånger, en gång av API Management och sedan av Azure Functions.

## <a name="build-the-javascript-spa-to-consume-the-api"></a>Skapa JavaScript SPA för att använda API
1. Öppna bladet för lagringskonton i Azure-portalen 
1. Välj det konto du skapade och välj bladet "Statisk webbplats" i avsnittet Inställningar (om du inte ser alternativet "Statisk webbplats" kontrollerar du att du har skapat ett V2-konto).
1. Ställ in den statiska webbhostingfunktionen på "aktiverad" och ställ in indexdokumentnamnet på 'index.html', klicka sedan på 'save'.
1. Lägg till innehållet i den primära slutpunkten, eftersom den här platsen är där frontend-platsen kommer att vara värd. 

   > [!NOTE]
   > Du kan använda antingen Azure Blob Storage + CDN omskrivning, eller Azure App Service - men Blob Storage statisk webbplats hosting-funktionen ger oss en standardbehållare för att betjäna statiskt webbinnehåll / html / js / css från Azure Storage och kommer att dra slutsatsen en standardsida för oss för noll arbete.

## <a name="upload-the-js-spa-sample"></a>Ladda upp JS SPA-provet
1. I bladet för lagringskonto väljer du bladet "Blobbar" från avsnittet Blob Service och klickar på den $web behållaren som visas i den högra rutan.
1. Spara koden nedan till en fil lokalt på datorn som index.html och ladda sedan upp filen index.html till $web behållaren.

   ```html
   <!doctype html>
   <html lang="en">
   <head>
        <meta charset="utf-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1">
        <title>Sample JS SPA</title>
        <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" integrity="sha384-Gn5384xqQ1aoWXA+058RXPxPg6fy4IWvTNh0E263XmFcJlSAwiGgFAW/dAiS6JXm" crossorigin="anonymous">
   </head>
   <body>
        <div class="container-fluid">
            <div class="row">
                <div class="col-md-12">
                    <nav class="navbar navbar-expand-lg navbar-light bg-light navbar-dark bg-dark">
                        <a class="navbar-brand" href="#">Sample Code</a>
                        <ul class="navbar-nav ml-md-auto">
                            <li class="nav-item dropdown">
                                <a class="btn btn-large btn-success" onClick="login()">Sign In</a>
                            </li>
                        </ul>
                    </nav>
                </div>
            </div>
            <div class="row">
                <div class="col-md-12">
                    <div class="jumbotron">
                        <h2>
                    <div id="message">Hello, world!</div>
                    </h2>
                        <p>
                            <a class="btn btn-primary btn-large" onClick="GetAPIData()">Call API</a>
                        </p>
                    </div>
                </div>
            </div>
        </div>
        <script src="https://code.jquery.com/jquery-3.2.1.min.js"></script>
        <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.12.9/umd/popper.min.js" integrity="sha384-ApNbgh9B+Y1QKtv3Rn7W3mgPxhU9K/ScQsAP7hUibX39j7fakFPskvXusvfa0b4Q" crossorigin="anonymous"></script>
        <script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/js/bootstrap.min.js" integrity="sha384-JZR6Spejh4U02d8jOt6vLEHfe/JQGiRRSQQxSfFWpi1MquVdAyjUar5+76PVCmYl" crossorigin="anonymous"></script>
        <script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.js"></script>
        <script lang="javascript">
            var applicationConfig = {
                clientID: "clientidgoeshere",
                authority: "https://tenant.b2clogin.com/tfp/tenant/policy",
                b2cScopes: ["https://tenant/app/scope"],
                webApi: 'http://functionurl',
                subKey: 'apimkeygoeshere'
            };
            var msalConfig = {
                auth: {
                        clientId: applicationConfig.clientID, 
                        authority:  applicationConfig.authority,
                        validateAuthority: false
                },
                cache: {
                        cacheLocation: "localStorage",
                        storeAuthStateInCookie: true
                }
            };
            var clientApplication = new Msal.UserAgentApplication(msalConfig);
            function login() {
                var loginRequest = {
                    scopes: applicationConfig.b2cScopes
                };
                clientApplication.loginPopup(loginRequest).then(function (loginResponse) {
                    var tokenRequest = {
                        scopes: applicationConfig.b2cScopes
                    };
                    clientApplication.acquireTokenSilent(tokenRequest).then(function (tokenResponse) {
                        document.getElementById("signinbtn").innerHTML = "Logged in as: " + clientApplication.account.name;
                        document.getElementById("callapibtn").hidden = false
                        }).catch(function (error) {
                            clientApplication.acquireTokenPopup(tokenRequest).then(function (tokenResponse) {
                                }).catch (function (error) {
                                    console.log("Error acquiring the popup:\n" + error);
                                });
                        })
                    }).catch (function (error) {
                        console.log("Error during login:\n" + error);
                });
            }
            function GetAPIData() {
                var tokenRequest = {
                    scopes: applicationConfig.b2cScopes
                }
                clientApplication.acquireTokenSilent(tokenRequest).then(function (tokenResponse) {
                    callApiWithAccessToken(tokenResponse.accessToken);
                    }).catch(function (error) {
                        clientApplication.acquireTokenPopup(tokenRequest).then(function (tokenResponse) {
                            callApiWithAccessToken(tokenResponse.accessToken);
                            
                        }).catch(function (error) {
                            console.log("Error acquiring the access token to call the Web api:\n" + error);
                        });
                    })
            }
            function callApiWithAccessToken(token)
            {
                console.log("calling "  + applicationConfig.webApi +  " with " + token);
                    // Make the api call here
                $.ajax({
                    type: "get",
                    headers: {'Authorization': 'Bearer ' + token, 'Ocp-Apim-Subscription-Key': applicationConfig.subKey},                   url: applicationConfig.webApi
                }
                ).done(function (body) {
                    document.getElementById("message").innerHTML = "The API Said " + body;
                });
            }
        </script>
    </body>
   </html>
   
   ```

1. Bläddra till den primära slutpunkten för statisk webbplats som du sparade tidigare i det sista avsnittet.

   > [!NOTE]
   > Grattis har du precis distribuerat en JavaScript Single Page App till Azure Storage Eftersom vi inte har konfigurerat JS-appen med dina nycklar för API:et eller konfigurerat JS-appen med din Azure AD B2C-information ännu – sidan fungerar inte ännu om du öppnar den.

## <a name="configure-the-js-spa-for-azure-ad-b2c"></a>Konfigurera JS SPA för Azure AD B2C
1. Nu vet vi var allt är: vi kan konfigurera SPA med lämplig API Management API-adress och rätt Azure AD B2C-program / klient-ID
1. Gå tillbaka till Azure portal lagring blad och klicka på index.html, välj sedan "Redigera Blob" 
1. Uppdatera auth-informationen för att matcha frontend-programmet som du registrerade i B2C tidigare, och notera att "b2cScopes"-värdena är för API-serverdelen.
1. WebApi-nyckeln och api-url:en finns i testfönstret för API-hantering för API-åtgärden.
1. Skapa en APIM-prenumerationsnyckel genom att gå till API Management tillbaka till API Management-bladet, välja Prenumerationer och klicka på Lägg till prenumeration och spara posten. Om du klickar på ellipsen (...) bredvid den skapade raden kan du visa tangenterna så att du kan kopiera primärnyckeln.
1. Det bör se ut ungefär som nedanstående kod: -  

   ```javascript
   var applicationConfig =
      clientID: "{aadb2c-clientid-goeshere}",
      authority: "https://{tenant}.b2clogin.com/{tenant}/{policy}",
      b2cScopes: ["https://{tenant}/{app}/{scope}"], 
      webApi: 'http://{apim-url-for-your-function}',
      subKey: '{apim-subscription-key-goes-here}'
   };
   ```

1. Klicka på Spara

## <a name="set-the-redirect-uris-for-the-azure-ad-b2c-frontend-app"></a>Ange omdirigerings-URI:er för Azure AD B2C-klientdelsappen
1. Öppna Azure AD B2C-bladet och navigera till programregistreringen för JavaScript-klientdelsprogrammet
1. Ange omdirigerings-URL:en till den du noterade nedåt när du tidigare konfigurerade den statiska webbplatsens primära slutpunkt ovan

   > [!NOTE] 
   > Den här konfigurationen resulterar i att en klient för klienten för klientprogrammet tar emot en åtkomsttoken med lämpliga anspråk från Azure AD B2C.
   > SPA kommer att kunna lägga till detta som en bärare token i https-huvudet i anropet till serverd API.
   > API Management kommer att för validera token, rate-limit anrop till slutpunkten av prenumerantnyckeln, innan du skickar via begäran till den mottagande Azure Function API.
   > SPA kommer att återge svaret i webbläsaren.

   > *Grattis, du har konfigurerat Azure AD B2C, Azure API Management, Azure Functions, Azure App Service Authorization att fungera i perfekt harmoni!*

   > [!NOTE]
   > Nu har vi en enkel app med ett enkelt skyddat API, låt oss testa den.

## <a name="test-the-client-application"></a>Testa klientprogrammet
1. Öppna exempelapp-URL:en som du noterade nere från lagringskontot som du skapade tidigare
1. Klicka på "Logga in" i det övre högra hörnet, det här klicket kommer att dyka upp din Azure AD B2C registrera dig eller logga in profil.
1. Post Logga in "Inloggad som" delen av skärmen kommer att fyllas i från din JWT.
1. Klicka nu på "Ring webb-API", och du sidan ska uppdatera med de värden som skickas tillbaka från ditt säkra API.

## <a name="and-were-done"></a>Och vi är klara
Stegen ovan kan anpassas och redigeras för att tillåta många olika användningsområden för Azure AD B2C med API Management.

## <a name="next-steps"></a>Nästa steg
* Läs mer om [Azure Active Directory och OAuth2.0](../active-directory/develop/authentication-scenarios.md).
* Kolla in fler [videor](https://azure.microsoft.com/documentation/videos/index/?services=api-management) om API Management.
* Andra sätt att skydda backend-tjänsten finns i [Autentisering av ömsesidigt certifikat](api-management-howto-mutual-certificates.md).
* [Skapa en API Management-tjänstinstans](get-started-create-service-instance.md).
* [Hantera ditt första API](import-and-publish.md).
