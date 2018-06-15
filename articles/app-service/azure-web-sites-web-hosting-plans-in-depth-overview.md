---
title: Översikt av Azure App Service-plan | Microsoft Docs
description: Lär dig hur App Service-planer för Azure App Service och hur de får din hanteringsupplevelse.
keywords: App service, azure app service, skala, skalbar, skalbarhet, apptjänstplan, kostnad för app service
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: dea3f41e-cf35-481b-a6bc-33d7fc9d01b1
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: cephalin
ms.openlocfilehash: 268844eae8dc06937529e79d52515cad2f6da3f4
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2018
ms.locfileid: "27862367"
---
# <a name="azure-app-service-plan-overview"></a>Översikt av Azure App Service-plan

I App Service är en app som körs i en _programtjänstplanen_. En apptjänstplan definierar en uppsättning beräkningsresurser för ett webbprogram ska köras. Dessa beräkna resurser kan jämföras med den [ _servergruppen_ ](https://wikipedia.org/wiki/Server_farm) i konventionella webbhotell. En eller flera appar kan konfigureras för att köras på samma dataresurser (eller i samma programtjänstplan).

När du skapar en apptjänstplan i en viss region (till exempel västra Europa) skapas en uppsättning beräkningsresurser för planen i den regionen. De appar du lägga till i den här programtjänstplanen som körs på de beräkningsresurser som definierats av din programtjänstplan. Varje App Service-plan definierar:

- Region (västra USA, östra USA osv.)
- Antal VM-instanser
- Storleken på VM-instanser (Small, Medium, Large)
- Prisnivå (ledigt, delade, Basic, Standard, Premium, PremiumV2, isolerad, förbrukning)

Den _prisnivån_ av en App Service plan avgör vilken App Service-funktioner som du får och hur mycket du betala för planen. Det finns några kategorier av prisnivåer:

- **Delade beräkning**: **lediga** och **delade**, två grundläggande nivåer, kör en app på samma Azure-VM som andra Apptjänst-appar, inklusive appar från andra kunder. Dessa nivåer tilldela CPU kvoter till varje app som körs i delade resurser och resurser kan inte skalas ut.
- **Dedikerad beräkning**: den **grundläggande**, **Standard**, **Premium**, och **PremiumV2** nivåer köra appar på dedikerade Azure Virtuella datorer. Endast appar i samma programtjänstplanen delar samma beräkningsresurser. Ju högre nivån mer VM-instanser är tillgängliga för skalbar.
- **Isolerade**: det här skiktet körs dedikerade virtuella Azure-datorer på dedikerade virtuella Azure-nätverk, vilket ger nätverksisolering ovanpå beräkning isolering till dina appar. Det ger högsta skalbar funktionerna.
- **Förbrukning**: det här skiktet är bara tillgänglig för [fungerar appar](../azure-functions/functions-overview.md). Skalningen av funktioner dynamiskt beroende på arbetsbelastningen. Mer information finns i [Azure Functions värd planer jämförelse](../azure-functions/functions-scale.md).

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Varje nivå innehåller också en delmängd av App Service-funktioner. Dessa funktioner inkluderar anpassade domäner och SSL-certifikat, autoskalning, distributionsplatser, säkerhetskopiering, Traffic Manager-integrering med mera. Ju högre nivån fler funktioner är tillgängliga. Om du vill veta vilka funktioner som stöds i varje prisnivå [information för App Service-plan](https://azure.microsoft.com/pricing/details/app-service/plans/).

<a name="new-pricing-tier-premiumv2"></a>

> [!NOTE]
> Den nya **PremiumV2** prisnivån ger [Dv2-serien VMs](../virtual-machines/windows/sizes-general.md#dv2-series) med snabba processorer, SSD-lagringen och dubbla kärnor till minne jämfört med **Standard** nivå. **PremiumV2** också stöd för ökad skala via ökad instanser samtidigt som de avancerade funktioner som finns i Standard-plan. Alla funktioner som är tillgängliga i den befintliga **Premium** nivå ingår i **PremiumV2**.
>
> Precis som andra dedikerade nivåer, tre storlekar på VM är tillgängliga för det här skiktet:
>
> - Liten (en processorkärna, 3.5 GiB minne) 
> - Medel (två CPU-kärnor, 7 GiB minne) 
> - Stor (fyra CPU-kärnor, 14 GiB minne)  
>
> För **PremiumV2** information om priser finns [priser för Apptjänst](https://azure.microsoft.com/pricing/details/app-service/).
>
> Du kommer igång med den nya **PremiumV2** prisnivån, se [konfigurera PremiumV2 nivån för Apptjänst](app-service-configure-premium-tier.md).

## <a name="how-does-my-app-run-and-scale"></a>Hur min app körs och skala?

I den **lediga** och **delade** nivåer, en app som tar emot CPU: e minut på en delad VM-instans och det går inte att skala ut. En app körs på andra nivåer och skalas enligt följande.

När du skapar en app i App Service placeras den i en apptjänstplan. När appen körs körs den på alla VM-instanser som konfigurerats i App Service-plan. Om flera appar finns i samma App Service-plan måste delar alla samma VM-instanser. Om du har flera distributionsplatser för en app kan köra alla distributionsplatser också på samma VM-instanser. Om du aktiverar diagnostikloggar, säkerhetskopiera eller kör WebJobs använda de också CPU-cykler och minne på dessa VM-instanser.

På så sätt är programtjänstplanen skalningsenhet för Apptjänst-appar. Om planen har konfigurerats för att köra fem VM-instanser, köra alla appar i planen på alla fem instanser. Om planen har konfigurerats för autoskalning, och sedan alla appar i planen skalas ut tillsammans baserat på inställningarna för Autoskala.

Information om att skala ut en app finns i [skala instansantalet manuellt eller automatiskt](../monitoring-and-diagnostics/insights-how-to-scale.md).

<a name="cost"></a>

## <a name="how-much-does-my-app-service-plan-cost"></a>Hur mycket kostar App Service-plan?

Det här avsnittet beskrivs hur Apptjänst-appar debiteras. Mer detaljerad, regionspecifika prisinformation finns [priser för Apptjänst](https://azure.microsoft.com/pricing/details/app-service/).

Undantag för **lediga** nivå, en apptjänstplan bedriver timkostnaden beräkningsresurser som används.

- I den **delade** nivån varje app som tar emot en kvot på CPU minuter så _varje app_ debiteras timvis för CPU-kvoten.
- I den dedicerade compute-nivåer (**grundläggande**, **Standard**, **Premium**, **PremiumV2**), i App Service-plan definierar antal VM instanser apparna skalas till, så _varje VM-instans_ i App Service-plan har timkostnaden. Dessa VM-instanser debiteras samma oavsett hur många appar körs på dem. Om du vill undvika oväntade debiteringar finns [rensa en apptjänstplan](app-service-plan-manage.md#delete).
- I den **isolerad** nivån Apptjänst-miljön definierar antalet isolerade arbetare som kör dina appar och _varje worker_ debiteras per timme. Det är dessutom ett grundläggande timkostnaden för körning Apptjänstmiljö sig själv. 
- (Endast azure-funktioner) Den **förbrukning** nivå dynamiskt allokerar VM-instanser för att underhålla en funktionsapp arbetsbelastning och dynamiskt debiteras per sekund av Azure. Mer information finns i [priser för Azure Functions](https://azure.microsoft.com/pricing/details/functions/).

Du debiteras inte hämta för att använda Apptjänst-funktioner som är tillgängliga för dig (konfigurera anpassade domäner, SSL-certifikat, distributionsplatser, säkerhetskopior osv.). Undantagen är:

- App Service domäner - betalar du när du köper en i Azure och när du förnyar det varje år.
- Apptjänstcertifikat - betalar du när du köper en i Azure och när du förnyar det varje år.
- IP-baserade SSL-anslutningar - där har timkostnaden för varje IP-baserade SSL-anslutning, men vissa **Standard** tjänstnivån eller senare får du en IP-baserade SSL-anslutning kostnadsfritt. SNI-baserade SSL-anslutningar är kostnadsfri.

> [!NOTE]
> Om du integrerar Apptjänst med en annan Azure-tjänst som du kan behöva överväga avgifter från dessa andra tjänster. Om du använder Azure Traffic Manager för att skala ditt program geografiskt, Azure Traffic Manager även avgifter baserat på förbrukningen. Om du vill beräkna dina kostnader för globala tjänster i Azure finns [priser Kalkylatorn](https://azure.microsoft.com/pricing/calculator/). 
>
>

## <a name="what-if-my-app-needs-more-capabilities-or-features"></a>Vad händer om min app måste flera funktioner eller funktioner?

App Service-plan kan skalas upp och ned när som helst. Det är så enkelt som att ändra prisnivå för planen. Du kan välja en lägre prisnivå först och skala upp senare när du behöver fler funktioner i Apptjänst.

Du kan till exempel starta testa ditt webbprogram i en **lediga** Apptjänst planera och betalar ingenting. När du vill lägga till din [anpassade DNS-namnet](app-service-web-tutorial-custom-domain.md) till webb-app bara skala din plan upp till **delade** nivå. Senare, när du vill lägga till en [anpassade SSL-certifikat](app-service-web-tutorial-custom-ssl.md), skala upp till din plan **grundläggande** nivå. När du vill ha [mellanlagringsmiljöer](web-sites-staged-publishing.md), skala upp till **Standard** nivå. När du behöver flera kärnor, minne eller lagring, kan du skala upp till en större VM-storlek i samma nivå.

Samma fungerar i tvärtom. När du anser att du inte längre behöver funktioner eller funktioner i en högre nivå, du kan skala till en lägre nivå, som sparar pengar.

Information om att skala upp programtjänstplanen finns [skala upp en app i Azure](web-sites-scale.md).

Om din app finns i samma App Service-plan med andra appar, kanske du vill förbättra appens prestanda genom att isolera beräkningsresurserna. Du kan göra det genom att flytta appen till en separat App Service-plan. Mer information finns i [flytta en app till en annan programtjänstplan](app-service-plan-manage.md#move).

## <a name="should-i-put-an-app-in-a-new-plan-or-an-existing-plan"></a>Ska jag lägga till en app i en ny plan eller en befintlig plan?

Eftersom du betala för dataresurser din programtjänstplan allokerar (se [hur mycket kostar min App Service-plan?](#cost)), potentiellt kan du spara pengar genom att lägga till flera appar till en App Service-plan. Du kan fortsätta att lägga till appar i en befintlig plan så länge planen har tillräckligt med resurser för att hantera belastningen. Tänk dock på att appar i samma App Service-plan som alla har samma beräkningsresurser. Om du vill avgöra om den nya appen har nödvändiga resurser, måste du förstå kapaciteten hos befintlig programtjänstplan och den förväntade belastningen för den nya appen. Överbelastning App Service-plan kan orsaka driftstopp för dina nya och befintliga appar.

Isolera din app till en ny App Service-plan när:

- Appen är resurskrävande.
- Du vill skala appen oberoende av de andra apparna den befintliga planen.
- Appen måste resurs i en annan geografisk region.

Det här sättet du tilldela en ny uppsättning resurser för din app och få större kontroll över dina appar.

## <a name="manage-an-app-service-plan"></a>Hantera en apptjänstplan

> [!div class="nextstepaction"]
> [Hantera en apptjänstplan](app-service-plan-manage.md)
