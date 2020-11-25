---
author: PatrickFarley
ms.service: cognitive-services
ms.topic: include
ms.date: 02/07/2019
ms.author: pafarley
ms.openlocfilehash: 874d76bebdfd3bd0daba1f83cb1f06c093f192ec
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021291"
---
## <a name="get-the-training-and-prediction-keys"></a>Hämta utbildnings- och förutsägelsenycklarna

Projektet kräver en giltig uppsättning prenumerations nycklar för att samverka med tjänsten. Du kan hitta objekten på  [Custom vision webbplats](https://customvision.ai). Logga in med det konto som är associerat med det Azure-konto som du använde för att skapa dina Custom Vision-resurser. På Start sidan (sidan med alternativet för att lägga till ett nytt projekt) väljer du __kugg hjuls ikonen__ i det övre högra hörnet. Hitta dina utbildnings-och förutsägelse resurser i listan och expandera dem. Här hittar du utbildnings nyckel, förutsägelse nyckel och förutsägelse resurs-ID-värden. Spara värdena på en tillfällig plats.

> [!NOTE]
> Om du använder en Cognitive Services allt-i-ett-nyckel för att komma åt Custom Vision visas bara en nyckel på skärmen Inställningar. I det här fallet använder du samma nyckel för både utbildning och förutsägelse-åtgärder.

![Bild på gränssnittet för nycklarna](../media/csharp-tutorial/training-prediction-keys.png)

Du kan också hämta dessa nycklar och ID från [Azure Portal](https://www.portal.azure.com) genom att titta på Custom vision utbildning och förutsägelse resurser och navigera till fliken __nycklar__ . Där hittar du utbildnings nyckeln och den förutsägelse nyckeln. Gå till fliken __Egenskaper__ för förutsägelse resursen för att hämta ditt förutsägelse resurs-ID.

