---
title: Vad är StorSimple Snapshot Manager? | Microsoft Docs
description: Beskriver StorSimple Snapshot Manager, dess arkitektur och dess funktioner.
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: ''
ms.assetid: 6094c31e-e2d9-4592-8a15-76bdcf60a754
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: v-sharos
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 38c197c7bc57110b29b1d8cb789d5b7310823da2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23876833"
---
# <a name="an-introduction-to-storsimple-snapshot-manager"></a>En introduktion till StorSimple Snapshot Manager

## <a name="overview"></a>Översikt
StorSimple Snapshot Manager är en snapin-modul i Microsoft Management Console (MMC) som förenklar dataskydd och hantering av säkerhetskopiering i en miljö med Microsoft Azure StorSimple. Med StorSimple Snapshot Manager hantera Microsoft Azure StorSimple-data i datacentret och i molnet som en enda integrerad lagringslösning därmed förenkla processer för säkerhetskopiering och minska kostnaderna.

Den här översikten beskriver StorSimple Snapshot Manager, beskriver funktioner och förklarar sin roll i Microsoft Azure StorSimple. 

En översikt över hela Microsoft Azure StorSimple systemet, inklusive StorSimple-enhet, StorSimple Manager-tjänst, StorSimple Snapshot Manager och StorSimple nätverkskort för SharePoint, se [StorSimple 8000-serien: en hybridlagringslösning för molnet](storsimple-overview.md). 

> [!NOTE]
> * Du kan inte använda StorSimple Snapshot Manager för att hantera Microsoft Azure StorSimple virtuell matriser (även kallat StorSimple lokala virtuella enheter).
> * Om du planerar att installera StorSimple uppdatering 2 på din StorSimple-enhet, måste du hämta den senaste versionen av StorSimple Snapshot Manager och installera den **innan du installerar StorSimple uppdatering 2**. Den senaste versionen av StorSimple Snapshot Manager är bakåtkompatibla och fungerar med alla versioner av Microsoft Azure StorSimple. Om du använder den tidigare versionen av StorSimple Snapshot Manager behöver du uppdatera den (du inte behöver avinstallera den tidigare versionen innan du installerar den nya versionen).
> 
> 

## <a name="storsimple-snapshot-manager-purpose-and-architecture"></a>StorSimple Snapshot Manager syfte och arkitektur
StorSimple Snapshot Manager ger en central hanteringskonsol som du kan använda för att skapa enhetliga, point-in-time-säkerhetskopior av lokala och molndata. Du kan till exempel använda konsolen för att:

* Konfigurera, säkerhetskopiera och ta bort volymer.
* Konfigurera volym grupper så säkerhetskopierade data är programkonsekventa.
* Hantera principer för säkerhetskopiering så att data säkerhetskopieras enligt ett förutbestämt schema.
* Skapa lokala och molnbaserade ögonblicksbilder som kan lagras i molnet och används för katastrofåterställning.

StorSimple Snapshot Manager hämtar listan över program som är registrerade med VSS-provider på värden. Om du vill skapa programkonsekventa säkerhetskopior kontrollerar de volymer som används av ett program och föreslår volym grupper om du vill konfigurera. StorSimple Snapshot Manager använder grupperna volym för att generera säkerhetskopior som är programkonsekventa. (Programkonsekvens finns när alla relaterade filer och databaser som ska synkroniseras och representerar true tillståndet för programmet vid en viss tidpunkt.) 

StorSimple Snapshot Manager säkerhetskopieringar utgöras av inkrementell ögonblicksbilder som avbildar bara ändringarna sedan den senaste säkerhetskopieringen. Därför kan säkerhetskopior kräver mindre lagringsutrymme och skapas och återställs snabbt. StorSimple Snapshot Manager använder Windows Volume Shadow Copy Service (VSS) för att säkerställa att ögonblicksbilder avbilda programkonsekvent data. (Mer information går du till integrering med Windows Volume Shadow Copy Service-avsnitt.) Med StorSimple Snapshot Manager som du kan skapa scheman för säkerhetskopiering eller vidta omedelbara säkerhetskopieringar efter behov. Om du behöver återställa data från en säkerhetskopiering, StorSimple Snapshot Manager kan välja du från en katalog i lokala eller molnögonblicksbilder. Azure StorSimple återställer endast de data som krävs när det behövs, vilket förhindrar fördröjningar i datatillgänglighet under återställning.)

![Arkitektur för StorSimple Snapshot Manager](./media/storsimple-what-is-snapshot-manager/HCS_SSM_Overview.png)

**Arkitektur för StorSimple Snapshot Manager** 

## <a name="support-for-multiple-volume-types"></a>Stöd för flera volymtyper av
Du kan använda StorSimple Snapshot Manager för att konfigurera och säkerhetskopiera följande typer av volymer: 

