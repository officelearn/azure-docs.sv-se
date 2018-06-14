---
title: Introduktion till App Service miljö v1
description: Mer information om funktionen Apptjänstmiljö v1 som tillhandahåller säker, ansluten till VNet, dedikerad skalningsenheter för att köra alla dina appar.
services: app-service
documentationcenter: ''
author: stefsch
manager: erikre
editor: ''
ms.assetid: 78e6d4f5-da46-4eb5-a632-b5fdc17d2394
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.openlocfilehash: b13f726ada6deba67d5ed484eca769fed354e400
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23836786"
---
# <a name="introduction-to-app-service-environment-v1"></a>Introduktion till App Service miljö v1

> [!NOTE]
> Den här artikeln handlar om Apptjänstmiljö v1.  Det finns en nyare version av Apptjänst-miljön som är enklare att använda och körs på mer kraftfulla infrastruktur. Mer information om den nya versionen start med den [introduktion till Apptjänst-miljön](intro.md).
> 

## <a name="overview"></a>Översikt
En Apptjänst-miljö är en [Premium] [ PremiumTier] service plan möjlighet att [Azure App Service](../app-service-web-overview.md) som ger en helt isolerad och dedikerad miljö för säker körning Azure Apptjänst-appar i hög skala, inklusive Webbappar, Mobilappar och API Apps.  

Apptjänstmiljöer är perfekt för programarbetsbelastningar som kräver:

* Mycket hög skala
* Isolering och säker nätverksåtkomst

Kunder kan skapa flera Apptjänstmiljöer inom en enda Azure region, samt över flera Azure-regioner.  Detta gör Apptjänstmiljöer idealiskt för vågrätt skalning statuslösa programnivåerna stöd för hög RPS arbetsbelastningar.

Apptjänstmiljöer är isolerad för att endast en enskild kund program som körs och alltid har distribuerats till ett virtuellt nätverk.  Kunder har detaljerad kontroll över både inkommande och utgående nätverkstrafik och program kan upprätta säkra höghastighetsanslutning över virtuella nätverk till lokala företagsresurser.

En översikt över hur Apptjänstmiljöer Aktivera hög skala och säker nätverksåtkomst, finns det [AzureCon ingående] [ AzureConDeepDive] på Apptjänstmiljöer!

En djupdykning i vågrätt skalning med flera Apptjänstmiljöer finns i artikeln på hur du konfigurerar en [geodistribuerad app storleken][GeodistributedAppFootprint].

Om du vill se hur säkerhetsarkitekturen som visas i AzureCon ingående har konfigurerats, finns i artikeln om hur du implementerar en [lager säkerhetsarkitekturen](app-service-app-service-environment-layered-security.md) med Apptjänstmiljöer.

Appar som körs på Apptjänstmiljöer kan behörighet gated av överordnad enheter, till exempel web application brandväggar (Brandvägg).  Artikel om [konfigurera en Brandvägg för Apptjänstmiljöer](app-service-app-service-environment-web-application-firewall.md) omfattar det här scenariot. 

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="dedicated-compute-resources"></a>Dedikerad beräkningsresurser
Alla beräkningsresurser i en Apptjänst-miljö är avsedda enbart för en enda prenumeration och en Apptjänst-miljö kan konfigureras med upp till 50 (50)-beräkningsresurser för exklusiv användning av ett program.

En Apptjänst-miljö består av en resurspool för frontend beräkning, samt en till tre worker beräkning resurspooler. 

Frontend poolen innehåller beräkningsresurser som är ansvarig för SSL-avslutning som också automatisk belastningsbalansering av app-förfrågningar inom en Apptjänst-miljö. 

Varje arbetspool innehåller beräkningsresurser som allokeras till [Apptjänstplaner][AppServicePlan], som i sin tur innehåller en eller flera appar i Azure App Service.  Eftersom det kan vara upp till tre olika arbetarpooler i en Apptjänst-miljö, har du möjlighet att välja olika beräkningsresurser för varje worker-pool.  

T.ex, kan detta du skapa en arbetspool med mindre kraftfulla beräkningsresurser för Apptjänstplaner avsett för utveckling eller testning appar.  En andra (eller även tredje) arbetspool kan använda mer kraftfulla beräkningsresurser som är avsedd för Apptjänstplaner produktion program som körs.

