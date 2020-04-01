---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: 41b7c7163ff56c848b1b66e606c06ba45d36e610
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80419559"
---
* De multitenantsystem som stöder hela skalan av prisplaner utom Isolerade.
* App servicemiljön, som distribueras till ditt virtuella nätverk och stöder appar för isolerade prisplaner.

Funktionen VNet-integrering används i appar med flera innehavare. Om din app finns i [App Service Environment][ASEintro]finns den redan i ett virtuellt nätverk och kräver inte användning av funktionen VNet-integrering för att nå resurser i samma virtuella nätverk. Mer information om alla nätverksfunktioner finns i [App Service-nätverksfunktioner][Networkingfeatures].

VNet-integrering ger din app åtkomst till resurser i ditt virtuella nätverk, men det ger inte inkommande privat åtkomst till din app från det virtuella nätverket. Privat webbplatsåtkomst avser att göra en app endast tillgänglig från ett privat nätverk, till exempel från ett virtuellt Azure-nätverk. VNet-integrering används bara för att ringa utgående samtal från din app till ditt virtuella nätverk. Funktionen VNet-integrering fungerar annorlunda när den används med virtuella nätverk i samma region och med virtuella nätverk i andra regioner. Funktionen VNet-integrering har två varianter:

* **Regional VNet-integrering:** När du ansluter till virtuella Azure Resource Manager-nätverk i samma region måste du ha ett dedikerat undernät i det virtuella nätverk som du integrerar med.
* **Gateway-obligatorisk VNet-integrering:** När du ansluter till virtuella nätverk i andra regioner eller till ett klassiskt virtuellt nätverk i samma region behöver du en Azure Virtual Network-gateway som är etablerad i det virtuella nätverket för målet.

Funktionerna för VNet-integrering:

* Kräv en prisplan för Standard, Premium, PremiumV2 eller Elastic Premium.
* Stöd TCP och UDP.
* Arbeta med Azure App Service-appar och funktionsappar.

Det finns några saker som VNet Integration inte stöder, till exempel:

* Montera en drivning.
* Active Directory-integrering.
* Netbios.

Gateway-obligatorisk VNet-integrering ger endast åtkomst till resurser i det virtuella målnätverket eller i nätverk som är anslutna till det virtuella nätverket med peering eller VPN. Gateway-obligatorisk VNet-integrering möjliggör inte åtkomst till resurser som är tillgängliga för Azure ExpressRoute-anslutningar eller fungerar med tjänstslutpunkter.

Oavsett vilken version som används ger VNet Integration din app åtkomst till resurser i det virtuella nätverket, men det ger inte inkommande privat åtkomst till din app från det virtuella nätverket. Privat webbplatsåtkomst avser att göra din app endast tillgänglig från ett privat nätverk, till exempel från ett virtuellt Azure-nätverk. VNet-integrering är endast till för att ringa utgående samtal från din app till ditt virtuella nätverk.

<!--Links-->
[ASEintro]: https://docs.microsoft.com/azure/app-service/environment/intro
[Networkingfeatures]: https://docs.microsoft.com/azure/app-service/networking-features
