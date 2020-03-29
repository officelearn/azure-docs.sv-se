---
title: Använda en avisering för att utlösa en Azure Automation-runbook
description: Lär dig hur du utlöser en runbook för att köras när en Azure-avisering höjs.
services: automation
ms.subservice: process-automation
ms.date: 04/29/2019
ms.topic: conceptual
ms.openlocfilehash: df28116c588ed77f02c78a42a85feb91ca339e7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75366708"
---
# <a name="use-an-alert-to-trigger-an-azure-automation-runbook"></a>Använda en avisering för att utlösa en Azure Automation-runbook

Du kan använda [Azure Monitor](../azure-monitor/overview.md?toc=%2fazure%2fautomation%2ftoc.json) för att övervaka mått och loggar på basnivå för de flesta tjänster i Azure. Du kan anropa Azure Automation-runbooks med hjälp av [åtgärdsgrupper](../azure-monitor/platform/action-groups.md?toc=%2fazure%2fautomation%2ftoc.json) eller genom att använda klassiska aviseringar för att automatisera uppgifter baserat på aviseringar. Den här artikeln visar hur du konfigurerar och kör en runbook med hjälp av aviseringar.

## <a name="alert-types"></a>Aviseringstyper

Du kan använda automatiseringskörningsböcker med tre varningstyper:

* Vanliga aviseringar
* Aktivitetsloggaviseringar
* Måttaviseringar i nära realtid

> [!NOTE]
> Det gemensamma varningsschemat standardiserar förbrukningsupplevelsen för varningsmeddelanden i Azure idag. Historiskt sett har de tre varningstyperna i Azure idag (mått, logg och aktivitetslogg) haft sina egna e-postmallar, webhook-scheman osv. Mer information finns i [Gemensamt varningsschema](../azure-monitor/platform/alerts-common-schema.md)

När en avisering anropar en runbook är det faktiska anropet en HTTP POST-begäran till webhooken. Brödtexten i POST-begäran innehåller ett JSON-formaterat objekt som har användbara egenskaper som är relaterade till aviseringen. I följande tabell visas länkar till nyttolastschemat för varje aviseringstyp:

