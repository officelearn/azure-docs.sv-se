---
title: Exportera eller ta bort dina data i Content kontrollant - kognitiva Azure-tjänster | Microsoft Docs
description: Lär dig hur du exporterar eller ta bort dina data i Content kontrollanten.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 05/25/2018
ms.author: v-jaswel
ms.openlocfilehash: 1dbb645a033c6db5ffa9003a53f30fd927131298
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35355374"
---
# <a name="export-or-delete-user-data-in-content-moderator"></a>Exportera eller ta bort användardata innehåll kontrollant

Innehåll kontrollant samlar in användardata för att använda tjänsten, men kunder har fullständig kontroll över visa, exportera och ta bort sina data med hjälp av den [granska UI](http://contentmoderator.cognitive.microsoft.com/) och [API: er](https://docs.microsoft.com/en-us/azure/cognitive-services/content-moderator/api-reference).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Mer information om hur du exporterar och ta bort användardata innehåll kontrollant finns i följande tabell.

| Data | Exportåtgärden | Borttagningsåtgärd |
| ---- | ---------------- | ---------------- |
| Kontoinformation (prenumeration nycklar) | Gäller inte | Ta bort med hjälp av Azure portal (Azure-prenumerationer). Eller Använd den **ta bort Team** knappen i den [granska UI](http://contentmoderator.cognitive.microsoft.com/) Team inställningssidan. |
| Avbildningar för anpassade matchar | [Hämta bild ID](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f676). Avbildningar lagras i en envägs hash för egna format och det går inte att extrahera själva bilderna. | [Ta bort alla avbildningar](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f686). Eller ta bort innehåll kontrollant resursen med hjälp av Azure portal. |
| Villkor för egna matchning | [Hämta alla villkor](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67e) | [Ta bort alla villkor](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67d). Eller ta bort innehåll kontrollant resursen med hjälp av Azure portal. |
| Taggar | Gäller inte | Använd den **ta bort** ikonen som är tillgängliga för varje tagg sidan Granska UI-tagg. Eller Använd den **ta bort Team** knappen i den [granska UI](http://contentmoderator.cognitive.microsoft.com/) Team inställningssidan. |
| Omdömen | [Hämta granskning](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2) | Använd den **ta bort Team** knappen i den [granska UI](http://contentmoderator.cognitive.microsoft.com/) Team inställningssidan.
| Användare | Gäller inte | Använd den **ta bort** ikonen som är tillgängliga för varje användare i den [granska UI](http://contentmoderator.cognitive.microsoft.com/) Team inställningssidan. Eller Använd den **ta bort Team** knappen i den [granska UI](http://contentmoderator.cognitive.microsoft.com/) Team inställningssidan. |

