---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: c737447c3a3bd2d76d3ed620b7c61aaa81250130
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "70393820"
---
## <a name="set-up"></a>Konfigurera

### <a name="create-a-translator-text-resource"></a>Skapa en översättningstextresurs

Azure Cognitive Services representeras av Azure-resurser som du prenumererar på. Skapa en resurs för översättartext med [Azure-portalen](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) eller [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) på din lokala dator. Du kan också:

* Få en [testversion nyckel](https://azure.microsoft.com/try/cognitive-services) giltig för 7 dag gratis. När du har registrerat dig kommer den att vara tillgänglig på Azure-webbplatsen.
* Visa en befintlig resurs i [Azure-portalen](https://portal.azure.com/).

När du har fått en nyckel från din utvärderingsprenumeration eller -resurs skapar du två [miljövariabler:](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)

* `TRANSLATOR_TEXT_SUBSCRIPTION_KEY`- Prenumerationsnyckeln för din Translator Text-resurs.
* `TRANSLATOR_TEXT_ENDPOINT`- Den globala slutpunkten för översättartext. Använd `https://api.cognitive.microsofttranslator.com/`.
