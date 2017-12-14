---
title: "Introduktion till Azure App Service-miljöer"
description: "Kort översikt över Azure App Service-miljöer"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 3c7eaefa-1850-4643-8540-428e8982b7cb
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 06/13/2017
ms.author: ccompy
ms.custom: mvc
ms.openlocfilehash: 803a1cde5387b549504b42346d1a2e6a5df04746
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2017
---
# <a name="introduction-to-app-service-environments"></a>Introduktion till App Service-miljöer #
 
## <a name="overview"></a>Översikt ##

Azure App Service Environment är en funktion i Azure App Service som innehåller en fullständigt isolerad miljö särskilt avsedd för säker körning av högskaliga App Service-appar. Den här funktionen kan vara värd för dina webbappar, [mobilappar][mobileapps], API-appar och [funktioner][Functions].

App Service-miljöer (ASE) är lämpliga för programarbetsbelastningar som kräver:

- Mycket hög skala.
- Isolering och säker nätverksåtkomst.
- Hög minnesanvändning.

Kunderna kan skapa flera ASE-miljöer inom en enda Azure-region eller över flera Azure-regioner. Den här flexibiliteten gör ASE-miljöer perfekta för vågrätt skalning av tillståndslösa programnivåer med stöd för höga RPS-arbetsbelastningar.

ASE-miljöer är isolerade så att de endast kör en enda kunds program och de distribueras alltid till ett virtuellt nätverk. Kunderna har detaljerad kontroll över inkommande och utgående programnätverkstrafik. Programmen kan upprätta säkra anslutningar med hög hastighet över VPN till lokala företagsresurser.

