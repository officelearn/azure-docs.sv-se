---
title: Använda Azure PowerShell med Azure Storage | Microsoft-dokument
description: Lär dig hur du använder Azure PowerShell-cmdlets för Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/16/2018
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: d2404ee58f5f44fbe5625f267e6d1c504d0bd237
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75465104"
---
# <a name="using-azure-powershell-with-azure-storage"></a>Använda Azure PowerShell med Azure Storage

Azure PowerShell används för att skapa och hantera Azure-resurser från PowerShell-kommandoraden eller i skript. För Azure Storage kan dessa cmdlets delas in i två kategorier – kontrollplanet och dataplanet. Kontrollplans cmdlets används för att hantera lagringskontot - för att skapa lagringskonton, ange egenskaper, ta bort lagringskonton, rotera åtkomstnycklarna och så vidare. Dataplans cmdlets används för att hantera data som lagras *i* lagringskontot. Till exempel ladda upp blobbar, skapa filresurser och lägga till meddelanden i en kö.

Den här how-to artikeln täcker vanliga åtgärder med hjälp av hanteringsplan cmdlets för att hantera lagringskonton. Lär dig att:

> [!div class="checklist"]
> * Lista lagringskonton
> * Hämta en referens till ett befintligt lagringskonto
> * Skapa ett lagringskonto
> * Ange egenskaper för lagringskonto
> * Hämta och återskapa åtkomstnycklarna
> * Skydda åtkomsten till ditt lagringskonto
> * Aktivera lagringsanalys

Den här artikeln innehåller länkar till flera andra PowerShell-artiklar för lagring, till exempel hur du aktiverar och får åtkomst till Storage Analytics, hur du använder dataplans cmdlets och hur du kommer åt Azure-oberoende moln som China Cloud, German Cloud och Government Moln.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Den här övningen kräver Azure PowerShell-modulen Az version 0.7 eller senare. Kör `Get-Module -ListAvailable Az` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-Az-ps) (Installera Azure PowerShell-modul).

I den här övningen kan du skriva kommandona i ett vanligt PowerShell-fönster, eller använda [ISE (Windows PowerShell Integrated Scripting Environment)](/powershell/scripting/components/ise/exploring-the-windows-powershell-ise) och skriva kommandona i en redigerare och sedan testa ett eller flera kommandon i taget när du går igenom exemplen. Du kan markera de rader som du vill köra och klicka på Kör markerat för att bara köra dessa kommandon.

Mer information om lagringskonton finns i [Introduktion till lagring](storage-introduction.md) och Om [Azure-lagringskonton](storage-create-storage-account.md).

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på din Azure-prenumeration med kommandot `Connect-AzAccount` och följ anvisningarna på skärmen.

```powershell
Connect-AzAccount
```

## <a name="list-the-storage-accounts-in-the-subscription"></a>Lista lagringskontona i prenumerationen

Kör cmdleten [Get-AzStorageAccount](/powershell/module/az.storage/Get-azStorageAccount) för att hämta listan över lagringskonton i den aktuella prenumerationen.

```powershell
Get-AzStorageAccount | Select StorageAccountName, Location
```

## <a name="get-a-reference-to-a-storage-account"></a>Hämta en referens till ett lagringskonto

Därefter behöver du en referens till ett lagringskonto. Du kan antingen skapa ett nytt lagringskonto eller få en referens till ett befintligt lagringskonto. I följande avsnitt visas båda metoderna.

### <a name="use-an-existing-storage-account"></a>Använda ett befintligt lagringskonto

Om du vill hämta ett befintligt lagringskonto behöver du namnet på resursgruppen och namnet på lagringskontot. Ange variablerna för dessa två fält och använd sedan cmdleten [Get-AzStorageAccount.](/powershell/module/az.storage/Get-azStorageAccount)

```powershell
$resourceGroup = "myexistingresourcegroup"
$storageAccountName = "myexistingstorageaccount"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName
```

Nu har du $storageAccount, vilket pekar på ett befintligt lagringskonto.

### <a name="create-a-storage-account"></a>Skapa ett lagringskonto

Följande skript visar hur du skapar ett allmänt lagringskonto med [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount). När du har skapat kontot hämtar du dess kontext, som kan användas i efterföljande kommandon i stället för att ange autentiseringen för varje samtal.

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

Skriptet använder följande PowerShell-cmdlets:

*   [Get-AzLocation](/powershell/module/az.resources/get-azlocation) – hämtar en lista över giltiga platser. Exemplet används `eastus` för plats.

*   [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) – skapar en ny resursgrupp. En resursgrupp är en logisk behållare där dina Azure-resurser distribueras och hanteras. Vår heter `teststoragerg`.

*   [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) – skapar lagringskontot. I exemplet `testpshstorage`används .

SKU-namnet anger typen av replikering för lagringskontot, till exempel LRS (Lokalt redundant lagring). Mer information om replikering finns i [Azure Storage Replication](storage-redundancy.md).

