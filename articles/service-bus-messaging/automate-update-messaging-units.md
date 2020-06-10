---
title: Azure Service Bus – uppdatera meddelande enheter automatiskt
description: Den här artikeln visar hur du kan använda en Azure Automation Runbook för att automatiskt uppdatera meddelande enheter i ett Service Bus namn område.
services: service-bus-messaging
ms.service: service-bus-messaging
documentationcenter: ''
author: spelluru
ms.topic: how-to
ms.date: 05/14/2020
ms.author: spelluru
ms.openlocfilehash: db86f22247b757ed2f1cc2c168421346ad90a1fc
ms.sourcegitcommit: 5a8c8ac84c36859611158892422fc66395f808dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/10/2020
ms.locfileid: "84657653"
---
# <a name="automatically-update-messaging-units-of-an-azure-service-bus-namespace"></a>Uppdatera meddelande enheter automatiskt i ett Azure Service Bus namn område 
Den här artikeln visar hur du automatiskt kan uppdatera [meddelande enheter](service-bus-premium-messaging.md) för en Service Bus namnrymd baserat på resurs (CPU eller minne) användning. 

Exemplet i den här artikeln visar hur du ökar meddelande enheter för ett Service Bus-namnområde när processor användningen för namn området går över 75%. De övergripande stegen är:

1. Skapa en Azure Automation Runbook med PowerShell-skript som skalar upp (ökar) meddelande enheter för ett Service Bus namn område. 
2. Skapa en avisering om processor användning i Service Bus namn området, som anropar PowerShell-skriptet när namn områdets CPU-användning är över 75%. 

> [!IMPORTANT]
> Den här artikeln gäller endast **Premium** nivån för Azure Service Bus. 


