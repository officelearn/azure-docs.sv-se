---
title: "Distribuera Skalningsuppsättning virtuell dator med hjälp av Visual Studio | Microsoft Docs"
description: "Distribuera Skalningsuppsättningar i virtuella datorer med hjälp av Visual Studio och Resource Manager-mall"
services: virtual-machine-scale-sets
documentationcenter: 
author: gbowerman
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: ed0786b8-34b2-49a8-85b5-2a628128ead6
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2017
ms.author: guybo
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 78a4b0c8d305f57f495402cecb92d18425ff6bff
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-create-a-virtual-machine-scale-set-with-visual-studio"></a>Så här skapar du en Virtual Machine Scale Set med Visual Studio
Den här artikeln visar hur du distribuerar ett Azure Virtual Machine Scale Set med hjälp av en Visual Studio resurs distribution.

[Azure Virtual Machine-Skalningsuppsättningar](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/) är en Azure Compute-resurs för att distribuera och hantera en samling liknande virtuella datorer med Autoskala och belastningsutjämning. Du kan etablera och distribuera Skalningsuppsättningar i virtuella datorer med hjälp av [Azure Resource Manager-mallar](https://github.com/Azure/azure-quickstart-templates). Azure Resource Manager-mallar som kan distribueras med hjälp av Azure CLI, PowerShell, REST och även direkt från Visual Studio. Visual Studio tillhandahåller en uppsättning exempel mallar, som kan distribueras som en del av en grupp distributionsprojekt för Azure-resurs.

Azure-resursgrupp distributioner är ett sätt att gruppera och publicera en uppsättning relaterade Azure-resurser i en enskild distribution-åtgärd. Du kan lära dig mer om de här: [skapa och distribuera Azure-resursgrupper via Visual Studio](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## <a name="pre-requisites"></a>Förutsättningar
Om du vill komma igång med att distribuera Skalningsuppsättningar i virtuella datorer i Visual Studio, behöver du följande:

* Visual Studio 2013 eller senare
* Azure SDK 2.7, 2.8 eller 2.9

>[!NOTE]
>Dessa instruktioner förutsätter att du använder Visual Studio med [Azure SDK 2.8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/).

## <a name="creating-a-project"></a>Skapa ett projekt
1. Skapa ett nytt projekt i Visual Studio genom att välja **filen | Nya | Projektet**.
   
    ![Ny fil][file_new]

2. Under **Visual C# | Molnet**, Välj **Azure Resource Manager** att skapa ett projekt för distribution av en Azure Resource Manager-mall.
   
    ![Skapa projekt][create_project]

3. Välj Linux- eller Windows Virtual Machine Scale ange mall från listan över mallar.
   
   ![Välj mall][select_Template]

4. När projektet har skapats visas PowerShell-skript för distribution, en Azure Resource Manager-mall och en parameterfil för Skalningsuppsättning i virtuell dator.
   
    ![Solution Explorer][solution_explorer]

## <a name="customize-your-project"></a>Anpassa ditt projekt
Du kan nu redigera mallen om du vill anpassa för programmets behov, till exempel lägga till egenskaperna för VM-tillägget eller redigera regler för belastningsutjämning. Som standard konfigureras den skala ange mallar för virtuella datorer för att distribuera AzureDiagnostics filnamnstillägget, vilket gör det lätt att lägga till automatiska regler. Den distribuerar också en belastningsutjämnare med en offentlig IP-adress som konfigurerats med ingående NAT-regler. 

Belastningsutjämnaren kan du ansluta till VM-instanser med SSH (Linux) eller RDP (Windows). Frontend portintervallet startar vid 50000. För linux detta innebär att om du SSH till port 50000, du dirigeras till den första virtuella datorn i Scale Set port 22. Ansluter till port 50001 dirigeras till port 22 på den andra virtuella datorn och så vidare.

 Ett bra sätt att redigera dina mallar i Visual Studio är att använda JSON-disposition för att ordna parametrar, variabler och resurser. Med en förståelse av schemat peka Visual Studio ut fel i mallen innan du distribuerar den.

![JSON-Explorer][json_explorer]

## <a name="deploy-the-project"></a>Distribuera projektet
1. Distribuera Azure Resource Manager-mall för att skapa den virtuella datorn resursen. Högerklicka på projektnoden och väljer **distribuera | Ny distribution**.
   
    ![Distribuera mallen][5deploy_Template]
    
2. Välj prenumerationen i dialogrutan ”Distribuera till resursgrupp”.
   
    ![Distribuera mallen][6deploy_Template]

3. Härifrån kan skapa du en Azure-resursgrupp för att distribuera mallen.
   
    ![Ny resursgrupp][new_resource]

4. Klicka sedan på **redigera parametrar** ange parametrar som skickas till mallen. Ange användarnamn och lösenord för Operativsystemet, vilket krävs för att skapa distributionen. Om du inte har PowerShell-verktyg för Visual Studio installerat, rekommenderas att kontrollera **spara lösenord** att undvika dolda PowerShell Kommandotolken eller använda [keyvault support](https://azure.microsoft.com/blog/keyvault-support-for-arm-templates/).
   
    ![Redigera parametrar][edit_parameters]

5. Klicka på **distribuera**. Den **utdata** fönstret visas förloppet för distributionen. Observera att instruktionen körs den **distribuera AzureResourceGroup.ps1** skript.
   
   ![Utdatafönstret][output_window]

## <a name="exploring-your-virtual-machine-scale-set"></a>Utforska dina virtuella datorns Skaluppsättning
När distributionen är klar kan du visa den nya virtuella datorn Scale Set i Visual Studio **Cloud Explorer** (uppdatera listan). Cloud Explorer kan du hantera Azure-resurser i Visual Studio när du utvecklar program. Du kan också visa din Virtual Machine Scale Set i den [Azure-portalen](https://portal.azure.com) och [resursutforskaren Azure](https://resources.azure.com/).

![Cloud Explorer][cloud_explorer]

 Portalen innehåller det bästa sättet att hantera dina Azure-infrastrukturen med en webbläsare visuellt när resursutforskaren i Azure ger ett enkelt sätt att utforska och felsöka Azure-resurser, vilket ger ett fönster i ”instansvyn” och också med PowerShell-kommandon för de resurser som du tittar på.

## <a name="next-steps"></a>Nästa steg
När du har distribuerats Skalningsuppsättningar i virtuella datorer via Visual Studio, kan du anpassa ditt projekt så att de passar dina krav för programmet. Till exempel konfigurera Autoskala genom att lägga till en **Insights** resurs, lägger till infrastrukturen i din mall (t.ex. fristående virtuella datorer) eller distribuera program med hjälp av tillägget för anpassat skript. Bra exempel mallar finns i den [Azure Quickstart mallar](https://github.com/Azure/azure-quickstart-templates) GitHub-lagringsplatsen (Sök efter ”vmss”).

[file_new]: ./media/virtual-machine-scale-sets-vs-create/1-FileNew.png
[create_project]: ./media/virtual-machine-scale-sets-vs-create/2-CreateProject.png
[select_Template]: ./media/virtual-machine-scale-sets-vs-create/3b-SelectTemplateLin.png
[solution_explorer]: ./media/virtual-machine-scale-sets-vs-create/4-SolutionExplorer.png
[json_explorer]: ./media/virtual-machine-scale-sets-vs-create/10-JsonExplorer.png
[5deploy_Template]: ./media/virtual-machine-scale-sets-vs-create/5-DeployTemplate.png
[6deploy_Template]: ./media/virtual-machine-scale-sets-vs-create/6-DeployTemplate.png
[new_resource]: ./media/virtual-machine-scale-sets-vs-create/7-NewResourceGroup.png
[edit_parameters]: ./media/virtual-machine-scale-sets-vs-create/8-EditParameter.png
[output_window]: ./media/virtual-machine-scale-sets-vs-create/9-Output.png
[cloud_explorer]: ./media/virtual-machine-scale-sets-vs-create/12-CloudExplorer.png
