---
title: StorSimple 8000-serien som mål för säkerhetskopian med Veeam | Microsoft Docs
description: Beskriver hur StorSimple mål för säkerhetskopian med Veeam.
services: storsimple
documentationcenter: ''
author: harshakirank
manager: matd
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2016
ms.author: hkanna
ms.openlocfilehash: cc1c7a3f77af76c451bb6e97a081a01c119333b5
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
ms.locfileid: "23877393"
---
# <a name="storsimple-as-a-backup-target-with-veeam"></a>StorSimple som ett mål med Veeam

## <a name="overview"></a>Översikt

Azure StorSimple är en cloud hybridlagringslösning från Microsoft. StorSimple adresser svårigheter av exponentiell datatillväxt med hjälp av Azure Storage-konto som ett tillägg för lösningen lokalt och automatiskt lagringsnivåer data över lokala lagring och lagringsutrymmet i molnet.

I den här artikeln tar vi upp StorSimple integrering med Veeam och bästa praxis för integrering av båda lösningarna. Vi kan också göra rekommendationer om hur du ställer in Veeam bäst integreras med StorSimple. Vi skjuter upp att Veeam bästa praxis, säkerhetskopiering arkitekter och administratörer för det bästa sättet att konfigurera Veeam för att uppfylla krav för enskilda säkerhetskopiering och servicenivåavtal (SLA).

Även om vi illustrerar konfigurationssteg och viktiga begrepp är i den här artikeln inte menat att en stegvis guide för konfiguration och installation. Vi förutsätter att den grundläggande komponenter och infrastruktur fungerar och är redo för att stödja de begrepp som beskrivs.

### <a name="who-should-read-this"></a>Vem ska läsa det här?

Informationen i den här artikeln är mest användbar för administratörer för säkerhetskopiering, lagringsadministratörer och lagring-arkitekter som känner till lagring, Windows Server 2012 R2, Ethernet, molntjänster och Veeam.

### <a name="supported-versions"></a>Versioner som stöds

