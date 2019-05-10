---
title: Ansluta eller koppla från en delad bildgalleriet i Azure Lab Services | Microsoft Docs
description: Lär dig hur du kopplar en delad bildgalleriet till ett labb i Azure Lab Services.
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
ms.openlocfilehash: de4e9fb4b15f4c346926fe46f23255c668204c2e
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2019
ms.locfileid: "65413893"
---
# <a name="attach-or-detach-a-shared-image-gallery-in-azure-lab-services"></a>Ansluta eller koppla från en delad bildgalleriet i Azure Lab Services
Lärare/testlabb-administratören kan spara en mall för VM-avbildning i en Azure [delade bildgalleriet](../../virtual-machines/windows/shared-image-galleries.md) för att det ska återanvändas av andra. Som ett första steg bifogar lab administratören en befintliga delade bildgalleriet till labb-kontot. Delade bildgalleriet är kopplade labs som skapats i labb-kontot kan spara bilder i delade avbildningsgalleri. Andra lärare kan välja den här avbildningen från det delade avbildningsgalleriet så att du skapar en mall för deras klasser. 

Den här artikeln visar hur du ansluta eller koppla från en delad bildgalleriet till ett labbkonto. 

## <a name="configure-at-the-time-of-lab-account-creation"></a>Konfigurera vid tidpunkten för labb har skapats
När du skapar ett labbkonto, kan du koppla en delad bildgalleriet till labb-kontot. Du kan välja ett befintligt delat avbildningsgalleri från den nedrullningsbara listan, eller så kan du skapa en ny. För att skapa och koppla en delad bildgalleriet till labb-kontot, Välj **Skapa nytt**, ange ett namn för galleriet och ange **OK**. 

![Konfigurera delade bildgalleriet vid tidpunkten för labb har skapats](../media/how-to-use-shared-image-gallery/new-lab-account.png)

## <a name="configure-after-the-lab-account-is-created"></a>Konfigurera när labb-kontot har skapats
När labbkonto har skapats kan göra du följande uppgifter:

- Skapa och koppla en delad bildgalleri
- Koppla en delad bildgalleriet till labbkonto
- Koppla från en delad bildgalleriet från labbkonto

## <a name="create-and-attach-a-shared-image-gallery"></a>Skapa och koppla en delad bildgalleri
1. Logga in på [Azure Portal](https://portal.azure.com).
2. På menyn till vänster väljer du **Alla tjänster**. Välj **Lab Services** i den **DEVOPS** avsnittet. Om du väljer star (`*`) bredvid **Lab Services**, läggs den till den **Favoriter** avsnitt i den vänstra menyn. Nästa gång och senare, väljer du **Lab Services** under **Favoriter**.

    ![Alla tjänster -> Lab Services](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Välj ditt labbkonto för att se den **Labbkonto** sidan. 
4. Välj **delad bildgalleriet** på den vänstra menyn och välj **+ skapa** i verktygsfältet.  

    ![Skapa delade bild galleri-knapp](../media/how-to-use-shared-image-gallery/new-shared-image-gallery-button.png)
5. I den **skapa delade bildgalleriet** fönstret, ange en **namn** för galleriet, och ange **OK**. 

    ![Skapa delade bild gallery-fönstret](../media/how-to-use-shared-image-gallery/create-shared-image-gallery-window.png)

    Azure Lab Services skapar delad avbildningsgalleriet och bifogas labbkonto. Alla labs som skapats i den här testlabb-kontot har åtkomst till anslutna delade bildgalleriet. 

    ![Bifogad bild-galleriet](../media/how-to-use-shared-image-gallery/image-gallery-in-list.png)

    Längst ned i fönstret, kan du se avbildningar i galleriet med delade avbildningar. Det finns inga bilder i det här nya galleriet. När du överför avbildningar i galleriet, visas de på den här sidan.     

    Alla avbildningar i galleriet med anslutna delade avbildningar är aktiverade som standard. Du kan aktivera eller inaktivera valda avbildningar genom att markera dem i listan och använda den **aktivera valda avbildningar** eller **inaktivera valda avbildningar** knappen.

## <a name="attach-an-existing-shared-image-gallery"></a>Bifoga en befintlig delad bildgalleri
Följande procedur visar hur du kopplar ett galleri med befintliga delade avbildningar till ett labbkonto. 

1. På den **Labbkonto** väljer **delad bildgalleriet** på den vänstra menyn och välj **bifoga** i verktygsfältet. 

    ![Delade bild galleriet – Lägg till knapp](../media/how-to-use-shared-image-gallery/sig-attach-button.png)
5. På den **bifoga en befintlig delad bildgalleriet** väljer bildgalleriet delade, och välj **OK**.

    ![Välj ett befintligt galleri](../media/how-to-use-shared-image-gallery/select-image-gallery.png)
6. Du ser följande skärm: 

    ![Min galleriet i listan](../media/how-to-use-shared-image-gallery/my-gallery-in-list.png)
    
    I det här exemplet finns inga bilder i delade avbildningsgalleri ännu.

    Azure Lab Services identitet har lagts till som deltagare i delade avbildningsgalleri som är kopplad till labbet. Det gör att lärare / IT-administratören att spara VM-avbildningar i delade avbildningsgalleri. Alla labs som skapats i den här testlabb-kontot har åtkomst till anslutna delade bildgalleriet. 

    Alla avbildningar i galleriet med anslutna delade avbildningar är aktiverade som standard. Du kan aktivera eller inaktivera valda avbildningar genom att markera dem i listan och använda den **aktivera valda avbildningar** eller **inaktivera valda avbildningar** knappen. 

## <a name="save-an-image-to-the-shared-image-gallery"></a>Spara en avbildning i delade avbildningsgalleri
När en delad bildgalleriet kopplas en labb-kontoadministratören eller en lärare spara eller ladda upp en bild i delade avbildningsgalleri så att den kan återanvändas av andra lärare. Anvisningar för att ladda upp en bild i delade avbildningsgalleri finns i [delad bildgalleriet översikt](../../virtual-machines/windows/shared-images.md). 

> [!NOTE]
> Konfigurationfilen, stöder labb för klassrum-användargränssnittet (UI) inte sparar en labb-avbildning i delade avbildningsgalleri. 

## <a name="detach-a-shared-image-gallery"></a>Koppla från en delad bildgalleri
Endast en delad bildgalleriet kan kopplas till ett labb. Om du vill koppla en annan delad bildgalleriet koppla från den aktuella artikeln innan du kopplar den nya servern. Om du vill koppla bort en delad bildgalleriet i din Övning, Välj **Detach** i verktygsfältet och Bekräfta vid frigörandet. 

![Koppla från delade bildgalleriet från labbkonto](../media/how-to-use-shared-image-gallery/detach.png)

## <a name="next-steps"></a>Nästa steg
Läs om hur du sparar en labb-avbildning i delade avbildningsgalleri eller använda en avbildning från avbildningsgalleriet delade skapa en virtuell dator i [hur du använder delade bildgalleriet](how-to-use-shared-image-gallery.md).

För mer information om delade bildgallerier i allmänhet, se [delade bildgalleriet](../../virtual-machines/windows/shared-image-galleries.md).