* **Enkla volymer** – en enkel volym är en enskild partition på en standarddisk. 
* **Enkla volymer** – en enkel volym är en dynamisk volym som innehåller diskutrymmet från en enda dynamisk disk. En enkel volym består av en enda region på en disk eller flera regioner som är länkade på samma disk. (Du kan skapa enkla volymer på dynamiska diskar.) Enkla volymer är inte feltoleranta.
* **Dynamiska volymer** – en dynamisk volym är en volym som har skapats på en dynamisk disk. Dynamiska diskar använder en databas för att spåra information om volymer som finns på dynamiska diskar på en dator. 
* **Dynamiska volymer med spegling** – dynamiska volymer med spegling bygger på RAID 1-arkitekturen. Med RAID 1 skrivs identiska data på två eller flera disk, producerar en speglad uppsättning. En läsbegäran kan sedan hanteras av en disk som innehåller data som begärdes.
* **Klusterdelade volymer** – med klusterdelade volymer (CSV), flera noder i ett failover-kluster kan samtidigt läsa eller skriva till samma disk. Växling vid fel från en nod till en annan nod kan inträffa snabbt, utan en ändring i äganderätt till enhet eller montera demontera och ta bort en volym. 

> [!IMPORTANT]
> Blanda inte CSV: er och icke-CSV: er i samma ögonblicksbilden. Blandning av CSV: er och icke-CSV: er i en ögonblicksbild stöds inte. 
> 
> 

Du kan använda StorSimple Snapshot Manager för att återställa hela volymen grupper eller klona enskilda volymer och återställa enskilda filer.

