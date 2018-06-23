---
title: Hantera versioner i THOMAS appar i Azure | Microsoft Docs
description: Lär dig mer om att hantera versioner i språk förstå (THOMAS) program.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/29/2017
ms.author: v-geberr
ms.openlocfilehash: 672f7991be0fc236e39daf7d1ce1d6080b31815b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351702"
---
# <a name="manage-versions"></a>Hantera versioner

Varje gång du arbeta med en modell som skapar en annan [version](luis-concept-version.md) av appen. 

## <a name="set-active-version"></a>Ange active version
Om du vill arbeta med versioner, öppna appen genom att markera namnet på **Mina appar** och väljer sedan **inställningar** på den översta raden.

![Versioner sida](./media/luis-how-to-manage-versions/settings.png)

Den **inställningar** sidan kan du konfigurera inställningar för hela appen inklusive versioner och medarbetare. 

## <a name="clone-a-version"></a>Klona en version
1. På den **inställningar** sidan efter avsnitten App-inställningar och samarbetspartner hitta raden med den version du vill klona. Välj de tre punkterna (...) på längst till höger. 

    ![Version radegenskaper](./media/luis-how-to-manage-versions/version-section.png)

2. Välj **klona** från listan.

    ![Version rad egenskaper val](./media/luis-how-to-manage-versions/version-three-dots-modal.png)

3. I den **klona version** dialogrutan, ange ett namn för den nya versionen, till exempel ”0,2”.

   ![Dialogrutan för kloning Version](./media/luis-how-to-manage-versions/version-clone-version-dialog.png)
 
 > [!NOTE]
 > Version ID kan endast bestå av tecken, siffror eller '.' och får inte vara längre än 10 tecken.
 
 En ny version med det angivna namnet skapas och ange den aktiva versionen.
 
  ![Version skapas och läggs till i listan](./media/luis-how-to-manage-versions/new-version.png)

 > [!NOTE]
 > I föregående bild visas en publicerad version är associerad med ett färgade märke, som anger typen av plats där den har publicerats: produktion (grön), mellanlagring (röd) och båda (svart). Utbildning och publishing datumen visas för varje publicerad version.

## <a name="set-active-version"></a>Ange active version
1. På den **inställningar** sidan den **versioner** väljer du de tre punkterna (…) längst till höger.

2. I popup-listan, Välj **aktivera**.

    ![Ange active version](./media/luis-how-to-manage-versions/set-active-version.png)

    Den aktiva versionen är markerade med en blå ljusare som visas i följande skärmbild:

    ![Den aktiva versionen](./media/luis-how-to-manage-versions/set-active-version-done.png) 


## <a name="import-version"></a>Importera version
Du kan importera en version från en JSON-fil. När du importerar en version, blir den aktiva versionen i den nya versionen.

**Importera en version:**

1. På den **inställningar** väljer **Importera nya version** knappen.

    ![Knappen Importera](./media/luis-how-to-manage-versions/import-version.png)

2. Välj **Bläddra** och välj JSON-filen.

    ![Importera version dialogrutan](./media/luis-how-to-manage-versions/import-version-dialog.png)

Du behöver bara ange ett versions-ID om versionen i JSON-filen finns redan i appen.

## <a name="export-version"></a>Exportera version
Du kan exportera en version till en JSON-fil.

**Så här exporterar du en version:**

1. På den **inställningar** sidan den **versioner** väljer du de tre punkterna (…) längst till höger.

2. Välj **exportera** i popup-listan över åtgärder och välj där du vill spara filen.

## <a name="delete-a-version"></a>Ta bort en version
Du kan ta bort versioner, men du behöver ha minst en version av appen. Du kan ta bort alla versioner utom den aktiva versionen. 

1. På den **inställningar** sidan den **versioner** väljer du de tre punkterna (…) längst till höger.

2. Välj **ta bort** i popup-listan över åtgärder och välj där du vill spara filen.

    ![Version bekräftelse på borttagning](./media/luis-how-to-manage-versions/delete-menu.png) 


## <a name="rename-a-version"></a>Byt namn på en version
Du kan byta namn på versioner som versionsnamnet inte är redan används.  

1. På den **inställningar** sidan den **versioner** väljer du de tre punkterna (…) längst till höger.

2. Välj **Byt namn på** i popup-listan över åtgärder.

3. Ange det nya versionsnamnet och välj **klar**.

    ![Byt namn på version bekräftelse](./media/luis-how-to-manage-versions/rename-popup.png) 
