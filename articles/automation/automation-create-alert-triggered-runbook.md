---
title: Använd en avisering för att utlösa en Azure Automation-runbook
description: Lär dig hur du utlöser en runbook ska köras när en Azure-avisering utlöses.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/29/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 5d8e7bba6d43ba1daa3173ce5d7e043e2310a482
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2019
ms.locfileid: "65229976"
---
# <a name="use-an-alert-to-trigger-an-azure-automation-runbook"></a>Använd en avisering för att utlösa en Azure Automation-runbook

Du kan använda [Azure Monitor](../azure-monitor/overview.md?toc=%2fazure%2fautomation%2ftoc.json) att övervaka beroende på mått och loggar för de flesta tjänster i Azure. Du kan anropa Azure Automation-runbooks med hjälp av [åtgärdsgrupper](../azure-monitor/platform/action-groups.md?toc=%2fazure%2fautomation%2ftoc.json) eller genom att använda klassiska aviseringar för att automatisera uppgifter baserat på varningar. Den här artikeln visar hur du konfigurerar och kör en runbook med hjälp av aviseringar.

## <a name="alert-types"></a>Aviseringstyper

Du kan använda automation-runbooks med fyra aviseringstyper:

* Vanligaste aviseringar
* Aktivitetsloggaviseringar
* Aviseringar i nära realtid mått

> [!NOTE]
> Det gemensamma schemat för avisering standardiserar förbrukning upplevelsen för aviseringar i Azure idag. Historiskt sett har tre aviseringstyperna i Azure i dag (mått, logg och aktivitetsloggen) hade sina egna e-postmallar, webhook-scheman, osv. Mer information finns i [gemensamma schemat för avisering](../azure-monitor/platform/alerts-common-schema.md)

När en avisering anropar en runbook, är det faktiska anropet en HTTP POST-begäran till webhooken. Brödtexten i POST-begäran innehåller ett JSON-formaterad objekt som har användbara egenskaper som är relaterade till aviseringen. I följande tabell visas länkar till nyttolast-schemat för varje typ av avisering:

