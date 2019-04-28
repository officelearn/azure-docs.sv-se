---
title: Distribuera Virtual Machine Scale Sets med Visual Studio | Microsoft Docs
description: Distribuera Virtual Machine Scale Sets med Visual Studio och Resource Manager-mall
services: virtual-machine-scale-sets
ms.custom: H1Hack27Feb2017
ms.workload: na
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ed0786b8-34b2-49a8-85b5-2a628128ead6
ms.service: virtual-machine-scale-sets
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2017
ms.author: manayar
ms.openlocfilehash: 3d472aeaae7e7f02eba58aadea1df042d6c0f27b
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62108076"
---
# <a name="how-to-create-a-virtual-machine-scale-set-with-visual-studio"></a>Så här skapar du en Virtual Machine Scale Sets med Visual Studio
Den här artikeln visar hur du distribuerar ett Azure Virtual Machine Scale Sets med hjälp av en Visual Studio-Resursgruppsdistribution.

[Azure Virtual Machine Scale Sets](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/) är en Azure Compute-resurs för att distribuera och hantera en uppsättning liknande virtuella datorer med automatisk skalning och belastningsutjämning. Du kan etablera och distribuera Virtual Machine Scale Sets med hjälp av [Azure Resource Manager-mallar](https://github.com/Azure/azure-quickstart-templates). Azure Resource Manager-mallar kan distribueras med hjälp av Azure CLI, PowerShell, REST och även direkt från Visual Studio. Visual Studio innehåller en uppsättning exempel på mallar, som kan distribueras som en del av ett projekt för distribution av Azure resursgrupper.

Azure-resursgrupp-distributioner är ett sätt att gruppera och publicera en uppsättning relaterade Azure-resurser i en enda Distributionsåtgärden. Du kan läsa mer om dem här: [Skapa och distribuera Azure-resursgrupper via Visual Studio](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## <a name="pre-requisites"></a>Förutsättningar
För att komma igång med att distribuera Virtual Machine Scale Sets i Visual Studio, behöver du följande:

* Visual Studio 2013 eller senare
* Azure SDK 2.7, 2.8 eller 2.9

>[!NOTE]
>Dessa instruktioner förutsätter att du använder Visual Studio med [Azure SDK 2.8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/).

## <a name="creating-a-project"></a>Skapa ett projekt
1. Skapa ett nytt projekt i Visual Studio genom att välja **fil | Ny | Projektet**.
   
    ![Ny fil][file_new]

2. Under **Visual C# | Molnet**, Välj **Azure Resource Manager** att skapa ett projekt för att distribuera en Azure Resource Manager-mall.
   
    ![Skapa projekt][create_project]

3. Välj Linux eller Windows VM Scale ange mall i listan med mallar.
   
   ![Välj mall][select_Template]

4. När projektet har skapats visas PowerShell-distributionsskript, en Azure Resource Manager-mall och en parameterfil för Virtual Machine Scale Sets.
   
    ![Solution Explorer][solution_explorer]

## <a name="customize-your-project"></a>Anpassa ditt projekt
Du kan nu redigera mallen för att anpassa den efter ditt programs behov, till exempel att lägga till VM-tilläggsegenskaper eller redigera regler för belastningsutjämning. Som standard konfigureras den skala ange mallar för virtuella datorer för att distribuera AzureDiagnostics tillägg, vilket gör det enkelt att lägga till regler för automatisk skalning. Den distribuerar också en belastningsutjämnare med en offentlig IP-adress som konfigurerats med inkommande NAT-regler. 

Belastningsutjämnaren kan du ansluta till Virtuella datorer med SSH (Linux) eller RDP (Windows). Klientdelens portintervall börjar vid 50000. För linux som det innebär att om du SSH till port 50000, du dirigeras till port 22 för den första virtuella datorn i Skalningsuppsättningen. Ansluta till port 50001 dirigeras till port 22 på den andra virtuella datorn och så vidare.

 Ett bra sätt att redigera dina mallar med Visual Studio är att använda JSON-disposition för att ordna parametrar, variabler och resurser. Med en förståelse för schemat kan Visual Studio peka ut fel i mallen innan du distribuerar den.

![JSON-Explorer][json_explorer]

## <a name="deploy-the-project"></a>Distribuera projektet
1. Distribuera Azure Resource Manager-mall för att skapa Virtual Machine Scale Sets-resurs. Högerklicka på projektnoden och välj **distribuera | Ny distribution**.
   
    ![Distribuera mallen][5deploy_Template]
    
2. Välj din prenumeration i dialogrutan ”Distribuera till resursgrupp”.
   
    ![Distribuera mallen][6deploy_Template]

3. Härifrån kan skapa du en Azure-resursgrupp för att distribuera din mall.
   
    ![Ny resursgrupp][new_resource]

4. Klicka sedan på **redigera parametrar** ange parametrar som skickas till din mall. Ange användarnamnet och lösenordet för operativsystem, vilket krävs för att skapa distributionen. Om du inte har PowerShell Tools för Visual Studio installerat, rekommenderar vi att kontrollera **spara lösenord** att undvika dolda PowerShell Kommandotolken eller använda [keyvault support](https://azure.microsoft.com/blog/keyvault-support-for-arm-templates/).
   
    ![Redigera parametrar][edit_parameters]

5. Klicka på **distribuera**. Den **utdata** visar förloppet för distributionen. Observera att åtgärden körs den **distribuera AzureResourceGroup.ps1** skript.
   
   ![Utdatafönstret][output_window]

## <a name="exploring-your-virtual-machine-scale-set"></a>Utforska Virtual Machine Scale Sets
När distributionen är klar kan du visa den nya Virtual Machine Scale Sets i Visual Studio **Cloud Explorer** (uppdatera listan). Cloud Explorer kan du hantera Azure-resurser i Visual Studio när du utvecklar program. Du kan också visa din Virtual Machine Scale Sets i den [Azure-portalen](https://portal.azure.com) och [Azure Resource Explorer](https://resources.azure.com/).

![Cloud Explorer][cloud_explorer]

 Portalen ger det bästa sättet att visuellt hantera Azure-infrastrukturen med en webbläsare medan Azure Resource Explorer ger ett enkelt sätt att utforska och felsöka Azure-resurser, vilket ger ett fönster i ”instance-view” och visar även PowerShell-kommandon för resurserna tittar du på.

## <a name="next-steps"></a>Nästa steg
När du har distribuerats Virtual Machine Scale Sets via Visual Studio kan du anpassa ditt projekt så att det passar dina program. Till exempel konfigurera automatisk skalning genom att lägga till en **Insights** resurs, lägger till infrastrukturen i din mall (t.ex. fristående virtuella datorer) eller distribuera program med hjälp av det anpassade skripttillägget. Bra exempel på mallar finns i den [Azure-Snabbstartsmallar](https://github.com/Azure/azure-quickstart-templates) GitHub-lagringsplatsen (Sök efter ”vmss”).

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
