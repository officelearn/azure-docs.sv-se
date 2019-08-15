---
title: Frekventa, låg frekventa och Arkiv åtkomst nivåer för blobbar – Azure Storage
description: Åtkomst nivåer för frekvent åtkomst, låg frekvent åtkomst och arkivering för Azure Storage-konton.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/23/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: clausjor
ms.openlocfilehash: 8f180308133ffba12cc3bffb19130aa7a129da9f
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/12/2019
ms.locfileid: "68952908"
---
# <a name="azure-blob-storage-hot-cool-and-archive-access-tiers"></a>Azure Blob Storage: frekvent åtkomst, låg frekvent åtkomst och Arkiv lag rings nivåer

Azure Storage erbjuder olika åtkomst nivåer, vilket gör att du kan lagra BLOB-Datadata på det mest kostnads effektiva sättet. Tillgängliga åtkomst nivåer är:

- **Snabb** – optimerad för att lagra data som används ofta.
- **Cool** -optimerad för att lagra data som inte används ofta och som lagras i minst 30 dagar.
- **Arkiv** -optimerad för att lagra data som sällan används och lagras i minst 180 dagar med flexibla latens krav (i antal timmar).

Följande överväganden gäller för olika åtkomst nivåer:

- Endast frekventa och frekventa åtkomst nivåer kan ställas in på konto nivå. Arkiv åtkomst nivån är inte tillgänglig på konto nivå.
- Nivåerna frekvent, låg frekvent och Arkiv lag rings nivå kan anges på blobnivå.
- Data i låg frekvent åtkomst nivå kan tolerera något lägre tillgänglighet, men kräver fortfarande hög hållbarhet, hämtnings tid och data flödes egenskaper som liknar frekventa data. För låg frekvent data är ett något lägre tillgänglighets avtal på service nivå (SLA) och högre åtkomst kostnader jämfört med frekventa data acceptabla för lägre lagrings kostnader.
- Arkiv lag ring lagrar data offline och erbjuder de lägsta lagrings kostnaderna, men även de högsta data rehydratiseras-och åtkomst kostnaderna.

Data som lagras i molnet växer i exponentiell takt. Om du vill hålla kontroll på och optimera kostnaderna för dina växande lagringsbehov är det en bra idé att ordna data baserat på attribut som åtkomstfrekvens och planerad kvarhållningsperiod. Data som lagras i molnet kan vara olika vad gäller hur de genereras, bearbetas och används under dess livs längd. Vissa data används aktivt och ändras under livslängden. Vissa data används ofta i början av livslängden och sedan minskar användning drastiskt när dessa data blir äldre. Vissa data är inaktiva i molnet och är sällan, om de någonsin används, efter att de har lagrats.

Var och en av dessa data åtkomst scenarier fördelar från en annan åtkomst nivå som är optimerad för ett visst åtkomst mönster. Med nivåerna frekvent åtkomst, låg frekvent åtkomst och Arkiv lag rings nivå kan Azure Blob-lagring hantera det här behovet av differentierade åtkomst nivåer med separata pris modeller.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="storage-accounts-that-support-tiering"></a>Lagringskonton med stöd för flera lagringsnivåer

Objekt lagrings data skiktning till frekvent, låg frekvent eller arkivering stöds bara i Blob Storage-och Generell användning v2-konton (GPv2). GPv1-konton (General Purpose v1) har inte stöd för flera lagringsnivåer. Kunder kan dock enkelt konvertera sina befintliga GPv1- eller Blob Storage-konton till GPv2-konton via en enklicksprocess i Azure Portal. GPv2 tillhandahåller en ny pris struktur för blobbar, filer och köer, samt åtkomst till en mängd nya lagrings funktioner. I framtiden kommer vissa nya funktioner och rabatter dessutom endast att erbjudas för GPv2-konton. Kunderna bör därför utvärdera användningen av GPv2-konton efter en omfattande utvärdering av den nya prissättningen eftersom vissa arbets belastningar kan vara dyrare på GPv2 än GPv1. Mer information finns i [kontoöversikten för Azure Storage](../common/storage-account-overview.md).

