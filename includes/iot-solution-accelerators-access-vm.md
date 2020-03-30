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
ms.openlocfilehash: a58e408feadd10e6dbc9d6878b82a4d045918ea6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68781499"
---
## <a name="access-the-virtual-machine"></a>Få tillgång till den virtuella datorn

Följande steg använder Azure CLI i Azure Cloud Shell. Om du vill kan du [installera Azure CLI](/cli/azure/install-azure-cli) på din utvecklingsdator och köra kommandona lokalt.

Följande steg visar hur du konfigurerar den virtuella Azure-datorn så att **SSH-åtkomst tillåts.** Stegen som visas förutsätter att namnet du valde för lösningsacceleratorn är **contoso-simulering** – ersätta det här värdet med namnet på distributionen:

1. Lista innehållet i resursgruppen som innehåller resurser för lösningsaccelerator:

    ```azurecli-interactive
    az resource list -g contoso-simulation -o table
    ```

    Anteckna namnet på den virtuella datorn, den offentliga IP-adressen och nätverkssäkerhetsgruppen - du behöver dessa värden senare.

1. Uppdatera nätverkssäkerhetsgruppen så att SSH-åtkomst kan tillåtas. Följande kommando förutsätter att namnet på **nätverkssäkerhetsgruppen är contoso-simulation-nsg** – ersätta det här värdet med namnet på nätverkssäkerhetsgruppen:

    ```azurecli-interactive
    az network nsg rule update --name SSH --nsg-name contoso-simulation-nsg -g contoso-simulation --access Allow -o table
    ```

    Aktivera endast SSH-åtkomst under test och utveckling. Om du aktiverar SSH [bör du inaktivera det igen så snart som möjligt](https://docs.microsoft.com/azure/security/fundamentals/network-best-practices#disable-rdpssh-access-to-virtual-machines).

1. Uppdatera lösenordet för **azureuser-kontot** på den virtuella datorn till ett lösenord som du känner till. Välj ditt eget lösenord när du kör följande kommando:

    ```azurecli-interactive
    az vm user update --name vm-vikxv --username azureuser --password YOURSECRETPASSWORD  -g contoso-simulation
    ```

1. Hitta den offentliga IP-adressen för din virtuella dator. Följande kommando förutsätter att namnet på den virtuella datorn är **vm-vikxv** - ersätta det här värdet med namnet på den virtuella datorn som du har antecknat tidigare:

    ```azurecli-interactive
    az vm list-ip-addresses --name vm-vikxv -g contoso-simulation -o table
    ```

    Anteckna den offentliga IP-adressen för din virtuella dator.
