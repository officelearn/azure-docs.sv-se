---
title: Introduktion till ASE v1
description: Läs mer om funktionerna i App Service Environment v1. Det här dokumentet tillhandahålls endast för kunder som använder den äldre v1 ASE.
author: stefsch
ms.assetid: 78e6d4f5-da46-4eb5-a632-b5fdc17d2394
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: f655b7793bfbb5bbeddfc2f1f8e7bc973dabeb4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74687364"
---
# <a name="introduction-to-app-service-environment-v1"></a>Introduktion till App Service Environment v1

> [!NOTE]
> Den här artikeln handlar om App Service Environment v1.  Det finns en nyare version av App Service Environment som är enklare att använda och körs på mer kraftfull infrastruktur. Om du vill veta mer om den nya versionen börjar med [introduktionen till App Service Environment](intro.md).

## <a name="overview"></a>Översikt

En App Service [Premium][PremiumTier] Environment är ett premiumtjänstabonnemangsalternativ för [Azure App Service](../overview.md) som tillhandahåller en helt isolerad och dedikerad miljö för säker körning av Azure App Service-appar i hög skala, inklusive webbappar, mobilappar och API-appar.  

App Service-miljöer är idealiska för programarbetsbelastningar som kräver:

* Mycket hög skala
* Isolering och säker nätverksåtkomst

Kunder kan skapa flera App Service-miljöer inom en enda Azure-region samt i flera Azure-regioner.  Detta gör App Service-miljöer idealiska för horisontellt skalning av tillståndslösa programnivåer till stöd för höga RPS-arbetsbelastningar.

App Service-miljöer är isolerade till att bara köra en enskild kunds program och distribueras alltid till ett virtuellt nätverk.  Kunderna har finkornig kontroll över både inkommande och utgående programnätverkstrafik, och program kan upprätta snabba säkra anslutningar via virtuella nätverk till lokala företagsresurser.

En översikt över hur App Service Environments möjliggör hög skala och säker nätverksåtkomst finns i [AzureCon Deep Dive][AzureConDeepDive] on App Service Environments!

För en djupdykning på horisontellt skalning med flera App Service-miljöer, se artikeln om hur du ställer in en [geodistribuerad appfotavtryck][GeodistributedAppFootprint].

Information om hur säkerhetsarkitekturen som visas i AzureCon Deep Dive har konfigurerats finns i artikeln om implementering av en [säkerhetsarkitektur i flera lager](app-service-app-service-environment-layered-security.md) med App Service-miljöer.

Appar som körs på App Service-miljöer kan få åtkomst gated av uppströmsenheter som brandväggar för webbprogram (WAF).  Artikeln om [hur du konfigurerar en WAF för App Service-miljöer](app-service-app-service-environment-web-application-firewall.md) beskriver det här scenariot.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="dedicated-compute-resources"></a>Dedikerade beräkningsresurser

Alla beräkningsresurser i en App Service-miljö är endast avsedda för en enda prenumeration och en App Service-miljö kan konfigureras med upp till femtio (50) beräkningsresurser för exklusiv användning av ett enda program.

En App Service-miljö består av en frontend-beräkningsresurspool samt en till tre arbetsberäkningsresurspooler.

Frontend-poolen innehåller beräkningsresurser som ansvarar för SSL-avslutning samt automatisk belastningsutjämning av appbegäranden i en App Service-miljö.

Varje arbetarpool innehåller beräkningsresurser som allokerats till [App Service Plans][AppServicePlan], som i sin tur innehåller en eller flera Azure App Service-appar.  Eftersom det kan finnas upp till tre olika arbetspooler i en App Service-miljö har du flexibiliteten att välja olika beräkningsresurser för varje arbetspool.  

På så sätt kan du till exempel skapa en arbetspool med mindre kraftfulla beräkningsresurser för App Service-planer som är avsedda för utvecklings- eller testappar.  En andra (eller till och med tredje) arbetarpool kan använda mer kraftfulla beräkningsresurser som är avsedda för App Service Plans som kör produktionsappar.

Mer information om antalet beräkningsresurser som är tillgängliga för frontend- och arbetarpoolerna finns i [Så här konfigurerar du en apptjänstmiljö][HowToConfigureanAppServiceEnvironment].  

Mer information om tillgängliga beräkningsresursstorlekar som stöds i en App Service-miljö finns på sidan Prissättning av [App-tjänst][AppServicePricing] och läser de tillgängliga alternativen för App Service-miljöer på prisnivån Premium.

## <a name="virtual-network-support"></a>Stöd för virtuella nätverk

En App Service-miljö kan skapas i **antingen** ett virtuellt Azure Resource Manager-nätverk **eller** ett virtuellt klassiskt virtuellt nätverk för distributionsmodell ([mer information om virtuella nätverk][MoreInfoOnVirtualNetworks]).  Eftersom det alltid finns en App Service-miljö i ett virtuellt nätverk, och mer exakt i ett undernät i ett virtuellt nätverk, kan du utnyttja säkerhetsfunktionerna i virtuella nätverk för att styra både inkommande och utgående nätverkskommunikation.  

En App Service-miljö kan antingen vara Internet vänd med en offentlig IP-adress eller intern inför med endast en Azure Internal Load Balancer (ILB) adress.

Du kan använda [nätverkssäkerhetsgrupper][NetworkSecurityGroups] för att begränsa inkommande nätverkskommunikation till undernätet där en App Service-miljö finns.  På så sätt kan du köra appar bakom uppströmsenheter och tjänster som brandväggar för webbprogram och nätverks-SaaS-leverantörer.

Apparna måste ofta även komma åt företagsresurser, t.ex. interna databaser och webbtjänster.  En vanlig metod är att göra dessa slutpunkter tillgängliga endast för intern nätverkstrafik som flödar inom ett virtuellt Azure-nätverk.  När en apptjänstmiljö har anslutits till samma virtuella nätverk som de interna tjänsterna kan appar som körs i miljön komma åt dem, inklusive slutpunkter som kan nås via [Site-to-Site-][SiteToSite] och [Azure ExpressRoute-anslutningar.][ExpressRoute]

Mer information om hur App Service-miljöer fungerar med virtuella nätverk och lokala nätverk finns i följande artiklar om [Nätverksarkitektur,][NetworkArchitectureOverview] [Styra inkommande trafik][ControllingInboundTraffic]och säkert ansluta till [serveringsenheter][SecurelyConnectingToBackends]. 

## <a name="getting-started"></a>Komma igång

Lär dig hur du skapar [en apptjänstmiljö][HowToCreateAnAppServiceEnvironment]

En översikt över nätverksarkitekturen för App Service Environment finns i artikeln [Översikt över nätverksarkitektur.][NetworkArchitectureOverview]

Mer information om hur du använder en apptjänstmiljö med ExpressRoute finns i följande artikel om [Express Route- och App Service-miljöer][NetworkConfigDetailsForExpressRoute].

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