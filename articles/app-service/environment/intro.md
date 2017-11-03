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
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.openlocfilehash: 322cf2ebbe83d00fcebcec618e07141d26f4f255
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-app-service-environments"></a>Introduktion till apptjänstmiljöer #
 
## <a name="overview"></a>Översikt ##

Azure Apptjänst-miljö är en funktion i Azure App Service som tillhandahåller en helt isolerad och dedikerad miljö för säker körning av Apptjänst-appar i hög skala. Den här funktionen kan vara värd för dina webbappar [mobilappar][mobileapps], API apps och [funktioner][Functions].

Apptjänstmiljöer (ASEs) som är lämpliga för arbetsbelastningar för program som kräver:

- Mycket hög skala.
- Isolering och säker nätverksåtkomst.
- Hög minnesanvändning.

Kunder kan skapa flera ASEs inom ett enda Azure-region eller över flera Azure-regioner. Den här flexibiliteten gör ASEs idealiskt för vågrätt skalning tillståndslös programnivåerna stöd för hög RPS arbetsbelastningar.

ASEs är isolerad för att endast en enskild kund program som körs och alltid har distribuerats till ett virtuellt nätverk. Kunder har detaljerad kontroll över inkommande och utgående nätverkstrafik. Program kan upprätta säkra höghastighetsanslutning över VPN-anslutningar till lokala företagsresurser.

* ASEs Aktivera hög skala appen värd med säker nätverksåtkomst. Mer information finns i [AzureCon ingående](https://azure.microsoft.com/documentation/videos/azurecon-2015-deploying-highly-scalable-and-secure-web-and-mobile-apps/) på ASEs.
* Flera ASEs kan användas för att skala vågrätt. Mer information finns i [hur du ställer in en fördelade app storleken](app-service-app-service-environment-geo-distributed-scale.md).
* ASEs kan användas för att konfigurera Säkerhetsarkitektur som visas i AzureCon ingående. Hur säkerhetsarkitekturen som visas i AzureCon ingående har konfigurerats, finns det [artikel om hur du implementerar en skiktad säkerhetsarkitekturen](app-service-app-service-environment-layered-security.md) med Apptjänst-miljöer.
* Appar som körs på ASEs kan behörighet gated av överordnad enheter, till exempel web application brandväggar (WAFs). Mer information finns i [konfigurera en Brandvägg för apptjänstmiljöer](app-service-app-service-environment-web-application-firewall.md).

## <a name="dedicated-environment"></a>Dedikerade miljön ##

En ASE är avsedda enbart för en enda prenumeration och kan vara värd för 100 instanser. Intervallet kan sträcka sig över 100 instanser i en enda programtjänstplanen till 100 single instance App Service-planer och allt i mellan.

En ASE består av frontwebbservrarna och personer. Frontwebbservrarna ansvarar för HTTP/HTTPS-avslutning och automatisk belastningsutjämning för app-förfrågningar inom en ASE. Frontwebbservrarna läggs automatiskt till som programtjänstplaner i ASE skalas ut.

Anställda är roller som värd för kund-appar. Anställda finns i tre fast storlek:

* En core/3.5 GB RAM-minne
* Två kärnor/7 GB RAM-minne
* Fyra kärnor/14 GB RAM-minne

Kunder behöver inte hantera frontwebbservrarna och personer. Alla infrastruktur läggs automatiskt till som kunder skala ut sin App Service-planer. Eftersom programtjänstplaner skapas eller skalas i en ASE, den nödvändiga infrastrukturen läggs till eller tas bort efter behov.

Det finns en platt månatliga för en ASE som betalar för infrastrukturen och ändras inte med storleken på ASE. Dessutom finns en kostnad per App Service-plan kärna. Alla appar som finns i en ASE finns i isolerad priser SKU. Mer information om priser för en ASE finns i [priser för Apptjänst] [ Pricing] sidan och granska de tillgängliga alternativen för ASEs.

## <a name="virtual-network-support"></a>Stöd för virtuella nätverk ##

En ASE kan bara skapas i ett virtuellt nätverk med Azure Resource Manager. Mer information om virtuella Azure-nätverk finns i [Azure virtual networks vanliga frågor och svar](https://azure.microsoft.com/documentation/articles/virtual-networks-faq/). En ASE finns alltid i ett virtuellt nätverk och mer exakt i ett undernät i ett virtuellt nätverk. Du kan använda säkerhetsfunktionerna i virtuella nätverk för att kontrollera inkommande och utgående nätverkskommunikation för dina appar.

En ASE kan vara mot internet med en offentlig IP-adress eller internt riktade med endast en Azure internt (ILB)-adressen för belastningsutjämnaren.

[Nätverkssäkerhetsgrupper] [ NSGs] begränsa inkommande nätverkskommunikationen till undernätet där en ASE finns. Du kan använda NSG: er för att köra bakom överordnade enheter och tjänster som WAFs och nätverksprovider för SaaS-appar.

Apparna måste också ofta att komma åt företagets resurser, till exempel interna databaser och webbtjänster. Om du distribuerar ASE i ett virtuellt nätverk som har en VPN-anslutning till det lokala nätverket appar i ASE kan komma åt lokala resurser. Den här funktionen gäller oavsett om VPN är en [plats-till-plats](https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/) eller [Azure ExpressRoute](http://azure.microsoft.com/services/expressroute/) VPN.

Mer information om hur ASEs fungerar med virtuella nätverk och lokala nätverk finns [Apptjänstmiljö Nätverksöverväganden][ASENetwork].

## <a name="app-service-environment-v1"></a>App Service Environment v1 ##

Apptjänst-miljö har två versioner: ASEv1 och ASEv2. Informationen ovan har baserat på ASEv2. Detta avsnitt visar skillnaderna mellan ASEv1 och ASEv2. 

ASEv1 behöver du hantera alla resurser manuellt. Som innehåller frontwebbservrarna, personal och IP-adresser som används för IP-baserade SSL. Innan du kan skala ut din programtjänstplan måste du första skala ut arbetspool där du vill ha den.

ASEv1 använder en annan prisnivå modell från ASEv2. ASEv1 betalar du för varje kärna allokerade. Som innehåller kärnor som används för frontwebbservrarna eller personer som inte är värd för alla arbetsbelastningar. I ASEv1 är maximal skala standardstorleken för en ASE 55 Totalt antal värdar. Som innehåller arbetare och frontwebbservrarna. En fördel med att ASEv1 är kan distribueras i ett klassiskt virtuellt nätverk och ett virtuellt nätverk för hanteraren för filserverresurser. Läs mer om ASEv1 i [Apptjänstmiljö v1 introduktion][ASEv1Intro].

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
