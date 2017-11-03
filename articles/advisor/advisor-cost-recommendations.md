---
title: "Azure kostnaden för Advisor-rekommendationer | Microsoft Docs"
description: "Använd Azure Advisor för att optimera kostnader för Azure-distributioner."
services: advisor
documentationcenter: NA
author: KumudD
manager: carmonm
editor: 
ms.assetid: 
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
ms.openlocfilehash: 51320d93689da3e37c0946d8877b27a11793d9c7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="advisor-cost-recommendations"></a>Kostnad Advisor-rekommendationer

Advisor hjälper dig att optimera och minska din övergripande Azure tillbringar med att identifiera inaktiv och underutnyttjade resurser. Du kan hämta cost rekommendationerna från den **kostnaden** på Advisor-instrumentpanelen.

![Advisor kostnaden fliken](./media/advisor-cost-recommendations/advisor-cost-tab2.png)

## <a name="optimize-virtual-machine-spend-by-resizing-underutilized-instances"></a>Optimera virtuella tillbringar genom att ändra storlek underutnyttjade instanser 
Även om vissa Programscenarier kan resultera i lågt utnyttjande av design, kan du ofta spara pengar genom att hantera storlek och antalet virtuella datorer. Advisor övervakar din användning av virtuella datorer på 14 dagar och identifierar låg belastning virtuella datorer. Virtuella datorer vars CPU-användning är 5 procent eller mindre och nätverksanvändning är 7 MB eller mindre för fyra eller flera dagar betraktas som låg belastning virtuella datorer.

Advisor visar de uppskattade kostnaderna för fortsätter att köra den virtuella datorn så att du kan välja att stänga av eller ändra storlek på den.  

![Advisor kostnad rekommendationer för storleksändring av virtuella datorer](./media/advisor-cost-recommendations/advisor-cost-resizevms.png)

## <a name="use-a-cost-effective-solution-to-manage-performance-goals-of-multiple-sql-databases"></a>Använda en kostnadseffektiv lösning för att hantera prestandamål av flera SQL-databaser
Advisor identifierar SQL server-instanser som kan dra nytta av att skapa elastiska databaspooler. Elastiska databaspooler ger en enkel och kostnadseffektiv lösning för att hantera prestandamål av flera databaser som har olika användningsmönster. Läs mer om Azure elastiska pooler [vad är en Azure elastisk pool?](https://azure.microsoft.com/en-us/documentation/articles/sql-database-elastic-pool/).

![Advisor kostnad rekommendationer för elastiska databaspooler](./media/advisor-cost-recommendations/advisor-cost-elasticdbpools.png)

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>Hur du kommer åt kostnaden rekommendationerna i Azure Advisor

1. Logga in på [Azure Portal](https://portal.azure.com).

2. I den vänstra rutan klickar du på **fler tjänster**.

3. I fönstret service menyn under **övervakning och hantering av**, klickar du på **Azure Advisor**.  
 Advisor-instrumentpanelen visas.

4. Advisor-instrumentpanelen, klicka på den **kostnaden** fliken.

5. Välj den prenumeration som du vill få rekommendationer och klicka sedan på **få rekommendationer**.

> [!NOTE]
> Om du vill komma åt Advisor-rekommendationer, måste du först *registrera prenumerationen* med Advisor. En prenumeration registreras när en *prenumeration ägare* startar Advisor instrumentpanelen och klickar på den **få rekommendationer** knappen. Det här är en *engångsåtgärd*. När prenumerationen har registrerats kan du komma åt Advisor-rekommendationer som *ägare*, *deltagare*, eller *Reader* för en prenumeration, resursgrupp eller en viss resurs.

## <a name="next-steps"></a>Nästa steg

Mer information om Advisor-rekommendationer finns:
* [Introduktion till Advisor](advisor-overview.md)
* [Kom igång](advisor-get-started.md)
* [Advisor-rekommendationer](advisor-cost-recommendations.md)
* [Advisor-rekommendationer för hög tillgänglighet](advisor-cost-recommendations.md)
* [Advisor säkerhetsrekommendationer](advisor-cost-recommendations.md)