|Avisering  |Beskrivning|Nyttolast-schema  |
|---------|---------|---------|
|[Vanliga avisering](../azure-monitor/platform/alerts-common-schema.md?toc=%2fazure%2fautomation%2ftoc.json)|Det gemensamma aviseringarna schemat som standardiserar förbrukning upplevelsen för aviseringar i Azure idag.|[Samma avisering nyttolast-schema](../azure-monitor/platform/alerts-common-schema-definitions.md?toc=%2fazure%2fautomation%2ftoc.json#sample-alert-payload)|
|[Aktivitetsloggavisering](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)    |Skickar ett meddelande när en ny händelse i aktivitetsloggen för Azure matchar specifika villkor. Till exempel när en `Delete VM` åtgärden sker i **myProductionResourceGroup** eller när en ny Azure Service Health-händelse med en **Active** status visas.| [Aviseringen nyttolast för aktivitetslogg](../azure-monitor/platform/activity-log-alerts-webhook.md)        |
|[Nära realtid metrisk varning](../azure-monitor/platform/alerts-metric-near-real-time.md?toc=%2fazure%2fautomation%2ftoc.json)    |Skickar ett meddelande snabbare än måttaviseringar när ett eller flera på plattformsnivå mått uppfyller angivna villkor. Till exempel när värdet för **CPU %** på en virtuell dator är större än **90**, och värdet för **nätverk i** är större än **500 MB** under senaste 5 : e minut.| [Nära realtid metrisk varning nyttolast schema](../azure-monitor/platform/alerts-webhooks.md#payload-schema)          |

Eftersom de data som tillhandahålls av varje typ av avisering är olika, hanteras annorlunda varje typ av avisering. I nästa avsnitt lär du dig att skapa en runbook för att hantera olika typer av aviseringar.

## <a name="create-a-runbook-to-handle-alerts"></a>Skapa en runbook för att hantera aviseringar

Om du vill använda Automation med aviseringar, behöver du en runbook som innehåller logik som hanterar avisering JSON-nyttolasten som skickas till runbooken. Följande exempel-runbook måste anropas från en Azure-avisering.

Enligt beskrivningen i föregående avsnitt, har varje typ av avisering ett annat schema. Skriptet använder i webhookdata i den `WebhookData` indataparameter för runbook från en avisering. Skriptet utvärderar sedan JSON-nyttolast för att avgöra vilken typ av avisering har använts.

Det här exemplet används en avisering från en virtuell dator. Virtuella datorns data hämtas från nyttolasten och använder sedan informationen för att stoppa den virtuella datorn. Anslutningen måste konfigureras i Automation-konto där runbook körs. När du utlöser runbooks med hjälp av aviseringar är det viktigt att kontrollera status för aviseringen i en runbook som utlöses. Runbook-flödet utlöser varje gång som ändrar status för aviseringen. Aviseringar har olika tillstånd, de två vanligaste statusvärdena är `Activated` och `Resolved`. Sök efter det här tillståndet i runbook-logik för att säkerställa att din runbook inte körs mer än en gång. Exemplet i den här artikeln visar hur du söker efter `Activated` aviseringar endast.

Används den **AzureRunAsConnection** [kör som-konto](automation-create-runas-account.md) att autentisera med Azure för att utföra management-åtgärd mot den virtuella datorn.

Använd det här exemplet för att skapa en runbook med namnet **Stop-AzureVmInResponsetoVMAlert**. Du kan ändra PowerShell-skriptet och använda den med många olika resurser.

1. Gå till ditt Azure Automation-konto.
2. Under **Processautomatisering**väljer **Runbooks**.
3. Överst i listan med runbooks, Välj **+ skapa en runbook**.
4. På den **Lägg till Runbook** anger **Stop-AzureVmInResponsetoVMAlert** för runbook-namn. Typ av runbook, Välj **PowerShell**. Välj sedan **Create** (Skapa).  
5. Kopiera följande PowerShell-exempel i den **redigera** sidan.

    ```powershell-interactive
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
        if ($schemaId -eq "azureMonitorCommonAlertSchema") {
            # This is the common Metric Alert schema (released March 2019)
            $Essentials = [object] ($WebhookBody.data).essentials
            # Get the first target only as this script doesn't handle multiple
            $alertTargetIdArray = (($Essentials.alertTargetIds)[0]).Split("/")
            $SubId = ($alertTargetIdArray)[2]
            $ResourceGroupName = ($alertTargetIdArray)[4]
            $ResourceType = ($alertTargetIdArray)[6] + "/" + ($alertTargetIdArray)[7]
            $ResourceName = ($alertTargetIdArray)[-1]
            $status = $Essentials.monitorCondition
        }
        elseif ($schemaId -eq "AzureMonitorMetricAlert") {
            # This is the near-real-time Metric Alert schema
            $AlertContext = [object] ($WebhookBody.data).context
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = $AlertContext.resourceName
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq "Microsoft.Insights/activityLogs") {
            # This is the Activity Log Alert schema
            $AlertContext = [object] (($WebhookBody.data).context).activityLog
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = (($AlertContext.resourceId).Split("/"))[-1]
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq $null) {
            # This is the original Metric Alert schema
            $AlertContext = [object] $WebhookBody.context
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = $AlertContext.resourceName
            $status = $WebhookBody.status
        }
        else {
            # Schema not supported
            Write-Error "The alert data schema - $schemaId - is not supported."
        }

        Write-Verbose "status: $status" -Verbose
        if (($status -eq "Activated") -or ($status -eq "Fired"))
        {
            Write-Verbose "resourceType: $ResourceType" -Verbose
            Write-Verbose "resourceName: $ResourceName" -Verbose
            Write-Verbose "resourceGroupName: $ResourceGroupName" -Verbose
            Write-Verbose "subscriptionId: $SubId" -Verbose

            # Determine code path depending on the resourceType
            if ($ResourceType -eq "Microsoft.Compute/virtualMachines")
            {
                # This is an Resource Manager VM
                Write-Verbose "This is an Resource Manager VM." -Verbose

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

                # Stop the Resource Manager VM
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
            # The alert status was not 'Activated' or 'Fired' so no action taken
            Write-Verbose ("No action taken. Alert status: " + $status) -Verbose
        }
    }
    else {
        # Error
        Write-Error "This runbook is meant to be started from an Azure alert webhook only."
    }
    ```

6. Välj **publicera** att spara och publicera en runbook.

## <a name="create-the-alert"></a>Skapa aviseringen

Aviseringar använda åtgärdsgrupper som är samlingar av åtgärder som utlöses av aviseringen. Runbooks är bara en av de många åtgärder som du kan använda med åtgärdsgrupper.

1. I ditt Automation-konto väljer **aviseringar** under **övervakning**.
1. Välj **+ ny aviseringsregel**.
1. Klicka på **Välj** under **Resource**. På den **väljer du en resurs** väljer du den virtuella datorn ska aviseringen ut från, och klicka på **klar**.
1. Klicka på **Lägg till villkor** under **villkor**. Välj signalen som du vill använda, till exempel **procent CPU** och klicka på **klar**.
1. På den **konfigurera signallogiken** anger du din **tröskelvärdet** under **Avisera logic**, och klicka på **klar**.
1. Välj **Skapa ny** under **Åtgärdsgrupper**.
1. På den **Lägg till åtgärdsgrupp** sidan, ge åtgärdsgruppen ett namn och ett kort namn.
1. Ge åtgärden ett namn. Åtgärdstyp, Välj **Automation-Runbook**.
1. Välj **redigera information**. På den **konfigurerar Runbook** sidan under **Runbook-källa**väljer **användaren**.  
1. Välj din **prenumeration** och **Automation-konto**, och välj sedan den **Stop-AzureVmInResponsetoVMAlert** runbook.  
1. Välj **Ja** för **aktivera det gemensamma schemat för avisering**.
1. Om du vill skapa åtgärdsgruppen, Välj **OK**.

    ![Lägg till åtgärd gruppsidan](./media/automation-create-alert-triggered-runbook/add-action-group.png)

    Du kan använda den här åtgärdsgruppen i den [aktivitetsloggaviseringar](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json) och [nära realtidsaviseringar](../azure-monitor/platform/alerts-overview.md?toc=%2fazure%2fautomation%2ftoc.json) som du skapar.

1. Under **aviseringsinformation**, lägga till en varningsregel namn och en beskrivning och klickar på **skapa varningsregel**.

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du startar en Automation-runbook med en webhook finns i [starta en runbook från en webhook](automation-webhooks.md).
* Mer information om olika sätt att starta en runbook finns [starta en runbook](automation-starting-a-runbook.md).
* Läs hur du skapar en aktivitetsloggavisering i [skapa aviseringar för aktivitetsloggen](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json).
* Läs hur du skapar en avisering om nästan i realtid i [skapar en aviseringsregel i Azure-portalen](../azure-monitor/platform/alerts-metric.md?toc=/azure/azure-monitor/toc.json).
