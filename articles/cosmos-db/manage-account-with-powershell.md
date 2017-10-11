---
title: Azure Cosmos DB Automation - hantering med Powershell | Microsoft Docs
description: "Använd Azure Powershell hantera dina Azure DB som Cosmos-konton."
services: cosmos-db
author: dmakwana
manager: jhubbard
editor: 
tags: azure-resource-manager
documentationcenter: 
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/21/2017
ms.author: dimakwan
ms.openlocfilehash: 25c543528119410dff0684845a713dcb0d6151d6
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="create-an-azure-cosmos-db-account-using-powershell"></a>Skapa ett Azure DB som Cosmos-konto med hjälp av PowerShell

Enligt följande anvisningar beskriver kommandon för att automatisera hanteringen av dina Azure Cosmos DB databasen konton med hjälp av Azure Powershell. Den inkluderar också kommandon för att hantera nycklar och prioriteringar för växling vid fel i [flera regioner databasen konton][scaling-globally]. Uppdatera ditt konto kan du ändra konsekvenskontroll principer och Lägg till/ta bort regioner. Plattformsoberoende hantering av Azure DB som Cosmos-konto, kan använda antingen [Azure CLI](cli-samples.md), [Resource Provider REST API][rp-rest-api], eller [Azure-portalen ](create-documentdb-dotnet.md#create-account).

## <a name="getting-started"></a>Komma igång

Följ instruktionerna i [hur du installerar och konfigurerar du Azure PowerShell] [ powershell-install-configure] att installera och logga in på ditt Azure Resource Manager-konto i Powershell.

### <a name="notes"></a>Anteckningar

* Om du vill köra följande kommandon utan bekräftelse från användaren, Lägg till den `-Force` flaggan i kommandot.
* Följande kommandon är synkrona.

## <a id="create-documentdb-account-powershell"></a>Skapa ett Azure Cosmos DB-konto

Det här kommandot kan du skapa ett databaskonto Azure Cosmos DB. Konfigurera ditt nya konto som antingen en region eller [flera regioner] [ scaling-globally] med en viss [konsekvent princip](consistency-levels.md).

    $locations = @(@{"locationName"="<write-region-location>"; "failoverPriority"=0}, @{"locationName"="<read-region-location>"; "failoverPriority"=1})
    $iprangefilter = "<ip-range-filter>"
    $consistencyPolicy = @{"defaultConsistencyLevel"="<default-consistency-level>"; "maxIntervalInSeconds"="<max-interval>"; "maxStalenessPrefix"="<max-staleness-prefix>"}
    $CosmosDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    New-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName <resource-group-name>  -Location "<resource-group-location>" -Name <database-account-name> -Properties $CosmosDBProperties
    
* `<write-region-location>`Platsnamnet för databaskontot skrivåtgärder regionen. Den här platsen måste ha ett värde för växling vid fel prioritet 0. Det måste finnas exakt en skrivning region per konto.
* `<read-region-location>`Platsnamnet för den skrivskyddade regionen för databaskontot. Den här platsen måste ha ett värde för prioritet för växling vid fel som är större än 0. Det kan finnas mer än en skrivskyddad regioner per konto.
* `<ip-range-filter>`Anger uppsättningen IP-adresser och IP-adressintervall i CIDR-format ska ingå som listan över tillåtna klientens IP-adresser för en viss databas-konto. IP-adressintervall måste vara kommatecken avgränsade och får inte innehålla blanksteg. Mer information finns i [Azure Cosmos DB-brandväggsstöd](firewall-support.md)
* `<default-consistency-level>`Konsekvenskontroll standardnivå för Azure DB som Cosmos-konto. Mer information finns i [Konsekvensnivåer i Azure Cosmos DB](consistency-levels.md).
* `<max-interval>`När det används med begränsas föråldringskonsekvens representerar det här värdet tid mängden föråldrad (i sekunder) tolereras. Tillåtet område för det här värdet är 1-100.
* `<max-staleness-prefix>`När det används med begränsas föråldringskonsekvens representerar det här värdet antalet inaktuella begäranden tolereras. Tillåtet område för det här värdet är 1 – 2 147 483 647.
* `<resource-group-name>`Namnet på den [Azure-resursgrupp] [ azure-resource-groups] som det nya kontot i Azure Cosmos DB databasen tillhör.
* `<resource-group-location>`Platsen för Azure-resursgrupp som det nya kontot i Azure Cosmos DB databasen tillhör.
* `<database-account-name>`Namnet på kontot som ska skapas Azure Cosmos DB. Det kan endast använda gemena bokstäver, siffror, den '-' tecken och måste vara mellan 3 och 50 tecken.

Exempel: 

    $locations = @(@{"locationName"="West US"; "failoverPriority"=0}, @{"locationName"="East US"; "failoverPriority"=1})
    $iprangefilter = ""
    $consistencyPolicy = @{"defaultConsistencyLevel"="BoundedStaleness"; "maxIntervalInSeconds"=5; "maxStalenessPrefix"=100}
    $CosmosDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    New-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Location "West US" -Name "docdb-test" -Properties $CosmosDBProperties

### <a name="notes"></a>Anteckningar
* I föregående exempel skapas ett databaskonto med två områden. Det är också möjligt att skapa ett databaskonto med en region (som är utsedd till skrivåtgärder region och har ett prioritetsvärde för växling vid fel 0) eller fler än två regioner. Mer information finns i [flera regioner databasen konton][scaling-globally].
* Platser måste vara regioner som Azure Cosmos DB är allmänt tillgänglig. Den aktuella listan över regioner finns på den [Azure-regioner sidan](https://azure.microsoft.com/regions/#services).

## <a id="update-documentdb-account-powershell"></a>Uppdatera ett DocumentDB-databaskonto

Det här kommandot kan du uppdatera kontoegenskaperna Azure DB som Cosmos-databasen. Detta inkluderar konsekvenskontroll principen och de platser som kontot som finns i.

> [!NOTE]
> Det här kommandot kan du lägga till och ta bort regioner, men tillåter inte att ändra prioriteringarna för växling vid fel. Om du vill ändra prioriteringarna för växling vid fel finns [under](#modify-failover-priority-powershell).

    $locations = @(@{"locationName"="<write-region-location>"; "failoverPriority"=0}, @{"locationName"="<read-region-location>"; "failoverPriority"=1})
    $iprangefilter = "<ip-range-filter>"
    $consistencyPolicy = @{"defaultConsistencyLevel"="<default-consistency-level>"; "maxIntervalInSeconds"="<max-interval>"; "maxStalenessPrefix"="<max-staleness-prefix>"}
    $CosmosDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    Set-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName <resource-group-name> -Name <database-account-name> -Properties $CosmosDBProperties
    
* `<write-region-location>`Platsnamnet för databaskontot skrivåtgärder regionen. Den här platsen måste ha ett värde för växling vid fel prioritet 0. Det måste finnas exakt en skrivning region per konto.
* `<read-region-location>`Platsnamnet för den skrivskyddade regionen för databaskontot. Den här platsen måste ha ett värde för prioritet för växling vid fel som är större än 0. Det kan finnas mer än en skrivskyddad regioner per konto.
* `<default-consistency-level>`Konsekvenskontroll standardnivå för Azure DB som Cosmos-konto. Mer information finns i [Konsekvensnivåer i Azure Cosmos DB](consistency-levels.md).
* `<ip-range-filter>`Anger uppsättningen IP-adresser och IP-adressintervall i CIDR-format ska ingå som listan över tillåtna klientens IP-adresser för en viss databas-konto. IP-adressintervall måste vara kommatecken avgränsade och får inte innehålla blanksteg. Mer information finns i [Azure Cosmos DB-brandväggsstöd](firewall-support.md)
* `<max-interval>`När det används med begränsas föråldringskonsekvens representerar det här värdet tid mängden föråldrad (i sekunder) tolereras. Tillåtet område för det här värdet är 1-100.
* `<max-staleness-prefix>`När det används med begränsas föråldringskonsekvens representerar det här värdet antalet inaktuella begäranden tolereras. Tillåtet område för det här värdet är 1 – 2 147 483 647.
* `<resource-group-name>`Namnet på den [Azure-resursgrupp] [ azure-resource-groups] som det nya kontot i Azure Cosmos DB databasen tillhör.
* `<resource-group-location>`Platsen för Azure-resursgrupp som det nya kontot i Azure Cosmos DB databasen tillhör.
* `<database-account-name>`Namnet på kontot som ska uppdateras Azure Cosmos DB.

Exempel: 

    $locations = @(@{"locationName"="West US"; "failoverPriority"=0}, @{"locationName"="East US"; "failoverPriority"=1})
    $iprangefilter = ""
    $consistencyPolicy = @{"defaultConsistencyLevel"="BoundedStaleness"; "maxIntervalInSeconds"=5; "maxStalenessPrefix"=100}
    $CosmosDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    Set-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test" -Properties $CosmosDBProperties

## <a id="delete-documentdb-account-powershell"></a>Ta bort en DocumentDB-databaskonto

Det här kommandot kan du ta bort ett befintligt konto i Azure DB som Cosmos-databasen.

    Remove-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"
    
* `<resource-group-name>`Namnet på den [Azure-resursgrupp] [ azure-resource-groups] som det nya kontot i Azure Cosmos DB databasen tillhör.
* `<database-account-name>`Namnet på det Azure Cosmos DB kontot ska tas bort.

Exempel:

    Remove-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a id="get-documentdb-properties-powershell"></a>Hämta egenskaper för ett DocumentDB-databaskonto

Det här kommandot kan du hämta egenskaperna för ett befintligt konto i Azure DB som Cosmos-databasen.

    Get-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"

* `<resource-group-name>`Namnet på den [Azure-resursgrupp] [ azure-resource-groups] som det nya kontot i Azure Cosmos DB databasen tillhör.
* `<database-account-name>`Namnet på Azure Cosmos DB databaskontot.

Exempel:

    Get-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a id="update-tags-powershell"></a>Uppdatera taggar för ett konto för Azure Cosmos DB-databas

I följande exempel beskrivs hur du ställer in [Azure resurstaggar] [ azure-resource-tags] Azure Cosmos-DB databasen i kontot.

> [!NOTE]
> Det här kommandot kan kombineras med kommandon för att skapa eller uppdatera genom att lägga till den `-Tags` flaggan med motsvarande parameter.

Exempel:

    $tags = @{"dept" = "Finance”; environment = “Production”}
    Set-AzureRmResource -ResourceType “Microsoft.DocumentDB/databaseAccounts”  -ResourceGroupName "rg-test" -Name "docdb-test" -Tags $tags

## <a id="list-account-keys-powershell"></a>Lista nycklar

När du skapar ett konto i Azure Cosmos DB genererar tjänsten två master åtkomstnycklar som kan användas för autentisering vid åtkomst av Azure DB som Cosmos-konto. Genom att tillhandahålla två åtkomstnycklar kan Azure Cosmos DB du återskapa nycklarna utan avbrott i Azure DB som Cosmos-konto. Det finns också skrivskyddade nycklar för att autentisera skrivskyddade åtgärder. Det finns två skrivskyddad (primär eller sekundär) och två skrivskyddade nycklar (primär eller sekundär).

    $keys = Invoke-AzureRmResourceAction -Action listKeys -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"

* `<resource-group-name>`Namnet på den [Azure-resursgrupp] [ azure-resource-groups] som det nya kontot i Azure Cosmos DB databasen tillhör.
* `<database-account-name>`Namnet på Azure Cosmos DB databaskontot.

Exempel:

    $keys = Invoke-AzureRmResourceAction -Action listKeys -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a id="list-connection-strings-powershell"></a>Lista över anslutningssträngar

För MongoDB-konton, kan du hämta anslutningssträngen till Anslut MongoDB-appen till databaskontot med följande kommando.

    $keys = Invoke-AzureRmResourceAction -Action listConnectionStrings -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"

* `<resource-group-name>`Namnet på den [Azure-resursgrupp] [ azure-resource-groups] som det nya kontot i Azure Cosmos DB databasen tillhör.
* `<database-account-name>`Namnet på Azure Cosmos DB databaskontot.

Exempel:

    $keys = Invoke-AzureRmResourceAction -Action listConnectionStrings -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a id="regenerate-account-key-powershell"></a>Återskapa Kontonyckel

Du bör ändra åtkomstnycklarna till ditt Azure DB som Cosmos-konto med jämna mellanrum för att skydda dina anslutningar. Två åtkomstnycklar tilldelas så att du kan upprätthålla anslutningar till Azure Cosmos DB kontot den ena åtkomstnyckeln medan du återskapar den andra åtkomstnyckeln.

    Invoke-AzureRmResourceAction -Action regenerateKey -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>" -Parameters @{"keyKind"="<key-kind>"}

* `<resource-group-name>`Namnet på den [Azure-resursgrupp] [ azure-resource-groups] som det nya kontot i Azure Cosmos DB databasen tillhör.
* `<database-account-name>`Namnet på Azure Cosmos DB databaskontot.
* `<key-kind>`En av de fyra typerna av nycklar: [”primär” | ” Sekundär ”|” PrimaryReadonly ”|” SecondaryReadonly ”] som du vill återskapa.

Exempel:

    Invoke-AzureRmResourceAction -Action regenerateKey -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test" -Parameters @{"keyKind"="Primary"}

## <a id="modify-failover-priority-powershell"></a>Ändra redundans prioriteten för en Azure Cosmos DB-databaskonto

Du kan ändra prioriteten för växling vid fel för olika regioner som Azure Cosmos DB databaskontot finns i för flera regioner databasen konton. Mer information om växling vid fel i din Azure Cosmos DB databaskonto finns [distribuera data globalt med Azure Cosmos DB][distribute-data-globally].

    $failoverPolicies = @(@{"locationName"="<write-region-location>"; "failoverPriority"=0},@{"locationName"="<read-region-location>"; "failoverPriority"=1})
    Invoke-AzureRmResourceAction -Action failoverPriorityChange -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>" -Parameters @{"failoverPolicies"=$failoverPolicies}

* `<write-region-location>`Platsnamnet för databaskontot skrivåtgärder regionen. Den här platsen måste ha ett värde för växling vid fel prioritet 0. Det måste finnas exakt en skrivning region per konto.
* `<read-region-location>`Platsnamnet för den skrivskyddade regionen för databaskontot. Den här platsen måste ha ett värde för prioritet för växling vid fel som är större än 0. Det kan finnas mer än en skrivskyddad regioner per konto.
* `<resource-group-name>`Namnet på den [Azure-resursgrupp] [ azure-resource-groups] som det nya kontot i Azure Cosmos DB databasen tillhör.
* `<database-account-name>`Namnet på Azure Cosmos DB databaskontot.

Exempel:

    $failoverPolicies = @(@{"locationName"="East US"; "failoverPriority"=0},@{"locationName"="West US"; "failoverPriority"=1})
    Invoke-AzureRmResourceAction -Action failoverPriorityChange -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test" -Parameters @{"failoverPolicies"=$failoverPolicies}

## <a name="next-steps"></a>Nästa steg

* Om du vill ansluta med hjälp av .NET finns [ansluter och frågar med .NET](create-documentdb-dotnet.md).
* Om du vill ansluta med .NET Core, se [Anslut och fråga med .NET Core](create-documentdb-dotnet-core.md).
* Om du vill ansluta med Node.js, se [ansluter och frågar med Node.js och en MongoDB-app](create-mongodb-nodejs.md).

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[powershell-install-configure]: https://docs.microsoft.com/azure/powershell-install-configure
[scaling-globally]: distribute-data-globally.md#EnableGlobalDistribution
[distribute-data-globally]: distribute-data-globally.md
[azure-resource-groups]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups
[azure-resource-tags]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags
[rp-rest-api]: https://docs.microsoft.com/rest/api/documentdbresourceprovider/
