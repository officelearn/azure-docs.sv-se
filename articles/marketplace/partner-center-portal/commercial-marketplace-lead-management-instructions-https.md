---
title: HTTPS-slutpunkt | Azure Marketplace
description: Konfigurera leadhantering för en HTTPS-slutpunkt.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: dsindona
ms.openlocfilehash: 6a0131cf94759fc529a52ea33d5392a60c5ef30c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281603"
---
# <a name="configure-lead-management-using-an-https-endpoint"></a>Konfigurera leadhantering med hjälp av en HTTPS-slutpunkt

Om ditt CRM-system (Customer Relationship Management) inte uttryckligen stöds i Partner Center för att ta emot Azure Marketplace- och AppSource-leads kan du använda en HTTPS-slutpunkt i MS Flow för att hantera dessa leads. Med en HTTPS-slutpunkt kan dessa leads skickas ut som ett e-postmeddelande eller skrivas till ett CRM-system (Customer Relationship Management) som stöds av MS Flow. Instruktionerna i den här artikeln kommer att gå igenom den grundläggande processen för att skapa ett nytt flöde med Hjälp av Microsoft Flow, som kommer att generera HTTP POST URL som du kommer att ange i publiceringsportalen för Lead Management > **HTTPS Slutpunkt URL-fält.** Dessutom ingår instruktioner om hur du kan testa ditt flöde med hjälp av ett verktyg som kallas [Brevbäraren](https://www.getpostman.com/downloads/) som finns på nätet.

## <a name="create-a-flow-using-microsoft-flow"></a>Skapa ett flöde med Microsoft Flow

1. Öppna webbsidan [Flöde.](https://flow.microsoft.com/) Välj **Logga in**, eller om du inte redan har ett konto väljer du Registrera dig **gratis** för att skapa ett fritt Flow-konto.

2. Logga in och välj **Mina flöden** i menyraden.

3. Välj **+Automatiserad - från tom**.

    ![Mina flöden + Automatiserad - från tomt](./media/commercial-marketplace-lead-management-instructions-https/my-flows-automated.png)

4. Välj **Hoppa över**i fönstret Skapa ett *automatiskt flöde* . 

    ![Skapa automatiserat flöde - Hoppa över](./media/commercial-marketplace-lead-management-instructions-https/build-automated-flow.png)

5. Skriv "begäran" i fältet **Sökkopplingar och utlösare** för att hitta anslutningsappen För begäran.
6. Under *Utlösare*väljer du **När en HTTP-begäran tas emot**. 

    ![begäran anslutning - Utlösare](./media/commercial-marketplace-lead-management-instructions-https/request-connector.png)

7. Kopiera och klistra in JSON-schemat nedan i textrutan **Begärandetext JSON Schema** i *när en HTTP-begäran tas emot.* Det här schemat används av Microsoft för att innehålla dina leaddata.

    ![begäran anslutning - Utlösare](./media/commercial-marketplace-lead-management-instructions-https/https-request-received.png)

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
>Vid denna punkt i konfigurationen kan du välja att antingen ansluta till ett CRM-system eller konfigurera ett e-postmeddelande. Följ de återstående instruktionerna baserat på ditt val.

### <a name="to-connect-to-a-crm-system"></a>Så här ansluter du till ett CRM-system

1. Välj **+ Nytt steg**.
2. Välj det CRM-system som du väljer genom att söka efter det där det står *Sökkopplingar och åtgärder*och välj det under avsnittet *Åtgärder* med åtgärden för att skapa en ny post. Följande skärminspelning visar **Dynamics 365 - Skapa** en ny post som ett exempel.

    ![Skapa en ny post](./media/commercial-marketplace-lead-management-instructions-https/create-new-record.png)

3. Ange **organisationsnamnet** som är associerat med CRM-systemet. Välj **Leads** i listrutan **Enhetsnamn.**

    ![Välj leads](./media/commercial-marketplace-lead-management-instructions-https/select-leads.png)

4. Flödet visar ett formulär för att tillhandahålla leadinformation. Du kan mappa objekt från indatabegäran genom att välja att lägga till dynamiskt innehåll. Följande skärmdump visar **OfferTitle** som ett exempel.

    ![Lägg till dynamiskt innehåll](./media/commercial-marketplace-lead-management-instructions-https/add-dynamic-content.png)

5. Mappa de fält du vill använda och välj sedan **Spara** för att spara flödet. En HTTP POST-URL skapas och är tillgänglig i fönstret *När en HTTP-begäran tas emot.* Kopiera den här webbadressen med hjälp av kopieringskontrollen som finns till höger om HTTP POST-URL:en - detta är viktigt så att du inte av misstag missar någon del av hela webbadressen. Spara den här webbadressen som du behöver när du konfigurerar leadhantering i publiceringsportalen.

    ![När en HTTP-begäran tas emot.](./media/commercial-marketplace-lead-management-instructions-https/when-http-request-received.png)

### <a name="to-set-up-email-notification"></a>Så här konfigurerar du e-postmeddelande

1. Nu när du har slutfört JSON-schemat väljer du **+ Nytt steg**.
2. Under **Välj en åtgärd**väljer du **Åtgärder**.
3. Under **Åtgärder**väljer du **Skicka ett e-postmeddelande (Office 365 Outlook)**.

    >[!Note]
    >Om du vill använda en annan e-postleverantör söka efter och välj *Skicka ett e-postmeddelande (Mail)* som åtgärd i stället.

    ![Lägga till en e-poståtgärd](./media/commercial-marketplace-lead-management-instructions-https/https-request-received-send-email.png)

4. Konfigurera följande obligatoriska fält i **Skicka ett e-postfönster:**

   - **Till** - Ange minst en giltig e-postadress, där leads kommer att skickas.
   - **Ämne** - Flow ger dig möjlighet att lägga till dynamiskt innehåll, som **LeadSource** i följande skärmdump. Börja med att skriva in ett fältnamn följt av att klicka på listan för dynamiskt innehåll i popup-fönstret. 

        >[!Note] 
        > När du lägger till fältnamn kan du följa var och en med en ":" och sedan ange för att skapa en ny rad. När du har lagt till dina fältnamn kan du sedan lägga till varje associerad parameter från den dynamiska plocklistan.

        ![Lägga till en e-poståtgärd med dynamiskt innehåll](./media/commercial-marketplace-lead-management-instructions-https/add-email-using-dynamic-content.png)

   - **Brödtext** - Lägg till den information du vill ha i brödtexten i e-postmeddelandet i listan Över dynamiskt innehåll. Till exempel Efternamn, Förnamn, E-post och Företag. <br> <br> När du är klar med att konfigurera e-postmeddelandet ser det ut som exemplet i följande skärmdump.


       ![Lägga till en e-poståtgärd](./media/commercial-marketplace-lead-management-instructions-https/send-an-email.png)

5. Välj **Spara** för att slutföra flödet. En HTTP POST-URL skapas och är tillgänglig i fönstret *När en HTTP-begäran tas emot.* Kopiera den här webbadressen med hjälp av kopieringskontrollen som finns till höger om HTTP POST-URL:en - detta är viktigt så att du inte av misstag missar någon del av hela webbadressen. Spara den här webbadressen som du behöver när du konfigurerar leadhantering i publiceringsportalen.

   ![URL FÖR HTTP-INLÄGG ](./media/commercial-marketplace-lead-management-instructions-https/http-post-url.png)

### <a name="testing"></a>Testning

Du kan testa att allt fungerar som förväntat med hjälp av följande steg med hjälp av ett verktyg som heter [Postman](https://app.getpostman.com/app/download/win64), som kan laddas ner online. Detta är tillgängligt för Windows. 

1. Starta Postman och välj **Ny** > **begäran för** att ställa in testverktyget. 

   ![Begäran om att ställa in testverktyget](./media/commercial-marketplace-lead-management-instructions-https/postman-request.png)

2. Fyll i formuläret *Spara begäran* och **spara** sedan i mappen som du skapade.

   ![Spara begäran](./media/commercial-marketplace-lead-management-instructions-https/postman-save-to-test.png)

3. Välj **POST** i listrutan. 

   ![Testa mitt flöde](./media/commercial-marketplace-lead-management-instructions-https/test-my-flow.png)

4. Klistra in HTTP POST-URL:en från flödet som du skapade i MS Flow där det står *Ange URL för begäran*.

   ![Klistra in HTTP POST-URL:en](./media/commercial-marketplace-lead-management-instructions-https/paste-http-post-url.png)

5. Gå tillbaka till [Flow](https://flow.microsoft.com/) och hitta flödet du skapade för att skicka leads genom att gå till **Mina flöden** från Flow-menyraden.  Markera de tre punkterna bredvid flödesnamnet och välj **Redigera**.

   ![Mina flöden - Redigera](./media/commercial-marketplace-lead-management-instructions-https/my-flows-edit.png)

6. Välj **Testa** i det övre högra hörnet, välj "Jag utför utlösaråtgärden" och välj sedan **Testa**. Du kommer att se en indikation högst upp på skärmen som anger att testet har startat

   ![Testflöde - utlösare](./media/commercial-marketplace-lead-management-instructions-https/test-flow-trigger-action.png)

7. Gå tillbaka till postman-appen och välj **Skicka** bredvid den plats där du klistrade in HTTPS-URL:en.

   ![Testa mitt flöde - Skicka](./media/commercial-marketplace-lead-management-instructions-https/postman-send.png)

8. Gå tillbaka till ditt flöde och kontrollera resultatet. Om allt fungerar som förväntat kommer du att se ett meddelande som anger att det lyckades.

   ![Flöde - Kontrollera resultat](./media/commercial-marketplace-lead-management-instructions-https/my-flow-check-results.png)

9. Du borde också ha fått ett e-postmeddelande. Kontrollera din e-post inkorg. 

    >[!Note] 
    >Om du inte ser ett e-postmeddelande från testet, kontrollera sedan din spam och skräp mappar. Nedan kommer du att märka bara de fältetiketter du lagt till när du konfigurerar e-postmeddelandet. Om detta var ett verkligt lead som genereras från ditt erbjudande, skulle du också se den faktiska informationen från leadkontakten i brödtexten och i ämnesraden.

   ![Mottaget e-postmeddelande](./media/commercial-marketplace-lead-management-instructions-https/email-received.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>Konfigurera erbjudandet för att skicka leads till HTTPS-slutpunkten

När du är redo att konfigurera leadhanteringsinformationen för ditt erbjudande i publiceringsportalen följer du stegen nedan:

1. Navigera till **inställningssidan** för Erbjudandet för ditt erbjudande.
2. Välj **Anslut** under avsnittet LeadHantering.
3. I popup-fönstret Anslutningsinformation väljer du **HTTPS-slutpunkt** för **leadmålet** och klistrar in HTTP POST-URL:en från flödet som du skapade genom att följa tidigare steg i **HTTPS-slutpunkts-URL-fältet.**
4. Välj **Spara**. 

>[!Note] 
>Du måste slutföra konfigurationen av resten av erbjudandet och publicera det innan du kan ta emot leads för erbjudandet.

När leads genereras skickar Microsoft leads till flödet, som dirigeras till CRM-systemet eller e-postadressen som du har konfigurerat.

![Leadhantering - anslut](./media/commercial-marketplace-lead-management-instructions-https/lead-management-connect.png)

![Anslutningsinformation](./media/commercial-marketplace-lead-management-instructions-https/connection-details.png)

![Anslutningsinformation](./media/commercial-marketplace-lead-management-instructions-https/connection-details-1.png)

