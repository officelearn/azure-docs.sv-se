---
title: Vad är StorSimple Snapshot Manager? | Microsoft Docs
description: Beskriver StorSimple Snapshot Manager, dess arkitektur och dess funktioner.
services: storsimple
documentationcenter: NA
author: twooley
manager: timlt
editor: ''
ms.assetid: 6094c31e-e2d9-4592-8a15-76bdcf60a754
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: twooley
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e58e2d929dd1e4db16ce495ad54045e9dc3a6fb1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267422"
---
# <a name="an-introduction-to-storsimple-snapshot-manager"></a>En introduktion till StorSimple Snapshot Manager

## <a name="overview"></a>Översikt
StorSimple Snapshot Manager är en MMC-snapin-modul (Microsoft Management Console) som förenklar dataskydd och säkerhetskopieringshantering i en Microsoft Azure StorSimple-miljö. Med StorSimple Snapshot Manager kan du hantera Microsoft Azure StorSimple-data i datacentret och i molnet som en enda integrerad lagringslösning, vilket förenklar säkerhetskopieringsprocesser och minskar kostnaderna.

Den här översikten introducerar StorSimple Snapshot Manager, beskriver dess funktioner och förklarar dess roll i Microsoft Azure StorSimple. 

En översikt över hela Microsoft Azure StorSimple-systemet, inklusive StorSimple-enheten, StorSimple Manager-tjänsten, StorSimple Snapshot Manager och StorSimple-adaptern för SharePoint, finns i [StorSimple 8000-serien: en hybridmolnlagringslösning](storsimple-overview.md). 

> [!NOTE]
> * Du kan inte använda StorSimple Snapshot Manager för att hantera Microsoft Azure StorSimple Virtual Arrays (kallas även StorSimple lokala virtuella enheter).
> * Om du planerar att installera StorSimple Update 2 på din StorSimple-enhet måste du hämta den senaste versionen av StorSimple Snapshot Manager och installera den **innan du installerar StorSimple Update 2**. Den senaste versionen av StorSimple Snapshot Manager är bakåtkompatibel och fungerar med alla utgivna versioner av Microsoft Azure StorSimple. Om du använder den tidigare versionen av StorSimple Snapshot Manager måste du uppdatera den (du behöver inte avinstallera den tidigare versionen innan du installerar den nya versionen).
> 
> 

## <a name="storsimple-snapshot-manager-purpose-and-architecture"></a>Syfte och arkitektur för StorSimple Snapshot Manager
StorSimple Snapshot Manager tillhandahåller en central hanteringskonsol som du kan använda för att skapa konsekventa, punkt-i-tid-säkerhetskopior av lokala data och molndata. Du kan till exempel använda konsolen för att:

* Konfigurera, säkerhetskopiera och ta bort volymer.
* Konfigurera volymgrupper för att säkerställa att säkerhetskopierade data är programkonsekvent.
* Hantera principer för säkerhetskopiering så att data säkerhetskopieras enligt ett förutbestämt schema.
* Skapa lokala ögonblicksbilder och molnögonblicksbilder, som kan lagras i molnet och användas för haveriberedskap.

StorSimple Snapshot Manager hämtar listan över program som är registrerade hos VSS-providern på värden. För att skapa programkonsekventa säkerhetskopior kontrollerar den sedan de volymer som används av ett program och föreslår att volymgrupper konfigureras. StorSimple Snapshot Manager använder dessa volymgrupper för att generera säkerhetskopior som är programkonsekventa. (Programkonsekvens finns när alla relaterade filer och databaser synkroniseras och representerar programmets verkliga tillstånd vid en viss tidpunkt.) 

Säkerhetskopior av StorSimple Snapshot Manager har formen av inkrementella ögonblicksbilder, som bara fångar ändringarna sedan den senaste säkerhetskopieringen. Därför kräver säkerhetskopior mindre lagringsutrymme och kan skapas och återställas snabbt. StorSimple Snapshot Manager använder VSS (Windows Volume Shadow Copy Service) för att säkerställa att ögonblicksbilder samlar in programkonsekventa data. (Mer information finns i avsnittet Integration med Windows Volume Shadow Copy Service.) Med StorSimple Snapshot Manager kan du skapa säkerhetskopieringsscheman eller vidta omedelbara säkerhetskopior efter behov. Om du behöver återställa data från en säkerhetskopia kan du med StorSimple Snapshot Manager välja från en katalog med lokala ögonblicksbilder eller ögonblicksbilder i molnet. Azure StorSimple återställer endast de data som behövs när det behövs, vilket förhindrar fördröjningar i datatillgänglighet under återställningsåtgärder.)

![Arkitekturen StorSimple Snapshot Manager](./media/storsimple-what-is-snapshot-manager/HCS_SSM_Overview.png)

**Arkitekturen StorSimple Snapshot Manager** 

## <a name="support-for-multiple-volume-types"></a>Stöd för flera volymtyper
Du kan använda StorSimple Snapshot Manager för att konfigurera och säkerhetskopiera följande typer av volymer: 

