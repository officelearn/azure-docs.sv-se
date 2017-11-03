---
title: " Åtgärda Azure VM aviseringar med Automation-Runbooks | Microsoft Docs"
description: "Den här artikeln visar hur du integrerar aviseringar för virtuell dator i Azure med Azure Automation-runbooks och åtgärda problem automatiskt"
services: automation
documentationcenter: 
author: eslesar
manager: jwhit
editor: tysonn
ms.assetid: 1f7baa7f-7283-4a4f-9385-3f5cd1062c7f
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/29/2017
ms.author: csand;magoedte
ms.openlocfilehash: 18cccc88ab74235722e2f4886671fc483ab67da8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-automation-scenario---remediate-azure-vm-alerts"></a>Azure Automation-scenario – åtgärda Virtuella Azure-aviseringar
Azure Automation och Azure virtuella datorer har släppt en ny funktion som gör att du kan konfigurera aviseringar för virtuell dator (VM) för att köra Automation-runbooks. Den här nya funktionen kan du automatiskt utföra vanliga åtgärder som svar på VM-aviseringar som startar om eller stoppar den virtuella datorn.

Tidigare under skapande av VM varningsregeln kunde du [ange en Automation-webhook](https://azure.microsoft.com/blog/using-azure-automation-to-take-actions-on-azure-alerts/) till en runbook för att köra runbook när aviseringen utlöses. Det måste dock du arbetar för att skapa runbook, webhooken på runbook, och sedan kopiera och klistra in webhooken när regeln skapades. Med den här nya versionen är processen enklare eftersom du kan välja en runbook från en lista direkt under skapande av varning och du kan välja ett Automation-konto som kör runbook eller enkelt skapa ett konto.

I den här artikeln visar vi du hur enkelt det är att skapa en virtuell dator i Azure-avisering och konfigurera en Automation-runbook ska köras när aviseringen utlöses. Exempelscenarier är att starta om en virtuell dator när minnesanvändningen överskrider vissa tröskelvärdet på grund av ett program på den virtuella datorn med en minnesläcka eller stoppa en virtuell dator när CPU-tid som användaren har mindre än 1% för senaste timmen och används inte. Vi förklarar också hur automatisk skapandet av en tjänstens huvudnamn i Automation-kontot förenklar användningen av runbooks i Azure avisering reparation.

## <a name="create-an-alert-on-a-vm"></a>Skapa en avisering på en virtuell dator
Utför följande steg om du vill konfigurera en avisering om du vill starta en runbook när dess tröskelvärde har uppfyllts.

> [!NOTE]
> Med den här versionen stöder vi bara V2 virtuella datorer och stöd för klassiska virtuella datorer läggs snart.  
> 
> 

1. Logga in på Azure portal och klicka på **virtuella datorer**.  
2. Välj någon av de virtuella datorerna.  
3. På skärmen VM i den **övervakning** klickar du på **Varna regler**.
4. På den **Varna regler** rutan klickar du på **Lägg till avisering**.

Detta öppnar den **lägga till en varningsregel** sida där du kan konfigurera villkor för aviseringen och välja mellan en eller flera av följande alternativ: skicka e-post till någon använder en webhook att vidarebefordra aviseringen till ett annat system och/eller kör en Automation-runbook som svar försök att åtgärda problemet.

## <a name="configure-a-runbook"></a>Konfigurera en runbook
Om du vill konfigurera en runbook ska köras när tröskelvärdet VM uppfylls, Välj **Automation-Runbook**. I den **konfigurerar runbook** fönstret kan du välja runbook körs och att köra runbook i Automation-kontot.

![Konfigurera en Automation-runbook och skapa ett nytt Automation-konto](media/automation-azure-vm-alert-integration/ConfigureRunbookNewAccount.png)

> [!NOTE]
> Den här versionen som du kan välja mellan tre runbooks som tjänsten tillhandahåller – starta om VM, stoppa VM eller ta bort virtuell dator (ta bort det).  Möjlighet att välja andra runbooks eller en av dina egna runbooks blir tillgänglig i framtida versioner.
> 
> 

![Runbooks kan välja mellan](media/automation-azure-vm-alert-integration/RunbooksToChoose.png)

När du väljer ett av tre tillgängliga runbooks, den **Automation-konto** listrutan visas och du kan välja ett automation-konto som runbook körs i. Runbooks måste köras i kontexten för en [Automation-konto](automation-security-overview.md) som finns i din Azure-prenumeration. Du kan välja ett Automation-konto som du redan skapat eller du kan ha ett nytt Automation-konto för dig.

Runbooks som tillhandahålls autentisera till Azure med ett huvudnamn för tjänsten. Om du väljer att köra runbook i en av dina befintliga Automation-konton, skapa vi automatiskt tjänsten huvudnamn. Om du vill skapa ett nytt Automation-konto kan skapas vi automatiskt kontot och tjänstens huvudnamn. I båda fallen skapas också två tillgångar i Automation-konto – ett med namnet certifikattillgång **AzureRunAsCertificate** och en anslutningstillgång med namnet **AzureRunAsConnection**. Använda runbooks **AzureRunAsConnection** att autentisera med Azure för att kunna utföra management-åtgärd mot den virtuella datorn.

> [!NOTE]
> Tjänstens huvudnamn skapas i prenumerationsomfattningen och deltagarrollen tilldelas. Den här rollen krävs för kontot som ska ha behörighet att köra Automation-runbooks för att hantera virtuella Azure-datorer.  Skapandet av ett Automaton konto eller tjänstens huvudnamn är en enstaka händelse. Du kan använda det kontot för att köra runbooks för andra Virtuella Azure-aviseringar när de skapas.
> 
> 

När du klickar på **OK** aviseringen har konfigurerats och om du har valt alternativet för att skapa ett nytt Automation-konto skapas tillsammans med tjänstens huvudnamn.  Det kan ta några sekunder att slutföra.  

![Runbook som konfigureras](media/automation-azure-vm-alert-integration/RunbookBeingConfigured.png)

När konfigurationen är klar kan du se namnet på runbook i den **lägga till en varningsregel** sidan.

![Runbook har konfigurerats](media/automation-azure-vm-alert-integration/RunbookConfigured.png)

Klicka på **OK** i den **lägga till en varningsregel** sidan.  Varningsregeln har skapats och aktivera om den virtuella datorn är i körningstillstånd.

### <a name="enable-or-disable-a-runbook"></a>Aktivera eller inaktivera en runbook
Om du har en runbook som konfigurerats för en avisering kan inaktivera du det utan att ta bort runbook-konfigurationen. På så sätt kan du behålla aviseringen körs och testa kanske vissa av Varningsregler och sedan senare återaktivera runbook.

## <a name="create-a-runbook-that-works-with-an-azure-alert"></a>Skapa en runbook som fungerar med en Azure avisering
När du väljer en runbook som en del av en Azure aviseringsregel behöver runbook logik i Hantera aviseringsdata som skickas till den.  När en runbook är konfigurerad i en aviseringsregel, har en webhook skapats för runbook; Denna webhook används sedan för att starta runbook varje gång aviseringen utlöses.  Det faktiska anropet till att starta runbook är en HTTP POST-begäran till Webhooksadressen. POST-begäran innehåller en JSON-formaterad objekt som innehåller användbar egenskaper som är relaterade till aviseringen.  Som du ser nedan innehåller information som subscriptionID, resourceGroupName, resourceName och resourceType aviseringsdata.

### <a name="example-of-alert-data"></a>Exempel på aviseringsdata
```
{
    "WebhookName": "AzureAlertTest",
    "RequestBody": "{
    \"status\":\"Activated\",
    \"context\": {
        \"id\":\"/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/microsoft.insights/alertrules/AlertTest\",
        \"name\":\"AlertTest\",
        \"description\":\"\",
        \"condition\": {
            \"metricName\":\"CPU percentage guest OS\",
            \"metricUnit\":\"Percent\",
            \"metricValue\":\"4.26337916666667\",
            \"threshold\":\"1\",
            \"windowSize\":\"60\",
            \"timeAggregation\":\"Average\",
            \"operator\":\"GreaterThan\"},
        \"subscriptionId\":\<subscriptionID> \",
        \"resourceGroupName\":\"TestResourceGroup\",
        \"timestamp\":\"2016-04-24T23:19:50.1440170Z\",
        \"resourceName\":\"TestVM\",
        \"resourceType\":\"microsoft.compute/virtualmachines\",
        \"resourceRegion\":\"westus\",
        \"resourceId\":\"/subscriptions/<subscriptionId>/resourceGroups/TestResourceGroup/providers/Microsoft.Compute/virtualMachines/TestVM\",
        \"portalLink\":\"https://portal.azure.com/#resource/subscriptions/<subscriptionId>/resourceGroups/TestResourceGroup/providers/Microsoft.Compute/virtualMachines/TestVM\"
        },
    \"properties\":{}
    }",
    "RequestHeader": {
        "Connection": "Keep-Alive",
        "Host": "<webhookURL>"
    }
}
```

När Automation-webhook-tjänst tar emot HTTP POST extraherar aviseringsdata och skickar den till en runbook i WebhookData runbookinmatningsparameter.  Nedan visas en exempel-runbook som visar hur du använder parametern WebhookData och extrahera aviseringsdata och hantera Azure-resurs som utlöste aviseringen.

### <a name="example-runbook"></a>Exempel-runbook
```
#  This runbook restarts an ARM (V2) VM in response to an Azure VM alert.

[OutputType("PSAzureOperationResponse")]

param ( [object] $WebhookData )

if ($WebhookData)
{
    # Get the data object from WebhookData
    $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

    # Assure that the alert status is 'Activated' (alert condition went from false to true)
    # and not 'Resolved' (alert condition went from true to false)
    if ($WebhookBody.status -eq "Activated")
    {
        # Get the info needed to identify the VM
        $AlertContext = [object] $WebhookBody.context
        $ResourceName = $AlertContext.resourceName
        $ResourceType = $AlertContext.resourceType
        $ResourceGroupName = $AlertContext.resourceGroupName
        $SubId = $AlertContext.subscriptionId

        # Assure that this is the expected resource type
        Write-Verbose "ResourceType: $ResourceType"
        if ($ResourceType -eq "microsoft.compute/virtualmachines")
        {
            # This is an ARM (V2) VM

            # Authenticate to Azure with service principal and certificate
            $ConnectionAssetName = "AzureRunAsConnection"
            $Conn = Get-AutomationConnection -Name $ConnectionAssetName
            if ($Conn -eq $null) {
                throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
            }
            Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Verbose
            Set-AzureRmContext -SubscriptionId $SubId -ErrorAction Stop | Write-Verbose

            # Restart the VM
            Restart-AzureRmVM -Name $ResourceName -ResourceGroupName $ResourceGroupName
        } else {
            Write-Error "$ResourceType is not a supported resource type for this runbook."
        }
    } else {
        # The alert status was not 'Activated' so no action taken
        Write-Verbose ("No action taken. Alert status: " + $WebhookBody.status)
    }
} else {
    Write-Error "This runbook is meant to be started from an Azure alert only."
}
```

## <a name="summary"></a>Sammanfattning
När du konfigurerar en avisering på en Azure VM, nu har du möjlighet att enkelt konfigurera en Automation-runbook för att automatiskt utföra Reparationsåtgärd när aviseringen utlöses. Den här versionen kan du från runbooks starta om, stoppa eller ta bort en virtuell dator beroende på aviseringen scenario. Detta är bara början för att aktivera scenarier där du kan bestämma åtgärder (meddelande, felsökning, reparation) som ska utföras automatiskt när en avisering utlöses.

## <a name="next-steps"></a>Nästa steg
* Information om hur du kommer igång med grafiska runbooks finns i [Min första grafisk runbook](automation-first-runbook-graphical.md)
* Se hur du kommer igång med runbooks baserade på PowerShell-arbetsflöden i [Min första PowerShell-arbetsflödesbaserade runbook](automation-first-runbook-textual.md)
* Mer information om typer av runbooks, och om deras fördelar och begränsningar, finns i [Typer av Azure Automation-runbooks](automation-runbook-types.md)

