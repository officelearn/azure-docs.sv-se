---
title: Frekvent, lågfrekvent och arkivnivå åtkomst för BLOB - Azure Storage
description: Frekvent, lågfrekvent och arkivnivå åtkomst för Azure storage-konton.
services: storage
author: mhopkins-msft
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2019
ms.author: mhopkins
ms.subservice: blobs
ms.openlocfilehash: 78158f49748a8e9e08f3695860d95a2a6eff9fd4
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2019
ms.locfileid: "58402447"
---
# <a name="azure-blob-storage-hot-cool-and-archive-access-tiers"></a>Azure Blob storage: frekvent, lågfrekvent och arkivnivå åtkomst

Azure storage erbjuder olika åtkomstnivåer som du kan lagra data i blob-objekt på det mest kostnadseffektiva sättet. Tillgängliga åtkomstnivåer är:

- **Frekvent** - optimerad för att lagra data som används ofta.
- **Lågfrekvent** - optimerad för att lagra data som används sällan och som lagras i minst 30 dagar.
- **Arkivera** - optimerad för att lagra data som används sällan och som lagras i minst 180 dagar med flexibla svarstidskrav (i storleksordningen timmar).

Följande gäller för olika åtkomstnivåer:

- Arkiv som åtkomstnivå finns bara på blobnivån och inte på lagringskontonivån.
- Data i den lågfrekventa lagringsnivån klarar lite lägre tillgänglighet, men kräver fortfarande hög hållbarhet och liknande time-åtkomst och dataflödesegenskaper som data frekvent åtkomstnivå. För lågfrekventa data, en lite lägre tillgänglighet servicenivåavtal (SLA) och högre är åtkomstkostnader jämfört med frekventa data godtagbara med tanke på de lägre lagringskostnaderna.
- Arkivlagring är i frånkopplat tillstånd och erbjuder de lägsta lagringskostnaderna men även de högsta åtkomstkostnaderna.
- Endast de frekvent och lågfrekvent lagringsnivå kan anges på kontonivå.
- Frekvent, lågfrekvent åtkomst eller arkivering kan ställas in på objektnivå.

Data som lagras i molnet växer i exponentiell takt. Om du vill hålla kontroll på och optimera kostnaderna för dina växande lagringsbehov är det en bra idé att ordna data baserat på attribut som åtkomstfrekvens och planerad kvarhållningsperiod. Data som lagras i molnet kan vara olika beroende på hur den har genereras, bearbetas och används under livslängden. Vissa data används aktivt och ändras under livslängden. Vissa data används ofta i början av livslängden och sedan minskar användning drastiskt när dessa data blir äldre. Vissa data förblir inaktiva i molnet och är mycket sällan kanske aldrig, när den är lagrad.

Varje scenario dra nytta av en annan åtkomstnivå som är optimerad för motsvarande åtkomstmönster. Med i frekvent, lågfrekvent åtkomst eller arkivering åtkomst, Azure Blob storage-adresser behovet differentierad åtkomstnivåerna med olika prissättningsmodeller.

## <a name="storage-accounts-that-support-tiering"></a>Lagringskonton med stöd för flera lagringsnivåer

Du kan bara dina data frekvent, lågfrekvent lagring eller arkivlagring i Blob storage och generell användning v2 (GPv2)-konton. GPv1-konton (General Purpose v1) har inte stöd för flera lagringsnivåer. Du kan dock enkelt konvertera befintliga GPv1- eller Blob storage-konton till GPv2-konton via en process med ett klick i Azure-portalen. GPv2 innehåller en ny prisstruktur för blobbar, filer och köer och åtkomst till en mängd andra nya lagringsfunktioner. Framöver kommer erbjuds vissa nya funktioner och rabatter endast i GPv2-konton. Därför bör du överväga att använda GPv2-konton, men endast använda dem när du har granskat priserna för alla tjänster. Vissa arbetsbelastningar kan bli dyrare med GPv2 än GPv1. Mer information finns i [kontoöversikten för Azure Storage](../common/storage-account-overview.md).