> [!IMPORTANT]
> Namnet på ditt lagringskonto måste vara unikt i Azure och måste vara gemener. Namnkonventioner och begränsningar finns i [Namngivning och referera behållare, blobbar och metadata](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata).
>

Nu har du ett nytt lagringskonto och en referens till det.

## <a name="manage-the-storage-account"></a>Hantera lagringskontot

Nu när du har en referens till ett nytt lagringskonto eller ett befintligt lagringskonto visas några av de kommandon som du kan använda för att hantera ditt lagringskonto i följande avsnitt.

### <a name="storage-account-properties"></a>Egenskaper för lagringskonto

Om du vill ändra inställningarna för ett lagringskonto använder du [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount). Du kan inte ändra platsen för ett lagringskonto eller resursgruppen där det finns, men du kan ändra många av de andra egenskaperna. I följande lista visas några av de egenskaper som du kan ändra med PowerShell.

* Den **anpassade domän** som tilldelats lagringskontot.

* **Taggarna** som tilldelats lagringskontot. Taggar används ofta för att kategorisera resurser för faktureringsändamål.

* **SKU** är replikeringsinställningen för lagringskontot, till exempel LRS för lokalt redundant lagring. Du kan till exempel\_ändra från Standard\_LRS\_till Standard GRS eller Standard RAGRS. Observera att du inte\_kan ändra Standard\_ZRS,\_Standard GZRS,\_Standard RAGZRS eller Premium LRS till andra SKU:er eller ändra andra SKU:er till dessa.

* **Åtkomstnivån** för Blob-lagringskonton. Värdet för åtkomstnivån är inställt **på frekvent** eller **lågfrekvent**och gör att du kan minimera din kostnad genom att välja den åtkomstnivå som stämmer överens med hur du använder lagringskontot. Mer information finns i [Hot-, cool- och arkivlagringsnivåer](../blobs/storage-blob-storage-tiers.md).

* Tillåt endast HTTPS-trafik.

### <a name="manage-the-access-keys"></a>Hantera åtkomstnycklarna

Ett Azure Storage-konto levereras med två kontonycklar. Om du vill hämta nycklarna använder du [Get-AzStorageAccountKey](/powershell/module/az.Storage/Get-azStorageAccountKey). Det här exemplet hämtar den första nyckeln. Om du vill hämta `Value[1]` den `Value[0]`andra använder du i stället för .

```powershell
$storageAccountKey = `
    (Get-AzStorageAccountKey `
    -ResourceGroupName $resourceGroup `
    -Name $storageAccountName).Value[0]
