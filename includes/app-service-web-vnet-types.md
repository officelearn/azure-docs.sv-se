---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: da43e639779dd4946cb014be777e3a6db0366e24
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78671517"
---
* Datorer med flera innehavare som stöder alla pris avtal förutom isolerade
* App Service-miljön (ASE), som distribueras till ditt VNet och stöder isolerade appar för pris plan

Det här dokumentet går igenom funktionen för VNet-integrering som används i appar för flera klient organisationer. Om din app är i [App Service-miljön][ASEintro]är den redan i ett VNet och kräver inte att funktionen VNet-integrering används för att komma åt resurser i samma VNet. Mer information om funktionerna i nätverket finns i [App Service nätverksfunktioner][Networkingfeatures]

VNet-integrering ger din app åtkomst till resurser i det virtuella nätverket, men beviljar inte inkommande privat åtkomst till din app från VNet. Åtkomst till privata webbplatser avser att göra appen endast tillgänglig från ett privat nätverk, till exempel från ett virtuellt Azure-nätverk. VNet-integrering är bara för att göra utgående samtal från din app till ditt VNet. Funktionen VNet-integrering fungerar annorlunda när den används med virtuella nätverk i samma region och med virtuella nätverk i andra regioner. Funktionen för VNet-integrering har två varianter.

* Regional VNet-integration – när du ansluter till Resource Manager-virtuella nätverk i samma region måste du ha ett dedikerat undernät i det virtuella nätverk som du integrerar med. 
* Gateway krävs VNet-integrering – vid anslutning till virtuella nätverk i andra regioner eller till ett klassiskt VNet i samma region måste du ha en Virtual Network Gateway etablerad i mål-VNet.

Funktionerna för VNet-integrering:

* Kräv en pris plan för standard, Premium, PremiumV2 eller elastisk Premium 
* stöd för TCP och UDP
* arbeta med App Service appar och Function-appar

Det finns vissa saker som VNet-integrering inte stöder, inklusive:

* montera en enhet
* AD-integrering 
* NetBios

Gateway krävs endast VNet-integrering ger till gång till resurser i målets virtuella nätverk eller i nätverk som är anslutna till målets virtuella nätverk med peering eller VPN. Gateway krävs VNet-integrering ger inte åtkomst till resurser som är tillgängliga i ExpressRoute-anslutningar eller fungerar med tjänst slut punkter. 

Oavsett vilken version som används ger VNet-integration appen åtkomst till resurser i det virtuella nätverket, men beviljar inte inkommande privat åtkomst till din app från det virtuella nätverket. Åtkomst till privata webbplatser avser att göra din app endast tillgänglig från ett privat nätverk, till exempel från ett virtuellt Azure-nätverk. VNet-integrering är bara för att göra utgående samtal från din app till ditt VNet. 

<!--Links-->
[ASEintro]: https://docs.microsoft.com/azure/app-service/environment/intro
[Networkingfeatures]: https://docs.microsoft.com/azure/app-service/networking-features