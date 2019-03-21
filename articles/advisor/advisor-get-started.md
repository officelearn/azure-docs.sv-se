---
title: Kom igång med Azure Advisor | Microsoft Docs
description: Kom igång med Azure Advisor.
services: advisor
author: kasparks
ms.service: advisor
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/01/2019
ms.author: kasparks
ms.openlocfilehash: a7e82fffdd9c865de6040c05ec28bc8bd2dced61
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58111403"
---
# <a name="get-started-with-azure-advisor"></a>Kom igång med Azure Advisor

Lär dig att komma åt Advisor via Azure portal, få rekommendationer och implementera rekommendationer.

> [!NOTE]
> Azure Advisor körs automatiskt i bakgrunden att hitta nyligen skapade resurser. Det kan ta upp till 24 timmar att tillhandahålla rekommendationer för dessa resurser.

## <a name="get-recommendations"></a>Få rekommendationer

1. Logga in på [Azure Portal](https://portal.azure.com).

1. I den vänstra rutan klickar du på **Advisor**.  Om du inte ser Advisor i den vänstra rutan klickar du på **alla tjänster**.  I fönstret service menyn under **övervakning och hantering av**, klickar du på **Advisor**. Advisor-instrumentpanelen visas.

   ![Åtkomst till Azure Advisor i Azure Portal](./media/advisor-get-started/advisor-portal-menu.png) 

1. Advisor-instrumentpanelen visas en sammanfattning av dina rekommendationer för alla valda prenumerationer.  Du kan välja de prenumerationer som du vill att rekommendationer som ska visas för prenumerationen filtrera listrutan.

1. Klicka på någon av flikarna för att få rekommendationer för en viss kategori: **Hög tillgänglighet**, **Security**, **prestanda**, eller **kostnaden**. 

   ![Azure Advisor-instrumentpanelen](./media/advisor-overview/advisor-dashboard.png)

## <a name="get-recommendation-details-and-implement-a-solution"></a>Hämta information om rekommendationen och implementera en lösning

Du kan välja en rekommendation i Advisor att visa ytterligare information – till exempel rekommendation åtgärder och resurser som påverkas – och för att implementera lösningen i rekommendationen.  

1. Logga in på den [Azure-portalen](https://portal.azure.com), och öppna sedan [Advisor](https://aka.ms/azureadvisordashboard).

1. Välj en rekommendation kategori för att visa en lista över rekommendationer i den kategorin eller markera den **alla** fliken för att visa alla rekommendationer.

1. Klicka på en rekommendation som du vill granska i detalj.

1. Granska information om rekommendationen och de resurser som rekommendationen gäller för.

1. Klicka på den **rekommenderad åtgärd** du implementerar rekommendationen.

## <a name="filter-recommendations"></a>Filtrera rekommendationer

Du kan filtrera rekommendationer för att öka detaljnivån till vad som är viktigast för dig.  Du kan filtrera efter prenumeration, resurstypen eller rekommendation status.  

1. Logga in på den [Azure-portalen](https://portal.azure.com), och öppna sedan [Advisor](https://aka.ms/azureadvisordashboard).

1. Använd listrutorna på Advisor-instrumentpanelen för att filtrera efter prenumeration, resurstypen eller rekommendation status.

    ![Advisor-sökfilter kriterier](./media/advisor-get-started/advisor-filters.png)

## <a name="postpone-or-dismiss-recommendations"></a>Skjut upp och avvisa rekommendationer

1. Logga in på den [Azure-portalen](https://portal.azure.com), och öppna sedan [Advisor](https://aka.ms/azureadvisordashboard).

1. Navigera till den rekommendationen som du vill skjuta upp eller avvisa.

1. Klicka på rekommendationen.

1. Klicka på **skjuta upp**. 

1. Ange en Skjut upp tidsperiod, eller välj **aldrig** att Stäng rekommendationen.

## <a name="exclude-subscriptions-or-resource-groups"></a>Undanta prenumerationer eller resursgrupper

Du kan ha resursgrupper eller prenumerationer för vilket du inte vill få Advisor-rekommendationer – till exempel ”test”.  Du kan konfigurera Advisor för att endast skapa rekommendationer för specifika prenumerationer och resursgrupper.

> [!NOTE]
> Om du vill inkludera eller exkludera en prenumeration eller resursgrupp från Advisor, måste du vara ägare för en prenumeration.  Om du inte har behörigheterna som krävs för en prenumeration eller resursgrupp inaktiveras alternativet att inkludera eller exkludera den i användargränssnittet.

1. Logga in på den [Azure-portalen](https://portal.azure.com), och öppna sedan [Advisor](https://aka.ms/azureadvisordashboard).

1. Klicka på **konfigurera** i åtgärdsfältet.

1. Avmarkera alla prenumerationer eller resursgrupper som du inte vill få Advisor-rekommendationer för.

    ![Advisor konfigurera resurser exempel](./media/advisor-get-started/advisor-configure-resources.png)

1. Klicka på knappen **Använd**.

## <a name="configure-low-usage-vm-recommendation"></a>Konfigurera med låg användning rekommendation för virtuella datorer

Den här proceduren konfigurerar att regeln för användning av Genomsnittlig CPU för rekommendationen för virtuella datorer med låg användning.

Advisor övervakar din användning av virtuella datorer under 14 dagar och identifierar virtuella datorer med låg användning. Virtuella datorer vars genomsnittliga CPU-användningen är 5% eller mindre och nätverksanvändning är 7 MB eller mindre för fyra eller flera dagar betraktas som låg användning virtuella datorer.

Om du vill vara mer aggressivt för att fastställa VM med låg användning kan justera du Genomsnittlig CPU användning regeln på basis av per prenumeration.  Regeln för Genomsnittlig CPU-användning kan ställas in till % 5, 10%, 15% eller 20%.

> [!NOTE]
> Om du vill justera Genomsnittlig CPU användning regeln för att identifiera virtuella datorer med låg användning, måste du vara en prenumeration *ägare*.  Om du inte har behörigheterna som krävs för en prenumeration eller resursgrupp inaktiveras kan inkludera eller exkludera den i användargränssnittet. 

1. Logga in på den [Azure-portalen](https://portal.azure.com), och öppna sedan [Advisor](https://aka.ms/azureadvisordashboard).

1. Klicka på **konfigurera** i åtgärdsfältet.

1. Klicka på den **regler** fliken.

1. Välj den prenumeration som du vill justera Genomsnittlig CPU användning regeln för och klicka sedan på **redigera**.

1. Välj det önskade genomsnittliga CPU användning värdet och klicka på **tillämpa**.

1. Klicka på **uppdatera rekommendationer** att uppdatera dina befintliga rekommendationer att använda den nya genomsnittliga CPU användning regeln. 

   ![Advisor konfigurera rekommendation regler exempel](./media/advisor-get-started/advisor-configure-rules.png)

## <a name="download-recommendations"></a>Ladda ned rekommendationer

Advisor kan du ladda ned en sammanfattning för dina rekommendationer.  Du kan ladda ned dina rekommendationer som en PDF-fil eller en CSV-fil.  Ladda ned dina rekommendationer kan du enkelt kan dela med dina kollegor eller utföra egna analyser ovanpå rekommendation data.

1. Logga in på den [Azure-portalen](https://portal.azure.com), och öppna sedan [Advisor](https://aka.ms/azureadvisordashboard).

1. Klicka på **hämta som CSV** eller **hämta som PDF** i åtgärdsfältet.

Alternativet download värnar om eventuella filter som du har kopplat till Advisor-instrumentpanelen.  Om du väljer Hämtningsalternativet medan du visar en specifik rekommendation kategori eller rekommendation innehåller hämtade sammanfattningen endast information för kategorin eller rekommendation. 

## <a name="next-steps"></a>Nästa steg

Mer information om Advisor finns:

- [Introduktion till Azure Advisor](advisor-overview.md)
- [Advisor-rekommendationer för hög tillgänglighet](advisor-high-availability-recommendations.md)
- [Advisor säkerhetsrekommendationer](advisor-security-recommendations.md)
- [Advisor-rekommendationer](advisor-performance-recommendations.md)
- [Advisor kostnadsrekommendationer](advisor-performance-recommendations.md)
