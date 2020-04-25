---
title: Konfigurera ledar hantering för Salesforce | Azure Marketplace
description: Konfigurera ledar hantering på Salesforce för Azure Marketplace-kunder.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: e0fbb09370e198772b4fc485b3c0fe8a56da4226
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2020
ms.locfileid: "82133634"
---
# <a name="configure-lead-management-for-salesforce"></a>Konfigurera ledar hantering för Salesforce

Den här artikeln beskriver hur du konfigurerar ditt Salesforce-system för att bearbeta försäljnings leads från ditt kommersiella Marketplace-erbjudande.

> [!NOTE]
> Azure Marketplace har inte stöd för förifyllda listor, till exempel en lista över värden för fältet **land** . Se till att inga listor har kon figurer ATS innan du fortsätter. Alternativt kan du konfigurera en [https-slutpunkt](./commercial-marketplace-lead-management-instructions-https.md) eller en [Azure-tabell](./commercial-marketplace-lead-management-instructions-azure-table.md) för att ta emot leads.

## <a name="set-up-your-salesforce-system"></a>Konfigurera ditt Salesforce-system

1. Logga in på Salesforce.
1. Om du använder Salesforce-belysningen:
    1. Välj **Inställningar** på Start sidan för Salesforce.

       ![Salesforce-installation](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-1.png)

    1. På sidan **installation** går du till **plattforms verktyg** > **funktions inställningar** > **marknadsföring** > **webb-till-lead**.

        ![Salesforce webb-till-lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-2.png)

1. Om du använder den klassiska Salesforce-miljön:

    1. Välj **Inställningar** på Start sidan för Salesforce.

       ![Klassisk Salesforce-installation](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-setup.png)

    1. På sidan **installation** väljer du **skapa** > **Anpassa** > **leads** > **webb-till-lead**.

        ![Salesforce-klassiskt webb-till-lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-web-to-lead.png)

Resten av anvisningarna är desamma oavsett vilken Salesforce-upplevelse du använder.

1. På sidan **konfiguration av webb-till-lead** väljer du knappen **skapa webb-till-lead-formulär** .
1. Välj **skapa ett webb-till-lead-formulär**på **webb-till-plats-konfiguration**.

    ![Salesforce-konfiguration för webb-till-lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-3.png)

1. När du **skapar ett webb-till-lead-formulär**kontrollerar du `Include reCAPTCHA in HTML` att inställningen är avmarkerad och väljer **generera**.

    ![Salesforce skapa ett webb-till-lead-formulär](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-4.png)

1. HTML-text visas. Sök efter texten "OID" och kopiera **värdet "OID"** från HTML-texten (endast texten mellan citat tecken) och spara den. Du kommer att klistra in det här värdet i fältet **organisations-ID** på publicerings portalen.

    ![Salesforce skapa ett webb-till-lead-formulär som visar HTML-värdet "OID"](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-5.png)

1. Välj **avslutad**.

## <a name="configure-your-offer-to-send-leads-to-salesforce"></a>Konfigurera ditt erbjudande för att skicka leads till Salesforce

När du är redo att konfigurera ledar hanterings informationen för ditt erbjudande i publicerings portalen följer du de här stegen.

1. Gå till installations sidan för **erbjudandet** för ditt erbjudande.
1. Välj **Anslut** under avsnittet **ledar hantering** .

    ![Knappen Anslut till hanterings avsnitt för lead](./media/commercial-marketplace-lead-management-instructions-salesforce/lead-management-connect.png)

1. I popup-fönstret **anslutnings information** väljer du **Salesforce** som **lead-mål** och klistrar in `oid` värdet från det webb-till-lead-formulär som du skapade i fältet **organisations-ID** .

1. Under **Kontakta e-postadress**anger du e-postadresser för personer i företaget som ska få e-postaviseringar när ett nytt lead tas emot. Du kan ange flera e-postmeddelanden genom att avgränsa dem med semikolon.

1. Välj **OK**.

Om du vill kontrol lera att du har anslutit till ett lead-mål väljer du knappen **Verifiera** . Om det lyckas har du ett test lead i lead-målet.

>[!NOTE]
>Du måste slutföra konfigurationen av resten av erbjudandet och publicera den innan du kan ta emot leads för erbjudandet.

![Anslutnings information, popup-fönster Välj ett mål för lead-rutan](./media/commercial-marketplace-lead-management-instructions-salesforce/choose-lead-destination.png)

![Popup-fönster för anslutnings information verifiera kontaktens e-ruta](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-connection-details.png)
