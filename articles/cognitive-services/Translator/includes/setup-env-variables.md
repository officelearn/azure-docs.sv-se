---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 54ee19f3e278b424384ff55d7065713584235df1
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2020
ms.locfileid: "86144236"
---
## <a name="set-up"></a>Konfigurera

### <a name="create-a-translator-resource"></a>Skapa en översättare-resurs

Azure-Cognitive Services representeras av Azure-resurser som du prenumererar på. Skapa en resurs för Translator med hjälp av [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) eller [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) på den lokala datorn. Du kan också:

* Visa en befintlig resurs i [Azure Portal](https://portal.azure.com/).

När du har skaffat en nyckel från din utvärderings prenumeration eller resurs skapar du två [miljövariabler](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication):

* `TRANSLATOR_TEXT_SUBSCRIPTION_KEY`– Prenumerations nyckeln för din Translator-resurs.
* `TRANSLATOR_TEXT_ENDPOINT`– Den globala slut punkten för Translator. Använd `https://api.cognitive.microsofttranslator.com/`.
