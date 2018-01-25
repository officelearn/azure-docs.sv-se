---
title: "Använda Azure PowerShell med Azure Storage | Microsoft Docs"
description: "Lär dig hur du använder Azure PowerShell-cmdlets för Azure Storage."
services: storage
documentationcenter: na
author: robinsh
manager: timlt
ms.assetid: f4704f58-abc6-4f89-8b6d-1b1659746f5a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/02/2017
ms.author: robinsh
ms.openlocfilehash: f94febfa1610795cd46b4315bbbbe56aa2bca861
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2018
---
# <a name="using-azure-powershell-with-azure-storage"></a>Använda Azure PowerShell med Azure Storage

Azure PowerShell används för att skapa och hantera Azure-resurser från PowerShell-Kommandotolken eller i skript. För Azure Storage dessa cmdlets är indelade i två kategorier--kontrollplan och dataplan. Kontrollen plan cmdlets används för att hantera storage-konto – för att skapa storage-konton, ange egenskaper, ta bort storage-konton, rotera snabbtangenterna och så vidare. Data plan cmdlets som används för att hantera de data som lagras *i* storage-konto. Till exempel överföra blobbar, skapa filresurser och lägga till meddelanden till en kö.

Den här artikeln beskrivs vanliga åtgärder som använder den plan cmdlet: ar för att hantera storage-konton. Lär dig att: 

> [!div class="checklist"]
> * Lista storage-konton
> * Hämta en referens till ett befintligt lagringskonto
> * skapar ett lagringskonto 
> * Ange egenskaper för lagring
> * Hämta och återskapa åtkomstnycklarna
> * Skydda åtkomst till ditt lagringskonto 
> * Aktivera Storage Analytics

Den här artikeln innehåller länkar till flera PowerShell artiklar för lagring, till exempel hur du aktiverar och komma åt Storage Analytics, hur du använder data plan cmdlets och hur du kommer åt Azure oberoende moln som Kina molnet, tyska molnet och myndigheter Moln.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

Den här övningen kräver Azure PowerShell Modulversion 4.4 eller senare. Kör `Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). 

I den här övningen du skriver kommandona i ett vanligt PowerShell-fönster eller använda den [Windows PowerShell Integrated Scripting Environment (ISE)](/powershell/scripting/getting-started/fundamental/windows-powershell-integrated-scripting-environment--ise-) och Skriv kommandon i en textredigerare och sedan testa ett eller flera kommandon samtidigt som du gå igenom exemplen. Du kan markera de rader som du vill köra och klicka Kör valda att de bara köra dessa kommandon.

Mer information om lagringskonton finns [introduktion till Storage](storage-introduction.md) och [om Azure storage-konton](storage-create-storage-account.md).

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure-prenumerationen med kommandot `Login-AzureRmAccount` och följ anvisningarna på skärmen.

```powershell
Login-AzureRmAccount
```

## <a name="list-the-storage-accounts-in-the-subscription"></a>Visa en lista med lagringskonton i prenumerationen

Kör den [Get-AzureRMStorageAccount](/powershell/module/azurerm.resources/get-azurermstorageaccount) för att hämta listan över storage-konton i den aktuella prenumerationen. 

```powershell
Get-AzureRMStorageAccount | Select StorageAccountName, Location
```

## <a name="get-a-reference-to-a-storage-account"></a>Hämta en referens till ett lagringskonto

Sedan måste en referens till ett lagringskonto. Du kan skapa ett nytt lagringskonto, eller så kan du hämta en referens till ett befintligt lagringskonto. I följande avsnitt beskrivs båda metoderna. 

### <a name="use-an-existing-storage-account"></a>Använd ett befintligt lagringskonto 

Om du vill hämta ett befintligt lagringskonto, måste namnet på resursgruppen och namnet på lagringskontot. Ange variablerna för dessa två fält och sedan använda den [Get-AzureRmStorageAccount](/powershell/module/azurerm.storage/Get-AzureRmStorageAccount) cmdlet. 

```powershell
$resourceGroup = "myexistingresourcegroup"
$storageAccountName = "myexistingstorageaccount"

