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
ms.openlocfilehash: 722b82aabe796367d906e9338355b83a1a8b1e1c
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2018
---
# <a name="register-custom-connectors-in-azure-logic-apps"></a>Registrera anpassade kopplingar i Azure Logic Apps

När du skapa REST-API, konfigurera autentisering och hämta definitionsfilen OpenAPI eller en Postman samling, är du redo att registrera din connector. 

## <a name="prerequisites"></a>Förutsättningar

Om du vill registrera din anpassade connector vad du behöver:

* Information för att registrera din anslutningstjänst i Azure, till exempel namn, Azure-prenumeration, Azure-resursgrupp och plats som du vill använda

* En OpenAPI (Swagger)-fil eller en samling för Postman som beskriver ditt API

  För den här kursen kan du använda den [Azure resurser Manager OpenAPI exempelfilen](http://pwrappssamples.blob.core.windows.net/samples/AzureResourceManager.json).

* En ikon som representerar anslutningsappen

* En kort beskrivning för din connector

* Var värd för ditt API

## <a name="1-create-your-connector"></a>1. Skapa din anslutningsapp

1. Välj i Azure-portalen på Azure huvudmenyn, **skapar du en resurs**. Skriv ”logic apps connector” i sökrutan som filter och tryck på Retur.

   ![Ny, sök efter ”logic apps connector”](./media/logic-apps-custom-connector-register/create-logic-apps-connector.png)

2. I resultatlistorna väljer du **Logic Apps Custom Connector** > **Skapa**.

   ![Skapa Logic Apps-anslutningsapp](./media/logic-apps-custom-connector-register/choose-logic-apps-connector.png)

3. Ange information för att registrera anslutningsappen, enligt beskrivningen i tabellen. När du är klar väljer du **Fäst på instrumentpanelen** > **Skapa**.

   ![Information om anpassad Logic App-anslutningsapp](./media/logic-apps-custom-connector-register/logic-apps-connector-details.png)

   | Egenskap | Föreslaget värde | Beskrivning | 
   | -------- | --------------- | ----------- | 
   | **Namn** | *namn-på-anpassad-anslutningsapp* | Ange ett namn på anslutningsappen. | 
   | **Prenumeration** | *Azure-prenumerationsnamn* | Välj din Azure-prenumeration. | 
   | **Resursgrupp** | *Azure-resursgruppsnamn* | Skapa eller välj en Azure-grupp för att ordna dina Azure-resurser. | 
   | **Plats** | *distributionsregion* | Välj en distributionsregion för din anslutningsapp. | 
   |||| 

   När Azure har distribuerat din anslutningsapp öppnas menyn för anpassade anslutningsappar automatiskt. 
   Om inte väljer du din anpassade anslutningsapp från Azure-instrumentpanelen.

## <a name="2-define-your-connector"></a>2. Definiera din anslutningsapp

Nu ange OpenAPI filen eller Postman samling för att skapa din koppling, autentisering som använder din koppling, åtgärder och utlösare som tillhandahåller en anpassad koppling och parametrar som kan använda för åtgärder och utlösare.

### <a name="2a-specify-the-openapi-file-or-postman-collection-for-your-connector"></a>2a. Ange OpenAPI filen eller Postman samling för din connector

1. På anslutningsappens meny väljer du **Logic Apps-anslutningsapp**, om det inte redan har valts. Välj **Redigera** i verktygsfältet.

   ![Redigera anpassad koppling](./media/logic-apps-custom-connector-register/edit-custom-connector.png)

2. Välj **allmänna** så att du kan ange information i dessa tabeller för att skapa, skydda och definiera åtgärder och utlösare för en anpassad koppling.

   1. För **anpassade kopplingar**, Välj ett alternativ så att du kan ange OpenAPI (Swagger)-fil som beskriver ditt API.

      ![Ange filen OpenAPI för ditt API](./media/logic-apps-custom-connector-register/provide-openapi-file.png)

      | Alternativ | Format |Beskrivning | 
      | ------ | ------ | ----------- | 
      | **Överföra en fil med OpenAPI** | *OpenAPI (Swagger)-json-file* | Bläddra till platsen för filen OpenAPI och markera filen. | 
      | **Använda en OpenAPI URL** | http://*path-to-swagger-json-file* | Ange en URL för din API-OpenAPI-fil. | 
      | **Överför Postman samling V1** | *exported-Postman-collection-V1-file* | Bläddra till platsen för en exporterad Postman samling i V1-format. | 
      |||| 

   2. För **Allmän information** laddar du upp en ikon för anslutningsappen. 
   Normalt den **beskrivning**, **värden**, och **bas-URL** fält fylls automatiskt i från OpenAPI-filen. 
   Men om de inte fylls i lägger du till informationen enligt beskrivningen i tabellen och väljer **Fortsätt**. 

      ![Anslutningsinformation](./media/logic-apps-custom-connector-register/add-connector-details.png)

      | Alternativ eller inställning | Format | Beskrivning | 
      | ----------------- | ------ | ----------- | 
      | **Ladda upp ikon** | *png-eller-jpg-fil-under-1-MB* | En ikon som representerar anslutningsappen <p>Färg: En vit logotyp mot en färgad bakgrund rekommenderas. <p>Mått: Logotyp på ~160 pixlar inuti en fyrkant på 230 pixlar | 
      | **Bakgrundsfärg för ikonen** | *hexadecimal-kod-för-ikonvarumärkesfärg* | <p>Färgen bakom ikonen som matchar bakgrundsfärgen i ikonfilen. <p>Format: hexadecimalt. Till exempel representerar #007ee5 blått. | 
      | **Beskrivning** | *beskrivning-av-anslutningsapp* | Ange en kort beskrivning av din anslutningsapp. | 
      | **Värd** | *anslutningsappens-värd* | Ange värddomänen för webb-API. | 
      | **Grundläggande URL** | *anslutningsappens-grundläggande-URL* | Ange en bas-URL för Web API. | 
      |||| 

### <a name="2b-describe-the-authentication-that-your-connector-uses"></a>2b. Beskriv autentiseringen som anslutningsappen använder

1. Välj nu **Säkerhet** så att du kan granska eller beskriva autentiseringen som anslutningsappen använder. Autentiseringen ser till att dina användares identiteter flödar korrekt mellan tjänsten och klienter.

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
     | **klient-id** | *application-ID-for-connector-Azure-AD-app* | Program-ID som du sparat tidigare för din anslutningstjänst Azure AD-appar | 
     | **Klienthemlighet** | *client-key-for-connector-Azure-AD-app* | Klientnyckel för Azure AD-appar för din koppling | 
     | **Inloggnings-URL** | `https://login.windows.net` | | 
     | **Resurs-URL** | `https://management.core.windows.net/` | Kontrollera att du lägger till URL: en exakt som det anges, inklusive avslutande snedstreck. | 
     |||| 

   * En samling Postman som genererar automatiskt en OpenAPI-fil för dig, fyller automatiskt autentiseringstypen *endast* när du använder stöds autentiseringstyper som OAuth 2.0- eller Basic.

2. Om du vill spara din anslutningsapp när du har angett säkerhetsinformationen väljer du **Uppdatera anslutningsprogrammet** högst upp på sidan. Välj sedan **Fortsätt**. 

### <a name="2c-review-update-or-define-actions-and-triggers-for-your-connector"></a>2c. Granska, uppdatera eller definiera åtgärder och utlösare för din anslutningsapp

1. Välj nu **Definition** så att du kan granska, redigera eller definiera nya åtgärder och utlösare som användare kan lägga till i sina arbetsflöden.

   Åtgärder och utlösare baseras på de åtgärder som definierats i din OpenAPI fil eller Postman samling, som automatiskt fylla i **Definition** sidan och innehåller värden för förfrågan och svar. Så om de värden som krävs redan visas här kan du gå till nästa steg i registreringsprocessen utan att göra ändringar på den här sidan.

   ![Anslutningsappens definition](./media/logic-apps-custom-connector-register/definition.png)

2. Du kan också om du vill redigera befintliga åtgärder och utlösare eller lägga till nya fortsätta med de här stegen.

<a name="add-action-or-trigger"></a> 

#### <a name="edit-or-add-actions-for-your-connector"></a>Redigera eller lägga till åtgärder för din koppling

1. Om du vill redigera en befintlig åtgärd väljer du hur många för åtgärden. Att lägga till en åtgärd som inte finns i filen OpenAPI eller Postman samling under **åtgärder**, Välj **ny åtgärd**.

2. Under **allmänna**, ange eller redigera den här informationen för åtgärden:
   
   | Inställning | Föreslaget värde | Beskrivning | 
   | ------- | --------------- | ----------- | 
   | **Sammanfattning** | *operation-name* | Rubriken för den här åtgärden | 
   | **Beskrivning** | *operation-description* | Beskrivning för den här åtgärden. <p>**Tips**: se till att beskrivningen slutar med en punkt. |
   | **Åtgärds-ID** | *operation-identifier* | Ett unikt namn för att identifiera den här åtgärden. Användningsfall slitbanor, till exempel: ”GetPullRequest” | 
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
   Välj **Lägg till standardsvar**.

   2. På den **Import från exemplet** sidan, klistra in en exempelsvar och välj sedan **importera**.

5. Slutligen under **validering**, granska och åtgärda alla eventuella problem som har identifierats för åtgärden.

#### <a name="edit-or-add-triggers-for-your-connector"></a>Ändra eller lägga till utlösare för din connector

1. Om du vill redigera en befintlig utlösare, väljer du hur många för denna utlösare. Att lägga till en utlösare som inte finns i filen OpenAPI eller Postman samling under **utlösare**, Välj **ny utlösare**.

2. Under **allmänna**, ange eller redigera den här informationen för utlösaren:

   | Inställning | Föreslaget värde | Beskrivning | 
   | ------- | --------------- | ----------- | 
   | **Sammanfattning** | *operation-name* | Rubriken för den här utlösaren | 
   | **Beskrivning** | *operation-description* | Beskrivning för denna utlösare. <p>**Tips**: se till att beskrivningen slutar med en punkt. | 
   | **Åtgärds-ID** | *operation-identifier* | Ett unikt namn för att identifiera den här utlösaren. Användningsfall slitbanor, till exempel: ”TriggerOnGitHubPushEvent” | 
   |**Synlighet**| **ingen**, **avancerade**, **interna**, eller **viktigt** | Användarinriktad synlighet för denna utlösare. Mer information finns i [OpenAPI tillägg](../logic-apps/custom-connector-openapi-extensions.md#visibility). | 
   | **Utlösartyp** | **Webhook** eller **avsökning** | Typen för denna utlösare. Till exempel väntar en webhook-utlösare på en viss händelse inträffar innan att. En utlösare för avsökning kontrollerar regelbundet en tjänstslutpunkt baserat på ett visst intervall och frekvens. <p>Mer information om webhook och avsökning utlösaren mönster finns [skapa anpassade API: er](../logic-apps/logic-apps-create-api-app.md). | 
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
   Välj **Lägg till standardsvar**.

   2. På den **Import från exemplet** sidan, klistra in en exempelsvar och välj sedan **importera**.

   3. Under **konfiguration av utlösare**, ange Frågeparametern värdet för att skicka för parametern, och en *utlösaren tipset* som anger en korrekt avsökningsintervallet för nästa begäran.

5. Slutligen under **validering**, granska och åtgärda alla eventuella problem som har identifierats för utlösaren.

#### <a name="review-reference-definitions-for-your-connector"></a>Granska referens definitioner för din connector

Den **referenser** avsnittet automatiskt fyller från din API-beskrivning och beskriver alla fält som kan referera till åtgärder och utlösare. 

1. Under **referenser**, väljer du hur många för definitionen referens som du vill granska.

2. Under **namn**, granska eller uppdatera definition referensnamn.

3. Under **validering**, granska och korrigera alla potentiella problem som har identifierats för referens-definition.

## <a name="3-finish-creating-your-connector"></a>3. Slutför att skapa din anslutningsapp

När du är klar kan du välja **skapa** så att du kan distribuera din koppling. Om du uppdaterar en befintlig anslutning, Välj **uppdatera anslutningen**.

Grattis! När du nu skapar en logikapp hittar du din anslutningsapp i Logikappdesigner och kan lägga till anslutningsappen i logikappen.

![Hitta anslutningsappen i Logikappdesigner](./media/logic-apps-custom-connector-register/custom-connector-created.png)

## <a name="share-your-connector-with-other-logic-apps-users"></a>Dela din anslutningsapp med andra Logic Apps-användare

Registrerade men ocertifierade anpassade anslutningsappar fungerar som Microsoft-hanterade anslutningsappar men är *bara* synlig och tillgänglig för anslutningsappens skapare och användare som har samma Azure Active Directory-klient och Azure-prenumeration för logikappar i den region där de apparna har distribuerats. Även om delning är valfri finns vissa scenarier där du vill dela dina anslutningsappar med andra användare. 

> [!IMPORTANT]
> Om du delar en anslutningsapp kan andra börja förlita sig på den anslutningsappen. 
> ***Om du tar bort anslutningsappen tar du bort alla anslutningar till den anslutningsappen.***
 
Dela din anslutningstjänst med externa användare utanför dessa gränser, till exempel med alla Logic Apps flödet och PowerApps användare [skicka din connector för Microsoft-certifiering](../logic-apps/custom-connector-submit-certification.md).

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

**F:** Finns det några begränsningar för anpassade anslutningsappar? </br>
**S:** Ja, se [begränsningarna för anpassade anslutningsappar här](../logic-apps/logic-apps-limits-and-config.md#custom-connector-limits).

## <a name="get-support"></a>Få support

* Kontakta [condevhelp@microsoft.com](mailto:condevhelp@microsoft.com) för support med utveckling och registrering eller om du vill begära funktioner som inte är tillgängliga i registreringsguiden. Microsoft bevakar det här kontot för utvecklarfrågor och -problem och vidarebefordrar dem till lämpligt team.

* I [Azure Logic Apps-forumet](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) kan du ställa frågor, besvara frågor eller se vad andra Azure Logic Apps-användare håller på med.

* På [webbplatsen för Logic Apps-användarfeedback](http://aka.ms/logicapps-wish) kan du hjälpa till med att förbättra Logic Apps genom att rösta på förslag eller komma med egna förslag på förbättringar. 

## <a name="next-steps"></a>Nästa steg

* [Valfritt: Certifiera din koppling](../logic-apps/custom-connector-submit-certification.md)
* [Vanliga frågor och svar för anpassade anslutningsappar](../logic-apps/custom-connector-faq.md)