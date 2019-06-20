---
title: Inaktivera övervakning i Azure Monitor för virtuella datorer (förhandsversion) | Microsoft Docs
description: Den här artikeln beskriver hur du stoppar övervakningen av dina virtuella datorer i Azure Monitor för virtuella datorer.
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
ms.date: 11/05/2018
ms.author: magoedte
ms.openlocfilehash: eb667486a6e3279cb78fefe02723f14d9f7c9b4f
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/17/2019
ms.locfileid: "67155693"
---
# <a name="disable-monitoring-of-your-vms-in-azure-monitor-for-vms-preview"></a>Inaktivera övervakning av dina virtuella datorer i Azure Monitor för virtuella datorer (förhandsversion)

När du aktiverar övervakning av dina virtuella datorer (VM), kan du senare välja att inaktivera övervakning i Azure Monitor för virtuella datorer. Den här artikeln visar hur du inaktiverar övervakning för en eller flera virtuella datorer.  

Azure Monitor för virtuella datorer stöd inte för närvarande för selektiv inaktivering av VM-övervakning. Log Analytics-arbetsytan stöder Azure Monitor för virtuella datorer och andra lösningar. Det kan också samla in andra övervakningsdata. Om Log Analytics-arbetsytan ger de här tjänsterna, måste du förstå effekten och metoder för att inaktivera övervakning innan du börjar.

Azure Monitor för virtuella datorer är beroende av att leverera sin erfarenhet av följande komponenter:

* En Log Analytics-arbetsyta, som lagrar övervakningsdata från virtuella datorer och andra källor.
* En samling med prestandaräknare som ställts in på arbetsytan. Samlingen uppdaterar konfigurationen av övervakningen på alla virtuella datorer är anslutna till arbetsytan.
* `InfrastructureInsights` och `ServiceMap`, som övervakar lösningar som ställts in på arbetsytan. Dessa lösningar uppdatera konfigurationen av övervakningen på alla virtuella datorer är anslutna till arbetsytan.
* `MicrosoftMonitoringAgent` och `DependencyAgent`, som är Azure VM-tillägg. De här tilläggen samla in och skicka data till arbetsytan.

När du förbereder att inaktivera övervakning av dina virtuella datorer ha detta i åtanke:

* Om du med en enda virtuell dator och använda Log Analytics-arbetsytan förvalda standard, kan du inaktivera övervakning genom att avinstallera beroendeagenten från den virtuella datorn och kopplar från Log Analytics-agenten från den här arbetsytan. Den här metoden är lämplig om du planerar att använda den virtuella datorn för andra ändamål och bestämma senare att återansluta till en annan arbetsyta.
* Om du har valt en redan befintliga Log Analytics-arbetsyta som har stöd för andra lösningar för övervakning och insamling av data från andra källor kan du ta bort komponenter från arbetsytan utan att avbryta eller påverka din arbetsyta.  

>[!NOTE]
> När du tar bort komponenter från din arbetsyta kan du fortsätter att se hälsotillståndet från dina virtuella datorer i Azure; mer specifikt kan du se prestanda och mappa data när du går till någon av vyerna i portalen. Data kommer så småningom inte att visas i den **prestanda** och **kartan** vyer. Men **hälsotillstånd** vyn kommer att fortsätta att visa hälsostatus för dina virtuella datorer. Den **Prova nu** alternativet blir tillgängliga från den valda virtuella Azure-datorn så du kan aktivera övervakning i framtiden.  

## <a name="remove-azure-monitor-for-vms-completely"></a>Ta bort Azure Monitor för virtuella datorer

Om du fortfarande behöver Log Analytics-arbetsytan, Följ dessa steg för att helt ta bort Azure Monitor för virtuella datorer. Tar du bort den `InfrastructureInsights` och `ServiceMap` lösningar från arbetsytan.  

>[!NOTE]
>Om du använde Tjänstkartan övervakningslösning innan du har aktiverat Azure Monitor för virtuella datorer och du fortfarande använder den kan inte ta bort lösningen som beskrivs i det sista steget i följande procedur.  
>

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **Alla tjänster** i Azure-portalen. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan över förslag baserat på dina indata. Välj **Log Analytics**.
3. I listan med Log Analytics-arbetsytor, Välj arbetsytan du valde när du har aktiverat Azure Monitor för virtuella datorer.
4. Till vänster, Välj **lösningar**.  
5. Välj i listan över lösningar, **InfrastructureInsights (namn på arbetsyta)** . På den **översikt** för lösningen väljer **ta bort**. När du uppmanas att bekräfta, Välj **Ja**.  
6. Välj i listan över lösningar, **ServiceMap (namn på arbetsyta)** . På den **översikt** för lösningen väljer **ta bort**. När du uppmanas att bekräfta, Välj **Ja**.  

Innan du har aktiverat Azure Monitor för virtuella datorer, om du inte gjort [samla in prestandaräknare](vminsights-enable-overview.md#performance-counters-enabled) för Windows- eller Linux-baserade virtuella datorer i din arbetsyta [inaktivera dessa regler](../platform/data-sources-performance-counters.md#configuring-performance-counters) för Windows och Linux.

## <a name="disable-monitoring-and-keep-the-workspace"></a>Inaktivera övervakning och hålla arbetsytan  

Om Log Analytics-arbetsytan fortfarande behöver stöd för övervakning från andra källor, följa dessa steg för att inaktivera övervakning på den virtuella datorn som du använde för att utvärdera Azure Monitor för virtuella datorer. För virtuella Azure-datorer tar du bort beroendeagenten VM-tillägget och Log Analytics-agenten VM-tillägg för Windows eller Linux direkt från den virtuella datorn. 

>[!NOTE]
>Inte ta bort Log Analytics-agenten om: 
>
> * Azure Automation hanterar den virtuella datorn att samordna processer eller hantera konfigurationen eller uppdateringar. 
> * Azure Security Center hanterar den virtuella datorn för säkerhet och hotidentifiering. 
>
> Om du tar bort Log Analytics-agenten, förhindras dessa tjänster och lösningar från proaktiv hantering av den virtuella datorn. 

1. Logga in på [Azure Portal](https://portal.azure.com). 
2. I Azure-portalen väljer du **virtuella datorer**. 
3. Välj en virtuell dator i listan. 
4. Till vänster, Välj **tillägg**. På den **tillägg** väljer **DependencyAgent**.
5. På sidan tillägget egenskaper väljer **avinstallera**.
6. På den **tillägg** väljer **MicrosoftMonitoringAgent**. På sidan tillägget egenskaper väljer **avinstallera**.  
