---
title: Använda Azure PowerShell med Azure Storage | Microsoft Docs
description: Lär dig hur du använder Azure PowerShell-cmdletar för Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/16/2018
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: ac210a03f8b1a0a5f7fff07cbc68b4cd6bc98632
ms.sourcegitcommit: df7942ba1f28903ff7bef640ecef894e95f7f335
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/14/2019
ms.locfileid: "69016350"
---
# <a name="using-azure-powershell-with-azure-storage"></a>Använda Azure PowerShell med Azure Storage

Azure PowerShell används för att skapa och hantera Azure-resurser från kommando raden i PowerShell eller i skript. För Azure Storage delas dessa cmdlets in i två kategorier – kontroll planet och data planet. Kontroll Plans-cmdletarna används för att hantera lagrings kontot – för att skapa lagrings konton, ange egenskaper, ta bort lagrings konton, rotera åtkomst nycklar och så vidare. Data Plans-cmdletarna används för att hantera data som lagras *i* lagrings kontot. Du kan till exempel Ladda upp blobar, skapa fil resurser och lägga till meddelanden i en kö.

Den här instruktions artikeln täcker vanliga åtgärder med hjälp av hanterings plan-cmdletar för att hantera lagrings konton. Lär dig att:

> [!div class="checklist"]
> * Lista lagrings konton
> * Hämta en referens till ett befintligt lagrings konto
> * skapar ett lagringskonto
> * Ange egenskaper för lagrings konto
> * Hämta och återskapa åtkomst nycklarna
> * Skydda åtkomsten till ditt lagrings konto
> * Aktivera Lagringsanalys

Den här artikeln innehåller länkar till flera andra PowerShell-artiklar för lagring, till exempel hur du aktiverar och får åtkomst till Lagringsanalys, hur du använder data Plans-cmdletar och hur du kommer åt Azures oberoende moln, till exempel Kina, tyska och myndigheter Kunde.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Den här övningen kräver Azure PowerShell-modulen AZ version 0,7 eller senare. Kör `Get-Module -ListAvailable Az` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-Az-ps) (Installera Azure PowerShell-modul).

I den här övningen kan du skriva kommandona i ett vanligt PowerShell-fönster, eller använda [Windows POWERSHELL Ise (Integrated Scripting Environment)](/powershell/scripting/getting-started/fundamental/windows-powershell-integrated-scripting-environment--ise-) och skriva kommandona i ett redigerings program. testa sedan ett eller flera kommandon i taget när du går igenom fler. Du kan markera de rader som du vill köra och klicka på Kör markerade för att bara köra dessa kommandon.

Mer information om lagrings konton finns i [Introduktion till lagring](storage-introduction.md) och [Azure Storage-konton](storage-create-storage-account.md).

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på din Azure-prenumeration med kommandot `Connect-AzAccount` och följ anvisningarna på skärmen.

```powershell
Connect-AzAccount
```

## <a name="list-the-storage-accounts-in-the-subscription"></a>Visa en lista över lagrings kontona i prenumerationen

Kör cmdleten [Get-AzStorageAccount](/powershell/module/az.storage/Get-azStorageAccount) för att hämta listan över lagrings konton i den aktuella prenumerationen.

```powershell
Get-AzStorageAccount | Select StorageAccountName, Location
```

## <a name="get-a-reference-to-a-storage-account"></a>Hämta en referens till ett lagrings konto

Sedan behöver du en referens till ett lagrings konto. Du kan antingen skapa ett nytt lagrings konto eller hämta en referens till ett befintligt lagrings konto. I följande avsnitt visas båda metoderna.

### <a name="use-an-existing-storage-account"></a>Använd ett befintligt lagrings konto

Om du vill hämta ett befintligt lagrings konto behöver du namnet på resurs gruppen och namnet på lagrings kontot. Ange variablerna för dessa två fält och Använd sedan cmdleten [Get-AzStorageAccount](/powershell/module/az.storage/Get-azStorageAccount) .

```powershell
$resourceGroup = "myexistingresourcegroup"
$storageAccountName = "myexistingstorageaccount"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName
```

Nu har du $storageAccount som pekar på ett befintligt lagrings konto.

### <a name="create-a-storage-account"></a>skapar ett lagringskonto

Följande skript visar hur du skapar ett allmänt lagrings konto med hjälp av [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount). När du har skapat kontot hämtar du dess kontext, som kan användas i efterföljande kommandon i stället för att ange autentiseringen med varje anrop.

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

*   [Get-AzLocation](/powershell/module/az.resources/get-azlocation) – hämtar en lista över giltiga platser. Exemplet använder `eastus` för plats.

*   [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) – skapar en ny resurs grupp. En resurs grupp är en logisk behållare där dina Azure-resurser distribueras och hanteras. Oss kallas `teststoragerg`för.

*   [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) – skapar lagrings kontot. Exemplet använder `testpshstorage`.

