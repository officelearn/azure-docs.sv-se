---
title: Uppgradera till ett V2-lagringskonto för generell användning
titleSuffix: Azure Storage
description: Uppgradera till virtuella virtuella lagringskonton för allmänt ändamål.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/25/2019
ms.author: tamram
ms.openlocfilehash: 9afbade408d6f95fcd3a61aa1ba65bc09c7a875b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067220"
---
# <a name="upgrade-to-a-general-purpose-v2-storage-account"></a>Uppgradera till ett V2-lagringskonto för generell användning

Generella v2-lagringskonton stöder de senaste Azure Storage-funktionerna och innehåller alla funktioner för generella v1- och Blob-lagringskonton. Allmänna v2-konton rekommenderas för de flesta lagringsscenarier. Generella v2-konton ger de lägsta kapacitetspriserna per gigabyte för Azure Storage samt branschkonkurrella transaktionspriser. Virtuella 2-konton för allmänna ändamål stöder standardåtkomstnivåer för frekvent eller lågfrekvent och blobnivånivånivå mellan frekvent, lågfrekvent eller arkiv.

Det är enkelt att uppgradera till ett allmänt v2-lagringskonto från dina generella v1- eller Blob-lagringskonton. Du kan uppgradera med Azure-portalen, PowerShell eller Azure CLI. Det finns ingen driftstopp eller risk för dataförlust i samband med uppgradering till ett allmänt v2-lagringskonto. Kontouppgraderingen sker via en enkel Azure Resource Manager-åtgärd som ändrar kontotypen.

