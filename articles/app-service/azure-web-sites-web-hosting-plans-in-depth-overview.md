---
title: "Apptjänstplaner i Azure App Service Web Apps | Microsoft Docs"
description: "Lär dig hur App Service-planer för Azure App Service och hur de får din hanteringsupplevelse."
keywords: app service, azure app service, scale, scalable, app service plan, app service cost
services: app-service
documentationcenter: 
author: btardif
manager: erikre
editor: 
ms.assetid: dea3f41e-cf35-481b-a6bc-33d7fc9d01b1
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: byvinyal
ms.openlocfilehash: fb5b782f09bdd8c8a862eddfbd65b0f86ef8d08c
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2017
---
# <a name="app-service-plans-in-azure-app-service-web-apps"></a>Apptjänstplaner i Azure App Service Web Apps

Programtjänstplaner representerar mängd fysiska resurser som används som värd för dina appar.

App Service-planer definierar följande:

- Region (västra USA, östra USA osv.)
- Skalningsantalet (en, två, tre instanser, etc.)
- Storlek (Small, Medium, Large)
- SKU (lediga delad, Basic, Standard, Premium, PremiumV2, isolerat)

Web Apps, Mobilappar, API Apps, funktionen appar (eller funktioner) i [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) alla kör i en apptjänstplan.  Appar i samma prenumeration och region kan dela en App Service-plan. 

Alla program som har tilldelats en **programtjänstplanen** delar resurser som definieras av den. Den här delning sparar pengar när värd för flera appar i en enda App Service-plan.

Din **programtjänstplanen** kan skala från **lediga** och **delade** nivåer att **grundläggande**, **Standard**,  **Premium**, och **isolerad** nivåer. Varje högre nivå ger dig tillgång till ytterligare resurser och funktioner.

Om din App Service-plan anges till **grundläggande** tjänstnivån eller högre, sedan du kan styra den **storlek** och skala antalet på de virtuella datorerna.

Om exempelvis planen är konfigurerad för att använda två ”liten” instanser i den **Standard** nivån alla appar i planen som körs på båda instanser. Appar har också åtkomst till den **Standard** tjänstnivån funktioner. Instanser av programtjänstplanen som kör appar är helt hanterad och hög tillgänglighet.

> [!IMPORTANT]
> Prisnivå (SKU) för App Service-plan anger kostnad och inte antalet appar i den.

Den här artikeln innehåller kriterier i en apptjänstplan, till exempel priser nivåer och skala och hur de fungerar när du hanterar dina appar.

## <a name="new-pricing-tier-premiumv2"></a>Nya prisnivån: PremiumV2

