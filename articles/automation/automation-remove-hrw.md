---
title: Ta bort Azure Automation Hybrid Runbook Worker | Microsoft Docs
description: "Den här artikeln innehåller information om hur du hanterar distribuerade Azure Automation Hybrid Runbook Worker som gör det möjligt att köra runbooks på datorer i din lokala miljö datacenter eller molnet."
services: automation
documentationcenter: 
author: georgewallace
manager: carmonm
editor: tysonn
ms.assetid: 
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2017
ms.author: magoedte
ms.openlocfilehash: a897a97b9a1e259f5994a27b974eb5183fd1194b
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2017
---
# <a name="remove-azure-automation-hybrid-runbook-workers"></a>Ta bort Azure Automation Hybrid Runbook Worker

Du kan 

## <a name="removing-hybrid-runbook-worker"></a>Tar bort Hybrid Runbook Worker

Du kan ta bort en eller flera Runbook Worker-hybrider från en grupp och du tar bort gruppen, beroende på dina krav.  Utför följande steg för att ta bort en Hybrid Runbook Worker från en lokal dator.

1. Navigera till ditt Automation-konto i Azure-portalen.  
2. Från den **inställningar** bladet väljer **nycklar** och anteckna värdena för fältet **URL** och **primära åtkomstnyckeln**.  Du behöver den här informationen för nästa steg.
3. Öppna ett PowerShell-session i administratörsläge och kör följande kommando – `Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey>`.  Använd den **-Verbose** växla för en detaljerad logg över borttagningen.

> [!NOTE]
> Microsoft Monitoring Agent tas inte bort från datorn, funktioner och konfiguration av Hybrid Runbook Worker-rollen.  

## <a name="remove-hybrid-worker-groups"></a>Ta bort Hybrid Worker-grupper

Om du vill ta bort en grupp, måste du först ta bort Hybrid Runbook Worker från varje dator som är medlem i gruppen med hjälp av proceduren som visades tidigare och utför sedan följande steg för att ta bort gruppen.  

1. Öppna Automation-kontot i Azure-portalen.
1. Välj den **Hybrid Worker grupper** panelen och i den **Hybrid Worker grupper** bladet Välj den grupp som du vill ta bort.  När du har valt en viss grupp av **Hybrid worker-gruppen** egenskapsbladet visas.<br> ![Bladet hybrid Runbook Worker-grupp](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)   
1. Klicka på egenskapsbladet för den valda gruppen **ta bort**.  Ett meddelande visas där du ombeds bekräfta åtgärden, Välj **Ja** om du är säker på att du vill fortsätta.<br> ![Ta bort grupp bekräftelsedialogruta](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)<br> Den här processen kan ta flera sekunder att slutföra och du kan spåra förloppet under **Meddelanden** på menyn. 

## <a name="next-steps"></a>Nästa steg

* Granska [köra runbooks på en Hybrid Runbook Worker](automation-hrw-run-runbooks.md) att lära dig hur du konfigurerar dina runbooks för att automatisera processer i ditt lokala datacenter eller andra molnmiljö.
* Information om hur du installerar Windows Hybrid Runbook Worker-arbeten finns [Azure Automation Windows Hybrid Runbook Worker](automation-windows-hrw-install.md).
* Information om hur du installerar Linux Hybrid Runbook Worker-arbeten finns [Azure Automation Linux Hybrid Runbook Worker](automation-linux-hrw-install.md).