-   Veeam 9 och senare versioner
-   [StorSimple uppdatering 3 och senare versioner](storsimple-overview.md#storsimple-workload-summary)


## <a name="why-storsimple-as-a-backup-target"></a>Varför StorSimple som mål för säkerhetskopian?

StorSimple är ett bra alternativ för ett mål för säkerhetskopian eftersom:

-   Det ger standard, lokal lagring för säkerhetskopiering program som ska användas som ett mål för snabb säkerhetskopiering, utan några ändringar. Du kan också använda StorSimple för en snabb återställning av nya säkerhetskopior.
-   Dess molnet skiktning har integrerats med ett Azure-molnet storage-konto för att använda kostnadseffektivt Azure Storage.
-   Tillhandahåller det automatiskt lagring på annan plats för katastrofåterställning.


## <a name="key-concepts"></a>Viktiga begrepp

Precis som med alla lagringslösning, en noggrann utvärdering av lösningens lagringsprestanda, SLA: er, är frekvensen för ändrings- och tillväxt kapacitetsbehov viktigt att lyckas. Den huvudsakliga idé är som genom att introducera molnnivån dina åtkomsttider och genomflöden till molnet play en grundläggande roll i StorSimple förmåga att utföra sitt jobb.

StorSimple är utformad för att ge lagring för program som körs på en väldefinierad arbetsminnet för data (varm data). I den här modellen arbetsminnet för data lagras på de lokala nivåerna och de återstående ledig/kall/arkiveras datamängder nivåer till molnet. Den här modellen visas i följande bild. Nästan platt grön raden representerar de data som lagras på de lokala nivåerna av StorSimple-enhet. Den röda linjen representerar den totala mängden data som lagras på StorSimple-lösningen över alla nivåer. Avståndet mellan flat gröna linjen och exponentiell red kurvan representerar den totala mängden data som lagras i molnet.

**StorSimple skiktning**
![StorSimple lagringsnivåer diagram](./media/storsimple-configure-backup-target-using-veeam/image1.jpg)

Du kommer märka att StorSimple är idealisk för fungerar som ett mål med den här arkitekturen i åtanke. Du kan använda StorSimple till:

-   Utföra dina mest frekventa återställningar från lokala arbetsminnet för data.
-   Använda molnet för externt katastrofåterställning och äldre data där återställningar är mer sällan.

## <a name="storsimple-benefits"></a>StorSimple fördelar

StorSimple är en lokal lösning som är sömlöst integrerad med Microsoft Azure genom att utnyttja sömlös åtkomst till lokala och molnlagring.

StorSimple använder automatisk skiktning mellan lokala enheten, vilket har SSD-enhet (SSD) och serieansluten SCSI (SAS) lagring och Azure Storage. Automatisk skiktning håller ofta använda data lokalt på SSD- och SAS-nivåer. Data som sällan används flyttas till Azure Storage.

StorSimple ger följande fördelar:

-   Unik deduplicering och komprimering algoritmer som använder molnet för att uppnå en oöverträffad deduplicering
-   Hög tillgänglighet
-   GEO-replikering med hjälp av Azure geo-replikering
-   Azure-integrering
-   Kryptering av data i molnet
-   Förbättrad återställning och efterlevnad

Även om StorSimple uppvisar grunden två huvudsakliga distributionsscenarier (mål för säkerhetskopian av primära och sekundära mål för säkerhetskopian), är en vanlig, blocklagringsserver. StorSimple har alla komprimering och deduplicering. Sömlöst skickar och hämtar data mellan molnet och program och filsystemet.

Läs mer om StorSimple [StorSimple 8000-serien: Hybrid cloud lagringslösning](storsimple-overview.md). Dessutom kan du granska den [tekniska specifikationer för StorSimple 8000-serien](storsimple-technical-specifications-and-compliance.md).

> [!IMPORTANT]
> Med hjälp av en StorSimple-enheten som ett mål stöds bara för StorSimple 8000 uppdatering 3 och senare versioner.

## <a name="architecture-overview"></a>Översikt över arkitekturen

I följande tabeller visas enheten modell-arkitektur inledande vägledning.

**StorSimple kapacitet för lokala och lagringsutrymmet i molnet**

| Lagringskapacitet | 8100 | 8600 |
|---|---|---|
| Lokal lagringskapacitet | &lt; 10 TiB\*  | &lt; 20 TiB\*  |
| Kapacitet för molnlagring | &gt; 200 TiB\* | &gt; 500 TiB\* |

\* Lagringsstorleken förutsätter inga deduplicering eller komprimering.

**StorSimple kapacitet för primära och sekundära säkerhetskopieringar**

| Säkerhetskopiering scenario  | Lokal lagringskapacitet  | Kapacitet för molnlagring  |
|---|---|---|
| Primär säkerhetskopiering  | Nya säkerhetskopior lokalt lagrade snabb återställning för att uppfylla återställningspunktmål (RPO) | Historik för säkerhetskopiering (RPO) passar i kapacitet |
| Sekundär säkerhetskopiering | Sekundär kopia av säkerhetskopierade data kan lagras i kapacitet  | Gäller inte  |

## <a name="storsimple-as-a-primary-backup-target"></a>StorSimple som en primär mål för säkerhetskopian

I det här scenariot visas StorSimple-volymer till säkerhetskopieringsprogrammet som en enda lagringsplats för säkerhetskopiering. Följande bild visar en lösningsarkitektur där alla säkerhetskopieringar används StorSimple nivåindelade volymer för säkerhetskopiering och återställning.

![StorSimple som ett logiskt diagram för primära mål för säkerhetskopian](./media/storsimple-configure-backup-target-using-veeam/primarybackuptargetlogicaldiagram.png)

### <a name="primary-target-backup-logical-steps"></a>Primära mål säkerhetskopiering logiska steg

1.  Backup-servern kontaktar mål backup-agenten och backup-agenten skickar data till backup-servern.
2.  Backup-servern skriver data till den virtuella StorSimple nivåindelade volymer.
3.  Backup-servern uppdaterar katalogdatabasen och sedan slutförs jobbet.
4.  Ett skript för ögonblicksbild utlöser StorSimple moln snapshot manager (start eller ta bort).
5.  Backup-servern tar bort utgångna säkerhetskopieringar utifrån en bevarandeprincip.

### <a name="primary-target-restore-logical-steps"></a>Primära mål återställning logiska steg

1.  Backup-servern startar återställer lämplig data från lagringsplatsen.
2.  Backup-agenten tar emot data från backup-servern.
3.  Backup-servern är klar återställningsjobbet.

## <a name="storsimple-as-a-secondary-backup-target"></a>StorSimple som en sekundär mål för säkerhetskopian

I det här scenariot används främst StorSimple-volymer för långsiktig kvarhållning eller arkivering.

Följande bild visar en arkitektur som inledande säkerhetskopior och återställer målvolymen hög prestanda. Dessa säkerhetskopior kopieras och arkiveras till ett StorSimple nivåer volym på ett schema.

Det är viktigt att ändra storlek högpresterande volymen så att den kan hantera din princip Kapacitets- och krav på datalagring.

![StorSimple som ett logiskt diagram sekundära mål för säkerhetskopian](./media/storsimple-configure-backup-target-using-veeam/secondarybackuptargetlogicaldiagram.png)

### <a name="secondary-target-backup-logical-steps"></a>Sekundär mål säkerhetskopiering logiska steg

1.  Backup-servern kontaktar mål backup-agenten och backup-agenten skickar data till backup-servern.
2.  Backup-servern skriver data till lagring med höga prestanda.
3.  Backup-servern uppdaterar katalogdatabasen och sedan slutförs jobbet.
4.  Backup-servern kopierar säkerhetskopieringar till StorSimple baserat på en bevarandeprincip.
5.  Ett skript för ögonblicksbild utlöser StorSimple moln snapshot manager (start eller ta bort).
6.  Backup-servern tar bort utgångna säkerhetskopieringar utifrån en bevarandeprincip.

### <a name="secondary-target-restore-logical-steps"></a>Sekundär mål återställning logiska steg

1.  Backup-servern startar återställer lämplig data från lagringsplatsen.
2.  Backup-agenten tar emot data från backup-servern.
3.  Backup-servern är klar återställningsjobbet.

## <a name="deploy-the-solution"></a>Distribuera lösningen

Distribuera lösningen kräver tre steg:

1. Förbered nätverkets infrastruktur.
2. Distribuera din StorSimple-enhet som mål för säkerhetskopian.
3. Distribuera Veeam.

Varje steg beskrivs i detalj i följande avsnitt.

### <a name="set-up-the-network"></a>Konfigurera nätverket

Eftersom StorSimple är en lösning som är integrerad med Azure-molnet, kräver StorSimple en aktiv och en fungerande anslutning till Azure-molnet. Den här anslutningen används för åtgärder som molnögonblicksbilder, hantering och överföring av metadata och äldre-nivån, mindre data används till Azure-molnlagring.

För lösningen ska fungera optimalt, rekommenderar vi att du följer dessa nätverk metodtips:

-   Den länk som ansluter din StorSimple skiktning till Azure måste uppfylla krav på bandbredd. Göra detta genom att använda den nödvändiga nivån för tjänstkvalitet (QoS) för infrastrukturen växlar för att matcha dina Återställningspunktmål och återställning tid mål för Återställningstid serviceavtal.
-   Maximal Azure Blob storage-åtkomstfördröjning ska vara runt 80 ms.

### <a name="deploy-storsimple"></a>Distribuera StorSimple

Stegvisa anvisningar för StorSimple-distribution, se [distribuera din lokala StorSimple-enhet](storsimple-deployment-walkthrough-u2.md).

### <a name="deploy-veeam"></a>Distribuera Veeam

Veeam Metodtips för installation, se [Veeam säkerhetskopiering och bästa praxis för replikering](https://bp.veeam.expert/), och läsa användarhandboken på [Veeam hjälp och support (teknisk dokumentation)](https://www.veeam.com/documentation-guides-datasheets.html).

## <a name="set-up-the-solution"></a>Konfigurera lösningen

I det här avsnittet visar vi några Konfigurationsexempel. Följande exempel och rekommendationer visar den mest grundläggande och grundläggande implementeringen. Den här implementeringen kan inte tillämpas direkt på dina specifika behov för säkerhetskopiering.

### <a name="set-up-storsimple"></a>Ställ in StorSimple

| Distributionsuppgifter för StorSimple  | Ytterligare kommentarer |
|---|---|
| Distribuera din lokala StorSimple-enhet. | Versioner som stöds: uppdatera 3 och senare versioner. |
| Aktivera målet för säkerhetskopian. | Använd dessa kommandon för att aktivera eller inaktivera mål för säkerhetskopian läge och för att hämta status. Mer information finns i [Anslut via en fjärranslutning till en StorSimple-enhet](storsimple-remote-connect.md).</br> Att aktivera säkerhetskopiering läge: `Set-HCSBackupApplianceMode -enable`. </br> Inaktivera säkerhetskopieringsläge: `Set-HCSBackupApplianceMode -disable`. </br> Att hämta aktuell status för inställningar för säkerhetskopiering: `Get-HCSBackupApplianceMode`. |
| Skapa en gemensam volymbehållare för volymen som lagrar säkerhetskopierade data. Alla data i en volymbehållare är deduplicerad. | Behållare för StorSimple-volym definiera deduplicering domäner.  |
| Skapa StorSimple-volymer. | Skapa volymer med storlekar som nära den förväntade användningen som möjligt, eftersom volymens storlek påverkar molnet ögonblicksbild varaktighet. För information om hur du kan ändra storlek på en volym, Läs mer om [bevarandeprinciper](#retention-policies).</br> </br> Använd StorSimple nivåindelade volymer och välj den **Använd volymen för mindre ofta använda arkiveringsdata** kryssrutan. </br> Endast lokalt fästa volymer stöds inte. |
| Skapa en unik StorSimple-säkerhetskopieringsprincip för alla volymer som mål för säkerhetskopian. | En princip för säkerhetskopiering av StorSimple definierar gruppen volym konsekvenskontroll. |
| Inaktivera schemat när ögonblicksbilder upphör. | Ögonblicksbilder utlöses som en efterbearbetning åtgärd. |

### <a name="set-up-the-host-backup-server-storage"></a>Konfigurera värd reservserver lagring

Konfigurera värd reservserver storage enligt dessa riktlinjer:  

- Använd inte volymer (skapas av Windows Diskhantering). Volymer stöds inte.
- Formatera volymerna med NTFS med 64 KB allokeringsenhetsstorlek.
- Mappa StorSimple-volymer direkt till Veeam-servern.
    - Använd iSCSI för fysiska servrar.


## <a name="best-practices-for-storsimple-and-veeam"></a>Metodtips för StorSimple och Veeam

Ställ in din lösning enligt riktlinjerna i följande avsnitten.

### <a name="operating-system-best-practices"></a>Metodtips för operativsystem

-   Inaktivera Windows Server-kryptering och deduplicering för NTFS-filsystemet.
-   Inaktivera Windows Server defragmentering på StorSimple-volymer.
-   Inaktivera Windows Server-indexering på StorSimple-volymer.
-   Köra antivirusprogram på källvärden (inte mot StorSimple-volymer).
-   Inaktivera standard [Windows serverunderhåll](https://msdn.microsoft.com/library/windows/desktop/hh848037.aspx) i Aktivitetshanteraren. Gör på något av följande sätt:
    - Inaktivera Underhåll konfiguratorn i Schemaläggaren i Windows.
    - Hämta [PsExec](https://technet.microsoft.com/sysinternals/bb897553.aspx) från Windows Sysinternals. När du har hämtat PsExec, kör du Windows PowerShell som administratör och skriv:
      ```powershell
      psexec \\%computername% -s schtasks /change /tn “MicrosoftWindowsTaskSchedulerMaintenance Configurator" /disable
      ```

### <a name="storsimple-best-practices"></a>Metodtips för StorSimple

-   Se till att StorSimple-enheten har uppdaterats till [Update 3 eller senare](storsimple-install-update-3.md).
-   Isolera iSCSI och molntrafik. Använd dedikerade iSCSI-anslutningar för trafik mellan StorSimple och backup-servern.
-   Se till att din StorSimple-enhet är en dedikerad mål för säkerhetskopian. Blandade arbetsbelastningar stöds inte eftersom de påverkar din RTO och Återställningspunktmål.

### <a name="veeam-best-practices"></a>Metodtips för Veeam

-   Veeam databasen bör vara lokala på servern och inte finns på en StorSimple-volym.
-   Säkerhetskopiering av databasen Veeam på en StorSimple-volym för katastrofåterställning.
-   Vi stöder Veeam fullständiga och inkrementella säkerhetskopior för den här lösningen. Vi rekommenderar att du inte använder syntetiska och differentiell säkerhetskopiering.
-   Säkerhetskopierade datafiler bör innehålla endast data för ett specifikt jobb. Till exempel lägger till något medium över olika jobb är tillåtna.
-   Inaktivera verifiering av jobbet. Om det behövs ska verifiering schemaläggas efter det senaste jobbet. Det är viktigt att förstå att det här jobbet påverkar säkerhetskopieringsfönstret.
-   Aktivera före allokering av media.
-   Se till parallell bearbetning är aktiverat.
-   Stäng av komprimering.
-   Stäng av deduplicering för säkerhetskopieringsjobbet.
-   Ange optimering **LAN mål**.
-   Aktivera **skapa active fullständig säkerhetskopiering** (varannan vecka).
-   Ställ in på den säkerhetskopierade databasen **använder per VM säkerhetskopior**.
-   Ange **använder flera överför strömmar per jobb** till **8** (högst 16 tillåts). Justera det här antalet uppåt eller nedåt baserat på processoranvändningen på StorSimple-enheten.

## <a name="retention-policies"></a>Principer för kvarhållning

En av de vanligaste principer för lagring av säkerhetskopior är en princip för farfar, pappa och Son offentlig sektor (GFS). I en GFS princip en inkrementell säkerhetskopiering utförs dagligen och fullständiga säkerhetskopieringar är klar vecka och månad. Den här principen resulterar i sex StorSimple nivåindelade volymer: en volym som innehåller de varje vecka, månad och år fullständig säkerhetskopieringarna. de fem volymerna lagrar dagliga inkrementella säkerhetskopieringar.

Vi använder en GFS rotation i följande exempel. I exemplet förutsätter vi följande:

-   Icke-deduplicerade eller komprimerade data används.
-   Fullständiga säkerhetskopieringar är 1 TiB.
-   Dagliga säkerhetskopior är 500 GiB.
-   Fyra veckovisa säkerhetskopior bevaras under en månad.
-   Tolv månatliga säkerhetskopior hålls för ett år.
-   En årlig säkerhetskopiering sparas för 10 år.

Baserat på föregående antaganden kan du skapa en 26 TiB StorSimple nivåer volymen för en fullständig säkerhetskopiering varje månad och år. Skapa en 5 TiB StorSimple nivåer volymen för var och en för de dagliga säkerhetskopiorna.

| Typ av säkerhetskopiering kvarhållning | Storlek (TiB) | Multiplikatorn GFS\* | Total kapacitet (TiB)  |
|---|---|---|---|
| Varje vecka fullständig | 1 | 4  | 4 |
| Dagliga inkrementella | 0.5 | 20 (cykler lika antal veckor per månad) | 12 (2 för ytterligare kvoten) |
| Månatliga fullständig | 1 | 12 | 12 |
| Årlig fullständig | 1  | 10 | 10 |
| GFS krav |   | 38 |   |
| Ytterligare kvot  | 4  |   | 42 totala GFS krav  |
\* Multiplikatorn GFS är antalet kopior måste du skydda och behålla så att de uppfyller dina krav för princip för säkerhetskopiering.

## <a name="set-up-veeam-storage"></a>Konfigurera Veeam lagring

### <a name="to-set-up-veeam-storage"></a>Att ställa in Veeam lagring

1.  I konsolen Veeam säkerhetskopiering och replikering i **databasen verktyg**, gå till **säkerhetskopiering infrastruktur**. Högerklicka på **säkerhetskopiering databaser**, och välj sedan **Lägg till säkerhetskopiering databas**.

    ![Säkerhetskopiera databasen sida i hanteringskonsolen Veeam](./media/storsimple-configure-backup-target-using-veeam/veeamimage1.png)

2.  I den **nya säkerhetskopiering databasen** dialogrutan Ange ett namn och beskrivning för databasen. Välj **Nästa**.

    ![Veeam konsolen, namn och beskrivning av sidan för hantering](./media/storsimple-configure-backup-target-using-veeam/veeamimage2.png)

3.  Typ, Välj **Microsoft Windows server**. Välj den Veeam-servern. Välj **Nästa**.

    ![Välj typ av säkerhetskopiering databasen i hanteringskonsolen Veeam](./media/storsimple-configure-backup-target-using-veeam/veeamimage3.png)

4.  Ange **plats**, bläddra och välja volym. Välj den **maximala samtidiga uppgifter för att begränsa:** kryssrutan och ange värdet **4**. Detta säkerställer att endast fyra virtuella diskar som bearbetas samtidigt medan varje virtuell dator (VM) bearbetas. Välj den **Avancerat** knappen.

    ![Välj volymen i hanteringskonsolen Veeam](./media/storsimple-configure-backup-target-using-veeam/veeamimage4.png)


5.  I den **lagring kompatibilitetsinställningar** väljer den **använder per VM säkerhetskopior** kryssrutan.

    ![Veeam konsolen för hantering av kompatibilitetsinställningar för lagring](./media/storsimple-configure-backup-target-using-veeam/veeamimage5.png)

6.  I den **nya säkerhetskopiering databasen** dialogrutan markerar du den **aktivera vPower NFS-tjänsten på servern montera (rekommenderas)** kryssrutan. Välj **Nästa**.

    ![Säkerhetskopiera databasen sida i hanteringskonsolen Veeam](./media/storsimple-configure-backup-target-using-veeam/veeamimage6.png)

7.  Granska inställningarna och välj sedan **nästa**.

    ![Säkerhetskopiera databasen sida i hanteringskonsolen Veeam](./media/storsimple-configure-backup-target-using-veeam/veeamimage7.png)

    En databas läggs till Veeam-servern.

## <a name="set-up-storsimple-as-a-primary-backup-target"></a>Ställa in StorSimple som en primär mål för säkerhetskopian

> [!IMPORTANT]
> Återställning av data från en säkerhetskopia som har varit nivåindelade till molnet inträffar hastigheter molnet.

Följande bild visar mappningen av en typisk volym till en säkerhetskopiering. I det här fallet alla veckovisa säkerhetskopior mappas till lördag fullständig disken och inkrementella säkerhetskopieringar mappa till måndag-fredag inkrementell diskar. Alla säkerhetskopior och återställer från en StorSimple nivåer volym.

![Primära mål för säkerhetskopian configuration logiskt diagram](./media/storsimple-configure-backup-target-using-veeam/primarybackuptargetdiagram.png)

### <a name="storsimple-as-a-primary-backup-target-gfs-schedule-example"></a>StorSimple som mål för primär säkerhetskopiering GFS schemalägga exempel

Här är ett exempel på ett GFS rotationsschema för fyra veckor, månatliga och årliga:

| Typ av frekvens/säkerhetskopiering | Fullständig | Stegvis (1-5 dagar)  |   
|---|---|---|
| Varje vecka (1 – 4 veckor) | Lördag | Monday-Friday |
| Månadsvis  | Lördag  |   |
| Varje år | Lördag  |   |   |


### <a name="assign-storsimple-volumes-to-a-veeam-backup-job"></a>Tilldela ett säkerhetskopieringsjobb Veeam StorSimple-volymer

Skapa dagliga jobb för primära mål för säkerhetskopian scenariot med din primära Veeam StorSimple-volym. Skapa ett dagliga jobb med hjälp av direkt ansluten lagring (DAS), nätverksansluten lagring (NAS) eller bara en grupp av diskar JBOD-lagring för ett scenario med en sekundär mål för säkerhetskopian.

#### <a name="to-assign-storsimple-volumes-to-a-veeam-backup-job"></a>Att tilldela ett säkerhetskopieringsjobb Veeam StorSimple-volymer

1.  I konsolen Veeam säkerhetskopiering och replikering, Välj **för säkerhetskopiering och replikering**. Högerklicka på **säkerhetskopiering**, och välj sedan **VMware** eller **Hyper-V**, beroende på din miljö.

    ![Nytt säkerhetskopieringsjobb i hanteringskonsolen Veeam](./media/storsimple-configure-backup-target-using-veeam/veeamimage8.png)

2.  I den **nytt säkerhetskopieringsjobb i gång** dialogrutan Ange ett namn och beskrivning för daglig säkerhetskopiering.

    ![Veeam konsolen för hantering av ny säkerhetskopieringsjobbet sida](./media/storsimple-configure-backup-target-using-veeam/veeamimage9.png)

3.  Välj en virtuell dator för säkerhetskopieringen.

    ![Veeam konsolen för hantering av ny säkerhetskopieringsjobbet sida](./media/storsimple-configure-backup-target-using-veeam/veeamimage10.png)

4.  Markera de värden som du vill använda för **säkerhetskopiera proxy** och **säkerhetskopiering databasen**. Välj ett värde för **återställningspunkter ska finnas kvar på disken** enligt Återställningspunktmål och Återställningstidsmål definitioner för din miljö på lokalt anslutna lagring. Välj **avancerade**.

    ![Veeam konsolen för hantering av ny säkerhetskopieringsjobbet sida](./media/storsimple-configure-backup-target-using-veeam/veeamimage11.png)

5. I den **avancerade inställningar** dialogrutan den **säkerhetskopiering** väljer **stegvis**. Kontrollera att den **skapa syntetiska fullständiga säkerhetskopieringar regelbundet** kryssrutan är avmarkerad. Välj den **skapa active fullständiga säkerhetskopieringar regelbundet** kryssrutan. Under **Active fullständig säkerhetskopiering**, Välj den **varje vecka på valda dagar** kryssrutan för lördag.

    ![Veeam hanteringskonsolen nytt säkerhetskopieringsjobb i gång avancerade inställningar](./media/storsimple-configure-backup-target-using-veeam/veeamimage12.png)

6. På den **lagring** kontrollerar du att som den **aktivera infogad datadeduplicering** kryssrutan är avmarkerad. Välj den **utelämna växlingen filblocken** kryssrutan och välj den **utelämna bort filblocken** kryssrutan. Ange **komprimeringsnivå** till **ingen**. Ange för belastningsutjämnade prestanda och deduplicering **lagringsoptimering** till **LAN mål**. Välj **OK**.

    ![Veeam hanteringskonsolen nytt säkerhetskopieringsjobb i gång avancerade inställningar](./media/storsimple-configure-backup-target-using-veeam/veeamimage13.png)

    Information om inställningar för komprimering och deduplicering av Veeam finns [datakomprimering och Deduplicering](https://helpcenter.veeam.com/backup/vsphere/compression_deduplication.html).

7.  I den **redigera säkerhetskopieringsjobb** dialogrutan kan du välja den **aktivera Programmedveten bearbetning** kryssrutan (valfritt).

    ![Veeam konsolen för hantering av ny säkerhetskopieringsjobbet gäst bearbetning sida](./media/storsimple-configure-backup-target-using-veeam/veeamimage14.png)

8.  Ange ett schema för att köra när varje dag, vid en tidpunkt som du kan ange.

    ![Veeam konsolen för hantering av ny säkerhetskopieringsjobbet schema sida](./media/storsimple-configure-backup-target-using-veeam/veeamimage15.png)

## <a name="set-up-storsimple-as-a-secondary-backup-target"></a>Ställa in StorSimple som en sekundär mål för säkerhetskopian

> [!NOTE]
> Data återställs från en säkerhetskopia som har varit nivåindelade till molnet uppstå hastigheter molnet.

I den här modellen måste du ha ett lagringsmedium (andra än StorSimple) som fungerar som en tillfällig cache. Du kan till exempel använda ett redundant matris av oberoende diskar (RAID) volymen för att passa utrymme, indata/utdata (I/O) och bandbredd. Vi rekommenderar att du använder RAID 5, 50 och 10.

Följande bild visar vanliga kortsiktig kvarhållning lokala (till servern) volymer och långsiktig kvarhållning Arkiv volymer. I det här scenariot kör alla säkerhetskopior på den lokala (på servern) RAID-volymen. Dessa säkerhetskopior är regelbundet dubbletter och arkiveras till en volym som Arkiv. Det är viktigt att anpassa din lokala (på servern) RAID-volym så att den kan hantera ditt kortsiktiga krav på datalagring kapacitet och prestanda.

![StorSimple som sekundär mål för säkerhetskopian logiskt diagram](./media/storsimple-configure-backup-target-using-veeam/secondarybackuptargetdiagram.png)

### <a name="storsimple-as-a-secondary-backup-target-gfs-example"></a>StorSimple som en sekundär mål för säkerhetskopian GFS-exempel

Följande tabell visar hur du konfigurerar säkerhetskopieringar att köras på den lokala och StorSimple-diskar. Den innehåller enskilda och totala kapacitetsbehov.

| Typ av säkerhetskopiering och lagring | Konfigurerad lagring | Storlek (TiB) | Multiplikatorn GFS | Total kapacitet\* (TiB) |
|---|---|---|---|---|
| Vecka 1 (fullständiga och inkrementella) |Lokal disk (kortsiktig)| 1 | 1 | 1 |
| StorSimple veckor 2-4 |StorSimple disk (långsiktiga) | 1 | 4 | 4 |
| Månatliga fullständig |StorSimple disk (långsiktiga) | 1 | 12 | 12 |
| Årlig fullständig |StorSimple disk (långsiktiga) | 1 | 1 | 1 |
|Kravet GFS volymer |  |  |  | 18*|
\* Total kapacitet innehåller 17 TiB StorSimple-diskar och 1 TiB lokal RAID-volym.


### <a name="gfs-example-schedule"></a>GFS exempel schema

GFS rotation schema för varje vecka, månad och år

| Vecka | Fullständig | Inkrementell dag 1 | Inkrementell dag 2 | Inkrementell dag 3 | Inkrementell dag 4 | Inkrementell dag 5 |
|---|---|---|---|---|---|---|
| Vecka 1 | Lokal RAID-volym  | Lokal RAID-volym | Lokal RAID-volym | Lokal RAID-volym | Lokal RAID-volym | Lokal RAID-volym |
| Vecka 2 | StorSimple veckor 2-4 |   |   |   |   |   |
| Vecka 3 | StorSimple veckor 2-4 |   |   |   |   |   |
| Vecka 4 | StorSimple veckor 2-4 |   |   |   |   |   |
| Månadsvis | StorSimple varje månad |   |   |   |   |   |
| Varje år | StorSimple varje år  |   |   |   |   |   |   |

### <a name="assign-storsimple-volumes-to-a-veeam-copy-job"></a>Tilldela en Veeam kopieringsjobbet StorSimple-volymer

#### <a name="to-assign-storsimple-volumes-to-a-veeam-copy-job"></a>Att tilldela en Veeam kopieringsjobbet StorSimple-volymer

1.  I konsolen Veeam säkerhetskopiering och replikering, Välj **för säkerhetskopiering och replikering**. Högerklicka på **säkerhetskopiering**, och välj sedan **VMware** eller **Hyper-V**, beroende på din miljö.

    ![Veeam konsolen för hantering av ny säkerhetskopia jobbet sida](./media/storsimple-configure-backup-target-using-veeam/veeamimage16.png)

2.  I den **nytt säkerhetskopieringsjobb kopiera** dialogrutan Ange ett namn och beskrivning för jobbet.

    ![Veeam konsolen för hantering av ny säkerhetskopia jobbet sida](./media/storsimple-configure-backup-target-using-veeam/veeamimage17.png)

3.  Välj de virtuella datorerna som du vill bearbeta. Välj från säkerhetskopior och välj sedan den dagliga säkerhetskopieringen som du skapade tidigare.

    ![Veeam konsolen för hantering av ny säkerhetskopia jobbet sida](./media/storsimple-configure-backup-target-using-veeam/veeamimage18.png)

4.  Uteslut objekt från säkerhetskopia jobb, om det behövs.

5.  Välj lagringsplatsen för säkerhetskopiering och ange ett värde för **återställningspunkter att hålla**. Se till att välja den **Tänk på följande återställning för arkiveringsändamål** kryssrutan. Definiera säkerhetskopieringsfrekvensen och välj sedan **Avancerat**.

    ![Veeam konsolen för hantering av ny säkerhetskopia jobbet sida](./media/storsimple-configure-backup-target-using-veeam/veeamimage19.png)

6.  Ange följande avancerade inställningar:

    * På den **Underhåll** fliken, Stäng av lagring nivån skadade guard.

    ![Veeam hanteringskonsolen nytt säkerhetskopia jobb avancerade inställningar](./media/storsimple-configure-backup-target-using-veeam/veeamimage20.png)

    * På den **lagring** fliken, se till att deduplicering och komprimering inaktiveras.

    ![Veeam hanteringskonsolen nytt säkerhetskopia jobb avancerade inställningar](./media/storsimple-configure-backup-target-using-veeam/veeamimage21.png)

7.  Ange att direkt överföra data.

8.  Definiera säkerhetskopia fönstret schemat efter behov och slutför sedan guiden.

Mer information finns i [Skapa säkerhetskopia jobb](https://helpcenter.veeam.com/backup/hyperv/backup_copy_create.html).

## <a name="storsimple-cloud-snapshots"></a>StorSimple molnögonblicksbilder

StorSimple molnögonblicksbilder skydda data som finns i din StorSimple-enhet. Skapa en ögonblicksbild i molnet motsvarar leverans lokala band och en annan plats. Om du använder Azure geo-redundant lagring, motsvarar skapa en ögonblicksbild i molnet leverans band till flera platser. Om du behöver återställa en enhet efter en katastrof kan du använda en annan StorSimple enhet online och gör en redundansväxling. Efter växling vid fel, skulle du kunna komma åt data (hastigheter moln) från den senaste ögonblicksbilden i molnet.

I följande avsnitt beskrivs hur du skapar ett kort skript för att starta och ta bort ögonblicksbilder för StorSimple-moln under säkerhetskopiering efter bearbetning.

> [!NOTE]
> Ögonblicksbilder som manuellt eller programmässigt skapas följer inte förfalloprincipen för StorSimple snapshot. Dessa ögonblicksbilder raderas manuellt eller programmässigt.

### <a name="start-and-delete-cloud-snapshots-by-using-a-script"></a>Starta och ta bort molnögonblicksbilder med hjälp av ett skript

> [!NOTE]
> Utvärdera noggrant för efterlevnad och konsekvenser för kvarhållning av data innan du tar bort en StorSimple-ögonblicksbild. Mer information om hur du kör ett skript för efter säkerhetskopieringen, finns i dokumentationen för Veeam.


### <a name="backup-lifecycle"></a>Livscykeln för säkerhetskopiering

![Diagram över livscykeln för säkerhetskopiering](./media/storsimple-configure-backup-target-using-veeam/backuplifecycle.png)

### <a name="requirements"></a>Krav

-   Den server som kör skriptet måste ha åtkomst till resurser i Azure-molnet.
-   Användarkontot måste ha behörighet.
-   En princip för StorSimple-säkerhetskopiering med associerade StorSimple-volymer måste ställa in men inte aktiverad.
-   Du behöver StorSimple resursnamnet, registreringsnyckel, namn och en säkerhetskopiering princip-enhets-ID.

### <a name="to-start-or-delete-a-cloud-snapshot"></a>Starta eller ta bort en ögonblicksbild i molnet

1. [Installera Azure PowerShell](/powershell/azure/overview).
2. Hämta och installera [hantera CloudSnapshots.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Manage-CloudSnapshots.ps1) PowerShell-skript.
3. Kör PowerShell som administratör på den server som kör skriptet. Se till att du kör skriptet med `-WhatIf $true` att se vilka ändringar skriptet gör. När verifieringen är klar kan du skicka `-WhatIf $false`. Kör den nedan kommando:
```powershell
.\Manage-CloudSnapshots.ps1 -SubscriptionId [Subscription Id] -TenantId [Tenant ID] -ResourceGroupName [Resource Group Name] -ManagerName [StorSimple Device Manager Name] -DeviceName [device name] -BackupPolicyName [backup policyname] -RetentionInDays [Retention days] -WhatIf [$true or $false]
```
4. Avancerade alternativ om du vill lägga till skriptet säkerhetskopieringsjobbet, redigera Veeam-jobb.

    ![Veeam avancerade inställningar för säkerhetskopiering skript fliken](./media/storsimple-configure-backup-target-using-veeam/veeamimage22.png)

Vi rekommenderar att du kör din StorSimple moln ögonblicksbild säkerhetskopieringsprincip som ett skript för efterbearbetning i slutet av det dagliga säkerhetskopieringsjobbet. Mer information om hur du säkerhetskopierar och återställer säkerhetskopieringsprogram miljön som hjälper dig att uppfylla dina Återställningspunktmål och Återställningstidsmål finns med dina säkerhetskopierade systemarkitekter.

## <a name="storsimple-as-a-restore-source"></a>StorSimple som en källa för återställning

Återställer från en StorSimple-enhet fungerar som återställer från valfri enhet för lagring av block. Återställning av data som är nivåer till molnet inträffar hastigheter molnet. För lokala data finnas återställer på lokal diskhastigheten på enheten.

Med Veeam får du snabb, detaljerade och filnivå recovery via StorSimple via inbyggda explorer-vyer i Veeam-konsolen. Använd den Veeam olika utforskarfönster för att återställa enskilda objekt, t.ex. e-postmeddelanden, Active Directory-objekt och SharePoint-objekt från säkerhetskopior. Återställningen kan göras utan lokala VM avbrott. Du kan också göra point-in-time-återställning för Azure SQL Database och Oracle-databaser. Veeam och StorSimple gör processen för återställning på objektnivå från Azure snabbt och enkelt. Information om hur du utför en återställning finns i dokumentationen för Veeam:

- För [Exchange Server](https://www.veeam.com/microsoft-exchange-recovery.html)
- För [Active Directory](https://www.veeam.com/microsoft-active-directory-explorer.html)
- För [SQLServer](https://www.veeam.com/microsoft-sql-server-explorer.html)
- För [SharePoint](https://www.veeam.com/microsoft-sharepoint-recovery-explorer.html)
- För [Oracle](https://www.veeam.com/oracle-backup-recovery-explorer.html)


## <a name="storsimple-failover-and-disaster-recovery"></a>StorSimple redundans och disaster recovery

> [!NOTE]
> Mål för säkerhetskopian scenarier stöds inte StorSimple moln installation som ett mål för återställning.

En katastrof kan orsakas av olika faktorer. I följande tabell visas vanliga disaster recovery-scenarier.

| Scenario | Påverkan | Så här återställer du | Anteckningar |
|---|---|---|---|
| Fel för StorSimple-enhet | Åtgärder för säkerhetskopiering och återställning avbryts. | Ersätt misslyckade enheten och utföra [StorSimple redundans och disaster recovery](storsimple-device-failover-disaster-recovery.md). | Om du behöver utföra en återställning efter återställning av enheten hämtas arbetsminnet fullständiga data från molnet till den nya enheten. Alla åtgärder är hastigheter molnet. Index och katalogen körs process kan leda till alla säkerhetskopior som ska genomsökas och hämtas från molnnivån på nivån för lokala enheter som kan vara en tidskrävande process. |
| Veeam-serverfel | Åtgärder för säkerhetskopiering och återställning avbryts. | Återskapa backup-servern och utföra återställning av databasen enligt anvisningarna i [Veeam hjälp och support (teknisk dokumentation)](https://www.veeam.com/documentation-guides-datasheets.html).  | Du måste återskapa eller återställa Veeam servern på återställningsplatsen för katastrofåterställning. Återställa databasen till den senaste tidpunkten. Om den återställda databasen Veeam inte är synkroniserad med din senaste säkerhetskopieringsjobb krävs indexering och katalogiserar. Den här index och katalogen som processen körs kan orsaka alla säkerhetskopior som ska genomsökas och hämtas från molnnivån på nivån för lokala enheter. Detta gör det mer tidskrävande. |
| Platsfel som leder till förlust av både reservserver och StorSimple | Åtgärder för säkerhetskopiering och återställning avbryts. | Återställ StorSimple först och sedan återställa Veeam. | Återställ StorSimple först och sedan återställa Veeam. Om du behöver utföra en återställning efter återställning av enheten hämtas arbetsminnet fullständiga data från molnet till den nya enheten. Alla åtgärder är hastigheter molnet. |


## <a name="references"></a>Referenser

Följande dokument har referenser till den här artikeln:

- [StorSimple multipath i/o-installationen](storsimple-configure-mpio-windows-server.md)
- [Scenarier för lagring: tunn allokering](http://msdn.microsoft.com/library/windows/hardware/dn265487.aspx)
- [Använda GPT-enheter](http://msdn.microsoft.com/windows/hardware/gg463524.aspx#EHD)
- [Konfigurera skuggkopior för delade mappar](http://technet.microsoft.com/library/cc771893.aspx)

## <a name="next-steps"></a>Nästa steg

- Mer information om hur du [återställning från en säkerhetskopia](storsimple-restore-from-backup-set-u2.md).
- Mer information om hur du utför [enheten redundans och disaster recovery](storsimple-device-failover-disaster-recovery.md).
