---
title: 'Snabb start: skapa och använda inlärnings slinga med SDK – personanpassa'
description: Den här snabb starten visar hur du skapar och hanterar din kunskaps bas med hjälp av klient-SDK.
ms.topic: quickstart
ms.date: 07/30/2020
ms.custom: devx-track-python, devx-track-javascript
zone_pivot_groups: programming-languages-set-six
ms.openlocfilehash: 480e860952c2659d053f8a5659e312feacff2d2b
ms.sourcegitcommit: dea88d5e28bd4bbd55f5303d7d58785fad5a341d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2020
ms.locfileid: "87874494"
---
# <a name="quickstart-personalizer-client-library"></a>Snabb start: klient bibliotek för Personanpassare

Visa personligt innehåll i den här snabb starten med tjänsten personanpassa.

Kom igång med personanpassa klient biblioteket. Följ de här stegen för att installera paketet och prova exempel koden för grundläggande uppgifter.

 * Rang-API – väljer det bästa objektet, från åtgärder, baserat på information i real tid som du anger om innehåll och kontext.
 * Belönings-API – du fastställer belönings poängen utifrån dina affärs behov och skickar det sedan till en Personligre med detta API. Poängen kan vara ett enda värde, till exempel 1 för bra, och 0 för dåligt eller en algoritm som du skapar utifrån dina affärs behov.

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

Om du vill rensa och ta bort en Cognitive Services prenumeration kan du ta bort resursen eller resurs gruppen. Om du tar bort resurs gruppen raderas även andra resurser som är kopplade till den.

* [Portal](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
>[Så här fungerar Personanpassning](how-personalizer-works.md)

* [Vad är Personanpassning?](what-is-personalizer.md)
* [Var kan du använda Personanpassning?](where-can-you-use-personalizer.md)
* [Felsökning](troubleshooting.md)
* Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/python/sample.py).
