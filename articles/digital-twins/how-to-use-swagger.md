---
title: Förstå hur du använder Azure Digital Twins referens Swagger | Microsoft Docs
description: För att förstå hur du använder Azure Digital Twins Swagger referensdokumentation.
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: v-adgera
ms.custom: seodec18
ms.openlocfilehash: 842c35a1575c7bcf6f547fb04d5680178b3bee78
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/06/2019
ms.locfileid: "66730355"
---
# <a name="azure-digital-twins-swagger-reference-documentation"></a>Referensdokumentation för Azure Digital Twins Swagger

Varje etablerade Azure Digital Twins-instans innehåller en egen automatiskt genererade Swagger-referensdokumentation.

[Swagger](https://swagger.io/), eller [OpenAPI](https://www.openapis.org/), unites komplex API-information till en resurs för interaktiva och språkoberoende referens. Swagger innehåller viktiga referensmaterial om vilken JSON-nyttolaster, HTTP-metoder och slutpunkter du använder för att utföra åtgärder mot ett API.

## <a name="swagger-summary"></a>Swagger-sammanfattning

Swagger innehåller en interaktiv sammanfattning av ditt API, vilket innefattar:

* Information om modellen API och objekt.
* REST API-slutpunkter som anger nödvändiga begäran nyttolaster, rubriker, parametrar, kontext sökvägar och HTTP-metoder.
* Testning av API-funktioner.
* Exempel Svarsinformation som används för att validera och bekräfta HTTP-svar.
* Information om felet.

Swagger är ett praktiskt verktyg som hjälper till med utveckling och testning anrop som görs till Azure Digital Twins Management API: erna.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

## <a name="reference-material"></a>Referensmaterial

Det automatiskt genererade Swagger-referensmaterialet tillhandahåller en snabb överblick över viktiga begrepp, tillgängliga Management API-slutpunkter och en beskrivning av varje objektmodellen för att hjälpa utveckling och testning.

En kortfattad sammanfattning beskriver API: et.

[![Swagger upp](media/how-to-use-swagger/swagger_management_top.PNG)](media/how-to-use-swagger/swagger_management_top.PNG#lightbox)

Hantering av API-objektmodeller visas också.

[![Swagger-modeller](media/how-to-use-swagger/swagger_management_models.PNG)](media/how-to-use-swagger/swagger_management_models.PNG#lightbox)

Du kan välja varje listad objektmodell för en mer detaljerad översikt över nyckelattribut.

[![Swagger-modell](media/how-to-use-swagger/swagger_management_model.PNG)](media/how-to-use-swagger/swagger_management_model.PNG#lightbox)

De genererade Swagger-objektmodellerna bekvämt att se alla tillgängliga Azure Digital Twins [objekt och API: er](./concepts-objectmodel-spatialgraph.md). Utvecklare kan använda den här resursen när de skapar lösningar på Azure Digital Twins.

## <a name="endpoint-summary"></a>Sammanfattning av slutpunkt

Swagger innehåller också en omfattande översikt över alla slutpunkter som ska utgöra Management-API: er.

Varje listad slutpunkt innehåller också nödvändiga informationen, till exempel den:

* Obligatoriska parametrar.
* Den begärda parameterdatatyper.
* HTTP-metoden för åtkomst till resursen.

[![Swagger-slutpunkter](media/how-to-use-swagger/swagger_management_endpoints.PNG)](media/how-to-use-swagger/swagger_management_endpoints.PNG#lightbox)

Välj varje resurs om du vill se en mer detaljerad översikt.

## <a name="use-swagger-to-test-endpoints"></a>Använd Swagger för att testa slutpunkter

En av kraftfulla funktioner som innehåller Swagger är möjligheten att testa en API-slutpunkt direkt via i UI-dokumentationen.

När du har valt en viss slutpunkt kan du se **prova**.

[![Swagger-försök](media/how-to-use-swagger/swagger_management_try.PNG)](media/how-to-use-swagger/swagger_management_try.PNG#lightbox)

Expandera avsnittet att ta fram inmatningsfält för alla obligatoriska och valfria parametrar. Ange rätt värden och välj **kör**.

[![Ett försök gjordes att swagger](media/how-to-use-swagger/swagger_management_tried.PNG)](media/how-to-use-swagger/swagger_management_tried.PNG#lightbox)

När du kör testet kan verifiera du svarsdata.

## <a name="swagger-response-data"></a>Svarsdata för swagger

Varje listad slutpunkt innehåller också svarsdata för brödtexten för att verifiera din utveckling och tester. De här exemplen omfattar statuskoder och JSON som du vill se för lyckade HTTP-begäranden.

[![Swagger-svar](media/how-to-use-swagger/swagger_management_response.PNG)](media/how-to-use-swagger/swagger_management_response.PNG#lightbox)

Exemplen omfattar också felkoder för att felsöka eller förbättra misslyckas tester.

## <a name="swagger-oauth-20-authorization"></a>Swagger OAuth 2.0-auktorisering

Mer information om hur du testar interaktivt begäranden som skyddas av OAuth 2.0 finns i [officiella dokumentationen](https://swagger.io/docs/specification/authentication/oauth2/).

> [!NOTE]
> Huvudnamn användaren som skapade Azure Digital Twins resursen har en rolltilldelning för utrymme administratör och kommer att kunna skapa ytterligare rolltilldelningar för andra användare.

1. Följ stegen i [snabbstarten](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad) att skapa ett Azure AD-program av typen ***webbapp / API***. Eller du kan återanvända en befintlig appregistrering.

2. Lägg till följande svars-url i appregistreringen:

    ```plaintext
    https://YOUR_SWAGGER_URL/ui/oauth2-redirect-html
    ```
    | Namn  | Ersätt med | Exempel |
    |---------|---------|---------|
    | YOUR_SWAGGER_URL | Din URL för Management REST API-dokumentationen som finns i portalen  | `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger` |

3. Bevilja behörigheter för din app åtkomst till Azure Digital Twins. Under **nödvändiga behörigheter**, ange `Azure Digital Twins` och välj **delegerade behörigheter**. Välj sedan **bevilja behörigheter**.

    ![Azure AD app-registreringar Lägg till api](../../includes/media/digital-twins-permissions/aad-app-req-permissions.png)

4. Konfigurera applikationsmanifestet för att tillåta implicit OAuth 2.0-flöde. Klicka på **Manifest** att öppna programmanifestet för din app. Ange *oauth2AllowImplicitFlow* till `true`.

    ![Azure AD implicit flöde](../../includes/media/digital-twins-permissions/aad-app-allow-implicit-flow.png)

5. Kopiera ID för din Azure AD-app.

När du har slutfört registreringen för Azure Active Directory:

6. Klicka på den **auktorisera** knappen på din swagger-sidan.

    [![Klicka på Swagger auktorisera knappen](media/how-to-use-swagger/swagger-select-authorize-btn.png)](media/how-to-use-swagger/swagger-select-authorize-btn.png#lightbox)

7. Klistra in program-ID i den **client_id** fält.

    [![Swagger client_id fält](media/how-to-use-swagger/swagger-auth-form.png)](media/how-to-use-swagger/swagger-auth-form.png#lightbox)

8. Du omdirigeras sedan till följande modal framgång.

    [![Swagger omdirigering modal](media/how-to-use-swagger/swagger_auth_redirect.PNG)](media/how-to-use-swagger/swagger_auth_redirect.PNG#lightbox)

## <a name="next-steps"></a>Nästa steg

- Läs mer om Azure Digital Twins objektmodeller och rumsliga intelligence diagrammet, läsa [förstå Azure Digital Twins objekt modeller](./concepts-objectmodel-spatialgraph.md).

- Läs hur du autentiserar med ditt API Management, [autentisera med API: er](./security-authenticating-apis.md).