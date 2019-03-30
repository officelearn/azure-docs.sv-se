---
title: Använda en delad bildgalleriet i Azure Lab Services | Microsoft Docs
description: Lär dig mer om att konfigurera ett labbkonto om du vill använda en delad avbildningsgalleri så att en användare kan dela en avbildning med andra och en annan användare kan använda avbildningen för att skapa en mall för virtuell dator i labbet.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2019
ms.author: spelluru
ms.openlocfilehash: 93136c7d685bd9fc8ec4bcdea3a900b28029059b
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58653218"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>Använda en delad bildgalleriet i Azure Lab Services
Den här artikeln visar hur lärare/testlabb-administratören kan spara en avbildning av virtuell dator mall för att det ska återanvändas av andra. Dessa avbildningar sparas i en Azure [delade bildgalleriet](../../virtual-machines/windows/shared-image-galleries.md). Som ett första steg bifogar lab administratören en befintliga delade bildgalleriet till labb-kontot. Delade bildgalleriet är kopplade labs som skapats i labb-kontot kan spara bilder i delade avbildningsgalleri. Andra lärare kan välja den här avbildningen från det delade avbildningsgalleriet så att du skapar en mall för deras klasser. 

## <a name="prerequisites"></a>Förutsättningar
Skapa en delad bildgalleriet genom att använda antingen [Azure PowerShell](../../virtual-machines/windows/shared-images.md) eller [Azure CLI](../../virtual-machines/linux/shared-images.md).

## <a name="attach-a-shared-image-gallery-to-a-lab-account"></a>Koppla en delad bildgalleriet till ett labbkonto
Följande procedur visar hur du kopplar en delad bildgalleriet till ett labbkonto. 

1. Logga in på [Azure Portal](https://portal.azure.com).
2. På menyn till vänster väljer du **Alla tjänster**. Välj **Lab Services** i den **DEVOPS** avsnittet. Om du väljer star (`*`) bredvid **Lab Services**, läggs den till den **Favoriter** avsnitt i den vänstra menyn. Nästa gång och senare, väljer du **Lab Services** under **Favoriter**.

    ![Alla tjänster -> Lab Services](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Välj ditt labbkonto för att se den **Labbkonto** sidan. 
4. Välj **delad bildgalleriet** på den vänstra menyn och välj **bifoga** i verktygsfältet. 

    ![Delade bild galleriet – Lägg till knapp](../media/how-to-use-shared-image-gallery/sig-attach-button.png)
5. På den **bifoga en befintlig delad bildgalleriet** väljer bildgalleriet delade, och välj **OK**.

    ![Välj ett befintligt galleri](../media/how-to-use-shared-image-gallery/select-image-gallery.png)
6. Du ser följande skärm: 

    ![Min galleriet i listan](../media/how-to-use-shared-image-gallery/my-gallery-in-list.png)
    
    I det här exemplet finns inga bilder i delade avbildningsgalleri ännu.

Azure Lab Services identitet har lagts till som deltagare i delade avbildningsgalleri som är kopplad till labbet. Det gör att lärare / IT-administratören att spara VM-avbildningar i delade avbildningsgalleri. Alla labs som skapats i den här testlabb-kontot har åtkomst till anslutna delade bildgalleriet. 

Alla avbildningar i galleriet med anslutna delade avbildningar är aktiverade som standard. Du kan aktivera eller inaktivera valda avbildningar genom att markera dem i listan och använda den **aktivera valda avbildningar** eller **inaktivera valda avbildningar** knappen. 

## <a name="detach-a-shared-image-gallery"></a>Koppla från en delad bildgalleri
Endast en delad bildgalleriet kan kopplas till ett labb. Om du vill koppla en annan delad bildgalleriet koppla från den aktuella artikeln innan du kopplar den nya servern. Om du vill koppla bort en delad bildgalleriet i din Övning, Välj **Detach** i verktygsfältet och Bekräfta vid frigörandet. 

## <a name="save-an-image-to-the-shared-image-gallery"></a>Spara en avbildning i delade avbildningsgalleri
När en delad bildgalleriet kopplas spara en lärare en mall-bild i delade avbildningsgalleri så att den kan återanvändas av andra lärare.

![Spara en avbildning av virtuell dator i galleriet](../media/how-to-use-shared-image-gallery/save-virtual-machine.png)

## <a name="use-an-image-from-the-shared-image-gallery"></a>Använda en avbildning från avbildningsgalleriet delade
En lärare kan välja en anpassad avbildning som är tillgängliga i delade bildgalleriet för mallen under skapandet av ny labb.

![Använd avbildning av virtuell dator från galleriet](../media/how-to-use-shared-image-gallery/use-shared-image.png)

## <a name="next-steps"></a>Nästa steg
Mer information om delade bildgallerier finns [delade bildgalleriet](../../virtual-machines/windows/shared-image-galleries.md).
