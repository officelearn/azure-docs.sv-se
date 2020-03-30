---
title: Introduktion till Azure Advisor
description: Använd Azure Advisor för att optimera dina Azure-distributioner.
ms.topic: article
ms.date: 02/01/2019
ms.openlocfilehash: 600bda282d46f86979d0366719826c3a6c1323e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443084"
---
# <a name="introduction-to-azure-advisor"></a>Introduktion till Azure Advisor

Lär dig mer om de viktigaste funktionerna i Azure Advisor och få svar på vanliga frågor.

## <a name="what-is-advisor"></a>Vad är Advisor?
Advisor är en personlig molnkonsult som hjälper dig att följa metodtips för att optimera dina Azure-distributioner. Advisor analyserar din resurskonfiguration och användningstelemetri och rekommenderar sedan lösningar som kan hjälpa dig att förbättra kostnadseffektiviteten, prestanda, tillgängligheten och säkerheten för dina Azure-resurser.

Med Advisor kan du:
* Få proaktiva, användbara och personligt anpassade rekommendationer. 
* Förbättra prestanda, säkerhet och hög tillgänglighet för dina resurser, när du identifierar möjligheter att minska dina totala Azure-utgifter.
* Få rekommendationer med föreslagna åtgärder infogade.

Du kan komma åt Advisor via [Azure-portalen](https://aka.ms/azureadvisordashboard). Logga in på [portalen,](https://portal.azure.com)leta reda på **Advisor** i navigeringsmenyn eller sök efter den på menyn **Alla tjänster.**

Instrumentpanelen Advisor visar anpassade rekommendationer för alla dina prenumerationer.  Du kan använda filter för att visa rekommendationer för specifika prenumerationer och resurstyper.  Rekommendationerna är indelade i fyra kategorier: 

* **Hög tillgänglighet**: För att säkerställa och förbättra kontinuiteten i dina affärskritiska program. Mer information finns i [Advisors rekommendationer om hög tillgänglighet](advisor-high-availability-recommendations.md).
* **Säkerhet**: För att upptäcka hot och sårbarheter som kan leda till säkerhetsöverträdelser. Mer information finns i [Rekommendationer för rådgivaresäkerhet](advisor-security-recommendations.md).
* **Prestanda**: För att förbättra hastigheten på dina program. Mer information finns i [Advisor Performance rekommendationer](advisor-performance-recommendations.md).
* **Kostnad:** För att optimera och minska dina totala Azure-utgifter. Mer information finns i [Advisor Cost rekommendationer](advisor-cost-recommendations.md).
* **Operational Excellence**: För att hjälpa dig att uppnå process- och arbetsflödeseffektivitet, resurshantbarhet och metodtips för distribution. . Mer information finns i [Advisor Operational Excellence rekommendationer](advisor-operational-excellence-recommendations.md).

  ![Rekommendationstyper för rådgivare](./media/advisor-overview/advisor-dashboard.png)

Du kan klicka på en kategori för att visa listan med rekommendationer inom den kategorin och välja en rekommendation om du vill veta mer om den.  Du kan också lära dig mer om åtgärder som du kan utföra för att dra nytta av en affärsmöjlighet eller lösa ett problem.

![Kategorin rekommendation för rådgivare](./media/advisor-overview/advisor-ha-category-example.png) 

Välj den rekommenderade åtgärden för en rekommendation för att implementera rekommendationen.  Ett enkelt gränssnitt öppnas som gör att du kan implementera rekommendationen eller hänvisa dig till dokumentation som hjälper dig med implementeringen.  När du implementerar en rekommendation kan det ta upp till en dag för Advisor att inse det.

Om du inte tänker vidta omedelbara åtgärder på en rekommendation kan du skjuta upp den under en angiven tidsperiod eller avvisa den.  Om du inte vill få rekommendationer för en viss prenumeration eller resursgrupp kan du konfigurera Advisor så att den bara genererar rekommendationer för angivna prenumerationer och resursgrupper.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="how-do-i-access-advisor"></a>Hur får jag åtkomst till Advisor?
Du kan komma åt Advisor via [Azure-portalen](https://aka.ms/azureadvisordashboard). Logga in på [portalen,](https://portal.azure.com)leta reda på **Advisor** i navigeringsmenyn eller sök efter den på menyn **Alla tjänster.**

Du kan också visa Advisor-rekommendationer via resursgränssnittet för virtuella datorer. Välj en virtuell dator och bläddra sedan till Advisor-rekommendationerna på menyn. 

### <a name="what-permissions-do-i-need-to-access-advisor"></a>Vilka behörigheter behöver jag för att komma åt Advisor?
 
Du kan komma åt Advisor-rekommendationer som *ägare,* *deltagare*eller *läsare* av en prenumeration.

### <a name="what-resources-does-advisor-provide-recommendations-for"></a>Vilka resurser ger Advisor rekommendationer för?

Advisor ger rekommendationer för Application Gateway, App Services, tillgänglighetsuppsättningar, Azure Cache, Azure Data Factory, Azure Database for MySQL, Azure Database for PostgreSQL, Azure Database for MariaDB, Azure ExpressRoute, Azure Cosmos DB, Azure public IP-adresser, SQL Data Warehouse, SQL-servrar, lagringskonton, Traffic Manager-profiler och virtuella datorer.

Azure Advisor innehåller också dina rekommendationer från [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-recommendations) som kan innehålla rekommendationer för ytterligare resurstyper.

### <a name="can-i-postpone-or-dismiss-a-recommendation"></a>Kan jag skjuta upp eller avvisa en rekommendation?

Om du vill skjuta upp eller avvisa en rekommendation klickar du på länken **Skjuta upp.** Du kan ange en uppskjutningsperiod eller välja **Aldrig** att avvisa rekommendationen.

## <a name="next-steps"></a>Nästa steg

Mer information om Advisor-rekommendationer finns i:

* [Kom igång med Advisor](advisor-get-started.md)
* [Advisor rekommendationer för hög tillgänglighet](advisor-high-availability-recommendations.md)
* [Säkerhetsrekommendationer för rådgivare](advisor-security-recommendations.md)
* [Advisors prestandarekommendationer](advisor-performance-recommendations.md)
* [Kostnadsrekommendationer för rådgivare](advisor-cost-recommendations.md)
