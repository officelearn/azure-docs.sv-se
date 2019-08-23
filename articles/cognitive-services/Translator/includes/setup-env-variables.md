---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 156486f4f4f0df3d4bb4ab76492709bbecfb8eb5
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2019
ms.locfileid: "69906620"
---
## <a name="set-up"></a>Konfigurera

### <a name="create-a-translator-text-resource"></a>Skapa en Translator Text resurs

Azure-Cognitive Services representeras av Azure-resurser som du prenumererar på. Skapa en resurs för Translator Text med hjälp av [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) eller [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) på den lokala datorn. Du kan också:

* Få en [utvärderings nyckel](https://azure.microsoft.com/try/cognitive-services) som är giltig i 7 dagar utan kostnad. När du har registrerat dig är den tillgänglig på Azure-webbplatsen.
* Visa en befintlig resurs i [Azure Portal](https://portal.azure.com/).

När du har skaffat en nyckel från din utvärderings prenumeration eller resurs [](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)skapar du två miljövariabler:

* `TRANSLATOR_TEXT_SUBSCRIPTION_KEY`– Prenumerations nyckeln för din Translator Text-resurs.
* `TRANSLATOR_TEXT_ENDPOINT`– Den regionala slut punkten eller det anpassade under domän namnet för din resurs.