BLOB storage- och GPv2-konton visas den **åtkomstnivå** attributet på kontonivå. Det här attributet kan du ange standardnivån som frekvent eller lågfrekvent för alla blobar i lagringskontot som inte har en explicit nivå angiven på objektnivå. För objekt där nivån har ställts in på objektnivå används inte nivåinställningen för kontot. Arkivnivån kan tillämpas endast på objektnivå. Du kan växla mellan de olika nivåerna när som helst.

## <a name="premium-performance-block-blob-storage"></a>Premium-prestanda block blob-lagring

Premium-prestanda block blob-lagring gör att data som används ofta är tillgängliga via maskinvara med höga prestanda. Data i den här prestandanivån lagras på SSD (solid-state drive), som är optimerade för låg och konsekvent svarstid. SSD ger högre transaktionella pris och genomflöde jämfört med traditionella hårddiskar.

Premium-prestanda block blob-lagring är perfekt för arbetsbelastningar som kräver snabb och konsekvent svarstider. Det är bäst för arbetsbelastningar som utför många små transaktioner, till exempel samla in telemetridata, meddelanden och transformering av data. Data som innebär att slutanvändare som interaktiva videoredigering, statiskt webbinnehåll och onlinetransaktioner är också bra kandidater.

Premium-prestanda block blob-lagring är tillgänglig endast via lagringskontotypen för block blob och stöder för närvarande inte lagringsnivåer för frekvent, lågfrekvent eller arkivnivå åtkomst.

## <a name="hot-access-tier"></a>Frekvent åtkomstnivå

Frekvent åtkomstnivå har högre kostnader för lagring än lågfrekvent lagring och arkivlagring nivåer, men de lägsta åtkomstkostnaderna. Exempelscenarier för frekvent åtkomstnivå är:

- Data som används aktivt eller förväntas användas (läsa och skriva till) ofta.
- Data som mellanlagras för bearbetning och eventuell migrering till lågfrekvent åtkomstnivå.

## <a name="cool-access-tier"></a>Lågfrekvent åtkomstnivå

Lågfrekvent åtkomstnivå har lägre kostnader för lagring och högre åtkomstkostnader jämfört med frekvent lagring. Den här nivån är avsedd för data som är kvar på den lågfrekventa nivån i minst 30 dagar. Exempelscenarier för lågfrekvent åtkomstnivå är:

- Datauppsättningar för kortsiktig säkerhetskopiering och haveriberedskap.
- Äldre medieinnehåll som inte visas så ofta längre, men som förväntas vara tillgängligt direkt vid behov.
- Stora datamängder som behöver en kostnadseffektiv lagring under tiden mer data samlas in för framtida bearbetning. (*Till exempel* långsiktig lagring av vetenskapliga data eller telemetridata (rådata) från en tillverkningsanläggning.)

## <a name="archive-access-tier"></a>Arkivåtkomstnivå

Arkiv som åtkomstnivå har lägst lagringskostnad och högre kostnader för datahämtning jämfört med frekventa och lågfrekventa nivåer. Den här nivån är avsedd för data som kan tolerera flera timmars fördröjning vid hämtning och som finns kvar på arkivnivån i minst 180 dagar.

När en blob finns i arkivlagring, blob-data är offline och inte kan läsas, kopieras, skrivas över eller ändrade. Du kan inte ta ögonblicksbilder av en blob i arkivlagring. Dock fortfarande blob-metadata är online och tillgänglig, där du kan visa blob och dess egenskaper. För blobbar i arkivet är de enda giltiga åtgärderna GetBlobProperties, GetBlobMetadata, ListBlobs, SetBlobTier och DeleteBlob.

Exempelscenarier för Arkiv som åtkomstnivå är:

