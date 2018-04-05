---
title: Frekvent, lågfrekvent och arkivlagring i Azure för blobbar | Microsoft Docs
description: Frekvent och lågfrekvent lagring samt arkivlagring för Azure Storage-konton.
services: storage
documentationcenter: ''
author: kuhussai
manager: jwillis
editor: ''
ms.assetid: eb33ed4f-1b17-4fd6-82e2-8d5372800eef
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/11/2017
ms.author: kuhussai
ms.openlocfilehash: c62f3a92e6199f6467556054c9f58c20b6ceba2c
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="azure-blob-storage-hot-cool-and-archive-storage-tiers"></a>Azure Blob Storage: Nivåer för frekvent lagring, lågfrekvent lagring och arkivlagring

## <a name="overview"></a>Översikt

Azure Storage erbjuder tre lagringsnivåer för lagring av Blob-objekt så att du kan lagra data så kostnadseffektivt som möjligt, beroende på din användning. Azures **frekventa lagringsnivå** är optimerad för att lagra data som används ofta. Azures **lågfrekventa lagringsnivå** är optimerad för att lagra data som inte används ofta och som lagras i minst 30 dagar. **Arkivlagringsnivån** i Azure är optimerad för att lagra data som används sällan och som lagras i minst 180 dagar med flexibla svarstidskrav (i storleksordningen timmar). Arkivlagringsnivån är endast tillgänglig på blobnivån och inte på lagringskontonivån. Data i den lågfrekventa lagringsnivån klarar lite lägre tillgänglighet, men kräver fortfarande hög hållbarhet och liknande åtkomsttid och dataflödesegenskaper som data i frekvent lagringsnivå. För data på den lågfrekventa lagringsnivån är serviceavtal med lägre tillgänglighet och högre åtkomstkostnader jämfört med frekventa data godtagbara med tanke på de lägre lagringskostnaderna. Arkivlagring är i frånkopplat tillstånd och erbjuder de lägsta lagringskostnaderna men även de högsta åtkomstkostnaderna. Endast frekventa och lågfrekventa lagringsnivåer (ej arkiv) kan anges på kontonivå. Alla tre nivåer kan ställas in på objektnivå.

Idag växer mängden data som lagras i molnet i exponentiell takt. Om du vill hålla kontroll på och optimera kostnaderna för dina växande lagringsbehov är det en bra idé att ordna data baserat på attribut som åtkomstfrekvens och planerad kvarhållningsperiod. Data som lagras i molnet kan vara olika beroende på hur de genereras, bearbetas och används under livslängden. Vissa data används aktivt och ändras under livslängden. Vissa data används ofta i början av livslängden och sedan minskar användning drastiskt när dessa data blir äldre. Vissa data förblir inaktiva i molnet och används sällan, eller kanske aldrig, när de har lagrats.

För varje scenario finns en lagringsnivå som är optimerad för motsvarande åtkomstmönster. Med nivåerna för frekvent åtkomst, lågfrekvent åtkomst samt arkivlagringsnivån uppfyller Azure Blob Storage behovet av olika lagringsnivåer med olika prissättningsmodeller.

## <a name="storage-accounts-that-support-tiering"></a>Lagringskonton med stöd för flera lagringsnivåer

