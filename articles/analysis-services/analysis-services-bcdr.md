---
title: Azure Analysis Services hög tillgänglighet | Microsoft-dokument
description: I den här artikeln beskrivs hur Azure Analysis Services ger hög tillgänglighet vid avbrott i tjänsten.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 2e750dce804ea93f3d3068ffd36bc7a73a50906a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73573362"
---
# <a name="analysis-services-high-availability"></a>Analysis Services hög tillgänglighet

I den här artikeln beskrivs hög tillgänglighet för Azure Analysis Services-servrar. 

## <a name="assuring-high-availability-during-a-service-disruption"></a>Säkerställa hög tillgänglighet vid avbrott i tjänsten

Även om det är sällsynt kan ett Azure-datacenter ha ett avbrott. När ett avbrott inträffar orsakar det ett avbrott i verksamheten som kan pågå i några minuter eller kan pågå i timmar. Hög tillgänglighet uppnås oftast med serverredundans. Med Azure Analysis Services kan du uppnå redundans genom att skapa ytterligare sekundära servrar i en eller flera regioner. När du skapar redundanta servrar kan du:

* Distribuera modeller till redundanta servrar i andra regioner. Den här metoden kräver bearbetning av data på både den primära servern och redundanta servrar parallellt, vilket garanterar att alla servrar är synkroniserade.

* [Säkerhetskopior](analysis-services-backup.md) från den primära servern och återställa på redundanta servrar. Du kan till exempel automatisera nattliga säkerhetskopior till Azure-lagring och återställa till andra redundanta servrar i andra regioner. 

I båda fallen, om din primära server upplever ett avbrott, måste du ändra anslutningssträngarna i rapporteringsklienter för att ansluta till servern i ett annat regionalt datacenter. Denna förändring bör betraktas som en sista utväg och endast om ett katastrofalt regionalt datacenter avbrott inträffar. Det är mer troligt att ett datacenter avbrott som är värd för din primära server skulle komma tillbaka online innan du kunde uppdatera anslutningar på alla klienter. 

Om du vill undvika att behöva ändra anslutningssträngar för rapportklienter kan du skapa ett [serveralias](analysis-services-server-alias.md) för den primära servern. Om den primära servern går ned kan du ändra aliaset så att det pekar på en redundant server i en annan region. Du kan automatisera alias till servernamn genom att koda en hälsokontroll av slutpunkter på den primära servern. Om hälsokontrollen misslyckas kan samma slutpunkt dirigeras till en redundant server i en annan region. 

## <a name="related-information"></a>Relaterad information

[Säkerhetskopiera och återställa](analysis-services-backup.md)   
[Hantera Azure Analysis Services](analysis-services-manage.md)   
[Namn på aliasserver](analysis-services-server-alias.md) 

