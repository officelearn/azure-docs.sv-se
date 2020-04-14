---
title: Distribuera skaluppsättning för virtuella datorer med Visual Studio
description: Distribuera skaluppsättningar för virtuella datorer med Visual Studio och en Resource Manager-mall
ms.custom: vs-azure, H1Hack27Feb2017
ms.workload: azure-vs
author: mimckitt
tags: azure-resource-manager
ms.assetid: ed0786b8-34b2-49a8-85b5-2a628128ead6
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: mimckitt
ms.openlocfilehash: adc91d5f4f79be8a85dfed7d10a882493f6427b0
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273349"
---
# <a name="how-to-create-a-virtual-machine-scale-set-with-visual-studio"></a>Skapa en skaluppsättning för virtuella datorer med Visual Studio

Den här artikeln visar hur du distribuerar en Azure Virtual Machine Scale Set med hjälp av en Visual Studio Resource Group-distribution.

[Azure Virtual Machine Scale Sets](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/) är en Azure Compute-resurs för att distribuera och hantera en samling av liknande virtuella datorer med automatisk skalning och belastningsutjämning. Du kan etablera och distribuera skalningsuppsättningar för virtuella datorer med hjälp av [Azure Resource Manager-mallar](https://github.com/Azure/azure-quickstart-templates). Azure Resource Manager-mallar kan distribueras med Azure CLI, PowerShell, REST och även direkt från Visual Studio. Visual Studio innehåller en uppsättning exempelmallar som du kan distribuera som en del av ett Distributionsprojekt för Azure Resource Group.

Azure Resource Group-distributioner är ett sätt att gruppera och publicera en uppsättning relaterade Azure-resurser i en enda distributionsåtgärd. Mer information finns i [Skapa och distribuera Azure-resursgrupper via Visual Studio](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## <a name="prerequisites"></a>Krav

För att komma igång med att distribuera skaluppsättningar för virtuella datorer i Visual Studio behöver du följande förutsättningar:

* Visual Studio 2013 eller senare
* Azure SDK 2.7, 2.8 eller 2.9

>[!NOTE]
>I den här artikeln används Visual Studio 2019 med [Azure SDK 2.8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/).

## <a name="create-a-project"></a>Skapa ett projekt<a name="creating-a-project"></a> 

1. Öppna Visual Studio och välj **Skapa ett nytt projekt**.

1. I **Skapa ett nytt projekt**väljer du Azure Resource **Group** för C# och väljer sedan **Nästa**.

1. Ange ett namn **i Konfigurera det nya projektet**och välj **Skapa**.

    ![Namnge och skapa projektet](media/virtual-machine-scale-sets-vs-create/configure-azure-resource-group.png)

1. Välj antingen **skaluppsättningen** för Windows Virtual Machine eller **Linux Virtual Machine Scale Set** i listan över mallar. Välj **OK**.

   ![Välj en mall för virtuella datorer](media/virtual-machine-scale-sets-vs-create/select-vm-template.png)

När du har skapat projektet innehåller **Solution Explorer** ett PowerShell-distributionsskript, en Azure Resource Manager-mall och en parameterfil för skalningsuppsättningen för den virtuella datorn.

## <a name="customize-your-project"></a>Anpassa projektet

Nu kan du redigera mallen för att anpassa den efter programmets behov. Du kan lägga till egenskaper för tillägg till virtuella datorer eller redigera belastningsutjämningsregler. Som standard är mallarna för skalningsuppsättning för virtuella datorer konfigurerade för att distribuera **AzureDiagnostics-tillägget,** vilket gör det enkelt att lägga till regler för automatisk skalning. Mallarna distribuerar också en belastningsutjämnare med en offentlig IP-adress, konfigurerad med inkommande NAT-regler.

Med belastningsutjämnaren kan du ansluta till instanserna för virtuella datorer med SSH (Linux) eller RDP (Windows). Den främre portområdet börjar på 50000. Om du SSH till port 50000 för Linux dirigerar belastningsutjämning dig till port 22 för den första virtuella datorn i skaluppsättningen. Anslutning till port 50001 dirigeras till port 22 på den andra virtuella datorn och så vidare.

 Ett bra sätt att redigera mallarna med Visual Studio är att använda **JSON Outline**. Du kan ordna parametrar, variabler och resurser. Med en förståelse för schemat kan Visual Studio peka på fel i mallen innan du distribuerar det.

![JSON-utforskare](media/virtual-machine-scale-sets-vs-create/json-explorer.png)

## <a name="deploy-the-project"></a>Distribuera projektet

Distribuera Azure Resource Manager-mallen för att skapa skalauppsättningen för virtuell dator:

1. Högerklicka på projektet i **Solution Explorer**och välj **Distribuera** > **nytt**.

    ![Distribuera projektet](media/virtual-machine-scale-sets-vs-create/deploy-new-project.png)

1. Välj vilken prenumeration som ska användas i **Distribuera till resursgrupp**och välj en resursgrupp. Du kan skapa en resursgrupp om det behövs.

1. Välj sedan **Redigera parametrar för** att ange parametrar som skickas till mallen.

   ![Ange prenumeration och resursgrupp](media/virtual-machine-scale-sets-vs-create/deploy-to-resource-group.png)

1. Ange användarnamn och lösenord för operativsystemet. Dessa värden krävs för att skapa distributionen. Om du inte har Installerat PowerShell-verktyg för Visual Studio väljer du **Spara lösenord** för att undvika en dold PowerShell-kommandotolk eller använder Stöd för [Key Vault](https://azure.microsoft.com/blog/keyvault-support-for-arm-templates/). Välj **Spara** för att fortsätta.

    ![Redigera distributionsparametrar](media/virtual-machine-scale-sets-vs-create/edit-deployment-parameters.png)

1. Välj **Distribuera** **i Gruppen Distribuera till resurs**. Åtgärden kör skriptet **Deploy-AzureResourceGroup.ps1.** **Utdatafönstret** visar distributionsstatus.

   ![Utdata visar resultat](media/virtual-machine-scale-sets-vs-create/deployment-output.png)

## <a name="explore-your-virtual-machine-scale-set"></a>Utforska din skaluppsättning för virtuella datorer<a name="exploring-your-virtual-machine-scale-set"></a>

Välj **Visa** > **Molnutforskaren** om du vill visa den nya skaluppsättningen för virtuella datorer. Använd **Uppdatera alla**om det behövs.

![Cloud Explorer](media/virtual-machine-scale-sets-vs-create/cloud-explorer.png)

**Med Cloud Explorer** kan du hantera Azure-resurser i Visual Studio samtidigt som du utvecklar program. Du kan också visa din skalauppsättning för virtuella datorer i [Azure-portalen](https://portal.azure.com) och [Azure Resource Explorer](https://resources.azure.com/).

 Portalen är det bästa sättet att hantera din Azure-infrastruktur med en webbläsare. Azure Resource Explorer är ett enkelt sätt att utforska och felsöka Azure-resurser. Azure Resource Explorer erbjuder instansvyn och visar även PowerShell-kommandon för de resurser du tittar på.

## <a name="next-steps"></a>Nästa steg

När du har distribuerat skalningsuppsättningar för virtuella datorer via Visual Studio kan du anpassa projektet ytterligare så att det passar dina programkrav. Konfigurera till exempel automatisk skalning genom att lägga till en **Insights-resurs.** Du kan lägga till infrastruktur i mallen, till exempel fristående virtuella datorer, eller distribuera program med det anpassade skripttillägget. Bra exempel mallar finns i [Azure Quickstart Mallar](https://github.com/Azure/azure-quickstart-templates) GitHub-databasen. Sök efter `vmss`.
