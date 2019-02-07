---
title: Med hjälp av Azure PowerShell med Azure Storage | Microsoft Docs
description: Lär dig hur du använder Azure PowerShell-cmdlets för Azure Storage.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 08/16/2018
ms.author: rogarana
ms.subservice: common
ms.openlocfilehash: 2787353230408d6374db36eb5621f98285245364
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2019
ms.locfileid: "55766529"
---
# <a name="using-azure-powershell-with-azure-storage"></a>Använda Azure PowerShell med Azure Storage

Azure PowerShell används för att skapa och hantera Azure-resurser från PowerShell-kommandoraden eller i skript. Dessa cmdletar indelade i två kategorier – kontrollplanet och dataplanet för Azure Storage. Plan-cmdlet: ar för kontroll används för att hantera storage-konto – för att skapa storage-konton, ange egenskaper, tar bort lagringskonton, rotera åtkomstnycklar och så vidare. Plan-cmdlet: ar för data som används för att hantera de data som lagras *i* storage-konto. Till exempel ladda upp blobar, skapa filresurser och lägger till meddelanden i en kö.

I den här artikeln beskriver vanliga åtgärder som använder de plan cmdletarna för att hantera lagringskonton. Lär dig att: 

> [!div class="checklist"]
> * Lista över storage-konton
> * Hämta en referens till ett befintligt lagringskonto
> * skapar ett lagringskonto 
> * Ange egenskaper för lagringskontot
> * Hämta och återskapa åtkomstnycklarna
> * Skydda åtkomsten till ditt storage-konto 
> * Aktivera Storage Analytics

Den här artikeln innehåller länkar till flera andra PowerShell-artiklar för lagring, till exempel hur du aktiverar och komma åt Storage Analytics, hur du använder data plan cmdletar och hur du kommer åt Azure oberoende moln som Kina-molnet, tyska molnet och myndigheter Moln.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Den här övningen kräver Azure PowerShell-modulen Az 0.7 eller senare. Kör `Get-Module -ListAvailable Az` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-Az-ps) (Installera Azure PowerShell-modul). 

I den här övningen du kan skriva kommandon i ett vanligt PowerShell-fönster och du kan använda den [Windows PowerShell Integrated Scripting Environment (ISE)](/powershell/scripting/getting-started/fundamental/windows-powershell-integrated-scripting-environment--ise-) och Skriv kommandona i ett redigeringsprogram för och testa ett eller flera kommandon samtidigt som du går igenom exemplen. Du kan markera de rader som du vill köra och klicka Kör valda att bara köra dessa kommandon.

Mer information om lagringskonton finns i [introduktion till Storage](storage-introduction.md) och [om Azure storage-konton](storage-create-storage-account.md).

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure-prenumerationen med kommandot `Connect-AzAccount` och följ anvisningarna på skärmen.

```powershell
Connect-AzAccount
```

## <a name="list-the-storage-accounts-in-the-subscription"></a>Lista över storage-konton i prenumerationen

Kör den [Get-AzStorageAccount](/powershell/module/az.storage/Get-azStorageAccount) cmdlet för att hämta listan med lagringskonton i den aktuella prenumerationen. 

```powershell
Get-AzStorageAccount | Select StorageAccountName, Location
```

## <a name="get-a-reference-to-a-storage-account"></a>Hämta en referens till ett lagringskonto

Därefter behöver du en referens till ett lagringskonto. Du kan skapa ett nytt lagringskonto, eller så kan du hämta en referens till ett befintligt lagringskonto. I följande avsnitt beskrivs båda metoderna. 

### <a name="use-an-existing-storage-account"></a>Använd ett befintligt lagringskonto 

Om du vill hämta ett befintligt lagringskonto behöver du namnet på resursgruppen och namnet på lagringskontot. Ställ in variabler för dessa två fält och sedan använda den [Get-AzStorageAccount](/powershell/module/az.storage/Get-azStorageAccount) cmdlet. 

