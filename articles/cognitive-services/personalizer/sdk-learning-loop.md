---
title: 'Snabbstart: Skapa och använd inlärningsslinga med SDK - Personalizer'
description: Den här snabbstarten visar hur du skapar och hanterar din kunskapsbas med hjälp av klienten SDK.
ms.topic: quickstart
ms.date: 01/15/2020
zone_pivot_groups: programming-languages-set-six
ms.openlocfilehash: 7ebe22227b4323b2e6b1c3fc9ca31e171d1d97cd
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77524877"
---
# <a name="quickstart-personalizer-client-library"></a>Snabbstart: Personalizer-klientbibliotek

Visa personligt innehåll i den här snabbstarten med Personalizer-tjänsten.

Kom igång med Personalizer-klientbiblioteket. Följ dessa steg för att installera paketet och prova exempelkoden för grundläggande uppgifter.

 * Rank API - Väljer det bästa objektet, från innehållsobjekt, baserat på realtidsinformation som du anger om innehåll och kontext.
 * Belönings-API - Du bestämmer belöningspoängen baserat på dina affärsbehov och skickar den sedan till Personalizer med det här API:et. Den poängen kan vara ett enda värde som 1 för gott och 0 för dåliga, eller en algoritm som du skapar baserat på dina affärsbehov.

::: zone pivot="programming-language-csharp"
[!INCLUDE [Get intent with C# SDK](./includes/quickstart-sdk-csharp.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [Get intent with Node.js SDK](./includes/quickstart-sdk-nodejs.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Get intent with Python SDK](./includes/quickstart-sdk-python.md)]
::: zone-end

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services-prenumeration kan du ta bort resursen eller resursgruppen. Om du tar bort resursgruppen tas även alla andra resurser som är associerade bort.

* [Portal](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
>[Så här fungerar Personanpassning](how-personalizer-works.md)

* [Vad är Personanpassning?](what-is-personalizer.md)
* [Var kan du använda Personanpassning?](where-can-you-use-personalizer.md)
* [Troubleshooting](troubleshooting.md) (Felsökning)
* Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/python/sample.py).
