---
title: "Azure Analysis Services hög tillgänglighet | Microsoft Docs"
description: "Säkerställa hög tillgänglighet för Azure Analysis Services."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
ms.assetid: 
ms.service: analysis-services
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2018
ms.author: owend
ms.openlocfilehash: ed2bb2fe159db146ee520fc600c8b11f2dd4f761
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="analysis-services-high-availability"></a>Hög tillgänglighet för Analysis Services
Den här artikeln beskriver garantera hög tillgänglighet för Azure Analysis Services-servrar. 


## <a name="assuring-high-availability-during-a-service-disruption"></a>Säkerställa hög tillgänglighet vid ett avbrott i tjänsten
När det är sällsynt, kan ett Azure-Datacenter ha ett avbrott. När ett avbrott inträffar gör en business-avbrott som kan senaste några minuter eller kan senaste timmar. Hög tillgänglighet uppnås ofta med server redundans. Du kan uppnå redundans med Azure Analysis Services genom att skapa ytterligare sekundära servrar i en eller flera regioner. När du skapar redundanta servrar för att garantera data och metadata på dessa servrar är synkroniserad med servern i en region som är offline, kan du:

* Distribuera modeller till redundanta servrar i andra regioner. Den här metoden kräver databearbetning på både primära servern och redundanta servrar i parallellt, så alla servrar är synkroniserad.

* [Säkerhetskopiering](analysis-services-backup.md) databaser från den primära servern och återställning på redundanta servrar. Du kan till exempel automatisera säkerhetskopieringar till Azure-lagring och återställa till andra redundant servrar i andra regioner. 

Om den primära servern skulle få ett avbrott i båda fallen måste du ändra anslutningssträngar i reporting klienter att ansluta till servern i ett annat regionala datacenter. Den här ändringen ska betraktas som en sista utväg och endast om ett oåterkalleligt regionala data center avbrott inträffar. Det är troligt att en data center avbrott som värd för den primära servern skulle komma online igen innan du kan uppdatera anslutningar på alla klienter. 

Om du vill undvika att behöva ändra anslutningssträngar på reporting klienter, kan du skapa en server [alias](analysis-services-server-alias.md) för den primära servern. Om den primära servern kraschar, kan du ändra alias att peka till en redundant server i en annan region. Du kan automatisera alias namn genom att skriva en slutpunkt hälsokontroll på den primära servern. Om hälsokontrollen misslyckas, kan samma slutpunkten direkt till en redundant server i en annan region. 

## <a name="related-information"></a>Relaterad information
[Säkerhetskopiering och återställning](analysis-services-backup.md)   
[Hantera Azure Analysis Services](analysis-services-manage.md)   
[Alias servernamn](analysis-services-server-alias.md) 