Den nya **PremiumV2** prisnivån ger [Dv2-serien VMs](../virtual-machines/windows/sizes-general.md#dv2-series) med snabba processorer, SSD-lagringen och dubbla kärnor till minne jämfört med **Standard** nivå. **PremiumV2** också stöd för ökad skala via ökad instanser samtidigt som de avancerade funktioner som finns i Standard-plan. Alla funktioner som är tillgängliga i den befintliga **Premium** nivå ingår i **PremiumV2**.

Precis som andra dedikerade nivåer, tre storlekar på VM är tillgängliga för det här skiktet:

- Liten (1 processorkärna, 3.5 GiB minne) 
- Medel (2 CPU-kärnor, 7 GiB minne) 
- Stor (4 CPU-kärnor, 14 GiB minne)  

För **PremiumV2** information om priser finns [priser för Apptjänst](/pricing/details/app-service/).

Du kommer igång med den nya **PremiumV2** prisnivån, se [konfigurera PremiumV2 nivån för Apptjänst](app-service-configure-premium-tier.md).

## <a name="apps-and-app-service-plans"></a>Appar och App Service-planer

En app i App Service kan associeras med endast en App Service-plan vid en given tidpunkt.

Både appar och planer finns i en **resursgruppen**. En resursgrupp fungerar som livscykel gräns för varje resurs som ligger inom det. Du kan använda resursgrupper för att hantera alla delar av ett program tillsammans.

Eftersom en enskild resursgrupp kan ha flera programtjänstplaner, kan du allokera olika appar till olika fysiska resurser.

Du kan till exempel separata resurser mellan dev, test- och miljöer. Med separata miljöer för produktion och utveckling och testning kan du isolera resurser. På så sätt kan konkurrerar läsa in testar mot en ny version av dina appar inte för samma resurser som dina appar för produktion som hanterar verkliga kunder.

När du har flera planer i en enskild resursgrupp kan du också definiera ett program som omfattar geografiska regioner.

En hög tillgänglighet app som körs i två områden innehåller till exempel minst två planer, en för varje region och en app som är associerade med varje plan. I en sådan situation finns sedan alla kopior av appen i en enskild resursgrupp. Med en resursgrupp med flera planer och flera appar gör det enkelt att hantera, styra och visa hälsotillståndet för programmet.

## <a name="create-an-app-service-plan-or-use-existing-one"></a>Skapa en apptjänstplan eller använda befintlig

När du skapar en ny Webbapp i App Service kan dela du värdresurser genom att placera appen i en befintlig programtjänstplan. För att avgöra om den nya appen har nödvändiga resurser, måste du förstå kapaciteten hos befintlig programtjänstplan och den förväntade belastningen för den nya appen. Över fördela resurser kan orsaka driftstopp för dina nya och befintliga appar.

Vi rekommenderar att isolera din app till en ny Apptjänst planerar:

- Appen är resurskrävande.
- Programmet har olika skalning faktorer från andra appar som finns i ett befintligt schema.
- Appen måste resurs i en annan geografisk region.

Det här sättet du tilldela en ny uppsättning resurser för din app och få större kontroll över dina appar.

## <a name="create-an-app-service-plan"></a>Skapa en App Service-plan

> [!TIP]
> Om du har en Apptjänst-miljö, se [skapa en apptjänstplan i en Apptjänst-miljö](../app-service/environment/app-service-web-how-to-create-a-web-app-in-an-ase.md#createplan).

Du kan skapa en tom App Service-plan eller som en del av skapa en app.

I den [Azure-portalen](https://portal.azure.com), klickar du på **ny** > **webb + mobilt**, och välj sedan **Web App** eller en annan typ för Apptjänst-app.

![Skapa en app i Azure-portalen.][createWebApp]

Du kan sedan välja eller skapa en apptjänstplan för den nya appen.

 ![Skapa en apptjänstplan.][createASP]

Klicka för att skapa en apptjänstplan **[+] Skapa nya**, typen av **programtjänstplanen** namn och välj sedan ett lämpligt **plats**. Klicka på **prisnivå**, och välj sedan ett lämpligt prisnivån för tjänsten. Välj **visa alla** att visa priser fler alternativ, exempelvis **lediga** och **delade**. När du har valt prisnivå, klickar du på den **Välj** knappen.

## <a name="move-an-app-to-a-different-app-service-plan"></a>Flytta en app till en annan programtjänstplan

Du kan flytta en app till en annan programtjänstplan i den [Azure-portalen](https://portal.azure.com). Du kan flytta appar mellan planer så länge planerna finns i den _samma resursgrupp och geografiska region_.

Flytta en app till en annan plan:

- Navigera till den app som du vill flytta.
- I den **menyn**, leta efter den **Apptjänstplan** avsnitt.
- Välj **ändra programtjänstplanen** att starta processen.

**Ändra programtjänstplanen** öppnar den **programtjänstplanen** väljare. Nu kan du välja en befintlig plan för att flytta den här appen till. Endast planer i samma resursgrupp och region visas.

![App Service-plan väljare.][change]

Varje plan har sin egen prisnivån. Till exempel flytta en plats från en kostnadsfria nivån till ett skikt som Standard kan alla appar som har tilldelats till den använda de funktioner och resurser i standardnivån.

## <a name="clone-an-app-to-a-different-app-service-plan"></a>Klona en app på en annan programtjänstplan

Om du vill flytta appen till en annan region är ett alternativ app kloning. Kloningen gör en kopia av din app i en ny eller befintlig programtjänstplan i en region.

Du kan hitta **klona App** i den **utvecklingsverktyg** på menyn.

> [!IMPORTANT]
> Kloningen har vissa begränsningar som du kan läsa om vid [Azure Apptjänst-App kloning](app-service-web-app-cloning.md).

## <a name="scale-an-app-service-plan"></a>Skala en programtjänstplan

Det finns tre sätt att skala en plan:

- **Ändra planen prisnivån**. En plan i den grundläggande nivån kan konverteras till Standard- och alla appar som har tilldelats för att använda funktioner i standardnivån.
- **Ändra instansstorleken för planens**. Exempelvis kan en plan i den grundläggande nivån som använder små instanser ändras om du vill använda stora instanser. Alla appar som är kopplade med planen kan använda ytterligare minne och processorresurser som större instansstorleken erbjuder.
- **Ändra planens instansantalet**. Till exempel kan en standardplan skalas ut till tre instanser skalas till 10 instanser. En premiumplan kan skaländras ut till 20 instanser (beroende på tillgänglighet). Alla appar som är kopplade med planen kan använda ytterligare minne och processorresurser som erbjuder större instansantalet.

Du kan ändra prisnivå nivå och instans storlek genom att klicka på den **skala upp** objekt under inställningar för appen eller App Service-plan. Ändringarna tillämpas på App Service-plan och påverkar alla appar som den är värd för.

 ![Ange värden för att skala upp en app.][pricingtier]

## <a name="app-service-plan-cleanup"></a>Rensning av App Service-plan

> [!IMPORTANT]
> **Apptjänstplaner** som har inga appar som är kopplade till dem fortfarande avgifter eftersom de fortsätter att reservera beräkningskapacitet.

Om du vill undvika oväntade kostnader, senaste appen finns i en apptjänstplan tas bort, raderas även resulterande tom App Service-plan som standard.

## <a name="summary"></a>Sammanfattning

Programtjänstplaner representerar en uppsättning funktioner och kapaciteter som du kan dela över dina appar. Apptjänstplaner ger dig flexibiliteten att allokera specifika appar till en uppsättning resurser och ytterligare optimera din Azure resursutnyttjande. På så sätt kan om du vill spara pengar i din testmiljö kan du dela en plan över flera appar. Du kan också maximera dataflödet för produktionsmiljön genom att skala över flera regioner och planer.

## <a name="whats-changed"></a>Nyheter

- En guide till övergången från webbplatser till App Service finns: [Azure App Service och dess påverkan på befintliga Azure-tjänster](http://go.microsoft.com/fwlink/?LinkId=529714)

[pricingtier]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/appserviceplan-pricingtier.png
[assign]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/assing-appserviceplan.png
[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png
