---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: da43e639779dd4946cb014be777e3a6db0366e24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671517"
---
* De flerarnatoriska system som stöder hela skalan av prisplaner utom isolerade
* App Service Environment (ASE), som distribueras till ditt virtuella nätverk och stöder appar för isolerade prisplaner

Det här dokumentet går igenom funktionen VNet-integrering, som är avsedd att användas i appar med flera innehavare. Om din app finns i [App Service Environment][ASEintro]finns den redan i ett virtuella nätverk och kräver inte användning av funktionen VNet-integrering för att nå resurser i samma virtuella nätverk. Mer information om alla nätverksfunktioner finns i [App Service-nätverksfunktioner][Networkingfeatures]

VNet-integrering ger din app åtkomst till resurser i ditt virtuella nätverk men ger inte inkommande privat åtkomst till din app från det virtuella nätverket. Privat webbplatsåtkomst avser att göra appen endast tillgänglig från ett privat nätverk, till exempel inifrån ett virtuellt Azure-nätverk. VNet-integrering är endast till för att ringa utgående samtal från din app till ditt virtuella nätverk. Funktionen VNet-integrering fungerar annorlunda när den används med virtuella nätverk i samma region och med virtuella nätverk i andra regioner. Funktionen VNet-integrering har två varianter.

* Regional VNet-integrering – När du ansluter till virtuella nätverk för Resurshanteraren i samma region måste du ha ett dedikerat undernät i det virtuella nätverk som du integrerar med. 
* Gateway krävs VNet Integration - När du ansluter till virtuella nätverk i andra regioner eller till ett klassiskt virtuellt nätverk i samma region behöver du en virtuell nätverksgateway etablerad i målet VNet.

Funktionerna för VNet-integrering:

* kräver en prisplan för Standard, Premium, PremiumV2 eller Elastic Premium 
* stöd för TCP och UDP
* arbeta med App Service-appar och Funktionsappar

Det finns några saker som VNet Integration inte stöder, inklusive:

* montera en drivning
* AD-integrering 
* NetBios

Gateway krävs VNet Integration ger endast tillgång till resurser i målet virtuella nätverk eller i nätverk som är anslutna till målet VNet med peering eller VPN. Gateway krävs VNet Integration inte aktivera åtkomst till resurser som är tillgängliga över ExpressRoute anslutningar eller fungerar med tjänsten slutpunkter. 

Oavsett vilken version som används ger VNet Integration din app åtkomst till resurser i det virtuella nätverket, men ger inte inkommande privat åtkomst till din app från det virtuella nätverket. Privat webbplatsåtkomst avser att göra din app endast tillgänglig från ett privat nätverk, till exempel från ett virtuellt Azure-nätverk. VNet-integrering är endast till för att ringa utgående samtal från din app till ditt virtuella nätverk. 

<!--Links-->
[ASEintro]: https://docs.microsoft.com/azure/app-service/environment/intro
[Networkingfeatures]: https://docs.microsoft.com/azure/app-service/networking-features