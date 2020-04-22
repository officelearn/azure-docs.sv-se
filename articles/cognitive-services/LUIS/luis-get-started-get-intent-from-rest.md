---
title: 'Snabbstart: Få avsikt med REST API: er - LUIS'
description: I den här REST API-snabbstarten använder du en tillgänglig offentlig LUIS-app för att avgöra en användares avsikt från konversationstext.
ms.topic: quickstart
ms.date: 04/20/2020
zone_pivot_groups: programming-languages-set-one
ms.openlocfilehash: 03f1c1539233872d9b98fab317c9caf997d5df69
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733314"
---
# <a name="quickstart-get-intent-with-rest-apis"></a>Snabbstart: Få avsikt med REST API:er

I den här snabbstarten använder du en tillgänglig offentlig LUIS-app för att fastställa användarens avsikt i konversationstext. Skicka användarens avsikt som text till den offentliga appens HTTP-slutpunkt för förutsägelser. Vid slutpunkten tillämpar LUIS den offentliga appens modell för att analysera betydelsen av text med naturligt språk, och fastställa den övergripande avsikten och extrahera data som är relevanta för appens ämnesdomän.

I den här snabbstarten används slutpunktens REST-API. Mer information finns i [dokumentationen om slutpunkts-API:er](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78).

I den här artikeln behöver du ett kostnadsfritt [LUIS](https://www.luis.ai)-konto.

<a name="create-luis-subscription-key"></a>

::: zone pivot="programming-language-csharp"
[!INCLUDE [Get intent with C# and REST](./includes/get-started-get-intent-rest-csharp.md)]
::: zone-end

::: zone pivot="programming-language-go"
[!INCLUDE [Get intent with Go and REST](./includes/get-started-get-intent-rest-go.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Get intent with Java and REST](./includes/get-started-get-intent-rest-java.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [Get intent with Node.js and REST](./includes/get-started-get-intent-rest-nodejs.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Get intent with Python and REST](./includes/get-started-get-intent-rest-python.md)]
::: zone-end

