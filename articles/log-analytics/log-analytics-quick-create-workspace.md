---
title: Skapa en Log Analytics-arbetsyta i Azure Portal | Microsoft Docs
description: Lär dig hur du skapar en Log Analytics-arbetsyta om du vill aktivera hantering av lösningar och datainsamling från ditt moln och lokala miljöer i Azure-portalen.
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
ms.topic: conceptal
ms.date: 08/23/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: c041437b610c56fd34667d9b8f3fe67080b3aafc
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2018
ms.locfileid: "50419358"
---
# <a name="create-a-log-analytics-workspace-in-the-azure-portal"></a>Skapa en Log Analytics-arbetsyta i Azure portal
I Azure portal som du kan ställa in en Log Analytics-arbetsyta, vilket är en unik Log Analytics-miljö med en egen databas, datakällor och lösningar.  Stegen som beskrivs i den här artikeln krävs om du avser att samla in data från följande källor:

* Azure-resurser i din prenumeration
* Lokala datorer som övervakas av System Center Operations Manager
* Enhetssamlingar från System Center Configuration Manager 
* Diagnostik och log data från Azure storage

Andra källor, till exempel virtuella Azure-datorer och Windows eller Linux-datorer i din miljö finns i följande avsnitt:

*  [Samla in data från Azure-datorer](log-analytics-quick-collect-azurevm.md) 
*  [Samla in data från hybrid Linux-dator](log-analytics-quick-collect-linux-computer.md)
*  [Samla in data från hybrid Windows-dator](log-analytics-quick-collect-windows-computer.md)

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="sign-in-to-azure-portal"></a>Logga in på Azure-portalen
Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Skapa en arbetsyta
1. Klicka på **Alla tjänster** på Azure Portal. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Log Analytics**.

    ![Azure Portal](media/log-analytics-quick-create-workspace/azure-portal-01.png)
  
2. Klicka på **Skapa** och välj sedan alternativ för följande objekt:

  * Ange ett namn för den nya **Log Analytics-arbetsytan**, till exempel *DefaultLAWorkspace*. 
  * Välj en **prenumeration** att länka till genom att välja från den listrutan om standardvalet inte är lämpligt.
  * För **resursgrupp**, välja att använda en befintlig resurs grupp redan installationen eller skapa en ny.  
  * Välj ett tillgängligt **plats**.  Mer information finns i som [regioner Log Analytics är tillgängligt i](https://azure.microsoft.com/regions/services/).
  * Om du skapar en arbetsyta i en ny prenumeration som skapats efter 2 april 2018 används prisplanen *Per GB* automatiskt och alternativet för att välja en prisnivå är inte tillgängligt.  Om du skapar en arbetsyta för en befintlig prenumeration som skapats före 2 April eller en prenumeration som är kopplad till en befintlig Enterprise Agreement (EA)-registrering, väljer du önskad prisnivå.  Mer information om de olika nivåerna finns i [prisinformation om Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).

        ![Create Log Analytics resource blade](media/log-analytics-quick-create-workspace/create-loganalytics-workspace-02.png)  

3. När du har angett den nödvändiga informationen i fönsterrutan **Log Analytics-arbetsyta** klickar du på **OK**.  

När informationen har verifierats och arbetsytan skapas, kan du spåra förloppet under **Meddelanden** på menyn. 

## <a name="next-steps"></a>Nästa steg
Nu när du har en arbetsyta som är tillgängliga kan du konfigurera insamling av övervakning av telemetri, köra loggsökningar för att analysera dessa data och lägga till en hanteringslösning för att ge ytterligare data och analytisk insikt. 

* Om du vill aktivera insamling av data från Azure-resurser med Azure Diagnostics eller Azure storage, se [samla in Azure-tjänsteloggar och mått för användning i Log Analytics](log-analytics-azure-storage.md).  
* [Lägg till System Center Operations Manager som en datakälla](log-analytics-om-agents.md) att samla in data från agenter som rapporterar Operations Manager-hanteringsgrupp och lagra den i Log Analytics-arbetsytan. 
* Ansluta [Configuration Manager](log-analytics-sccm.md) att importera datorer som är medlemmar i samlingar i hierarkin.  
* Granska den [hanteringslösningar](https://docs.microsoft.com/azure/monitoring/monitoring-solutions-inventory?toc=%2fazure%2flog-analytics%2ftoc.json) tillgängliga och hur du lägger till eller ta bort en lösning från din arbetsyta.
