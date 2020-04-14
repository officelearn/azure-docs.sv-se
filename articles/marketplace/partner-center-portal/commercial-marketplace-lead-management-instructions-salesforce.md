---
title: Konfigurera leadhantering för Salesforce | Azure Marketplace
description: Konfigurera leadhantering på Salesforce för Azure Marketplace-kunder.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 087cdafe8b819e4929e1608ed7e00be2c1169414
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81263102"
---
# <a name="configure-lead-management-for-salesforce"></a>Konfigurera leadhantering för Salesforce

I den här artikeln beskrivs hur du konfigurerar ditt Salesforce-system för att bearbeta försäljningsleads från ditt kommersiella marketplace-erbjudande.

> [!Note]
> Marketplace stöder inte förifyllda listor, till exempel en lista med värden för fältet **Land.** Kontrollera att det inte finns några listor konfigurerade innan du fortsätter. Du kan också konfigurera en [HTTPS-slutpunkt](./commercial-marketplace-lead-management-instructions-https.md) eller en [Azure-tabell](./commercial-marketplace-lead-management-instructions-azure-table.md) för att ta emot leads.

## <a name="set-up-your-salesforce-system"></a>Konfigurera ditt Salesforce-system

1. Logga in på Salesforce.
2. Om du använder Salesforce-ljusupplevelsen.
    1. Välj **Inställningar** från Salesforce-startsidan.
    ![Salesforce-inställning](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-1.png)

    1. Från sidan Installationsprogrammet navigerar du via den vänstra navigeringen till **plattformsverktyg->funktionsinställningar->Marknadsföring->Web-to-Lead**.

        ![Salesforce Web-to-Lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-2.png)

3. Om du använder Salesforce Classic-upplevelsen:
    1. Välj **Inställningar** från Salesforce-startsidan.
    ![Salesforce klassiska setup](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-setup.png)

    1. På sidan Installationsprogrammet navigerar du via den vänstra navigeringen till **Build ->Customize->Leads->Web-to-Lead**.

        ![Salesforce klassiska web-to-lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-web-to-lead.png)

Resten av instruktionerna är desamma oavsett vilken Salesforce-upplevelse du använder.

4. På **sidan Inställningar för webb-till-lead**väljer du knappen **Skapa webb-till-lead-formulär.**
5. Välj **Skapa webb-till-lead-formulär**i **webb-till-lead-formulär**.
    ![Salesforce - Installationsprogram för webb-till-lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-3.png)

6. Kontrollera att `the Include reCAPTCHA in HTML` inställningen är avmarkerad i formuläret Skapa ett **webb-till-lead-formulär**och välj **Generera**. 
    ![Salesforce - Skapa ett webb-till-lead-formulär](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-4.png)

7. Du kommer att presenteras med lite HTML-text. Sök efter texten "oid" och kopiera **oid-värdet** från HTML-texten (endast texten mellan citattecken) och spara den. Du klistrar in det här värdet i fältet **Organisationsidentifierare** på publiceringsportalen.
    ![Salesforce - Skapa ett webb-till-lead-formulär](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-5.png)

8. Markerad **Färdig**.

## <a name="configure-your-offer-to-send-leads-to-salesforce"></a>Konfigurera ditt erbjudande för att skicka leads till Salesforce

När du är redo att konfigurera leadhanteringsinformationen för ditt erbjudande i publiceringsportalen följer du stegen nedan:

1. Navigera till **inställningssidan** för Erbjudandet för ditt erbjudande.
1. Välj **Anslut** under avsnittet LeadHantering.
    ![Leadhantering - Anslut](./media/commercial-marketplace-lead-management-instructions-salesforce/lead-management-connect.png)

1. I popup-fönstret Anslutningsinformation väljer du **Salesforce** för **leadmålet** och klistrar in i formuläret `oid` från web-to-lead som du skapade genom att följa tidigare steg i fältet **Organisationsidentifierare.**

1. **Kontakta e-post** - Ge e-post till personer i ditt företag som ska få e-postmeddelanden när ett nytt lead tas emot. Du kan tillhandahålla flera e-postmeddelanden genom att separera dem med semikolon.

1. Välj **Ok**.

Om du vill vara säker på att du har anslutit till ett leadmål klickar du på knappen validera. Om det lyckas kommer du att ha en testledning i leaddestinationen.

>[!Note]
>Du måste slutföra konfigurationen av resten av erbjudandet och publicera det innan du kan ta emot leads för erbjudandet.

![Anslutningsinformation - Välj ett leadmål](./media/commercial-marketplace-lead-management-instructions-salesforce/choose-lead-destination.png)

![Anslutningsinformation - Välj ett leadmål](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-connection-details.png)
