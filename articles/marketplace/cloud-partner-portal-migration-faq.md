---
title: Vanliga frågor om migrering till Partner Center – Microsoft Commercial Marketplace
description: Svar på vanliga frågor om migreringen av erbjudanden från Cloud Partner Portal till Partner Center.
author: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: mingshen
ms.openlocfilehash: c4fdde94ea703d194e2de27a8df429f62ea374f4
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2020
ms.locfileid: "83727671"
---
# <a name="common-questions-about-migrating-from-the-cloud-partner-portal-to-partner-center"></a>Vanliga frågor om att migrera från Cloud Partner Portal till Partner Center

Den här artikeln handlar om vanliga frågor om migrering av erbjudanden från Cloud Partner Portal till Partner Center.

## <a name="what-does-offer-migration-mean"></a>Vad betyder migrering?

Vi flyttar dina erbjudande data från Cloud Partner Portal till Partner Center med ändringar i publicerings-och hanterings upplevelsen för erbjudandet.

| Område  | Något  |
|-------|----------|
| **Hanterings upplevelse för publicering och erbjudande** | Du får en bättre användar upplevelse med ett intuitivt gränssnitt i Partner Center. Mer information finns i [Vad är skillnaderna mellan partner Center och Cloud Partner Portal?](#what-are-the-differences-between-partner-center-and-the-cloud-partner-portal) |
| **Tillgänglighet för dina erbjudanden på Marketplace** | Inga ändringar. Om ditt erbjudande är aktivt i Marketplace fortsätter det att vara aktivt under och efter att migreringen har slutförts. |
| **Nya inköp och distributioner** | Inga ändringar. Dina kunder kan fortsätta att köpa och distribuera erbjudanden utan avbrott. |
| **Utbetalningar** | Alla inköp och distributioner som sker under eller efter migreringen fortsätter att betalas ut till dig som vanligt. |
|**API-integration med befintliga [Cloud Partner Portal-API: er](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview)** | Befintliga Cloud Partner Portal-API: er fortsätter att stödja efter migreringen och din befintliga integrering fortsätter att fungera. Mer information finns i [Cloud Partner Portal REST-API: er som stöds efter migrering?](#will-the-cloud-partner-portal-rest-apis-be-supported-post-migration) |
| | |

## <a name="can-i-still-access-the-cloud-partner-portal-and-manage-my-offers-during-migration"></a>Kan jag fortfarande komma åt Cloud Partner Portal och hantera erbjudanden under migreringen?

Alla erbjudanden har migrerats till Partner Center. Migrerings perioden för varje erbjudande måste ha varit mindre än 24 timmar. När ditt erbjudande har migrerats bör du ha fått ett e-postmeddelande.

När dina erbjudanden har migrerats, är de i skrivskyddat läge **under en begränsad tids period** i Cloud Partner Portal. Statusen "flyttad to Partner Center" visas och innehåller en länk till erbjudandet i Partner Center, som du ser i följande skärm bilder. I det här läget hanterar du uppdateringar av alla dina erbjudanden eller skapar nya erbjudanden exklusivt via partner Center.

:::image type="content" source="media/migration-faq/all-offers-2.png" alt-text="Visar meddelandet som ges för erbjudanden som har migrerats till Partner Center":::

:::image type="content" source="media/migration-faq/offer-has-moved.png" alt-text="Visar Cloud Partner Portal sidan för ett migrerat erbjudande.":::

## <a name="how-will-i-create-new-offers"></a>Hur skapar jag nya erbjudanden?

Från Cloud Partner Portal dirigeras du om att skapa nya erbjudanden i Partner Center

:::image type="content" source="media/migration-faq/create-new-offer-1.png" alt-text="Visar menyn för att skapa ett nytt erbjudande i Cloud Partner Portal":::

När du har valt ett nytt erbjudande visas ett meddelande, till exempel följande.

:::image type="content" source="media/migration-faq/create-new-offer-2.png" alt-text="Illustrerar meddelandet som tas emot när du skapar ett nytt erbjudande i CPP":::

## <a name="do-i-need-to-create-a-new-account-to-manage-offers-in-partner-center"></a>Måste jag skapa ett nytt konto för att hantera erbjudanden i Partner Center?

Nej. Ditt underliggande konto kommer att bevaras och du bör redan hantera det i Partner Center. Det innebär att om du är en befintlig partner kan du använda dina befintliga Cloud Partner Portal kontoautentiseringsuppgifter för att logga in på Partner Center efter migreringen. Endast erbjudanden och tillhör ande hanterings upplevelse flyttas från Cloud Partner Portal till Partner Center. Vi ber att du inte skapar några nya konton eftersom dina erbjudanden inte associeras med det nya kontot.

## <a name="i-see-a-message-in-the-cloud-partner-portal-to-activate-my-account-what-does-this-mean"></a>Det här innebär att jag ser ett meddelande i Cloud Partner Portal för att aktivera mitt konto?

Vi behöver några ytterligare uppgifter från dig för att migrera ditt konto till Partner Center på ett korrekt sätt och du kan hantera dina erbjudanden i Partner Center när erbjudandet har migrerats. Mer information om konto aktivering finns i [konto migrering från Cloud Partner Portal till Partner Center](https://docs.microsoft.com/azure/marketplace/partner-center-portal/account-migration-from-cpp-to-pc).

De steg som krävs för att slutföra konto aktiveringen varierar beroende på din konto roll.

| Konto roll | Aktiverings steg |
|--------------|----------------|
|Ägare | Gå till [utgivarens profil](https://cloudpartner.azure.com/#profile) sida i Cloud Partner Portal och klicka sedan på länken i banderollen för att aktivera. Du omdirigeras till Partner Center för att slutföra konto aktiveringen. |
| Deltagare | Endast en användare i kontot med en ägar roll kan aktivera kontot. Kontakta kontots ägare för att slutföra konto aktiveringen. Dina konto ägare bör visas i informations meddelandet. |
| | |

## <a name="im-having-trouble-logging-in-to-my-account-and-opening-a-support-ticket"></a>Jag har problem med att logga in på mitt konto och öppna ett support ärende

Om du inte kan logga in på ditt konto kan du öppna ett [support ärende](https://partner.microsoft.com/support/v2/?stage=1).

## <a name="where-can-i-find-documentation-on-the-new-partner-center-publishing-experience"></a>Var hittar jag dokumentation om den nya publicerings upplevelsen för partner Center?

Gå till den [kommersiella Marketplace-dokumentationen](https://docs.microsoft.com/azure/marketplace/). Sedan kan du expandera den **kommersiella Marketplace-portalen i Partner Center**   >  och**skapa ett nytt erbjudande** för att se hjälp avsnitten för att skapa varje typ av erbjudande.

:::image type="content" source="media/migration-faq/marketplace-help-topics.png" alt-text="Visar hjälp avsnitten för partner Center":::

### <a name="what-are-the-differences-between-partner-center-and-the-cloud-partner-portal"></a>Vad är skillnaderna mellan partner Center och Cloud Partner Portal?

Du kan märka följande skillnader mellan Cloud Partner Portal-och partner Center.

### <a name="modular-publishing-capabilities"></a>Funktioner för modulär publicering

Partner Center tillhandahåller ett modulärt publicerings alternativ som låter dig välja de ändringar som du vill publicera i stället för att alltid publicera alla uppdateringar samtidigt. Följande skärm bild visar till exempel att de enda ändringar som är markerade för publicering är ändringarna i **Egenskaper** och **erbjudande lista**.

:::image type="content" source="media/migration-faq/review-and-publish-migration.png" alt-text="Visar sidan för hands version och publicering":::

Uppdateringarna som du inte publicerar sparas som utkast. Fortsätt att använda din för hands version av erbjudandet för att verifiera ditt erbjudande innan du gör det tillgängligt för allmänheten.

### <a name="rich-text-format"></a>RTF-format

Förbättra ditt erbjudande och din plan beskrivning med hjälp av RTF-redigeraren på list sidan för erbjudande och prenumerationer.

:::image type="content" source="media/migration-faq/rich-text-editor-migration.png" alt-text="Visar Rich Text Editor":::

### <a name="enhanced-preview-options"></a>Förbättrade alternativ för förhands granskning

Partner Center innehåller en [jämförelse funktion](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#compare-changes-to-marketplace-offers) med förbättrade filtrerings alternativ. Det ger dig möjlighet att jämföra mot för hands versionen och live-versionen av erbjudandet.

:::image type="content" source="media/migration-faq/enhanced-preview.png" alt-text="Visar jämförelse funktionen":::

### <a name="branding-and-navigation-changes"></a>Anpassnings-och navigerings ändringar

Du märker några anpassnings ändringar. Till exempel är "SKU" märkta som "planer" i Partner Center.

:::image type="content" source="media/migration-faq/plan-overview-migration.png" alt-text="Visar plan översikten.":::

Den information som du använde för att tillhandahålla i **Marketplace** -eller S**torefront-information** (konsult tjänsten Power BI app) i Cloud Partner Portal samlas in på sidan för **erbjudande listan** i Partner Center.

:::image type="content" source="media/migration-faq/offer-listing-migration.png" alt-text="Visar sidan med erbjudande listan.":::

Den information som du använde för att tillhandahålla SKU: er på en enda sida i Cloud Partner Portal kan nu samlas in på flera sidor i Partner Center:

* Sidan planera konfiguration
* Plan List sida
* Plan tillgänglighets sida
* Sidan planera teknisk konfiguration, som visas i den här skärm bilden.

:::image type="content" source="media/migration-faq/tech-config-migration.png" alt-text="Visar sidan planera teknisk konfiguration.":::

Ditt erbjudande-ID visas nu i det vänstra navigerings fältet i erbjudandet.

:::image type="content" source="media/migration-faq/offer-id-offer-overview.png" alt-text="Visar den vänstra navigerings menyn med erbjudande-ID.":::

### <a name="stop-selling-an-offer"></a>Sluta sälja ett erbjudande

Du kan begära att [sluta sälja ett erbjudande](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan) på Marketplace direkt från Partner Center-portalen. Alternativet finns på sidan **erbjudande översikt** för ditt erbjudande.

:::image type="content" source="media/migration-faq/stop-selling-migration.png" alt-text="Visar sidan erbjudande översikt med alternativet sluta sälja.":::

## <a name="what-pages-in-partner-center-correspond-to-pages-i-used-in-the-cloud-partner-portal"></a>Vilka sidor i Partner Center motsvarar sidor som jag använde i Cloud Partner Portal?

I följande tabell visas motsvarande länkar mellan de två portalerna.

| Sida | Cloud Partner Portal länk | Partner Center-länk |
|------|---------------------------|---------------------|
| **Sidan med alla erbjudanden** | https://cloudpartner.azure.com/#alloffers | https://partner.microsoft.com/dashboard/commercial-marketplace/overview |
| **Sidan med alla utgivare** | https://cloudpartner.azure.com/#publishers | https://partner.microsoft.com/dashboard/account/v3/publishers/list |
| **Utgivar profil** | https://cloudpartner.azure.com/#profile | https://partner.microsoft.com/dashboard/account/management |
| **Användarsidan** | https://cloudpartner.azure.com/#users | https://partner.microsoft.com/dashboard/account/usermanagement |
| **Sidan historik** | https://cloudpartner.azure.com/#history | Historik funktionen stöds inte ännu i Partner Center. |
| **Instrument panel för insikter** | https://cloudpartner.azure.com/#insights | https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary |
| **Utbetalnings rapport** | https://cloudpartner.azure.com/#insights/payout | https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments |
| | |

## <a name="will-the-cloud-partner-portal-rest-apis-be-supported-post-migration"></a>Kommer Cloud Partner Portal REST-API: er att stödjas efter migrering?

Cloud Partner Portal API: er är integrerade med partner Center och fortsätter att fungera när dina erbjudanden har migrerats till Partner Center. I integrationen presenteras små ändringar. Granska ändringarna i tabellen nedan för att se till att koden fortsätter att fungera efter migreringen till Partner Center.

| **API** | **Ändra beskrivning** | **Påverkan** |
| ------- | ---------------------- | ---------- |
| Publicera, GoLive, Avbryt | För migrerade erbjudanden har svars huvudet ett annat format men fortsätter att fungera på samma sätt, vilket anger en relativ sökväg för att hämta åtgärds statusen. | När du skickar någon av motsvarande POST-förfrågningar för ett erbjudande får plats huvudet ett av två format beroende på status för migreringen:<ul><li>Ej migrerade erbjudanden<br>`/api/operations/{PublisherId}${offerId}$2$preview?api-version=2017-10-31`</li><li>Migrerade erbjudanden<br>`/api/publishers/{PublisherId}/offers/{offereId}/operations/408a4835-0000-1000-0000-000000000000?api-version=2017-10-31`</li> |
| Hämta åtgärd | För erbjudanden som tidigare har stöd för fältet notification-email i svaret kommer det här fältet att föråldras och inte längre returneras för migrerade erbjudanden. | För migrerade erbjudanden skickar vi inte längre meddelanden till listan över e-postmeddelanden som anges i förfrågningarna. I stället justeras API-tjänsten med e-postprocessen för meddelanden i Partner Center för att skicka e-postmeddelanden. Mer specifikt skickas meddelanden till den e-postadress som anges i avsnittet säljare kontakt information i dina konto inställningar i Partner Center för att meddela dig om åtgärds förlopp.<br><br>Granska e-postadressen som angetts i avsnittet om säljar kontakt information i [konto inställningarna](https://partner.microsoft.com/dashboard/account/management) i Partner Center för att se till att rätt e-postadress tillhandahålls för meddelanden.  |
| | | |
