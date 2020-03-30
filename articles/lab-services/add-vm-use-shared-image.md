---
title: Lägga till en virtuell dator med en delad avbildning i Azure DevTest Labs | Microsoft-dokument
description: Lär dig hur du lägger till en virtuell dator (VM) med hjälp av en avbildning från det bifogade delade avbildningsgalleriet i Azure DevTest Labs
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2019
ms.author: spelluru
ms.openlocfilehash: c13b2c3d00a0ab0af0f7785feae645d907ffdaa3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68775596"
---
# <a name="add-a-vm-using-an-image-from-the-attached-shared-image-gallery"></a>Lägga till en virtuell dator med en avbildning från det bifogade delade bildgalleriet
Med Azure DevTest Labs kan du koppla ett delat avbildningsgalleri till labbet och sedan använda avbildningar i galleriet som baser för de virtuella datorer som du skapar i labbet. Mer information om hur du bifogar ett delat bildgalleri i labbet finns i [Konfigurera delat bildgalleri](configure-shared-image-gallery.md). I den här artikeln visas hur du lägger till en virtuell dator i labbet med hjälp av en avbildning från det bifogade delade bildgalleriet som bas. 

## <a name="azure-portal"></a>Azure Portal
I det här avsnittet får du lära dig hur du använder Azure-portalen för att lägga till en virtuell dator i labbet baserat på en avbildning från det bifogade delade avbildningsgalleriet. Det här avsnittet innehåller inte detaljerade steg-för-steg-instruktioner för att skapa en virtuell dator med Azure-portalen. Mer information finns i [Skapa en virtuell dator - Azure-portal](devtest-lab-add-vm.md). Det markerar bara de steg där du väljer en bild från det bifogade delade bildgalleriet och väljer en version av bilden som du vill använda. 

När du lägger till en virtuell dator i labbet kan du välja en bild från det bifogade delade bildgalleriet som basavbildning: 

![Välj en delad bild för basen](./media/add-vm-use-shared-image/select-shared-image-for-base.png)

På fliken **Avancerade inställningar** på sidan **Skapa labbresurs** kan du sedan välja den version av bilden som du vill använda som basavbildning:

![Välj bildversion](./media/add-vm-use-shared-image/select-version-shared-image.png)

Du kan växla till att använda en annan version av avbildningen när den virtuella datorn har skapats. 

## <a name="resource-manager-template"></a>Resource Manager-mall
Om du använder en Azure Resource Manager-mall för att skapa en virtuell dator med hjälp av en avbildning av ett delat avbildningsgalleri anger du ett värde för **sharedImageId** i avsnittet **Egenskaper.** Se följande exempel: 

```json
"resources": [
{
    ...
    "properties": {
         "sharedImageId": "/subscriptions/111111111-1111-1111-1111-111111111111/resourcegroups/mydtlrg/providers/microsoft.devtestlab/labs/mydtllab/sharedgalleries/spsig/sharedimages/myimagefromgallery",
        "sharedImageVersion": "1.0.1",
        ...
    }
}
],
```

Ett fullständigt exempel på Resource Manager-mall finns i Skapa en virtuell dator med hjälp av ett exempel på ett [avbildningsformulär](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-vm-username-pwd-sharedimage) för delat bildgalleri i vår GitHub-databas. 

## <a name="rest-api"></a>REST API

1. Först måste du få id för bilden i det delade bildgalleriet. Ett sätt är att lista alla bilder i det bifogade delade bildgalleriet med hjälp av följande GET-kommando. 

    ```rest
    GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}/sharedimages?api-version= 2018-10-15-preview
    ```
2. Anropa PUT-metoden på virtuella datorer genom att skicka ID:et för `properties.SharedImageId`den delade avbildningen som du fick från föregående anrop till .

## <a name="next-steps"></a>Nästa steg
Mer information om hur du bifogar ett delat bildgalleri i ett labb och konfigurerar det finns i [Konfigurera delat bildgalleri](configure-shared-image-gallery.md).