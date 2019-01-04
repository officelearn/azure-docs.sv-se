---
title: Översikt över Web Language Model API
titleSuffix: Azure Cognitive Services
description: Web Language Model API i Microsoft Cognitive Services innehåller de senaste verktygen för bearbetning av naturligt språk.
services: cognitive-services
author: piyushbehre
manager: cgronlun
ms.service: cognitive-services
ms.component: web-language-model
ms.topic: overview
ms.date: 08/12/2016
ms.author: pibehre
ROBOTS: NOINDEX
ms.openlocfilehash: 3c7f9e4cc4cf023aa93bd80ad1165ba9595e9034
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/28/2018
ms.locfileid: "53807450"
---
# <a name="what-is-the-web-language-model-api-preview"></a>Vad är Web Language Model API? (Förhandsversion)

> [!IMPORTANT]
> Förhandsversionen av Web Language Model togs ur bruk den 9 augusti 2018. Vi rekommenderar att du använder [Azure Machine Learning-textanalysmoduler](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics) för textbearbetning och -analys.

Web Language Model API i Microsoft Cognitive Services är en REST-baserad molntjänst med de senaste verktygen för bearbetning av naturligt språk. Med detta API kan ditt program använda sig av big data i form av språkmodeller som tränats upp med korpus i webbskala som Bing samlat in på den amerikanska marknaden.

Dessa n-gram-språkmodeller har stöd för Markov-kedjor upp till ordning 5 och är tränade på följande korpus:

- Brödtext på webbsidor
- Rubriker på webbsidor
- Fästpunktstext på webbsidor
- Frågetext i webbsökningar

Web Language Model API har stöd för fyra sökåtgärder:

1. Gemensam (log10) sannolikhet för en sekvens med ord.
2. Villkorad (log10) sannolikhet för ett ord givet en sekvens med föregående ord.
3. Lista med ord (ifyllningar) som troligast kommer att följa en given ordsekvens.
4. Ordindelning av strängar som inte innehåller några blanksteg.

## <a name="getting-started"></a>Komma igång

1. Prenumerera på tjänsten.
2. Ladda ned [SDK:t](https://www.github.com/microsoft/cognitive-weblm-windows).
3. Kör SDK-exempelkoden.
4. I [API-referensen](http://web.archive.org/web/20170503191852/westus.dev.cognitive.microsoft.com/docs/services/55de9ca4e597ed1fd4e2f104/operations/55de9ca4e597ed19b0de8a51) finns all information om slutpunkterna, inklusive kodfragment på olika språk.

## <a name="underlying-technology"></a>Underliggande teknik

I följande dokument beskrivs utvecklingen av de här språkmodellerna. Det ska anges som referens i forskningsartiklar där den här tjänsten används:

- [En översikt över Microsoft Web N-gram Kristi och program](https://research.microsoft.com/apps/pubs/default.aspx?id=130762), NAACL-HLT 2010

Klicka [här](https://academic.microsoft.com/#/search?iq=And%28Ty%3D'0'%2CRId%3D2145833060%29&q=papers%20citing%20an%20overview%20of%20microsoft%20web%20n%20gram%20corpus%20and%20applications&filters=&from=0&sort=0) om du vill se en aktuell lista med forskningsartiklar där arbetet används som referens.
