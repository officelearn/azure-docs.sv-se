---
title: Åtkomst nivåer för Azure Blob Storage – frekvent, låg frekvent och Arkiv lag ring
description: Läs om åtkomst nivåer för frekvent åtkomst, låg frekvent åtkomst och arkivering för Azure Blob Storage. Granska lagrings konton som stöder skiktning. Jämför Block Blob Storage-alternativ.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/08/2020
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: clausjor
ms.openlocfilehash: 51998c159018b614ab519766c54fdddf7437e95b
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96923980"
---
# <a name="access-tiers-for-azure-blob-storage---hot-cool-and-archive"></a>Åtkomst nivåer för Azure Blob Storage – frekvent, låg frekvent och Arkiv lag ring

Azure Storage erbjuder olika åtkomst nivåer, vilket gör att du kan lagra BLOB-Datadata på det mest kostnads effektiva sättet. Tillgängliga åtkomstnivåer är:

- **Snabb** – optimerad för att lagra data som används ofta.
- **Cool** -optimerad för att lagra data som inte används ofta och som lagras i minst 30 dagar.
- **Arkiv** -optimerad för att lagra data som sällan används och lagras i minst 180 dagar med flexibla latens krav (i antal timmar).

Följande överväganden gäller för olika åtkomstnivåer:

- Endast de lågfrekventa och frekventa åtkomstnivåerna kan anges på kontonivå. Arkivåtkomstnivån är inte tillgänglig på kontonivå.
- Den frekventa åtkomstnivån, den lågfrekventa åtkomstnivån och arkivåtkomstnivån kan anges på blobnivå under eller efter uppladdningen.
- Data på den lågfrekventa åtkomstnivån kan tolerera något lägre tillgänglighet, men kräver fortfarande hög hållbarhet, svarstid vid hämtning och dataflödesegenskaper som liknar frekvent använda data. För lågfrekvent använda data är ett något lägre serviceavtal för tillgänglighet och högre åtkomstkostnader jämfört med frekvent använda data acceptabla för att få lägre lagringskostnader.
- Arkivlagringen lagrar data offline och erbjuder de lägsta lagringskostnaderna, men även de högsta kostnaderna för extrahering och åtkomst av data.

Data som lagras i molnet växer i exponentiell takt. Om du vill hålla kontroll på och optimera kostnaderna för dina växande lagringsbehov är det en bra idé att ordna data baserat på attribut som åtkomstfrekvens och planerad kvarhållningsperiod. Data som lagras i molnet kan vara olika beroende på hur de genereras, bearbetas och används under sin livslängd. Vissa data används aktivt och ändras under livslängden. Vissa data används ofta i början av livslängden och sedan minskar användning drastiskt när dessa data blir äldre. Vissa data är inaktiva i molnet och används sällan, om ens någonsin, efter att de har lagrats.

Var och en av dessa data åtkomst scenarier fördelar från en annan åtkomst nivå som är optimerad för ett visst åtkomst mönster. Med nivåerna frekvent åtkomst, låg frekvent åtkomst och Arkiv lag rings nivå kan Azure Blob Storage hantera olika åtkomst nivåer med separata pris modeller.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="storage-accounts-that-support-tiering"></a>Lagringskonton med stöd för flera lagringsnivåer

Objekt lagrings data skiktning mellan frekvent, låg frekvent och arkivering stöds bara i Blob Storage-och Generell användning v2-konton (GPv2). Generell användning v1-konton (GPv1) stöder inte nivåer. Kunder kan enkelt konvertera sina befintliga GPv1 eller Blob Storage konton till GPv2-konton via Azure Portal. GPv2 tillhandahåller nya priser och funktioner för blobbar, filer och köer. Vissa funktioner och priser är bara tillgängliga i GPv2-konton. Utvärdera användningen av GPv2-konton efter en omfattande omvisning av priserna. Vissa arbets belastningar kan vara dyrare på GPv2 än GPv1. Mer information finns i [kontoöversikten för Azure Storage](../common/storage-account-overview.md).