- Långsiktig säkerhetskopiering, sekundär säkerhetskopiering och datauppsättningar för arkivering
- Ursprungliga rådata som måste bevaras, även efter att de har bearbetats till ett slutligt användbart format. (*Till exempel* mediefiler i RAW-format som har omkodats till andra format.)
- Efterlevnads- och arkiveringsdata som behöver lagras under en längre tid och som nästan aldrig används. (*Till exempel*, säkerhetskameror, gamla röntgenbilder/Magnetröntgenbilder för vårdorganisationer, ljudinspelningar och transkript av kundsamtal för ekonomiska tjänster)

### <a name="blob-rehydration"></a>Återuppväckning av blob

Om du vill läsa data i arkivlagring måste du först ändra nivå för din blob till frekvent eller lågfrekvent. Processen kallas återuppväckning och kan ta upp till 15 timmar. Stora rekommenderas för optimala prestanda. Återställning av flera små blobbar samtidigt kan ta ännu längre tid.

Under återuppväckning kan du kontrollera blobegenskapen **Arkivstatus** för att se om nivån har ändrats. Status är ”rehydrate-pending-to-hot” eller ”rehydrate-pending-to-cool” beroende på målnivån. När åtgärden har slutförts tas blobegenskapen Arkivstatus bort och blobegenskapen **Åtkomstnivå** anger den nya nivån: frekvent eller lågfrekvent.  

## <a name="blob-level-tiering"></a>Blobnivåindelning

Med blobnivåindelning kan du ändra nivå för dina data på objektnivå med hjälp av en enda åtgärd som kallas [Ange blobnivå](/rest/api/storageservices/set-blob-tier). Du kan enkelt ändra åtkomstnivå för en blob mellan frekvent, lågfrekvent eller arkivnivå när användningsmönster ändras, utan att behöva flytta data mellan konton. Alla nivåändringar sker omedelbart. Men kan en blob återuppväcks från arkivet ta flera timmar.

Tiden för den senaste ändringen på blobnivån är tillgänglig via blobegenskapen **Ändringstid för åtkomstnivå**. Om en blob finns i arkivnivån kan kan inte den skrivas över, så att ladda upp samma blob inte tillåts i det här scenariot. Du kan skriva över en blob i en frekvent eller lågfrekvent nivå, som ärver fall den nya bloben nivån för bloben som skrevs över.

Blobar i alla tre åtkomstnivåerna kan existera tillsammans i samma konto. En blob som inte har en uttryckligen tilldelad nivå härleder nivån från kontots åtkomstnivåinställning. Om åtkomstnivån härleds från kontot, visas den **åtkomstnivå härledd** blob-egenskapen angetts till ”true”, och blobegenskapen **åtkomstnivå** matchar kontonivån. I Azure-portalen åtkomstnivån härleds egenskapen visas med blobåtkomstnivån (till exempel **frekvent (härledd)** eller **lågfrekvent (härledd)**).

> [!NOTE]
> Arkivlagring och blobnivåindelning stöder endast blockblobar. Du kan även ändra nivå för en blockblob som har ögonblicksbilder.

> [!NOTE]
> Data som lagras i ett block blob storage-konto kan inte för närvarande nivåer för frekvent, lågfrekvent eller Arkiv med [ange Blobnivå](/rest/api/storageservices/set-blob-tier) eller med hjälp av Livscykelhantering för Azure Blob Storage.
> För att flytta data, måste du synkront kopiera blobar från block blob storage-konto till frekvent åtkomstnivå i ett annat konto med den [placera Block från URL: en API](/rest/api/storageservices/put-block-from-url) eller en version av AzCopy som har stöd för detta API.
> Den *placera Block från URL: en* API synkront kopierar data på servern, vilket innebär att anropet har slutförts bara en gång alla data flyttas från den ursprungliga serverplatsen till målplatsen.

### <a name="blob-lifecycle-management"></a>Livscykelhantering för BLOB

Livscykelhantering för Blob Storage (förhandsversion) erbjuder en omfattande, regel-baserad princip som du kan använda för att överföra data till bästa åtkomstnivå och för att ta bort data i slutet av livscykeln. Se [hantering av Azure Blob storage-livscykeln](storage-lifecycle-management-concepts.md) vill veta mer.  

