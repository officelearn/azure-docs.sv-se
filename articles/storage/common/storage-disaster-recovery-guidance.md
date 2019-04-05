---
title: Disaster recovery och storage-konto redundans (förhandsversion), Azure Storage
description: Azure Storage stöder konto redundans (förhandsversion) för ra-GRS-konton. Med kontot redundans kan du initiera redundansprocessen för ditt lagringskonto om den primära slutpunkten blir otillgänglig.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/25/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 87499c1b71e243fe976e436b525e0150689d3aa1
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2019
ms.locfileid: "59051197"
---
# <a name="disaster-recovery-and-storage-account-failover-preview-in-azure-storage"></a>Disaster recovery och storage-konto redundans (förhandsversion) i Azure Storage

Microsoft strävar efter att säkerställa att Azure-tjänster som alltid är tillgängliga. Oplanerade driftstopp kan dock uppstå. Om programmet kräver återhämtning, Microsoft rekommenderar att du använder geo-redundant lagring, så att dina data replikeras i en andra region. Kunder bör dessutom ha en katastrofåterställning planera för hantering av ett regionalt avbrott. En viktig del av en haveriberedskapsplan förbereds att växla över till den sekundära slutpunkten i händelse av att den primära slutpunkten blir otillgänglig. 

Azure Storage stöder konto redundans (förhandsversion) för ra-GRS-konton. Med kontot redundans kan du initiera redundansprocessen för ditt lagringskonto om den primära slutpunkten blir otillgänglig. Redundansen uppdaterar den sekundära slutpunkten om du vill bli den primära slutpunkten för ditt lagringskonto. När redundansväxlingen är klar, kan klienter börja skriva till den nya primära slutpunkten.

Den här artikeln beskrivs koncepten och med redundans för ett konto som ingår i processen och beskriver hur du förbereder ditt storage-konto med minsta möjliga påverkan för kunden. Läs hur du startar en konto-redundans i Azure portal eller PowerShell i [påbörja en växling (förhandsversion) för kontot](storage-initiate-account-failover.md).


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="choose-the-right-redundancy-option"></a>Välja rätt redundans

Alla lagringskonton replikeras för redundans. Vilka alternativ för dataredundans du väljer för ditt konto beror på graden av återhämtning som du behöver. Välj geo-redundant lagring, med eller utan alternativet läsbehörighet från den sekundära regionen för skydd mot regionala avbrott:  

**GEO-redundant lagring (GRS)** replikerar data asynkront i två geografiska områden som är minst hundratals mil ifrån varandra. Om den primära regionen drabbas av ett avbrott, fungerar den sekundära regionen som en redundant källa för dina data. Du kan påbörja en växling för att omvandla den sekundära slutpunkten till den primära slutpunkten.

**Läsåtkomst till geografiskt redundant lagring (RA-GRS)** tillhandahåller geo-redundant lagring med den ytterligare fördelen av läsbehörighet till den sekundära slutpunkten. Om ett avbrott uppstår i den primära slutpunkten kan program konfigurerade för RA-GRS och utformat för hög tillgänglighet fortsätta att läsa från den sekundära slutpunkten. Microsoft rekommenderar RA-GRS för maximal flexibilitet för dina program.

Andra redundansalternativ för Azure Storage är zonredundant lagring (ZRS), som replikerar data mellan tillgänglighetszoner i en enda region och lokalt redundant lagring (LRS), som replikerar dina data i ett datacenter i en region. Om ditt lagringskonto har konfigurerats för ZRS eller LRS, kan du konvertera detta konto för att använda GRS eller RA-GRS. Konfigurera ditt konto för geo-redundant lagring tillkommer ytterligare kostnader. Mer information finns i [Azure Storage-replikering](storage-redundancy.md).

