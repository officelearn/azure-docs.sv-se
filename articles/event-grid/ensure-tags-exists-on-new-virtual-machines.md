---
title: "Integrera Azure Automation med händelsen rutnät | Microsoft Docs"
description: "Lär dig hur du lägger till en tagg automatiskt när en ny virtuell dator skapas och skickas till Microsoft-Teams."
keywords: "Automation runbook, team, händelse rutnät, virtuell dator, VM"
services: automation
documentationcenter: 
author: eamonoreilly
manager: 
editor: 
ms.assetid: 0dd95270-761f-448e-af48-c8b1e82cd821
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2017
ms.author: eamono
ms.openlocfilehash: 6798f98755ad1d70d316b074643700f7b3e25ee7
ms.sourcegitcommit: 80eb8523913fc7c5f876ab9afde506f39d17b5a1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2017
---
# <a name="integrating-azure-automation-with-event-grid-and-microsoft-teams"></a>Integrera Azure Automation med händelsen rutnätet och Microsoft team

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Importera händelse rutnätet exempel-runbook.
> * Skapa en valfri webhook team.
> * Skapa en webhook för runbook.
> * Skapa en händelse rutnätet prenumeration.
> * Skapa virtuell dator som utlöser runbook.

## <a name="prerequisites"></a>Krav

Den här kursen krävs följande.
+ [Automation-konto](../automation/automation-offering-get-started.md) för den runbook som utlöses från rutnätet händelseprenumerationen.

## <a name="import-event-grid-sample-runbook"></a>Importera händelse rutnätet exempel-runbook
1.  Öppna Automation-kontot och klicka på sidan Runbooks.
2.  Klicka på knappen ”Bläddra galleriet”.
![Runbook-listan från Gränssnittet](media/ensure-tags-exists-on-new-virtual-machines/event-grid-runbook-list.png)
3.  Sök efter ”händelse rutnätet” och importera runbook i Automation-kontot.
![Importera galleriet runbook](media/ensure-tags-exists-on-new-virtual-machines/gallery-event-grid.png)
4.  Klicka på ”Edit” visa källan Runbook och klicka på knappen ”Publicera”.
![Publicera runbook från Gränssnittet](media/ensure-tags-exists-on-new-virtual-machines/publish-runbook.png)

## <a name="create-an-optional-teams-webhook"></a>Skapa en valfri team-webhook
1.  Välj fler alternativ (...) bredvid kanalnamnet på i Microsoft-Teams och välj kopplingar.
![Team-anslutningar](media/ensure-tags-exists-on-new-virtual-machines/teams-webhook.png)
2.  Rulla igenom listan över kopplingar till inkommande Webhook och klicka på Lägg till.
![Team webhook-anslutning](media/ensure-tags-exists-on-new-virtual-machines/select-teams-webhook.png)
3.  Ange AzureAutomationIntegration för namn och klicka på Skapa.
![Team-webhook](media/ensure-tags-exists-on-new-virtual-machines/configure-teams-webhook.png)
4.  Kopiera webhooken till Urklipp och spara den. Webhooksadressen används för att skicka information till Microsoft-Teams.
5.  Välj för att spara webhooken.

## <a name="create-a-webhook-for-the-runbook"></a>Skapa en webhook för runbook
1.  Öppna titta på VMWrite runbook.
2.  Klicka på Webhooks och knappen Lägg till webhook ![skapa webhooken](media/ensure-tags-exists-on-new-virtual-machines/add-webhook.png)
2.  Ange ”WatchVMEventGrid” för namn och kopiera Webbadressen till Urklipp och spara.
![Konfigurera namnet på webhook](media/ensure-tags-exists-on-new-virtual-machines/configure-webhook-name.png)
3.  Välj parametrar och ange Microsoft-Teams Webhooksadressen och lämna WEBHOOKDATA tomt.
![Konfigurera parametrarna för webhooken](media/ensure-tags-exists-on-new-virtual-machines/configure-webhook-parameters.png)
4.  Välj OK om du vill skapa den runbook Automation-webhooken.

## <a name="create-an-event-grid-subscription"></a>Skapa en händelse rutnätet prenumeration
1.  Klicka på händelsen rutnätssidan från översikt för Automation-konto.
![Händelselistan i rutnätet](media/ensure-tags-exists-on-new-virtual-machines/event-grid-list.png)
2.  Klicka på ny händelse prenumeration.
3.  Konfigurera prenumerationen med följande information:
    *   Ange AzureAutomation för namnet. 
    *   I avsnittet typ, väljer du Azure-prenumerationer.
    *   Avmarkera kryssrutan ”Abonnera på alla händelsetyper”
    *   Händelsetyper, Välj resurs skriva lyckades.
    *   Ange Webhooksadressen i prenumeranten slutpunkt för titta på VMWrite runbook.
    *   Ange prenumeration och resursgrupp som du vill söka efter nya virtuella datorer som skapats i prefixet Filter. Det bör se ut /subscriptions/124aa551-849d-46e4-a6dc-0bc4895422aB/resourcegroups/ContosoResourceGroup/providers/Microsoft.Compute/virtualMachines ![händelse rutnätet lista](media/ensure-tags-exists-on-new-virtual-machines/configure-event-grid-subscription.png)
6.  Klicka på ”Skapa” om du vill spara rutnätet händelseprenumerationen.

## <a name="create-vm-that-triggers-runbook"></a>Skapa virtuell dator som utlöser runbook
1.  Skapa en ny virtuell dator i resursgruppen som du angav i händelseloggen rutnätet prenumeration prefixet filter.
2.  Titta på VMWrite runbook ska anropas och en ny post läggs till den virtuella datorn.
![VMTag](media/ensure-tags-exists-on-new-virtual-machines/vm-tag.png)
3.  Ett nytt meddelande skickas till team-kanalen.

![Team-meddelande](media/ensure-tags-exists-on-new-virtual-machines/teams-vm-message.png)

## <a name="next-steps"></a>Nästa steg
I den här kursen kan du ställa in integration mellan händelse rutnätet och automatisering. Du har lärt dig att:

> [!div class="checklist"]
> * Importera händelse rutnätet exempel-runbook.
> * Skapa en valfri webhook team.
> * Skapa en webhook för runbook.
> * Skapa en händelse rutnätet prenumeration.
> * Skapa virtuell dator som utlöser runbook.

> [!div class="nextstepaction"]
> [Skapa och dirigera anpassade händelser med händelse rutnätet](../event-grid/custom-event-quickstart.md)
