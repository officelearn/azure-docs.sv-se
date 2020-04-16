---
title: Frekventa, svala och arkivera åtkomstnivåer för blobbar – Azure Storage
description: Frekventa, häftiga och arkivera åtkomstnivåer för Azure-lagringskonton.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/23/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: clausjor
ms.openlocfilehash: c803d489b70cda6910865f6096d21c2021c4ae3a
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393707"
---
# <a name="azure-blob-storage-hot-cool-and-archive-access-tiers"></a>Azure Blob Storage: nivåer för frekvent åtkomst, lågfrekvent åtkomst och arkivlagring

Azure storage erbjuder olika åtkomstnivåer, vilket gör att du kan lagra blob-objektdata på det mest kostnadseffektiva sättet. De tillgängliga åtkomstnivåerna inkluderar:

- **Hot** - Optimerad för lagring av data som används ofta.
- **Cool** - Optimerad för lagring av data som sällan används och lagras i minst 30 dagar.
- **Arkiv** - Optimerad för lagring av data som sällan nås och lagras i minst 180 dagar med flexibla svarstidskrav (i timordning).

Följande överväganden gäller för de olika åtkomstnivåerna:

- Endast nivåerna för frekvent och sval åtkomst kan ställas in på kontonivå. Arkivåtkomstnivån är inte tillgänglig på kontonivå.
- Hot, cool och arkivnivåer kan ställas in på blob-nivå under uppladdning eller efter uppladdning.
- Data på den häftiga åtkomstnivån kan tolerera något lägre tillgänglighet, men kräver fortfarande hög hållbarhet, svarstid för hämtning och dataflöde som liknar heta data. För häftiga data är ett något lägre serviceavtal på servicenivå (SLA) och högre åtkomstkostnader jämfört med heta data godtagbara kompromisser för lägre lagringskostnader.
- Arkivlagring lagrar data offline och erbjuder de lägsta lagringskostnaderna men också de högsta dataredrhydrat- och åtkomstkostnaderna.

Data som lagras i molnet växer i en exponentiell takt. Om du vill hålla kontroll på och optimera kostnaderna för dina växande lagringsbehov är det en bra idé att ordna data baserat på attribut som åtkomstfrekvens och planerad kvarhållningsperiod. Data som lagras i molnet kan vara olika beroende på hur de genereras, bearbetas och nås under sin livstid. Vissa data används aktivt och ändras under livslängden. Vissa data används ofta i början av livslängden och sedan minskar användning drastiskt när dessa data blir äldre. Vissa data förblir inaktiva i molnet och är sällan, om någonsin, nås efter att den har lagrats.

Var och en av dessa dataåtkomstscenarier drar nytta av en annan åtkomstnivå som är optimerad för ett visst åtkomstmönster. Med frekventa, svala och arkivåtkomstnivåer åtgärdar Azure Blob-lagring detta behov av differentierade åtkomstnivåer med separata prismodeller.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="storage-accounts-that-support-tiering"></a>Lagringskonton med stöd för flera lagringsnivåer

Datanivåiering av objektlagring mellan frekvent, lågfrekvent och arkiv stöds endast i Blob storage och GPv2-konton (General Purpose v2). GPv1-konton (General Purpose v1) stöder inte nivåindelning. Kunder kan enkelt konvertera sina befintliga GPv1- eller Blob-lagringskonton till GPv2-konton via Azure-portalen. GPv2 innehåller nya priser och funktioner för blobbar, filer och köer. Vissa funktioner och prissänkningar erbjuds endast i GPv2-konton. Utvärdera med hjälp av GPv2-konton efter att ha granskat prissättningen på ett omfattande sätt. Vissa arbetsbelastningar kan vara dyrare på GPv2 än GPv1. Mer information finns i [kontoöversikten för Azure Storage](../common/storage-account-overview.md).

Blob-lagring och GPv2-konton **exponerar attributet Access Tier** på kontonivå. Med det här attributet kan du ange standardåtkomstnivån för alla blob som inte har den explicit inställd på objektnivå. För objekt med nivån inställd på objektnivå gäller inte kontonivån. Arkivnivån kan endast användas på objektnivå. Du kan växla mellan dessa åtkomstnivåer när som helst.

