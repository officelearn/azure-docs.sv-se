---
title: Hantera Azure Cache för Redis med hjälp av Azure klassiskt CLI | Microsoft Docs
description: Lär dig hur du installerar Azure klassiska CLI på valfri plattform, använda den för att ansluta till ditt Azure-konto och skapa och hantera en Azure Cache för Redis från klassiskt CLI.
services: azure-cache-for-redis
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 964ff245-859d-4bc1-bccf-62e4b3c1169f
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: azure-cache-for-redis
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: wesmc
ms.openlocfilehash: 54aa6f12afd83d4fb180b718ed420f62a8663ff6
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/07/2018
ms.locfileid: "53019875"
---
# <a name="how-to-create-and-manage-azure-cache-for-redis-using-the-azure-classic-cli"></a>Hur du skapar och hanterar Azure Cache för Redis med hjälp av den klassiska Azure CLI
> [!div class="op_single_selector"]
> * [PowerShell](cache-howto-manage-redis-cache-powershell.md)
> * [Klassisk Azure CLI](cache-manage-cli.md)
>

Den klassiska Azure CLI är ett bra sätt att hantera Azure-infrastrukturen från valfri plattform. Den här artikeln visar hur du skapar och hanterar din Azure-Cache för Redis-instanser med den klassiska Azure CLI.

[!INCLUDE [outdated-cli-content](../../includes/contains-classic-cli-content.md)]
> [!NOTE]
> Läs de senaste Azure CLI-exempelskript [Azure CLI Azure Cache för exempel på Redis](cli-samples.md).

## <a name="prerequisites"></a>Förutsättningar
Du måste slutföra följande steg för att skapa och hantera Azure Cache för Redis-instanser med Azure klassiska CLI.

* Du måste ha ett Azure-konto. Om du inte har någon kan du skapa en [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) efter en liten stund.
* [Installera Azure CLI för klassiska](../cli-install-nodejs.md).
* Ansluta Azure CLI-installationen med ett personligt konto i Azure, eller med ett arbets eller skolkonto för Azure och logga in från den klassiska CLI med den `azure login` kommando.
* Innan du kör något av följande kommandon för att växla klassiskt CLI i Resource Manager-läge genom att köra den `azure config mode arm` kommando. Mer information finns i [använda klassiska Azure CLI för att hantera Azure-resurser och resursgrupper](../xplat-cli-azure-resource-manager.md).

## <a name="azure-cache-for-redis-properties"></a>Azure Cache för Redis-egenskaper
Följande egenskaper som används när du skapar och uppdaterar Azure Cache för Redis-instanser.

| Egenskap  | Växel | Beskrivning |
| --- | --- | --- |
| namn |-n, – namn |Namnet på Azure Cache för Redis. |
| Resursgrupp |-g,--resursgrupp |Namnet på resursgruppen. |
| location |l-,--plats |Plats för att skapa cache. |
| storlek |z-,--storlek |Storleken på cacheminnet för Azure Redis. Giltiga värden: [C0, C1, C2, C3, C4, C5, C6, P1, P2, P3, P4] |
| sku |-x-,--sku |Redis-SKU. Bör vara något av: [Basic, Standard, Premium] |
| enableNonSslPort |e-,--aktivera icke-ssl-port |EnableNonSslPort-egenskapen för Azure Cache för Redis. Lägg till den här flaggan om du vill aktivera icke-SSL-porten för din cachelagring |
| Redis-konfiguration |c-,--redis-konfiguration |Redis-konfiguration. Ange en JSON-formaterad sträng med konfigurationsnycklar och värden här. Format ”: {” ”:” ””, ””: ”}” |
| Redis-konfiguration |f-,--redis-konfigurationsfil |Redis-konfiguration. Ange sökvägen till en fil som innehåller konfigurationsnycklar och värden här. Formatet för posten fil: {””: ”” ”,” ”:”} |
| Shardantal |r-,--shardantal |Antal fragment för att skapa på Premium-kluster Cache med klustring. |
| Virtual Network |v-,--virtuellt nätverk |När du är värd för ditt cacheminne i ett virtuellt nätverk, anger exakta ARM resurs-ID för det virtuella nätverket för att distribuera Azure Cache för Redis-i. Exempel på format: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| Nyckeltyp |-t,--key-typ |Typen av nyckel att förnya. Giltiga värden: [primär, sekundär] |
| StaticIP |-p, – statisk ip < statisk ip > |När du har ditt cacheminne i ett virtuellt nätverk, anger du en unik IP-adress i undernätet för cachen. Om du inte är något valt från undernätet. |
| Undernät |t,--undernät <subnet> |När du har ditt cacheminne i ett virtuellt nätverk, anger du namnet på undernätet där du kan distribuera cacheminnet. |
| VirtualNetwork |v-,--virtuellt nätverk < virtuellt nätverk > |När du är värd för ditt cacheminne i ett virtuellt nätverk, anger exakta ARM resurs-ID för det virtuella nätverket för att distribuera Azure Cache för Redis-i. Exempel på format: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| Prenumeration |-s,--prenumeration |Prenumerations-ID. |