Blob Storage-och GPv2-konton exponerar attributet **åtkomst nivå** på konto nivå. Med det här attributet kan du ange standard åtkomst nivå för alla blobar som inte har angetts explicit på objekt nivå. För objekt med nivån angiven på objekt nivå gäller inte konto nivån. Arkiv nivån kan endast tillämpas på objekt nivå. Du kan när som helst växla mellan dessa åtkomst nivåer.

## <a name="hot-access-tier"></a>Frekvent lagringsnivå

Den frekventa åtkomstnivån har högre lagringskostnader än lågfrekvent åtkomstnivå och arkivåtkomstnivå, men den har lägst åtkomstkostnader. Exempel på användningsscenarier för frekvent åtkomstnivå är:

- Data som används aktivt eller förväntas användas (läsas från och skrivas till) ofta.
- Data som mellanlagras för bearbetning och eventuell migrering till lågfrekvent åtkomstnivå.

## <a name="cool-access-tier"></a>Lågfrekvent åtkomstnivå

Den lågfrekventa åtkomstnivån har lägre lagringskostnader och högre åtkomstkostnader jämfört med frekvent lagring. Den här nivån är avsedd för data som är kvar på den lågfrekventa nivån i minst 30 dagar. Exempel på användningsscenarier för lågfrekvent åtkomstnivå är:

- Datauppsättningar för kortsiktig säkerhetskopiering och haveriberedskap.
- Äldre medieinnehåll som inte visas så ofta längre, men som förväntas vara tillgängligt direkt vid behov.
- Stora datamängder som behöver en kostnadseffektiv lagring under tiden mer data samlas in för framtida bearbetning. (*Till exempel* långsiktig lagring av vetenskapliga data eller telemetridata (rådata) från en tillverkningsanläggning.)

## <a name="archive-access-tier"></a>Arkivlagring

Arkiv åtkomst nivån har lägst lagrings kostnad. Men den har högre kostnader för data hämtning jämfört med frekventa och låg frekventa nivåer. Data måste finnas kvar på Arkiv nivå i minst 180 dagar eller omfattas av en avgift för tidig borttagning. Det kan ta flera timmar att hämta data på Arkiv nivån beroende på ÅTERUPPVÄCKNING prioritet. För små objekt kan en rehydratisera med hög prioritet hämta objektet från arkivet på under en timme. Mer information finns i avsnittet om [dehydratisera BLOB-data från Arkiv](storage-blob-rehydration.md) lag rings nivån.

När en BLOB finns i Arkiv lag ring är BLOB-data offline och kan inte läsas, skrivas över eller ändras. Om du vill läsa eller ladda ned en BLOB i arkivet måste du först extrahera den till en onlinenivå. Du kan inte ta ögonblicks bilder av en BLOB i Arkiv lag ring. BLOB-metadata är dock online och tillgängliga, så att du kan lista BLOB, egenskaper, metadata och blob-taggar. Det är inte tillåtet att ange eller ändra BLOB-metadata i arkivet. Du kan dock ange och ändra BLOB-index taggar. För blobbar i arkivet är de enda giltiga åtgärderna GetBlobProperties, GetBlobMetadata, SetBlobTags, GetBlobTags, FindBlobsByTags, ListBlobs, SetBlobTier, CopyBlob och DeleteBlob.

Exempel på användnings scenarier för Arkiv åtkomst nivån är:

- Långsiktig säkerhetskopiering, sekundär säkerhetskopiering och datauppsättningar för arkivering
- Ursprungliga rådata som måste bevaras, även efter att de har bearbetats till ett slutligt användbart format.
- Efterlevnads- och arkiveringsdata som behöver lagras under en längre tid och som nästan aldrig används.

> [!NOTE]
> Arkiv nivån stöds för närvarande inte för ZRS-, GZRS-eller RA-GZRS-konton.

## <a name="account-level-tiering"></a>Skiktning på konto nivå

