---
title: Azure Resource Manager-mallexempel – Azure Front Door-tjänsten | Microsoft Docs
description: Azure Resource Manager-mallexempel för Azure Front Door-tjänsten
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/13/2018
ms.author: sharadag
ms.openlocfilehash: 7e40a847b6ea4daa640a7ae6bc69fd25daa86a5b
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64575797"
---
# <a name="azure-resource-manager-deployment-model-templates-for-front-door"></a>Azure Resource Manager-distributionsmodellmallar för Front Door

Följande tabell innehåller länkar till Azure Resource Manager-distributionsmodellmallar för Azure Front Door-tjänsten. 

| | |
| ---| ---|
| [Skapa en grundläggande Front Door](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-create-basic)| Skapar en grundläggande Front Door-konfiguration med en enda serverdel. |
| [Skapa en Front Door med flera serverdelar och serverdelspooler och URL-baserad routning](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-create-multiple-backends)| Skapar en Front Door med lastbalansering konfigurerat för flera serverdelar i en serverdelspool och även över serverdelspooler baserade på en URL-sökväg. |
| [Registrera en anpassad domän med HTTPS (Front Door-hanterat certifikat) med Front Door](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-custom-domain)| Lägg till en anpassad domän till din Front Door och aktivera HTTPS-trafik för den med ett Front Door-hanterat certifikat som skapats via DigiCert. |
| [Skapa Front Door med geofiltrering](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-geo-filtering)| Skapa en ytterdörren som tillåter/blockerar trafik från vissa länder/regioner. |
| [Kontrollera hälsoavsökningar för dina serverdelar i Front Door](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-health-probes)| Uppdatera din Front Door för att ändra inställningarna för avsökning av hälsotillstånd genom att uppdatera sökvägen för avsökning och de intervall då avsökningar kommer att skickas. |
| [Skapa Front Door med en serverdelskonfiguration i aktivt/vänteläge](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-priority-lb)| Skapar en Front Door som visar prioritetsbaserad routning för programtopologin aktiv/vänteläge, det vill säga att som standard skicka all trafik till den primära (högsta prioritet)-serverdelen tills den inte är tillgänglig. |
| [Skapa Front Door med cachelagring aktiverat för vissa vägar](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-create-caching)| Skapar en Front Door med cachelagring aktiverat för den definierade routningskonfigurationen vilket cachelagrar statiska tillgångar för din arbetsbelastning. |
| [Konfigurera sessionstillhörighet för dina Front Door-värdnamn](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-session-affinity) | Uppdaterar en Front Door för att aktivera sessionstillhörighet för din klientdelsvärd, skickar efterföljande trafik från samma användarsession till samma serverdel. |
| [Konfigurera Front Door för IP-vitlistning eller svartlistning i klienten](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip)| Konfigurerar en Front Door att begränsa trafiken för vissa klienters IP-adresser som använder anpassade åtkomstkontroller med hjälp av klienternas IP-adresser. |
| [Konfigurera Front Door att vidta åtgärder med specifika http-parametrar](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-http-params)| Konfigurerar en Front Door att tillåta eller blockera viss trafik baserat på http-parametrarna i inkommande begäranden med hjälp av anpassade regler för åtkomstkontroll med http-parametrar. |
| [Konfigurera Front Door-hastighetsbegränsning](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-rate-limiting)| Konfigurerar en Front Door för inkommande trafik för en given klientdelsvärd. |
| | |

## <a name="next-steps"></a>Nästa steg

- Läs hur du [skapar en Front Door](quickstart-create-front-door.md).
- Läs [hur Front Door fungerar](front-door-routing-architecture.md).