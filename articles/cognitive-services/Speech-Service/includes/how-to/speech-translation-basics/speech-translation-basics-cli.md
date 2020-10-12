---
author: v-demjoh
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: v-demjoh
ms.openlocfilehash: f28dd3e94db9d16645bf0d63841a17ee66defd7a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91776670"
---
En av de viktigaste funktionerna i tal tjänsten är möjligheten att identifiera mänskligt tal och översätta den till andra språk. I den här snabb starten lär du dig hur du använder tal-SDK i dina appar och produkter för att utföra högkvalitativ tal översättning. Den här snabb starten översätter tal från mikrofonen till text på ett annat språk.

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du har ett Azure-konto och en röst tjänst prenumeration. Om du inte har ett konto och en prenumeration kan du [prova att använda tal tjänsten kostnads fritt](../../../get-started.md).

[!INCLUDE [SPX Setup](../../spx-setup.md)]

## <a name="set-source-and-target-language"></a>Ange käll-och mål språk

Det här kommandot anropar tal-CLI för att översätta tal från mikrofonen från italienska till franska.

```shell
 spx translate --microphone --source it-IT --target fr
```
