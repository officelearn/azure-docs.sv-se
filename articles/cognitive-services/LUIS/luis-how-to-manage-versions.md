---
title: Hantera versioner i LUIS-appar i Azure | Microsoft Docs
description: Lär dig mer om att hantera versioner i Språkförståelse (LUIS) program.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/29/2017
ms.author: diberry
ms.openlocfilehash: 4941cf533f1b860ead07a416d5af6f62a1978305
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2018
ms.locfileid: "39226602"
---
# <a name="manage-versions"></a>Hantera versioner

Varje gång som du arbetar med modellen, skapa en annan [version](luis-concept-version.md) av appen. 

## <a name="set-active-version"></a>Ange active version
Om du vill arbeta med versioner, kan du öppna din app genom att välja dess namn på **Mina appar** och välj sedan **inställningar** i det översta fältet.

![Sidan med versioner](./media/luis-how-to-manage-versions/settings.png)

Den **inställningar** sidan kan du konfigurera inställningar för hela appen, inklusive versioner och medarbetare. 

## <a name="clone-a-version"></a>Klona en version
1. På den **inställningar** sidan efter avsnitten Appinställningar och medarbetare hitta raden med den version du vill klona. Välj ellipsen (***...*** )-knappen längst till höger. 

    ![Version radegenskaper](./media/luis-how-to-manage-versions/version-section.png)

2. Välj **klona** i listan.

    ![Version rad egenskaper val](./media/luis-how-to-manage-versions/version-three-dots-modal.png)

3. I den **klona version** dialogrutan, ange ett namn för den nya versionen, till exempel ”0.2”.

   ![Dialogrutan för kloning Version](./media/luis-how-to-manage-versions/version-clone-version-dialog.png)
 
 > [!NOTE]
 > Version ID bestå av tecken, siffror eller '.' och får inte vara längre än 10 tecken.
 
 En ny version med det angivna namnet skapas och Ställ in som den aktiva versionen.
 
  ![Version skapas och läggs till i listan](./media/luis-how-to-manage-versions/new-version.png)

 > [!NOTE]
 > I föregående bild visas en publicerad version är associerad med en färgad markering, som anger typen av plats där den har publicerats: produktion (grön), mellanlagring (röd), och båda (svart). Utbildning och publicera datumen visas för varje utgiven version.

## <a name="set-active-version"></a>Ange active version
1. På den **inställningar** sidan den **versioner** väljer du de tre punkterna (***...*** ) längst till höger.

2. I popup-listan, väljer **ange som aktiv**.

    ![Ange active version](./media/luis-how-to-manage-versions/set-active-version.png)

    Den aktiva versionen markeras med en blå ljusare enligt följande skärmbild:

    ![Den aktiva versionen](./media/luis-how-to-manage-versions/set-active-version-done.png) 


## <a name="import-version"></a>Import-version
Du kan importera en version från en JSON-fil. När du importerar en version, blir den aktiva versionen i den nya versionen.

**Importera en version:**

1. På den **inställningar** väljer **Importera nya version** knappen.

    ![Knappen Importera](./media/luis-how-to-manage-versions/import-version.png)

2. Välj **Bläddra** och välj JSON-filen.

    ![Dialogrutan för import till version](./media/luis-how-to-manage-versions/import-version-dialog.png)

Du behöver bara ange ett versions-ID om versionen i JSON-filen finns redan i appen.

## <a name="export-version"></a>Exportera version
Du kan exportera en version till en JSON-fil.

**Så här exporterar du en version:**

1. På den **inställningar** sidan den **versioner** väljer du de tre punkterna (***...*** ) längst till höger.

2. Välj **exportera** i popup-listan över åtgärder och välj där du vill spara filen.

## <a name="delete-a-version"></a>Ta bort en version
Du kan ta bort versioner, men du behöver ha minst en version av appen. Du kan ta bort alla versioner utom den aktiva versionen. 

1. På den **inställningar** sidan den **versioner** väljer du de tre punkterna (***...*** ) längst till höger.

2. Välj **ta bort** i popup-listan över åtgärder och välj där du vill spara filen.

    ![Version Bekräfta borttagning](./media/luis-how-to-manage-versions/delete-menu.png) 


## <a name="rename-a-version"></a>Byt namn på en version
Du kan byta namn på versioner så länge versionsnamnet inte är redan används.  

1. På den **inställningar** sidan den **versioner** väljer du de tre punkterna (***...*** ) längst till höger.

2. Välj **Byt namn på** i popup-listan över åtgärder.

3. Ange versionsnamnet på nya och välj **klar**.

    ![Byt namn på version bekräftelse](./media/luis-how-to-manage-versions/rename-popup.png) 
