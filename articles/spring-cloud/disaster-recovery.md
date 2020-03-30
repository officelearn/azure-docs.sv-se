---
title: Geokatastrofåterställning av Azure Spring Cloud | Microsoft-dokument
description: Lär dig hur du skyddar ditt Spring Cloud-program från regionala avbrott
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: brendm
ms.openlocfilehash: 4961e5a63e5bc1933cf19b1f291b521d89cbda0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76279143"
---
# <a name="azure-spring-cloud-disaster-recovery"></a>Azure Spring Cloud haveriberedskap

I den här artikeln beskrivs några strategier som du kan använda för att skydda dina Azure Spring Cloud-program från att uppleva driftstopp.  Alla regioner eller datacenter kan drabbas av driftstopp som orsakas av regionala katastrofer, men noggrann planering kan minska påverkan på dina kunder.

## <a name="plan-your-application-deployment"></a>Planera programdistributionen

Azure Spring Cloud-program körs i en viss region.  Azure körs i ett antal geografier över hela världen. En Azure-geografi är ett definierat område i världen som innehåller minst en Azure-region. En Azure-region är ett område inom en geografi som innehåller ett eller flera datacenter.  Varje Azure-region paras ihop med en annan region inom samma geografi, vilket tillsammans gör ett regionalt par. Azure serialiserar plattformsuppdateringar (planerat underhåll) över regionala par, vilket säkerställer att endast en region i varje par uppdateras åt gången. I händelse av ett avbrott som påverkar flera regioner prioriteras minst en region i varje par för återställning.

För att säkerställa hög tillgänglighet och skydd mot katastrofer krävs att du distribuerar dina Spring Cloud-program till flera regioner.  Azure innehåller en lista över [parade regioner](../best-practices-availability-paired-regions.md) så att du kan planera dina Spring Cloud-distributioner till regionala par.  Vi rekommenderar att du överväger tre viktiga faktorer när du utformar din mikrotjänstarkitektur: regiontillgänglighet, Azure-parade regioner och tjänsttillgänglighet.

*  Regiontillgänglighet: Välj ett geografiskt område nära användarna för att minimera nätverksfördröjning och överföringstid.
*  Azure-parade regioner: Välj parade regioner inom ditt valda geografiska område för att säkerställa samordnade plattformsuppdateringar och prioriterade återställningsinsatser om det behövs.
*  Tjänstens tillgänglighet: Bestäm om dina parade områden ska köras varmt/varmt, varmt/varmt eller varmt/kallt.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Använda Azure Traffic Manager för att dirigera trafik

[Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) tillhandahåller DNS-baserad trafikbelastningsutjämning och kan distribuera nätverkstrafik mellan flera regioner.  Använd Azure Traffic Manager för att dirigera kunder till närmaste Azure Spring Cloud-tjänstinstans till dem.  För bästa prestanda och redundans kan du styra all programtrafik via Azure Traffic Manager innan du skickar den till din Azure Spring Cloud-tjänst.

Om du har Azure Spring Cloud-program i flera regioner använder du Azure Traffic Manager för att styra trafikflödet till dina program i varje region.  Definiera en Slutpunkt för Azure Traffic Manager för varje tjänst med hjälp av tjänst-IP.Define an Azure Traffic Manager endpoint for each service using the service IP. Kunder bör ansluta till ett DNS-namn för Azure Traffic Manager som pekar på Azure Spring Cloud-tjänsten.  Azure Traffic Manager belastning balanserar trafik över de definierade slutpunkterna.  Om en katastrof träffar ett datacenter dirigerar Azure Traffic Manager trafik från den regionen till dess par, vilket säkerställer tjänstens kontinuitet.