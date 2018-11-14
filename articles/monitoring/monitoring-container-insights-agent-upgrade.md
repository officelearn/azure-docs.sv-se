---
title: Uppgradera Azure Monitor för behållare (förhandsgranskning)-agenten | Microsoft Docs
description: Den här artikeln beskrivs hur du uppgraderar Log Analytics-agenten som används av Azure Monitor för behållare.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/13/2018
ms.author: magoedte
ms.openlocfilehash: fb45002d284bc28dfb7093f69cfac7aae0681e8d
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2018
ms.locfileid: "51629193"
---
# <a name="how-to-upgrade-the-azure-monitor-for-containers-preview-agent"></a>Så här uppgraderar du i Azure Monitor för behållare (förhandsgranskning)-agenten
Azure Monitor för behållare använder en behållare version av Log Analytics-agenten för Linux. När en ny version av agenten släpps, uppgraderas agenten inte automatiskt på dina hanterade Kubernetes-kluster som finns på Azure Kubernetes Service (AKS).

Den här artikeln beskriver processen för att uppgradera agenten.

## <a name="upgrading-agent-on-monitored-kubernetes-cluster"></a>Uppgraderar agent på övervakade Kubernetes-kluster
Processen för att uppgradera agenten består av två okomplicerat steg. Det första steget är att inaktivera övervakning med Azure Monitor för behållare med hjälp av Azure CLI.  Följ stegen som beskrivs i den [inaktivera övervakning](monitoring-container-insights-optout.md?toc=%2fazure%2fmonitoring%2ftoc.json#azure-cli) artikeln. Med Azure CLI kan vi ta bort agenten från noder i klustret utan att påverka lösningen och motsvarande data som lagras på arbetsytan. 

>[!NOTE]
>När du utför den här aktiviteten för underhåll, noderna i klustret inte vidarebefordrar insamlade data och prestandavyer kommer inte att visa data mellan tid du tar bort agenten och installera den nya versionen. 
>

Om du vill installera den nya versionen av agenten, följer du stegen som beskrivs i den [integrera övervakning](monitoring-container-insights-onboard.md?toc=%2fazure%2fmonitoring%2ftoc.json#enable-monitoring-using-azure-cli) artikel med Azure CLI, för att slutföra den här processen.  

När du har återaktiveras övervakning, kan det ta ungefär 15 minuter innan du kan visa uppdaterade hälsomått för klustret. 

## <a name="next-steps"></a>Nästa steg
Om det uppstår problem när du uppgraderar agenten kan du granska den [felsökningsguide](monitoring-container-insights-troubleshoot.md) för support.