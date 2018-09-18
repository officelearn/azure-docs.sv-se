---
title: Frekvent, lågfrekvent och arkivlagring i Azure för blobbar | Microsoft Docs
description: Frekvent och lågfrekvent lagring samt arkivlagring för Azure Storage-konton.
services: storage
author: kuhussai
ms.service: storage
ms.topic: article
ms.date: 09/11/2018
ms.author: kuhussai
ms.component: blobs
ms.openlocfilehash: 66c47a97eee6759eb963db43d5c573fb6612bde6
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2018
ms.locfileid: "45735926"
---
# <a name="azure-blob-storage-hot-cool-and-archive-storage-tiers"></a>Azure Blob Storage: nivåer för frekvent lagring, lågfrekvent lagring och arkivlagring

## <a name="overview"></a>Översikt

Azure Storage erbjuder tre lagringsnivåer för lagring av Blob-objekt så att du kan lagra data så kostnadseffektivt som möjligt, beroende på din användning. Azures **frekventa lagringsnivå** är optimerad för att lagra data som används ofta. Azures **lågfrekventa lagringsnivå** är optimerad för att lagra data som inte används ofta och som lagras i minst 30 dagar. **Arkivlagringsnivån** i Azure är optimerad för att lagra data som används sällan och som lagras i minst 180 dagar med flexibla svarstidskrav (i storleksordningen timmar). Arkivlagringsnivån är endast tillgänglig på blobnivån och inte på lagringskontonivån. Data i den lågfrekventa lagringsnivån klarar lite lägre tillgänglighet, men kräver fortfarande hög hållbarhet och liknande åtkomsttid och dataflödesegenskaper som data i frekvent lagringsnivå. För data på den lågfrekventa lagringsnivån är serviceavtal med lägre tillgänglighet och högre åtkomstkostnader jämfört med frekventa data godtagbara med tanke på de lägre lagringskostnaderna. Arkivlagring är i frånkopplat tillstånd och erbjuder de lägsta lagringskostnaderna men även de högsta åtkomstkostnaderna. Endast frekventa och lågfrekventa lagringsnivåer (ej arkiv) kan anges på kontonivå. Alla tre nivåer kan ställas in på objektnivå.

Idag växer mängden data som lagras i molnet i exponentiell takt. Om du vill hålla kontroll på och optimera kostnaderna för dina växande lagringsbehov är det en bra idé att ordna data baserat på attribut som åtkomstfrekvens och planerad kvarhållningsperiod. Data som lagras i molnet kan vara olika beroende på hur de genereras, bearbetas och används under livslängden. Vissa data används aktivt och ändras under livslängden. Vissa data används ofta i början av livslängden och sedan minskar användning drastiskt när dessa data blir äldre. Vissa data förblir inaktiva i molnet och används sällan, eller kanske aldrig, när de har lagrats.

För varje scenario finns en lagringsnivå som är optimerad för motsvarande åtkomstmönster. Med nivåerna för frekvent åtkomst, lågfrekvent åtkomst samt arkivlagringsnivån uppfyller Azure Blob Storage behovet av olika lagringsnivåer med olika prissättningsmodeller.

## <a name="storage-accounts-that-support-tiering"></a>Lagringskonton med stöd för flera lagringsnivåer

För användning av olika lagringsnivåer (frekvent, lågfrekvent eller arkivlagring) krävs ett Blob Storage eller GPv2-konto (General Purpose v2). GPv1-konton (General Purpose v1) har inte stöd för flera lagringsnivåer. Kunder kan dock enkelt konvertera sina befintliga GPv1- eller Blob Storage-konton till GPv2-konton via en enklicksprocess i Azure Portal. GPv2 har en ny prisstruktur för blobbar, filer, och köer, och ger också åtkomst till en mängd andra nya lagringsfunktioner. I framtiden kommer vissa nya funktioner och rabatter dessutom endast att erbjudas för GPv2-konton. Kunderna bör därför överväga att använda GPv2-konton, men först efter att ha granskat priserna för alla tjänster, eftersom vissa arbetsbelastningar kan bli dyrare med GPv2 än GPv1. Mer information finns i [översikt över Azure storage-konton](../common/storage-account-overview.md).

