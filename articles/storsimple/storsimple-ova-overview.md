---
title: Översikt över Microsoft Azure StorSimple Virtual Array
description: Beskriver StorSimple Virtual Array, en integrerad lagringslösning som hanterar lagringsuppgifter mellan en lokal virtuell matris och Microsoft Azure-molnlagring.
author: alkohli
ms.assetid: 169c639b-1124-46a5-ae69-ba9695525b77
ms.service: storsimple
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: 32781a83aec996b23f161f5fe695f39a0de38685
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76273873"
---
# <a name="introduction-to-the-storsimple-virtual-array"></a>Introduktion till StorSimples virtuella matris

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

## <a name="overview"></a>Översikt

Microsoft Azure StorSimple Virtual Array är en integrerad lagringslösning som hanterar lagringsuppgifter mellan en lokal virtuell matris som körs i en hypervisor- och Microsoft Azure-molnlagring. Den virtuella matrisen är en effektiv, kostnadseffektiv och enkelt hanterad filserver eller iSCSI-serverlösning som eliminerar många av de problem och utgifter som är förknippade med företagslagring och dataskydd. Den virtuella matrisen är särskilt väl lämpad för lagring av sällan använda arkivdata.

Den här artikeln innehåller en översikt över den virtuella matrisen – här är några andra resurser:

