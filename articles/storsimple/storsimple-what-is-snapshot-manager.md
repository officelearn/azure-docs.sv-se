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
ms.openlocfilehash: 3f7436bb63f52c9c2b697c8e7031922ce89d786b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60789628"
---
# <a name="an-introduction-to-storsimple-snapshot-manager"></a>En introduktion till StorSimple Snapshot Manager

## <a name="overview"></a>Översikt
StorSimple Snapshot Manager är en snapin-modul i Microsoft Management Console (MMC) som förenklar dataskydd och hantering av säkerhetskopiering i en miljö med Microsoft Azure StorSimple. Med StorSimple Snapshot Manager hantera Microsoft Azure StorSimple data i datacentret och i molnet som en enda integrerad lagringslösning, vilket förenklar säkerhetskopiering processer och minska kostnaderna.

Den här översikten beskriver StorSimple Snapshot Manager, beskriver funktioner och förklarar dess roll i Microsoft Azure StorSimple. 

En översikt över hela Microsoft Azure StorSimple-systemet, inklusive StorSimple-enheten, StorSimple Manager-tjänsten, StorSimple Snapshot Manager och StorSimple Adapter för SharePoint, finns i [StorSimple 8000-serien: ett hybridmoln lagringslösning](storsimple-overview.md). 

> [!NOTE]
> * Du kan inte använda StorSimple Snapshot Manager för att hantera Microsoft Azure StorSimple Virtual Array (även kallat StorSimple lokala virtuella enheter).
> * Om du planerar att installera StorSimple Update 2 på StorSimple-enheten, måste du hämta den senaste versionen av StorSimple Snapshot Manager och installera den **innan du installerar StorSimple Update 2**. Den senaste versionen av StorSimple Snapshot Manager är bakåtkompatibla och fungerar med alla versioner av Microsoft Azure StorSimple. Om du använder den tidigare versionen av StorSimple Snapshot Manager behöver du uppdatera den (du inte behöver avinstallera den tidigare versionen innan du installerar den nya versionen).
> 
> 

## <a name="storsimple-snapshot-manager-purpose-and-architecture"></a>StorSimple Snapshot Manager syfte och arkitektur
StorSimple Snapshot Manager innehåller en central hantering-konsol som du kan använda för att skapa enhetliga, point-in-time-säkerhetskopior av lokala och data i molnet. Du kan till exempel använda konsolen för att:

* Konfigurera, säkerhetskopiera och ta bort volymer.
* Konfigurera volymgrupper så säkerhetskopierade data är konsekventa.
* Hantera principer för säkerhetskopiering så att data säkerhetskopieras enligt ett förutbestämt schema.
* Skapa lokala och molnbaserade ögonblicksbilder som kan lagras i molnet och används för haveriberedskap.

StorSimple Snapshot Manager hämtar listan över program som är registrerade med VSS-providern på värden. För att skapa programkonsekventa säkerhetskopior, kontrollerar de volymer som används av ett program och föreslår volymgrupper att konfigurera. StorSimple Snapshot Manager använder grupperna volym för att generera säkerhetskopior som är konsekventa. (Programkonsekvens finns när alla relaterade filer och databaser synkroniseras och återger SANT tillståndet för programmet vid en viss tidpunkt). 

StorSimple Snapshot Manager säkerhetskopior bestå av inkrementella ögonblicksbilder som samlar in endast ändringar sedan den senaste säkerhetskopieringen. Därför kan säkerhetskopior kräver mindre lagringsutrymme och skapas och återställa snabbt. StorSimple Snapshot Manager använder Windows Volume Shadow Copy Service (VSS) för att se till att hårdiskdata programkonsekvent data. (Mer information finns på integrering med Windows Volume Shadow Copy Service-avsnittet.) Med StorSimple Snapshot Manager, som du kan skapa scheman för säkerhetskopiering eller göra säkerhetskopior av omedelbart efter behov. Om du behöver återställa data från en säkerhetskopiering, StorSimple Snapshot Manager kan välja du från en katalog med lokal eller ögonblicksbilder av molnet. Azure StorSimple återställer endast de data som krävs när det behövs, vilket förhindrar fördröjningar i datatillgänglighet under återställningsåtgärder.)

![Arkitektur för StorSimple Snapshot Manager](./media/storsimple-what-is-snapshot-manager/HCS_SSM_Overview.png)

**Arkitektur för StorSimple Snapshot Manager** 

## <a name="support-for-multiple-volume-types"></a>Stöd för flera volymtyper av
Du kan använda StorSimple Snapshot Manager för att konfigurera och säkerhetskopiera följande typer av volymer: 

