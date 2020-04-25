---
title: Microsoft kommersiell marknads introduktions hantering med HTTPS
description: Konfigurera Microsoft kommersiell marknads introduktions hantering för en HTTPS-slutpunkt.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 1c3337e970fdbb22cb1ed88f105d5e7798a68f74
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2020
ms.locfileid: "82133740"
---
# <a name="configure-lead-management-by-using-an-https-endpoint"></a>Konfigurera hantering av leads med hjälp av en HTTPS-slutpunkt

>[!NOTE]
>Den Energis par anslutning som används i dessa instruktioner kräver en betald prenumeration för att automatisera energi. Se till att du tar hänsyn till detta innan du följer anvisningarna i den här artikeln.

Om ditt CRM-system (Customer Relations hip Management) inte uttryckligen stöds i Partner Center för att ta emot Microsoft AppSource och Azure Marketplace-leads, kan du använda en HTTPS-slutpunkt i energi automatisering för att hantera dessa leads. Med en HTTPS-slutpunkt kan de här leads skickas som ett e-postmeddelande eller de kan skrivas till ett CRM-system som stöds av Power automatisering. Anvisningarna i den här artikeln vägleder dig genom Basic-processen för att skapa ett nytt flöde med hjälp av Power gener, vilket genererar http post-URL: en som du ska ange i publicerings portalen för**https-slutpunkts-URL: en** för **hantering** > av https Här finns också anvisningar om hur du testar ditt flöde med hjälp av ett verktyg som kallas [Postman](https://www.getpostman.com/downloads/), som är tillgängligt online.

## <a name="create-a-flow-by-using-power-automate"></a>Skapa ett flöde med hjälp av energi automatisering

1. Öppna webb sidan [automatiserad start](https://flow.microsoft.com/) . Välj **Logga in**. Om du inte redan har ett konto väljer du **Registrera dig kostnads fritt** för att skapa ett kostnads fritt energi automatiserat konto.

1. Logga in och välj **mina flöden** i menyn.

1. Välj **+ automatiserad – från Tom**.

    ![Mina flöden + automatiserad – från Tom](./media/commercial-marketplace-lead-management-instructions-https/my-flows-automated.png)

1. I fönstret **Bygg ett automatiserat flöde** väljer du **hoppa över**. 

    ![Knappen för att sätta upp en automatisk flödes fönster](./media/commercial-marketplace-lead-management-instructions-https/build-automated-flow.png)

1. I fältet **Sök anslutningar och utlösare** anger du **begäran** om att hitta begär ande anslutningen.
1. Under **utlösare**väljer du **när en http-begäran tas emot**. 

    ![Menyn utlösare](./media/commercial-marketplace-lead-management-instructions-https/request-connector.png)

1. I fönstret **när en HTTP-förfrågan tas emot** kopierar du och klistrar in följande JSON-schema i text rutan **JSON-schema för begär ande** text. Det här schemat används av Microsoft för att innehålla dina lead-data.

    ![Text ruta för JSON-schema för begär ande text](./media/commercial-marketplace-lead-management-instructions-https/https-request-received.png)

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

>[!NOTE]
>I det här läget i konfigurationen kan du välja att antingen ansluta till ett CRM-system eller konfigurera ett e-postmeddelande. Följ de återstående instruktionerna som du väljer.

### <a name="connect-to-a-crm-system"></a>Ansluta till ett CRM-system

1. Välj **+ Nytt steg**.
1. Välj önskat CRM-system genom att söka efter det där det står **Sök anslutningar och åtgärder**. Välj den på fliken **åtgärder** med åtgärden för att skapa en ny post. Följande skärm bild visar hur du **skapar en ny post (Dynamics 365)** som exempel.

    ![Skapa en ny post](./media/commercial-marketplace-lead-management-instructions-https/create-new-record.png)

1. Ange **organisations namnet** som är kopplat till CRM-systemet. Välj **leads** i list rutan **entitetsnamn** .

    ![Välj leads](./media/commercial-marketplace-lead-management-instructions-https/select-leads.png)

1. Energis par visar ett formulär för att tillhandahålla lead-information. Du kan mappa objekt från Indataporten genom att välja att lägga till dynamiskt innehåll. Följande skärm bild visar **OfferTitle** som ett exempel.

    ![Lägg till dynamiskt innehåll](./media/commercial-marketplace-lead-management-instructions-https/add-dynamic-content.png)

1. Mappa de fält som du vill använda och välj sedan **Spara** för att spara ditt flöde. En HTTP POST-URL skapas och är tillgänglig i fönstret **när en HTTP-förfrågan tas emot** . Kopiera den här URL: en med hjälp av kopierings kontrollen som finns till höger om HTTP POST-URL: en. Att använda kopierings kontrollen är viktigt så att du inte saknar någon del av hela URL: en. Spara den här URL: en eftersom du behöver den när du konfigurerar hantering av leads i publicerings portalen.

    ![När en HTTP-begäran tas emot](./media/commercial-marketplace-lead-management-instructions-https/when-http-request-received.png)

### <a name="set-up-email-notification"></a>Konfigurera e-postavisering

1. Nu när du har slutfört JSON-schemat väljer du **+ nytt steg**.
1. Under **Välj en åtgärd**väljer du **åtgärder**.
1. På fliken **åtgärder** väljer du **Skicka ett e-postmeddelande (Office 365 Outlook)**.

    >[!NOTE]
    >Om du vill använda en annan e-postprovider söker du efter och väljer **Skicka ett e-postmeddelande (e-post)** som åtgärd i stället.

    ![Lägg till en e-poståtgärd](./media/commercial-marketplace-lead-management-instructions-https/https-request-received-send-email.png)

1. I fönstret **Skicka ett e-postmeddelande** konfigurerar du följande obligatoriska fält:

   - **Till**: ange minst en giltig e-postadress dit leads ska skickas.
   - **Subject**: med energi alternativ får du möjlighet att lägga till dynamiskt innehåll, t. ex. **LeadSource** som visas på följande skärm bild. Börja med att ange ett fält namn. Välj sedan listan över dynamiskt innehåll i listan från popup-fönstret. 

        >[!NOTE] 
        > När du lägger till fält namn kan du följa varje namn med ett kolon (:) och välj sedan **RETUR** för att skapa en ny rad. När du har lagt till dina fält namn kan du lägga till varje associerad parameter från den dynamiska plock listan.

        ![Lägg till en e-poståtgärd med hjälp av dynamiskt innehåll](./media/commercial-marketplace-lead-management-instructions-https/add-email-using-dynamic-content.png)

   - **Brödtext**: Lägg till den information som du vill ha i e-postmeddelandets brödtext i listan med dynamiskt innehåll. Använd till exempel LastName, FirstName, E-mail och Company. När du är klar med att konfigurera e-postmeddelandet ser det ut som exemplet på följande skärm bild.


       ![Exempel på e-postavisering](./media/commercial-marketplace-lead-management-instructions-https/send-an-email.png)

1. Välj **Spara** för att slutföra ditt flöde. En HTTP POST-URL skapas och är tillgänglig i fönstret **när en HTTP-förfrågan tas emot** . Kopiera den här URL: en med hjälp av kopierings kontrollen som finns till höger om HTTP POST-URL: en. Att använda den här kontrollen är viktigt så att du inte saknar någon del av hela URL: en. Spara den här URL: en eftersom du behöver den när du konfigurerar hantering av leads i publicerings portalen.

   ![HTTP POST-URL](./media/commercial-marketplace-lead-management-instructions-https/http-post-url.png)

### <a name="testing"></a>Testning

Du kan testa att allt fungerar som förväntat genom att använda ett verktyg som kallas [Postman](https://app.getpostman.com/app/download/win64), som kan hämtas online. Det här verktyget är tillgängligt för Windows. 

1. Starta Postman och välj **ny** > **begäran** för att ställa in ditt test verktyg. 

   ![Förfrågan om att ställa in ditt test verktyg](./media/commercial-marketplace-lead-management-instructions-https/postman-request.png)

1. Fyll i formuläret **Spara begäran** och spara sedan i den mapp som du skapade.

   ![Formulär för att spara begäran](./media/commercial-marketplace-lead-management-instructions-https/postman-save-to-test.png)

1. Välj **post** från den nedrullningsbara listan. 

   ![Testa mitt flöde](./media/commercial-marketplace-lead-management-instructions-https/test-my-flow.png)

1. Klistra in HTTP POST-URL: en från flödet som du skapade i energi spar läge där det står **Retur-URL**.

   ![Klistra in URL: en för HTTP-POST](./media/commercial-marketplace-lead-management-instructions-https/paste-http-post-url.png)

1. Gå tillbaka till [Energis par automatisering](https://flow.microsoft.com/). Hitta det flöde som du skapade för att skicka leads genom att gå till **mina flöden** från meny raden för att automatisera snabb start. Välj ellipsen bredvid flödes namnet för att se fler alternativ och välj **Redigera**.


1. Välj **test** i det övre högra hörnet, Välj **Jag vill utföra utlösnings åtgärden**och välj sedan **testa**. Du ser en indikering överst på skärmen som testet har startat.

   ![Jag utför alternativet utlösnings åtgärd](./media/commercial-marketplace-lead-management-instructions-https/test-flow-trigger-action.png)

1. Gå tillbaka till din Postman-app och välj **Skicka**.

   ![Knappen Skicka](./media/commercial-marketplace-lead-management-instructions-https/postman-send.png)

1. Gå tillbaka till ditt flöde och kontrol lera resultatet. Om allt fungerar som förväntat visas ett meddelande som anger att flödet lyckades.

   ![Kontrol lera resultat](./media/commercial-marketplace-lead-management-instructions-https/my-flow-check-results.png)

1. Du bör också ha fått ett e-postmeddelande. Kontrol lera din e-postinkorg. 

    >[!NOTE] 
    >Om du inte ser ett e-postmeddelande från testet kontrollerar du mapparna spam och skräp post. I följande skärm ser du bara fält etiketterna som du lade till när du konfigurerade e-postmeddelandet. Om detta var ett faktiskt lead som genererats från ditt erbjudande, skulle du också se den faktiska informationen från lead-kontakten i bröd texten och på ämnes raden.

   ![E-post mottagen](./media/commercial-marketplace-lead-management-instructions-https/email-received.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>Konfigurera ditt erbjudande för att skicka leads till HTTPS-slutpunkten

När du är redo att konfigurera ledar hanterings informationen för ditt erbjudande i publicerings portalen följer du de här stegen.

1. Gå till installations sidan för **erbjudandet** för ditt erbjudande.
1. Välj **Anslut** under avsnittet **ledar hantering** .
1. I popup-fönstret **anslutnings information** väljer du https- **slutpunkt** för lead- **målet**. Klistra in HTTP POST-URL: en från flödet som du skapade genom att följa tidigare steg i URL-fältet för **https-slutpunkt** .
1. Under **Kontakta e-postadress**anger du e-postadresser för personer i företaget som ska få e-postaviseringar när ett nytt lead tas emot. Du kan ange flera e-postmeddelanden genom att avgränsa dem med semikolon.
1. Välj **OK**.

Om du vill kontrol lera att du har anslutit till ett lead-mål väljer du knappen **Verifiera** . Om det lyckas har du ett test lead i lead-målet.

>[!NOTE] 
>Du måste slutföra konfigurationen av resten av erbjudandet och publicera den innan du kan ta emot leads för erbjudandet.

När leads genereras skickar Microsoft leads till flödet. Leads dirigeras till CRM-systemet eller e-postadressen som du har konfigurerat.

![Knappen Anslut till ledar hantering](./media/commercial-marketplace-lead-management-instructions-https/lead-management-connect.png)

![Anslutnings information för lead-destination](./media/commercial-marketplace-lead-management-instructions-https/connection-details.png)

![Anslutnings information kontakta e-post](./media/commercial-marketplace-lead-management-instructions-https/https-connection-details.png)

