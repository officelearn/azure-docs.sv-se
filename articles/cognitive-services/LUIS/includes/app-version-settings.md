---
title: ta med fil
ms.topic: include
ms.custom: include file
ms.date: 5/04/2020
ms.openlocfilehash: 819dfa3127eb91a2f08687a76ea0586439b650a4
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83591002"
---
|Nivå|Användar gränssnitts inställning|API-inställning|Information|
|--|--|--|--|
|App|Gör slut punkter offentliga|`Public`|Alla kan komma åt din offentliga app om de har en förutsägelse nyckel och känner till ditt app-ID. |
|Version|Använd icke-deterministisk utbildning|`UseAllTrainingData`|Träning använder en liten procent andel av negativ sampling. Om du vill använda alla data i stället för den små negativa insamlingen anger du till `true` . |
|Version|Normalisera dia kritiska tecken|`NormalizeDiacritics`|Normaliserar dia kritiska tecken ersätter tecknen med dia kritiska tecken i yttranden med vanliga tecken.|
|Version|Normalisera interpunktion|`NormalizePunctuation`|Normalisera interpunktion innebär att innan dina modeller blir utbildade och innan dina slut punkts frågor hämtas, tas skiljetecken bort från yttranden.|
|Version|Normalisera ord former|`NormalizeWordForm`|Ignorera ord former bortom roten.|

Lär dig [begrepp](../luis-concept-utterance.md#utterance-normalization-for-diacritics-and-punctuation) för normalisering och hur du använder [app](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58aeface39e2bb03dcd5909e) -och [versions](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) -API: er för att uppdatera dessa inställningar eller Använd sidan för Luis-portalens **hanterings** avsnitt, **program inställningar** .