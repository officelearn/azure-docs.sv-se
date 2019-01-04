---
title: ta med fil
description: ta med fil
services: iot-accelerators
author: dominicbetts
ms.service: iot-accelerators
ms.topic: include
ms.date: 08/16/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: db1af4f046bd8849fddee299e949d6edbdaae86a
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/19/2018
ms.locfileid: "53609097"
---
## <a name="access-the-virtual-machine"></a>Få åtkomst till den virtuella datorn

I följande anvisningar används den `az` i Azure Cloud Shell. Om du föredrar, kan du [installera Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) på utvecklingen av din dator och kör kommandon lokalt.

Följande steg visar hur du konfigurerar Azure-dator så att **SSH** åtkomst. Anvisningarna förutsätter att det namn du valde för solution accelerator är **contoso-simulering** --Ersätt detta värde med namnet på din distribution:

1. Visa innehållet i resursgruppen som innehåller solution accelerator resurser:

    ```azurecli-interactive
    az resource list -g contoso-simulation -o table
    ```

    Anteckna namnet på den virtuella datorn, offentliga IP-adressen och nätverkssäkerhetsgruppen – du behöver dessa värden senare.

1. Uppdatera nätverkssäkerhetsgruppen för att tillåta SSH-åtkomst. Följande kommando förutsätter att namnet på den nya nätverkssäkerhetsgruppen är **contoso-simulering-nsg** --Ersätt detta värde med namnet på din grupp:

    ```azurecli-interactive
    az network nsg rule update --name SSH --nsg-name contoso-simulation-nsg -g contoso-simulation --access Allow -o table
    ```

    Aktivera endast SSH-åtkomst under testning och utveckling. Om du aktiverar SSH, [bör du inaktivera det igen så snart som möjligt](https://docs.microsoft.com/azure/security/azure-security-network-security-best-practices#disable-rdpssh-access-to-virtual-machines).

1. Uppdatera lösenordet för den **azureuser** konto på den virtuella datorn till ett lösenord som du vet. Välj ditt eget lösenord när du kör följande kommando:

    ```azurecli-interactive
    az vm user update --name vm-vikxv --username azureuser --password YOURSECRETPASSWORD  -g contoso-simulation
    ```

1. Hitta den offentliga IP-adressen för den virtuella datorn. Följande kommando förutsätter att namnet på den virtuella datorn är **vm-vikxv** --Ersätt detta värde med namnet på den virtuella datorn som du antecknade tidigare:

    ```azurecli-interactive
    az vm list-ip-addresses --name vm-vikxv -g contoso-simulation -o table
    ```

    Anteckna den offentliga IP-adressen för den virtuella datorn.
