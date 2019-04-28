---
title: Konfigurera kundleads | Microsoft Docs
description: Konfigurera kundleads i partnerportalen i molnet.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pbutlerm
ms.openlocfilehash: 2a425e607ea7dac394ab90a3fed4d4026056bbc1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61069633"
---
<a name="get-customer-leads"></a>Hämta kundleads
==================

Den här artikeln beskriver hur du skapar kundleads med partnerportalen i molnet. Du kan ansluta dessa leads till CRM-systemet och integrera dem i din Säljpipeline.

## <a name="leads"></a>Leads

Leads är kunder som vill eller distribuerar dina produkter från den [Azure Marketplace](https://azuremarketplace.microsoft.com/) eller från [AppSource](https://appsource.microsoft.com).

### <a name="azure-marketplace"></a>Azure Marketplace

1.  Kunden tar en ”Test Drive” i ditt erbjudande. Test Drive är en snabbare möjlighet att dela din verksamhet direkt med potentiella kunder utan några hinder för den posten. Alla Provkörningar genererar ett lead för en kund som är intresserad av att försöka din produkt mer information. Mer information om Test Drives på [Azure Marketplace Test Drive](https://azuremarketplace.azureedge.net/documents/azure-marketplace-test-drive-program.pdf).

    ![Marketplace test drive-exempel](./media/cloud-partner-portal-get-customer-leads/test-drive-offer.png)
 

<!-- -->

1. Kunden samtycker till att dela sin information när du har valt ”Hämta nu”. Det här är en **inledande intresse** ledare, där vi delar information om kunder som har visat intresse vid hämtning av din produkt. Det är upp förvärv tratten.

   ![Hämta den nu alternativ](./media/cloud-partner-portal-get-customer-leads/get-it-now-button.png)

1. Kunden väljer ”köp” i den [Azure-portalen](https://portal.azure.com/) att hämta din produkt. Det här är en **active** ledare, där vi delar information om en kund som har startats för att distribuera din produkt.

   ![Köpalternativ](./media/cloud-partner-portal-get-customer-leads/purchase-button.png)


### <a name="appsource"></a>AppSource

1.  Kunden tog ”testa” för ditt erbjudande. Test Drive är en snabbare möjlighet att dela din verksamhet direkt med potentiella kunder utan några hinder för den posten. Alla Provkörningar genererar ett lead på en kund som är intresserad av skulle din produkt mer information. Mer information om Test Drives på [AppSource Test Drive](https://appsource.microsoft.com/blogs/want-to-try-an-app-take-a-test-drive).

    ![Test drive-exempel](./media/cloud-partner-portal-get-customer-leads/test-drive-offer-2.png)

2.  Kunden samtycker till att dela sin information när du har valt ”Hämta nu”. Det här är en **inledande intresse** ledare, där vi delar information om kund som visar hur du hämtar din produkt. Det är upp förvärv tratten.

      ![Hämta den nu alternativ](./media/cloud-partner-portal-get-customer-leads/get-it-now-button-2.png)


3.  Kunden väljer ”kontakta mig” på ditt erbjudande. Det här är en **active** ledare, där vi delar information om en kund som ber om följas om produkten.

    ![Kontakta mig alternativet](./media/cloud-partner-portal-get-customer-leads/contact-me-image.png)

<a name="lead-data"></a>Lead-Data
---------

Olika potentiella kunderna under förvärvsprocess kunden har data i specifika fält. Eftersom du får leads från flera steg, är det bästa sättet att hantera de potentiella kunderna att ta bort duplicerad och anpassa uppföljningar. Det här sättet varje kund får ett meddelande visas och du skapar ett unikt samarbete.

### <a name="lead-source"></a>Lead-källa

Formatet för en leadkälla är **källa**-**åtgärd** |  **erbjuder**

**Källor**: ”AzureMarketplace”, ”Granskningsrapporteringsdata”, ”TestDrive” och ”AppSource (SPZA)”

**Åtgärder**:
- ”Moduler”--installationen. Den här åtgärden finns på Azure Marketplace eller AppSource när en kund köper din produkt.
- ”PLT”--står för Partner ledde utvärderingsversion. Den här åtgärden är på AppSource när en kund använder kontakten mig alternativ.
- "DNC" -- Do Not Contact. Den här åtgärden är på AppSource när en Partner som har flera visas på sidan med appen hämtar begärde att kontaktas. Vi delar nytta av att kunden har mellan visas på din app, men de behöver inte kontaktas.
- ”Skapa” – den här åtgärden är endast i Azure Portal och genereras när en kund köper ditt erbjudande till sitt konto.
- ”StartTestDrive”--åtgärden avser endast för att testa enheter och genereras när en kund startar sin test-enhet.

**Erbjudanden**

I följande exempel visas unika identifierare som tilldelas till en utgivare och ett specifikt erbjudande: checkpoint.check-punkt-r77-10sg-byol och bitnami.openedxcypress docusign.3701c77e-1cfa - 4c 56-91e6-3ed0b622145a.


### <a name="customer-info"></a>Kundinformation

Fälten i följande exempel visas den kundinformation som ingår i ett lead.
- Förnamn: John
- Efternamn: Smith
- E-postadress: jsmith\@microsoft.com
- Telefon: 1234567890
- Land/region: USA
- Företag: Microsoft
- Rubrik: CTO

>[!Note]
>Inte alla data i föregående exempel är alltid tillgänglig för olika potentiella kunderna.

Vi arbetar aktivt med att förbättra leads, så om det finns ett datafält som du inte ser här men vill ha mer [Skicka gärna feedback](mailto:AzureMarketOnboard@microsoft.com).

<a name="how-to-connect-your-crm-system-with-the-cloud-partner-portal"></a>Hur du ansluter din CRM-system med partnerportalen i molnet
------------------------------------------------------------

Om du vill börja få leads, har vi byggt våra leda Management-anslutningstjänsten på partnerportalen i molnet så att du enkelt kan ansluta din CRM-information och vi gör anslutningen åt dig. Du kan nu enkelt utnyttja leads som skapades av marketplace utan en betydande engineering ansträngning att integrera med ett externt system.

![Leda management-anslutningstjänsten](./media/cloud-partner-portal-get-customer-leads/lead-management-connector.png)

Vi kan skriva leads i en mängd olika CRM-system eller direkt till Azure Storage-tabell där du kan hantera de potentiella kunderna men du vill ha. Var och en av följande länkar ger instruktioner för att ansluta till möjliga lead mål:

-   [Dynamics CRM Online](./cloud-partner-portal-lead-management-instructions-dynamics.md) att hämta anvisningarna om hur du konfigurerar Dynamics CRM Online för att få leads.
-   [Marketo](./cloud-partner-portal-lead-management-instructions-marketo.md) här för instruktioner för att konfigurera Marketo leda konfiguration för att få leads.
-    [Salesforce](./cloud-partner-portal-lead-management-instructions-salesforce.md) här för instruktioner för att konfigurera din Salesforce-instans för att få leads.
-    [Azure-tabell](./cloud-partner-portal-lead-management-instructions-azure-table.md) här för instruktioner för hur du konfigurerar ditt Azure storage-konto för att få leads i en Azure-tabell.
-   [Https-slutpunkt](./cloud-partner-portal-lead-management-instructions-https.md) här för instruktioner för att konfigurera Https-slutpunkt för att få leads.

När du konfigurerar ditt lead-mål och publicera ditt erbjudande, vi verifiera anslutningen och skickar dig ett test-lead. Du kan också testa din lead-anslutning genom att hämta erbjudandet själv i förhandsversionsmiljön när du visar erbjudandet innan du går vidare live. Det är viktigt att se till att dina lead inställningar Håll dig uppdaterad så att du inte förlorar alla leads, så se till att du uppdatera dessa anslutningar när något har ändrats från din sida.

<a name="what-next"></a>Vad är nästa steg?
----------

När de tekniska konfigurera är på plats, ska du införliva dessa leads i din aktuella Försäljning & marknadsföringsstrategi och driftrutiner. Vi är väldigt intresserade bättre förstå din övergripande försäljning bearbeta och vill samarbeta med dig på att tillhandahålla hög kvalitet leads och tillräckligt med data för att du ska lyckas. Vi uppskattar din feedback på hur vi kan optimera och förbättra leads som vi skickar dig med ytterligare data för att göra dessa kunder lyckas. Kontakta oss gärna om du är intresserad av [ge feedback](mailto:AzureMarketOnboard@microsoft.com) och förslag för att aktivera försäljningsteamet ska lyckas med Marketplace-Leads.