Blob Storage-och GPv2-konton exponerar attributet **åtkomst nivå** på konto nivå. Med det här attributet kan du ange standard åtkomst nivån som frekvent eller låg frekvent för alla blobar i lagrings kontot som inte har en explicit nivå angiven på objekt nivå. För objekt där nivån har ställts in på objektnivå används inte nivåinställningen för kontot. Arkiv nivån kan endast tillämpas på objekt nivå. Du kan när som helst växla mellan dessa åtkomst nivåer.

## <a name="premium-performance-block-blob-storage"></a>Förstklassigt prestanda Block Blob Storage

Med Premium Performance Block Blob Storage kan du använda data som används ofta och som är tillgängliga via maskin vara med hög prestanda. Data i den här prestanda nivån lagras i solid-state-hårddiskar (SSD), som är optimerade för låg och konsekvent svars tid. SSD ger högre transaktions hastigheter och data flöde jämfört med traditionella hård diskar.

Förstklassiga prestanda Block Blob Storage är perfekt för arbets belastningar som kräver snabba och konsekventa svars tider. Det är bäst för arbets belastningar som utför många små transaktioner, till exempel insamling av telemetridata, meddelanden och datatransformering. Data som omfattar slutanvändare, till exempel interaktiv video redigering, statiskt webb innehåll och onlinetransaktioner är också bra kandidater.

Förstklassiga prestanda Block Blob Storage är endast tillgängligt via Block Blob Storage-konto typen och stöder för närvarande inte nivåer för frekvent åtkomst, låg frekvent åtkomst eller Arkiv lag rings nivå.

## <a name="hot-access-tier"></a>Frekvent åtkomstnivå

Frekvent åtkomst nivå har högre lagrings kostnader än låg frekvent lagring och Arkiv lag ring, men de lägsta åtkomst kostnaderna. Exempel på användnings scenarier för frekvent åtkomst nivå är:

- Data som används i aktivt läge eller förväntas nås (läses från och skrivs till) ofta.
- Data som mellanlagras för bearbetning och eventuell migrering till låg frekvent åtkomst nivå.

## <a name="cool-access-tier"></a>Lågfrekvent åtkomstnivå

Låg frekvent åtkomst nivå har lägre kostnader för lagring och högre åtkomst kostnader jämfört med frekvent lagring. Den här nivån är avsedd för data som är kvar på den lågfrekventa nivån i minst 30 dagar. Exempel på användnings scenarier för låg frekvent åtkomst nivå är:

- Datauppsättningar för kortsiktig säkerhetskopiering och haveriberedskap.
- Äldre medieinnehåll som inte visas så ofta längre, men som förväntas vara tillgängligt direkt vid behov.
- Stora datamängder som behöver en kostnadseffektiv lagring under tiden mer data samlas in för framtida bearbetning. (*Till exempel* långsiktig lagring av vetenskapliga data eller telemetridata (rådata) från en tillverkningsanläggning.)

## <a name="archive-access-tier"></a>Arkivåtkomstnivå

Arkiv åtkomst nivån har lägst lagrings kostnad och högre kostnader för data hämtning jämfört med frekventa och låg frekventa nivåer. Den här nivån är avsedd för data som kan tolerera flera timmars fördröjning vid hämtning och som finns kvar på arkivnivån i minst 180 dagar.

När en BLOB finns i Arkiv lag ring, är BLOB-data offline och kan inte läsas, kopieras, skrivas över eller ändras. Du kan inte ta ögonblicks bilder av en BLOB i Arkiv lag ring. BLOB-metadata är dock online och tillgängliga, så att du kan lista bloben och dess egenskaper. För blobbar i arkivet är de enda giltiga åtgärderna GetBlobProperties, GetBlobMetadata, ListBlobs, SetBlobTier och DeleteBlob.

Exempel på användnings scenarier för Arkiv åtkomst nivån är:

