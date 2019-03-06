---
title: Premium, frekvent, lågfrekvent lagring och Arkivlagring lagring för BLOB - Azure Storage
description: Premium, lagring för frekvent, lågfrekvent lagring och Arkivlagring för Azure storage-konton.
services: storage
author: kuhussai
ms.service: storage
ms.topic: article
ms.date: 03/06/2019
ms.author: kuhussai
ms.subservice: blobs
ms.openlocfilehash: ec024f8f1cd411455a6cbb2a0b12e7b4751af5fe
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2019
ms.locfileid: "57405342"
---
# <a name="azure-blob-storage-premium-preview-hot-cool-and-archive-storage-tiers"></a>Azure Blob storage: Premium (förhandsversion), lagringsnivåerna frekvent, lågfrekvent lagring och Arkivlagring

## <a name="overview"></a>Översikt

Azure storage erbjuder olika lagringsnivåer, som gör att du kan lagra Blob-objekt på det mest kostnadseffektiva sättet. De tillgängliga nivåerna är:

- **Premium storage (förhandsversion)** tillhandahåller maskinvara med höga prestanda för data som används ofta.
 
- **Frekvent lagring**: är optimerad för att lagra data som används ofta. 

- **Lågfrekvent lagring** är optimerad för att lagra data som används sällan och som lagras i minst 30 dagar.
 
- **Arkivlagring** är optimerad för att lagra data som används sällan och som lagras i minst 180 dagar med flexibla svarstidskrav (i storleksordningen timmar).

Följande överväganden medföljer olika lagringsnivåer:

- Arkivlagringsnivån är endast tillgänglig på blobnivån och inte på lagringskontonivån.
 
- Data i den lågfrekventa lagringsnivån klarar lite lägre tillgänglighet, men kräver fortfarande hög hållbarhet och liknande time-åtkomst och dataflödesegenskaper som data frekvent åtkomstnivå. För lågfrekventa data, ett serviceavtal med lägre tillgänglighet och högre är åtkomstkostnader jämfört med frekventa data godtagbara med tanke på de lägre lagringskostnaderna.

- Arkivlagring är i frånkopplat tillstånd och erbjuder de lägsta lagringskostnaderna men även de högsta åtkomstkostnaderna.
 
- Endast frekvent och lågfrekvent lagringsnivåer kan anges på kontonivå. Arkivnivån kan för närvarande inte anges på kontonivå.
 
- Frekvent, lågfrekvent, och lagringsnivån kan ställas in på objektnivå.

Data som lagras i molnet växer i exponentiell takt. Om du vill hålla kontroll på och optimera kostnaderna för dina växande lagringsbehov är det en bra idé att ordna data baserat på attribut som åtkomstfrekvens och planerad kvarhållningsperiod. Data som lagras i molnet kan vara olika beroende på hur de genereras, bearbetas och används under livslängden. Vissa data används aktivt och ändras under livslängden. Vissa data används ofta i början av livslängden och sedan minskar användning drastiskt när dessa data blir äldre. Vissa data förblir inaktiva i molnet och används sällan, eller kanske aldrig, när de har lagrats.

För varje scenario finns en lagringsnivå som är optimerad för motsvarande åtkomstmönster. Med frekvent, lågfrekvent och Arkivlagringsnivå Azure Blob storage behovet av olika lagringsnivåer med olika prissättningsmodeller.

## <a name="storage-accounts-that-support-tiering"></a>Lagringskonton med stöd för flera lagringsnivåer

Du kan endast datanivå dina data för lagring av objekt för att frekvent, lågfrekvent eller Arkiv i Blob storage och generell användning v2 (GPv2)-konton. GPv1-konton (General Purpose v1) har inte stöd för flera lagringsnivåer. Kunder kan dock enkelt konvertera sina befintliga GPv1- eller Blob Storage-konton till GPv2-konton via en enklicksprocess i Azure Portal. GPv2 har en ny prisstruktur för blobbar, filer, och köer, och ger också åtkomst till en mängd andra nya lagringsfunktioner. I framtiden kommer vissa nya funktioner och rabatter dessutom endast att erbjudas för GPv2-konton. Kunderna bör därför överväga att använda GPv2-konton, men först efter att ha granskat priserna för alla tjänster, eftersom vissa arbetsbelastningar kan bli dyrare med GPv2 än GPv1. Mer information finns i [kontoöversikten för Azure Storage](../common/storage-account-overview.md).

