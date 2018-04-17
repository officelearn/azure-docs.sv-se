---
title: ta med fil
description: ta med fil
services: virtual-network
author: genli
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: 8476a7dadeaff64e703186396b4505cc307e4d3a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
## <a name="how-to-create-a-classic-vnet-using-azure-cli"></a>Så här skapar du ett klassiska VNet med Azure CLI
Du kan använda Azure CLI för att hantera dina Azure-resurser från kommandotolken från valfri dator som kör Windows, Linux eller OSX.

1. Om du aldrig har använt Azure CLI, se [installera och konfigurera Azure CLI](../articles/cli-install-nodejs.md) och följ instruktionerna upp till den punkt där du väljer Azure-konto och prenumeration.
2. Om du vill skapa ett VNet och ett undernät, kör den **azure network vnet skapa** kommando:
   
            azure network vnet create --vnet TestVNet -e 192.168.0.0 -i 16 -n FrontEnd -p 192.168.1.0 -r 24 -l "Central US"
   
    Förväntad utdata:
   
            info:    Executing command network vnet create
            + Looking up network configuration
            + Looking up locations
            + Setting network configuration
            info:    network vnet create command OK
   
   * **--vnet**. Namnet på den VNet som ska skapas. I scenariot *TestVNet*
   * **-e (eller---adressutrymmet)**. VNet-adressutrymmet. I scenariot *192.168.0.0*
   * **-i (eller - cidr)**. Nätverksmasken i CIDR-format. I scenariot *16*.
   * **-n (eller--undernätsnamn**). Namnet på det första undernätet. I scenariot *klientdel*.
   * **-p (eller--undernät-start-ip)**. Första IP-adressen för undernätet eller undernätsadressutrymmet. I scenariot *192.168.1.0*.
   * **-r (eller--undernät cidr)**. Nätverksmasken i CIDR-format för undernätet. I scenariot *24*.
   * **-l (eller --location)**. Azure-region där VNet skapas. I scenariot *centrala USA*.
3. Om du vill skapa ett undernät, kör den **azure network vnet subnet skapa** kommando:
   
            azure network vnet subnet create -t TestVNet -n BackEnd -a 192.168.2.0/24
   
    Förväntad utdata för föregående kommando:
   
            info:    Executing command network vnet subnet create
            + Looking up network configuration
            + Creating subnet "BackEnd"
            + Setting network configuration
            + Looking up the subnet "BackEnd"
            + Looking up network configuration
            data:    Name                            : BackEnd
            data:    Address prefix                  : 192.168.2.0/24
            info:    network vnet subnet create command OK
   
   * **-t (eller--vnet-name**. Namnet på VNet där undernätet kommer att skapas. I scenariot *TestVNet*.
   * **-n (eller --name)**. Namnet på det nya undernätet. I scenariot *BackEnd*.
   * **-a (eller --address-prefix)**. CIDR-block för undernätet. I scenariot *192.168.2.0/24*.
4. Om du vill visa egenskaperna för det nya vnet, kör den **azure network vnet show** kommando:
   
            azure network vnet show
   
    Utdata som förväntas för det föregående kommandot:
   
            info:    Executing command network vnet show
            Virtual network name: TestVNet
            + Looking up the virtual network sites
            data:    Name                            : TestVNet
            data:    Location                        : Central US
            data:    State                           : Created
            data:    Address space                   : 192.168.0.0/16
            data:    Subnets:
            data:      Name                          : FrontEnd
            data:      Address prefix                : 192.168.1.0/24
            data:
            data:      Name                          : BackEnd
            data:      Address prefix                : 192.168.2.0/24
            data:
            info:    network vnet show command OK

