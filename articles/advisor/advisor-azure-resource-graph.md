---
title: Advisor-data i Azure Resource Graph
description: Skapa frågor för Advisor-data i Azure Resource Graph
ms.topic: article
ms.date: 03/12/2020
ms.author: sagupt
ms.openlocfilehash: f8ad8fd450bc004d9caa2699922717f38d38b482
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502456"
---
# <a name="query-for-advisor-data-in-resource-graph-explorer-azure-resource-graph"></a>Fråga efter Advisor-data i Resource Graph Explorer (Azure Resource Graph)

Advisor-resurser finns nu till [Azure Resource Graph](https://azure.microsoft.com/features/resource-graph/). Detta lägger grunden för många i skala kundscenarier för Advisor rekommendationer. Få scenarier som inte var möjliga innan att göra i stor skala och nu kan uppnås med hjälp av Resource Graph är:
* Ger funktioner för att utföra komplexa frågor för alla dina prenumerationer i Azure Portal
* Rekommendationer sammanfattas efter kategorityper (som hög tillgänglighet, prestanda) och effekttyper (hög, medel, låg)
* Alla rekommendationer för en viss rekommendationstyp
* Påverkat resursantal efter rekommendationskategori

![Rådgivare i Utforskaren för Azure-resursdiagram](./media/azure-resource-graph-1.png)  


## <a name="advisor-resource-types-in-azure-graph"></a>Resurstyper för rådgivare i Azure Graph

Resurstyper för tillgänglig rådgivare i [Resursdiagram:](https://docs.microsoft.com/azure/governance/resource-graph/)Det finns tre resurstyper som är tillgängliga för frågor under Advisor-resurser. Här är listan över de resurser som nu är tillgängliga för frågor i Resource Graph.
* Microsoft.Advisor/konfigurationer
* Microsoft.Advisor/rekommendationer
* Microsoft.Advisor/dämpningar

Dessa resurstyper visas under en ny tabell med namnet AdvisorResources, som du också kan fråga i Resource Graph Explorer i Azure-portalen.


## <a name="next-steps"></a>Nästa steg

Mer information om Advisor-rekommendationer finns i:
* [Introduktion till Azure Advisor](advisor-overview.md)
* [Kom igång med Advisor](advisor-get-started.md)
* [Kostnadsrekommendationer för rådgivare](advisor-cost-recommendations.md)
* [Advisors prestandarekommendationer](advisor-performance-recommendations.md)
* [Säkerhetsrekommendationer för rådgivare](advisor-security-recommendations.md)
* [Advisor Operational Excellence rekommendationer](advisor-operational-excellence-recommendations.md)
* [REST-API för rådgivare](https://docs.microsoft.com/rest/api/advisor/)
