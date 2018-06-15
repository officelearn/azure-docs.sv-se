---
title: Använd en avisering för att utlösa en Azure Automation-runbook
description: Lär dig mer om att utlösa en runbook ska köras när en Azure avisering utlöses.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: e791cddb07d3204f807dbeff98b7fc69419ae23c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34194044"
---
# <a name="use-an-alert-to-trigger-an-azure-automation-runbook"></a>Använd en avisering för att utlösa en Azure Automation-runbook

Du kan använda [Azure-Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md?toc=%2fazure%2fautomation%2ftoc.json) att övervaka basnivå mått och loggfiler för de flesta tjänster i Azure. Du kan anropa Azure Automation-runbooks med hjälp av [åtgärdsgrupper](../monitoring-and-diagnostics/monitoring-action-groups.md?toc=%2fazure%2fautomation%2ftoc.json) eller genom att använda klassiska aviseringar för att automatisera uppgifter baserat på aviseringar. Den här artikeln visar hur du konfigurerar och köra en runbook med hjälp av aviseringar.

## <a name="alert-types"></a>Aviseringstyper

Du kan använda automation-runbooks med tre aviseringstyper:
* Klassiska mått aviseringar
* Aktivitetsloggaviseringar
* Nära realtid mått aviseringar

När en avisering anropar en runbook, är det faktiska anropet en HTTP POST-begäran till webhooken. POST-begäran innehåller en JSON-formaterad objekt som har användbara egenskaper som är relaterade till aviseringen. Följande tabell innehåller länkar till nyttolast schemat för varje typ av avisering:

|Varning  |Beskrivning|Nyttolasten i schemat  |
|---------|---------|---------|
|[Klassiska mått avisering](../monitoring-and-diagnostics/insights-alerts-portal.md?toc=%2fazure%2fautomation%2ftoc.json)    |Skickar en avisering när någon plattform nivå mått uppfyller ett visst villkor. Till exempel när värdet för **CPU i %** på en virtuell dator är större än **90** för de senaste fem minuterna.| [Klassen mått avisering nyttolast schemat](../monitoring-and-diagnostics/insights-webhooks-alerts.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)         |
|[Varning för logg](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)    |Skickar ett meddelande när en ny händelse i Azure-aktivitetsloggen matchar vissa villkor. Till exempel när en `Delete VM` åtgärden genomförs i **myProductionResourceGroup** eller när en ny Azure-tjänstens hälsa händelse med ett **Active** status visas.| [Aviseringen nyttolasten för aktivitetslogg](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)        |
|[Nära realtid mått avisering](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)    |Skickar ett meddelande snabbare än mått aviseringar när ett eller flera mått för plattformen nivå uppfyller angivna villkor. Till exempel när värdet för **CPU i %** på en virtuell dator är större än **90**, och värdet för **nätverk i** är större än **500 MB** under senaste 5 minuter.| [Nära realtid mått avisering nyttolast schemat](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)          |

Eftersom de data som tillhandahålls av varje typ av avisering är olika, hanteras varje typ av avisering annorlunda. Lär dig hur du skapar en runbook för att hantera olika typer av aviseringar i nästa avsnitt.

## <a name="create-a-runbook-to-handle-alerts"></a>Skapa en runbook för att hantera aviseringar

För att använda Automation med aviseringar, behöver du en runbook som logik som hanterar avisering JSON-nyttolast som skickas till runbook. Följande exempel-runbook måste anropas från en Azure avisering. 

Enligt beskrivningen i föregående avsnitt, har varje typ av avisering ett annat schema. Skriptet använder i webhook-data i den `WebhookData` runbookinmatningsparameter från en avisering. Sedan utvärderar skriptet JSON-nyttolast för att avgöra vilken typ av avisering användes. 

Det här exemplet används en avisering från en virtuell dator. VM-data hämtas från nyttolasten och använder sedan informationen för att stoppa den virtuella datorn. Anslutningen måste ställas in i Automation-konto där runbook körs.

Används av **AzureRunAsConnection** [kör som-konto](automation-create-runas-account.md) att autentisera med Azure för att utföra management-åtgärd mot den virtuella datorn.

Använd det här exemplet för att skapa en runbook med namnet **stoppa AzureVmInResponsetoVMAlert**. Du kan ändra PowerShell-skriptet och använda den med många olika resurser.

1. Gå till Azure Automation-konto.
1. Under **PROCESSAUTOMATISERING**väljer **Runbooks**.
1. Markera överst i listan med runbooks **lägga till en runbook**. 
1. På sidan **Lägg till runbook** väljer du **Snabbregistrering**.
1. Runbook-namn, ange **stoppa AzureVmInResponsetoVMAlert**. Vilken runbooktyp av, Välj **PowerShell**. Välj sedan **Create** (Skapa).  
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