* Metodtips för bästa praxis finns i [metodtips för StorSimple Virtual Array](storsimple-ova-best-practices.md).
* En översikt över StorSimple 8000-seriens enheter finns i [StorSimple 8000-serien: en hybridmolnlösning](storsimple-overview.md).
* Information om StorSimple 5000/7000-seriens enheter finns i [StorSimple Online Help](http://onlinehelp.storsimple.com/).

Den virtuella matrisen stöder SMB-protokollet (iSCSI eller Server Message Block). Den körs på din befintliga hypervisor-infrastruktur och ger nivåindelning till molnet, säkerhetskopiering av molnet, snabb återställning, återställning på objektnivå och funktioner för haveriberedskap.

I följande tabell sammanfattas de viktiga funktionerna i StorSimple Virtual Array.

| Funktion | StorSimple virtuell matris |
| --- | --- |
| Installationskrav |Använder virtualiseringsinfrastruktur (Hyper-V eller VMware) |
| Tillgänglighet |Enkel nod |
| Total kapacitet (inklusive moln) |Upp till 64 TB användbar kapacitet per virtuell matris |
| Lokal kapacitet |390 GB till 6,4 TB användbar kapacitet per virtuell matris (måste etablera 500 GB till 8 TB diskutrymme) |
| Inbyggda protokoll |iSCSI eller SMB |
| Mål för återställningstid (RTO) |iSCSI: mindre än 2 minuter oavsett storlek |
| Mål för återställningspunkt (RPO) |Dagliga säkerhetskopieringar och säkerhetskopieringar på begäran |
| Lagringsnivåer |Använder värmemappning för att avgöra vilka data som ska nivåindelad in eller ut |
| Support |Infrastruktur för virtualisering som stöds av leverantören |
| Prestanda |Varierar beroende på underliggande infrastruktur |
| Datamobilitet |Kan återställa till samma enhet eller göra återställning på objektnivå (filserver) |
| Lagringsnivåer |Lokal hypervisorlagring och moln |
| Dela storlek |Nivåindelade: upp till 20 TB; lokalt fäst: upp till 2 TB |
| Volymstorlek |Nivåindelade: 500 GB till 5 TB; lokalt fäst: 50 GB till 200 GB <br> Maximal lokal reservation för nivåindelade volymer är 200 GB. |
| Ögonblicksbilder |Kraschkonsekvent |
| Återställning på objektnivå |Ja, det är jag. användare kan återställa från aktier |

## <a name="why-use-storsimple"></a>Varför använda StorSimple?

StorSimple ansluter användare och servrar till Azure-lagring på några minuter, utan programändringar.

I följande tabell beskrivs några av de viktigaste fördelarna som StorSimple Virtual Array-lösningen tillhandahåller.

| Funktion | Fördelar |
| --- | --- |
| Transparent integration |Den virtuella matrisen stöder iSCSI eller SMB-protokollet. Datarörelsen mellan den lokala nivån och molnnivån är sömlös och transparent för användaren. |
| Minskade lagringskostnader |Med StorSimple etablerar du tillräckligt med lokal lagring för att uppfylla aktuella krav på de mest använda frekventa data. I takt med att lagringsbehoven ökar, nivåer StorSimple kalldata till kostnadseffektiv molnlagring. Data dedupliceras och komprimeras innan de skickas till molnet för att ytterligare minska lagringskrav och kostnader. |
| Förenklad lagringshantering |StorSimple tillhandahåller centraliserad hantering i molnet med StorSimple Device Manager för att hantera flera enheter. |
| Förbättrad haveriberedskap och efterlevnad |StorSimple underlättar snabbare haveriberedskap genom att återställa metadata omedelbart och återställa data efter behov. Detta innebär att normal drift kan fortsätta med minimala störningar. |
| Datamobilitet |Data som nivåindelas till molnet kan nås från andra platser för återställnings- och migreringsändamål. Observera att du bara kan återställa data till den ursprungliga virtuella matrisen. Du kan dock använda funktioner för haveriberedskap för att återställa hela den virtuella matrisen till en annan virtuell matris. |

## <a name="storsimple-workload-summary"></a>Sammanfattning av StorSimple-arbetsbelastning

En sammanfattning av storsimplearbetsbelastningar som stöds tas i tabell nedan.

|Scenario     |Arbetsbelastning     |Stöds      |Begränsningar               | Versioner gäller|
|-------------|-------------|---------------|---------------------------|--------------------|
|Fjärrkontor/filialkontor (ROBO)  |Fildelning     |Ja      |Se [maxgränser för filserver .](storsimple-ova-limits.md)<br></br>Se [systemkrav för SMB-versioner som stöds](storsimple-ova-system-requirements.md).| Alla versioner     |
|Molnarkivering  |Fildelning av arkiv     |Ja      |Se [maxgränser för filserver .](storsimple-ova-limits.md)<br></br>Se [systemkrav för SMB-versioner som stöds](storsimple-ova-system-requirements.md).| Alla versioner     |

StorSimple Virtual Array passar bäst för sällan använda data. Medan den virtuella matrisen har en lokal cache för att öka prestanda, bör användarna anta att enheten tjänster filer på den lägsta lagringsnivån (molnet). Varje virtuell matris kan skriva och läsa till Azure-lagring med cirka 100 Mbps. Den länken delas över alla begäranden som kommer in i enheten och kan bli en flaskhals som visas i diagrammet nedan.

![Molnarkivering](./media/storsimple-ova-overview/cloud-archiving.png)

När flera samtidiga användare får åtkomst till den virtuella matrisen delar de alla anslutningen till Azure som leder till lägre prestanda. Det finns ingen garanterad prestanda per användare och enheten bearbetar enskilda begäranden när de anländer.

StorSimple Virtual Array är inte lämplig för arbetsbelastningar som kräver hög tillgänglighet. Den virtuella matrisen är en enda nodenhet som har driftstopp när programuppdateringar installeras. Administratörer bör planera för ett underhållsfönster på 30 minuter 3-4 gånger per år.

## <a name="workflows"></a>Arbetsflöden

StorSimple Virtual Array är särskilt lämplig för följande arbetsflöden:

* [Molnbaserad lagringshantering](#cloud-based-storage-management)
* [Platsoberoende säkerhetskopiering](#location-independent-backup)
* [Dataskydd och katastrofåterställning](#data-protection-and-disaster-recovery)

### <a name="cloud-based-storage-management"></a>Molnbaserad lagringshantering
Du kan använda Tjänsten StorSimple Device Manager som körs i Azure-portalen för att hantera data som lagras på flera enheter och på flera platser. Detta är särskilt användbart i distribuerade grenscenarier. Observera att du måste skapa separata instanser av Tjänsten StorSimple Device Manager för att hantera virtuella matriser och fysiska StorSimple-enheter. Observera också att den virtuella matrisen nu använder den nya Azure-portalen i stället för den klassiska Azure-portalen.

![molnbaserad lagringshantering](./media/storsimple-ova-overview/cloud-based-storage-management.png)

### <a name="location-independent-backup"></a>Platsoberoende säkerhetskopiering
Med den virtuella matrisen ger ögonblicksbilder i molnet en platsoberoende, punkt-i-tid-kopia av en volym eller resurs. Ögonblicksbilder av molnet är aktiverade som standard och kan inte inaktiveras. Alla volymer och resurser säkerhetskopieras samtidigt genom en enda daglig säkerhetskopieringsprincip, och du kan ta ytterligare ad hoc-säkerhetskopior när det behövs.

### <a name="data-protection-and-disaster-recovery"></a>Dataskydd och katastrofåterställning
Den virtuella matrisen stöder följande scenarier för dataskydd och katastrofåterställning:

* **Volym- eller delningsåterställning** – Använd återställningen som nytt arbetsflöde för att återställa en volym eller resurs. Använd den här metoden för att återställa hela volymen eller resursen.
* **Återställning på objektnivå** – Resurser ger förenklad åtkomst till de senaste säkerhetskopieringarna. Du kan enkelt återställa en enskild fil från en särskild *.backup-mapp* som är tillgänglig i molnet. Den här återställningsfunktionen är användardriven och ingen administrativ åtgärd krävs.
* **Haveriberedskap** – Använd redundansfunktionen för att återställa alla volymer eller resurser till en ny virtuell matris. Du skapar den nya virtuella matrisen och registrerar den med StorSimple Enhetshanteraren och växlar sedan över den ursprungliga virtuella matrisen. Den nya virtuella matrisen antar sedan de etablerade resurserna.

## <a name="storsimple-virtual-array-components"></a>StorSimple Virtual Array-komponenter

Den virtuella matrisen innehåller följande komponenter:

* [Virtuell matris](#virtual-array) – En hybridmolnlagringsenhet baserad på en virtuell dator som etablerats i din virtualiserade miljö eller hypervisor.
* [StorSimple Device Manager-tjänsten](#storsimple-device-manager-service) – Ett tillägg till Azure-portalen som gör att du kan hantera en eller flera StorSimple-enheter från ett enda webbgränssnitt som du kan komma åt från olika geografiska platser. Du kan använda Tjänsten StorSimple Device Manager för att skapa och hantera tjänster, visa och hantera enheter och aviseringar samt hantera volymer, resurser och befintliga ögonblicksbilder.
* [Lokalt webbanvändargränssnitt](#local-web-user-interface) – Ett webbaserat användargränssnitt som används för att konfigurera enheten så att den kan ansluta till det lokala nätverket och sedan registrera enheten med Tjänsten StorSimple Device Manager. 
* [Kommandoradsgränssnitt](#command-line-interface) – Ett Windows PowerShell-gränssnitt som du kan använda för att starta en supportsession på den virtuella matrisen.
  I följande avsnitt beskrivs var och en av dessa komponenter mer i detalj och hur lösningen ordnar data, allokerar lagring och underlättar lagringshantering och dataskydd.

### <a name="virtual-array"></a>Virtuell matris

Den virtuella matrisen är en lagringslösning för en nod som tillhandahåller primär lagring, hanterar kommunikation med molnlagring och bidrar till att säkerställa säkerhet och konfidentialitet för alla data som lagras på enheten.

Den virtuella matrisen är tillgänglig i en modell som är tillgänglig för hämtning. Den virtuella matrisen har en maximal kapacitet på 6,4 TB på enheten (med ett underliggande lagringskrav på 8 TB) och 64 TB inklusive molnlagring.

Den virtuella matrisen har följande funktioner:

* Det är kostnadseffektivt. Den använder din befintliga virtualiseringsinfrastruktur och kan distribueras på din befintliga Hyper-V eller VMware hypervisor.
* Den finns i datacentret och kan konfigureras som en iSCSI-server eller en filserver.
* Det är integrerat med molnet.
* Säkerhetskopior lagras i molnet, vilket kan underlätta haveriberedskap och förenkla återställning på objektnivå (ILR).
* Du kan installera uppdateringar på den virtuella matrisen, på samma sätt som du använder dem på en fysisk enhet.

> [!NOTE]
> Det går inte att expandera en virtuell matris. Därför är det viktigt att etablera lämplig lagring när du skapar den virtuella matrisen.

### <a name="storsimple-device-manager-service"></a>StorSimple Device Manager-tjänsten

Microsoft Azure StorSimple tillhandahåller ett webbaserat användargränssnitt, Tjänsten StorSimple Device Manager, som gör att du kan hantera StorSimple-lagring centralt. Du kan använda Tjänsten StorSimple Device Manager för att utföra följande uppgifter:

* Hantera flera StorSimple-virtuella matriser från en enda tjänst.
* Konfigurera och hantera säkerhetsinställningar för StorSimple Virtual Arrays. (Kryptering i molnet är beroende av Microsoft Azure API:er.)
* Konfigurera autentiseringsuppgifter och egenskaper för lagringskonto.
* Konfigurera och hantera volymer eller resurser.
* Säkerhetskopiera och återställa data på volymer eller resurser.
* Övervaka prestanda.
* Granska systeminställningarna och identifiera eventuella problem.

Du använder Tjänsten StorSimple Device Manager för att utföra daglig administration av den virtuella matrisen.

Mer information finns i [Använda Tjänsten StorSimple Device Manager för att administrera din StorSimple-enhet](storsimple-virtual-array-manager-service-administration.md).

### <a name="local-web-user-interface"></a>Lokalt webbanvändargränssnitt

Den virtuella matrisen innehåller ett webbaserat användargränssnitt som används för engångskonfiguration och registrering av enheten med Tjänsten StorSimple Device Manager. Du kan använda den för att stänga av och starta om den virtuella matrisen, köra diagnostiska tester, uppdatera programvara, ändra lösenord för enhetsadministratören, visa systemloggar och kontakta Microsoft Support för att lämna in en tjänstbegäran.

Information om hur du använder det webbaserade användargränssnittet finns i [Använda det webbaserade användargränssnittet för att administrera den virtuella storsimple-matrisen](storsimple-ova-web-ui-admin.md).

### <a name="command-line-interface"></a>Kommandoradsgränssnitt

Med det medföljande Windows PowerShell-gränssnittet kan du initiera en supportsession med Microsoft Support så att de kan hjälpa dig att felsöka och lösa problem som kan uppstå på den virtuella matrisen.

## <a name="storage-management-technologies"></a>Teknik för lagringshantering

Förutom den virtuella matrisen och andra komponenter använder StorSimple-lösningen följande programvaruteknik för att ge snabb åtkomst till viktiga data, minska lagringsförbrukningen och skydda data som lagras på din virtuella matris:

* [Automatisk lagringsnivå](#automatic-storage-tiering) 
* [Lokalt fästa resurser och volymer](#locally-pinned-shares-and-volumes)
* Deduplicering och komprimering för datanivåerade eller säkerhetskopierade till molnet 
* [Schemalagda och on-demand-säkerhetskopior](#scheduled-and-on-demand-backups)

### <a name="automatic-storage-tiering"></a>Automatisk lagringsnivå
Den virtuella matrisen använder en ny nivåindelningsmekanism för att hantera lagrade data över den virtuella matrisen och molnet. Det finns bara två nivåer: den lokala virtuella matrisen och Azure-molnlagring. StorSimple Virtual Array ordnar automatiskt data i nivåerna baserat på en värmekarta, som spårar aktuell användning, ålder och relationer till andra data. Data som är mest aktiva (hetaste) lagras lokalt, medan mindre aktiva och inaktiva data automatiskt migreras till molnet. (Alla säkerhetskopior lagras i molnet.) StorSimple justerar och ordnar om data- och lagringstilldelningar när användningsmönstren ändras. Viss information kan till exempel bli mindre aktiv med tiden. När den blir progressivt mindre aktiv, är det nivåindelade ut till molnet. Om samma data blir aktiva igen, är det nivåinerat i lagringsmatrisen.

Data för en viss nivåindelad resurs eller volym garanteras dess eget lokala nivåutrymme (cirka 10 % av det totala avvägda utrymmet för den resursen eller volymen). Även om detta minskar det tillgängliga lagringsutrymmet på den virtuella matrisen för den resursen eller volymen, säkerställer det att nivåindelning för en resurs eller volym inte påverkas av nivådelningsbehoven för andra resurser eller volymer. En mycket upptagen arbetsbelastning på en resurs eller volym kan därför inte tvinga alla andra arbetsbelastningar till molnet.

Nivåindelad volymer som skapats för iSCSI har en maximal lokal reservation på 200 GB oavsett volymens storlek.

![automatisk lagringsnivå](./media/storsimple-ova-overview/automatic-storage-tiering.png)

> [!NOTE]
> Du kan ange en volym som lokalt fäst, i vilket fall data finns kvar på den virtuella matrisen och aldrig nivåindelas till molnet. Mer information finns i [Lokalt fästa resurser och volymer](#locally-pinned-shares-and-volumes).


### <a name="locally-pinned-shares-and-volumes"></a>Lokalt fästa resurser och volymer

Du kan skapa lämpliga resurser och volymer som lokalt fästa. Den här funktionen säkerställer att data som krävs av kritiska program finns kvar i den virtuella matrisen och aldrig är nivåindelade till molnet. Lokalt fästa resurser och volymer har följande funktioner:

* De är inte föremål för moln svarstider eller anslutningsproblem.
* De drar fortfarande nytta av StorSimple-funktioner för säkerhetskopiering och haveriberedskap vid molnet.

Du kan återställa en lokalt fäst resurs eller volym som nivåindelad eller en nivåindelad resurs eller volym som lokalt fäst. 

Mer information om lokalt fästa volymer finns i [Använda Tjänsten StorSimple Device Manager för att hantera volymer](storsimple-virtual-array-manage-volumes.md).

### <a name="deduplication-and-compression-for-data-tiered-or-backed-up-to-the-cloud"></a>Deduplicering och komprimering för datanivåerade eller säkerhetskopierade till molnet

StorSimple använder deduplicering och datakomprimering för att ytterligare minska lagringskraven i molnet. Deduplicering minskar den totala mängden data som lagras genom att eliminera redundans i den lagrade datauppsättningen. När informationen ändras ignorerar StorSimple oförändrade data och samlar bara in ändringarna. Dessutom minskar StorSimple mängden lagrade data genom att identifiera och ta bort dubblettinformation.

> [!NOTE]
> Data som lagras på den virtuella matrisen är inte deduplicated eller komprimeras. All deduplicering och komprimering sker strax innan data skickas till molnet.

### <a name="scheduled-and-on-demand-backups"></a>Schemalagda och on-demand-säkerhetskopior

Med StorSimples dataskyddsfunktioner kan du skapa säkerhetskopieringar på begäran. Dessutom säkerställer ett standardschema för säkerhetskopiering att data säkerhetskopieras dagligen. Säkerhetskopior tas i form av inkrementella ögonblicksbilder, som lagras i molnet. Ögonblicksbilder, som bara registrerar ändringarna sedan den senaste säkerhetskopieringen, kan skapas och återställas snabbt. Dessa ögonblicksbilder kan vara mycket viktiga i katastrofåterställningsscenarier eftersom de ersätter sekundära lagringssystem (till exempel säkerhetskopiering av band) och gör att du kan återställa data till ditt datacenter eller till alternativa platser om det behövs.

## <a name="managing-personal-information"></a>Hantera personlig information

StorSimple Device Manager för virtuella serier samlar in personlig information i två viktiga instanser:
 - Avisera användarinställningar där e-postadresser till användare är konfigurerade. Den här informationen kan rensas av administratören. 
 - Användare som kan komma åt data som finns på resurserna. En lista över användare som kan komma åt delningsdata visas och kan exporteras. Den här listan tas också bort när resurserna tas bort.

Mer information finns i [Microsofts sekretesspolicy på Trust Center](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [förbereder den virtuella matrisportalen](storsimple-virtual-array-deploy1-portal-prep.md).