* [Volymer och volymen grupper](#volumes-and-volume-groups) 
* [Typer av säkerhetskopiering och principer för säkerhetskopiering](#backup-types-and-backup-policies) 

Mer information om funktioner för StorSimple Snapshot Manager och hur de används finns [StorSimple Snapshot Manager användargränssnittet](storsimple-use-snapshot-manager.md).

## <a name="volumes-and-volume-groups"></a>Volymer och volymen grupper
Med StorSimple Snapshot Manager som du skapar volymer och konfigurera dem i grupper på volymen. 

StorSimple Snapshot Manager använder volymen grupper för att skapa säkerhetskopior som är programkonsekventa. Det finns programkonsekvens när alla relaterade filer och databaser som ska synkroniseras och representerar status för ett program vid en viss tidpunkt. Volymen grupper (som kallas även *konsekvenskontroll grupper*) utgör grunden för en säkerhetskopia eller återställa jobb.

Volymen grupper är inte samma som volymbehållare. En volymbehållare innehåller en eller flera volymer som delar ett lagringskonto i molnet och andra attribut, till exempel kryptering och bandbredd. En enda volymbehållare kan innehålla upp till 256 tunt allokerade StorSimple-volymer. Mer information om volymbehållare går du till [hantera din volymbehållare](storsimple-manage-volume-containers.md). Volymen grupper är samlingar av volymer som du konfigurerar för att underlätta säkerhetskopieringsåtgärder. Om du markerar två volymer som tillhör annan volymbehållare, placerar dem på en enda volym-grupp och sedan skapa en princip för säkerhetskopiering för den volym gruppen säkerhetskopieras varje volym i behållaren lämplig volym med hjälp av lämplig storage-konto.

> [!NOTE]
> Alla volymer i en volym-grupp måste komma från en enda molntjänstleverantören.
> 
> 

## <a name="integration-with-windows-volume-shadow-copy-service"></a>Integrering med Windows Volume Shadow Copy-tjänsten
StorSimple Snapshot Manager använder Windows Volume Shadow Copy Service (VSS) för att samla in programkonsekvent data. VSS underlättar programkonsekvens genom att kommunicera med VSS-medvetna program att samordna skapandet av inkrementell ögonblicksbilder. VSS säkerställer att programmen är tillfälligt inaktiv eller overksamt, när ögonblicksbilder tas. 

Implementeringen av VSS StorSimple Snapshot Manager fungerar med SQL Server och allmänna NTFS-volymer. Processen är följande: 

1. En begärande som är vanligtvis en datahantering och lösning för skydd av (till exempel StorSimple Snapshot Manager) eller ett program för säkerhetskopiering, anropar VSS och ber att samla in information från skrivaren programvaran i målprogrammet.
2. VSS kontaktar writer komponenten om du vill hämta en beskrivning av data. Skrivaren returnerar beskrivning av de data som ska säkerhetskopieras. 
3. VSS signalerar skrivaren för att förbereda program för säkerhetskopiering. Skrivaren förbereder data för säkerhetskopiering genom att öppna transaktioner uppdaterar transaktionsloggar och så vidare och meddelar VSS.
4. VSS instruerar skrivaren för att tillfälligt stoppa programmets datalager och se till att inga data skrivs till volymen medan ögonblicksbilden skapas. Det här steget säkerställer datakonsekvens och tar mer än 60 sekunder.
5. VSS instruerar leverantör för att skapa en skuggkopia. Providers, vilket kan vara program - eller maskinvarubaserade, hantera de volymer som körs för tillfället och skapa skuggkopior av dem på begäran. Providern skapar skuggkopian och meddelar VSS när den har slutförts.
6. VSS kontaktar skrivaren att meddela det program som kan återuppta i/o och bekräfta att i/o paus under skapandet av skuggkopian. 
7. Om kopian lyckades returnerar VSS kopians plats till begäranden. 
8. Om data skrevs när skuggkopian skapats, kommer säkerhetskopian vara inkonsekvent. VSS tar bort skuggkopian och meddelar begäranden. Begäranden kan upprepa processen för säkerhetskopiering automatiskt eller meddela administratören ska kunna köra det igen vid ett senare tillfälle.

Se följande illustration.

![VSS-processen](./media/storsimple-what-is-snapshot-manager/HCS_SSM_VSS_process.png)

**Tjänsten Windows Volume Shadow Copy-processen** 

## <a name="backup-types-and-backup-policies"></a>Typer av säkerhetskopiering och principer för säkerhetskopiering
Med StorSimple Snapshot Manager kan du säkerhetskopiera data och lagrar den lokalt och i molnet. Du kan använda StorSimple Snapshot Manager för att säkerhetskopiera data direkt eller du kan använda en princip för säkerhetskopiering för att skapa ett schema för säkerhetskopiering automatiskt. Principer för säkerhetskopiering kan du ange hur många ögonblicksbilder ska behållas. 

### <a name="backup-types"></a>Typer av säkerhetskopiering
Du kan använda StorSimple Snapshot Manager för att skapa följande typer av säkerhetskopiering:

* **Lokala ögonblicksbilder** – lokala ögonblicksbilder är i tidpunkt kopior av volymens data som lagras på StorSimple-enheten. Normalt kan den här typen av säkerhetskopiering skapas och återställs snabbt. Du kan använda en lokal ögonblicksbild som en lokal säkerhetskopia.
* **Molnbaserade ögonblicksbilder** – molnögonblicksbilder är i tidpunkt kopior av volymens data som lagras i molnet. En ögonblicksbild i molnet är detsamma som en ögonblicksbild replikeras på ett annat, externt lagringssystem. Molnögonblicksbilder är särskilt användbart i katastrofåterställning.

### <a name="on-demand-and-scheduled-backups"></a>På begäran och schemalagda säkerhetskopieringar
Med StorSimple Snapshot Manager kan du initiera en enstaka säkerhetskopia skapas direkt eller du kan använda en princip för säkerhetskopiering för att schemalägga återkommande säkerhetskopieringsåtgärder.

En princip för säkerhetskopiering är en uppsättning automatiska regler som du kan använda för att schemalägga regelbundna säkerhetskopieringar. En princip för säkerhetskopiering kan du definiera frekvens och parametrar för generering av ögonblicksbilder av en viss volym-grupp. Du kan använda principer för att ange start-och förfallodatum, tider, frekvenser och krav på datalagring, för både lokala och molnbaserade ögonblicksbilder. En princip tillämpas omedelbart när du har definierat den. 

Du kan använda StorSimple Snapshot Manager för att konfigurera eller ändra principer för säkerhetskopiering när så krävs. 

Du kan konfigurera följande information för varje princip för säkerhetskopiering som du skapar:

* **Namnet** – det unika namnet för den valda säkerhetskopieringsprincipen.
* **Typen** – typ av princip för säkerhetskopiering, antingen lokal ögonblicksbild eller ögonblicksbild i molnet.
* **Volymen grupp** – gruppen volym som den markerade principen för säkerhetskopiering har tilldelats.
* **Kvarhållning** – antal säkerhetskopior ska bevaras. Om du markerar den **alla** rutan alla säkerhetskopior bevaras tills det maximala antalet säkerhetskopior per volym har uppnåtts, då principen misslyckas och genererar ett felmeddelande. Alternativt kan du ange ett antal säkerhetskopior att behålla (mellan 1 och 64).
* **Datum** – det datum då principen för säkerhetskopiering skapades.

Information om hur du konfigurerar principer för säkerhetskopiering, gå till [Använd StorSimple Snapshot Manager för att skapa och hantera principer för säkerhetskopiering](storsimple-snapshot-manager-manage-backup-policies.md).

### <a name="backup-job-monitoring-and-management"></a>Säkerhetskopieringsjobbet övervakning och hantering
Du kan använda StorSimple Snapshot Manager för att övervaka och hantera kommande planerade och slutförda säkerhetskopieringsjobb. Dessutom ger StorSimple Snapshot Manager en katalog med upp till 64 slutförda säkerhetskopieringar. Du kan använda katalogen för att hitta och återställa volymer eller enskilda filer. 

Information om övervakning säkerhetskopieringsjobb, gå till [Använd StorSimple Snapshot Manager för att visa och hantera säkerhetskopieringsjobb](storsimple-snapshot-manager-manage-backup-jobs.md).

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [använder StorSimple Snapshot Manager för att administrera din StorSimple-lösning](storsimple-snapshot-manager-admin.md).
* Hämta [StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220).

