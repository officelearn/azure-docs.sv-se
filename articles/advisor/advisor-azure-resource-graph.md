---
title: Advisor-data i Azure Resource Graph
description: Skapa frågor för Advisor-data i Azure Resource Graph
ms.topic: article
ms.date: 03/12/2020
ms.author: sagupt
ms.openlocfilehash: f8ad8fd450bc004d9caa2699922717f38d38b482
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "79502456"
---
# <a name="query-for-advisor-data-in-resource-graph-explorer-azure-resource-graph"></a>Fråga efter Advisor-data i resurs diagram Utforskaren (Azure Resource Graph)

Advisor-resurser registreras nu i [Azure Resource Graph](https://azure.microsoft.com/features/resource-graph/). Det här är en grund för många i och skalade kund scenarier för Advisor-rekommendationer. Några scenarier som inte var möjliga innan du skulle göra i skala och nu kan uppnås med hjälp av resurs diagram:
* Ger möjlighet att utföra komplexa frågor för alla dina prenumerationer i Azure Portal
* Rekommendationer sammanfattas av kategori typer (t. ex. hög tillgänglighet, prestanda) och effekt typer (hög, medel, låg)
* Alla rekommendationer för en viss rekommendations typ
* Kategorin påverkade resurs antal efter rekommendation

![Advisor i Azure Resource Graph Explorer](./media/azure-resource-graph-1.png)  


## <a name="advisor-resource-types-in-azure-graph"></a>Advisor-resurs typer i Azure Graph

Tillgängliga Advisor-resurs typer i [resurs diagram](https://docs.microsoft.com/azure/governance/resource-graph/): det finns 3 resurs typer som är tillgängliga för frågor under Advisor-resurser. Här är en lista över de resurser som nu är tillgängliga för frågor i resurs diagram.
* Microsoft. Advisor/konfigurationer
* Microsoft. Advisor/rekommendationer
* Microsoft. Advisor/undertrycker

Dessa resurs typer visas under en ny tabell med namnet as AdvisorResources, som du även kan fråga i resurs diagram Utforskaren i Azure Portal.


## <a name="next-steps"></a>Nästa steg

Mer information om Advisor-rekommendationer finns i:
* [Introduktion till Azure Advisor](advisor-overview.md)
* [Kom igång med Advisor](advisor-get-started.md)
* [Rekommendationer om Advisor-kostnader](advisor-cost-recommendations.md)
* [Rekommendationer för Advisor-prestanda](advisor-performance-recommendations.md)
* [Rekommendationer för Advisor-säkerhet](advisor-security-recommendations.md)
* [Rekommendationer om operativa rekommendationer](advisor-operational-excellence-recommendations.md)
* [Advisor REST API](https://docs.microsoft.com/rest/api/advisor/)
