---
title: Azure Cosmos DB Automation - hantering med Powershell | Microsoft Docs
description: Använd Azure Powershell hantera Azure Cosmos DB-konton.
services: cosmos-db
author: SnehaGunda
manager: kfile
editor: ''
tags: azure-resource-manager
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 04/21/2017
ms.author: sngun
ms.openlocfilehash: 82ab30ebab1b69d5ae636702b3b56d3792c09010
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2018
ms.locfileid: "47394581"
---
# <a name="create-an-azure-cosmos-db-account-using-powershell"></a>Skapa ett Azure Cosmos DB-konto med hjälp av PowerShell

Enligt följande anvisningar beskriver kommandon för att automatisera hanteringen av din Azure Cosmos DB-databaskonton med Azure Powershell. Den innehåller också kommandon för att hantera nycklar och redundansprioriteringar i [databaskonton] [distribuera-data-globally.md]. Uppdaterar ditt databaskonto kan du ändra principer för konsekvens och Lägg till/ta bort regioner. Plattformsoberoende hantering av ditt Azure Cosmos DB-konto, kan du använda antingen [Azure CLI](cli-samples.md), [Resource Provider REST API][rp-rest-api], eller [Azure-portalen ](create-sql-api-dotnet.md#create-account).

## <a name="getting-started"></a>Komma igång

Följ instruktionerna i [hur du installerar och konfigurerar du Azure PowerShell] [ powershell-install-configure] att installera och logga in på ditt Azure Resource Manager-konto i Powershell.

### <a name="notes"></a>Anteckningar

* Om du vill köra följande kommandon utan att användaren ska bekräfta, Lägg till den `-Force` flaggan till kommandot.
* Följande kommandon är synkrona.

## <a id="create-documentdb-account-powershell"></a> Skapa ett Azure Cosmos DB-konto

Det här kommandot kan du skapa ett Azure Cosmos DB-databaskonto. Konfigurera ditt nya databaskonto som en region eller [flera regioner] [distribuera-data-globally.md] med en viss [konsekvens princip](consistency-levels.md).

    $locations = @(@{"locationName"="<write-region-location>"; "failoverPriority"=0}, @{"locationName"="<read-region-location>"; "failoverPriority"=1})
    $iprangefilter = "<ip-range-filter>"
    $consistencyPolicy = @{"defaultConsistencyLevel"="<default-consistency-level>"; "maxIntervalInSeconds"="<max-interval>"; "maxStalenessPrefix"="<max-staleness-prefix>"}
    $CosmosDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    New-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName <resource-group-name>  -Location "<resource-group-location>" -Name <database-account-name> -Properties $CosmosDBProperties
    
* `<write-region-location>` Platsnamnet för skrivningsregionen av databaskontot. Den här platsen måste ha ett prioritetsvärde för växling vid fel på 0. Det måste finnas exakt en skrivregion per konto.
* `<read-region-location>` Platsnamnet för läsregionen för databaskontot. Den här platsen måste ha ett prioritetsvärde för växling vid fel är större än 0. Det kan finnas flera läsregioner per konto.
* `<ip-range-filter>` Anger uppsättningen IP-adresser eller IP-adressintervall i CIDR-form som ska tas med som listan över tillåtna för klienten IP-adresser för ett visst databaskonto. IP-adresser/intervall måste vara kommatecken avgränsade och får inte innehålla några blanksteg. Mer information finns i [Azure Cosmos DB-brandväggsstöd](firewall-support.md)
* `<default-consistency-level>` Standard-konsekvensnivå på Azure Cosmos DB-kontot. Mer information finns i [Konsekvensnivåer i Azure Cosmos DB](consistency-levels.md).
* `<max-interval>` När det används med begränsad föråldring, konsekvens, representerar det här värdet det tid föråldring (i sekunder) ska tolereras. Godkända intervallet för det här värdet är 1-100.
* `<max-staleness-prefix>` När det används med begränsad föråldring, konsekvens, representerar det här värdet antalet inaktuella begäranden som ska tolereras. Godkända intervallet för det här värdet är 1 – 2 147 483 647.
* `<resource-group-name>` Namnet på den [Azure-resursgrupp] [ azure-resource-groups] som det nya Azure Cosmos DB-databaskontot som hör till.
* `<resource-group-location>` Platsen för Azure-resursgrupp som det nya Azure Cosmos DB-databaskontot som hör till.
* `<database-account-name>` Namnet på Azure Cosmos DB-databaskontot som ska skapas. Det kan endast använda gemena bokstäver, siffror, den '-' bindestreck och måste vara mellan 3 och 50 tecken.

Exempel: 

    $locations = @(@{"locationName"="West US"; "failoverPriority"=0}, @{"locationName"="East US"; "failoverPriority"=1})
    $iprangefilter = ""
    $consistencyPolicy = @{"defaultConsistencyLevel"="BoundedStaleness"; "maxIntervalInSeconds"=5; "maxStalenessPrefix"=100}
    $CosmosDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    New-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Location "West US" -Name "docdb-test" -Properties $CosmosDBProperties

### <a name="notes"></a>Anteckningar
* I föregående exempel skapas ett databaskonto med två regioner. Du kan också skapa ett databaskonto med en region (som är utsedd till skrivbar och ha ett prioritetsvärde för växling vid fel 0) eller fler än två regioner. Mer information finns i [databaskonton] [distribuera-data-globally.md].
* Platserna måste vara regioner som Azure Cosmos DB är allmänt tillgänglig. Den aktuella listan över regioner finns på den [Azure-regionerna sidan](https://azure.microsoft.com/regions/#services).

## <a id="update-documentdb-account-powershell"></a> Uppdatera ett Azure Cosmos DB-databaskonto

Det här kommandot kan du uppdatera egenskaperna för databasen ditt Azure Cosmos DB. Detta inkluderar konsekvens principen och de platser som kontot som finns i.

> [!NOTE]
> Det här kommandot kan du lägga till och ta bort regioner, men tillåter inte att ändra prioriteter för redundans. Ändring av redundansprioriteringar finns i [nedan](#modify-failover-priority-powershell).

    $locations = @(@{"locationName"="<write-region-location>"; "failoverPriority"=0}, @{"locationName"="<read-region-location>"; "failoverPriority"=1})
    $iprangefilter = "<ip-range-filter>"
    $consistencyPolicy = @{"defaultConsistencyLevel"="<default-consistency-level>"; "maxIntervalInSeconds"="<max-interval>"; "maxStalenessPrefix"="<max-staleness-prefix>"}
    $CosmosDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    Set-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName <resource-group-name> -Name <database-account-name> -Properties $CosmosDBProperties
    
* `<write-region-location>` Platsnamnet för skrivningsregionen av databaskontot. Den här platsen måste ha ett prioritetsvärde för växling vid fel på 0. Det måste finnas exakt en skrivregion per konto.
* `<read-region-location>` Platsnamnet för läsregionen för databaskontot. Den här platsen måste ha ett prioritetsvärde för växling vid fel är större än 0. Det kan finnas flera läsregioner per konto.
* `<default-consistency-level>` Standard-konsekvensnivå på Azure Cosmos DB-kontot. Mer information finns i [Konsekvensnivåer i Azure Cosmos DB](consistency-levels.md).
* `<ip-range-filter>` Anger uppsättningen IP-adresser eller IP-adressintervall i CIDR-form som ska tas med som listan över tillåtna för klienten IP-adresser för ett visst databaskonto. IP-adresser/intervall måste vara kommatecken avgränsade och får inte innehålla några blanksteg. Mer information finns i [Azure Cosmos DB-brandväggsstöd](firewall-support.md)
* `<max-interval>` När det används med begränsad föråldring, konsekvens, representerar det här värdet det tid föråldring (i sekunder) ska tolereras. Godkända intervallet för det här värdet är 1-100.
* `<max-staleness-prefix>` När det används med begränsad föråldring, konsekvens, representerar det här värdet antalet inaktuella begäranden som ska tolereras. Godkända intervallet för det här värdet är 1 – 2 147 483 647.
* `<resource-group-name>` Namnet på den [Azure-resursgrupp] [ azure-resource-groups] som det nya Azure Cosmos DB-databaskontot som hör till.
* `<resource-group-location>` Platsen för Azure-resursgrupp som det nya Azure Cosmos DB-databaskontot som hör till.
* `<database-account-name>` Namnet på Azure Cosmos DB-databaskontot som ska uppdateras.

Exempel: 

    $locations = @(@{"locationName"="West US"; "failoverPriority"=0}, @{"locationName"="East US"; "failoverPriority"=1})
    $iprangefilter = ""
    $consistencyPolicy = @{"defaultConsistencyLevel"="BoundedStaleness"; "maxIntervalInSeconds"=5; "maxStalenessPrefix"=100}
    $CosmosDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    Set-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test" -Properties $CosmosDBProperties

## <a id="delete-documentdb-account-powershell"></a> Ta bort ett Azure Cosmos DB-databaskonto

Det här kommandot kan du ta bort en befintlig Azure Cosmos DB-databaskonto.

    Remove-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"
    
* `<resource-group-name>` Namnet på den [Azure-resursgrupp] [ azure-resource-groups] som det nya Azure Cosmos DB-databaskontot som hör till.
* `<database-account-name>` Namnet på Azure Cosmos DB-databaskontot som ska tas bort.

Exempel:

    Remove-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a id="get-documentdb-properties-powershell"></a> Hämta egenskaper för ett Azure Cosmos DB-databaskonto

Det här kommandot kan du hämta egenskaperna för ett befintligt Azure Cosmos DB-databaskonto.

    Get-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"

* `<resource-group-name>` Namnet på den [Azure-resursgrupp] [ azure-resource-groups] som det nya Azure Cosmos DB-databaskontot som hör till.
* `<database-account-name>` Namnet på Azure Cosmos DB-databaskonto.

Exempel:

    Get-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a id="update-tags-powershell"></a> Uppdatera taggar för en Azure Cosmos DB-databaskontot

I följande exempel beskrivs hur du ställer in [Azure-resurstaggar] [ azure-resource-tags] för Azure Cosmos DB-databaskonto.

> [!NOTE]
> Det här kommandot kan kombineras med kommandon för att skapa eller uppdatera genom att lägga till den `-Tags` flagga motsvarande parameter.

Exempel:

    $tags = @{"dept" = "Finance"; environment = "Production"}
    Set-AzureRmResource -ResourceType "Microsoft.DocumentDB/databaseAccounts"  -ResourceGroupName "rg-test" -Name "docdb-test" -Tags $tags

## <a id="list-account-keys-powershell"></a> Lista nycklar

När du skapar ett Azure Cosmos DB-konto, genererar två master åtkomstnycklar som kan användas för autentisering när Azure Cosmos DB-kontot används i tjänsten. Genom att tillhandahålla två åtkomstnycklar för kan Azure Cosmos DB du återskapa nycklarna utan avbrott på ditt Azure Cosmos DB-konto. Det finns också skrivskyddade nycklar för att autentisera skrivskyddade åtgärder. Det finns två skrivskyddade nycklar (primära och sekundära) och två skrivskyddade nycklar (primär eller sekundär).

    $keys = Invoke-AzureRmResourceAction -Action listKeys -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"

* `<resource-group-name>` Namnet på den [Azure-resursgrupp] [ azure-resource-groups] som det nya Azure Cosmos DB-databaskontot som hör till.
* `<database-account-name>` Namnet på Azure Cosmos DB-databaskonto.

Exempel:

    $keys = Invoke-AzureRmResourceAction -Action listKeys -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a id="list-connection-strings-powershell"></a> Lista anslutningssträngar

För MongoDB-konton, kan anslutningssträngen för att ansluta din MongoDB-app till databaskontot hämtas med hjälp av följande kommando.

    $keys = Invoke-AzureRmResourceAction -Action listConnectionStrings -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"

* `<resource-group-name>` Namnet på den [Azure-resursgrupp] [ azure-resource-groups] som det nya Azure Cosmos DB-databaskontot som hör till.
* `<database-account-name>` Namnet på Azure Cosmos DB-databaskonto.

Exempel:

    $keys = Invoke-AzureRmResourceAction -Action listConnectionStrings -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a id="regenerate-account-key-powershell"></a> Återskapa Kontonyckeln

Du bör ändra åtkomstnycklar till ditt Azure Cosmos DB-konto med jämna mellanrum för att skydda dina anslutningar. Två åtkomstnycklar tilldelas så att du kan upprätthålla anslutningar till Azure Cosmos DB-kontot som den ena åtkomstnyckeln medan du återskapar den andra åtkomstnyckeln.

    Invoke-AzureRmResourceAction -Action regenerateKey -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>" -Parameters @{"keyKind"="<key-kind>"}

* `<resource-group-name>` Namnet på den [Azure-resursgrupp] [ azure-resource-groups] som det nya Azure Cosmos DB-databaskontot som hör till.
* `<database-account-name>` Namnet på Azure Cosmos DB-databaskonto.
* `<key-kind>` En av de fyra typerna av nycklar: [”primär” | ” Sekundär ”|” PrimaryReadonly ”|” SecondaryReadonly ”] som du vill återskapa.

Exempel:

    Invoke-AzureRmResourceAction -Action regenerateKey -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test" -Parameters @{"keyKind"="Primary"}

## <a id="modify-failover-priority-powershell"></a> Ändra Redundansprioritet för ett Azure Cosmos DB-databaskontot

För databaskonton, kan du ändra redundansprioritet för olika regioner som Azure Cosmos DB-databaskontot finns i. Läs mer på redundans i ditt Azure Cosmos DB-databaskonto [distribuera data globalt med Azure Cosmos DB][distribute-data-globally].

    $failoverPolicies = @(@{"locationName"="<write-region-location>"; "failoverPriority"=0},@{"locationName"="<read-region-location>"; "failoverPriority"=1})
    Invoke-AzureRmResourceAction -Action failoverPriorityChange -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>" -Parameters @{"failoverPolicies"=$failoverPolicies}

* `<write-region-location>` Platsnamnet för skrivningsregionen av databaskontot. Den här platsen måste ha ett prioritetsvärde för växling vid fel på 0. Det måste finnas exakt en skrivregion per konto.
* `<read-region-location>` Platsnamnet för läsregionen för databaskontot. Den här platsen måste ha ett prioritetsvärde för växling vid fel är större än 0. Det kan finnas flera läsregioner per konto.
* `<resource-group-name>` Namnet på den [Azure-resursgrupp] [ azure-resource-groups] som det nya Azure Cosmos DB-databaskontot som hör till.
* `<database-account-name>` Namnet på Azure Cosmos DB-databaskonto.

Exempel:

    $failoverPolicies = @(@{"locationName"="East US"; "failoverPriority"=0},@{"locationName"="West US"; "failoverPriority"=1})
    Invoke-AzureRmResourceAction -Action failoverPriorityChange -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test" -Parameters @{"failoverPolicies"=$failoverPolicies}

## <a name="next-steps"></a>Nästa steg

* Om du vill ansluta med hjälp av .NET, se [ansluta och fråga med .NET](create-sql-api-dotnet.md).
* Om du vill ansluta med hjälp av Node.js, se [ansluta och fråga med Node.js och MongoDB-app](create-mongodb-nodejs.md).

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[powershell-install-configure]: https://docs.microsoft.com/azure/powershell-install-configure
[scaling-globally]: distribute-data-globally.md#EnableGlobalDistribution
[distribute-data-globally]: distribute-data-globally.md
[azure-resource-groups]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups
[azure-resource-tags]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags
[rp-rest-api]: https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/