* **Grundläggande volymer** – En grundläggande volym är en enda partition på en enkel disk. 
* **Enkla volymer** – En enkel volym är en dynamisk volym som innehåller diskutrymme från en enda dynamisk disk. En enkel volym består av en enda region på en disk eller flera regioner som är sammanlänkade på samma disk. (Du kan bara skapa enkla volymer på dynamiska diskar.) Enkla volymer är inte feltoleranta.
* **Dynamiska volymer** – En dynamisk volym är en volym som skapas på en dynamisk disk. Dynamiska diskar använder en databas för att spåra information om volymer som finns på dynamiska diskar i en dator. 
* **Dynamiska volymer med spegling** – Dynamiska volymer med spegling bygger på RAID 1-arkitekturen. Med RAID 1 skrivs identiska data på två eller flera diskar, vilket ger en speglad uppsättning. En läsbegäran kan sedan hanteras av alla diskar som innehåller de begärda uppgifterna.
* **Klusterdelade volymer** – Med klusterdelade volymer (CSV) kan flera noder i ett redundanskluster samtidigt läsa eller skriva till samma disk. Redundans från en nod till en annan nod kan ske snabbt, utan att kräva en förändring i enhetens ägarskap eller montering, demontering och ta bort en volym. 

> [!IMPORTANT]
> Blanda inte CSV:er och icke-CSV:er i samma ögonblicksbild. Det går inte att blanda CSV:er och icke-CSV:er i en ögonblicksbild. 
> 
> 

Du kan använda StorSimple Snapshot Manager för att återställa hela volymgrupper eller klona enskilda volymer och återställa enskilda filer.