SKU-namnet anger typ av replikering för lagrings kontot, t. ex. LRS (lokalt redundant lagring). Mer information om replikering finns i [Azure Storage replikering](storage-redundancy.md).

> [!IMPORTANT]
> Namnet på ditt lagrings konto måste vara unikt i Azure och måste vara i gemener. Information om namngivning av konventioner och begränsningar finns i [namngivning och referens av behållare, blobbar och metadata](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata).
>

Nu har du ett nytt lagrings konto och en referens till det.

## <a name="manage-the-storage-account"></a>Hantera lagrings kontot

Nu när du har en referens till ett nytt lagrings konto eller ett befintligt lagrings konto visar följande avsnitt några av de kommandon som du kan använda för att hantera ditt lagrings konto.

### <a name="storage-account-properties"></a>Egenskaper för lagrings konto

Om du vill ändra inställningarna för ett lagrings konto använder du [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount). Även om du inte kan ändra platsen för ett lagrings konto eller resurs gruppen där det finns, kan du ändra många av de andra egenskaperna. Nedan visas några av de egenskaper som du kan ändra med hjälp av PowerShell.

* Den **anpassade domän** som har tilldelats lagrings kontot.

* **Taggarna** som har tilldelats lagrings kontot. Taggar används ofta för att kategorisera resurser i fakturerings syfte.

* **SKU: n** är replikeringsinställningarna för lagrings kontot, till exempel LRS för lokalt redundant lagring. Du kan till exempel ändra från standard\_-LRS till standard\_-GRS eller standard\_-RAGRS. Observera att du inte kan ändra\_standard ZRS,\_standard-GZRS\_, standard-RAGZRS\_eller Premium-LRS till andra SKU: er eller ändra andra SKU: er till dessa.

* **Åtkomst nivån** för Blob Storage-konton. Värdet för åtkomst nivån är inställt på frekventeller låg frekvent, och gör att du kan minimera kostnaden genom att välja åtkomst nivån som motsvarar hur du använder lagrings kontot. Mer information finns i lagrings nivåerna frekvent, låg frekvent [och Arkiv lag](../blobs/storage-blob-storage-tiers.md)ring.

* Tillåt endast HTTPS-trafik.

### <a name="manage-the-access-keys"></a>Hantera åtkomst nycklar

Ett Azure Storage konto levereras med två konto nycklar. Hämta nycklarna med [Get-AzStorageAccountKey](/powershell/module/az.Storage/Get-azStorageAccountKey). I det här exemplet hämtas den första nyckeln. Om du vill hämta den andra använder `Value[1]` du i `Value[0]`stället för.

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

Om du vill återskapa den andra nyckeln använder `key2` du som nyckel namn i stället `key1`för.

Återskapa en av dina nycklar och hämta det sedan igen för att se det nya värdet.

