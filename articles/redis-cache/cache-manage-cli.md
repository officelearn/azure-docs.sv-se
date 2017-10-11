---
title: "Hantera Azure Redis-Cache med hjälp av Azure CLI | Microsoft Docs"
description: "Lär dig hur du installerar Azure CLI på valfri plattform, hur du ansluter till ditt Azure-konto och skapa och hantera ett Redis-cache från Azure CLI."
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: 964ff245-859d-4bc1-bccf-62e4b3c1169f
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: sdanie
ms.openlocfilehash: ba078a870a3998568170cc197bd6698b97b7fadb
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-create-and-manage-azure-redis-cache-using-the-azure-command-line-interface-azure-cli"></a>Skapa och hantera Azure Redis-Cache med hjälp av Azure-kommandoradsgränssnittet (Azure CLI)
> [!div class="op_single_selector"]
> * [PowerShell](cache-howto-manage-redis-cache-powershell.md)
> * [Azure CLI](cache-manage-cli.md)
>
>

Azure CLI är ett bra sätt att hantera dina Azure-infrastrukturen från valfri plattform. Den här artikeln visar hur du skapar och hanterar din Azure Redis-Cache-instanser som använder Azure CLI.

> [!NOTE]
> Den här artikeln gäller för en tidigare version av Azure CLI. De senaste Azure CLI 2.0 exempelskript finns [Azure Redis-CLI cache exempel](cli-samples.md).
> 
> 

## <a name="prerequisites"></a>Krav
Om du vill skapa och hantera Azure Redis-Cache-instanser som använder Azure CLI, måste du slutföra följande steg.

* Du måste ha ett Azure-konto. Om du inte har någon, kan du skapa en [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) i en liten stund.
* [Installera Azure CLI](../cli-install-nodejs.md).
* Anslut Azure CLI-installationen med ett personligt konto i Azure, eller med ett arbets eller skolan Azure-konto och logga in från Azure CLI med hjälp av den `azure login` kommando. Om du vill förstå skillnaderna och väljer, se [Anslut till en Azure-prenumeration från Azure-kommandoradsgränssnittet (Azure CLI)](../xplat-cli-connect.md).
* Innan du kan köra följande kommandon för att växla Azure CLI i Resource Manager-läge genom att köra den `azure config mode arm` kommando. Mer information finns i [använda Azure CLI för att hantera Azure-resurser och resursgrupper](../xplat-cli-azure-resource-manager.md).

## <a name="redis-cache-properties"></a>Redis-Cache-egenskaper
Följande egenskaper används när du skapar och uppdaterar Redis-Cache-instanser.

| Egenskap | Växel | Beskrivning |
| --- | --- | --- |
| namn |-n,--namn |Namnet på Redis-Cache. |
| Resursgrupp |-g,--resursgruppen. |Namnet på resursgruppen. |
| location |-l,--plats |Plats för att skapa cache. |
| Storlek |-z,--storlek |Storleken på Redis-Cache. Giltiga värden: [C0, C1, C2, C3, C4, C5, C6, P1, P2, P3, P4] |
| SKU |-x,--sku |Redis-SKU. Ska vara någon av: [Basic, Standard, Premium] |
| enableNonSslPort |e-,--aktivera icke-ssl-port |EnableNonSslPort-egenskapen för Redis-Cache. Lägg till den här flaggan om du vill aktivera icke-SSL-Port för ditt cacheminne |
| Redis-konfiguration |-c,--redis-konfiguration |Redis-konfiguration. Ange en JSON-formaterad sträng konfigurationsnycklar och värden här. Format ”: {” ”:” ””, ””: ”}” |
| Redis-konfiguration |-f,--redis-konfigurationsfilen |Redis-konfiguration. Ange en sökväg till en fil som innehåller konfigurationsnycklar och värden här. Formatet för posten fil: {””: ”” ”,” ”:”} |
| Fragmentera antal |-r,--Fragmentera antal |Antalet delar för att skapa på Premium klustret Cache med kluster. |
| Virtual Network |-v,--virtuellt nätverk |När värd för ditt cacheminne i ett virtuellt nätverk, anger du exakt ARM resurs-ID för det virtuella nätverket för att distribuera redis-cache i. Exempel på format: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| Nyckeltyp |-t,--typ av nyckel |Typ av nyckel att förnya. Giltiga värden: [primära, sekundära] |
| StaticIP |-p,--statiska ip-< statiska IP-> |När värd för ditt cacheminne i ett virtuellt nätverk, anger du en unik IP-adress i undernätet för cachen. Om inte är en valt i undernätet. |
| Undernät |t,--undernät<subnet> |När värd för ditt cacheminne i ett virtuellt nätverk, anger du namnet på undernätet där du vill distribuera cachen. |
| VirtualNetwork |-v,--virtuellt nätverk < virtuella nätverk > |När värd för ditt cacheminne i ett virtuellt nätverk, anger du exakt ARM resurs-ID för det virtuella nätverket för att distribuera redis-cache i. Exempel på format: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| Prenumeration |-s, -prenumeration |Prenumerations-ID. |