BLOB storage- och GPv2-konton visas den **åtkomstnivå** attributet på kontonivå, vilket gör att du kan ange standardlagringsnivån som frekvent eller lågfrekvent för alla blobar i lagringskontot som inte har en explicit nivå angiven på den objektnivå. För objekt där nivån har ställts in på objektnivå används inte nivåinställningen för kontot. Nivån Arkivlagring kan endast tillämpas på objektnivå. Du kan växla mellan dessa lagringsnivåer när som helst.

## <a name="premium-access-tier"></a>Premium-åtkomstnivå

Tillgängligt i förhandsversionen är en Premium åtkomstnivå, vilket gör ofta använda data som är tillgängliga via maskinvara med höga prestanda. Data som lagras i den här nivån lagras på SSD-enheter som är optimerade för kortare svarstider och högre transaktionella priser jämfört med traditionella hårddiskar. Åtkomstnivå Premium är tillgängligt via Blockblob lagringskontotypen endast.

Den här nivån är perfekt för arbetsbelastningar som kräver snabb och konsekvent svarstider. Data som innebär att slutanvändare, till exempel interaktiva videoredigering, statiskt webbinnehåll, onlinetransaktioner och liknande är bra kandidater för nivån Premium och åtkomst. Den här nivån är utformad för arbetsbelastningar som utför många små transaktioner, till exempel samla in telemetridata, meddelanden och transformering av data.