$storageAccount = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName 
```

Nu har du $storageAccount som pekar på ett befintligt lagringskonto.

### <a name="create-a-storage-account"></a>skapar ett lagringskonto 

Följande skript visar hur du skapar ett allmänt lagringskonto konto med [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount). När du har skapat kontot hämta dess kontext, som kan användas i efterföljande kommandon i stället för att ange autentiseringen med varje anrop.

```powershell
# Get list of locations and select one.
Get-AzureRmLocation | select Location 
$location = "eastus"

# Create a new resource group.
$resourceGroup = "teststoragerg"
New-AzureRmResourceGroup -Name $resourceGroup -Location $location 

# Set the name of the storage account and the SKU name. 
$storageAccountName = "testpshstorage"
$skuName = "Standard_LRS"
    
# Create the storage account.
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName $skuName

# Retrieve the context. 
$ctx = $storageAccount.Context
```

Skriptet använder följande PowerShell-cmdlets: 

*   [Get-AzureRmLocation](/powershell/module/azurerm.storage/Get-AzureRmLocation) --hämtar en lista över giltiga platser. I exemplet används `eastus` för platsen.

*   [Nya AzureRmResourceGroup](/powershell/module/azurerm.resources/New-AzureRmResourceGroup) – skapar en ny resursgrupp. En resursgrupp är en logisk behållare som Azure-resurser distribueras och hanteras. Vår kallas `teststoragerg`. 

*   [Nya AzureRmStorageAccount](/powershell/module/azurerm.resources/New-AzureRmStorageAcccount) --skapar faktiska storage-konto. I exemplet används `testpshstorage`.

SKU-namnet anger vilken typ av replikering för storage-konto, till exempel LRS (lokalt Redundant lagring). Mer information om replikering finns [Azure Storage-replikering](storage-redundancy.md).

> [!IMPORTANT]
> Namnet på ditt lagringskonto måste vara unikt i Azure och måste vara gemener. Namnkonventioner och begränsningar finns i [namnge och referera till behållare, Blobbar och Metadata](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata).
> 

Nu har du ett nytt lagringskonto och en referens till den. 

## <a name="manage-the-storage-account"></a>Hantera storage-konto

Nu när du har en referens till ett nytt lagringskonto eller ett befintligt lagringskonto beskrivs i följande avsnitt några av de kommandon som du kan använda för att hantera ditt lagringskonto.

### <a name="storage-account-properties"></a>Lagringskontoegenskaperna

Om du vill ändra inställningarna för ett lagringskonto [Set-AzureRmStorageAccount](/powershell/module/azurerm.resources/Set-AzureRmStorageAccount). När du inte kan ändra platsen för ett lagringskonto eller resursgruppen där den finns, kan du ändra många av de andra egenskaperna. Nedan visas några av de egenskaper som du kan ändra med hjälp av PowerShell.

* Den **domänen** tilldelats storage-konto.

* Den **taggar** tilldelats storage-konto. Taggar används ofta för att kategorisera resurser för fakturering.

* Den **SKU** är replikeringsinställningen för lagringskontot, till exempel LRS för lokalt Redundant lagring. Du kan till exempel ändra från standarden\_LRS till Standard\_GRS- eller Standard\_RAGRS. Observera att du inte kan ändra Standard\_ZRS- eller Premium\_LRS till andra SKU: er, eller ändra andra SKU: er till dessa.

* Den **åtkomstnivå** för Blob storage-konton. Värdet för åtkomstnivå är inställt **varm** eller **kall**, och du kan minska dina kostnader genom att välja den åtkomstnivå som överensstämmer med hur du använder lagringskontot. Mer information finns i [Hot, svalna, och arkivera lagringsnivåer](../blobs/storage-blob-storage-tiers.md).

* Storage kryptering tjänstinställningen för blob-lagring och/eller fillagring. Läs mer om SSE [Lagringstjänstens kryptering](storage-service-encryption.md).

* Tillåt endast HTTPS-trafik. 

### <a name="manage-the-access-keys"></a>Hantera åtkomstnycklarna

Ett Azure Storage-konto innehåller två nycklar. Använd för att hämta nycklarna [Get-AzureRmStorageAccountKey](/powershell/module/AzureRM.Storage/Get-AzureRmStorageAccountKey). Det här exemplet hämtar den första nyckeln. Använd för att hämta en `Value[1]` i stället för `Value[0]`.

```powershell
$storageAccountKey = `
    (Get-AzureRmStorageAccountKey `
    -ResourceGroupName $resourceGroup `
    -Name $storageAccountName).Value[0]
```

