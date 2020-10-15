---
title: Snabb start – skapa och hantera åtkomsttoken
titleSuffix: An Azure Communication Services quickstart
description: Lär dig hur du hanterar identiteter och åtkomsttoken med hjälp av Azure Communication Services administrations klient bibliotek.
author: tomaschladek
manager: jken
services: azure-communication-services
ms.author: tchladek
ms.date: 08/20/2020
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: e323f1f50fe6c67a841c300fcbec1eed3afc4497
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2020
ms.locfileid: "92074132"
---
# <a name="quickstart-create-and-manage-access-tokens"></a>Snabb start: skapa och hantera åtkomsttoken

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Kom igång med Azure Communication Services med hjälp av klient biblioteket kommunikations tjänster för administration för att etablera och hantera dina åtkomsttoken. Med åtkomst-token kan chatten och anropa klient biblioteken autentisera direkt mot Azure Communication Services. Dessa tokens genereras på en server sidans etablerings tjänst för token som du implementerar. De används sedan för att initiera klient biblioteken för kommunikations tjänster på klient enheter.

Observera att alla priser som visas i bilder i den här självstudien endast är till exempel syfte.

::: zone pivot="programming-language-csharp"
[!INCLUDE [.NET](./includes/user-access-token-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/user-access-token-js.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/user-access-token-python.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/user-access-token-java.md)]
::: zone-end

Utdata från appen beskriver varje åtgärd som slutförs:
<!---cSpell:disable --->
```console
Azure Communication Services - Access Tokens Quickstart

Issued a access token with 'voip' scope for identity with ID: 8:acs:fecfaddf-bf2c-4a0e-b52f-7d918c9536e6_65012b-1400da9050:
<token signature here>

Issued a access token with 'chat' scope for identity with ID: 8:acs:fecfaddf-bf2c-4a0e-b52f-7d918c9536e6_65012b-1400da9050:
<token signature here>

Successfully deleted the identity with ID: 8:acs:fecfaddf-bf2c-4a0e-b52f-7d918c9536e6_65012b-1400da9050

Deleted the identity with ID: 8:acs:fecfaddf-bf2c-4a0e-b52f-7d918c9536e6_65012b-1400da9050
```
<!---cSpell:enable --->

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en kommunikations tjänst prenumeration kan du ta bort resursen eller resurs gruppen. Om du tar bort resurs gruppen raderas även andra resurser som är kopplade till den. Läs mer om att [Rensa resurser](./create-communication-resource.md#clean-up-resources).


## <a name="next-steps"></a>Efterföljande moment

I den här snabbstarten har du lärt dig att:

> [!div class="checklist"]
> * Hantera identiteter
> * Utfärda åtkomsttoken
> * Använda klient biblioteket för administration av kommunikations tjänster


> [!div class="nextstepaction"]
> [Lägga till röstsamtal i din app](./voice-video-calling/getting-started-with-calling.md)

Du kanske också vill:

 - [Läs mer om autentisering](../concepts/authentication.md)
 - [Lägga till chatt i din app](./chat/get-started.md)
 - [Lär dig mer om klient-och server arkitektur](../concepts/client-and-server-architecture.md)