### <a name="blob-level-tiering-billing"></a>Fakturering för blobnivåindelning

När en blob flyttas till en mer lågfrekvent nivå (frekvent -> lågfrekvent, frekvent -> Arkiv eller lågfrekvent -> Arkiv) faktureras åtgärden som en skrivning till målnivån där Skrivåtgärden (per 10 000) och kostnaderna för dataskrivning (per GB) för målnivån tillämpas.

När en blob flyttas till en varmare nivå (Arkiv -> lågfrekvent, Arkiv -> frekvent eller lågfrekvent -> frekvent) faktureras åtgärden som en läsning från källnivån där Läsåtgärden (per 10 000) och kostnaderna för datahämtning (per GB) för källnivån tillämpas. I följande tabell sammanfattas hur nivåändringar faktureras.

| | **Skriva avgifter (åtgärden + åtkomst)** | **Läs avgifter (åtgärden + åtkomst)**
| ---- | ----- | ----- |
| **SetBlobTier Direction** | frekvent -> lågfrekvent, frekvent -> Arkiv, cool (lågfrekvent) -> Arkiv | Arkiv -> lågfrekvent, Arkiv -> frekvent, lågfrekvent -> frekvent

Om du ändrar kontonivån från frekvent till lågfrekvent debiteras du för skrivåtgärder (per 10 000) för blobbar som saknar angiven nivå, men endast i GPv2-konton. Det finns ingen kostnad för den här ändringen i Blob storage-konton. Du debiteras för både läsåtgärder (per 10 000) och datahämtning (per GB) om du växlar ditt Blob Storage- eller GPv2-konto från lågfrekvent till frekvent. Kostnader för tidig borttagning av blobbar från lågfrekvent lagring eller arkivlagring kan också tillkomma.

### <a name="cool-and-archive-early-deletion"></a>Tidig borttagning i lågfrekvent lagring och i arkivlagring

Utöver kostnaderna per GB och per månad kan kostnader tillkomma för tidig borttagning. För blobbar som flyttas till lågfrekvent lagring (gäller endast GPv2-konton) är den minsta lagringstiden 30 dagar, och för blobbar som flyttas till arkivlagring är den minsta lagringstiden 180 dagar. Den här kostnaden beräknas proportionellt. Exempel: Om en blob flyttas till arkivlagring och sedan tas bort eller flyttas till frekvent nivå efter 45 dagar, debiteras du en avgift för tidig borttagning motsvarande 135 (180 minus 45) dagars arkivlagring av blobben.