Om du vill återskapa nyckeln använda [ny AzureRmStorageAccountKey](/powershell/module/AzureRM.Storage/New-AzureRmStorageAccountKey). 

```powershell
New-AzureRmStorageAccountKey -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -KeyName key1 
```

Om du vill återskapa den andra nyckeln använda `key2` nyckelnamn i stället för `key1`.

Återskapa en av dina nycklar och hämta det igen för att se det nya värdet.

> [!NOTE] 
> Du bör utföra noggrann planering innan du återskapar nyckeln för ett lagringskonto för produktion. Återskapande av en eller båda nycklarna kommer att upphäva åtkomst för alla program som använder den nyckel som har skapats. Mer information finns [återskapa åtkomstnycklar för lagring](storage-create-storage-account.md#regenerate-storage-access-keys).


### <a name="delete-a-storage-account"></a>Ta bort ett lagringskonto 

Om du vill ta bort ett lagringskonto, Använd [Remove-AzureRmStorageAccount](/powershell/module/azurerm.storage/Remove-AzureRmStorageAccount).

```powershell
Remove-AzureRmStorageAccount -ResourceGroup $resourceGroup -AccountName $storageAccountName
```

> [!IMPORTANT]
> När du tar bort ett lagringskonto tas alla tillgångar i kontot bort. Om du tar bort ett konto råkar Ring supporten omedelbart och skapa ett ärende för att återställa storage-konto. Återställning av data kan inte garanteras, men det fungerar ibland. Skapa inte ett nytt lagringskonto med samma namn som den gamla servern tills supportärende har lösts. 
>

### <a name="protect-your-storage-account-using-vnets-and-firewalls"></a>Skydda ditt lagringskonto med hjälp av Vnet och brandväggar

Som standard är alla lagringskonton tillgängliga för några nätverk som har åtkomst till internet. Du kan dock konfigurera Nätverksregler så att bara program från specifika virtuella nätverk att få åtkomst till ett lagringskonto. Mer information finns i [konfigurera brandväggar för Azure-lagring och virtuella nätverk](storage-network-security.md). 

Artikeln visar hur du hanterar dessa inställningar med hjälp av följande PowerShell-cmdlets:
* [Add-AzureRmStorageAccountNetworkRule](/powershell/module/AzureRM.Storage/Add-AzureRmStorageAccountNetworkRule)
* [Update-AzureRmStorageAccountNetworkRuleSet](/powershell/module/azurerm.storage/update-azurermstorageaccountnetworkruleset)
* [Remove-AzureRmStorageAccountNetworkRule](/powershell/module/azurerm.storage/remove-azurermstorage-account-networkrule)

## <a name="use-storage-analytics"></a>Använd storage analytics  

[Azure Storage Analytics](storage-analytics.md) består av [Storage Analytics mätvärden](/rest/api/storageservices/about-storage-analytics-metrics) och [Storage Analytics loggning](/rest/api/storageservices/about-storage-analytics-logging). 

**Storage Analytics mätvärden** används för att samla in statistik för din Azure storage-konton som du kan använda för att övervaka hälsotillståndet för ett lagringskonto. Mått kan aktiveras för blobbar, filer, tabeller och köer.

**Loggning av Storage Analytics** händer serversidan och gör att du kan registrera information för både slutförda och misslyckade begäranden till ditt lagringskonto. Dessa loggar kan du se detaljer för Läs-, Skriv- och delete-åtgärder mot dina tabeller, köer, blobbar samt och orsakerna till misslyckade begäranden. Loggning är inte tillgänglig för Azure-filer.

Du kan konfigurera övervakning med hjälp av den [Azure-portalen](https://portal.azure.com), PowerShell eller via programmering med storage-klientbiblioteket. 

> [!NOTE]
> Du kan aktivera minut analytics med hjälp av PowerShell. Den här funktionen är inte tillgänglig i portalen.
>

* Information om hur du aktiverar och visa Storage Metrics data med hjälp av PowerShell finns [Enabling Azure Storage-mätvärden och visa mätvärdesdata](storage-enable-and-view-metrics.md#how-to-enable-metrics-using-powershell).

* Information om hur du aktiverar och hämta data för loggning av lagring med hjälp av PowerShell finns [Aktivera lagring loggning med hjälp av PowerShell](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data#how-to-enable-storage-logging-using-powershell) och [hitta din lagring loggning loggdata](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data#finding-your-storage-logging-log-data).

* Detaljerad information om hur du använder Storage-mätvärden och lagring loggning för felsökning av problem med lagring finns [övervakning, diagnostisera och felsöka Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="manage-the-data-in-the-storage-account"></a>Hantera data i lagringskontot

Du kan använda följande artiklar för att lära dig att få åtkomst till dataobjekt i storage-konto nu när du vet hur du hanterar ditt lagringskonto med PowerShell.

* [Så här hanterar du blobbar med PowerShell](../blobs/storage-how-to-use-blobs-powershell.md)
* [Hantera filer med PowerShell](../files/storage-how-to-use-files-powershell.md)
* [Så här hanterar du köer med PowerShell](../queues/storage-powershell-how-to-use-queues.md)
* [Utföra åtgärder för Azure Table storage med PowerShell](../../cosmos-db/table-storage-how-to-use-powershell.md)

Azure Cosmos DB tabell API innehåller premium-funktioner för tabellagring, till exempel NYCKELFÄRDIGT global distributionsplatsen, låg latens läsningar och skrivningar, automatisk sekundära indexering och dedikerad genomströmning. 

* Mer information finns i [Azure Cosmos DB tabell API](../../cosmos-db/table-introduction.md). 
* Information om hur du använder PowerShell för att utföra åtgärder på Azure Cosmos DB tabell API finns [utföra Azure Cosmos DB tabell-API: et med PowerShell](../../cosmos-db/table-powershell.md).

## <a name="independent-cloud-deployments-of-azure"></a>Oberoende molndistributioner av Azure

De flesta använder offentliga Azure-molnet för sina globala Azure-distribution. Det finns även vissa oberoende distributioner av Microsoft Azure på grund av suveränitet och så vidare. Dessa oberoende distributioner kallas ”miljöer”. Dessa är de tillgängliga miljöerna:

* [Azure Government-moln](https://azure.microsoft.com/features/gov/)
* [Kina Azuremolnet drivs av 21Vianet i Kina](http://www.windowsazure.cn/)
* [Azure German Cloud](../../germany/germany-welcome.md)

Mer information om hur du kommer åt dessa moln och deras lagringsutrymmen med PowerShell finns [hantera lagring i Azure oberoende moln med hjälp av PowerShell](storage-powershell-independent-clouds.md).

## <a name="clean-up-resources"></a>Rensa resurser

Om du har skapat en ny resursgrupp och ett lagringskonto för den här övningen yous kan ta bort alla resurser som du skapade genom att ta bort resursgruppen. Detta tar även bort alla resurser som ingår i gruppen. I det här fallet den tar bort lagringskonto som skapats och resursgruppen sig själv.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```
## <a name="next-steps"></a>Nästa steg

Den här artikeln beskrivs vanliga åtgärder som använder den plan cmdlet: ar för att hantera storage-konton. Du har lärt dig att: 

> [!div class="checklist"]
> * Lista storage-konton
> * Hämta en referens till ett befintligt lagringskonto
> * skapar ett lagringskonto 
> * Ange egenskaper för lagring
> * Hämta och återskapa åtkomstnycklarna
> * Skydda åtkomst till ditt lagringskonto 
> * Aktivera Storage Analytics

Den här artikeln anges också referenser till flera andra artiklar, till exempel hur du hanterar dataobjekt, hur du aktiverar Storage Analytics och hur du kommer åt Azure oberoende moln, till exempel Kina moln, tyska molnet och offentliga moln. Här följer några fler relaterade artiklar och resurser för referens:

* [Azure Storage kontrollen plan PowerShell-cmdlets](/powershell/module/AzureRM.Storage/)
* [Azure Storage data plan PowerShell-cmdlets](/powershell/module/azure.storage/)
* [Windows PowerShell-referens](https://msdn.microsoft.com/library/ms714469.aspx)
