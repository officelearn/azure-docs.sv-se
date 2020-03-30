---
title: Hantera Azure Cache för Redis med Azure classic CLI
description: Lär dig hur du installerar Azure classic CLI på valfri plattform, hur du använder den för att ansluta till ditt Azure-konto och hur du skapar och hanterar en Azure-cache för Redis från den klassiska CLI.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 01/23/2017
ms.author: yegu
ms.openlocfilehash: e2b1ed693ea57e3414d465a57a5ba2b1203f67c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277991"
---
# <a name="how-to-create-and-manage-azure-cache-for-redis-using-the-azure-classic-cli"></a>Så här skapar och hanterar du Azure Cache för Redis med hjälp av Azure classic CLI
> [!div class="op_single_selector"]
> * [Powershell](cache-how-to-manage-redis-cache-powershell.md)
> * [Klassisk Azure CLI](cache-manage-cli.md)
>

Azure classic CLI är ett bra sätt att hantera din Azure-infrastruktur från alla plattformar. Den här artikeln visar hur du skapar och hanterar dina Azure-cache för Redis-instanser med azure-klassikern CLI.

[!INCLUDE [outdated-cli-content](../../includes/contains-classic-cli-content.md)]
> [!NOTE]
> De senaste Azure CLI-exempelskripten finns i [Azure CLI Azure Cache for Redis-exempel](cli-samples.md).

## <a name="prerequisites"></a>Krav
Om du vill skapa och hantera Azure Cache för Redis-instanser med Azure Classic CLI måste du utföra följande steg.

* Du måste ha ett Azure-konto. Om du inte har ett kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) på bara några ögonblick.
* [Installera den klassiska AZURE CLI.](../cli-install-nodejs.md)
* Anslut din Azure CLI-installation med ett personligt Azure-konto, eller med ett Azure-konto för arbete eller skola, och logga in från den klassiska CLI med `azure login` kommandot.
* Innan du kör något av följande kommandon växlar du den `azure config mode arm` klassiska CLI-klien till Resource Manager-läge genom att köra kommandot. Mer information finns i [Använda azure-klassikern CLI för att hantera Azure-resurser och resursgrupper](../xplat-cli-azure-resource-manager.md).

## <a name="azure-cache-for-redis-properties"></a>Egenskaper för Azure Cache for Redis
Följande egenskaper används när du skapar och uppdaterar Azure Cache för Redis-instanser.

| Egenskap | Växel | Beskrivning |
| --- | --- | --- |
| namn |-n, --namn |Namn på Azure Cache för Redis. |
| Resursgrupp |-g, --resurs-grupp |Namn på resursgruppen. |
| location |-L, --plats |Plats för att skapa cache. |
| size |-z, --storlek |Storleken på Azure-cache för Redis. Giltiga värden: [C0, C1, C2, C3, C4, C5, C6, P1, P2, P3, P4] |
| sku |-x, --sku |Redis SKU. Bör vara en av: [Basic, Standard, Premium] |
| EnableNonSslPort |-e, --enable-non-ssl-port |EnableNonSslPort-egenskapen för Azure Cache för Redis. Lägg till den här flaggan om du vill aktivera icke SSL-porten för cacheminnet |
| Redis-konfiguration |-c, --redis-konfiguration |Redis-konfiguration. Ange en JSON-formaterad sträng med konfigurationsnycklar och värden här. Format:"{"":"","":""}" |
| Redis-konfiguration |-f, --redis-configuration-file |Redis-konfiguration. Ange sökvägen till en fil som innehåller konfigurationsnycklar och värden här. Format för filposten: {"":"","":""} |
| Antal fragment |-r, --shard-count |Antal Shards som ska skapas på en Premium-klustercache med klustring. |
| Virtual Network |-v, --virtual-nätverk |När du är värd för cacheminnet i ett virtuellt nätverk anger du det exakta ARM-resurs-ID:n för det virtuella nätverket som ska distribuera Azure-cachen för Redis i. Exempelformat: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| nyckeltyp |-t, --nyckel-typ |Typ av nyckel att förnya. Giltiga värden: [Primär, Sekundär] |
| StatisktIP |-p, --static-ip \<static-ip\> |När du är värd för cacheminnet i ett VNET anger du en unik IP-adress i undernätet för cacheminnet. Om det inte anges, väljs en för dig från undernätet. |
| Undernät |t, --undernät \<undernät\> |När du är värd för cacheminnet i ett VNET anger du namnet på det undernät som cachen ska distribueras i. |
| VirtualNetwork |-v, --virtual-network \<virtuellt nätverk\> |När du är värd för cacheminnet i ett virtuellt nätverk anger du det exakta ARM-resurs-ID:n för det virtuella nätverket som ska distribuera Azure-cachen för Redis i. Exempelformat: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| Prenumeration |-s, --prenumeration |Prenumerationsidentifieraren. |

