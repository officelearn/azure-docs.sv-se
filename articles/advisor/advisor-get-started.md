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
ms.openlocfilehash: d15f91c91281eae28ddaff52af3ee8e617f142c1
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="get-started-with-azure-advisor"></a>Kom igång med Azure Advisor

Lär dig att komma åt Advisor via Azure portal, få rekommendationer och implementera rekommendationer.

## <a name="get-advisor-recommendations"></a>Få Advisor-rekommendationer

1. Logga in på [Azure Portal](https://portal.azure.com).

2. I den vänstra rutan klickar du på **Advisor**.  Om du inte ser Advisor i den vänstra rutan klickar du på **alla tjänster**.  I fönstret service menyn under **övervakning och hantering av**, klickar du på **Advisor**.
 Advisor-instrumentpanelen visas.

   ![Klassificering av åtkomst till Azure med Azure-portalen](./media/advisor-get-started/advisor-portal-menu.png) 

4. Advisor-instrumentpanelen visar en sammanfattning av rekommendationer för alla valda prenumerationer.  Du kan välja de prenumerationer som du vill rekommendationer som ska visas för att använda prenumerationen filtrera listrutan.

5. Få rekommendationer för en viss kategori klickar du på någon av flikarna: **hög tillgänglighet**, **säkerhet**, **prestanda**, eller **kostnaden**.
 
> [!NOTE]
> Du använder Azure Advisor med en prenumeration på en prenumeration *ägare* får starta Advisor-instrumentpanelen.  Den här åtgärden registrerar prenumerationen med Advisor.  Från den punkten på någon prenumeration *ägare*, *deltagare*, eller *Reader* kan komma åt Advisor-rekommendationer för prenumerationen.  

  ![Azure Advisor-instrumentpanelen](./media/advisor-overview/advisor-dashboard.png)

## <a name="get-advisor-recommendation-details-and-implement-a-solution"></a>Hämta information om Advisor rekommendation och implementera en lösning

Du kan välja en rekommendation i Advisor att visa ytterligare information – till exempel rekommendation åtgärder och vilka resurser – och att implementera lösningen på rekommendationen.  

1. Logga in på den [Azure-portalen](https://portal.azure.com), och sedan öppna [Advisor](https://aka.ms/azureadvisordashboard).

2. Välj en rekommendation kategori för att visa en lista över rekommendationerna i den kategorin eller Välj den **alla** att visa alla rekommendationer.

3. Klicka på en rekommendation som du vill granska i detalj.

4. Granska informationen om rekommendationen och resurser som rekommendationen gäller för.

5. Klicka på den **rekommenderad åtgärd** att följa rekommendationen.

## <a name="filter-advisor-recommendations"></a>Filtrera Advisor-rekommendationer

Du kan filtrera rekommendationer för att öka detaljnivån till vad som är viktigast för dig.  Du kan filtrera efter prenumeration, resurstypen eller rekommendation status.  

1. Logga in på den [Azure-portalen](https://portal.azure.com), och sedan öppna [Advisor](https://aka.ms/azureadvisordashboard).

2.  Använd de nedrullningsbara listorna på Advisor-instrumentpanelen för att filtrera efter prenumeration, resurstypen eller rekommendation status.

    ![Advisor sökfilter villkor](./media/advisor-get-started/advisor-filters.png)

## <a name="snooze-or-dismiss-advisor-recommendations"></a>Viloläge eller ignorera Advisor-rekommendationer

1. Logga in på den [Azure-portalen](https://portal.azure.com), och sedan öppna [Advisor](https://aka.ms/azureadvisordashboard).

2. Gå till rekommendationen som du vill viloläge eller stängas.

3. Klicka på rekommendationen.

4. Klicka på **viloläge**. 

5. Ange en viloläge tidsperiod, eller välj **aldrig** stänga rekommendationen.

## <a name="exclude-subscriptions-or-resource-groups-from-advisor"></a>Exkludera prenumerationer eller resursgrupper från Advisor

Du kan ha resursgrupper eller prenumerationer för som du inte vill ta emot Advisor-rekommendationer – till exempel ”test” resurser.  Du kan konfigurera Advisor för att endast skapa rekommendationer för specifika prenumerationer och resursgrupper.

> [!NOTE]
> Om du vill inkludera eller exkludera en prenumeration eller resursgrupp från Advisor, måste du vara en prenumeration ägare.  Om du inte har behörigheterna som krävs för en prenumeration eller resursgrupp inaktiveras alternativet för att inkludera eller exkludera den i användargränssnittet.

1. Logga in på den [Azure-portalen](https://portal.azure.com), och sedan öppna [Advisor](https://aka.ms/azureadvisordashboard).

2. Klicka på **konfigurera** i åtgärdsfältet.

3. Avmarkera alla prenumerationer eller resursgrupper som du inte vill få Advisor-rekommendationer för.

    ![Advisor konfigurera resurser exempel](./media/advisor-get-started/advisor-configure-resources.png)

4. Klicka på den **tillämpa** knappen.

## <a name="configure-the-average-cpu-utilization-rule-for-the-low-usage-virtual-machine-recommendation"></a>Konfigurera Genomsnittlig CPU-användning regel för virtuell dator rekommenderar låg belastning

Advisor övervakar din användning av virtuella datorer på 14 dagar och identifierar låg belastning virtuella datorer. Virtuella datorer vars Genomsnittlig CPU-användning är 5 procent eller mindre och nätverksanvändning är 7 MB eller mindre för fyra eller flera dagar betraktas som låg belastning virtuella datorer.

Om du vill att mer aggressivt för att fastställa låg belastning virtuella datorer kan du justera regeln Genomsnittlig CPU-användning på grundval av per prenumeration.  Genomsnittlig CPU-användning regeln kan anges till 5% 10%, 15% eller 20%.

> [!NOTE]
> Om du vill justera Genomsnittlig CPU-användning regeln för att identifiera låg belastning virtuella datorer, måste du vara en prenumeration *ägare*.  Om du inte har behörigheterna som krävs för en prenumeration eller resursgrupp inaktiveras alternativet för att inkludera eller exkludera den i användargränssnittet. 

1. Logga in på den [Azure-portalen](https://portal.azure.com), och sedan öppna [Advisor](https://aka.ms/azureadvisordashboard).

2. Klicka på **konfigurera** i åtgärdsfältet.

3. Klicka på den **regler** fliken.

4. Välj den prenumeration som du vill justera Genomsnittlig CPU-användning regeln för och klicka sedan på **redigera**.

5. Välj önskad CPU-användning medelvärdet och klicka på **tillämpa**.

6. Klicka på **uppdatera rekommendationer** att uppdatera din befintliga rekommendationer för att använda den nya genomsnittliga CPU-användning regeln. 

   ![Advisor konfigurera rekommendation regler exempel](./media/advisor-get-started/advisor-configure-rules.png)

## <a name="download-your-advisor-recommendations"></a>Hämta Advisor-rekommendationer

Advisor kan du hämta en sammanfattning av rekommendationerna.  Du kan hämta din rekommendationer som en PDF-fil eller en CSV-fil.  Hämta rekommendationerna kan du enkelt dela med dina kollegor eller utföra analyser ovanpå rekommendation data.

1. Logga in på den [Azure-portalen](https://portal.azure.com), och sedan öppna [Advisor](https://aka.ms/azureadvisordashboard).

2. Klicka på **ladda ned som CSV** eller **hämta som PDF** i åtgärdsfältet.

Alternativet download respekterar eventuella filter som du har kopplat till Advisor-instrumentpanelen.  Om du väljer alternativet Hämta när du visar en specifik rekommendation kategori eller rekommendation innehåller hämtade sammanfattningen endast information för kategori eller rekommendation. 

## <a name="next-steps"></a>Nästa steg

Mer information om Advisor finns:
* [Introduktion till Azure Advisor](advisor-overview.md)
* [Advisor-rekommendationer för hög tillgänglighet](advisor-high-availability-recommendations.md)
* [Advisor säkerhetsrekommendationer](advisor-security-recommendations.md)
-  [Advisor-rekommendationer](advisor-performance-recommendations.md)
* [Kostnad Advisor-rekommendationer](advisor-performance-recommendations.md)
