---
title: 'Snabb start: skapa avsikt med REST-API: er – LUIS'
description: I den här REST API snabb starten använder du en tillgänglig offentlig LUIS-app för att fastställa en användares avsikt från konversations text.
ms.topic: quickstart
ms.date: 05/18/2020
ms.custom: tracking-python
zone_pivot_groups: programming-languages-set-one
ms.openlocfilehash: 5d43a1aee9e3f355a3cfcab927d87571798677e7
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/09/2020
ms.locfileid: "84608561"
---
# <a name="quickstart-get-intent-with-rest-apis"></a>Snabb start: skapa avsikt med REST API: er

I den här snabb starten ska du använda en LUIS-app för att fastställa en användares avsikt från konversations text. Skicka användarens avsikt som text till pizza-appens HTTP förutsägelse-slutpunkt. Vid slut punkten tillämpar LUIS pizza-appens modell för att analysera den naturliga språk texten, vilket avgör övergripande avsikt och extrahering av data som är relevanta för appens ämnes domän.

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