|Varning  |Beskrivning|Schema för nyttolast  |
|---------|---------|---------|
|[Vanlig varning](../azure-monitor/platform/alerts-common-schema.md?toc=%2fazure%2fautomation%2ftoc.json)|Det gemensamma varningsschemat som standardiserar förbrukningsupplevelsen för varningsmeddelanden i Azure idag.|Gemensamt schema för varningsnyttolast|
|[Avisering om aktivitetslogg](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)    |Skickar ett meddelande när en ny händelse i Azure-aktivitetsloggen matchar specifika villkor. Till exempel när `Delete VM` en åtgärd inträffar i **myProductionResourceGroup** eller när en ny Azure Service Health-händelse med en **aktiv** status visas.| [Nyttolastschema för aktivitetsloggvarning](../azure-monitor/platform/activity-log-alerts-webhook.md)        |
|[Måttavisering i nära realtid](../azure-monitor/platform/alerts-metric-near-real-time.md?toc=%2fazure%2fautomation%2ftoc.json)    |Skickar ett meddelande snabbare än måttaviseringar när ett eller flera mätvärden på plattformsnivå uppfyller angivna villkor. Till exempel när värdet för **CPU %** på en virtuell dator är större än **90**och värdet för **Network In** är större än **500 MB** under de senaste 5 minuterna.| [Schema för nyttolast i realtid i realtid](../azure-monitor/platform/alerts-webhooks.md#payload-schema)          |

Eftersom de data som tillhandahålls av varje typ av avisering är olika hanteras varje aviseringstyp på olika sätt. I nästa avsnitt får du lära dig hur du skapar en runbook för att hantera olika typer av aviseringar.

## <a name="create-a-runbook-to-handle-alerts"></a>Skapa en runbook för att hantera aviseringar

Om du vill använda Automation med aviseringar behöver du en runbook som har logik som hanterar den aviserings-JSON-nyttolast som skickas till runbooken. Följande exempelkörningsbok måste anropas från en Azure-avisering.

Som beskrivs i föregående avsnitt har varje typ av avisering ett annat schema. Skriptet tar in webhook-data i `WebhookData` runbook-inmatningsparametern från en avisering. Skriptet utvärderar sedan JSON-nyttolasten för att avgöra vilken aviseringstyp som användes.

I det här exemplet används en avisering från en virtuell dator. Den hämtar VM-data från nyttolasten och använder sedan den informationen för att stoppa den virtuella datorn. Anslutningen måste ställas in i automationskontot där runbooken körs. När du använder aviseringar för att utlösa runbooks är det viktigt att kontrollera status för aviseringen i runbooken som utlöses. Runbooken utlöses varje gång aviseringen ändras. Aviseringar har flera tillstånd, de två `Activated` `Resolved`vanligaste tillstånden är och . Kontrollera om det här tillståndet finns i runbook-logiken för att säkerställa att runbooken inte körs mer än en gång. Exemplet i den här artikeln `Activated` visar bara hur du söker efter aviseringar.

Runbook använder **AzureRunAsConnection** [Run As-kontot](automation-create-runas-account.md) för att autentisera med Azure för att utföra hanteringsåtgärder mot den virtuella datorn.

Använd det här exemplet om du vill skapa en runbook som heter **Stop-AzureVmInResponsetoVMAlert**. Du kan ändra PowerShell-skriptet och använda det med många olika resurser.

1. Gå till ditt Azure Automation-konto.
2. Under **Processautomatisering**väljer du **Runbooks**.
3. Högst upp i listan över runbooks väljer du **+ Skapa en runbook**.
4. På sidan **Lägg till runbook** anger du **Stop-AzureVmInResponsetoVMAlert** för runbooknamnet. För runbook-typen väljer du **PowerShell**. Välj sedan **Create** (Skapa).  
5. Kopiera följande PowerShell-exempel till sidan **Redigera.**

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

6. Välj **Publicera** om du vill spara och publicera runbooken.

## <a name="create-the-alert"></a>Skapa aviseringen

Aviseringar använder åtgärdsgrupper, som är samlingar av åtgärder som utlöses av aviseringen. Runbooks är bara en av de många åtgärder som du kan använda med åtgärdsgrupper.

1. I ditt Automation-konto väljer du **Aviseringar** under **Övervakning**.
1. Välj **+ Ny aviseringsregel**.
1. Klicka på **Välj** under **Resurs**. På sidan **Välj en resurs** väljer du den virtuella datorn att avisera och klickar på **Klar**.
1. Klicka på **Lägg till villkor** under **skick**. Välj den signal du vill använda, till exempel **Procent CPU** och klicka på **Klar**.
1. På sidan **Konfigurera signallogik** anger du **tröskelvärdet** under **Varningslogik**och klickar på **Klar**.
1. Under **Åtgärdsgrupper**väljer du **Skapa nytt**.
1. På sidan **Lägg till åtgärdsgrupp** ger du din åtgärdsgrupp ett namn och ett kort namn.
1. Ge åtgärden ett namn. För åtgärdstypen väljer du **Automation Runbook**.
1. Välj **Redigera information**. Välj **Användare**under **Runbook-källa**på sidan **Konfigurera runbook.**  
1. Välj ditt **prenumerations-** och **automationskonto**och välj sedan **runbooken Stop-AzureVmInResponsetoVMAlert.**  
1. Välj **Ja** för **Aktivera det gemensamma varningsschemat**.
1. Om du vill skapa åtgärdsgruppen väljer du **OK**.

    ![Sidan Lägg till åtgärdsgrupp](./media/automation-create-alert-triggered-runbook/add-action-group.png)

    Du kan använda den här [åtgärdsgruppen i aktivitetsloggaviseringarna](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json) och [varningar i nära realtid](../azure-monitor/platform/alerts-overview.md?toc=%2fazure%2fautomation%2ftoc.json) som du skapar.

1. Lägg till ett varningsregelnamn och beskrivning under **Varningsinformation**och klicka på **Skapa varningsregel**.

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du startar en Automation-runbook med hjälp av en webhook finns i [Starta en runbook från en webhook](automation-webhooks.md).
* Mer information om olika sätt att starta en runbook finns i [Starta en runbook](automation-starting-a-runbook.md).
* Mer information om hur du skapar en aktivitetsloggavisering finns i [Skapa aktivitetsloggaviseringar](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json).
* Mer information om hur du skapar en varning i nära realtid finns [i Skapa en varningsregel i Azure-portalen](../azure-monitor/platform/alerts-metric.md?toc=/azure/azure-monitor/toc.json).