Du kan beräkna tidig borttagning med hjälp av blob-egenskapen **Skapandetid**, om det har ingen åtkomst nivåändringar. Annars kan du använda när åtkomstnivån senast ändrades till lågfrekvent nivå eller arkivnivå genom att visa blob-egenskapen: **åtkomst-nivå--tid för**. Mer information om blobegenskaper finns i [hämta Blobegenskaper](https://docs.microsoft.com/rest/api/storageservices/get-blob-properties).

## <a name="comparing-block-blob-storage-options"></a>Jämför alternativ för block blob storage

Följande tabell innehåller en jämförelse av premium-prestanda block blob-lagring och frekvent, lågfrekvent och Arkiv åtkomstnivåerna.

|                                           | **Premium-prestanda** | **Frekvent nivå** | **Den lågfrekventa nivån** | **Arkivnivån**
| ----------------------------------------- | ---------------- | ------------ | ----- | ----- |
| **Tillgänglighet**                          | 99,9 %            | 99,9 %        | 99 % | Saknas |
| **Tillgänglighet** <br> **(RA-GRS-läsningar)**  | Gäller inte              | 99,99 %       | 99,9 % | Saknas |
| **Avgifter för användning**                         | Högre kostnader för lagring, lägre åtkomst och transaktionskostnaden | Högre kostnader för lagring, lägre transaktionskostnader för åtkomst, och | Lägre kostnader för lagring och högre transaktionskostnader | Lägst kostnader för lagring, högsta åtkomst och transaktionskostnader |
| **Minsta objektstorlek**                   | Saknas | Saknas | Saknas | Saknas |
| **Minsta lagringstid**              | Saknas | Gäller inte | 30 dagar (endast GPv2) | 180 dagar
| **Svarstid** <br> **(Tid till första byte)** | Ensiffriga millisekunder | millisekunder | millisekunder | < 15 timmar

> [!NOTE]
> Skalbarhets- och prestandamål finns [Azure Storage skalbarhets- och prestandamål för storage-konton](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

## <a name="quickstart-scenarios"></a>Snabbstartsscenarier

I det här avsnittet visas följande scenarier på Azure Portal:

- Ändra standardåtkomstnivån för ett GPv2- eller Blob Storage-konto.
- Ändra åtkomstnivån för en blob i ett GPv2- eller Blob Storage-konto.

### <a name="change-the-default-account-access-tier-of-a-gpv2-or-blob-storage-account"></a>Ändra standardåtkomstnivå för ett GPv2- eller Blob Storage-konto

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Gå till ditt lagringskonto genom att välja Alla resurser och välj sedan ditt lagringskonto.

1. Klicka på **Konfiguration** på bladet Inställningar för att visa och/eller ändra kontokonfigurationen.

1. Välj rätt åtkomstnivå för dina behov: Ange den **åtkomstnivå** antingen **lågfrekvent** eller **frekvent**.

1. Klicka på **Spara** överst på bladet.

### <a name="change-the-tier-of-a-blob-in-a-gpv2-or-blob-storage-account"></a>Ändra nivå för en blob i ett GPv2- eller Blob storage-konto

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Gå till din blob i lagringskontot, välja **alla resurser**, Välj ditt lagringskonto, din behållare och sedan välja din blob.

1. I den **Blobegenskaper** bladet och välja den **åtkomstnivå** listrutan att välja den **frekvent**, **lågfrekvent**, eller **Arkiv**  åtkomstnivå.

1. Klicka på **Spara** överst på bladet.

## <a name="pricing-and-billing"></a>Priser och fakturering

Alla lagringskonton används en prissättningsmodell för blobblagring baserat på vilken nivå av varje blob. Tänk på följande för debitering:

- **Lagringskostnader**: Utöver mängden data som lagras, varierar för att lagra data beroende på åtkomstnivå. Kostnaden per gigabyte minskas när nivån blir mer lågfrekvent.
- **Kostnader för dataåtkomst**: Öka kostnaderna för dataåtkomst när nivån blir mer lågfrekvent. För data i lågfrekvent lagring och arkivlagring åtkomstnivå debiteras du en per-åtkomst gigabyte för läsningar.
- **Transaktionskostnader**: Det finns en kostnad för alla nivåer per transaktion som ökar när nivån blir mer lågfrekvent.
- **Dataöverföringskostnader för GEO-replikering**: Den här avgiften gäller endast konton med konfigurerad geo-replikering, inklusive GRS och RA-GRS. Dataöverföring för geo-replikering debiteras per gigabyte.
- **Kostnaderna för utgående dataöverföring**: Utgående dataöverföringar (data som överförs från en Azure-region) debiteras för bandbreddsanvändning regelbundet per gigabyte, konsekvent med allmänna lagringskonton.
- **Ändring av åtkomstnivå**: Ändring av åtkomstnivå konto från lågfrekvent till frekvent utgår en avgift motsvarande läsningen av alla data i lagringskontot. Men utgår ändrar åtkomstnivå kontot från frekvent till lågfrekvent en avgift motsvarande Skrivningen av alla data till den lågfrekventa nivån (endast GPv2-konton).

> [!NOTE]
> Mer information om priser för Blob storage-konton finns i [priser för Azure Storage](https://azure.microsoft.com/pricing/details/storage/) sidan. Mer information om kostnaderna för utgående dataöverföring finns på sidan [Prisinformation om Dataöverföringar](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

**Bör jag använda Blob Storage- eller GPv2-konton om jag vill lagra data i olika nivåer?**

Vi rekommenderar att du använder GPv2 i stället för Blob Storage-konton om du vill använda lagringsnivåer. GPv2 har stöd för samma funktioner som Blob Storage-konton, och mycket mer. Kostnaden för Blob Storage- och GPv2-konton är i stort sett densamma, men vissa nya funktioner och rabatter kommer bara att vara tillgängliga för GPv2-konton. GPv1-konton stöder inte lagringsnivåer.

Prisstrukturen för GPv1- och GPv2-konton skiljer sig åt, så kunderna bör noggrant utvärdera båda alternativen innan de bestämmer sig för att använda GPv2-konton. Du kan enkelt konvertera ett befintligt Blob Storage eller GPv1-konto till GPv2 via en enklicksprocess. Mer information finns i [kontoöversikten för Azure Storage](../common/storage-account-overview.md).

**Kan jag lagra objekt i alla tre (frekvent, lågfrekvent och Arkiv) åtkomstnivåerna i samma konto?**

Ja. Attributet **Åtkomstnivå** på kontonivå används som standardlagringsnivå för alla objekt i kontot som inte har en egen angiven nivå. Med blobnivåindelningen kan du ange åtkomstnivå på objektnivå oavsett vilken åtkomstnivå som angetts för kontot. Blobar i någon av tre åtkomstnivåer (frekvent, lågfrekvent eller Arkiv) kan finnas i samma konto.

**Kan jag ändra standardnivå för mitt Blob- eller GPv2-lagringskonto?**

Ja, du kan ändra åtkomstnivå standard genom att ange den **åtkomstnivå** attribut för lagringskontot. Ändring av åtkomstnivå gäller för alla objekt som lagras i kontot och som inte har en uttryckligen inställd nivå. Ändrar åtkomstnivå från frekvent till lågfrekvent tillkommer kostnader för skrivåtgärder (per 10 000) för alla blobbar utan en angiven lagringsnivå i GPv2-konton och ändrar från lågfrekvent till frekvent tillkommer läsåtgärder (per 10 000) och datahämtning (per GB) avgifter för alla blobbar i Blob storage och GPv2-konton.

**Kan jag ange arkivlagring som standardnivå för mitt konto?**

Nej. Frekvent och lågfrekvent lagringsnivå kan bara anges som standardnivå för kontot. Arkivlagringsnivån kan endast anges på objektnivå.

**Regioner som är frekvent, lågfrekvent och arkivlagring åtkomstnivåerna som är tillgängliga i?**

Frekvent och lågfrekvent åtkomstnivå tillsammans med blob-nivå är tillgängliga i alla regioner. Arkivlagring är inledningsvis endast tillgängligt i vissa regioner. En fullständig lista finns under [Tillgängliga Azure-produkter efter region](https://azure.microsoft.com/regions/services/).

**Sig blobarna på lågfrekvent åtkomstnivå annorlunda än de i frekvent åtkomstnivå?**

Blobbar på frekvent åtkomstnivå har samma svarstid som blobbar i GPv1, GPv2 och Blob storage-konton. Blobar i lågfrekvent åtkomstnivå har liknande svarstid (i millisekunder) som blobbar i GPv1, GPv2 och Blob storage-konton. Blobbar på Arkiv som åtkomstnivå har flera timmars svarstid i GPv1, GPv2 och Blob storage-konton.

Blobbar på lågfrekvent åtkomstnivå har en något lägre tillgänglighetsnivå (enligt SLA) än blobbar som lagras i frekvent åtkomstnivå. Mer information finns i [SLA för Storage](https://azure.microsoft.com/support/legal/sla/storage/v1_2/).

**Kan samma åtgärder användas för lagringsnivåerna frekvent, lågfrekvent och arkivlagring?**

Ja. På lagringsnivåerna frekvent och lågfrekvent fungerar alla åtgärder på samma sätt. På arkivnivå är vissa åtgärder giltiga (som ta bort, lista, hämta blobegenskaper/metadata och ange blobåtkomstnivå), och dessa fungerar på samma sätt som på frekvent och lågfrekvent nivå. En blob kan inte läsas eller ändras om lagringsnivån är arkivlagring.

**När jag återställer en blob från arkivlagring till frekvent eller lågfrekvent lagring, hur vet jag när processen är klar?**

Under återställning kan du använda åtgärden Hämta blobegenskaper och kontrollera attributet **Arkivstatus** för att se när nivåändringen har genomförts. Status är ”rehydrate-pending-to-hot” eller ”rehydrate-pending-to-cool” beroende på målnivån. När åtgärden har slutförts tas blobegenskapen Arkivstatus bort och blobegenskapen **Åtkomstnivå** anger den nya nivån: frekvent eller lågfrekvent.  

**När jag har angett nivå för en blob: hur lång tid tar det innan jag börjar debiteras för den nya nivån?**

Varje blob faktureras alltid enligt den nivå som anges av blobens **åtkomstnivå** egenskapen. När du ställer in en ny nivå för en blob i **åtkomstnivå** egenskapen omedelbart visar den nya nivån för alla typer av ändringar. Men att återställa en blob från arkivnivån till en frekvent eller lågfrekvent nivå kan ta flera timmar. I det här fallet debiteras du arkivnivån tills processen är klar, då den **åtkomstnivå** egenskapen visar den nya nivån. Du debiteras då för den bloben till frekvent eller lågfrekvent betalning.

**Hur kan jag se om jag påförs en avgift för tidig borttagning när jag tar bort eller flyttar en blob från lågfrekvent lagring eller arkivlagring?**

Om en blob tas bort eller flyttas från lågfrekvent lagring (endast GPv2-konton) inom 30 dagar, eller från arkivlagring inom 180 dagar, påförs en proportionellt beräknad avgift för tidig borttagning. Du kan fastställa hur länge en blob har lagrats på nivån för lågfrekvent nivå eller arkivnivå genom att markera den **ändringstid för nivån** blobegenskapen som innehåller en tidsstämpel för den senaste ändringen. Mer information finns i [tidig borttagning i lågfrekvent lagring och arkivlagring](#cool-and-archive-early-deletion).

**Vilka Azure-verktyg och SDK:er stöder blobnivåindelning och arkivlagring?**

Azure Portal, PowerShell, CLI-verktyg och klientbiblioteken för .NET, Java, Python och Node.js har stöd för blobnivåindelning och arkivlagring.  

**Hur mycket data kan jag lagra på lagringsnivåerna frekvent, lågfrekvent och arkiv?**

Datalagringsbegränsningar och andra begränsningar anges på kontonivå, inte på åtkomstnivå. Därför kan du välja att använda hela ditt lagringsutrymme i en nivå eller i alla tre nivåer. Mer information finns i [skalbarhets- och prestandamål i Azure Storage](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="next-steps"></a>Nästa steg

### <a name="evaluate-hot-cool-and-archive-in-gpv2-blob-storage-accounts"></a>Utvärdera frekvent/lågfrekvent lagring och arkivlagring i GPv2-/Blob Storage-konton

[Kontrollera tillgängligheten för frekvent/lågfrekvent lagring och arkivlagring efter region](https://azure.microsoft.com/regions/#services)

[Hantera livscykeln för Azure Blob-lagring](storage-lifecycle-management-concepts.md)

[Utvärdera användningen av dina aktuella lagringskonton genom att aktivera mätvärden i Azure Storage.](../common/storage-enable-and-view-metrics.md)

[Kontrollera priser för frekvent/lågfrekvent lagring och arkivlagring i Blob Storage-/GPv2-konton efter region](https://azure.microsoft.com/pricing/details/storage/)

[Se priser för dataöverföring](https://azure.microsoft.com/pricing/details/data-transfers/)