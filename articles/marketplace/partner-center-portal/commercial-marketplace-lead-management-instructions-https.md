---
title: HTTPS-slutpunkt | Azure Marketplace
description: Konfigurera ledar hantering för en HTTPS-slutpunkt.
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: evansma
ms.openlocfilehash: 83efb9cfd1ee7464a334ebc4064dbfaa20ab30de
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73812293"
---
# <a name="configure-lead-management-using-an-https-endpoint"></a>Konfigurera hantering av leads med en HTTPS-slutpunkt

Om ditt CRM-system (Customer Relations hip Management) inte uttryckligen stöds i Partner Center för att ta emot Azure Marketplace-och AppSource-leads, kan du använda en HTTPS-slutpunkt i MS Flow för att hantera dessa leads. Med en HTTPS-slutpunkt kan dessa leads skickas som ett e-postmeddelande eller skrivas till ett CRM-system (Customer Relations hip Management) som stöds av MS Flow. Anvisningarna i den här artikeln vägleder dig genom Basic-processen för att skapa ett nytt flöde med hjälp av Microsoft Flow, vilket genererar HTTP POST-URL: en som du kommer att ange i publicerings portalen för **URL: en för URL: en** för den inledande hanterings > https. Dessutom finns anvisningar om hur du kan testa flödet med hjälp av ett verktyg som kallas [Postman](https://www.getpostman.com/downloads/) som kan hittas online.

## <a name="create-a-flow-using-microsoft-flow"></a>Skapa ett flöde med hjälp av Microsoft Flow

1. Öppna webb sidan för [flödet](https://flow.microsoft.com/) . Välj **Logga**in, eller om du inte redan har ett konto väljer du **Registrera dig kostnads fritt** för att skapa ett kostnads fritt flödes konto.

2. Logga in och välj **mina flöden** på Meny raden.

3. Välj **+ automatiserad – från Tom**.

    ![Mina flöden + automatiserad från Tom](./media/commercial-marketplace-lead-management-instructions-https/my-flows-automated.png)

4. I fönstret *Bygg ett automatiserat flöde* väljer du **hoppa över**. 

    ![Bygg automatiskt flöde – hoppa över](./media/commercial-marketplace-lead-management-instructions-https/build-automated-flow.png)

5. I fältet **Sök anslutningar och utlösare** skriver du "Request" för att hitta begär ande anslutningen.
6. Under *utlösare*väljer du **när en http-begäran tas emot**. 

    ![Request Connector – utlösare](./media/commercial-marketplace-lead-management-instructions-https/request-connector.png)

7. I fönstret *när en HTTP-förfrågan tas emot* kopierar du och klistrar in JSON-schemat nedan i text rutan **JSON-schema för begär ande** text. Det här schemat används av Microsoft för att innehålla dina lead-data.

    ![Request Connector – utlösare](./media/commercial-marketplace-lead-management-instructions-https/https-request-received.png)

    **JSON-schema**

    ```JSON
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

>[!Note]
>I den här punkten i konfigurationen kan du välja att antingen ansluta till ett CRM-system eller konfigurera ett e-postmeddelande. Följ de återstående instruktionerna som du väljer.

### <a name="to-connect-to-a-crm-system"></a>Så här ansluter du till ett CRM-system

1. Välj **+ nytt steg**.
2. Välj önskat CRM-system genom att söka efter det där det står *Sök anslutningar och åtgärder*, och markera det under avsnittet *åtgärder* med åtgärden för att skapa en ny post. Följande skärm bild visar **Dynamics 365 – skapa** en ny post som exempel.

    ![Skapa en ny post](./media/commercial-marketplace-lead-management-instructions-https/create-new-record.png)

3. Ange **organisations namnet** som är kopplat till CRM-systemet. Välj **leads** i list rutan **entitetsnamn** .

    ![Välj leads](./media/commercial-marketplace-lead-management-instructions-https/select-leads.png)

4. Flow visar ett formulär för att tillhandahålla lead-information. Du kan mappa objekt från Indataporten genom att välja att lägga till dynamiskt innehåll. Följande skärm bild visar **OfferTitle** som ett exempel.

    ![Lägg till dynamiskt innehåll](./media/commercial-marketplace-lead-management-instructions-https/add-dynamic-content.png)

5. Mappa de fält som du vill använda och välj sedan **Spara** för att spara ditt flöde. En HTTP POST-URL skapas och är tillgänglig i fönstret *när en HTTP-förfrågan tas emot* . Kopiera den här URL: en med hjälp av kopierings kontrollen som finns till höger om HTTP POST-URL: en är viktig så att du inte av misstag saknar någon del av hela URL: en. Spara den här URL: en eftersom du kommer att behöva den när du konfigurerar hantering av lead i publicerings portalen.

    ![När en HTTP-begäran tas emot.](./media/commercial-marketplace-lead-management-instructions-https/when-http-request-received.png)

### <a name="to-set-up-email-notification"></a>Konfigurera e-postavisering

1. Nu när du har slutfört JSON-schemat väljer du **+ nytt steg**.
2. Under **Välj en åtgärd**väljer du **åtgärder**.
3. Under **åtgärder**väljer du **Skicka ett e-postmeddelande (Office 365 Outlook)** .

    >[!Note]
    >Om du vill använda en annan e-postprovider söker du efter och väljer *Skicka ett e-postmeddelande (e-post)* som åtgärd i stället.

    ![Lägg till en e-poståtgärd](./media/commercial-marketplace-lead-management-instructions-https/https-request-received-send-email.png)

4. Konfigurera följande obligatoriska fält i fönstret **Skicka ett e-postmeddelande** :

   - Ange minst en giltig e-postadress, där leads **ska skickas** .
   - **Subject** -Flow ger dig möjlighet att lägga till dynamiskt innehåll, som **LeadSource** i följande skärm bild. Börja med att skriva in ett fält namn följt av genom att klicka på listan med dynamiskt innehåll i fönstret för att välja dynamiskt innehåll. 

        >[!Note] 
        > När du lägger till fält namn kan du följa var och en med ett ":" och sedan ange för att skapa en ny rad. När du har lagt till dina fält namn kan du lägga till varje associerad parameter från den dynamiska plock listan.

        ![Lägg till en e-poståtgärd med dynamiskt innehåll](./media/commercial-marketplace-lead-management-instructions-https/add-email-using-dynamic-content.png)

   - **Brödtext** – från listan över dynamiska innehålls val lägger du till den information som du vill ha i e-postmeddelandets brödtext. Till exempel LastName, FirstName, E-mail och Company. <br> <br> När du är klar med att konfigurera e-postmeddelandet ser det ut som i exemplet på följande skärm bild.


       ![Lägg till en e-poståtgärd](./media/commercial-marketplace-lead-management-instructions-https/send-an-email.png)

5. Välj **Spara** för att slutföra ditt flöde. En HTTP POST-URL skapas och är tillgänglig i fönstret *när en HTTP-förfrågan tas emot* . Kopiera den här URL: en med hjälp av kopierings kontrollen som finns till höger om HTTP POST-URL: en är viktig så att du inte av misstag saknar någon del av hela URL: en. Spara den här URL: en eftersom du kommer att behöva den när du konfigurerar hantering av lead i publicerings portalen.

   ![HTTP POST-URL ](./media/commercial-marketplace-lead-management-instructions-https/http-post-url.png)

### <a name="testing"></a>Testning

Du kan testa att allt fungerar som förväntat med hjälp av ett verktyg som kallas [Postman](https://app.getpostman.com/app/download/win64), som kan hämtas online. Detta är tillgängligt för Windows. 

1. Starta Postman och välj **ny** > **begäran** om du vill ställa in ditt test verktyg. 

   ![Begäran om att konfigurera ditt test verktyg](./media/commercial-marketplace-lead-management-instructions-https/postman-request.png)

2. Fyll i formuläret *Spara begäran* och **Spara** det sedan i den mapp som du skapade.

   ![Spara begäran](./media/commercial-marketplace-lead-management-instructions-https/postman-save-to-test.png)

3. Välj **post** från den nedrullningsbara listan. 

   ![Testa mitt flöde](./media/commercial-marketplace-lead-management-instructions-https/test-my-flow.png)

4. Klistra in HTTP POST-URL: en från det flöde som du skapade i MS Flow där det står i *Ange URL för begäran*.

   ![Klistra in URL: en för HTTP-POST](./media/commercial-marketplace-lead-management-instructions-https/paste-http-post-url.png)

5. Gå tillbaka till [flödet](https://flow.microsoft.com/) och hitta det flöde som du skapade för att skicka leads genom att gå till **mina flöden** från menyn i Flow.  Välj de tre punkterna bredvid flödes namnet och välj **Redigera**.

   ![Mina flöden – redigera](./media/commercial-marketplace-lead-management-instructions-https/my-flows-edit.png)

6. Välj **test** i det övre högra hörnet, välj "Jag ska utföra utlösnings åtgärden" och välj sedan **testa**. En indikation visas överst på skärmen som visar att testet har startat

   ![Test flöde – utlösare](./media/commercial-marketplace-lead-management-instructions-https/test-flow-trigger-action.png)

7. Gå tillbaka till din Postman-app och välj **Skicka** bredvid den plats där du klistrade in https-URL: en.

   ![Testa mitt flöde – skicka](./media/commercial-marketplace-lead-management-instructions-https/postman-send.png)

8. Gå tillbaka till ditt flöde och kontrol lera resultatet. Om allt fungerar som förväntat visas ett meddelande som anger att det lyckades.

   ![Flödes kontroll resultat](./media/commercial-marketplace-lead-management-instructions-https/my-flow-check-results.png)

9. Du bör också ha fått ett e-postmeddelande. Kontrol lera din e-postinkorg. 

    >[!Note] 
    >Om du inte ser ett e-postmeddelande från testet kontrollerar du mapparna spam och skräp post. Nedan ser du bara fält etiketterna som du lade till när du konfigurerade e-postmeddelandet. Om detta var ett faktiskt lead som genererats från ditt erbjudande, skulle du också se den faktiska informationen från lead-kontakten i bröd texten och på ämnes raden.

   ![E-post mottagen](./media/commercial-marketplace-lead-management-instructions-https/email-received.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>Konfigurera ditt erbjudande för att skicka leads till HTTPS-slutpunkten

När du är redo att konfigurera ledar hanterings informationen för ditt erbjudande i publicerings portalen följer du stegen nedan:

1. Gå till sidan med **installations programmet** för erbjudandet.
2. Välj **Anslut** under avsnittet ledar hantering.
3. I popup-fönstret anslutnings information väljer du https- **slutpunkt** för lead- **målet** och klistrar in URL: en för http-post från det flöde som du skapade genom att följa tidigare steg i URL-fältet för **https-slutpunkt** .
4. Välj **Spara**. 

>[!Note] 
>Du måste slutföra konfigurationen av resten av erbjudandet och publicera den innan du kan ta emot leads för erbjudandet.

När leads skapas skickar Microsoft leads till flödet, som dirigeras till CRM-systemet eller e-postadressen som du har konfigurerat.

![Lead-hantering – Anslut](./media/commercial-marketplace-lead-management-instructions-https/lead-management-connect.png)

![Anslutningsinformation](./media/commercial-marketplace-lead-management-instructions-https/connection-details.png)

![Anslutningsinformation](./media/commercial-marketplace-lead-management-instructions-https/connection-details-1.png)

