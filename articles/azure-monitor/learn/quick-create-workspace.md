---
title: Skapa en Log Analytics-arbetsyta i Azure Portal | Microsoft Docs
description: Lär dig hur du skapar en Log Analytics-arbetsyta om du vill aktivera hantering av lösningar och datainsamling från ditt moln och lokala miljöer i Azure-portalen.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2019
ms.openlocfilehash: 3c2e9d5634916c3713b7e3380c0496611d8f60a6
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77656287"
---
# <a name="create-a-log-analytics-workspace-in-the-azure-portal"></a>Skapa en Log Analytics-arbetsyta i Azure portal
Använd menyn **Log Analytics arbets ytor** för att skapa en arbets yta för Log Analytics med hjälp av Azure Portal. En Log Analytics-arbetsyta är en unik miljö för Azure Monitor loggdata. Varje arbets yta har sin egen data lagrings plats och konfiguration, och data källor och lösningar har kon figurer ATS för att lagra data i en viss arbets yta. Du behöver en Log Analytics arbets yta om du vill samla in data från följande källor:

* Azure-resurser i din prenumeration
* Lokala datorer som övervakas av System Center Operations Manager
* Enhets samlingar från Configuration Manager 
* Diagnostik och log data från Azure storage

Andra källor, till exempel virtuella Azure-datorer och Windows eller Linux-datorer i din miljö finns i följande avsnitt:

*  [Samla in data från virtuella Azure-datorer](../learn/quick-collect-azurevm.md) 
*  [Samla in data från hybrid Linux-datorer](../learn/quick-collect-linux-computer.md)
*  [Samla in data från hybrid Windows-dator](quick-collect-windows-computer.md)

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="sign-in-to-azure-portal"></a>Logga in på Azure-portalen
Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Skapa en arbetsyta
1. Klicka på **Alla tjänster** på Azure Portal. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Log Analytics arbets ytor**.

    ![Azure-portalen](media/quick-create-workspace/azure-portal-01.png)
  
2. Klicka på **Lägg till**och välj sedan alternativ för följande objekt:

   * Ange ett namn för den nya **Log Analytics-arbetsytan**, som *DefaultLAWorkspace*. Det här namnet måste vara globalt unikt för alla Azure Monitor prenumerationer.
   * Välj en **prenumeration** att länka till genom att välja från den listrutan om standardvalet inte är lämpligt.
   * För **resurs grupp**väljer du att använda en befintlig resurs grupp som redan har kon figurer ATS eller skapa en ny.  
   * Välj en tillgänglig **plats**.  Mer information finns i vilka [regioner Log Analytics är tillgängliga i](https://azure.microsoft.com/regions/services/) och söka efter Azure Monitor från fältet **Sök efter ett produkt** .  
   * Om du skapar en arbetsyta i en ny prenumeration som skapats efter 2 april 2018 används prisplanen *Per GB* automatiskt och alternativet för att välja en prisnivå är inte tillgängligt.  Om du skapar en arbetsyta för en befintlig prenumeration som skapats före 2 April eller en prenumeration som är kopplad till en befintlig Enterprise Agreement (EA)-registrering, väljer du önskad prisnivå.  Mer information om de specifika nivåerna finns [Log Analytics pris information](https://azure.microsoft.com/pricing/details/log-analytics/).

        ![Skapa Log Analytics resurs bladet](media/quick-create-workspace/create-loganalytics-workspace-02.png)  

3. När du har angett den nödvändiga informationen i fönsterrutan **Log Analytics-arbetsyta** klickar du på **OK**.  

När informationen har verifierats och arbetsytan skapas, kan du spåra förloppet under **Meddelanden** på menyn. 

## <a name="next-steps"></a>Nästa steg
Nu när du har en arbetsyta som är tillgängliga kan du konfigurera insamling av övervakning av telemetri, köra loggsökningar för att analysera dessa data och lägga till en hanteringslösning för att ge ytterligare data och analytisk insikt. 

* Information om hur du aktiverar data insamling från Azure-resurser med Azure-diagnostik eller Azure Storage finns i [samla in Azure Service-loggar och mått för användning i Log Analytics](../platform/collect-azure-metrics-logs.md).  
* [Lägg till System Center Operations Manager som en data källa](../platform/om-agents.md) för att samla in data från agenter som rapporterar din Operations Manager hanterings grupp och lagra den i din Log Analytics-arbetsyta. 
* Anslut [Configuration Manager](../platform/collect-sccm.md) för att importera datorer som är medlemmar i samlingar i hierarkin.  
* Granska de tillgängliga [övervaknings lösningarna](../insights/solutions.md) och Lägg till eller ta bort en lösning från din arbets yta.