> [!NOTE]
> Du bör utföra noggrann planering innan du återskapar nyckeln för ett produktions lagrings konto. Om du återskapar en eller båda nycklarna inkommer åtkomsten för alla program som använder den nyckel som återskapades. Mer information finns i [Åtkomstnycklar](storage-account-manage.md#access-keys).


### <a name="delete-a-storage-account"></a>Ta bort ett lagringskonto

Om du vill ta bort ett lagrings konto använder du [Remove-AzStorageAccount](/powershell/module/az.storage/Remove-azStorageAccount).

```powershell
Remove-AzStorageAccount -ResourceGroup $resourceGroup -AccountName $storageAccountName
```

> [!IMPORTANT]
> När du tar bort ett lagrings konto raderas även alla resurser som lagras i kontot. Om du tar bort ett konto av misstag kan du anropa supporten direkt och öppna ett ärende för att återställa lagrings kontot. Återställning av dina data är inte garanterat, men det fungerar ibland. Skapa inte ett nytt lagrings konto med samma namn som det gamla tills support ärendet har lösts.
>

### <a name="protect-your-storage-account-using-vnets-and-firewalls"></a>Skydda ditt lagrings konto med virtuella nätverk och brand väggar

Som standard är alla lagrings konton tillgängliga för alla nätverk som har åtkomst till Internet. Du kan dock konfigurera nätverks regler så att endast program från vissa virtuella nätverk får åtkomst till ett lagrings konto. Mer information finns i [konfigurera Azure Storage brand väggar och virtuella nätverk](storage-network-security.md).

Artikeln visar hur du hanterar dessa inställningar med hjälp av följande PowerShell-cmdletar:
* [Add-AzStorageAccountNetworkRule](/powershell/module/az.Storage/Add-azStorageAccountNetworkRule)
* [Update-AzStorageAccountNetworkRuleSet](/powershell/module/az.storage/update-azstorageaccountnetworkruleset)
* [Remove-AzStorageAccountNetworkRule](https://docs.microsoft.com/powershell/module/az.storage/remove-azstorageaccountnetworkrule)

## <a name="use-storage-analytics"></a>Använd Storage Analytics  

[Azure-lagringsanalys](storage-analytics.md) består av [Lagringsanalys mått](/rest/api/storageservices/about-storage-analytics-metrics) och [Lagringsanalys loggning](/rest/api/storageservices/about-storage-analytics-logging).

**Lagringsanalys mått** används för att samla in mått för dina Azure Storage-konton som du kan använda för att övervaka hälso tillståndet för ett lagrings konto. Mått kan aktive ras för blobbar, filer, tabeller och köer.

**Lagringsanalys loggning** sker på Server sidan och du kan registrera information för både lyckade och misslyckade förfrågningar till ditt lagrings konto. Med dessa loggar kan du se information om Läs-, skriv-och borttagnings åtgärder mot dina tabeller, köer och blobbar samt orsakerna till misslyckade förfrågningar. Det går inte att logga in för Azure Files.

Du kan konfigurera övervakning med hjälp av [Azure Portal](https://portal.azure.com), PowerShell eller program mässigt med hjälp av lagrings klient biblioteket.

> [!NOTE]
> Du kan aktivera minut analys med hjälp av PowerShell. Den här funktionen är inte tillgänglig i portalen.
>

* Information om hur du aktiverar och visar lagrings mått data med hjälp av PowerShell finns i [Storage Analytics-mått](storage-analytics-metrics.md).

* Information om hur du aktiverar och hämtar lagrings loggnings data med hjälp av PowerShell finns i [så här aktiverar du lagrings loggning med PowerShell](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data) och [söker efter logg data för lagrings loggning](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data).

* Detaljerad information om hur du använder lagrings mått och lagrings loggning för att felsöka lagrings problem finns i [övervakning, diagnostisering och fel sökning Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="manage-the-data-in-the-storage-account"></a>Hantera data i lagrings kontot

Nu när du förstår hur du hanterar ditt lagrings konto med PowerShell kan du använda följande artiklar för att lära dig hur du får åtkomst till data objekt i lagrings kontot.

* [Hantera blobbar med PowerShell](../blobs/storage-how-to-use-blobs-powershell.md)
* [Så här hanterar du filer med PowerShell](../files/storage-how-to-use-files-powershell.md)
* [Hantera köer med PowerShell](../queues/storage-powershell-how-to-use-queues.md)
* [Utföra Azure Table Storage-åtgärder med PowerShell](../../storage/tables/table-storage-how-to-use-powershell.md)

Azure Cosmos DB Tabell-API innehåller Premium funktioner för tabell lagring, till exempel nyckel färdig global distribution, låg latens läsningar och skrivningar, automatisk sekundär indexering och dedikerat data flöde.

* Mer information finns i [Azure Cosmos DB tabell-API](../../cosmos-db/table-introduction.md).

## <a name="independent-cloud-deployments-of-azure"></a>Oberoende moln distributioner av Azure

De flesta människor använder Azures offentliga moln för sin globala Azure-distribution. Det finns också några oberoende distributioner av Microsoft Azure av suveränitets skäl och så vidare. Dessa oberoende distributioner kallas "miljöer". Det här är tillgängliga miljöer:

* [Azure Government Cloud](https://azure.microsoft.com/features/gov/)
* [Azure Kina 21Vianet-moln som drivs av 21Vianet i Kina](http://www.windowsazure.cn/)
* [Azure German Cloud](../../germany/germany-welcome.md)

Information om hur du kommer åt dessa moln och deras lagring med PowerShell finns i [Hantera lagring i Azures oberoende moln med PowerShell](storage-powershell-independent-clouds.md).

## <a name="clean-up-resources"></a>Rensa resurser

Om du har skapat en ny resurs grupp och ett lagrings konto för den här övningen kan yous ta bort alla till gångar som du har skapat genom att ta bort resurs gruppen. Detta tar även bort alla resurser som ingår i gruppen. I det här fallet tar den bort det lagrings konto som skapats och själva resurs gruppen.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```
## <a name="next-steps"></a>Nästa steg

Den här instruktions artikeln täcker vanliga åtgärder med hjälp av hanterings plan-cmdletar för att hantera lagrings konton. Du har lärt dig att:

> [!div class="checklist"]
> * Lista lagrings konton
> * Hämta en referens till ett befintligt lagrings konto
> * skapar ett lagringskonto
> * Ange egenskaper för lagrings konto
> * Hämta och återskapa åtkomst nycklarna
> * Skydda åtkomsten till ditt lagrings konto
> * Aktivera Lagringsanalys

Den här artikeln innehåller även referenser till flera andra artiklar, t. ex. hur du hanterar data objekt, hur du aktiverar Lagringsanalys och hur du kommer åt Azures oberoende moln, till exempel Kina, tyska, molnet och myndighets moln. Här följer några relaterade artiklar och resurser för referens:

* [PowerShell-cmdletar för Azure Storage Control plan](/powershell/module/az.storage/)
* [PowerShell-cmdletar för Azure Storage data plan](/powershell/module/azure.storage/)
* [Windows PowerShell-referens](https://msdn.microsoft.com/library/ms714469.aspx)
