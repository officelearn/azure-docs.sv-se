---
title: Hantera Azure-cache för Redis med Azure PowerShell
description: Lär dig hur du utför administrativa uppgifter för Azure Cache för Redis med Azure PowerShell.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/13/2017
ms.author: yegu
ms.openlocfilehash: a385d3ed7ef46389f96de72c98ffc29cebf60ec4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278537"
---
# <a name="manage-azure-cache-for-redis-with-azure-powershell"></a>Hantera Azure-cache för Redis med Azure PowerShell
> [!div class="op_single_selector"]
> * [Powershell](cache-how-to-manage-redis-cache-powershell.md)
> * [Azure CLI](cache-manage-cli.md)
> 
> 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Det här avsnittet visar hur du utför vanliga uppgifter som att skapa, uppdatera och skala dina Azure-cache för Redis-instanser, hur du återskapar åtkomstnycklar och hur du visar information om dina cacheminnen. En fullständig lista över Azure-cache för Redis PowerShell-cmdlets finns i [Azure Cache för Redis-cmdlets](https://docs.microsoft.com/powershell/module/az.rediscache).

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

Mer information om den klassiska distributionsmodellen finns i [Azure Resource Manager kontra klassisk distribution: Förstå distributionsmodeller och tillståndet för dina resurser](../azure-resource-manager/management/deployment-models.md).

## <a name="prerequisites"></a>Krav
Om du redan har installerat Azure PowerShell måste du ha Azure PowerShell version 1.0.0 eller senare. Du kan kontrollera vilken version av Azure PowerShell som du har installerat med det här kommandot i Kommandotolken i Azure PowerShell.

    Get-Module Az | format-table version


Först måste du logga in på Azure med det här kommandot.

    Connect-AzAccount

Ange e-postadressen för ditt Azure-konto och dess lösenord i microsoft Azure-inloggningsdialogrutan.

Om du har flera Azure-prenumerationer måste du sedan ange din Azure-prenumeration. Om du vill visa en lista över dina aktuella prenumerationer kör du det här kommandot.

    Get-AzSubscription | sort SubscriptionName | Select SubscriptionName

Om du vill ange prenumerationen kör du följande kommando. I följande exempel är `ContosoSubscription`prenumerationsnamnet .

    Select-AzSubscription -SubscriptionName ContosoSubscription

Innan du kan använda Windows PowerShell med Azure Resource Manager behöver du följande:

* Windows PowerShell, version 3.0 eller 4.0. Om du vill hitta versionen av`$PSVersionTable` Windows PowerShell `PSVersion` skriver du: och verifierar värdet på 3.0 eller 4.0. Om du vill installera en kompatibel version finns i [Windows Management Framework 3.0](https://www.microsoft.com/download/details.aspx?id=34595) eller [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855).

Om du vill ha detaljerad hjälp för alla cmdlet som du ser i den här självstudien använder du cmdleten Get-Help.

    Get-Help <cmdlet-name> -Detailed

Om du till exempel `New-AzRedisCache` vill ha hjälp med cmdlet skriver du:

    Get-Help New-AzRedisCache -Detailed

### <a name="how-to-connect-to-other-clouds"></a>Så här ansluter du till andra moln
Som standard azure-miljön är `AzureCloud`, som representerar den globala Azure molninstansen. Om du vill ansluta till `Connect-AzAccount` en `-Environment` annan`EnvironmentName` instans använder du kommandot med kommandoradsväxeln eller - med önskat miljö- eller miljönamn.

Om du vill se listan över `Get-AzEnvironment` tillgängliga miljöer kör du cmdleten.

### <a name="to-connect-to-the-azure-government-cloud"></a>Så här ansluter du till Azure Government Cloud
Om du vill ansluta till Azure Government Cloud använder du något av följande kommandon.

    Connect-AzAccount -EnvironmentName AzureUSGovernment

eller

    Connect-AzAccount -Environment (Get-AzEnvironment -Name AzureUSGovernment)

Om du vill skapa en cache i Azure Government Cloud använder du en av följande platser.

* USGov Virginia
* USGov Iowa

Mer information om Azure Government Cloud finns i [Microsoft Azure Government](https://azure.microsoft.com/features/gov/) och Microsoft Azure Government Developer [Guide](../azure-government-developer-guide.md).

### <a name="to-connect-to-the-azure-china-cloud"></a>Så här ansluter du till Azure China Cloud
Om du vill ansluta till Azure China Cloud använder du något av följande kommandon.

    Connect-AzAccount -EnvironmentName AzureChinaCloud

eller

    Connect-AzAccount -Environment (Get-AzEnvironment -Name AzureChinaCloud)

Om du vill skapa en cache i Azure China Cloud använder du någon av följande platser.

* Kina, östra
* Kina, norra

Mer information om Azure China Cloud finns i [AzureChinaCloud för Azure som drivs av 21Vianet i Kina](https://www.windowsazure.cn/).

### <a name="to-connect-to-microsoft-azure-germany"></a>Så här ansluter du till Microsoft Azure Germany
Om du vill ansluta till Microsoft Azure Tyskland använder du något av följande kommandon.

    Connect-AzAccount -EnvironmentName AzureGermanCloud


eller

    Connect-AzAccount -Environment (Get-AzEnvironment -Name AzureGermanCloud)

Om du vill skapa en cache i Microsoft Azure Tyskland använder du någon av följande platser.

* Tyskland, centrala
* Tyskland, nordöstra

Mer information om Microsoft Azure Tyskland finns i [Microsoft Azure Germany](https://azure.microsoft.com/overview/clouds/germany/).

### <a name="properties-used-for-azure-cache-for-redis-powershell"></a>Egenskaper som används för Azure Cache för Redis PowerShell
Följande tabell innehåller egenskaper och beskrivningar för vanliga parametrar när du skapar och hanterar dina Azure-cache för Redis-instanser med Azure PowerShell.

| Parameter | Beskrivning | Default |
| --- | --- | --- |
| Namn |Namnet på cacheminnet | |
| Location |Cachens plats | |
| ResourceGroupName |Resursgruppsnamn där cacheminnet ska skapas | |
| Storlek |Storleken på cacheminnet. Giltiga värden är: P1, P2, P3, P4, C0, C1, C2, C3, C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB |1 GB |
| ShardCount (ShardCount) |Antalet shards som ska skapas när du skapar en premiumcache med klusteraktiverad. Giltiga värden är: 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 | |
| SKU |Anger cacheminnets SKU. Giltiga värden är: Basic, Standard, Premium |Standard |
| Konfigurera om |Anger redis-konfigurationsinställningar. Mer information om varje inställning finns i följande egenskapstabell för [återkonfiguration.](#redisconfiguration-properties) | |
| EnableNonSslPort |Anger om icke-SSL-porten är aktiverad. |False |
| MaxMemoryPolicy |Den här parametern har inaktuellt - använd RedisConfiguration i stället. | |
| StatisktIP |När du är värd för cacheminnet i ett VNET anger du en unik IP-adress i undernätet för cacheminnet. Om det inte anges, väljs en för dig från undernätet. | |
| Undernät |När du är värd för cacheminnet i ett VNET anger du namnet på det undernät som cachen ska distribueras i. | |
| VirtualNetwork |När du är värd för cacheminnet i ett VNET anger du resurs-ID:n för det virtuella nätverk som cachen ska distribueras i. | |
| Keytype |Anger vilken åtkomstnyckel som ska återskapas vid förnyelse av åtkomstnycklar. Giltiga värden är: Primär, Sekundär | |

### <a name="redisconfiguration-properties"></a>Egenskaper för återkonfiguration
| Egenskap | Beskrivning | Prisnivåer |
| --- | --- | --- |
| rdb-backup-aktiverad |Om [Redis-data persistens](cache-how-to-premium-persistence.md) är aktiverat |Endast premium |
| rdb-lagring-anslutning-sträng |Anslutningssträngen till lagringskontot för [Redis-data persistens](cache-how-to-premium-persistence.md) |Endast premium |
| rdb-backup-frekvens |Säkerhetskopieringsfrekvensen för [Redis-data persistens](cache-how-to-premium-persistence.md) |Endast premium |
| maxmemory-reserverade |Konfigurerar [minnet som reserverats](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) för processer som inte är cacheprocesser |Standard och Premium |
| maxmemory-politik |Konfigurerar [vräkningsprincipen](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) för cacheminnet |Alla prisnivåer |
| notify-keyspace-events |Konfigurerar [keyspace-meddelanden](cache-configure.md#keyspace-notifications-advanced-settings) |Standard och Premium |
| hash-max-ziplist-poster |Konfigurerar [minnesoptimering](https://redis.io/topics/memory-optimization) för små mängddatatyper |Standard och Premium |
| hash-max-ziplist-värde |Konfigurerar [minnesoptimering](https://redis.io/topics/memory-optimization) för små mängddatatyper |Standard och Premium |
| set-max-intset-poster |Konfigurerar [minnesoptimering](https://redis.io/topics/memory-optimization) för små mängddatatyper |Standard och Premium |
| zset-max-ziplist-poster |Konfigurerar [minnesoptimering](https://redis.io/topics/memory-optimization) för små mängddatatyper |Standard och Premium |
| zset-max-ziplist-värde |Konfigurerar [minnesoptimering](https://redis.io/topics/memory-optimization) för små mängddatatyper |Standard och Premium |
| databaser |Konfigurerar antalet databaser. Den här egenskapen kan bara konfigureras när cache skapas. |Standard och Premium |

## <a name="to-create-an-azure-cache-for-redis"></a>Så här skapar du en Azure-cache för Redis
Nya Azure-cache för Redis-instanser skapas med cmdleten [New-AzRedisCache.](https://docs.microsoft.com/powershell/module/az.rediscache/new-azrediscache)

> [!IMPORTANT]
> Första gången du skapar en Azure Cache för Redis i en `Microsoft.Cache` prenumeration med Hjälp av Azure-portalen registrerar portalen namnområdet för den prenumerationen. Om du försöker skapa den första Azure-cachen för Redis i en prenumeration med PowerShell måste du först registrera namnområdet med följande kommando. i annat fall cmdlets som `New-AzRedisCache` och `Get-AzRedisCache` misslyckas.
> 
> `Register-AzResourceProvider -ProviderNamespace "Microsoft.Cache"`
> 
> 

Om du vill visa en lista `New-AzRedisCache`över tillgängliga parametrar och deras beskrivningar för kör du följande kommando.

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

Om du vill skapa en cache med standardparametrar kör du följande kommando.

    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US"

`ResourceGroupName`, `Name`och `Location` är obligatoriska parametrar, men resten är valfria och har standardvärden. Om du kör föregående kommando skapas en Standard SKU Azure-cache för Redis-instans med det angivna namnet, platsen och resursgruppen, som är 1 GB i storlek med icke-SSL-porten inaktiverad.

Om du vill skapa en premiumcache anger du en storlek på P1 (6 GB - 60 GB), P2 (13 GB - 130 GB), P3 (26 GB - 260 GB) eller P4 (53 GB - 530 GB). Om du vill aktivera klustring `ShardCount` anger du ett fragmentantal med parametern. I följande exempel skapas en P1-premiumcache med 3 shards. En P1 premium cache är 6 GB i storlek, och eftersom vi angav tre shards den totala storleken är 18 GB (3 x 6 GB).

    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P1 -ShardCount 3

Om du vill `RedisConfiguration` ange värden för parametern `{}` omsluter du `@{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}`värdena inuti som nyckel-/värdepar som . I följande exempel skapas en standard `allkeys-random` 1 GB-cache med maxmemory-princip och keyspace-meddelanden som konfigurerats med `KEA`. Mer information finns i [Keyspace-meddelanden (avancerade inställningar)](cache-configure.md#keyspace-notifications-advanced-settings) och [minnesprinciper](cache-configure.md#memory-policies).

    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}

<a name="databases"></a>

## <a name="to-configure-the-databases-setting-during-cache-creation"></a>Så här konfigurerar du databasinställningen när cacheminnet skapas
Inställningen `databases` kan bara konfigureras när cacheminnet skapas. I följande exempel skapas en premium P3-cache (26 GB) med 48 databaser med cmdleten [New-AzRedisCache.](https://docs.microsoft.com/powershell/module/az.rediscache/New-azRedisCache)

    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P3 -RedisConfiguration @{"databases" = "48"}

Mer information om `databases` egenskapen finns i [Standardkonfiguration för Azure-cache för Redis-server](cache-configure.md#default-redis-server-configuration). Mer information om hur du skapar en cache med cmdlet [New-AzRedisCache](https://docs.microsoft.com/powershell/module/az.rediscache/new-azrediscache) finns i föregående Avsnittet Skapa en Azure-cache för Redis.

## <a name="to-update-an-azure-cache-for-redis"></a>Så här uppdaterar du en Azure-cache för Redis
Azure Cache för Redis-instanser uppdateras med hjälp av [cmdlet set-azredisCache.](https://docs.microsoft.com/powershell/module/az.rediscache/Set-azRedisCache)

Om du vill visa en lista `Set-AzRedisCache`över tillgängliga parametrar och deras beskrivningar för kör du följande kommando.

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

`Set-AzRedisCache` Cmdleten kan användas för att `Size` `Sku`uppdatera `EnableNonSslPort`egenskaper `RedisConfiguration` som , , och värdena. 

Följande kommando uppdaterar maxmemory-principen för Azure Cache för Redis som heter myCache.

    Set-AzRedisCache -ResourceGroupName "myGroup" -Name "myCache" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random"}

<a name="scale"></a>

## <a name="to-scale-an-azure-cache-for-redis"></a>Så här skalar du en Azure-cache för Redis
`Set-AzRedisCache`kan användas för att skala en Azure-cache `Sku`för `ShardCount` Redis-instans när `Size`, eller egenskaperna ändras. 

> [!NOTE]
> Skalning av en cache med PowerShell omfattas av samma gränser och riktlinjer som skalning av en cache från Azure-portalen. Du kan skala till en annan prisnivå med följande begränsningar.
> 
> * Du kan inte skala från en högre prisnivå till en lägre prisnivå.
> * Du kan inte skala från en **Premium-cache** ned till en **Standard-** eller **Basic-cache.**
> * Du kan inte skala från en **standardcache** ned till en **Basic-cache.**
> * Du kan skala från en **Grundläggande** cache till en **standardcache,** men du kan inte ändra storleken samtidigt. Om du behöver en annan storlek kan du göra en efterföljande skalningsåtgärd till önskad storlek.
> * Du kan inte skala från en **Basic-cache** direkt till en **Premium-cache.** Du måste skala från **Basic** till **Standard** i en skalningsåtgärd och sedan från **Standard** till **Premium** i en efterföljande skalningsåtgärd.
> * Du kan inte skala från en större storlek ner till **C0 (250 MB)** storlek.
> 
> Mer information finns i Så här skalar du [Azure Cache för Redis](cache-how-to-scale.md).
> 
> 

I följande exempel visas hur `myCache` du skalar en cache med namnet till en 2,5 GB-cache. Observera att det här kommandot fungerar för både en Basic- eller en standardcache.

    Set-AzRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB

När det här kommandot har utfärdats returneras cachens status `Get-AzRedisCache`(liknande anrop ). Observera att `ProvisioningState` `Scaling`är .

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

När skalningsåtgärden är `ProvisioningState` klar `Succeeded`ändras till . Om du behöver göra en efterföljande skalningsåtgärd, till exempel ändra från Basic till Standard och sedan ändra storleken, måste du vänta tills den föregående åtgärden är klar eller om du får ett fel som liknar följande.

    Set-AzRedisCache : Conflict: The resource '...' is not in a stable state, and is currently unable to accept the update request.

## <a name="to-get-information-about-an-azure-cache-for-redis"></a>Så här hämtar du information om en Azure-cache för Redis
Du kan hämta information om en cache med hjälp av [Cmdlet Get-AzRedisCache.](https://docs.microsoft.com/powershell/module/az.rediscache/get-azrediscache)

Om du vill visa en lista `Get-AzRedisCache`över tillgängliga parametrar och deras beskrivningar för kör du följande kommando.

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

Om du vill returnera information om alla `Get-AzRedisCache` cacheminnen i den aktuella prenumerationen körs du utan några parametrar.

    Get-AzRedisCache

Om du vill returnera information om alla `Get-AzRedisCache` cacheminnen i en viss resursgrupp kör du med parametern. `ResourceGroupName`

    Get-AzRedisCache -ResourceGroupName myGroup

Om du vill returnera information `Get-AzRedisCache` om `Name` en viss cache kör du `ResourceGroupName` med parametern som innehåller namnet på cacheminnet och parametern med resursgruppen som innehåller cacheminnet.

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

## <a name="to-retrieve-the-access-keys-for-an-azure-cache-for-redis"></a>Så här hämtar du åtkomstnycklarna för en Azure-cache för Redis
Om du vill hämta åtkomstnycklarna för cacheminnet kan du använda cmdleten [Get-AzRedisCacheKey.](https://docs.microsoft.com/powershell/module/az.rediscache/Get-azRedisCacheKey)

Om du vill visa en lista `Get-AzRedisCacheKey`över tillgängliga parametrar och deras beskrivningar för kör du följande kommando.

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

Om du vill hämta nycklarna `Get-AzRedisCacheKey` till cacheminnet anropar du cmdleten och skickar namnet på cacheminnet namnet på resursgruppen som innehåller cacheminnet.

    PS C:\> Get-AzRedisCacheKey -Name myCache -ResourceGroupName myGroup

    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : ABhfB757JgjIgt785JgKH9865eifmekfnn649303JKL=

## <a name="to-regenerate-access-keys-for-your-azure-cache-for-redis"></a>Så här återskapar du åtkomstnycklar för din Azure-cache för Redis
Om du vill återskapa åtkomstnycklarna för cacheminnet kan du använda cmdleten [New-AzRedisCacheKey.](https://docs.microsoft.com/powershell/module/az.rediscache/New-azRedisCacheKey)

Om du vill visa en lista `New-AzRedisCacheKey`över tillgängliga parametrar och deras beskrivningar för kör du följande kommando.

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

Om du vill återskapa den primära eller `New-AzRedisCacheKey` sekundära nyckeln för cacheminnet anropar du `Primary` cmdleten och skickar i namnet, resursgruppen och anger antingen eller `Secondary` för parametern. `KeyType` I följande exempel återskapas den sekundära åtkomstnyckeln för en cache.

    PS C:\> New-AzRedisCacheKey -Name myCache -ResourceGroupName myGroup -KeyType Secondary

    Confirm
    Are you sure you want to regenerate Secondary key for Azure Cache for Redis 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y


    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : c53hj3kh4jhHjPJk8l0jji785JgKH9865eifmekfnn6=

## <a name="to-delete-an-azure-cache-for-redis"></a>Så här tar du bort en Azure-cache för Redis
Om du vill ta bort en Azure-cache för Redis använder du cmdleten [Remove-AzRedisCache.](https://docs.microsoft.com/powershell/module/az.rediscache/remove-azrediscache)

Om du vill visa en lista `Remove-AzRedisCache`över tillgängliga parametrar och deras beskrivningar för kör du följande kommando.

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

I följande exempel tas `myCache` cachen med namnet bort.

    PS C:\> Remove-AzRedisCache -Name myCache -ResourceGroupName myGroup

    Confirm
    Are you sure you want to remove Azure Cache for Redis 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y


## <a name="to-import-an-azure-cache-for-redis"></a>Så här importerar du en Azure-cache för Redis
Du kan importera data till en Azure-cache för Redis-instans med cmdlet. `Import-AzRedisCache`

> [!IMPORTANT]
> Import/export är endast tillgängligt för [cacheminnen på premiumnivå.](cache-premium-tier-intro.md) Mer information om Import/Export finns [i Importera och exportera data i Azure Cache för Redis](cache-how-to-import-export-data.md).
> 
> 

Om du vill visa en lista `Import-AzRedisCache`över tillgängliga parametrar och deras beskrivningar för kör du följande kommando.

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


Följande kommando importerar data från bloben som anges av SAS uri till Azure Cache för Redis.

    PS C:\>Import-AzRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Files @("https://mystorageaccount.blob.core.windows.net/mycontainername/blobname?sv=2015-04-05&sr=b&sig=caIwutG2uDa0NZ8mjdNJdgOY8%2F8mhwRuGNdICU%2B0pI4%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwd") -Force

## <a name="to-export-an-azure-cache-for-redis"></a>Så här exporterar du en Azure-cache för Redis
Du kan exportera data från en Azure-cache för Redis-instans med cmdlet. `Export-AzRedisCache`

> [!IMPORTANT]
> Import/export är endast tillgängligt för [cacheminnen på premiumnivå.](cache-premium-tier-intro.md) Mer information om Import/Export finns [i Importera och exportera data i Azure Cache för Redis](cache-how-to-import-export-data.md).
> 
> 

Om du vill visa en lista `Export-AzRedisCache`över tillgängliga parametrar och deras beskrivningar för kör du följande kommando.

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


Följande kommando exporterar data från en Azure Cache for Redis-instans till behållaren som anges av SAS uri.

        PS C:\>Export-AzRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Prefix "blobprefix"
        -Container "https://mystorageaccount.blob.core.windows.net/mycontainer?sv=2015-04-05&sr=c&sig=HezZtBZ3DURmEGDduauE7
        pvETY4kqlPI8JCNa8ATmaw%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwdl"

## <a name="to-reboot-an-azure-cache-for-redis"></a>Så här startar du om en Azure-cache för Redis
Du kan starta om Azure-cache `Reset-AzRedisCache` för Redis-instans med cmdlet.

> [!IMPORTANT]
> Omstart är endast tillgängligt för [cacheminnen på premiumnivå.](cache-premium-tier-intro.md) Mer information om hur du startar om cacheminnet finns i [Cacheadministration - starta om](cache-administration.md#reboot).
> 
> 

Om du vill visa en lista `Reset-AzRedisCache`över tillgängliga parametrar och deras beskrivningar för kör du följande kommando.

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


Följande kommando startar om båda noderna i den angivna cachen.

        PS C:\>Reset-AzRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -RebootType "AllNodes"
        -Force


## <a name="next-steps"></a>Nästa steg
Mer information om hur du använder Windows PowerShell med Azure finns i följande resurser:

* [Azure Cache för Redis cmdlet-dokumentation på MSDN](https://docs.microsoft.com/powershell/module/az.rediscache)
* [Cmdlets för Azure Resource Manager](https://go.microsoft.com/fwlink/?LinkID=394765): Lär dig att använda cmdlets i Azure Resource Manager-modulen.
* [Använda resursgrupper för att hantera dina Azure-resurser:](../azure-resource-manager/templates/deploy-portal.md)Lär dig hur du skapar och hanterar resursgrupper i Azure-portalen.
* [Azure-blogg](https://azure.microsoft.com/blog/): Läs mer om nya funktioner i Azure.
* [Windows PowerShell-blogg:](https://blogs.msdn.com/powershell)Läs mer om nya funktioner i Windows PowerShell.
* ["Hej, Scripting Guy!" Blogg](https://blogs.technet.com/b/heyscriptingguy/): Få verkliga tips och tricks från Windows PowerShell-communityn.

