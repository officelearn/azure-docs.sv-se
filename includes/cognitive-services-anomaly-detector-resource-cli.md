---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 06/20/2019
ms.openlocfilehash: 1ea0b01997d3d5cecff73f951c51de5898c2f07a
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503471"
---
Följande Azure CLI-kommandon kommer att etablera en Avvikelseidentifiering detektor resurs på den kostnadsfria prisnivån. Klicka på den **prova** knappen, klistra in koden och tryck RETUR för att köra den i Azure cloudshell. Dina nycklar för att autentisera ditt program skrivs. När den är klar [skapar en miljövariabel](../articles/cognitive-services/cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) för din någon av dina nycklar, med namnet `ANOMALY_DETECTOR_KEY`.

> [!NOTE]
> * Alternativt kan du:
>    * Skapa en resurs med hjälp av den [Azure-portalen](../articles/cognitive-services/cognitive-services-apis-create-account.md), eller [Azure CLI](../articles/cognitive-services/cognitive-services-apis-create-account.md) på den lokala datorn.
>    * Hämta en [utvärderingsversion nyckeln](https://azure.microsoft.com/try/cognitive-services/#decision) giltig för 7 dagar utan kostnad. När du registrerar dig det blir tillgängligt på den [Azure-webbplatsen](https://azure.microsoft.com/try/cognitive-services/my-apis/).
>    * Visa den här resursen på den [Azure-portalen](https://portal.azure.com/). 

Cognitive Services representeras av Azure-resurser som du etablerar. Varje Cognitive Services-konto (och dess associerade Azure-resurs) måste tillhöra en Azure-resursgrupp.

1. Skapa en Azure-resursgrupp

    ```azurecli-interactive
    az group create \
        --name example-anomaly-detector-resource-group \
        --location westus2
    ```

2. Skapa en resurs för Avvikelseidentifiering detektor på den kostnadsfria nivån

    ```azurecli-interactive
    az cognitiveservices account create \
        --name example-anomaly-detector-resource \
        --resource-group example-anomaly-detector-resource-group \
        --kind AnomalyDetector \
        --sku F0 \
        --location westus2 \
        --yes
    ```

3. Lista nycklarna för din resurs

    ```azurecli-interactive
    az cognitiveservices account keys list \
        --name example-anomaly-detector-resource \
        --resource-group example-anomaly-detector-resource-group
    ```