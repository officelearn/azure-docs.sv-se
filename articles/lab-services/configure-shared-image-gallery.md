---
title: Konfigurera ett delat avbildningsgalleri i Azure DevTest Labs | Microsoft-dokument
description: Lär dig hur du konfigurerar ett delat avbildningsgalleri i Azure DevTest Labs
services: devtest-lab
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
ms.date: 09/05/2019
ms.author: spelluru
ms.openlocfilehash: 9593d60f76802cd515ca85616bce028cf3aa0d49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77589325"
---
# <a name="configure-a-shared-image-gallery-in-azure-devtest-labs"></a>Konfigurera ett delat bildgalleri i Azure DevTest Labs
DevTest Labs stöder nu funktionen [Delat bildgalleri.](../virtual-machines/windows/shared-image-galleries.md) Det gör det möjligt för labbanvändare att komma åt bilder från en delad plats samtidigt som labbresurser skapas. Det hjälper dig också att skapa struktur och organisation runt dina anpassade hanterade VM-avbildningar. Funktionen Delat bildgalleri stöder:

- Hanterad global replikering av bilder
- Versionshantering och gruppering av bilder för enklare hantering
- Gör dina avbildningar mycket tillgängliga med ZRS-konton (Zone Redundant Storage) i regioner som stöder tillgänglighetszoner. ZRS erbjuder bättre motståndskraft mot zonfel.
- Dela mellan prenumerationer, och till och med mellan klienter, med hjälp av rollbaserad åtkomstkontroll (RBAC).

Mer information finns i [dokumentationen för Delat bildgalleri](../virtual-machines/windows/shared-image-galleries.md). 
 
Om du har ett stort antal hanterade bilder som du behöver underhålla och vill göra dem tillgängliga i hela företaget kan du använda ett delat bildgalleri som en databas som gör det enkelt att uppdatera och dela dina bilder. Som labbägare kan du bifoga ett befintligt delat bildgalleri till labbet. När det här galleriet är bifogat kan labbanvändare skapa maskiner från de senaste avbildningen. En viktig fördel med den här funktionen är att DevTest Labs nu kan dra nytta av att dela bilder över labb, prenumerationer och mellan regioner. 

> [!NOTE]
> Mer information om kostnader som är kopplade till tjänsten Delat bildgalleri finns i [Fakturering för delat bildgalleri](../virtual-machines/windows/shared-image-galleries.md#billing).

## <a name="considerations"></a>Överväganden
- Du kan bara bifoga ett delat bildgalleri till ett labb åt gången. Om du vill bifoga ett annat galleri måste du koppla från det befintliga galleriet och bifoga ett annat. 
- DevTest Labs stöder för närvarande endast generaliserade bilder i det delade bildgalleriet.
- DevTest Labs stöder för närvarande inte att ladda upp bilder till galleriet via labbet. 
- När du skapar en virtuell dator med hjälp av en delad bildgalleri, använder DevTest Labs alltid den senaste publicerade versionen av den här avbildningen. Men om en avbildning har flera versioner kan användaren välja att skapa en dator från en tidigare version genom att gå till fliken Avancerade inställningar när den virtuella datorn skapas.  
- Även om DevTest Labs automatiskt gör ett bästa försök att se till att delat bildgalleri replikerar avbildningar till den region där labbet finns, är det inte alltid möjligt. Se till att avbildningarna redan replikeras till labbets region om du vill undvika att användarna har problem med att skapa virtuella datorer från dessa avbildningar."

## <a name="use-azure-portal"></a>Använda Azure-portalen
1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **Alla tjänster** på menyn till vänster.
1. Välj **DevTest Labs** från listan.
1. Välj ditt **labb**i listan över labb.
1. Välj **Konfiguration och principer** i avsnittet **Inställningar** på den vänstra menyn.
1. Välj **Delade bildgallerier** under **Baser för virtuella datorer** på den vänstra menyn.

    ![Menyn Delade bildgallerier](./media/configure-shared-image-gallery/shared-image-galleries-menu.png)
1. Bifoga ett befintligt delat bildgalleri till labbet genom att klicka på knappen **Bifoga** och välja galleriet i listrutan.

    ![Anslut](./media/configure-shared-image-gallery/attach-options.png)
1. Gå till det bifogade galleriet och konfigurera galleriet så att **det aktiverar eller inaktiverar** delade avbildningar för att skapa virtuella datorer. Välj ett bildgalleri i listan för att konfigurera det. 

    Som standard är **tillåt att alla avbildningar används som baser för virtuella datorer** inställd på **Ja**. Det innebär att alla bilder som är tillgängliga i det bifogade delade bildgalleriet kommer att vara tillgängliga för en labbanvändare när du skapar en ny virtuell labb-dator. Om åtkomsten till vissa bilder behöver begränsas ändrar **du Tillåt att alla avbildningar används som baser för virtuella datorer** till **Nej**och väljer de bilder som du vill tillåta när du skapar virtuella datorer och väljer sedan knappen **Spara.**

    ![Aktivera eller inaktivera](./media/configure-shared-image-gallery/enable-disable.png)
1. Labbanvändare kan sedan skapa en virtuell dator med hjälp av aktiverade bilder genom att klicka på **+Lägg till** och hitta bilden på **välj bassida.**

    ![Labbanvändare](./media/configure-shared-image-gallery/lab-users.png)
## <a name="use-azure-resource-manager-template"></a>Använda Azure Resource Manager-mall

### <a name="attach-a-shared-image-gallery-to-your-lab"></a>Bifoga ett delat bildgalleri till labbet
Om du använder en Azure Resource Manager-mall för att koppla ett delat avbildningsgalleri till labbet måste du lägga till det under avsnittet resurser i Resource Manager-mallen, som visas i följande exempel:

```json
"resources": [
{
    "apiVersion": "2018-10-15-preview",
    "type": "Microsoft.DevTestLab/labs",
    "name": "mylab",
    "location": "eastus",
    "resources": [
    {
        "apiVersion":"2018-10-15-preview",
        "name":"myGallery",
        "type":"sharedGalleries",
        "properties": {
            "galleryId":"/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/mySharedGalleryRg/providers/Microsoft.Compute/galleries/mySharedGallery",
            "allowAllImages": "Enabled"
        }
    }
    ]
}
```

Ett fullständigt exempel på Resource Manager-mall finns i de här exempel på Resource Manager-mallen i vår offentliga GitHub-databas: [Konfigurera ett delat bildgalleri när du skapar ett labb](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-lab-shared-gallery-configured).

## <a name="use-api"></a>Använda API

### <a name="shared-image-galleries---create-or-update"></a>Delade bildgallerier – skapa eller uppdatera

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}?api-version= 2018-10-15-preview
Body: 
{
    "properties":{
        "galleryId": "[Shared Image Gallery resource Id]",
        "allowAllImages": "Enabled"
    }
}

```

### <a name="shared-image-galleries-images---list"></a>Bilder med delade bildgallerier – lista 

```rest
GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}/sharedimages?api-version= 2018-10-15-preview
```




## <a name="next-steps"></a>Nästa steg
Se följande artiklar om hur du skapar en virtuell dator med hjälp av en avbildning från det bifogade delade bildgalleriet: [Skapa en virtuell dator med en delad avbildning från galleriet](add-vm-use-shared-image.md)
