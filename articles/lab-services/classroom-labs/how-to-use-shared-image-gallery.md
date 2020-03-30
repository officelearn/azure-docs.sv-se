---
title: Använda ett delat avbildningsgalleri i Azure Lab Services | Microsoft-dokument
description: Lär dig hur du konfigurerar ett labbkonto för att använda ett delat bildgalleri så att en användare kan dela en avbildning med andra och en annan användare kan använda avbildningen för att skapa en virtuell mall virtuell dator i labbet.
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
ms.openlocfilehash: c611ecdb5a2534f7368e533e3e19e6e3f96de57f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190457"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>Använda ett delat avbildningsgalleri i Azure Lab Services
Den här artikeln visar hur lärare/labbadministratör kan spara en mallavbildning för virtuella datorer för att den ska återanvändas av andra. Dessa avbildningar sparas i ett [delat azure-bildgalleri](../../virtual-machines/windows/shared-image-galleries.md). Som ett första steg bifogar labbadministratören ett befintligt delat bildgalleri till labbkontot. När det delade bildgalleriet är bifogat kan övningar som skapats i labbkontot spara bilder i det delade bildgalleriet. Andra lärare kan välja den här bilden från det delade bildgalleriet för att skapa en mall för sina klasser. 

> [!NOTE]
> Azure Lab Services stöder för närvarande skapandet av virtuella mall-datorer baserat på endast **generaliserade** VM-avbildningar (inte specialiserade avbildningar) i ett delat bildgalleri. 

## <a name="prerequisites"></a>Krav
- Skapa ett delat avbildningsgalleri med hjälp av [Azure PowerShell](../../virtual-machines/windows/shared-images.md) eller [Azure CLI](../../virtual-machines/linux/shared-images.md).
- Du har bifogat det delade bildgalleriet till labbkontot. Stegvisa instruktioner finns i [Så här bifogar eller kopplar du från delat bildgalleri](how-to-attach-detach-shared-image-gallery.md).


## <a name="save-an-image-to-the-shared-image-gallery"></a>Spara en bild i det delade bildgalleriet
När ett delat bildgalleri har bifogats kan en labbkontoadministratör eller en lärare spara en bild i det delade bildgalleriet så att den kan återanvändas av andra lärare. 

1. På **sidan Mall** för labbet väljer du **Exportera till delat bildgalleri** i verktygsfältet.

    ![Knappen Spara bild](../media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-button.png)
2. I dialogrutan **Exportera till delat bildgalleri** anger du ett **namn på bilden**och väljer sedan **Exportera**. 

    ![Dialogrutan Exportera till delat bildgalleri](../media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-dialog.png)
3. Du kan se förloppet för den här åtgärden på **sidan Mall.** Den här operationen kan ta någon gång. 

    ![Export pågår](../media/how-to-use-shared-image-gallery/exporting-image-in-progress.png)
4. När exportåtgärden lyckas visas följande meddelande:

    ![Exporten har slutförts](../media/how-to-use-shared-image-gallery/exporting-image-completed.png)

Du kan också ladda upp en bild till det delade bildgalleriet utanför ramen för ett labb. Mer information finns i [Översikt över delat bildgalleri](../../virtual-machines/windows/shared-images.md). 

## <a name="use-an-image-from-the-shared-image-gallery"></a>Använda en bild från det delade bildgalleriet
En lärare/professor kan välja en anpassad bild som är tillgänglig i det delade bildgalleriet för mallen när nya labbet skapas.

![Använda avbildning för virtuella datorer från galleriet](../media/how-to-use-shared-image-gallery/use-shared-image.png)

## <a name="next-steps"></a>Nästa steg
Mer information om delade bildgallerier finns i [det delade bildgalleriet](../../virtual-machines/windows/shared-image-galleries.md).
