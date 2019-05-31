---
title: Konfigurera en delad bildgalleriet i Azure DevTest Labs | Microsoft Docs
description: Lär dig hur du konfigurerar en delad bildgalleriet i Azure DevTest Labs
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
ms.date: 05/30/2019
ms.author: spelluru
ms.openlocfilehash: 51b394043f88789865edea5be6376ae536f88848
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66420445"
---
# <a name="configure-a-shared-image-gallery-in-azure-devtest-labs"></a>Konfigurera en delad bildgalleriet i Azure DevTest Labs
DevTest Labs nu stöder den [delad bildgalleriet](/virtual-machines/windows/shared-image-galleries.md) funktionen. Det gör det möjligt för labbanvändare åtkomst till bilder från en delad plats när du skapar labbresurser. Det hjälper dig också att skapa struktur och organisation runt dina anpassade-hanterade VM-avbildningar. Delad bildgalleriet-funktionen stöder:

- Hanterad global replikering av avbildningar
- Versionshantering och gruppering av avbildningar för enklare hantering
- Gör dina avbildningar med hög tillgänglighet med Zonredundant lagring (ZRS)-konton i regioner som har stöd för tillgänglighetszoner. ZRS ger bättre återhämtning mot zonindelad fel.
- Delning mellan prenumerationer och även mellan klienter, med hjälp av rollbaserad åtkomstkontroll (RBAC).

Mer information finns i [delad bildgalleriet dokumentation](../virtual-machines/windows/shared-image-galleries.md). 
 
Om du har ett stort antal hanterade avbildningar som du behöver underhålla och vill göra dem tillgängliga i hela företaget kan använda du en delad bildgalleriet som en lagringsplats som gör det enkelt att uppdatera och dela dina avbildningar. Som labbägare kan koppla du en befintlig delad bildgalleriet till labbet. När det här galleriet är kan labbanvändare skapa datorer från de här senaste bilder. En viktig fördel med den här funktionen är att DevTest Labs kan nu dra nytta av delning avbildningar mellan labs, mellan prenumerationer och mellan regioner. 

## <a name="considerations"></a>Överväganden
- Du kan endast koppla en delad bildgalleriet i ett labb i taget. Om du vill koppla en annan galleriet måste du koppla från den befintliga påverkas och kopplar ett annat. 
- DevTest Labs stöder för närvarande inte upp avbildningar i galleriet via labbet. 
- När du skapar en virtuell dator med en delad bild galleriet bilden använder labb alltid den senaste publicerade versionen av den här avbildningen.
- Även om DevTest Labs kan du automatiskt göra ett bästa försök att kontrollera delade bildgalleriet replikerar avbildningar till den region där labbet finns, går det inte alltid. Om du vill undvika att de har problem med att skapa virtuella datorer från dessa avbildningar, se till att bilderna redan har replikerats till den testmiljön region ”.

