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
ms.date: 05/07/2019
ms.author: spelluru
ms.openlocfilehash: 8d8b6fffe197d4180b091518dcd1615d0e0b9d19
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2019
ms.locfileid: "65412859"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>Använda en delad bildgalleriet i Azure Lab Services
Den här artikeln visar hur lärare/testlabb-administratören kan spara en avbildning av virtuell dator mall för att det ska återanvändas av andra. Dessa avbildningar sparas i en Azure [delade bildgalleriet](../../virtual-machines/windows/shared-image-galleries.md). Som ett första steg bifogar lab administratören en befintliga delade bildgalleriet till labb-kontot. Delade bildgalleriet är kopplade labs som skapats i labb-kontot kan spara bilder i delade avbildningsgalleri. Andra lärare kan välja den här avbildningen från det delade avbildningsgalleriet så att du skapar en mall för deras klasser. 

## <a name="prerequisites"></a>Nödvändiga komponenter
- Skapa en delad bildgalleriet genom att använda antingen [Azure PowerShell](../../virtual-machines/windows/shared-images.md) eller [Azure CLI](../../virtual-machines/linux/shared-images.md).
- Du har kopplat delade bildgalleriet till labbkonto. Stegvisa instruktioner finns i [så ansluta eller koppla från delade bildgalleriet](how-to-attach-detach-shared-image-gallery.md).


## <a name="save-an-image-to-the-shared-image-gallery"></a>Spara en avbildning i delade avbildningsgalleri
När en delad bildgalleriet kopplas en lärare spara eller ladda upp en bild i delade avbildningsgalleri så att den kan återanvändas av andra lärare. Anvisningar för att ladda upp en bild i delade avbildningsgalleri finns i [delad bildgalleriet översikt](../../virtual-machines/windows/shared-images.md). 

> [!NOTE]
> Konfigurationfilen, stöder labb för klassrum-användargränssnittet (UI) inte sparar en labb-avbildning i delade avbildningsgalleri. 

## <a name="use-an-image-from-the-shared-image-gallery"></a>Använda en avbildning från avbildningsgalleriet delade
En lärare kan välja en anpassad avbildning som är tillgängliga i delade bildgalleriet för mallen under skapandet av ny labb.

![Använd avbildning av virtuell dator från galleriet](../media/how-to-use-shared-image-gallery/use-shared-image.png)

## <a name="next-steps"></a>Nästa steg
Mer information om delade bildgallerier finns [delade bildgalleriet](../../virtual-machines/windows/shared-image-galleries.md).
