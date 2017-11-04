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
ms.date: 11/01/2017
ms.author: owend
ms.openlocfilehash: 554c5e6e3e3cfa2742ef27a3c1510176184b6bd0
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2017
---
# <a name="analysis-services-high-availability"></a>Hög tillgänglighet för Analysis Services
Den här artikeln beskriver garantera hög tillgänglighet för Azure Analysis Services-servrar. 


## <a name="assuring-high-availability-during-a-service-disruption"></a>Säkerställa hög tillgänglighet vid ett avbrott i tjänsten
När det är sällsynt, kan ett Azure-Datacenter ha ett avbrott. När ett avbrott inträffar gör en business-avbrott som kan senaste några minuter eller kan senaste timmar. Hög tillgänglighet uppnås ofta med server redundans. Du kan uppnå redundans med Azure Analysis Services genom att skapa ytterligare sekundära servrar i en eller flera regioner. När du skapar redundanta servrar för att garantera data och metadata på dessa servrar är synkroniserad med servern i en region som är offline, kan du:

* Distribuera modeller till redundanta servrar i andra regioner. Den här metoden kräver databearbetning på både primära servern och redundanta servrar i parallellt, så alla servrar är synkroniserad.

* Säkerhetskopiera databaser från den primära servern och återställning på redundanta servrar. Du kan till exempel automatisera säkerhetskopieringar till Azure-lagring och återställa till andra redundant servrar i andra regioner. 

Om den primära servern skulle få ett avbrott i båda fallen måste du ändra anslutningssträngar i reporting klienter att ansluta till servern i ett annat regionala datacenter. Den här ändringen ska betraktas som en sista utväg och endast om ett oåterkalleligt regionala data center avbrott inträffar. Det är troligt att en data center avbrott som värd för den primära servern skulle komma online igen innan du kan uppdatera anslutningar på alla klienter. 



## <a name="related-information"></a>Relaterad information
[Säkerhetskopiering och återställning](analysis-services-backup.md)   
[Hantera Azure Analysis Services](analysis-services-manage.md) 

