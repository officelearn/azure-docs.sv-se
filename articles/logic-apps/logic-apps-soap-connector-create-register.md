---
title: Skapa och registrera SOAP - kopplingar i Azure Logic Apps | Microsoft Docs
description: "Ställ in SOAP-anslutningar för användning i Logikappar i Azure"
author: divyaswarnkar
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
ms.author: LADocs; divswa
ms.openlocfilehash: 0323b0f7ee03dce209d5a71c6711988a34ba7633
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2017
---
# <a name="create-and-register-soap-connectors-in-azure-logic-apps"></a>Skapa och registrera SOAP-kopplingar i Azure Logic Apps

Du kan skapa och registrera en anpassad SOAP Simple Object Access Protocol ()-koppling med hjälp av Web Services WSDL (Description Language) som beskriver SOAP-tjänsten om du vill integrera SOAP-tjänster i din app logik-arbetsflöden. SOAP-kopplingarna fungerar som färdiga kopplingar, så du kan använda dem på samma sätt som andra kopplingar i dina logic apps.


## <a name="prerequisites"></a>Krav

Om du vill registrera SOAP-koppling, vad du behöver:

* En Azure-prenumeration. Om du inte har en prenumeration kan du starta med en [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). Annars kan registrera dig för en [betala per användning prenumeration](https://azure.microsoft.com/pricing/purchase-options/).

* Här några objekt:
  * En URL till en WSDL som definierar SOAP-tjänsten och de API: er
  * Ett WSDL-fil som definierar SOAP-tjänsten och de API: er

  Den här kursen kan du använda våra exempel [order SOAP tjänsten](http://fazioapisoap.azurewebsites.net/FazioService.svc?singleWsdl).

* Valfritt: En bild som ska användas som en ikon för din anpassade connector


## <a name="1-create-your-connector"></a>1. Skapa en koppling

1. Välj i Azure-portalen på Azure huvudmenyn, **ny**. Ange ”logic apps connector” som filter i sökrutan och tryck på RETUR.

   ![Nya, söka efter ”logic apps connector”](./media/logic-apps-soap-connector-create-register/create-logic-apps-connector.png)

2. Välj resultatlistan **Logic Apps Connector** > **skapa**.

   ![Skapa Logic Apps-koppling](./media/logic-apps-soap-connector-create-register/choose-logic-apps-connector.png)

3. Ange information för att registrera din koppling som beskrivs i tabellen. När du är klar väljer **fäst på instrumentpanelen** > **skapa**.

   ![Information om logiska appar anpassad koppling](./media/logic-apps-soap-connector-create-register/logic-apps-soap-connector-details.png)

   | Egenskap | Föreslaget värde | Beskrivning | 
   | -------- | --------------- | ----------- | 
   | **Namn** | *SOAP-Kopplingsnamn* | Ange ett namn för din anslutningen. | 
   | **Prenumeration** | *Azure-prenumeration-name* | Välj din Azure-prenumeration. | 
   | **Resursgrupp** | *Azure-grupp-resursnamn* | Skapa eller välj en Azure-grupp för att ordna dina Azure-resurser. | 
   | **Plats** | *distribution region* | Välj en region för distribution i din koppling. | 
   |||| 

   När Azure distribuerar din koppling, öppnas automatiskt logic apps connector-menyn. 
   Om inte, Välj soap-anslutningen från Azure instrumentpanelen.

## <a name="2-define-your-connector"></a>2. Definiera din koppling

Ange WSDL-fil eller URL för att skapa din koppling, autentisering som din anslutningen används och åtgärder och utlösare som innehåller soap-koppling


### <a name="2a-specify-the-wsdl-file-or-url-for-your-connector"></a>2a. Ange WSDL-fil eller URL för din connector

1. Om inte redan har markerats i din connector-menyn, välja **Logic Apps Connector**. I verktygsfältet väljer **redigera**.

   ![Redigera anpassad koppling](./media/logic-apps-soap-connector-create-register/edit-soap-connector.png)

2. Välj **allmänna** så att du kan ange information i dessa tabeller för att skapa, skydda och definiera åtgärder och utlösare för SOAP-anslutningen.

   1. För **anpassade kopplingar**väljer **SOAP** för din **API-slutpunkt** så att du kan erbjuda WSDL-filen som beskriver ditt API.

      ![Ange WSDL-filen för ditt API](./media/logic-apps-soap-connector-create-register/provide-wsdl-file.png)

      | Alternativ | Format |Beskrivning | 
      | ------ | ------ | ----------- | 
      | **Överför WSDL från filen** | *WSDL-filen* | Bläddra till platsen för WSDL-filen och markera filen. | 
      | **Överför WSDL från URL** | http://*sökväg till wsdl-filen* | Ange en URL för din tjänst-WSDL-filen. | 
      | **SOAP-resten** |   | Omvandla API: er i SOAP-tjänsten till REST API: er. | 
      |||| 

   2. För **allmän information**, överför en ikon för din anslutningen. 
   Normalt den **beskrivning**, **värden**, och **bas-URL** fält fylls automatiskt i från WSDL-filen. 
   Men om de inte lägga till den här informationen som beskrivs i tabellen och välj **Fortsätt**. 

      ![Information om koppling](./media/logic-apps-soap-connector-create-register/add-general-details.png)

      | Alternativ eller -inställning | Format | Beskrivning | 
      | ----------------- | ------ | ----------- | 
      | **Överför ikon** | *PNG-or-jpg-File-under-1-MB* | En ikon som representerar din kontakt <p>Färg: Helst en vit logotyp mot en bakgrundsfärg. <p>Mått: En ~ 160 pixel logotyp i en ruta 230 bildpunkt | 
      | **Ikon för bakgrundsfärgen** | *ikonen varumärken-färg-hexadecimalt-kod* | <p>Färgen bakom en ikon som matchar bakgrundsfärgen som används i din fil. <p>Format: hexadecimalt. Till exempel representerar #007ee5 blå färg. | 
      | **Beskrivning** | *Beskrivning av koppling* | Ange en kort beskrivning för din anslutningen. | 
      | **Värden** | *Connector-värd* | Ange värddomänen för SOAP-tjänsten. | 
      | **Bas-URL** | *Connector-bas-URL* | Ange den grundläggande Webbadressen för SOAP-tjänsten. | 
      |||| 

### <a name="2b-describe-the-authentication-that-your-connector-uses"></a>2b. Beskriv den autentisering som använder din koppling

1. Nu välja **säkerhet** så att du kan granska eller beskriver den autentisering som använder din koppling. Autentisering ser till att användarnas identiteter korrekt flöda mellan din tjänst och klienter.

   Som standard din anslutningstjänsts **autentiseringstyp** är inställd på **ingen autentisering**.
   
   ![Autentiseringstyp](./media/logic-apps-soap-connector-create-register/security-authentication-options.png)

   Om du vill ändra autentiseringstyp väljer **redigera**. Du kan välja **grundläggande autentisering**. Om du vill använda parametern etiketter än standardvärdena, uppdatera dem under **parameterns namn**.

   ![Grundläggande autentisering](./media/logic-apps-soap-connector-create-register/security.png)

   
2. Om du vill spara din anslutningstjänst när du har angett säkerhetsinformation överst på sidan Välj **uppdatera anslutningen**, Välj **Fortsätt**. 

### <a name="2c-review-update-or-define-actions-and-triggers-for-your-connector"></a>2c. Granska, uppdatera och definiera åtgärder och utlösare för din connector

1. Nu välja **Definition** så att du kan granska, redigera eller definiera nya åtgärder och utlösare som användare kan lägga till sina arbetsflöden.

   Åtgärder och utlösare baseras på de åtgärder som definierats i WSDL-filen som automatiskt fylla i **Definition** sidan och innehåller värden för förfrågan och svar. Så om nödvändiga åtgärder redan visas här kan du gå till nästa steg i registreringen utan att göra ändringar på den här sidan.

   ![Definition av koppling](./media/logic-apps-soap-connector-create-register/definition.png)

2. Du kan också, om du vill redigera befintliga åtgärder och utlösare eller lägga till nya, [fortsätta med de här stegen](logic-apps-custom-connector-register.md#add-action-or-trigger).


## <a name="3-finish-creating-your-connector"></a>3. Slutför för att skapa din anslutning

När du är klar kan du välja **uppdatering Connector** så att du kan distribuera din koppling. 

Grattis! Nu när du skapar en logikapp, kan du hitta din anslutningstjänst i Logic Apps Designer och lägga till kopplingen i din logikapp.

![Hitta din anslutningstjänst i Logic Apps Designer](./media/logic-apps-soap-connector-create-register/soap-connector-created.png)

## <a name="share-your-connector-with-other-logic-apps-users"></a>Dela din kontakt med andra användare med Logic Apps

Registrerad men ocertifierade anpassade kopplingar fungerar som Microsoft-hanterade anslutningar, men är synliga och tillgängliga *endast* kopplingens författare och användare som har samma Azure Active Directory-klient och Azure-prenumeration för logikappar i den region där de apparna som har distribuerats. Delning är valfri, kanske scenarier där du vill dela dina kontakter med andra användare. 

> [!IMPORTANT]
> Om du delar en koppling, kan andra börja beror på kopplingen. 
> ***Ta bort din anslutning tar bort alla anslutningar till kopplingen.***
 
Dela din anslutningstjänst med externa användare utanför dessa gränser, till exempel med alla Logic Apps användare [skicka din connector för Microsoft-certifiering](../logic-apps/custom-connector-submit-certification.md).

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

**F:** är SOAP kopplingen allmänt tillgänglig (GA)? </br>
**S:** SOAP-kopplingen har **Preview**, och ännu inte är en GA-tjänst.

**F:** finns det några begränsningar och kända problem för SOAP-anslutningen? </br>
**S:** Ja, finns det [SOAP connector begränsningar och kända problem](../api-management/api-management-api-import-restrictions.md#wsdl).

**F:** finns det några gränser för anpassade kopplingar? </br>
**S:** Ja, finns det [anpassad koppling begränsar här](../logic-apps/logic-apps-limits-and-config.md#custom-connector-limits).

## <a name="get-support"></a>Få support

* Kontakta för support med utveckling och onboarding eller funktioner som inte är tillgängliga i Registreringsguiden för [ condevhelp@microsoft.com ](mailto:condevhelp@microsoft.com). Microsoft övervakar det här kontot för utvecklare frågor och problem och skickar dem till lämpliga teamet.

* Om du vill fråga eller besvara frågor, eller se vad andra användare i Azure Logic Apps gör finns i [Logikappar i Azure-forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* För att förbättra Logic Apps, rösta eller skicka in förslag på den [Logic Apps användaren feedbackwebbplats](http://aka.ms/logicapps-wish). 

## <a name="next-steps"></a>Nästa steg

* [Valfritt: Certifiera din koppling](../logic-apps/custom-connector-submit-certification.md)
* [Anpassad koppling vanliga frågor och svar](../logic-apps/custom-connector-faq.md)