Blobbar i alla tre åtkomst nivåer kan finnas i samma konto. Alla blobar som inte har en uttryckligen tilldelad nivå härleder nivån från kontots åtkomst nivå inställning. Om åtkomst nivån kommer från kontot ser du att egenskapen **åtkomst nivå härledd** BLOB har angetts till true och att **åtkomst nivåns** BLOB-egenskap matchar konto nivån. I Azure Portal visas egenskapen _härledd åtkomst nivå_ med BLOB-åtkomst nivån som **aktiv (härledd)** eller låg frekvent **(härledd)**.

Att ändra kontots åtkomst nivå gäller för alla _härledda objekt i åtkomst nivån_ som lagras i kontot som inte har någon explicit nivå uppsättning. Om du växlar konto nivån från frekvent till låg frekvent kommer du att debiteras för Skriv åtgärder (per 10 000) för alla blobbar utan en uppsättnings nivå i GPv2-konton. Det kostar inget att ändra i Blob Storage-konton. Du debiteras för både Läs åtgärder (per 10 000) och data hämtning (per GB) om du växlar från låg frekvent till frekvent i Blob Storage-eller GPv2-konton.

## <a name="blob-level-tiering"></a>Blobnivåindelning

Med skiktning på blobnivå kan du ladda upp data till den åtkomst nivå som du väljer med hjälp av åtgärden [Lägg till BLOB](/rest/api/storageservices/put-blob) eller Lägg till [blockera-lista](/rest/api/storageservices/put-block-list) och ändra nivån för dina data på objekt nivå med hjälp av funktionen [Ange BLOB-nivå](/rest/api/storageservices/set-blob-tier) eller [livs cykel hantering](#blob-lifecycle-management) . Du kan ladda upp data till den nödvändiga åtkomst nivån och sedan enkelt ändra BLOB-åtkomsten mellan nivåerna frekvent, låg frekvent eller Arkiv form när användnings mönster ändras, utan att behöva flytta data mellan konton. Alla ändrings begär Anden sker omedelbart och nivå förändringar mellan frekvent och låg frekvent omedelbar omedelbar inaktive ras. Det kan dock ta flera timmar att återuppväcks en BLOB från arkivet.

Tiden för den senaste ändringen på blobnivån är tillgänglig via blobegenskapen **Ändringstid för åtkomstnivå**. När en BLOB skrivs över på frekvent eller låg frekvent nivå ärver den nya bloben den nivå i blobben som skrevs av, såvida inte den nya BLOB-åtkomst nivån uttryckligen anges när den skapas. Om en BLOB finns på Arkiv nivån kan den inte skrivas över, så det är inte tillåtet att ladda upp samma BLOB i det här scenariot. 

> [!NOTE]
> Arkivlagring och blobnivåindelning stöder endast blockblobar.

### <a name="blob-lifecycle-management"></a>Hantering av BLOB-livscykel

Blob Storage livs cykel hantering ger en omfattande, regelbaserade princip som du kan använda för att överföra data till bästa åtkomst nivå och för att förfalla data i slutet av livs cykeln. Mer information finns i [Hantera Azure Blob Storage livs cykeln](storage-lifecycle-management-concepts.md) .  

> [!NOTE]
> Data som lagras i ett Block Blob Storage-konto (Premium prestanda) kan för närvarande inte på nivå till frekvent, låg frekvent eller arkiveras med hjälp av [Ange BLOB-nivå](/rest/api/storageservices/set-blob-tier) eller använda Azure Blob Storage livs cykel hantering.
> Om du vill flytta data måste du synkront kopiera blobar från Block Blob Storage-kontot till frekvent åtkomst nivå i ett annat konto med hjälp av [undantag från URL-API](/rest/api/storageservices/put-block-from-url) eller en version av AzCopy som stöder detta API.
> *Placera block från URL* -API: n kopierar synkront data på servern, vilket innebär att anropet slutförs endast när alla data har flyttats från den ursprungliga Server platsen till mål platsen.

### <a name="blob-level-tiering-billing"></a>Fakturering för blobnivåindelning

När en BLOB överförs eller flyttas till den frekventa nivån, låg frekvent lagrings nivå eller Arkiv lag rings nivån, debiteras den enligt motsvarande taxa omedelbart vid nivå ändringar.

När en BLOB flyttas till en låg frekvent nivå (frekvent >låg frekvent lagring, >arkiv lag ring eller cool->Arkiv) faktureras åtgärden som en Skriv åtgärd till mål nivån, där avgifter för Skriv åtgärden (per 10 000) och data skrivning (per GB) för mål nivån tillämpas.

När en BLOB flyttas till en varm nivå (Arkiv->låg frekvent, Arkiv->frekvent eller låg frekvent >frekvent) debiteras åtgärden som en läsning från käll nivån, där avgifterna Läs åtgärd (per 10 000) och data hämtning (per GB) för käll nivån tillämpas. Kostnader för tidig borttagning av blobbar från lågfrekvent lagring eller arkivlagring kan också tillkomma. [Återuppväcks-data från arkivet](storage-blob-rehydration.md) tar tid och data debiteras arkiverade priser tills data återställs online och blobnivå ändras till frekvent eller låg frekvent. I följande tabell sammanfattas hur nivå ändringar faktureras:

| | **Skriv avgifter (åtgärd + åtkomst)** | **Läs avgifter (åtgärd + åtkomst)**
| ---- | ----- | ----- |
| **SetBlobTier riktning** | varm->sval,<br> Hot->Arkiv,<br> kall >Arkiv | Arkiv->häftigt,<br> Arkiv->varmt,<br> hett >frekvent

### <a name="cool-and-archive-early-deletion"></a>Tidig borttagning i lågfrekvent lagring och i arkivlagring

Alla blobar som flyttas till den låg frekventa nivån (endast GPv2-konton) omfattas av en låg tidig borttagnings period på 30 dagar. En blob som flyttas till Arkiv nivån omfattas av en tidig borttagnings period på 180 dagar. Den här kostnaden beräknas proportionellt. Om en blob till exempel flyttas till arkivet och sedan tas bort eller flyttas till den frekventa nivån efter 45 dagar, kommer du att debiteras en avgift för tidig borttagning som motsvarar 135 (180 minus 45) dagar för att lagra bloben i arkivet.

Det finns viss information när du flyttar mellan låg frekvent nivå och Arkiv lag ring:

1. Om en BLOB härleds som låg frekvent baserat på lagrings kontots standard åtkomst nivå och blobben flyttas till arkivet, finns det ingen avgift för tidig borttagning.
1. Om en BLOB uttryckligen flyttas till den låg frekventa nivån och sedan flyttas till arkivet, gäller avgiften för tidig borttagning.

Du kan beräkna tidig borttagningen med hjälp av BLOB-egenskapen, **senast ändrad**, om det inte har gjorts några ändringar i åtkomst nivån. Annars kan du använda när åtkomst nivån senast ändrades till låg frekvent eller arkivera genom att visa egenskapen BLOB: **Access-Tier-Change-Time**. Mer information om BLOB-egenskaper finns i [Hämta BLOB-egenskaper](/rest/api/storageservices/get-blob-properties).

## <a name="comparing-block-blob-storage-options"></a>Jämför lagrings alternativ för Block Blob

I följande tabell visas en jämförelse av Premium Performance Block Blob Storage och åtkomst nivåerna frekvent, låg frekvent och Arkiv lag ring.

|                                           | **Förstklassig prestanda**   | **Frekvent nivå** | **Låg frekvent nivå**       | **Arkiv lag ring**  |
| ----------------------------------------- | ------------------------- | ------------ | ------------------- | ----------------- |
| **Tillgänglighet**                          | 99,9 %                     | 99,9 %        | 99 %                 | Offline           |
| **Tillgänglighet** <br> **(RA-GRS-läsningar)**  | Saknas                       | 99,99 %       | 99,9 %               | Offline           |
| **Användnings kostnader**                         | Högre kostnader för lagring, lägre åtkomst och kostnad för transaktioner | Högre kostnader för lagring, lägre åtkomst och transaktionskostnader | Lägre kostnader för lagring, högre åtkomst och transaktionskostnader | Lägsta kostnader för lagring, högsta åtkomst och transaktionskostnader |
| **Minsta objektstorlek**                   | Saknas                       | Saknas          | Saknas                 | Saknas               |
| **Minsta lagringstid**              | Saknas                       | Saknas          | 30 dagar<sup>1</sup> | 180 dagar
| **Svarstid** <br> **(Tid till första byte)** | Ensiffriga millisekunder | millisekunder | millisekunder        | timmar<sup>2</sup> |

<sup>1</sup> objekt på den lägsta nivån i GPv2-konton har en minsta Retentions tid på 30 dagar. Blob Storage-konton har inte minsta Retentions tid för den låg frekventa nivån.

<sup>2</sup> arkivlagring för närvarande stöd för 2 rehydratiseras-prioriteringar, hög och standard, som erbjuder olika hämtnings fördröjningar. Mer information finns i [rehydratisera BLOB-data från Arkiv](storage-blob-rehydration.md)lag rings nivån.

> [!NOTE]
> Blob Storage-konton har stöd för samma prestanda och skalbarhets mål som lagrings konton för generell användning v2. Mer information finns i [skalbarhets-och prestanda mål för Blob Storage](scalability-targets.md).

## <a name="quickstart-scenarios"></a>Snabbstartsscenarier

I det här avsnittet visas följande scenarier med hjälp av Azure Portal och PowerShell:

- Ändra standardåtkomstnivån för ett GPv2- eller Blob Storage-konto.
- Ändra åtkomstnivån för en blob i ett GPv2- eller Blob Storage-konto.

### <a name="change-the-default-account-access-tier-of-a-gpv2-or-blob-storage-account"></a>Ändra standard konto åtkomst nivån för ett GPv2-eller Blob Storage konto

# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Sök efter och välj **alla resurser** i Azure Portal.

1. Välj ditt lagringskonto.

1. I **Inställningar** väljer du **konfiguration** för att visa och ändra konto konfigurationen.

1. Välj rätt åtkomst nivå för dina behov: ange **åtkomst nivå** till antingen **cool** eller **hett**.

1. Klicka på **Spara** högst upp.

![Ändra standard konto nivå i Azure Portal](media/storage-tiers/account-tier.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Följande PowerShell-skript kan användas för att ändra konto nivån. `$rgName`Variabeln måste initieras med resurs gruppens namn. `$accountName`Variabeln måste initieras med ditt lagrings konto namn. 
```powershell
#Initialize the following with your resource group and storage account names
$rgName = ""
$accountName = ""

#Change the storage account tier to hot
Set-AzStorageAccount -ResourceGroupName $rgName -Name $accountName -AccessTier Hot
```
---

### <a name="change-the-tier-of-a-blob-in-a-gpv2-or-blob-storage-account"></a>Ändra nivån för en BLOB i ett GPv2-eller Blob Storage konto
# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Sök efter och välj **alla resurser** i Azure Portal.

1. Välj ditt lagringskonto.

1. Välj din behållare och välj sedan din BLOB.

1. I **BLOB-egenskaperna** väljer du **ändra nivå**.

1. Välj åtkomst nivån frekvent **Cool** **, låg** frekvent eller **Arkiv** . Om din BLOB för närvarande finns i arkivet och du vill rehydratisera till en onlinenivå, kan du också välja en rehydratiserad prioritet för **standard** eller **hög**.

1. Välj **Spara** längst ned.

![Ändra BLOB-nivå i Azure Portal](media/storage-tiers/blob-access-tier.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Följande PowerShell-skript kan användas för att ändra BLOB-nivån. `$rgName`Variabeln måste initieras med resurs gruppens namn. `$accountName`Variabeln måste initieras med ditt lagrings konto namn. `$containerName`Variabeln måste initieras med ditt container namn. `$blobName`Variabeln måste initieras med ditt BLOB-namn. 
```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$containerName = ""
$blobName == ""

#Select the storage account and get the context
$storageAccount =Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

#Select the blob from a container
$blob = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx

#Change the blob’s access tier to archive
$blob.ICloudBlob.SetStandardBlobTier("Archive")
```
---

## <a name="pricing-and-billing"></a>Priser och fakturering

Alla lagrings konton använder en pris modell för Block-Blob-lagring baserat på nivån för varje blob. Tänk på följande saker om fakturering:

- **Lagringskostnader**: Utöver mängden data som lagras varierar lagringskostnaden beroende på åtkomstnivå. Kostnaden per gigabyte minskas när nivån blir mer lågfrekvent.
- **Kostnader för dataåtkomst**: Kostnaderna för dataåtkomst ökar när nivån blir mer lågfrekvent. För data i låg frekvent och Arkiv lag rings nivå debiteras du en åtkomst avgift per Gigabyte för läsningar.
- **Transaktionskostnader**: det finns en avgift per transaktion för alla nivåer som ökar när nivån blir kylare.
- **Dataöverföringskostnader för geo-replikering**: Den här avgiften gäller endast konton med konfigurerad geo-replikering, inklusive GRS och RA-GRS. Dataöverföring för geo-replikering debiteras per gigabyte.
- **Kostnader för utgående dataöverföring**: Utgående dataöverföringar (data som överförs utanför en Azure-region) debiteras för bandbreddsanvändning per gigabyte, på samma sätt som för allmänna lagringskonton.
- Om **du ändrar åtkomst nivån**: om du ändrar kontots åtkomst nivå ändras kostnader för nivå ändringar för _åtkomst nivå härledda_ blobbar lagrade i kontot som inte har en explicit nivå uppsättning. Information om hur du ändrar åtkomst nivå för en enskild BLOB finns på [nivå fakturering på BLOB-nivå](#blob-level-tiering-billing).

    Om du ändrar åtkomst nivån för en BLOB när versions hantering har Aktiver ATS, eller om bloben har ögonblicks bilder, kan ytterligare kostnader uppstå. Mer information om hur du faktureras när BLOB-versioner aktive ras och du uttryckligen ändrar en blobs nivå finns i [priser och fakturering](versioning-overview.md#pricing-and-billing) i dokumentationen för BLOB-versioner. Mer information om hur du faktureras när en BLOB har ögonblicks bilder och du uttryckligen ändrar blobens nivå finns i [priser och fakturering](snapshots-overview.md#pricing-and-billing) i dokumentationen för BLOB-ögonblicksbilder.

> [!NOTE]
> Mer information om priser för block blobbar finns [Azure Storage prissättnings](https://azure.microsoft.com/pricing/details/storage/blobs/) sida. Mer information om kostnaderna för utgående dataöverföring finns på sidan [Prisinformation om Dataöverföringar](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

**Bör jag använda Blob Storage-eller GPv2-konton om jag vill göra data på nivå av mina data?**

Vi rekommenderar att du använder GPv2 i stället för Blob Storage-konton om du vill använda lagringsnivåer. GPv2 har stöd för samma funktioner som Blob Storage-konton, och mycket mer. Kostnaden för Blob Storage- och GPv2-konton är i stort sett densamma, men vissa nya funktioner och rabatter kommer bara att vara tillgängliga för GPv2-konton. GPv1-konton stöder inte nivåer.

Prisstrukturen för GPv1- och GPv2-konton skiljer sig åt, så kunderna bör noggrant utvärdera båda alternativen innan de bestämmer sig för att använda GPv2-konton. Du kan enkelt konvertera ett befintligt Blob Storage eller GPv1-konto till GPv2 via en enklicksprocess. Mer information finns i [kontoöversikten för Azure Storage](../common/storage-account-overview.md).

**Kan jag lagra objekt i alla tre (frekventa, svala och arkiverade) åtkomst nivåer i samma konto?**

Ja. Attributet **åtkomst nivå** som anges på konto nivå är standard konto nivån som gäller för alla objekt i kontot utan en explicit uppsättnings nivå. Med skiktning på blobnivå kan du ange åtkomst nivå på objekt nivå, oavsett vad åtkomst nivå inställningen för kontot är. Blobbar i någon av de tre åtkomst nivåerna (frekvent, låg frekvent eller Arkiv) kan finnas i samma konto.

**Kan jag ändra standard åtkomst nivån för mitt blob-eller GPv2-lagrings konto?**

Ja, du kan ändra standard konto nivån genom att ange attributet **åtkomst nivå** för lagrings kontot. Ändring av konto nivån gäller för alla objekt som lagras i kontot som inte har en explicit nivå uppsättning (till exempel frekvent **(härledd)** eller låg frekvent **(härledd)**). När konto nivån växlas från frekvent till låg frekvent tillkommer Skriv åtgärder (per 10 000) för alla blobbar utan en uppsättnings nivå i GPv2-konton och växling från låg frekvent till frekventt medför både Läs åtgärder (per 10 000) och data hämtning (per GB) avgifter för alla blobbar i Blob Storage-och GPv2-konton.

**Kan jag ange arkivlagring som standardnivå för mitt konto?**

Nej. Endast frekventa och frekventa åtkomst nivåer kan anges som standard åtkomst nivå för kontot. Arkivlagringsnivån kan endast anges på objektnivå. Vid BLOB-uppladdning anger du den åtkomst nivå som du vill använda som frekvent, låg frekvent eller Arkiverad oavsett vilken konto nivå som är standard. Med den här funktionen kan du skriva data direkt till Arkiv nivån för att realisera kostnads besparingar från det ögonblick då du skapar data i Blob Storage.

**I vilka regioner är åtkomst nivåerna frekvent åtkomst, låg frekvent åtkomst och arkivering tillgängliga i?**

Nivåerna frekvent och låg frekvent åtkomst tillsammans med skiktning på BLOB-nivå är tillgängliga i alla regioner. Arkivlagring är inledningsvis endast tillgängligt i vissa regioner. En fullständig lista finns under [Tillgängliga Azure-produkter efter region](https://azure.microsoft.com/regions/services/).

**Vilka alternativ för redundans stöds för nivåerna frekvent åtkomst, låg frekvent åtkomst och Arkiv lag ring?**

Frekventa och låg frekventa nivåer stöder alla alternativ för redundans. Arkiv nivån stöder endast LRS, GRS och RA-GRS. ZRS, GZRS och RA-GZRS stöds inte för Arkiv nivån.

**Beter sig Blobbarna på den frekventa åtkomst nivån annorlunda än de i frekvent åtkomst nivå?**

Blobbar i frekvent åtkomst nivå har samma svars tid som blobbar i GPv1-, GPv2-och Blob Storage-konton. Blobbar i låg frekvent åtkomst nivå har liknande svars tid (i millisekunder) som blobbar i GPv1-, GPv2-och Blob Storage-konton. Blobbar i Arkiv åtkomst nivån har flera timmars svars tid i GPv1, GPv2 och Blob Storage konton.

Blobbar i låg frekvent åtkomst nivå har något lägre tillgänglighets nivå (SLA) än blobbar som lagras i frekvent åtkomst nivå. Mer information finns i [SLA för Storage](https://azure.microsoft.com/support/legal/sla/storage/v1_5/).

**Kan samma åtgärder användas för lagringsnivåerna frekvent, lågfrekvent och arkivlagring?**

På lagringsnivåerna frekvent och lågfrekvent fungerar alla åtgärder på samma sätt. Alla giltiga Arkiv åtgärder inklusive GetBlobProperties, GetBlobMetadata, SetBlobTags, GetBlobTags, FindBlobsByTags, ListBlobs, SetBlobTier och DeleteBlob är 100% konsekventa med frekvent och låg frekvent. BLOB-data kan inte läsas eller ändras på Arkiv nivå förrän de har rehydratiserats; endast Läs åtgärder för BLOB-metadata stöds i arkivet. BLOB-taggar kan dock läsas, anges eller ändras under arkivet.

**När jag återställer en blob från arkivlagring till frekvent eller lågfrekvent lagring, hur vet jag när processen är klar?**

Under ÅTERUPPVÄCKNING kan du använda åtgärden Hämta BLOB-egenskaper för att avsöka attributet **arkivera status** och bekräfta när nivå ändringen har slutförts. Status är ”rehydrate-pending-to-hot” eller ”rehydrate-pending-to-cool” beroende på målnivån. När åtgärden har slutförts tas statusen för status för arkivet bort och egenskapen BLOB för **åtkomst nivå** visar den nya frekventa eller låg frekventa nivån. Mer information finns i avsnittet om [dehydratisera BLOB-data från Arkiv](storage-blob-rehydration.md) lag rings nivån.

**När jag har angett nivå för en blob: hur lång tid tar det innan jag börjar debiteras för den nya nivån?**

Varje Blob faktureras alltid enligt den nivå som anges av blobbets egenskap för **åtkomst nivå** . När du anger en ny onlinenivå för en BLOB, Visar egenskapen **åtkomst nivå** omedelbart den nya nivån för alla över gångar. Men det kan ta flera timmar att återuppväcks en BLOB från offline-arkivets lagrings nivå till en frekvent eller låg frekvent nivå. I det här fallet debiteras du enligt Arkiv taxa tills ÅTERUPPVÄCKNING är slutförd, där egenskapen **åtkomst nivå** visar den nya nivån. När du har dehydratiserat till online-nivån debiteras du för denna BLOB vid frekvent eller låg frekvent hastighet.

**Hur gör jag för att avgöra om jag får en avgift för tidig borttagning när en BLOB tas bort eller flyttas från låg frekvent lagring eller Arkiv lag ring?**

Om en blob tas bort eller flyttas från lågfrekvent lagring (endast GPv2-konton) inom 30 dagar, eller från arkivlagring inom 180 dagar, påförs en proportionellt beräknad avgift för tidig borttagning. Du kan bestämma hur länge en BLOB har varit i låg frekvent lagrings nivå eller Arkiv lag ring genom att kontrol lera egenskapen för **ändrings tid för åtkomst nivå** , vilket ger en stämpel för den senaste nivå ändringen. Om blobens nivå aldrig har ändrats kan du kontrol lera den **senast ändrade** BLOB-egenskapen. Mer information finns i [kyl och arkivering tidig borttagning](#cool-and-archive-early-deletion).

**Vilka Azure-verktyg och SDK:er stöder blobnivåindelning och arkivlagring?**

Azure Portal, PowerShell, CLI-verktyg och klientbiblioteken för .NET, Java, Python och Node.js har stöd för blobnivåindelning och arkivlagring.  

**Hur mycket data kan jag lagra på lagringsnivåerna frekvent, lågfrekvent och arkiv?**

Data lagring tillsammans med andra gränser anges på konto nivå och inte per åtkomst nivå. Du kan välja att använda hela gränsen på en nivå eller på alla tre nivåer. Mer information finns i [skalbarhets-och prestanda mål för standard lagrings konton](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="next-steps"></a>Nästa steg

Utvärdera frekvent, låg frekvent och Arkiv lag ring i GPv2 och Blob Storage konton

- [Kontrollera tillgängligheten för frekvent/lågfrekvent lagring och arkivlagring efter region](https://azure.microsoft.com/regions/#services)
- [Hantera Azure Blob Storage-livscykeln](storage-lifecycle-management-concepts.md)
- [Lär dig mer om återuppväcks BLOB-data från Arkiv lag rings nivån](storage-blob-rehydration.md)
- [Ta reda på om Premium prestanda skulle dra nytta av din app](storage-blob-performance-tiers.md)
- [Utvärdera användningen av dina aktuella lagringskonton genom att aktivera mätvärden i Azure Storage.](./monitor-blob-storage.md)
- [Kontrol lera priser för frekvent, låg frekvent och Arkiv lag ring i Blob Storage-och GPv2-konton per region](https://azure.microsoft.com/pricing/details/storage/)
- [Se priser för dataöverföring](https://azure.microsoft.com/pricing/details/data-transfers/)