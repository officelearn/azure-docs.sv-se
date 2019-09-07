---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: c737447c3a3bd2d76d3ed620b7c61aaa81250130
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/06/2019
ms.locfileid: "70393820"
---
## <a name="set-up"></a>Konfigurera

### <a name="create-a-translator-text-resource"></a>Skapa en Translator Text resurs

Azure-Cognitive Services representeras av Azure-resurser som du prenumererar på. Skapa en resurs för Translator Text med hjälp av [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) eller [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) på den lokala datorn. Du kan också:

* Få en [utvärderings nyckel](https://azure.microsoft.com/try/cognitive-services) som är giltig i 7 dagar utan kostnad. När du har registrerat dig är den tillgänglig på Azure-webbplatsen.
* Visa en befintlig resurs i [Azure Portal](https://portal.azure.com/).

När du har skaffat en nyckel från din utvärderings prenumeration eller resurs skapar du två [miljövariabler](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication):

* `TRANSLATOR_TEXT_SUBSCRIPTION_KEY`– Prenumerations nyckeln för din Translator Text-resurs.
* `TRANSLATOR_TEXT_ENDPOINT`– Den globala slut punkten för Translator Text. Använda `https://api.cognitive.microsofttranslator.com/`.
