---
title: Använd en avisering för att utlösa en Azure Automation-runbook
description: Lär dig hur du utlöser en runbook ska köras när en Azure-avisering utlöses.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 09/18/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 88fe7740170638e9e0d7398a02dcf83ab81f6ffc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61073870"
---
# <a name="use-an-alert-to-trigger-an-azure-automation-runbook"></a>Använd en avisering för att utlösa en Azure Automation-runbook

Du kan använda [Azure Monitor](../azure-monitor/overview.md?toc=%2fazure%2fautomation%2ftoc.json) att övervaka beroende på mått och loggar för de flesta tjänster i Azure. Du kan anropa Azure Automation-runbooks med hjälp av [åtgärdsgrupper](../azure-monitor/platform/action-groups.md?toc=%2fazure%2fautomation%2ftoc.json) eller genom att använda klassiska aviseringar för att automatisera uppgifter baserat på varningar. Den här artikeln visar hur du konfigurerar och kör en runbook med hjälp av aviseringar.

## <a name="alert-types"></a>Aviseringstyper

Du kan använda automation-runbooks med tre aviseringstyper:
* Klassiska måttaviseringar
* Aktivitetsloggaviseringar
* Aviseringar i nära realtid mått

När en avisering anropar en runbook, är det faktiska anropet en HTTP POST-begäran till webhooken. Brödtexten i POST-begäran innehåller ett JSON-formaterad objekt som har användbara egenskaper som är relaterade till aviseringen. I följande tabell visas länkar till nyttolast-schemat för varje typ av avisering:

|Varning  |Beskrivning|Nyttolast-schema  |
|---------|---------|---------|
|[Klassisk måttavisering](../monitoring-and-diagnostics/insights-alerts-portal.md?toc=%2fazure%2fautomation%2ftoc.json)    |Skickar ett meddelande om vilka mått som helst på plattformsnivå uppfyller ett visst villkor. Till exempel när värdet för **CPU %** på en virtuell dator är större än **90** under de senaste 5 minuterna.| [Klasschema för metrisk varning nyttolast](../azure-monitor/platform/alerts-webhooks.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)         |
|[Aktivitetsloggavisering](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)    |Skickar ett meddelande när en ny händelse i aktivitetsloggen för Azure matchar specifika villkor. Till exempel när en `Delete VM` åtgärden sker i **myProductionResourceGroup** eller när en ny Azure Service Health-händelse med en **Active** status visas.| [Aviseringen nyttolast för aktivitetslogg](../azure-monitor/platform/activity-log-alerts-webhook.md)        |
|[Nära realtid metrisk varning](../azure-monitor/platform/alerts-metric-near-real-time.md?toc=%2fazure%2fautomation%2ftoc.json)    |Skickar ett meddelande snabbare än måttaviseringar när ett eller flera på plattformsnivå mått uppfyller angivna villkor. Till exempel när värdet för **CPU %** på en virtuell dator är större än **90**, och värdet för **nätverk i** är större än **500 MB** under senaste 5 : e minut.| [Nära realtid metrisk varning nyttolast schema](../azure-monitor/platform/alerts-webhooks.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)          |

Eftersom de data som tillhandahålls av varje typ av avisering är olika, hanteras annorlunda varje typ av avisering. I nästa avsnitt lär du dig att skapa en runbook för att hantera olika typer av aviseringar.

## <a name="create-a-runbook-to-handle-alerts"></a>Skapa en runbook för att hantera aviseringar

Om du vill använda Automation med aviseringar, behöver du en runbook som innehåller logik som hanterar avisering JSON-nyttolasten som skickas till runbooken. Följande exempel-runbook måste anropas från en Azure-avisering.

Enligt beskrivningen i föregående avsnitt, har varje typ av avisering ett annat schema. Skriptet använder i webhookdata i den `WebhookData` indataparameter för runbook från en avisering. Skriptet utvärderar sedan JSON-nyttolast för att avgöra vilken typ av avisering har använts.

Det här exemplet används en avisering från en virtuell dator. Virtuella datorns data hämtas från nyttolasten och använder sedan informationen för att stoppa den virtuella datorn. Anslutningen måste konfigureras i Automation-konto där runbook körs. När du utlöser runbooks med hjälp av aviseringar är det viktigt att kontrollera status för aviseringen i en runbook som utlöses. Runbook-flödet utlöser varje gång som ändrar status för aviseringen. Aviseringar har olika tillstånd, de två vanligaste statusvärdena är `Activated` och `Resolved`. Sök efter det här tillståndet i runbook-logik för att säkerställa att din runbook inte körs mer än en gång. Exemplet i den här artikeln visar hur du söker efter `Activated` aviseringar endast.

