---
title: HTTPS-slutpunkt | Azure Marketplace
description: Konfigurera leadhantering för en HTTPS-slutpunkt.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/24/2018
ms.author: dsindona
ms.openlocfilehash: cb6ef173e97a7c2bbd7d7cad5e5074b1f2d0f066
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288605"
---
# <a name="configure-lead-management-using-an-https-endpoint"></a>Konfigurera leadhantering med hjälp av en HTTPS-slutpunkt

Du kan använda en HTTPS-slutpunkt för att hantera Azure Marketplace- och AppSource-leads. Dessa leads kan skrivas till som kan skrivas till ett CRM-system (Customer Relationship Management) eller skickas ut som ett e-postmeddelande. I den här artikeln beskrivs hur du konfigurerar leadhantering med hjälp av Microsoft [Flow](https://powerapps.microsoft.com/automate-processes/) automationstjänst.

## <a name="create-a-flow-using-microsoft-flow"></a>Skapa ett flöde med Microsoft Flow

1. Öppna webbsidan [Flöde.](https://flow.microsoft.com/) Välj **Logga in** eller välj Registrera dig **gratis** för att skapa ett fritt Flow-konto.

2. Logga in och välj **Mina flöden** i menyraden.

    ![Mina flöden](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows.png)

3. Välj **+ Skapa från tom**.

    ![Skapa från början](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-create-fromblank.png)

4. Välj **Skapa från tom**.

    ![Skapa från början](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-create-fromblank2.png)

5. Skriv "begäran" i fältet **Sökkopplingar och utlösare** för att hitta anslutningsappen För begäran.
6. Under **Utlösare**väljer du **När en HTTP-begäran tas emot**. 

    ![Välj den mottagna HTTP-begäran-utlösaren](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-pick-request-trigger.png)

7. Använd något av följande steg för att konfigurera **JSON-schemat för begäranden:**

   - Kopiera [JSON-schemat](#json-schema) i slutet av den här artikeln till textrutan **Begärandetext JSON Schema.**
   - Välj **Generera schemat genom att använda en exempelnyttolast**. Klistra in i [JSON-exemplet](#json-example)i textrutan Retur eller klistra in ett exempel på **nyttolast.** Välj **Klar** om du vill skapa schemat.

   >[!Note]
   >Vid denna punkt i flödet kan du antingen ansluta till ett CRM-system eller konfigurera ett e-postmeddelande.

### <a name="to-connect-to-a-crm-system"></a>Så här ansluter du till ett CRM-system

1. Välj **+ Nytt steg**.
2. Välj det CRM-system som du väljer med åtgärden för att skapa en ny post. Följande skärminspelning visar **Dynamics 365 - Skapa en ny post** som ett exempel.

    ![Skapa en ny post](./media/cloud-partner-portal-lead-management-instructions-https/https-image009.png)

3. Ange **organisationsnamnet** som är anslutningsingångarna för kopplingen. Välj **Leads** i listrutan **Enhetsnamn.**

    ![Välj leads](./media/cloud-partner-portal-lead-management-instructions-https/https-image011.png)

4. Flödet visar ett formulär för att tillhandahålla leadinformation. Du kan mappa objekt från indatabegäran genom att välja att lägga till dynamiskt innehåll. Följande skärmdump visar **OfferTitle** som ett exempel.

    ![Lägg till dynamiskt innehåll](./media/cloud-partner-portal-lead-management-instructions-https/https-image013.png)

5. Mappa de fält du vill använda och välj sedan **Spara** för att spara flödet.

6. En HTTP POST-URL skapas i begäran. Kopiera den här URL:en och använd den som HTTPS-slutpunkt.

    ![URL för HTTP-inlägg](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-get-post-url.png)

### <a name="to-set-up-email-notification"></a>Så här konfigurerar du e-postmeddelande

1. Välj **+ Nytt steg**.
2. Under **Välj en åtgärd**väljer du **Åtgärder**.
3. Gå till **Åtgärder** och välj **Skicka ett e-postmeddelande**.

    ![Lägga till en e-poståtgärd](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-add-email-action.png)

4. Konfigurera följande obligatoriska fält i **Skicka ett e-postmeddelande:**

   - **Till** - Ange minst en giltig e-postadress.
   - **Ämne** - Flow ger dig möjlighet att lägga till dynamiskt innehåll, som **LeadSource** i följande skärmdump.

     ![Lägga till en e-poståtgärd med dynamiskt innehåll](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-configure-email-dynamic-content.png)

   - **Brödtext** - Lägg till den information du vill ha i brödtexten i e-postmeddelandet i listan Över dynamiskt innehåll. Till exempel Efternamn, Förnamn, E-post och Företag.

   När du är klar med att konfigurera e-postmeddelandet ser det ut som exemplet i följande skärmdump.

   ![Lägga till en e-poståtgärd](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-configure-email-action.png)

5. Välj **Spara** för att slutföra flödet.
6. En HTTP POST-URL skapas i begäran. Kopiera den här URL:en och använd den som HTTPS-slutpunkt.

    ![URL för HTTP-inlägg](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-get-post-url.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>Konfigurera erbjudandet för att skicka leads till HTTPS-slutpunkten

När du konfigurerar leadhanteringsinformationen för erbjudandet väljer du **HTTPS-slutpunkt** för **leadmålet** och klistrar in den HTTP POST-URL som du kopierade i föregående steg.  

![Lägg till dynamiskt innehåll](./media/cloud-partner-portal-lead-management-instructions-https/https-image017.png)

När leads genereras skickar Microsoft leads till flödet, som dirigeras till CRM-systemet eller e-postadressen som du har konfigurerat.

## <a name="json-schema-and-example"></a>JSON schema och exempel

I JSON-testexemplet används följande schema:

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

Du kan kopiera och redigera följande JSON-exempel som du kan använda som ett test i MS Flow.

### <a name="json-example"></a>JSON exempel

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

Om du inte redan har gjort det konfigurerar du [kundleads](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads) i Cloud Partner Portal.