## <a name="create-a-service-bus-namespace"></a>Skapa ett namnområde för Service Bus
Skapa en Premier-Tier Service Bus-namnrymd. Skapa namn området genom att följa stegen i avsnittet [skapa ett namn område i Azure Portal](service-bus-quickstart-portal.md#create-a-namespace-in-the-azure-portal) artikeln. 

## <a name="create-an-azure-automation-account"></a>Skapa ett Azure Automation-konto
Skapa ett Azure Automation-konto genom att följa anvisningarna i artikeln [skapa ett Azure Automation konto](../automation/automation-quickstart-create-account.md) . 

## <a name="import-azservice-module-from-gallery"></a>Importera AZ. service module från galleriet
Importera `Az.Accounts` och `Az.ServiceBus` moduler från galleriet till Automation-kontot. `Az.ServiceBus`Modulen är beroende av `Az.Accounts` modulen, så den måste installeras först. 

Stegvisa instruktioner finns i [Importera en modul från modul-galleriet](../automation/automation-runbook-gallery.md#import-a-module-from-the-module-gallery-with-the-azure-portal).

## <a name="create-and-publish-a-powershell-runbook"></a>Skapa och publicera en PowerShell-Runbook

1. Skapa en PowerShell-Runbook genom att följa anvisningarna i artikeln [skapa en PowerShell-Runbook](../automation/automation-quickstart-create-runbook.md) . 

    Här är ett exempel på ett PowerShell-skript som du kan använda för att öka meddelande enheter för ett Service Bus-namnområde. Det här PowerShell-skriptet i en Automation-Runbook ökar MUs från 1 till 2, 2 till 4 eller 4 till 8. De tillåtna värdena för den här egenskapen är: 1, 2, 4 och 8. Du kan skapa en annan Runbook för att minska meddelande enheterna.

    Parametrarna **namespaceName** och **resourceGroupName** används för att testa skriptet separat från aviserings scenariot. 
    
    Parametern **WebHookData** är för aviseringen att skicka information, till exempel resurs gruppens namn, resurs namnet osv. vid körning. 

    ```powershell
    [OutputType("PSAzureOperationResponse")]
    param
    (
        [Parameter (Mandatory=$false)]
        [object] $WebhookData,
    
        [Parameter (Mandatory = $false)]
        [String] $namespaceName,
    
        [Parameter (Mandatory = $false)]
        [String] $resourceGroupName
    )
    
    
    if ($WebhookData)
    {
        # Get the data object from WebhookData
        $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)
    
        # Get the alert schema ID
        $schemaId = $WebhookBody.schemaId

        # If it's a metric alert
        if ($schemaId -eq "AzureMonitorMetricAlert") {

            # Get the resource group name from the alert context
            $resourceGroupName = $WebhookBody.resourceGroupName
            
            # Get the namespace name from the alert context
            $namespaceName = $WebhookBody.resourceName
        }
    }
    
    # Connect to Azure account
    $connection = Get-AutomationConnection -Name AzureRunAsConnection
    
    while(!($connectionResult) -And ($logonAttempt -le 10))
    {
        $LogonAttempt++
        # Logging in to Azure...
        $connectionResult =    Connect-AzAccount `
                                    -ServicePrincipal `
                                    -Tenant $connection.TenantID `
                                    -ApplicationId $connection.ApplicationID `
                                    -CertificateThumbprint $connection.CertificateThumbprint
    
        Start-Sleep -Seconds 30
    }
    
    # Get the current capacity (number of messaging units) of the namespace
    $sbusns=Get-AzServiceBusNamespace `
        -Name $namespaceName `
        -ResourceGroupName $resourceGroupName
    
    $currentCapacity = $sbusns.Sku.Capacity
    
    # Increase the capacity
    # Capacity can be one of these values: 1, 2, 4, 8
    if ($currentCapacity -eq 1) {
        $newMU = 2
    }
    elseif ($currentCapacity -eq 2) {
        $newMU = 4
    }
    elseif ($currentCapacity -eq 4) {
        $newMU = 8    
    }
    else {
    
    }
    
    # Update the capacity of the namespace
    Set-AzServiceBusNamespace `
            -Location eastus `
            -SkuName Premium `
            -Name $namespaceName `
            -SkuCapacity $newMU `
            -ResourceGroupName $resourceGroupName

    ```
2. [Testa arbets boken](../automation/manage-runbooks.md#test-a-runbook) genom att ange värden för parametrarna **namespaceName** och **resourceGroupName** . Bekräfta att meddelande enheterna i namn området har uppdaterats. 
3. När du har testat kan du [publicera arbets boken](..//automation/manage-runbooks.md#publish-a-runbook) så att den är tillgänglig för att lägga till som en åtgärd för en avisering i namn området senare. 

## <a name="create-an-alert-on-the-namespace-to-trigger-the-runbook"></a>Skapa en avisering i namn området för att utlösa Runbook
Se [använda en avisering för att utlösa en Azure Automation Runbook](../automation/automation-create-alert-triggered-runbook.md) -artikel för att konfigurera en avisering på ditt Service Bus namn område för att utlösa den Automation-Runbook som du skapade. Du kan till exempel skapa en avisering om **processor användning per namnrymd** eller **minnes storlek per namn områdes** mått och lägga till en åtgärd för att utlösa den Automation-Runbook som du skapade. Mer information om dessa mått finns i [användnings statistik för resursanvändningen](service-bus-metrics-azure-monitor.md#resource-usage-metrics).

Följande procedur visar hur du skapar en avisering som utlöser Automation-runbooken när namn områdets **CPU-användning** går över **75%**.

1. På sidan **Service Bus namn område** för ditt namn område väljer du **aviseringar** på den vänstra menyn och väljer sedan **+ ny varnings regel** i verktygsfältet. 
    
    ![Sidan aviseringar – knappen Ny varnings regel](./media/automate-update-messaging-units/alerts-page.png)
2. Klicka på **Välj villkor**på sidan **skapa aviserings regel** . 

    ![Sidan skapa aviserings regel – Välj villkor](./media/automate-update-messaging-units/alert-rule-select-condition.png) 
3. På sidan **Konfigurera signal logik** väljer du **processor** för signalen. 

    ![Konfigurera signal logik – Välj processor](./media/automate-update-messaging-units/configure-signal-logic.png)
4. Ange ett **tröskelvärde** (i det här exemplet är det **75%**) och välj **färdig**. 

    ![Konfigurera CPU-signal](./media/automate-update-messaging-units/cpu-signal-configuration.png)
5. Gå nu till **sidan Skapa avisering**och klicka på **Välj åtgärds grupp**.
    
    ![Välj åtgärds grupp](./media/automate-update-messaging-units/select-action-group-button.png)
6. Välj knappen **skapa åtgärds grupp** i verktygsfältet. 

    ![Knappen Skapa åtgärds grupp](./media/automate-update-messaging-units/create-action-group-button.png)
7. Utför följande steg på sidan **Lägg till åtgärds grupp** :
    1. Ange ett **namn** på åtgärds gruppen. 
    2. Ange ett **kort namn** för åtgärds gruppen.
    3. Välj den **prenumeration** där du vill skapa den här åtgärds gruppen.
    4. Välj **resurs gruppen**. 
    5. I avsnittet **åtgärder** anger **du ett namn för åtgärden**och väljer **Automation Runbook** för **Åtgärds typ**. 

        ![Sidan Lägg till åtgärds grupp](./media/automate-update-messaging-units/add-action-group-page.png)
8. Utför följande steg på sidan **Konfigurera Runbook** :
    1. För **Runbook-källa**väljer du **användare**. 
    2. För **prenumeration**väljer du din Azure- **prenumeration** som innehåller Automation-kontot. 
    3. För **Automation-konto**väljer du ditt **Automation-konto**.
    4. För **Runbook**väljer du din Runbook. 
    5. Välj **OK** på sidan **Konfigurera Runbook** . 
        ![Konfigurera Runbook](./media/automate-update-messaging-units/configure-runbook.png)
9. Välj **OK** på sidan **Lägg till åtgärds grupp** . 
5. På sidan **skapa aviserings regel** anger du ett **namn för regeln**och väljer sedan **skapa aviserings regel**. 
    ![Skapa aviseringsregel](./media/automate-update-messaging-units/create-alert-rule.png)

    > [!NOTE]
    > När namn områdets CPU-användning är över 75, utlöser aviseringen Automation-runbooken, vilket ökar meddelande enheter för Service Bus namn området. På samma sätt kan du skapa en avisering för en annan Automation-Runbook, vilket minskar meddelande enheterna om CPU-användningen för namn området går under 25. 

## <a name="next-steps"></a>Nästa steg
Läs mer om meddelande enheter i [Premium Messaging](service-bus-premium-messaging.md)
