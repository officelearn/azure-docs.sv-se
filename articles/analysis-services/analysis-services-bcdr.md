---
title: Azure Analysis Services, hög tillgänglighet | Microsoft Docs
description: Säkerställa hög tillgänglighet för Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 51a0f560a0e4b6ff791d5ed3f9f221eb2eeb9b4d
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2019
ms.locfileid: "54191351"
---
# <a name="analysis-services-high-availability"></a>Hög tillgänglighet för Analysis Services

Den här artikeln beskriver säkerställa hög tillgänglighet för Azure Analysis Services-servrar. 

## <a name="assuring-high-availability-during-a-service-disruption"></a>Säkerställa hög tillgänglighet under ett avbrott i tjänsten

När det är sällsynt, kan ett Azure-Datacenter ha ett avbrott. När ett avbrott uppstår orsakar det ett verksamhetsavbrott som kan vara några få minuter eller flera timmar. Hög tillgänglighet uppnås oftast med server-redundans. Du kan få redundans genom att skapa ytterligare, sekundära servrar i en eller flera regioner med Azure Analysis Services. När du skapar redundanta servrar om du vill säkerställa data och metadata på dessa servrar är synkroniserat med servern i en region som är offline, kan du:

* Distribuera modeller till redundanta servrar i andra regioner. Den här metoden kräver att bearbetningsdata på både primära servern och redundanta servrar i parallella, avveckla alla servrar är synkroniserade.

* [Backup](analysis-services-backup.md) databaser från din primära servern och återställning på redundanta servrar. Exempelvis kan du automatisera säkerhetskopieringar till Azure storage och återställa till andra redundanta servrar i andra regioner. 

Om den primära servern uppstår ett avbrott i båda fallen måste du ändra anslutningssträngar i reporting-klienter att ansluta till servern i ett annat regionala datacenter. Den här ändringen ska betraktas som en sista utväg och endast om ett oåterkalleligt regionalt avbrott på datacentret sker. Det är troligare att avbrott i datacentret som är värd för den primära servern är online igen innan du kan uppdatera anslutningar på alla klienter. 

Om du vill undvika att behöva ändra anslutningssträngar på felrapporteringsklienterna, kan du skapa en server [alias](analysis-services-server-alias.md) för den primära servern. Om den primära servern slutar fungera kan ändra du alias så att den pekar till en redundant server i en annan region. Du kan automatisera alias namn genom att skriva en hälsokontroll för slutpunkten på den primära servern. Om hälsokontrollen misslyckas, kan samma slutpunkt dirigera till en redundant server i en annan region. 

## <a name="related-information"></a>Relaterad information

[Säkerhetskopiering och återställning](analysis-services-backup.md)   
[Hantera Azure Analysis Services](analysis-services-manage.md)   
[Aliasnamn för servern](analysis-services-server-alias.md) 

