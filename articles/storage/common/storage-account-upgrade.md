---
title: Uppgradera till ett gpv2-lagringskonto – Azure Storage | Microsoft Docs
description: Uppgradera till gpv2-konton.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/28/2019
ms.author: tamram
ms.openlocfilehash: d57023063fe23db9f57d52ab9cdf99e0687c1fdf
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/01/2019
ms.locfileid: "57217299"
---
# <a name="upgrade-to-a-general-purpose-v2-storage-account"></a>Uppgradera till ett gpv2-konto

Generell användning v2-konton stöder de senaste funktionerna i Azure Storage och alla funktioner i allmänna v1 och Blob storage-konton. Gpv2-konton rekommenderas för de flesta lagringsscenarier med. Gpv2-konton leverera lägsta per gigabyte kapacitet priser för Azure Storage, samt bransch konkurrenskraftiga transaktionspriser.

Det är enkelt att uppgradera till ett gpv2-konto från dina allmänna v1- eller Blob storage-konton. Du kan uppgradera med Azure-portalen, PowerShell eller Azure CLI.

> [!IMPORTANT]
> Uppgradera ett v1 storage-konto till gpv2 är permanent och kan inte ångras.

## <a name="upgrade-using-the-azure-portal"></a>Uppgradera med Azure portal

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Navigera till ditt lagringskonto.
3. I den **inställningar** klickar du på **Configuration**.
4. Klicka på **Uppgradera** under **Typ av konto**.
5. Ange namnet på ditt konto under **Bekräfta uppgradering**. 
6. Klicka på **uppgradera** längst ned på bladet.

## <a name="upgrade-with-powershell"></a>Uppgradera med PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Om du vill uppgradera ett general-purpose v1-konto till ett gpv2-konto med hjälp av PowerShell, först uppdatera PowerShell om du vill använda den senaste versionen av den **Az.Storage** modulen. Mer information om hur du installerar PowerShell finns i [Installera och konfigurera Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps). 