För användning av olika lagringsnivåer (frekvent, lågfrekvent eller arkivlagring) krävs ett Blob Storage eller GPv2-konto (General Purpose v2). GPv1-konton (General Purpose v1) har inte stöd för flera lagringsnivåer. Kunder kan dock enkelt konvertera sina befintliga GPv1- eller Blob Storage-konton till GPv2-konton via en enklicksprocess i Azure Portal. GPv2 har en ny prisstruktur för blobbar, filer, och köer, och ger också åtkomst till en mängd andra nya lagringsfunktioner. I framtiden kommer vissa nya funktioner och rabatter dessutom endast att erbjudas för GPv2-konton. Kunderna bör därför överväga att använda GPv2-konton, men först efter att ha granskat priserna för alla tjänster, eftersom vissa arbetsbelastningar kan bli dyrare med GPv2 än GPv1. Mer information finns i [Alternativ för Azure Storage-konton](../common/storage-account-options.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

På Blob Storage- och GPv2-konton visas attributet för **åtkomstnivå** på kontonivå, vilket innebär att du kan ange standardlagringsnivån som frekvent eller lågfrekvent för alla blobbar i lagringskontot där nivån inte har angetts på objektnivå. För objekt där nivån har ställts in på objektnivå används inte nivåinställningen för kontot. Nivån arkivlagring kan endast anges på objektnivå. Du kan växla mellan dessa lagringsnivåer när som helst.

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
Om du vill läsa data i arkivlagring måste du först ändra nivå för din blob till frekvent eller lågfrekvent. Processen kallas återuppväckning och kan ta upp till 15 timmar. Stora blobbstorlekar rekommenderas för optimala prestanda. Återställning av flera små blobbar samtidigt kan ta ännu längre tid.

Under återuppväckning kan du kontrollera blobegenskapen **Arkivstatus** för att se om nivån har ändrats. Status är ”rehydrate-pending-to-hot” eller ”rehydrate-pending-to-cool” beroende på målnivån. När åtgärden har slutförts tas blobegenskapen Arkivstatus bort och blobegenskapen **Åtkomstnivå** anger den nya nivån: frekvent eller lågfrekvent.  

## <a name="blob-level-tiering"></a>Blobnivåindelning

Med blobnivåindelning kan du ändra nivå för dina data på objektnivå med hjälp av en enda åtgärd som kallas [Ange blobnivå](/rest/api/storageservices/set-blob-tier). Du kan enkelt ändra åtkomstnivå för en blob mellan frekvent, lågfrekvent eller arkivnivå när användningsmönster ändras, utan att behöva flytta data mellan konton. Alla nivåändringar sker omedelbart förutom när en blob återuppväcks från arkivet, vilket kan ta flera timmar. Tiden för den senaste ändringen på blobnivån är tillgänglig via blobegenskapen **Ändringstid för åtkomstnivå**. Om en blob finns i arkivnivån kan den inte skrivas över och därför är det inte tillåtet att ladda upp samma blob i det här scenariot. Du kan skriva över en blob på den frekventa och lågfrekventa nivån. Då ärver den nya bloben nivån för den gamla bloben som skrevs över.

Blobar i alla tre lagringsnivåer kan finnas tillsammans i samma konto. En blob som inte har en uttryckligen tilldelad nivå härleder nivån från kontots åtkomstnivåinställning. Om åtkomstnivån härleds från kontot så är blobegenskapen **Åtkomstnivå härledd** inställd på ”true”, och blobegenskapen **Åtkomstnivå** matchar kontonivån. Egenskapen som härletts från åtkomstnivån i Azure Portal visas med blobåtkomstnivån (till exempel frekvent (härledd) eller lågfrekvent (härledd)).

> [!NOTE]
> Arkivlagring och blobnivåindelning stöder endast blockblobar. Du kan även ändra nivå för en blockblob som har ögonblicksbilder.

### <a name="blob-level-tiering-billing"></a>Fakturering för blobnivåindelning

Om en blob flyttas till en mer lågfrekvent nivå (frekvent -> lågfrekvent, frekvent -> arkiv eller lågfrekvent -> arkiv) faktureras åtgärden som en skrivning till målnivån och avgifter för skrivåtgärden (per 10 000) och dataskrivning (per GB) för målnivån tillämpas. Om en blob flyttas till en mer frekvent nivå (arkiv -> lågfrekvent, arkiv -> frekvent eller lågfrekvent -> frekvent) faktureras åtgärden som en läsning från källnivån och avgifter för läsåtgärden (per 10 000) och datahämtning (per GB) för källnivån tillämpas.

Om du ändrar kontonivån från frekvent till lågfrekvent debiteras du för skrivåtgärder (per 10 000) för blobbar som saknar angiven nivå, men endast i GPv2-konton. Ingen debitering sker för detta i Blob Storage-konton. Du debiteras för både läsåtgärder (per 10 000) och datahämtning (per GB) om du växlar ditt Blob Storage- eller GPv2-konto från lågfrekvent till frekvent. Kostnader för tidig borttagning av blobbar från lågfrekvent lagring eller arkivlagring kan också tillkomma.

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
| **Minsta lagringstid** | Saknas | 30 dagar (endast GPv2) | 180 dagar
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

2. Gå till din blob i lagringskontot genom att välja Alla resurser, ditt lagringskonto, din behållare och sedan välja din blob.

3. På bladet Blobegenskaper klickar du på listrutemenyn **Åtkomstnivå** för att välja lagringsnivån **Frekvent**, **Lågfrekvent** eller **Arkiv**.

5. Klicka på Spara överst på bladet.

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

**Bör jag använda Blob Storage- eller GPv2-konton om jag vill lagra data i olika nivåer?**

Vi rekommenderar att du använder GPv2 i stället för Blob Storage-konton om du vill använda lagringsnivåer. GPv2 har stöd för samma funktioner som Blob Storage-konton, och mycket mer. Kostnaden för Blob Storage- och GPv2-konton är i stort sett densamma, men vissa nya funktioner och rabatter kommer bara att vara tillgängliga för GPv2-konton. GPv1-konton stöder inte lagringsnivåer.

Prisstrukturen för GPv1- och GPv2-konton skiljer sig åt, så kunderna bör noggrant utvärdera båda alternativen innan de bestämmer sig för att använda GPv2-konton. Du kan enkelt konvertera ett befintligt Blob Storage eller GPv1-konto till GPv2 via en enklicksprocess. Mer information finns i [Alternativ för Azure Storage-konton](../common/storage-account-options.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

**Kan jag lagra objekt på alla tre lagringsnivåer (frekvent, lågfrekvent och arkiv) inom samma konto?**

Ja. Attributet **Åtkomstnivå** på kontonivå används som standardlagringsnivå för alla objekt i kontot som inte har en egen angiven nivå. Med blobnivåindelningen kan du ange åtkomstnivå på objektnivå oavsett vilken åtkomstnivå som angetts för kontot. Blobar från alla tre lagringsnivåer (frekvent, lågfrekvent eller arkiv) kan finnas samtidigt i samma konto.

**Kan jag ändra standardlagringsnivå för mitt Blob- eller GPv2-lagringskonto?**

Ja, du kan ändra standardlagringsnivå med attributet **Åtkomstnivå** för lagringskontot. Ändringar av lagringsnivån gäller för alla objekt som lagras i kontot som inte har en uttryckligen inställd nivå. Om du ändrar lagringsnivå från frekvent till lågfrekvent tillkommer kostnader för skrivåtgärder (per 10 000) för alla blobbar utan en angiven lagringsnivå (gäller endast GPv2-konton). Om du ändrar nivå från lågfrekvent till frekvent tillkommer kostnader för läsåtgärder (per 10 000) och datahämtning (per GB) för alla blobbar i Blob Storage- och GPv2-konton.

**Kan jag ange arkivlagring som standardnivå för mitt konto?**

Nej. Endast frekvent och lågfrekvent lagringsnivå kan anges som standardnivå för konton. Arkivlagringsnivån kan endast anges på objektnivå.

**I vilka regioner är lagringsnivåerna frekvent, lågfrekvent och arkivlagring tillgängliga?**

Frekvent och lågfrekvent lagring och blobnivåindelning är tillgängligt i alla regioner. Arkivlagring är inledningsvis endast tillgängligt i vissa regioner. En fullständig lista finns under [Tillgängliga Azure-produkter efter region](https://azure.microsoft.com/regions/services/).

**Beter sig blobarna på lågfrekvent lagringsnivå annorlunda än på frekvent lagringsnivå?**

Blobbar på frekvent lagringsnivå har samma svarstid som blobbar i GPv1-, GPv2- och Blob Storage-konton. Blobbar på lågfrekvent lagringsnivå har liknande svarstid (i millisekunder) som blobbar i GPv1-, GPv2- och Blob Storage-konton. Blobbar på arkivlagringsnivå har flera timmars svarstid i GPv1-, GPv2- och Blob Storage-konton.

Blobbar på lågfrekvent lagringsnivå har något lägre tillgänglighetsnivå (enligt SLA) än blobbar som lagras på frekvent lagringsnivå. Mer information finns i [SLA för Storage](https://azure.microsoft.com/support/legal/sla/storage/v1_2/).

**Kan samma åtgärder användas för lagringsnivåerna frekvent, lågfrekvent och arkivlagring?**

Ja. På lagringsnivåerna frekvent och lågfrekvent fungerar alla åtgärder på samma sätt. På arkivnivå är vissa åtgärder giltiga (som ta bort, lista, hämta blobegenskaper/metadata och ange blobåtkomstnivå), och dessa fungerar på samma sätt som på frekvent och lågfrekvent nivå. En blob kan inte läsas eller ändras om lagringsnivån är arkivlagring.

**När jag återställer en blob från arkivlagring till frekvent eller lågfrekvent lagring, hur vet jag när processen är klar?**

Under återställning kan du använda åtgärden Hämta blobegenskaper och kontrollera attributet **Arkivstatus** för att se när nivåändringen har genomförts. Status är ”rehydrate-pending-to-hot” eller ”rehydrate-pending-to-cool” beroende på målnivån. När åtgärden har slutförts tas blobegenskapen Arkivstatus bort och blobegenskapen **Åtkomstnivå** anger den nya nivån: frekvent eller lågfrekvent.  

**När jag har angett nivå för en blob: hur lång tid tar det innan jag börjar debiteras för den nya nivån?**

Varje blob faktureras alltid enligt den nivå som angetts med blobegenskapen **Åtkomstnivå**. När du ändrar nivån för en blob återspeglas detta omedelbart i egenskapen **Åtkomstnivå** för alla typer av ändringar förutom när en blob återuppväcks från arkivet till frekvent eller lågfrekvent, vilket kan ta flera timmar. I sådana fall fortsätter debiteringen enligt arkivnivån till dess att återställningen har slutförts och den nya nivån visas i attributet **Åtkomstnivå**. Det är först när attributvärdet har ändrats som debiteringen justeras.

**Hur kan jag se om jag påförs en avgift för tidig borttagning när jag tar bort eller flyttar en blob från lågfrekvent lagring eller arkivlagring?**

Om en blob tas bort eller flyttas från lågfrekvent lagring (endast GPv2-konton) inom 30 dagar, eller från arkivlagring inom 180 dagar, påförs en proportionellt beräknad avgift för tidig borttagning. Du kan se hur länge en blob har lagrats på lågfrekvent nivå eller arkivnivå genom att kontrollera blobegenskapen **Ändringstid för åtkomstnivå** som innehåller en tidsstämpel för den senaste nivåändringen. Mer information finns under [Tidig borttagning från lågfrekvent lagring och arkivlagring](#cool-and-archive-early-deletion).

**Vilka Azure-verktyg och SDK:er stöder blobnivåindelning och arkivlagring?**

Azure Portal, PowerShell, CLI-verktyg och klientbiblioteken för .NET, Java, Python och Node.js har stöd för blobnivåindelning och arkivlagring.  

**Hur mycket data kan jag lagra på lagringsnivåerna frekvent, lågfrekvent och arkiv?**

Datalagringsbegränsningar och andra begränsningar anges på kontonivå, inte på lagringsnivå. Du kan alltså välja att använda hela ditt lagringsutrymme på en enda nivå eller fördelat på alla tre nivåer. Mer information finns i [Skalbarhets- och prestandamål i Azure Storage](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="next-steps"></a>Nästa steg

### <a name="evaluate-hot-cool-and-archive-in-gpv2-blob-storage-accounts"></a>Utvärdera frekvent/lågfrekvent lagring och arkivlagring i GPv2-/Blob Storage-konton

[Kontrollera tillgängligheten för frekvent/lågfrekvent lagring och arkivlagring efter region](https://azure.microsoft.com/regions/#services)

[Utvärdera användningen av dina aktuella lagringskonton genom att aktivera mätvärden i Azure Storage.](../common/storage-enable-and-view-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

[Kontrollera priser för frekvent/lågfrekvent lagring och arkivlagring i Blob Storage-/GPv2-konton efter region](https://azure.microsoft.com/pricing/details/storage/)

[Se priser för dataöverföring](https://azure.microsoft.com/pricing/details/data-transfers/)
