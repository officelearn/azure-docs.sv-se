---
title: HTTPS-slutpunkt | Microsoft Docs
description: Konfigurera lead-hantering för en HTTPS-slutpunkt.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/24/2018
ms.author: pbutlerm
ms.openlocfilehash: 8898149e6858c5a1cdb2d4510ad2764ffe25fda5
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2019
ms.locfileid: "55964261"
---
# <a name="configure-lead-management-using-an-https-endpoint"></a>Konfigurera lead-hantering med hjälp av en HTTPS-slutpunkt

Du kan använda en HTTPS-slutpunkt för att hantera Azure Marketplace och AppSource leads. De här potentiella kunderna kan skrivas till som kan skrivas till en kund relation Management (CRM)-system eller skickas ut som ett e-postmeddelande. Den här artikeln beskriver hur du konfigurerar lead med hjälp av den [Microsoft Flow](https://powerapps.microsoft.com/automate-processes/) automation-tjänsten.

## <a name="create-a-flow-using-microsoft-flow"></a>Skapa ett flöde som använder Microsoft Flow

1. Öppna den [Flow](https://flow.microsoft.com/) webbsidan. Välj **logga in** eller välj **registrera dig kostnadsfritt** att skapa en kostnadsfri Flow-konto.

2. Logga in och välj **Mina flöden** på menyraden.

    ![Mina flöden](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows.png)

3. Välj **+ skapa från tom**.

    ![Skapa från början](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-create-fromblank.png)

4. Välj **skapa från tom**.

    ![Skapa från början](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-create-fromblank2.png)

5. I den **Sök anslutningsappar och utlösare** skriver ”begär” för att hitta kopplingen för begäran.
6. Under **utlösare**väljer **när en HTTP-begäran tas emot**. 

    ![Välj utlösaren för HTTP-begäran mottagen](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-pick-request-trigger.png)

7. Använd någon av följande steg för att konfigurera den **begär JSON-Brödtextsschema**:

   - Kopiera den [JSON-schema](#JSON-schema) i slutet av den här artikeln till den **begär JSON-Brödtextsschema** textrutan.
   - Välj **Generera schemat genom att använda en exempelnyttolast**. I den **Skriv eller klistra in en JSON-exempelnyttolast** textrutan, klistra in den [exempel JSON](#JSON-example). Välj **klar** att skapa schemat.

   >[!Note]
   >I det här läget i flödet kan du ansluta till ett CRM-system eller konfigurera ett e-postmeddelande.

### <a name="to-connect-to-a-crm-system"></a>Att ansluta till ett CRM-system

1. Välj **+ nytt steg**.
2. Välj CRM-systemet för med åtgärden att skapa en ny post. Följande skärmdumpar visar **Dynamics 365 – skapa en ny post** som exempel.

    ![Skapa en ny post](./media/cloud-partner-portal-lead-management-instructions-https/https-image009.png)

3. Ange den **organisationsnamn** som är anslutningsindata för din anslutningsapp. Välj **leder** från den **entitetsnamn** listrutan.

    ![Välj leads](./media/cloud-partner-portal-lead-management-instructions-https/https-image011.png)

4. Flow visar ett formulär för att tillhandahålla lead-information. Du kan mappa objekt från den inkommande begäran genom att välja att lägga till dynamiskt innehåll. Följande skärmdumpar visar **OfferTitle** som exempel.

    ![Lägg till dynamiskt innehåll](./media/cloud-partner-portal-lead-management-instructions-https/https-image013.png)

5. Mappa fälten du vill och välj sedan **spara** att spara ditt flöde.

6. En HTTP POST-Webbadress har skapats i begäran. Kopiera URL: en och använda den som HTTPS-slutpunkten.

    ![HTTP post-URL](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-get-post-url.png)

### <a name="to-set-up-email-notification"></a>Att ställa in e-postmeddelande

1. Välj **+ nytt steg**.
2. Under **Välj en åtgärd**väljer **åtgärder**.
3. Gå till **Åtgärder** och välj **Skicka ett e-postmeddelande**.

    ![Lägg till en e poståtgärd](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-add-email-action.png)

4. I **skicka ett e-postmeddelande**, konfigurera följande obligatoriska fält:

   - **Att** -ange minst en giltig e-postadress.
   - **Ämne** -Flow ger dig möjlighet att lägga till dynamiskt innehåll, t.ex **LeadSource** i följande skärmbild.

    ![Lägg till en e poståtgärd med dynamiskt innehåll](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-configure-email-dynamic-content.png)

   - **Brödtext** – från den dynamiska innehållslistan, lägga till information i brödtexten i e-postmeddelandet. Till exempel LastName, FirstName e-post, och företag.

   När du är klar att konfigurera e-postmeddelandet, ut det som i exemplet i följande skärmbild.

   ![Lägg till en e poståtgärd](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-configure-email-action.png)

5. Välj **spara** Slutför ditt flöde.
6. En HTTP POST-Webbadress har skapats i begäran. Kopiera URL: en och använda den som HTTPS-slutpunkten.

    ![HTTP post-URL](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-get-post-url.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>Konfigurera ditt erbjudande till skicka leads till HTTPS-slutpunkt

När du konfigurerar lead hanteringsinformation för ditt erbjudande, Välj **HTTPS-slutpunkt** för den **leda mål** och klistra in i HTTP POST-URL som du kopierade i föregående steg.  

![Lägg till dynamiskt innehåll](./media/cloud-partner-portal-lead-management-instructions-https/https-image017.png)

När leads genereras skickar Microsoft leads till flödet som dirigeras till CRM-system eller e-postadress du har konfigurerat.

## <a name="json-schema-and-example"></a>JSON-schema och exempel

I JSON-test-exemplet används följande schema:

### <a name="json-schema"></a>JSON-schema

``` json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "definitions": {},
  "id": "http://example.com/example.json",
  "properties": {
    "ActionCode": {
      "id": "/properties/ActionCode",
      "type": "string"
    },
    "OfferTitle": {
      "id": "/properties/OfferTitle",
      "type": "string"
    },
    "LeadSource": {
      "id": "/properties/LeadSource",
      "type": "string"
    },
    "UserDetails": {
      "id": "/properties/UserDetails",
      "properties": {
        "Company": {
          "id": "/properties/UserDetails/properties/Company",
          "type": "string"
        },
        "Country": {
          "id": "/properties/UserDetails/properties/Country",
          "type": "string"
        },
        "Email": {
          "id": "/properties/UserDetails/properties/Email",
          "type": "string"
        },
        "FirstName": {
          "id": "/properties/UserDetails/properties/FirstName",
          "type": "string"
        },
        "LastName": {
          "id": "/properties/UserDetails/properties/LastName",
          "type": "string"
        },
        "Phone": {
          "id": "/properties/UserDetails/properties/Phone",
          "type": "string"
        },
        "Title": {
          "id": "/properties/UserDetails/properties/Title",
          "type": "string"
        }
      },
      "type": "object"
    }
  },
  "type": "object"
}
```

Du kan kopiera och redigera följande JSON-exempel ska användas som ett test i MS Flow.

### <a name="json-example"></a>JSON-exempel

```json
{
"OfferTitle": "Test Microsoft",
"LeadSource": "Test run through MS Flow",
"UserDetails": {
"Company": "Contoso",
"Country": "USA",
"Email": "someone@contoso.com",
"FirstName": "Some",
"LastName": "One",
"Phone": "16175555555",
"Title": "Esquire"
}
}
```

## <a name="next-steps"></a>Nästa steg

Om du inte redan gjort det, konfigurera kunden [leder](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads) i partnerportalen i molnet.
