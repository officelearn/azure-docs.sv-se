---
title: Förbättra drifts excellencyen för dina Azure-prenumerationer med Azure Advisor
description: Använd Advisor för att optimera och få vuxen drifts kvalitet för dina Azure-prenumerationer
ms.topic: article
ms.date: 10/24/2019
ms.openlocfilehash: 861b12c2267ffa89985ff11357de92da5a4ac870
ms.sourcegitcommit: 5a8c8ac84c36859611158892422fc66395f808dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/10/2020
ms.locfileid: "84658448"
---
# <a name="achieve-operational-excellence-with-azure-advisor"></a>Få drifts expert med Azure Advisor

Azure Advisor operativa rekommendationer hjälper kunden med process-och arbets flödes effektivitet, resurs hantering och bästa metoder för distribution. Du kan få de här rekommendationerna från Advisor på fliken **användnings expert** på instrument panelen för Advisor.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-issues-affect-you"></a>Skapa Azure Service Health aviseringar för att bli informerad när Azure-problem påverkar dig

Vi rekommenderar att du konfigurerar Azure Service Health aviseringar för att bli informerad när problem med Azure-tjänsten påverkar dig. [Azure Service Health](https://azure.microsoft.com/features/service-health/) är en kostnads fri tjänst som ger personlig vägledning och support när du påverkas av ett problem med en Azure-tjänst. Advisor identifierar prenumerationer som inte har konfigurerade varningar och rekommenderar att du skapar en.


## <a name="design-your-storage-accounts-to-prevent-hitting-the-maximum-subscription-limit"></a>Utforma dina lagrings konton för att förhindra att den maximala prenumerations gränsen överskrids

En Azure-region kan stödja högst 250 lagrings konton per prenumeration. När gränsen har nåtts kan du inte skapa fler lagrings konton i samma region/prenumerations kombination. Advisor kontrollerar dina prenumerations-och ytbehandlings rekommendationer för att du ska kunna utforma färre lagrings konton för alla som är nära att nå Max gränsen.

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>Se till att du har åtkomst till Azure Cloud-experter när du behöver det

När du kör en verksamhets kritisk arbets belastning är det viktigt att ha till gång till teknisk support vid behov. Advisor identifierar potentiella affärs kritiska prenumerationer som inte har teknisk support som ingår i support avtalet och rekommenderar att du uppgraderar till ett alternativ som omfattar teknisk support.

## <a name="delete-and-recreate-your-pool-to-remove-a-deprecated-internal-component"></a>Ta bort och återskapa poolen för att ta bort en inaktuell intern komponent

Poolen använder en inaktuell intern komponent. Ta bort och återskapa poolen för bättre stabilitet och prestanda.

## <a name="repair-invalid-log-alert-rules"></a>Reparera ogiltiga logg aviserings regler

Azure Advisor identifierar aviserings regler som har ogiltiga frågor angivna i villkors avsnittet. Loggaviseringsregler skapas i Azure Monitor och används för att köra analysfrågor enligt angivna intervall. Frågans resultat avgör sedan om en avisering måste utlösas eller inte. Analysfrågorna kan bli ogiltiga med tiden om något ändras i de resurser, tabeller eller kommandon som det hänvisas till. Advisor rekommenderar att du korrigerar frågan i aviserings regeln för att förhindra att den automatiskt inaktive ras och att övervaknings täckningen för dina resurser i Azure används. [Läs mer om fel sökning av aviserings regler](https://aka.ms/aa_logalerts_queryrepair)

## <a name="follow-best-practices-using-azure-policy"></a>Följ bästa praxis med Azure Policy

Azure Policy är en tjänst i Azure som används till att skapa, tilldela och hantera principer. Dessa principer tillämpar olika regler och effekter för dina resurser. Nedan visas Azure Policy rekommendationer som hjälper dig att uppnå drifts excellency: 
1. Hantera Taggar med Azure Policy: den här principen lägger till eller ersätter den angivna taggen och värdet när en resurs skapas eller uppdateras. Befintliga resurser kan åtgärdas genom att utlösa en reparationsåtgärd. Detta ändrar inte heller Taggar i resurs grupper.
2. Framtvinga krav på Geo-kompatibilitet med hjälp av Azure Policy: med principen kan du begränsa vilka platser som organisationen kan ange när resurser distribueras. 
3. Ange tillåtna SKU: er för virtuella datorer för distributioner: med den här principen kan du ange en uppsättning virtuella dator-SKU: er som din organisation kan distribuera.
4. Tillämpa ”Granska virtuella datorer som inte använder hanterade diskar” med Azure Policy
5. Använd Ärv en tagg från resurs grupper med hjälp av Azure Policy: principen lägger till eller ersätter den angivna taggen och värdet från den överordnade resurs gruppen när en resurs skapas eller uppdateras. Befintliga resurser kan åtgärdas genom att utlösa en reparationsåtgärd.

## <a name="next-steps"></a>Nästa steg

Mer information om Advisor-rekommendationer finns i:
* [Introduktion till Advisor](advisor-overview.md)
* [Komma igång](advisor-get-started.md)
* [Rekommendationer om Advisor-kostnader](advisor-cost-recommendations.md)
* [Rekommendationer för Advisor-prestanda](advisor-performance-recommendations.md)
* [Rekommendationer om rekommendationer för hög tillgänglighet](advisor-high-availability-recommendations.md)
* [Rekommendationer för Advisor-säkerhet](advisor-security-recommendations.md)
