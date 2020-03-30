---
title: Haveriberedskap för Azure Data Share
description: Haveriberedskap för Azure Data Share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 12/18/2019
ms.openlocfilehash: a736e3ddfcf785f9ce27140eed58374a0732c1f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75483185"
---
# <a name="disaster-recovery-for-azure-data-share"></a>Haveriberedskap för Azure Data Share

I den här artikeln går vi igenom hur du konfigurerar en katastrofåterställningsmiljö för Azure Data Share. Avbrott i Azure-datacenter är sällsynta, men kan pågå allt från några minuter till timmar. Avbrott i Data center kan orsaka avbrott i miljöer som förlitar sig på data som delas av dataleverantören. Genom att följa stegen i den här artikeln kan dataleverantörer fortsätta att dela data med sina datakonsumenter i händelse av ett datacenteravbrott för den primära regionen som är värd för din dataresurs. 

## <a name="achieving-business-continuity-for-azure-data-share"></a>Uppnå affärskontinuitet för Azure Data Share

Dataprovidern kan ha en dataresursmiljö etablerad i en sekundär region för att förberedas för ett avbrott i datacenter. Det finns åtgärder som kan vidtas som säkerställer en smidig redundans i händelse av att ett datacenteravbrott inträffar. 

Dataleverantörer kan etablera sekundära Azure Data Share-resurser i ytterligare en region. Dessa datadelningsresurser kan konfigureras så att de innehåller datauppsättningar som finns i den primära dataresursmiljön. Datakonsumenter kan läggas till i dataresursen när de konfigurerar DR-miljön, eller läggs till vid en senare tidpunkt (dvs. som en del av manuella redundanssteg).

Om datakonsumenterna har en aktiv aktieprenumeration i en sekundär miljö som etablerats för DR-ändamål, kan de aktivera ögonblicksbildschemat som en del av en redundans. Om de datakonsumenter inte vill prenumerera på en sekundär region för DR-ändamål kan de bjudas in till den sekundära dataresursen vid ett senare tillfälle. 

Datakonsumenter kan antingen ha en aktiv delningsprenumeration som är inaktiv för DR-ändamål, eller så kan dataleverantörer lägga till dem vid en senare tidpunkt som en del av manuella redundansprocedurer. 

## <a name="related-information"></a>Relaterad information

- [Kontinuitet i verksamheten och katastrofåterställning](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)
- [Bygg in hög tillgänglighet i din BCDR-strategi](https://docs.microsoft.com/azure/architecture/solution-ideas/articles/build-high-availability-into-your-bcdr-strategy)

## <a name="next-steps"></a>Nästa steg

Om du vill veta hur du börjar dela data fortsätter du till [att dela din datahandledning.](share-your-data.md)




