---
title: Hantera versioner – LUIS
titleSuffix: Azure Cognitive Services
description: Versioner kan du skapa och publicera olika modeller. Ett bra tips är att klona den aktuella aktiva modellen till en annan version av appen innan du gör ändringar i modellen.
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
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74221897"
---
# <a name="use-versions-to-edit-and-test-without-impacting-staging-or-production-apps"></a>Använda versioner för att redigera och testa utan att påverka mellanlagrings-eller produktions program

Versioner kan du skapa och publicera olika modeller. En bra idé är att klona den aktuella aktiva modellen till en annan [version](luis-concept-version.md) av appen innan du gör ändringar i modellen. 

Om du vill arbeta med versioner öppnar du appen genom att välja namnet på sidan **Mina appar** och väljer sedan **Hantera** i det översta fältet och sedan **versioner** i det vänstra navigerings fältet. 

I listan över versioner visas vilka versioner som publiceras, var de publiceras och vilken version som för närvarande är aktiv. 

> [!div class="mx-imgBorder"]
> [![Avsnittet hantera, sidan versioner](./media/luis-how-to-manage-versions/versions-import.png "Hantera avsnittet versioner sida")](./media/luis-how-to-manage-versions/versions-import.png#lightbox)

## <a name="clone-a-version"></a>Klona en version

1. Välj den version som du vill klona och välj sedan **klona** i verktygsfältet. 

2. I dialog rutan **klona version** anger du ett namn för den nya versionen, till exempel "0,2".

   ![Dialogrutan för kloning Version](./media/luis-how-to-manage-versions/version-clone-version-dialog.png)
 
     > [!NOTE]
     > Version ID bestå av tecken, siffror eller '.' och får inte vara längre än 10 tecken.
 
   En ny version med det angivna namnet skapas och Ställ in som den aktiva versionen.

## <a name="set-active-version"></a>Ange active version

Välj en version i listan och välj sedan **Aktivera** från verktygsfältet. 

> [!div class="mx-imgBorder"]
> [![Hantera avsnitt, versioner, sidan, skapa en versions åtgärd](./media/luis-how-to-manage-versions/versions-other.png "Hantera avsnitt, versioner, sidan, skapa en versions åtgärd")](./media/luis-how-to-manage-versions/versions-other.png#lightbox)

## <a name="import-version"></a>Import-version

Du kan importera en `.json` eller en `.lu` version av programmet.

1. Välj **Importera** från verktygsfältet och välj sedan formatet. 

2. Ange det nya 10-siffriga versions namnet i popup-fönstret **Importera ny version** . Du behöver bara ange ett versions-ID om versionen i filen redan finns i appen.

    ![Hantera avsnitt, versioner, sidan Importera ny version](./media/luis-how-to-manage-versions/versions-import-pop-up.png)

    När du importerar en version, blir den aktiva versionen i den nya versionen.

### <a name="import-errors"></a>Import fel

* Tokenizer-fel: om du får ett **Tokenizer-fel** när du importerar försöker du importera en version som använder en annan [Tokenizer](luis-language-support.md#custom-tokenizer-versions) än appen som för närvarande använder. Information om hur du åtgärdar detta finns i [Migrera mellan tokenizer-versioner](luis-language-support.md#migrating-between-tokenizer-versions).

<a name = "export-version"></a>

## <a name="other-actions"></a>Andra åtgärder

* Om du vill **ta bort** en version väljer du en version i listan och väljer sedan **ta bort** från verktygsfältet. Välj **OK**. 
* Om du vill **byta namn på** en version väljer du en version i listan och väljer sedan **Byt namn** från verktygsfältet. Ange ett nytt namn och välj sedan **OK**. 
* Om du vill **Exportera** en version väljer du en version i listan och väljer sedan **Exportera app** i verktygsfältet. Välj JSON för att exportera för säkerhets kopiering, Välj **Exportera för container** om du vill [använda den här appen i en Luis-behållare](luis-container-howto.md).  

