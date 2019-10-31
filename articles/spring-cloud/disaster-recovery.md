---
title: Azure våren Cloud geo-Disaster Recovery | Microsoft Docs
description: Lär dig hur du skyddar ditt våren Cloud-program från regionala avbrott
services: spring-cloud
author: jpconnock
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: jeconnoc
ms.openlocfilehash: 4752cba6081d376bd006406a154b9b182c6a3a72
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73164970"
---
# <a name="azure-spring-cloud-disaster-recovery"></a>Haveri beredskap för Azure våren Cloud

I den här artikeln beskrivs några strategier som du kan använda för att skydda dina Azure våren Cloud-program från att drabbas av drift stopp.  En region eller ett Data Center kan drabbas av drift stopp som orsakas av regionala katastrofer, men noggrann planering kan minimera påverkan på dina kunder.

## <a name="plan-your-application-deployment"></a>Planera program distributionen

Azure våren Cloud-program körs i en angiven region.  Azure fungerar i flera geografiska områden runtom i världen. En Azure-geografi är ett definierat område i världen som innehåller minst en Azure-region. En Azure-region är ett område inom ett geografiskt område som innehåller ett eller flera data Center.  Varje Azure-region är kopplad till en annan region inom samma geografi, tillsammans med ett regionalt par. Azure serialiserar plattforms uppdateringar (planerat underhåll) över regionala par, vilket säkerställer att endast en region i varje par uppdateras i taget. I händelse av ett avbrott som påverkar flera regioner prioriteras minst en region i varje par för återställning.

Att säkerställa hög tillgänglighet och skydd från katastrofer kräver att du distribuerar dina moln program till flera regioner.  Azure tillhandahåller en lista över [kopplade regioner](../best-practices-availability-paired-regions.md) så att du kan planera dina våren-moln distributioner till regionala par.  Vi rekommenderar att du beaktar tre viktiga faktorer när du utformar en mikrotjänsts arkitektur: regions tillgänglighet, Azure-kopplade regioner och tjänst tillgänglighet.

*  Region tillgänglighet: Välj ett geografiskt område nära användarna för att minimera nätverks fördröjningen och överförings tiden.
*  Azure-kopplade regioner: Välj kopplade regioner inom det valda geografiska utrymmet för att säkerställa koordinerade plattforms uppdateringar och prioriterad återställning vid behov.
*  Tjänst tillgänglighet: Bestäm om dina kopplade regioner ska köra frekvent/varm, varm/varm eller varm/kall.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Använd Azure Traffic Manager för att dirigera trafik

[Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) tillhandahåller DNS-baserad belastnings utjämning för trafiken och kan distribuera nätverks trafik över flera regioner.  Använd Azure Traffic Manager för att dirigera kunder till den närmaste Azure våren Cloud Service-instansen till dem.  För bästa prestanda och redundans ska du dirigera all program trafik via Azure Traffic Manager innan du skickar den till din Azure våren Cloud-tjänst.

Om du har Azure våren Cloud-program i flera regioner använder du Azure-Traffic Manager för att styra trafik flödet till dina program i varje region.  Definiera en Azure Traffic Manager-slutpunkt för varje tjänst som använder tjänstens IP-adress. Kunder bör ansluta till ett Azure Traffic Manager DNS-namn som pekar på moln tjänsten Azure våren.  Azure Traffic Manager belastnings Utjämnings trafik mellan de definierade slut punkterna.  Om en katastrof träffar ett Data Center dirigerar Azure Traffic Manager trafiken från den regionen till dess par, vilket garanterar tjänste kontinuiteten.