---
title: Introduktion till App Service Environment version 1 – Azure
description: Läs mer om App Service Environment v1-funktion som ger säker, anslutna till virtuella nätverk och dedikerade skalningsenheter för att köra alla dina appar.
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
ms.custom: seodec18
ms.openlocfilehash: 2bb1a9c3922f435b6be78614aacff6e85bf475ff
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62130746"
---
# <a name="introduction-to-app-service-environment-v1"></a>Introduktion till App Service Environment version 1

> [!NOTE]
> Den här artikeln handlar om App Service Environment v1.  Det finns en nyare version av App Service Environment som är enklare att använda och körs på kraftfullare infrastruktur. Mer information om den nya versionen början med den [introduktion till App Service Environment](intro.md).

## <a name="overview"></a>Översikt

En App Service Environment är en [Premium] [ PremiumTier] tjänsten Prenumerationsalternativ av [Azure App Service](../overview.md) som ger en helt isolerad och dedikerad miljö för säker körning av Azure App Service-appar med hög skalbarhet, inklusive Web Apps, Mobile Apps och API Apps.  

App Service-miljöer är perfekt för programarbetsbelastningar som kräver:

* Mycket hög skala
* Isolering och säker nätverksåtkomst

Kunder kan skapa flera App Service-miljöer inom en enda Azure-region, samt mellan flera Azure-regioner.  Detta gör App Service-miljöer perfekta för vågrätt skalning tillstånd utan programnivåer med stöd höga RPS-arbetsbelastningar.

App Service-miljöer är isolerade i endast en enda kunds program som körs och de distribueras alltid till ett virtuellt nätverk.  Kunderna har detaljerad kontroll över både inkommande och utgående programnätverkstrafik och programmen kan upprätta säkra höghastighetsanslutningar över virtuella nätverk till lokala företagsresurser.

En översikt över hur App Service-miljöer Aktivera hög skala och säker nätverksåtkomst, finns i den [AzureCon Deep Dive] [ AzureConDeepDive] på App Service-miljöer!

En djupdykning i vågrätt skala med flera App Service-miljöer finns i artikeln om hur du konfigurerar en [geo appfotavtryck][GeodistributedAppFootprint].

Om du vill se hur säkerhetsarkitekturen som visades i AzureCon Deep Dive har konfigurerats, kan du läsa artikeln om att implementera en [lager Säkerhetsarkitektur](app-service-app-service-environment-layered-security.md) med App Service-miljöer.

Appar som körs på App Service-miljöer kan ha sin egen åtkomst begränsad av överordnade enheter, till exempel brandväggar för webbprogram (WAF).  Artikeln om [konfigurering av en WAF för App Service-miljöer](app-service-app-service-environment-web-application-firewall.md) täcker det här scenariot.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="dedicated-compute-resources"></a>Dedikerad beräkningsresurser

Alla beräkningsresurser i en App Service Environment är strikt reserverad för en enda prenumeration och en App Service Environment kan konfigureras med upp till 50 (50)-beräkningsresurser för exklusiv användning av ett enda program.

En App Service Environment består av en resurspool för klientdelen beräkning, samt en till tre worker beräkning resurspooler.

-Adresspoolen på klientsidan innehåller beräkningsresurser som är ansvarig för SSL-avslutning som även automatisk belastningsutjämning av appförfrågningar i en App Service Environment.

Varje arbetarpool innehåller beräkningsresurser som allokeras till [App Service-planer][AppServicePlan], vilket i sin tur innehåller en eller flera Azure App Service-appar.  Eftersom det kan vara upp till tre olika arbetarpooler i en App Service Environment, har du flexibiliteten att välja olika beräkningsresurser för varje arbetarpool.  

Till exempel kan det här du skapa en arbetarpool med mindre kraftfulla beräkningsresurser för App Service-planer avsedd för utveckling eller testning appar.  En andra (eller även tredje) arbetarpool kan använda mer kraftfulla beräkningsresurser som är avsedd för App Service-planer körning av produktionsappar.

