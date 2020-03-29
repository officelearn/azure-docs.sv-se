---
title: Förbättra operativ excellens för dina Azure-prenumerationer med Azure Advisor
description: Använd Advisor för att optimera och bli mogen i drift för dina Azure-prenumerationer
ms.topic: article
ms.date: 10/24/2019
ms.openlocfilehash: f34284ba62bd5dea98345ebe73365b332d38ee78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443075"
---
# <a name="achieve-operational-excellence-with-azure-advisor"></a>Uppnå operativ excellens med Azure Advisor

Azure Advisors rekommendationer om operativ excellens hjälper kunden med process- och arbetsflödeseffektivitet, resurshanterbarhet och metodtips för distribution. Du kan få dessa rekommendationer från Advisor på fliken **Operational Excellence** på advisor-instrumentpanelen.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-issues-affect-you"></a>Skapa Azure Service Health-aviseringar som ska meddelas när Azure-problem påverkar dig

Vi rekommenderar att du ställer in Azure Service Health-aviseringar som ska meddelas när Azure-tjänstproblem påverkar dig. [Azure Service Health](https://azure.microsoft.com/features/service-health/) är en kostnadsfri tjänst som ger personlig vägledning och support när du påverkas av ett Azure-tjänstproblem. Advisor identifierar prenumerationer som inte har aviseringar konfigurerade och rekommenderar att du skapar en.

## <a name="design-your-storage-accounts-to-prevent-hitting-the-maximum-subscription-limit"></a>Utforma dina lagringskonton för att förhindra att du når den maximala prenumerationsgränsen

En Azure-region kan stödja högst 250 lagringskonton per prenumeration. När gränsen har nåtts kan du inte skapa fler lagringskonton i den regionen/prenumerationskombinationen. Advisor kontrollerar dina prenumerationer och ytrekommendationer för att du ska kunna utforma färre lagringskonton för alla som är nära att nå maxgränsen.

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>Se till att du har åtkomst till Azure-molnexperter när du behöver det

När du kör en affärskritisk arbetsbelastning är det viktigt att ha tillgång till teknisk support när det behövs. Advisor identifierar potentiella affärskritiska prenumerationer som inte har teknisk support som ingår i supportplanen och rekommenderar uppgradering till ett alternativ som inkluderar teknisk support.

## <a name="repair-invalid-log-alert-rules"></a>Reparera ogiltiga loggvarningsregler

Azure Advisor identifierar aviseringsregler som har ogiltiga frågor som anges i deras villkorsavsnitt. Loggaviseringsregler skapas i Azure Monitor och används för att köra analysfrågor enligt angivna intervall. Frågans resultat avgör sedan om en avisering måste utlösas eller inte. Analysfrågorna kan bli ogiltiga med tiden om något ändras i de resurser, tabeller eller kommandon som det hänvisas till. Advisor rekommenderar att du korrigerar frågan i aviseringsregeln för att förhindra att den inaktiveras automatiskt och säkerställer övervakning av dina resurser i Azure. [Läs mer om felsökning av varningsregler](https://aka.ms/aa_logalerts_queryrepair)

## <a name="follow-best-practices-using-azure-policy"></a>Följ metodtips med Hjälp av Azure Policy

Azure Policy är en tjänst i Azure som används till att skapa, tilldela och hantera principer. Dessa principer tillämpar olika regler och effekter för dina resurser. Nedan följer Azure-principrekommendationerna som hjälper dig att uppnå operativ excellens: 
1. Hantera taggar med Azure-princip: Den här principen lägger till eller ersätter den angivna taggen och värdet när en resurs skapas eller uppdateras. Befintliga resurser kan åtgärdas genom att utlösa en reparationsåtgärd. Detta ändrar inte heller taggar på resursgrupper.
2. Tvinga geo-efterlevnadskrav med Azure-principen: Principen gör att du kan begränsa de platser som din organisation kan ange när du distribuerar resurser. 
3. Ange tillåtna SKU:er för virtuella datorer för distributioner: Med den här principen kan du ange en uppsättning SKU:er för virtuella datorer som din organisation kan distribuera.
4. Framtvinga virtuella gransknings-datorer som inte använder hanterade diskar med Azure-principen
5. Använd "Ärva en tagg från resursgrupper" med Azure-principen: Principen lägger till eller ersätter den angivna taggen och värdet från den överordnade resursgruppen när en resurs skapas eller uppdateras. Befintliga resurser kan åtgärdas genom att utlösa en reparationsåtgärd.

## <a name="next-steps"></a>Nästa steg

Mer information om Advisor-rekommendationer finns i:
* [Introduktion till Advisor](advisor-overview.md)
* [Komma igång](advisor-get-started.md)
* [Kostnadsrekommendationer för rådgivare](advisor-cost-recommendations.md)
* [Advisors prestandarekommendationer](advisor-performance-recommendations.md)
* [Advisor rekommendationer för hög tillgänglighet](advisor-high-availability-recommendations.md)
* [Säkerhetsrekommendationer för rådgivare](advisor-security-recommendations.md)
