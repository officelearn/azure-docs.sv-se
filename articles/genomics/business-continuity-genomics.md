---
title: Verksamhetskontinuitet – Microsoft Genomics | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur Microsoft Genomics stöder kontinuitet för företag
keywords: kontinuitet för företag, katastrofåterställning
services: microsoft-genomics
author: grhuynh
manager: jhubbard
editor: jasonwhowell
ms.author: grhuynh
ms.service: microsoft-genomics
ms.workload: genomics
ms.topic: article
ms.date: 04/06/2018
ms.openlocfilehash: cb3825cb89aff386c4f7c3f3b0d771d73fe637b1
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="overview-of-business-continuity-with-microsoft-genomics"></a>Översikt över kontinuitet för företag med Microsoft Genomics
Den här översikten beskriver funktionerna som Microsoft Genomics ger för affärskontinuitet och haveriberedskap. Läs mer om alternativ för att återställa från avbrott, till exempel ett avbrott för Azure-region som kan orsaka förlust av data. 


## <a name="microsoft-genomics-features-that-support-business-continuity"></a>Microsoft Genomics funktioner som stöd för företagskontinuitet 
Även om det är sällsynt, kan ett Azure-Datacenter ha ett avbrott, vilket kan leda till avbrott i verksamheten som kan senast några minuter till några timmar. När ett avbrott inträffar, misslyckas alla jobb som körs i datacentret och tjänsten Microsoft Genomics kommer inte automatiskt skicka jobb till en sekundär region. 

* Ett alternativ är att vänta tills Datacenter att ansluts igen när avbrott Datacenter är över. Om avbrottet är kort tjänsten Microsoft Genomics identifierar automatiskt jobb som misslyckades och arbetsflödet startas automatiskt.

* Ett annat alternativ är att skicka arbetsflöde i en annan dataområdet. Microsoft Genomics distribuerar instanser i flera [Azure-regioner](https://azure.microsoft.com/regions/services/), och varje instans regionspecifika är oberoende. Om någon av instanserna Microsoft Genomics ett lokalt fel region, andra regioner som kör instanser av Microsoft Genomics kommer att fortsätta att bearbeta jobb. Den här överföringen till en annan region, kontrolleras av användaren och tillgänglig när som helst.


### <a name="manually-failover-microsoft-genomics-workflows-to-another-region"></a>Manuellt redundans Microsoft Genomics arbetsflöden till en annan region
När det gäller ett regionalt data center avbrott, kan du välja att skicka Microsoft Genomics arbetsflöden i en sekundär region, baserat på dina enskilda suveränitet och business continuity krav. Att manuellt redundans Microsoft Genomics arbetsflöden använder du en annan regionspecifika. Genomik konto och skicka jobbet med lämpliga regionspecifika genomik och lagringskontouppgifter.

Mer specifikt måste du:
* Skapa en genomik-konto i den sekundära regionen som använder Azure portal. 
* Migrera dina indata till ett lagringskonto på den sekundära regionen och Ställ in en utdatamapp i den sekundära regionen.
* Skicka arbetsflödet i den sekundära regionen.

När den ursprungliga regionen återställs migrerar tjänsten Microsoft Genomics inte data från den sekundära regionen tillbaka till den ursprungliga regionen. Du kan välja att flytta inkommande och utgående filer från den sekundära regionen tillbaka till ursprungliga region.  Om du väljer att flytta sina data, utanför tjänsten genomik och alla kostnader som är relaterade till är dataflytten ditt ansvar. 

### <a name="preparing-for-a-possible-region-specific-outage"></a>Förbereda för ett möjligt regionspecifika avbrott
Om du är orolig snabbare återställning vid ett avbrott för data center finns det några steg som du kan vidta för att minska den tid det tar att skicka dina Microsoft Genomics arbetsflöden till en sekundär region manuellt:

* Identifiera en lämplig sekundär region och pro-aktivt skapar ett genomik-konto i den regionen
* Kopiera dina data i den primära och sekundära regionen så att dina data är omedelbart tillgängligt i den sekundära regionen. Detta kan vara klart manuellt eller med hjälp av den [geo-redundant lagring](https://docs.microsoft.com/azure/storage/common/storage-redundancy) funktion i Azure storage. 

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig om alternativen för affärskontinuitet och haveriberedskap när du använder Microsoft Genomics-tjänsten. Mer information om kontinuitet för företag och katastrofåterställning i Azure i allmänhet finns [Azure återhämtning teknisk vägledning.](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region) 