---
title: Skapa och registrera SOAP - kopplingar i Azure Logic Apps | Microsoft Docs
description: "Konfigurera SOAP-anslutningsappar för användning i Azure Logic Apps"
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
ms.date: 10/24/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 031762e5639fc52e0b0a6a5bf8d12db25da25e12
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/03/2018
---
# <a name="create-and-register-soap-connectors-in-azure-logic-apps"></a>Skapa och registrera SOAP-kopplingar i Azure Logic Apps

Om du vill integrera SOAP-tjänster i dina logikapparbetsflöden kan du skapa och registrera en anpassad SOAP-anslutningsapp (Simple Object Access Protocol) genom att använda WSDL (Web Services Description Language) som beskriver din SOAP-tjänst. SOAP-anslutningsappar fungerar som färdiga anslutningsappar, så du kan använda dem på samma sätt som andra anslutningsappar i dina logikappar.


## <a name="prerequisites"></a>Förutsättningar

Om du vill registrera SOAP-anslutningsappen behöver du följande objekt:

* En Azure-prenumeration. Om du inte har en prenumeration kan du [börja med ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). Annars kan du registrera dig för en [prenumeration enligt principen Betala per användning](https://azure.microsoft.com/pricing/purchase-options/).

* Valfritt objekt här:
  * En URL till ett WSDL som definierar din SOAP-tjänst och API:erna
  * En WSDL-fil som definierar din SOAP-tjänst och API:erna

  För den här självstudiekursen kan du använda vårt exempel [SOAP-tjänsten Orders](http://fazioapisoap.azurewebsites.net/FazioService.svc?singleWsdl).

* Valfritt: En bild att använda som ikon för din anpassade anslutningsapp


## <a name="1-create-your-connector"></a>1. Skapa din anslutningsapp

1. Välj **Nytt** i Azure Portal, på Azure-huvudmenyn. Skriv ”logic apps connector” i sökrutan som filter och tryck på Retur.

   ![Ny, sök efter ”logic apps connector”](./media/logic-apps-soap-connector-create-register/create-logic-apps-connector.png)

2. I resultatlistorna väljer du **Logic Apps Custom Connector** > **Skapa**.

   ![Skapa Logic Apps-anslutningsapp](./media/logic-apps-soap-connector-create-register/choose-logic-apps-connector.png)

3. Ange information för att registrera anslutningsappen, enligt beskrivningen i tabellen. När du är klar väljer du **Fäst på instrumentpanelen** > **Skapa**.

   ![Information om anpassad Logic App-anslutningsapp](./media/logic-apps-soap-connector-create-register/logic-apps-soap-connector-details.png)

   | Egenskap | Föreslaget värde | Beskrivning | 
   | -------- | --------------- | ----------- | 
   | **Namn** | *soap-anslutningsnamn* | Ange ett namn på anslutningsappen. | 
   | **Prenumeration** | *Azure-prenumerationsnamn* | Välj din Azure-prenumeration. | 
   | **Resursgrupp** | *Azure-resursgruppsnamn* | Skapa eller välj en Azure-grupp för att ordna dina Azure-resurser. | 
   | **Plats** | *distributionsregion* | Välj en distributionsregion för din anslutningsapp. | 
   |||| 

   När Azure har distribuerat din anslutningsapp öppnas menyn för Logic Apps-anslutningsappar automatiskt. 
   Om inte väljer du din soap-anslutningsapp från Azure-instrumentpanelen.

## <a name="2-define-your-connector"></a>2. Definiera din anslutningsapp

Ange nu WSDL-filen eller URL:en för att skapa anslutningsappen, autentiseringen som anslutningsappen använder och de åtgärder och utlösare som soap-anslutningsappen ger


### <a name="2a-specify-the-wsdl-file-or-url-for-your-connector"></a>2a. Ange WSDL-filen eller URL:en för din anslutningsapp

1. På anslutningsappens meny väljer du **Logic Apps-anslutningsapp**, om det inte redan har valts. Välj **Redigera** i verktygsfältet.

   ![Redigera anpassad koppling](./media/logic-apps-soap-connector-create-register/edit-soap-connector.png)

2. Välj **Allmänt** så att du kan ange informationen i dessa tabeller för att skapa, säkra och definiera åtgärderna och utlösarna för SOAP-anslutningsappen.

   1. För **Anpassade anslutningsprogram** väljer du **SOAP** som **API-slutpunkt** så att du kan ange WSDL-filen som beskriver ditt API.

      ![Ange WSDL-filen för ditt API](./media/logic-apps-soap-connector-create-register/provide-wsdl-file.png)

      | Alternativ | Format |Beskrivning | 
      | ------ | ------ | ----------- | 
      | **Ladda upp WSDL från fil** | *WSDL-fil* | Bläddra till platsen för WSDL-filen och välj filen. | 
      | **Ladda upp WSDL från URL** | http://*sökväg-till-wsdl-fil* | Ange URL:en för tjänstens WSDL-fil. | 
      | **SOAP till REST** |   | Transformera API:er i SOAP-tjänst till REST-API:er. | 
      |||| 

   2. För **Allmän information** laddar du upp en ikon för anslutningsappen. 
   Normalt fylls fälten **Beskrivning**, **Värd** och **Grundläggande URL** automatiskt i från din WSDL-fil. 
   Men om de inte fylls i lägger du till informationen enligt beskrivningen i tabellen och väljer **Fortsätt**. 

      ![Anslutningsinformation](./media/logic-apps-soap-connector-create-register/add-general-details.png)

      | Alternativ eller inställning | Format | Beskrivning | 
      | ----------------- | ------ | ----------- | 
      | **Ladda upp ikon** | *png-eller-jpg-fil-under-1-MB* | En ikon som representerar anslutningsappen <p>Färg: En vit logotyp mot en färgad bakgrund rekommenderas. <p>Mått: Logotyp på ~160 pixlar inuti en fyrkant på 230 pixlar | 
      | **Bakgrundsfärg för ikonen** | *hexadecimal-kod-för-ikonvarumärkesfärg* | <p>Färgen bakom ikonen som matchar bakgrundsfärgen i ikonfilen. <p>Format: hexadecimalt. Till exempel representerar #007ee5 blått. | 
      | **Beskrivning** | *beskrivning-av-anslutningsapp* | Ange en kort beskrivning av din anslutningsapp. | 
      | **Värd** | *anslutningsappens-värd* | Ange värddomänen för din SOAP-tjänst. | 
      | **Grundläggande URL** | *anslutningsappens-grundläggande-URL* | Ange den grundläggande URL:en för din SOAP-tjänst. | 
      |||| 

### <a name="2b-describe-the-authentication-that-your-connector-uses"></a>2b. Beskriv autentiseringen som anslutningsappen använder

1. Välj nu **Säkerhet** så att du kan granska eller beskriva autentiseringen som anslutningsappen använder. Autentiseringen ser till att dina användares identiteter flödar korrekt mellan tjänsten och klienter.

   Som standard är anslutningsappens **Autentiseringstyp** inställd på **Ingen autentisering**.
   
   ![Autentiseringstyp](./media/logic-apps-soap-connector-create-register/security-authentication-options.png)

   Välj **Redigera** om du vill ändra autentiseringstyp. Du kan välja **Grundläggande autentisering**. Om du vill använda andra parameteretiketter än standardvärden uppdaterar du dem under **Parameteretikett**.

   ![Grundläggande autentisering](./media/logic-apps-soap-connector-create-register/security.png)

   
2. Om du vill spara din anslutningsapp när du har angett säkerhetsinformationen väljer du **Uppdatera anslutningsprogrammet** högst upp på sidan. Välj sedan **Fortsätt**. 

### <a name="2c-review-update-or-define-actions-and-triggers-for-your-connector"></a>2c. Granska, uppdatera eller definiera åtgärder och utlösare för din anslutningsapp

1. Välj nu **Definition** så att du kan granska, redigera eller definiera nya åtgärder och utlösare som användare kan lägga till i sina arbetsflöden.

   Åtgärder och utlösare baseras på åtgärderna som definieras i din WSDL-fil, som automatiskt fyller i sidan **Definition** och inkludera begäran- och svarsvärden. Så om de värden som krävs redan visas här kan du gå till nästa steg i registreringsprocessen utan att göra ändringar på den här sidan.

   ![Anslutningsappens definition](./media/logic-apps-soap-connector-create-register/definition.png)

2. Du kan också, om du vill redigera befintliga åtgärder och utlösare eller lägga till nya, [fortsätta med de här stegen](logic-apps-custom-connector-register.md#add-action-or-trigger).


## <a name="3-finish-creating-your-connector"></a>3. Slutför att skapa din anslutningsapp

När du är klar väljer du **Uppdatera anslutningsprogrammet** så att du kan distribuera anslutningsappen. 

Grattis! När du nu skapar en logikapp hittar du din anslutningsapp i Logikappdesigner och kan lägga till anslutningsappen i logikappen.

![Hitta anslutningsappen i Logikappdesigner](./media/logic-apps-soap-connector-create-register/soap-connector-created.png)

## <a name="share-your-connector-with-other-logic-apps-users"></a>Dela din anslutningsapp med andra Logic Apps-användare

Registrerade men ocertifierade anpassade anslutningsappar fungerar som Microsoft-hanterade anslutningsappar men är *bara* synlig och tillgänglig för anslutningsappens skapare och användare som har samma Azure Active Directory-klient och Azure-prenumeration för logikappar i den region där de apparna har distribuerats. Även om delning är valfri finns vissa scenarier där du vill dela dina anslutningsappar med andra användare. 

> [!IMPORTANT]
> Om du delar en anslutningsapp kan andra börja förlita sig på den anslutningsappen. 
> ***Om du tar bort anslutningsappen tar du bort alla anslutningar till den anslutningsappen.***
 
Dela din anslutningstjänst med externa användare utanför dessa gränser, till exempel med alla Logic Apps användare [skicka din connector för Microsoft-certifiering](../logic-apps/custom-connector-submit-certification.md).

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

**F:** Är SOAP-anslutningsappen allmänt tillgänglig? </br>
**S:** SOAP-anslutningsappen är en **förhandsversion** och är inte allmänt tillgänglig just nu.

**F:** Finns det några begränsningar och kända problem för SOAP-anslutningsappen? </br>
**S:** Ja, se [begränsningar och kända problem för SOAP-anslutningsappar](../api-management/api-management-api-import-restrictions.md#wsdl).

**F:** Finns det några begränsningar för anpassade anslutningsappar? </br>
**S:** Ja, se [begränsningarna för anpassade anslutningsappar här](../logic-apps/logic-apps-limits-and-config.md#custom-connector-limits).

## <a name="get-support"></a>Få support

* Kontakta [condevhelp@microsoft.com](mailto:condevhelp@microsoft.com) för support med utveckling och registrering eller om du vill begära funktioner som inte är tillgängliga i registreringsguiden. Microsoft bevakar det här kontot för utvecklarfrågor och -problem och vidarebefordrar dem till lämpligt team.

* I [Azure Logic Apps-forumet](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) kan du ställa frågor, besvara frågor eller se vad andra Azure Logic Apps-användare håller på med.

* På [webbplatsen för Logic Apps-användarfeedback](http://aka.ms/logicapps-wish) kan du hjälpa till med att förbättra Logic Apps genom att rösta på förslag eller komma med egna förslag på förbättringar. 

## <a name="next-steps"></a>Nästa steg

* [Valfritt: Certifiera din koppling](../logic-apps/custom-connector-submit-certification.md)
* [Vanliga frågor och svar för anpassade anslutningsappar](../logic-apps/custom-connector-faq.md)