## <a name="use-azure-portal"></a>Använda Azure-portalen
1. Logga in på [Azure Portal](https://portal.azure.com).
1. Välj **alla tjänster** på menyn till vänster navigering.
1. Välj **DevTest Labs** från listan.
1. I listan över labbar, väljer din **lab**.
1. Välj **konfiguration och principer** i den **inställningar** avsnitt i den vänstra menyn.
1. Välj **delad Image Galleries** under **VM-baser** på den vänstra menyn.
1. Koppla en befintlig delad bildgalleriet till labbet genom att klicka på den **bifoga** knappen och markera galleriet i listrutan.
1. Gå till galleriet anslutna och konfigurera galleriet till **aktivera eller inaktivera** delade bilder för att skapa en virtuell dator.
1. Labbanvändare kan sedan skapa en virtuell dator med aktiverad avbildningar genom att klicka på **+ Lägg till** och att hitta avbildningen i den **väljer din base** sidan.

## <a name="use-azure-resource-manager-template"></a>Använd Azure Resource Manager-mallar

### <a name="attach-a-shared-image-gallery-to-your-lab"></a>Bifoga en delad bildgalleriet i labbet
Om du använder en Azure Resource Manager-mall för att bifoga en delad bildgalleriet i labbet, måste du lägga till det under resursavsnittet för Resource Manager-mall som du ser i följande exempel:

```json
"resources": [
{
    "apiVersion": "2018-10-15-preview",
    "type": "Microsoft.DevTestLab/labs",
    "name": "[parameters('newLabName')]",
    "location": "[resourceGroup (). location]",
    "resources": [
    {
        "apiVersion": "2018-10-15-preview",
        "name": "[variables('labVirtualNetworkName')]",
        "type": "virtualNetworks",
        "dependsOn": [
            "[resourceId('Microsoft.DevTestLab/labs', parameters('newLabName'))]"
        ]
    },
    {
        "apiVersion":"2018-10-15-preview",
        "name":"myGallery",
        "type":"sharedGalleries",
        "properties": {
            "galleryId":"[parameters('existingSharedGalleryId')]",
            "allowAllImages": "Enabled"
        },
        "dependsOn":[
            "[resourceId('Microsoft.DevTestLab/labs', parameters('newLabName'))]"
        ]
    }
    ]
} 

```

En fullständig mallexemplet Resource Manager finns i dessa Resource Manager-mallexempel i våra offentliga GitHub-lagringsplatsen: [Konfigurera en delad bildgalleriet när du skapar ett labb](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-lab-shared-gallery-configured).

### <a name="create-a-vm-using-an-image-from-the-shared-image-gallery"></a>Skapa en virtuell dator med hjälp av en avbildning från galleriet med delade avbildningar
Om du använder en Azure Resource Manager-mall för att skapa en virtuell dator som använder en delad bild galleriavbildning, Använd följande exempel:

```json

"resources": [
{
    "apiVersion": "2018-10-15-preview",
    "type": "Microsoft.DevTestLab/labs/virtualMachines",
    "name": "[variables('resourceName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "sharedImageId": "[parameters('existingSharedImageId')]",
        "size": "[parameters('newVMSize')]",
        "isAuthenticationWithSshKey": false,
        "userName": "[parameters('userName')]",
        "sshKey": "",
        "password": "[parameters('password')]",
        "labVirtualNetworkId": "[variables('labVirtualNetworkId')]",
        "labSubnetName": "[variables('labSubnetName')]"
    }
}
],

```

Mer information finns i dessa Resource Manager-mallexempel på våra offentliga GitHub.
[Skapa en virtuell dator som använder en delad bild galleriavbildning](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-vm-username-pwd-sharedimage).

## <a name="use-api"></a>Använda API: et

- Använd API-versionen 2018-10-15-preview.
- Skicka begäran om du vill koppla galleriet enligt följande kodavsnitt:
    
    ``` 
    PUT [Lab Resource Id]/SharedGalleries/[newGalleryName]
    Body: 
    {
        “properties”:{
            “galleryId”: “[Shared Image Gallery resource Id]”,
            “allowAllImages”:”Enabled”
        }
    }
    ```
- Om du vill visa alla avbildningar i bildgalleriet delade, kan du lista alla delade avbildningar tillsammans med deras resurs-ID genom att

    ```
    GET [Lab Resource Id]/SharedGalleries/mySharedGallery/SharedImages
    ````
- För att skapa en virtuell dator med delade avbildningar, kan du göra en placering på virtuella datorer och i egenskaperna för virtuella datorer, skicka ID för de delade avbildningar som du fick från det föregående anropet. Egenskaper. SharedImageId


## <a name="next-steps"></a>Nästa steg
Se följande artiklar på artefakter:

- [Ange obligatoriska artefakter för ditt labb](devtest-lab-mandatory-artifacts.md)
- [Skapa anpassade artefakter](devtest-lab-artifact-author.md)
- [Lägg till en artefaktcentrallagret till ett labb](devtest-lab-artifact-author.md)
- [Diagnostisera fel i artefakter](devtest-lab-troubleshoot-artifact-failure.md)
