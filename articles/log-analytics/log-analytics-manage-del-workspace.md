---
title: Ta bort en Azure logganalys-arbetsytan | Microsoft Docs
description: Lär dig hur du tar bort logganalys-arbetsytan om du har skapat en i en personlig prenumeration eller omstrukturera din arbetsyta modell.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: magoedte
ms.component: na
ms.openlocfilehash: 54f2af60751ed0d9c64e71efad6fa9aa3ef06589
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37129123"
---
# <a name="delete-an-azure-log-analytics-workspace-with-the-azure-portal"></a>Ta bort en Azure logganalys-arbetsytan med Azure-portalen
Den här artikeln visar hur du använder Azure-portalen för att ta bort logganalys-arbetsytan som du kan inte längre behöver. 

## <a name="to-delete-a-workspace"></a>Så här tar du bort en arbetsyta 
När du tar bort logganalys-arbetsytan raderas alla data som rör din arbetsyta från tjänsten inom 30 dagar.  Vill du vara försiktig när du tar bort en arbetsyta eftersom det kan vara viktiga data och konfigurationer som kan påverka din tjänståtgärder. Överväg andra Azure-tjänster och källor som lagrar data i logganalys, exempelvis:

* Application Insights
* Azure Security Center
* Azure Automation
* Agenter som körs på Windows- och Linux virtuella datorer
* Agenter som körs på Windows- och Linux datorer i din miljö
* System Center Operations Manager
* Hanteringslösningar 

Alla agenter och System Center Operations Manager-hanteringsgrupper som är konfigurerad för att rapportera till arbetsytan kan du fortsätta att överblivna tillståndet.  Inventera vilka agenter, lösningar och andra Azure-tjänster som är integrerade med arbetsytan innan du fortsätter.   
 
Om du är administratör och det finns flera användare som är kopplade till arbetsytan bryts kopplingen mellan användare och arbetsytan. Om användarna är associerade med andra arbetsytor kan de fortsätta använda Log Analytics med de andra arbetsytorna. Men om de inte är associerade med andra arbetsytor behöver sedan de skapa en arbetsyta för att använda Log Analytics. 

1. Logga in på [Azure-portalen](http://portal.azure.com). 
2. I Azure Portal klickar du på knappen **Fler tjänster** längst upp till vänster. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Log Analytics**.
3. Välj en arbetsyta i fönstret logganalys prenumerationer och klicka sedan på **ta bort** överst i den mellersta rutan.<br><br> ![Ta bort alternativet från arbetsytan egenskapsrutan](media/log-analytics-manage-del-workspace/log-analytics-delete-workspace.png)<br>  
4. När fönstret bekräftelse meddelande visas där du ombeds bekräfta borttagning av arbetsytan klickar du på **Ja**.<br><br> ![Bekräfta borttagning av arbetsytan](media/log-analytics-manage-del-workspace/log-analytics-delete-workspace-confirm.png)

