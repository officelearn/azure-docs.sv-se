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
ms.openlocfilehash: 57bfa47d60ffd8aa7c4240ab77f788773e426bd8
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2018
ms.locfileid: "51715869"
---
# <a name="how-to-upgrade-the-azure-monitor-for-containers-preview-agent"></a>Så här uppgraderar du i Azure Monitor för behållare (förhandsgranskning)-agenten
Azure Monitor för behållare använder en behållare version av Log Analytics-agenten för Linux. När en ny version av agenten släpps, uppgraderas agenten inte automatiskt på dina hanterade Kubernetes-kluster som finns på Azure Kubernetes Service (AKS).

Den här artikeln beskriver processen för att uppgradera agenten.

## <a name="upgrading-agent-on-monitored-kubernetes-cluster"></a>Uppgraderar agent på övervakade Kubernetes-kluster
Processen för att uppgradera agenten består av två okomplicerat steg. Det första steget är att inaktivera övervakning med Azure Monitor för behållare med hjälp av Azure CLI.  Följ stegen som beskrivs i den [inaktivera övervakning](container-insights-optout.md?toc=%2fazure%2fmonitoring%2ftoc.json#azure-cli) artikeln. Med Azure CLI kan vi ta bort agenten från noder i klustret utan att påverka lösningen och motsvarande data som lagras på arbetsytan. 

>[!NOTE]
>När du utför den här aktiviteten för underhåll, noderna i klustret inte vidarebefordrar insamlade data och prestandavyer kommer inte att visa data mellan tid du tar bort agenten och installera den nya versionen. 
>

Om du vill installera den nya versionen av agenten, följer du stegen som beskrivs i den [integrera övervakning](container-insights-onboard.md?toc=%2fazure%2fmonitoring%2ftoc.json#enable-monitoring-using-azure-cli) artikel med Azure CLI, för att slutföra den här processen.  

När du har återaktiveras övervakning, kan det ta ungefär 15 minuter innan du kan visa uppdaterade hälsomått för klustret. 

## <a name="next-steps"></a>Nästa steg
Om det uppstår problem när du uppgraderar agenten kan du granska den [felsökningsguide](container-insights-troubleshoot.md) för support.