På Blob Storage- och GPv2-konton visas attributet för **åtkomstnivå** på kontonivå, vilket innebär att du kan ange standardlagringsnivån som frekvent eller lågfrekvent för alla blobar i lagringskontot där nivån inte har angetts på objektnivå. För objekt där nivån har ställts in på objektnivå används inte nivåinställningen för kontot. Nivån arkivlagring kan endast anges på objektnivå. Du kan växla mellan dessa lagringsnivåer när som helst.

## <a name="hot-access-tier"></a>Frekvent åtkomstnivå

Frekvent lagring har högre kostnader för lagring än lågfrekvent lagring och arkivlagring, men de lägsta åtkomstkostnaderna. Exempelscenarier för frekvent lagringsnivå:

* Data används aktivt eller förväntas användas ofta (både för att läsa och skriva).
* Data som mellanlagras för bearbetning och eventuell migrering till lågfrekvent åtkomstnivå.

## <a name="cool-access-tier"></a>Lågfrekvent åtkomstnivå

Lågfrekvent lagring har lägre kostnader för lagring och högre åtkomstkostnader jämfört med frekvent lagring. Den här nivån är avsedd för data som är kvar på den lågfrekventa nivån i minst 30 dagar. Exempelscenarier för lågfrekvent lagringsnivå:

* Datauppsättningar för kortsiktig säkerhetskopiering och haveriberedskap.
* Äldre medieinnehåll som inte visas så ofta längre, men som förväntas vara tillgängligt direkt vid behov.
* Stora datamängder som behöver en kostnadseffektiv lagring under tiden mer data samlas in för framtida bearbetning. (*Till exempel* långsiktig lagring av vetenskapliga data eller telemetridata (rådata) från en tillverkningsanläggning.)

## <a name="archive-access-tier"></a>Arkivåtkomstnivå

Arkivlagring har lägst lagringskostnad och högre kostnader för datahämtning jämfört med frekvent och lågfrekvent lagring. Den här nivån är avsedd för data som kan tolerera flera timmars fördröjning vid hämtning och som finns kvar på arkivnivån i minst 180 dagar.

När en blob finns i arkivlagring är den i offline-tillstånd och kan inte läsas (förutom metadata), kopieras, skrivas över eller ändras. Du kan inte heller ta ögonblicksbilder av en blob i arkivlagring. Du kan emellertid använda befintliga åtgärder för att ta bort, lista, hämta blob-egenskaper/-metadata eller ändra nivå för din blob.

Exempelscenarier för arkivlagringsnivå:

* Långsiktig säkerhetskopiering, sekundär säkerhetskopiering och datauppsättningar för arkivering
* Ursprungliga rådata som måste bevaras, även efter att de har bearbetats till ett slutligt användbart format. (*Till exempel* mediefiler i RAW-format som har omkodats till andra format.)
* Efterlevnads- och arkiveringsdata som behöver lagras under en längre tid och som nästan aldrig används. (*Till exempel* film från säkerhetskameror, gamla röntgenbilder/magnetröntgenbilder för vårdorganisationer, ljudinspelningar och transkript av kundsamtal för ekonomiska tjänster.)

### <a name="blob-rehydration"></a>Återuppväckning av blob
Om du vill läsa data i arkivlagring måste du först ändra nivå för din blob till frekvent eller lågfrekvent. Processen kallas återuppväckning och kan ta upp till 15 timmar. Stora rekommenderas för optimala prestanda. Återställning av flera små blobbar samtidigt kan ta ännu längre tid.

Under återuppväckning kan du kontrollera blobegenskapen **Arkivstatus** för att se om nivån har ändrats. Status är ”rehydrate-pending-to-hot” eller ”rehydrate-pending-to-cool” beroende på målnivån. När åtgärden har slutförts tas blobegenskapen Arkivstatus bort och blobegenskapen **Åtkomstnivå** anger den nya nivån: frekvent eller lågfrekvent.  

## <a name="blob-level-tiering"></a>Blobnivåindelning

