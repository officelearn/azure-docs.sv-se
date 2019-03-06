---
title: Översikt över Microsoft Azure StorSimple Virtual Array | Microsoft Docs
description: Beskriver StorSimple Virtual Array, en integrerad lagringslösning som hanterar lagringsuppgifter mellan en lokal virtuell matris och Microsoft Azure-molnlagring.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: 169c639b-1124-46a5-ae69-ba9695525b77
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 08/02/2018
ms.author: alkohli
ms.openlocfilehash: decc63ad7ce0b35de41ec53e6b5e02276aa33705
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57456082"
---
# <a name="introduction-to-the-storsimple-virtual-array"></a>Introduktion till StorSimple Virtual Array

## <a name="overview"></a>Översikt

Microsoft Azure StorSimple Virtual Array är en integrerad lagringslösning som hanterar lagringsuppgifter mellan en lokal virtuell matris som körs på en hypervisor-programmet och Microsoft Azure-molnlagring. Den virtuella matrisen är en effektiv, kostnadseffektiv och lätthanterade filserver eller iSCSI-server-lösning som eliminerar många av de problem och kostnader som är förknippade med företagets lagring och dataskydd. Den virtuella matrisen lämpar sig väl för lagring av data som sällan används arkivering.

Den här artikeln innehåller en översikt över den virtuella matrisen – här är några andra resurser:

* Bästa praxis, se [Metodtips för StorSimple Virtual Array](storsimple-ova-best-practices.md).
* En översikt över StorSimple 8000-serieenheter går du till [StorSimple 8000-serien: en hybridmolnlösning](storsimple-overview.md).
* Information om serieenheter för StorSimple 5000/7000-, går du till [StorSimple onlinehjälp](http://onlinehelp.storsimple.com/).

Den virtuella matrisen har stöd för iSCSI- eller Server Message Block (SMB) protokollet. Den körs på din befintliga infrastruktur för hypervisor-program och ger lagringsnivåer till molnet, säkerhetskopiering i molnet, snabb återställning, återställning och funktioner för katastrofåterställning.

I följande tabell sammanfattas de viktigaste funktionerna i StorSimple Virtual Array.

| Funktion | StorSimple virtuell matris |
| --- | --- |
| Installationskrav |Använder virtualiseringsinfrastrukturen för (Hyper-V eller VMware) |
| Tillgänglighet |Enkel nod |
| Total kapacitet (inklusive molnet) |Upp till 64 TB användbar kapacitet per virtuell matris |
| Lokal kapacitet |390 GB till 6.4 TB användbar kapacitet per virtuell matris (du behöver etablera 500 GB till 8 TB diskutrymme) |
| Interna protokoll |iSCSI- eller SMB |
| Mål för återställningstid (RTO) |iSCSI: 2 minuter, oavsett storlek |
| Mål för återställningspunkt (RPO) |Dagliga säkerhetskopieringar och säkerhetskopieringar på begäran |
| Lagringsnivåer |Utnyttjar värme mappning för att avgöra vilka data bör vara nivåindelad in eller ut |
| Support |Virtualiseringsinfrastrukturen som stöds av leverantören |
| Prestanda |Varierar beroende på underliggande infrastruktur |
| Lätt att flytta data |Kan återställa till samma enhet eller objektnivå recovery (filserver) |
| Lagringsnivåer |Lokala hypervisorn lagring och molnet |
| Filresursens storlek |Nivåer: upp till 20 TB; lokalt fixerade: upp till 2 TB |
| Volymens storlek |Nivåer: 500 GB till 5 TB; lokalt fixerade: 50 GB till 200 GB <br> Maximal lokal reservation för nivåindelade volymer är 200 GB. |
| Ögonblicksbilder |Kraschkonsekvent |
| Objektnivååterställning |Ja. användare kan återställa från resurser |

## <a name="why-use-storsimple"></a>Varför använda StorSimple?

StorSimple ansluter användare och servrar till Azure storage på några minuter, utan att några ändringar i programmet.

I följande tabell beskrivs några av de viktigaste fördelarna med StorSimple Virtual Array-lösningen innehåller.

| Funktion | Fördelar |
| --- | --- |
| Transparent integration |Den virtuella matrisen har stöd för iSCSI- eller SMB-protokollet. Dataförflyttning mellan den lokala nivån och molnnivån är sömlös och transparent för användaren. |
| Lägre lagringskostnader |Med StorSimple kan du etablera tillräckligt med lokal lagring för att uppfylla aktuella behoven för de mest använda frekventa data. Eftersom storage behov ökar, molnlagring StorSimple nivåer kalla data till kostnadseffektiv. Data är deduplicerad och komprimeras innan de skickas till molnet att ytterligare minska lagringskrav och utgifter. |
| Förenklad lagringshantering |StorSimple tillhandahåller centraliserad hantering i molnet med StorSimple Device Manager för att hantera flera enheter. |
| Förbättrad haveriberedskap och efterlevnad |StorSimple underlättar snabbare katastrofåterställning genom återställning av metadata omedelbart och återställa data efter behov. Det innebär att normal drift kan fortsätta med minimala störningar. |
| Lätt att flytta data |Nivåindelade data till molnet kan nås från andra platser för återställning och migrering. Observera att du kan återställa data endast till den ursprungliga virtuella matrisen. Dock kan du använda funktioner för katastrofåterställning, för att återställa hela virtuell matris till en annan virtuell matris. |

## <a name="storsimple-workload-summary"></a>Översikt över StorSimple-arbetsbelastningar

En sammanfattning av StorSimple-arbetsbelastningar som stöds visas i tabellen nedan.

|Scenario     |Arbetsbelastning     |Stöds      |Begränsningar               |
|-------------|-------------|---------------|---------------------------|
|Office/fjärrkontor (ROBO)  |Fildelning     |Ja      |Se [gränsvärden för filserver](storsimple-ova-limits.md).<br></br>Se [systemkrav för SMB-versioner som stöds](storsimple-ova-system-requirements.md).| Alla versioner     |
|Molnet arkivering  |Arkivering fildelning     |Ja      |Se [gränsvärden för filserver](storsimple-ova-limits.md).<br></br>Se [systemkrav för SMB-versioner som stöds](storsimple-ova-system-requirements.md).| Alla versioner     |

StorSimple Virtual Array är bäst lämpade för sällan. Medan den virtuella matrisen har en lokal cache för att öka prestandan, bör användarna förutsätter att enheten services filerna på den lägsta nivån lagringsutrymme (moln). Varje virtuell matris kan skriva och läsa till Azure storage med cirka 100 Mbit/s. Länken delas mellan alla förfrågningar som kommer till enheten och kan bli en flaskhals som visas i diagrammet nedan.

![Molnet arkivering](./media/storsimple-ova-overview/cloud-archiving.png)

När flera samtidiga användare komma åt den virtuella matrisen kan dela de anslutningen till Azure leder till en lägre prestanda. Det finns ingen garanterad prestanda per användare och enheten bearbetar enskilda begäranden när de tas emot.

StorSimple Virtual Array är inte lämpligt för arbetsbelastningar som kräver hög tillgänglighet. Den virtuella matrisen är en enskild nod-enhet som inträffar avbrott när programuppdateringar installeras. Administratörer bör planera för en underhållsperiod på 30 minuter 3 – 4 gånger per år.

## <a name="workflows"></a>Arbetsflöden

StorSimple Virtual Array är särskilt lämpligt för följande arbetsflöden:

* [Molnbaserad lagringshantering](#cloud-based-storage-management)
* [Platsoberoende säkerhetskopiering](#location-independent-backup)
* [Dataåterställning för skydd och haveriberedskap](#data-protection-and-disaster-recovery)

### <a name="cloud-based-storage-management"></a>Molnbaserad lagringshantering
Du kan använda StorSimple Device Manager-tjänsten som körs i Azure-portalen för att hantera data som lagras på flera enheter och på flera platser. Detta är särskilt användbart i scenarier med distribuerade gren. Observera att du måste skapa separata instanser av StorSimple Device Manager-tjänsten för att hantera virtuella matriser och fysiska StorSimple-enheter. Observera också att den virtuella matrisen nu använder den nya Azure-portalen i stället för den klassiska Azure-portalen.

![Molnbaserad lagringshantering](./media/storsimple-ova-overview/cloud-based-storage-management.png)

### <a name="location-independent-backup"></a>Platsoberoende säkerhetskopiering
Med den virtuella matrisen ger ögonblicksbilder av molnet som dig en Platsoberoende, point-in-time kopia av en volym eller resurs. Ögonblicksbilder av molnet som är aktiverade som standard och kan inte inaktiveras. Alla volymer och resurser finns säkerhetskopiera samtidigt via en enda princip för daglig säkerhetskopiering du kan göra ytterligare ad hoc-säkerhetskopior när det behövs.

### <a name="data-protection-and-disaster-recovery"></a>Dataåterställning för skydd och haveriberedskap
Den virtuella matrisen har stöd för följande dataskydd och katastrofåterställning:

* **Volym eller resurs återställning** – Använd återställningen som nytt arbetsflöde för att återställa en volym eller resurs. Använd den här metoden för att återställa hela volymen eller resursen.
* **Återställa** – resurser Tillåt förenklad åtkomst till säkerhetskopior som nyligen skapats. Du kan enkelt återställa en enskild fil från en särskild *.backup* mapp som är tillgängliga i molnet. Den här funktionen för återställning är-användardrivna och inga administrativa åtgärder krävs.
* **Katastrofåterställning** – använda redundanskapacitet för att återställa alla volymer eller resurser till en ny virtuell matris. Du skapar den nya virtuella matrisen och registrera den med StorSimple Device Manager-tjänsten och sedan växla över den ursprungliga virtuella matrisen. Den nya virtuella matrisen förutsätter sedan de etablerade resurserna.

## <a name="storsimple-virtual-array-components"></a>StorSimple Virtual Array-komponenter

Den virtuella matrisen innehåller följande komponenter:

* [Virtuell matris](#virtual-array) – en hybrid cloud enhet baserat på en virtuell dator som etablerats i din virtualiserade miljö eller hypervisor-programmet.
* [StorSimple Device Manager-tjänsten](#storsimple-device-manager-service) – ett tillägg till Azure portal som kan hantera en eller flera StorSimple-enheter i ett enda webbgränssnitt som du kan komma åt från olika geografiska platser. Du kan använda StorSimple Device Manager-tjänsten för att skapa och hantera tjänster, visa och hantera enheter och aviseringar och hantera volymer, resurser och befintliga ögonblicksbilder.
* [Lokala webbgränssnitt](#local-web-user-interface) – ett webbaserat gränssnitt som används för att konfigurera enheten så att den kan ansluta till det lokala nätverket och registrera enheten med StorSimple Device Manager-tjänsten. 
* [Kommandoradsgränssnittet](#command-line-interface) – A Windows PowerShell-gränssnittet som du kan använda för att starta en supportsession på den virtuella matrisen.
  I följande avsnitt beskrivs var och en av dessa komponenter i detalj och förklarar hur lösningen ordnar data allokerar lagring och underlättar lagringshantering och dataskydd.

### <a name="virtual-array"></a>Virtuell matris

Den virtuella matrisen är en nod som tillhandahåller primärlagring, hanterar kommunikation med molnlagring och bidrar till att säkerställa säkerheten och sekretessen för alla data som lagras på enheten.

Den virtuella matrisen är tillgänglig i en modell som är tillgänglig för hämtning. Den virtuella matrisen har en maxkapacitet på 6.4 TB på enheten (med en underliggande krav för lagring på 8 TB) och 64 TB, inklusive molnlagring.

Den virtuella matrisen har följande funktioner:

* Det är kostnadseffektivt. Det gör användning av din befintliga virtualiseringsinfrastruktur för och kan distribueras på dina befintliga Hyper-V eller VMware hypervisor.
* Det finns i datacentret och kan konfigureras som en iSCSI-server eller en server.
* Det är integrerat med molnet.
* Säkerhetskopior lagras i molnet, vilket kan underlätta katastrofåterställning och förenkla återställning på objektnivå (ILR).
* Du kan använda uppdateringar för den virtuella matrisen, precis som du vill tillämpa dem på en fysisk enhet.

> [!NOTE]
> En virtuell matris kan inte expanderas. Det är därför viktigt att etablera tillräckligt lagringsutrymme när du skapar den virtuella matrisen.

### <a name="storsimple-device-manager-service"></a>StorSimple Device Manager-tjänst

Microsoft Azure StorSimple ger ett webbaserat användargränssnitt StorSimple Device Manager-tjänsten, som låter dig centralt hantera StorSimple-lagring. Du kan använda StorSimple Device Manager-tjänsten för att utföra följande uppgifter:

* Hantera flera StorSimple Virtual Array från en enskild tjänst.
* Konfigurera och hantera säkerhetsinställningar för StorSimple Virtual Array. (Kryptering i molnet är beroende av Microsoft Azure API: er.)
* Konfigurera autentiseringsuppgifterna för lagringskontot och egenskaper.
* Konfigurera och hantera volymer eller resurser.
* Säkerhetskopiera och återställa data på volymer eller resurser.
* Övervaka prestanda.
* Granska inställningarna och identifierar möjliga problem.

Du kan använda StorSimple Device Manager-tjänsten för att utföra dagliga administrationen av den virtuella matrisen.

Mer information går du till [använda StorSimple Device Manager-tjänsten för att administrera din StorSimple-enhet](storsimple-virtual-array-manager-service-administration.md).

### <a name="local-web-user-interface"></a>Lokala webbgränssnitt

Den virtuella matrisen innehåller ett webbaserat gränssnitt som används för engångskonfiguration och registrering av enheten med StorSimple Device Manager-tjänsten. Du kan använda den för att stänga av och starta om den virtuella matrisen, kör diagnostiktest, uppdaterar programvara, ändra enhetens administratörslösenord, visa systemloggar och kontakta Microsoft Support om du vill skicka en serviceförfrågan.

Information om hur du använder webbaserat gränssnitt, går du till [använda webbaserat gränssnitt för att administrera din StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

### <a name="command-line-interface"></a>Kommandoradsgränssnitt

Det ingår Windows PowerShell-gränssnittet gör det möjligt för dig att starta en supportsession med Microsoft Support så att de kan hjälpa dig att felsöka och lösa problem som kan uppstå på din virtuella matris.

## <a name="storage-management-technologies"></a>Tekniker för hantering av lagring

Förutom att den virtuella matrisen och andra komponenter använder StorSimple-lösningen följande programvara tekniker för att ge snabb åtkomst till viktiga data, minska lagringsanvändningen och skydda data som lagras på din virtuella matris:

* [Automatisk lagringsnivåer](#automatic-storage-tiering) 
* [Lokalt fixerade resurserna och volymerna](#locally-pinned-shares-and-volumes)
* Avduplicering och komprimering för data nivåer eller har säkerhetskopierats till molnet 
* [Schemalagda säkerhetskopieringar och på begäran säkerhetskopieringar](#scheduled-and-on-demand-backups)

### <a name="automatic-storage-tiering"></a>Automatisk lagringsnivåer
Den virtuella matrisen använder en ny lagringsnivåer mekanism för att hantera lagrade data på den virtuella matrisen och i molnet. Det finns två nivåer: den lokala virtuella matrisen och Azure-molnlagring. StorSimple Virtual Array ordnar automatiskt data i nivåer, baserat på en termisk karta som spårar aktuell användning, ålder och relationer till andra data. Data som är mest aktiva (hetaste) lagras lokalt, medan mindre aktiva och inaktiva data automatiskt migreras till molnet. (Alla säkerhetskopior lagras i molnet.) StorSimple justerar och samlar data och lagring tilldelningar som användningsmönster ändras. Viss information kan till exempel bli mindre aktiva över tid. Så fort de blir progressivt mindre aktiva, är det nivåindelad ut till molnet. Om samma data blir aktiva igen, är det nivåindelad i till lagringsmatrisen.

Data för en viss nivåindelad resurs eller en volym är säkert sin egen lokala nivån utrymme (cirka 10% av det totala allokerade utrymmet för den resursen eller volymen). Visserligen detta minskar tillgängligt utrymme på den virtuella matrisen för den resursen eller volymen säkerställer att lagringsnivåer för en resursen eller volymen inte kommer att påverkas av lagringsnivåer behoven hos andra resurser eller volymer. Därför kan inte en hårt belastad arbetsbelastning på en resursen eller volymen tvinga andra arbetsbelastningar till molnet.

Nivåindelade volymer skapade för iSCSI har en maximal lokal reservation 200 GB oavsett storleken på volymen.

![Automatisk lagringsnivåer](./media/storsimple-ova-overview/automatic-storage-tiering.png)

> [!NOTE]
> Du kan ange en lokalt Fäst volym, i vilket fall data finns kvar på den virtuella matrisen och är aldrig nivåer till molnet. Mer information går du till [lokalt fixerade resurserna och volymerna](#locally-pinned-shares-and-volumes).


### <a name="locally-pinned-shares-and-volumes"></a>Lokalt fixerade resurserna och volymerna

Du kan skapa lämpliga resurser och som lokalt fixerade volymer. Den här funktionen säkerställer att data som krävs av kritiska program finns kvar i den virtuella matrisen och aldrig nivåer till molnet. Lokalt fixerade resurserna och volymerna har följande funktioner:

* Omfattas inte av molnet fördröjningar eller problem med nätverksanslutningen.
* De fortfarande dra nytta av StorSimple cloud säkerhetskopiering och katastrofåterställning återställningsfunktioner.

Du kan återställa en lokalt Fäst resursen eller volymen som nivåindelade eller en nivåindelad resurs eller volym som lokalt Fäst. 

Mer information om lokalt fixerade volymer går du till [använda StorSimple Device Manager-tjänsten för att hantera volymer](storsimple-virtual-array-manage-volumes.md).

### <a name="deduplication-and-compression-for-data-tiered-or-backed-up-to-the-cloud"></a>Avduplicering och komprimering för data nivåer eller har säkerhetskopierats till molnet

StorSimple använder deduplicering och data komprimering för att ytterligare minska utrymmeskraven i molnet. Deduplicering minskar det totala mängden data som lagras genom att eliminera redundans i lagrade datauppsättningen. När informationen ändras StorSimple ignorerar ej ändrade data och samlar in bara ändringarna. StorSimple minskar också mängden lagrade data genom att identifiera och ta bort dubblettinformation.

> [!NOTE]
> Data som lagras på den virtuella matrisen är inte deduplicerats eller komprimeras. Alla dedupliceringen och komprimering sker precis innan data skickas till molnet.

### <a name="scheduled-and-on-demand-backups"></a>Schemalagda säkerhetskopieringar och på begäran säkerhetskopieringar

StorSimple funktioner för dataskydd kan du skapa säkerhetskopieringar på begäran. Dessutom kan garanterar ett schema för säkerhetskopiering av standard att data säkerhetskopieras varje dag. Säkerhetskopior tas i form av inkrementella ögonblicksbilder som lagras i molnet. Ögonblicksbilder som registrerar bara ändringarna sedan den senaste säkerhetskopieringen, skapas och återställa snabbt. De här ögonblicksbilderna kan vara ytterst viktigt i katastrofåterställning eftersom de ersätter sekundära lagringssystem (till exempel bandsäkerhetskopiering) och att du kan återställa data till ditt datacenter eller till en annan om det behövs.

## <a name="managing-personal-information"></a>Hantera personlig information

StorSimple Device Manager för virtuell serie samlar in personlig information i två viktiga instanser:
 - Avisera användarinställningar där e-postadresserna för användarna är konfigurerade. Den här informationen kan tas bort av administratören. 
 - Användare som har åtkomst till data som finns på filresurser. En lista över användare som har åtkomst till resursdata visas och kan exporteras. Den här listan är också tas bort när filresurser tas bort.

Mer information finns i [Microsoft Privacy-policy på Säkerhetscenter](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [förbereda den virtuella matris portalen](storsimple-virtual-array-deploy1-portal-prep.md).
