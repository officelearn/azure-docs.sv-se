---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: aa76f7b85302651f6874747610c3355f0572a7ee
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "88556363"
---
<!-- List and set subscriptions -->

1. Hämta en lista över dina prenumerationer med kommandot [az account list](/cli/azure/account#az-account-list):

    ```
    az account list --output table
    ```

2. Använd `az account set` med det prenumerations-ID eller namn som du vill växla till.

    ```
    az account set --subscription "My Demos"
    ```
