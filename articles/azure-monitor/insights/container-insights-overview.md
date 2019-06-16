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
ms.date: 04/25/2019
ms.author: magoedte
ms.openlocfilehash: d6321564672097fbf901d1d33afac9f606fcb63a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65521842"
---
# <a name="azure-monitor-for-containers-overview"></a>Azure Monitor för översikt över behållare

Azure Monitor för behållare är en funktion som utformats för att övervaka prestanda för arbetsbelastningar distribueras till Azure Container Instances eller hanterade Kubernetes-kluster som finns på Azure Kubernetes Service (AKS). Övervaka dina behållare är viktigt, särskilt när du kör ett produktionskluster i skala med flera program.

Azure Monitor för behållare ger dig insyn i prestanda genom att samla in minne och processor mått från domänkontrollanter, noder och behållare som är tillgängliga i Kubernetes via mått-API. Containerloggar samlas också.  När du aktiverar övervakning från Kubernetes-kluster, samlas mått och loggar automatiskt in för dig via en behållare version av Log Analytics-agenten för Linux. Mätvärdena skrivs till arkivet mätvärden och loggdata skrivs till arkivet loggar som är associerade med din [Log Analytics](../log-query/log-query-overview.md) arbetsyta. 

![Azure Monitor för behållare-arkitektur](./media/container-insights-overview/azmon-containers-architecture-01.png)
 
## <a name="what-does-azure-monitor-for-containers-provide"></a>Vad kostar Azure Monitor för behållare ger?

Azure Monitor för behållare levererar en omfattande övervakningsupplevelse med olika funktioner i Azure Monitor så att du kan förstå prestanda och hälsotillståndet för ett Kubernetes-kluster och behållararbetsbelastningar. Med Azure Monitor för behållare kan du:

* Identifiera AKS-behållare som körs på noden och deras genomsnittsanvändningen av processor och minne. Den här kunskapen kan hjälpa dig att identifiera flaskhalsar för resurser.
* Identifiera processor och minne användning av behållargrupper och behållare i Azure Container Instances.  
* Identifiera där behållaren finns i en domänkontrollant eller en pod. Med hjälp av den här kunskapen kan du visa kontrollantens eller pod's övergripande prestanda.
* Granska arbetsbelastningar som körs på värden som inte är relaterade till de processer som standard som har stöd för poden Resursanvändning.
* Förstå beteendet för klustret under genomsnittliga och högsta belastning. Den här kunskapen kan hjälpa dig att identifiera kapacitetsbehov och avgöra den maximala belastningen kan klustret hantera. 
* Konfigurera aviseringar för att proaktivt meddela dig eller registrera den när överskrider din tröskelvärden för användning av processor och minne på noderna eller behållare.  

## <a name="how-do-i-access-this-feature"></a>Hur kommer jag åt den här funktionen?
Du kan komma åt Azure Monitor för behållare två sätt, från Azure Monitor eller direkt från det valda AKS-klustret. Från Azure Monitor kan du ha en global översikt över alla behållarna som distribueras som övervakas och vilka som inte, så att du kan söka och filtrera i dina prenumerationer och resursgrupper, och visa sedan detaljnivåerna i Azure Monitor för behållare från den vald behållare.  I annat fall kan du komma åt funktionen direkt från en vald AKS-behållare från sidan AKS.  

![Översikt över metoder för att komma åt Azure Monitor för behållare](./media/container-insights-overview/azmon-containers-experience.png)

Om du vill övervaka och hantera Docker och Windows behållare-värdar att visa konfiguration, granskning och användningen av resurser finns i den [lösning för övervakning av behållare](../../azure-monitor/insights/containers.md).

## <a name="next-steps"></a>Nästa steg
Om du vill börja övervaka ditt AKS-kluster, granska [så här aktiverar du i Azure Monitor för behållare](container-insights-onboard.md) att förstå de krav och tillgängliga metoder för att aktivera övervakning.  
