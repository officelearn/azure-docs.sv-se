---
title: Redundans- och lagringskonto för katastrofberedskap och lagringskonto (förhandsversion)
titleSuffix: Azure Storage
description: Azure Storage stöder konto redundans (förhandsversion) för geo-redundanta lagringskonton. Med felinformation om kontot kan du initiera redundansprocessen för ditt lagringskonto om den primära slutpunkten blir otillgänglig.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: f7a8f6d0d3ab3b456c41128da9b689f6b7eda0f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79365379"
---
# <a name="disaster-recovery-and-account-failover-preview"></a>Katastrofåterställning och växling av konton (förhandsversion)

Microsoft strävar efter att se till att Azure-tjänster alltid är tillgängliga. Oplanerade avbrott i tjänsten kan dock förekomma. Om ditt program kräver återhämtning rekommenderar Microsoft att du använder geouppsagbar lagring, så att dina data kopieras till en andra region. Dessutom bör kunder ha en katastrofåterställningsplan på plats för hantering av ett regionalt avbrott i tjänsten. En viktig del av en haveriberedskapsplan förbereder sig för att växla över till den sekundära slutpunkten i händelse av att den primära slutpunkten blir otillgänglig.

Azure Storage stöder konto redundans (förhandsversion) för geo-redundanta lagringskonton. Med felinformation om kontot kan du initiera redundansprocessen för ditt lagringskonto om den primära slutpunkten blir otillgänglig. Redundansen uppdaterar den sekundära slutpunkten för att bli den primära slutpunkten för ditt lagringskonto. När redundansen är klar kan klienter börja skriva till den nya primära slutpunkten.

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

