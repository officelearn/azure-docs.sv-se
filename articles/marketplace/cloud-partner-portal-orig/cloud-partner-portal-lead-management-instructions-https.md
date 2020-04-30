---
title: Konfigurera hantering av leads med en HTTPS-slutpunkt | Azure Marketplace
description: Lär dig hur du använder en HTTP-slutpunkt för att hantera Microsoft AppSource och Azure Marketplace-leads.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: dsindona
ms.openlocfilehash: f56cc5aaad7d77ff8dc753115ef1becb08ddde73
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81770204"
---
# <a name="configure-lead-management-using-an-https-endpoint"></a>Konfigurera hantering av leads med en HTTPS-slutpunkt

Du kan använda en HTTPS-slutpunkt för att hantera Microsoft AppSource och Azure Marketplace-leads. Dessa leads kan skrivas till ett CRM-system (Customer Relations hip Management) eller skickas som ett e-postmeddelande. Den här artikeln beskriver hur du konfigurerar hantering av leads med hjälp av [Microsoft Power](https://powerapps.microsoft.com/automate-processes/) Automation Automation-tjänsten.

## <a name="create-a-flow-using-microsoft-power-automate"></a>Skapa ett flöde med Microsoft Power automatisering

1. Öppna webb sidan [automatiserad start](https://flow.microsoft.com/) . Välj **Logga** in eller Välj **Registrera dig kostnads fritt** om du vill skapa ett kostnads fritt flödes konto.

1. Logga in och välj **mina flöden** på Meny raden.
    > [!div class="mx-imgBorder"]
    > ![Mina flöden](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows.png)

1. Under **+ ny**väljer du **+ snabb – från Tom**.
    > [!div class="mx-imgBorder"]
    > ![Skapa från Tom](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-create-fromblank.png)

1. Namnge ditt flöde och välj sedan **när en HTTP-begäran tas emot**under **Välj hur det här flödet ska utlösas**.

    > [!div class="mx-imgBorder"]
    > ![Välj den mottagna HTTP-begäran-utlösaren](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-pick-request-trigger.png)

1. Klicka på flödes steget för att expandera det.

    > [!div class="mx-imgBorder"]
    > ![Expandera flödes steget](./media/cloud-partner-portal-lead-management-instructions-https/expand-flow-step.png)

1. Använd någon av följande metoder för att konfigurera **begär ande texten JSON-schema**:

   - Kopiera [JSON-schemat](#json-schema) i slutet av den här artikeln till text rutan JSON- **schema för begär ande** text.
   - Välj **Generera schemat genom att använda en exempelnyttolast**. Klistra in i [JSON-exemplet](#json-example)i text rutan **Ange eller klistra in en JSON-nyttolast i exemplet** . Välj **färdig** för att skapa schemat.

   >[!Note]
   >I det här flödet kan du antingen ansluta till ett CRM-system eller konfigurera ett e-postmeddelande.

### <a name="to-connect-to-a-crm-system"></a>Så här ansluter du till ett CRM-system

1. Välj **+ Nytt steg**.
2. Välj det CRM-system som du väljer med åtgärden för att skapa en ny post. Följande skärm bild visar **Dynamics 365 – skapa en ny post** som exempel.

    ![Skapa en ny post](./media/cloud-partner-portal-lead-management-instructions-https/https-image009.png)

3. Ange **organisations namnet** som är anslutnings indata för din anslutning. Välj **leads** i list rutan **entitetsnamn** .

    ![Välj leads](./media/cloud-partner-portal-lead-management-instructions-https/https-image011.png)

4. Flow visar ett formulär för att tillhandahålla lead-information. Du kan mappa objekt från Indataporten genom att välja att lägga till dynamiskt innehåll. Följande skärm bild visar **OfferTitle** som ett exempel.

    ![Lägg till dynamiskt innehåll](./media/cloud-partner-portal-lead-management-instructions-https/https-image013.png)

5. Mappa de fält som du vill använda och välj sedan **Spara** för att spara ditt flöde.

6. En HTTP POST-URL skapas i begäran. Kopiera denna URL och Använd den som HTTPS-slutpunkt.

    ![HTTP post-URL](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-get-post-url.png)

### <a name="to-set-up-email-notification"></a>Konfigurera e-postavisering

1. Välj **+ Nytt steg**.
2. Under **Välj en åtgärd**väljer du **åtgärder**.
3. Gå till **Åtgärder** och välj **Skicka ett e-postmeddelande**.

    ![Lägg till en e-poståtgärd](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-add-email-action.png)

4. I **Skicka ett e-postmeddelande**konfigurerar du följande obligatoriska fält:

   - Ange minst en giltig **e-postadress** .
   - **Subject** -Flow ger dig möjlighet att lägga till dynamiskt innehåll, som **LeadSource** i följande skärm bild.

     ![Lägg till en e-poståtgärd med dynamiskt innehåll](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-configure-email-dynamic-content.png)

   - **Brödtext** – från listan med dynamiskt innehåll lägger du till den information som du vill ha i e-postmeddelandets brödtext. Till exempel LastName, FirstName, E-mail och Company.

   När du är klar med att konfigurera e-postmeddelandet ser det ut som i exemplet på följande skärm bild.

   ![Lägg till en e-poståtgärd](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-configure-email-action.png)

5. Välj **Spara** för att slutföra ditt flöde.

6. En HTTP POST-URL skapas i begäran. Kopiera denna URL och Använd den som HTTPS-slutpunkt.

    ![HTTP post-URL](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-get-post-url.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>Konfigurera ditt erbjudande för att skicka leads till HTTPS-slutpunkten

När du konfigurerar information om lead-hantering för ditt erbjudande väljer du **https-slutpunkt** för lead- **målet** och klistrar in den http post-URL som du kopierade i föregående steg.  

![Lägg till dynamiskt innehåll](./media/cloud-partner-portal-lead-management-instructions-https/https-image017.png)

När leads skapas skickar Microsoft leads till ditt energi automatiserade flöde, som dirigeras till CRM-systemet eller e-postadressen som du har konfigurerat.

## <a name="json-schema-and-example"></a>JSON-schema och exempel

Exemplet på JSON-test använder följande schema:

### <a name="json-schema"></a>JSON-schema

``` json
{
  "$schema": "https://json-schema.org/draft-04/schema#",
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
    "Description": {
      "id": "/properties/Description",
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

Du kan kopiera och redigera följande JSON-exempel för att använda som ett test i ditt flöde.

### <a name="json-example"></a>JSON-exempel

```json
{
  "UserDetails": {
    "FirstName": "Some",
    "LastName": "One",
    "Email": "someone@contoso.com",
    "Phone": "16175555555",
    "Country": "USA",
    "Company": "Contoso",
    "Title": "Esquire"
 },
  "LeadSource": "AzureMarketplace",
  "ActionCode": "INS",
  "OfferTitle": "Test Microsoft",
  "Description": "Test run through Power Automate"
}
```

## <a name="next-steps"></a>Nästa steg

Om du inte redan har gjort det konfigurerar du kund [leads](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads) i Cloud Partner Portal.
