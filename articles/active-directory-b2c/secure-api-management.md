---
title: Skydda ett Azure API Management API med Hjälp av Azure Active Directory B2C
description: Lär dig hur du använder åtkomsttoken som utfärdats av Azure Active Directory B2C för att skydda en Azure API Management API-slutpunkt.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/31/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 00938d831e70289b24acb599b81016aa6e564d78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186938"
---
# <a name="secure-an-azure-api-management-api-with-azure-ad-b2c"></a>Skydda ett Azure API Management API med Azure AD B2C

Lär dig hur du begränsar åtkomsten till DITT API (Azure API Management) till klienter som har autentiserats med Azure Active Directory B2C (Azure AD B2C). Följ stegen i den här artikeln för att skapa och testa en inkommande princip i APIM som begränsar åtkomsten till endast de begäranden som innehåller en giltig Azure AD B2C-utfärdad åtkomsttoken.

## <a name="prerequisites"></a>Krav

Du behöver följande resurser innan du fortsätter med stegen i den här artikeln:

* [Azure AD B2C-klient](tutorial-create-tenant.md)
* [Ansökan registrerad](tutorial-register-applications.md) i din klient
* [Användarflöden som skapats](tutorial-create-user-flows.md) i din klientorganisation
* [Publicerat API](../api-management/import-and-publish.md) i Azure API Management
* [Brevbärare](https://www.getpostman.com/) för att testa säker åtkomst (valfritt)

## <a name="get-azure-ad-b2c-application-id"></a>Hämta Azure AD B2C-program-ID

När du skyddar ett API i Azure API Management med Azure AD B2C behöver du flera värden för den [inkommande principen](../api-management/api-management-howto-policies.md) som du skapar i APIM. Registrera först program-ID:et för ett program som du tidigare har skapat i din Azure AD B2C-klient. Om du använder programmet som du skapade i förutsättningarna använder du program-ID:et för *webbapp1*.

Du kan använda den aktuella **programupplevelsen** eller vår nya enhetliga **appregistreringar (Förhandsversion)** för att få program-ID: t. [Läs mer om den nya upplevelsen](https://aka.ms/b2cappregintro)

#### <a name="applications"></a>[Program](#tab/applications/)

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **katalog + prenumerationsfilter** på den övre menyn och välj sedan den katalog som innehåller din Azure AD B2C-klient.
1. Välj Azure AD **B2C**på den vänstra menyn . Du kan också välja **Alla tjänster** och sök efter och välj Azure **AD B2C**.
1. Välj **Program**under **Hantera**.
1. Registrera värdet i kolumnen **APPLICATION ID** för *webapp1* eller ett annat program som du tidigare har skapat.

#### <a name="app-registrations-preview"></a>[Appregistreringar (förhandsversion)](#tab/app-reg-preview/)

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **katalog + prenumerationsfilter** på den övre menyn och välj sedan den katalog som innehåller din Azure AD B2C-klient.
1. Välj Azure AD **B2C**på den vänstra menyn . Du kan också välja **Alla tjänster** och sök efter och välj Azure **AD B2C**.
1. Välj **Appregistreringar (förhandsgranskning)** och välj sedan fliken **Ägda program.**
1. Registrera värdet i kolumnen **Program (klient) ID** för *webapp1* eller ett annat program som du tidigare har skapat.

* * *

## <a name="get-token-issuer-endpoint"></a>Hämta slutpunkt för tokenutfärdaren

Hämta sedan den välkända konfigurations-URL:en för ett av dina Azure AD B2C-användarflöden. Du behöver också den tokenutfärdareslutpunkt URI som du vill stödja i Azure API Management.

1. Bläddra till din Azure AD B2C-klient i [Azure-portalen](https://portal.azure.com).
1. Under **Principer**väljer du **Användarflöden (principer)**.
1. Välj en befintlig princip, till exempel *B2C_1_signupsignin1*och välj sedan **Kör användarflöde**.
1. Registrera URL:en i hyperlänk som visas under rubriken **Kör användarflöde** högst upp på sidan. Den här URL:en är den välkända identifieringsslutpunkten för OpenID Connect för användarflödet och du använder den i nästa avsnitt när du konfigurerar den inkommande principen i Azure API Management.

    ![Välkänd URI-hyperlänk på sidan Kör nu i Azure-portalen](media/secure-apim-with-b2c-token/portal-01-policy-link.png)

1. Välj hyperlänken som du vill bläddra till den välkända konfigurationssidan OpenID Connect.
1. På sidan som öppnas i webbläsaren `issuer` registrerar du värdet, till exempel:

    `https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/`

    Du använder det här värdet i nästa avsnitt när du konfigurerar ditt API i Azure API Management.

Du bör nu ha två webbadresser som registrerats för användning i nästa avsnitt: den välkända url:en för den välkända konfigurationsslutpunkten OpenID Connect och utfärdaren URI. Ett exempel:

```
https://yourb2ctenant.b2clogin.com/yourb2ctenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1_signupsignin1
https://yourb2ctenant.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/
```

## <a name="configure-inbound-policy-in-azure-api-management"></a>Konfigurera inkommande princip i Azure API Management

Du är nu redo att lägga till den inkommande principen i Azure API Management som validerar API-anrop. Genom att lägga till en [JWT-valideringsprincip](../api-management/api-management-access-restriction-policies.md#ValidateJWT) som verifierar målgruppen och utfärdaren i en åtkomsttoken kan du se till att endast API-anrop med en giltig token accepteras.

1. Bläddra till din Azure API Management-instans i [Azure-portalen](https://portal.azure.com).
1. Välj **API:er**.
1. Välj det API som du vill skydda med Azure AD B2C.
1. Välj fliken **Design**.
1. Öppna principkodredigeraren under **Inkommande bearbetning.** ** \< / **
1. Placera följande `<validate-jwt>` tagg `<inbound>` i principen.

    1. Uppdatera `url` värdet i `<openid-config>` elementet med principens välkända konfigurations-URL.
    1. Uppdatera `<audience>` elementet med program-ID för det program som du skapade tidigare i din B2C-klientorganisation (till exempel *webapp1*).
    1. Uppdatera `<issuer>` elementet med slutpunkten för tokenutfärdaren som du spelade in tidigare.

    ```xml
    <policies>
        <inbound>
            <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
                <openid-config url="https://yourb2ctenant.b2clogin.com/yourb2ctenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1_signupsignin1" />
                <audiences>
                    <audience>44444444-0000-0000-0000-444444444444</audience>
                </audiences>
                <issuers>
                    <issuer>https://yourb2ctenant.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
                </issuers>
            </validate-jwt>
            <base />
        </inbound>
        <backend> <base /> </backend>
        <outbound> <base /> </outbound>
        <on-error> <base /> </on-error>
    </policies>
    ```

## <a name="validate-secure-api-access"></a>Verifiera säker API-åtkomst

Om du bara vill vara säkra på att autentiserade anropare kan komma åt ditt API kan du validera din Azure API Management-konfiguration genom att anropa API:et med [Postman](https://www.getpostman.com/).

För att anropa API:et behöver du både en åtkomsttoken som utfärdats av Azure AD B2C och en APIM-prenumerationsnyckel.

### <a name="get-an-access-token"></a>Hämta en åtkomsttoken

Du behöver först en token som utfärdats av Azure `Authorization` AD B2C för att använda i huvudet i Postman. Du kan få en genom att använda **funktionen Kör nu** i ditt användarflöde för registrering/inloggning som du borde ha skapat som en av förutsättningarna.

1. Bläddra till din Azure AD B2C-klient i [Azure-portalen](https://portal.azure.com).
1. Under **Principer**väljer du **Användarflöden (principer)**.
1. Välj ett befintligt användarflöde för registrering/inloggning, till exempel *B2C_1_signupsignin1*.
1. För **Program**väljer du *webapp1*.
1. För **Svars-URL**väljer du `https://jwt.ms`.
1. Välj **Kör användarflöde**.

    ![Kör sidan användarflöde för registrering av inloggningsanvändarflöde i Azure-portalen](media/secure-apim-with-b2c-token/portal-03-user-flow.png)

1. Slutför inloggningsprocessen. Du bör omdirigeras `https://jwt.ms`till .
1. Registrera det kodade tokenvärdet som visas i webbläsaren. Du använder det här tokenvärdet för auktoriseringshuvudet i Postman.

    ![Kodat tokenvärde som visas på jwt.ms](media/secure-apim-with-b2c-token/jwt-ms-01-token.png)

### <a name="get-api-subscription-key"></a>Hämta API-prenumerationsnyckel

Ett klientprogram (i det här fallet Postman) som anropar ett publicerat API måste innehålla en giltig API Management-prenumerationsnyckel i sina HTTP-begäranden till API:et. Så här hämtar du en prenumerationsnyckel som ska inkluderas i din HTTP-begäran för Postman:

1. Bläddra till din Azure API Management-tjänstinstans i [Azure-portalen](https://portal.azure.com).
1. Välj **Prenumerationer**.
1. Välj ellipsen för **Produkt: Obegränsad**och välj sedan **Visa/dölj nycklar**.
1. Registrera **primärnyckeln** för produkten. Du använder den `Ocp-Apim-Subscription-Key` här nyckeln för huvudet i HTTP-begäran i Postman.

![Sidan Prenumerationsnyckel med Visa/dölj nycklar markerade i Azure Portal](media/secure-apim-with-b2c-token/portal-04-api-subscription-key.png)

### <a name="test-a-secure-api-call"></a>Testa ett säkert API-anrop

Med åtkomsttoken och APIM-prenumerationsnyckeln inspelad är du nu redo att testa om du har konfigurerat säker åtkomst till API:et.

1. Skapa en `GET` ny begäran i [Postman](https://www.getpostman.com/). För url:en för begäran anger du slutpunkten för högtalarlistan för API:et som du publicerade som en av förutsättningarna. Ett exempel:

    `https://contosoapim.azure-api.net/conference/speakers`

1. Lägg sedan till följande rubriker:

    | Nyckel | Värde |
    | --- | ----- |
    | `Authorization` | Kodat tokenvärde som du spelade in `Bearer ` tidigare, föregås av (inkludera utrymmet efter "Bärare") |
    | `Ocp-Apim-Subscription-Key` | APIM-prenumerationsnyckel som du spelat in tidigare |

    URL:en och **rubrikerna för** **GET-begäran** ska se ut ungefär som:

    ![Postman UI som visar GET begäran URL och rubriker](media/secure-apim-with-b2c-token/postman-01-headers.png)

1. Välj knappen **Skicka** i Postman för att köra begäran. Om du har konfigurerat allt korrekt, bör du presenteras med ett JSON-svar med en samling konferenshögtalare (visas här trunkerade):

    ```JSON
    {
      "collection": {
        "version": "1.0",
        "href": "https://conferenceapi.azurewebsites.net:443/speakers",
        "links": [],
        "items": [
          {
            "href": "https://conferenceapi.azurewebsites.net/speaker/1",
            "data": [
              {
                "name": "Name",
                "value": "Scott Guthrie"
              }
            ],
            "links": [
              {
                "rel": "http://tavis.net/rels/sessions",
                "href": "https://conferenceapi.azurewebsites.net/speaker/1/sessions"
              }
            ]
          },
    [...]
    ```

### <a name="test-an-insecure-api-call"></a>Testa ett osäkert API-anrop

Nu när du har gjort en lyckad begäran, testa felfallet för att säkerställa att samtal till ditt API med en *ogiltig* token avvisas som förväntat. Ett sätt att utföra testet är att lägga till eller ändra några `GET` tecken i tokenvärdet och sedan köra samma begäran som tidigare.

1. Lägg till flera tecken i tokenvärdet för att simulera en ogiltig token. Lägg till exempel till "OGILTIG" i tokenvärdet:

    ![Avsnittet Rubriker i postmangränssnittet som visar OGILTIGt tillagt i token](media/secure-apim-with-b2c-token/postman-02-invalid-token.png)

1. Välj knappen **Skicka** för att köra begäran. Med en ogiltig token är `401` det förväntade resultatet en obehörig statuskod:

    ```JSON
    {
        "statusCode": 401,
        "message": "Unauthorized. Access token is missing or invalid."
    }
    ```

Om du `401` ser statuskoden har du verifierat att endast anropare med en giltig åtkomsttoken som utfärdats av Azure AD B2C kan göra lyckade begäranden till ditt Azure API Management API.

## <a name="support-multiple-applications-and-issuers"></a>Stöd för flera program och utfärdare

Flera program interagerar vanligtvis med ett enda REST API. Om du vill att ditt API ska kunna acceptera token som `<audiences>` är avsedda för flera program lägger du till deras program-ID:er i elementet i APIM-principen för inkommande.

```XML
<!-- Accept tokens intended for these recipient applications -->
<audiences>
    <audience>44444444-0000-0000-0000-444444444444</audience>
    <audience>66666666-0000-0000-0000-666666666666</audience>
</audiences>
```

På samma sätt, för att stödja flera tokenutfärdare, lägga till sina slutpunkts-URI:er i elementet `<issuers>` i APIM-inkommande principen.

```XML
<!-- Accept tokens from multiple issuers -->
<issuers>
    <issuer>https://yourb2ctenant.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
    <issuer>https://login.microsoftonline.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
</issuers>
```

## <a name="migrate-to-b2clogincom"></a>Migrera till b2clogin.com

Om du har ett APIM API som validerar `login.microsoftonline.com` token som utfärdats av den äldre slutpunkten, bör du migrera API:et och de program som anropar det för att använda token som utfärdats av [b2clogin.com](b2clogin.md).

Du kan följa den här allmänna processen för att utföra en stegvis migrering:

1. Lägg till stöd i din APIM-inkommande princip för token som utfärdats av både b2clogin.com och login.microsoftonline.com.
1. Uppdatera dina program en i taget för att hämta token från b2clogin.com slutpunkten.
1. När alla dina program har hämtats korrekt från b2clogin.com tar du bort stöd för login.microsoftonline.com utfärdade token från API:et.

I följande exempel visar APIM-principen för inkommande token hur du accepterar token som utfärdats av både b2clogin.com och login.microsoftonline.com. Dessutom stöder den API-begäranden från två program.

```XML
<policies>
    <inbound>
        <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
            <openid-config url="https://yourb2ctenant.b2clogin.com/yourb2ctenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1_signupsignin1" />
            <audiences>
                <audience>44444444-0000-0000-0000-444444444444</audience>
                <audience>66666666-0000-0000-0000-666666666666</audience>
            </audiences>
            <issuers>
                <issuer>https://login.microsoftonline.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
                <issuer>https://yourb2ctenant.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
            </issuers>
        </validate-jwt>
        <base />
    </inbound>
    <backend> <base /> </backend>
    <outbound> <base /> </outbound>
    <on-error> <base /> </on-error>
</policies>
```

## <a name="next-steps"></a>Nästa steg

Mer information om Azure API Management-principer finns i REFERENSINDEX för [APIM-principen](../api-management/api-management-policies.md).

Du hittar information om hur du migrerar OWIN-baserade webb-API:er och deras program för att b2clogin.com i [Migrera ett OWIN-baserat webb-API till b2clogin.com](multiple-token-endpoints.md).