Används den **AzureRunAsConnection** [kör som-konto](automation-create-runas-account.md) att autentisera med Azure för att utföra management-åtgärd mot den virtuella datorn.

Använd det här exemplet för att skapa en runbook med namnet **Stop-AzureVmInResponsetoVMAlert**. Du kan ändra PowerShell-skriptet och använda den med många olika resurser.

1. Gå till ditt Azure Automation-konto.
1. Under **PROCESSAUTOMATISERING**väljer **Runbooks**.
1. Överst i listan med runbooks, Välj **Lägg till en runbook**. 
1. På sidan **Lägg till runbook** väljer du **Snabbregistrering**.
1. Runbooknamn, ange **Stop-AzureVmInResponsetoVMAlert**. Typ av runbook, Välj **PowerShell**. Välj sedan **Create** (Skapa).  
1. Kopiera följande PowerShell-exempel i den **redigera** fönstret. 

    ```powershell-interactive
    <#
    .SYNOPSIS
    This runbook stops a resource management VM in response to an Azure alert trigger.

    .DESCRIPTION
    This runbook stops a resource management VM in response to an Azure alert trigger.
    The input is alert data that has the information required to identify which VM to stop.
    
    DEPENDENCIES
    - The runbook must be called from an Azure alert via a webhook.
    
    REQUIRED AUTOMATION ASSETS
    - An Automation connection asset called "AzureRunAsConnection" that is of type AzureRunAsConnection.
    - An Automation certificate asset called "AzureRunAsCertificate".

    .PARAMETER WebhookData
    Optional. (The user doesn't need to enter anything, but the service always passes an object.)
    This is the data that's sent in the webhook that's triggered from the alert.

    .NOTES
    AUTHOR: Azure Automation Team
    LASTEDIT: 2017-11-22
    #>

    [OutputType("PSAzureOperationResponse")]

    param
    (
        [Parameter (Mandatory=$false)]
        [object] $WebhookData
    )

    $ErrorActionPreference = "stop"

    if ($WebhookData)
    {
        # Get the data object from WebhookData.
        $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

        # Get the info needed to identify the VM (depends on the payload schema).
        $schemaId = $WebhookBody.schemaId
        Write-Verbose "schemaId: $schemaId" -Verbose
        if ($schemaId -eq "AzureMonitorMetricAlert") {
            # This is the near-real-time Metric Alert schema
            $AlertContext = [object] ($WebhookBody.data).context
            $ResourceName = $AlertContext.resourceName
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq "Microsoft.Insights/activityLogs") {
            # This is the Activity Log Alert schema
            $AlertContext = [object] (($WebhookBody.data).context).activityLog
            $ResourceName = (($AlertContext.resourceId).Split("/"))[-1]
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq $null) {
            # This is the original Metric Alert schema
            $AlertContext = [object] $WebhookBody.context
            $ResourceName = $AlertContext.resourceName
            $status = $WebhookBody.status
        }
        else {
            # The schema isn't supported.
            Write-Error "The alert data schema - $schemaId - is not supported."
        }

        Write-Verbose "status: $status" -Verbose
        if ($status -eq "Activated")
        {
            $ResourceType = $AlertContext.resourceType
            $ResourceGroupName = $AlertContext.resourceGroupName
            $SubId = $AlertContext.subscriptionId
            Write-Verbose "resourceType: $ResourceType" -Verbose
            Write-Verbose "resourceName: $ResourceName" -Verbose
            Write-Verbose "resourceGroupName: $ResourceGroupName" -Verbose
            Write-Verbose "subscriptionId: $SubId" -Verbose

            # Use this only if this is a resource management VM.
            if ($ResourceType -eq "Microsoft.Compute/virtualMachines")
            {
                # This is the VM.
                Write-Verbose "This is a resource management VM." -Verbose

                # Authenticate to Azure by using the service principal and certificate. Then, set the subscription.
                Write-Verbose "Authenticating to Azure with service principal and certificate" -Verbose
                $ConnectionAssetName = "AzureRunAsConnection"
                Write-Verbose "Get connection asset: $ConnectionAssetName" -Verbose
                $Conn = Get-AutomationConnection -Name $ConnectionAssetName
                if ($Conn -eq $null)
                {
                    throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
                }
                Write-Verbose "Authenticating to Azure with service principal." -Verbose
                Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Verbose
                Write-Verbose "Setting subscription to work against: $SubId" -Verbose
                Set-AzureRmContext -SubscriptionId $SubId -ErrorAction Stop | Write-Verbose

                # Stop the VM.
                Write-Verbose "Stopping the VM - $ResourceName - in resource group - $ResourceGroupName -" -Verbose
                Stop-AzureRmVM -Name $ResourceName -ResourceGroupName $ResourceGroupName -Force
                # [OutputType(PSAzureOperationResponse")]
            }
            else {
                # ResourceType isn't supported.
                Write-Error "$ResourceType is not a supported resource type for this runbook."
            }
        }
        else {
            # The alert status was not 'Activated', so no action taken.
            Write-Verbose ("No action taken. Alert status: " + $status) -Verbose
        }
    }
    else {
        # Error
        Write-Error "This runbook is meant to be started from an Azure alert webhook only."
    }
    ```
