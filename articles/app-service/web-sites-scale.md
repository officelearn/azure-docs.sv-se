---
title: Skala upp funktioner och kapaciteter – Azure App Service | Microsoft Docs
description: Lär dig mer om att skala upp en app i Azure App Service för att lägga till kapacitet och funktioner.
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: mollybos
ms.assetid: f7091b25-b2b6-48da-8d4a-dcf9b7baccab
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 98d3d1f6fc0f2f30196f360811808579dfbab312
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60832152"
---
# <a name="scale-up-an-app-in-azure"></a>Skala upp en app i Azure

> [!NOTE]
> Den nya **PremiumV2** nivån får du snabbare processorer, SSD-lagring och fördubblar minne-till-kärna-förhållande på befintliga prisnivåer. Du kan spara pengar genom att köra dina appar på färre instanser med fördelen prestanda. Att skala upp till **PremiumV2** nivå, finns i [konfigurera PremiumV2-nivå för App Service](app-service-configure-premium-tier.md).
>

Den här artikeln visar hur du skalar din app i Azure App Service. Det finns två arbetsflöden för skalning, skala upp och skala ut och den här artikeln förklarar skalan in arbetsflöde.

* [Skala upp](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): Få mer CPU, minne, diskutrymme och extra funktioner såsom dedikerade virtuella datorer (VM), anpassade domäner och certifikat, platser och automatisk skalning. Du skala upp genom att ändra prisnivån för App Service-plan som appen hör till.
* [Skala ut](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): Öka antalet VM-instanser som kör din app.
  Du kan skala ut till upp till 20 instanser, beroende på vilken prisnivå. [App Service-miljöer](environment/intro.md) i **isolerad** ytterligare nivå ökar din skala ut antalet till 100 instanser. Läs mer om att skala ut [skala instansantalet manuellt eller automatiskt](../monitoring-and-diagnostics/insights-how-to-scale.md). Där kan ta du reda på hur du använder automatisk skalning, vilket är att skala instansantalet automatiskt baserat på fördefinierade regler och scheman.

Inställningarna för tar bara några sekunder att tillämpa och påverkar alla appar i din [App Service-plan](../app-service/overview-hosting-plans.md).
De kräver inte att ändra koden eller distribuera om ditt program.

Information om priser och funktioner i enskilda App Service-planer finns i [prisinformation för App Service](https://azure.microsoft.com/pricing/details/web-sites/).  

> [!NOTE]
> Innan du växlar en App Service-plan från den **kostnadsfri** nivå, måste du först ta bort den [utgiftsgränser](https://azure.microsoft.com/pricing/spending-limits/) för din Azure-prenumeration. Om du vill visa eller ändra alternativ för din Microsoft Azure App Service-prenumeration, se [Microsoft Azure-prenumerationer][azuresubscriptions].
> 
> 

<a name="scalingsharedorbasic"></a>
<a name="scalingstandard"></a>

## <a name="scale-up-your-pricing-tier"></a>Skala upp din prisnivå
1. I webbläsaren, öppna den [Azure-portalen][portal].
2. I din App Service-app-sida klickar du på **alla inställningar**, och klicka sedan på **skala upp**.
   
    ![Navigera för att skala upp din Azure-app.][ChooseWHP]
3. Välj nivå och klicka sedan på **tillämpa**.
   
    Den **meddelanden** fliken kommer flash en grön **lyckades** när åtgärden har slutförts.

<a name="ScalingSQLServer"></a>

## <a name="scale-related-resources"></a>Skala relaterade resurser
Om din app är beroende av andra tjänster som Azure SQL Database eller Azure Storage, kan du skala upp de här resurserna separat. De här resurserna hanteras inte av App Service-planen.

1. I **Essentials**, klickar du på den **resursgrupp** länk.
   
    ![Skala upp din Azure-app relaterade resurser](./media/web-sites-scale/RGEssentialsLink.png)
2. I den **sammanfattning** en del av den **resursgrupp** klickar du på en resurs som du vill skala. I följande skärmbild visas en SQL Database-resurs och en Azure Storage-resurs.
   
    ![Gå till sidan med resursgrupper att skala upp din Azure-app](./media/web-sites-scale/ResourceGroup.png)
3. För en SQL Database-resurs, klickar du på **inställningar** > **prisnivå** skala prisnivån.
   
    ![Skala upp SQL Database-serverdelen för din Azure-app](./media/web-sites-scale/ScaleDatabase.png)
   
    Du kan också aktivera [geo-replikering](../sql-database/sql-database-geo-replication-overview.md) för SQL Database-instansen.
   
    För ett Azure Storage-resurs, klickar du på **inställningar** > **Configuration** att skala upp din lagringsalternativ.
   
    ![Skala upp Azure Storage-kontot som används av din Azure-app](./media/web-sites-scale/ScaleStorage.png)

<a name="OtherFeatures"></a>
<a name="devfeatures"></a>

## <a name="compare-pricing-tiers"></a>Jämför prisnivåer
Detaljerad information, till exempel VM-storlekar för varje prisnivå finns i [prisinformation för App Service](https://azure.microsoft.com/pricing/details/web-sites/).
En tabell av tjänstbegränsningar, kvoter och begränsningar och funktioner som stöds i respektive nivå finns [gränser för Apptjänst](../azure-subscription-service-limits.md#app-service-limits).

<a name="Next Steps"></a>

## <a name="next-steps"></a>Nästa steg
* Information om priser, support och SLA finns i följande länkar:
  
    [Prisinformation om dataöverföringar](https://azure.microsoft.com/pricing/details/data-transfers/)
  
    [Supportavtal för Microsoft Azure](https://azure.microsoft.com/support/plans/)
  
    [serviceavtal](https://azure.microsoft.com/support/legal/sla/)
  
    [Prisinformation för SQL-databas](https://azure.microsoft.com/pricing/details/sql-database/)
  
    [Virtuell dator och Molntjänststorlekar för Microsoft Azure][vmsizes]
  
* Information om Azure App Service finns metodtips, inklusive att skapa en skalbar och återhämtningsbar arkitektur [metodtips: Azure App Service Web Apps](https://azure.microsoft.com/blog/best-practices-windows-azure-websites-waws/).
* Videor om skalning av App Service-appar, finns i följande resurser:
  
  * [När du skalar Azure Websites - med Stefan Schackow](https://azure.microsoft.com/resources/videos/azure-web-sites-free-vs-standard-scaling/)
  * [Automatisk skalning av Azure-webbplatser, CPU eller schemalagda - med Stefan Schackow](https://azure.microsoft.com/resources/videos/auto-scaling-azure-web-sites/)
  * [Hur Azure Websites skala – med Stefan Schackow](https://azure.microsoft.com/resources/videos/how-azure-web-sites-scale/)

<!-- LINKS -->
[vmsizes]:https://azure.microsoft.com/pricing/details/app-service/
[SQLaccountsbilling]:https://go.microsoft.com/fwlink/?LinkId=234930
[azuresubscriptions]:https://account.windowsazure.com/subscriptions
[portal]: https://portal.azure.com/

<!-- IMAGES -->
[ChooseWHP]: ./media/web-sites-scale/scale1ChooseWHP.png
[ResourceGroup]: ./media/web-sites-scale/scale10ResourceGroup.png
[ScaleDatabase]: ./media/web-sites-scale/scale11SQLScale.png
[GeoReplication]: ./media/web-sites-scale/scale12SQLGeoReplication.png
