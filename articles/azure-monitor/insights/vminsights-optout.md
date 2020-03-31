---
title: Inaktivera övervakning i Azure Monitor för virtuella datorer
description: I den här artikeln beskrivs hur du slutar övervaka dina virtuella datorer i Azure Monitor för virtuella datorer.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 80473aa494b8fbcea5e43870b7717cd3472dd7d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480529"
---
# <a name="disable-monitoring-of-your-vms-in-azure-monitor-for-vms"></a>Inaktivera övervakning av dina virtuella datorer i Azure Monitor för virtuella datorer

När du har aktiverat övervakning av virtuella datorer kan du senare välja att inaktivera övervakning i Azure Monitor för virtuella datorer. Den här artikeln visar hur du inaktiverar övervakning för en eller flera virtuella datorer.  

Azure Monitor för virtuella datorer stöder för närvarande inte selektiv inaktivering av vm-övervakning. Din Log Analytics-arbetsyta kan ha stöd för Azure Monitor för virtuella datorer och andra lösningar. Den kan också samla in andra övervakningsdata. Om din Log Analytics-arbetsyta tillhandahåller dessa tjänster måste du förstå effekten och metoderna för att inaktivera övervakning innan du börjar.

Azure Monitor för virtuella datorer är beroende av följande komponenter för att leverera sin upplevelse:

* En Log Analytics-arbetsyta som lagrar övervakningsdata från virtuella datorer och andra källor.
* En samling prestandaräknare som konfigurerats på arbetsytan. Samlingen uppdaterar övervakningskonfigurationen på alla virtuella datorer som är anslutna till arbetsytan.
* `VMInsights`, som är en övervakningslösning som konfigurerats på arbetsytan. Den här lösningen uppdaterar övervakningskonfigurationen på alla virtuella datorer som är anslutna till arbetsytan.
* `MicrosoftMonitoringAgent`och `DependencyAgent`, som är Azure VM-tillägg. Dessa tillägg samlar in och skickar data till arbetsytan.

När du förbereder dig för att inaktivera övervakning av dina virtuella datorer bör du tänka på följande:

* Om du utvärderade med en enda virtuell dator och använde den förvalda standardarbetsytan Log Analytics kan du inaktivera övervakning genom att avinstallera beroendeagenten från den virtuella datorn och koppla från Log Analytics-agenten från den här arbetsytan. Den här metoden är lämplig om du tänker använda den virtuella datorn för andra ändamål och senare bestämmer dig för att återansluta den till en annan arbetsyta.
* Om du har valt en befintlig Log Analytics-arbetsyta som stöder andra övervakningslösningar och datainsamling från andra källor kan du ta bort lösningskomponenter från arbetsytan utan att avbryta eller påverka arbetsytan.  

>[!NOTE]
> När du har tagit bort lösningskomponenterna från arbetsytan kan du fortsätta att se prestanda och mappa data för dina virtuella Azure-datorer. Data slutar så småningom att visas i vyerna **Prestanda** **och Karta.** Alternativet **Aktivera** kommer att vara tillgängligt från den valda Azure VM så att du kan återaktivera övervakning i framtiden.  

## <a name="remove-azure-monitor-for-vms-completely"></a>Ta bort Azure Monitor för virtuella datorer helt

Om du fortfarande behöver log analytics-arbetsytan följer du dessa steg för att helt ta bort Azure Monitor för virtuella datorer. Du tar bort `VMInsights` lösningen från arbetsytan.  

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **Alla tjänster** i Azure-portalen. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtrerar listan förslag baserat på dina indata. Välj **Log Analytics**.
3. I listan över Log Analytics-arbetsytor väljer du den arbetsyta du valde när du aktiverade Azure Monitor för virtuella datorer.
4. Till vänster väljer du **Lösningar**.  
5. Välj **VMInsights(arbetsytenamn)** i listan över lösningar . På sidan **Översikt** för lösningen väljer du **Ta bort**. När du uppmanas att bekräfta väljer du **Ja**.

## <a name="disable-monitoring-and-keep-the-workspace"></a>Inaktivera övervakning och behåll arbetsytan  

Om din Log Analytics-arbetsyta fortfarande behöver stöd för övervakning från andra källor följer du dessa steg för att inaktivera övervakning på den virtuella datorn som du använde för att utvärdera Azure Monitor för virtuella datorer. För virtuella Azure-datorer tar du bort tillägget beroendeagent VM och tillägget Log Analytics-agent vm för Windows eller Linux direkt från den virtuella datorn. 

>[!NOTE]
>Ta inte bort Log Analytics-agenten om: 
>
> * Azure Automation hanterar den virtuella datorn för att dirigera processer eller hantera konfiguration eller uppdateringar. 
> * Azure Security Center hanterar den virtuella datorn för identifiering av säkerhet och hot. 
>
> Om du tar bort Log Analytics-agenten förhindrar du att dessa tjänster och lösningar proaktivt hanterar din virtuella dator. 

1. Logga in på [Azure-portalen](https://portal.azure.com). 
2. I Azure-portalen väljer du **Virtuella datorer**. 
3. Välj en virtuell dator i listan. 
4. Till vänster väljer du **Tillägg**. På sidan **Tillägg** väljer du **DependencyAgent**.
5. På sidan Tilläggsegenskaper väljer du **Avinstallera**.
6. På sidan **Tillägg** väljer du **MicrosoftMonitoringAgent**. På sidan Tilläggsegenskaper väljer du **Avinstallera**.  
