---
title: Hantera Azure cache för Redis med Azure PowerShell
description: Lär dig hur du utför administrativa uppgifter för Azure cache för Redis med hjälp av Azure PowerShell.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/13/2017
ms.author: yegu
ms.openlocfilehash: 6eeabb279f5bd5165ebb249651b8bc926deb92a6
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/28/2019
ms.locfileid: "75530926"
---
# <a name="manage-azure-cache-for-redis-with-azure-powershell"></a>Hantera Azure cache för Redis med Azure PowerShell
> [!div class="op_single_selector"]
> * [PowerShell](cache-how-to-manage-redis-cache-powershell.md)
> * [Azure CLI](cache-manage-cli.md)
> 
> 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Det här avsnittet visar hur du utför vanliga uppgifter som att skapa, uppdatera och skala Azure-cachen för Redis-instanser, hur du återskapar åtkomst nycklar och hur du visar information om dina cacheminnen. En fullständig lista över Azure-cache för PowerShell-cmdlets för Redis finns i [Azure cache för Redis-cmdletar](https://docs.microsoft.com/powershell/module/az.rediscache).

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

Mer information om den klassiska distributions modellen finns i [Azure Resource Manager vs. klassisk distribution: förstå distributions modeller och status för dina resurser](../azure-resource-manager/resource-manager-deployment-model.md).

## <a name="prerequisites"></a>Krav
Om du redan har installerat Azure PowerShell måste du ha Azure PowerShell version 1.0.0 eller senare. Du kan kontrol lera vilken version av Azure PowerShell som du har installerat med det här kommandot i Azure PowerShell kommando tolken.

    Get-Module Az | format-table version


Först måste du logga in på Azure med det här kommandot.

    Connect-AzAccount

Ange e-postadressen för ditt Azure-konto och lösen ordet i dialog rutan Microsoft Azure inloggning.

Sedan, om du har flera Azure-prenumerationer, måste du ange din Azure-prenumeration. Kör det här kommandot om du vill se en lista över dina aktuella prenumerationer.

    Get-AzSubscription | sort SubscriptionName | Select SubscriptionName

Kör följande kommando för att ange prenumerationen. I följande exempel är prenumerations namnet `ContosoSubscription`.

    Select-AzSubscription -SubscriptionName ContosoSubscription

Innan du kan använda Windows PowerShell med Azure Resource Manager behöver du följande:

* Windows PowerShell, version 3,0 eller 4,0. Du hittar versionen av Windows PowerShell genom att skriva:`$PSVersionTable` och kontrol lera värdet för `PSVersion` är 3,0 eller 4,0. Information om hur du installerar en kompatibel version finns i [Windows Management framework 3,0](https://www.microsoft.com/download/details.aspx?id=34595) eller [windows Management Framework 4,0](https://www.microsoft.com/download/details.aspx?id=40855).

Om du vill ha mer information om en cmdlet som du ser i den här självstudien använder du cmdleten Get-Help.

    Get-Help <cmdlet-name> -Detailed

Om du till exempel vill få hjälp med `New-AzRedisCache`-cmdlet skriver du:

    Get-Help New-AzRedisCache -Detailed

### <a name="how-to-connect-to-other-clouds"></a>Så här ansluter du till andra moln
Som standard är Azure-miljön `AzureCloud`, som representerar den globala Azure Cloud-instansen. Om du vill ansluta till en annan instans använder du kommandot `Connect-AzAccount` med kommando rads växeln `-Environment` eller-`EnvironmentName` med önskat miljö-eller miljö namn.

Kör cmdleten `Get-AzEnvironment` om du vill se en lista över tillgängliga miljöer.

### <a name="to-connect-to-the-azure-government-cloud"></a>Så här ansluter du till Azure Government molnet
Använd något av följande kommandon för att ansluta till Azure Government molnet.

    Connect-AzAccount -EnvironmentName AzureUSGovernment

eller

    Connect-AzAccount -Environment (Get-AzEnvironment -Name AzureUSGovernment)

Använd någon av följande platser om du vill skapa en cache i Azure Government molnet.

* USGov Virginia
* USGov Iowa

Mer information om Azure Government molnet finns i [Microsoft Azure Government](https://azure.microsoft.com/features/gov/) och [Microsoft Azure Government Developer Guide](../azure-government-developer-guide.md).

### <a name="to-connect-to-the-azure-china-cloud"></a>Så här ansluter du till Azure Kina-molnet
Använd något av följande kommandon för att ansluta till molnet för Azure Kina.

    Connect-AzAccount -EnvironmentName AzureChinaCloud

eller

    Connect-AzAccount -Environment (Get-AzEnvironment -Name AzureChinaCloud)

Använd någon av följande platser om du vill skapa en cache i Azure Kina-molnet.

* Kina, östra
* Kina, norra

Mer information om Azure Kina-molnet finns i [AzureChinaCloud för Azure som drivs av 21Vianet i Kina](https://www.windowsazure.cn/).

### <a name="to-connect-to-microsoft-azure-germany"></a>Så här ansluter du till Microsoft Azure Tyskland
Använd något av följande kommandon för att ansluta till Microsoft Azure Tyskland.

    Connect-AzAccount -EnvironmentName AzureGermanCloud


eller

    Connect-AzAccount -Environment (Get-AzEnvironment -Name AzureGermanCloud)

Om du vill skapa en cache i Microsoft Azure Tyskland använder du någon av följande platser.

* Tyskland, centrala
* Tyskland, nordöstra

Mer information om Microsoft Azure Tyskland finns i [Microsoft Azure Tyskland](https://azure.microsoft.com/overview/clouds/germany/).

### <a name="properties-used-for-azure-cache-for-redis-powershell"></a>Egenskaper som används för Azure cache för Redis PowerShell
Följande tabell innehåller egenskaper och beskrivningar för vanliga parametrar när du skapar och hanterar Azure-cache för Redis-instanser med hjälp av Azure PowerShell.

| Parameter | Beskrivning | Default |
| --- | --- | --- |
| Namn |Namn på cacheminnet | |
| Location |Plats för cachen | |
| ResourceGroupName |Resurs grupp namn som cachen ska skapas i | |
| Storlek |Storleken på cacheminnet. Giltiga värden är: P1, P2, P3, P4, C0, C1, C2, C3, C4, C5, C6, 250 GB, 1 GB, 2,5 GB, 6 GB, 13GB, 26GB, 53GB |1 GB |
| ShardCount |Antalet Shards som ska skapas när du skapar en Premium-cache med klustrad aktive rad. Giltiga värden är: 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 | |
| SKU |Anger SKU för cacheminnet. Giltiga värden är: Basic, standard, Premium |Standard |
| RedisConfiguration |Anger konfigurations inställningar för Redis. Mer information om varje inställning finns i följande [RedisConfiguration Properties](#redisconfiguration-properties) -tabell. | |
| EnableNonSslPort |Anger om icke-SSL-porten är aktive rad. |Falskt |
| MaxMemoryPolicy |Den här parametern är inaktuell, Använd RedisConfiguration i stället. | |
| StaticIP |När du är värd för din cache i ett VNET, anger en unik IP-adress i under nätet för cachen. Om detta inte anges väljs en för dig från under nätet. | |
| Undernät |När du är värd för din cache i ett VNET anger namnet på det undernät som cachen ska distribueras till. | |
| VirtualNetwork |När du är värd för din cache i ett VNET, anger resurs-ID för det VNET som cachen ska distribueras till. | |
| KeyType |Anger vilken åtkomst nyckel som ska återskapas när åtkomst nycklar förnyas. Giltiga värden är: primär, sekundär | |

### <a name="redisconfiguration-properties"></a>Egenskaper för RedisConfiguration
| Egenskap | Beskrivning | Prisnivåer |
| --- | --- | --- |
| RDB – säkerhets kopiering har Aktiver ATS |Huruvida [Redis data persistes](cache-how-to-premium-persistence.md) är aktiverat |Endast Premium |
| rdb-storage-connection-string |Anslutnings strängen till lagrings kontot för [Redis data persistes](cache-how-to-premium-persistence.md) |Endast Premium |
| rdb-backup-frequency |Säkerhets kopierings frekvensen för [Redis data persistes](cache-how-to-premium-persistence.md) |Endast Premium |
| maxmemory-reserved |Konfigurerar det minne som är [reserverat](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) för icke-cache-processer |Standard och Premium |
| maxmemory-princip |Konfigurerar [borttagnings principen](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) för cachen |Alla pris nivåer |
| notify-keyspace-events |Konfigurerar [meddelanden om disk utrymme](cache-configure.md#keyspace-notifications-advanced-settings) |Standard och Premium |
| hash-Max-ziplist-poster |Konfigurerar [minnes optimering](https://redis.io/topics/memory-optimization) för små mängd data typer |Standard och Premium |
| hash-max-ziplist-value |Konfigurerar [minnes optimering](https://redis.io/topics/memory-optimization) för små mängd data typer |Standard och Premium |
| set-max-intset-entries |Konfigurerar [minnes optimering](https://redis.io/topics/memory-optimization) för små mängd data typer |Standard och Premium |
| zset-max-ziplist-entries |Konfigurerar [minnes optimering](https://redis.io/topics/memory-optimization) för små mängd data typer |Standard och Premium |
| zset-max-ziplist-value |Konfigurerar [minnes optimering](https://redis.io/topics/memory-optimization) för små mängd data typer |Standard och Premium |
| databases |Konfigurerar antalet databaser. Den här egenskapen kan bara konfigureras när cachen skapas. |Standard och Premium |

## <a name="to-create-an-azure-cache-for-redis"></a>Så här skapar du en Azure-cache för Redis
Nya Azure cache för Redis-instanser skapas med cmdleten [New-AzRedisCache](https://docs.microsoft.com/powershell/module/az.rediscache/new-azrediscache) .

> [!IMPORTANT]
> Första gången du skapar ett Azure-cacheminne för Redis i en prenumeration med hjälp av Azure Portal, registrerar portalen `Microsoft.Cache` namn området för den prenumerationen. Om du försöker skapa den första Azure-cachen för Redis i en prenumeration med hjälp av PowerShell måste du först registrera det namn området med hjälp av följande kommando. annars fungerar inte cmdlets, till exempel `New-AzRedisCache` och `Get-AzRedisCache`.
> 
> `Register-AzResourceProvider -ProviderNamespace "Microsoft.Cache"`
> 
> 

Om du vill se en lista över tillgängliga parametrar och deras beskrivningar för `New-AzRedisCache`kör du följande kommando.

    PS C:\> Get-Help New-AzRedisCache -detailed

    NAME
        New-AzRedisCache

    SYNOPSIS
        Creates a new Azure Cache for Redis.


    SYNTAX
        New-AzRedisCache -Name <String> -ResourceGroupName <String> -Location <String> [-RedisVersion <String>]
        [-Size <String>] [-Sku <String>] [-MaxMemoryPolicy <String>] [-RedisConfiguration <Hashtable>] [-EnableNonSslPort
        <Boolean>] [-ShardCount <Integer>] [-VirtualNetwork <String>] [-Subnet <String>] [-StaticIP <String>]
        [<CommonParameters>]


    DESCRIPTION
        The New-AzRedisCache cmdlet creates a new Azure Cache for Redis.


    PARAMETERS
        -Name <String>
            Name of the Azure Cache for Redis to create.

        -ResourceGroupName <String>
            Name of resource group in which to create the Azure Cache for Redis.

        -Location <String>
            Location in which to create the Azure Cache for Redis.

        -RedisVersion <String>
            RedisVersion is deprecated and will be removed in future release.

        -Size <String>
            Size of the Azure Cache for Redis. The default value is 1GB or C1. Possible values are P1, P2, P3, P4, C0, C1, C2, C3,
            C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB.

        -Sku <String>
            Sku of Azure Cache for Redis. The default value is Standard. Possible values are Basic, Standard and Premium.

        -MaxMemoryPolicy <String>
            The 'MaxMemoryPolicy' setting has been deprecated. Please use 'RedisConfiguration' setting to set
            MaxMemoryPolicy. e.g. -RedisConfiguration @{"maxmemory-policy" = "allkeys-lru"}

        -RedisConfiguration <Hashtable>
            All Redis Configuration Settings. Few possible keys: rdb-backup-enabled, rdb-storage-connection-string,
            rdb-backup-frequency, maxmemory-reserved, maxmemory-policy, notify-keyspace-events, hash-max-ziplist-entries,
            hash-max-ziplist-value, set-max-intset-entries, zset-max-ziplist-entries, zset-max-ziplist-value, databases.

        -EnableNonSslPort <Boolean>
            EnableNonSslPort is used by Azure Cache for Redis. If no value is provided, the default value is false and the
            non-SSL port will be disabled. Possible values are true and false.

        -ShardCount <Integer>
            The number of shards to create on a Premium Cluster Cache.

        -VirtualNetwork <String>
            The exact ARM resource ID of the virtual network to deploy the Azure Cache for Redis in. Example format: /subscriptions/{
            subid}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicNetwork/VirtualNetworks/{vnetName}

        -Subnet <String>
            Required when deploying an Azure Cache for Redis inside an existing Azure Virtual Network.

        -StaticIP <String>
            Required when deploying an Azure Cache for Redis inside an existing Azure Virtual Network.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

Kör följande kommando för att skapa en cache med standard parametrar.

    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US"

`ResourceGroupName`, `Name`och `Location` är obligatoriska parametrar, men resten är valfria och har standardvärden. Genom att köra föregående kommando skapas en standard-SKU Azure-cache för Redis-instansen med det angivna namnet, platsen och resurs gruppen, vilket är 1 GB i storlek med en icke-SSL-port inaktive rad.

Om du vill skapa en Premium-cache anger du storleken P1 (6 GB-60 GB), P2 (13 GB-130 GB), P3 (26 GB-260 GB) eller P4 (53 GB-530 GB). Om du vill aktivera klustring anger du ett Shard-antal med hjälp av parametern `ShardCount`. I följande exempel skapas en P1 Premium-cache med 3 Shards. En P1 Premium-cache är 6 GB stor och eftersom vi har angett tre Shards är den totala storleken 18 GB (3 × 6 GB).

    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P1 -ShardCount 3

Om du vill ange värden för parametern `RedisConfiguration` omger du värdena inuti `{}` som nyckel/värde-par som `@{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}`. I följande exempel skapas en standard-1 GB-cache med `allkeys-random` maxmemory-princip och meddelanden om disk utrymme som kon figurer ATS med `KEA`. Mer information finns i [meddelanden om disk utrymme (avancerade inställningar)](cache-configure.md#keyspace-notifications-advanced-settings) och [minnes principer](cache-configure.md#memory-policies).

    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}

<a name="databases"></a>

## <a name="to-configure-the-databases-setting-during-cache-creation"></a>Konfigurera databas inställningen när cachen skapas
Inställningen `databases` kan bara konfigureras när cachelagring skapas. I följande exempel skapas en Premium P3-cache (26 GB) med 48-databaser med cmdleten [New-AzRedisCache](https://docs.microsoft.com/powershell/module/az.rediscache/New-azRedisCache) .

    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P3 -RedisConfiguration @{"databases" = "48"}

Mer information om `databases`-egenskapen finns i [standard Azure-cache för Server konfiguration för Redis](cache-configure.md#default-redis-server-configuration). Mer information om hur du skapar en cache med cmdleten [New-AzRedisCache](https://docs.microsoft.com/powershell/module/az.rediscache/new-azrediscache) finns i föregående att skapa en Azure-cache för Redis-avsnittet.

## <a name="to-update-an-azure-cache-for-redis"></a>Så här uppdaterar du ett Azure-cacheminne för Redis
Azure cache för Redis-instanser uppdateras med cmdleten [set-AzRedisCache](https://docs.microsoft.com/powershell/module/az.rediscache/Set-azRedisCache) .

Om du vill se en lista över tillgängliga parametrar och deras beskrivningar för `Set-AzRedisCache`kör du följande kommando.

    PS C:\> Get-Help Set-AzRedisCache -detailed

    NAME
        Set-AzRedisCache

    SYNOPSIS
        Set Azure Cache for Redis updatable parameters.

    SYNTAX
        Set-AzRedisCache -Name <String> -ResourceGroupName <String> [-Size <String>] [-Sku <String>]
        [-MaxMemoryPolicy <String>] [-RedisConfiguration <Hashtable>] [-EnableNonSslPort <Boolean>] [-ShardCount
        <Integer>] [<CommonParameters>]

    DESCRIPTION
        The Set-AzRedisCache cmdlet sets Azure Cache for Redis parameters.

    PARAMETERS
        -Name <String>
            Name of the Azure Cache for Redis to update.

        -ResourceGroupName <String>
            Name of the resource group for the cache.

        -Size <String>
            Size of the Azure Cache for Redis. The default value is 1GB or C1. Possible values are P1, P2, P3, P4, C0, C1, C2, C3,
            C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB.

        -Sku <String>
            Sku of Azure Cache for Redis. The default value is Standard. Possible values are Basic, Standard and Premium.

        -MaxMemoryPolicy <String>
            The 'MaxMemoryPolicy' setting has been deprecated. Please use 'RedisConfiguration' setting to set
            MaxMemoryPolicy. e.g. -RedisConfiguration @{"maxmemory-policy" = "allkeys-lru"}

        -RedisConfiguration <Hashtable>
            All Redis Configuration Settings. Few possible keys: rdb-backup-enabled, rdb-storage-connection-string,
            rdb-backup-frequency, maxmemory-reserved, maxmemory-policy, notify-keyspace-events, hash-max-ziplist-entries,
            hash-max-ziplist-value, set-max-intset-entries, zset-max-ziplist-entries, zset-max-ziplist-value.

        -EnableNonSslPort <Boolean>
            EnableNonSslPort is used by Azure Cache for Redis. The default value is null and no change will be made to the
            currently configured value. Possible values are true and false.

        -ShardCount <Integer>
            The number of shards to create on a Premium Cluster Cache.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

`Set-AzRedisCache` cmdlet kan användas för att uppdatera egenskaper som `Size`, `Sku`, `EnableNonSslPort`och `RedisConfiguration` värden. 

Följande kommando uppdaterar maxmemory-principen för Azure-cachen för Redis med namnet cachelagring.

    Set-AzRedisCache -ResourceGroupName "myGroup" -Name "myCache" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random"}

<a name="scale"></a>

## <a name="to-scale-an-azure-cache-for-redis"></a>Skala en Azure-cache för Redis
`Set-AzRedisCache` kan användas för att skala en Azure-cache för Redis-instansen när egenskaperna `Size`, `Sku`eller `ShardCount` ändras. 

> [!NOTE]
> Skalning av en cache med PowerShell omfattas av samma begränsningar och rikt linjer som skalning av en cache från Azure Portal. Du kan skala till en annan pris nivå med följande begränsningar.
> 
> * Du kan inte skala från en högre pris nivå till en lägre pris nivå.
> * Du kan inte skala från en **Premium** -cache till en **standard** -eller **Basic** -cache.
> * Du kan inte skala från **en** standardcache till en **grundläggande** cache.
> * Du kan skala från en **grundläggande** **cache till en** standardcache, men du kan inte ändra storlek på samma tid. Om du behöver en annan storlek kan du utföra en efterföljande skalnings åtgärd till önskad storlek.
> * Du kan inte skala från en **grundläggande** cache direkt till en **Premium** -cache. Du måste skala från **Basic** till **standard** i en skalnings åtgärd och sedan från **standard** till **Premium** i en efterföljande skalnings åtgärd.
> * Du kan inte skala från en större storlek till storleken på **C0 (250 MB)** .
> 
> Mer information finns i [skala Azure cache för Redis](cache-how-to-scale.md).
> 
> 

I följande exempel visas hur du skalar en cache med namnet `myCache` till en 2,5 GB-cache. Observera att det här kommandot fungerar för både en Basic-eller standard-cache.

    Set-AzRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB

När det här kommandot har utfärdats returneras statusen för cachen (liknar att anropa `Get-AzRedisCache`). Observera att `ProvisioningState` är `Scaling`.

    PS C:\> Set-AzRedisCache -Name myCache -ResourceGroupName myGroup -Size 2.5GB


    Name               : mycache
    Id                 : /subscriptions/12ad12bd-abdc-2231-a2ed-a2b8b246bbad4/resourceGroups/mygroup/providers/Mi
                         crosoft.Cache/Redis/mycache
    Location           : South Central US
    Type               : Microsoft.Cache/Redis
    HostName           : mycache.redis.cache.windows.net
    Port               : 6379
    ProvisioningState  : Scaling
    SslPort            : 6380
    RedisConfiguration : {[maxmemory-policy, volatile-lru], [maxmemory-reserved, 150], [notify-keyspace-events, KEA],
                         [maxmemory-delta, 150]...}
    EnableNonSslPort   : False
    RedisVersion       : 3.0
    Size               : 1GB
    Sku                : Standard
    ResourceGroupName  : mygroup
    PrimaryKey         : ....
    SecondaryKey       : ....
    VirtualNetwork     :
    Subnet             :
    StaticIP           :
    TenantSettings     : {}
    ShardCount         :

När skalnings åtgärden är klar ändras `ProvisioningState` till `Succeeded`. Om du behöver göra en efterföljande skalnings åtgärd, till exempel genom att ändra från Basic till standard och sedan ändra storlek, måste du vänta tills den föregående åtgärden har slutförts eller så visas ett fel som liknar följande.

    Set-AzRedisCache : Conflict: The resource '...' is not in a stable state, and is currently unable to accept the update request.

## <a name="to-get-information-about-an-azure-cache-for-redis"></a>Hämta information om en Azure-cache för Redis
Du kan hämta information om en cache med hjälp av cmdleten [Get-AzRedisCache](https://docs.microsoft.com/powershell/module/az.rediscache/get-azrediscache) .

Om du vill se en lista över tillgängliga parametrar och deras beskrivningar för `Get-AzRedisCache`kör du följande kommando.

    PS C:\> Get-Help Get-AzRedisCache -detailed

    NAME
        Get-AzRedisCache

    SYNOPSIS
        Gets details about a single cache or all caches in the specified resource group or all caches in the current
        subscription.

    SYNTAX
        Get-AzRedisCache [-Name <String>] [-ResourceGroupName <String>] [<CommonParameters>]

    DESCRIPTION
        The Get-AzRedisCache cmdlet gets the details about a cache or caches depending on input parameters. If both
        ResourceGroupName and Name parameters are provided then Get-AzRedisCache will return details about the
        specific cache name provided.

        If only ResourceGroupName is provided than it will return details about all caches in the specified resource group.

        If no parameters are given than it will return details about all caches the current subscription.

    PARAMETERS
        -Name <String>
            The name of the cache. When this parameter is provided along with ResourceGroupName, Get-AzRedisCache
            returns the details for the cache.

        -ResourceGroupName <String>
            The name of the resource group that contains the cache or caches. If ResourceGroupName is provided with Name
            then Get-AzRedisCache returns the details of the cache specified by Name. If only the ResourceGroup
            parameter is provided, then details for all caches in the resource group are returned.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

Om du vill returnera information om alla cacheminnen i den aktuella prenumerationen kör `Get-AzRedisCache` utan parametrar.

    Get-AzRedisCache

Om du vill returnera information om alla cacheminnen i en speciell resurs grupp kör `Get-AzRedisCache` med parametern `ResourceGroupName`.

    Get-AzRedisCache -ResourceGroupName myGroup

Om du vill returnera information om en speciell cache kör du `Get-AzRedisCache` med parametern `Name` som innehåller namnet på cachen och `ResourceGroupName`-parametern med den aktuella cachen i resurs gruppen.

    PS C:\> Get-AzRedisCache -Name myCache -ResourceGroupName myGroup

    Name               : mycache
    Id                 : /subscriptions/12ad12bd-abdc-2231-a2ed-a2b8b246bbad4/resourceGroups/myGroup/providers/Mi
                         crosoft.Cache/Redis/mycache
    Location           : South Central US
    Type               : Microsoft.Cache/Redis
    HostName           : mycache.redis.cache.windows.net
    Port               : 6379
    ProvisioningState  : Succeeded
    SslPort            : 6380
    RedisConfiguration : {[maxmemory-policy, volatile-lru], [maxmemory-reserved, 62], [notify-keyspace-events, KEA],
                         [maxclients, 1000]...}
    EnableNonSslPort   : False
    RedisVersion       : 3.0
    Size               : 1GB
    Sku                : Standard
    ResourceGroupName  : myGroup
    VirtualNetwork     :
    Subnet             :
    StaticIP           :
    TenantSettings     : {}
    ShardCount         :

## <a name="to-retrieve-the-access-keys-for-an-azure-cache-for-redis"></a>Hämta åtkomst nycklar för en Azure-cache för Redis
Om du vill hämta åtkomst nycklarna för ditt cacheminne kan du använda cmdleten [Get-AzRedisCacheKey](https://docs.microsoft.com/powershell/module/az.rediscache/Get-azRedisCacheKey) .

Om du vill se en lista över tillgängliga parametrar och deras beskrivningar för `Get-AzRedisCacheKey`kör du följande kommando.

    PS C:\> Get-Help Get-AzRedisCacheKey -detailed

    NAME
        Get-AzRedisCacheKey

    SYNOPSIS
        Gets the accesskeys for the specified Azure Cache for Redis.


    SYNTAX
        Get-AzRedisCacheKey -Name <String> -ResourceGroupName <String> [<CommonParameters>]

    DESCRIPTION
        The Get-AzRedisCacheKey cmdlet gets the access keys for the specified cache.

    PARAMETERS
        -Name <String>
            Name of the Azure Cache for Redis.

        -ResourceGroupName <String>
            Name of the resource group for the cache.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

Om du vill hämta nycklar för cacheminnet anropar du `Get-AzRedisCacheKey` cmdlet och skickar namnet på din cache till namnet på den resurs grupp som innehåller cachen.

    PS C:\> Get-AzRedisCacheKey -Name myCache -ResourceGroupName myGroup

    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : ABhfB757JgjIgt785JgKH9865eifmekfnn649303JKL=

## <a name="to-regenerate-access-keys-for-your-azure-cache-for-redis"></a>Återskapa åtkomst nycklar för Azure cache för Redis
Du kan använda cmdleten [New-AzRedisCacheKey](https://docs.microsoft.com/powershell/module/az.rediscache/New-azRedisCacheKey) för att återskapa åtkomst nycklarna för cacheminnet.

Om du vill se en lista över tillgängliga parametrar och deras beskrivningar för `New-AzRedisCacheKey`kör du följande kommando.

    PS C:\> Get-Help New-AzRedisCacheKey -detailed

    NAME
        New-AzRedisCacheKey

    SYNOPSIS
        Regenerates the access key of an Azure Cache for Redis.

    SYNTAX
        New-AzRedisCacheKey -Name <String> -ResourceGroupName <String> -KeyType <String> [-Force] [<CommonParameters>]

    DESCRIPTION
        The New-AzRedisCacheKey cmdlet regenerate the access key of an Azure Cache for Redis.

    PARAMETERS
        -Name <String>
            Name of the Azure Cache for Redis.

        -ResourceGroupName <String>
            Name of the resource group for the cache.

        -KeyType <String>
            Specifies whether to regenerate the primary or secondary access key. Possible values are Primary or Secondary.

        -Force
            When the Force parameter is provided, the specified access key is regenerated without any confirmation prompts.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

Om du vill återskapa den primära eller sekundära nyckeln för cacheminnet anropar du `New-AzRedisCacheKey` cmdlet och skickar in namnet, resurs gruppen och anger antingen `Primary` eller `Secondary` för parametern `KeyType`. I följande exempel återskapas den sekundära åtkomst nyckeln för en cache.

    PS C:\> New-AzRedisCacheKey -Name myCache -ResourceGroupName myGroup -KeyType Secondary

    Confirm
    Are you sure you want to regenerate Secondary key for Azure Cache for Redis 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y


    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : c53hj3kh4jhHjPJk8l0jji785JgKH9865eifmekfnn6=

## <a name="to-delete-an-azure-cache-for-redis"></a>Ta bort ett Azure-cacheminne för Redis
Om du vill ta bort en Azure-cache för Redis använder du cmdleten [Remove-AzRedisCache](https://docs.microsoft.com/powershell/module/az.rediscache/remove-azrediscache) .

Om du vill se en lista över tillgängliga parametrar och deras beskrivningar för `Remove-AzRedisCache`kör du följande kommando.

    PS C:\> Get-Help Remove-AzRedisCache -detailed

    NAME
        Remove-AzRedisCache

    SYNOPSIS
        Remove Azure Cache for Redis if exists.

    SYNTAX
        Remove-AzRedisCache -Name <String> -ResourceGroupName <String> [-Force] [-PassThru] [<CommonParameters>

    DESCRIPTION
        The Remove-AzRedisCache cmdlet removes an Azure Cache for Redis if it exists.

    PARAMETERS
        -Name <String>
            Name of the Azure Cache for Redis to remove.

        -ResourceGroupName <String>
            Name of the resource group of the cache to remove.

        -Force
            When the Force parameter is provided, the cache is removed without any confirmation prompts.

        -PassThru
            By default Remove-AzRedisCache removes the cache and does not return any value. If the PassThru par
            is provided then Remove-AzRedisCache returns a boolean value indicating the success of the operatio

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

I följande exempel tas cachen med namnet `myCache` bort.

    PS C:\> Remove-AzRedisCache -Name myCache -ResourceGroupName myGroup

    Confirm
    Are you sure you want to remove Azure Cache for Redis 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y


## <a name="to-import-an-azure-cache-for-redis"></a>Så här importerar du ett Azure-cacheminne för Redis
Du kan importera data till en Azure-cache för Redis-instans med hjälp av `Import-AzRedisCache`-cmdleten.

> [!IMPORTANT]
> Import/export är endast tillgängligt för cacheminnen på [Premium-nivån](cache-premium-tier-intro.md) . Mer information om import/export finns i [Importera och exportera data i Azure cache för Redis](cache-how-to-import-export-data.md).
> 
> 

Om du vill se en lista över tillgängliga parametrar och deras beskrivningar för `Import-AzRedisCache`kör du följande kommando.

    PS C:\> Get-Help Import-AzRedisCache -detailed

    NAME
        Import-AzRedisCache

    SYNOPSIS
        Import data from blobs to Azure Cache for Redis.


    SYNTAX
        Import-AzRedisCache -Name <String> -ResourceGroupName <String> -Files <String[]> [-Format <String>] [-Force]
        [-PassThru] [<CommonParameters>]


    DESCRIPTION
        The Import-AzRedisCache cmdlet imports data from the specified blobs into Azure Cache for Redis.


    PARAMETERS
        -Name <String>
            The name of the cache.

        -ResourceGroupName <String>
            The name of the resource group that contains the cache.

        -Files <String[]>
            SAS urls of blobs whose content should be imported into the cache.

        -Format <String>
            Format for the blob.  Currently "rdb" is the only supported, with other formats expected in the future.

        -Force
            When the Force parameter is provided, import will be performed without any confirmation prompts.

        -PassThru
            By default Import-AzRedisCache imports data in cache and does not return any value. If the PassThru
            parameter is provided then Import-AzRedisCache returns a boolean value indicating the success of the
            operation.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).


Följande kommando importerar data från den blob som anges av SAS-URI: n till Azure cache för Redis.

    PS C:\>Import-AzRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Files @("https://mystorageaccount.blob.core.windows.net/mycontainername/blobname?sv=2015-04-05&sr=b&sig=caIwutG2uDa0NZ8mjdNJdgOY8%2F8mhwRuGNdICU%2B0pI4%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwd") -Force

## <a name="to-export-an-azure-cache-for-redis"></a>Exportera en Azure-cache för Redis
Du kan exportera data från en Azure-cache för Redis-instans med hjälp av `Export-AzRedisCache`-cmdleten.

> [!IMPORTANT]
> Import/export är endast tillgängligt för cacheminnen på [Premium-nivån](cache-premium-tier-intro.md) . Mer information om import/export finns i [Importera och exportera data i Azure cache för Redis](cache-how-to-import-export-data.md).
> 
> 

Om du vill se en lista över tillgängliga parametrar och deras beskrivningar för `Export-AzRedisCache`kör du följande kommando.

    PS C:\> Get-Help Export-AzRedisCache -detailed

    NAME
        Export-AzRedisCache

    SYNOPSIS
        Exports data from Azure Cache for Redis to a specified container.


    SYNTAX
        Export-AzRedisCache -Name <String> -ResourceGroupName <String> -Prefix <String> -Container <String> [-Format
        <String>] [-PassThru] [<CommonParameters>]


    DESCRIPTION
        The Export-AzRedisCache cmdlet exports data from Azure Cache for Redis to a specified container.


    PARAMETERS
        -Name <String>
            The name of the cache.

        -ResourceGroupName <String>
            The name of the resource group that contains the cache.

        -Prefix <String>
            Prefix to use for blob names.

        -Container <String>
            SAS url of container where data should be exported.

        -Format <String>
            Format for the blob.  Currently "rdb" is the only supported, with other formats expected in the future.

        -PassThru
            By default Export-AzRedisCache does not return any value. If the PassThru parameter is provided
            then Export-AzRedisCache returns a boolean value indicating the success of the operation.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).


Följande kommando exporterar data från en Azure-cache för Redis-instansen till den behållare som anges av SAS-URI: n.

        PS C:\>Export-AzRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Prefix "blobprefix"
        -Container "https://mystorageaccount.blob.core.windows.net/mycontainer?sv=2015-04-05&sr=c&sig=HezZtBZ3DURmEGDduauE7
        pvETY4kqlPI8JCNa8ATmaw%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwdl"

## <a name="to-reboot-an-azure-cache-for-redis"></a>Starta om en Azure-cache för Redis
Du kan starta om Azure-cachen för Redis-instansen med hjälp av `Reset-AzRedisCache`-cmdleten.

> [!IMPORTANT]
> Omstart är endast tillgängligt för cacheminnen på [Premium-nivån](cache-premium-tier-intro.md) . Mer information om hur du startar om cacheminnet finns i [cache-administration-starta om](cache-administration.md#reboot).
> 
> 

Om du vill se en lista över tillgängliga parametrar och deras beskrivningar för `Reset-AzRedisCache`kör du följande kommando.

    PS C:\> Get-Help Reset-AzRedisCache -detailed

    NAME
        Reset-AzRedisCache

    SYNOPSIS
        Reboot specified node(s) of an Azure Cache for Redis instance.


    SYNTAX
        Reset-AzRedisCache -Name <String> -ResourceGroupName <String> -RebootType <String> [-ShardId <Integer>]
        [-Force] [-PassThru] [<CommonParameters>]


    DESCRIPTION
        The Reset-AzRedisCache cmdlet reboots the specified node(s) of an Azure Cache for Redis instance.


    PARAMETERS
        -Name <String>
            The name of the cache.

        -ResourceGroupName <String>
            The name of the resource group that contains the cache.

        -RebootType <String>
            Which node to reboot. Possible values are "PrimaryNode", "SecondaryNode", "AllNodes".

        -ShardId <Integer>
            Which shard to reboot when rebooting a premium cache with clustering enabled.

        -Force
            When the Force parameter is provided, reset will be performed without any confirmation prompts.

        -PassThru
            By default Reset-AzRedisCache does not return any value. If the PassThru parameter is provided
            then Reset-AzRedisCache returns a boolean value indicating the success of the operation.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).


Följande kommando startar om båda noderna i det angivna cacheminnet.

        PS C:\>Reset-AzRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -RebootType "AllNodes"
        -Force


## <a name="next-steps"></a>Nästa steg
Mer information om hur du använder Windows PowerShell med Azure finns i följande resurser:

* [Dokumentation om Azure cache för Redis-cmdlet på MSDN](https://docs.microsoft.com/powershell/module/az.rediscache)
* [Azure Resource Manager-cmdlet](https://go.microsoft.com/fwlink/?LinkID=394765): ar: Lär dig att använda cmdletarna i modulen Azure Resource Manager.
* [Använda resurs grupper för att hantera dina Azure-resurser](../azure-resource-manager/resource-group-template-deploy-portal.md): Lär dig hur du skapar och hanterar resurs grupper i Azure Portal.
* [Azure-blogg](https://azure.microsoft.com/blog/): Lär dig om nya funktioner i Azure.
* [Windows PowerShell-blogg](https://blogs.msdn.com/powershell): Lär dig om nya funktioner i Windows PowerShell.
* ["Hej, Scripting Guy!" Blogg](https://blogs.technet.com/b/heyscriptingguy/): få tips och knep från Windows PowerShell-communityn.

