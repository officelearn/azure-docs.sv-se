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
ms.openlocfilehash: 8d1f0f6ae3ffc123f1ae8318a6fbce4a9a3024fa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66814869"
---
## <a name="access-the-virtual-machine"></a>Få åtkomst till den virtuella datorn

Följande steg kan du använda Azure CLI i Azure Cloud Shell. Om du föredrar, kan du [installera Azure CLI](/cli/azure/install-azure-cli) på utvecklingen av din dator och kör kommandon lokalt.

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
