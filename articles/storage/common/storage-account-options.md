---
title: Alternativ för Azure Storage-konton | Microsoft Docs
description: Information om alternativ för Azure Storage.
services: storage
author: xyh1
manager: jwillis
ms.service: storage
ms.workload: storage
ms.topic: get-started-article
ms.date: 06/22/2018
ms.author: hux
ms.openlocfilehash: 3f1dfa09c0f123d20a7be043aa8d0033a5b6bd72
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "36335779"
---
# <a name="azure-storage-account-options"></a>Alternativ för Azure Storage-konton

## <a name="overview"></a>Översikt
Azure Storage tillhandahåller tre olika kontoalternativ, med olika priser och olika funktioner som stöds. Fundera på de här skillnaderna innan du skapar ett lagringskonto för att komma fram till vilket alternativ som passar bäst för dina behov. Dessa är de tre alternativen för lagringskonton:

* **GPv2**-konton (General-purpose v2) 
* **GPv1**-konton (General-purpose v1)
* **Blob Storage**-konton

Varje typ av konto beskrivs mer detaljerat i avsnittet nedan:

## <a name="storage-account-options"></a>Alternativ för lagringskontot

### <a name="general-purpose-v2"></a>General-purpose v2

GPv2-konton (General-purpose v2) är lagringskonton som stöder alla de senaste funktionerna för blobar, filer, köer och tabeller. GPv2-konton stöder alla API: er och funktioner som stöds av GPv1- och Blob Storage-konton. De har också stöd för samma hållbarhets-, tillgänglighets-, skalbarhets- och prestandafunktioner i dessa kontotyper. Prisstrukturen för GPv2-konton har utformats med låga gigabytepriser och konkurrenskraftiga transaktionspriser.

Du kan uppgradera ditt GPv1- eller Blob Storage-konto till ett GPv2-konto med Azure-portalen, PowerShell eller Azure CLI. 

För blockblobar i ett GPv2-lagringskonto kan du ange frekvent och lågfrekvent lagringsnivå på kontonivå, eller ange frekvent eller lågfrekvent nivå eller arkivnivå på blobnivå, baserat på åtkomstmönster. Lagra data som sällan, mer sällan eller ofta används på lagringsnivåerna för frekvent eller lågfrekvent åtkomst eller arkivering för att optimera kostnaderna. 

I GPv2-konton visas attributet **Åtkomstnivå** på kontonivå, vilket innebär att standardlagringsnivån för kontot kan anges till **Frekvent** eller **Lågfrekvent**. Lagringskontonivån som är standard tillämpas på alla blobar som inte har en explicit nivå angiven på blobnivån. Du kan när som helst byta mellan de olika lagringsnivåerna om användningsmönstret förändras. **Arkivnivån** kan endast användas på blobnivån.

