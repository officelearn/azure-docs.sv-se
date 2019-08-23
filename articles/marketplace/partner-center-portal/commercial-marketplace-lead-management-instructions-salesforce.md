---
title: Konfigurera ledar hantering för Salesforce | Azure Marketplace
description: Konfigurera ledar hantering på Salesforce för Azure Marketplace-kunder.
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: evansma
ms.openlocfilehash: d941a7902dd374a931147bdaa8f015c46268ee8a
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2019
ms.locfileid: "69901992"
---
# <a name="configure-lead-management-for-salesforce"></a>Konfigurera ledar hantering för Salesforce

Den här artikeln beskriver hur du konfigurerar Salesforce-systemet för att bearbeta försäljnings leads från Marketplace-erbjudandet.

## <a name="set-up-your-salesforce-system"></a>Konfigurera ditt Salesforce-system

1. Logga in på Salesforce.
2. Om du använder Salesforce-belysningen.
    1. Välj **Konfigurera** från Salesforce start sida.
    ![Salesforce-installation](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-1.png)

    1. Från installations sidan navigerar du via den vänstra navigeringen till **plattforms verktyg – > funktions inställningar-> marknadsföring – > webb-till-lead**.
    ![Salesforce webb-till-lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-2.png)

3. Om du använder den klassiska Salesforce-miljön:
    1. Välj **Konfigurera** från Salesforce start sida.
    ![Klassisk Salesforce-installation](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-setup.png)

    1. Från installations sidan navigerar du via den vänstra navigeringen för att **skapa > anpassa-> leads-> webb-till-lead**.
    ![Salesforce-klassiskt webb-till-lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-web-to-lead.png)

Resten av anvisningarna är samma oavsett vilken Salesforce-upplevelse du använder.

4. På **sidan konfiguration av webb-till-lead**väljer du knappen **skapa webb-till-lead-formulär** .
5. Välj **skapa webb-till-lead-formulär**på **webb-till-plats-konfiguration**.
    ![Salesforce – webb-till-ledtid-konfiguration](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-3.png)

6. I **formuläret Skapa ett webb-till-lead**ser du till att `the Include reCAPTCHA in HTML` inställningen är omarkerad och väljer **generera**. 
    ![Salesforce – skapa ett webb-till-lead-formulär](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-4.png)

7. HTML-text visas. Sök efter texten "OID" och kopiera OID- **värdet** från HTML-texten (endast texten mellan citat tecken) och spara den. Du kommer att klistra in det här värdet i fältet organisations- **ID** på publicerings portalen.
    ![Salesforce – skapa ett webb-till-lead-formulär](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-5.png)

8. **Har**valts.

## <a name="configure-your-offer-to-send-leads-to-salesforce"></a>Konfigurera ditt erbjudande för att skicka leads till Salesforce

När du är redo att konfigurera ledar hanterings informationen för ditt erbjudande i publicerings portalen följer du stegen nedan:

1. Gå till sidan med **installations programmet** för erbjudandet.
1. Välj **Anslut** under avsnittet ledar hantering.
    ![Lead-hantering – Anslut](./media/commercial-marketplace-lead-management-instructions-salesforce/lead-management-connect.png)

1. I popup-fönstret anslutnings information väljer du **Salesforce** som **mål** för leadet och `oid` klistrar in formuläret från webb-till-lead som du skapade genom att följa tidigare steg i fältet organisations- **ID** .

1. Välj **Spara**. 

    >[!Note]
    >Du måste slutföra konfigurationen av resten av erbjudandet och publicera den innan du kan ta emot leads för erbjudandet.

    ![Anslutnings information – Välj ett mål för lead](./media/commercial-marketplace-lead-management-instructions-salesforce/choose-lead-destination.png)

    ![Anslutnings information – Välj ett mål för lead](./media/commercial-marketplace-lead-management-instructions-salesforce/connection-details.png)