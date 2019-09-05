---
title: Använd ett delat avbildnings galleri i Azure Lab Services | Microsoft Docs
description: Lär dig hur du konfigurerar ett labb konto för att använda ett delat bild galleri så att en användare kan dela en avbildning med andra och en annan användare kan använda avbildningen för att skapa en mall för virtuella datorer i labbet.
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
ms.date: 09/05/2019
ms.author: spelluru
ms.openlocfilehash: 5ce0a81f6c9c886fcbe9186dd7363c38170ca580
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382957"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>Använd ett delat avbildnings galleri i Azure Lab Services
Den här artikeln visar hur lärare/labb administratören kan spara en mall för en virtuell dator för att återanvändas av andra. Dessa bilder sparas i ett galleri för Azure- [delade avbildningar](../../virtual-machines/windows/shared-image-galleries.md). Som ett första steg kopplar labb administratören ett befintligt delat avbildnings galleri till labb kontot. När du har bifogat det delade bild galleriet kan labb som skapats i labb kontot Spara bilder i det delade avbildnings galleriet. Andra lärare kan välja den här avbildningen från galleriet för delade avbildningar för att skapa en mall för deras klasser. 

## <a name="prerequisites"></a>Förutsättningar
- Skapa ett galleri för delad avbildning med hjälp av antingen [Azure PowerShell](../../virtual-machines/windows/shared-images.md) eller [Azure CLI](../../virtual-machines/linux/shared-images.md).
- Du har kopplat det delade avbildnings galleriet till labb kontot. Stegvisa instruktioner finns i [så här ansluter du eller kopplar från delade avbildnings galleriet](how-to-attach-detach-shared-image-gallery.md).


## <a name="save-an-image-to-the-shared-image-gallery"></a>Spara en bild i galleriet för delade avbildningar
När ett delat avbildnings Galleri har kopplats kan en labb konto administratör eller en lärare Spara en bild i det delade bild galleriet så att den kan återanvändas av andra lärare. 

1. På Start sidan för labbet väljer du **Spara bild** på panelen i avsnittet **mall** .

    ![Knappen Spara bild](../media/how-to-use-shared-image-gallery/save-image-button.png)
2.  I fönstret **Spara avbildning av virtuell dator** anger du ett namn för avbildningen och väljer **Spara**. 

    ![Spara fönstret avbildning av virtuell dator](../media/how-to-use-shared-image-gallery/save-virtual-machine-image.png)
3. Kontrol lera status på panelen labb. 

    ![Status för åtgärden Spara avbildning](../media/how-to-use-shared-image-gallery/save-image-status.png)

 Du kan också ladda upp en avbildning till galleriet för delade avbildningar utanför kontexten för ett labb. Mer information finns i [Översikt över Shared Image Gallery](../../virtual-machines/windows/shared-images.md). 

## <a name="use-an-image-from-the-shared-image-gallery"></a>Använd en avbildning från galleriet för delad avbildning
En lärare/lärare kan välja en anpassad avbildning som är tillgänglig i galleriet för delade avbildningar för mallen när nya labb skapas.

![Använd avbildningen av en virtuell dator från galleriet](../media/how-to-use-shared-image-gallery/use-shared-image.png)

## <a name="next-steps"></a>Nästa steg
Mer information om delade avbildnings gallerier finns i avsnittet [Shared Image Gallery](../../virtual-machines/windows/shared-image-galleries.md).