1. Välj **publicera** att spara och publicera en runbook.

## <a name="create-an-action-group"></a>Skapa en åtgärdsgrupp

En åtgärdsgrupp är en uppsättning åtgärder som utlöses av en avisering. Runbooks är bara en av de många åtgärder som du kan använda med åtgärdsgrupper.

1. I Azure-portalen väljer du **övervakaren** > **inställningar** > **åtgärdsgrupper**.
1. Välj **Lägg till åtgärdsgrupp**, och sedan ange informationen som krävs:  
    1. I den **namn på åtgärdsgrupp** anger du ett namn.
    1. I den **kortnamnet** anger du ett namn. Det korta namnet används i stället för en fullständig åtgärdsgruppnamn när meddelanden skickas med hjälp av den här åtgärdsgruppen.
    1. Den **prenumeration** rutan fylls automatiskt med din aktuella prenumeration. Det här är den prenumeration där åtgärdsgruppen sparas.
    1. Välj den resursgrupp där åtgärdsgruppen sparas.

I det här exemplet skapar du två åtgärder: runbook-åtgärden och en meddelandeåtgärd.

### <a name="runbook-action"></a>Runbook-åtgärden

Skapa en runbook-åtgärden i åtgärdsgruppen:

1. Under **åtgärder**, för **ÅTGÄRDSNAMN**, ange ett namn för åtgärden. För **ÅTGÄRDSTYP**väljer **Automation-Runbook**.
1. Under **information**väljer **redigera information**.  
1. På den **konfigurerar Runbook** sidan under **Runbook-källa**väljer **användaren**.  
1. Välj din **prenumeration** och **Automation-konto**, och välj sedan den **Stop-AzureVmInResponsetoVMAlert** runbook.  
1. När du är klar väljer du **OK**.

### <a name="notification-action"></a>Meddelandeåtgärd

Skapa en meddelandeåtgärd i åtgärdsgruppen:

1. Under **åtgärder**, för **ÅTGÄRDSNAMN**, ange ett namn för åtgärden. För **ÅTGÄRDSTYP**väljer **e-post**.  
1. Under **information** väljer, **redigera information**.  
1. På den **e-post** anger du den e-postadressen om du vill använda för avisering och välj sedan **OK**. Det kan vara bra att lägga till en e-postadress förutom runbook som en åtgärd. Du meddelas när runbooken startar.  

    Din åtgärdsgrupp bör se ut som på följande bild:

   ![Lägg till åtgärd gruppsidan](./media/automation-create-alert-triggered-runbook/add-action-group.png)
1. Om du vill skapa åtgärdsgruppen, Välj **OK**.

Du kan använda den här åtgärdsgruppen i den [aktivitetsloggaviseringar](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json) och [nära realtidsaviseringar](../azure-monitor/platform/alerts-overview.md?toc=%2fazure%2fautomation%2ftoc.json) som du skapar.

## <a name="classic-alert"></a>Klassiska avisering

Klassiska aviseringar baserat på mått och använda inte åtgärdsgrupper. Du kan dock ställa in en runbook-åtgärd baserat på en klassisk avisering. 

Att skapa en klassisk:

1. Välj **Lägg till metrisk varning**.
1. Namnet **myVMCPUAlert**. Ange en kort beskrivning av aviseringen.
1. Metrisk aviseringstillståndet, Välj **är större än**. För den **tröskelvärdet** värde, väljer **10**. För den **Period** värde, väljer **under de senaste fem minuterna**.
1. Under **vidta åtgärder**väljer **kör en runbook från den här aviseringen**.
1. På den **konfigurerar Runbook** sidan för **Runbook-källa**väljer **användaren**. Välj ditt automation-konto och välj sedan den **Stop-AzureVmInResponsetoVMAlert** runbook. Välj **OK**.
1. Välj för att spara varningsregeln **OK**.

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du startar en Automation-runbook med en webhook finns i [starta en runbook från en webhook](automation-webhooks.md).
* Mer information om olika sätt att starta en runbook finns [starta en runbook](automation-starting-a-runbook.md).
* Läs hur du skapar en aktivitetsloggavisering i [skapa aviseringar för aktivitetsloggen](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json).
* Läs hur du skapar en avisering om nästan i realtid i [skapar en aviseringsregel i Azure-portalen](../azure-monitor/platform/alerts-metric.md?toc=/azure/azure-monitor/toc.json).