> [!WARNING]
> GEO-redundant lagring innebär en risk för dataförlust. Data replikeras till den sekundära regionen asynkront, vilket innebär att det finns en fördröjning mellan när data som skrivs till den primära regionen skrivs till den sekundära regionen. Skriva åtgärder till den primära slutpunkten som ännu inte har replikerats till den sekundära slutpunkten går förlorade vid ett eventuellt strömavbrott. 

## <a name="design-for-high-availability"></a>Design för hög tillgänglighet

Det är viktigt att utforma ditt program för hög tillgänglighet från början. Se dessa Azure-resurser för vägledning i utforma ditt program och planera för katastrofåterställning:

* [Designa elastiska program för Azure](https://docs.microsoft.com/azure/architecture/resiliency/): En översikt över viktiga begrepp för att skapa program med hög tillgänglighet i Azure.
* [Checklista för tillgänglighet](https://docs.microsoft.com/azure/architecture/checklist/availability): En checklista för att verifiera att ditt program använder Metodtips för design för hög tillgänglighet.
* [Utforma högtillgängliga program med hjälp av RA-GRS](storage-designing-ha-apps-with-ragrs.md): Designriktlinjer för att bygga program för att dra nytta av RA-GRS.
* [Självstudier: Skapa ett program med hög tillgänglighet med Blob storage](../blobs/storage-create-geo-redundant-storage.md): En självstudiekurs som visar hur du skapar ett program med hög tillgänglighet som växlar automatiskt mellan slutpunkter som fel och återställningar simuleras. 

Dessutom vill ha i åtanke dessa bästa metoder för att upprätthålla hög tillgänglighet för dina Azure Storage-data:

* **Disks:** Använd [Azure Backup](https://azure.microsoft.com/services/backup/) för säkerhetskopiering av VM-diskar som används av virtuella datorer i Azure. Överväga att använda [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) att skydda dina virtuella datorer i händelse av ett regionalt haveri.
* **Blockblob-objekt:** Aktivera [mjuk borttagning](../blobs/storage-blob-soft-delete.md) att skydda mot på objektnivå borttagningar och skriver över eller kopiera blockblob-objekt till ett annat lagringskonto i en annan region med hjälp av [AzCopy](storage-use-azcopy.md), [Azure PowerShell ](storage-powershell-guide-full.md), eller [Azure Data Movement library](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/).
* **Filer:** Använd [AzCopy](storage-use-azcopy.md) eller [Azure PowerShell](storage-powershell-guide-full.md) att kopiera filer till ett annat lagringskonto i en annan region.
* **Tabeller:** använder [AzCopy](storage-use-azcopy.md) att exportera data från tabeller till ett annat lagringskonto i en annan region.

## <a name="track-outages"></a>Spåra avbrott

Kunder kan prenumerera på [Hälsoinstrumentpanelen för Azure](https://azure.microsoft.com/status/) att spåra hälsotillstånd och status för Azure Storage och andra Azure-tjänster.

Microsoft rekommenderar också att du utformar ditt program för att förbereda för risken för skrivfel. Ditt program ska exponera skrivfel på ett sätt som varnar dig möjligheten att ett avbrott i den primära regionen.

## <a name="understand-the-account-failover-process"></a>Förstå redundansprocessen konto

Kundhanterad konto redundans (förhandsversion) kan du redundansväxla ditt hela storage-konto till den sekundära regionen om den primära servern blir otillgänglig av någon anledning. När du framtvingar en redundansväxling till den sekundära regionen kan klienter börja skriva data till den sekundära slutpunkten när redundansen är klar. Redundansväxlingen tar normalt ungefär en timme.

### <a name="how-an-account-failover-works"></a>Så här fungerar en konto-redundans

En klient skriver data till ett Azure Storage-konto i den primära regionen under normala omständigheter, och dessa data replikeras asynkront till den sekundära regionen. Följande bild visar scenario när den primära regionen är tillgänglig:

![Klienter skriver data till lagringskontot i den primära regionen](media/storage-disaster-recovery-guidance/primary-available.png)

Om den primära slutpunkten blir otillgänglig av någon anledning, är klienten inte längre kunna skriva till lagringskontot. Följande bild visar scenario där primärt har blivit otillgänglig, men ingen återställning har genomförts ännu:

![Primärt inte är tillgänglig, så att klienter inte skriva data](media/storage-disaster-recovery-guidance/primary-unavailable-before-failover.png)

Kunden initierar konto redundans till den sekundära slutpunkten. Redundansprocessen uppdaterar DNS-posten som tillhandahålls av Azure Storage så att den sekundära slutpunkten blir den nya primära slutpunkten för ditt lagringskonto enligt följande bild:

![Kunden initierar konto redundans till en sekundär slutpunkt](media/storage-disaster-recovery-guidance/failover-to-secondary.png)

Skrivåtkomst återställs för GRS och RA-GRS-konton när DNS-posten har uppdaterats och begäranden dirigeras till den nya primära slutpunkten. Tjänstslutpunkter för befintliga lagring för blobbar, tabeller, köer och filer förblir desamma efter redundansen.

> [!IMPORTANT]
> När redundansväxlingen är klar, är storage-konto konfigurerad för att vara lokalt redundant i den nya primära slutpunkten. Konfigurera kontot för att använda geo-redundant lagring igen (RA-GRS eller GRS) om du vill återuppta replikeringen till den nya sekundärt.
>
> Tänk på att konvertera ett LRS-konto till RA-GRS- eller GRS tillkommer en kostnad. Den här kostnaden gäller för uppdatering av lagringskontot i den nya primära regionen du använder RA-GRS- eller GRS efter en redundansväxling.  

### <a name="anticipate-data-loss"></a>Förutse dataförlust

> [!CAUTION]
> En konto-redundans är oftast att data kan gå förlorade. Det är viktigt att du förstår följderna av att initiera en konto-redundans.  

Eftersom data skrivs asynkront från den primära regionen till den sekundära regionen, finns det alltid en fördröjning innan en skrivning till den primära regionen har replikerats till den sekundära regionen. Om den primära regionen blir otillgänglig, kanske senaste skrivningar inte ännu har replikerats till den sekundära regionen.

När du framtvingar en redundansväxling, förloras alla data i den primära regionen när den sekundära regionen blir den nya primära regionen och storage-konto är konfigurerad för att vara lokalt redundant. Alla data som redan replikeras till sekundärt bevaras när redundansväxlingen sker. Dock går alla data skrivs till den primära också inte har replikerats till sekundärt förlorade permanent. 

Den **senaste synkroniseringstid** egenskapen anger de senaste tidpunkt att data från den primära regionen kommer att ha skrivits till den sekundära regionen. Alla data som skrivs före den senaste synkronisering är tillgänglig på sekundärt, samtidigt som data som skrivs efter den senaste synkronisering inte kanske har skrivits till sekundärt och kan gå förlorade. Använd den här egenskapen vid ett eventuellt strömavbrott för att uppskatta mängden dataförlust som kan tillkomma genom att initiera en konto-redundans. 

Ett bra tips är att designa programmet så att du kan använda den senaste synkronisering för att utvärdera förväntade data går förlorade. Exempel: Om du loggar alla skrivåtgärder, och sedan kan du jämföra tidpunkten för din senaste skrivåtgärder till den senaste synkronisera tid för att fastställa vilka skrivningar har inte synkroniserats till sekundärt.

### <a name="use-caution-when-failing-back-to-the-original-primary"></a>Var försiktig när du växla tillbaka till den ursprungliga primärt

När du redundansväxlar från primärt till den sekundära regionen, är ditt lagringskonto konfigurerad för att vara lokalt redundant i den nya primära regionen. Du kan konfigurera kontot för geo-redundans igen genom att uppdatera den om du vill använda GRS eller RA-GRS. När kontot har konfigurerats för geo-redundans igen efter en redundansväxling, genast den nya primära regionen replikera data till den nya sekundära regionen, som var primärt innan den ursprungliga redundansen. Det kan dock ta en viss tidsperiod innan befintliga data i primärt helt har replikerats till den nya sekundärt.

När lagringskontot har konfigurerats om för geo-redundans, är det möjligt att påbörja en annan växling från den nya primära tillbaka till den nya sekundärt. I det här fallet den ursprungliga primära regionen innan redundansen blir den primära regionen igen och är konfigurerad för att vara lokalt redundant. Alla data i den primära regionen för postredundant (den ursprungliga sekundärt) är sedan förlorat. Om de flesta av data i lagringskontot inte har replikerats till den nya sekundärt innan du återställer, kan du bli sämre dataförluster. 

För att undvika dataförluster, kontrollera värdet för den **senaste synkroniseringstid** egenskapen innan du växlar tillbaka. Jämför den senaste synkronisering till den senaste tider dessa data skrevs till den nya primärt att utvärdera förväntade data går förlorade. 

## <a name="initiate-an-account-failover"></a>Initiera en kontoredundans

Du kan initiera en konto-redundans från Azure portal, PowerShell, Azure CLI eller Azure Storage resource provider API. Läs mer om hur du påbörja en växling [påbörja en växling (förhandsversion) för kontot](storage-initiate-account-failover.md).

## <a name="about-the-preview"></a>Om förhandsversionen av

konto-redundans är tillgänglig som förhandsversion för alla kunder som använder GRS eller RA-GRS med Azure Resource Manager-distributioner. General-Purpose v1, gpv2 och Blob storage-kontotyper stöds. konto-redundans är nu tillgänglig i dessa regioner:

- USA, västra 2
- USA, västra centrala

Förhandsversionen är endast avsedd för icke-produktion användning. Produktion servicenivåavtal (SLA) är inte tillgängliga.

### <a name="register-for-the-preview"></a>Registrera dig för förhandsversionen

Registrera dig för förhandsversionen, kör du följande kommandon i PowerShell. Se till att ersätta platshållarna inom hakparentes med ditt eget prenumerations-ID:

```powershell
Connect-AzAccount -SubscriptionId <subscription-id>
Register-AzProviderFeature -FeatureName CustomerControlledFailover -ProviderNamespace Microsoft.Storage
```

Det kan ta 1 – 2 dagar som fått godkännande för förhandsversionen. Verifiera att registreringen har godkänts genom att köra följande kommando:

```powershell
Get-AzProviderFeature -FeatureName CustomerControlledFailover -ProviderNamespace Microsoft.Storage
```

### <a name="additional-considerations"></a>Annat som är bra att tänka på 

Gå igenom ytterligare överväganden som beskrivs i det här avsnittet för att förstå hur dina program och tjänster kan påverkas när du framtvingar en redundansväxling för förhandsversionen.

#### <a name="azure-virtual-machines"></a>Virtuella Azure-datorer

Azure-datorer (VM) växlar inte över som en del av en konto-redundans. Om du växlar över till den sekundära regionen när den primära regionen blir otillgänglig, måste du återskapa alla virtuella datorer efter redundansen. 

#### <a name="azure-unmanaged-disks"></a>Azure ohanterade diskar

Som bästa praxis rekommenderar Microsoft konvertering ohanterade diskar till hanterade diskar. Om du vill växla över ett konto som innehåller ohanterade diskar är anslutna till virtuella Azure-datorer behöver du dock att stänga av den virtuella datorn innan du utför redundans.

Ohanterade diskar lagras som sidblobar i Azure Storage. När en virtuell dator körs i Azure, lånar ut eventuella ohanterade diskar som är anslutna till den virtuella datorn. En konto-redundans kan inte fortsätta om det finns ett lån vid en blob. Följ dessa steg för att utföra redundansväxlingen:

1. Anteckna namnen på eventuella ohanterade diskar, deras logiska enhetsnummer (LUN) och den virtuella datorn som de är anslutna innan du börjar. Detta gör det enklare att ansluta diskarna efter redundansen. 
2. Stäng av den virtuella datorn.
3. Ta bort den virtuella datorn, men behåll VHD-filer för ohanterade diskar. Observera den tid då du tog bort den virtuella datorn.
4. Vänta tills den **senaste synkroniseringstid** har uppdaterats och är senare än den tid då du tog bort den virtuella datorn. Det här steget är viktigt, eftersom om den sekundära slutpunkten inte har uppdaterats helt med VHD-filerna när redundansväxlingen sker, sedan den virtuella datorn inte kanske fungerar korrekt i den nya primära regionen.
5. Initiera växling vid fel för kontot.
6. Vänta tills kontot redundansväxlingen är klar och den sekundära regionen har blivit den nya primära regionen.
7. Skapa en virtuell dator i den nya primära regionen och ansluta de virtuella hårddiskarna.
8. Starta den nya virtuella datorn.

Tänk på att alla data som lagras i en tillfällig disk förloras när Virtuellt datorn stängs av.

### <a name="unsupported-features-or-services"></a>Funktioner som inte stöds eller tjänster
Följande funktioner eller tjänster stöds inte för kontot redundans i förhandsversionen:

- Azure File Sync har inte stöd för växling vid fel för storage-konto. Storage-konton som innehåller Azure-filresurser som används som molnslutpunkter i Azure File Sync bör inte att redundansväxla. Synkronisering av orsaken till slutar fungera och kan också gör oväntade data går förlorade vid nyligen nivåindelade filer.  
- Lagringskonton med hjälp av Azure Data Lake Storage Gen2 hierarkiskt namnområde redundansväxlas inte.
- Ett lagringskonto som innehåller arkiverade blobbar redundansväxlas inte. Underhålla arkiverade blobar i ett separat lagringskonto som du inte planerar att redundansväxla.
- Ett lagringskonto som innehåller premium blockblob-objekt kan inte växlas. Storage-konton som har stöd för premium blockblobar stöder för närvarande inte geo-redundans.
- När redundansväxlingen är klar kan följande funktioner att sluta fungera om ursprungligen aktiverat: [Händelseprenumerationer](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-overview), [livscykel principer](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts), [Storage Analytics loggning](https://docs.microsoft.com/rest/api/storageservices/about-storage-analytics-logging).

## <a name="copying-data-as-an-alternative-to-failover"></a>Kopiering av data som ett alternativ till redundans

Om ditt lagringskonto har konfigurerats för RA-GRS, sedan har du läsbehörighet till dina data med hjälp av den sekundära slutpunkten. Om du vill inte att växla över vid ett eventuellt strömavbrott i den primära regionen kan du använda verktyg som [AzCopy](storage-use-azcopy.md), [Azure PowerShell](storage-powershell-guide-full.md), eller [Azure Data Movement library](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/) till Kopiera data från ditt lagringskonto i den sekundära regionen till ett annat lagringskonto i en region som påverkas inte. Du kan peka dina program till det lagringskontot för både läsning och skrivtillgänglighet.

## <a name="microsoft-managed-failover"></a>Microsoft-hanterade redundans

Microsoft kan initiera en regional redundans i extrema fall där en region går förlorad på grund av en betydande katastrof. I så fall krävs ingen åtgärd från din sida. Tills Microsoft-hanterade redundans har slutförts kan du inte skrivåtkomst till ditt lagringskonto. Dina program kan läsa från den sekundära regionen om ditt lagringskonto har konfigurerats för RA-GRS. 

## <a name="see-also"></a>Se också

* [Påbörja en växling för kontot (förhandsversion)](storage-initiate-account-failover.md)
* [Utforma högtillgängliga program med hjälp av RA-GRS](storage-designing-ha-apps-with-ragrs.md)
* [Självstudier: Skapa ett program med hög tillgänglighet med Blob-lagring](../blobs/storage-create-geo-redundant-storage.md) 