- Långsiktig säkerhetskopiering, sekundär säkerhetskopiering och datauppsättningar för arkivering
- Ursprungliga rådata som måste bevaras, även efter att de har bearbetats till ett slutligt användbart format. (*Till exempel* mediefiler i RAW-format som har omkodats till andra format.)
- Efterlevnads- och arkiveringsdata som behöver lagras under en längre tid och som nästan aldrig används. (*Till exempel*säkerhets kamera tagning, gamla X-strålar/magnet för sjukvårds organisationer, ljud inspelningar och avskrifter av kund samtal för finansiella tjänster)

### <a name="blob-rehydration"></a>Återuppväckning av blob

[!INCLUDE [storage-blob-rehydrate-include](../../../includes/storage-blob-rehydrate-include.md)]

## <a name="account-level-tiering"></a>Skiktning på konto nivå

Blobbar i alla tre åtkomst nivåer kan finnas i samma konto. En blob som inte har en uttryckligen tilldelad nivå härleder nivån från kontots åtkomstnivåinställning. Om åtkomst nivån härleds från kontot ser du att egenskapen **åtkomst nivå härledd** BLOB har angetts till true och att BLOB-egenskapen för BLOB-åtkomstnivå matchar konto nivån. I Azure Portal visas egenskapen _härledd åtkomst nivå_ med BLOB-åtkomst nivån som **aktiv (härledd)** eller låg frekvent **(härledd)** .

Att ändra kontots åtkomst nivå gäller för alla härledda objekt i _åtkomst nivån_ som lagras i kontot som inte har någon explicit nivå uppsättning. Om du ändrar kontonivån från frekvent till lågfrekvent debiteras du för skrivåtgärder (per 10 000) för blobbar som saknar angiven nivå, men endast i GPv2-konton. Det kostar inget att ändra i Blob Storage-konton. Du debiteras för både Läs åtgärder (per 10 000) och data hämtning (per GB) om du växlar från låg frekvent till frekvent i Blob Storage-eller GPv2-konton.

## <a name="blob-level-tiering"></a>Blobnivåindelning

Med blobnivåindelning kan du ändra nivå för dina data på objektnivå med hjälp av en enda åtgärd som kallas [Ange blobnivå](/rest/api/storageservices/set-blob-tier). Du kan enkelt ändra åtkomstnivå för en blob mellan frekvent, lågfrekvent eller arkivnivå när användningsmönster ändras, utan att behöva flytta data mellan konton. Alla nivå ändringar sker omedelbart. Det kan dock ta flera timmar att återuppväcks en BLOB från arkivet.

Tiden för den senaste ändringen på blobnivån är tillgänglig via blobegenskapen **Ändringstid för åtkomstnivå**. Om en BLOB finns på Arkiv nivån kan den inte skrivas över, så överföring av samma BLOB tillåts inte i det här scenariot. Du kan skriva över en BLOB på en frekvent eller låg frekvent nivå, och i så fall ärver den nya blobben nivån i bloben som skrevs över.

> [!NOTE]
> Arkivlagring och blobnivåindelning stöder endast blockblobar. Du kan inte heller ändra nivån på en Block-Blob som har ögonblicks bilder.

### <a name="blob-lifecycle-management"></a>Hantering av BLOB-livscykel

Blob Storage livs cykel hantering ger en omfattande, regelbaserade princip som du kan använda för att överföra data till bästa åtkomst nivå och för att förfalla data i slutet av livs cykeln. Mer information finns i [Hantera Azure Blob Storage](storage-lifecycle-management-concepts.md) -livscykeln.  

> [!NOTE]
> Data som lagras i ett Block Blob Storage-konto (Premium prestanda) kan för närvarande inte på nivå till frekvent, låg frekvent eller arkiveras med hjälp av [Ange BLOB-nivå](/rest/api/storageservices/set-blob-tier) eller använda Azure Blob Storage livs cykel hantering.
> Om du vill flytta data måste du synkront kopiera blobar från Block Blob Storage-kontot till frekvent åtkomst nivå i ett annat konto med hjälp av [undantag från URL-API](/rest/api/storageservices/put-block-from-url) eller en version av AzCopy som stöder detta API.
> *Placera block från URL* -API: n kopierar synkront data på servern, vilket innebär att anropet slutförs endast när alla data har flyttats från den ursprungliga Server platsen till mål platsen.