Mer information om mängden beräkningsresurser som är tillgängliga på frontend- och arbetsroller lagringspooler finns [så här konfigurerar du en Apptjänst-miljö][HowToConfigureanAppServiceEnvironment].  

Mer information om tillgängliga beräknings resurs storlekar som stöds i en Apptjänst-miljö finns i [priser för Apptjänst] [ AppServicePricing] sidan och granska de tillgängliga alternativen för Apptjänstmiljöer i den Premium-prisnivån.

## <a name="virtual-network-support"></a>Stöd för virtuella nätverk
En Apptjänst-miljö kan skapas i **antingen** ett virtuellt nätverk i Azure Resource Manager **eller** klassisk distribution modellen virtuella nätverk ([mer information om virtuella nätverk] [MoreInfoOnVirtualNetworks]).  Eftersom en Apptjänst-miljö finns alltid i ett virtuellt nätverk och mer exakt inom ett undernät i ett virtuellt nätverk, kan du utnyttja säkerhetsfunktionerna i virtuella nätverk för att styra både inkommande och utgående nätverkskommunikation.  

En Apptjänst-miljö kan vara antingen mot med en offentlig IP-adress eller intern med endast en Azure interna belastningen belastningsutjämnare (ILB)-adress mot Internet.

Du kan använda [nätverkssäkerhetsgrupper] [ NetworkSecurityGroups] att begränsa inkommande nätverkskommunikationen till undernätet där det finns en Apptjänst-miljö.  På så sätt kan du köra bakom överordnade enheter och tjänster som web application brandväggar och nätverksprovider för SaaS-appar.

Apparna måste också ofta att komma åt företagets resurser, till exempel interna databaser och webbtjänster.  En vanlig metod är att göra dessa slutpunkter som är tillgänglig endast för interna nätverkstrafik som går inom ett virtuellt Azure-nätverk.  När en Apptjänst-miljö är ansluten till samma virtuella nätverk som interna tjänster, appar som körs i miljön kan komma åt dem, inklusive slutpunkter kan nås via [plats-till-plats] [ SiteToSite] och [Azure ExpressRoute] [ ExpressRoute] anslutningar.

För mer information om hur Apptjänstmiljöer fungerar med virtuella nätverk och lokala nätverk finns i följande artiklar på [nätverksarkitektur][NetworkArchitectureOverview], [styra inkommande Trafik][ControllingInboundTraffic], och [säker anslutning till serverdelar][SecurelyConnectingToBackends]. 

## <a name="getting-started"></a>Komma igång
Kom igång med Apptjänstmiljöer finns [hur att skapa en Apptjänst-miljö][HowToCreateAnAppServiceEnvironment]

En översikt över nätverksarkitektur Apptjänst-miljö finns i [arkitektur, översikt] [ NetworkArchitectureOverview] artikel.

Mer information om hur du använder en Apptjänst-miljö med ExpressRoute finns i följande artikel på [Express Route och Apptjänstmiljöer][NetworkConfigDetailsForExpressRoute].

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[PremiumTier]: http://azure.microsoft.com/pricing/details/app-service/
[MoreInfoOnVirtualNetworks]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePlan]: ../azure-web-sites-web-hosting-plans-in-depth-overview.md
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md
[LogicApps]: http://azure.microsoft.com/documentation/articles/app-service-logic-what-are-logic-apps/
[AzureConDeepDive]:  https://azure.microsoft.com/documentation/videos/azurecon-2015-deploying-highly-scalable-and-secure-web-and-mobile-apps/
[GeodistributedAppFootprint]:  app-service-app-service-environment-geo-distributed-scale.md
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[SiteToSite]: https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[HowToConfigureanAppServiceEnvironment]:  app-service-web-configure-an-app-service-environment.md
[ControllingInboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[SecurelyConnectingToBackends]:  app-service-app-service-environment-securely-connecting-to-backend-resources.md
[NetworkArchitectureOverview]:  app-service-app-service-environment-network-architecture-overview.md
[NetworkConfigDetailsForExpressRoute]:  app-service-app-service-environment-network-configuration-expressroute.md
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/ 

<!-- IMAGES -->