## <a name="see-all-redis-cache-commands"></a>Visa alla kommandon som Redis-Cache
Om du vill se alla Redis-Cache-kommandon och deras parametrar i `azure rediscache -h` kommando.

    C:\>azure rediscache -h
    help:    Commands to manage your Azure Redis Cache(s)
    help:
    help:    Create a Redis Cache
    help:      rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
    help:
    help:    Delete an existing Redis Cache
    help:      rediscache delete [--name <name> --resource-group <resource-group> ]
    help:
    help:    List all Redis Caches within your Subscription or Resource Group
    help:      rediscache list [options]
    help:
    help:    Show properties of an existing Redis Cache
    help:      rediscache show [--name <name> --resource-group <resource-group>]
    help:
    help:    Change settings of an existing Redis Cache
    help:      rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
    help:
    help:    Renew the authentication key for an existing Redis Cache
    help:      rediscache renew-key [--name <name> --resource-group <resource-group> ]
    help:
    help:    Lists Primary and Secondary key of an existing Redis Cache
    help:      rediscache list-keys [--name <name> --resource-group <resource-group>]
    help:
    help:    Options:
    help:      -h, --help  output usage information
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="create-a-redis-cache"></a>Skapa en Redis-cache
Om du vill skapa ett Redis-Cache, använder du följande kommando:

    azure rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]

Mer information om det här kommandot Kör den `azure rediscache create -h` kommando.

    C:\>azure rediscache create -h
    help:    Create a Redis Cache
    help:
    help:    Usage: rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
    help:
    help:    Options:
    help:      -h, --help                                               output usage information
    help:      -v, --verbose                                            use verbose output
    help:      -vv                                                      more verbose with debug output
    help:      --json                                                   use json output
    help:      -n, --name <name>                                        Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>                    Name of the Resource Group
    help:      -l, --location <location>                                Location to create cache.
    help:      -z, --size <size>                                        Size of the Redis Cache. Valid values: [C0, C1, C2, C3, C4, C5, C6, P1, P2, P3, P4]
    help:      -x, --sku <sku>                                          Redis SKU. Should be one of : [Basic, Standard, Premium]
    help:      -e, --enable-non-ssl-port                                EnableNonSslPort property of the Redis Cache. Add this flag if you want to enable the Non SSL Port for your cache
    help:      -c, --redis-configuration <redis-configuration>          Redis Configuration. Enter a JSON formatted string of configuration keys and values here. Format:"{"<key1>":"<value1>","<key2>":"<value2>"}"
    help:      -f, --redis-configuration-file <redisConfigurationFile>  Redis Configuration. Enter the path of a file containing configuration keys and values here. Format for the file entry: {"<key1>":"<value1>","<key2>":"<value2>"}
    help:      -r, --shard-count <shard-count>                          Number of Shards to create on a Premium Cluster Cache
    help:      -v, --virtual-network <virtual-network>                  The exact ARM resource ID of the virtual network to deploy the redis cache in. Example format: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1
    help:      -t, --subnet <subnet>                                    Required when deploying a redis cache inside an existing Azure Virtual Network
    help:      -p, --static-ip <static-ip>                              Required when deploying a redis cache inside an existing Azure Virtual Network
    help:      -s, --subscription <id>                                  the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="delete-an-existing-redis-cache"></a>Ta bort en befintlig Redis-Cache
