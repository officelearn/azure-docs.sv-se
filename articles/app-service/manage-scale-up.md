---
title: Skala upp funktioner och kapacitet
description: Läs om hur du skalar upp en app i Azure App Service. Få mer CPU, minne, diskutrymme och extra funktioner.
ms.assetid: f7091b25-b2b6-48da-8d4a-dcf9b7baccab
ms.topic: article
ms.date: 08/19/2019
ms.custom: seodec18
ms.openlocfilehash: dfd9297e80836978b8a185df3fc4659676383802
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75659907"
---
# <a name="scale-up-an-app-in-azure-app-service"></a>Skala upp en app i Azure App Service

I den här artikeln visas hur du skalar din app i Azure App Service. Det finns två arbetsflöden för skalning, skala upp och skala ut, och den här artikeln förklarar skala upp arbetsflödet.

* [Skala upp:](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling)Få mer CPU, minne, diskutrymme och extra funktioner som dedikerade virtuella datorer , anpassade domäner och certifikat, mellanlagringsplatser, automatisk skalning och mycket mer. Du skalar upp genom att ändra prisnivån för apptjänstplanen som din app tillhör.
* [Skala ut:](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling)Öka antalet VM-instanser som kör din app.
  Du kan skala ut till så många som 30 instanser, beroende på din prisnivå. [App Service-miljöer](environment/intro.md) på **isolerad** nivå ökar antalet utskalningsförhöjningar ytterligare till 100 instanser. Mer information om skalning finns i [Skala instansantal manuellt eller automatiskt](../monitoring-and-diagnostics/insights-how-to-scale.md). Där tar du reda på hur du använder automatisk skalning, vilket är att skala instansantalet automatiskt baserat på fördefinierade regler och scheman.

Skalningsinställningarna tar bara några sekunder att tillämpa och påverka alla appar i [App Service-planen](../app-service/overview-hosting-plans.md).
De kräver inte att du ändrar din kod eller distribuerar om programmet.

Information om priser och funktioner i enskilda App Service-abonnemang finns i [Prisinformation för App Service](https://azure.microsoft.com/pricing/details/web-sites/).  

> [!NOTE]
> Innan du växlar en App Service-plan från den **kostnadsfria** nivån måste du först ta bort [utgiftsgränserna](https://azure.microsoft.com/pricing/spending-limits/) för din Azure-prenumeration. Information om hur du visar eller ändrar alternativ för din Microsoft Azure App Service-prenumeration finns i [Microsoft Azure-prenumerationer][azuresubscriptions].
> 
> 

<a name="scalingsharedorbasic"></a>
<a name="scalingstandard"></a>

## <a name="scale-up-your-pricing-tier"></a>Skala upp din prisnivå

> [!NOTE]
> Om du vill skala upp till **PremiumV2-nivå** finns i [Konfigurera PremiumV2-nivå för App Service](app-service-configure-premium-tier.md).
>

1. Öppna [Azure-portalen][portal] i webbläsaren.

1. På appsidan för App Service på den vänstra menyn väljer du **Skala upp (App Service-plan).**
   
3. Välj din nivå och välj sedan **Använd**. Välj de olika kategorierna (till exempel **Produktion)** och **även Se ytterligare alternativ** för att visa fler nivåer.
   
    ![Navigera för att skala upp din Azure-app.][ChooseWHP]

    När åtgärden är klar visas ett popup-fönster för meddelanden med en grön kontrollmarkering.

<a name="ScalingSQLServer"></a>

## <a name="scale-related-resources"></a>Skala relaterade resurser
Om din app är beroende av andra tjänster, till exempel Azure SQL Database eller Azure Storage, kan du skala upp dessa resurser separat. Dessa resurser hanteras inte av App Service-planen.

1. Välj länken **Resursgrupp** på sidan **Översikt** för appen.
   
    ![Skala upp azure-appens relaterade resurser](./media/web-sites-scale/RGEssentialsLink.png)

2. Välj en resurs som du vill skala i **sammanfattningsdelen** på sidan **Resursgrupp.** Följande skärmbild visar en SQL Database-resurs.
   
    ![Navigera till resursgruppssidan för att skala upp din Azure-app](./media/web-sites-scale/ResourceGroup.png)

    Om du vill skala upp den relaterade resursen läser du dokumentationen för den specifika resurstypen. Om du till exempel vill skala upp en enda SQL-databas finns i [Skala enskilda databasresurser i Azure SQL Database](../sql-database/sql-database-single-database-scale.md). Information om hur du skalar upp en Azure-databas för MySQL-resurs finns i [Skala MySQL-resurser](../mysql/concepts-pricing-tiers.md#scale-resources).

<a name="OtherFeatures"></a>
<a name="devfeatures"></a>

## <a name="compare-pricing-tiers"></a>Jämför prisnivåer

Detaljerad information, till exempel VM-storlekar för varje prisnivå, finns i [Prisinformation för App Service](https://azure.microsoft.com/pricing/details/app-service).

En tabell över tjänstbegränsningar, kvoter och begränsningar och funktioner som stöds på varje nivå finns i [Begränsningar för App-tjänsten](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits).

<a name="Next Steps"></a>

## <a name="more-resources"></a>Fler resurser

[Skala instansantalet manuellt eller automatiskt](../monitoring-and-diagnostics/insights-how-to-scale.md)  
[Konfigurera PremiumV2-nivå för App Service](app-service-configure-premium-tier.md)

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
