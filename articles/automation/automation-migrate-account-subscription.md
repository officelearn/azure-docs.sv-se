---
title: Migrera Automation-konto och resurser | Microsoft Docs
description: "Den här artikeln beskriver hur du flyttar ett Automation-konto i Azure Automation och associerade resurser från en prenumeration till en annan."
services: automation
documentationcenter: 
author: georgewallace
manager: jwhit
editor: tysonn
ms.assetid: 9c2db4a2-f324-48dc-8ce7-3343bf7230d5
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/29/2017
ms.author: magoedte
ms.openlocfilehash: c13ee767cc2a1fb7880e6d0491cd6a247c737c13
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2017
---
# <a name="migrate-automation-account-and-resources"></a>Migrera Automation-konto och resurser
För Automation-konton och dess associerade resurser (det vill säga tillgångar, runbooks, moduler, etc.) som du har skapat i Azure-portalen och vill migrera från en resursgrupp till en annan eller från en prenumeration till en annan kan göra du det enkelt med den [flyttar resurser](../azure-resource-manager/resource-group-move-resources.md) funktion i Azure-portalen. Men innan du fortsätter med den här åtgärden, bör du först granska följande [checklistan innan du flyttar resurser](../azure-resource-manager/resource-group-move-resources.md#checklist-before-moving-resources) och dessutom följande lista för automatisering.   

1. Mål-prenumeration/resursgrupp måste vara i samma region som källa.  D.v.s. kan Automation-konton inte flyttas över regioner.
2. När du flyttar resurser (t.ex. runbooks, jobb, etc.), låst både gruppen och målgruppen under åtgärden. Skriva och ta bort blockeras på grupper tills flyttningen är klar.  
3. Alla runbooks eller variabler som refererar till en resurs eller prenumerations-ID från den befintliga prenumerationen ska uppdateras när migreringen är klar.   

> [!NOTE]
> Den här funktionen stöder inte flytta klassisk automation resurser.
>
>

## <a name="to-move-the-automation-account-using-the-portal"></a>Att flytta Automation-konto med hjälp av portalen
1. Från ditt Automation-konto klickar du på **flytta** överst på sidan.<br> ![Flytta alternativet](media/automation-migrate-account-subscription/automation-menu-move.png)<br>
2. På den **flyttar resurser** rutan, meddelande som det innehåller resurser som rör både Automation-konto och resurs-grupper.  Välj den **prenumeration** och **resursgruppen** från listrutorna eller Välj alternativet **skapa en ny resursgrupp** och ange namn på en ny resursgrupp i fältet har angetts.  
3. Granska och markera kryssrutan för att bekräfta att du *förstå verktyg och skript kommer att behöva uppdateras för att använda ny resurs-ID när resurser har flyttats* och klicka sedan på **OK**.<br> ![Flytta fönstret för resurser](media/automation-migrate-account-subscription/automation-move-resources-blade.png)<br>   

Den här åtgärden kan ta flera minuter att slutföra.  I **meddelanden**, visas statusen för alla åtgärder som sker - verifieringen, migrering, och sedan slutligen när den har slutförts.     

## <a name="to-move-the-automation-account-using-powershell"></a>Att flytta Automation-konto med hjälp av PowerShell
Flytta befintliga Automation resurser till en annan resursgrupp eller prenumeration genom att använda den **Get-AzureRmResource** för att hämta specifika Automation-kontot och sedan **flytta AzureRmResource** att Utför övergången.

Det första exemplet visar hur du flyttar ett Automation-konto till en ny resursgrupp.

   ```
    $resource = Get-AzureRmResource -ResourceName "TestAutomationAccount" -ResourceGroupName "ResourceGroup01"
    Move-AzureRmResource -ResourceId $resource.ResourceId -DestinationResourceGroupName "NewResourceGroup"
   ```

När du kör kodexemplet ovan, uppmanas du att verifiera att du vill utföra den här åtgärden.  När du klickar på **Ja** och skript kan fortsätta, du får inga meddelanden när den utför migreringen.  

Om du vill flytta till en ny prenumeration, innehåller ett värde för den *DestinationSubscriptionId* parameter.

   ```
    $resource = Get-AzureRmResource -ResourceName "TestAutomationAccount" -ResourceGroupName "ResourceGroup01"
    Move-AzureRmResource -ResourceId $resource.ResourceId -DestinationResourceGroupName "NewResourceGroup" -DestinationSubscriptionId "SubscriptionId"
   ```

Som i föregående exempel uppmanas att bekräfta flytten.  

## <a name="next-steps"></a>Nästa steg
* Mer information om resurserna flyttas till en ny resursgrupp eller prenumeration finns [flytta resurser till en ny resursgrupp eller prenumeration](../azure-resource-manager/resource-group-move-resources.md)
* Mer information om rollbaserad åtkomstkontroll i Azure Automation finns i [Rollbaserad åtkomstkontroll i Azure Automation](automation-role-based-access-control.md).
* Mer information om PowerShell-cmdletar för att hantera din prenumeration, se [med hjälp av Azure PowerShell med Resource Manager](../azure-resource-manager/powershell-azure-resource-manager.md)
* Mer information om Företagsportalen funktioner för att hantera din prenumeration, se [med Azure-portalen för att hantera resurser](../azure-resource-manager/resource-group-portal.md).
