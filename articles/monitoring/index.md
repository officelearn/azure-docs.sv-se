---
title: Azure-hantering – övervakning | Microsoft Docs
description: Azure har flera tjänster och verktyg som samarbetar för att tillhandahålla fullständig hantering för såväl program som körs i Azure som för program i andra moln och lokalt.  Den här artikeln innehåller en översiktlig beskrivning av de olika hanteringsområdena samt länkar till innehåll om Azure-verktyg för hantering av molnprogram och resurser.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/09/2018
ms.author: bwren
ms.openlocfilehash: 36dd04be167d9e8e63ab38e4af80c5766ba55370
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
ms.locfileid: "31410302"
---
# <a name="azure-management---monitoring"></a>Azure-hantering – övervakning

Övervakning i Azure är en del av Azure-hanteringen.  Den här artikeln beskriver kortfattat de olika hanteringsområdena som krävs för att distribuera och underhålla program och resurser i Azure och innehåller länkar till dokumentation som hjälper dig att komma igång.

## <a name="management-in-azure"></a>Hantering i Azure

Hantering syftar på uppgifter och processer som krävs för att underhålla företagsprogram samt resurser som stöder dessa program.  Azure har flera tjänster och verktyg som samarbetar för att tillhandahålla fullständig hantering för såväl program som körs i Azure som för program i andra moln och lokalt.  Det första steget för att utforma en komplett hanteringsmiljö är att förstå de olika verktygen som är tillgängliga, och hur de kan användas tillsammans i en mängd olika hanteringsscenarier.

Följande diagram visar de olika hanteringsområdena som krävs för att underhålla alla program och resurser.  Dessa olika områden kan hanteras som livscyklar där varje område måste hanteras i kontinuerlig följd under en resurs livslängd.  Det börjar med den första distributionen, fortsätter under drift, och slutar när resursen dras tillbaka.

![Hanteringsfunktioner](media/management-overview/management-capabilities.png)


Ingen enskild Azure-tjänst uppfyller alla behov för ett visst hanteringsområde. Istället uppfylls kraven genom att flera tjänster arbetar ihop.  Vissa tjänster tillhandahåller riktade funktioner, till exempel Application Insights som övervakar webbprogram.  Andra tillhandahåller vanliga funktioner, exempelvis Log Analytics, som lagrar hanteringsdata för andra tjänster vilket gör att du kan analysera olika typer av data som samlats in av olika tjänster.  

Följande avsnitt beskriver i korta ordalag de olika hanteringsområdena och tillhandahåller länkar till detaljerat innehåll om de viktigaste Azure-tjänsterna.

## <a name="monitor"></a>Övervaka
Övervakning syftar till insamling och analys av data för att avgöra prestanda, hälsotillstånd och tillgänglighet för affärsprogram och resurser som dessa program är beroende av. En effektiv övervakningsstrategi hjälper dig att få en detaljerad översikt över hur de olika komponenterna fungerar i ditt program och hjälper dig att förbättra din drifttid genom att proaktivt meddela dig om kritiska tillstånd så att du kan hantera dem innan de hunnit bli problem.  I [Monitoring Azure applications and resources](monitoring-overview.md) (Övervaka Azure-program och -resurser) finns en översikt över övervakning i Azure och information om de olika tjänsterna som används för en övervakningsstrategi.


## <a name="configure"></a>Konfigurera
Konfiguration syftar på den första distributionen och konfigurationen av program och resurser samt det löpande underhållet av dessa program och resurser som utförs genom korrigeringar och programvaruuppdateringar.  Genom att automatisera de här uppgifterna via skript och principer kan du eliminera redundans och minimera tiden och arbetet som krävs samtidigt som du ökar din precision och effektivitet.  [Azure Automation](..\automation\automation-intro.md) innehåller många tjänster för att automatisera konfigurationsåtgärder.  Förutom runbook-flöden för att automatisera processer tillhandahåller Azure Automation även hantering av konfigurationer och uppdateringar, vilket hjälper dig att hantera konfigurationer via principer och att identifiera och distribuera uppdateringar.

## <a name="govern"></a>Styrning
Styrning tillhandahåller mekanismer och processer för att behålla kontrollen över dina program och resurser i Azure.  Det omfattar att planera initiativ och att fatta beslut om strategiska prioriteringar.  Styrning i Azure implementeras främst genom två tjänster.  [Azure Policy](../azure-policy/azure-policy-introduction.md) gör det möjligt att skapa, tilldela och hantera principdefinitioner som tillämpar olika regler och åtgärder på resurserna, så att resurserna följer företagets standarder och serviceavtal. [Azure Cost Management från Cloudyn](../cost-management/overview.md) gör det möjligt att spåra molnanvändning och utgifter för dina Azure-resurser och andra molnleverantörer, inklusive AWS och Google.

## <a name="secure"></a>Skydda
Hantering av säkerhet för program, resurser och data innefattar en kombination av att bedöma hot, samla in och analysera säkerhetsdata och att säkerställa att program och resurser är utformade och konfigurerade på ett säkert sätt.  Säkerhetsövervakning och hotanalys tillhandahålls av [Azure Security Center](../security-center/security-center-intro.md), som ger enhetlig säkerhetshantering och avancerat skydd mot hot i olika hybridmolnarbetsbelastningar.  Du bör även läsa [Introduktion till Azure-säkerhet](../security/azure-security.md) för mer utförlig information om säkerhet i Azure och för att få hjälp med att konfigurera Azure-resurser på ett säkert sätt.


## <a name="protect"></a>Skydda
Skydd refererar till att säkerställa att program och data alltid är tillgängliga, även vid avbrott som du inte kan styra över.  Skydd i Azure tillhandahålls av två tjänster.  [Azure Backup](../backup/backup-introduction-to-azure-backup.md) tillhandahåller säkerhetskopiering och återställning av data, antingen i molnet eller lokalt.    [Azure Site Recovery](../site-recovery/site-recovery-overview.md) säkerställer hög tillgänglighet för ditt program genom att tillhandahålla verksamhetskontinuitet och omedelbar återställning vid haveri.

## <a name="migrate"></a>Migrera 
Migrering refererar till att överföra arbetsbelastningar som körs lokalt till Azure-molnet.  [Azure Migrate](../migrate/migrate-overview.md) är en tjänst som hjälper dig att bedöma lämpligheten för migrering. Den omfattar prestandabaserade storleksändringar och kostnadsuppskattningar för lokala virtuella datorer till Azure.  Azure Site Recovery kan hjälpa dig att utföra den faktiska migreringen av virtuella datorer [från en lokal plats](../site-recovery/migrate-tutorial-on-premises-azure.md) eller [från Amazon Web Services](../site-recovery/migrate-tutorial-aws-azure.md).  [Azure Database Migration](../dms/dms-overview.md) hjälper dig att migrera flera databaskällor till Azure-dataplattformar.


## <a name="operations-management-suite"></a>Operations Management Suite
Tidigare teknisk dokumentation för Azure-hantering innefattade Operations Management Suite (OMS), som är en sammanslagning av följande Azure-hanteringstjänster:

- Azure Automation
- Azure Backup
- Log Analytics
- Webbplatsåterställning

Vi beskriver inte längre det sammanslagna paketet i vår tekniska dokumentation eftersom den fullständiga hanteringen i Azure har utökats för att omfatta andra tjänster. Ingen av de tjänster som ingår i OMS har ändrats och alla tjänster spelar fortfarande en viktig roll i hanteringen av Azures program och resurser. Du bör fokusera på de hanteringsuppgifter som du måste utföra och på de olika Azure-tjänster som fungerar tillsammans för varje uppgift.