Om du vill ta bort ett Redis-Cache, använder du följande kommando:

    azure rediscache delete [--name <name> --resource-group <resource-group> ]

Mer information om det här kommandot Kör den `azure rediscache delete -h` kommando.

    C:\>azure rediscache delete -h
    help:    Delete an existing Redis Cache
    help:
    help:    Usage: rediscache delete [--name <name> --resource-group <resource-group> ]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group under which the cache exists
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="list-all-redis-caches-within-your-subscription-or-resource-group"></a>Visa en lista med alla Redis-cache i din prenumeration eller resursgrupp
Om du vill visa en lista med alla Redis-cache i din prenumeration eller resursgrupp, använder du följande kommando:

    azure rediscache list [options]

Mer information om det här kommandot Kör den `azure rediscache list -h` kommando.

    C:\>azure rediscache list -h
    help:    List all Redis Caches within your Subscription or Resource Group
    help:
    help:    Usage: rediscache list [options]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -g, --resource-group <resource-group>  Name of the Resource Group
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="show-properties-of-an-existing-redis-cache"></a>Visa egenskaperna för en befintlig Redis-Cache
Om du vill visa egenskaperna för en befintlig Redis-Cache, använder du följande kommando:

    azure rediscache show [--name <name> --resource-group <resource-group>]

Mer information om det här kommandot Kör den `azure rediscache show -h` kommando.

    C:\>azure rediscache show -h
    help:    Show properties of an existing Redis Cache
    help:
    help:    Usage: rediscache show [--name <name> --resource-group <resource-group>]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

<a name="scale"></a>

## <a name="change-settings-of-an-existing-redis-cache"></a>Ändra inställningar för en befintlig Redis-Cache
Om du vill ändra inställningarna för en befintlig Redis-Cache, använder du följande kommando:

    azure rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]

Mer information om det här kommandot Kör den `azure rediscache set -h` kommando.

    C:\>azure rediscache set -h
    help:    Change settings of an existing Redis Cache
    help:
    help:    Usage: rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
    help:
    help:    Options:
    help:      -h, --help                                               output usage information
    help:      -v, --verbose                                            use verbose output
    help:      -vv                                                      more verbose with debug output
    help:      --json                                                   use json output
    help:      -n, --name <name>                                        Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>                    Name of the Resource Group
    help:      -c, --redis-configuration <redis-configuration>          Redis Configuration. Enter a JSON formatted string of configuration keys and values here.
    help:      -f, --redis-configuration-file <redisConfigurationFile>  Redis Configuration. Enter the path of a file containing configuration keys and values here.
    help:      -s, --subscription <subscription>                        the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="renew-the-authentication-key-for-an-existing-redis-cache"></a>Förnya autentiseringsnyckel för en befintlig Redis-Cache
Om du vill förnya autentiseringsnyckeln för en befintlig Redis-Cache, använder du följande kommando:

    azure rediscache renew-key [--name <name> --resource-group <resource-group> --key-type <key-type>]

Ange `Primary` eller `Secondary` för `key-type`.

Mer information om det här kommandot Kör den `azure rediscache renew-key -h` kommando.

    C:\>azure rediscache renew-key -h
    help:    Renew the authentication key for an existing Redis Cache
    help:
    help:    Usage: rediscache renew-key [--name <name> --resource-group <resource-group> ]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group under which cache exists
    help:      -t, --key-type <key-type>              type of key to renew. Valid values are: 'Primary', 'Secondary'.
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="list-primary-and-secondary-keys-of-an-existing-redis-cache"></a>Lista över primära och sekundära nycklarna i en befintlig Redis-Cache
Lista över primära och sekundära nycklarna för en befintlig Redis-Cache, använder du följande kommando:

    azure rediscache list-keys [--name <name> --resource-group <resource-group>]

Mer information om det här kommandot Kör den `azure rediscache list-keys -h` kommando.

    C:\>azure rediscache list-keys -h
    help:    Lists Primary and Secondary key of an existing Redis Cache
    help:
    help:    Usage: rediscache list-keys [--name <name> --resource-group <resource-group>]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group under which Cache exists
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)