### <a name="blob-level-tiering-billing"></a>Fakturering för blobnivåindelning

När en BLOB flyttas till en låg frekvent nivå (frekvent > låg frekvent lagring, > Arkiv lag ring eller cool-> Arkiv) faktureras åtgärden som en Skriv åtgärd till mål nivån, där avgifter för Skriv åtgärden (per 10 000) och data skrivning (per GB) för mål nivån tillämpas.

När en BLOB flyttas till en varm nivå (Arkiv-> låg frekvent, Arkiv-> frekvent eller låg frekvent > frekvent) debiteras åtgärden som en läsning från käll nivån, där avgifterna Läs åtgärd (per 10 000) och data hämtning (per GB) för käll nivån tillämpas. Kostnader för tidig borttagning av blobbar från lågfrekvent lagring eller arkivlagring kan också tillkomma. I följande tabell sammanfattas hur nivå ändringar faktureras.

| | **Skriv avgifter (åtgärd + åtkomst)** | **Läs avgifter (åtgärd + åtkomst)**
| ---- | ----- | ----- |
| **SetBlobTier riktning** | varm-> sval,<br> Hot-> Arkiv,<br> kall > Arkiv | Arkiv-> häftigt,<br> Arkiv-> varmt,<br> hett > frekvent

### <a name="cool-and-archive-early-deletion"></a>Tidig borttagning i lågfrekvent lagring och i arkivlagring

Utöver kostnaderna per GB och per månad kan kostnader tillkomma för tidig borttagning. För blobbar som flyttas till lågfrekvent lagring (gäller endast GPv2-konton) är den minsta lagringstiden 30 dagar, och för blobbar som flyttas till arkivlagring är den minsta lagringstiden 180 dagar. Den här kostnaden beräknas proportionellt. Exempel: Om en blob flyttas till arkivlagring och sedan tas bort eller flyttas till frekvent nivå efter 45 dagar, debiteras du en avgift för tidig borttagning motsvarande 135 (180 minus 45) dagars arkivlagring av blobben.

