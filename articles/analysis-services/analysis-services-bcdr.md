---
title: Azure Analysis Services hög tillgänglighet | Microsoft Docs
description: Säkerställa hög tillgänglighet för Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: db8f8b9b5af1583662418f774b2eb141bea53ffa
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34596744"
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

