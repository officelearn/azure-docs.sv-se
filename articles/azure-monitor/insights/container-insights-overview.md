---
title: Översikt över Azure Monitor för behållare | Microsoft Docs
description: Den här artikeln beskrivs Azure Monitor för behållare som övervakar AKS-behållare för lösningen och det värde som du får genom att övervaka hälsotillståndet för dina AKS-kluster och Container Instances i Azure.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/27/2019
ms.author: magoedte
ms.openlocfilehash: a31380c8581503a340c55c374afc02c6e1fa290b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60494815"
---
# <a name="azure-monitor-for-containers-overview"></a>Azure Monitor för översikt över behållare

Azure Monitor för behållare är en funktion som utformats för att övervaka prestanda för arbetsbelastningar distribueras till Azure Container Instances eller hanterade Kubernetes-kluster som finns på Azure Kubernetes Service (AKS). Övervaka dina behållare är viktigt, särskilt när du kör ett produktionskluster i skala med flera program.

Azure Monitor för behållare ger dig insyn i prestanda genom att samla in minne och processor mått från domänkontrollanter, noder och behållare som är tillgängliga i Kubernetes via mått-API. Containerloggar samlas också.  När du aktiverar övervakning från Kubernetes-kluster kan dessa mått och loggar automatiskt som samlas in för dig via en behållare version av Log Analytics-agenten för Linux och lagras i din [Log Analytics](../log-query/log-query-overview.md) arbetsyta. 
 
## <a name="what-does-azure-monitor-for-containers-provide"></a>Vad kostar Azure Monitor för behållare ger?

Azure Monitor för behållare innehåller flera fördefinierade vyer som visar som förvaras behållararbetsbelastningar och vad påverkar prestanda hälsotillståndet för övervakade Kubernetes-klustret så att du kan:  

* Identifiera AKS-behållare som körs på noden och deras genomsnittsanvändningen av processor och minne. Den här kunskapen kan hjälpa dig att identifiera flaskhalsar för resurser.
* Identifiera processor och minne användning av behållargrupper och behållare i Azure Container Instances.  
* Identifiera där behållaren finns i en domänkontrollant eller en pod. Med hjälp av den här kunskapen kan du visa kontrollantens eller pod's övergripande prestanda.
* Granska arbetsbelastningar som körs på värden som inte är relaterade till de processer som standard som har stöd för poden Resursanvändning.
* Förstå beteendet för klustret under genomsnittliga och högsta belastning. Den här kunskapen kan hjälpa dig att identifiera kapacitetsbehov och avgöra den maximala belastningen kan klustret hantera. 

Du kan också konfigurera aviseringar för att proaktivt meddela dig eller registrera den när överskrider din tröskelvärden för användning av processor och minne på noderna eller behållare.  

## <a name="how-do-i-access-this-feature"></a>Hur kommer jag åt den här funktionen?
Du kan komma åt Azure Monitor för behållare två sätt, från Azure Monitor eller direkt från det valda AKS-klustret. Från Azure Monitor som du har distribuerat en global översikt över alla behållare, som övervakas och som inte, så att du kan söka och filtrera i dina prenumerationer och resursgrupper, och visa sedan detaljnivåerna i Azure Monitor för behållare från det valda behållaren.  I annat fall kan du helt enkelt hittar funktionen direkt från en vald AKS-behållare från sidan AKS.  

![Översikt över metoder för att komma åt Azure Monitor för behållare](./media/container-insights-overview/azmon-containers-views-1812.png)

Om du vill övervaka och hantera Docker och Windows behållare-värdar att visa konfiguration, granskning och användningen av resurser finns i den [lösning för övervakning av behållare](../../azure-monitor/insights/containers.md).

## <a name="next-steps"></a>Nästa steg
Om du vill börja övervaka ditt AKS-kluster, granska [hur att publicera Azure övervakar för behållare](container-insights-onboard.md) att förstå de krav och tillgängliga metoder för att aktivera övervakning.  
