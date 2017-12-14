---
title: "Integrera Azure Automation med händelsen rutnät | Microsoft Docs"
description: "Lär dig hur du lägger till en tagg automatiskt när en ny virtuell dator skapas och skickas till Microsoft-Teams."
keywords: "Automation runbook, team, händelse rutnät, virtuell dator, VM"
services: automation
documentationcenter: 
author: eamonoreilly
manager: 
editor: 
ms.service: automation
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/06/2017
ms.author: eamono
ms.openlocfilehash: 9a4d6ecf19fc96a9c7b92cf246effbf3948fb478
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/07/2017
---
# <a name="integrate-azure-automation-with-event-grid-and-microsoft-teams"></a>Integrera Azure Automation med händelsen rutnätet och Microsoft team

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Importera en händelse rutnätet exempel-runbook.
> * Skapa en valfri Microsoft Teams webhook.
> * Skapa en webhook för runbook.
> * Skapa en händelse rutnätet prenumeration.
> * Skapa en virtuell dator som utlöser en runbook.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Krav

Att slutföra den här självstudiekursen en [Azure Automation-konto](../automation/automation-offering-get-started.md) krävs för att rymma den runbook som utlöses från Azure Event rutnätet prenumerationen.

## <a name="import-an-event-grid-sample-runbook"></a>Importera en händelse rutnätet exempel-runbook
1. Välj ditt Automation-konto och välj den **Runbooks** sidan.

   ![Välj runbooks](./media/ensure-tags-exists-on-new-virtual-machines/select-runbooks.png)

2. Välj den **Bläddra galleriet** knappen.

3. Sök efter **händelse rutnätet**, och välj **integrera Azure Automation med händelsen rutnätet**. 

    ![Importera galleriet runbook](media/ensure-tags-exists-on-new-virtual-machines/gallery-event-grid.png)

4. Välj **importera** och ger den namnet **titta på VMWrite**.

5. När den har importerats väljer **redigera** att visa runbook-källa. Välj sedan knappen **Publicera**.

## <a name="create-an-optional-microsoft-teams-webhook"></a>Skapa en valfri Microsoft Teams-webhook
1. Välj i Microsoft-Teams **fler alternativ** nästa till dess namn och välj sedan **kopplingar**.

    ![Microsoft-Teams anslutningar](media/ensure-tags-exists-on-new-virtual-machines/teams-webhook.png)

2. Bläddra i listan över kopplingar till **inkommande Webhook**, och välj **Lägg till**.

3. Ange **AzureAutomationIntegration** för namn och välj **skapa**.

4. Kopiera webhooken till Urklipp och spara den. Webhooksadressen används för att skicka information till Microsoft-Teams.

5. Välj **klar** att spara webhooken.

## <a name="create-a-webhook-for-the-runbook"></a>Skapa en webhook för runbook
1. Öppna titta på VMWrite runbook.

2. Välj **Webhooks**, och välj den **lägga till Webhook** knappen.

3. Ange **WatchVMEventGrid** för namnet. Kopiera Webbadressen till Urklipp och spara den.

    ![Konfigurera namnet på webhook](media/ensure-tags-exists-on-new-virtual-machines/copy-url.png)

4. Välj **konfigurera parametrar och körningsinställningar**, och ange Microsoft-Teams Webhooksadressen för **CHANNELURL**. Lämna **WEBHOOKDATA** tomt.

    ![Konfigurera parametrarna för webhooken](media/ensure-tags-exists-on-new-virtual-machines/configure-webhook-parameters.png)

5. Välj **OK** att skapa Automation-runbook webhook.


## <a name="create-an-event-grid-subscription"></a>Skapa en händelse rutnätet-prenumeration
1. På den **Automation-konto** översiktssidan väljer **händelse rutnätet**.

    ![Välj händelse rutnätet](media/ensure-tags-exists-on-new-virtual-machines/select-event-grid.png)

2. Välj den **+ händelseprenumerationen** knappen.

3. Konfigurera prenumerationen med följande information:

    *   Ange **AzureAutomation** för namnet.
    *   I **ämnestyp**väljer **Azure-prenumerationer**.
    *   Avmarkera den **prenumerera på alla händelsetyper** kryssrutan.
    *   I **händelsetyper**väljer **resurs skriva lyckade**.
    *   I **prenumeranten Endpoint**, ange Webhooksadressen för titta på VMWrite runbook.
    *   I **prefixet Filter**, anger du prenumerationen och resursgrupp där du vill söka efter nya virtuella datorer skapas. Det bör se ut:`/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.Compute/virtualMachines`

4. Välj **skapa** spara händelsen rutnätet prenumerationen.

## <a name="create-a-vm-that-triggers-the-runbook"></a>Skapa en virtuell dator som utlöser en runbook
1. Skapa en ny virtuell dator i resursgruppen som du angav i händelseloggen rutnätet prenumeration prefixet filter.

2. Titta på VMWrite runbook ska anropas och en ny post läggs till den virtuella datorn.

    ![VM-tagg](media/ensure-tags-exists-on-new-virtual-machines/vm-tag.png)

3. Ett nytt meddelande skickas till Microsoft-Teams-kanalen.

    ![Microsoft-Teams meddelande](media/ensure-tags-exists-on-new-virtual-machines/teams-vm-message.png)

## <a name="next-steps"></a>Nästa steg
I den här kursen kan du ställa in integration mellan händelse rutnätet och automatisering. Du har lärt dig att:

> [!div class="checklist"]
> * Importera en händelse rutnätet exempel-runbook.
> * Skapa en valfri Microsoft Teams webhook.
> * Skapa en webhook för runbook.
> * Skapa en händelse rutnätet prenumeration.
> * Skapa en virtuell dator som utlöser en runbook.

> [!div class="nextstepaction"]
> [Skapa och dirigera anpassade händelser med händelse rutnätet](../event-grid/custom-event-quickstart.md)
