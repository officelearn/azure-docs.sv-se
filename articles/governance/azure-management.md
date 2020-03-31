---
title: Översikt över Azure Management – Azure-styrning
description: Översikt över områdena hantering av Azure-program och resurser med länkar till innehåll på Azure-hanteringsverktyg.
ms.date: 12/06/2018
ms.topic: overview
ms.openlocfilehash: 644f4fc3a04f86426c2eb04b02c29882fa39fb88
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "75980908"
---
# <a name="overview-of-management-services-in-azure"></a>Översikt över hanteringstjänster i Azure

Styrning i Azure är en aspekt av Azure Management. Den här artikeln beskriver de olika hanteringsområdena för distribution och underhåll av dina resurser i Azure.

Hantering syftar på uppgifter och processer som krävs för att underhålla företagsprogram samt resurser som stöder dessa program. Azure har många tjänster och verktyg som fungerar tillsammans för att tillhandahålla fullständig hantering. Dessa tjänster är inte bara för resurser i Azure, men även i andra moln och lokalt. Att förstå de olika verktygen och hur de arbetar tillsammans är det första steget i utformningen av en komplett hanteringsmiljö.

Följande diagram visar de olika hanteringsområdena som krävs för att underhålla alla program och resurser. Dessa olika områden kan ses som en livscykel. Varje område krävs i kontinuerlig följd över en resurs livslängd. Den här resurslivscykeln börjar med den första distributionen, genom fortsatt drift och slutligen när den dras tillbaka.

![Ledningsdiscipliner i Azure](../monitoring/media/management-overview/management-capabilities.png)

Ingen enskild Azure-tjänst fyller helt kraven för ett visst hanteringsområde. I stället realiseras var och en av flera tjänster som arbetar tillsammans. Vissa tjänster, till exempel Application Insights, tillhandahåller riktade övervakningsfunktioner för webbprogram. Andra, som Azure Monitor-loggar, lagrar hanteringsdata för andra tjänster. Med den här funktionen kan du analysera data av olika typer som samlas in av olika tjänster.

Följande avsnitt beskriver i korta ordalag de olika hanteringsområdena och tillhandahåller länkar till detaljerat innehåll om de viktigaste Azure-tjänsterna.

## <a name="monitor"></a>Övervaka

Övervakning är handlingen att samla in och analysera data för att granska prestanda, hälsa och tillgänglighet för dina resurser. En effektiv övervakningsstrategi hjälper dig att förstå hur komponenter fungerar och att öka din drifttid med meddelanden. Läs en översikt över Övervakning som täcker de olika tjänster som används vid [Övervakning av Azure-program och -resurser](../monitoring/monitoring-overview.md).

## <a name="configure"></a>Konfigurera

Konfigurera refererar till den första distributionen och konfigurationen av resurser och löpande underhåll.
Automatisering av dessa uppgifter gör att du kan eliminera redundans, minimera din tid och ansträngning och öka din noggrannhet och effektivitet. [Azure Automation](../automation/automation-intro.md) innehåller många tjänster för att automatisera konfigurationsåtgärder. Medan runbooks hanterar processautomatisering, hjälper konfiguration och uppdateringshantering till att hantera konfigurationen.

## <a name="govern"></a>Styrning

Styrning tillhandahåller mekanismer och processer för att behålla kontrollen över dina program och resurser i Azure. Det omfattar att planera initiativ och att fatta beslut om strategiska prioriteringar.
Styrning i Azure implementeras främst genom två tjänster. [Med Azure Policy](./policy/overview.md) kan du skapa, tilldela och hantera principdefinitioner för att tillämpa regler för dina resurser. Den här funktionen håller dessa resurser i enlighet med företagets standarder. [Med Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md) kan du spåra molnanvändning och utgifter för dina Azure-resurser och andra molnleverantörer.

## <a name="secure"></a>Skydda

Hantera säkerheten för dina resurser och data. Ett säkerhetsprogram innebär att bedöma hot, samla in och analysera data och efterlevnad av dina program och resurser. Säkerhetsövervakning och hotanalys tillhandahålls av [Azure Security Center](../security-center/security-center-intro.md), som inkluderar enhetlig säkerhetshantering och avancerat skydd mot hot över hybridmolnarbetsbelastningar. Se [Introduktion till Azure Security](../security/fundamentals/overview.md) för omfattande information och vägledning om hur du skyddar Azure-resurser.

## <a name="protect"></a>Skydda

Skydd avser att hålla dina program och data tillgängliga, även med avbrott som ligger utanför din kontroll. Skydd i Azure tillhandahålls av två tjänster. [Azure Backup](../backup/backup-introduction-to-azure-backup.md) tillhandahåller säkerhetskopiering och återställning av data, antingen i molnet eller lokalt. [Azure Site Recovery](../site-recovery/site-recovery-overview.md) ger kontinuitet i verksamheten och omedelbar återställning under en katastrof.

## <a name="migrate"></a>Migrera

Migrering refererar till att överföra arbetsbelastningar som körs lokalt till Azure-molnet.
[Azure Migrate](../migrate/migrate-overview.md) är en tjänst som hjälper dig att bedöma migreringsenligheten för lokala virtuella datorer till Azure. Azure Site Recovery migrerar virtuella datorer [från lokala](../site-recovery/migrate-tutorial-on-premises-azure.md) eller från Amazon [Web Services](../site-recovery/migrate-tutorial-aws-azure.md). [Azure Database Migration](../dms/dms-overview.md) hjälper dig att migrera databaskällor till Azure Data-plattformar.
