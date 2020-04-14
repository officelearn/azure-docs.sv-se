---
title: Vanliga frågor och svar om övergången till Partner Center | Azure Marketplace
description: Den här artikeln tar upp vanliga frågor om migrering av erbjudanden från Cloud Partner Portal till Partner Center.
author: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: mingshen
ms.openlocfilehash: 672153eba4aa2b739b67694f939c4796b39ac4c6
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81274385"
---
# <a name="frequently-asked-questions-for-migrating-from-the-cloud-partner-portal-to-partner-center"></a>Vanliga frågor och svar om hur du migrerar från Cloud Partner Portal till Partner Center

Den här artikeln tar upp vanliga frågor om migrering av erbjudanden från Cloud Partner Portal till Partner Center.

## <a name="what-does-offer-migration-mean"></a>Vad betyder erbjudandemigrering?

Vi flyttar dina erbjudandedata från Cloud Partner Portal till Partner Center med ändringar i erbjudandepublicerings- och hanteringsupplevelsen.

| Område  | Ändringar  |
|-------|----------|
| **Publicering och erbjudandehantering erfarenhet** | Du får en förbättrad användarupplevelse med ett intuitivt gränssnitt i Partner Center. Mer information finns i [Vilka skillnader finns i Vilka skillnader finns mellan Partner Center och Cloud Partner Portal?](#what-are-the-differences-between-partner-center-and-the-cloud-partner-portal) |
| **Tillgänglighet för dina erbjudanden på marknaden** | Inga ändringar. Om ditt erbjudande är live på marknaden kommer det att fortsätta att vara live under och efter att migreringen har slutförts. |
| **Nya inköp och distributioner** | Inga ändringar. Dina kunder kommer att fortsätta att kunna köpa och distribuera dina erbjudanden utan avbrott. |
| **Utbetalningar** | Alla inköp och distributioner som sker under eller efter migreringen kommer att fortsätta att betalas ut till dig som vanligt. |
|**API-integreringar med befintliga [API:er för Cloud Partner Portal](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview)** | Befintliga Api:er för Cloud Partner Portal-api:erna kommer att fortsätta att stödjas efter migreringen och dina befintliga integreringar fortsätter att fungera. Mer information finns i [Kommer REST-API:erna för Cloud Partner Portal att stödjas efter migreringen?](#will-the-cloud-partner-portal-rest-apis-be-supported-post-migration) |
| | |

## <a name="can-i-still-access-the-cloud-partner-portal-and-manage-my-offers-during-migration"></a>Kan jag fortfarande komma åt Cloud Partner Portal och hantera mina erbjudanden under migreringen?

Erbjudanden kommer att migreras till Partner Center från och med 13 april under hela maj månad. Under den här tidsperioden kan du komma åt Cloud Partner Portal som vanligt, förutom den tid du erbjuder är schemalagd för migreringen.
Medan dina erbjudanden migreras har de statusen "Låst – flytta till Partner Center", vilket visas i följande skärmbild. Den här migreringsperioden bör vara mindre än 24 timmar. Under den här tiden kan du inte göra några uppdateringar av dina erbjudanden. Du får ett e-postmeddelande när vi har slutfört migreringen av dina erbjudanden.

:::image type="content" source="media/migration-faq/all-offers-1.png" alt-text="Illustrerar status för migrerade erbjudanden.":::

När dina erbjudanden har migrerats är de skrivskyddade **under en begränsad tid** i Cloud Partner Portal. Deras status visar "Flyttad till Partner Center" och innehåller en länk till ditt erbjudande i Partner Center, som visas i följande skärmdumpar. Från och med nu hanterar du uppdateringar av alla dina erbjudanden eller skapar nya erbjudanden exklusivt via Partner Center,

:::image type="content" source="media/migration-faq/all-offers-2.png" alt-text="Illustrerar meddelandet för erbjudanden som har migrerats till Partnercenter":::

:::image type="content" source="media/migration-faq/offer-has-moved.png" alt-text="Illustrerar cloud partnerportalsidan för ett migrerat erbjudande.":::

## <a name="how-will-i-create-new-offers"></a>Hur skapar jag nya erbjudanden?

Från Cloud Partner Portal kommer du att uppmanas att skapa nya erbjudanden i Partner Center

:::image type="content" source="media/migration-faq/create-new-offer-1.png" alt-text="Illustrerar menyn för att skapa ett nytt erbjudande i Cloud Partner Portal":::

När du har valt ett nytt erbjudande visas ett meddelande, till exempel följande.

:::image type="content" source="media/migration-faq/create-new-offer-2.png" alt-text="Illustrerar det mottagna meddelandet när du skapar ett nytt erbjudande i CPP":::

## <a name="do-i-need-to-create-a-new-account-to-manage-offers-in-partner-center"></a>Måste jag skapa ett nytt konto för att hantera erbjudanden i Partner Center?

Nej. Ditt underliggande konto bevaras och du bör redan hantera det i Partner Center. Det innebär att om du är en befintlig partner kan du använda dina befintliga Cloud Partner Portal-kontoautentiseringsuppgifter för att logga in på Partner Center efter migreringen. Endast erbjudanden och tillhörande hanteringsupplevelse flyttas från Cloud Partner Portal till Partner Center. Vi ber dig att inte skapa några nya konton eftersom dina erbjudanden inte kopplas till det nya kontot.

## <a name="i-see-a-message-in-the-cloud-partner-portal-to-activate-my-account-what-does-this-mean"></a>Jag ser ett meddelande i Cloud Partner Portal för att aktivera mitt konto, vad betyder det?

Vi behöver lite mer information från dig för att kunna migrera ditt konto till Partner Center på rätt sätt och göra det möjligt för dig att hantera dina erbjudanden i Partner Center när erbjudandemigreringen är klar. Mer information om kontoaktivering finns i [Kontomigrering från Cloud Partner Portal till Partner Center](https://docs.microsoft.com/azure/marketplace/partner-center-portal/account-migration-from-cpp-to-pc).

Vilka steg som krävs för att slutföra kontoaktiveringen varierar beroende på din kontoroll.

| Kontoroll | Aktiveringssteg |
|--------------|----------------|
|Ägare | Gå till sidan [Publisher-profil](https://cloudpartner.azure.com/#profile) i Cloud Partner Portal och klicka sedan på länken i banderollen för att aktivera. Du omdirigeras till Partner Center för att slutföra kontoaktiveringen. |
| Deltagare | Endast en användare i kontot med en ägarroll kan aktivera kontot. Kontakta dina kontoägare för att slutföra kontoaktiveringen. Dina kontoägare bör visas i bannermeddelandet. |
| | |

## <a name="im-having-trouble-logging-in-to-my-account-and-opening-a-support-ticket"></a>Jag har problem med att logga in på mitt konto och öppna en supportbiljett

Om du inte kan logga in på ditt konto kan du öppna en [supportbiljett](https://partner.microsoft.com/support/v2/?stage=1).

## <a name="where-can-i-find-documentation-on-the-new-partner-center-publishing-experience"></a>Var hittar jag dokumentation om den nya partnercenterpubliceringsupplevelsen?

Gå till dokumentationen för den [kommersiella marknadsplatsen](https://docs.microsoft.com/azure/marketplace/). Expandera sedan **Commercial Marketplace Portal i Partner Center**  > **Skapa ett nytt erbjudande** för att se hjälpavsnitten för att skapa varje typ av erbjudande.

:::image type="content" source="media/migration-faq/marketplace-help-topics.png" alt-text="Illustrerar hjälpavsnitten för Partner center":::

### <a name="what-are-the-differences-between-partner-center-and-the-cloud-partner-portal"></a>Vilka är skillnaderna mellan Partner Center och Cloud Partner Portal?

Du kanske märker följande skillnader mellan Cloud Partner Portal och Partner Center.

### <a name="modular-publishing-capabilities"></a>Modulära publiceringsfunktioner

Partner Center har ett modulärt publiceringsalternativ som gör att du kan välja de ändringar du vill publicera i stället för att alltid publicera alla uppdateringar samtidigt. Följande skärmbild visar till exempel att de enda ändringar som valts för att publiceras är ändringarna i **Egenskaper** och **Erbjudandelista**.

:::image type="content" source="media/migration-faq/review-and-publish-migration.png" alt-text="Illustrerar sidan Förhandsgranska och publicera":::

Uppdateringarna som du inte publicerar sparas som utkast. Fortsätt att använda din förhandsgranskning av erbjudandet för att verifiera ditt erbjudande innan du gör det live för allmänheten.

### <a name="rich-text-format"></a>RTF-format

Förbättra ditt erbjudande och planera beskrivning med rtf-redigeraren på sidan Erbjudandelista och listning.

:::image type="content" source="media/migration-faq/rich-text-editor-migration.png" alt-text="Illustrerar RTF-redigeraren":::

### <a name="enhanced-preview-options"></a>Förbättrade alternativ för förhandsversion

Partner center innehåller en [jämförelsefunktion](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#compare-changes-to-marketplace-offers) med förbättrade filtreringsalternativ. Detta ger dig möjlighet att jämföra med förhandsgranskning och live versioner av erbjudandet.

:::image type="content" source="media/migration-faq/enhanced-preview.png" alt-text="Illustrerar jämförelsefunktionen":::

### <a name="branding-and-navigation-changes"></a>Varumärkes- och navigeringsändringar

Du kommer att märka några varumärkesförändringar. Till exempel är "SKU: er" märkta som "Planer" i Partner Center.

:::image type="content" source="media/migration-faq/plan-overview-migration.png" alt-text="Illustrerar planöversikten.":::

Dessutom samlas den information som du använde för att tillhandahålla på **sidorna Marketplace** eller S**torefront Details** (Consulting Service, Power BI app) på Cloud Partner Portal på sidan **Erbjudandelista** i Partnercenter.

:::image type="content" source="media/migration-faq/offer-listing-migration.png" alt-text="Illustrerar sidan för erbjudandelistning.":::

Den information som du använde för att tillhandahålla SKU:er på en enda sida i Cloud Partner Portal kan nu samlas in på flera sidor i Partnercenter:

* Planera ställa in sida
* Sidan Planera listning
* Sidan Planera tillgänglighet
* Planera teknisk konfigurationssida, som visas i den här skärmbilden.

:::image type="content" source="media/migration-faq/tech-config-migration.png" alt-text="Illustrerar sidan Planera teknisk konfiguration.":::

Ditt erbjudande-ID visas nu i erbjudandets vänstra navigeringsfält.

:::image type="content" source="media/migration-faq/offer-id-offer-overview.png" alt-text="Illustrerar den vänstra navigeringsmenyn med erbjudande-ID.":::

### <a name="stop-selling-an-offer"></a>Sluta sälja ett erbjudande

Du kan begära att [sluta sälja ett erbjudande](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan) på marknadsplatsen direkt från Partner Center-portalen. Alternativet finns på sidan **Erbjudandeöversikt** för ditt erbjudande.

:::image type="content" source="media/migration-faq/stop-selling-migration.png" alt-text="Illustrerar sidan Erbjudandeöversikt med alternativet sluta sälja.":::

## <a name="what-pages-in-partner-center-correspond-to-pages-i-used-in-the-cloud-partner-portal"></a>Vilka sidor i Partnercenter motsvarar sidor som jag använde i Cloud Partner Portal?

I följande tabell visas motsvarande länkar mellan de två portalerna.

| Sida | Länk till Molnpartnerportal | Länk till Partner center |
|------|---------------------------|---------------------|
| **Sidan med alla erbjudanden** | https://cloudpartner.azure.com/#alloffers | https://partner.microsoft.com/dashboard/commercial-marketplace/overview |
| **Sidan med alla utgivare** | https://cloudpartner.azure.com/#publishers | https://partner.microsoft.com/dashboard/account/v3/publishers/list |
| **Publisher-profil** | https://cloudpartner.azure.com/#profile | https://partner.microsoft.com/dashboard/account/management |
| **Användarsidan** | https://cloudpartner.azure.com/#users | https://partner.microsoft.com/dashboard/account/usermanagement |
| **Sidan Historik** | https://cloudpartner.azure.com/#history | Historikfunktionen stöds ännu inte i Partner Center. |
| **Instrumentpanelen Insikter** | https://cloudpartner.azure.com/#insights | https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary |
| **Utbetalningsrapport** | https://cloudpartner.azure.com/#insights/payout | https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments |
| | |

## <a name="will-the-cloud-partner-portal-rest-apis-be-supported-post-migration"></a>Kommer REST-API:erna för Cloud Partner Portal att stödjas efter migreringen?

Api:erna för Cloud Partner Portal är integrerade med Partner Center och fortsätter att fungera när dina erbjudanden har migrerats till Partner Center. Integrationen medför små förändringar. Granska ändringarna i tabellen nedan för att se till att koden fortsätter att fungera efter migreringen till Partner center.

| **Api** | **Ändra beskrivning** | **Påverkan** |
| ------- | ---------------------- | ---------- |
| PUBLICERA, GoLive, Avbryt | För migrerade erbjudanden har svarshuvudet ett annat format men fortsätter att fungera på samma sätt och anger en relativ sökväg för att hämta åtgärdsstatusen. | När du skickar någon av motsvarande POST-begäranden för ett erbjudande har platshuvudet ett av två format beroende på erbjudandets migreringsstatus:<ul><li>Ej migrerade erbjudanden<br>`/api/operations/{PublisherId}${offerId}$2$preview?api-version=2017-10-31`</li><li>Migrerade erbjudanden<br>`/api/publishers/{PublisherId}/offers/{offereId}/operations/408a4835-0000-1000-0000-000000000000?api-version=2017-10-31`</li> |
| FÅ-åtgärd | För erbjudanden som tidigare stödde fältet "meddelande-e-post" i svaret kommer det här fältet att vara inaktuellt och inte längre returnerat för migrerade erbjudanden. | För migrerade erbjudanden skickar vi inte längre meddelanden till listan över e-postmeddelanden som anges i begärandena. Api-tjänsten justeras i stället med e-postprocessen för meddelanden i Partner Center för att skicka e-postmeddelanden. Meddelanden skickas specifikt till den e-postadress som anges i avsnittet Kontaktinformation för säljare i dina kontoinställningar i Partnercenter för att meddela dig om driftens förlopp.<br><br>Granska e-postadressen som anges i avsnittet Kontaktinformation för säljare i [kontoinställningarna](https://partner.microsoft.com/dashboard/account/management) i Partnercenter för att se till att rätt e-post tillhandahålls för meddelanden.  |
| | | |
