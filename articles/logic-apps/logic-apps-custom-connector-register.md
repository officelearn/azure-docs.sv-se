---
title: Registrera anpassade kopplingar - Azure Logic Apps | Microsoft Docs
description: "Konfigurera anpassade anslutningar för användning i Logikappar i Azure"
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 9e3d88abe751b37700590cc68c458f208d5868d2
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2017
---
# <a name="register-custom-connectors-in-azure-logic-apps"></a>Registrera anpassade kopplingar i Azure Logic Apps

När du skapa REST-API, konfigurera autentisering och hämta definitionsfilen OpenAPI eller en Postman samling, är du redo att registrera din connector. 

## <a name="prerequisites"></a>Krav

Om du vill registrera din anpassade connector vad du behöver:

* Information för att registrera din anslutningstjänst i Azure, till exempel namn, Azure-prenumeration, Azure-resursgrupp och plats som du vill använda

* En OpenAPI (Swagger)-fil eller en samling för Postman som beskriver ditt API

  För den här kursen kan du använda den [Azure resurser Manager OpenAPI exempelfilen](http://pwrappssamples.blob.core.windows.net/samples/AzureResourceManager.json).

* En ikon som representerar din kontakt

* En kort beskrivning för din connector

* Var värd för ditt API

## <a name="1-create-your-connector"></a>1. Skapa en koppling

1. Välj i Azure-portalen på Azure huvudmenyn, **ny**. Ange ”logic apps connector” som filter i sökrutan och tryck på RETUR.

   ![Nya, söka efter ”logic apps connector”](./media/logic-apps-custom-connector-register/create-logic-apps-connector.png)

2. Välj resultatlistan **Logic Apps Connector** > **skapa**.

   ![Skapa Logic Apps-koppling](./media/logic-apps-custom-connector-register/choose-logic-apps-connector.png)

3. Ange information för att registrera din koppling som beskrivs i tabellen. När du är klar väljer **fäst på instrumentpanelen** > **skapa**.

   ![Information om logiska appar anpassad koppling](./media/logic-apps-custom-connector-register/logic-apps-connector-details.png)

   | Egenskap | Föreslaget värde | Beskrivning | 
   | -------- | --------------- | ----------- | 
   | **Namn** | *anpassad Kopplingsnamn* | Ange ett namn för din anslutningen. | 
   | **Prenumeration** | *Azure-prenumeration-name* | Välj din Azure-prenumeration. | 
   | **Resursgrupp** | *Azure-grupp-resursnamn* | Skapa eller välj en Azure-grupp för att ordna dina Azure-resurser. | 
   | **Plats** | *distribution region* | Välj en region för distribution i din koppling. | 
   |||| 

   När Azure distribuerar din koppling, öppnas anpassad koppling menyn automatiskt. 
   Om inte, Välj din anpassad koppling från Azure instrumentpanelen.

## <a name="2-define-your-connector"></a>2. Definiera din koppling

Nu ange OpenAPI filen eller Postman samling för att skapa din koppling, autentisering som använder din koppling, åtgärder och utlösare som tillhandahåller en anpassad koppling och parametrar som kan använda för åtgärder och utlösare.

### <a name="2a-specify-the-openapi-file-or-postman-collection-for-your-connector"></a>2a. Ange OpenAPI filen eller Postman samling för din connector

1. Om inte redan har markerats i din connector-menyn, välja **Logic Apps Connector**. I verktygsfältet väljer **redigera**.

   ![Redigera anpassad koppling](./media/logic-apps-custom-connector-register/edit-custom-connector.png)

2. Välj **allmänna** så att du kan ange information i dessa tabeller för att skapa, skydda och definiera åtgärder och utlösare för en anpassad koppling.

   1. För **anpassade kopplingar**, Välj ett alternativ så att du kan ange OpenAPI (Swagger)-fil som beskriver ditt API.

      ![Ange filen OpenAPI för ditt API](./media/logic-apps-custom-connector-register/provide-openapi-file.png)

      | Alternativ | Format |Beskrivning | 
      | ------ | ------ | ----------- | 
      | **Överföra en fil med OpenAPI** | *OpenAPI (Swagger) - json - fil* | Bläddra till platsen för filen OpenAPI och markera filen. | 
      | **Använda en OpenAPI URL** | http://*sökväg till swagger-json-fil* | Ange en URL för din API-OpenAPI-fil. | 
      | **Överför Postman samling V1** | *exporterade Postman-samling-V1-fil* | Bläddra till platsen för en exporterad Postman samling i V1-format. | 
      |||| 

   2. För **allmän information**, överför en ikon för din anslutningen. 
   Normalt den **beskrivning**, **värden**, och **bas-URL** fält fylls automatiskt i från OpenAPI-filen. 
   Men om de inte lägga till den här informationen som beskrivs i tabellen och välj **Fortsätt**. 

      ![Information om koppling](./media/logic-apps-custom-connector-register/add-connector-details.png)

      | Alternativ eller -inställning | Format | Beskrivning | 
      | ----------------- | ------ | ----------- | 
      | **Överför ikon** | *PNG-or-jpg-File-under-1-MB* | En ikon som representerar din kontakt <p>Färg: Helst en vit logotyp mot en bakgrundsfärg. <p>Mått: En ~ 160 pixel logotyp i en ruta 230 bildpunkt | 
      | **Ikon för bakgrundsfärgen** | *ikonen varumärken-färg-hexadecimalt-kod* | <p>Färgen bakom en ikon som matchar bakgrundsfärgen som används i din fil. <p>Format: hexadecimalt. Till exempel representerar #007ee5 blå färg. | 
      | **Beskrivning** | *Beskrivning av koppling* | Ange en kort beskrivning för din anslutningen. | 
      | **Värden** | *Connector-värd* | Ange värddomänen för webb-API. | 
      | **Bas-URL** | *Connector-bas-URL* | Ange en bas-URL för Web API. | 
      |||| 

### <a name="2b-describe-the-authentication-that-your-connector-uses"></a>2b. Beskriv den autentisering som använder din koppling

1. Nu välja **säkerhet** så att du kan granska eller beskriver den autentisering som använder din koppling. Autentisering ser till att användarnas identiteter korrekt flöda mellan din tjänst och klienter.

   ![Autentiseringstyp](./media/logic-apps-custom-connector-register/security.png)

   * Om du överför en fil OpenAPI Registreringsguiden automatiskt identifierar den autentiseringstyp som används för webb-API och fyller automatiskt i **säkerhet** avsnitt i guiden, baserat på den `securityDefinitions` objekt i som filen. Här är till exempel ett avsnitt som anger med OAuth2.0:

     ``` json
     "securityDefinitions": {
       "AAD": {
       "type": "oauth2",
       "flow": "accessCode",
       "authorizationUrl": "https://login.windows.net/common/oauth2/authorize",
       "tokenUrl": "https://login.windows.net/common/oauth2/token",
       "scopes": {}
       }
     },
     ```

   * Om filen OpenAPI inte fyller autentiseringstyp och egenskaper, Välj **redigera** så att du kan lägga till den här informationen. 
   
     Till exempel om du tidigare [konfigurerar Azure AD-autentisering i den här självstudiekursen](../logic-apps/custom-connector-azure-active-directory-authentication.md), du skapade Azure AD-appar för att skydda dina webb-API och din koppling. 
     Du kan nu ange program-ID och klienten nyckeln som du sparat tidigare:
    
     | Inställning | Föreslaget värde | Beskrivning | 
     | ------- | --------------- | ----------- | 
     | **Autentiseringstyp** | OAuth 2.0 | | 
     | **Identitetsleverantören.** | Azure Active Directory | | 
     | **Klient-id** | *application-ID-for-connector-Azure-AD-app* | Program-ID som du sparat tidigare för din anslutningstjänst Azure AD-appar | 
     | **Klienthemlighet** | *client-key-for-connector-Azure-AD-app* | Klientnyckel för Azure AD-appar för din koppling | 
     | **Inloggnings-URL** | `https://login.windows.net` | | 
     | **Resurs-URL** | `https://management.core.windows.net/` | Kontrollera att du lägger till URL: en exakt som det anges, inklusive avslutande snedstreck. | 
     |||| 

   * En samling Postman som genererar automatiskt en OpenAPI-fil för dig, fyller automatiskt autentiseringstypen *endast* när du använder stöds autentiseringstyper som OAuth 2.0- eller Basic.

2. Om du vill spara din anslutningstjänst när du har angett säkerhetsinformation överst på sidan Välj **uppdatera anslutningen**, Välj **Fortsätt**. 

### <a name="2c-review-update-or-define-actions-and-triggers-for-your-connector"></a>2c. Granska, uppdatera och definiera åtgärder och utlösare för din connector

1. Nu välja **Definition** så att du kan granska, redigera eller definiera nya åtgärder och utlösare som användare kan lägga till sina arbetsflöden.

   Åtgärder och utlösare baseras på de åtgärder som definierats i din OpenAPI fil eller Postman samling, som automatiskt fylla i **Definition** sidan och innehåller värden för förfrågan och svar. Så om nödvändiga åtgärder redan visas här kan du gå till nästa steg i registreringen utan att göra ändringar på den här sidan.

   ![Definition av koppling](./media/logic-apps-custom-connector-register/definition.png)

2. Du kan också om du vill redigera befintliga åtgärder och utlösare eller lägga till nya fortsätta med de här stegen.

<a name="add-action-or-trigger"></a> 

#### <a name="edit-or-add-actions-for-your-connector"></a>Redigera eller lägga till åtgärder för din koppling

1. Om du vill redigera en befintlig åtgärd väljer du hur många för åtgärden. Att lägga till en åtgärd som inte finns i filen OpenAPI eller Postman samling under **åtgärder**, Välj **ny åtgärd**.

2. Under **allmänna**, ange eller redigera den här informationen för åtgärden:
   
   | Inställning | Föreslaget värde | Beskrivning | 
   | ------- | --------------- | ----------- | 
   | **Sammanfattning** | *Åtgärdens namn* | Rubriken för den här åtgärden | 
   | **Beskrivning** | *Beskrivning av åtgärden* | Beskrivning för den här åtgärden. <p>**Tips**: se till att beskrivningen slutar med en punkt. |
   | **Åtgärds-ID** | *åtgärden-ID* | Ett unikt namn för att identifiera den här åtgärden. Användningsfall slitbanor, till exempel: ”GetPullRequest” | 
   |**Synlighet**| **ingen**, **avancerade**, **interna**, eller **viktigt** | Användarinriktad synlighet för den här åtgärden. Mer information finns i [OpenAPI tillägg](../logic-apps/custom-connector-openapi-extensions.md#visibility). | 
   |||| 

3. Nu ska du ange begäran för åtgärden.
  
   1. I den **begära** väljer **Import från exemplet**. 

   2. På den **Import från exemplet** och klistra in en exempel-begäran. 

      Vanligtvis exempel begäranden är tillgängliga i API-dokumentationen där du kan få information om den **Verb**, **URL**, **huvuden**, och **brödtext**fält. Se exempelvis den [Text Analytics API-dokumentationen](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7).

      ![Importera exempelbegäran](./media/logic-apps-custom-connector-register/import-sample-operation-request.png)

      > [!IMPORTANT]
      > Om du skapar en koppling från en Postman samling, kontrollera att du tar bort den `Content-type` huvudet från åtgärder och utlösare. Logic Apps lägger automatiskt till det här sidhuvudet. Ta också bort autentiseringshuvuden som du definierade i den `Security` avsnittet från åtgärder och utlösare.

      Avancerade funktioner för OpenAPI, se [OpenAPI tillägg för anpassade kopplingar](../logic-apps/custom-connector-openapi-extensions.md).

   3. För att slutföra begäran definition väljer **importera**.

4. Nu ska du ange svar för åtgärden.

   1. Under **svar**, du kan ange ett standardsvar. 
   Välj **lägga till standardtext**.

   2. På den **Import från exemplet** sidan, klistra in en exempelsvar och välj sedan **importera**.

5. Slutligen under **validering**, granska och åtgärda alla eventuella problem som har identifierats för åtgärden.

#### <a name="edit-or-add-triggers-for-your-connector"></a>Ändra eller lägga till utlösare för din connector

1. Om du vill redigera en befintlig utlösare, väljer du hur många för denna utlösare. Att lägga till en utlösare som inte finns i filen OpenAPI eller Postman samling under **utlösare**, Välj **ny utlösare**.

2. Under **allmänna**, ange eller redigera den här informationen för utlösaren:

   | Inställning | Föreslaget värde | Beskrivning | 
   | ------- | --------------- | ----------- | 
   | **Sammanfattning** | *Åtgärdens namn* | Rubriken för den här utlösaren | 
   | **Beskrivning** | *Beskrivning av åtgärden* | Beskrivning för denna utlösare. <p>**Tips**: se till att beskrivningen slutar med en punkt. | 
   | **Åtgärds-ID** | *åtgärden-ID* | Ett unikt namn för att identifiera den här utlösaren. Användningsfall slitbanor, till exempel: ”TriggerOnGitHubPushEvent” | 
   |**Synlighet**| **ingen**, **avancerade**, **interna**, eller **viktigt** | Användarinriktad synlighet för denna utlösare. Mer information finns i [OpenAPI tillägg](../logic-apps/custom-connector-openapi-extensions.md#visibility). | 
   | **Typ av utlösare** | **Webhook** eller **avsökning** | Typen för denna utlösare. Till exempel väntar en webhook-utlösare på en viss händelse inträffar innan att. En utlösare för avsökning kontrollerar regelbundet en tjänstslutpunkt baserat på ett visst intervall och frekvens. <p>Mer information om webhook och avsökning utlösaren mönster finns [skapa anpassade API: er](../logic-apps/logic-apps-create-api-app.md). | 
   |||| 

3. Nu ska du ange den begäran som skapar utlösaren. 

   1. I den **begära** väljer **Import från exemplet**.

   2. På den **Import från exemplet** och klistra in en exempel-begäran. 

      Vanligtvis exempel begäranden är tillgängliga i API-dokumentationen där du kan få information om den **Verb**, **URL**, **huvuden**, och **brödtext**fält. Se exempelvis den [Text Analytics API-dokumentationen](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7).

      ![Importera exempelbegäran](./media/logic-apps-custom-connector-register/import-sample-operation-request.png)

      > [!IMPORTANT]
      > Om du skapar en koppling från en Postman samling, kontrollera att du tar bort den `Content-type` huvudet från åtgärder och utlösare. Logic Apps lägger automatiskt till det här sidhuvudet. Ta också bort autentiseringshuvuden som du definierade i den `Security` avsnittet från åtgärder och utlösare.

      Avancerade funktioner för OpenAPI, se [OpenAPI tillägg för anpassade kopplingar](../logic-apps/custom-connector-openapi-extensions.md).

   3. För att slutföra begäran definition väljer **importera**. 

4. Nu ska du ange utlösarens svar. I den **svar** avsnitt, baserat på utlösartypen, gör du följande:

   **Webhook-utlösare**
   1. I **Webhook svar**, Välj **Import från exemplet**. 

   2. På den **Import från exemplet** sidan, klistra in en exempelsvar och välj sedan **importera**. Ett exempelsvar finns i [GitHub API-referens för att skapa en webhook](https://developer.github.com/v3/repos/hooks/#create-a-hook).

   3. Under **konfiguration av utlösare**, Välj en parameter som ska användas för webhook-förfrågan. Logic Apps använder det här parametervärdet för att fylla återanrop-URL som används av tjänsten för att kommunicera med utlösaren.

   **Avsökningen utlösare**
   1. Under **svar**, du kan ange ett standardsvar. 
   Välj **lägga till standardtext**.

   2. På den **Import från exemplet** sidan, klistra in en exempelsvar och välj sedan **importera**.

   3. Under **konfiguration av utlösare**, ange Frågeparametern värdet för att skicka för parametern, och en *utlösaren tipset* som anger en korrekt avsökningsintervallet för nästa begäran.

5. Slutligen under **validering**, granska och åtgärda alla eventuella problem som har identifierats för utlösaren.

#### <a name="review-reference-definitions-for-your-connector"></a>Granska referens definitioner för din connector

Den **referenser** avsnittet automatiskt fyller från din API-beskrivning och beskriver alla fält som kan referera till åtgärder och utlösare. 

1. Under **referenser**, väljer du hur många för definitionen referens som du vill granska.

2. Under **namn**, granska eller uppdatera definition referensnamn.

3. Under **validering**, granska och korrigera alla potentiella problem som har identifierats för referens-definition.

## <a name="3-finish-creating-your-connector"></a>3. Slutför för att skapa din anslutning

När du är klar kan du välja **skapa** så att du kan distribuera din koppling. Om du uppdaterar en befintlig anslutning, Välj **uppdatera anslutningen**.

Grattis! Nu när du skapar en logikapp, kan du hitta din anslutningstjänst i Logic Apps Designer och lägga till kopplingen i din logikapp.

![Hitta din anslutningstjänst i Logic Apps Designer](./media/logic-apps-custom-connector-register/custom-connector-created.png)

## <a name="share-your-connector-with-other-logic-apps-users"></a>Dela din kontakt med andra användare med Logic Apps

Registrerad men ocertifierade anpassade kopplingar fungerar som Microsoft-hanterade anslutningar, men är synliga och tillgängliga *endast* kopplingens författare och användare som har samma Azure Active Directory-klient och Azure-prenumeration för logikappar i den region där de apparna som har distribuerats. Delning är valfri, kanske scenarier där du vill dela dina kontakter med andra användare. 

> [!IMPORTANT]
> Om du delar en koppling, kan andra börja beror på kopplingen. 
> ***Ta bort din anslutning tar bort alla anslutningar till kopplingen.***
 
Dela din anslutningstjänst med externa användare utanför dessa gränser, till exempel med alla Logic Apps flödet och PowerApps användare [skicka din connector för Microsoft-certifiering](../logic-apps/custom-connector-submit-certification.md).

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

**F:** finns det några gränser för anpassade kopplingar? </br>
**S:** Ja, finns det [anpassad koppling begränsar här](../logic-apps/logic-apps-limits-and-config.md#custom-connector-limits).

## <a name="get-support"></a>Få support

* Kontakta för support med utveckling och onboarding eller funktioner som inte är tillgängliga i Registreringsguiden för [ condevhelp@microsoft.com ](mailto:condevhelp@microsoft.com). Microsoft övervakar det här kontot för utvecklare frågor och problem och skickar dem till lämpliga teamet.

* Om du vill fråga eller besvara frågor, eller se vad andra användare i Azure Logic Apps gör finns i [Logikappar i Azure-forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* För att förbättra Logic Apps, rösta eller skicka in förslag på den [Logic Apps användaren feedbackwebbplats](http://aka.ms/logicapps-wish). 

## <a name="next-steps"></a>Nästa steg

* [Valfritt: Certifiera din koppling](../logic-apps/custom-connector-submit-certification.md)
* [Anpassad koppling vanliga frågor och svar](../logic-apps/custom-connector-faq.md)