* ASE-miljöer möjliggör appvärdfunktioner med hög skala och säker nätverksåtkomst. Mer information finns i [AzureCon Deep Dive](https://azure.microsoft.com/documentation/videos/azurecon-2015-deploying-highly-scalable-and-secure-web-and-mobile-apps/) (Djupgående om AzureCon) om ASE-miljöer.
* Flera ASE-miljöer kan användas för att skala vågrätt. Om du vill ha mer information kan du läsa om [hur man konfigurerar ett geodistribuerat appfotavtryck](app-service-app-service-environment-geo-distributed-scale.md).
* ASE-miljöer kan användas för att konfigurera en säkerhetsarkitektur, vilket visas i AzureCon Deep Dive (Djupgående om AzureCon). Om du vill se hur säkerhetsarkitekturen som visades i AzureCon Deep Dive (Djupgående om AzureCon) har konfigurerats kan du läsa [artikeln om att implementera en arkitektur med flernivåsäkerhet](app-service-app-service-environment-layered-security.md) med App Service-miljöer.
* Appar som körs i ASE kan ha sin egen åtkomst begränsad av överordnade enheter, t.ex. brandväggar för webbaserade program (WAF). Mer information finns i [Configure a WAF for App Service environments](app-service-app-service-environment-web-application-firewall.md) (Konfigurera en brandvägg för webbaserade program i App Service-miljöer).

## <a name="dedicated-environment"></a>Dedikerad miljö ##

En ASE-miljö är exklusivt avsedd för en enda prenumeration och kan vara värd för 100 instanser. Omfånget kan sträcka sig över 100 instanser i en enda App Service-plan till 100 App Service-planer med varsin instans, och allt däremellan.

En ASE-miljö består av klientdelar och arbetare. Klientdelarna ansvarar för HTTP/HTTPS-avslutning och automatisk belastningsutjämning av appförfrågningar i en ASE-miljö. Klientdelarna läggs till automatiskt när App Service-planerna i ASE skalas ut.

Arbetare är roller som är värdar för kundappar. Arbetare finns i tre fasta storlekar:

* En vCPU/3,5 GB RAM
* Två vCPU/7 GB RAM
* Fyra vCPU/14 GB RAM

Kunderna behöver inte hantera klientdelar och arbetare. All infrastruktur läggs till automatiskt när kunderna skalar ut sina App Service-planer. Vartefter App Service-planer skapas eller skalas i en ASE-miljö kommer den infrastruktur som krävs att läggas till eller tas bort efter behov.

Det finns en fast månadskostnad för en ASE-miljö som betalar för infrastrukturen och som inte ändras med storleken på ASE-miljön. Dessutom finns en kostnad per vCPU för App Service-planen. Alla appar som har en ASE som värd finns i SKU med isolerad prissättning. Mer information om priser för en ASE-miljö finns på sidan [Prissättning för App Service][Pricing], där du även kan se de tillgängliga alternativen för ASE-miljöer.

## <a name="virtual-network-support"></a>Stöd för virtuellt nätverk ##

En ASE-miljö kan bara skapas i ett virtuellt Azure Resource Manager-nätverk. Mer information om virtuella Azure-nätverk finns i [Azure virtual networks FAQ](https://azure.microsoft.com/documentation/articles/virtual-networks-faq/) (Vanliga frågor och svar om virtuella Azure-nätverk). En ASE-miljö finns alltid i ett virtuellt nätverk och, mer exakt, i ett undernät till ett virtuellt nätverk. Du kan använda säkerhetsfunktionerna för virtuella nätverk för att styra inkommande och utgående nätverkskommunikation för apparna.

En ASE-miljö kan vara antingen Internetuppkopplad med en offentlig IP-adress eller intern med bara en adress för en intern belastningsutjämnare (ILB) i Azure.

[Nätverkssäkerhetsgrupper][NSGs] begränsar inkommande kommunikation till undernätet där en ASE-miljö finns. Du kan använda nätverkssäkerhetsgrupper för att köra appar bakom överordnade enheter och tjänster, t.ex. WAF och SaaS-nätverksleverantörer.

Apparna måste ofta även komma åt företagsresurser, t.ex. interna databaser och webbtjänster. Om du distribuerar ASE-miljön i ett virtuellt nätverk som har en VPN-anslutning till det lokala nätverket kan apparna i ASE-miljön komma åt de lokala resurserna. Den här funktionen gäller oavsett om VPN är en VPN för [plats-till-plats](https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/) eller [Azure ExpressRoute](http://azure.microsoft.com/services/expressroute/).

Mer information om hur ASE-miljöer fungerar med virtuella nätverk och lokala nätverk finns i [App Service Environment network considerations][ASENetwork] (Nätverksöverväganden för App Service Environment).

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Application-Service-Environments-v2-Private-PaaS-Environments-in-the-Cloud/player]

## <a name="app-service-environment-v1"></a>App Service Environment v1 ##

App Service Environment finns i två versioner: ASEv1 och ASEv2. Informationen ovan baserades på ASEv2. Det här avsnittet visar skillnaderna mellan ASEv1 och ASEv2. 

I ASEv1 måste du hantera alla resurser manuellt. Det omfattar klientdelar, arbetare och IP-adresser som används för IP-baserad SSL. Innan du kan skala ut App Service-planen måste du först skala ut arbetarpoolen som du vill använda som värd.

ASEv1 använder en annan prissättningsmodell än ASEv2. I ASEv1 betalar du för varje allokerad vCPU. Det omfattar vCPU-enheter som används för klientdelar eller arbetare som inte är värdar för någon arbetsbelastning. I ASEv1 är den högsta skalstorleken för en ASE-miljö totalt 55 värdar. Det omfattar arbetare och klientdelar. En fördel med ASEv1 är att det går att distribuera i ett klassiskt virtuellt nätverk och ett virtuellt Resource Manager-nätverk. Mer information om ASEv1 finns i [App Service Environment v1 introduction][ASEv1Intro] (Introduktion till App Service Environment v1).

<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/virtual-networks-nsg.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[webapps]: ../app-service-web-overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../web-sites-purchase-ssl-web-site.md
[Kudu]: http://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