## <a name="hot-access-tier"></a>Frekvent åtkomstnivå

Den aktiva åtkomstnivån har högre lagringskostnader än coola nivåer och arkivnivåer, men de lägsta åtkomstkostnaderna. Exempel på användningsscenarier för hot access-nivån är:

- Data som används aktivt eller som förväntas kommas åt (läs från och skrivas till) ofta.
- Data som är iscensatta för bearbetning och eventuell migrering till den häftiga åtkomstnivån.

## <a name="cool-access-tier"></a>Lågfrekvent åtkomstnivå

Den låga åtkomstnivån har lägre lagringskostnader och högre åtkomstkostnader jämfört med varmlagring. Den här nivån är avsedd för data som är kvar på den lågfrekventa nivån i minst 30 dagar. Exempel på användningsscenarier för den häftiga åtkomstnivån är:

- Datauppsättningar för kortsiktig säkerhetskopiering och haveriberedskap.
- Äldre medieinnehåll som inte visas så ofta längre, men som förväntas vara tillgängligt direkt vid behov.
- Stora datamängder som behöver en kostnadseffektiv lagring under tiden mer data samlas in för framtida bearbetning. (*Till exempel* långsiktig lagring av vetenskapliga data eller telemetridata (rådata) från en tillverkningsanläggning.)

## <a name="archive-access-tier"></a>Arkivåtkomstnivå

Arkivåtkomstnivån har den lägsta lagringskostnaden. Men det har högre kostnader för datahämtning jämfört med de heta och svala nivåerna. Det kan ta flera timmar att hämta data på arkivnivån. Data måste finnas kvar på arkivnivån i minst 180 dagar eller bli föremål för en avgift för tidig borttagning.

Medan en blob lagras i arkivlagring är blob-data offline och kan inte läsas, skrivas över eller ändras. Om du vill läsa eller hämta en blob i arkivet måste du först rehydrera den till en onlinenivå. Du kan inte ta ögonblicksbilder av en blob i arkivlagring. Blobmetadata är dock online och tillgängliga, så att du kan lista blobben och dess egenskaper. För blobbar i arkivet är de enda giltiga åtgärderna GetBlobProperties, GetBlobMetadata, ListBlobs, SetBlobTier, CopyBlob och DeleteBlob. Mer information [finns i Rehydrate blob-data från arkivnivån.](storage-blob-rehydration.md)

Exempel på användningsscenarier för arkivåtkomstnivån är:

- Långsiktig säkerhetskopiering, sekundär säkerhetskopiering och datauppsättningar för arkivering
- Ursprungliga rådata som måste bevaras, även efter att de har bearbetats till ett slutligt användbart format.
- Efterlevnads- och arkiveringsdata som behöver lagras under en längre tid och som nästan aldrig används.

## <a name="account-level-tiering"></a>Nivåindelning på kontonivå

Blobbar i alla tre åtkomstnivåer kan samexistera inom samma konto. Alla blob som inte har en uttryckligen tilldelad nivå härleder nivån från inställningen för kontoåtkomstnivå. Om åtkomstnivån kommer från kontot visas egenskapen **Access Tier Inferred** blob inställd på "true" och egenskapen **Access Tier** blob matchar kontonivån. I Azure-portalen visas egenskapen _åtkomstnivåinlett_ med blob-åtkomstnivån som **Hot (inferred)** eller **Cool (härledd).**

Att ändra kontoåtkomstnivån gäller för alla _åtkomstnivåer som används objekt_ som lagras i kontot och som inte har en explicit nivåuppsättning. Om du växlar kontonivån från frekvent till cool debiteras du för skrivåtgärder (per 10 000) för alla blobbar utan en viss nivå endast i GPv2-konton. Det finns ingen avgift för den här ändringen i Blob-lagringskonton. Du debiteras för både läsåtgärder (per 10 000) och datahämtning (per GB) om du växlar från cool till het i Blob-lagring eller GPv2-konton.

## <a name="blob-level-tiering"></a>Blobnivåindelning

