---
title: BLOB-ögonblicksbilder
titleSuffix: Azure Storage
description: Lär dig hur du skapar en skrivskyddad ögonblicks bild av en BLOB för att säkerhetskopiera BLOB-data vid en specifik tidpunkt.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/02/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 24118e6ae5c31399ce5d33361dd60e3a08424681
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2020
ms.locfileid: "88055776"
---
# <a name="blob-snapshots"></a>BLOB-ögonblicksbilder

En ögonblicks bild är en skrivskyddad version av en blob som tas vid en tidpunkt.

> [!NOTE]
> BLOB-versioner (för hands version) erbjuder ett alternativt sätt att underhålla historiska kopior av en blob. Mer information finns i [BLOB-Versioning (för hands version)](versioning-overview.md).

## <a name="about-blob-snapshots"></a>Om BLOB-ögonblicksbilder

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

En ögonblicks bild av en BLOB är identisk med dess bas-BLOB, förutom att BLOB-URI: n har ett **datetime** -värde som läggs till i BLOB-URI: n för att ange tiden då ögonblicks bilden togs. Om till exempel en URI-URI för en sida är `http://storagesample.core.blob.windows.net/mydrives/myvhd` , liknar ögonblicks bildens URI `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z` .

> [!NOTE]
> Alla ögonblicks bilder delar bas-blobens URI. Den enda skillnaden mellan bas-bloben och ögonblicks bilden är det tillagda **datetime** -värdet.
>

En BLOB kan ha valfritt antal ögonblicks bilder. Ögonblicks bilder behålls tills de tas bort uttryckligen, antingen oberoende eller som en del av åtgärden ta bort BLOB för bas-bloben. Du kan räkna upp ögonblicks bilderna som är kopplade till bas-bloben för att spåra dina befintliga ögonblicks bilder.

När du skapar en ögonblicks bild av en BLOB kopieras blobens system egenskaper till ögonblicks bilden med samma värden. Bas-blobens metadata kopieras också till ögonblicks bilden, om du inte anger separata metadata för ögonblicks bilden när du skapar den. När du har skapat en ögonblicks bild kan du läsa, kopiera eller ta bort den, men du kan inte ändra den.

Alla lån som är associerade med bas-bloben påverkar inte ögonblicks bilden. Du kan inte förvärva ett lån för en ögonblicks bild.

En VHD-fil används för att lagra aktuell information och status för en VM-disk. Du kan koppla från en disk inifrån den virtuella datorn eller stänga av den virtuella datorn och sedan ta en ögonblicks bild av sin VHD-fil. Du kan använda den ögonblicks bild filen senare för att hämta VHD-filen vid den tidpunkten och återskapa den virtuella datorn.

## <a name="understand-how-snapshots-accrue-charges"></a>Förstå hur ögonblicks bilder debiteras

När du skapar en ögonblicks bild, som är en skrivskyddad kopia av en BLOB, kan det leda till ytterligare avgifter för data lagring till ditt konto. När du designar ditt program är det viktigt att vara medveten om hur dessa avgifter kan påföras så att du kan minimera kostnaderna.

### <a name="important-billing-considerations"></a>Viktiga fakturerings överväganden

I följande lista finns viktiga punkter att tänka på när du skapar en ögonblicks bild.

- Ditt lagrings konto debiteras för unika block eller sidor, oavsett om de finns i blobben eller i ögonblicks bilden. Ditt konto debiterar inte ytterligare avgifter för ögonblicks bilder som är kopplade till en BLOB förrän du uppdaterar den blob som de baseras på. När du har uppdaterat bas-bloben avviker den från ögonblicks bilderna. När detta inträffar debiteras du för unika block eller sidor i varje BLOB eller ögonblicks bild.
- När du ersätter ett block i en Block-Blob debiteras det här blocket som ett unikt block. Detta gäller även om blocket har samma block-ID och samma data som i ögonblicks bilden. När blocket har allokerats igen skiljer det sig från dess motsvarighet i alla ögonblicks bilder och du debiteras för data. Samma undantag gäller för en sida i en sid-blob som uppdateras med identiska data.
- Om du ersätter en block-BLOB genom att anropa metoden [UploadFromFile] [dotnet_UploadFromFile], [UploadText] [dotnet_UploadText], [UploadFromStream] [dotnet_UploadFromStream] eller [UploadFromByteArray] [dotnet_UploadFromByteArray], ersätter alla block i blobben. Om du har en ögonblicks bild som är associerad med denna BLOB så kommer alla block i bas-blob och ögonblicks bilder att debiteras för alla block i båda blobarna. Detta gäller även om data i bas-bloben och ögonblicks bilden är identiska.
- Azure-Blob Service har inget sätt att avgöra om två block innehåller identiska data. Varje block som överförs och bekräftas behandlas som unikt, även om det har samma data och samma block-ID. Eftersom avgifterna är för unika block, är det viktigt att överväga att uppdatera en blob som har en ögonblicks bild som resulterar i ytterligare unika block och ytterligare kostnader.