Med blobnivåindelning kan du ändra nivå för dina data på objektnivå med hjälp av en enda åtgärd som kallas [Ange blobnivå](/rest/api/storageservices/set-blob-tier). Du kan enkelt ändra åtkomstnivå för en blob mellan frekvent, lågfrekvent eller arkivnivå när användningsmönster ändras, utan att behöva flytta data mellan konton. Alla nivåändringar sker omedelbart. Men kan en blob återuppväcks från arkivet ta flera timmar. 

Tiden för den senaste ändringen på blobnivån är tillgänglig via blobegenskapen **Ändringstid för åtkomstnivå**. Om en blob finns i arkivnivån kan kan inte den skrivas över, så att ladda upp samma blob inte tillåts i det här scenariot. Du kan skriva över en blob i en frekvent eller lågfrekvent nivå, som ärver fall den nya bloben nivån för bloben som skrevs över.

Blobar i alla tre lagringsnivåer kan finnas tillsammans i samma konto. En blob som inte har en uttryckligen tilldelad nivå härleder nivån från kontots åtkomstnivåinställning. Om åtkomstnivån härleds från kontot, visas den **åtkomstnivå härledd** blob-egenskapen angetts till ”true”, och blobegenskapen **åtkomstnivå** matchar kontonivån. Egenskapen som härletts från åtkomstnivån i Azure Portal visas med blobåtkomstnivån (till exempel frekvent (härledd) eller lågfrekvent (härledd)).

> [!NOTE]
> Arkivlagring och blobnivåindelning stöder endast blockblobar. Du kan även ändra nivå för en blockblob som har ögonblicksbilder.