```powershell
$resourceGroup = "myexistingresourcegroup"
$storageAccountName = "myexistingstorageaccount"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName 
```

Nu har du $storageAccount som pekar mot ett befintligt lagringskonto.

### <a name="create-a-storage-account"></a>skapar ett lagringskonto 

Följande skript visar hur du skapar ett allmänt storage-konto med [New AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount). När du har skapat kontot kan hämta dess kontext, som kan användas i efterföljande kommandon i stället för att ange autentiseringen med varje anrop.

```powershell
# Get list of locations and select one.
Get-AzLocation | select Location 
$location = "eastus"

# Create a new resource group.
$resourceGroup = "teststoragerg"
New-AzResourceGroup -Name $resourceGroup -Location $location 

# Set the name of the storage account and the SKU name. 
$storageAccountName = "testpshstorage"
$skuName = "Standard_LRS"
    
# Create the storage account.
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName $skuName

# Retrieve the context. 
$ctx = $storageAccount.Context
```

Skriptet använder följande PowerShell-cmdletar: 

*   [Get-AzLocation](/powershell/module/az.resources/get-azlocation) – hämtar en lista över giltiga platser. I exemplet används `eastus` för platsen.

*   [Ny AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) – skapar en ny resursgrupp. En resursgrupp är en logisk behållare där dina Azure-resurser distribueras och hanteras. Våra kallas `teststoragerg`. 

*   [Ny AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) --skapar storage-konto. I exemplet används `testpshstorage`.

SKU-namnet anger vilken typ av replikering för lagringskontot, till exempel LRS (lokalt Redundant lagring). Mer information om replikering finns i [Azure Storage-replikering](storage-redundancy.md).

> [!IMPORTANT]
> Namnet på ditt lagringskonto måste vara unikt i Azure och måste vara i gemener. Namngivningsregler och begränsningar finns i [namngivning och referens av behållare, Blobar och Metadata](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata).
> 

Nu har du ett nytt lagringskonto och en referens till den. 

## <a name="manage-the-storage-account"></a>Hantera storage-konto

Nu när du har en referens till ett nytt lagringskonto eller ett befintligt lagringskonto beskrivs i följande avsnitt några av de kommandon som du kan använda för att hantera ditt lagringskonto.

### <a name="storage-account-properties"></a>Egenskaper för lagringskontot

Du kan ändra inställningarna för ett lagringskonto med [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount). Medan du inte kan ändra platsen för ett lagringskonto eller resursgruppen där den finns, kan du ändra många av de andra egenskaperna. Här nedan listas några av de egenskaper som du kan ändra med hjälp av PowerShell.

* Den **anpassad domän** tilldelad till lagringskontot.

* Den **taggar** tilldelad till lagringskontot. Taggar används ofta för att kategorisera resurser för fakturering.

* Den **SKU** är replikeringsinställningen för lagringskontot, till exempel LRS för lokalt Redundant lagring. Du kan till exempel ändra från Standard\_LRS till Standard\_GRS- eller Standard\_RAGRS. Observera att du inte kan ändra Standard\_ZRS- eller Premium\_LRS till andra SKU: er eller ändra andra SKU: er till dessa.

* Den **åtkomstnivå** för Blob storage-konton. Värdet för åtkomstnivå är inställt **frekvent** eller **lågfrekvent**, och du kan minimera kostnaden genom att välja den åtkomstnivå som överensstämmer med hur du använder storage-konto. Mer information finns i [frekvent, lågfrekvent och arkivlagringsnivå](../blobs/storage-blob-storage-tiers.md).

* Tillåt endast HTTPS-trafik. 

### <a name="manage-the-access-keys"></a>Hantera åtkomstnycklar

Ett Azure Storage-konto levereras med två nycklar. Använd för att hämta nycklarna [Get-AzStorageAccountKey](/powershell/module/az.Storage/Get-azStorageAccountKey). Det här exemplet hämtar den första nyckeln. Använd för att hämta en `Value[1]` i stället för `Value[0]`.