* **Enkla volymer** – en enkel volym är en enskild partition på en standarddisk. 
* **Enkla volymer** – en enkel volym är en dynamisk volym som innehåller diskutrymmet från en enda dynamisk disk. En enkel volym består av en enda region på en disk eller flera regioner länkas samman på samma disk. (Du kan skapa enkla volymer på dynamiska diskar.) Enkla volymer är inte feltoleranta.
* **Dynamiska volymer** – en dynamisk volym är en volym som har skapats på en dynamisk disk. Dynamiska diskar använder en databas för att spåra information om volymer som finns på dynamiska diskar på en dator. 
* **Dynamiska volymer med spegling** – dynamiska volymer med spegling bygger på RAID 1-arkitektur. Med RAID 1 skrivs identiska data på två eller flera disk, producera en speglad uppsättning. En läsbegäran kan sedan hanteras av en disk som innehåller data som begärdes.
* **Klusterdelade volymer** – med klusterdelade volymer (CSV), flera noder i ett failover-kluster kan samtidigt läsa eller skriva till samma disk. Redundans från en nod till en annan nod kan inträffa snabbt, utan en ändring i ägandet eller montera demontera och ta bort en volym. 

> [!IMPORTANT]
> Blanda inte CSV: er och icke-CSV: er i samma ögonblicksbild. Blanda CSV: er och icke-CSV: er i en ögonblicksbild stöds inte. 
> 
> 

Du kan använda StorSimple Snapshot Manager för att återställa hela volymgrupper eller klona enskilda volymer och återställa enskilda filer.

