---
title: Hantering av Azure - övervakning | Microsoft Docs
description: Azure har flera tjänster och verktyg som arbetar tillsammans för att tillhandahålla fullständig hantering för inte bara ditt program som körs i Azure utan även i andra moln och lokalt.  Den här artikeln innehåller en översiktlig beskrivning av de olika områdena för hantering och länkar till innehåll på Azure-verktyg för att hantera dina molnprogram och resurser.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/27/2018
ms.author: bwren
ms.openlocfilehash: b20283e1189e4f1a3555e2dd8d25972c9a677cd6
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2018
---
# <a name="azure-management---monitoring"></a>Hantering av Azure - övervakning

Övervakning i Azure är en del av Azure Management.  Den här artikeln beskrivs kortfattat de olika områdena för hantering som krävs för att distribuera och underhålla dina program och resurser i Azure med länkar till dokumentationen om du vill komma igång.

## <a name="management-in-azure"></a>Hantering i Azure

Hantering av refererar till uppgifter och processer som krävs för att underhålla dina företagsprogram och resurser som stöder dessa.  Azure har flera tjänster och verktyg som arbetar tillsammans för att tillhandahålla fullständig hantering för inte bara ditt program som körs i Azure utan även i andra moln och lokalt.  Förstå de olika verktyg som är tillgängliga och hur de kan användas tillsammans för en mängd olika hanteringsscenarier är det första steget i utforma en komplett management-miljö.

Följande diagram illustrerar de olika områdena för hantering som krävs för att underhålla program eller resurs.  Dessa olika områden kan betraktas som en livscykel där varje krävs i kontinuerlig följd över livslängd för en resurs.  Detta startar med den första distributionen via fortsatt drift, och slutligen när den har dragits tillbaka.

![Hanteringsfunktioner](media/management-overview/management-capabilities.png)


Ingen enskild Azure service fylls helt behoven inom en viss management område, men i stället var realiseras av flera tjänster som arbetar tillsammans.  Vissa tjänster innehåller riktade funktioner, till exempel Application Insights som ger övervakning för webbprogram.  Andra innehåller vanliga funktioner, till exempel logganalys som lagrar data som hanteras för andra tjänster som gör att du kan analysera data av olika typer som samlas in av olika tjänster.  

I följande avsnitt beskriver de olika områdena kort och ger länkar till detaljerad innehåll på huvudsakliga Azure-tjänster som är avsedd att åtgärda dem.

## <a name="monitor"></a>Övervaka
Övervakning är att samla in och analysera data för att avgöra prestanda, hälsotillstånd och tillgänglighet för dina affärsprogram och resurser som den är beroende av. En effektiv övervakning strategi hjälper dig att förstå den detaljerade driften av de olika komponenterna i ditt program och att öka din drifttid genom att meddela dig om kritiska problem så att du kan lösa dem innan de hunnit bli problem.  Du kan läsa en översikt över övervakning i Azure som identifierar de olika tjänster som används för en strategi för övervakning på [övervakning Azure-program och resurser](monitoring-overview.md).


## <a name="configure"></a>Konfigurera
Konfigurera refererar till den första distributionen och konfigurationen av program och resurser och deras löpande underhållet med korrigeringarna och programvaruuppdateringarna.  Automatisering av dessa uppgifter via skript och principer kan du ta bort redundans, minimera din tid och arbete och öka dina Precision och effektivitet.  [Azure Automation](..\automation\automation-intro.md) innehåller flesta av tjänster för att automatisera konfigurationsåtgärder.  Förutom att runbooks för att automatisera processer, tillhandahåller konfiguration och hantering, som hjälper dig att hantera konfiguration via Grupprincip och identifiera och distribuera uppdateringar.

## <a name="govern"></a>Styr
Styrning tillhandahåller metoder och processer för att behålla kontrollen över dina program och resurser i Azure.  Den omfattar planering din initiativ och inställning strategiska prioriteringar.  Styrning i Azure implementeras i första hand med två tjänster.  [Azure princip](../azure-policy/azure-policy-introduction.md) kan du skapa, tilldela och hantera principdefinitioner som använder olika regler och åtgärder via dina resurser, så resurserna vara kompatibla med företagets standarder och serviceavtal. [Azure kostnaden hanteringen av Cloudyn](../cost-management/overview.md) kan du spåra molnanvändning av och omkostnader för Azure-resurser och andra molntjänstleverantörer inklusive AWS och Google.

## <a name="secure"></a>Skydda
Hantera säkerheten för dina program, resurser och data innebär en kombination av bedömningen av hot, samla in och analysera säkerhetsdata och säkerställer att dina program och resurser utformad och konfigurerats på ett säkert sätt.  Övervakning och hot säkerhetsanalys tillhandahålls av [Azure Security Center](../security-center/security-center-intro.md) som innehåller unified säkerhetshantering och avancerade skydd över hybrid cloud arbetsbelastningar.  Du bör också se [introduktion till Azure-säkerhet](../security/azure-security.md) utförlig information om säkerhet i Azure och konfigurera Azure-resurser på ett säkert sätt.


## <a name="protect"></a>Skydda
Skydd refererar till att säkerställa att dina program och data alltid är tillgängliga även vid avbrott utanför din kontroll.  Skydd i Azure som två tjänster.  [Azure-säkerhetskopiering](../backup/backup-introduction-to-azure-backup.md) tillhandahåller säkerhetskopiering och återställning av data, antingen i molnet eller lokalt.    [Azure Site Recovery](../site-recovery/site-recovery-overview.md) säkerställer hög tillgänglighet för ditt program genom att tillhandahålla kontinuerlig verksamhet och omedelbar återställning vid katastrofåterställning.

## <a name="migrate"></a>Migrera 
Migrering refererar till övergång arbetsbelastningar som körs lokalt till Azure-molnet.  [Azure migrera](../migrate/migrate-overview.md) är en tjänst som hjälper dig att bedöma migrering, inklusive prestandabaserad storlek och kostnaden uppskattningar av lokala virtuella datorer till Azure.  Azure Site Recovery kan hjälpa dig att utföra den faktiska migreringen av virtuella datorer [från lokala](../site-recovery/migrate-tutorial-on-premises-azure.md) eller [från Amazon Web Services](../site-recovery/migrate-tutorial-aws-azure.md).  [Azure-Databasmigrering](../dms/dms-overview.md) hjälper dig att migrera flera databaskällor till Azure Data plattformar.