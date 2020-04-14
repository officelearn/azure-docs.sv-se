---
title: Konfigurera leadhantering i Marketo | Azure Marketplace
description: Konfigurera leadhantering för Marketo för Azure marketplace-kunder.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 04eae529efcf9509d8cd43a7629bb0b76b593cd0
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81252507"
---
# <a name="configure-lead-management-in-marketo"></a>Konfigurera leadhantering i Marketo

I den här artikeln beskrivs hur du ställer in ditt Marketo CRM-system för att bearbeta försäljningsleads från ditt marknadsplatserbjudande.

## <a name="set-up-your-marketo-crm-system"></a>Konfigurera ditt Marketo CRM-system

1. Logga in på Marketo.
2. Välj **Design Studio**.
    ![Marketo Design Studio](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-1.png)

3.  Välj **Nytt formulär**.
    ![Marketo ny form](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-2.png)

4.  Fyll i de obligatoriska fälten i det nya formuläret och välj sedan **Skapa**.
    ![Marketo skapa ny form](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-3.png)

5.  Välj **Slutför**på Fältinformation .
    ![Marketo-slutformulär](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-4.png)

6.  Godkänn och stäng.

7. Välj **Bädda in kod**på fliken *MarketplaceLeadBacked* . 

    ![Bädda in kod](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-6.png)

8. Marketo Embed Code visar kod som liknar följande exempel.

    ```
    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>
    ```

9. Kopiera värdena för fälten nedan som visas i formuläret Bädda in kod. Du kommer att använda dessa värden för att konfigurera erbjudandet för att ta emot leads i nästa steg. Använd nästa exempel som en guide för att få de ID:er du behöver från exemplet Marketo Embed Code.

    - Server-ID = **ys12**
    - Munchkin ID = **123-PQR-789**
    - Formulär-ID = **1179**

    **Ett annat sätt att räkna ut dessa värden**

    - Server-ID finns i URL:en för din Marketo-instans, till exempel " ".`serverID.marketo.com`
    - Få din prenumerations Munching ID genom att gå till din Admin>Munchkin menyn i "Munchkin Konto-ID" fält, eller `https://{Munchkin ID}.mktorest.com`från den första delen av din Marketo REST API värd underdomän: .
    - Formulär-ID är ID för formuläret Bädda in kod som du skapade i steg 7 för att dirigera leads från vår marknadsplats.

## <a name="configure-your-offer-to-send-leads-to-marketo"></a>Konfigurera ditt erbjudande för att skicka leads till Marketo

När du är redo att konfigurera leadhanteringsinformationen för ditt erbjudande i publiceringsportalen följer du stegen nedan: 

1. Navigera till **inställningssidan** för Erbjudandet för ditt erbjudande.
1. Välj **Anslut** under avsnittet LeadHantering. 

    ![Leadhantering - Anslut](./media/commercial-marketplace-lead-management-instructions-marketo/lead-management-connect.png)

1. I popup-fönstret Anslutningsinformation väljer du **Marketo** för leaddestinationen.

    ![Välj en leaddestination](./media/commercial-marketplace-lead-management-instructions-marketo/choose-lead-destination.png)

4. Ange **server-ID,** **Mumskonto-ID**och **formulär-ID**.

    >[!Note]
    >Du måste slutföra konfigurationen av resten av erbjudandet och publicera det innan du kan ta emot leads för erbjudandet. 

5. **Kontakta e-post** - Ge e-post till personer i ditt företag som ska få e-postmeddelanden när ett nytt lead tas emot. Du kan tillhandahålla flera e-postmeddelanden genom att separera dem med ett semikolon.
6. Välj **OK**.

Om du vill vara säker på att du har anslutit till ett leadmål klickar du på knappen validera. Om det lyckas kommer du att ha en testledning i leaddestinationen.

![Anslutningsinformation](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-connection-details.png)