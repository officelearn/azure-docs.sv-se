---
title: "Lägga till taggar i ett labb i Azure DevTest Labs | Microsoft Docs"
description: "Lär dig hur du lägger till en tagg i ett labb i Azure DevTest Labs"
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: dc5b327a-62e4-41bc-80ef-deb3c23d51b2
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/07/2017
ms.author: v-craic
ms.openlocfilehash: 72e82b207a543a9020b38b2c4494717e9f648ab7
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="add-tags-to-a-lab-in-azure-devtest-labs"></a>Lägga till taggar i ett labb i Azure DevTest Labs

Du kan skapa egna etiketter och koppla dem till din DevTest Labs resurser till logiskt kategorisera dina resurser. Senare kan du snabbt och se enkelt alla resurser i din prenumeration som har den taggen. Taggar är användbart när du behöver ordna resurser för fakturerings- eller hantering.

Resurser som stöds av taggar inkluderar

* Beräkna virtuella datorer
* Nätverkskort
* IP-adresser
* Belastningsutjämnare
* Lagringskonton
* Hanterade diskar

Du kan använda taggar när du [skapa ett labb](devtest-lab-create-lab.md) och hantera dem senare via bladet taggar under inställningar och konfiguration.

Varje tagg består av en **namn**/**värdet** par. Du kan till exempel skapa en tagg med namnet *costcenter* som har värdet *34543*. En tagg som detta kan hjälpa dig senare identifiera lab resurser som är fakturerbar detta specifika område i din organisation. Du får välja namn och värden som passar för hur du vill ordna din prenumeration.

## <a name="steps-to-manage-tags-in-an-existing-lab"></a>Steg för att hantera taggar i en befintlig labb

1. Logga in på [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Om det behövs väljer **alla tjänster**, och välj sedan **DevTest Labs** från listan. Ditt labb kanske redan visas på instrumentpanelen under **alla resurser**.
1. Välj labbet som du vill lägga till eller hantera taggar i listan över övningar.  
1. På testmiljön **översikt** väljer **konfiguration och principer**.  

    ![Knapp för konfiguration och principer](./media/devtest-lab-add-tag/devtestlab-config-and-policies.png)

1. Vänster under **hantera**väljer **taggar**.
1. Om du vill skapa en ny post för den här övningen, ange en **namn**/**värdet** länka och välj **spara**. Du kan också välja en befintlig tagg från listan för att visa eller hantera resurser som är associerade med taggen.

    ![Hantera taggar](./media/devtest-lab-add-tag/devtestlab-manage-tags.png)

## <a name="understanding-limitations-to-tags"></a>Förstå begränsningar till taggar

Följande begränsningar gäller för taggar:

* Varje resurs eller resursgrupp kan innehålla upp till 15 taggnamn-/taggvärdepar. Den här begränsningen gäller endast för taggar som tillämpas direkt på resursgruppen eller resursen. En resursgrupp kan innehålla många resurser som var och en har 15 taggnamn-/taggvärdepar. 
* Taggnamnet är begränsat till 512 tecken och taggvärdet är begränsat till 256 tecken. För lagringskonton är taggnamnet begränsat till 128 tecken och taggvärdet till 256 tecken.
* Taggar som lagts till för en resursgrupp ärvs inte av resurserna i den resursgruppen.

[Ordna dina Azure-resurser med hjälp av taggar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) ger mer information om att använda taggar i Azure, inklusive hur du hanterar taggar med PowerShell eller Azure CLI.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Nästa steg
* Du kan tillämpa begränsningar och konventioner över din prenumeration med hjälp av anpassade principer. En princip som du definierar kan kräva att alla resurser som har ett värde för en viss tagg. Mer information finns i [ange principer och scheman](devtest-lab-set-lab-policy.md).
* Utforska den [DevTest Labs Azure Resource Manager QuickStart mallgalleriet](https://github.com/Azure/azure-devtestlab/tree/master/Samples).
