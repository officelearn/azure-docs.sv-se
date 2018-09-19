---
title: Ändra storlek på och Beskär miniatyrer för Bing - bildsökning i Bing
description: Lär dig hur du ändrar storlek på och Beskär miniatyrer som ingår i ett svar från den bildsökning i Bing.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.assetid: F4FFAE91-A003-4F7C-8E60-83A142485E28
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: conceptual
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: de82cc5554af91294dda3826dfb394cc94dbf3d0
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2018
ms.locfileid: "46296235"
---
# <a name="resizing-and-cropping-thumbnail-images"></a>Ändra storlek på och beskära miniatyrbilder

Vid bearbetning av en sökfråga Bing genererar miniatyr information för alla avbildningar i dess [svar](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-get-images#bing-image-search-response-format). Den här informationen kan användas för att visa alla eller en delmängd av returnerade miniatyrbilderna. Om du visar en delmängd, anger du ett alternativ för att visa återstående bilder.


<!-- Removing image until we can replace it with a sanatized version.
![Expanded view of thumbnail image](../bing-web-search/media/cognitive-services-bing-web-api/bing-web-image-thumbnail-expansion.PNG)
-->

Om användaren klickar på miniatyrbilden kan du använda [contentUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-contenturl) för att visa bilden i fullstorlek för användaren. Se till att tillskriva bildkällan.

Om `shoppingSourcesCount` eller `recipeSourcesCount` är större än noll bör du lägga till ikoner som visar att det finns shopping eller recept för objektet i bilden.

<!-- this is a sanitized version but we're removing all images for now until everything is sanitized.
![Shopping sources badge](./media/cognitive-services-bing-images-api/bing-images-shopping-source.PNG)
-->

För att få insikter om bilden, till exempel webbplatser som inkluderar bilden eller personer som kändes igen i bilden, använder du [imageInsightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-imageinsightstoken). Information finns i [Image Insights](image-insights.md) (Bildinsikter).

## <a name="resizing-and-cropping-thumbnails"></a>Ändra storlek på och beskära miniatyrer

Du kan också ändra storlek på och expandera miniatyrer, till exempel när en användares markören håller muspekaren över den.
> [!NOTE]
> Se till att tillskriva bildkällan om du expanderar bilden. Till exempel kan du extrahera värden från [hostPageDisplayUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-hostpagedisplayurl) och visa den nedanför bilden.

[!INCLUDE [cognitive-services-bing-resize-crop-thumbnails](../../../includes/cognitive-services-bing-resize-crop-thumbnails.md)]