Mer information om hur många beräkningsresurser som är tillgängliga för klient- och worker-pooler finns i [så här konfigurerar du en App Service Environment][HowToConfigureanAppServiceEnvironment].  

Mer information om tillgängliga beräknings-resource-storlekar som stöds i en App Service Environment finns den [priser för Apptjänst] [ AppServicePricing] sidan och granska de tillgängliga alternativen för App Service Environment i den Premium-prisnivån.

## <a name="virtual-network-support"></a>Stöd för virtuella nätverk

Du kan skapa en App Service Environment i **antingen** ett Azure Resource Manager-nätverk **eller** ett virtuellt nätverk för klassisk distribution-modellen ([mer information om virtuella nätverk] [MoreInfoOnVirtualNetworks]).  Eftersom en App Service Environment finns alltid i ett virtuellt nätverk och mer exakt i ett undernät till ett virtuellt nätverk kan du utnyttja säkerhetsfunktionerna i virtuella nätverk för att styra både inkommande och utgående nätverkskommunikation.  

En App Service Environment kan vara antingen Internetuppkopplad med en offentlig IP-adress eller intern med bara en Azure intern belastningsutjämnaren (ILB)-adress som riktas mot.

Du kan använda [nätverkssäkerhetsgrupper] [ NetworkSecurityGroups] att begränsa inkommande kommunikation till undernätet där en App Service Environment finns.  På så sätt kan du köra appar bakom överordnade enheter och tjänster, till exempel brandväggar för webbprogram och SaaS-nätverksleverantörer.

Apparna måste ofta även komma åt företagsresurser, t.ex. interna databaser och webbtjänster.  En vanlig metod är att göra de här slutpunkterna tillgänglig endast för intern nätverkstrafik som går i ett Azure-nätverk.  När en App Service Environment är ansluten till samma virtuella nätverk som interna tjänster, appar som körs i miljön kan komma åt dem, inklusive slutpunkter kan nås via [plats-till-plats] [ SiteToSite] och [Azure ExpressRoute] [ ExpressRoute] anslutningar.

För mer information om hur App Service-miljöer fungerar med virtuella nätverk och lokala nätverk finns i följande artiklar på [nätverksarkitektur][NetworkArchitectureOverview], [styra inkommande Trafik][ControllingInboundTraffic], och [på ett säkert sätt ansluta till serverdelar][SecurelyConnectingToBackends]. 

## <a name="getting-started"></a>Komma igång

Kom igång med App Service-miljöer, se [hur du skapar en App Service Environment][HowToCreateAnAppServiceEnvironment]

En översikt över nätverksarkitekturen i App Service Environment finns i den [översikt över nätverksarkitekturen] [ NetworkArchitectureOverview] artikeln.

Mer information om hur du använder en App Service Environment med ExpressRoute, finns i följande artikel på [Expressroute och Apptjänstmiljöer][NetworkConfigDetailsForExpressRoute].

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[PremiumTier]: https://azure.microsoft.com/pricing/details/app-service/
[MoreInfoOnVirtualNetworks]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePlan]: ../overview-hosting-plans.md
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md
[LogicApps]: https://azure.microsoft.com/documentation/articles/app-service-logic-what-are-logic-apps/
[AzureConDeepDive]:  https://azure.microsoft.com/documentation/videos/azurecon-2015-deploying-highly-scalable-and-secure-web-and-mobile-apps/
[GeodistributedAppFootprint]:  app-service-app-service-environment-geo-distributed-scale.md
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[SiteToSite]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-multi-site
[ExpressRoute]: https://azure.microsoft.com/services/expressroute/
[HowToConfigureanAppServiceEnvironment]:  app-service-web-configure-an-app-service-environment.md
[ControllingInboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[SecurelyConnectingToBackends]:  app-service-app-service-environment-securely-connecting-to-backend-resources.md
[NetworkArchitectureOverview]:  app-service-app-service-environment-network-architecture-overview.md
[NetworkConfigDetailsForExpressRoute]:  app-service-app-service-environment-network-configuration-expressroute.md
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/ 

<!-- IMAGES -->