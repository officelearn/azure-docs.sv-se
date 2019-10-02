---
title: Skydda ett Azure API Management-API med Azure Active Directory B2C
description: Lär dig hur du använder åtkomsttoken som utfärdats av Azure Active Directory B2C för att skydda en Azure API Management API-slutpunkt.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/31/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: c5fb79fc3aa3297068f93b631d11e967c9345f4c
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2019
ms.locfileid: "71717150"
---
# <a name="secure-an-azure-api-management-api-with-azure-ad-b2c"></a>Skydda ett Azure API Management-API med Azure AD B2C

Lär dig hur du begränsar åtkomsten till ditt Azure API Management-API (APIM) till klienter som har autentiserats med Azure Active Directory B2C (Azure AD B2C). Följ stegen i den här artikeln för att skapa och testa en inkommande princip i APIM som begränsar åtkomsten till de begär Anden som innehåller en giltig Azure AD B2C-utfärdad åtkomsttoken.

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande resurser på plats innan du fortsätter med stegen i den här artikeln:

* [Azure AD B2C klient](tutorial-create-tenant.md)
* [Programmet är registrerat](tutorial-register-applications.md) i din klient
* [Användar flöden som skapats](tutorial-create-user-flows.md) i din klient organisation
* [Publicerat API](../api-management/import-and-publish.md) i Azure API Management
* [Postman](https://www.getpostman.com/) för att testa skyddad åtkomst (valfritt)

## <a name="get-azure-ad-b2c-application-id"></a>Hämta Azure AD B2C program-ID

När du skyddar ett API i Azure API Management med Azure AD B2C behöver du flera värden för den [inkommande principen](../api-management/api-management-howto-policies.md) som du skapar i APIM. Registrera först program-ID: t för ett program som du tidigare har skapat i Azure AD B2C klient organisationen. Om du använder det program som du skapade i kraven använder du program-ID: t för *webbapp1*.

1. Bläddra till Azure AD B2C-klienten i [Azure Portal](https://portal.azure.com).
1. Under **Hantera**väljer du **program**.
1. Registrera värdet i **program-ID:** t för *webapp1* eller något annat program som du har skapat tidigare.

  ![Platsen för ett B2C programs program-ID i Azure Portal](media/secure-apim-with-b2c-token/portal-02-app-id.png)

## <a name="get-token-issuer-endpoint"></a>Hämta token för utfärdarens slut punkt

Sedan hämtar du den välkända konfigurations-URL: en för en av dina Azure AD B2C användar flöden. Du behöver också slut punkts-URI för token som du vill ha stöd för i Azure API Management.

1. Bläddra till Azure AD B2C-klienten i [Azure Portal](https://portal.azure.com).
1. Under **principer**väljer du **användar flöden (principer)** .
1. Välj en befintlig princip, till exempel *B2C_1_signupsignin1*, och välj sedan **Kör användar flöde**.
1. Registrera URL: en i en hyperlänk som visas under rubriken **Kör användar flöde** längst upp på sidan. Den här URL: en är den OpenID anslutnings välkända identifierings slut punkten för användar flödet och du använder den i nästa avsnitt när du konfigurerar den inkommande principen i Azure API Management.

    ![Välkänd URI-hyperlänk på sidan kör nu i Azure Portal](media/secure-apim-with-b2c-token/portal-01-policy-link.png)

1. Välj hyperlänken för att bläddra till OpenID Connect-välkända konfigurations sidan.
1. På sidan som öppnas i webbläsaren registrerar du värdet `issuer`, till exempel:

    `https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/`

    Du använder det här värdet i nästa avsnitt när du konfigurerar ditt API i Azure API Management.

Du bör nu ha två URL: er som har registrerats för användning i nästa avsnitt: den OpenID Connect-välkända konfigurations slut punkts-URL: en och utfärdar-URI: n. Exempel:

```
https://yourb2ctenant.b2clogin.com/yourb2ctenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1_signupsignin1
https://yourb2ctenant.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/
```

## <a name="configure-inbound-policy-in-azure-api-management"></a>Konfigurera inkommande princip i Azure API Management

Nu är du redo att lägga till den inkommande principen i Azure API Management som validerar API-anrop. Genom att lägga till en princip för [JWT-validering](../api-management/api-management-access-restriction-policies.md#ValidateJWT) som verifierar mål gruppen och utfärdaren i en åtkomsttoken, kan du se till att endast API-anrop med en giltig token accepteras.

1. Bläddra till Azure API Management-instansen i [Azure Portal](https://portal.azure.com).
1. Välj **API:er**.
1. Välj det API som du vill skydda med Azure AD B2C.
1. Välj fliken **Design**.
1. Under **inkommande bearbetning**väljer du **\< @ no__t-3 @ no__t-4** för att öppna princip kod redigeraren.
1. Placera följande `<validate-jwt>`-tagg i principen `<inbound>`.

    1. Uppdatera värdet `url` i elementet `<openid-config>` med din princips välkända konfigurations-URL.
    1. Uppdatera elementet `<audience>` med program-ID: t för programmet som du skapade tidigare i B2C-klienten (till exempel *webapp1*).
    1. Uppdatera elementet `<issuer>` med den slut punkt för token issueer som du registrerade tidigare.

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

För att säkerställa att endast autentiserade anropare har åtkomst till ditt API kan du verifiera din Azure API Management-konfiguration genom att anropa API: et med [Postman](https://www.getpostman.com/).

Om du vill anropa API: et behöver du både en åtkomsttoken som utfärdats av Azure AD B2C och en prenumerations nyckel för APIM.

### <a name="get-an-access-token"></a>Hämta en åtkomsttoken

Du behöver först en token som utfärdats av Azure AD B2C att använda i rubriken `Authorization` i Postman. Du kan få en genom att använda funktionen **Kör nu** i ditt inloggnings-eller inloggnings användar flöde som du har skapat som en av kraven.

1. Bläddra till Azure AD B2C-klienten i [Azure Portal](https://portal.azure.com).
1. Under **principer**väljer du **användar flöden (principer)** .
1. Välj ett befintligt inloggnings-eller inloggnings användar flöde, till exempel *B2C_1_signupsignin1*.
1. För **program**väljer du *webapp1*.
1. För **svars-URL**väljer du `https://jwt.ms`.
1. Välj **Kör användar flöde**.

    ![Sidan kör användar flöde för registrering av inloggnings användar flöde i Azure Portal](media/secure-apim-with-b2c-token/portal-03-user-flow.png)

1. Slutför inloggningsprocessen. Du bör omdirigera till `https://jwt.ms`.
1. Registrera det kodade token-värdet som visas i webbläsaren. Du använder det här token-värdet för Authorization-huvudet i Postman.

    ![Kodat token-värde visas på jwt.ms](media/secure-apim-with-b2c-token/jwt-ms-01-token.png)

### <a name="get-api-subscription-key"></a>Hämta API-prenumerations nyckel

Ett klient program (i det här fallet Postman) som anropar ett publicerat API måste innehålla en giltig API Management prenumerations nyckel i HTTP-begäranden till API: et. Så här hämtar du en prenumerations nyckel som ska ingå i din Postman HTTP-begäran:

1. Bläddra till din Azure API Management-tjänstinstans i [Azure Portal](https://portal.azure.com).
1. Välj **Prenumerationer**.
1. Välj ellipsen för **Product: Obegränsad @ no__t-0 och välj sedan **Visa/Dölj nycklar**.
1. Registrera den **primära nyckeln** för produkten. Du använder den här nyckeln för sidhuvudet `Ocp-Apim-Subscription-Key` i din HTTP-begäran i Postman.

![Sidan prenumerations nyckel med Visa/Dölj-nycklar markerade i Azure Portal](media/secure-apim-with-b2c-token/portal-04-api-subscription-key.png)

### <a name="test-a-secure-api-call"></a>Testa ett Secure API-anrop

När du har registrerat åtkomst-token och APIM prenumerations nyckel är du nu redo att testa om du har konfigurerat säker åtkomst till API: et korrekt.

1. Skapa en ny `GET`-begäran i [Postman](https://www.getpostman.com/). För fråge-URL: en anger du slut punkten för Utskicks listan för API: et som du har publicerat som en av kraven. Exempel:

    `https://contosoapim.azure-api.net/conference/speakers`

1. Lägg sedan till följande rubriker:

    | Nyckel | Value |
    | --- | ----- |
    | `Authorization` | Kodat token-värde som du registrerade tidigare, föregås av `Bearer ` (inklusive utrymmet efter "Bearer") |
    | `Ocp-Apim-Subscription-Key` | APIM prenumerations nyckel som du registrerade tidigare |

    URL: en för **Get** -begäran och **rubrikerna** bör se ut ungefär så här:

    ![Postman UI visar URL och rubriker för GET-begäran](media/secure-apim-with-b2c-token/postman-01-headers.png)

1. Klicka på knappen **Skicka** i Postman för att köra begäran. Om du har konfigurerat allting korrekt, bör du se ett JSON-svar med en samling konferens högtalare (visas här trunkerade):

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

Nu när du har gjort en lyckad begäran kan du testa fel fallet för att säkerställa att anrop till ditt API med en *ogiltig* token avvisas som förväntat. Ett sätt att utföra testet är att lägga till eller ändra några tecken i värdet token och sedan köra samma `GET`-begäran som tidigare.

1. Lägg till flera tecken till token-värdet för att simulera en ogiltig token. Lägg exempelvis till "ogiltig" till värdet för token:

    ![Avsnittet rubriker i Postman UI visar ogiltig tillagd till token](media/secure-apim-with-b2c-token/postman-02-invalid-token.png)

1. Klicka på knappen **Skicka** för att köra begäran. Med en ogiltig token är det förväntade resultatet en `401` otillåten status kod:

    ```JSON
    {
        "statusCode": 401,
        "message": "Unauthorized. Access token is missing or invalid."
    }
    ```

Om du ser status koden `401` har du verifierat att endast anropare med en giltig åtkomsttoken som utfärdats av Azure AD B2C kan göra lyckade förfrågningar till ditt Azure API Management-API.

## <a name="support-multiple-applications-and-issuers"></a>Stöd för flera program och utfärdare

Flera program interagerar vanligt vis med en enda REST API. Om du vill aktivera API: et för att acceptera token som är avsedda för flera program, lägger du till deras program-ID i `<audiences>`-elementet i den inkommande APIM-principen.

```XML
<!-- Accept tokens intended for these recipient applications -->
<audiences>
    <audience>44444444-0000-0000-0000-444444444444</audience>
    <audience>66666666-0000-0000-0000-666666666666</audience>
</audiences>
```

På samma sätt kan du lägga till slut punkts-URI: er för ett `<issuers>`-element i APIM inkommande princip för att stödja flera token utfärdare.

```XML
<!-- Accept tokens from multiple issuers -->
<issuers>
    <issuer>https://yourb2ctenant.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
    <issuer>https://login.microsoftonline.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
</issuers>
```

## <a name="migrate-to-b2clogincom"></a>Migrera till b2clogin.com

Om du har ett APIM-API som validerar token som utfärdats av den äldre `login.microsoftonline.com`-slutpunkten bör du migrera API: et och de program som anropar det för att använda token som utfärdats av [b2clogin.com](b2clogin.md).

Du kan följa den här allmänna processen för att utföra en mellanlagrad migrering:

1. Lägg till stöd i din APIM inkommande princip för token som utfärdats av både b2clogin.com och login.microsoftonline.com.
1. Uppdatera dina program en i taget för att hämta tokens från b2clogin.com-slutpunkten.
1. När alla dina program har tagit emot token från b2clogin.com tar du bort stöd för login.microsoftonline.com utfärdade tokens från API: et.

I följande exempel APIM inkommande princip visas hur du accepterar tokens som utfärdats av både b2clogin.com och login.microsoftonline.com. Dessutom stöder den API-begäranden från två program.

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

Mer information om Azure API Management-principer finns i [referens index för APIM-principer](../api-management/api-management-policies.md).

Du hittar information om hur du migrerar OWIN webb-API: er och deras program till b2clogin.com i [Migrera ett OWIN-baserat webb-API till b2clogin.com](multiple-token-endpoints.md).
