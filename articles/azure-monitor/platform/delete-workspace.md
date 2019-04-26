---
title: Ta bort en Azure Log Analytics-arbetsyta | Microsoft Docs
description: Lär dig hur du tar bort logganalys-arbetsytan om du har skapat en i en personlig prenumeration eller omstrukturera din arbetsyta-modell.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: magoedte
ms.openlocfilehash: a6542838acba3143123dc90d96746179a2b4469b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60236100"
---
# <a name="delete-an-azure-log-analytics-workspace-with-the-azure-portal"></a>Ta bort en Azure Log Analytics-arbetsyta med Azure portal
Den här artikeln visar hur du använder Azure-portalen för att ta bort en Log Analytics-arbetsyta som du kan inte längre behöver. 

## <a name="to-delete-a-workspace"></a>Så här tar du bort en arbetsyta 
När du tar bort en Log Analytics-arbetsyta raderas alla data som är relaterade till arbetsytan från tjänsten inom 30 dagar.  Vill du vara försiktig när du tar bort en arbetsyta, eftersom det kan vara viktiga data och konfigurationer som kan påverka din tjänståtgärder. Överväg andra Azure-tjänster och källor som lagrar data i Log Analytics, till exempel:

* Application Insights
* Azure Security Center
* Azure Automation
* Agenter som körs på Windows och Linux-datorer
* Agenter som körs på Windows och Linux datorer i din miljö
* System Center Operations Manager
* Hanteringslösningar 

Alla agenter och System Center Operations Manager-hanteringsgrupper som konfigurerats för att rapportera till arbetsytan kan du fortsätta att i ett överblivna tillstånd.  Inventera vilka agenter, lösningar, och andra Azure-tjänster som är integrerade med arbetsytan innan du fortsätter.   
 
Om du är administratör och det finns flera användare som är kopplade till arbetsytan bryts kopplingen mellan användare och arbetsytan. Om användarna är associerade med andra arbetsytor kan de fortsätta använda Log Analytics med de andra arbetsytorna. Men om de inte är associerade med andra arbetsytor behöver sedan de skapa en arbetsyta om du vill använda Log Analytics. 

1. Logga in på [Azure-portalen](https://portal.azure.com). 
2. I Azure Portal klickar du på knappen **Fler tjänster** längst upp till vänster. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Log Analytics-arbetsytor**.
3. Välj en arbetsyta i fönstret Log Analytics-prenumerationer och klicka sedan på **ta bort** högst upp på den mellersta rutan.<br><br> ![Ta bort alternativet från fönstret med arbetsytans egenskaper](media/delete-workspace/log-analytics-delete-workspace.png)<br>  
4. När fönstret bekräftelse meddelande visas där du uppmanas att bekräfta borttagningen i arbetsytan, klickar du på **Ja**.<br><br> ![Bekräfta borttagning av arbetsyta](media/delete-workspace/log-analytics-delete-workspace-confirm.png)