## <a name="see-all-azure-cache-for-redis-commands"></a>Se alla Azure Cache för Redis-kommandon
Om du vill visa alla Azure Cache for `azure rediscache -h` Redis-kommandon och deras parametrar använder du kommandot.

    C:\>azure rediscache -h
    help:    Commands to manage your Azure Cache for Redis(s)
    help:
    help:    Create an Azure Cache for Redis
    help:      rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
    help:
    help:    Delete an existing Azure Cache for Redis
    help:      rediscache delete [--name <name> --resource-group <resource-group> ]
    help:
    help:    List all Azure Cache for Redis within your Subscription or Resource Group
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

## <a name="create-an-azure-cache-for-redis"></a>Skapa en Azure Cache for Redis
Om du vill skapa en Azure-cache för Redis använder du följande kommando:

    azure rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]

Om du vill ha mer `azure rediscache create -h` information om det här kommandot kör du kommandot.

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

## <a name="delete-an-existing-azure-cache-for-redis"></a>Ta bort en befintlig Azure-cache för Redis
Om du vill ta bort en Azure-cache för Redis använder du följande kommando:

    azure rediscache delete [--name <name> --resource-group <resource-group> ]

Om du vill ha mer `azure rediscache delete -h` information om det här kommandot kör du kommandot.

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

## <a name="list-all-azure-cache-for-redis-within-your-subscription-or-resource-group"></a>Lista all Azure-cache för redis i din prenumeration eller resursgrupp
Om du vill visa alla Azure-cache för redis i din prenumeration eller resursgrupp använder du följande kommando:

    azure rediscache list [options]

Om du vill ha mer `azure rediscache list -h` information om det här kommandot kör du kommandot.

    C:\>azure rediscache list -h
    help:    List all Azure Cache for Redis within your Subscription or Resource Group
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

## <a name="show-properties-of-an-existing-azure-cache-for-redis"></a>Visa egenskaper för en befintlig Azure-cache för Redis
Om du vill visa egenskaper för en befintlig Azure-cache för Redis använder du följande kommando:

    azure rediscache show [--name <name> --resource-group <resource-group>]

Om du vill ha mer `azure rediscache show -h` information om det här kommandot kör du kommandot.

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

## <a name="change-settings-of-an-existing-azure-cache-for-redis"></a>Ändra inställningarna för en befintlig Azure-cache för Redis
Om du vill ändra inställningarna för en befintlig Azure-cache för Redis använder du följande kommando:

    azure rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]

Om du vill ha mer `azure rediscache set -h` information om det här kommandot kör du kommandot.

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

## <a name="renew-the-authentication-key-for-an-existing-azure-cache-for-redis"></a>Förnya autentiseringsnyckeln för en befintlig Azure-cache för Redis
Om du vill förnya autentiseringsnyckeln för en befintlig Azure-cache för Redis använder du följande kommando:

    azure rediscache renew-key [--name <name> --resource-group <resource-group> --key-type <key-type>]

Ange `Primary` `Secondary` eller `key-type`för .

Om du vill ha mer `azure rediscache renew-key -h` information om det här kommandot kör du kommandot.

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

## <a name="list-primary-and-secondary-keys-of-an-existing-azure-cache-for-redis"></a>Lista primära och sekundära nycklar för en befintlig Azure-cache för Redis
Om du vill visa primära och sekundära nycklar för en befintlig Azure-cache för Redis använder du följande kommando:

    azure rediscache list-keys [--name <name> --resource-group <resource-group>]

Om du vill ha mer `azure rediscache list-keys -h` information om det här kommandot kör du kommandot.

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
