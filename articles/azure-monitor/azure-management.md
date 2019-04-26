---
title: Azure-hantering och Operations Management Suite (OMS) | Microsoft Docs
description: Översikt över hanteringsområden för Azure-program och -resurser med länkar till innehåll om Azure-hanteringsverktyg som tidigare fanns med i paketet för Operations Management Suite (OMS).
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/07/2018
ms.author: bwren
ms.openlocfilehash: b56993b9ad03f2ab50fe3954ab5e8855d0d8bc0f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60371653"
---
# <a name="azure-management---monitoring"></a>Azure-hantering – övervakning

Övervakning i Azure är en del av Azure-hanteringen.  Den här artikeln beskriver kortfattat de olika hanteringsområdena som krävs för att distribuera och underhålla program och resurser i Azure och innehåller länkar till dokumentation som hjälper dig att komma igång.

## <a name="management-in-azure"></a>Hantering i Azure

Hantering syftar på uppgifter och processer som krävs för att underhålla företagsprogram samt resurser som stöder dessa program.  Azure har flera tjänster och verktyg som samarbetar för att tillhandahålla fullständig hantering för såväl program som körs i Azure som för program i andra moln och lokalt.  Det första steget för att utforma en komplett hanteringsmiljö är att förstå de olika verktygen som är tillgängliga, och hur de kan användas tillsammans i en mängd olika hanteringsscenarier.

Följande diagram visar de olika hanteringsområdena som krävs för att underhålla alla program och resurser.  Dessa olika områden kan hanteras som livscyklar där varje område måste hanteras i kontinuerlig följd under en resurs livslängd.  Det börjar med den första distributionen, fortsätter under drift, och slutar när resursen dras tillbaka.

![Hanteringsfunktioner](media/management-overview/management-capabilities.png)


Följande avsnitt beskriver i korta ordalag de olika hanteringsområdena och tillhandahåller länkar till detaljerat innehåll om de viktigaste Azure-tjänsterna.

## <a name="monitor"></a>Övervaka
Övervakning syftar till insamling och analys av data för att avgöra prestanda, hälsotillstånd och tillgänglighet för affärsprogram och resurser som dessa program är beroende av. En effektiv övervakningsstrategi hjälper dig att få en detaljerad översikt över hur de olika komponenterna fungerar i ditt program och hjälper dig att förbättra din drifttid genom att proaktivt meddela dig om kritiska tillstånd så att du kan hantera dem innan de hunnit bli problem. Övervakning i Azure har tillhandahålls främst av [Azure Monitor](../azure-monitor/overview.md) som innehåller gemensamma lagringsplatser för övervakningsdata, flera datakällor för insamling av data från de olika lagren som stöder ditt program och funktioner för att analysera och svara på insamlade data.

## <a name="configure"></a>Konfigurera
Konfiguration syftar på den första distributionen och konfigurationen av program och resurser samt det löpande underhållet av dessa program och resurser som utförs genom korrigeringar och programvaruuppdateringar.  Genom att automatisera de här uppgifterna via skript och principer kan du eliminera redundans och minimera tiden och arbetet som krävs samtidigt som du ökar din precision och effektivitet.  [Azure Automation](../automation/automation-intro.md) innehåller många tjänster för att automatisera konfigurationsåtgärder.  Förutom runbook-flöden för att automatisera processer tillhandahåller Azure Automation även hantering av konfigurationer och uppdateringar, vilket hjälper dig att hantera konfigurationer via principer och att identifiera och distribuera uppdateringar.

## <a name="govern"></a>Styrning
Styrning tillhandahåller mekanismer och processer för att behålla kontrollen över dina program och resurser i Azure.  Det omfattar att planera initiativ och att fatta beslut om strategiska prioriteringar.  Styrning i Azure implementeras främst genom två tjänster.  [Azure Policy](../governance/policy/overview.md) gör det möjligt att skapa, tilldela och hantera principdefinitioner som tillämpar olika regler och åtgärder på resurserna, så att resurserna följer företagets standarder och serviceavtal. [Azure Cost Management från Cloudyn](../cost-management/overview.md) gör det möjligt att spåra molnanvändning och utgifter för dina Azure-resurser och andra molnleverantörer, inklusive AWS och Google.

## <a name="secure"></a>Skydda
Hantering av säkerhet för program, resurser och data innefattar en kombination av att bedöma hot, samla in och analysera säkerhetsdata och att säkerställa att program och resurser är utformade och konfigurerade på ett säkert sätt.  Säkerhetsövervakning och hotanalys tillhandahålls av [Azure Security Center](../security-center/security-center-intro.md), som ger enhetlig säkerhetshantering och avancerat skydd mot hot i olika hybridmolnarbetsbelastningar.  Du bör även läsa [Introduktion till Azure-säkerhet](../security/azure-security.md) för mer utförlig information om säkerhet i Azure och för att få hjälp med att konfigurera Azure-resurser på ett säkert sätt.


## <a name="protect"></a>Skydda
Skydd refererar till att säkerställa att program och data alltid är tillgängliga, även vid avbrott som du inte kan styra över.  Skydd i Azure tillhandahålls av två tjänster.  [Azure Backup](../backup/backup-introduction-to-azure-backup.md) tillhandahåller säkerhetskopiering och återställning av data, antingen i molnet eller lokalt.    [Azure Site Recovery](../site-recovery/site-recovery-overview.md) säkerställer hög tillgänglighet för ditt program genom att tillhandahålla verksamhetskontinuitet och omedelbar återställning vid haveri.

## <a name="migrate"></a>Migrera 
Migrering refererar till att överföra arbetsbelastningar som körs lokalt till Azure-molnet.  [Azure Migrate](../migrate/migrate-overview.md) är en tjänst som hjälper dig att bedöma lämpligheten för migrering. Den omfattar prestandabaserade storleksändringar och kostnadsuppskattningar för lokala virtuella datorer till Azure.  Azure Site Recovery kan hjälpa dig att utföra den faktiska migreringen av virtuella datorer [från en lokal plats](../site-recovery/migrate-tutorial-on-premises-azure.md) eller [från Amazon Web Services](../site-recovery/migrate-tutorial-aws-azure.md).  [Azure Database Migration](../dms/dms-overview.md) hjälper dig att migrera flera databaskällor till Azure-dataplattformar.

