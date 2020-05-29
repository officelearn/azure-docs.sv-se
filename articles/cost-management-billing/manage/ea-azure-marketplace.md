---
title: Azure Marketplace
description: Beskriver hur EA-kunder kan använda Azure Marketplace
author: bandersmsft
ms.reviewer: baolcsva
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 05/12/2020
ms.author: banders
ms.openlocfilehash: 73c9e011359d2f86ec9f91d7e1e9b2869e68c794
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83648650"
---
# <a name="azure-marketplace"></a>Azure Marketplace

I den här artikeln förklarar vi hur EA-kunder och -partner kan se Marketplace-avgifter och aktivera Azure Marketplace-köp.

## <a name="azure-marketplace-for-ea-customers"></a>Azure Marketplace för EA-kunder

För direktkunder visas Azure Marketplace-avgifter i Azure Enterprise-portalen. Azure Marketplace-köp och -förbrukning debiteras utanför betalningsåtagandet och debiteras kvartalsvis eller månadsvis och i efterskott.

Indirekta kunder kan se sina Azure Marketplace-prenumerationer på sidan **Hantera prenumerationer** i Azure Enterprise-portalen, men priserna är dolda. Kunderna bör kontakta sin LSP (leverantör av licenslösningar) för information om Azure Marketplace-avgifter.

Nya månatliga eller årligen återkommande Azure Marketplace-köp debiteras hela beloppet inom den period då Azure Marketplace-objekten köptes. Dessa objekt förnyas automatiskt i nästa period på samma dag då det ursprungliga köpet gjordes.

Befintliga månatliga återkommande avgifter fortsätter att förnyas den första i varje kalendermånad. Årsavgifter förnyas på årsdagen för inköpet.

Vissa tjänster från tredjepartsåterförsäljare på Azure Marketplace ingår nu i EA-betalningsåtagandet och påverkar saldot. Tidigare fakturerades dessa tjänster separat och ingick inte i EA-betalningsåtagandet. EA-betalningsåtagandet för dessa tjänster på Azure Marketplace förenklar hanteringen av köp och betalningar från kunder. En fullständig lista över tjänster som ingår i betalningsåtagandet finns i [uppdateringen från 6 mars 2018 på Azure-webbplatsen](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment/).

### <a name="partners"></a>Partner

LSP:er kan ladda ned en Azure Marketplace-prislista från prisdokumentsidan i Azure Enterprise-portalen. Välj länken **Marketplace-prislistan** i det övre högra hörnet. Azure Marketplace-prislistan visar alla tillgängliga tjänster och motsvarande priser.

Så här laddar du ned prislistan:

1. I Azure Enterprise-portalen går du till **Rapporter** > **Prisdokument**.
1. Leta reda på länken Azure Marketplace-prislistan under ditt användarnamn i det övre högra hörnet.
1. Högerklicka på länken och välj **Spara mål som**.
1. I fönstret **Spara** ändrar du dokumentets titel till `AzureMarketplacePricelist.zip` så att filen ändras från en XLSX-fil till en ZIP-fil.
1. När hämtningen är klar har du en ZIP-fil med landsspecifika prislistor.
1. LSP:er kan se priser för respektive land i motsvarande fil. På fliken **Notifications** (Meddelanden) kan LSP:er se vilka SKU:er som är nya eller som har tagits bort.
1. Prisändringar är ovanliga. LSP:er meddelas via e-post om prisökningar och valutakursändringar 30 dagar i förväg.
1. LSP:er får en faktura per registrering, ISV och kvartal.

### <a name="enabling-azure-marketplace-purchases"></a>Aktivera Azure Marketplace-inköp

Företagsadministratörer kan inaktivera eller aktivera Marketplace-inköp för alla Azure-prenumerationer som ingår i registreringen. Om företagsadministratören inaktiverar köp och det finns Azure-prenumerationer som redan har Azure Marketplace-prenumerationer, avbryts eller påverkas inte dessa Azure Marketplace-prenumerationer.

Även om kunderna kan konvertera sina Azure-direktprenumerationer till Azure EA genom att koppla dem till registreringen i Azure Enterprise-portalen konverteras inte de underordnade prenumerationerna automatiskt med den åtgärden.

Så här aktiverar du Azure Marketplace-inköp:

1. Logga in på Azure Enterprise-portalen som företagsadministratör.
1. Gå till **Hantera**.
1. Under **Enrollment Detail** (Registreringsinformation) väljer du pennikonen bredvid **Azure Marketplace**.
1. Växla **Aktiverad/inaktiverad** eller **Endast kostnadsfria/BYOL SKU:er** efter behov.
1. Välj **Spara**.

> [!NOTE]
> BYOL (Bring your own license) och alternativet för endast kostnadsfritt begränsar inköp och förvärv av Azure Marketplace-SKU:er till endast BYOL och kostnadsfria SKU:er.