### <a name="minimize-cost-with-snapshot-management"></a>Minimera kostnaderna med ögonblicks bild hantering

Vi rekommenderar att du hanterar ögonblicks bilderna noggrant för att undvika extra kostnader. Du kan följa dessa metod tips för att minimera kostnaderna för lagringen av dina ögonblicks bilder:

- Ta bort och återskapa ögonblicks bilder som är associerade med en BLOB varje gång du uppdaterar blobben, även om du uppdaterar med identiska data, om inte program designen kräver att du underhåller ögonblicks bilder. Genom att ta bort och återskapa blobens ögonblicks bilder kan du se till att bloben och ögonblicks bilderna inte avviker.
- Om du behåller ögonblicks bilder för en BLOB bör du undvika att anropa [UploadFromFile] [dotnet_UploadFromFile], [UploadText] [dotnet_UploadText], [UploadFromStream] [dotnet_UploadFromStream] eller [UploadFromByteArray] [dotnet_UploadFromByteArray] för att uppdatera blobben. Dessa metoder ersätter alla block i blobben, vilket leder till att bas-bloben och dess ögonblicks bilder avviker markant. Uppdatera i stället minsta möjliga antal block med metoderna [PutBlock] [dotnet_PutBlock] och [PutBlockList] [dotnet_PutBlockList].

### <a name="snapshot-billing-scenarios"></a>Fakturerings scenarier för ögonblicks bilder

Följande scenarier visar hur avgifterna påförs för en Block-Blob och dess ögonblicks bilder.

#### <a name="scenario-1"></a>Scenario 1

I Scenario 1 har bas-bloben inte uppdaterats efter att ögonblicks bilden togs, så avgifterna debiteras bara för unika block 1, 2 och 3.

![Azure Storage resurser](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-1.png)

#### <a name="scenario-2"></a>Scenario 2

I scenario 2 har bas-bloben uppdaterats, men ögonblicks bilden har inte det. Block 3 har uppdaterats och även om det innehåller samma data och samma ID är det inte samma som för Block 3 i ögonblicks bilden. Det innebär att kontot debiteras för fyra block.

![Azure Storage resurser](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-2.png)

#### <a name="scenario-3"></a>Scenario 3

I scenario 3 har bas-bloben uppdaterats, men ögonblicks bilden har inte det. Block 3 har ersatts med block 4 i bas-blobben, men ögonblicks bilden visar fortfarande Block 3. Det innebär att kontot debiteras för fyra block.

![Azure Storage resurser](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-3.png)

#### <a name="scenario-4"></a>Scenario 4

I Scenario 4 har bas-bloben uppdaterats helt och innehåller inget av de ursprungliga blocken. Kontot debiteras därför för alla åtta unika block. Det här scenariot kan inträffa om du använder en uppdaterings metod, till exempel [UploadFromFile] [dotnet_UploadFromFile], [UploadText] [dotnet_UploadText], [UploadFromStream] [dotnet_UploadFromStream] eller [UploadFromByteArray] [dotnet_UploadFromByteArray], eftersom dessa metoder ersätter hela innehållet i en blob.

![Azure Storage resurser](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-4.png)

## <a name="next-steps"></a>Nästa steg

- [Skapa och hantera en BLOB-ögonblicksbild i .NET](snapshots-manage-dotnet.md)
- [Säkerhetskopiera Azure ohanterade virtuella dator diskar med stegvisa ögonblicks bilder](../../virtual-machines/windows/incremental-snapshots.md)