I den här artikeln beskrivs begreppen och processen som ingår i en kontoundanssväxling och hur du förbereder ditt lagringskonto för återställning med minsta möjliga kundpåverkan. Mer information om hur du initierar en kontoundanslag i Azure-portalen eller PowerShell finns [i Initiera en återställning av kontot (förhandsversion).](storage-initiate-account-failover.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="choose-the-right-redundancy-option"></a>Välj rätt redundansalternativ

Azure Storage underhåller flera kopior av ditt lagringskonto för att säkerställa hållbarhet och hög tillgänglighet. Vilket redundansalternativ du väljer för ditt konto beror på hur hög grad av återhämtning du behöver. För skydd mot regionala avbrott väljer du geoupptundit lagring, med eller utan möjlighet till läsåtkomst från den sekundära regionen:  

**Geo-redundant lagring (GRS) eller geo-zon-redundant lagring (GZRS) (förhandsvisning)** kopierar dina data asynkront i två geografiska regioner som är minst hundratals miles från varandra. Om den primära regionen drabbas av ett avbrott fungerar den sekundära regionen som en redundant källa för dina data. Du kan initiera en redundans för att omvandla den sekundära slutpunkten till den primära slutpunkten.

**Läsåtkomst geo-redundant lagring (RA-GRS) eller läsåtkomst geo-zon-redundant lagring (RA-GZRS) (förhandsvisning)** ger geo-redundant lagring med ytterligare nytta av läsåtkomst till den sekundära slutpunkten. Om ett avbrott inträffar i den primära slutpunkten kan program som konfigurerats för RA-GRS och utformats för hög tillgänglighet fortsätta att läsa från den sekundära slutpunkten. Microsoft rekommenderar RA-GRS för maximal återhämtning för dina program.

Mer information om redundans i Azure Storage finns i [Azure Storage redundans](storage-redundancy.md).

> [!WARNING]
> Geo redundant lagring medför en risk för dataförlust. Data kopieras till den sekundära regionen asynkront, vilket innebär att det finns en fördröjning mellan när data som skrivs till den primära regionen skrivs till den sekundära regionen. I händelse av ett avbrott går skrivåtgärder till den primära slutpunkten som ännu inte har kopierats till den sekundära slutpunkten förlorade.

## <a name="design-for-high-availability"></a>Utforma för hög tillgänglighet

Det är viktigt att utforma ditt program för hög tillgänglighet från början. Se dessa Azure-resurser för vägledning i utformningen av ditt program och planering för haveriberedskap:

- [Designa elastiska program för Azure](/azure/architecture/checklist/resiliency-per-service): En översikt över de viktigaste begreppen för att skapa högtillgängliga program i Azure.
- [Checklista](/azure/architecture/checklist/resiliency-per-service)för tillgänglighet : En checklista för att verifiera att ditt program implementerar de bästa designmetoderna för hög tillgänglighet.
- [Designa högtillgängliga applikationer med RA-GRS:](storage-designing-ha-apps-with-ragrs.md)Designvägledning för att bygga applikationer för att dra nytta av RA-GRS.
- [Självstudiekurs: Skapa ett program med högtillgänglighet med Blob-lagring](../blobs/storage-create-geo-redundant-storage.md): En självstudiekurs som visar hur du skapar ett program med högtillgänglighet som automatiskt växlar mellan slutpunkter när fel och återställningar simuleras. 

Tänk dessutom på de här metodtipsen för att upprätthålla hög tillgänglighet för dina Azure Storage-data:

- **Diskar:** Använd [Azure Backup](https://azure.microsoft.com/services/backup/) för att säkerhetskopiera de VM-diskar som används av dina virtuella Azure-datorer. Överväg också att använda [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) för att skydda dina virtuella datorer i händelse av en regional katastrof.
- **Blockblobar:** Aktivera [mjuk borttagning](../blobs/storage-blob-soft-delete.md) för att skydda mot borttagningar och överskrivningar på objektnivå, eller kopiera blockblobar till ett annat lagringskonto i en annan region med [AzCopy,](storage-use-azcopy.md) [Azure PowerShell](storage-powershell-guide-full.md)eller [Azure Data Movement-biblioteket](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/).
- **Filer:** Använd [AzCopy](storage-use-azcopy.md) eller [Azure PowerShell](storage-powershell-guide-full.md) för att kopiera dina filer till ett annat lagringskonto i en annan region.
- **Tabeller:** Använd [AzCopy](storage-use-azcopy.md) för att exportera tabelldata till ett annat lagringskonto i en annan region.

## <a name="track-outages"></a>Spåra avbrott

Kunder kan prenumerera på [Azure Service Health Dashboard](https://azure.microsoft.com/status/) för att spåra hälsotillstånd och status för Azure Storage och andra Azure-tjänster.

Microsoft rekommenderar också att du utformar programmet för att förbereda dig för risken för skrivfel. Ditt program bör exponera skrivfel på ett sätt som varnar dig för risken för ett avbrott i den primära regionen.

## <a name="understand-the-account-failover-process"></a>Förstå felningsprocessen för kontot

Med kundhanterad felväxling (förhandsversion) kan du underkänna hela ditt lagringskonto till den sekundära regionen om den primära blir otillgänglig av någon anledning. När du tvingar en redundans till den sekundära regionen kan klienter börja skriva data till den sekundära slutpunkten när redundansen har slutförts. Redundansen tar vanligtvis ungefär en timme.

### <a name="how-an-account-failover-works"></a>Så här fungerar kontoredundans

Under normala omständigheter skriver en klient data till ett Azure Storage-konto i den primära regionen och att data kopieras asynkront till den sekundära regionen. Följande bild visar scenariot när den primära regionen är tillgänglig:

![Klienter skriver data till lagringskontot i den primära regionen](media/storage-disaster-recovery-guidance/primary-available.png)

Om den primära slutpunkten blir otillgänglig av någon anledning kan klienten inte längre skriva till lagringskontot. Följande bild visar scenariot där den primära har blivit otillgänglig, men ingen återställning har skett ännu:

![Den primära är inte tillgänglig, så klienter kan inte skriva data](media/storage-disaster-recovery-guidance/primary-unavailable-before-failover.png)

Kunden initierar kontot redundans till den sekundära slutpunkten. Redundansprocessen uppdaterar DNS-posten som tillhandahålls av Azure Storage så att den sekundära slutpunkten blir den nya primära slutpunkten för ditt lagringskonto, som visas i följande bild:

![Kunden initierar kontoundans till sekundär slutpunkt](media/storage-disaster-recovery-guidance/failover-to-secondary.png)

Skrivåtkomsten återställs för GRS- och RA-GRS-konton när DNS-posten har uppdaterats och begäranden dirigeras till den nya primära slutpunkten. Befintliga slutpunkter för lagringstjänster för blobbar, tabeller, köer och filer förblir desamma efter redundansen.

> [!IMPORTANT]
> När redundansen är klar konfigureras lagringskontot så att det är lokalt redundant i den nya primära slutpunkten. Om du vill återuppta replikeringen till den nya sekundära konfigurerar du kontot så att det använder geoupptundsräknad lagring igen (antingen RA-GRS eller GRS).
>
> Tänk på att det kostar att konvertera ett LRS-konto till RA-GRS eller GRS. Den här kostnaden gäller för uppdatering av lagringskontot i den nya primära regionen för att använda RA-GRS eller GRS efter en redundans.  

### <a name="anticipate-data-loss"></a>Förutse dataförlust

> [!CAUTION]
> En växlingsförlust för kontot innebär vanligtvis viss dataförlust. Det är viktigt att förstå konsekvenserna av att initiera en växling av kontot.  

Eftersom data skrivs asynkront från den primära regionen till den sekundära regionen, finns det alltid en fördröjning innan en skrivning till den primära regionen kopieras till den sekundära regionen. Om den primära regionen blir otillgänglig kanske de senaste skrivna ännu inte har kopierats till den sekundära regionen.

När du tvingar fram en redundans går alla data i den primära regionen förlorade när den sekundära regionen blir den nya primära regionen och lagringskontot är konfigurerat för att vara lokalt redundant. Alla data som redan har kopierats till den sekundära underhålls när redundansen sker. Alla data som skrivs till den primära som inte också har kopierats till den sekundära går dock förlorade permanent.

Egenskapen **Senaste synkroniseringstid** anger den senaste tiden då data från den primära regionen garanterat har skrivits till den sekundära regionen. Alla data som skrivits före den senaste synkroniseringstiden är tillgängliga på den sekundära, medan data som skrivits efter den senaste synkroniseringstiden kanske inte har skrivits till den sekundära och kan gå förlorade. Använd den här egenskapen i händelse av ett avbrott för att uppskatta mängden dataförlust som du kan ådra dig genom att initiera en växlingsöverkoppling för kontot.

Du kan göra bästa genom att utforma programmet så att du kan använda den senaste synkroniseringstiden för att utvärdera förväntad dataförlust. Om du till exempel loggar alla skrivåtgärder kan du jämföra tiden för dina senaste skrivåtgärder med den senaste synkroniseringstiden för att avgöra vilka skrivningar som inte har synkroniserats med den sekundära.

### <a name="use-caution-when-failing-back-to-the-original-primary"></a>Var försiktig när du misslyckas tillbaka till den ursprungliga primära

När du har växlat över från den primära till den sekundära regionen är ditt lagringskonto konfigurerat så att det är lokalt redundant i den nya primära regionen. Du kan konfigurera kontot för georedundans igen genom att uppdatera det för att använda GRS eller RA-GRS. När kontot har konfigurerats för geo-redundans igen efter en redundans, börjar den nya primära regionen omedelbart kopiera data till den nya sekundära regionen, som var den primära före den ursprungliga redundansen. Det kan dock ta lite tid innan befintliga data i den primära kopieras helt till den nya sekundära.

När lagringskontot har konfigurerats om för georedundans är det möjligt att initiera en ny redundans från den nya primära tillbaka till den nya sekundära. I det här fallet blir den ursprungliga primära regionen före redundansen den primära regionen igen och är konfigurerad för att vara lokalt redundant. Alla data i den primära regionen efter redundans (den ursprungliga sekundära) går sedan förlorade. Om de flesta data i lagringskontot inte har kopierats till den nya sekundära innan du misslyckas tillbaka, kan du drabbas av en större dataförlust.

Om du vill undvika en större dataförlust kontrollerar du värdet för egenskapen **Senaste synkroniseringstid** innan du misslyckas tillbaka. Jämför den senaste synkroniseringstiden med de senaste gångerna som data skrevs till den nya primärt för att utvärdera förväntad dataförlust. 

## <a name="initiate-an-account-failover"></a>Initiera en kontoredundans

Du kan initiera en kontoundangång från Azure-portalen, PowerShell, Azure CLI eller Azure Storage-resursproviderns API. Mer information om hur du initierar en redundans finns i [Initiera en kontoundanvisning (förhandsgranskning).](storage-initiate-account-failover.md)

## <a name="about-the-preview"></a>Om förhandsgranskningen

Kontoundans är tillgängligt i förhandsversion för alla kunder som använder GRS- eller RA-GRS med Azure Resource Manager-distributioner. Generella v1-, general-purpose v2- och Blob-lagringskontotyper stöds. Kontoundans är för närvarande tillgängligt i alla offentliga regioner. Konto redundans är inte tillgängligt i suveräna/nationella moln just nu.

Förhandsgranskningen är endast avsedd för användning som inte är produktion. Avtal på produktionsnivå (SLA) är för närvarande inte tillgängliga.

### <a name="additional-considerations"></a>Annat som är bra att tänka på

Granska de ytterligare överväganden som beskrivs i det här avsnittet för att förstå hur dina program och tjänster kan påverkas när du tvingar fram en redundans under förhandsgranskningsperioden.

#### <a name="storage-account-containing-archived-blobs"></a>Lagringskonto som innehåller arkiverade blobbar

Lagringskonton som innehåller arkiverade blobbar supportkonto redundans. När redundansen är klar måste alla arkiverade blobbar om du vill konvertera kontot till GRS eller RA-GRS.

#### <a name="storage-resource-provider"></a>Lagringsresursprovider

När en redundans har slutförts kan klienter återigen läsa och skriva Azure Storage-data i den nya primära regionen. Azure Storage-resursprovidern växlar dock inte över, så resurshanteringsåtgärder måste fortfarande ske i den primära regionen. Om den primära regionen inte är tillgänglig kan du inte utföra hanteringsåtgärder på lagringskontot.

Eftersom Azure Storage-resursprovidern inte [Location](/dotnet/api/microsoft.azure.management.storage.models.trackedresource.location) växlar över returneras den ursprungliga primära platsen när redundansen har slutförts.

#### <a name="azure-virtual-machines"></a>Virtuella Azure-datorer

Virtuella Azure-datorer (VMs) växlar inte över som en del av en växlingsöverkoppling för kontot. Om den primära regionen blir otillgänglig och du växlar över till den sekundära regionen måste du återskapa alla virtuella datorer efter redundansen. Det finns också en potentiell dataförlust som är associerad med kontot redundans. Microsoft rekommenderar följande vägledning om [hög tillgänglighet](../../virtual-machines/windows/manage-availability.md) och [katastrofåterställning](../../virtual-machines/virtual-machines-disaster-recovery-guidance.md) som är specifik för virtuella datorer i Azure.

#### <a name="azure-unmanaged-disks"></a>Ohanterat azure-diskar

Som ett bästa praxis rekommenderar Microsoft att konvertera ohanterade diskar till hanterade diskar. Men om du behöver växla över ett konto som innehåller ohanterade diskar som är kopplade till virtuella Azure-datorer måste du stänga av den virtuella datorn innan du initierar redundansen.

Ohanterat diskar lagras som sidblobar i Azure Storage. När en virtuell dator körs i Azure, alla ohanterade diskar som är anslutna till den virtuella datorn hyrs. En kontoundans kan inte fortsätta när det finns ett lån på en blob. Så här utför du redundansen:

1. Innan du börjar bör du anteckna namnen på alla ohanterade diskar, deras logiska enhetsnummer (LUN) och den virtuella dator som de är kopplade till. Om du gör det blir det lättare att sätta tillbaka diskarna efter redundansen.
2. Stäng av den virtuella datorn.
3. Ta bort den virtuella datorn, men behåll VHD-filerna för ohanterade diskar. Observera den tidpunkt då du tog bort den virtuella datorn.
4. Vänta tills den **senaste synkroniseringstiden** har uppdaterats och är senare än den tidpunkt då du tog bort den virtuella datorn. Det här steget är viktigt, för om den sekundära slutpunkten inte har uppdaterats helt med VHD-filerna när redundansen inträffar, kanske den virtuella datorn inte fungerar korrekt i den nya primära regionen.
5. Initiera kontot redundans.
6. Vänta tills kontot redundans är klar och den sekundära regionen har blivit den nya primära regionen.
7. Skapa en virtuell dator i den nya primära regionen och sätt tillbaka de virtuella hårddiskarna.
8. Starta den nya virtuella datorn.

Tänk på att alla data som lagras på en tillfällig disk går förlorade när den virtuella datorn stängs av.

### <a name="unsupported-features-and-services"></a>Funktioner och tjänster som inte stöds

Följande funktioner och tjänster stöds inte för kontoundansväxling för förhandsversionen:

- Azure File Sync stöder inte redundans för lagringskonto. Lagringskonton med Azure-filresurser som används som molnslutpunkter i Azure File Sync får inte redundansväxlas. Om du gör det slutar synkroniseringen att fungera och det kan leda till oväntade dataförluster för nyligen nivåbaserade filer.
- ADLS Gen2-lagringskonton (konton som har aktiverat hierarkiskt namnområde) stöds inte just nu.
- Det går inte att överföra ett lagringskonto som innehåller premiumblockblobar. Lagringskonton som stöder premiumblockblobar stöder för närvarande inte georedundans.
- Ett lagringskonto som innehåller en [WORM-princip för oföränderlighet](../blobs/storage-blob-immutable-storage.md) kan inte överföras. Olåsta/låsta tidsbaserade bevarande- eller legal hold-principer förhindrar redundans för att upprätthålla efterlevnad.
- När redundansen är klar kan följande funktioner sluta fungera om de ursprungligen aktiverades: [Händelseprenumerationer,](../blobs/storage-blob-event-overview.md) [ändringsflöde,](../blobs/storage-blob-change-feed.md) [livscykelprinciper](../blobs/storage-lifecycle-management-concepts.md)och [lagringsanalysloggning](storage-analytics-logging.md).

## <a name="copying-data-as-an-alternative-to-failover"></a>Kopiera data som ett alternativ till redundans

Om ditt lagringskonto är konfigurerat för RA-GRS har du läsbehörighet till dina data med hjälp av den sekundära slutpunkten. Om du föredrar att inte växla över i händelse av ett avbrott i den primära regionen kan du använda verktyg som [AzCopy,](storage-use-azcopy.md) [Azure PowerShell](storage-powershell-guide-full.md)eller [Azure Data Movement-biblioteket](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/) för att kopiera data från ditt lagringskonto i den sekundära regionen till ett annat lagringskonto i en opåverkad region. Du kan sedan peka dina program på det lagringskontot för både läs- och skrivtillgänglighet.

> [!CAUTION]
> En felväxling för kontot bör inte användas som en del av din datamigreringsstrategi.


## <a name="microsoft-managed-failover"></a>Microsoft-hanterad redundans

Under extrema omständigheter där en region går förlorad på grund av en betydande katastrof kan Microsoft initiera en regional redundans. I det här fallet krävs ingen åtgärd från din sida. Tills den Microsoft-hanterade redundansen har slutförts har du inte skrivbehörighet till ditt lagringskonto. Dina program kan läsa från den sekundära regionen om ditt lagringskonto är konfigurerat för RA-GRS. 

## <a name="see-also"></a>Se även

- [Initiera en redundans för kontot (förhandsgranskning)](storage-initiate-account-failover.md)
- [Utforma högtillgängliga program med hjälp av RA GRS](storage-designing-ha-apps-with-ragrs.md)
- [Självstudiekurs: Skapa ett program med högtillgänge med Blob-lagring](../blobs/storage-create-geo-redundant-storage.md) 
