---
title: Översikt över affärskontinuitet
titleSuffix: Microsoft Genomics
description: I den här översikten beskrivs de funktioner som Microsoft Genomics tillhandahåller för affärskontinuitet och haveriberedskap.
keywords: kontinuitet i verksamheten, katastrofåterställning
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: conceptual
ms.date: 04/06/2018
ms.openlocfilehash: 28a4a53851155c56e8d34981862bf52a3a2cf15b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72249180"
---
# <a name="overview-of-business-continuity-with-microsoft-genomics"></a>Översikt över affärskontinuitet med Microsoft Genomics
I den här översikten beskrivs de funktioner som Microsoft Genomics tillhandahåller för affärskontinuitet och haveriberedskap. Lär dig mer om alternativ för återställning från störande händelser, till exempel ett avbrott i Azure-regionen, som kan orsaka dataförlust. 


## <a name="microsoft-genomics-features-that-support-business-continuity"></a>Microsoft Genomics-funktioner som stöder affärskontinuitet 
Även om det är sällsynt kan ett Azure-datacenter ha ett avbrott, vilket kan orsaka ett avbrott i verksamheten som kan pågå några minuter till några timmar. När ett avbrott inträffar misslyckas alla jobb som körs i datacentret och Microsoft Genomics-tjänsten skickar inte automatiskt jobb till en sekundär region. 

* Ett alternativ är att vänta på att datacentret är online igen när datacentrets avbrott är över. Om avbrottet är kort identifierar Microsoft Genomics-tjänsten automatiskt de misslyckade jobben och arbetsflödet startas om automatiskt.

* Ett annat alternativ är att proaktivt skicka arbetsflödet i en annan dataregion. Microsoft Genomics distribuerar instanser i flera [Azure-regioner](https://azure.microsoft.com/regions/services/)och varje regionspecifik instans är oberoende. Om en av Microsoft Genomics-instanserna drabbas av ett lokalt regionfel fortsätter andra regioner som kör instanser av Microsoft Genomics att bearbeta jobb. Denna överföring till en alternativ region är under användarens kontroll och tillgänglig när som helst.


### <a name="manually-failover-microsoft-genomics-workflows-to-another-region"></a>Manuellt redundans Microsoft Genomics-arbetsflöden till en annan region
Om ett regionalt datacenter avbrott inträffar kan du välja att skicka in Microsoft Genomics-arbetsflöden i en sekundär region, baserat på din individuella datasuveränitet och krav på affärskontinuitet. Om du vill redundan Microsoft Genomics-arbetsflöden manuellt använder du en annan regionspecifik. Genomics-konto och skicka jobbet med lämpliga regionspecifika genomik- och lagringskontouppgifter.

Specifikt måste du:
* Skapa ett genomikkonto i den sekundära regionen med hjälp av Azure-portalen. 
* Migrera indata till ett lagringskonto i den sekundära regionen och konfigurera en utdatamapp i den sekundära regionen.
* Skicka arbetsflödet i den sekundära regionen.

När den ursprungliga regionen återställs migrerar inte Microsoft Genomics-tjänsten data från den sekundära regionen tillbaka till den ursprungliga regionen. Du kan välja att flytta in- och utdatafilerna från den sekundära regionen tillbaka till den ursprungliga regionen.  Om du väljer att flytta deras data ligger detta utanför Genomics-tjänsten och alla avgifter relaterade till dataförflyttningen skulle vara ditt ansvar. 

### <a name="preparing-for-a-possible-region-specific-outage"></a>Förberedelser för ett eventuellt regionspecifikt avbrott
Om du är orolig för snabbare återställning vid avbrott i ett datacenter finns det några steg du kan vidta för att minska den tid det tar för dig att manuellt skicka in dina Microsoft Genomics-arbetsflöden till en sekundär region:

* Identifiera en lämplig sekundär region och proaktivt skapa ett genomikkonto i den regionen
* Duplicera dina data i den primära och sekundära regionen så att dina data omedelbart är tillgängliga i den sekundära regionen. Detta kan göras manuellt eller med hjälp av den [geo-redundanta lagringsfunktionen](https://docs.microsoft.com/azure/storage/common/storage-redundancy) som är tillgänglig i Azure-lagring. 

## <a name="next-steps"></a>Nästa steg
I den här artikeln fick du lära dig om dina alternativ för affärskontinuitet och haveriberedskap när du använder Microsoft Genomics-tjänsten. Mer information om affärskontinuitet och haveriberedskap i Azure i allmänhet finns i [Teknisk vägledning för Azure resiliency.](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region) 