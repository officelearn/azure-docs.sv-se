---
title: Azure Automation Linux Hybrid Runbook Worker | Microsoft Docs
description: "Den här artikeln innehåller information om hur du installerar en Azure Automation Hybrid Runbook Worker som gör det möjligt att köra runbooks på Linux-baserade datorer i ditt lokala datacenter eller molnmiljö."
services: automation
documentationcenter: 
author: eslesar
manager: carmonm
editor: tysonn
ms.assetid: 
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/07/2017
ms.author: magoedte
ms.openlocfilehash: 5e6687e4e868ae998c77bba231437d52fdbe719c
ms.sourcegitcommit: 5d772f6c5fd066b38396a7eb179751132c22b681
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2017
---
# <a name="how-to-deploy-a-linux-hybrid-runbook-worker"></a>Så här distribuerar du en Linux Hybrid Runbook Worker

Runbooks i Azure Automation kan inte komma åt resurser i andra moln eller lokala miljö eftersom de körs i Azure-molnet. Funktionen Hybrid Runbook Worker i Azure Automation kan du köra runbooks direkt på den dator där rollen och mot resurser i miljön för att hantera de lokala resurserna. Runbooks lagras och hanteras i Azure Automation och sedan levereras till en eller flera särskilda datorer.

Den här funktionen visas i följande bild:<br>  

![Hybrid Runbook Worker-översikt](media/automation-offering-get-started/automation-infradiagram-networkcomms.png)

En teknisk översikt över Hybrid Runbook Worker-rollen finns [Automation arkitektur, översikt](automation-offering-get-started.md#automation-architecture-overview). Granska följande information om den [maskin- och programvarukrav](automation-offering-get-started.md#hybrid-runbook-worker) och [information för att förbereda nätverket](automation-offering-get-started.md#network-planning) innan du börjar distribuera en Hybrid Runbook Worker.  När en runbook worker har distribuerats, granska [köra runbooks på en Hybrid Runbook Worker](automation-hrw-run-runbooks.md) att lära dig hur du konfigurerar dina runbooks för att automatisera processer i ditt lokala datacenter eller andra molnmiljö.     

## <a name="hybrid-runbook-worker-groups"></a>Hybrid Runbook Worker-grupper
Varje Runbook Worker-Hybrid är medlem i en Hybrid Runbook Worker-grupp som du anger när du installerar agenten.  En grupp kan innehålla en enskild agent, men du kan installera flera agenter i en grupp för hög tillgänglighet.

När du startar en runbook på en Hybrid Runbook Worker, anger du den grupp som den körs på.  Medlemmar i gruppen avgör vilken worker services begäran.  Du kan inte ange en viss worker.

## <a name="installing-linux-hybrid-runbook-worker"></a>Installera Linux Hybrid Runbook Worker
Om du vill installera och konfigurera en Hybrid Runbook Worker på Linux-dator, kan du följa en mycket enkelt vidarebefordra process för att manuellt installera och konfigurera rollen.   Det krävs att aktivera den **Automation Hybrid Worker** lösning i OMS-arbetsyta och sedan köra en uppsättning kommandon för att registrera datorn som en arbetare och lägga till den i en ny eller befintlig grupp. 

Innan du fortsätter måste du Observera logganalys-arbetsytan Automation-kontot är länkat till och den primära nyckeln för ditt Automation-konto.  Du kan hitta båda från portalen genom att välja Automation-kontot och markera **arbetsytan** för arbetsyte-ID och välja **nycklar** för den primära nyckeln.  

1.  Aktivera lösningen ”Automation Hybrid Worker” i OMS. Detta kan göras genom att antingen:

   1. Från galleriet lösningar i den [OMS-portalen](https://mms.microsoft.com) aktivera den **Automation Hybrid Worker** lösning
   2. Kör följande cmdlet:

        ```powershell
         $null = Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName  <ResourceGroupName> -WorkspaceName <WorkspaceName> -IntelligencePackName  "AzureAutomation" -Enabled $true
        ```

2.  Kör följande kommando, ändra värdena för parametrarna *-w*, *-k*, *-g*, och *-e*. För den *-g* parametern Ersätt värdet med namnet på Hybrid Runbook Worker-grupp som ska ansluta till den nya Linux Hybrid Runbook Worker. Om namnet inte finns redan i Automation-kontot kommer att göras en ny Hybrid Runbook Worker-grupp med det namnet.
    
    ```
    sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <OMSworkspaceId> -k <AutomationSharedKey> -g <hybridgroupname> -e <automationendpoint>
    ```
3. När kommandot har slutförts bladet Hybrid Worker-grupper i Azure portal visas den nya gruppen och antal medlemmar eller om en befintlig grupp, ökar antalet medlemmar.  Du kan välja grupp från listan på den **Hybrid Worker grupper** och välj den **hybrider** panelen.  På den **hybrider** bladet visas varje medlem i gruppen i listan.  


## <a name="turning-off-signature-validation"></a>Om du inaktiverar signaturverifiering 
Som standard kräver Linux Hybrid Runbook Worker signaturverifiering. Om du kör en osignerad runbook mot en arbetare, visas ett fel som innehåller ”signaturverifieringen misslyckades”. Om du vill inaktivera signaturverifiering, kör du följande kommando och ersätter den andra parametern med din OMS arbetsyte-ID:

    ```
    sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <OMSworkspaceId>
    ```
   
## <a name="next-steps"></a>Nästa steg

* Granska [köra runbooks på en Hybrid Runbook Worker](automation-hrw-run-runbooks.md) att lära dig hur du konfigurerar dina runbooks för att automatisera processer i ditt lokala datacenter eller andra molnmiljö.
* Anvisningar för hur du tar bort Hybrid Runbook Workers finns [ta bort Azure Automation Runbook Worker-hybrider](automation-remove-hrw.md)
