---
title: "Kom igång med Azure Advisor | Microsoft Docs"
description: "Kom igång med Azure Advisor."
services: advisor
documentationcenter: NA
author: manbeenkohli
manager: carmonm
editor: 
ms.assetid: 
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/10/2017
ms.author: makohli
ms.openlocfilehash: a662841bebda460d4225e080f16705b3f16fdc46
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="get-started-with-azure-advisor"></a>Kom igång med Azure Advisor

Lär dig att komma åt Advisor via Azure portal, få rekommendationer, implementera rekommendationer, söka efter rekommendationer och uppdatera rekommendationer.

## <a name="get-advisor-recommendations"></a>Få Advisor-rekommendationer

1. Logga in på [Azure Portal](https://portal.azure.com).

2. I den vänstra rutan klickar du på **fler tjänster**.

3. I fönstret service menyn under **övervakning och hantering av**, klickar du på **Azure Advisor**.  
 Advisor-instrumentpanelen visas.

   ![Klassificering av åtkomst till Azure med Azure-portalen](./media/advisor-overview/advisor-azure-portal-menu.png) 

4. Välj den prenumeration som du vill få rekommendationer på Advisor-instrumentpanelen.  
Advisor-instrumentpanelen innehåller anpassad rekommendationer för den valda prenumerationen. 

5. Få rekommendationer för en viss kategori klickar du på någon av flikarna: **hög tillgänglighet**, **säkerhet**, **prestanda**, eller **kostnaden**.
 
> [!NOTE]
> Om du vill komma åt Advisor-rekommendationer, måste du först *registrera prenumerationen* med Advisor. En prenumeration registreras när en *prenumeration ägare* startar Advisor instrumentpanelen och klickar på den **få rekommendationer** knappen. Det här är en *engångsåtgärd*. När prenumerationen har registrerats kan du komma åt Advisor-rekommendationer som *ägare*, *deltagare*, eller *Reader* för en prenumeration, resursgrupp eller en viss resurs.

  ![Azure Advisor-instrumentpanelen](./media/advisor-overview/advisor-all-tab.png)

## <a name="get-advisor-recommendation-details-and-implement-a-solution"></a>Hämta information om Advisor rekommendation och implementera en lösning

Den **rekommendation** bladet i Advisor innehåller ytterligare information om hur rekommendationen. 

1. Logga in på den [Azure-portalen](https://portal.azure.com), och sedan starta [Azure Advisor](https://aka.ms/azureadvisordashboard).

2. På den **Advisor-rekommendationer** instrumentpanelen, klickar du på **få rekommendationer**.

3. Klicka på en rekommendation som du vill granska i detalj i listan över rekommendationer.  
Den **rekommendation** bladet visas.

4. På den **rekommendationer** bladet granska information om åtgärder du kan utföra för att lösa eventuella problem eller dra nytta av en kostnad spara affärsmöjlighet. 
  
  ![Bladet Advisor-rekommendationer](./media/advisor-overview/advisor-recommendation-action-example.png)

## <a name="search-for-advisor-recommendations"></a>Sök efter Advisor-rekommendationer

Du kan söka efter rekommendationer för en viss grupp av prenumerationen eller resursen. Du kan också söka efter rekommendationer efter status.

1. Logga in på den [Azure-portalen](https://portal.azure.com), och sedan starta [Azure Advisor](https://aka.ms/azureadvisordashboard).

2. Sök efter rekommendationer genom att filtrera för prenumerationer och resursgrupper rekommendation status (**Active** eller **Snoozed**).

3. Om du vill visa en lista med Advisor-rekommendationer som baseras på dina sökfilter kriterier klickar du på **få rekommendationer**.

  ![Advisor sökfilter villkor](./media/advisor-get-started/advisor-search.png)

## <a name="snooze-or-dismiss-advisor-recommendations"></a>Viloläge eller ignorera Advisor-rekommendationer

1. Logga in på den [Azure-portalen](https://portal.azure.com), och sedan starta [Azure Advisor](https://aka.ms/azureadvisordashboard).

2. Klicka på **få rekommendationer**, och klicka sedan på en rekommendation av rekommendationer.

3. På den **rekommendation** bladet, klickar du på **viloläge**.  

   ![Advisor rekommendation åtgärd exempel](./media/advisor-get-started/advisor-snooze.png)

4. Ange en viloläge tidsperiod, eller välj **aldrig** stänga rekommendationen.


## <a name="next-steps"></a>Nästa steg

Mer information om Advisor finns:
* [Introduktion till Azure Advisor](advisor-overview.md)
* [Advisor-rekommendationer för hög tillgänglighet](advisor-high-availability-recommendations.md)
* [Advisor säkerhetsrekommendationer](advisor-security-recommendations.md)
-  [Advisor-rekommendationer](advisor-performance-recommendations.md)
* [Kostnad Advisor-rekommendationer](advisor-performance-recommendations.md)
