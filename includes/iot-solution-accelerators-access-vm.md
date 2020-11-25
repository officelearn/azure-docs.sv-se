---
title: ta med fil
description: ta med fil
services: iot-accelerators
author: dominicbetts
ms.service: iot-accelerators
ms.topic: include
ms.date: 08/16/2018
ms.author: dobett
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: e3eed0e7378f513e45e0eb9ac719a92e87ec1d53
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96027174"
---
## <a name="access-the-virtual-machine"></a>Åtkomst till den virtuella datorn

I följande steg används Azure CLI i Azure Cloud Shell. Om du vill kan du [Installera Azure CLI](/cli/azure/install-azure-cli) på utvecklings datorn och köra kommandona lokalt.

Följande steg visar hur du konfigurerar den virtuella Azure-datorn så att **SSH** -åtkomst tillåts. Stegen som visas förutsätter att det namn du valde för Solution Accelerator är **contoso-simulering** --Ersätt det här värdet med namnet på din distribution:

1. Visa en lista med innehållet i resurs gruppen som innehåller lösnings acceleratorns resurser:

    ```azurecli-interactive
    az resource list -g contoso-simulation -o table
    ```

    Anteckna namnet på den virtuella datorn, den offentliga IP-adressen och nätverks säkerhets gruppen – du behöver dessa värden senare.

1. Uppdatera nätverks säkerhets gruppen för att tillåta SSH-åtkomst. Följande kommando förutsätter att namnet på nätverks säkerhets gruppen är **contoso-simulering-NSG** --Ersätt det här värdet med namnet på din nätverks säkerhets grupp:

    ```azurecli-interactive
    az network nsg rule update --name SSH --nsg-name contoso-simulation-nsg -g contoso-simulation --access Allow -o table
    ```

    Aktivera endast SSH-åtkomst under testning och utveckling. Om du aktiverar SSH [bör du inaktivera det igen så snart som möjligt](../articles/security/fundamentals/network-best-practices.md#disable-rdpssh-access-to-virtual-machines).

1. Uppdatera lösen ordet för **azureuser** -kontot på den virtuella datorn till ett lösen ord som du känner till. Välj ditt eget lösen ord när du kör följande kommando:

    ```azurecli-interactive
    az vm user update --name vm-vikxv --username azureuser --password YOURSECRETPASSWORD  -g contoso-simulation
    ```

1. Hitta den offentliga IP-adressen för den virtuella datorn. Följande kommando förutsätter att namnet på den virtuella datorn är **VM-vikxv** – Ersätt det här värdet med namnet på den virtuella datorn som du antecknade tidigare:

    ```azurecli-interactive
    az vm list-ip-addresses --name vm-vikxv -g contoso-simulation -o table
    ```

    Anteckna den offentliga IP-adressen för den virtuella datorn.