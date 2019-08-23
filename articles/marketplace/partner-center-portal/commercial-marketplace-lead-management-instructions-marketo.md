---
title: Konfigurera ledar hantering i Marketo | Azure Marketplace
description: Konfigurera ledar hantering för Marketo för Azure Marketplace-kunder.
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: evansma
ms.openlocfilehash: 9470a593f5db564689a0b26601a201874c215aa2
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2019
ms.locfileid: "69902096"
---
# <a name="configure-lead-management-in-marketo"></a>Konfigurera ledar hantering i Marketo

Den här artikeln beskriver hur du konfigurerar ett Marketo CRM-system för att bearbeta försäljnings leads från Marketplace-erbjudandet.

## <a name="set-up-your-marketo-crm-system"></a>Konfigurera ditt Marketo CRM-system

1. Logga in på Marketo.
2. Välj **Design Studio**.
    ![Marketo Design Studio](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-1.png)

3.  Välj **nytt formulär**.
    ![Marketo-nytt formulär](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-2.png)

4.  Fyll i de obligatoriska fälten i det nya formuläret och välj sedan **skapa**.
    ![Skapa nytt formulär på marknaden](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-3.png)

5.  På fält information väljer du **Slutför**.
    ![Slutför formulär för Marketo](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-4.png)

6.  Godkänn och Stäng.

7. På fliken *MarketplaceLeadBacked* väljer du **bädda in kod**. 

    ![Bädda in kod](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-6.png)

8. Marketo Inbäddnings kod visar kod som liknar följande exempel.

    ```
    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>
    ```

9. Kopiera värdena för fälten nedan som visas i Inbäddnings kod formuläret. Du kommer att använda dessa värden för att konfigurera erbjudandet att ta emot leads i nästa steg. Använd nästa exempel som en guide för att hämta de ID: n du behöver från Marketo embed-kod exemplet.

    - Server-ID = **ys12**
    - Munchkin-ID = **123-PQR-789**
    - Formulär-ID = **1179**

    **Ett annat sätt att räkna ut dessa värden**

    - Server-ID finns i URL: en för din Marketo-instans, till exempel "`serverID.marketo.com`".
    - Hämta din prenumerations munching-ID genom att gå till administratörs > Munchkin-menyn i fältet "Munchkin Account ID" eller från den första delen av Marketo REST API värd under domän: `https://{Munchkin ID}.mktorest.com`.
    - Formulär-ID är ID för Inbäddnings kod formuläret som du skapade i steg 7 för att dirigera leads från vår Marketplace.

## <a name="configure-your-offer-to-send-leads-to-marketo"></a>Konfigurera ditt erbjudande för att skicka leads till Marketo

När du är redo att konfigurera ledar hanterings informationen för ditt erbjudande i publicerings portalen följer du stegen nedan: 

1. Gå till sidan med **installations programmet** för erbjudandet.
1. Välj **Anslut** under avsnittet ledar hantering. 

    ![Lead-hantering – Anslut](./media/commercial-marketplace-lead-management-instructions-marketo/lead-management-connect.png)

1. Välj **Marketo** för lead-målet i popup-fönstret anslutnings information.

    ![Välj ett mål för lead](./media/commercial-marketplace-lead-management-instructions-marketo/choose-lead-destination.png)

4. Ange **Server-ID**, **munching-konto-ID**och **formulär-ID**.

    >[!Note]
    >Du måste slutföra konfigurationen av resten av erbjudandet och publicera den innan du kan ta emot leads för erbjudandet. 

