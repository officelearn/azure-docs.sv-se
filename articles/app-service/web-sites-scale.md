---
title: Skala upp en app i Azure | Microsoft Docs
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
ms.openlocfilehash: f8478a0aef52f8e573fa300a0b1328ea9ce5129f
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37031006"
---
# <a name="scale-up-an-app-in-azure"></a>Skala upp en app i Azure

> [!NOTE]
> Den nya **PremiumV2** nivå ger snabbare processorer, SSD-lagringen och fördubblar kärnor till minne förhållandet mellan befintliga prisnivåer. Du kan spara pengar genom att köra dina appar på färre instanser med fördelen prestanda. Att skala upp till **PremiumV2** tjänstnivån, se [konfigurera PremiumV2 nivån för Apptjänst](app-service-configure-premium-tier.md).
>

Den här artikeln visar hur du skala ditt program i Azure App Service. Det finns två arbetsflöden för skalning, skala upp och skala ut och den här artikeln förklarar skala in arbetsflöde.

* [Skala upp](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): hämta mer CPU, minne, diskutrymme och extra funktioner såsom dedikerade virtuella datorer (VM), anpassade domäner och certifikat, mellanlagringsplatser kortplatser, autoskalning med mera. Du skalar upp genom att ändra prisnivå för App Service-plan som tillhör din app.
* [Skala ut](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): öka antalet VM-instanser som appen körs.
  Du kan skala ut till upp till 20 instanser, beroende på din prisnivå. [Apptjänstmiljöer](environment/intro.md) i **isolerad** ytterligare nivå ökar din skalbara antalet till 100 instanser. Mer information om att skala ut finns [skala instansantalet manuellt eller automatiskt](../monitoring-and-diagnostics/insights-how-to-scale.md). Det, ta reda på hur du använder autoskalning, vilket är att skala instansantalet automatiskt baserat på fördefinierade regler och scheman.

Skalinställningarna Tar bara några sekunder tillämpas och påverkar alla program i din [programtjänstplanen](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).
De behöver du ändra koden och omdistribuera ditt program.

Information om priser och funktioner i enskilda App Service-planer finns [App Service-prisinformation](https://azure.microsoft.com/pricing/details/web-sites/).  

> [!NOTE]
> Innan du växlar en apptjänstplan från den **lediga** nivån, måste du först ta bort den [utgiftsgränser](https://azure.microsoft.com/pricing/spending-limits/) för din Azure-prenumeration. Om du vill visa eller ändra alternativen för din prenumeration på Microsoft Azure App Service finns [Microsoft Azure-prenumerationer][azuresubscriptions].
> 
> 

<a name="scalingsharedorbasic"></a>
<a name="scalingstandard"></a>

## <a name="scale-up-your-pricing-tier"></a>Skala upp din prisnivå
1. I webbläsaren och öppna den [Azure-portalen][portal].
2. På din Apptjänst-app klickar du på **alla inställningar**, och klicka sedan på **skala upp**.
   
    ![Navigera för att skala upp din Azure-app.][ChooseWHP]
3. Välj din nivå och klicka sedan på **tillämpa**.
   
    Den **meddelanden** fliken blinkar en grön **lyckade** när åtgärden har slutförts.

<a name="ScalingSQLServer"></a>

## <a name="scale-related-resources"></a>Skala relaterade resurser
Om din app är beroende av andra tjänster, till exempel Azure SQL Database eller Azure Storage kan du skala upp resurserna separat. Dessa resurser hanteras inte av App Service-plan.

1. I **Essentials**, klicka på den **resursgruppen** länk.
   
    ![Skala upp din Azure-app relaterade resurser](./media/web-sites-scale/RGEssentialsLink.png)
2. I den **sammanfattning** ingår i den **resursgruppen** klickar du på en resurs som du vill skala. Följande skärmbild visar en SQL-databas resurs och ett Azure Storage-resurs.
   
    ![Gå till gruppen resurssidan att skala upp din Azure-app](./media/web-sites-scale/ResourceGroup.png)
3. För en resurs för SQL-databasen, klickar du på **inställningar** > **prisnivå** att skala prisnivån.
   
    ![Skala upp SQL Database-serverdelen för din Azure-app](./media/web-sites-scale/ScaleDatabase.png)
   
    Du kan också aktivera [georeplikering](../sql-database/sql-database-geo-replication-overview.md) för din instans av SQL-databas.
   
    För ett Azure Storage-resurs, klickar du på **inställningar** > **Configuration** att skala upp din lagringsalternativ.
   
    ![Skala upp Azure Storage-konto som används av din Azure-app](./media/web-sites-scale/ScaleStorage.png)

<a name="OtherFeatures"></a>
<a name="devfeatures"></a>

## <a name="compare-pricing-tiers"></a>Jämför prisnivåer
Detaljerad information, till exempel VM-storlekar för varje prisnivå finns [App Service-prisinformation](https://azure.microsoft.com/pricing/details/web-sites/).
En tabell med tjänsten gränser, kvoter och begränsningar och funktioner som stöds i varje nivå finns [gränser för Apptjänst](../azure-subscription-service-limits.md#app-service-limits).

<a name="Next Steps"></a>

## <a name="next-steps"></a>Nästa steg
* Information om priser, support och SLA finns i följande länkar:
  
    [Prisinformation för dataöverföringar](https://azure.microsoft.com/pricing/details/data-transfers/)
  
    [Microsoft Azure-supportplaner](https://azure.microsoft.com/support/plans/)
  
    [serviceavtal](https://azure.microsoft.com/support/legal/sla/)
  
    [Prisinformation för SQL-databas](https://azure.microsoft.com/pricing/details/sql-database/)
  
    [Virtuell dator och Molntjänststorlekar för Microsoft Azure][vmsizes]
  
* Information om Azure App Service finns bästa praxis, inklusive att skapa en skalbar och flexibel arkitektur [metodtips: Azure App Service Web Apps](https://azure.microsoft.com/blog/best-practices-windows-azure-websites-waws/).
* Videor om att skala Apptjänst-appar finns i följande resurser:
  
  * [När ska du skala Azure Websites – med Stefan Schackow](https://azure.microsoft.com/resources/videos/azure-web-sites-free-vs-standard-scaling/)
  * [Automatisk skalning Azure Websites, CPU eller schemalagt - med Stefan Schackow](https://azure.microsoft.com/resources/videos/auto-scaling-azure-web-sites/)
  * [Hur Azure webbplatser skala - med Stefan Schackow](https://azure.microsoft.com/resources/videos/how-azure-web-sites-scale/)

<!-- LINKS -->
[vmsizes]:/pricing/details/app-service/
[SQLaccountsbilling]:http://go.microsoft.com/fwlink/?LinkId=234930
[azuresubscriptions]:http://go.microsoft.com/fwlink/?LinkID=235288
[portal]: https://portal.azure.com/

<!-- IMAGES -->
[ChooseWHP]: ./media/web-sites-scale/scale1ChooseWHP.png
[ResourceGroup]: ./media/web-sites-scale/scale10ResourceGroup.png
[ScaleDatabase]: ./media/web-sites-scale/scale11SQLScale.png
[GeoReplication]: ./media/web-sites-scale/scale12SQLGeoReplication.png
