---
title: Verksamhetskontinuitet – Microsoft Genomics | Microsoft Docs
titleSuffix: Azure
description: Den här översikten beskriver de funktioner som Microsoft Genomics tillhandahåller för affärskontinuitet och haveriberedskap. Läs mer om alternativ för att återställa från avbrott, till exempel driftstörningar i Azure-region, som kan orsaka förlust av data.
keywords: Affärskontinuitet, katastrofåterställning
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: article
ms.date: 04/06/2018
ms.openlocfilehash: 7a51477dbbf6f4e50959a6d979342961c7e49ad9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60641117"
---
# <a name="overview-of-business-continuity-with-microsoft-genomics"></a>Översikt över affärskontinuitet med Microsoft Genomics
Den här översikten beskriver de funktioner som Microsoft Genomics tillhandahåller för affärskontinuitet och haveriberedskap. Läs mer om alternativ för att återställa från avbrott, till exempel driftstörningar i Azure-region, som kan orsaka förlust av data. 


## <a name="microsoft-genomics-features-that-support-business-continuity"></a>Microsoft Genomics funktioner som stöd för affärskontinuitet 
Även om det är sällsynt, kan ett Azure-Datacenter ha ett avbrott, vilket kan leda till ett verksamhetsavbrott som kan vara ett par minuter till några timmar. När ett eventuellt strömavbrott misslyckas alla jobb som körs i datacentret och Microsoft Genomics-tjänsten kommer inte automatiskt skicka jobb till en sekundär region. 

* Ett alternativ är att vänta tills Datacenter att är tillbaka online när avbrott i datacenter är över. Om avbrottet är kort Microsoft Genomics-tjänsten identifierar automatiskt jobb som misslyckades och arbetsflödet startas automatiskt.

* Ett annat alternativ är att proaktivt skicka arbetsflödet i en annan dataregion. Microsoft Genomics distribuerar instanser i flera [Azure-regioner](https://azure.microsoft.com/regions/services/), och varje instans regionspecifika är oberoende. Om någon av Microsoft Genomics-instanserna får ett lokalt fel region, andra instanser av Microsoft Genomics-regioner kommer att fortsätta att bearbeta jobb. Den här överföringen till en alternativ region, kontrolleras av användaren och tillgängliga när som helst.


### <a name="manually-failover-microsoft-genomics-workflows-to-another-region"></a>Manuellt redundans Microsoft Genomics arbetsflöden till en annan region
När det gäller ett regionalt avbrott på datacentret, kan du välja att skicka Microsoft Genomics arbetsflöden i en sekundär region, baserat på dina enskilda datasuveränitet och business continuity krav. Du kan utföra olika regionspecifika manuellt redundans Microsoft Genomics arbetsflöden. Genomics-konto och skicka jobbet med lämpliga regionspecifika Genomics och autentiseringsuppgifterna för lagringskontot.

Mer specifikt måste du:
* Skapa ett Genomics-konto i den andra regionen, med hjälp av Azure portal. 
* Migrera dina indata till ett lagringskonto i den sekundära regionen och ställa in en utdatamapp i den sekundära regionen.
* Skicka arbetsflödet i den sekundära regionen.

När den ursprungliga regionen återställs Microsoft Genomics-tjänsten inte migrera data från den sekundära regionen tillbaka till den ursprungliga regionen. Du kan välja att flytta indata och utdatafiler från den sekundära regionen tillbaka till ursprungliga region.  Om du väljer att flytta sina data, det är utanför Genomics-tjänsten och alla avgifter som rör är dataförflyttning ditt ansvar. 

### <a name="preparing-for-a-possible-region-specific-outage"></a>Förbereda för ett möjligt regionspecifika avbrott
Om du är orolig snabbare återställning vid ett avbrott på datacentret, finns det några steg som du kan vidta för att minska den tid det tar du manuellt skicka dina Microsoft Genomics-arbetsflöden till en sekundär region:

* Identifiera en lämplig sekundär region och proaktivt skapar ett Genomics-konto i den regionen
* Duplicera dina data i den primära och sekundära regionen så att dina data är omedelbart tillgängliga i den sekundära regionen. Detta kan vara klar manuellt eller med hjälp av den [geo-redundant lagring](https://docs.microsoft.com/azure/storage/common/storage-redundancy) funktioner som är tillgängliga i Azure storage. 

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig om dina alternativ för affärskontinuitet och haveriberedskap när du använder Microsoft Genomics-tjänsten. Läs mer om affärskontinuitet och haveriberedskap i Azure i allmänhet [Azure återhämtning, tekniska riktlinjer.](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region) 