Du kan beräkna tidig borttagningen med hjälp av BLOB-egenskapen, **senast ändrad**, om det inte har gjorts några ändringar i åtkomst nivån. Annars kan du använda när åtkomst nivån senast ändrades till låg frekvent eller arkivera genom att visa egenskapen BLOB: **Access-Tier-Change-Time**. Mer information om BLOB-egenskaper finns i [Hämta BLOB-egenskaper](https://docs.microsoft.com/rest/api/storageservices/get-blob-properties).

## <a name="comparing-block-blob-storage-options"></a>Jämför lagrings alternativ för Block Blob

I följande tabell visas en jämförelse av Premium Performance Block Blob Storage och åtkomst nivåerna frekvent, låg frekvent och Arkiv lag ring.

|                                           | **Förstklassig prestanda**   | **Frekvent nivå** | **Låg frekvent nivå**       | **Arkiv lag ring**  |
| ----------------------------------------- | ------------------------- | ------------ | ------------------- | ----------------- |
| **Tillgänglighet**                          | 99,9 %                     | 99,9 %        | 99 %                 | Offline           |
| **Tillgänglighet** <br> **(RA-GRS-läsningar)**  | Gäller inte                       | 99,99 %       | 99,9 %               | Offline           |
| **Avgifter för användning**                         | Högre kostnader för lagring, lägre åtkomst och kostnad för transaktioner | Högre kostnader för lagring, lägre åtkomst och transaktionskostnader | Lägre kostnader för lagring, högre åtkomst och transaktionskostnader | Lägsta kostnader för lagring, högsta åtkomst och transaktionskostnader |
| **Minsta objektstorlek**                   | Saknas                       | Saknas          | Saknas                 | Saknas               |
| **Minsta lagringstid**              | Saknas                       | Gäller inte          | 30 dagar<sup>1</sup> | 180 dagar
| **Svarstid** <br> **(Tid till första byte)** | Ensiffriga millisekunder | millisekunder | millisekunder        | timmar<sup>2</sup> |

<sup>1</sup> objekt på den lägsta nivån i GPv2-konton har en minsta Retentions tid på 30 dagar. Blob Storage-konton har inte minsta Retentions tid för den låg frekventa nivån.

<sup>2</sup> arkivlagring för närvarande stöd för 2 reproduktion-prioriteringar, hög och standard, som erbjuder olika hämtnings fördröjningar. Mer information finns i [BLOB-ÅTERUPPVÄCKNING](#blob-rehydration).

> [!NOTE]
> Blob Storage-konton har stöd för samma prestanda-och skalbarhets mål som lagrings konton för generell användning v2. Mer information finns i [Azure Storage skalbarhets-och prestanda mål](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="quickstart-scenarios"></a>Snabbstartsscenarier

I det här avsnittet visas följande scenarier på Azure Portal:

- Ändra standardåtkomstnivån för ett GPv2- eller Blob Storage-konto.
- Ändra åtkomstnivån för en blob i ett GPv2- eller Blob Storage-konto.

### <a name="change-the-default-account-access-tier-of-a-gpv2-or-blob-storage-account"></a>Ändra standardåtkomstnivå för ett GPv2- eller Blob Storage-konto

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Gå till ditt lagringskonto genom att välja Alla resurser och välj sedan ditt lagringskonto.

1. Klicka på **Konfiguration** på bladet Inställningar för att visa och/eller ändra kontokonfigurationen.

1. Välj rätt åtkomst nivå för dina behov: Ange **åtkomst nivå** till antingen **cool** eller frekvent.

1. Klicka på **Spara** överst på bladet.

### <a name="change-the-tier-of-a-blob-in-a-gpv2-or-blob-storage-account"></a>Ändra nivån för en BLOB i ett GPv2-eller Blob Storage-konto

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Om du vill navigera till din BLOB i ditt lagrings konto väljer du **alla resurser**, väljer ditt lagrings konto, väljer din behållare och väljer sedan din BLOB.

1. På bladet **BLOB-egenskaper** väljer du List menyn **åtkomst nivå** för att väljaåtkomst nivånfrekvent, låg frekvent eller **Arkiv** .

1. Klicka på **Spara** överst på bladet.

## <a name="pricing-and-billing"></a>Priser och fakturering

Alla lagrings konton använder en pris modell för Block-Blob-lagring baserat på nivån för varje blob. Tänk på följande saker om fakturering:

- **Lagrings kostnader**: Utöver mängden data som lagras varierar kostnaden för att lagra data beroende på åtkomst nivå. Kostnaden per gigabyte minskas när nivån blir mer lågfrekvent.
- **Kostnader för data åtkomst**: Avgifter för data åtkomst ökar när nivån blir kylare. För data i den låg frekventa och Arkiv åtkomst nivån debiteras du en åtkomst avgift per Gigabyte för läsningar.
- **Transaktionskostnader**: Det finns en avgift per transaktion för alla nivåer som ökar när nivån blir kylare.
- **Kostnader för data överföring mellan geo-replikering**: Den här avgiften gäller endast konton med konfigurerad geo-replikering, inklusive GRS och RA-GRS. Dataöverföring för geo-replikering debiteras per gigabyte.
- **Kostnader för utgående data överföring**: Utgående data överföringar (data som överförs från en Azure-region) debiteras för bandbredds användning per GB, konsekvent med allmänna lagrings konton.
- **Ändra åtkomst nivå**: Att ändra kontots åtkomst nivå leder till att nivån ändrar avgifter för _åtkomst nivå härledda_ blobbar lagrade i kontot som inte har någon explicit nivå uppsättning. Information om hur du ändrar åtkomst nivå för en enskild BLOB finns på [nivå fakturering på BLOB-nivå](#blob-level-tiering-billing).

> [!NOTE]
> Mer information om priser för block blobbar finns [Azure Storage prissättnings](https://azure.microsoft.com/pricing/details/storage/blobs/) sida. Mer information om kostnaderna för utgående dataöverföring finns på sidan [Prisinformation om Dataöverföringar](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

**Bör jag använda Blob Storage- eller GPv2-konton om jag vill lagra data i olika nivåer?**

Vi rekommenderar att du använder GPv2 i stället för Blob Storage-konton om du vill använda lagringsnivåer. GPv2 har stöd för samma funktioner som Blob Storage-konton, och mycket mer. Kostnaden för Blob Storage- och GPv2-konton är i stort sett densamma, men vissa nya funktioner och rabatter kommer bara att vara tillgängliga för GPv2-konton. GPv1-konton stöder inte lagringsnivåer.

Prisstrukturen för GPv1- och GPv2-konton skiljer sig åt, så kunderna bör noggrant utvärdera båda alternativen innan de bestämmer sig för att använda GPv2-konton. Du kan enkelt konvertera ett befintligt Blob Storage eller GPv1-konto till GPv2 via en enklicksprocess. Mer information finns i [kontoöversikten för Azure Storage](../common/storage-account-overview.md).

**Kan jag lagra objekt i alla tre (frekventa, svala och arkiverade) åtkomst nivåer i samma konto?**

Ja. Attributet **åtkomst nivå** som anges på konto nivå är standard konto nivån som gäller för alla objekt i kontot utan en explicit uppsättnings nivå. Med blobnivåindelningen kan du ange åtkomstnivå på objektnivå oavsett vilken åtkomstnivå som angetts för kontot. Blobbar i någon av de tre åtkomst nivåerna (frekvent, låg frekvent eller Arkiv) kan finnas i samma konto.

**Kan jag ändra standard åtkomst nivån för mitt blob-eller GPv2-lagrings konto?**

Ja, du kan ändra standard konto nivån genom att ange attributet **åtkomst nivå** för lagrings kontot. Ändring av konto nivån gäller för alla objekt som lagras i kontot som inte har en explicit nivå uppsättning (till exempel frekvent **(härledd)** eller låg frekvent **(härledd)** ). När konto nivån växlas från frekvent till låg frekvent tillkommer Skriv åtgärder (per 10 000) för alla blobbar utan en uppsättnings nivå i GPv2-konton, och växling från låg frekvent till frekvent medför både Läs åtgärder (per 10 000) och data hämtning (per GB) avgifter för alla blobar i Blob Storage och GPv2-konton.

**Kan jag ange arkivlagring som standardnivå för mitt konto?**

Nej. Endast frekventa och frekventa åtkomst nivåer kan anges som standard åtkomst nivå för kontot. Arkivlagringsnivån kan endast anges på objektnivå.

**I vilka regioner är åtkomst nivåerna frekvent åtkomst, låg frekvent åtkomst och arkivering tillgängliga i?**

Nivåerna frekvent och låg frekvent åtkomst tillsammans med skiktning på BLOB-nivå är tillgängliga i alla regioner. Arkivlagring är inledningsvis endast tillgängligt i vissa regioner. En fullständig lista finns under [Tillgängliga Azure-produkter efter region](https://azure.microsoft.com/regions/services/).

**Beter sig Blobbarna på den frekventa åtkomst nivån annorlunda än de i frekvent åtkomst nivå?**

Blobbar i frekvent åtkomst nivå har samma svars tid som blobbar i GPv1-, GPv2-och Blob Storage-konton. Blobbar i låg frekvent åtkomst nivå har liknande svars tid (i millisekunder) som blobbar i GPv1-, GPv2-och Blob Storage-konton. Blobbar i Arkiv åtkomst nivån har flera timmars svars tid i GPv1-, GPv2-och Blob Storage-konton.

Blobbar i låg frekvent åtkomst nivå har något lägre tillgänglighets nivå (SLA) än blobbar som lagras i frekvent åtkomst nivå. Mer information finns i [SLA för Storage](https://azure.microsoft.com/support/legal/sla/storage/v1_2/).

**Kan samma åtgärder användas för lagringsnivåerna frekvent, lågfrekvent och arkivlagring?**

På lagringsnivåerna frekvent och lågfrekvent fungerar alla åtgärder på samma sätt. Alla giltiga Arkiv åtgärder inklusive GetBlobProperties, GetBlobMetadata, ListBlobs, SetBlobTier och DeleteBlob är 100% konsekventa med frekvent och låg frekvent. BLOB-data kan inte läsas eller ändras på Arkiv nivå tills den har rehydratiserats; endast Läs åtgärder för BLOB-metadata stöds i arkivet.

**När jag återställer en blob från arkivlagring till frekvent eller lågfrekvent lagring, hur vet jag när processen är klar?**

Under återställning kan du använda åtgärden Hämta blobegenskaper och kontrollera attributet **Arkivstatus** för att se när nivåändringen har genomförts. Status är ”rehydrate-pending-to-hot” eller ”rehydrate-pending-to-cool” beroende på målnivån. När åtgärden har slutförts tas blobegenskapen Arkivstatus bort och blobegenskapen **Åtkomstnivå** anger den nya nivån: frekvent eller lågfrekvent.  

**När jag har angett nivå för en blob: hur lång tid tar det innan jag börjar debiteras för den nya nivån?**

Varje Blob faktureras alltid enligt den nivå som anges av blobbets egenskap för **åtkomst nivå** . När du anger en ny nivå för en BLOB, Visar egenskapen **åtkomst nivå** omedelbart den nya nivån för alla över gångar. Men att återställa en blob från arkivnivån till en frekvent eller lågfrekvent nivå kan ta flera timmar. I det här fallet debiteras du enligt Arkiv taxa tills ÅTERUPPVÄCKNING är slutförd, där egenskapen **åtkomst nivå** visar den nya nivån. Vid den tidpunkten debiteras du för denna BLOB vid frekvent eller låg frekvent hastighet.

**Hur kan jag se om jag påförs en avgift för tidig borttagning när jag tar bort eller flyttar en blob från lågfrekvent lagring eller arkivlagring?**

Om en blob tas bort eller flyttas från lågfrekvent lagring (endast GPv2-konton) inom 30 dagar, eller från arkivlagring inom 180 dagar, påförs en proportionellt beräknad avgift för tidig borttagning. Du kan bestämma hur länge en BLOB har varit i låg frekvent lagrings nivå eller Arkiv lag ring genom att kontrol lera egenskapen för **ändrings tid för åtkomst nivå** , vilket ger en stämpel för den senaste nivå ändringen. Om blobens nivå aldrig har ändrats kan du kontrol lera den **senast ändrade** BLOB-egenskapen. Mer information finns i [kyl och arkivering tidig borttagning](#cool-and-archive-early-deletion).

**Vilka Azure-verktyg och SDK:er stöder blobnivåindelning och arkivlagring?**

Azure Portal, PowerShell, CLI-verktyg och klientbiblioteken för .NET, Java, Python och Node.js har stöd för blobnivåindelning och arkivlagring.  

**Hur mycket data kan jag lagra på lagringsnivåerna frekvent, lågfrekvent och arkiv?**

Data lagring tillsammans med andra gränser anges på konto nivå och inte per åtkomst nivå. Därför kan du välja att använda hela gränsen på en nivå eller på alla tre nivåer. Mer information finns i [Azure Storage skalbarhets-och prestanda mål](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="next-steps"></a>Nästa steg

### <a name="evaluate-hot-cool-and-archive-in-gpv2-and-blob-storage-accounts"></a>Utvärdera frekvent, låg frekvent och Arkiv lag ring i GPv2-och Blob Storage-konton

[Kontrollera tillgängligheten för frekvent/lågfrekvent lagring och arkivlagring efter region](https://azure.microsoft.com/regions/#services)

[Hantera Azure Blob Storage-livscykeln](storage-lifecycle-management-concepts.md)

[Utvärdera användningen av dina aktuella lagringskonton genom att aktivera mätvärden i Azure Storage.](../common/storage-enable-and-view-metrics.md)

[Kontrollera priser för frekvent/lågfrekvent lagring och arkivlagring i Blob Storage-/GPv2-konton efter region](https://azure.microsoft.com/pricing/details/storage/)

[Se priser för dataöverföring](https://azure.microsoft.com/pricing/details/data-transfers/)
