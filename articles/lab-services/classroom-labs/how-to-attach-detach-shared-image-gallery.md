---
title: Koppla eller koppla från ett delat avbildnings galleri i Azure Lab Services | Microsoft Docs
description: I den här artikeln beskrivs hur du ansluter ett delat avbildnings galleri till ett klass rums labb i Azure Lab Services.
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
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78360584"
---
# <a name="attach-or-detach-a-shared-image-gallery-in-azure-lab-services"></a>Koppla eller koppla från ett delat avbildnings galleri i Azure Lab Services
Lärare/labb administratören kan spara en mall för en virtuell dator i ett galleri för Azure- [delade avbildningar](../../virtual-machines/windows/shared-image-galleries.md) så att de kan återanvändas av andra. Som ett första steg kopplar labb administratören ett befintligt delat avbildnings galleri till labb kontot. När du har bifogat det delade bild galleriet kan labb som skapats i labb kontot Spara bilder i det delade avbildnings galleriet. Andra lärare kan välja den här avbildningen från galleriet för delade avbildningar för att skapa en mall för deras klasser. 

När en bild sparas i ett delat bild galleri, Azure Lab Services replikerar den sparade avbildningen till andra regioner som är tillgängliga i samma [geografi](https://azure.microsoft.com/global-infrastructure/geographies/). Det garanterar att avbildningen är tillgänglig för labb som skapats i andra regioner i samma geografi. Att spara bilder i ett delat avbildnings Galleri debiteras ytterligare en kostnad, vilket innefattar kostnad för alla replikerade avbildningar. Kostnaden är separat från Azure Lab Services användnings kostnad. Mer information om priser för delade avbildnings gallerier finns i avsnittet [Shared Image Gallery – fakturering]( https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#billing).

Den här artikeln visar hur du kopplar eller kopplar från ett delat avbildnings galleri till ett labb konto. 

> [!NOTE]
> För närvarande stöder Azure Lab Services skapandet av mall-VM: ar baserat på endast **generaliserade** VM-avbildningar (inte specialiserade bilder) i ett delat avbildnings Galleri. 


## <a name="configure-at-the-time-of-lab-account-creation"></a>Konfigurera när labb kontot skapas
När du skapar ett labb konto kan du koppla ett delat avbildnings galleri till labb kontot. Du kan antingen välja ett befintligt delat avbildnings Galleri från den nedrullningsbara listan eller skapa ett nytt. Om du vill skapa och koppla ett delat avbildnings galleri till labb kontot väljer du **Skapa nytt**, anger ett namn för galleriet och anger **OK**. 

![Konfigurera det delade avbildnings galleriet när labb kontot skapas](../media/how-to-use-shared-image-gallery/new-lab-account.png)

## <a name="configure-after-the-lab-account-is-created"></a>Konfigurera när labb kontot har skapats
När du har skapat labb kontot kan du utföra följande uppgifter:

- Skapa och bifoga ett delat avbildnings Galleri
- Koppla ett delat avbildnings galleri till labb kontot
- Koppla bort ett delat avbildnings Galleri från labb kontot

## <a name="create-and-attach-a-shared-image-gallery"></a>Skapa och bifoga ett delat avbildnings Galleri
1. Logga in på [Azure Portal](https://portal.azure.com).
2. På menyn till vänster väljer du **Alla tjänster**. Välj **labb tjänster** i avsnittet **DEVOPS** . Om du väljer Star (`*`) bredvid **labb tjänster**läggs den till i avsnittet **Favoriter** på den vänstra menyn. I nästa steg kan du välja **labb tjänster** under **Favoriter**.

    ![Alla tjänster – > Lab-tjänster](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Välj ditt labb konto för att se sidan **labb konto** . 
4. Välj **Galleri för delad avbildning** på den vänstra menyn och välj **+ skapa** i verktygsfältet.  

    ![Knappen Skapa delad bild galleri](../media/how-to-use-shared-image-gallery/new-shared-image-gallery-button.png)
5. I fönstret **skapa delad avbildnings Galleri** anger du ett **namn** för galleriet och anger **OK**. 

    ![Skapa ett delat bild galleri fönster](../media/how-to-use-shared-image-gallery/create-shared-image-gallery-window.png)

    Azure Lab Services skapar galleriet för delade avbildningar och bifogar det till labb kontot. Alla labb som skapats i det här labb kontot har åtkomst till den bifogade delade avbildnings galleriet. 

    ![Galleri för bifogade bilder](../media/how-to-use-shared-image-gallery/image-gallery-in-list.png)

    I det nedre fönstret visas bilder i det delade bild galleriet. Det finns inga bilder i det här nya galleriet. När du laddar upp bilder till galleriet kan du se dem på den här sidan.     

    Alla avbildningar i den bifogade delade avbildnings galleriet är aktiverade som standard. Du kan aktivera eller inaktivera valda avbildningar genom att markera dem i listan och använda knappen **aktivera markerade bilder** eller **inaktivera valda avbildningar** .

## <a name="attach-an-existing-shared-image-gallery"></a>Bifoga ett befintligt delat avbildnings Galleri
Följande procedur visar hur du ansluter ett befintligt delat avbildnings galleri till ett labb konto. 

1. På sidan **labb konto** väljer du **delad avbildnings Galleri** på den vänstra menyn och väljer **koppla** i verktygsfältet. 

    ![Galleri för delad avbildning – Lägg till knapp](../media/how-to-use-shared-image-gallery/sig-attach-button.png)
5. På sidan **bifoga en befintlig delad avbildning** väljer du ditt delade avbildnings galleri och väljer **OK**.

    ![Välj ett befintligt Galleri](../media/how-to-use-shared-image-gallery/select-image-gallery.png)
6. Följande skärm bild visas: 

    ![Mitt galleri i listan](../media/how-to-use-shared-image-gallery/my-gallery-in-list.png)
    
    I det här exemplet finns det inga avbildningar i galleriet för delade avbildningar ännu.

    Azure Lab Services identitet läggs till som deltagare i det delade avbildnings galleriet som är kopplat till labbet. Det gör det möjligt för lärare/IT-administratörer att spara avbildningar av virtuella datorer i det delade avbildnings galleriet. Alla labb som skapats i det här labb kontot har åtkomst till den bifogade delade avbildnings galleriet. 

    Alla avbildningar i den bifogade delade avbildnings galleriet är aktiverade som standard. Du kan aktivera eller inaktivera valda avbildningar genom att markera dem i listan och använda knappen **aktivera markerade bilder** eller **inaktivera valda avbildningar** . 

## <a name="detach-a-shared-image-gallery"></a>Koppla från ett delat avbildnings Galleri
Endast ett delat avbildnings Galleri kan kopplas till ett labb. Om du vill koppla ett annat galleri för delad avbildning tar du bort det nuvarande innan du kopplar det nya. Om du vill koppla från ett delat avbildnings Galleri från labbet väljer du **koppla** från i verktygsfältet och bekräftar från kopplings åtgärden. 

![Koppla bort galleriet för delade avbildningar från labb kontot](../media/how-to-use-shared-image-gallery/detach.png)

## <a name="next-steps"></a>Nästa steg
Information om hur du sparar en labb avbildning i det delade avbildnings galleriet eller använder en avbildning från galleriet för delade avbildningar för att skapa en virtuell dator finns i [använda delade avbildnings galleriet](how-to-use-shared-image-gallery.md).

Mer information om delade avbildnings gallerier i allmänhet finns i avsnittet [Shared Image Gallery](../../virtual-machines/windows/shared-image-galleries.md).
