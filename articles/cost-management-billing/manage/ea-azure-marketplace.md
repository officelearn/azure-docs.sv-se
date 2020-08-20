---
title: Azure Marketplace
description: Beskriver hur EA-kunder kan använda Azure Marketplace
author: bandersmsft
ms.reviewer: baolcsva
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 08/14/2020
ms.author: banders
ms.openlocfilehash: 83f88ffbd456e64a33d8ca6d8f0ab06098e8b861
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2020
ms.locfileid: "88245833"
---
# <a name="azure-marketplace"></a>Azure Marketplace

I den här artikeln förklarar vi hur EA-kunder och -partner kan se Marketplace-avgifter och aktivera Azure Marketplace-köp.

## <a name="azure-marketplace-for-ea-customers"></a>Azure Marketplace för EA-kunder

För direktkunder visas Azure Marketplace-avgifter i Azure Enterprise-portalen. Azure Marketplace-köp och -förbrukning debiteras utanför Azure-förskottsbetalningar och debiteras kvartalsvis eller månadsvis och i efterskott.

Indirekta kunder kan se sina Azure Marketplace-prenumerationer på sidan **Hantera prenumerationer** i Azure Enterprise-portalen, men priserna är dolda. Kunderna bör kontakta sin LSP (leverantör av licenslösningar) för information om Azure Marketplace-avgifter.

Nya månatliga eller årligen återkommande Azure Marketplace-köp debiteras hela beloppet inom den period då Azure Marketplace-objekten köptes. Dessa objekt förnyas automatiskt i nästa period på samma dag då det ursprungliga köpet gjordes.

Befintliga månatliga återkommande avgifter fortsätter att förnyas den första i varje kalendermånad. Årsavgifter förnyas på årsdagen för inköpet.

Nu förbrukar vissa tjänster från tredjepartsåterförsäljare på Azure Marketplace ditt saldo för Azure EA-förskottsbetalningar. Tidigare debiterades dessa tjänster utanför Azure EA-förskottsbetalningar och fakturerades separat. Azure EA-förskottsbetalningar för dessa tjänster på Azure Marketplace förenklar hanteringen av köp och betalningar från kunder. En fullständig lista över tjänster som förbrukar Azure-förskottsbetalningar finns i [uppdateringen från 6 mars 2018 på Azure-webbplatsen](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment/).

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

Det här avsnittet förklarar hur din Azure-förskottsbetalning kan tillämpas med tjänster från tredjepartsåterförsäljare på Azure Marketplace.

### <a name="what-changed-with-azure-marketplace-services-and-azure-ea-prepayment"></a>På vilket sätt har Azure Marketplace-tjänster och Azure EA-förskottsbetalningar ändrats?

Sedan den 1 mars 2018 förbrukar en del tjänster från tredjepartsåterförsäljare Azure EA-förskottsbetalningar. Förutom för reserverade VM-instanser i Azure fakturerades tjänsterna tidigare separat utanför Azure EA-förskottsbetalningar.

Vi har utökat användningen av Azure-förskottsbetalningar till att nu omfatta några av de mest köpta Azure Marketplace-tjänster som publicerats av tredje part. Azure EA-förskottsbetalningar för dessa tjänster på Azure Marketplace förenklar hanteringen av ditt köp och dina betalningar.

### <a name="why-did-we-make-this-change"></a>Varför har vi gjort den här ändringen?

Kunder letar ständigt efter nya sätt att dra nytta av förskottsbetalningarna som de har gjort i form av Azure-förskottsbetalningar. Vi uppfyller en vanlig förfrågan som påverkar en stor del av våra Azure Marketplace-kunder.

### <a name="how-do-you-benefit"></a>Vad innebär det för fördel för dig?

Faktureringsupplevelsen blir enklare och du kan använda din Azure EA-förskottsbetalning på ett bättre sätt. Eftersom dessa tjänster förbrukar Azure-förskottsbetalningar blir din Azure EA-förskottsbetalning ännu mer värdefull.

### <a name="what-azure-marketplace-services-use-azure-ea-prepayment-and-how-do-i-know"></a>Vilka Azure Marketplace-tjänster använder Azure EA-förskottsbetalningar och hur vet jag vilka de är?

När du köper en tjänst som använder Azure-förskottsbetalningar visas en ansvarsfriskrivning på Azure Marketplace. För närvarande stöds vissa tjänster som publiceras av Red Hat, SUSE, Autodesk och Oracle. Tjänster som har liknande namngivningskonventioner men som publiceras av andra parter, dras inte av från Azure-förskottsbetalningar. En fullständig lista finns i slutet av dessa vanliga frågor och svar.

### <a name="what-if-my-azure-ea-prepayment-runs-out"></a>Vad händer om min Azure EA-förskottsbetalning tar slut?

När du har förbrukat din Azure-förskottsbetalning övergår användningen till överförbrukning, och avgifterna för dessa tjänster visas på din nästa överförbrukningsfaktura tillsammans med andra förbrukningstjänster. Före ändringen 1 mars 2018 fakturerades dessa avgifter med andra Azure Marketplace-tjänster.

### <a name="why-dont-all-azure-marketplaces-consume-azure-ea-prepayment"></a>Varför förbrukar inte alla Azure Marketplace-tjänster Azure EA-förskottsbetalningar?

Vår ambition är att leverera den bästa kundupplevelsen för Azure EA-förskottsbetalningar. Den här ändringen påverkade ett stort antal kunder och en stor del av de totala köpen i Azure Marketplace. Andra tjänster kan läggas till i framtiden.

### <a name="how-does-this-impact-indirect-enrollment-and-partners"></a>Hur påverkar detta indirekt registrering och partner?

Det påverkar inte vår indirekta registrering för kunder eller partner. Dessa tjänster har samma påläggsmöjligheter för partner som andra konsumtionstjänster. Det enda som ändras är att debiteringarna visas på en annan faktura och att betalningen av avgifterna kommer från kundens Azure EA-förskottsbetalning.

### <a name="is-there-a-list-of-azure-marketplace-services-that-consume-azure-ea-prepayment"></a>Finns det någon lista över Azure Marketplace-tjänster som förbrukar Azure EA-förskottsbetalningar?

Specifika Azure Marketplace-erbjudanden kan förbruka Azure-förskottsbetalningssaldot. Se avsnittet om [tjänster från tredje part som använder Azure-förskottsbetalningar](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment) för en fullständig lista över produkter som ingår i det här programmet.


## <a name="next-steps"></a>Nästa steg

- Få mer information om [prissättning](ea-pricing-overview.md).