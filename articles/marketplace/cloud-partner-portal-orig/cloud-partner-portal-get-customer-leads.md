---
title: Konfigurera kund leads | Azure Marketplace
description: Konfigurera kund leads i Cloud Partner Portal.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pabutler
ms.openlocfilehash: 312e172321c25248f1b0801bdbccf71762319989
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818819"
---
<a name="get-customer-leads"></a>Hämta kundleads
==================

Den här artikeln förklarar hur du skapar kund leads med hjälp av Cloud Partner Portal. Du kan ansluta dessa leads till ditt CRM-system och integrera dem i din försäljnings pipeline.

## <a name="leads"></a>Potentiella

Leads är kunder som är intresserade av, eller som distribuerar dina produkter från [Azure Marketplace](https://azuremarketplace.microsoft.com/) eller från [AppSource](https://appsource.microsoft.com).

### <a name="azure-marketplace"></a>Azure Marketplace

1.  Kunden tar en "test enhet" av ditt erbjudande. Test enheter är en accelererad möjlighet att dela din verksamhet direkt med potentiella kunder utan hinder i registreringen. Alla test enheter genererar ett lead för en kund som är intresse rad av att prova produkten för att få mer information. Lär dig mer om test enheter på [Azure Marketplace Test Drive](https://azuremarketplace.azureedge.net/documents/azure-marketplace-test-drive-program.pdf).

    ![Exempel på Marketplace Test Drive](./media/cloud-partner-portal-get-customer-leads/test-drive-offer.png)
 

<!-- -->

1. Kunden samverkar med att dela sin information efter att ha valt "Hämta nu". Det här leadet är ett **inledande intresse** som leder till att vi delar information om kunder som har visat intresse för att få din produkt. Leadet ligger överst i förvärvs tratten.

   ![Alternativet Hämta nu](./media/cloud-partner-portal-get-customer-leads/get-it-now-button.png)

1. Kunden väljer "köp" i [Azure Portal](https://portal.azure.com/) för att hämta din produkt. Det här leadet är ett **aktivt** lead där vi delar information om en kund som har börjat distribuera produkten.

   ![Köp alternativ](./media/cloud-partner-portal-get-customer-leads/purchase-button.png)


### <a name="appsource"></a>AppSource

1.  Kunden tog en "test enhet" för ditt erbjudande. Test enheter är en accelererad möjlighet att dela din verksamhet direkt med potentiella kunder utan hinder i registreringen. Alla test enheter genererar ett lead från en kund som är intresserade av att prova produkten och läsa mer. Lär dig mer om test enheter på [AppSource Test Drive](https://appsource.microsoft.com/blogs/want-to-try-an-app-take-a-test-drive).

    ![Exempel på test enhet](./media/cloud-partner-portal-get-customer-leads/test-drive-offer-2.png)

2.  Kunden samverkar med att dela sin information efter att ha valt "Hämta nu". Det här leadet är ett **inledande intresse** som leder till att vi delar information om kunder som uttrycker intresse för att få ut din produkt. Leadet ligger överst i förvärvs tratten.

      ![Alternativet Hämta nu](./media/cloud-partner-portal-get-customer-leads/get-it-now-button-2.png)


3.  Kunden väljer "kontakta mig" på ditt erbjudande. Det här leadet är ett **aktivt** lead där vi delar information om en kund som ber att följa med om din produkt.

    ![Alternativet kontakta mig](./media/cloud-partner-portal-get-customer-leads/contact-me-image.png)

<a name="lead-data"></a>Lead-data
---------

Varje lead som du får under kund förvärvs processen har data i specifika fält. Eftersom du får leads från flera steg är det bästa sättet att hantera leads för att avduplicera och anpassa uppföljningen. På så sätt får varje kund ett lämpligt meddelande och du skapar en unik relation.

### <a name="lead-source"></a>Lead-källa

Formatet för en lead-källa är **käll**-**åtgärd** |  **erbjudande**

**Källor**: "AzureMarketplace", "AzurePortal", "testdrive" och "APPSOURCE (SPZA)"

**Åtgärder**:
- "INS"--installation. Den här åtgärden finns på Azure Marketplace eller AppSource när en kund köper din produkt.
- "PLT"--står för partner LED-utvärdering. Den här åtgärden är på AppSource när en kund använder alternativet kontakta mig.
- "DNC"--kontakta inte. Den här åtgärden finns på AppSource när en partner som var korsad på din app-sida uppmanas att kontaktas. Vi delar upp de huvuden som den här kunden hade korsat i din app, men de behöver inte kontaktas.
- "Skapa" – den här åtgärden finns bara i Azure-portalen och genereras när en kund köper ditt erbjudande till sitt konto.
- "StartTestDrive" – den här åtgärden gäller endast för test enheter och genereras när en kund startar sin test-enhet.

**Erbjudanden**

I följande exempel visas unika identifierare som har tilldelats till en utgivare och ett särskilt erbjudande: Checkpoint. Check-Point-R77-10sg-BYOL, Bitnami. openedxcypress och DocuSign. 3701c77e-1cfa-4c56-91e6-3ed0b622145a.


### <a name="customer-info"></a>Kund information

Fälten i följande exempel visar kund information som finns i ett lead.
- FirstName: John
- LastName: Smith
- E-post: jsmith\@microsoft.com
- Telefon: 1234567890
- Land: US
- Företag: Microsoft
- Rubrik: CTO

>[!Note]
>Alla data i föregående exempel är alltid tillgängliga för varje lead.

Vi arbetar aktivt med att förbättra leads, så om det finns ett data fält som du inte ser här, men som vill ha, kan du [skicka oss din feedback](mailto:AzureMarketOnboard@microsoft.com).

<a name="how-to-connect-your-crm-system-with-the-cloud-partner-portal"></a>Så här ansluter du ditt CRM-system med Cloud Partner Portal
------------------------------------------------------------

För att komma igång med leads har vi skapat vår ledar hanterings anslutning på den Cloud Partner Portal så att du enkelt kan ansluta din CRM-information så kommer vi att upprätta anslutningen åt dig. Nu kan du enkelt utnyttja de leads som genereras av Marketplace utan en betydande teknik ansträngning för att integrera med ett externt system.

![Anslutning till ledar hantering](./media/cloud-partner-portal-get-customer-leads/lead-management-connector.png)

Vi kan skriva leads i en mängd olika CRM-system eller direkt till en Azure Storage tabell där du kan hantera de leads som du vill. Var och en av följande länkar innehåller instruktioner för att ansluta till möjliga mål för mål:

-   [Dynamics CRM Online](./cloud-partner-portal-lead-management-instructions-dynamics.md) för att få instruktioner om hur du konfigurerar Dynamics CRM Online för att få leads.
-   [Marketo](./cloud-partner-portal-lead-management-instructions-marketo.md) för att få anvisningar om hur du konfigurerar Marketo-ledar konfiguration för att hämta leads.
-    [Salesforce](./cloud-partner-portal-lead-management-instructions-salesforce.md) för att få anvisningar om hur du konfigurerar din Salesforce-instans för att hämta leads.
-    [Azure-tabell](./cloud-partner-portal-lead-management-instructions-azure-table.md) för att få anvisningar om hur du konfigurerar ditt Azure Storage-konto för att hämta leads i en Azure-tabell.
-   [Https-slutpunkt](./cloud-partner-portal-lead-management-instructions-https.md) för att få anvisningar om hur du konfigurerar din HTTPS-slutpunkt för att hämta leads.

När du har konfigurerat ditt lead-mål och publicerat ditt erbjudande, validerar vi anslutningen och skickar ett test lead. När du visar erbjudandet innan du går live kan du också testa din lead-anslutning genom att försöka skaffa erbjudandet själv i för hands versionen. Det är viktigt att se till att dina lead-inställningar hålls uppdaterade så att du inte förlorar några leads, så se till att du uppdaterar anslutningarna när något har ändrats i slutet.

<a name="what-next"></a>Vad är nästa steg?
----------

När den tekniska inställningen är på plats bör du inkludera dessa leads i din aktuella försäljning & marknadsförings strategi och operativa processer. Vi är mycket intresserade av att bättre förstå din övergripande Sälj process och vill arbeta nära dig med att tillhandahålla högkvalitativa leads och tillräckligt med data för att lyckas. Vi välkomnar din feedback om hur vi kan optimera och förbättra de leads som vi skickar till dig med ytterligare data för att hjälpa kunderna att lyckas. Berätta för oss om du är intresse rad av att [ge feedback](mailto:AzureMarketOnboard@microsoft.com) och förslag för att göra det möjligt för ditt säljteam att bli mer framgångs rik med Marketplace-leads.
