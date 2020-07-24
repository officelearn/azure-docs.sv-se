---
title: Förbättra drift excellency med Advisor
description: Använd Azure Advisor för att optimera och mogna din operativa kompetens för dina Azure-prenumerationer.
ms.topic: article
ms.date: 10/24/2019
ms.openlocfilehash: 2b4c4726400134e4eec3868e155da47cb8c515b5
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87057646"
---
# <a name="achieve-operational-excellence-by-using-azure-advisor"></a>Få drifts kvalitet genom att använda Azure Advisor

Rekommendationer för drifts rekommendationer i Azure Advisor kan hjälpa dig med: 
- Process-och arbets flödes effektivitet.
- Resurs hantering.
- Metod tips för distribution. 

Du kan få de här rekommendationerna på fliken **användnings expert** på instrument panelens instrument panel.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-problems-affect-you"></a>Skapa Azure Service Health aviseringar för att bli informerad när Azure-problem påverkar dig

Vi rekommenderar att du konfigurerar Azure Service Health aviseringar så att du får ett meddelande när Azure-tjänstens problem påverkar dig. [Azure Service Health](https://azure.microsoft.com/features/service-health/) är en kostnads fri tjänst som ger personlig vägledning och support när du påverkas av ett problem med Azure-tjänsten. Advisor identifierar prenumerationer som inte har konfigurerade varningar och rekommenderar att du konfigurerar dem.


## <a name="design-your-storage-accounts-to-prevent-reaching-the-maximum-subscription-limit"></a>Utforma dina lagrings konton för att förhindra att den maximala prenumerations gränsen uppnås

En Azure-region kan stödja högst 250 lagrings konton per prenumeration. När du har nått gränsen kan du inte skapa lagrings konton i denna region/prenumerations kombination. Advisor kontrollerar dina prenumerationer och ger rekommendationer för att utforma för färre lagrings konton för regioner/prenumerationer som är nära att nå gränsen.

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>Se till att du har åtkomst till Azure Cloud-experter när du behöver det

När du kör en verksamhets kritisk arbets belastning är det viktigt att ha till gång till teknisk support när du behöver den. Advisor identifierar potentiella affärs kritiska prenumerationer som inte har teknisk support som ingår i support avtalet. Vi rekommenderar att du uppgraderar till ett alternativ som omfattar teknisk support.

## <a name="delete-and-re-create-your-pool-to-remove-a-deprecated-internal-component"></a>Ta bort och återskapa poolen för att ta bort en inaktuell intern komponent

Om poolen använder en inaktuell intern komponent tar du bort och återskapar poolen för bättre stabilitet och prestanda.

## <a name="repair-invalid-log-alert-rules"></a>Reparera ogiltiga logg aviserings regler

Azure Advisor identifierar aviserings regler som har ogiltiga frågor angivna i villkors avsnittet. Du kan skapa logg aviserings regler i Azure Monitor och använda dem för att köra Analytics-frågor vid angivna intervall. Frågans resultat avgör sedan om en avisering måste utlösas eller inte. Analys frågor kan bli ogiltiga över tid på grund av ändringar i refererade resurser, tabeller eller kommandon. Advisor rekommenderar att du korrigerar frågan i varnings regeln för att förhindra att den automatiskt inaktive ras och säkerställer övervakningen av dina resurser i Azure. [Läs mer om fel sökning av aviserings regler.](https://aka.ms/aa_logalerts_queryrepair)

## <a name="use-azure-policy-recommendations"></a>Använd Azure Policy rekommendationer

Azure Policy är en tjänst i Azure som du kan använda för att skapa, tilldela och hantera principer. Dessa principer tillämpar regler och effekter på dina resurser. Följande Azure Policy rekommendationer kan hjälpa dig att uppnå operativa excellency: 

**Hantera taggar.** Den här principen lägger till eller ersätter den angivna taggen och värdet när en resurs skapas eller uppdateras. Du kan åtgärda befintliga resurser genom att utlösa en reparations uppgift. Den här principen ändrar inte Taggar i resurs grupper.

**Framtvinga krav på Geo-efterlevnad.** Med den här principen kan du begränsa vilka platser som organisationen kan ange när den distribuerar resurser. 

**Ange tillåtna SKU: er för virtuella datorer för distributioner.** Med den här principen kan du ange en uppsättning-SKU:er för virtuella datorer som din organisation kan distribuera.

**Framtvinga *granskning av virtuella datorer som inte använder hanterade diskar*.**

**Aktivera *Ärv en tagg från resurs grupper*.** Den här principen lägger till eller ersätter den angivna taggen och värdet från den överordnade resursgruppen när en resurs skapas eller uppdateras. Du kan åtgärda befintliga resurser genom att utlösa en reparations uppgift.

## <a name="next-steps"></a>Nästa steg

Mer information om Advisor-rekommendationer finns i:
* [Introduktion till Advisor](advisor-overview.md)
* [Komma igång](advisor-get-started.md)
* [Kostnadsrekommendationer i Advisor](advisor-cost-recommendations.md)
* [Rekommendationer för Advisor-prestanda](advisor-performance-recommendations.md)
* [Rekommendationer om Advisor-tillförlitlighet](advisor-high-availability-recommendations.md)
* [Rekommendationer för Advisor-säkerhet](advisor-security-recommendations.md)
* [Advisor REST API](/rest/api/advisor/)
