---
title: "Översikt över Microsoft Azure StorSimple virtuell matris | Microsoft Docs"
description: "Beskriver den virtuella StorSimple-matrisen, en integrerad lagringslösning som hanterar lagringsuppgifter mellan en lokal virtuell matris och molnlagring i Microsoft Azure."
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: 
ms.assetid: 169c639b-1124-46a5-ae69-ba9695525b77
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 10/16/2017
ms.author: alkohli
ms.openlocfilehash: b9a5797751fa970c569c93e5efe300d4d74319ce
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2017
---
# <a name="introduction-to-the-storsimple-virtual-array"></a>Introduktion till den virtuella StorSimple-matrisen

## <a name="overview"></a>Översikt

Microsoft Azure StorSimple virtuell matrisen är en integrerad lagringslösning som hanterar lagringsuppgifter mellan en lokal virtuell matris körs i ett hypervisor-programmet och Microsoft Azure-molnlagring. Virtuella matrisen är ett effektivt, kostnadseffektiv och lätthanterade filserver eller iSCSI-server-lösning som eliminerar många problem och kostnader som är associerade med enterprise lagring och dataskydd. Virtuella matrisen är särskilt väl lämpade för lagring av data som sällan används arkivering.

Den här artikeln innehåller en översikt över virtuella matrisen – nedan följer några andra resurser:

* Bästa praxis, se [virtuella StorSimple-matris metodtips](storsimple-ova-best-practices.md).
* En översikt över StorSimple 8000-serien enheter går du till [StorSimple 8000-serien: en hybridmolnlösningen](storsimple-overview.md).
* För information om enheter för StorSimple 5000/7000-serien, gå till [StorSimple onlinehjälp](http://onlinehelp.storsimple.com/).

Virtuella matrisen stöder iSCSI- eller Server Message Block (SMB)-protokollet. Det körs på din befintliga infrastruktur för hypervisor-program och ger skiktning till molnet, säkerhetskopiering i molnet, snabb återställning, objektnivååterställning och disaster recovery-funktioner.

I följande tabell sammanfattas de viktigaste funktionerna i den virtuella StorSimple-matrisen.

| Funktion | StorSimple virtuell matris |
| --- | --- |
| Installationskrav |Använder infrastrukturen för virtualisering (Hyper-V eller VMware) |
| Tillgänglighet |Enkel nod |
| Total kapacitet (inklusive moln) |Upp till 64 TB användbar kapacitet per virtuell matris |
| Lokala kapacitet |390 GB till användbara kapacitet 6.4 TB per virtuell matris (behovet av att etablera 500 GB till 8 TB utrymme) |
| Intern protokoll |iSCSI- eller SMB |
| Mål för återställningstid (RTO) |iSCSI: 2 minuter oavsett storlek |
| Mål för återställningspunkt (RPO) |Dagliga säkerhetskopieringar och säkerhetskopieringar på begäran |
| Lagringsnivåer |Utnyttjar värme mappning för att avgöra vilka data ska vara nivåer in eller ut |
| Support |Virtualiseringsinfrastruktur som stöds av leverantören |
| Prestanda |Varierar beroende på underliggande infrastruktur |
| Data mobility |Återställa till samma enhet eller objektnivå återställningen (filserver) |
| Lagringsnivåer |Lokala hypervisorn lagring och molnet |
| Dela storlek |Nivåer: upp till 20 TB; lokalt Fäst: upp till 2 TB |
| Volymens storlek |Nivåindelad: 500 GB till 5 TB; lokalt Fäst: 50 GB till 200 GB <br> Maximal lokala reservation för nivåindelade volymer är 200 GB. |
| Ögonblicksbilder |Kraschkonsekventa |
| Objektnivååterställning |Ja. användare kan återställa från resurser |

## <a name="why-use-storsimple"></a>Varför använda StorSimple?

StorSimple ansluter användare och servrar till Azure-lagring i minuter, utan att några ändringar i programmet.

I följande tabell beskrivs några av de främsta fördelarna som ger virtuella StorSimple-matris-lösningen.

| Funktion | Fördelar |
| --- | --- |
| Transparent integrering |Virtuella matrisen stöder iSCSI- eller SMB-protokollet. Dataförflyttning mellan den lokala nivån och molnnivån är sömlös och transparent för användaren. |
| Minskad lagringskostnader |Med StorSimple, kan du etablera tillräcklig lokal lagring för att uppfylla aktuella krav används mest aktiva data. När lagring behöver växa, molnlagring StorSimple nivåerna kall data till kostnadseffektiv. Data är deduplicerad och komprimerade innan du skickar till molnet för att ytterligare minska utrymmeskraven och utgifter. |
| Förenklad lagringshantering |StorSimple tillhandahåller centraliserad hantering i molnet med Enhetshanteraren för StorSimple för att hantera flera enheter. |
| Förbättrad återställning och efterlevnad |StorSimple underlättar snabbare katastrofåterställning genom att återställa metadata omedelbart och återställa data efter behov. Det innebär att normal drift kan fortsätta med minimala störningar. |
| Data mobility |Data nivåer till molnet kan nås från andra platser för återställning och migrering. Observera att du kan återställa data endast till den ursprungliga virtuella matrisen. Du dock använda disaster recovery funktioner för att återställa hela virtuella matris till en annan virtuell matris. |

## <a name="storsimple-workload-summary"></a>Översikt över arbetsbelastningar för StorSimple

En sammanfattning av StorSimple-arbetsbelastningar som stöds visas i tabellen nedan.

|Scenario     |Arbetsbelastning     |Stöds      |Begränsningar               |
|-------------|-------------|---------------|---------------------------|
|ROBO  |Fildelning     |Ja      |Se [gränsvärden för filserver](storsimple-ova-limits.md).<br></br>Se [systemkrav för SMB-versioner som stöds](storsimple-ova-system-requirements.md).| Alla versioner     |
|Molnet arkivering  |Arkivering fildelning     |Ja      |Se [gränsvärden för filserver](storsimple-ova-limits.md).<br></br>Se [systemkrav för SMB-versioner som stöds](storsimple-ova-system-requirements.md).| Alla versioner     |

Den virtuella StorSimple-matrisen passar bäst för data som sällan används. Medan virtuella matrisen har en lokal cache för att öka prestanda, bör användarna förutsätter att enheten tjänster filerna på den lägsta nivån av lagring (moln). Varje virtuell matris kan skriva och läsa till Azure-lagring med cirka 100 Mbit/s. Länken är gemensamma för alla begäranden som kommer till enheten och kan bli en flaskhals som visas i diagrammet nedan.

![Molnet arkivering](./media/storsimple-ova-overview/cloud-archiving.png)

När flera samtidiga användare har åtkomst till den virtuella matrisen delar alla anslutningen till Azure leder till en lägre prestanda. Det finns inga garanterad prestanda per användare och enheten bearbetar enskilda begäranden som tas emot.

StorSimple virtuell matrisen är inte lämpligt för arbetsbelastningar som kräver hög tillgänglighet. Virtuella matrisen är en enskild nod-enhet som uppstår driftstopp när programuppdateringar installeras. Administratörer bör planera för en underhållsperiod 30 minuter 3 – 4 gånger per år.

## <a name="workflows"></a>Arbetsflöden

Den virtuella StorSimple-matrisen är särskilt lämpad för följande arbetsflöden:

* [Molnbaserad lagringshantering](#cloud-based-storage-management)
* [Platsoberoende säkerhetskopiering](#location-independent-backup)
* [Återställning av data protection och katastrofåterställning](#data-protection-and-disaster-recovery)

### <a name="cloud-based-storage-management"></a>molnbaserad lagringshantering
Du kan använda Enhetshanteraren för StorSimple-tjänsten som körs i Azure-portalen för att hantera data som lagras på flera enheter och på flera platser. Detta är särskilt användbart i scenarier med distribuerade grenen. Observera att du måste skapa separata instanser av tjänsten StorSimple Device Manager för att hantera matriser virtuella och fysiska StorSimple-enheter. Observera också att virtuella matrisen nu använder den nya Azure-portalen i stället för den klassiska Azure-portalen.

![molnbaserad lagringshantering](./media/storsimple-ova-overview/cloud-based-storage-management.png)

### <a name="location-independent-backup"></a>Platsoberoende säkerhetskopiering
Med virtuella matrisen ge molnögonblicksbilder en Platsoberoende, point-in-time kopia av en volym eller resurs. Molnögonblicksbilder är aktiverade som standard och kan inte inaktiveras. Alla volymer och resurser är en säkerhetskopia i taget via en enda princip för daglig säkerhetskopiering och du kan vidta ytterligare ad hoc-säkerhetskopiering vid behov.

### <a name="data-protection-and-disaster-recovery"></a>Återställning av data protection och katastrofåterställning
Virtuella matrisen stöder följande dataskydd och katastrofåterställning:

* **Volym eller resurs återställning** – Använd återställningen som nytt arbetsflöde för att återställa en volym eller resurs. Använd den här metoden för att återställa hela volymen eller resursen.
* **Återställa** – resurser Tillåt förenklad åtkomst till nya säkerhetskopior. Du kan enkelt återställa en enskild fil från en särskild *.backup* mapp som är tillgänglig i molnet. Den här funktionen för återställning är-användardrivna och inga administrativa åtgärder krävs.
* **Katastrofåterställning** – använda redundanskapacitet för att återställa alla volymer eller resurser till en ny virtuell matris. Skapa den nya virtuella matrisen och registrera den med StorSimple enheten Manager-tjänsten och sedan växla över den ursprungliga virtuella matrisen. Den nya virtuella matrisen antar sedan de allokerade resurserna.

## <a name="storsimple-virtual-array-components"></a>StorSimple virtuell matris komponenter

Virtuella matrisen innehåller följande komponenter:

* [Virtuella matris](#virtual-array) – en hybrid cloud lagringsenhet baserat på en virtuell dator etablerats i din virtualiserade miljö eller hypervisor.
* [StorSimple Enhetshanteraren service](#storsimple-device-manager-service) – ett tillägg för Azure-portalen där du kan hantera en eller flera StorSimple-enheter från en enda webbgränssnitt som du kan komma åt från olika geografiska platser. Du kan använda Enhetshanteraren för StorSimple-tjänsten för att skapa och hantera tjänster, visa och hantera enheter och aviseringar och hantera volymer, resurser och befintliga ögonblicksbilder.
* [Lokala webbanvändargränssnitt](#local-web-user-interface) – ett webbaserat gränssnitt som används för att konfigurera enheten så att den kan ansluta till det lokala nätverket och sedan registrera enheten med Enhetshanteraren för StorSimple-tjänsten. 
* [Kommandoradsgränssnittet](#command-line-interface) – en Windows PowerShell-gränssnitt som du kan använda för att starta en session med stöd för på virtuella matrisen.
  I följande avsnitt beskrivs var och en av dessa komponenter i detalj och förklara hur lösningen ordnar data allokerar lagringsutrymme och underlättar lagringshantering och dataskydd.

### <a name="virtual-array"></a>Virtuell matris

Virtuella matrisen är en nod lagringslösning som ger primär lagring, hanterar kommunikationen med molnlagring och hjälper dig för att kontrollera säkerhet och sekretess för alla data som lagras på enheten.

Virtuella matrisen är tillgänglig i en modell som är tillgänglig för hämtning. Virtuella matrisen har en maximal kapacitet för 6.4 TB på enhet (med en underliggande utrymmeskravet på 8 TB) och 64 TB, inklusive molnlagring.

Virtuella matrisen har följande funktioner:

* Det är kostnadseffektiv. Det gör användning av din befintliga virtualiseringsinfrastruktur för och kan distribueras på din befintliga Hyper-V- eller VMware hypervisor-programmet.
* Den finns i datacentret och kan konfigureras som en iSCSI-server eller en filserver.
* Det är integrerat med molnet.
* Säkerhetskopior lagras i molnet, som kan underlätta återställning och förenkla återställning på objektnivå (ILR).
* Du kan tillämpa uppdateringar på virtuella matrisen, precis som du skulle använda dem på en fysisk enhet.

> [!NOTE]
> En virtuell matris kan inte expanderas. Det är därför viktigt att tillhandahålla tillräcklig lagring när du skapar virtuella matrisen.

### <a name="storsimple-device-manager-service"></a>StorSimple enheten Manager-tjänsten

Microsoft Azure StorSimple innehåller ett webbaserat användargränssnitt tjänsten StorSimple Device Manager, som gör det möjligt att centralt hantera StorSimple lagring. Du kan använda Enhetshanteraren för StorSimple-tjänsten för att utföra följande uppgifter:

* Hantera flera StorSimple virtuell matriser från en enskild tjänst.
* Konfigurera och hantera säkerhetsinställningar för virtuell StorSimple-matriser. (Kryptering i molnet är beroende av Microsoft Azure API: er).
* Konfigurera lagringskontouppgifter och egenskaper.
* Konfigurera och hantera volymer eller resurser.
* Säkerhetskopiera och återställa data på volymer eller resurser.
* Övervaka prestanda.
* Granska systeminställningarna för och identifiera eventuella problem.

Du kan använda Enhetshanteraren för StorSimple-tjänsten för att utföra dagliga administration av virtuella matrisen.

Mer information finns på [använda Enhetshanteraren för StorSimple-tjänsten för att administrera din StorSimple-enhet](storsimple-virtual-array-manager-service-administration.md).

### <a name="local-web-user-interface"></a>Lokala webbanvändargränssnitt

Virtuella matrisen innehåller ett webbaserat gränssnitt som används för engångskonfiguration och registrering av enheten med Enhetshanteraren för StorSimple-tjänsten. Du kan använda den för att stänga av och starta om den virtuella matrisen, kör diagnostiktest, uppdatera programvara, ändra enhetens administratörslösenord, visa systemloggar och kontakta Microsoft-supporten om du vill skicka en serviceförfrågan.

Gå till information om hur du använder Användargränssnittet webbaserade [använder webbaserade UI för att administrera din virtuella StorSimple-matris](storsimple-ova-web-ui-admin.md).

### <a name="command-line-interface"></a>Kommandoradsgränssnittet

Med Windows PowerShell-gränssnittet kan du initiera en supportsession med Microsoft-supporten så att de kan hjälpa dig att felsöka och lösa problem som kan uppstå på din virtuella matrisen.

## <a name="storage-management-technologies"></a>Tekniker för hantering av lagring

Använder följande tekniker för programvara för att ger snabb åtkomst till viktiga data, minska lagringsanvändningen och skydda data som lagras på din virtuella matrisen förutom virtuella matrisen och andra komponenter, StorSimple-lösningen:

* [Automatisk lagringsnivåer](#automatic-storage-tiering) 
* [Lokalt Fäst filresurser och volymer](#locally-pinned-shares-and-volumes)
* [Deduplicering och komprimering av data nivåer eller säkerhetskopieras till molnet](#deduplication-and-compression-for-data-tiered/backed-up-to-the-cloud) 
* [Schemalagda säkerhetskopieringar och på begäran säkerhetskopieringar](#scheduled-and-on-demand-backups)

### <a name="automatic-storage-tiering"></a>automatisk lagringsnivåer
Virtuella matrisen använder en ny lagringsnivåer mekanism för att hantera lagrade data över virtuella matrisen och molnet. Det finns bara två nivåer: den lokala virtuella matris och Azure molnlagring. Den virtuella StorSimple-matrisen ordnar automatiskt data i nivåer baserat på en termisk karta som spårar aktuell användning, ålder och relationer till andra data. Data som är mest aktiv (senaste) lagras lokalt, medan mindre aktiva och inaktiva data migreras automatiskt till molnet. (Alla säkerhetskopior lagras i molnet.) StorSimple anpassar och ordnar om data och ändra lagring tilldelningar som användningsmönster. Viss information kan till exempel bli mindre aktiva över tid. Vartefter det blir mindre progressivt aktiva är den nivåer ut till molnet. Om samma data blir aktiva igen, är det nivåer i till lagringsmatrisen.

Data för en viss nivåindelade resursen eller volymen är garanterat egna lokala nivån utrymme (cirka 10% av det totala allokerade utrymmet för resursen eller volymen). När det här minskar tillgängligt lagringsutrymme på virtuella matrisen för resursen eller volymen säkerställer att skiktning för en resurs eller volym inte påverkas av lagringsnivåer behoven hos andra resurser eller volymer. Därför kan inte en hårt arbetsbelastning på en resurs eller volym tvinga andra arbetsbelastningar till molnet.

Nivåindelade volymer som skapats för iSCSI-har en maximal lokala reservation 200 GB oavsett storleken på volymen.

![automatisk lagringsnivåer](./media/storsimple-ova-overview/automatic-storage-tiering.png)

> [!NOTE]
> Du kan ange en lokalt Fäst volym, i vilket fall data finns kvar på den virtuella matrisen och aldrig nivåer till molnet. Mer information finns på [lokalt Fäst filresurser och volymer](#locally-pinned-shares-and-volumes).


### <a name="locally-pinned-shares-and-volumes"></a>Lokalt Fäst filresurser och volymer

Du kan skapa lämpliga resurser och lokalt fästa volymer. Den här funktionen gör att data som krävs av kritiska program finns kvar i virtuella matrisen och aldrig nivåer till molnet. Lokalt Fäst filresurser och volymer har följande funktioner:

* Omfattas inte molnet fördröjningar eller problem med nätverksanslutningen.
* De fortfarande dra nytta av StorSimple moln säkerhetskopiering och katastrofåterställning återställningsfunktioner.

Du kan återställa en lokalt Fäst resursen eller volymen som nivåer eller en skiktad resursen eller volymen lokalt Fäst. 

Mer information om lokalt fästa volymer, gå till [använda Enhetshanteraren för StorSimple-tjänsten för att hantera volymer](storsimple-virtual-array-manage-volumes.md).

### <a name="deduplication-and-compression-for-data-tiered-or-backed-up-to-the-cloud"></a>Deduplicering och komprimering av data nivåer eller säkerhetskopieras till molnet

StorSimple använder deduplicering och data komprimering för att ytterligare minska utrymmeskraven i molnet. Deduplicering minskar den övergripande informationen genom att eliminera redundans i uppsättningen av lagrade data. När information ändras, StorSimple ignorerar oförändrade data och samlar in bara ändringarna. Dessutom minskar StorSimple mängden lagrade data genom att identifiera och ta bort dubblettinformation.

> [!NOTE]
> Data som lagras på den virtuella matrisen är inte dedupliceras eller komprimerad. Alla dedupliceringen och komprimering sker precis innan data skickas till molnet.

### <a name="scheduled-and-on-demand-backups"></a>Schemalagda säkerhetskopieringar och på begäran säkerhetskopieringar

StorSimple Dataskyddsfunktioner kan du skapa säkerhetskopieringar på begäran. Dessutom garanterar ett schema för säkerhetskopiering av standard att data säkerhetskopieras varje dag. Säkerhetskopieringar vidtas i form av inkrementell ögonblicksbilder som lagras i molnet. Ögonblicksbilder som registrerar bara ändringarna sedan den senaste säkerhetskopieringen kan skapas och återställs snabbt. Dessa ögonblicksbilder kan vara ytterst viktigt i katastrofåterställning eftersom de ersätta sekundära lagringssystem (till exempel bandsäkerhetskopiering) och att du kan återställa data till ditt datacenter eller till en annan om det behövs.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [förbereda virtuella matris portal](storsimple-virtual-array-deploy1-portal-prep.md).
