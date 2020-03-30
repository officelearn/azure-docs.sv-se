---
title: Lägga till taggar i ett labb i Azure DevTest Labs | Microsoft-dokument
description: Lär dig hur du lägger till en tagg i ett labb i Azure DevTest Labs
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: dc5b327a-62e4-41bc-80ef-deb3c23d51b2
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2019
ms.author: spelluru
ms.openlocfilehash: 1d7175d000eebd3f68665e7f097bbe3f68025bc8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72302794"
---
# <a name="add-tags-to-a-lab-in-azure-devtest-labs"></a>Lägga till taggar i ett labb i Azure DevTest Labs

Du kan skapa anpassade taggar och använda dem på dina DevTest Labs-resurser för att logiskt kategorisera dina resurser. Senare kan du snabbt och enkelt se alla resurser i din prenumeration som har den taggen. Taggar är användbara när du behöver ordna resurser för fakturering eller hantering.

Resurser som stöds av taggar inkluderar

* Beräkna virtuella datorer
* Nätverkskort
* IP-adresser
* Lastbalanserare
* Lagringskonton
* Hanterade diskar

Du kan använda taggar när du [skapar ett labb](devtest-lab-create-lab.md) och senare hanterar dem via taggbladet under Konfiguration och inställningar.

Varje tagg består av ett/**namnvärdespar.** **name** Du kan till exempel skapa en tagg med namnet *costcenter* som har värdet *34543*. En tagg som denna kan hjälpa dig att senare identifiera labbresurser som kan faktureras till det här specifika området i organisationen. Du får välja namn och värden som är meningsfulla för hur du vill organisera prenumerationen.

## <a name="steps-to-manage-tags-in-an-existing-lab"></a>Steg för att hantera taggar i ett befintligt labb

1. Logga in på [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Om det behövs väljer du **Alla tjänster**och väljer sedan **DevTest Labs** i listan. Ditt labb kanske redan visas på instrumentpanelen under **Alla resurser**.
1. Välj det labb där du vill lägga till eller hantera taggar i listan över labb.
1. I labbets **översiktsområde** väljer du **Konfiguration och principer**.

    ![Knappen Konfiguration och principer](./media/devtest-lab-add-tag/devtestlab-config-and-policies.png)

1. Välj **Taggar**till vänster under **HANTERA**.
1. Om du vill skapa en ny tagg för det här labbet anger du ett/**namnvärdespar** och väljer **Spara**. **Name** Du kan också välja en befintlig tagg i listan om du vill visa eller hantera de resurser som är associerade med taggen.

    ![Hantera taggar](./media/devtest-lab-add-tag/devtestlab-manage-tags.png)

> [!NOTE]
> Taggar som skapas på labbnivå flödar genom alla fakturerbara resurser som labbet snurrar upp i din prenumeration. Till exempel flödestaggar på labbnivå till underliggande virtuella datorer för beräkning av labb-virtuella datorer.Du kan använda taggar i samband med kostnadshantering. Taggar på labbnivå visas i taggfiltret för kostnadshanteringen.

## <a name="understanding-limitations-to-tags"></a>Förstå begränsningar för taggar

Följande begränsningar gäller för taggar:

* Varje resurs eller resursgrupp kan innehålla upp till 15 taggnamn-/taggvärdepar. Den här begränsningen gäller endast för taggar som tillämpas direkt på resursgruppen eller resursen. En resursgrupp kan innehålla många resurser som var och en har 15 taggnamn-/taggvärdepar.
* Taggnamnet är begränsat till 512 tecken och taggvärdet är begränsat till 256 tecken. För lagringskonton är taggnamnet begränsat till 128 tecken och taggvärdet till 256 tecken.
* Taggar som lagts till för en resursgrupp ärvs inte av resurserna i den resursgruppen.

[Använd taggar för att ordna dina Azure-resurser](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) ger mer information om hur du använder taggar i Azure, inklusive hur du hanterar taggar med PowerShell eller Azure CLI.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Nästa steg
* Du kan tillämpa begränsningar och konventioner i din prenumeration med hjälp av anpassade principer. En princip som du definierar kan kräva att alla resurser har ett värde för en viss tagg. Mer information finns i [Ange principer och scheman](devtest-lab-set-lab-policy.md).
* Utforska [snabbstartsmallgalleriet DevTest Labs Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).
