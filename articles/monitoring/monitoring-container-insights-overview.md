---
title: Översikt över Azure Monitor för behållare (förhandsversion) | Microsoft Docs
description: Den här artikeln beskrivs Azure Monitor för behållare som övervakar AKS-behållare för lösningen och det värde som du får genom att övervaka hälsotillståndet för dina AKS-kluster och Container Instances i Azure.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2018
ms.author: magoedte
ms.openlocfilehash: 13fde8a4ec4ecf43b9997d5d4d181a232505626f
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2018
ms.locfileid: "50213006"
---
# <a name="azure-monitor-for-containers-preview-overview"></a>Azure Monitor för översikt över behållare (förhandsversion)

Azure Monitor för behållare är en funktion som utformats för att övervaka prestanda för arbetsbelastningar distribueras till hanterade Kubernetes-kluster som finns på Azure Kubernetes Service (AKS). Övervaka dina behållare är viktigt, särskilt när du kör ett produktionskluster i skala med flera program.

Azure Monitor för behållare ger dig insyn i prestanda genom att samla in minne och processor mått från domänkontrollanter, noder och behållare som är tillgängliga i Kubernetes via mått-API. Containerloggar samlas också.  När du aktiverar övervakning från Kubernetes-kluster kan dessa mått och loggar automatiskt som samlas in för dig via en behållare version av Log Analytics-agenten för Linux och lagras i din [Log Analytics](../log-analytics/log-analytics-overview.md) arbetsyta. 
 
## <a name="what-does-azure-monitor-for-containers-provide"></a>Vad kostar Azure Monitor för behållare ger?

Azure Monitor för behållare innehåller flera fördefinierade vyer som visar som förvaras behållararbetsbelastningar och vad påverkar prestanda hälsotillståndet för övervakade Kubernetes-klustret så att du kan:  

* Identifiera AKS-behållare som körs på noden och deras genomsnittsanvändningen av processor och minne. Den här kunskapen kan hjälpa dig att identifiera flaskhalsar för resurser.
* Identifiera där behållaren finns i en domänkontrollant eller en pod. Med hjälp av den här kunskapen kan du visa kontrollantens eller pod's övergripande prestanda. 
* Granska arbetsbelastningar som körs på värden som inte är relaterade till de processer som standard som har stöd för poden Resursanvändning.
* Förstå beteendet för klustret under genomsnittliga och högsta belastning. Den här kunskapen kan hjälpa dig att identifiera kapacitetsbehov och avgöra den maximala belastningen kan klustret hantera. 

## <a name="how-do-i-access-this-feature"></a>Hur kommer jag åt den här funktionen?
Du kan komma åt Azure Monitor för behållare två sätt, från Azure Monitor eller direkt från det valda AKS-klustret. Från Azure Monitor som du har distribuerat en global översikt över alla behållare, som övervakas och som inte, så att du kan söka och filtrera i dina prenumerationer och resursgrupper, och visa sedan detaljnivåerna i Azure Monitor för behållare från det valda behållaren.  I annat fall kan du helt enkelt hittar funktionen direkt från en vald AKS-behållare från sidan AKS.  

![Översikt över metoder för att komma åt Azure Monitor för behållare](./media/monitoring-container-insights-overview/azmon-containers-views.png)

Om du vill övervaka och hantera Docker och Windows behållare-värdar att visa konfiguration, granskning och användningen av resurser finns i den [lösning för övervakning av behållare](../log-analytics/log-analytics-containers.md).

## <a name="next-steps"></a>Nästa steg
Om du vill börja övervaka ditt AKS-kluster, granska [hur att publicera Azure övervakar för behållare](monitoring-container-insights-onboard.md) att förstå de krav och tillgängliga metoder för att aktivera övervakning.  