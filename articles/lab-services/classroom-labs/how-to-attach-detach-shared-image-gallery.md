---
title: Bifoga eller koppla från ett delat avbildningsgalleri i Azure Lab Services | Microsoft-dokument
description: I den här artikeln beskrivs hur du bifogar ett delat bildgalleri till ett klassrumslabb i Azure Lab Services.
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
ms.date: 02/24/2020
ms.author: spelluru
ms.openlocfilehash: 00dbef7b4453ffcb54020340bde51f55827759a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284322"
---
# <a name="attach-or-detach-a-shared-image-gallery-in-azure-lab-services"></a>Bifoga eller koppla från ett delat avbildningsgalleri i Azure Lab Services
Lärare/labbadministratör kan spara en vm-avbildning i en virtuell mall i ett [delat avbildningsgalleri](../../virtual-machines/windows/shared-image-galleries.md) i Azure för att den ska återanvändas av andra. Som ett första steg bifogar labbadministratören ett befintligt delat bildgalleri till labbkontot. När det delade bildgalleriet är bifogat kan övningar som skapats i labbkontot spara bilder i det delade bildgalleriet. Andra lärare kan välja den här bilden från det delade bildgalleriet för att skapa en mall för sina klasser. 

När en avbildning sparas i ett delat avbildningsgalleri replikerar Azure Lab Services den sparade avbildningen till andra regioner som är tillgängliga i samma [geografi](https://azure.microsoft.com/global-infrastructure/geographies/). Det säkerställer att avbildningen är tillgänglig för labb som skapats i andra regioner i samma geografi. Om du sparar bilder i ett delat bildgalleri medför en extra kostnad, vilket inkluderar kostnader för alla replikerade avbildningar. Den här kostnaden är skild från användningskostnaden för Azure Lab Services. Mer information om priser för delat bildgalleri finns i [Shared Image Gallery – Billing]( https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#billing).

I den här artikeln visas hur du bifogar eller kopplar från ett delat bildgalleri till ett labbkonto. 

> [!NOTE]
> Azure Lab Services stöder för närvarande skapandet av virtuella mall-datorer baserat på endast **generaliserade** VM-avbildningar (inte specialiserade avbildningar) i ett delat bildgalleri. 


## <a name="configure-at-the-time-of-lab-account-creation"></a>Konfigurera vid tidpunkten för skapandet av labbkonto
När du skapar ett labbkonto kan du bifoga ett delat bildgalleri till labbkontot. Du kan antingen välja ett befintligt delat bildgalleri i listrutan eller skapa ett nytt. Om du vill skapa och bifoga ett delat bildgalleri till labbkontot väljer du **Skapa nytt,** anger ett namn för galleriet och skriver **OK**. 

![Konfigurera det delade bildgalleriet när labbkontot skapas](../media/how-to-use-shared-image-gallery/new-lab-account.png)

## <a name="configure-after-the-lab-account-is-created"></a>Konfigurera när labbkontot har skapats
När labbkontot har skapats kan du utföra följande uppgifter:

- Skapa och bifoga ett delat bildgalleri
- Bifoga ett delat bildgalleri till labbkontot
- Koppla från ett delat bildgalleri från labbkontot

## <a name="create-and-attach-a-shared-image-gallery"></a>Skapa och bifoga ett delat bildgalleri
1. Logga in på [Azure-portalen](https://portal.azure.com).
2. På menyn till vänster väljer du **Alla tjänster**. Välj **Lab Services** i avsnittet **DEVOPS.** Om du väljer`*`stjärna ( ) bredvid **Lab Services**läggs den till i avsnittet **FAVORITER** på den vänstra menyn. Från och med nästa gång väljer du **Lab Services** under **FAVORITER.**

    ![Alla tjänster -> Lab Services](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Välj ditt labbkonto för att se sidan **Labbkonto.** 
4. Välj **Delat bildgalleri** på den vänstra menyn och välj **+ Skapa** i verktygsfältet.  

    ![Knappen Skapa delat bildgalleri](../media/how-to-use-shared-image-gallery/new-shared-image-gallery-button.png)
5. I fönstret **Skapa delat bildgalleri** anger du ett **namn** för galleriet och anger **OK**. 

    ![Fönstret Skapa delat bildgalleri](../media/how-to-use-shared-image-gallery/create-shared-image-gallery-window.png)

    Azure Lab Services skapar det delade avbildningsgalleriet och bifogade det till labbkontot. Alla labb som skapas i det här labbkontot har åtkomst till det bifogade delade bildgalleriet. 

    ![Bifogat bildgalleri](../media/how-to-use-shared-image-gallery/image-gallery-in-list.png)

    I det nedre fönstret visas bilder i det delade bildgalleriet. I det här nya galleriet finns det inga bilder. När du laddar upp bilder till galleriet visas dem på den här sidan.     

    Alla bilder i det bifogade delade bildgalleriet är aktiverade som standard. Du kan aktivera eller inaktivera markerade bilder genom att markera dem i listan och använda knappen **Aktivera markerade bilder** eller Inaktivera markerade **bilder.**

## <a name="attach-an-existing-shared-image-gallery"></a>Bifoga ett befintligt delat bildgalleri
Följande procedur visar hur du bifogar ett befintligt delat bildgalleri till ett labbkonto. 

1. På sidan **Labbkonto** väljer du **Delat bildgalleri** på den vänstra menyn och väljer **Bifoga** i verktygsfältet. 

    ![Delat bildgalleri - Knappen Lägg till](../media/how-to-use-shared-image-gallery/sig-attach-button.png)
5. På sidan **Bifoga ett befintligt delat bildgalleri** markerar du det delade bildgalleriet och väljer **OK**.

    ![Markera ett befintligt galleri](../media/how-to-use-shared-image-gallery/select-image-gallery.png)
6. Följande skärm visas: 

    ![Mitt galleri i listan](../media/how-to-use-shared-image-gallery/my-gallery-in-list.png)
    
    I det här exemplet finns det inga bilder i det delade bildgalleriet ännu.

    Azure Lab Services-identitet läggs till som deltagare i det delade avbildningsgalleriet som är kopplat till labbet. Det gör det möjligt för lärare /IT-administratör att spara virtuella datoravbildningar i det delade bildgalleriet. Alla labb som skapas i det här labbkontot har åtkomst till det bifogade delade bildgalleriet. 

    Alla bilder i det bifogade delade bildgalleriet är aktiverade som standard. Du kan aktivera eller inaktivera markerade bilder genom att markera dem i listan och använda knappen **Aktivera markerade bilder** eller Inaktivera markerade **bilder.** 

## <a name="detach-a-shared-image-gallery"></a>Koppla från ett delat bildgalleri
Endast ett delat bildgalleri kan kopplas till ett labb. Om du vill bifoga ett annat delat bildgalleri kopplar du från det aktuella galleriet innan du bifogar det nya galleriet. Om du vill koppla från ett delat bildgalleri från labbet väljer du **Koppla från** i verktygsfältet och bekräftar åtgärden koppla från. 

![Koppla från det delade bildgalleriet från labbkontot](../media/how-to-use-shared-image-gallery/detach.png)

## <a name="next-steps"></a>Nästa steg
Mer information om hur du sparar en labbavbildning i det delade bildgalleriet eller använder en bild från det delade bildgalleriet för att skapa en virtuell dator finns i [Så här använder du delat bildgalleri](how-to-use-shared-image-gallery.md).

Mer information om delade bildgallerier i allmänhet finns i [det delade bildgalleriet](../../virtual-machines/windows/shared-image-galleries.md).
