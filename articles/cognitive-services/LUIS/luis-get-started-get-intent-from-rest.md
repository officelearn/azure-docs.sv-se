---
title: 'Snabb start: skapa avsikt med REST-API: er – LUIS'
description: I den här REST API snabb starten använder du en tillgänglig offentlig LUIS-app för att fastställa en användares avsikt från konversations text.
ms.topic: quickstart
ms.date: 02/03/2020
zone_pivot_groups: programming-languages-set-one
ms.openlocfilehash: 50d2161dc0fa738a70476fa3a9327d9d720e7c15
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2020
ms.locfileid: "76987816"
---
# <a name="quickstart-get-intent-with-rest-apis"></a>Snabb start: skapa avsikt med REST API: er

I den här snabbstarten använder du en tillgänglig offentlig LUIS-app för att fastställa användarens avsikt i konversationstext. Skicka användarens avsikt som text till den offentliga appens HTTP-slutpunkt för förutsägelser. Vid slutpunkten tillämpar LUIS den offentliga appens modell för att analysera betydelsen av text med naturligt språk, och fastställa den övergripande avsikten och extrahera data som är relevanta för appens ämnesdomän.

I den här snabbstarten används slutpunktens REST-API. Mer information finns i [dokumentationen om slutpunkts-API:er](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78).

I den här artikeln behöver du ett kostnadsfritt [LUIS](https://www.luis.ai)-konto.

<a name="create-luis-subscription-key"></a>

::: zone pivot="programming-language-csharp"

[!INCLUDE [Get intent with C# and REST](./includes/get-started-get-intent-rest-csharp.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [Get intent with Java and REST](./includes/get-started-get-intent-rest-java.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [Get intent with Python and REST](./includes/get-started-get-intent-rest-python.md)]

::: zone-end

::: zone pivot="programming-language-nodejs"

[!INCLUDE [Get intent with Node.js and REST](./includes/get-started-get-intent-rest-nodejs.md)]

::: zone-end

::: zone pivot="programming-language-go"

[!INCLUDE [Get intent with Go and REST](./includes/get-started-get-intent-rest-go.md)]

::: zone-end
