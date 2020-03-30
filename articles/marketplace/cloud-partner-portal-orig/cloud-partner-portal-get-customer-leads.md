---
title: Konfigurera kundleads | Azure Marketplace
description: Konfigurera kundleads i Cloud Partner Portal.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: dsindona
ms.openlocfilehash: 56012fb2a907a6db6f87554660ee36b99a3dcbf9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280328"
---
<a name="get-customer-leads"></a>Hämta kundleads
==================

I den här artikeln beskrivs hur du skapar kundleads med hjälp av Cloud Partner Portal. Du kan ansluta dessa leder till ditt CRM-system, och integrera dem i din försäljning pipeline.

## <a name="leads"></a>Leads

Leads är kunder som är intresserade av eller distribuerar dina produkter från [Azure Marketplace](https://azuremarketplace.microsoft.com/) eller från [AppSource](https://appsource.microsoft.com).

### <a name="azure-marketplace"></a>Azure Marketplace

1.  Kunden tar en "Provkörning" av ditt erbjudande. Test drives är en accelererad möjlighet för dig att dela din verksamhet direkt med potentiella kunder utan några hinder för inträde. Alla testenheter genererar ett lead för en kund som är intresserad av att prova din produkt för att lära sig mer. Läs mer om testenheter på [Azure Marketplace Test Drive](https://azuremarketplace.azureedge.net/documents/azure-marketplace-test-drive-program.pdf).

    ![Exempel på Marketplace-provkörning](./media/cloud-partner-portal-get-customer-leads/test-drive-offer.png)
 

<!-- -->

1. Kunden samtycker till att dela sin information efter att ha valt "Hämta den nu". Detta lead är en **initial intresse** lead, där vi delar information om kund som har uttryckt intresse för att få din produkt. Ledningen är toppen av förvärvet tratten.

   ![Få det nu alternativ](./media/cloud-partner-portal-get-customer-leads/get-it-now-button.png)

1. Kunden väljer "Köp" i [Azure Portal](https://portal.azure.com/) för att hämta din produkt. Detta lead är ett **aktivt** lead, där vi delar information om en kund som har börjat distribuera din produkt.

   ![Köpoption](./media/cloud-partner-portal-get-customer-leads/purchase-button.png)


### <a name="appsource"></a>AppSource

1.  Kunden tog en "Test Drive" för ditt erbjudande. Test drives är en accelererad möjlighet för dig att dela din verksamhet direkt med potentiella kunder utan några hinder för inträde. Alla testenheter genererar ett lead för en kund som är intresserad av att prova din produkt för att lära sig mer. Läs mer om testenheter på [AppSource Test Drive](https://appsource.microsoft.com/blogs/want-to-try-an-app-take-a-test-drive).

    ![Exempel på provkörning](./media/cloud-partner-portal-get-customer-leads/test-drive-offer-2.png)

2.  Kunden samtycker till att dela sin information efter att ha valt "Hämta den nu". Detta lead är en **första intresse** lead, där vi delar information om kund som uttrycker intresse för att få din produkt. Ledningen är toppen av förvärvet tratten.

      ![Få det nu alternativ](./media/cloud-partner-portal-get-customer-leads/get-it-now-button-2.png)


3.  Kunden väljer "Kontakta mig" på ditt erbjudande. Detta lead är ett **aktivt** lead, där vi delar information om en kund som ber om att bli uppföljd med om din produkt.

    ![Kontakta mig alternativet](./media/cloud-partner-portal-get-customer-leads/contact-me-image.png)

<a name="lead-data"></a>Leaddata
---------

Varje lead som du får under kundinhämtningsprocessen har data i specifika fält. Eftersom du får leads från flera steg är det bästa sättet att hantera leadsen att avdubblett och anpassa uppföljningarna. På så sätt får varje kund ett lämpligt meddelande och du skapar en unik relation.

### <a name="lead-source"></a>Blykälla

Formatet för en leadkälla är **Source**-**Action** |  **Offer**

**Källor**: "AzureMarketplace", "AzurePortal", "TestDrive" och "AppSource (SPZA)"

**Åtgärder**:
- "INS" - Installation. Den här åtgärden finns på Azure Marketplace eller AppSource när en kund köper din produkt.
- "PLT" - Står för Partner Led Trial. Den här åtgärden finns på AppSource när en kund använder alternativet Kontakta mig.
- "DNC" - Kontakta inte. Den här åtgärden finns på AppSource när en partner som korslistades på din appsida uppmanas att kontaktas. Vi delar förvarningen att den här kunden har korslistats i din app, men de behöver inte kontaktas.
- "Skapa" – Den här åtgärden finns bara i Azure Portal och genereras när en kund köper ditt erbjudande till sitt konto.
- "StartTestDrive" – Den här åtgärden är endast till för testenheter och genereras när en kund startar sin provkörning.

**Erbjudanden**

Följande exempel visar unika identifierare som har tilldelats en utgivare och ett specifikt erbjudande: checkpoint.check-point-r77-10sg-byol, bitnami.openedxcypress och docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145a.


### <a name="customer-info"></a>Information om kunder

Fälten i följande exempel visar kundinformationen som finns i ett lead.
- Förnamn: John
- Efternamn: Svensson
- E-post:\@jsmith microsoft.com
- Telefon: 1234567890
- Land: USA
- Företag: Microsoft
- Titel: CTO

>[!Note]
>Alla data i föregående exempel är inte alltid tillgängliga för varje lead.

Vi arbetar aktivt med att förbättra leads, så om det finns ett datafält som du inte ser här men vill ha, [vänligen skicka oss din feedback.](mailto:AzureMarketOnboard@microsoft.com)

<a name="how-to-connect-your-crm-system-with-the-cloud-partner-portal"></a>Så här ansluter du ditt CRM-system till Cloud Partner Portal
------------------------------------------------------------

För att börja få leads har vi byggt vår Lead Management-kontakt på Cloud Partner Portal så att du enkelt kan ansluta din CRM-information, och vi kommer att göra anslutningen åt dig. Nu kan du enkelt utnyttja de leads som genereras av marknaden utan en betydande teknisk ansträngning att integrera med ett externt system.

![Anslutning för leadhantering](./media/cloud-partner-portal-get-customer-leads/lead-management-connector.png)

Vi kan skriva leads i en mängd olika CRM-system eller direkt till en Azure Storage Table där du kan hantera leads hur du vill. Var och en av följande länkar innehåller instruktioner för anslutning till möjliga leaddestinationer:

-   [Dynamics CRM Online](./cloud-partner-portal-lead-management-instructions-dynamics.md) för att få instruktioner om hur du konfigurerar Dynamics CRM Online för att få leads.
-   [Marketo](./cloud-partner-portal-lead-management-instructions-marketo.md) att få instruktioner för att inrätta Marketo Lead Configuration för att få leads.
-    [Salesforce](./cloud-partner-portal-lead-management-instructions-salesforce.md) för att få instruktioner för hur du konfigurerar din Salesforce-instans för att få leads.
-    [Azure Table](./cloud-partner-portal-lead-management-instructions-azure-table.md) för att få instruktioner för hur du konfigurerar ditt Azure-lagringskonto för att få leads i en Azure-tabell.
-   [Https Slutpunkt](./cloud-partner-portal-lead-management-instructions-https.md) för att få instruktioner för hur du konfigurerar din Https-slutpunkt för att få leads.

När du har konfigurerat leaddestinationen och publicerat ditt erbjudande validerar vi anslutningen och skickar en testledare. När du visar erbjudandet innan du sänder live kan du också testa din leadanslutning genom att försöka skaffa erbjudandet själv i förhandsversionen. Det är viktigt att se till att dina leadinställningar förblir uppdaterade så att du inte förlorar några leads, så se till att uppdatera dessa anslutningar när något har ändrats på din sida.

<a name="what-next"></a>Vad händer nu?
----------

När den tekniska uppsättningen är på plats, bör du införliva dessa leder i din nuvarande försäljning & marknadsföringsstrategi och operativa processer. Vi är mycket intresserade av att bättre förstå din övergripande försäljningsprocess och vill arbeta nära dig på att ge högkvalitativa leads och tillräckligt med data för att göra dig framgångsrik. Vi välkomnar din feedback på hur vi kan optimera och förbättra de leads vi skickar dig med ytterligare data för att hjälpa till att göra dessa kunder framgångsrika. Meddela oss om du är intresserad av att [ge feedback](mailto:AzureMarketOnboard@microsoft.com) och förslag för att ditt säljteam ska bli mer framgångsrikt med Marketplace Leads.
