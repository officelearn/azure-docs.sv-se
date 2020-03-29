---
title: Hantera versioner - LUIS
titleSuffix: Azure Cognitive Services
description: Med versioner kan du skapa och publicera olika modeller. En bra idé är att klona den aktuella aktiva modellen till en annan version av appen innan du gör ändringar i modellen.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: 138b84a9b7f54782fd6254304a3fdcf4dba83182
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220901"
---
# <a name="use-versions-to-edit-and-test-without-impacting-staging-or-production-apps"></a>Använda versioner för att redigera och testa utan att påverka mellanlagrings- eller produktionsappar

Med versioner kan du skapa och publicera olika modeller. En bra idé är att klona den aktuella aktiva modellen till en annan [version](luis-concept-version.md) av appen innan du gör ändringar i modellen. 

Om du vill arbeta med versioner öppnar du appen genom att välja dess namn på sidan **Mina appar** och väljer sedan **Hantera** i det övre fältet och väljer sedan **Versioner** i den vänstra navigeringen. 

Listan över versioner visar vilka versioner som publiceras, var de publiceras och vilken version som för närvarande är aktiv. 

> [!div class="mx-imgBorder"]
> [![Hantera avsnitt, versionssida](./media/luis-how-to-manage-versions/versions-import.png "Hantera avsnitt, versionssida")](./media/luis-how-to-manage-versions/versions-import.png#lightbox)

## <a name="clone-a-version"></a>Klona en version

1. Välj den version som du vill klona och välj sedan **Klona** i verktygsfältet. 

2. Skriv ett namn på den nya versionen som "0.2" i dialogrutan **Klonversion.**

   ![Dialogrutan Klonversion](./media/luis-how-to-manage-versions/version-clone-version-dialog.png)
 
     > [!NOTE]
     > Versions-ID kan endast bestå av tecken, siffror eller '.' och får inte vara längre än 10 tecken.
 
   En ny version med det angivna namnet skapas och anges som den aktiva versionen.

## <a name="set-active-version"></a>Ange aktiv version

Välj en version i listan och välj sedan **Aktivera** i verktygsfältet. 

> [!div class="mx-imgBorder"]
> [![Hantera avsnitt, versionssida, gör en versionsåtgärd](./media/luis-how-to-manage-versions/versions-other.png "Hantera avsnitt, versionssida, gör en versionsåtgärd")](./media/luis-how-to-manage-versions/versions-other.png#lightbox)

## <a name="import-version"></a>Importera version

Du kan `.json` importera `.lu` en eller en version av programmet.

1. Välj **Importera** i verktygsfältet och välj sedan formatet. 

2. I popup-fönstret **Importera ny version** anger du det nya versionsnamnet med tio tecken. Du behöver bara ange ett versions-ID om versionen i filen redan finns i appen.

    ![Hantera avsnitt, versionssida, importera ny version](./media/luis-how-to-manage-versions/versions-import-pop-up.png)

    När du har importerat en version blir den nya versionen den aktiva versionen.

### <a name="import-errors"></a>Importfel

* Tokenizer-fel: Om du får ett **tokenizerfel** vid import försöker du importera en version som använder en annan [tokenizer](luis-language-support.md#custom-tokenizer-versions) än den app som för närvarande använder. Åtgärda detta finns [i Migrera mellan tokenizerversioner](luis-language-support.md#migrating-between-tokenizer-versions).

<a name = "export-version"></a>

## <a name="other-actions"></a>Andra åtgärder

* Om du vill **ta bort** en version markerar du en version i listan och väljer sedan **Ta bort** i verktygsfältet. Välj **Ok**. 
* Om du vill **byta namn på** en version markerar du en version i listan och väljer sedan **Byt namn** i verktygsfältet. Ange nytt namn och välj **Klar**. 
* Om du vill **exportera** en version väljer du en version i listan och väljer sedan **Exportera app** i verktygsfältet. Välj JSON att exportera för säkerhetskopiering, välj **Exportera för behållare** för att använda den här appen i en [LUIS-behållare](luis-container-howto.md).  