## <a name="create-an-action-group"></a>Skapa en grupp

En grupp är en samling åtgärder som utlöses av en avisering. Runbooks är bara en av de många åtgärder som du kan använda med åtgärdsgrupper.

1. Välj i Azure-portalen **övervakaren** > **inställningar** > **åtgärdsgrupper**.
1. Välj **Lägg till grupp**, och sedan ange informationen som krävs:  
    1. I den **åtgärd gruppnamn** ange ett namn.
    1. I den **kortnamnet** ange ett namn. Det korta namnet används i stället för en fullständig åtgärd gruppnamn när meddelanden skickas med hjälp av den här åtgärdsgruppen.
    1. Den **prenumeration** rutan fylls automatiskt med din aktuella prenumeration. Det här är den prenumeration där åtgärdsgruppen sparas.
    1. Välj den resursgrupp som åtgärdsgruppen sparas.

Det här exemplet skapar du två åtgärder: runbook-åtgärden och en Aviseringsåtgärd.

### <a name="runbook-action"></a>Runbook-åtgärden

Så här skapar du en runbook-åtgärden i åtgärdsgruppen:

1. Under **åtgärder**, för **ÅTGÄRDSNAMN**, ange ett namn för åtgärden. För **ÅTGÄRDSTYP**väljer **Automation-Runbook**.
1. Under **information**väljer **redigera information**.  
1. På den **konfigurera Runbook** sidan under **Runbook-källa**väljer **användaren**.  
1. Välj din **prenumeration** och **Automation-konto**, och välj sedan den **stoppa AzureVmInResponsetoVMAlert** runbook.  
1. När du är klar väljer du **OK**.

### <a name="notification-action"></a>Meddelandeåtgärd

Skapa en Aviseringsåtgärd i åtgärdsgruppen:

1. Under **åtgärder**, för **ÅTGÄRDSNAMN**, ange ett namn för åtgärden. För **ÅTGÄRDSTYP**väljer **e-post**.  
1. Under **information** väljer, **redigera information**.  
1. På den **e-post** anger du den e-postadressen om du vill använda för meddelandet och välj sedan **OK**. Det är bra att lägga till en e-postadress förutom runbook som en åtgärd. Du meddelas när runbook startas.  

    Din grupp bör se ut som följande bild:

   ![Lägg till grupp på sidan åtgärd](./media/automation-create-alert-triggered-runbook/add-action-group.png)
1. Om du vill skapa åtgärdsgruppen **OK**.

Du kan använda den här åtgärdsgruppen i den [loggen Aktivitetsaviseringar](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json) och [nära realtid aviseringar](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md?toc=%2fazure%2fautomation%2ftoc.json#create-an-alert-rule-with-the-azure-portal) som du skapar.

## <a name="classic-alert"></a>Klassiska avisering

Klassiska aviseringar baseras på mått och Använd inte åtgärdsgrupper. Du kan dock ställa in en runbook-åtgärden baserat på en avisering om klassiska. 

Att skapa en klassisk:

1. Välj **Lägg till metrisk varning**.
1. Namnge aviseringen mått **myVMCPUAlert**. Ange en kort beskrivning för aviseringen.
1. Mått aviseringstillståndet, Välj **större än**. För den **tröskelvärdet** värde, Välj **10**. För den **Period** värde, Välj **under de senaste fem minuterna**.
1. Under **åtgärda**väljer **köra en runbook från denna avisering**.
1. På den **konfigurera Runbook** sidan för **Runbook-källa**väljer **användaren**. Välj ditt automation-konto och välj sedan den **stoppa AzureVmInResponsetoVMAlert** runbook. Välj **OK**.
1. Välj för att spara varningsregeln **OK**.

## <a name="next-steps"></a>Nästa steg

* Läs mer om hur du startar en Automation-runbook med hjälp av en webhook [startar en runbook från en webhook](automation-webhooks.md).
* Mer information om olika sätt att starta en runbook finns [starta en runbook](automation-starting-a-runbook.md).
* Information om hur du skapar en avisering för aktiviteten loggen finns [och skapa aktiviteten Logga varningar](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json).
* Information om hur du skapar en avisering om nära realtid finns [skapa en aviseringsregel i Azure portal](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md?toc=%2fazure%2fautomation%2ftoc.json#create-an-alert-rule-with-the-azure-portal).