> [!NOTE]
> Ändringar av lagringsnivån kan medför ytterligare avgifter. Mer information finns i avsnittet [Priser och fakturering](#pricing-and-billing).
>
> Microsoft rekommenderar att du väljer GPv2-lagringskonton framför Blob Storage-konton för de flesta scenarier.

### <a name="upgrade-a-storage-account-to-gpv2"></a>Uppgradera ett lagringskonto till GPv2

Användare kan när som helst uppgradera ett GPv1- eller Blob Storage-konto till ett GPv2-konto via PowerShell eller Azure CLI. Den här ändringen kan inte ångras och inga andra ändringar tillåts.

#### <a name="upgrade-with-powershell"></a>Uppgradera med PowerShell

Om du vill uppgradera ett GPv1- eller Blob Storage-konto till ett GPv2-konto med hjälp av PowerShell ska du först uppdatera PowerShell så att du använder den senaste versionen av modulen **AzureRm.Storage**. Mer information om hur du installerar PowerShell finns i [Installera och konfigurera Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). Anropa sedan följande kommando för att uppgradera kontot genom att ersätta namnet på resursgruppen och lagringskontot:

```powershell
Set-AzureRmStorageAccount -ResourceGroupName <resource-group> -AccountName <storage-account> -UpgradeToStorageV2
```

#### <a name="upgrade-with-azure-cli"></a>Uppgradera med Azure CLI

Om du vill uppgradera ett GPv1- eller Blob Storage-konto till ett GPv2-konto med Azure CLI ska du först installera den senaste versionen av Azure CLI. Information om att installera CLI finns i [Installera Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Anropa sedan följande kommando för att uppgradera kontot genom att ersätta namnet på resursgruppen och lagringskontot:

```cli
az storage account update -g <resource-group> -n <storage-account> --set kind=StorageV2
```` 

### <a name="general-purpose-v1"></a>General-purpose v1

GPv1-konton (General-purpose v1) ger åtkomst till alla Azure Storage-tjänster, men erbjuder kanske inte de senaste funktionerna eller det lägsta gigabytepriset. Lågfrekvent lagring och arkivlagring är två exempel på funktioner som inte stöds av GPv1. Transaktionspriserna är lägre för GPPv1, så arbetsbelastningar med hög omsättning eller många läsåtgärder kan ha nytta av den här kontotypen.

GPv1-lagringskonton (General Purpose v1) är den äldsta typen av lagringskonto, och det är endast denna typ som kan användas i den klassiska distributionsmodellen. 

### <a name="blob-storage-accounts"></a>Blob Storage-konton

Blob Storage-konton stöder samma funktioner för blockblobar som GPv2, men stödet är begränsat till blockblobar. Prisstrukturen liknar den som används för GPv2-konton. Alla kunder bör granska prisskillnaderna mellan Blob Storage- och GPv2-konton, och överväga att uppgradera till GPv2. Uppgraderingen kan inte ångras.

> [!NOTE]
> Blob Storage-konton stöder endast block- och tilläggsblobar, inte sidblobar.
>
> Microsoft rekommenderar att du väljer GPv2-lagringskonton framför Blob Storage-konton för de flesta scenarier.

## <a name="recommendations"></a>Rekommendationer

Mer information om lagringskonton finns i [Om Azure Storage-konton](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

För program som kräver de senaste block- eller tilläggsblobfunktionerna rekommenderar vi att du använder GPv2-konton så att du kan dra nytta av de olika prissättningsmodellerna för olika lagringsnivåer. Dock kanske du vill använda GPv1 i vissa fall, till exempel:

* Du måste fortfarande använda den klassiska distributionsmodellen. GPv2 och Blob Storage-konton är bara tillgängliga via Azure Resource Manager-distributionsmodellen.
* Du har stora transaktionsvolymer eller behöver stor bandbredd för geo-replikering, vilket ger högre kostnader med GPv2- och Blob Storage-konton än med GPv1, samtidigt som du inte har tillräckligt med lagringsutrymme för att kunna dra nytta av de lägre kostnaderna för GB-lagring.
* Du använder en version av [Storage Services REST API](https://msdn.microsoft.com/library/azure/dd894041.aspx) som är äldre än 2014-02-14 eller ett klientbiblioteket med en tidigare version än 4.x och det inte går att uppgradera ditt program.

## <a name="pricing-and-billing"></a>Priser och fakturering
För alla lagringskonton används en prissättningsmodell för bloblagring som baseras på nivån för varje blob. När du använder ett lagringskonto gäller följande för debitering:

* **Lagringskostnader**: Utöver mängden data som lagras varierar lagringskostnaden beroende på lagringsnivå. Kostnaden per gigabyte minskas när nivån blir mer lågfrekvent.
* **Kostnader för dataåtkomst**: Kostnaderna för dataåtkomst ökar när nivån blir mer lågfrekvent. För data på den lågfrekventa- och arkivlagringsnivån debiteras du en åtkomstavgift per gigabyte för läsningar.
* **Transaktionskostnader**: Du debiteras en kostnad per transaktion för alla nivåer som ökar när nivån blir mer lågfrekvent.
* **Dataöverföringskostnader för geo-replikering**: Den här avgiften gäller endast konton med konfigurerad geo-replikering, inklusive GRS och RA-GRS. Dataöverföring för geo-replikering debiteras per gigabyte.
* **Kostnader för utgående dataöverföring**: Utgående dataöverföringar (data som överförs utanför en Azure-region) debiteras för bandbreddsanvändning per gigabyte, på samma sätt som för allmänna lagringskonton.
* **Ändring av lagringsnivå**: Om du byter lagringskontonivå från lågfrekvent till frekvent utgår en avgift motsvarande läsningen av alla data på lagringskontot. Om du byter lagringskontonivå från frekvent till lågfrekvent utgår dock en avgift som motsvarar skrivningen av alla data till den lågfrekventa nivån (gäller endast GPv2-konton).

> [!NOTE]
> Mer information om prissättningen för lagringskonton finns på sidan [Pris för Azure Storage](https://azure.microsoft.com/pricing/details/storage/). Mer information om kostnaderna för utgående dataöverföring finns på sidan [Prisinformation om Dataöverföringar](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="quickstart-scenarios"></a>Snabbstartsscenarier

I det här avsnittet visas följande scenarier på Azure Portal:

* [Skapa ett GPv2-lagringskonto.](#create-a-gpv2-storage-account-using-the-azure-portal)
* [Konvertera ett GPv1- eller Blob Storage-konto till ett GPv2-lagringskonto.](#convert-a-gpv1-or-blob-storage-account-to-a-gpv2-storage-account-using-the-azure-portal)
* [Så här anger du kontonivå i ett GPv2- eller Blob Storage-konto.](#change-the-storage-tier-of-a-gpv2-storage-account-using-the-azure-portal)
* [Så här anger du blobnivå i ett GPv2- eller Blob Storage-konto.](#change-the-storage-tier-of-a-blob-using-the-azure-portal)

Du kan inte ange arkiv som åtkomstnivå i följande exempel eftersom den här inställningen gäller för hela lagringskontot. Arkiv kan bara anges för en specifik blob.

### <a name="create-a-gpv2-storage-account-using-the-azure-portal"></a>Skapa ett GPv2-lagringskonto med Azure Portal

1. Logga in på [Azure Portal](https://portal.azure.com).

2. I navmenyn väljer du **Skapa en resurs** > **Data + lagring** > **Lagringskonto**.

3. Ange ett namn för lagringskontot.

    Det här namnet måste vara globalt unikt. Det används som en del av URL:en som används för att få åtkomst till objekt i lagringskontot.  

4. Välj **Resource Manager** som distributionsmodell.

    Nivåindelad lagring kan bara användas med Resource Manager-lagringskonton. Resource Manager är den rekommenderade distributionsmodellen för nya resurser. Mer information finns i [översikten över Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).  

5. I listrutan **Typ av konto** väljer du **General-purpose v2**.

    När du väljer GPv2 anges prestandanivån till Standard. Nivåindelad lagring är inte tillgängligt med Premium-prestandanivån.

6. Välj replikeringsalternativ för lagringskontot: **LRS**, **ZRS**, **GRS** eller **RA-GRS**. Standardinställningen är **RA-GRS**.

    LRS = lokalt redundant lagring. GRS = geo-redundant lagring (två regioner). RA-GRS = skrivskyddad geo-redundant lagring (två regioner med läsbehörighet till den andra).

    Mer information om replikeringsalternativen för Azure Storage finns i [Azure Storage-replikering](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

7. Välj rätt lagringsnivå för dina behov: Ange **Åtkomstnivå** till antingen **Cool** (lågfrekvent) eller **Hot** (frekvent). Standardinställningen är **Frekvent**.

8. Välj den prenumeration som du vill skapa det nya lagringskontot i.

9. Ange en ny resursgrupp eller välj en befintlig resursgrupp. Mer information om resursgrupper finns i [Översikt över Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).

10. Välj regionen för ditt lagringskonto.

11. Skapa lagringskontot genom att klicka på **Skapa**.

### <a name="convert-a-gpv1-or-blob-storage-account-to-a-gpv2-storage-account-using-the-azure-portal"></a>Konvertera ett GPv1- eller Blob Storage-konto till ett GPv2-lagringskonto med Azure Portal

1. Logga in på [Azure Portal](https://portal.azure.com).

2. Så här går du till ditt lagringskonto: välj **Alla resurser**, välj sedan ditt lagringskonto.

3. Klicka på **Konfiguration** i avsnittet Inställningar.

4. Klicka på **Uppgradera** under **Typ av konto**.

5. Ange namnet på ditt konto under **Bekräfta uppgradering**. 

5. Klicka på Uppgradera längst ned på bladet.

### <a name="change-the-storage-tier-of-a-gpv2-storage-account-using-the-azure-portal"></a>Ändra lagringsnivån för ett GPv2-konto med hjälp av Azure Portal

1. Logga in på [Azure Portal](https://portal.azure.com).

2. Så här går du till ditt lagringskonto: välj **Alla resurser**, välj sedan ditt lagringskonto.

3. Klicka på **Konfiguration** på bladet Inställningar för att visa och/eller ändra kontokonfigurationen.

4. Välj rätt lagringsnivå för dina behov: Ange **Åtkomstnivå** till antingen **Cool** (lågfrekvent) eller **Hot** (frekvent).

5. Klicka på Spara överst på bladet.

### <a name="change-the-storage-tier-of-a-blob-using-the-azure-portal"></a>Ändra lagringsnivån för en blob med hjälp av Azure Portal

1. Logga in på [Azure Portal](https://portal.azure.com).

2. Så här går du till din blob i ditt lagringskonto: välj **Alla resurser**, välj ditt lagringskonto, välj din behållare och välj sedan din blob.

3. På bladet Blob-egenskaper klickar du på listrutemenyn **Åtkomstnivå** för att välja lagringsnivåerna **Frekvent**, **Lågfrekvent**, eller **Arkiv**.

5. Klicka på Spara överst på bladet.

> [!NOTE]
> Ändringar av lagringsnivån kan medför ytterligare avgifter. Mer information finns i avsnittet [Priser och fakturering](#pricing-and-billing).


## <a name="evaluating-and-migrating-to-gpv2-storage-accounts"></a>Utvärdera och migrera till GPv2-konton
Avsikten med det här avsnittet är att hjälpa användare med övergången till GPv2-lagringskonton från GPv1-lagringskonton. Det finns två scenarier:

* Du har ett befintligt GPv1-konto och vill utvärdera en övergång till ett GPv2-konto med rätt lagringsnivå.
* Du har bestämt dig för att använda ett GPv2-konto eller har redan ett sådant, och vill utvärdera om du ska använda lågfrekvent eller frekvent lagringsnivå.

I båda fallen bör du börja med att beräkna kostnaden för att lagra och komma åt dina data i GPv2-kontot och jämföra det med dina nuvarande kostnader.

## <a name="evaluating-gpv2-storage-account-tiers"></a>Utvärdera GPv2-kontonivåer

För att beräkna kostnaden för att lagra och komma åt data som lagras i ett GPv2-konto måste du utvärdera ditt nuvarande användningsmönster eller göra en uppskattning av ditt förväntade användningsmönster. Vanligtvis vill du veta:

* Din lagringsanvändning – hur mycket data lagras och hur ändras lagringen månadsvis?
* Ditt åtkomstmönster – hur mycket data läses in och skrivs till kontot (inklusive nya data)? Hur många transaktioner används för dataåtkomst och vilka typer av transaktioner är de?

## <a name="monitoring-existing-storage-accounts"></a>Övervaka befintliga lagringskonton

Om du vill övervaka dina befintliga lagringskonton och samla in dessa data kan du använda Azure-lagringsanalys som utför loggning och tillhandahåller mätvärden för ett lagringskonto. Azure-lagringsanalys kan lagra mätvärden som innehåller aggregerad transaktionsstatistik och kapacitetsdata om förfrågningar till lagringstjänsten för konton av typerna GPv1, GPv2 och Blob Storage. Dessa data lagras i välkända tabeller i samma lagringskonto.

Mer information finns i [Om mätvärden i Storage Analytics](https://msdn.microsoft.com/library/azure/hh343258.aspx) och [Schema över måttabeller i Storage Analytics](https://msdn.microsoft.com/library/azure/hh343264.aspx)

> [!NOTE]
> Blob Storage-konton exponerar endast tabelltjänstens slutpunkt för att lagra och komma åt mätvärden för det aktuella kontot. 

Om du vill övervaka lagringsanvändningen för Blob Storage måste du aktivera kapacitetsmåtten.
När du har gjort det registreras kapacitetsdata varje dag för ett lagringskontos Blob Service och registreras som en tabellpost som skrivs till tabellen *$MetricsCapacityBlob* i samma lagringskonto.

Om du vill övervaka dataåtkomstmönstren för Blob Storage måste du aktivera transaktionsmått för varje timme från API:et. Med transaktionsmått för varje timme aktiverat aggregeras transaktioner för varje API varje timme, och registreras som en tabellpost som skrivs till tabellen *$MetricsHourPrimaryTransactionsBlob* i samma lagringskonto. Tabellen *$MetricsHourSecondaryTransactionsBlob* registrerar transaktionerna till den sekundära slutpunkten när du använder RA-GRS-lagringskonton.

> [!NOTE]
> Om du har ett allmänt lagringskonto (GPvX) med lagrade sidblobar och virtuella datordiskar, eller köer, filer eller tabeller, utöver block- och tilläggsblobdata så gäller inte den här uppskattningsberäkningen. Kapacitetsdata gör ingen åtskillnad mellan blockblobar och andra typer och kapacitetsdata visas inte för andra datatyper. Om du använder dessa typer är en alternativ metod att titta på kvantiteterna på din senaste faktura.

För att få en bra uppskattning av din dataförbrukning och ditt åtkomstmönster rekommenderar vi att du väljer en kvarhållningsperiod för mätvärden som är representativ för din normala användning, och att du utgår därifrån. Ett alternativ är att spara mätvärdena i sju dagar och samla in data varje vecka, för att sedan analysera dessa data i slutet av månaden. Ett annat alternativ är att spara mätvärden i 30 dagar och samla in och analysera dessa data i slutet av 30-dagarsperioden.

Mer information om hur du aktiverar, samlar in och visar mätvärden finns i [Aktivera Azure Storage-mätvärden och visa måttdata](../common/storage-enable-and-view-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

> [!NOTE]
> Lagring, åtkomst och hämtning av analysdata debiteras på samma sätt som vanliga användardata.

### <a name="utilizing-usage-metrics-to-estimate-costs"></a>Beräkna kostnader med hjälp av användningsmått

#### <a name="storage-costs"></a>Lagringskostnader

Den senaste posten i kapacitetsmåttstabellen *$MetricsCapacityBlob* med radnyckeln *'data'* visar lagringskapacitet som förbrukats av användardata. Den senaste posten i kapacitetsmåttstabellen *$MetricsCapacityBlob* med radnyckeln *'analytics'* visar lagringskapaciteten som förbrukats av analysloggarna.

Den här totala kapaciteten som förbrukats av både användardata och analysloggar (om detta aktiverats) kan sedan användas för att beräkna kostnaden för datalagring i lagringskontot. Samma metod kan även användas för att uppskatta lagringskostnader för GPv1-konton.

#### <a name="transaction-costs"></a>Transaktionskostnader

Summan av *'TotalBillableRequests'*, för alla poster för ett API i tabellen över transaktionsmått, visar det totala antalet transaktioner för API:et i fråga. *Till exempel* kan det totala antalet *'GetBlob'*-transaktioner inom en viss period beräknas baserat på summan av totalt antal debiterbara begäranden för alla poster med radnyckeln *'user;GetBlob'*.

För att kunna beräkna transaktionskostnader för Blob Storage-konton måste du dela in transaktionerna i tre grupper eftersom de har olika pris.

* Skrivtransaktioner som *'PutBlob'*, *'PutBlock'*, *'PutBlockList'*, *'AppendBlock'*, *'ListBlobs'*, *'ListContainers'*, *'CreateContainer'*, *'SnapshotBlob'* och *'CopyBlob'*.
* Borttagningstransaktioner som *'DeleteBlob'* och *'DeleteContainer'*.
* Alla andra transaktioner.

För att kunna beräkna transaktionskostnaderna för GPv1-konton måste du aggregera alla transaktioner oavsett åtgärd/API.

### <a name="data-access-and-geo-replication-data-transfer-costs"></a>Dataöverföringskostnader för dataåtkomst och geo-replikering

Lagringsanalyserna visar inte mängden data som har lästs eller skrivits till ett lagringskonto, men du kan göra en ungefärlig uppskattning genom att titta i tabellen över transaktionsmått. Summan av *'TotalIngress'* för alla poster för ett API i tabellen över transaktionsmått anger den totala mängden inkommande data i antal byte för API:et i fråga. På samma sätt anger summan av *'TotalEgress'* den totala mängden utgående data i antal byte.

För att kunna beräkna kostnaderna för dataåtkomst för Blob Storage-konton måste du dela in transaktionerna i två grupper:

* Du kan beräkna mängden data som hämtas från lagringskontot genom att titta på summan av *'TotalEgress'* och särskilt för åtgärderna *'GetBlob'* och *'CopyBlob'*.
* Du kan beräkna mängden data som skrivs till lagringskontot genom att titta på summan av *'TotalIngress'* och särskilt för åtgärderna *'PutBlob'*, *'PutBlock'*, *'CopyBlob'* och *'AppendBlock'*.

När du använder ett GRS- eller RA-GRS-lagringskonto kan kostnaden för dataöverföring med geo-replikering för Blob Storage-konton också beräknas baserat på uppskattningen av mängden data som skrivits.

> [!NOTE]
> Ett mer detaljerat exempel på hur du beräknar kostnaderna för den frekventa eller lågfrekventa lagringsnivån finns i frågeavsnittet *”Vad är lågfrekvent och frekvent lagringsnivå och hur vet jag vilken jag ska använda?”* på [Azure Storage-prissidan](https://azure.microsoft.com/pricing/details/storage/).

## <a name="migrating-existing-data"></a>Migrera befintliga data

Ett GPv1-konto kan enkelt uppgraderas till GPv2 utan driftavbrott eller API-ändringar, och utan att du behöver migrera data. Därför rekommenderar starkt att du migrerar GPv1-konton till GPv2-konton istället för till Blob Storage-konton.

Om du behöver migrera till ett Blob Storage-konto och inte kan använda GPv2-konton, kan du använda instruktionerna nedan. 

Ett Blob Storage-konto är specialanpassat för lagring av endast block- och tilläggsblobar. Allmänna lagringskonton, där du kan lagra tabeller, köer, filer och diskar, och även blobbar, kan inte omvandlas till Blob Storage-konton. Om du vill använda lagringsnivåer måste du skapa Blob Storage-konton och migrera dina befintliga data till de nya kontona. 

Med följande metoder kan du migrera befintliga data till Blob Storage-konton från lokala lagringsenheter, från externa molnlagringsleverantörer eller från befintliga allmänna lagringskonton i Azure:

### <a name="azcopy"></a>AzCopy

AzCopy är ett Windows-kommandoradsverktyg för högpresterande kopiering av data till och från Azure Storage. Med AzCopy kan du kopiera data till ditt Blob Storage-konto från dina befintliga allmänna lagringskonton eller överföra data från dina lokala lagringsenheter till ditt Blob Storage-konto.

Mer information finns i [Överföra data med kommandoradsverktyget AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="data-movement-library"></a>Bibliotek för flytt av data

Azure Storage-biblioteket för flytt av data för .NET är baserat på det ramverk för flytt av data som används för AzCopy. Biblioteket är utformat för högpresterande, tillförlitliga och enkla åtgärder för dataöverföring och liknar de som används i AzCopy. Du kan använda det för att utnyttja fördelarna med funktionerna som tillhandahålls av AzCopy direkt i din app utan att du behöver köra och övervaka externa instanser av AzCopy.

Mer information finns i [Azure Storage Data Movement Library for .Net](https://github.com/Azure/azure-storage-net-data-movement)

### <a name="rest-api-or-client-library"></a>REST-API eller klientbibliotek

Du kan skapa en anpassad app för att migrera dina data till ett Blob Storage-konto med Azures klientbibliotek eller REST-API:t för Azure Storage-tjänster. Azure Storage innehåller omfattande klientbibliotek för flera språk och plattformar som .NET, Java, C++, Node.JS, PHP, Ruby och Python. Klientbiblioteken har avancerade funktioner, t.ex. logik för omprövning, loggning och parallell överföring. Du kan också utveckla direkt mot REST-API:t, som kan anropas med valfritt språk som kan skicka HTTP/HTTPS-begäranden.

Mer information finns i [Komma igång med Azure Blob Storage](../blobs/storage-dotnet-how-to-use-blobs.md).

> [!IMPORTANT]
> Blobar som krypteras med kryptering på klientsidan lagrar krypteringsrelaterade metadata tillsammans med bloben. Om du kopierar en blob som är krypterad med kryptering på klientsidan bör du se till att kopieringen bevarar blobmetadata och framför allt krypteringsrelaterade metadata. Om du kopierar en blob utan krypteringsmetadata kan blobinnehållet inte hämtas igen. Mer information om krypteringsrelaterade metadata finns i [Azure Storage Client Side Encryption](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

**Är de befintliga lagringskontona fortfarande tillgängliga?**

Ja, de befintliga lagringskontona (GPv1) är fortfarande tillgängliga. Varken priserna eller funktionerna har ändrats. Du kan inte välja lagringsnivå med GPv1-konton och du kommer inte heller att kunna göra det i framtiden.

**När och varför ska jag börja använda GPv2-konton?**

GPv2-konton är särskilt utformade för att tillhandahålla låga GB-lagringskostnader och konkurrenskraftiga kostnader för transaktioner och dataåtkomst. Vi rekommenderar att du använder GPv2-konton i fortsättningen för att lagra blobbar eftersom framtida funktioner, t.ex. ändringsmeddelanden, kommer att läggas till för den här kontotypen. Det är naturligtvis upp till dig när du vill uppgradera beroende på dina affärsbehov. Du kan till exempel välja att optimera dina transaktionsmönster innan du uppgraderar.

Eftersom du inte kan nedgradera från GPv2 bör du fundera över vad detta har för påverkan på kostnaderna innan du uppgraderar dina konton till GPv2.

**Kan jag uppgradera mitt befintliga lagringskonto till ett GPv2-lagringskonto?**

Ja. GPv1-lagringskonton eller Blob Storage-konton kan enkelt uppgraderas till GPv2 i Portal, eller med PowerShell eller CLI. 

Eftersom du inte kan nedgradera från GPv2 bör du fundera över vad detta har för påverkan på kostnaderna innan du uppgraderar dina konton till GPv2.

**Kan jag lagra objekt på båda lagringsnivåerna i samma konto?**

Ja. Attributet **Åtkomstnivå** anger standardlagringsnivån som används för alla objekt i kontot som inte har en angiven nivå. Med blobnivåindelningen kan du ange åtkomstnivå på objektnivå oavsett vilken åtkomstnivå som angetts för kontot. Blobar från alla tre lagringsnivåer (frekvent, lågfrekvent eller arkiv) kan finnas samtidigt i samma konto.

**Kan jag ändra lagringsnivå för mitt GPv2-lagringskonto?**

Ja, du kan ändra lagringsnivå för kontot med attributet **Åtkomstnivå** för lagringskontot. Ändringar av lagringsnivån för kontot gäller för alla objekt som lagras i kontot och som inte har en uttryckligen inställd nivå. Om du ändrar lagringsnivån från frekvent till lågfrekvent tillkommer avgifter för skrivåtgärder (per 10 000) (gäller endast GPv2-konton). Om du ändrar från lågfrekvent till frekvent tillkommer avgifter för både läsåtgärder (per 10 000) och datahämtning (per GB) för läsning av alla data på kontot.

**Hur ofta kan jag ändra lagringsnivå för mitt GPv2- eller Blob Storage-konto?**

Vi har ingen begränsning för hur ofta du kan ändra lagringsnivån, men observera att det kan medföra kostnader att ändra lagringsnivå från lågfrekvent till frekvent. Vi rekommenderar att du inte ändrar lagringsnivån så ofta.

**Beter sig blobarna på lågfrekvent lagringsnivå annorlunda än på frekvent lagringsnivå?**

Blobar på frekvent lagringsnivå i GPv2- och Blob Storage-konton har samma svarstid som blobar i GPv1-konton. Blobbar på lågfrekvent lagringsnivå har liknande svarstid (i millisekunder) som blobbar på frekvent lagringsnivå. Blobar på arkivlagringsnivån har flera timmars svarstid.

Blobbar på lågfrekvent lagringsnivå har något lägre tillgänglighetsnivå (enligt SLA) än blobbar som lagras på frekvent lagringsnivå. Mer information finns i [SLA för Storage](https://azure.microsoft.com/support/legal/sla/storage).

**Kan jag lagra sidblobar och virtuella datordiskar i Blob Storage-konton?**

Nej. Blob Storage-konton stöder endast block- och tilläggsblobar, inte sidblobar. Virtuella datordiskar i Azure backas upp av sidblobar, vilket gör att Blob Storage-konton inte kan användas för att lagra virtuella datordiskar. Däremot kan du lagra säkerhetskopior av virtuella datordiskar som blockblobar i ett Blob Storage-konto. Detta är en av anledningarna till att du bör överväga att använda GPv2 i stället för Blob Storage.

**Kan jag nivåindela sidblobbar i GPv2-lagringskonton?**

Nej. Sidblobbar kommer att härleda lagringsnivån från ditt konto, men det har ingen effekt på priser eller tillgänglighet. Du kommer inte att kunna ändra åtkomstnivån för en sidblob till het, kall eller arkivera. Åtgärden ange Blob-nivå tillåts på en sidblob i ett premiumlagringskonto men det bestämmer endast tillåten storlek, IOPS och bandbredd för premiumsidbloben. Mer information finns i [Ställ in blobnivå](https://docs.microsoft.com/en-us/rest/api/storageservices/set-blob-tier).

**Måste jag ändra mina befintliga appar/program för att använda GPv2-konton?**

GPv2-konton är 100 % API-konsekventa med GPv1- och Blob Storage-konton. Så länge ditt program använder blockblobbar eller tilläggsblobbar, och du använder 2014-02-14-versionen av [Storage Services REST API](https://msdn.microsoft.com/library/azure/dd894041.aspx) eller senare ska ditt program fungera korrekt. Om du använder en äldre version av protokollet måste du uppdatera appen till den nya versionen för att den ska fungera smidigt med båda typerna av lagringskonton. Normalt rekommenderar vi att du använder den senaste versionen oavsett vilken lagringskontotyp du använder.

Priserna för GPv2 är vanligtvis högre än GPv1 när det gäller transaktioner och bandbredd. Du kan därför behöva optimera dina transaktionsmönster innan du uppgraderar för att undvika att den totala kostnaden ökar.

**Ändras användarupplevelsen?**

GPv2-konton är mycket lika GPv1-konton, och de stöder alla viktiga funktioner i Azure Storage, med bland annat hög hållbarhet och tillgänglighet, skalbarhet, prestanda och säkerhet. Förutom funktionerna och begränsningarna som är specifika för Blob Storage-konton och lagringsnivåerna som nämnts ovan är allt annat detsamma när du uppgraderat till GPv2 eller Blob Storage.

## <a name="next-steps"></a>Nästa steg

### <a name="evaluate-blob-storage-accounts"></a>Utvärdera Blob Storage-konton

[Kontrollera tillgängligheten för Blob Storage-konton per region](https://azure.microsoft.com/regions/#services)

[Utvärdera användningen av dina aktuella lagringskonton genom att aktivera mätvärden i Azure Storage.](../common/storage-enable-and-view-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

[Se priser för Blob Storage per region](https://azure.microsoft.com/pricing/details/storage/)

[Se priser för dataöverföring](https://azure.microsoft.com/pricing/details/data-transfers/)

### <a name="start-using-gpv2-storage-accounts"></a>Börja använda GPv2-konton

[Komma igång med Azure Blob Storage](../blobs/storage-dotnet-how-to-use-blobs.md)

[Flytta data till och från Azure Storage](../common/storage-moving-data.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

[Överföra data med kommandoradsverktyget AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

[Bläddra igenom och utforska dina lagringskonton](http://storageexplorer.com/)
