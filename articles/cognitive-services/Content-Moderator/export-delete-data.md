---
title: Exportera eller ta bort användardata - Innehållsmoderator
titleSuffix: Azure Cognitive Services
description: Du har full kontroll över dina data. Läs om hur du visar, exporterar eller tar bort dina data i Innehållsmoderator.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: pafarley
ms.openlocfilehash: 81713bf8d424b9f272f6b1bccf3657810160d4cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "73744787"
---
# <a name="export-or-delete-user-data-in-content-moderator"></a>Exportera eller ta bort användardata i Innehållsmoderator

Innehållsmoderator samlar in användardata för att driva tjänsten, men kunderna har full kontroll över att visa, exportera och ta bort sina data med [granskningsverktyget](https://contentmoderator.cognitive.microsoft.com/) och [api:erna för moderering och granskning](https://docs.microsoft.com/azure/cognitive-services/content-moderator/api-reference).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Mer information om hur du exporterar och tar bort användardata i Innehållsmoderator finns i följande tabell.

| Data | Exportera åtgärd | Ta bort åtgärd |
| ---- | ---------------- | ---------------- |
| Kontoinformation (prenumerationsnycklar) | Ej tillämpligt | Ta bort med Azure-portalen (Azure-prenumerationer). Eller använd knappen **Ta bort team på** sidan Inställningar för [granskningsgränssnittsteamet.](https://contentmoderator.cognitive.microsoft.com/) |
| Bilder för anpassad matchning | Anropa [API:et hämta avbildnings-ID](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f676). Bilder lagras i ett enkelriktat eget hash-format, och det finns inget sätt att extrahera de faktiska bilderna. | Anropa [API:et ta bort alla bilder](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f686). Eller ta bort content moderator-resursen med hjälp av Azure-portalen. |
| Villkor för anpassad matchning | Cal [få alla termer API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67e) | Anropa [API:et ta bort alla termer](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67d). Eller ta bort content moderator-resursen med hjälp av Azure-portalen. |
| Taggar | Ej tillämpligt | Använd ikonen **Ta bort** tillgänglig för varje tagg på sidan Inställningar för granskningsgränssnittstagg. Eller använd knappen **Ta bort team på** sidan Inställningar för [granskningsgränssnittsteamet.](https://contentmoderator.cognitive.microsoft.com/) |
| Omdömen | Anropa [API:et för get review](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2) | Använd knappen **Ta bort team på** sidan Inställningar för [granskningsgränssnittsteamet.](https://contentmoderator.cognitive.microsoft.com/)
| Användare | Ej tillämpligt | Använd ikonen **Ta bort** som är tillgänglig för varje användare på sidan Inställningar för [granskningsgränssnittsgrupp.](https://contentmoderator.cognitive.microsoft.com/) Eller använd knappen **Ta bort team på** sidan Inställningar för [granskningsgränssnittsteamet.](https://contentmoderator.cognitive.microsoft.com/) |