## <a name="see-all-azure-cache-for-redis-commands"></a>Se alla Azure-Cache för Redis-kommandon
Om du vill se alla Azure-Cache för Redis-kommandon och deras parametrar, använda den `azure rediscache -h` kommando.

    C:\>azure rediscache -h
    help:    Commands to manage your Azure Cache for Redis(s)
    help:
    help:    Create an Azure Cache for Redis
    help:      rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
    help:
    help:    Delete an existing Azure Cache for Redis
    help:      rediscache delete [--name <name> --resource-group <resource-group> ]
    help:
    help:    List all Azure Cache for Rediss within your Subscription or Resource Group
    help:      rediscache list [options]
    help:
    help:    Show properties of an existing Azure Cache for Redis
    help:      rediscache show [--name <name> --resource-group <resource-group>]
    help:
    help:    Change settings of an existing Azure Cache for Redis
    help:      rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
    help:
    help:    Renew the authentication key for an existing Azure Cache for Redis
    help:      rediscache renew-key [--name <name> --resource-group <resource-group> ]
    help:
    help:    Lists Primary and Secondary key of an existing Azure Cache for Redis
    help:      rediscache list-keys [--name <name> --resource-group <resource-group>]
    help:
    help:    Options:
    help:      -h, --help  output usage information
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="create-an-azure-cache-for-redis"></a>Skapa en Azure Cache för Redis
Om du vill skapa en Azure Cache för Redis, använder du följande kommando:

    azure rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]

Mer information om det här kommandot Kör den `azure rediscache create -h` kommando.

    C:\>azure rediscache create -h
    help:    Create an Azure Cache for Redis
    help:
    help:    Usage: rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
    help:
    help:    Options:
    help:      -h, --help                                               output usage information
    help:      -v, --verbose                                            use verbose output
    help:      -vv                                                      more verbose with debug output
    help:      --json                                                   use json output
    help:      -n, --name <name>                                        Name of the Azure Cache for Redis.
    help:      -g, --resource-group <resource-group>                    Name of the Resource Group
    help:      -l, --location <location>                                Location to create cache.
    help:      -z, --size <size>                                        Size of the Azure Cache for Redis. Valid values: [C0, C1, C2, C3, C4, C5, C6, P1, P2, P3, P4]
    help:      -x, --sku <sku>                                          Redis SKU. Should be one of : [Basic, Standard, Premium]
    help:      -e, --enable-non-ssl-port                                EnableNonSslPort property of the Azure Cache for Redis. Add this flag if you want to enable the Non SSL Port for your cache
    help:      -c, --redis-configuration <redis-configuration>          Redis Configuration. Enter a JSON formatted string of configuration keys and values here. Format:"{"<key1>":"<value1>","<key2>":"<value2>"}"
    help:      -f, --redis-configuration-file <redisConfigurationFile>  Redis Configuration. Enter the path of a file containing configuration keys and values here. Format for the file entry: {"<key1>":"<value1>","<key2>":"<value2>"}
    help:      -r, --shard-count <shard-count>                          Number of Shards to create on a Premium Cluster Cache
    help:      -v, --virtual-network <virtual-network>                  The exact ARM resource ID of the virtual network to deploy the Azure Cache for Redis in. Example format: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1
    help:      -t, --subnet <subnet>                                    Required when deploying an Azure Cache for Redis inside an existing Azure Virtual Network
    help:      -p, --static-ip <static-ip>                              Required when deploying an Azure Cache for Redis inside an existing Azure Virtual Network
    help:      -s, --subscription <id>                                  the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="delete-an-existing-azure-cache-for-redis"></a>Ta bort en befintlig Azure-Cache för Redis