Med Blob-nivånivå kan du överföra data till den åtkomstnivå du väljer med hjälp av åtgärderna [Placera Blob](/rest/api/storageservices/put-blob) eller [Put Block List](/rest/api/storageservices/put-block-list) och ändra nivån för dina data på objektnivå med hjälp av funktionen Ange [Blob-nivå](/rest/api/storageservices/set-blob-tier) eller [livscykelhanteringsfunktionen.](#blob-lifecycle-management) Du kan ladda upp data till önskad åtkomstnivå och sedan enkelt ändra blob-åtkomstnivån bland nivåerna för frekvent, lågfrekvent eller arkiv när användningsmönstren ändras, utan att behöva flytta data mellan konton. Alla begäranden om nivåändring sker omedelbart och nivåändringar mellan frekvent och sval är omedelbara. Det kan dock ta flera timmar att rehydrera en blob från arkivet.

Tiden för den senaste ändringen på blobnivån är tillgänglig via blobegenskapen **Ändringstid för åtkomstnivå**. När du skriver över en blob på den aktiva eller svala nivån ärver den nyligen skapade bloben nivån för bloben som skrevs över om inte den nya blob-åtkomstnivån uttryckligen anges när den skapas. Om en blob finns på arkivnivån kan den inte skrivas över, så det är inte tillåtet att överföra samma blob i det här scenariot. 

> [!NOTE]
> Arkivlagring och blobnivåindelning stöder endast blockblobar. Du kan inte heller för närvarande ändra nivån för en blockblob som har ögonblicksbilder.

### <a name="blob-lifecycle-management"></a>Blob livscykelhantering

Blob Storage lifecycle management erbjuder en omfattande, regelbaserad princip som du kan använda för att överföra dina data till den bästa åtkomstnivån och för att förfalla data i slutet av livscykeln. Mer information finns i Hantera livscykeln för [Azure Blob-lagring.](storage-lifecycle-management-concepts.md)  

> [!NOTE]
> Data som lagras i ett block blob storage-konto (Premium-prestanda) kan för närvarande inte nivåindelad till frekvent, lågfrekvent eller arkiveras med [Ange Blob-nivå](/rest/api/storageservices/set-blob-tier) eller med hjälp av Azure Blob Storage lifecycle management.
> Om du vill flytta data måste du synkroniserat kopiera blobbar från kontot för lagring av blockblobb till hot access-nivån i ett annat konto med [api:et För att blockera från](/rest/api/storageservices/put-block-from-url) eller en version av AzCopy som stöder det här API:et.
> *Api:et Förinställ blockera från* URL kopierar avsiktligt data på servern, vilket innebär att anropet slutförs först när alla data har flyttats från den ursprungliga serverplatsen till målplatsen.

### <a name="blob-level-tiering-billing"></a>Fakturering för blobnivåindelning

När en blob överförs eller flyttas till den frekventa, svala eller arkivnivån debiteras den med motsvarande hastighet omedelbart efter nivåändring.

När en blob flyttas till en svalare nivå (hot->cool, hot->arkiv eller cool->arkiv), faktureras åtgärden som en skrivåtgärd till målnivån, där skrivåtgärden (per 10 000) och dataskrivningsavgifter (per GB) för målnivån gäller.

När en blob flyttas till en varmare nivå (arkiv->cool, arkiv->hot, eller cool->hot), faktureras åtgärden som en läsning från källnivån, där läsåtgärden (per 10 000) och datahämtning (per GB) avgifter för källnivån gäller. Kostnader för tidig borttagning av blobbar från lågfrekvent lagring eller arkivlagring kan också tillkomma. [Att rehydrera data från arkivet](storage-blob-rehydration.md) tar tid och data debiteras arkivpriser tills data återställs online och blob-nivån ändras till varm eller sval. I följande tabell sammanfattas hur nivåändringar faktureras:

| | **Skrivavgifter (Operation + Access)** | **Läsavgifter (Operation + Access)**
| ---- | ----- | ----- |
| **SetBlobTier riktning** | hot->cool,<br> hot->arkiv,<br> cool->arkiv | arkiv->cool,<br> arkiv->varm,<br> cool->varm

### <a name="cool-and-archive-early-deletion"></a>Tidig borttagning i lågfrekvent lagring och i arkivlagring

Alla blob som flyttas till den svala nivån (endast GPv2-konton) är föremål för en sval tidig borttagningsperiod på 30 dagar. Alla blob som flyttas till arkivnivån är föremål för en tidig borttagningsperiod för arkiv på 180 dagar. Den här kostnaden beräknas proportionellt. Om en blob till exempel flyttas till arkivet och sedan tas bort eller flyttas till den heta nivån efter 45 dagar debiteras du en avgift för tidig borttagning motsvarande 135 (180 minus 45) dagar för att lagra bloben i arkivet.

Du kan beräkna den tidiga borttagningen med hjälp av **blob-egenskapen, Senast ändrad,** om det inte har skett några ändringar på åtkomstnivån. Annars kan du använda när åtkomstnivån senast ändrades för att svalna eller arkivera genom att visa blob-egenskapen: **access-tier-change-time**. Mer information om blob-egenskaper finns i [Hämta Blob-egenskaper](https://docs.microsoft.com/rest/api/storageservices/get-blob-properties).

## <a name="comparing-block-blob-storage-options"></a>Jämföra alternativ för lagring av blockblob

Följande tabell visar en jämförelse av premium prestandablock blob lagring och hot, cool och arkiv åtkomstnivåer.

|                                           | **Premium prestanda**   | **Hot nivå** | **Cool nivå**       | **Arkivnivå**  |
| ----------------------------------------- | ------------------------- | ------------ | ------------------- | ----------------- |
| **Tillgänglighet**                          | 99,9 %                     | 99,9 %        | 99 %                 | Offline           |
| **Tillgänglighet** <br> **(RA-GRS-läsningar)**  | Ej tillämpligt                       | 99,99 %       | 99,9 %               | Offline           |
| **Avgifter för användning**                         | Högre lagringskostnader, lägre åtkomst och transaktionskostnader | Högre lagringskostnader, lägre åtkomst och transaktionskostnader | Lägre lagringskostnader, högre åtkomst och transaktionskostnader | Lägsta lagringskostnader, högsta åtkomst och transaktionskostnader |
| **Minsta objektstorlek**                   | Ej tillämpligt                       | Ej tillämpligt          | Ej tillämpligt                 | Ej tillämpligt               |
| **Minsta lagringstid**              | Ej tillämpligt                       | Ej tillämpligt          | 30 dagar<sup>1</sup> | 180 dagar
| **Svarstid** <br> **(Tid till första byte)** | Ensiffriga millisekunder | millisekunder | millisekunder        | timmar<sup>2</sup> |

<sup>1</sup> Objekt i den svala nivån på GPv2-konton har en minsta kvarhållningstid på 30 dagar. Blob storage-konton har inte en minsta kvarhållningstid för den coola nivån.

<sup>2</sup> Arkivlagring stöder för närvarande 2 rehydrerade prioriteringar, Hög och Standard, som erbjuder olika hämtningsfördytor. Mer information finns i [Rehydrate blob data från arkivnivån](storage-blob-rehydration.md).

> [!NOTE]
> Blob storage-konton stöder samma prestanda- och skalbarhetsmål som virtuella 2-lagringskonton för allmänna ändamål. Mer information finns i [Skalbarhets- och prestandamål för Blob-lagring](scalability-targets.md).

## <a name="quickstart-scenarios"></a>Snabbstartsscenarier

I det här avsnittet demonstreras följande scenarier med Hjälp av Azure-portalen och powershell:

- Ändra standardåtkomstnivån för ett GPv2- eller Blob Storage-konto.
- Ändra åtkomstnivån för en blob i ett GPv2- eller Blob Storage-konto.

### <a name="change-the-default-account-access-tier-of-a-gpv2-or-blob-storage-account"></a>Ändra standardåtkomstnivå för ett GPv2- eller Blob Storage-konto

# <a name="portal"></a>[Portalen](#tab/azure-portal)
1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Sök efter och välj **Alla resurser**i Azure-portalen .

1. Välj ditt lagringskonto.

1. Välj **Konfiguration** i **Inställningar**för att visa och ändra kontokonfigurationen.

1. Välj rätt åtkomstnivå för dina behov: Ställ in **åtkomstnivån** på antingen **Cool** eller **Hot**.

1. Klicka på **Spara** högst upp.

![Ändra lagringskontonivå](media/storage-tiers/account-tier.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Följande PowerShell-skript kan användas för att ändra kontonivån. Variabeln `$rgName` måste initieras med resursgruppsnamnet. Variabeln `$accountName` måste initieras med ditt lagringskontonamn. 
```powershell
#Initialize the following with your resource group and storage account names
$rgName = ""
$accountName = ""

#Change the storage account tier to hot
Set-AzStorageAccount -ResourceGroupName $rgName -Name $accountName -AccessTier Hot
```
---

### <a name="change-the-tier-of-a-blob-in-a-gpv2-or-blob-storage-account"></a>Ändra nivån för en blob i ett GPv2- eller Blob-lagringskonto
# <a name="portal"></a>[Portalen](#tab/azure-portal)
1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Sök efter och välj **Alla resurser**i Azure-portalen .

1. Välj ditt lagringskonto.

1. Markera din behållare och välj sedan din blob.

1. Välj **Ändra nivå**i **Blob-egenskaperna**.

1. Välj åtkomstnivån **Hot**, **Cool**eller **Arkiv.** Om din blob är i arkiv och du vill rehydrera till en online-nivå kan du också välja en Rehydrerad prioritet för **standard** eller **hög**.

1. Välj **Spara** längst ned.

![Ändra lagringskontonivå](media/storage-tiers/blob-access-tier.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Följande PowerShell-skript kan användas för att ändra blob-nivån. Variabeln `$rgName` måste initieras med resursgruppsnamnet. Variabeln `$accountName` måste initieras med ditt lagringskontonamn. Variabeln `$containerName` måste initieras med ditt behållarnamn. Variabeln `$blobName` måste initieras med blobnamnet. 
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

Alla lagringskonton använder en prismodell för Block blob-lagring baserat på nivån för varje blob. Tänk på följande faktureringsöverväganden:

- **Lagringskostnader**: Utöver mängden data som lagras varierar lagringskostnaden beroende på åtkomstnivå. Kostnaden per gigabyte minskas när nivån blir mer lågfrekvent.
- **Kostnader för dataåtkomst**: Kostnaderna för dataåtkomst ökar när nivån blir mer lågfrekvent. För data på den häftiga åtkomstnivån och arkivåtkomstnivån debiteras du en dataåtkomstavgift per gigabyte för läsningar.
- **Transaktionskostnader**: Det finns en avgift per transaktion för alla nivåer som ökar när nivån blir svalare.
- **Dataöverföringskostnader för geo-replikering**: Den här avgiften gäller endast konton med konfigurerad geo-replikering, inklusive GRS och RA-GRS. Dataöverföring för geo-replikering debiteras per gigabyte.
- **Kostnader för utgående dataöverföring**: Utgående dataöverföringar (data som överförs utanför en Azure-region) debiteras för bandbreddsanvändning per gigabyte, på samma sätt som för allmänna lagringskonton.
- **Ändra åtkomstnivån:** Ändra kontoåtkomstnivån resulterar i nivåändringsavgifter för _åtkomstnivåinledda_ blobbar som lagras i kontot som inte har en explicit nivåuppsättning. Information om hur du ändrar åtkomstnivån för en enskild blob finns i [fakturering på Blob-nivånivå](#blob-level-tiering-billing).

> [!NOTE]
> Mer information om priser för Block blobbar finns på sidan [Azure Storage Pricing.](https://azure.microsoft.com/pricing/details/storage/blobs/) Mer information om kostnaderna för utgående dataöverföring finns på sidan [Prisinformation om Dataöverföringar](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

**Bör jag använda Blob Storage- eller GPv2-konton om jag vill lagra data i olika nivåer?**

Vi rekommenderar att du använder GPv2 i stället för Blob Storage-konton om du vill använda lagringsnivåer. GPv2 har stöd för samma funktioner som Blob Storage-konton, och mycket mer. Kostnaden för Blob Storage- och GPv2-konton är i stort sett densamma, men vissa nya funktioner och rabatter kommer bara att vara tillgängliga för GPv2-konton. GPv1-konton stöder inte nivåindelning.

Prisstrukturen för GPv1- och GPv2-konton skiljer sig åt, så kunderna bör noggrant utvärdera båda alternativen innan de bestämmer sig för att använda GPv2-konton. Du kan enkelt konvertera ett befintligt Blob Storage eller GPv1-konto till GPv2 via en enklicksprocess. Mer information finns i [kontoöversikten för Azure Storage](../common/storage-account-overview.md).

**Kan jag lagra objekt i alla tre (heta, coola och arkivera) åtkomstnivåer i samma konto?**

Ja. **Attributet Access Tier** som angetts på kontonivå är standardkontonivån som gäller för alla objekt i det kontot utan en explicit uppsättning nivå. Med Blob-nivånivå kan du ange åtkomstnivån på objektnivå oavsett vad inställningen för åtkomstnivå på kontot är. Blobbar i någon av de tre åtkomstnivåerna (hot, cool eller archive) kan finnas inom samma konto.

**Kan jag ändra standardåtkomstnivån för mitt Blob- eller GPv2-lagringskonto?**

Ja, du kan ändra standardkontonivån genom att ange attributet **Access-nivå** på lagringskontot. Att ändra kontonivån gäller för alla objekt som lagras i kontot och som inte har en explicit nivåuppsättning (till exempel **Hot (inferred)** eller **Cool (inferred).** Växla kontonivån från frekvent till sval ådrar skrivåtgärder (per 10 000) för alla blobbar utan en viss nivå i GPv2-konton och växla från sval till frekvent ådrar sig både läsåtgärder (per 10 000) och avgifter för datahämtning (per GB) för alla blobbar i Blob-lagring och GPv2-konton.

**Kan jag ange arkivlagring som standardnivå för mitt konto?**

Nej. Endast nivåer för frekvent och kall åtkomst kan anges som standardåtkomstnivå för konto. Arkivlagringsnivån kan endast anges på objektnivå. Vid blob-uppladdning anger du vilken åtkomstnivå du väljer som du vill vara het, cool eller arkivera oavsett standardkontonivå. Med den här funktionen kan du skriva data direkt till arkivnivån för att realisera kostnadsbesparingar från det ögonblick du skapar data i blob-lagring.

**I vilka regioner är de heta, svala och arkivåtkomstnivåerna tillgängliga i?**

De aktiva och svala åtkomstnivåerna tillsammans med blob-nivåskiktning är tillgängliga i alla regioner. Arkivlagring är inledningsvis endast tillgängligt i vissa regioner. En fullständig lista finns under [Tillgängliga Azure-produkter efter region](https://azure.microsoft.com/regions/services/).

**Beter sig blobbar på den coola åtkomstnivån annorlunda än de på nivån för frekvent åtkomst?**

Blobbar på hot access-nivån har samma svarstid som blobbar i GPv1-, GPv2- och Blob-lagringskonton. Blobbar på den häftiga åtkomstnivån har en liknande svarstid (i millisekunder) som blobbar i GPv1-, GPv2- och Blob-lagringskonton. Blobbar i arkivåtkomstnivån har flera timmars svarstid i GPv1-, GPv2- och Blob-lagringskonton.

Blobbar på den låga åtkomstnivån har en något lägre tillgänglighetstjänstnivå (SLA) än blobbar som lagras på nivån för frekvent åtkomst. Mer information finns i [SLA för Storage](https://azure.microsoft.com/support/legal/sla/storage/v1_2/).

**Kan samma åtgärder användas för lagringsnivåerna frekvent, lågfrekvent och arkivlagring?**

På lagringsnivåerna frekvent och lågfrekvent fungerar alla åtgärder på samma sätt. Alla giltiga arkivoperationer inklusive GetBlobProperties, GetBlobMetadata, ListBlobs, SetBlobTier och DeleteBlob är 100% förenliga med varmt och svalt. Blob-data kan inte läsas eller ändras på arkivnivån förrän de har rehydrerats. Endast blobmetadataläsningsåtgärder stöds i arkiv.

**När jag återställer en blob från arkivlagring till frekvent eller lågfrekvent lagring, hur vet jag när processen är klar?**

Under rehydrering kan du använda åtgärden hämta blob-egenskaper för att avsöka **attributet Arkivstatus** och bekräfta när nivåändringen är klar. Status är ”rehydrate-pending-to-hot” eller ”rehydrate-pending-to-cool” beroende på målnivån. När den har slutförts tas egenskapen arkivstatus bort och **blob-egenskapen Access Tier** återspeglar den nya aktiva eller svala nivån. Mer information [finns i Rehydrate blob-data från arkivnivån.](storage-blob-rehydration.md)

**När jag har angett nivå för en blob: hur lång tid tar det innan jag börjar debiteras för den nya nivån?**

Varje blob faktureras alltid enligt den nivå som anges av blobens egenskap **Access Tier.** När du anger en ny onlinenivå för en blob återspeglar egenskapen **Access Tier** omedelbart den nya nivån för alla övergångar. Det kan dock ta flera timmar att rehydrera en blob från offlinearkivnivån till en frekvent eller låg nivå. I det här fallet debiteras du till arkivpriser tills återfuktningen är klar, då egenskapen **Access Tier** återspeglar den nya nivån. När du har rehydrerats till online-nivån debiteras du för den bloben med den varma eller svala hastigheten.

**Hur avgör jag om jag ska ådra mig en avgift för tidig borttagning när jag tar bort eller flyttar en blob från den svala nivån eller arkivnivån?**

Om en blob tas bort eller flyttas från lågfrekvent lagring (endast GPv2-konton) inom 30 dagar, eller från arkivlagring inom 180 dagar, påförs en proportionellt beräknad avgift för tidig borttagning. Du kan bestämma hur länge en blob har varit på den svala nivån eller arkivnivån genom att kontrollera **blob-egenskapen Åtkomstnivåändringstid,** som ger en stämpel för den senaste nivåändringen. Om blob-nivån aldrig har ändrats kan du kontrollera egenskapen **Senast ändrad** blob. Mer information finns i [Cool och arkivera tidig radering](#cool-and-archive-early-deletion).

**Vilka Azure-verktyg och SDK:er stöder blobnivåindelning och arkivlagring?**

Azure Portal, PowerShell, CLI-verktyg och klientbiblioteken för .NET, Java, Python och Node.js har stöd för blobnivåindelning och arkivlagring.  

**Hur mycket data kan jag lagra på lagringsnivåerna frekvent, lågfrekvent och arkiv?**

Datalagring tillsammans med andra gränser anges på kontonivå och inte per åtkomstnivå. Du kan välja att använda alla dina gränser på en nivå eller på alla tre nivåer. Mer information finns i [Skalbarhets- och prestandamål för standardlagringskonton](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="next-steps"></a>Nästa steg

Utvärdera frekventa, svala och arkivera i GPv2- och Blob-lagringskonton

- [Kontrollera tillgängligheten för frekvent/lågfrekvent lagring och arkivlagring efter region](https://azure.microsoft.com/regions/#services)
- [Hantera Azure Blob Storage-livscykeln](storage-lifecycle-management-concepts.md)
- [Lär dig mer om att rehydrera blob-data från arkivnivån](storage-blob-rehydration.md)
- [Ta reda på om premiumprestanda skulle gynna din app](storage-blob-performance-tiers.md)
- [Utvärdera användningen av dina aktuella lagringskonton genom att aktivera mätvärden i Azure Storage.](../common/storage-enable-and-view-metrics.md)
- [Kontrollera priser för frekvent/lågfrekvent lagring och arkivlagring i Blob Storage-/GPv2-konton efter region](https://azure.microsoft.com/pricing/details/storage/)
- [Se priser för dataöverföring](https://azure.microsoft.com/pricing/details/data-transfers/)
