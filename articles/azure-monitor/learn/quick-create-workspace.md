---
title: Skapa en log analytics-arbetsyta i Azure Portal | Microsoft-dokument
description: Lär dig hur du skapar en Log Analytics-arbetsyta för att aktivera hanteringslösningar och datainsamling från dina moln- och lokala miljöer i Azure-portalen.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2019
ms.openlocfilehash: 3c2e9d5634916c3713b7e3380c0496611d8f60a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77656287"
---
# <a name="create-a-log-analytics-workspace-in-the-azure-portal"></a>Skapa en log Analytics-arbetsyta i Azure-portalen
Använd menyn **Logganalysarbetsytor** för att skapa en Log Analytics-arbetsyta med Hjälp av Azure-portalen. En Log Analytics-arbetsyta är en unik miljö för Azure Monitor-loggdata. Varje arbetsyta har en egen datadatabas och konfiguration, och datakällor och lösningar är konfigurerade för att lagra sina data på en viss arbetsyta. Du behöver en Log Analytics-arbetsyta om du tänker samla in data från följande källor:

* Azure-resurser i din prenumeration
* Lokala datorer som övervakas av System Center Operations Manager
* Enhetssamlingar från Configuration Manager 
* Diagnostik eller loggdata från Azure-lagring

Andra källor, till exempel virtuella Azure-datorer och virtuella Windows- eller Linux-datorer i din miljö, finns i följande avsnitt:

*  [Samla in data från virtuella Azure-datorer](../learn/quick-collect-azurevm.md) 
*  [Samla in data från hybrid-Linux-dator](../learn/quick-collect-linux-computer.md)
*  [Samla in data från hybrid windows-dator](quick-collect-windows-computer.md)

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="sign-in-to-azure-portal"></a>Logga in på Azure-portalen
Logga in på Azure-portalen på [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Skapa en arbetsyta
1. Klicka på **Alla tjänster** i Azure-portalen. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Log Analytics-arbetsytor**.

    ![Azure Portal](media/quick-create-workspace/azure-portal-01.png)
  
2. Klicka på **Lägg till**och välj sedan alternativ för följande objekt:

   * Ange ett namn för den nya **Log Analytics-arbetsytan**, som *DefaultLAWorkspace*. Det här namnet måste vara globalt unikt för alla Azure Monitor-prenumerationer.
   * Välj en **prenumeration** att länka till genom att välja från den listrutan om standardvalet inte är lämpligt.
   * För **Resursgrupp**väljer du att använda en befintlig resursgrupp som redan har konfigurerats eller skapar en ny.  
   * Välj en tillgänglig **plats**.  Mer information finns [i](https://azure.microsoft.com/regions/services/) vilka regioner Log Analytics är tillgängligt i och söker efter Azure Monitor från fältet Sök efter en **produkt.**  
   * Om du skapar en arbetsyta i en ny prenumeration som skapats efter 2 april 2018 används prisplanen *Per GB* automatiskt och alternativet för att välja en prisnivå är inte tillgängligt.  Om du skapar en arbetsyta för en befintlig prenumeration som skapats före den 2 april, eller till prenumeration som var kopplad till en befintlig EA-registrering (Enterprise Agreement) väljer du önskad prisnivå.  Mer information om de aktuella nivåerna finns i [Information om logganalyspriser](https://azure.microsoft.com/pricing/details/log-analytics/).

        ![Skapa resursblad för Logganalys](media/quick-create-workspace/create-loganalytics-workspace-02.png)  

3. När du har angett den nödvändiga informationen i fönsterrutan **Log Analytics-arbetsyta** klickar du på **OK**.  

När informationen har verifierats och arbetsytan skapas, kan du spåra förloppet under **Meddelanden** på menyn. 

## <a name="next-steps"></a>Nästa steg
Nu när du har en arbetsyta tillgänglig kan du konfigurera insamling av övervakningstelemetri, köra loggsökningar för att analysera dessa data och lägga till en hanteringslösning för att tillhandahålla ytterligare data och analytiska insikter. 

* Information om hur du aktiverar datainsamling från Azure-resurser med Azure Diagnostics eller Azure-lagring finns [i Samla in Azure-tjänstloggar och mått för användning i Logganalys](../platform/collect-azure-metrics-logs.md).  
* [Lägg till System Center Operations Manager som en datakälla](../platform/om-agents.md) för att samla in data från agenter som rapporterar din Hanteringsgrupp i Operations Manager och lagra den på logganalysarbetsytan. 
* Anslut [Configuration Manager](../platform/collect-sccm.md) för att importera datorer som är medlemmar i samlingar i hierarkin.  
* Granska de [övervakningslösningar](../insights/solutions.md) som finns tillgängliga och hur du lägger till eller tar bort en lösning från arbetsytan.
