---
title: Skapa en säkerhetsgrupp för nätverk (klassiska) med hjälp av Azure CLI 1.0 | Microsoft Docs
description: Lär dig hur du skapar och distribuerar en nätverkssäkerhetsgrupp (klassisk) som använder Azure CLI 1.0.
services: virtual-network
documentationcenter: na
author: genli
manager: cshepard
editor: ''
tags: azure-service-management
ms.assetid: 17d98950-5fbb-4653-bef6-d822ab37541e
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: genli
ms.openlocfilehash: a9ab1a83366919a2d05da18819ed8167bdadb20a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="create-a-network-security-group-classic-using-the-azure-cli-10"></a>Skapa en säkerhetsgrupp för nätverk (klassiskt) med hjälp av Azure CLI 1.0
[!INCLUDE [virtual-networks-create-nsg-selectors-classic-include](../../includes/virtual-networks-create-nsg-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Den här artikeln beskriver hur du gör om du använder den klassiska distributionsmodellen. Du kan också [skapa NSG: er i Resource Manager-distributionsmodellen](tutorial-filter-network-traffic-cli.md).

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

Följande exempelkommandon Azure CLI förväntar sig en enkel miljö som redan har skapats baserat på scenariot. Om du vill köra kommandon som de visas i det här dokumentet först skapa testmiljö av [skapa ett virtuellt nätverk](virtual-networks-create-vnet-classic-cli.md).

## <a name="create-an-nsg-for-the-front-end-subnet"></a>Skapa en NSG för undernätet frontend

1. Om du aldrig har använt Azure CLI, se [installera och konfigurera Azure CLI](../cli-install-nodejs.md).
2. Växla till klassiskt läge:

    ```azurecli
    azure config mode asm
    ```   

3. Skapa en NSG::
   
    ```azurecli   
     azure network nsg create -l uswest -n NSG-FrontEnd
    ```
   
4. Skapa en anslutningssäkerhetsregel som ger åtkomst till port 3389 (RDP) från internet:
   
    ```azurecli
    azure network nsg rule create -a NSG-FrontEnd -n rdp-rule -c Allow -p Tcp -r Inbound -y 100 -f Internet -o * -e * -u 3389
   ```

5. Skapa en regel som tillåter åtkomst till port 80 (HTTP) från internet:
   
    ```azurecli
    azure network nsg rule create -a NSG-FrontEnd -n web-rule -c Allow -p Tcp -r Inbound -y 200 -f Internet -o * -e * -u 80
    ```   

6. Koppla NSG frontend-undernät:
   
    ```azurecli
    azure network nsg subnet add -a NSG-FrontEnd --vnet-name TestVNet --subnet-name FrontEnd
   ```

## <a name="create-the-nsg-for-the-back-end-subnet"></a>Skapa NSG för backend-undernät

1. Skapa NSG: N:
   
    ```azurecli
    azure network nsg create -l uswest -n NSG-BackEnd
   ```

2. Skapa en regel som tillåter åtkomst till port 1433 (SQL) i frontend undernätet:
   
    ```azurecli
    azure network nsg rule create -a NSG-BackEnd -n sql-rule -c Allow -p Tcp -r Inbound -y 100 -f 192.168.1.0/24 -o * -e * -u 1433
   ```

3. Skapa en regel som nekar åtkomst till internet:
   
    ```azurecli
    azure network nsg rule create -a NSG-BackEnd -n web-rule -c Deny -p Tcp -r Outbound -y 200 -f * -o * -e Internet -u 80
   ```

4. Koppla NSG till backend-undernät:
   
    ```azurecli
    azure network nsg subnet add -a NSG-BackEnd --vnet-name TestVNet --subnet-name BackEnd
    ```