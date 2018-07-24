---
title: Integrera Azure Automation med Event Grid | Microsoft Docs
description: Lär dig hur du lägger till en tagg automatiskt när en ny virtuell dator skapas och skicka ett meddelande till Microsoft Teams.
keywords: automation, runbook, teams, event grid, virtual machine, VM
services: automation
documentationcenter: ''
author: eamonoreilly
manager: ''
editor: ''
ms.service: automation
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/06/2017
ms.author: eamono
ms.openlocfilehash: 6270f8bad893798f46d8db91e7b1140b6a125350
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39049872"
---
# <a name="integrate-azure-automation-with-event-grid-and-microsoft-teams"></a>Integrera Azure Automation med Event Grid och Microsoft Teams

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Importera en Event Grid-exempel-runbook.
> * Skapa en valfri Microsoft Teams-webhook.
> * Skapa en webhook för runbooken.
> * Skapa en Event Grid-prenumeration.
> * Skapa en virtuell dator som utlöser runbooken.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

För slutföra den här självstudien krävs ett [Azure Automation-konto](../automation/automation-offering-get-started.md) för att lagra den runbook som utlöses från Azure Event Grid-prenumerationen.

* Modulen `AzureRM.Tags` måste läsas in på ditt Automation-konto. Se sidan om [hur du importerar moduler i Azure Automation](../automation/automation-update-azure-modules.md) för att lära dig hur du importerar moduler till Azure Automation.

## <a name="import-an-event-grid-sample-runbook"></a>Importera en Event Grid-exempel-runbook

1. Välj ditt Automation-konto och välj sidan **Runbooks**.

   ![Välja runbooks](./media/ensure-tags-exists-on-new-virtual-machines/select-runbooks.png)

2. Välj knappen **Sök i galleri**.

3. Sök efter **Event Grid** och välj **Integrera Azure Automation med Event Grid**.

    ![Importera galleri-runbook](media/ensure-tags-exists-on-new-virtual-machines/gallery-event-grid.png)

4. Välj **Importera** och ge den namnet **Watch-VMWrite**.

5. När den har importerats väljer du **Redigera** för att visa runbook-källan. Välj sedan knappen **Publicera**.

> [!NOTE]
> Rad 74 i skriptet måste ha få raden ändrad till `Update-AzureRmVM -ResourceGroupName $VMResourceGroup -VM $VM -Tag $Tag | Write-Verbose`. Parametern `-Tags` är nu `-Tag`.

## <a name="create-an-optional-microsoft-teams-webhook"></a>Skapa en valfri Microsoft Teams-webhook

1. I Microsoft Teams väljer du **Fler alternativ** intill kanalnamnet, och sedan väljer du **Anslutningsappar**.

    ![Microsoft Teams-anslutningar](media/ensure-tags-exists-on-new-virtual-machines/teams-webhook.png)

2. Bläddra igenom listan över anslutningsappar till **Inkommande Webhook** och välj **Lägg till**.

3. Ange **AzureAutomationIntegration** som namn och välj **Skapa**.

4. Kopiera webhooken till Urklipp och spara den. Webhooks-URL:en används för att skicka information till Microsoft Teams.

5. Välj **Klar** för att spara webhooken.

## <a name="create-a-webhook-for-the-runbook"></a>Skapa en webhook för runbooken

1. Öppna runbooken Watch-VMWrite.

2. Välj **Webhooks** och välj knappen **Lägg till Webhook**.

3. Ange **WatchVMEventGrid** som namn. Kopiera URL:en till Urklipp och spara den.

    ![Konfigurera namn på webhook](media/ensure-tags-exists-on-new-virtual-machines/copy-url.png)

4. Välj **Konfigurera parametrar och kör inställningar**, och ange en Microsoft Teams-webhook-URL för **CHANNELURL**. Lämna **WEBHOOKDATA** tomt.

    ![Konfigurera webhook-parametrar](media/ensure-tags-exists-on-new-virtual-machines/configure-webhook-parameters.png)

5. Välj **OK** för att skapa webhooken för Automation-runbook.


## <a name="create-an-event-grid-subscription"></a>Skapa en Event Grid-prenumeration
1. På översiktssidan **Automation-konto** väljer du **Event Grid**.

    ![Välja Event Grid](media/ensure-tags-exists-on-new-virtual-machines/select-event-grid.png)

2. Välj knappen **+ Händelseprenumeration**.

3. Konfigurera prenumerationen med följande information:

    *   Ange **AzureAutomation** som namn.
    *   I **Typ av ämne** väljer du **Azure-prenumerationer**.
    *   Avmarkera kryssrutan **Prenumerera på alla händelsetyper**.
    *   I **Händelsetyper** väljer du **Resource Write Success** (Resursskrivning lyckades).
    *   I **Slutpunkt för prenumerant** anger du webhook-URL:en för runbooken Watch-VMWrite runbook.
    *   I **Prefixfilter** anger du den prenumeration och den resursgrupp där du vill leta efter de nya virtuella datorerna som skapats. Det bör se ut så här: `/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.Compute/virtualMachines`

4. Välj **Skapa** för att spara Event Grid-prenumerationen.

## <a name="create-a-vm-that-triggers-the-runbook"></a>Skapa en virtuell dator som utlöser runbooken
1. Skapa en ny virtuell dator i den resursgrupp som du angav i prefixfiltret för Event Grid-prenumerationen.

2. Runbooken Watch-VMWrite ska anropas, och en ny tagg ska läggas till i den virtuella datorn.

    ![VM-tagg](media/ensure-tags-exists-on-new-virtual-machines/vm-tag.png)

3. Ett nytt meddelande skickas till Microsoft Teams-kanalen.

    ![Microsoft Teams-meddelande](media/ensure-tags-exists-on-new-virtual-machines/teams-vm-message.png)

## <a name="next-steps"></a>Nästa steg
I den här självstudien konfigurerar du integrering mellan Event Grid och Automation. Du har lärt dig att:

> [!div class="checklist"]
> * Importera en Event Grid-exempel-runbook.
> * Skapa en valfri Microsoft Teams-webhook.
> * Skapa en webhook för runbooken.
> * Skapa en Event Grid-prenumeration.
> * Skapa en virtuell dator som utlöser runbooken.

> [!div class="nextstepaction"]
> [Skapa och dirigera anpassade händelser med Event Grid](../event-grid/custom-event-quickstart.md)