### <a name="services-billed-hourly-for-azure-ea"></a>Tjänster debiteras per timme för Azure EA

Följande tjänster debiteras per timme enligt Enterprise-avtalet i stället för priset per månad i MOSP:

- Application Delivery Network
- Brandvägg för webbaserade program

### <a name="azure-remoteapp"></a>Azure RemoteApp

Om du har ett Enterprise-avtal betalar du för Azure RemoteApp baserat på prisnivån för ditt Enterprise-avtal. Det finns inga ytterligare avgifter. Standardprisnivån innefattar de första 40 timmarna. Den obegränsade prisnivån innefattar de första 80 timmarna. Remote App slutar generera användning efter dessa 80 timmar.

## <a name="azure-marketplace-faq"></a>Vanliga frågor och svar om Azure Marketplace

I det här avsnittet förklaras hur ditt Azure-betalningsåtagande kan tillämpas för återförsäljartjänster från tredje part i Azure Marketplace.

### <a name="what-changed-with-azure-marketplace-services-and-ea-monetary-commitment"></a>Vad har ändrats när det gäller Azure Marketplace-tjänster och EA-betalningsåtagandet?

Från och med 1 mars 2018 används EA-betalningsåtagandet för vissa återförsäljartjänster från tredje part. Förutom för Azure Reserved VM Instances fakturerades tjänsterna tidigare separat utanför EA-betalningsåtagandet.

Vi har utökat användningen av betalningsåtagandet till att nu omfatta några av de mest köpta Azure Marketplace-tjänster som publicerats av tredje part. EA-betalningsåtagandet för dessa tjänster på Azure Marketplace förenklar hanteringen av ditt köp och dina betalningar.

### <a name="why-did-we-make-this-change"></a>Varför har vi gjort den här ändringen?

Kunder letar ständigt efter nya sätt att dra nytta av förskottsbetalningen som de har gjort i form av Azure-betalningsåtagandet. Vi uppfyller en vanlig förfrågan som påverkar en stor del av våra Azure Marketplace-kunder.

### <a name="how-do-you-benefit"></a>Vad innebär det för fördel för dig?

Du får en enklare faktureringsupplevelse och kan se till att du utnyttjar ditt Azure-betalningsåtagande. Eftersom dessa tjänster ingår i det förbetalda betalningsåtagandet skapas ytterligare värde för Azure-betalningsåtagandet.

### <a name="what-azure-marketplace-services-use-ea-monetary-commitment-and-how-do-i-know"></a>För vilka Azure Marketplace-tjänster används EA-betalningsåtagandet och hur vet jag det?

När du köper en tjänst som använder betalningsåtagandet visas en ansvarsfriskrivning i Azure Marketplace. För närvarande stöds vissa tjänster som publiceras av Red Hat, SUSE, Autodesk och Oracle. Tjänster som har liknande namngivningskonventioner men som publiceras av andra parter, dras inte av från betalningsåtagandet. En fullständig lista finns i slutet av dessa vanliga frågor och svar.

### <a name="what-if-my-ea-monetary-commitment-runs-out"></a>Vad händer om EA-betalningsåtagandet tar slut?

När du har förbrukat betalningsåtagandet övergår användningen till överförbrukning, och avgifterna för dessa tjänster visas på din nästa överförbrukningsfaktura tillsammans med andra förbrukningstjänster. Före ändringen 1 mars 2018 fakturerades dessa avgifter med andra Azure Marketplace-tjänster.

### <a name="why-dont-all-azure-marketplaces-consume-ea-monetary-commitment"></a>Varför används inte EA-betalningsåtagandet för alla Azure Marketplace-köp?

Vi arbetar ständigt med att skapa den bästa kundupplevelsen för EA-betalningsåtagande. Den här ändringen påverkade ett stort antal kunder och en stor del av de totala köpen i Azure Marketplace. Andra tjänster kan läggas till i framtiden.

### <a name="how-does-this-impact-indirect-enrollment-and-partners"></a>Hur påverkar detta indirekt registrering och partner?

Det påverkar inte vår indirekta registrering för kunder eller partner. Dessa tjänster har samma påläggsmöjligheter för partner som andra konsumtionstjänster. Det enda som ändras är att debiteringarna visas på en annan faktura och att betalningen av avgifterna kommer från kundens EA-betalningsåtagande.

### <a name="is-there-a-list-of-azure-marketplace-services-that-consume-ea-monetary-commitment"></a>Finns det en lista över de Azure Marketplace-tjänster som använder EA-betalningsåtagandet?

Specifika Azure Marketplace-erbjudanden kan använda betalningsåtagandemedel. Se [tjänster från tredje part som använder betalningsåtagande](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment) för en fullständig lista över produkter som medverkar i det här programmet.


## <a name="next-steps"></a>Nästa steg

- Få mer information om [prissättning](ea-pricing-overview.md).