### <a name="blob-lifecycle-management"></a>Livscykelhantering för BLOB
Livscykelhantering för Blob Storage (förhandsversion) erbjuder en omfattande, regel-baserad princip som du kan använda för att överföra data till bästa åtkomstnivå och för att ta bort data i slutet av livscykeln. Se [hantering av Azure Blob storage-livscykeln](https://docs.microsoft.com/en-us/azure/storage/common/storage-lifecycle-managment-concepts) vill veta mer.  

### <a name="blob-level-tiering-billing"></a>Fakturering för blobnivåindelning

När en blob flyttas till en mer lågfrekvent nivå (frekvent -> lågfrekvent, frekvent -> Arkiv eller lågfrekvent -> Arkiv) faktureras åtgärden som en skrivning till målnivån där Skrivåtgärden (per 10 000) och kostnaderna för dataskrivning (per GB) för målnivån tillämpas. Om en blob flyttas till en mer frekvent nivå (arkiv -> lågfrekvent, arkiv -> frekvent eller lågfrekvent -> frekvent) faktureras åtgärden som en läsning från källnivån och avgifter för läsåtgärden (per 10 000) och datahämtning (per GB) för källnivån tillämpas.

Om du ändrar kontonivån från frekvent till lågfrekvent debiteras du för skrivåtgärder (per 10 000) för blobbar som saknar angiven nivå, men endast i GPv2-konton. Det finns ingen kostnad för den här ändringen i Blob storage-konton. Du debiteras för både läsåtgärder (per 10 000) och datahämtning (per GB) om du växlar ditt Blob Storage- eller GPv2-konto från lågfrekvent till frekvent. Kostnader för tidig borttagning av blobbar från lågfrekvent lagring eller arkivlagring kan också tillkomma.

### <a name="cool-and-archive-early-deletion"></a>Tidig borttagning i lågfrekvent lagring och i arkivlagring

Utöver kostnaderna per GB och per månad kan kostnader tillkomma för tidig borttagning. För blobbar som flyttas till lågfrekvent lagring (gäller endast GPv2-konton) är den minsta lagringstiden 30 dagar, och för blobbar som flyttas till arkivlagring är den minsta lagringstiden 180 dagar. Den här kostnaden beräknas proportionellt. Exempel: Om en blob flyttas till arkivlagring och sedan tas bort eller flyttas till frekvent nivå efter 45 dagar, debiteras du en avgift för tidig borttagning motsvarande 135 (180 minus 45) dagars arkivlagring av blobben.

## <a name="comparison-of-the-storage-tiers"></a>Jämförelse av lagringsnivåerna

Följande tabell innehåller en jämförelse av lagringsnivåerna: frekvent, lågfrekvent och arkiv.

| | **Frekvent lagringsnivå** | **Lågfrekvent lagringsnivå** | **Arkivlagringsnivå**
| ---- | ----- | ----- | ----- |
| **Tillgänglighet** | 99,9 % | 99 % | Saknas |
| **Tillgänglighet** <br> **(RA-GRS-läsningar)**| 99,99 % | 99,9 % | Saknas |
| **Avgifter för användning** | Högre kostnader för lagring, lägre kostnader för åtkomst och transaktioner | Lägre kostnader för lagring, högre kostnader för åtkomst och transaktioner | Lägst kostnader för lagring, högst kostnader för åtkomst och transaktioner |
| **Minsta objektstorlek** | Saknas | Saknas | Saknas |
| **Minsta lagringstid** | Gäller inte | 30 dagar (endast GPv2) | 180 dagar
| **Svarstid** <br> **(Tid till första byte)** | millisekunder | millisekunder | < 15 timmar
| **Mål för skalbarhet och prestanda** | Samma som allmänna lagringskonton | Samma som allmänna lagringskonton | Samma som allmänna lagringskonton |

> [!NOTE]
> Blob Storage-konton har samma mål för prestanda och skalbarhet som allmänna lagringskonton. Mer information finns i [Skalbarhets- och prestandamål i Azure Storage](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="quickstart-scenarios"></a>Snabbstartsscenarier

I det här avsnittet visas följande scenarier på Azure Portal:

* Ändra standardåtkomstnivån för ett GPv2- eller Blob Storage-konto.
* Ändra åtkomstnivån för en blob i ett GPv2- eller Blob Storage-konto.

### <a name="change-the-default-account-access-tier-of-a-gpv2-or-blob-storage-account"></a>Ändra standardåtkomstnivå för ett GPv2- eller Blob Storage-konto

1. Logga in på [Azure Portal](https://portal.azure.com).

2. Gå till ditt lagringskonto genom att välja Alla resurser och välj sedan ditt lagringskonto.

3. Klicka på **Konfiguration** på bladet Inställningar för att visa och/eller ändra kontokonfigurationen.

4. Välj rätt lagringsnivå för dina behov: Ange **Åtkomstnivå** till antingen **Cool** (lågfrekvent) eller **Hot** (frekvent).

5. Klicka på Spara överst på bladet.

### <a name="change-the-tier-of-a-blob-in-a-gpv2-or-blob-storage-account"></a>Ändra åtkomstnivå för en blob i ett GPv2- eller Blob Storage-konto.

1. Logga in på [Azure Portal](https://portal.azure.com).

2. Gå till din blob i lagringskontot genom att välja Alla resurser, ditt lagringskonto, din container och sedan välja din blob.

3. På bladet Blobegenskaper klickar du på listrutemenyn **Åtkomstnivå** för att välja lagringsnivån **Frekvent**, **Lågfrekvent** eller **Arkiv**.

5. Klicka på Spara överst på bladet.

## <a name="pricing-and-billing"></a>Priser och fakturering

Alla lagringskonton används en prissättningsmodell för blobblagring baserat på vilken nivå av varje blob. Tänk på följande för debitering:

* **Lagringskostnader**: Utöver mängden data som lagras varierar lagringskostnaden beroende på lagringsnivå. Kostnaden per gigabyte minskas när nivån blir mer lågfrekvent.
* **Kostnader för dataåtkomst**: Kostnaderna för dataåtkomst ökar när nivån blir mer lågfrekvent. För data på den lågfrekventa- och arkivlagringsnivån debiteras du en åtkomstavgift per gigabyte för läsningar.
* **Transaktionskostnader**: Du debiteras en kostnad per transaktion för alla nivåer som ökar när nivån blir mer lågfrekvent.
* **Dataöverföringskostnader för geo-replikering**: Den här avgiften gäller endast konton med konfigurerad geo-replikering, inklusive GRS och RA-GRS. Dataöverföring för geo-replikering debiteras per gigabyte.
* **Kostnader för utgående dataöverföring**: Utgående dataöverföringar (data som överförs utanför en Azure-region) debiteras för bandbreddsanvändning per gigabyte, på samma sätt som för allmänna lagringskonton.
* **Ändring av lagringsnivå**: Om du byter lagringskontonivå från lågfrekvent till frekvent utgår en avgift motsvarande läsningen av alla data på lagringskontot. Om du byter lagringskontonivå från frekvent till lågfrekvent utgår dock en avgift som motsvarar skrivningen av alla data till den lågfrekventa nivån (gäller endast GPv2-konton).

> [!NOTE]
> Mer information om priser för Blob storage-konton finns i [priser för Azure Storage](https://azure.microsoft.com/pricing/details/storage/) sidan. Mer information om kostnaderna för utgående dataöverföring finns på sidan [Prisinformation om Dataöverföringar](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

**Bör jag använda Blob Storage- eller GPv2-konton om jag vill lagra data i olika nivåer?**

Vi rekommenderar att du använder GPv2 i stället för Blob Storage-konton om du vill använda lagringsnivåer. GPv2 har stöd för samma funktioner som Blob Storage-konton, och mycket mer. Kostnaden för Blob Storage- och GPv2-konton är i stort sett densamma, men vissa nya funktioner och rabatter kommer bara att vara tillgängliga för GPv2-konton. GPv1-konton stöder inte lagringsnivåer.

Prisstrukturen för GPv1- och GPv2-konton skiljer sig åt, så kunderna bör noggrant utvärdera båda alternativen innan de bestämmer sig för att använda GPv2-konton. Du kan enkelt konvertera ett befintligt Blob Storage eller GPv1-konto till GPv2 via en enklicksprocess. Mer information finns i [översikt över Azure storage-konton](../common/storage-account-overview.md).

**Kan jag lagra objekt på alla tre lagringsnivåer (frekvent, lågfrekvent och arkiv) inom samma konto?**

Ja. Attributet **Åtkomstnivå** på kontonivå används som standardlagringsnivå för alla objekt i kontot som inte har en egen angiven nivå. Med blobnivåindelningen kan du ange åtkomstnivå på objektnivå oavsett vilken åtkomstnivå som angetts för kontot. Blobar från alla tre lagringsnivåer (frekvent, lågfrekvent eller arkiv) kan finnas samtidigt i samma konto.

**Kan jag ändra standardlagringsnivå för mitt Blob- eller GPv2-lagringskonto?**

Ja, du kan ändra standardlagringsnivå med attributet **Åtkomstnivå** för lagringskontot. Ändringar av lagringsnivån gäller för alla objekt som lagras i kontot som inte har en uttryckligen inställd nivå. Om du ändrar lagringsnivå från frekvent till lågfrekvent tillkommer kostnader för skrivåtgärder (per 10 000) för alla blobar utan en angiven lagringsnivå (gäller endast GPv2-konton). Om du ändrar nivå från lågfrekvent till frekvent tillkommer kostnader för läsåtgärder (per 10 000) och datahämtning (per GB) för alla blobar i Blob Storage- och GPv2-konton.

**Kan jag ange arkivlagring som standardnivå för mitt konto?**

Nej. Endast frekvent och lågfrekvent lagringsnivå kan anges som standardnivå för konton. Arkivlagringsnivån kan endast anges på objektnivå.

**I vilka regioner är lagringsnivåerna frekvent, lågfrekvent och arkivlagring tillgängliga?**

Frekvent och lågfrekvent lagring och blobnivåindelning är tillgängligt i alla regioner. Arkivlagring är inledningsvis endast tillgängligt i vissa regioner. En fullständig lista finns under [Tillgängliga Azure-produkter efter region](https://azure.microsoft.com/regions/services/).

**Beter sig blobarna på lågfrekvent lagringsnivå annorlunda än på frekvent lagringsnivå?**

Blobar på frekvent lagringsnivå har samma svarstid som blobbar i GPv1-, GPv2- och Blob Storage-konton. Blobar på lågfrekvent lagringsnivå har liknande svarstid (i millisekunder) som blobbar i GPv1-, GPv2- och Blob Storage-konton. Blobar på arkivlagringsnivå har flera timmars svarstid i GPv1-, GPv2- och Blob Storage-konton.

Blobbar på lågfrekvent lagringsnivå har något lägre tillgänglighetsnivå (enligt SLA) än blobbar som lagras på frekvent lagringsnivå. Mer information finns i [SLA för Storage](https://azure.microsoft.com/support/legal/sla/storage/v1_2/).

**Kan samma åtgärder användas för lagringsnivåerna frekvent, lågfrekvent och arkivlagring?**

Ja. På lagringsnivåerna frekvent och lågfrekvent fungerar alla åtgärder på samma sätt. På arkivnivå är vissa åtgärder giltiga (som ta bort, lista, hämta blobegenskaper/metadata och ange blobåtkomstnivå), och dessa fungerar på samma sätt som på frekvent och lågfrekvent nivå. En blob kan inte läsas eller ändras om lagringsnivån är arkivlagring.

**När jag återställer en blob från arkivlagring till frekvent eller lågfrekvent lagring, hur vet jag när processen är klar?**

Under återställning kan du använda åtgärden Hämta blobegenskaper och kontrollera attributet **Arkivstatus** för att se när nivåändringen har genomförts. Status är ”rehydrate-pending-to-hot” eller ”rehydrate-pending-to-cool” beroende på målnivån. När åtgärden har slutförts tas blobegenskapen Arkivstatus bort och blobegenskapen **Åtkomstnivå** anger den nya nivån: frekvent eller lågfrekvent.  

**När jag har angett nivå för en blob: hur lång tid tar det innan jag börjar debiteras för den nya nivån?**

Varje blob faktureras alltid enligt den nivå som anges av blobens **åtkomstnivå** egenskapen. När du ställer in en ny nivå för en blob i **åtkomstnivå** egenskapen omedelbart visar den nya nivån för alla typer av ändringar. Extrahera en blob från arkivlagring till frekvent eller lågfrekvent nivå kan dock ta flera timmar. I det här fallet debiteras du arkivnivån tills processen är klar, då den **åtkomstnivå** egenskapen visar den nya nivån. Du debiteras då för den bloben till frekvent eller lågfrekvent betalning.

**Hur kan jag se om jag påförs en avgift för tidig borttagning när jag tar bort eller flyttar en blob från lågfrekvent lagring eller arkivlagring?**

Om en blob tas bort eller flyttas från lågfrekvent lagring (endast GPv2-konton) inom 30 dagar, eller från arkivlagring inom 180 dagar, påförs en proportionellt beräknad avgift för tidig borttagning. Du kan fastställa hur länge en blob har lagrats på nivån för lågfrekvent nivå eller arkivnivå genom att markera den **ändringstid för nivån** blobegenskapen som innehåller en tidsstämpel för den senaste ändringen. Mer information finns under [Tidig borttagning från lågfrekvent lagring och arkivlagring](#cool-and-archive-early-deletion).

**Vilka Azure-verktyg och SDK:er stöder blobnivåindelning och arkivlagring?**

Azure Portal, PowerShell, CLI-verktyg och klientbiblioteken för .NET, Java, Python och Node.js har stöd för blobnivåindelning och arkivlagring.  

**Hur mycket data kan jag lagra på lagringsnivåerna frekvent, lågfrekvent och arkiv?**

Datalagringsbegränsningar och andra begränsningar anges på kontonivå, inte på lagringsnivå. Därför kan du välja att använda hela ditt lagringsutrymme i en nivå eller i alla tre nivåer. Mer information finns i [skalbarhet för lagring av Azure- och prestandamål](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="next-steps"></a>Nästa steg

### <a name="evaluate-hot-cool-and-archive-in-gpv2-blob-storage-accounts"></a>Utvärdera frekvent/lågfrekvent lagring och arkivlagring i GPv2-/Blob Storage-konton

[Kontrollera tillgängligheten för frekvent/lågfrekvent lagring och arkivlagring efter region](https://azure.microsoft.com/regions/#services)

[Hantera livscykeln för Azure Blob-lagring](https://docs.microsoft.com/azure/storage/common/storage-lifecycle-managment-concepts)

[Utvärdera användningen av dina aktuella lagringskonton genom att aktivera mätvärden i Azure Storage.](../common/storage-enable-and-view-metrics.md)

[Kontrollera priser för frekvent/lågfrekvent lagring och arkivlagring i Blob Storage-/GPv2-konton efter region](https://azure.microsoft.com/pricing/details/storage/)

[Se priser för dataöverföring](https://azure.microsoft.com/pricing/details/data-transfers/)
