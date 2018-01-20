---
title: "Svara på Azure-aviseringar med en automation-runbook | Microsoft Docs"
description: "Lär dig mer om att utlösa en runbook ska köras när Azure aviseringar."
services: automation
keywords: 
author: georgewallace
ms.author: gwallace
ms.date: 01/11/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 486a3c80c9d6c19ca0a7ba7d4e3cdcde927053b9
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2018
---
# <a name="respond-to-azure-alerts-with-an-automation-runbook"></a>Svara på Azure-aviseringar med en automation-runbook

[Azure-monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md?toc=%2fazure%2fautomation%2ftoc.json) innehåller basnivå mått och loggar för de flesta tjänster i Microsoft Azure. Azure automation-runbooks kan anropas med [åtgärdsgrupper](../monitoring-and-diagnostics/monitoring-action-groups.md?toc=%2fazure%2fautomation%2ftoc.json) eller från klassiska aviseringar för att automatisera uppgifter baserat på aviseringar. Den här artikeln visar hur du konfigurerar och köra en runbook som är baserade på dessa aviseringar.

## <a name="alert-types"></a>Aviseringstyper

Runbooks är åtgärder som stöds på alla tre typer av aviseringar. När en avisering anropar en runbook, är det faktiska anropet en HTTP POST-begäran till webhooken. POST-begäran innehåller en JSON-formaterad objekt som innehåller användbar egenskaper som är relaterade till aviseringen. Följande tabell innehåller länkar till nyttolast schemat för varje typ av avisering:

|Varning  |Beskrivning|Payload Schema  |
|---------|---------|---------|
|[Klassiska mått avisering](../monitoring-and-diagnostics/insights-alerts-portal.md?toc=%2fazure%2fautomation%2ftoc.json)    |Ett meddelande när någon plattform nivå mått uppfyller ett visst villkor (till exempel processor i procent på en virtuell dator är större än 90 för senaste 5 minuter).| [Nyttolasten i schemat](../monitoring-and-diagnostics/insights-webhooks-alerts.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)         |
|[Varning för logg](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)    |Få ett meddelande när en ny händelse i Azure-aktivitetsloggen matchar vissa villkor (till exempel när en åtgärd för ”ta bort virtuell dator” inträffar i myProductionResourceGroup eller när en ny händelse tjänstens hälsa med ”aktiv” status som visas).| [Nyttolasten i schemat](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)        |
|[Nära realtid mått avisering](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)    |Ett meddelande snabbare än mått aviseringar när ett eller flera mått för plattformen nivå uppfyller angivna villkor (till exempel processor i procent på en virtuell dator är större än 90 och nätverket i är större än 500 MB för senaste 5 minuter).| [Nyttolasten i schemat](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)          |

Eftersom data från varje avisering är olika, måste varje avisering hanteras annorlunda. Lär dig hur du skapar en runbook för att hantera dessa olika typer av aviseringar i nästa avsnitt.

## <a name="create-a-runbook-to-handle-alerts"></a>Skapa en runbook för att hantera aviseringar

För att använda automation med aviseringar, behöver du en runbook som innehåller logik för att hantera aviseringen JSON-nyttolast som har överförts till runbook. Följande exempel-runbook måste anropas från en Azure avisering. Enligt beskrivningen i föregående avsnitt, har varje typ av avisering ett annat schema. Skriptet använder i webhook-data i den `WebhookData` runbook Indataparametern från en avisering och utvärderar JSON-nyttolast för att avgöra vilken typ av avisering användes. I följande exempel används på en avisering från en virtuell dator. VM-data hämtas från nyttolasten och använder informationen för att stoppa den virtuella datorn. Anslutningen måste ställas in i Automation konto där runbook kördes.

Används av **AzureRunAsConnection** [kör som-konto](automation-create-runas-account.md) att autentisera med Azure för att kunna utföra management-åtgärd mot den virtuella datorn.

Följande PowerShell-skript kan ändras för användning med många olika resurser.

Vidta följande exempel och skapa en runbook med namnet **stoppa AzureVmInResponsetoVMAlert** med PowerShell-exempel.

1. Öppna ditt Automation-konto.
1. Klicka på **Runbooks** under **PROCESSAUTOMATISERING**. En lista med runbooks visas.
1. Klicka på knappen **Lägg till en runbook** överst i listan. På den **lägga till Runbook-sidan**väljer **Snabbregistrering**.
1. Ange ”Stop AzureVmInResponsetoVMAlert” för runbook **namn**, och välj **PowerShell** för **runbooktyp**. Klicka på **Skapa**.
1. Kopiera följande PowerShell-exempel i redigeringsrutan. Klicka på **publicera** att spara och publicera en runbook.