* [Volymer och volymgrupper](#volumes-and-volume-groups) 
* [Principer för säkerhetskopiering och säkerhetskopiering](#backup-types-and-backup-policies) 

Mer information om StorSimple Snapshot Manager-funktioner och hur du använder dem finns i [StorSimple Snapshot Manager användargränssnitt](storsimple-use-snapshot-manager.md).

## <a name="volumes-and-volume-groups"></a>Volymer och volymgrupper
Med StorSimple Snapshot Manager skapar du volymer och konfigurerar dem sedan i volymgrupper. 

StorSimple Snapshot Manager använder volymgrupper för att skapa säkerhetskopior som är programkonsekventa. Programmets konsekvens finns när alla relaterade filer och databaser synkroniseras och representerar det verkliga tillståndet för ett program vid en viss tidpunkt. Volymgrupper (som också kallas *konsekvensgrupper)* utgör grunden för ett säkerhetskopierings- eller återställningsjobb.

Volymgrupper är inte samma sak som volymbehållare. En volymbehållare innehåller en eller flera volymer som delar ett molnlagringskonto och andra attribut, till exempel kryptering och bandbreddsförbrukning. En enda volymbehållare kan innehålla upp till 256 tunt etablerade StorSimple-volymer. Mer information om volymbehållare finns i [Hantera volymbehållare](storsimple-manage-volume-containers.md). Volymgrupper är samlingar av volymer som du konfigurerar för att underlätta säkerhetskopieringsåtgärder. Om du väljer två volymer som tillhör olika volymbehållare placerar du dem i en enda volymgrupp och sedan skapar en princip för säkerhetskopiering för den volymgruppen, säkerhetskopieras varje volym i lämplig volymbehållare med rätt lagringskonto.

> [!NOTE]
> Alla volymer i en volymgrupp måste komma från en enda molntjänstleverantör.
> 
> 

## <a name="integration-with-windows-volume-shadow-copy-service"></a>Integrering med Windows Volume Shadow Copy Service
StorSimple Snapshot Manager använder VSS (Windows Volume Shadow Copy Service) för att samla in programkonsekventa data. VSS underlättar programmets konsekvens genom att kommunicera med VSS-medvetna program för att samordna skapandet av inkrementella ögonblicksbilder. VSS säkerställer att programmen är tillfälligt inaktiva, eller quiescent, när ögonblicksbilder tas. 

StorSimple Snapshot Manager-implementeringen av VSS fungerar med SQL Server och generiska NTFS-volymer. Processen är följande: 

1. En beställare, som vanligtvis är en lösning för datahantering och skydd (till exempel StorSimple Snapshot Manager) eller ett säkerhetskopieringsprogram, anropar VSS och ber den att samla in information från skribentens programvara i målprogrammet.
2. VSS kontaktar skrivkomponenten för att hämta en beskrivning av data. Författaren returnerar beskrivningen av de data som ska säkerhetskopieras. 
3. VSS signalerar författaren att förbereda programmet för säkerhetskopiering. Författaren förbereder data för säkerhetskopiering genom att slutföra öppna transaktioner, uppdatera transaktionsloggar och så vidare och sedan meddelar VSS.
4. VSS instruerar författaren att tillfälligt stoppa programmets datalager och se till att inga data skrivs till volymen medan skuggkopian skapas. Det här steget säkerställer datakonsekvens och tar inte mer än 60 sekunder.
5. VSS instruerar providern att skapa skuggkopian. Leverantörer, som kan vara programvaru- eller maskinvarubaserade, hanterar de volymer som körs och skapar skuggkopior av dem på begäran. Providern skapar skuggkopian och meddelar VSS när den är klar.
6. VSS kontaktar författaren för att meddela programmet att I/O kan återupptas och även för att bekräfta att I/O har pausats under skapandet av skuggkopiering. 
7. Om kopian lyckades returneras kopians plats till beställaren. 
8. Om data skrevs medan skuggkopian skapades blir säkerhetskopian inkonsekvent. VSS tar bort skuggkopian och meddelar beställaren. Beställaren kan antingen upprepa säkerhetskopieringen automatiskt eller meddela administratören att försöka igen vid ett senare tillfälle.

Se följande bild.

![VSS-process](./media/storsimple-what-is-snapshot-manager/HCS_SSM_VSS_process.png)

**Serviceprocess för skuggkopiering av Windows-volym** 

## <a name="backup-types-and-backup-policies"></a>Principer för säkerhetskopiering och säkerhetskopiering
Med StorSimple Snapshot Manager kan du säkerhetskopiera data och lagra dem lokalt och i molnet. Du kan använda StorSimple Snapshot Manager för att säkerhetskopiera data direkt, eller så kan du använda en princip för säkerhetskopiering för att skapa ett schema för att göra säkerhetskopior automatiskt. Med principer för säkerhetskopiering kan du också ange hur många ögonblicksbilder som ska behållas. 

### <a name="backup-types"></a>Typer av säkerhetskopiering
Du kan använda StorSimple Snapshot Manager för att skapa följande typer av säkerhetskopior:

* **Lokala ögonblicksbilder** – Lokala ögonblicksbilder är punkt-i-tid-kopior av volymdata som lagras på StorSimple-enheten. Vanligtvis kan den här typen av säkerhetskopiering skapas och återställas snabbt. Du kan använda en lokal ögonblicksbild på samma sätt som en lokal säkerhetskopia.
* **Ögonblicksbilder av molnet** – Ögonblicksbilder i molnet är punkt-i-tid-kopior av volymdata som lagras i molnet. En ögonblicksbild i molnet motsvarar en ögonblicksbild som replikeras på ett annat lagringssystem utanför platsen. Ögonblicksbilder av molnet är särskilt användbara i katastrofåterställningsscenarier.

### <a name="on-demand-and-scheduled-backups"></a>På begäran och schemalagda säkerhetskopieringar
Med StorSimple Snapshot Manager kan du initiera en engångskopia som ska skapas omedelbart, eller så kan du använda en principer för säkerhetskopiering för att schemalägga återkommande säkerhetskopieringsåtgärder.

En princip för säkerhetskopiering är en uppsättning automatiska regler som du kan använda för att schemalägga regelbundna säkerhetskopior. Med en princip för säkerhetskopiering kan du definiera frekvensen och parametrarna för att ta ögonblicksbilder av en viss volymgrupp. Du kan använda principer för att ange start- och förfallodatum, tider, frekvenser och kvarhållningskrav för både lokala ögonblicksbilder och ögonblicksbilder i molnet. En princip tillämpas omedelbart efter att du har definierat den. 

Du kan använda StorSimple Snapshot Manager för att konfigurera eller konfigurera om principer för säkerhetskopiering när det behövs. 

Du konfigurerar följande information för varje principer för säkerhetskopiering som du skapar:

* **Namn** – Det unika namnet på den valda säkerhetskopieringsprincipen.
* **Typ** – Typ av säkerhetskopieringsprincip; antingen lokal ögonblicksbild eller ögonblicksbild av molnet.
* **Volymgrupp** – Den volymgrupp som den valda säkerhetskopieringsprincipen är tilldelad.
* **Kvarhållning** – Antalet säkerhetskopior som ska behållas. Om du markerar rutan **Alla** behålls alla säkerhetskopior tills det maximala antalet säkerhetskopior per volym har uppnåtts, då principen misslyckas och genererar ett felmeddelande. Du kan också ange ett antal säkerhetskopior som ska behållas (mellan 1 och 64).
* **Datum** – Det datum då säkerhetskopieringsprincipen skapades.

Information om hur du konfigurerar principer för säkerhetskopiering finns i [Använda StorSimple Snapshot Manager för att skapa och hantera principer för säkerhetskopiering](storsimple-snapshot-manager-manage-backup-policies.md).

### <a name="backup-job-monitoring-and-management"></a>Övervakning och hantering av säkerhetskopieringsjobb
Du kan använda StorSimple Snapshot Manager för att övervaka och hantera kommande, schemalagda och slutförda säkerhetskopieringsjobb. Dessutom innehåller StorSimple Snapshot Manager en katalog med upp till 64 slutförda säkerhetskopior. Du kan använda katalogen för att hitta och återställa volymer eller enskilda filer. 

Information om hur du övervakar säkerhetskopieringsjobb finns i [Använd StorSimple Snapshot Manager för att visa och hantera säkerhetskopieringsjobb](storsimple-snapshot-manager-manage-backup-jobs.md).

## <a name="next-steps"></a>Nästa steg
* Läs mer om [hur du använder StorSimple Snapshot Manager för att administrera din StorSimple-lösning](storsimple-snapshot-manager-admin.md).
* Ladda ner [StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220).

