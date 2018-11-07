---
title: Så här inaktiverar du övervaka med Azure Monitor för virtuella datorer (förhandsversion) | Microsoft Docs
description: Den här artikeln beskrivs hur du kan sluta övervaka dina virtuella datorer med Azure Monitor för virtuella datorer.
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
ms.date: 11/05/2018
ms.author: magoedte
ms.openlocfilehash: c8eb191daaf48793e4a412fdfa7d7199673a8539
ms.sourcegitcommit: 1b186301dacfe6ad4aa028cfcd2975f35566d756
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2018
ms.locfileid: "51220051"
---
# <a name="how-to-disable-monitoring-of-your-virtual-machines-with-azure-monitor-for-vms-preview"></a>Så här inaktiverar du övervaka dina virtuella datorer med Azure Monitor för virtuella datorer (förhandsversion)

Om du väljer du inte längre vill övervaka dem med Azure Monitor för virtuella datorer när du aktiverar övervakning av dina virtuella datorer, kan du inaktivera övervakning. Den här artikeln visar hur du gör detta för en eller flera virtuella datorer.  

För närvarande stöder inte Azure Monitor för virtuella datorer selektivt inaktivering av övervakning av dina virtuella datorer. Om Log Analytics-arbetsytan är konfigurerad för att stödja den här lösningen och andra lösningar, såväl som att samla in andra övervakningsdata, är det viktigt att du förstår påverkan och metoder som beskrivs nedan innan du fortsätter.

Azure Monitor för virtuella datorer är beroende av att leverera sin erfarenhet av följande komponenter:

* En Log Analytics-arbetsyta som lagrar övervakningsdata som samlas in från virtuella datorer och andra källor.
* Insamling av prestandaräknare som ställts in på arbetsytan som uppdaterar övervakningskonfigurationen på alla virtuella datorer är anslutna till arbetsytan.
* Två övervakningslösningar som ställts in på arbetsytan - **InfrastructureInsights** och **ServiceMap**, som uppdatering övervakningskonfigurationen på alla virtuella datorer är anslutna till arbetsytan.
* Två Azure VM-tillägg, den **MicrosoftMonitoringAgent** och **DepenendencyAgent**, som samlar in och skickar data till arbetsytan.

Tänk på följande när du förbereder att inaktivera övervakning av dina virtuella datorer med Azure Monitor för virtuella datorer:

* Om du utvärderar med en enda virtuell dator och du har accepterat förvalda standard Log Analytics-arbetsytan, kan du inaktivera övervakning genom att avinstallera beroendeagenten från den virtuella datorn och kopplar från Log Analytics-agenten från den här arbetsytan. Den här metoden är lämplig om du avser att använda den virtuella datorn i andra syften och bestämma senare att återansluta till en annan arbetsyta.
* Om du använder Log Analytics-arbetsytan för att stödja andra övervaknings-lösningar och insamling av data från andra källor, kan du ta bort Azure Monitor för virtuella datorer lösningskomponenter från arbetsytan utan avbrott eller påverkan på din arbetsyta.  

>[!NOTE]
> När du tar bort komponenter från din arbetsyta som du kan fortsätta att visa hälsotillståndet från dina virtuella datorer i Azure; mer specifikt kartan data om prestanda och när du navigerar till någon av vyerna i portalen. Data kommer så småningom inte att visas i vyn prestanda och kartan efter en stund; men hälsovyn fortsätter att visa hälsostatus för dina virtuella datorer. Den **Prova nu** alternativet blir tillgängliga från den valda Azure-VM så att du kan återaktivera övervakning i framtiden.  

## <a name="complete-removal-of-azure-monitor-for-vms"></a>Slutföra borttagningen av Azure Monitor för virtuella datorer

Följande steg beskriver hur du tar bort Azure Monitor för virtuella datorer om du fortfarande behöver Log Analytics-arbetsytan. Du kommer att ta bort den **InfastructureInsights** och **ServiceMap** lösningar från arbetsytan.  

>[!NOTE]
>Om du använde Tjänstkartan övervakningslösning innan du aktiverar Azure Monitor för virtuella datorer och du fortfarande använder den, ta inte bort lösningen som beskrivs i steg 6 nedan.  
>

1. Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).
2. Klicka på **Alla tjänster** på Azure Portal. I listan över resurser skriver du Log Analytics. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Log Analytics**.
3. I listan med Log Analytics-arbetsytor, Välj den arbetsyta som du har valt när onboarding Azure Monitor för virtuella datorer.
4. I den vänstra rutan, Välj **lösningar**.  
5. Välj i listan över lösningar, **InfrastructureInsights (namn på arbetsyta)**, och klicka sedan på den **översikt** för lösningen och klicka på **ta bort**.  När du uppmanas att bekräfta, klickar du på **Ja**.  
6. I listan med lösningar, Välj **ServiceMap (namn på arbetsyta)**, och klicka sedan på den **översikt** för lösningen och klicka på **ta bort**.  När du uppmanas att bekräfta, klickar du på **Ja**.  

Om innan onboarding Azure Monitor för virtuella datorer, inte var [insamling av prestandaräknare aktiverat](monitoring-vminsights-onboard.md?toc=/azure/azure-monitor/toc.json#performance-counters-enabled) för Windows eller Linux-baserade virtuella datorer i din arbetsyta, måste du inaktivera dessa regler genom att följa stegen som beskrivs [här](../log-analytics/log-analytics-data-sources-performance-counters.md?toc=/azure/azure-monitor/toc.json#configuring-performance-counters) för Windows och Linux.

## <a name="disable-monitoring-for-an-azure-vm-and-retain-workspace"></a>Inaktivera övervakning för en Azure-dator och behålla arbetsyta  

Följande steg beskriver hur du inaktiverar övervakning för en virtuell dator som har aktiverats för att utvärdera Azure Monitor för virtuella datorer, men Log Analytics-arbetsyta krävs fortfarande till stöd för övervakning från andra källor. Om det är en Azure-dator kan ska du ta bort beroendeagenten VM-tillägget och Log Analytics-agenten VM-tillägg för Windows och Linux direkt från den virtuella datorn. 

>[!NOTE]
>Om den virtuella datorn hanteras av Azure Automation för att samordna processer, hantera konfiguration, hantera uppdateringar eller hanteras av Azure Security Center för hantering och hotidentifiering, den Log Analytics-agenten inte ska tas bort. Annars kan förhindrar du dessa tjänster och lösningar för proaktiv hantering av den virtuella datorn. 

1. Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com). 
2. I Azure-portalen väljer du **virtuella datorer**. 
3. Välj en virtuell dator i listan. 
4. I den vänstra rutan, Välj **tillägg** på den **tillägg** väljer **DependencyAgent**.
5. På sidan tillägget egenskaper **avinstallera**.
6. På den **tillägg** väljer **MicrosoftMonitoringAgent** och klickar på egenskapssidan tillägget **avinstallera**.  
