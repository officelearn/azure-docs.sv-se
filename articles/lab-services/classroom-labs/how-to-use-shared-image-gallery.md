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
ms.date: 05/08/2020
ms.author: spelluru
ms.openlocfilehash: 294a270107033590e340db49b85e8a67acbbb701
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83116874"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>Använd ett delat avbildnings galleri i Azure Lab Services
Den här artikeln visar hur lärare/labb administratören kan spara en mall för virtuella dator avbildningar i ett [delat bild galleri](../../virtual-machines/windows/shared-image-galleries.md) så att den kan användas av andra för att skapa labb. 

## <a name="scenarios"></a>Scenarier
Här är några av de scenarier som stöds av den här funktionen: 

- En labb konto administratör bifogar ett delat avbildnings galleri till labb kontot och laddar upp en bild till galleriet för delad avbildning utanför kontexten för ett labb. Sedan kan Lab-skapare använda avbildningen från galleriet för delad avbildning för att skapa labb labb. 
- Ett labb konto administratör bifogar ett delat avbildnings galleri till labb kontot. En labb skapare (instruktör) sparar den anpassade avbildningen av sitt labb labb i det delade avbildnings galleriet. Sedan kan andra labb skapare välja den här avbildningen från galleriet för delade avbildningar för att skapa en mall för sina labb. 

    När en bild sparas i ett delat bild galleri, Azure Lab Services replikerar den sparade avbildningen till andra regioner som är tillgängliga i samma [geografi](https://azure.microsoft.com/global-infrastructure/geographies/). Det garanterar att avbildningen är tillgänglig för labb som skapats i andra regioner i samma geografi. Att spara bilder i ett delat avbildnings Galleri debiteras ytterligare en kostnad, vilket innefattar kostnad för alla replikerade avbildningar. Kostnaden är separat från Azure Lab Services användnings kostnad. Mer information om priser för delade avbildnings gallerier finns i avsnittet [Shared Image Gallery – fakturering]( https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#billing).

    > [!NOTE]
    > Azure Lab Services har stöd för att skapa virtuella mall-datorer baserat på både **generaliserade** och **specialiserade** avbildningar i ett delat avbildnings Galleri. 


## <a name="prerequisites"></a>Krav
- Skapa ett galleri för delad avbildning med hjälp av antingen [Azure PowerShell](../../virtual-machines/windows/shared-images.md) eller [Azure CLI](../../virtual-machines/linux/shared-images.md).
- Du har kopplat det delade avbildnings galleriet till labb kontot. Stegvisa instruktioner finns i [så här ansluter du eller kopplar från delade avbildnings galleriet](how-to-attach-detach-shared-image-gallery.md).


## <a name="save-an-image-to-the-shared-image-gallery"></a>Spara en bild i galleriet för delade avbildningar
När ett delat avbildnings Galleri har kopplats kan en labb konto administratör eller en lärare Spara en bild i det delade bild galleriet så att den kan återanvändas av andra lärare. 

1. På **mall** -sidan för labbet väljer du **Exportera till delat avbildnings Galleri** i verktygsfältet.

    ![Knappen Spara bild](../media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-button.png)
2. I dialog rutan **Exportera till delad avbildnings Galleri** anger **du ett namn för avbildningen**och väljer sedan **Exportera**. 

    ![Dialog rutan exportera till delat bild galleri](../media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-dialog.png)
3. Du kan se förloppet för den här åtgärden på **mallsidan** . Den här åtgärden kan ta en stund. 

    ![Export pågår](../media/how-to-use-shared-image-gallery/exporting-image-in-progress.png)
4. När export åtgärden lyckas visas följande meddelande:

    ![Export slutförd](../media/how-to-use-shared-image-gallery/exporting-image-completed.png)

    > [!NOTE]
    > När du har sparat avbildningen i det delade bild galleriet kan du använda den bilden från galleriet när du skapar ett nytt labb. 
    > 
    > Du kan också ladda upp en avbildning till galleriet för delade avbildningar utanför kontexten för ett labb. Mer information finns i [Översikt över Shared Image Gallery](../../virtual-machines/windows/shared-images.md). 

    

## <a name="use-an-image-from-the-shared-image-gallery"></a>Använd en avbildning från galleriet för delad avbildning
En lärare/lärare kan välja en anpassad avbildning som är tillgänglig i galleriet för delade avbildningar för mallen när nya labb skapas.

![Använd avbildningen av en virtuell dator från galleriet](../media/how-to-use-shared-image-gallery/use-shared-image.png)

## <a name="next-steps"></a>Nästa steg
Mer information om delade avbildnings gallerier finns i avsnittet [Shared Image Gallery](../../virtual-machines/windows/shared-image-galleries.md).