* [Volymer och volymgrupper](#volumes-and-volume-groups) 
* [Säkerhetskopieringstyper och principer för säkerhetskopiering](#backup-types-and-backup-policies) 

Mer information om StorSimple Snapshot Manager-funktioner och hur de används finns i [StorSimple Snapshot Manager-användargränssnittet](storsimple-use-snapshot-manager.md).

## <a name="volumes-and-volume-groups"></a>Volymer och volymgrupper
Med StorSimple Snapshot Manager, som du skapar volymer och konfigurera dem i volymgrupper. 

StorSimple Snapshot Manager använder volymgrupper för att skapa säkerhetskopior som är konsekventa. Programkonsekvens finns när alla relaterade filer och databaser synkroniseras och återger SANT tillståndet för ett program vid en viss tidpunkt. Volymgrupper (som kallas även *konsekvensgrupper*) utgör grunden för en säkerhetskopia eller återställa jobbet.

Volymgrupper är inte samma som volymbehållare. En volymbehållare innehåller en eller flera volymer som delar ett molnlagringskonto och andra attribut, till exempel kryptering och bandbredd. En enskild volym-behållare kan innehålla upp till 256 tunt allokerade StorSimple-volymer. Mer information om volymbehållare går du till [hantera din volymbehållare](storsimple-manage-volume-containers.md). Volymgrupper är samlingar av volymer som du konfigurerar för att underlätta säkerhetskopiering. Om du väljer två volymer som tillhör olika volymbehållare, placera dem i en enda volym-grupp och sedan skapa en princip för säkerhetskopiering för den volym gruppen säkerhetskopieras varje volym i lämplig volymbehållaren med hjälp av lämplig storage-konto.

> [!NOTE]
> Alla volymer i en volym-grupp måste komma från en enda molntjänstleverantör.
> 
> 

## <a name="integration-with-windows-volume-shadow-copy-service"></a>Integrering med Windows Volume Shadow Copy-tjänsten
StorSimple Snapshot Manager använder Windows Volume Shadow Copy Service (VSS) för att samla in programkonsekvent data. VSS underlättar programkonsekvens genom att kommunicera med VSS-medvetna program att samordna skapandet av inkrementella ögonblicksbilder. VSS säkerställer att programmen är tillfälligt inaktiv eller overksamt, när ögonblicksbilder tas. 

StorSimple Snapshot Manager-implementeringen av VSS fungerar med SQL Server och allmän NTFS-volymer. Processen är följande: 

1. En begärande som är vanligtvis en datahantering och lösning för skydd av (till exempel StorSimple Snapshot Manager) eller ett program för säkerhetskopiering, anropar VSS och frågar den för att samla in information från skrivaren programvaran i målprogrammet.
2. VSS kontaktar writer-komponenten för att hämta en beskrivning av data. Skrivar returnerar beskrivningen av de data som ska säkerhetskopieras. 
3. VSS signalerar att skrivaren kan förbereda ett program för säkerhetskopiering. Skrivar förbereder data för säkerhetskopiering genom att slutföra öppna transaktioner uppdaterar transaktionsloggar och så vidare och meddelar VSS.
4. VSS instruerar skrivaren kan tillfälligt stoppa programmets datalager och se till att inga data skrivs till volymen medan skuggkopian skapas. Det här steget säkerställer att datakonsekvens och tar mer än 60 sekunder.
5. VSS instruerar provider för att skapa en skuggkopia. Leverantörer, som kan vara program - eller maskinvarubaserade, hantera de volymer som körs för tillfället och skapa skuggkopior av dem på begäran. Providern skapar skuggkopian och meddelar VSS när den har slutförts.
6. VSS kontaktar skrivaren att meddela programmet i/o kan återuppta och även för att bekräfta att i/o paus under skapandet av skuggkopian. 
7. Om kopieringen har slutförts, returnerar VSS den kopieringsplatsen till begäranden. 
8. Om data har skrivits när ögonblicksbilden har skapats, kommer säkerhetskopian inte överensstämmer. VSS tar bort skuggkopian och meddelar begäranden. Begäranden kan upprepa säkerhetskopieringsprocessen automatiskt eller meddela administratören försöka igen vid ett senare tillfälle.

Se följande bild.

![VSS-processen](./media/storsimple-what-is-snapshot-manager/HCS_SSM_VSS_process.png)

**Tjänsten Windows Volume Shadow Copy-processen** 

## <a name="backup-types-and-backup-policies"></a>Säkerhetskopieringstyper och principer för säkerhetskopiering
Med StorSimple Snapshot Manager kan du säkerhetskopiera data och lagra den lokalt och i molnet. Du kan använda StorSimple Snapshot Manager för att säkerhetskopiera data direkt, eller du kan använda en princip för säkerhetskopiering för att skapa ett schema för säkerhetskopiering automatiskt. Principer för säkerhetskopiering kan du ange hur många ögonblicksbilder ska behållas. 

### <a name="backup-types"></a>Typer av säkerhetskopiering
Du kan använda StorSimple Snapshot Manager för att skapa följande typer av säkerhetskopiering:

* **Lokala ögonblicksbilder** – lokala ögonblicksbilder är point-in-time-kopior av volymdata som lagrats på StorSimple-enheten. Normalt kan den här typen av säkerhetskopiering skapas och återställa snabbt. Du kan använda en lokal ögonblicksbild precis som en lokal säkerhetskopia.
* **Molnbaserade ögonblicksbilder** – molnögonblicksbilder är point-in-time-kopior av volymdata som lagras i molnet. En ögonblicksbild i molnet motsvarar en ögonblicksbild som replikerats på ett annat, externa lagringssystem. Ögonblicksbilder av molnet som är särskilt användbart i scenarier för haveriberedskap.

### <a name="on-demand-and-scheduled-backups"></a>På begäran och schemalagda säkerhetskopieringar
Du kan initiera en tidsbegränsad säkerhetskopia skapas direkt med StorSimple Snapshot Manager eller du kan använda en princip för säkerhetskopiering för att schemalägga återkommande säkerhetskopieringsåtgärder.

En princip för säkerhetskopiering är en uppsättning automatiska regler som du kan använda för att schemalägga regelbundna säkerhetskopieringar. En princip för säkerhetskopiering kan du definiera frekvensen och parametrar för att ta ögonblicksbilder av en specifik volym-grupp. Du kan använda principer för att ange start-och förfallodatum, tider, frekvenser och krav på datalagring, för både lokala och molnbaserade ögonblicksbilder. En princip tillämpas omedelbart när du har definierat den. 

Du kan använda StorSimple Snapshot Manager för att konfigurera eller ändra principer för säkerhetskopiering när det behövs. 

Du kan konfigurera följande information för varje princip för säkerhetskopiering som du skapar:

* **Namn på** – det unika namnet för den valda säkerhetskopieringsprincipen.
* **Typ** – typ av princip för säkerhetskopiering, antingen lokal ögonblicksbild eller ögonblicksbild i molnet.
* **Volymen grupp** – gruppen volym som den valda säkerhetskopieringsprincipen är tilldelad.
* **Kvarhållning** – antal säkerhetskopior att behålla. Om du markerar den **alla** rutan alla säkerhetskopiorna ska bevaras tills det maximala antalet säkerhetskopior per volym har uppnåtts, då principen misslyckas och genererar ett felmeddelande. Du kan också ange ett antal säkerhetskopieringar att behålla (mellan 1 och 64).
* **Datum** – det datum när säkerhetskopieringspolicyn har skapats.

Information om hur du konfigurerar principer för säkerhetskopiering, går du till [Använd StorSimple Snapshot Manager för att skapa och hantera principer för säkerhetskopiering](storsimple-snapshot-manager-manage-backup-policies.md).

### <a name="backup-job-monitoring-and-management"></a>Säkerhetskopieringsjobbet övervakning och hantering
Du kan använda StorSimple Snapshot Manager för att övervaka och hantera kommande, schemalagda och slutförda jobb. Dessutom ger StorSimple Snapshot Manager en katalog med upp till 64 slutförda säkerhetskopieringar. Du kan använda katalogen för att hitta och återställa volymer eller enskilda filer. 

Gå till information om hur du övervakar säkerhetskopieringsjobb [Använd StorSimple Snapshot Manager för att visa och hantera säkerhetskopieringsjobb](storsimple-snapshot-manager-manage-backup-jobs.md).

## <a name="next-steps"></a>Nästa steg
* Läs mer om [med StorSimple Snapshot Manager för att administrera din StorSimple-lösning](storsimple-snapshot-manager-admin.md).
* Ladda ned [StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220).

