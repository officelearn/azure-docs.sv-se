---
title: Skapa en Log Analytics-arbetsyta i Azure Portal | Microsoft Docs
description: Lär dig hur du skapar en Log Analytics-arbetsyta om du vill aktivera hantering av lösningar och datainsamling från ditt moln och lokala miljöer i Azure-portalen.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2019
ms.openlocfilehash: 0ab8019a8537d4080afdb457459f09cd156ff4ed
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75365537"
---
# <a name="create-a-log-analytics-workspace-in-the-azure-portal"></a>Skapa en Log Analytics-arbetsyta i Azure portal
Använd menyn **Log Analytics arbets ytor** för att skapa en arbets yta för Log Analytics med hjälp av Azure Portal. En Log Analytics-arbetsyta är en unik miljö för Azure Monitor loggdata. Varje arbets yta har sin egen data lagrings plats och konfiguration, och data källor och lösningar har kon figurer ATS för att lagra data i en viss arbets yta. Du behöver en Log Analytics arbets yta om du vill samla in data från följande källor:

* Azure-resurser i din prenumeration
* Lokala datorer som övervakas av System Center Operations Manager
* Enhetssamlingar från System Center Configuration Manager 
* Diagnostik och log data från Azure storage

Andra källor, till exempel virtuella Azure-datorer och Windows eller Linux-datorer i din miljö finns i följande avsnitt:

*  [Samla in data från Azure-datorer](../learn/quick-collect-azurevm.md) 
*  [Samla in data från hybrid Linux-dator](../learn/quick-collect-linux-computer.md)
*  [Samla in data från hybrid Windows-dator](quick-collect-windows-computer.md)

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="sign-in-to-azure-portal"></a>Logga in på Azure Portal
Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Skapa en arbetsyta
1. Klicka på **Alla tjänster** i Azure-portalen. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Log Analytics arbets ytor**.

    ![Azure portal](media/quick-create-workspace/azure-portal-01.png)
  
2. Klicka på **Lägg till**och välj sedan alternativ för följande objekt:

   * Ange ett namn för den nya **Log Analytics-arbetsytan**, som *DefaultLAWorkspace*. Det här namnet måste vara globalt unikt för alla Azure Monitor prenumerationer.
   * Välj en **prenumeration** att länka till genom att välja från den listrutan om standardvalet inte är lämpligt.
   * För **resursgrupp**, välja att använda en befintlig resurs grupp redan installationen eller skapa en ny.  
   * Välj ett tillgängligt **plats**.  Mer information finns i vilka [regioner Log Analytics är tillgängliga i](https://azure.microsoft.com/regions/services/) och söka efter Azure Monitor från fältet **Sök efter ett produkt** .  
   * Om du skapar en arbetsyta i en ny prenumeration som skapats efter 2 april 2018 används prisplanen *Per GB* automatiskt och alternativet för att välja en prisnivå är inte tillgängligt.  Om du skapar en arbetsyta för en befintlig prenumeration som skapats före 2 April eller en prenumeration som är kopplad till en befintlig Enterprise Agreement (EA)-registrering, väljer du önskad prisnivå.  Mer information om de olika nivåerna finns i [prisinformation om Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).

        ![Skapa Log Analytics resurs bladet](media/quick-create-workspace/create-loganalytics-workspace-02.png)  

3. När du har angett den nödvändiga informationen i fönsterrutan **Log Analytics-arbetsyta** klickar du på **OK**.  

När informationen har verifierats och arbetsytan skapas, kan du spåra förloppet under **Meddelanden** på menyn. 

## <a name="next-steps"></a>Nästa steg
Nu när du har en arbetsyta som är tillgängliga kan du konfigurera insamling av övervakning av telemetri, köra loggsökningar för att analysera dessa data och lägga till en hanteringslösning för att ge ytterligare data och analytisk insikt. 

* Om du vill aktivera insamling av data från Azure-resurser med Azure Diagnostics eller Azure storage, se [samla in Azure-tjänsteloggar och mått för användning i Log Analytics](../platform/collect-azure-metrics-logs.md).  
* [Lägg till System Center Operations Manager som en datakälla](../platform/om-agents.md) att samla in data från agenter som rapporterar Operations Manager-hanteringsgrupp och lagra den i Log Analytics-arbetsytan. 
* Ansluta [Configuration Manager](../platform/collect-sccm.md) att importera datorer som är medlemmar i samlingar i hierarkin.  
* Granska de tillgängliga [övervaknings lösningarna](../insights/solutions.md) och Lägg till eller ta bort en lösning från din arbets yta.
