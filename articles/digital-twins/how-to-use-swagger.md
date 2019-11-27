---
title: Använda Reference Swagger-dokumentation – Azure Digitals flätar | Microsoft Docs
description: Förstå hur du använder Azure Digitals sammanflätade Swagger Reference-dokumentation.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/22/2019
ms.custom: seodec18
ms.openlocfilehash: 3d4470a89455fd4b49fb7bb5ae26375f9cf9884c
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456960"
---
# <a name="azure-digital-twins-swagger-reference-documentation"></a>Dokumentation om Azure Digitals Swagger-referens

Alla etablerade Azure Digitals dubbla instanser innehåller en egen automatiskt genererad Swagger-referens dokumentation.

[Swagger](https://swagger.io/), eller [openapi](https://www.openapis.org/), bevarar komplex API-information till en interaktiv och språk-oberoende referens resurs. Swagger tillhandahåller kritiskt referens material om vilka JSON-nyttolaster, HTTP-metoder och vissa slut punkter som ska användas för att utföra åtgärder mot ett API.

## <a name="swagger-summary"></a>Sammanfattning av Swagger

Swagger innehåller en interaktiv Sammanfattning av ditt API, som innehåller:

* Information om API-och objekt modell.
* REST API slut punkter som anger begärda nytto laster, huvuden, parametrar, kontext Sök vägar och HTTP-metoder.
* Testning av API-funktioner.
* Exempel svars information som används för att verifiera och bekräfta HTTP-svar.
* Information om fel kod.

Swagger är ett bekvämt verktyg för att hjälpa till med utvecklings-och testnings anrop till API: erna för Azure Digitals sammanställda hantering.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

## <a name="reference-material"></a>Referensmaterial

Det automatiskt genererade Swagger-referensmaterial ger en snabb överblick över viktiga begrepp, tillgängliga hanterings-API-slutpunkter och en beskrivning av varje objekt modell som underlättar utveckling och testning.

En kortfattad sammanfattning beskriver API: et.

[översikts information om ![Swagger Sammanfattning och API](media/how-to-use-swagger/swagger-management-top-img.png)](media/how-to-use-swagger/swagger-management-top-img.png#lightbox)

Objekt modeller för hanterings-API visas också.

[![Swagger-modeller som visas längst ned i Swagger-ANVÄNDARGRÄNSSNITTET](media/how-to-use-swagger/swagger-management-models-img.png)](media/how-to-use-swagger/swagger-management-models-img.png#lightbox)

Du kan markera varje objekt modell i listan för en mer detaljerad sammanfattning av nyckelattribut.

[![Swagger-modeller expanderade för att visa innehållet i modeller](media/how-to-use-swagger/swagger-management-model-img.png)](media/how-to-use-swagger/swagger-management-model-img.png#lightbox)

De genererade Swagger-objekt modellerna är praktiska för att se alla tillgängliga Azure Digital-dubbla [objekt och API: er](./concepts-objectmodel-spatialgraph.md). Utvecklare kan använda den här resursen när de skapar lösningar på digitala Azure-dubbla.

## <a name="endpoint-summary"></a>Slut punkts Sammanfattning

Swagger tillhandahåller också en grundlig översikt över alla slut punkter som utgör hanterings-API: er.

Varje slut punkt i listan innehåller även nödvändig information om begäran, till exempel:

* Obligatoriska parametrar.
* Obligatoriska parameter data typer.
* HTTP-metod för att få åtkomst till resursen.

[![Swagger-slutpunkter som visas i Swagger-ANVÄNDARGRÄNSSNITTET](media/how-to-use-swagger/swagger-management-endpoints-img.png)](media/how-to-use-swagger/swagger-management-endpoints-img.png#lightbox)

Om du vill se en mer detaljerad översikt väljer du varje resurs.

## <a name="use-swagger-to-test-endpoints"></a>Använda Swagger för att testa slut punkter

En av de kraftfulla funktionerna i Swagger ger möjlighet att testa en API-slutpunkt direkt via dokumentations gränssnittet.

När du har valt en angiven slut punkt kan du se **prova**.

[![Swagger testa knappen](media/how-to-use-swagger/swagger-management-try-img.png)](media/how-to-use-swagger/swagger-management-try-img.png#lightbox)

Expandera avsnittet för att hämta indatatyper för varje obligatorisk och valfri parameter. Ange rätt värden och välj **Kör**.

[![Swagger testa det resultat exempel](media/how-to-use-swagger/swagger-management-tried-img.png)](media/how-to-use-swagger/swagger-management-tried-img.png#lightbox)

När du har kört testet kan du validera svars data.

## <a name="swagger-response-data"></a>Svars data för Swagger

Varje slut punkt i listan innehåller även svars text data för att verifiera utveckling och tester. I de här exemplen ingår de status koder och JSON som du vill se för lyckade HTTP-begäranden.

[exempel på ![Swagger JSON-svar](media/how-to-use-swagger/swagger-management-response-img.png)](media/how-to-use-swagger/swagger-management-response-img.png#lightbox)

I exemplen finns även felkoder som hjälper dig att felsöka eller förbättra misslyckade tester.

## <a name="swagger-oauth-20-authorization"></a>Swagger OAuth 2,0-auktorisering

> [!NOTE]
> * Användarens huvud namn som skapade Azure Digitals sammanflätade resurser har en roll tilldelning för utrymmes administratör och kommer att kunna skapa ytterligare roll tilldelningar för andra användare. Sådana användare och deras roller kan auktoriseras för att anropa API: erna.

1. Följ stegen i [den här snabb](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad) starten för att skapa och konfigurera ett Azure AD-program. Du kan också återanvända en befintlig app-registrering.

1. Lägg till följande **omdirigerings-URL** i din Azure AD-App-registrering:

    [![registrera Swagger omdirigerings-URL i AAD](media/how-to-use-swagger/swagger-aad-redirect-url-registration.png)](media/how-to-use-swagger/swagger-aad-redirect-url-registration.png#lightbox)

    ```plaintext
    https://YOUR_SWAGGER_URL/ui/oauth2-redirect-html
    ```
    | Namn  | Ersätt med | Exempel |
    |---------|---------|---------|
    | YOUR_SWAGGER_URL | URL: en för hanterings REST API-dokumentationen finns i portalen  | `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger` |

1. Kopiera **klient-ID: t** för din Azure AD-App.

När du har slutfört Azure Active Directory registreringen:

1. Välj knappen **auktorisera** på din Swagger-sida.

    [![att välja knappen Swagger-auktorisera](media/how-to-use-swagger/swagger-select-authorize-btn.png)](media/how-to-use-swagger/swagger-select-authorize-btn.png#lightbox)

1. Klistra in program-ID: t i fältet **client_id** .

    [![Swagger client_id fält](media/how-to-use-swagger/swagger-auth-form.png)](media/how-to-use-swagger/swagger-auth-form.png#lightbox)

1. Du omdirigeras sedan till följande lyckade spärred.

    [![Swagger Redirect modal](media/how-to-use-swagger/swagger-auth-redirect-img.png)](media/how-to-use-swagger/swagger-auth-redirect-img.png#lightbox)

Läs mer om interaktivt Est-begäranden som skyddas av OAuth 2,0 i den [officiella dokumentationen](https://swagger.io/docs/specification/authentication/oauth2/).

## <a name="next-steps"></a>Nästa steg

- Om du vill läsa mer om Azure Digitals dubbla objekt modeller och spatial Intelligence-diagrammet läser du [förstå Azure Digitals objekt modeller](./concepts-objectmodel-spatialgraph.md).

- Läs [autentisera med API: er](./security-authenticating-apis.md)för att lära dig hur du autentiserar med ditt hanterings-API.