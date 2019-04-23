---
title: Förstå hur du använder Azure Digital Twins referens Swagger | Microsoft Docs
description: För att förstå hur du använder Azure Digital Twins Swagger referensdokumentation.
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/31/2018
ms.author: adgera
ms.custom: seodec18
ms.openlocfilehash: 1746e1d53be01e6c40b5d1948c666960970b75a0
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60001991"
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

![Swagger upp][1]

Hantering av API-objektmodeller visas också.

![Swagger-modeller][2]

Du kan välja varje listad objektmodell för en mer detaljerad översikt över nyckelattribut.

![Swagger-modell][3]

De genererade Swagger-objektmodellerna bekvämt att se alla tillgängliga Azure Digital Twins [objekt och API: er](./concepts-objectmodel-spatialgraph.md). Utvecklare kan använda den här resursen när de skapar lösningar på Azure Digital Twins.

## <a name="endpoint-summary"></a>Sammanfattning av slutpunkt

Swagger innehåller också en omfattande översikt över alla slutpunkter som ska utgöra Management-API: er.

Varje listad slutpunkt innehåller också nödvändiga informationen, till exempel den:

* Obligatoriska parametrar.
* Den begärda parameterdatatyper.
* HTTP-metoden för åtkomst till resursen.

![Swagger-slutpunkter][4]

Välj varje resurs om du vill se en mer detaljerad översikt.

## <a name="use-swagger-to-test-endpoints"></a>Använd Swagger för att testa slutpunkter

En av kraftfulla funktioner som innehåller Swagger är möjligheten att testa en API-slutpunkt direkt via i UI-dokumentationen.

När du har valt en viss slutpunkt kan du se **prova**.

![Swagger-försök][5]

Expandera avsnittet att ta fram inmatningsfält för alla obligatoriska och valfria parametrar. Ange rätt värden och välj **kör**.

![Ett försök gjordes att swagger][6]

När du kör testet kan verifiera du svarsdata.

## <a name="swagger-response-data"></a>Svarsdata för swagger

Varje listad slutpunkt innehåller också svarsdata för brödtexten för att verifiera din utveckling och tester. De här exemplen omfattar statuskoder och JSON som du vill se för lyckade HTTP-begäranden.

![Swagger-svar][7]

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
    | Name  | Ersätt med | Exempel |
    |---------|---------|---------|
    | YOUR_SWAGGER_URL | Din URL för Management REST API-dokumentationen som finns i portalen  | `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger` |

3. Bevilja behörigheter för din app åtkomst till Azure Digital Twins. Under **nödvändiga behörigheter**, ange `Azure Digital Twins` och välj **delegerade behörigheter**. Välj sedan **bevilja behörigheter**.

    ![Azure AD app-registreringar Lägg till api](../../includes/media/digital-twins-permissions/aad-app-req-permissions.png)

4. Konfigurera applikationsmanifestet för att tillåta implicit OAuth 2.0-flöde. Klicka på **Manifest** att öppna programmanifestet för din app. Ange *oauth2AllowImplicitFlow* till `true`.

    ![Azure AD implicit flöde](../../includes/media/digital-twins-permissions/aad-app-allow-implicit-flow.png)

5. Kopiera ID för din Azure AD-app.

6. Klicka på auktorisera på sidan med swagger.

    ![Swagger auktorisera knappen](../../includes/media/digital-twins-permissions/swagger-select-authorize-btn.png)

7. Klistra in program-ID i fältet client_id.

    ![Swagger client_id fält](../../includes/media/digital-twins-permissions/swagger-auth-form.png)

    ![Swagger bevilja programbehörigheter](../../includes/media/digital-twins-permissions/swagger-grant-application-permissions.png)

8. Du bör nu se ägar-token för autentisering skickas i auktoriseringsrubriken och identiteten för den inloggade användaren visas i resultatet.

    ![Swagger token resultat](../../includes/media/digital-twins-permissions/swagger-token-example.png)

## <a name="next-steps"></a>Nästa steg

- Läs mer om Azure Digital Twins objektmodeller och rumsliga intelligence diagrammet, läsa [förstå Azure Digital Twins objekt modeller](./concepts-objectmodel-spatialgraph.md).

- Läs hur du autentiserar med ditt API Management, [autentisera med API: er](./security-authenticating-apis.md).

<!-- Images -->
[1]: media/how-to-use-swagger/swagger_management_top.PNG
[2]: media/how-to-use-swagger/swagger_management_models.PNG
[3]: media/how-to-use-swagger/swagger_management_model.PNG
[4]: media/how-to-use-swagger/swagger_management_endpoints.PNG
[5]: media/how-to-use-swagger/swagger_management_try.PNG
[6]: media/how-to-use-swagger/swagger_management_tried.PNG
[7]: media/how-to-use-swagger/swagger_management_response.PNG