```

Om du vill återskapa nyckeln använder du [New-AzStorageAccountKey](/powershell/module/az.Storage/New-azStorageAccountKey).

```powershell
New-AzStorageAccountKey -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -KeyName key1
```

Om du vill återskapa `key2` den andra nyckeln `key1`använder du som nyckelnamn i stället för .

Återskapa en av dina nycklar och hämta den sedan igen för att se det nya värdet.

> [!NOTE]
> Du bör utföra noggrann planering innan du återskapar nyckeln för ett produktionslagringskonto. Om du återskapar en eller båda nycklarna blir åtkomsten för alla program som använder nyckeln som har återskapats. Mer information finns i [Hantera åtkomstnycklar för lagringskonto](storage-account-keys-manage.md).


### <a name="delete-a-storage-account"></a>Ta bort ett lagringskonto

Om du vill ta bort ett lagringskonto använder du [Remove-AzStorageAccount](/powershell/module/az.storage/Remove-azStorageAccount).

```powershell
Remove-AzStorageAccount -ResourceGroup $resourceGroup -AccountName $storageAccountName
```

> [!IMPORTANT]
> När du tar bort ett lagringskonto tas även alla tillgångar som lagras i kontot bort. Om du tar bort ett konto av misstag ringer du supporten omedelbart och öppnar en biljett för att återställa lagringskontot. Återställning av dina data är inte garanterad, men det fungerar ibland. Skapa inte ett nytt lagringskonto med samma namn som det gamla förrän supportbiljetten har lösts.
>

### <a name="protect-your-storage-account-using-vnets-and-firewalls"></a>Skydda ditt lagringskonto med virtuella nätverk och brandväggar

Som standard är alla lagringskonton tillgängliga för alla nätverk som har åtkomst till Internet. Du kan dock konfigurera nätverksregler så att program från specifika virtuella nätverk endast tillåter att program från specifika virtuella nätverk får åtkomst till ett lagringskonto. Mer information finns i [Konfigurera Azure Storage-brandväggar och virtuella nätverk](storage-network-security.md).

Artikeln visar hur du hanterar dessa inställningar med hjälp av följande PowerShell-cmdlets:
* [Tillägg-AzStorageAccountNetworkRule](/powershell/module/az.Storage/Add-azStorageAccountNetworkRule)
* [Update-AzStorageAccountNetworkRuleSet](/powershell/module/az.storage/update-azstorageaccountnetworkruleset)
* [Ta bort-AzStorageAccountNetworkRule](https://docs.microsoft.com/powershell/module/az.storage/remove-azstorageaccountnetworkrule)

## <a name="use-storage-analytics"></a>Använda lagringsanalys

[Azure Storage Analytics](storage-analytics.md) består av [Lagringsanalysmått](/rest/api/storageservices/about-storage-analytics-metrics) och [Lagringsanalysloggning](/rest/api/storageservices/about-storage-analytics-logging).

**Storage Analytics-mått** används för att samla in mått för dina Azure-lagringskonton som du kan använda för att övervaka hälsotillståndet för ett lagringskonto. Mått kan aktiveras för blobbar, filer, tabeller och köer.

**Storage Analytics Loggning** sker på serversidan och gör att du kan registrera information för både lyckade och misslyckade begäranden till ditt lagringskonto. Med de här loggarna kan du se information om läs-, skriv- och borttagningsåtgärder mot tabeller, köer och blobbar samt orsakerna till misslyckade begäranden. Loggning är inte tillgängligt för Azure-filer.

Du kan konfigurera övervakning med hjälp av [Azure-portalen,](https://portal.azure.com)PowerShell eller programmässigt med hjälp av lagringsklientbiblioteket.

> [!NOTE]
> Du kan aktivera minutanalys med PowerShell. Den här funktionen är inte tillgänglig i portalen.
>

* Mer information om hur du aktiverar och visar lagringsmåttdata med PowerShell finns i [Mätvärden för lagringsanalys](storage-analytics-metrics.md).

* Mer information om hur du aktiverar och hämtar lagringsloggningsdata med PowerShell finns i [Så här aktiverar du Lagringsloggning med Hjälp av PowerShell](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data) och Söker efter [loggdata för lagringsloggning](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data).

* Detaljerad information om hur du använder lagringsmått och lagringsloggning för att felsöka lagringsproblem finns i [Övervaka, diagnostisera och felsöka Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="manage-the-data-in-the-storage-account"></a>Hantera data i lagringskontot

Nu när du förstår hur du hanterar ditt lagringskonto med PowerShell kan du använda följande artiklar för att lära dig hur du kommer åt dataobjekten i lagringskontot.

* [Hantera blobbar med PowerShell](../blobs/storage-how-to-use-blobs-powershell.md)
* [Hantera filer med PowerShell](../files/storage-how-to-use-files-powershell.md)
* [Hantera köer med PowerShell](../queues/storage-powershell-how-to-use-queues.md)
* [Utföra Azure Table-lagringsåtgärder med PowerShell](../../storage/tables/table-storage-how-to-use-powershell.md)

Azure Cosmos DB Table API innehåller premiumfunktioner för tabelllagring, till exempel totalenal distribution, läsningar och skrivningar med låg latens, automatisk sekundär indexering och dedikerat dataflöde.

* Mer information finns i [Azure Cosmos DB Table API](../../cosmos-db/table-introduction.md).

## <a name="independent-cloud-deployments-of-azure"></a>Oberoende molndistributioner av Azure

De flesta använder Azure Public Cloud för sin globala Azure-distribution. Det finns också några oberoende distributioner av Microsoft Azure på grund av suveränitet och så vidare. Dessa oberoende distributioner kallas "miljöer". Dessa är de tillgängliga miljöerna:

* [Azure Government Cloud](https://azure.microsoft.com/features/gov/)
* [Azure China 21Vianet Cloud drivs av 21Vianet i Kina](http://www.windowsazure.cn/)
* [Azure tyska molnet](../../germany/germany-welcome.md)

Information om hur du kommer åt dessa moln och deras lagring med PowerShell finns [i Hantera lagring i Azure-oberoende moln med PowerShell](storage-powershell-independent-clouds.md).

## <a name="clean-up-resources"></a>Rensa resurser

Om du har skapat en ny resursgrupp och ett lagringskonto för den här övningen kan du ta bort alla tillgångar som du har skapat genom att ta bort resursgruppen. Detta tar även bort alla resurser som ingår i gruppen. I det här fallet tas det skapade lagringskontot och resursgruppen bort.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```
## <a name="next-steps"></a>Nästa steg

Den här how-to artikeln täcker vanliga åtgärder med hjälp av hanteringsplan cmdlets för att hantera lagringskonton. Du har lärt dig att:

> [!div class="checklist"]
> * Lista lagringskonton
> * Hämta en referens till ett befintligt lagringskonto
> * Skapa ett lagringskonto
> * Ange egenskaper för lagringskonto
> * Hämta och återskapa åtkomstnycklarna
> * Skydda åtkomsten till ditt lagringskonto
> * Aktivera lagringsanalys

Den här artikeln innehåller också referenser till flera andra artiklar, till exempel hur du hanterar dataobjekt, hur du aktiverar Lagringsanalys och hur du kommer åt Azure-oberoende moln som China Cloud, German Cloud och Government Cloud. Här är några fler relaterade artiklar och resurser som referens:

* [Azure Storage-kontrollplan PowerShell-cmdlets](/powershell/module/az.storage/)
* [Azure Storage-dataplan PowerShell-cmdlets](/powershell/module/azure.storage/)
* [Windows PowerShell-referens](/powershell/scripting/overview)
