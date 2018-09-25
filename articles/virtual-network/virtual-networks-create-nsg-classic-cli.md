---
title: Skapa en nätverkssäkerhetsgrupp (klassisk) med hjälp av den klassiska Azure CLI | Microsoft Docs
description: Lär dig hur du skapar och distribuerar en nätverkssäkerhetsgrupp (klassisk) med hjälp av den klassiska Azure CLI.
services: virtual-network
documentationcenter: na
author: genlin
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
ms.openlocfilehash: 5e81fa709589e5fa4a109cdf7709b87bc446ac3c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46947242"
---
# <a name="create-a-network-security-group-classic-using-the-azure-classic-cli"></a>Skapa en nätverkssäkerhetsgrupp (klassisk) med den klassiska Azure CLI
[!INCLUDE [virtual-networks-create-nsg-selectors-classic-include](../../includes/virtual-networks-create-nsg-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Den här artikeln beskriver hur du gör om du använder den klassiska distributionsmodellen. Du kan också [skapa NSG: er i Resource Manager-distributionsmodellen](tutorial-filter-network-traffic-cli.md).

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

Följande exempelkommandon för Azure CLI förväntar sig en enkel miljö som redan har skapats baserat på scenariot. Om du vill köra kommandona i det här dokumentet visas först skapa testmiljö av [skapar ett virtuellt nätverk](virtual-networks-create-vnet-classic-cli.md).

## <a name="create-an-nsg-for-the-front-end-subnet"></a>Skapa en NSG för undernätet frontend

1. Om du aldrig har använt Azure CLI, se [installera och konfigurera Azure CLI](/cli/azure/install-cli-version-1.0).
2. Växla till klassiskt läge:

    ```azurecli
    azure config mode asm
    ```   

3. Skapa en NSG::
   
    ```azurecli   
     azure network nsg create -l uswest -n NSG-FrontEnd
    ```
   
4. Skapa en säkerhetsregel som tillåter åtkomst till port 3389 (RDP) från internet:
   
    ```azurecli
    azure network nsg rule create -a NSG-FrontEnd -n rdp-rule -c Allow -p Tcp -r Inbound -y 100 -f Internet -o * -e * -u 3389
   ```

5. Skapa en regel som tillåter åtkomst till port 80 (HTTP) från internet:
   
    ```azurecli
    azure network nsg rule create -a NSG-FrontEnd -n web-rule -c Allow -p Tcp -r Inbound -y 200 -f Internet -o * -e * -u 80
    ```   

6. Koppla NSG: N till klientdelsundernätet:
   
    ```azurecli
    azure network nsg subnet add -a NSG-FrontEnd --vnet-name TestVNet --subnet-name FrontEnd
   ```

## <a name="create-the-nsg-for-the-back-end-subnet"></a>Skapa NSG för backend-undernät

1. Skapa NSG:
   
    ```azurecli
    azure network nsg create -l uswest -n NSG-BackEnd
   ```

2. Skapa en regel som tillåter åtkomst till port 1433 (SQL) från klientdelens undernät:
   
    ```azurecli
    azure network nsg rule create -a NSG-BackEnd -n sql-rule -c Allow -p Tcp -r Inbound -y 100 -f 192.168.1.0/24 -o * -e * -u 1433
   ```

3. Skapa en regel som nekar åtkomst till internet:
   
    ```azurecli
    azure network nsg rule create -a NSG-BackEnd -n web-rule -c Deny -p Tcp -r Outbound -y 200 -f * -o * -e Internet -u 80
   ```

4. Koppla NSG: N till backend-undernät:
   
    ```azurecli
    azure network nsg subnet add -a NSG-BackEnd --vnet-name TestVNet --subnet-name BackEnd
    ```