Om du vill ta bort en Azure Cache för Redis, använder du följande kommando:

    azure rediscache delete [--name <name> --resource-group <resource-group> ]

Mer information om det här kommandot Kör den `azure rediscache delete -h` kommando.

    C:\>azure rediscache delete -h
    help:    Delete an existing Azure Cache for Redis
    help:
    help:    Usage: rediscache delete [--name <name> --resource-group <resource-group> ]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Azure Cache for Redis.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group under which the cache exists
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="list-all-azure-cache-for-rediss-within-your-subscription-or-resource-group"></a>Lista alla Azure-Cache för Rediss inom din prenumeration eller resursgrupp
Om du vill visa alla Azure-Cache för Rediss inom din prenumeration eller resursgrupp, använder du följande kommando:

    azure rediscache list [options]

Mer information om det här kommandot Kör den `azure rediscache list -h` kommando.

    C:\>azure rediscache list -h
    help:    List all Azure Cache for Rediss within your Subscription or Resource Group
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

## <a name="show-properties-of-an-existing-azure-cache-for-redis"></a>Visa egenskaperna för en befintlig Azure-Cache för Redis
För att visa egenskaperna för en befintlig Azure-Cache för Redis, använder du följande kommando:

    azure rediscache show [--name <name> --resource-group <resource-group>]

Mer information om det här kommandot Kör den `azure rediscache show -h` kommando.

    C:\>azure rediscache show -h
    help:    Show properties of an existing Azure Cache for Redis
    help:
    help:    Usage: rediscache show [--name <name> --resource-group <resource-group>]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Azure Cache for Redis.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

<a name="scale"></a>

## <a name="change-settings-of-an-existing-azure-cache-for-redis"></a>Ändra inställningar för en befintlig Azure-Cache för Redis
Om du vill ändra inställningarna för en befintlig Azure-Cache för Redis, använder du följande kommando:

    azure rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]

Mer information om det här kommandot Kör den `azure rediscache set -h` kommando.

    C:\>azure rediscache set -h
    help:    Change settings of an existing Azure Cache for Redis
    help:
    help:    Usage: rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
    help:
    help:    Options:
    help:      -h, --help                                               output usage information
    help:      -v, --verbose                                            use verbose output
    help:      -vv                                                      more verbose with debug output
    help:      --json                                                   use json output
    help:      -n, --name <name>                                        Name of the Azure Cache for Redis.
    help:      -g, --resource-group <resource-group>                    Name of the Resource Group
    help:      -c, --redis-configuration <redis-configuration>          Redis Configuration. Enter a JSON formatted string of configuration keys and values here.
    help:      -f, --redis-configuration-file <redisConfigurationFile>  Redis Configuration. Enter the path of a file containing configuration keys and values here.
    help:      -s, --subscription <subscription>                        the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="renew-the-authentication-key-for-an-existing-azure-cache-for-redis"></a>Förnya autentiseringsnyckeln för en befintlig Azure-Cache för Redis
Om du vill förnya autentiseringsnyckeln för en befintlig Azure-Cache för Redis, använder du följande kommando:

    azure rediscache renew-key [--name <name> --resource-group <resource-group> --key-type <key-type>]

Ange `Primary` eller `Secondary` för `key-type`.

Mer information om det här kommandot Kör den `azure rediscache renew-key -h` kommando.

    C:\>azure rediscache renew-key -h
    help:    Renew the authentication key for an existing Azure Cache for Redis
    help:
    help:    Usage: rediscache renew-key [--name <name> --resource-group <resource-group> ]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Azure Cache for Redis.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group under which cache exists
    help:      -t, --key-type <key-type>              type of key to renew. Valid values are: 'Primary', 'Secondary'.
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="list-primary-and-secondary-keys-of-an-existing-azure-cache-for-redis"></a>Lista över primära och sekundära nycklarna för en befintlig Azure-Cache för Redis
Att lista primära och sekundära nycklarna för en befintlig Azure-Cache för Redis, använder du följande kommando:

    azure rediscache list-keys [--name <name> --resource-group <resource-group>]

Mer information om det här kommandot Kör den `azure rediscache list-keys -h` kommando.

    C:\>azure rediscache list-keys -h
    help:    Lists Primary and Secondary key of an existing Azure Cache for Redis
    help:
    help:    Usage: rediscache list-keys [--name <name> --resource-group <resource-group>]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Azure Cache for Redis.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group under which Cache exists
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)