Anropa sedan följande kommando för att uppgradera kontot genom att ersätta namnet på resursgruppen och lagringskontot:

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group> -AccountName <storage-account> -UpgradeToStorageV2
```

## <a name="upgrade-with-azure-cli"></a>Uppgradera med Azure CLI

Om du vill uppgradera ett general-purpose v1-konto till ett gpv2-konto med Azure CLI, först installera den senaste versionen av Azure CLI. Information om att installera CLI finns i [Installera Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). 

Anropa sedan följande kommando för att uppgradera kontot genom att ersätta namnet på resursgruppen och lagringskontot:

```cli
az storage account update -g <resource-group> -n <storage-account> --set kind=StorageV2
``` 

## <a name="specify-an-access-tier-for-blob-data"></a>Ange en åtkomstnivå för blob-data

Gpv2-konton stöder alla Azure-lagringstjänster och dataobjekt, men åtkomstnivåerna är endast tillgängligt för blockblobbar i Blob storage. När du uppgraderar till ett gpv2-lagringskonto kan du ange åtkomstnivå för blob-data. 

Åtkomstnivåer kan du välja den mest kostnadseffektiva lagring baserat på ditt förväntade användningsmönster. Blockblob-objekt kan lagras i en frekvent, lågfrekvent eller Arkiv-nivå. Läs mer på åtkomstnivåerna [Azure Blob storage: Frekvent, lågfrekvent, och Arkivlagringsnivån](../blobs/storage-blob-storage-tiers.md).

Som standard skapas ett nytt lagringskonto i frekvent åtkomstnivå och ett lagringskonto i allmänna v1 uppgraderas till frekvent åtkomstnivå. Överväg att ditt scenario om du utforskar vilken åtkomstnivå som ska användas för efter datauppgraderingen. Det finns två vanliga scenarier för att migrera till ett gpv2-konto:

* Du har ett befintligt general-purpose v1-lagringskonto och vill utvärdera en uppgradering till ett gpv2-lagringskonto med rätt lagringsnivå åtkomst för blob-data.
* Du har valt att använda ett gpv2-lagringskonto eller redan har ett och vill utvärdera om du ska använda frekvent eller lågfrekvent åtkomstnivå för blob-data.

I båda fallen är högsta prioritet att beräkna kostnaden för lagring, åtkomst och körs på dina data som lagras i ett gpv2-lagringskonto och jämföra det med dina nuvarande kostnader.

## <a name="pricing-and-billing"></a>Priser och fakturering

Uppgradera ett v1-lagringskonto till ett gpv2-konto är kostnadsfri. Dock kan ändra åtkomstnivån resultera i ändringar till din faktura. 

För alla lagringskonton används en prissättningsmodell för bloblagring som baseras på nivån för varje blob. När du använder ett lagringskonto gäller följande för debitering:

* **Lagringskostnader**: Utöver mängden data som lagras, varierar för att lagra data beroende på åtkomstnivå för lagring. Kostnaden per gigabyte minskas när nivån blir mer lågfrekvent.

* **Kostnader för dataåtkomst**: Öka kostnaderna för dataåtkomst när nivån blir mer lågfrekvent. För data i lågfrekvent lagring och arkivlagring åtkomstnivå debiteras du en per-åtkomst gigabyte för läsningar.

* **Transaktionskostnader**: Det finns en kostnad för alla nivåer per transaktion som ökar när nivån blir mer lågfrekvent.

* **Dataöverföringskostnader för GEO-replikering**: Den här avgiften gäller endast konton med konfigurerad geo-replikering, inklusive GRS och RA-GRS. Dataöverföring för geo-replikering debiteras per gigabyte.

* **Kostnaderna för utgående dataöverföring**: Utgående dataöverföringar (data som överförs från en Azure-region) debiteras för bandbreddsanvändning regelbundet per gigabyte, konsekvent med allmänna lagringskonton.

* **Ändringar av lagringsnivån åtkomst**: Ändra åtkomstnivå för kontot från lågfrekvent till frekvent utgår en avgift motsvarande läsningen av alla data i lagringskontot. Men utgår ändrar åtkomstnivå kontot från frekvent till lågfrekvent en avgift motsvarande Skrivningen av alla data till den lågfrekventa nivån (endast GPv2-konton).

> [!NOTE]
> Mer information om prissättningen för lagringskonton finns på sidan [Pris för Azure Storage](https://azure.microsoft.com/pricing/details/storage/). Mer information om kostnaderna för utgående dataöverföring finns på sidan [Prisinformation om Dataöverföringar](https://azure.microsoft.com/pricing/details/data-transfers/).

### <a name="estimate-costs-for-your-current-usage-patterns"></a>Beräkna kostnader för ditt nuvarande användningsmönster

Om du vill beräkna kostnaden för att lagra och komma åt blobdata i ett gpv2-lagringskonto i en viss nivå måste du utvärdera ditt nuvarande användningsmönster eller göra en uppskattning av ditt förväntade användningsmönster. Vanligtvis vill du veta:

* Ditt Blob storage-användning, i gigabyte, inklusive:
    - Hur mycket data lagras i lagringskontot?
    - Hur ändras datavolymen på månadsbasis; ersätter nya data ständigt gamla data?
* Primär åtkomstmönstret för dina Blob storage-data, inklusive:
    - Hur mycket data läses in och skrivs till storage-kontot? 
    - Hur många läsåtgärder jämfört med skriva utförs på data i storage-konto?

När du ska välja bästa åtkomstnivå för dina behov, kan det vara bra att fastställa din kapacitet för blob-data och hur dessa data används. Detta kan göras bäst genom att titta på övervakning mått för ditt konto.

### <a name="monitoring-existing-storage-accounts"></a>Övervaka befintliga lagringskonton

Om du vill övervaka dina befintliga lagringskonton och samla in dessa data kan du använda Azure-lagringsanalys som utför loggning och tillhandahåller mätvärden för ett lagringskonto. Azure-lagringsanalys kan lagra mätvärden som innehåller aggregerad transaktionsstatistik och kapacitetsdata om förfrågningar till lagringstjänsten för konton av typerna GPv1, GPv2 och Blob Storage. Dessa data lagras i välkända tabeller i samma lagringskonto.

Mer information finns i [Om mätvärden i Storage Analytics](https://msdn.microsoft.com/library/azure/hh343258.aspx) och [Schema över måttabeller i Storage Analytics](https://msdn.microsoft.com/library/azure/hh343264.aspx)

> [!NOTE]
> Blob Storage-konton exponerar endast tabelltjänstens slutpunkt för att lagra och komma åt mätvärden för det aktuella kontot. 

Om du vill övervaka lagringsanvändningen för Blob Storage måste du aktivera kapacitetsmåtten.
När du har gjort det registreras kapacitetsdata varje dag för ett lagringskontos Blob Service och registreras som en tabellpost som skrivs till tabellen *$MetricsCapacityBlob* i samma lagringskonto.

Om du vill övervaka dataåtkomstmönstren för Blob Storage måste du aktivera transaktionsmått för varje timme från API:et. Med transaktionsmått för varje timme aktiverat aggregeras transaktioner för varje API varje timme, och registreras som en tabellpost som skrivs till tabellen *$MetricsHourPrimaryTransactionsBlob* i samma lagringskonto. Tabellen *$MetricsHourSecondaryTransactionsBlob* registrerar transaktionerna till den sekundära slutpunkten när du använder RA-GRS-lagringskonton.

> [!NOTE]
> Om du har ett allmänt lagringskonto (GPvX) där du har lagrat sidblobar och virtuella datordiskar, eller köer, filer eller tabeller, utöver block- och tilläggsblobdata så gäller inte den här uppskattningsberäkningen. Kapacitetsdata gör ingen åtskillnad mellan blockblobar och andra typer och kapacitetsdata visas inte för andra datatyper. Om du använder dessa typer är en alternativ metod att titta på kvantiteterna på din senaste faktura.

För att få en bra uppskattning av din dataförbrukning och ditt åtkomstmönster rekommenderar vi att du väljer en kvarhållningsperiod för mätvärden som är representativ för din normala användning, och att du utgår därifrån. Ett alternativ är att spara mätvärdena i sju dagar och samla in data varje vecka, för att sedan analysera dessa data i slutet av månaden. Ett annat alternativ är att spara mätvärden i 30 dagar och samla in och analysera dessa data i slutet av 30-dagarsperioden.

Mer information om hur du aktiverar, samlar in och visar mätvärden finns i [Aktivera Azure Storage-mätvärden och visa måttdata](../common/storage-enable-and-view-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

> [!NOTE]
> Lagring, åtkomst och hämtning av analysdata debiteras på samma sätt som vanliga användardata.

### <a name="utilizing-usage-metrics-to-estimate-costs"></a>Beräkna kostnader med hjälp av användningsmått

#### <a name="capacity-costs"></a>Kostnader för kapacitet

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
> Ta en titt på Frågeavsnittet för ett mer detaljerat exempel hur du beräknar kostnaderna för frekvent eller lågfrekvent åtkomstnivå *”vad är lågfrekvent och frekvent lagringsnivå och hur avgör jag vilken jag ska använda”?* på [Azure Storage-prissidan](https://azure.microsoft.com/pricing/details/storage/).

## <a name="next-steps"></a>Nästa steg

- [Skapa ett lagringskonto](storage-quickstart-create-account.md)
- [Hantera Azure storage-konton](storage-account-manage.md)