Läs mer om förhandsversionen av [förhandsversion av Azure Premium-Bloblagring](https://azure.microsoft.com/blog/azure-premium-blob-storage-public-preview/).

## <a name="hot-access-tier"></a>Frekvent åtkomstnivå

Frekvent lagring har högre kostnader för lagring än lågfrekvent lagring och Arkivlagring lagring, men de lägsta åtkomstkostnaderna. Exempelscenarier för frekvent lagringsnivå är:

* Data används aktivt eller förväntas användas ofta (både för att läsa och skriva).
* Data som mellanlagras för bearbetning och eventuell migrering till lågfrekvent lagringsnivå.

## <a name="cool-access-tier"></a>Lågfrekvent åtkomstnivå

Lågfrekvent lagring har lägre kostnader för lagring och högre åtkomstkostnader jämfört med frekvent lagring. Den här nivån är avsedd för data som är kvar på den lågfrekventa nivån i minst 30 dagar. Exempelscenarier för lågfrekvent lagringsnivå är:

* Datauppsättningar för kortsiktig säkerhetskopiering och haveriberedskap.
* Äldre medieinnehåll som inte visas så ofta längre, men som förväntas vara tillgängligt direkt vid behov.
* Stora datamängder som behöver en kostnadseffektiv lagring under tiden mer data samlas in för framtida bearbetning. (*Till exempel* långsiktig lagring av vetenskapliga data eller telemetridata (rådata) från en tillverkningsanläggning.)

## <a name="archive-access-tier"></a>Arkivåtkomstnivå

Arkivlagring har lägst lagringskostnad och högre kostnader för datahämtning jämfört med frekvent och lågfrekvent lagring. Den här nivån är avsedd för data som kan tolerera flera timmars svarstid för hämtning och finns kvar på arkivnivån i minst 180 dagar.

När en blob finns i arkivlagring, blob-data är offline och inte kan läsas, kopieras, skrivas över eller ändrade. Inte heller kan du ta ögonblicksbilder av en blob i arkivlagring. Dock fortfarande blob-metadata är online och tillgänglig, där du kan visa blob och dess egenskaper. För blob-objekt i arkivet är de enda giltiga åtgärderna GetBlobProperties, GetBlobMetadata, ListBlobs, SetBlobTier och DeleteBlob. 


Exempelscenarier för arkivlagringsnivå är:

* Långsiktig säkerhetskopiering, sekundär säkerhetskopiering och datauppsättningar för arkivering
* Ursprungliga rådata som måste bevaras, även efter att de har bearbetats till ett slutligt användbart format. (*Till exempel* mediefiler i RAW-format som har omkodats till andra format.)
* Efterlevnads- och arkiveringsdata som behöver lagras under en längre tid och som nästan aldrig används. (*Till exempel* film från säkerhetskameror, gamla röntgenbilder/magnetröntgenbilder för vårdorganisationer, ljudinspelningar och transkript av kundsamtal för ekonomiska tjänster.)

### <a name="blob-rehydration"></a>Återuppväckning av blob
Om du vill läsa data i arkivlagring måste du först ändra nivå för din blob till frekvent eller lågfrekvent. Processen kallas återuppväckning och kan ta upp till 15 timmar. Stora rekommenderas för optimala prestanda. Återställning av flera små blobbar samtidigt kan ta ännu längre tid.

Under återuppväckning kan du kontrollera blobegenskapen **Arkivstatus** för att se om nivån har ändrats. Status är ”rehydrate-pending-to-hot” eller ”rehydrate-pending-to-cool” beroende på målnivån. Statusegenskapen Arkiv tas bort när åtgärden har slutförts, och **åtkomstnivå** blob-egenskapen återspeglar den nya frekvent eller lågfrekvent nivå.  

## <a name="blob-level-tiering"></a>Blobnivåindelning

Med blobnivåindelning kan du ändra nivå för dina data på objektnivå med hjälp av en enda åtgärd som kallas [Ange blobnivå](/rest/api/storageservices/set-blob-tier). Du kan enkelt ändra åtkomstnivå för en blob mellan nivåerna frekvent, lågfrekvent eller arkivnivå när användningsmönster ändras, utan att behöva flytta data mellan konton. Alla nivåändringar sker omedelbart. Men kan en blob återuppväcks från arkivet ta flera timmar. 

Tiden för den senaste ändringen på blobnivån är tillgänglig via blobegenskapen **Ändringstid för åtkomstnivå**. Om en blob finns i arkivnivån kan kan inte den skrivas över, så att ladda upp samma blob inte tillåts i det här scenariot. Du kan skriva över en blob i en frekvent eller lågfrekvent nivå, som ärver fall den nya bloben nivån för bloben som skrevs över.

Blobar i alla tre lagringsnivåer kan finnas tillsammans i samma konto. En blob som inte har en uttryckligen tilldelad nivå härleder nivån från kontots åtkomstnivåinställning. Om åtkomstnivån härleds från kontot, visas den **åtkomstnivå härledd** blob-egenskapen angetts till ”true”, och blobegenskapen **åtkomstnivå** matchar kontonivån. Egenskapen som härletts från åtkomstnivån i Azure Portal visas med blobåtkomstnivån (till exempel frekvent (härledd) eller lågfrekvent (härledd)).

> [!NOTE]
> Arkivlagring och blobnivåindelning stöder endast blockblobar. Du kan även ändra nivå för en blockblob som har ögonblicksbilder.

> [!NOTE]
> Data som lagras i åtkomstnivån Premium för närvarande kan inte nivåindelas för frekvent, lågfrekvent eller Arkiv med [ange Blobnivå](/rest/api/storageservices/set-blob-tier) eller med hjälp av Livscykelhantering för Azure Blob Storage. För att flytta data, måste du synkront kopiera blobar från Premium-åtkomst till frekvent med hjälp av den [placera Block från URL: en API](/rest/api/storageservices/put-block-from-url) eller en version av AzCopy som har stöd för detta API. Den *placera Block från URL: en* API synkront kopierar data på servern, vilket innebär att anropet har slutförts bara en gång alla data flyttas från den ursprungliga serverplatsen till målplatsen.

### <a name="blob-lifecycle-management"></a>Livscykelhantering för BLOB
Livscykelhantering för Blob Storage (förhandsversion) erbjuder en omfattande, regel-baserad princip som du kan använda för att överföra data till bästa åtkomstnivå och för att ta bort data i slutet av livscykeln. Se [hantering av Azure Blob storage-livscykeln](storage-lifecycle-management-concepts.md) vill veta mer.  

### <a name="blob-level-tiering-billing"></a>Fakturering för blobnivåindelning

När en blob flyttas till en mer lågfrekvent nivå (frekvent -> lågfrekvent, frekvent -> Arkiv eller lågfrekvent -> Arkiv) faktureras åtgärden som en skrivning till målnivån där Skrivåtgärden (per 10 000) och kostnaderna för dataskrivning (per GB) för målnivån tillämpas. När en blob flyttas till en varmare nivå (Arkiv -> lågfrekvent, Arkiv -> frekvent eller lågfrekvent -> frekvent) faktureras åtgärden som en läsning från källnivån där Läsåtgärden (per 10 000) och kostnaderna för datahämtning (per GB) för källnivån tillämpas. I följande tabell sammanfattas hur nivåändringar faktureras.

| | **Skriva avgifter (åtgärden + åtkomst)** | **Läs avgifter (åtgärden + åtkomst)** 
| ---- | ----- | ----- |
| **SetBlobTier Direction** | Hot->Cool, Hot->Archive, Cool->Archive | Archive->Cool, Archive->Hot, Cool->Hot

Om du ändrar kontonivån från frekvent till lågfrekvent debiteras du för skrivåtgärder (per 10 000) för alla blobbar utan en angiven lagringsnivå i GPv2-konton. Det finns ingen kostnad för den här ändringen i Blob storage-konton. Du kommer att debiteras för både läsåtgärder (per 10 000) och datahämtning (per GB) om du växlar ditt Blob storage eller GPv2-konto från lågfrekvent till frekvent. Avgifter för tidig borttagning för alla blobbar som flyttas från lågfrekvent nivå eller arkivnivå nivå kan också tillkomma.

### <a name="cool-and-archive-early-deletion"></a>Tidig borttagning i lågfrekvent lagring och i arkivlagring

Utöver de per GB per månad kan kostnader en blob flyttas till lågfrekvent lagring (endast GPv2-konton) är föremål för en tidig borttagningsperiod på 30 dagar och en blob flyttas till arkivlagring kan en tidig borttagningsperiod på 180 dagar. Den här kostnaden beräknas proportionellt. Exempel: om en blob flyttas till arkivet och sedan tas bort eller flyttas till frekvent nivå efter 45 dagar debiteras du en tidig borttagningsavgift som motsvarar 135 (180 minus 45) dagars blobben Arkiv.

Du kan beräkna tidig borttagning med hjälp av blob-egenskapen **Skapandetid**, om det har ingen åtkomst nivåändringar. Annars kan du använda när åtkomstnivån senast ändrades till lågfrekvent eller Arkiv genom att visa blob-egenskapen: **åtkomst-nivå--tid för**. Mer information om blobegenskaper finns i [hämta Blobegenskaper](https://docs.microsoft.com/rest/api/storageservices/get-blob-properties).

## <a name="comparison-of-the-storage-tiers"></a>Jämförelse av lagringsnivåerna

Följande tabell visar en jämförelse av frekvent, lågfrekvent och Arkivlagringsnivå.

| | **Frekvent lagringsnivå** | **Lågfrekvent lagringsnivå** | **Arkivlagringsnivå**
| ---- | ----- | ----- | ----- |
| **Tillgänglighet** | 99,9 % | 99 % | Saknas |
| **Tillgänglighet** <br> **(RA-GRS-läsningar)**| 99,99 % | 99,9 % | Saknas |
| **Avgifter för användning** | Högre kostnader för lagring, lägre transaktionskostnader för åtkomst, och | Lägre kostnader för lagring och högre transaktionskostnader | Lägst kostnader för lagring, högsta åtkomst och transaktionskostnader |
| **Minsta objektstorlek** | Saknas | Saknas | Saknas |
| **Minsta lagringstid** | Gäller inte | 30 dagar (endast GPv2) | 180 dagar
| **Svarstid** <br> **(Tid till första byte)** | millisekunder | millisekunder | < 15 timmar
| **Mål för skalbarhet och prestanda** | Samma som allmänna lagringskonton | Samma som allmänna lagringskonton | Samma som allmänna lagringskonton |

> [!NOTE]
> Blob Storage-konton har samma mål för prestanda och skalbarhet som allmänna lagringskonton. Mer information finns i [skalbarhet för lagring av Azure- och prestandamål](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). 

## <a name="quickstart-scenarios"></a>Snabbstartsscenarier

I det här avsnittet visas följande scenarier på Azure Portal:

* Ändra standardåtkomstnivån för ett GPv2- eller Blob Storage-konto.
* Ändra åtkomstnivån för en blob i ett GPv2- eller Blob Storage-konto.

### <a name="change-the-default-account-access-tier-of-a-gpv2-or-blob-storage-account"></a>Ändra standardåtkomstnivå för ett GPv2- eller Blob Storage-konto

1. Logga in på [Azure Portal](https://portal.azure.com).

2. Gå till ditt lagringskonto genom att välja Alla resurser och välj sedan ditt lagringskonto.

3. Klicka på **Konfiguration** på bladet Inställningar för att visa och/eller ändra kontokonfigurationen.

4. Välj rätt lagringsnivå för dina behov: Ange den **åtkomstnivå** antingen **lågfrekvent** eller **frekvent**.

5. Klicka på Spara överst på bladet.

### <a name="change-the-tier-of-a-blob-in-a-gpv2-or-blob-storage-account"></a>Ändra åtkomstnivå för en blob i ett GPv2- eller Blob Storage-konto.

1. Logga in på [Azure Portal](https://portal.azure.com).

2. Gå till din blob i lagringskontot genom att välja Alla resurser, ditt lagringskonto, din container och sedan välja din blob.

3. På bladet Blobegenskaper klickar du på listrutemenyn **Åtkomstnivå** för att välja lagringsnivån **Frekvent**, **Lågfrekvent** eller **Arkiv**.

5. Klicka på Spara överst på bladet.

## <a name="pricing-and-billing"></a>Priser och fakturering

Alla lagringskonton används en prissättningsmodell för blobblagring baserat på vilken nivå av varje blob. Tänk på följande för debitering:

* **Lagringskostnader**: Utöver mängden data som lagras, kostnaden för att lagra data som varierar beroende på lagringsnivå. Kostnaden per gigabyte minskas när nivån blir mer lågfrekvent.
* **Kostnader för dataåtkomst**: Öka kostnaderna för dataåtkomst när nivån blir mer lågfrekvent. För data i Coolbar och arkivlagringsnivån debiteras du en per-åtkomst gigabyte för läsningar.
* **Transaktionskostnader**: Det finns en kostnad för alla nivåer per transaktion som ökar när nivån blir mer lågfrekvent.
* **Dataöverföringskostnader för GEO-replikering**: Den här avgiften gäller endast konton med konfigurerad geo-replikering, inklusive GRS och RA-GRS. Dataöverföring för geo-replikering debiteras per gigabyte.
* **Kostnaderna för utgående dataöverföring**: Utgående dataöverföringar (data som överförs från en Azure-region) debiteras för bandbreddsanvändning regelbundet per gigabyte, konsekvent med allmänna lagringskonton.
* **Ändringar av lagringsnivån**: Om du byter lagringskontonivå från lågfrekvent till frekvent utgår en avgift motsvarande läsningen av alla data i lagringskontot. Dock utgår byter lagringskontonivå från frekvent till lågfrekvent en avgift motsvarande Skrivningen av alla data till den lågfrekventa nivån (endast GPv2-konton).

> [!NOTE]
> Mer information om priser för Blob storage-konton finns i [priser för Azure Storage](https://azure.microsoft.com/pricing/details/storage/) sidan. Mer information om kostnaderna för utgående dataöverföring finns på sidan [Prisinformation om Dataöverföringar](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

**Bör jag använda Blob Storage- eller GPv2-konton om jag vill lagra data i olika nivåer?**

Vi rekommenderar att du använder GPv2 i stället för Blob Storage-konton om du vill använda lagringsnivåer. GPv2 har stöd för samma funktioner som Blob Storage-konton, och mycket mer. Kostnaden för Blob Storage- och GPv2-konton är i stort sett densamma, men vissa nya funktioner och rabatter kommer bara att vara tillgängliga för GPv2-konton. GPv1-konton stöder inte lagringsnivåer.

Prisstrukturen för GPv1- och GPv2-konton skiljer sig åt, så kunderna bör noggrant utvärdera båda alternativen innan de bestämmer sig för att använda GPv2-konton. Du kan enkelt konvertera ett befintligt Blob Storage eller GPv1-konto till GPv2 via en enklicksprocess. Mer information finns i [kontoöversikten för Azure Storage](../common/storage-account-overview.md).

**Kan jag lagra objekt på alla tre (frekvent, lågfrekvent och Arkiv) lagringsnivåerna i samma konto?**

Ja. Attributet **Åtkomstnivå** på kontonivå används som standardlagringsnivå för alla objekt i kontot som inte har en egen angiven nivå. Med blobnivåindelningen kan du ange åtkomstnivå på objektnivå oavsett vilken åtkomstnivå som angetts för kontot. Blobar från alla tre lagringsnivåer (frekvent, lågfrekvent eller Arkiv) kan finnas i samma konto.

**Kan jag ändra standardlagringsnivå för mitt Blob- eller GPv2-lagringskonto?**

Ja, du kan ändra standardlagringsnivå med attributet **Åtkomstnivå** för lagringskontot. Ändringar av lagringsnivån gäller för alla objekt som lagras i kontot som inte har en uttryckligen inställd nivå. Lagringsnivå från frekvent till lågfrekvent tillkommer kostnader för skrivåtgärder (per 10 000) för alla blobbar utan en angiven lagringsnivå i GPv2-konton och ändrar från lågfrekvent till frekvent tillkommer läsåtgärder (per 10 000) och datahämtning (per GB) avgifter för alla blobbar i Blob storage och GPv2-konton.

**Kan jag ange Mina standardåtkomstnivån till arkivet?**

Nej. Endast frekvent och lågfrekvent lagringsnivå kan anges som standardnivå för kontot. Arkivlagringsnivån kan endast anges på objektnivå.

**Där kan regioner är lagringsnivåerna frekvent, lågfrekvent lagring och Arkivlagring tillgängliga?**

Lagringsnivåerna frekvent och lågfrekvent tillsammans med blob-nivå är tillgängliga i alla regioner. Arkivlagring är inledningsvis endast tillgängligt i vissa regioner. En fullständig lista finns under [Tillgängliga Azure-produkter efter region](https://azure.microsoft.com/regions/services/).

**Sig blobarna på lågfrekvent lagringsnivå annorlunda än de i frekvent lagringsnivå?**

Blobbar på frekvent lagringsnivå har samma svarstid som blobbar i GPv1, GPv2 och Blob storage-konton. Blobbar på lågfrekvent lagringsnivå har liknande svarstid (i millisekunder) som blobbar i GPv1, GPv2 och Blob storage-konton. Blobbar på arkivlagringsnivå har flera timmars svarstid i GPv1, GPv2 och Blob storage-konton.

Blobbar på lågfrekvent lagringsnivå har en något lägre tillgänglighetsnivå (enligt SLA) än blobbar som lagras på frekvent lagringsnivå. Mer information finns i [SLA för Storage](https://azure.microsoft.com/support/legal/sla/storage/v1_2/).

**Är åtgärder mellan nivåerna frekvent, lågfrekvent lagring och Arkivlagring samma?**

Ja. Alla åtgärder mellan frekvent och lågfrekvent är 100% konsekventa. Alla giltiga arkivåtgärderna, inklusive ta bort, är lista, hämta blobegenskaper/metadata och Ställ in blobbnivå 100% konsekventa med frekvent och lågfrekvent. En blob kan inte läsas eller ändras i arkivnivån.

**När du återställer en blob från arkivlagring till frekvent eller lågfrekvent lagring, hur vet jag när processen är klar?**

Under återställning kan du använda åtgärden Hämta blobegenskaper och kontrollera attributet **Arkivstatus** för att se när nivåändringen har genomförts. Status är ”rehydrate-pending-to-hot” eller ”rehydrate-pending-to-cool” beroende på målnivån. Statusegenskapen Arkiv tas bort när åtgärden har slutförts, och **åtkomstnivå** blob-egenskapen återspeglar den nya frekvent eller lågfrekvent nivå.  

**När jag har angett nivå för en blob: hur lång tid tar det innan jag börjar debiteras för den nya nivån?**

Varje blob faktureras alltid enligt den nivå som anges av blobens **åtkomstnivå** egenskapen. När du ställer in en ny nivå för en blob i **åtkomstnivå** egenskapen omedelbart visar den nya nivån för alla typer av ändringar. Extrahera en blob från arkivlagring till en frekvent eller lågfrekvent nivå kan dock ta flera timmar. I det här fallet debiteras du arkivnivån tills processen är klar, då den **åtkomstnivå** egenskapen visar den nya nivån. Du debiteras då för den bloben som frekvent eller lågfrekvent hastighet.

**Hur vet jag om jag påförs en avgift för tidig borttagning när du tar bort eller flyttar en blob från lågfrekvent lagring eller arkivlagring?**

En blob tas bort eller flyttas från lågfrekvent lagring (endast GPv2-konton) eller arkivnivån 30 dagar och 180 dagar, påförs en proportionellt beräknad avgift för tidig borttagning. Du kan fastställa hur länge en blob har lagrats på nivån för lågfrekvent nivå eller arkivnivå genom att markera den **ändringstid för nivån** blobegenskapen som innehåller en tidsstämpel för den senaste ändringen. Mer information finns i [lågfrekvent lagring och tidig borttagning i arkivet](#cool-and-archive-early-deletion).

**Vilka Azure-verktyg och SDK: er stöder blobnivåindelning och arkivlagring?**

Azure-portalen, PowerShell och CLI verktyg och klientbibliotek för .NET, Java, Python och Node.js alla stöd för blobnivåindelning och arkivlagring.  

**Hur mycket data kan jag lagra nivåerna frekvent, lågfrekvent lagring och Arkivlagring?**

Datalagringsbegränsningar och andra begränsningar anges på kontonivå, inte på lagringsnivå. Därför kan du välja att använda hela ditt lagringsutrymme i en nivå eller i alla tre nivåer. Mer information finns i [skalbarhet för lagring av Azure- och prestandamål](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="next-steps"></a>Nästa steg

### <a name="evaluate-hot-cool-and-archive-in-gpv2-blob-storage-accounts"></a>Utvärdera frekvent, lågfrekvent lagring och Arkivlagring i gpv2-/ Blob storage-konton

[Kontrollera tillgängligheten för frekvent, lågfrekvent lagring och Arkivlagring efter region](https://azure.microsoft.com/regions/#services)

[Hantera livscykeln för Azure Blob-lagring](storage-lifecycle-management-concepts.md)

[Utvärdera användningen av dina aktuella lagringskonton genom att aktivera mätvärden i Azure Storage.](../common/storage-enable-and-view-metrics.md)

[Kontrollera frekvent, lågfrekvent lagring och Arkivlagring priser i Blob storage- och GPv2-konton efter region](https://azure.microsoft.com/pricing/details/storage/)

[Se priser för dataöverföring](https://azure.microsoft.com/pricing/details/data-transfers/)