> [!IMPORTANT]
> Uppgradering av ett allmänt v1- eller Blob-lagringskonto till allmänt v2 är permanent och kan inte ångras.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Navigera till ditt lagringskonto.
3. Klicka på **Konfiguration**i avsnittet **Inställningar** .
4. Klicka på **Uppgradera** under **Typ av konto**.
5. Ange namnet på ditt konto under **Bekräfta uppgradering**.
6. Klicka på **Uppgradera** längst ned på bladet.

    ![Typ av uppgraderingskonto](../blobs/media/storage-blob-account-upgrade/upgrade-to-gpv2-account.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Om du vill uppgradera ett allmänt v1-konto till ett allmänt v2-konto med PowerShell uppdaterar du först PowerShell så att den använder den senaste versionen av **Az.Storage-modulen.** Mer information om hur du installerar PowerShell finns i [Installera och konfigurera Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).

Anropa sedan följande kommando för att uppgradera kontot och ersätta resursgruppsnamnet, lagringskontonamnet och önskad kontoåtkomstnivå.

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group> -AccountName <storage-account> -UpgradeToStorageV2 -AccessTier <Hot/Cool>
```
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill uppgradera ett allmänt v1-konto till ett allmänt v2-konto med Azure CLI installerar du först den senaste versionen av Azure CLI. Information om att installera CLI finns i [Installera Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

Anropa sedan följande kommando för att uppgradera kontot och ersätta resursgruppsnamnet, lagringskontonamnet och önskad kontoåtkomstnivå.

```azurecli
az storage account update -g <resource-group> -n <storage-account> --set kind=StorageV2 --access-tier=<Hot/Cool>
```

---

## <a name="specify-an-access-tier-for-blob-data"></a>Ange en åtkomstnivå för blob-data

Virtuella 2-konton för allmänna ändamål stöder alla Azure-lagringstjänster och dataobjekt, men åtkomstnivåer är endast tillgängliga för blockblobar i Blob-lagring. När du uppgraderar till ett allmänt v2-lagringskonto kan du ange en standardåtkomstnivå för konto för frekvent eller lågfrekvent, vilket indikerar att standardnivån som dina blob-data kommer att överföras som om den enskilda parametern blob access tier inte har angetts.

Med åtkomstnivåer för blob kan du välja den mest kostnadseffektiva lagringen baserat på dina förväntade användningsmönster. Blockblobar kan lagras på en frekvent, lågfrekvent eller arkivnivå. Mer information om åtkomstnivåer finns i [Azure Blob storage: Hot, Cool och Archive storage tiers](../blobs/storage-blob-storage-tiers.md).

Som standard skapas ett nytt lagringskonto på hot access-nivån och ett allmänt v1-lagringskonto kan uppgraderas till antingen den aktiva eller den svala kontonivån. Om en kontoåtkomstnivå inte anges vid uppgraderingen uppgraderas den till hett som standard. Om du undersöker vilken åtkomstnivå som ska användas för uppgraderingen bör du tänka på ditt aktuella scenario för dataanvändning. Det finns två typiska användarscenarier för att migrera till ett v2-konto för allmänt syfte:

* Du har ett befintligt allmänt v1-lagringskonto och vill utvärdera en uppgradering till ett allmänt v2-lagringskonto med rätt lagringsåtkomstnivå för blob-data.
* Du har bestämt dig för att använda ett allmänt v2-lagringskonto eller redan har ett och vill utvärdera om du ska använda åtkomstnivån för frekvent eller lågfrekvent lagring för blob-data.

I båda fallen är den första prioriteringen att uppskatta kostnaden för att lagra, komma åt och driva dina data som lagras i ett allmänt v2-lagringskonto och jämföra det med dina aktuella kostnader.

## <a name="pricing-and-billing"></a>Priser och fakturering

Det är gratis att uppgradera ett v1-lagringskonto till ett allmänt v2-konto. Du kan ange önskad kontonivå under uppgraderingsprocessen. Om en kontonivå inte anges vid uppgraderingen kommer standardkontonivån `Hot`för det uppgraderade kontot att vara . Om du ändrar lagringsåtkomstnivån efter uppgraderingen kan det dock leda till ändringar i fakturan, så det rekommenderas att du anger den nya kontonivån under uppgraderingen.

För alla lagringskonton används en prissättningsmodell för bloblagring som baseras på nivån för varje blob. När du använder ett lagringskonto gäller följande för debitering:

* **Lagringskostnader**: Förutom mängden data som lagras varierar kostnaden för att lagra data beroende på lagringsåtkomstnivån. Kostnaden per gigabyte minskas när nivån blir mer lågfrekvent.

* **Kostnader för dataåtkomst**: Kostnaderna för dataåtkomst ökar när nivån blir mer lågfrekvent. För data på åtkomstnivån för kyl och arkivlagring debiteras du en dataåtkomstavgift per gigabyte för läsningar.

* **Transaktionskostnader**: Du debiteras en kostnad per transaktion för alla nivåer som ökar när nivån blir mer lågfrekvent.

* **Dataöverföringskostnader för geo-replikering**: Den här avgiften gäller endast konton med konfigurerad geo-replikering, inklusive GRS och RA-GRS. Dataöverföring för geo-replikering debiteras per gigabyte.

* **Kostnader för utgående dataöverföring**: Utgående dataöverföringar (data som överförs utanför en Azure-region) debiteras för bandbreddsanvändning per gigabyte, på samma sätt som för allmänna lagringskonton.

* **Ändra lagringsåtkomstnivån:** Om du ändrar åtkomstnivån för kontolagring från sval till frekvent medför en avgift som är lika med att läsa alla data som finns i lagringskontot. Men om du ändrar kontoåtkomstnivån från frekvent till lågfrekvent medför en avgift som är lika med att skriva alla data till den coola nivån (endast GPv2-konton).

> [!NOTE]
> Mer information om prissättningen för lagringskonton finns på sidan [Pris för Azure Storage](https://azure.microsoft.com/pricing/details/storage/). Mer information om kostnaderna för utgående dataöverföring finns på sidan [Prisinformation om Dataöverföringar](https://azure.microsoft.com/pricing/details/data-transfers/).

### <a name="estimate-costs-for-your-current-usage-patterns"></a>Uppskatta kostnader för dina aktuella användningsmönster

Om du vill uppskatta kostnaden för att lagra och komma åt blob-data i ett allmänt v2-lagringskonto på en viss nivå utvärderar du ditt befintliga användningsmönster eller approximerar ditt förväntade användningsmönster. Vanligtvis vill du veta:

* Din Blob lagringsförbrukning, i gigabyte, inklusive:
  * Hur mycket data lagras i lagringskontot?
  * Hur ändras datavolymen på månadsbasis; ersätter nya data ständigt gamla data?

* Det primära åtkomstmönstret för blob-lagringsdata, inklusive:
  * Hur mycket data läss från och skrivs till lagringskontot?
  * Hur många läsåtgärder jämfört med skrivåtgärder inträffar på data i lagringskontot?

Om du vill bestämma den bästa åtkomstnivån för dina behov kan det vara bra att bestämma din blob-datakapacitet och hur dessa data används. Detta kan bäst göras genom att titta på övervakningsmått för ditt konto.

### <a name="monitoring-existing-storage-accounts"></a>Övervaka befintliga lagringskonton

Om du vill övervaka dina befintliga lagringskonton och samla in dessa data kan du använda Azure-lagringsanalys som utför loggning och tillhandahåller mätvärden för ett lagringskonto. Azure-lagringsanalys kan lagra mätvärden som innehåller aggregerad transaktionsstatistik och kapacitetsdata om förfrågningar till lagringstjänsten för konton av typerna GPv1, GPv2 och Blob Storage. Dessa data lagras i välkända tabeller i samma lagringskonto.

Mer information finns i [Om mätvärden i Storage Analytics](https://msdn.microsoft.com/library/azure/hh343258.aspx) och [Schema över måttabeller i Storage Analytics](https://msdn.microsoft.com/library/azure/hh343264.aspx)

> [!NOTE]
> Blob Storage-konton exponerar endast tabelltjänstens slutpunkt för att lagra och komma åt mätvärden för det aktuella kontot.

Om du vill övervaka lagringsanvändningen för Blob Storage måste du aktivera kapacitetsmåtten.
Med detta aktiverat registreras kapacitetsdata dagligen för ett lagringskontos Blob-tjänst och registreras som en tabellpost som skrivs till *$MetricsCapacityBlob* tabellen i samma lagringskonto.

Om du vill övervaka dataåtkomstmönstren för Blob Storage måste du aktivera transaktionsmått för varje timme från API:et. Med transaktionsmått för varje timme aktiverat aggregeras transaktioner för varje API varje timme, och registreras som en tabellpost som skrivs till tabellen *$MetricsHourPrimaryTransactionsBlob* i samma lagringskonto. Tabellen *$MetricsHourSecondaryTransactionsBlob* registrerar transaktionerna till den sekundära slutpunkten när du använder RA-GRS-lagringskonton.

> [!NOTE]
> Om du har ett allmänt lagringskonto (GPvX) där du har lagrat sidblobar och virtuella datordiskar, eller köer, filer eller tabeller, utöver block- och tilläggsblobdata så gäller inte den här uppskattningsberäkningen. Kapacitetsdata gör ingen åtskillnad mellan blockblobar och andra typer och kapacitetsdata visas inte för andra datatyper. Om du använder dessa typer är en alternativ metod att titta på kvantiteterna på din senaste faktura.

För att få en bra uppskattning av din dataförbrukning och ditt åtkomstmönster rekommenderar vi att du väljer en kvarhållningsperiod för mätvärden som är representativ för din normala användning, och att du utgår därifrån. Ett alternativ är att spara mätvärdena i sju dagar och samla in data varje vecka, för att sedan analysera dessa data i slutet av månaden. Ett annat alternativ är att spara mätvärden i 30 dagar och samla in och analysera dessa data i slutet av 30-dagarsperioden.

Mer information om hur du aktiverar, samlar in och visar mätvärden finns i [Mätvärden för lagringsanalys](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

> [!NOTE]
> Lagring, åtkomst och hämtning av analysdata debiteras på samma sätt som vanliga användardata.

### <a name="utilizing-usage-metrics-to-estimate-costs"></a>Beräkna kostnader med hjälp av användningsmått

#### <a name="capacity-costs"></a>Kapacitetskostnader

Den senaste posten i kapacitetsmåttstabellen *$MetricsCapacityBlob* med radnyckeln *'data'* visar lagringskapacitet som förbrukats av användardata. Den senaste posten i kapacitetsmåttstabellen *$MetricsCapacityBlob* med radnyckeln *'analytics'* visar lagringskapaciteten som förbrukats av analysloggarna.

Den här totala kapaciteten som förbrukats av både användardata och analysloggar (om detta aktiverats) kan sedan användas för att beräkna kostnaden för datalagring i lagringskontot. Samma metod kan även användas för att uppskatta lagringskostnader för GPv1-konton.

#### <a name="transaction-costs"></a>Transaktionskostnader

Summan av *'TotalBillableRequests'*, för alla poster för ett API i tabellen över transaktionsmått, visar det totala antalet transaktioner för API:et i fråga. *Till exempel* kan det totala antalet *'GetBlob'*-transaktioner inom en viss period beräknas baserat på summan av totalt antal debiterbara begäranden för alla poster med radnyckeln *'user;GetBlob'*.

För att kunna beräkna transaktionskostnader för Blob Storage-konton måste du dela in transaktionerna i tre grupper eftersom de har olika pris.

* Skrivtransaktioner som *'PutBlob'*, *'PutBlock'*, *'PutBlockList'*, *'AppendBlock'*, *'ListBlobs'*, *'ListContainers'*, *'CreateContainer'*, *'SnapshotBlob'* och *'CopyBlob'*.
* Borttagningstransaktioner som *'DeleteBlob'* och *'DeleteContainer'*.
* Alla andra transaktioner.

För att kunna beräkna transaktionskostnaderna för GPv1-konton måste du aggregera alla transaktioner oavsett åtgärd/API.

#### <a name="data-access-and-geo-replication-data-transfer-costs"></a>Dataöverföringskostnader för dataåtkomst och geo-replikering

Lagringsanalyserna visar inte mängden data som har lästs eller skrivits till ett lagringskonto, men du kan göra en ungefärlig uppskattning genom att titta i tabellen över transaktionsmått. Summan av *'TotalIngress'* för alla poster för ett API i tabellen över transaktionsmått anger den totala mängden inkommande data i antal byte för API:et i fråga. På samma sätt anger summan av *'TotalEgress'* den totala mängden utgående data i antal byte.

För att kunna beräkna kostnaderna för dataåtkomst för Blob Storage-konton måste du dela in transaktionerna i två grupper.

* Du kan beräkna mängden data som hämtas från lagringskontot genom att titta på summan av *'TotalEgress'* och särskilt för åtgärderna *'GetBlob'* och *'CopyBlob'*.

* Du kan beräkna mängden data som skrivs till lagringskontot genom att titta på summan av *'TotalIngress'* och särskilt för åtgärderna *'PutBlob'*, *'PutBlock'*, *'CopyBlob'* och *'AppendBlock'*.

När du använder ett GRS- eller RA-GRS-lagringskonto kan kostnaden för dataöverföring med geo-replikering för Blob Storage-konton också beräknas baserat på uppskattningen av mängden data som skrivits.

> [!NOTE]
> För ett mer detaljerat exempel om beräkning av kostnaderna för att använda den heta eller svala lagringsåtkomstnivån, ta en titt på FAQ med titeln *"Vad är hot och cool åtkomstnivåer och hur ska jag avgöra vilken som ska användas?"* på [Azure Storage-prissidan](https://azure.microsoft.com/pricing/details/storage/).

## <a name="next-steps"></a>Nästa steg

* [skapar ett lagringskonto](storage-account-create.md)