```powershell-interactive
<#
.SYNOPSIS
  This runbook will stop a resource management VM in response to an Azure alert trigger.

.DESCRIPTION
  This runbook will stop an resource management VM in response to an Azure alert trigger.
  Input is alert data with information needed to identify which VM to stop.
  
  DEPENDENCIES
  - The runbook must be called from an Azure alert via a webhook.
  
  REQUIRED AUTOMATION ASSETS
  - An Automation connection asset called "AzureRunAsConnection" that is of type AzureRunAsConnection.
  - An Automation certificate asset called "AzureRunAsCertificate".

.PARAMETER WebhookData
   Optional (user does not need to enter anything, but the service will always pass an object)
   This is the data that is sent in the webhook that is triggered from the alert.

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
    # Get the data object from WebhookData
    $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

    # Get the info needed to identify the VM (depends on the payload schema)
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
        # Schema not supported
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

        # Do work only if this is a resource management VM
        if ($ResourceType -eq "Microsoft.Compute/virtualMachines")
        {
            # This is the VM
            Write-Verbose "This is a resource management VM." -Verbose

            # Authenticate to Azure with service principal and certificate and set subscription
            Write-Verbose "Authenticating to Azure with service principal and certificate" -Verbose
            $ConnectionAssetName = "AzureRunAsConnection"
            Write-Verbose "Get connection asset: $ConnectionAssetName" -Verbose
            $Conn = Get-AutomationConnection -Name $ConnectionAssetName
            if ($Conn -eq $null)
            {
                throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
            }
            Write-Verbose "Authenticating to Azure with service principal." -Verbose
            Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Verbose
            Write-Verbose "Setting subscription to work against: $SubId" -Verbose
            Set-AzureRmContext -SubscriptionId $SubId -ErrorAction Stop | Write-Verbose

            # Stop the VM
            Write-Verbose "Stopping the VM - $ResourceName - in resource group - $ResourceGroupName -" -Verbose
            Stop-AzureRmVM -Name $ResourceName -ResourceGroupName $ResourceGroupName -Force
            # [OutputType(PSAzureOperationResponse")]
        }
        else {
            # ResourceType not supported
            Write-Error "$ResourceType is not a supported resource type for this runbook."
        }
    }
    else {
        # The alert status was not 'Activated' so no action taken
        Write-Verbose ("No action taken. Alert status: " + $status) -Verbose
    }
}
else {
    # Error
    Write-Error "This runbook is meant to be started from an Azure alert webhook only."
}
```

## <a name="create-an-action-group"></a>Skapa en grupp

En grupp är en samling åtgärder som vidtas baserat på en avisering. Runbooks är bara en av de många åtgärder som är tillgängliga med åtgärdsgrupper.

1. I portalen, Välj **övervakaren**.

1. Välj **åtgärdsgrupper** under **inställningar**.

1. Välj **Lägg till grupp**, och Fyll i fälten.

1. Ange ett namn i rutan åtgärd gruppnamn och ange ett namn i rutan kort namn. Det korta namnet används i stället för en fullständig åtgärd gruppnamn när meddelanden som skickas med den här gruppen.

1. Den prenumeration autofills med din aktuella prenumeration. Den här prenumerationen är den som åtgärdsgruppen sparas.
Välj den resursgrupp som åtgärdsgruppen sparas.

I det här exemplet skapar du två åtgärder, en runbook-åtgärd och en Aviseringsåtgärd.

### <a name="runbook-action"></a>Runbook-åtgärden

Följande steg skapar du en runbook-åtgärden inom åtgärdsgruppen.

1. Under **åtgärder**, namnge åtgärden.

1. Välj **Automation-Runbook** för den **åtgärdstyp**.

1. Under **information** väljer, **redigera information**

1. På den **konfigurera Runbook** väljer **användare** under **Runbook-källa**.

1. Välj din **prenumeration** och **Automation-konto**, och väljer slutligen en runbook som du skapade i föregående steg kallas ”Stop AzureVmInResponsetoVMAlert”.

1. När du är klar klickar du på **OK**.

### <a name="notification-action"></a>Meddelandeåtgärd

Följande steg skapar du en Aviseringsåtgärd inom åtgärdsgruppen.

1. Under **åtgärder**, namnge åtgärden.

1. Välj **e-post** för den **åtgärdstyp**.

1. Under **information** väljer, **redigera information**

1. På den **e-post** anger du den e-postadressen för att meddela och klicka på **OK**. Det är bra att lägga till en e-postadress som runbook som en åtgärd som du meddelas när runbook startas. Din grupp bör se ut som följande bild:

   ![Lägg till grupp på sidan åtgärd](./media/automation-create-alert-triggered-runbook/add-action-group.png)

1. Klicka på **OK** att skapa åtgärdsgruppen.

Med åtgärdsgruppen skapas kan du skapa [loggen Aktivitetsaviseringar](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json) eller [nära realtid aviseringar](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md?toc=%2fazure%2fautomation%2ftoc.json#create-an-alert-rule-with-the-azure-portal) och använda den grupp du skapat.

## <a name="classic-alert"></a>Klassiska avisering

Klassiska aviseringar baseras på mått och gör inte använder åtgärdsgrupper, men runbook-åtgärder utifrån dem. Använd följande steg för att skapa en avisering om klassiska:

1. Välj **+ Lägg till mått avisering**.

1. Namnge aviseringen mått 'myVMCPUAlert' och ge en kort beskrivning för aviseringen.

1. Ange villkoret för aviseringen som ”större än” mått, ange tröskelvärde som ”10” och ange perioden som 'under de senaste 5 minuterna'.

1. Under **åtgärda**väljer **köra en runbook från denna avisering**

1. På den **konfigurera Runbook** väljer **användare** för **Runbook-källa**. Välj ditt automation-konto och den **stoppa AzureVmInResponsetoVMAlert** runbook. Klicka på **OK**, och klicka sedan på **OK** igen för att spara varningsregeln.

## <a name="next-steps"></a>Nästa steg

* Läs mer om hur du startar automation-runbooks med webhooks [startar en runbook från en webhook](automation-webhooks.md)
* Mer information om olika sätt att starta en runbook finns [starta en Runbook](automation-starting-a-runbook.md).
* Information om hur du skapar en avisering för aktiviteten loggen finns [och skapa aktiviteten Logga varningar](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)
* Så här skapar du en nära realtid avisering finns [skapa en aviseringsregel med Azure-portalen](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md?toc=%2fazure%2fautomation%2ftoc.json#create-an-alert-rule-with-the-azure-portal).