```powershell
$storageAccountKey = `
    (Get-AzStorageAccountKey `
    -ResourceGroupName $resourceGroup `
    -Name $storageAccountName).Value[0]
```

Om du vill återskapa nyckeln, Använd [New AzStorageAccountKey](/powershell/module/az.Storage/New-azStorageAccountKey). 

```powershell
New-AzStorageAccountKey -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -KeyName key1 
```

Om du vill återskapa den andra nyckeln använda `key2` nyckelnamn i stället för `key1`.

Återskapa en av dina nycklar och sedan hämtar den igen för att se det nya värdet.

> [!NOTE] 
> Du bör utföra noggrann planering innan du återskapar nyckeln för ett lagringskonto för produktion. Återskapar nycklar för en eller båda så ogiltigförklaras åtkomsten för alla program som använder den nyckel som har återskapats. Mer information finns i [Åtkomstnycklar](storage-account-manage.md#access-keys).


### <a name="delete-a-storage-account"></a>Ta bort ett lagringskonto 

Ta bort ett lagringskonto genom att använda [Remove-AzStorageAccount](/powershell/module/az.storage/Remove-azStorageAccount).

```powershell
Remove-AzStorageAccount -ResourceGroup $resourceGroup -AccountName $storageAccountName
```

> [!IMPORTANT]
> När du tar bort ett lagringskonto, tas alla resurser som lagras i kontot bort. Om du tar bort ett konto råkar anropa Support direkt och skapa ett ärende om du vill återställa storage-konto. Återställning av data är inte säkert, men det fungerar ibland. Skapa inte ett nytt lagringskonto med samma namn som den gamla servern tills supportärende har lösts. 
>

### <a name="protect-your-storage-account-using-vnets-and-firewalls"></a>Skydda ditt storage-konto med hjälp av virtuella nätverk och brandväggar

Som standard är alla lagringskonton är tillgängliga för några nätverk som har åtkomst till internet. Du kan dock konfigurera Nätverksregler för att endast tillåta att program från specifika virtuella nätverk till ett lagringskonto. Mer information finns i [konfigurera Azure Storage-brandväggar och virtuella nätverk](storage-network-security.md). 

Artikeln visar hur du hanterar de här inställningarna med hjälp av följande PowerShell-cmdletar:
* [Add-AzStorageAccountNetworkRule](/powershell/module/az.Storage/Add-azStorageAccountNetworkRule)
* [Update-AzStorageAccountNetworkRuleSet](/powershell/module/az.storage/update-azstorageaccountnetworkruleset)
* [Remove-AzStorageAccountNetworkRule](https://docs.microsoft.com/powershell/module/az.storage/remove-azstorageaccountnetworkrule)

## <a name="use-storage-analytics"></a>Med storage analytics  

[Azure Storage Analytics](storage-analytics.md) består av [mätvärden i Storage Analytics](/rest/api/storageservices/about-storage-analytics-metrics) och [Storage Analytics loggning](/rest/api/storageservices/about-storage-analytics-logging). 

**Mätvärden i Storage Analytics** används för att samla in mått för dina Azure storage-konton som du kan använda för att övervaka hälsotillståndet för ett lagringskonto. Mått kan aktiveras för blobar, filer, tabeller och köer.

**Storage Analytics loggning** händer serversidan och gör att du kan registrera information för både lyckade och misslyckade begäranden till ditt lagringskonto. Dessa loggar kan du se information om läsa, skriva och ta bort åtgärder mot dina tabeller, köer och BLOB samt orsaker för misslyckade förfrågningar. Loggning är inte tillgänglig för Azure Files.

Du kan konfigurera övervakning med hjälp av den [Azure-portalen](https://portal.azure.com), PowerShell, eller genom programmering med storage-klientbiblioteket. 

> [!NOTE]
> Du kan aktivera minut analytics med hjälp av PowerShell. Den här funktionen är inte tillgänglig i portalen.
>

* Läs hur du aktiverar och visar mätvärden i Storage-data med hjälp av PowerShell i [aktivera Azure Storage-mätvärden och visa måttdata](storage-enable-and-view-metrics.md#how-to-enable-metrics-using-powershell).

* Läs hur du aktiverar och hämtar data för loggning av lagring med hjälp av PowerShell i [så här aktiverar du loggning av lagring med hjälp av PowerShell](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data) och [att söka efter loggdata Storage Logging](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data).

* Detaljerad information om hur du använder Storage-mätvärden och loggning för lagring för att felsöka problem med lagring finns i [övervakning, diagnostisera och felsöka Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="manage-the-data-in-the-storage-account"></a>Hantera data i lagringskontot

Nu när du förstår hur du hanterar ditt lagringskonto med PowerShell, kan du använda i följande artiklar om hur du kan få åtkomst till dataobjekt i storage-kontot.

* [Hur du hanterar blobar med PowerShell](../blobs/storage-how-to-use-blobs-powershell.md)
* [Så här hanterar du filer med PowerShell](../files/storage-how-to-use-files-powershell.md)
* [Så här hanterar du köer med PowerShell](../queues/storage-powershell-how-to-use-queues.md)
* [Utföra åtgärder för Azure Table storage med PowerShell](../../storage/tables/table-storage-how-to-use-powershell.md)

Azure Cosmos DB Table API ger premium-funktioner för tabellagring, till exempel nyckelfärdig global distribution, låg latens vid läsning och skrivning, automatisk sekundär indexering och dedikerat dataflöde. 

* Mer information finns i [Azure Cosmos DB Table API](../../cosmos-db/table-introduction.md). 

## <a name="independent-cloud-deployments-of-azure"></a>Oberoende distributioner av Azure

De flesta använda Azures offentliga moln för sin globala Azure-distribution. Det finns även vissa oberoende distributioner av Microsoft Azure på grund av landsbaserad placering och så vidare. Dessa oberoende distributioner kallas ”miljöer”. Dessa är tillgängliga miljöer:

* [Azure Government Cloud](https://azure.microsoft.com/features/gov/)
* [Azure Kina-molnet som drivs av 21Vianet i Kina](http://www.windowsazure.cn/)
* [Azure German Cloud](../../germany/germany-welcome.md)

Information om hur du kommer åt dessa moln och deras lagringsutrymmen med PowerShell finns i [hantera lagring i Azure-oberoende molnet med hjälp av PowerShell](storage-powershell-independent-clouds.md).

## <a name="clean-up-resources"></a>Rensa resurser

Om du har skapat en ny resursgrupp och ett lagringskonto för den här övningen yous kan ta bort alla resurser som du skapade genom att ta bort resursgruppen. Detta tar även bort alla resurser som ingår i gruppen. I det här fallet tas bort lagringskontot som skapas och själva resursgruppen.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```
## <a name="next-steps"></a>Nästa steg

I den här artikeln beskriver vanliga åtgärder som använder de plan cmdletarna för att hantera lagringskonton. Du har lärt dig att: 

> [!div class="checklist"]
> * Lista över storage-konton
> * Hämta en referens till ett befintligt lagringskonto
> * skapar ett lagringskonto 
> * Ange egenskaper för lagringskontot
> * Hämta och återskapa åtkomstnycklarna
> * Skydda åtkomsten till ditt storage-konto 
> * Aktivera Storage Analytics

Den här artikeln får också referenser till flera andra artiklar, till exempel hur du hanterar dataobjekt, hur du aktiverar Storage Analytics och hur du kommer åt Azure oberoende moln som Kina-molnet, tyska molnet och Government-molnet. Här följer några fler relaterade artiklar och resurser för referens:

* [Azure Storage control plan PowerShell-cmdlets](/powershell/module/az.storage/)
* [PowerShell-cmdlets för Azure Storage-data-dataplaner](/powershell/module/azure.storage/)
* [Windows PowerShell-referens](https://msdn.microsoft.com/library/ms714469.aspx)
