---
title: Mallexempel för Azure Resource Manager - Azure Front Door
description: Mallexempel för Azure Resource Manager för Azure Front Door
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2020
ms.author: sharadag
ms.openlocfilehash: 61ce63b15d2126a25b444e97acc8a3ea3964296b
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985821"
---
# <a name="azure-resource-manager-deployment-model-templates-for-front-door"></a>Azure Resource Manager-distributionsmodellmallar för Front Door

Följande tabell innehåller länkar till Azure Resource Manager-distributionsmodellmallar för Azure Front Door. 

| | |
| ---| ---|
| [Skapa en grundläggande Front Door](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-create-basic)| Skapar en grundläggande Front Door-konfiguration med en enda serverdel. |
| [Skapa en Front Door med flera serverdelar och serverdelspooler och URL-baserad routning](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-create-multiple-backends)| Skapar en Front Door med lastbalansering konfigurerat för flera serverdelar i en serverdelspool och även över serverdelspooler baserade på en URL-sökväg. |
| [Ombord på en anpassad domän med ytterdörr](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-custom-domain)| Lägg till en anpassad domän i din Front Door. |
| [Skapa ytterdörr med geofiltrering](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-geo-filtering)| Skapa en ytterdörr som tillåter/blockerar trafik från vissa länder/regioner. |
| [Kontrollera hälsoavsökningar för dina serverdelar i Front Door](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-health-probes)| Uppdatera din Front Door för att ändra inställningarna för avsökning av hälsotillstånd genom att uppdatera sökvägen för avsökning och de intervall då avsökningar kommer att skickas. |
| [Skapa Front Door med en serverdelskonfiguration i aktivt/vänteläge](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-priority-lb)| Skapar en Front Door som visar prioritetsbaserad routning för programtopologin aktiv/vänteläge, det vill säga att som standard skicka all trafik till den primära (högsta prioritet)-serverdelen tills den inte är tillgänglig. |
| [Skapa Front Door med cachelagring aktiverat för vissa vägar](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-create-caching)| Skapar en Front Door med cachelagring aktiverat för den definierade routningskonfigurationen vilket cachelagrar statiska tillgångar för din arbetsbelastning. |
| [Konfigurera sessionstillhörighet för dina Front Door-värdnamn](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-session-affinity) | Uppdaterar en Front Door för att aktivera sessionstillhörighet för din klientdelsvärd, skickar efterföljande trafik från samma användarsession till samma serverdel. |
| [Konfigurera Front Door för IP-vitlistning eller svartlistning i klienten](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip)| Konfigurerar en Front Door att begränsa trafiken för vissa klienters IP-adresser som använder anpassade åtkomstkontroller med hjälp av klienternas IP-adresser. |
| [Konfigurera ytterdörren för att vidta åtgärder med specifika http-parametrar](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-http-params)| Konfigurerar en Front Door att tillåta eller blockera viss trafik baserat på http-parametrarna i inkommande begäranden med hjälp av anpassade regler för åtkomstkontroll med http-parametrar. |
| [Konfigurera hastighetsbegränsning för ytterdörren](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-rate-limiting)| Konfigurerar en Front Door för inkommande trafik för en given klientdelsvärd. |
| | |

## <a name="next-steps"></a>Nästa steg

- Läs hur du [skapar en Front Door](quickstart-create-front-door.md).
- Läs [hur Front Door fungerar](front-door-routing-architecture.md).
