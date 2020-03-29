---
title: StorSimple 8000-serien som backup mål med Veeam | Microsoft-dokument
description: Beskriver målkonfigurationen för Säkerhetskopiering av StorSimple med Veeam.
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
ms.author: matd
ms.openlocfilehash: 3ebf464fed1480e7452f246f04f3906faf0dd219
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67875308"
---
# <a name="storsimple-as-a-backup-target-with-veeam"></a>StorSimple som ett säkerhetskopieringsmål med Veeam

## <a name="overview"></a>Översikt

Azure StorSimple är en hybridmolnlagringslösning från Microsoft. StorSimple tar itu med komplexiteten i exponentiell datatillväxt genom att använda ett Azure Storage-konto som ett tillägg av den lokala lösningen och automatiskt nivåindelningsdata över lokal lagring och molnlagring.

I den här artikeln diskuterar vi StorSimple-integrering med Veeam och metodtips för att integrera båda lösningarna. Vi ger också rekommendationer om hur du ställer in Veeam för att på bästa sätt integrera med StorSimple. Vi skjuter upp till Veeams bästa praxis, säkerhetskopieringsarkitekter och administratörer för det bästa sättet att konfigurera Veeam för att uppfylla individuella säkerhetskopieringskrav och servicenivåavtal (SLA).

Även om vi illustrerar konfigurationssteg och nyckelbegrepp är den här artikeln inte på något sätt en steg-för-steg-konfiguration eller installationsguide. Vi utgår från att de grundläggande komponenterna och infrastrukturen är i funktionsdugligt skick och redo att stödja de begrepp som vi beskriver.

### <a name="who-should-read-this"></a>Vem ska läsa det här?

Informationen i den här artikeln är till stor hjälp för administratörer, lagringsadministratörer och lagringsarkitekter som har kunskap om lagring, Windows Server 2012 R2, Ethernet, molntjänster och Veeam.

### <a name="supported-versions"></a>Versioner som stöds

-   Veeam 9 och senare versioner
-   [StorSimple Update 3 och senare versioner](storsimple-overview.md#storsimple-workload-summary)


## <a name="why-storsimple-as-a-backup-target"></a>Varför StorSimple som ett reservmål?

StorSimple är ett bra val för ett säkerhetskopieringsmål eftersom:

-   Det ger standard, lokal lagring för säkerhetskopieringsprogram att använda som en snabb säkerhetskopiering destination, utan några ändringar. Du kan också använda StorSimple för en snabb återställning av de senaste säkerhetskopiorna.
-   Dess molnnivådelning är sömlöst integrerad med ett Azure-molnlagringskonto för att använda kostnadseffektiv Azure Storage.
-   Det ger automatiskt offsite lagring för haveriberedskap.


## <a name="key-concepts"></a>Viktiga begrepp

Som med alla lagringslösningar är en noggrann bedömning av lösningens lagringsprestanda, SLA, förändringstakt och kapacitetstillväxtbehov avgörande för att lyckas. Huvudtanken är att genom att införa en molnnivå spelar dina åtkomsttider och dataflöde till molnet en grundläggande roll för StorSimples förmåga att göra sitt jobb.

StorSimple är utformat för att tillhandahålla lagring till program som arbetar på en väldefinierad arbetsuppsättning med data (heta data). I den här modellen lagras arbetsuppsättningen med data på de lokala nivåerna och den återstående icke-arbetande/kalla/arkiverade uppsättningen data är nivåindelade till molnet. Denna modell representeras i följande figur. Den nästan platta gröna linjen representerar de data som lagras på de lokala nivåerna i StorSimple-enheten. Den röda linjen representerar den totala mängden data som lagras på StorSimple-lösningen på alla nivåer. Avståndet mellan den plana gröna linjen och den exponentiella röda kurvan representerar den totala mängden data som lagras i molnet.

**StorSimple-nivådelning StorSimple-nivådelningsdiagram**![
](./media/storsimple-configure-backup-target-using-veeam/image1.jpg)

Med denna arkitektur i åtanke kommer du att upptäcka att StorSimple är idealisk för att fungera som ett säkerhetskopieringsmål. Du kan använda StorSimple för att:

-   Utför de vanligaste återställningarna från den lokala arbetsuppsättningen med data.
-   Använd molnet för katastrofåterställning utanför platsen och äldre data, där återställningar är mindre frekventa.

## <a name="storsimple-benefits"></a>StorSimple fördelar

StorSimple tillhandahåller en lokal lösning som är sömlöst integrerad med Microsoft Azure, genom att dra nytta av sömlös åtkomst till lokal lagring och molnlagring.

StorSimple använder automatisk nivåindelning mellan den lokala enheten, som har SSD (Solid State Device) och SAS-lagring (Serial-attached SCSI) och Azure Storage. Automatisk nivåindelning håller ofta åtkomst till data lokalt, på SSD- och SAS-nivåerna. Den flyttar sällan åtkomst till data till Azure Storage.

StorSimple erbjuder följande fördelar:

-   Unika deduplicerings- och komprimeringsalgoritmer som använder molnet för att uppnå aldrig tidigare skådade dedupliceringsnivåer
-   Hög tillgänglighet
-   Geo-replikering med hjälp av Azure geo-replikering
-   Azure-integrering
-   Datakryptering i molnet
-   Förbättrad haveriberedskap och efterlevnad

Även om StorSimple presenterar två huvudsakliga distributionsscenarier (primärt säkerhetskopieringsmål och sekundärt säkerhetskopieringsmål) är det i grunden en vanlig, blocklagringsenhet. StorSimple gör all komprimering och deduplicering. Den skickar och hämtar sömlöst data mellan molnet och programmet och filsystemet.

Mer information om StorSimple finns i [StorSimple 8000-serien: Hybridmolnlagringslösning](storsimple-overview.md). Du kan också granska [de tekniska specifikationerna i StorSimple 8000-serien](storsimple-technical-specifications-and-compliance.md).

> [!IMPORTANT]
> Att använda en StorSimple-enhet som ett säkerhetskopieringsmål stöds endast för StorSimple 8000 Update 3 och senare versioner.

## <a name="architecture-overview"></a>Översikt över arkitekturen

Följande tabeller visar den initiala vägledningen för enhetsmodell-till-arkitektur.

**StorSimple kapacitet för lokal lagring och molnlagring**

| Lagringskapacitet | 8100 | 8600 |
|---|---|---|
| Lokal lagringskapacitet | &lt;10 TiB (10)\*  | &lt;20 TiB (20)\*  |
| Lagringskapacitet i molnet | &gt;200 TiB\* | &gt;500 TiB (09)\* |

\*Lagringsstorlek förutsätter ingen deduplicering eller komprimering.

**StorSimple kapacitet för primära och sekundära säkerhetskopior**

| Scenario för säkerhetskopiering  | Lokal lagringskapacitet  | Lagringskapacitet i molnet  |
|---|---|---|
| Primär säkerhetskopiering  | Senaste säkerhetskopior som lagras på lokal lagring för snabb återställning för att uppfylla återställningspunktmål (RPO) | Säkerhetskopieringshistorik (RPO) passar i molnkapacitet |
| Sekundär säkerhetskopiering | Sekundär kopia av säkerhetskopierade data kan lagras i molnkapacitet  | Ej tillämpligt  |

## <a name="storsimple-as-a-primary-backup-target"></a>StorSimple som ett primärt säkerhetskopieringsmål

I det här fallet presenteras StorSimple-volymer för säkerhetskopieringsprogrammet som den enda databasen för säkerhetskopior. Följande bild visar en lösningsarkitektur där alla säkerhetskopior använder StorSimple-nivåindelade volymer för säkerhetskopiering och återställningar.

![StorSimple som ett primärt logiskt säkerhetskopieringsmåldiagram](./media/storsimple-configure-backup-target-using-veeam/primarybackuptargetlogicaldiagram.png)

### <a name="primary-target-backup-logical-steps"></a>Logiska steg för primär målsäkerhetskopiering

1.  Säkerhetskopieringsservern kontaktar målsäkerhetsagenten och säkerhetskopieringsagenten överför data till säkerhetskopieringsservern.
2.  Säkerhetskopieringsservern skriver data till StorSimple-nivåindelade volymer.
3.  Säkerhetskopieringsservern uppdaterar katalogdatabasen och slutför sedan säkerhetskopieringsjobbet.
4.  Ett ögonblicksbildskript utlöser StorSimple cloud snapshot manager (start eller delete).
5.  Säkerhetskopieringsservern tar bort utgångna säkerhetskopior baserat på en bevarandeprincip.

### <a name="primary-target-restore-logical-steps"></a>Logiska steg för primär målåterställning

1.  Säkerhetskopieringsservern börjar återställa lämpliga data från lagringsdatabasen.
2.  Säkerhetskopieringsagenten tar emot data från säkerhetskopieringsservern.
3.  Säkerhetskopieringsservern slutför återställningsjobbet.

## <a name="storsimple-as-a-secondary-backup-target"></a>StorSimple som ett sekundärt säkerhetskopieringsmål

I det här fallet används StorSimple-volymer främst för långsiktig kvarhållning eller arkivering.

Följande bild visar en arkitektur där inledande säkerhetskopieringar och återställningar inriktas på en högpresterande volym. Dessa säkerhetskopior kopieras och arkiveras till en StorSimple-nivåindelad volym enligt ett ansett schema.

Det är viktigt att du ändrar storlek på din högpresterande volym så att den kan hantera din bevarandeprincipkapacitet och prestandakrav.

![StorSimple som ett sekundärt logiskt säkerhetskopieringsmåldiagram](./media/storsimple-configure-backup-target-using-veeam/secondarybackuptargetlogicaldiagram.png)

### <a name="secondary-target-backup-logical-steps"></a>Logiska steg för sekundär säkerhetskopiering av mål

1.  Säkerhetskopieringsservern kontaktar målsäkerhetsagenten och säkerhetskopieringsagenten överför data till säkerhetskopieringsservern.
2.  Säkerhetskopieringsservern skriver data till högpresterande lagring.
3.  Säkerhetskopieringsservern uppdaterar katalogdatabasen och slutför sedan säkerhetskopieringsjobbet.
4.  Säkerhetskopieringsservern kopierar säkerhetskopior till StorSimple baserat på en bevarandeprincip.
5.  Ett ögonblicksbildskript utlöser StorSimple cloud snapshot manager (start eller delete).
6.  Säkerhetskopieringsservern tar bort utgångna säkerhetskopior baserat på en bevarandeprincip.

### <a name="secondary-target-restore-logical-steps"></a>Sekundära logiska steg för återställning av mål

1.  Säkerhetskopieringsservern börjar återställa lämpliga data från lagringsdatabasen.
2.  Säkerhetskopieringsagenten tar emot data från säkerhetskopieringsservern.
3.  Säkerhetskopieringsservern slutför återställningsjobbet.

## <a name="deploy-the-solution"></a>Distribuera lösningen

Att distribuera lösningen kräver tre steg:

1. Förbered nätverksinfrastrukturen.
2. Distribuera din StorSimple-enhet som ett säkerhetskopieringsmål.
3. Distribuera Veeam.

Varje steg diskuteras i detalj i följande avsnitt.

### <a name="set-up-the-network"></a>Konfigurera nätverket

Eftersom StorSimple är en lösning som är integrerad med Azure-molnet kräver StorSimple en aktiv och fungerande anslutning till Azure-molnet. Den här anslutningen används för åtgärder som ögonblicksbilder av molnet, datahantering och metadataöverföring och för att nivå äldre, mindre åtkomstda data till Azure-molnlagring.

För att lösningen ska fungera optimalt rekommenderar vi att du följer de här metodtipsen för nätverk:

-   Länken som ansluter din StorSimple-nivå till Azure måste uppfylla dina bandbreddskrav. Uppnå detta genom att tillämpa den nödvändiga nivån för kvalitet på tjänsten (QoS) på din infrastruktur växlar för att matcha din RPO och återställningstid mål (RTO) SLA.
-   Maximal Azure Blob storage access-svarstider bör vara cirka 80 ms.

### <a name="deploy-storsimple"></a>Distribuera StorSimple

Stegvis storsimple-distributionsvägledning finns i [Distribuera din lokala StorSimple-enhet](storsimple-deployment-walkthrough-u2.md).

### <a name="deploy-veeam"></a>Distribuera Veeam

Metodtips för Veeam-installation finns i [Metodtips för Veeam-säkerhetskopiering & Replikering](https://bp.veeam.expert/)och läsa användarhandboken på [Veeam Help Center (teknisk dokumentation).](https://www.veeam.com/documentation-guides-datasheets.html)

## <a name="set-up-the-solution"></a>Konfigurera lösningen

I det här avsnittet demonstrerar vi några konfigurationsexempel. Följande exempel och rekommendationer illustrerar det mest grundläggande och grundläggande genomförandet. Den här implementeringen kanske inte gäller direkt för dina specifika säkerhetskopieringskrav.

### <a name="set-up-storsimple"></a>Konfigurera StorSimple

| StorSimple-distributionsuppgifter  | Ytterligare kommentarer |
|---|---|
| Distribuera din lokala StorSimple-enhet. | Versioner som stöds: Uppdatera 3 och senare versioner. |
| Slå på säkerhetskopieringsmålet. | Använd dessa kommandon för att aktivera eller inaktivera målläge för säkerhetskopiering och för att få status. Mer information finns i [Ansluta på distans till en StorSimple-enhet](storsimple-remote-connect.md).</br> Så här aktiverar `Set-HCSBackupApplianceMode -enable`du säkerhetskopieringsläge: . </br> Så här stänger `Set-HCSBackupApplianceMode -disable`du av säkerhetskopieringsläget: . </br> Så här hämtar du det `Get-HCSBackupApplianceMode`aktuella läget för inställningar för säkerhetskopieringsläge: . |
| Skapa en gemensam volymbehållare för volymen som lagrar säkerhetskopieringsdata. Alla data i en volymbehållare är deduplicated. | StorSimple-volymbehållare definierar dedupliceringsdomäner.  |
| Skapa StorSimple-volymer. | Skapa volymer med storlekar så nära den förväntade användningen som möjligt, eftersom volymstorlek påverkar varaktighetstiden för ögonblicksbilder i molnet. Information om hur du lyser med storlek på en volym finns i om [bevarandeprinciper](#retention-policies).</br> </br> Använd StorSimple-nivåindelade volymer och markera kryssrutan Använd den **här volymen för mindre ofta använda arkiveringsdata.** </br> Det går inte att använda lokalt fästa volymer. |
| Skapa en unik StorSimple-säkerhetskopieringsprincip för alla målvolymer för säkerhetskopiering. | En StorSimple-princip för säkerhetskopiering definierar gruppen för volymkonsekvens. |
| Inaktivera schemat när ögonblicksbilderna upphör att gälla. | Ögonblicksbilder utlöses som en efterbearbetning. |

### <a name="set-up-the-host-backup-server-storage"></a>Konfigurera lagring av värdsäkerhetsservern

Konfigurera lagring av värdsäkerhetsservern enligt följande riktlinjer:  

- Använd inte intervallerade volymer (skapad av Windows Diskhantering). Utsträckta volymer stöds inte.
- Formatera dina volymer med NTFS med 64 KB allokeringsenhetsstorlek.
- Mappa StorSimple-volymerna direkt till Veeam-servern.
    - Använd iSCSI för fysiska servrar.


## <a name="best-practices-for-storsimple-and-veeam"></a>Metodtips för StorSimple och Veeam

Konfigurera din lösning enligt riktlinjerna i följande få avsnitt.

### <a name="operating-system-best-practices"></a>Bästa praxis för operativsystem

- Inaktivera Windows Server-kryptering och deduplicering för NTFS-filsystemet.
- Inaktivera Defragmentering av Windows Server på StorSimple-volymerna.
- Inaktivera Windows Server-indexering på StorSimple-volymerna.
- Kör en antivirussökning hos källvärden (inte mot StorSimple-volymerna).
- Inaktivera [standardunderhållet för Windows Server](https://msdn.microsoft.com/library/windows/desktop/hh848037.aspx) i Aktivitetshanteraren. Gör detta på något av följande sätt:
  - Inaktivera underhållskonfiguratorn i Schemaläggaren i Windows.
  - Ladda ner [PsExec](https://technet.microsoft.com/sysinternals/bb897553.aspx) från Windows Sysinternals. När du har hämtat PsExec kör du Windows PowerShell som administratör och skriver:
    ```powershell
    psexec \\%computername% -s schtasks /change /tn “MicrosoftWindowsTaskSchedulerMaintenance Configurator" /disable
    ```

### <a name="storsimple-best-practices"></a>StorSimple bästa praxis

-   Kontrollera att StorSimple-enheten uppdateras till [uppdatering 3 eller senare](storsimple-install-update-3.md).
-   Isolera iSCSI- och molntrafik. Använd dedikerade iSCSI-anslutningar för trafik mellan StorSimple och säkerhetskopieringsservern.
-   Se till att din StorSimple-enhet är ett dedikerat säkerhetskopieringsmål. Blandade arbetsbelastningar stöds inte eftersom de påverkar din RTO och RPO.

### <a name="veeam-best-practices"></a>Veeam bästa praxis

-   Veeam-databasen ska vara lokal för servern och inte finnas på en StorSimple-volym.
-   För haveriberedskap säkerhetskopierar du Veeam-databasen på en StorSimple-volym.
-   Vi stöder Veeam fullständiga och inkrementella säkerhetskopior för den här lösningen. Vi rekommenderar att du inte använder syntetiska och differentiella säkerhetskopior.
-   Säkerhetskopierade datafiler bör bara innehålla data för ett visst jobb. Till exempel tillåts inga medietillägg mellan olika jobb.
-   Inaktivera jobbverifiering. Vid behov bör verifieringen schemaläggas efter det senaste säkerhetskopieringsjobbet. Det är viktigt att förstå att det här jobbet påverkar säkerhetskopieringsfönstret.
-   Aktivera medieförallokering.
-   Se till att parallell bearbetning är påslagen.
-   Stäng av komprimeringen.
-   Inaktivera deduplicering på säkerhetskopieringsjobbet.
-   Ange optimering till **LAN Target**.
-   Aktivera **Skapa aktiv fullständig säkerhetskopiering** (varannan vecka).
-   Konfigurera **Säkerhetskopior för säkerhetskopieringsfiler**i säkerhetskopian .
-   Ange **Använd flera uppladdningsströmmar per jobb** till **8** (högst 16 är tillåtet). Justera det här numret uppåt eller nedåt baserat på CPU-användning på StorSimple-enheten.

## <a name="retention-policies"></a>Principer för kvarhållning

En av de vanligaste principtyperna för lagring av säkerhetskopiering är en policy för farfar, far och son (GFS). I en GFS-princip utförs en inkrementell säkerhetskopiering dagligen och fullständiga säkerhetskopior görs veckovis och månadsvis. Den här principen resulterar i sex StorSimple-nivåindelade volymer: en volym innehåller veckovisa, månatliga och årliga fullständiga säkerhetskopior; de övriga fem volymerna lagrar dagliga inkrementella säkerhetskopior.

I följande exempel använder vi en GFS-rotation. Exemplet förutsätter följande:

-   Icke-avduped eller komprimerade data används.
-   Fullständiga säkerhetskopior är 1 TiB vardera.
-   Dagliga inkrementella säkerhetskopior är 500 GiB vardera.
-   Fyra veckovisa säkerhetskopior sparas i en månad.
-   Tolv månatliga säkerhetskopior sparas i ett år.
-   En årlig backup sparas i 10 år.

Baserat på föregående antaganden skapar du en 26-TiB StorSimple-nivåad volym för månatliga och årliga fullständiga säkerhetskopior. Skapa en 5-TiB StorSimple-nivåad volym för var och en av de inkrementella dagliga säkerhetskopieringarna.

| Kvarhållning av säkerhetskopieringstyp | Storlek (TiB) | GFS-multiplikator\* | Total kapacitet (TiB)  |
|---|---|---|---|
| Veckans fulla | 1 | 4  | 4 |
| Daglig inkrementell | 0,5 | 20 (cykler lika många veckor per månad) | 12 (2 för tilläggskvot) |
| Månadsvis full | 1 | 12 | 12 |
| Årlig full | 1  | 10 | 10 |
| GFS-krav |   | 38 |   |
| Ytterligare kvot  | 4  |   | 42 totalt GFS-krav  |

\*GFS-multiplikatorn är antalet kopior du behöver skydda och behålla för att uppfylla dina krav för säkerhetskopieringsprincip.

## <a name="set-up-veeam-storage"></a>Konfigurera Veeam-lagring

### <a name="to-set-up-veeam-storage"></a>Så här konfigurerar du Veeam-lagring

1.  I konsolen Veeam Backup and Replication går du till **Infrastruktur för säkerhetskopiering** **.** Högerklicka på **Säkerhetskopieringsdatabaser**och välj sedan **Lägg till databas för säkerhetskopiering**.

    ![Veeam management console, databassida för säkerhetskopiering](./media/storsimple-configure-backup-target-using-veeam/veeamimage1.png)

2.  Ange ett namn och en beskrivning för databasen i dialogrutan **Ny säkerhetskopia.** Välj **Nästa**.

    ![Veeam management konsol, namn och beskrivning sida](./media/storsimple-configure-backup-target-using-veeam/veeamimage2.png)

3.  För typen väljer du **Microsoft Windows-server**. Välj Veeam-servern. Välj **Nästa**.

    ![Veeam management console, välj typ av säkerhetskopieringsdatabas](./media/storsimple-configure-backup-target-using-veeam/veeamimage3.png)

4.  Om du vill ange **Plats**bläddrar du och väljer volymen. Markera kryssrutan **Begränsa maximala samtidiga aktiviteter till:** och ange värdet till **4**. Detta säkerställer att endast fyra virtuella diskar bearbetas samtidigt medan varje virtuell dator (VM) bearbetas. Välj knappen **Avancerat.**

    ![Veeam-hanteringskonsol, välj volym](./media/storsimple-configure-backup-target-using-veeam/veeamimage4.png)


5.  Markera kryssrutan **Använd säkerhetsfiler per virtuell dator i** dialogrutan Inställningar för **lagringskompatibilitet.**

    ![Veeam management console, inställningar för lagringskompatibilitet](./media/storsimple-configure-backup-target-using-veeam/veeamimage5.png)

6.  Markera kryssrutan **Aktivera vPower NFS-tjänsten på monteringsservern (rekommenderas) i** dialogrutan **Ny databassäkerhet.** Välj **Nästa**.

    ![Veeam management console, databassida för säkerhetskopiering](./media/storsimple-configure-backup-target-using-veeam/veeamimage6.png)

7.  Granska inställningarna och välj sedan **Nästa**.

    ![Veeam management console, databassida för säkerhetskopiering](./media/storsimple-configure-backup-target-using-veeam/veeamimage7.png)

    En databas läggs till på Veeam-servern.

## <a name="set-up-storsimple-as-a-primary-backup-target"></a>Konfigurera StorSimple som ett primärt säkerhetskopieringsmål

> [!IMPORTANT]
> Dataåterställning från en säkerhetskopia som har nivåats till molnet sker vid molnhastigheter.

Följande bild visar mappningen av en typisk volym till ett säkerhetskopieringsjobb. I det här fallet mappar alla veckovisa säkerhetskopior till den fullständiga disken på lördagen och de inkrementella säkerhetskopiorna till inkrementella diskar måndag-fredag. Alla säkerhetskopior och återställningar kommer från en StorSimple-nivåindelad volym.

![Logiskt diagram för primär konfiguration av säkerhetskopieringsmål](./media/storsimple-configure-backup-target-using-veeam/primarybackuptargetdiagram.png)

### <a name="storsimple-as-a-primary-backup-target-gfs-schedule-example"></a>StorSimple som ett primärt gfs-schemaexempel för säkerhetskopieringsmål

Här är ett exempel på en GFS rotation schema för fyra veckor, månadsvis och årligen:

| Typ av frekvens/säkerhetskopiering | Fullständig | Inkrementell (dag 1-5)  |   
|---|---|---|
| Varje vecka (vecka 1-4) | Lördag | Måndag-fredag |
| Månadsvis  | Lördag  |   |
| Varje år | Lördag  |   |


### <a name="assign-storsimple-volumes-to-a-veeam-backup-job"></a>Tilldela StorSimple-volymer till ett Veeam-säkerhetskopieringsjobb

Skapa ett dagligt jobb med din primära Veeam StorSimple-volym för primärt målscenario för säkerhetskopiering. Skapa ett dagligt jobb för en sekundär säkerhetskopieringsmålscenario med hjälp av DAS (Direct Attached Storage), NAS (Network Attached Storage) eller Just a Bunch of Disks (JBOD).

#### <a name="to-assign-storsimple-volumes-to-a-veeam-backup-job"></a>Så här tilldelar du StorSimple-volymer till ett Veeam-säkerhetskopieringsjobb

1.  I konsolen Veeam Backup and Replication väljer du **Säkerhetskopiering & Replication**. Högerklicka på **Säkerhetskopiering**och välj sedan **VMware** eller **Hyper-V**, beroende på din miljö.

    ![Veeam management console, nytt säkerhetskopieringsjobb](./media/storsimple-configure-backup-target-using-veeam/veeamimage8.png)

2.  Ange ett namn och en beskrivning för det dagliga säkerhetskopieringsjobbet i dialogrutan **Nytt säkerhetskopieringsjobb.**

    ![Veeam management console, ny säkerhetskopiering jobbsida](./media/storsimple-configure-backup-target-using-veeam/veeamimage9.png)

3.  Välj en virtuell dator att säkerhetskopiera till.

    ![Veeam management console, ny säkerhetskopiering jobbsida](./media/storsimple-configure-backup-target-using-veeam/veeamimage10.png)

4.  Välj de värden du vill använda för **säkerhetskopieringsproxy** och **säkerhetskopieringsdatabas**. Välj ett värde för **Återställningspunkter som ska behållas på disken** enligt RPO- och RTO-definitionerna för din miljö vid lokalt ansluten lagring. Välj **Avancerat**.

    ![Veeam management console, ny säkerhetskopiering jobbsida](./media/storsimple-configure-backup-target-using-veeam/veeamimage11.png)

5. Välj **Inkrementell**på fliken **Säkerhetskopiering** i dialogrutan **Avancerade inställningar** . Kontrollera att kryssrutan **Skapa syntetiska fullständiga säkerhetskopior med jämna mellanrum** är avmarkerad. Markera kryssrutan **Skapa aktiva fullständiga säkerhetskopior med jämna mellanrum.** Markera kryssrutan **Varje vecka under** Aktiv fullständig **säkerhetskopiering**för lördag.

    ![Veeam management console, nya avancerade inställningar för säkerhetskopieringsjobb](./media/storsimple-configure-backup-target-using-veeam/veeamimage12.png)

6. Kontrollera att kryssrutan **Aktivera inline-datadedlicering** är avmarkerad på fliken **Lagring.** Markera kryssrutan **Uteslut växlingsfilblock** och markera kryssrutan **Uteslut borttagna filblock.** Ange **komprimeringsnivå** till **Ingen**. För balanserad prestanda och deduplicering anger du **lagringsoptimering** till **LAN-mål**. Välj **OK**.

    ![Veeam management console, nya avancerade inställningar för säkerhetskopieringsjobb](./media/storsimple-configure-backup-target-using-veeam/veeamimage13.png)

    Information om inställningar för Veeam-deduplicering och komprimering finns i [Datakomprimering och deduplicering](https://helpcenter.veeam.com/backup/vsphere/compression_deduplication.html).

7.  I dialogrutan **Redigera säkerhetskopieringsjobb** kan du markera kryssrutan **Aktivera programmedveten bearbetning** (valfritt).

    ![Veeam management console, ny backup jobb gästbearbetning sida](./media/storsimple-configure-backup-target-using-veeam/veeamimage14.png)

8.  Ställ in schemat så att det körs en gång dagligen, i taget kan du ange.

    ![Veeam management console, ny schemasida för säkerhetskopieringsjobb](./media/storsimple-configure-backup-target-using-veeam/veeamimage15.png)

## <a name="set-up-storsimple-as-a-secondary-backup-target"></a>Konfigurera StorSimple som ett sekundärt säkerhetskopieringsmål

> [!NOTE]
> Data återställs från en säkerhetskopia som har nivåats till molnet sker vid molnhastigheter.

I den här modellen måste du ha ett lagringsmedia (förutom StorSimple) för att fungera som en tillfällig cache. Du kan till exempel använda en redundant matris med oberoende diskar (RAID) för att rymma utrymme, indata/utdata (I/O) och bandbredd. Vi rekommenderar att du använder RAID 5, 50 och 10.

Följande bild visar typiska lokala (på kort sikt) och lagringsarkivvolymer på lång sikt. I det här fallet körs alla säkerhetskopior på den lokala (till servern) RAID-volymen. Dessa säkerhetskopior dupliceras regelbundet och arkiveras till en arkivvolym. Det är viktigt att du ändrar storlek på din lokala (till servern) RAID-volymen så att den kan hantera dina kortsiktiga lagringskapacitet och prestandakrav.

![StorSimple som sekundärt logiskt säkerhetskopieringsmåldiagram](./media/storsimple-configure-backup-target-using-veeam/secondarybackuptargetdiagram.png)

### <a name="storsimple-as-a-secondary-backup-target-gfs-example"></a>StorSimple som ett sekundärt säkerhetskopieringsmål GFS exempel

Följande tabell visar hur du ställer in säkerhetskopior för att köras på de lokala diskarna och StorSimple-diskarna. Den omfattar individuella och totala kapacitetskrav.

| Typ och kvarhållning av säkerhetskopiering | Konfigurerad lagring | Storlek (TiB) | GFS-multiplikator | Total\* kapacitet (TiB) |
|---|---|---|---|---|
| Vecka 1 (hel och inkrementell) |Lokal disk (kortsiktig)| 1 | 1 | 1 |
| StorSimple vecka 2-4 |StorSimple disk (på lång sikt) | 1 | 4 | 4 |
| Månadsvis full |StorSimple disk (på lång sikt) | 1 | 12 | 12 |
| Årlig full |StorSimple disk (på lång sikt) | 1 | 1 | 1 |
|GFS volymer storlekskrav |  |  |  | 18*|

\*Total kapacitet inkluderar 17 TiB StorSimple-diskar och 1 TiB lokal RAID-volym.


### <a name="gfs-example-schedule"></a>GFS exempelschema

GFS rotation varje vecka, månad och års schema

| Vecka | Fullständig | Inkrementell dag 1 | Inkrementell dag 2 | Inkrementell dag 3 | Inkrementell dag 4 | Inkrementell dag 5 |
|---|---|---|---|---|---|---|
| Vecka 1 | Lokal RAID-volym  | Lokal RAID-volym | Lokal RAID-volym | Lokal RAID-volym | Lokal RAID-volym | Lokal RAID-volym |
| Vecka 2 | StorSimple vecka 2-4 |   |   |   |   |   |
| Vecka 3 | StorSimple vecka 2-4 |   |   |   |   |   |
| Vecka 4 | StorSimple vecka 2-4 |   |   |   |   |   |
| Månadsvis | StorSimple månadsvis |   |   |   |   |   |
| Varje år | StorSimple årligen  |   |   |   |   |   |

### <a name="assign-storsimple-volumes-to-a-veeam-copy-job"></a>Tilldela StorSimple-volymer till ett Veeam-kopieringsjobb

#### <a name="to-assign-storsimple-volumes-to-a-veeam-copy-job"></a>Så här tilldelar du StorSimple-volymer till ett Veeam-kopieringsjobb

1.  I konsolen Veeam Backup and Replication väljer du **Säkerhetskopiering & Replication**. Högerklicka på **Säkerhetskopiering**och välj sedan **VMware** eller **Hyper-V**, beroende på din miljö.

    ![Veeam management console, ny jobbsida för säkerhetskopieringskopiering](./media/storsimple-configure-backup-target-using-veeam/veeamimage16.png)

2.  Ange ett namn och en beskrivning för jobbet i dialogrutan **Nytt säkerhetskopieringskopieringsjobb.**

    ![Veeam management console, ny jobbsida för säkerhetskopieringskopiering](./media/storsimple-configure-backup-target-using-veeam/veeamimage17.png)

3.  Välj de virtuella datorer som du vill bearbeta. Välj bland säkerhetskopior och välj sedan den dagliga säkerhetskopian som du skapade tidigare.

    ![Veeam management console, ny jobbsida för säkerhetskopieringskopiering](./media/storsimple-configure-backup-target-using-veeam/veeamimage18.png)

4.  Uteslut objekt från säkerhetskopieringskopieringsjobbet om det behövs.

5.  Välj säkerhetskopian och ange ett värde för **Återställningspunkter att behålla**. Markera kryssrutan **Behåll följande återställningspunkter för arkivering.** Definiera säkerhetskopieringsfrekvensen och välj sedan **Avancerat**.

    ![Veeam management console, ny jobbsida för säkerhetskopieringskopiering](./media/storsimple-configure-backup-target-using-veeam/veeamimage19.png)

6.  Ange följande avancerade inställningar:

    * Stäng **Maintenance** av korruptionsskyddet på lagringsnivå.

    ![Veeam management console, nya backup kopiera jobb avancerade inställningar sida](./media/storsimple-configure-backup-target-using-veeam/veeamimage20.png)

    * Kontrollera att deduplicering och komprimering är inaktiverade på fliken **Lagring.**

    ![Veeam management console, nya backup kopiera jobb avancerade inställningar sida](./media/storsimple-configure-backup-target-using-veeam/veeamimage21.png)

7.  Ange att dataöverföringen är direkt.

8.  Definiera schemat för säkerhetskopians kopieringsfönster efter dina behov och slutför sedan guiden.

Mer information finns i [Skapa säkerhetskopieringskopieringsjobb](https://helpcenter.veeam.com/backup/hyperv/backup_copy_create.html).

## <a name="storsimple-cloud-snapshots"></a>StorSimple moln ögonblicksbilder

StorSimple ögonblicksbilder av molnet skydda data som finns i din StorSimple-enhet. Att skapa en ögonblicksbild i molnet motsvarar att skicka lokala säkerhetskopieringsband till en anläggning på annan plats. Om du använder Azure geo-redundant lagring, skapa en ögonblicksbild i molnet motsvarar leverans backup band till flera platser. Om du behöver återställa en enhet efter en katastrof kan du ansluta en annan StorSimple-enhet och göra en redundansväxling. Efter redundansen kan du komma åt data (med molnhastigheter) från den senaste ögonblicksbilden av molnet.

I följande avsnitt beskrivs hur du skapar ett kort skript för att starta och ta bort StorSimple-ögonblicksbilder av moln under efterbearbetning av säkerhetskopiering.

> [!NOTE]
> Ögonblicksbilder som skapas manuellt eller programmässigt följer inte principen för förfallodatum för ögonblicksbilder i StorSimple. Dessa ögonblicksbilder måste tas bort manuellt eller programmässigt.

### <a name="start-and-delete-cloud-snapshots-by-using-a-script"></a>Starta och ta bort ögonblicksbilder i molnet med hjälp av ett skript

> [!NOTE]
> Gör en noggrann bedömning av återverkningarna av efterlevnaden och datalagringen innan du tar bort en StorSimple-ögonblicksbild. Mer information om hur du kör ett skript efter säkerhetskopiering finns i Veeam-dokumentationen.


### <a name="backup-lifecycle"></a>Livscykel för säkerhetskopiering

![Livscykeldiagram för säkerhetskopiering](./media/storsimple-configure-backup-target-using-veeam/backuplifecycle.png)

### <a name="requirements"></a>Krav

-   Servern som kör skriptet måste ha åtkomst till Azure-molnresurser.
-   Användarkontot måste ha de behörigheter som krävs.
-   En StorSimple-säkerhetskopieringsprincip med tillhörande StorSimple-volymer måste ställas in men inte aktiveras.
-   Du behöver StorSimple-resursnamn, registreringsnyckel, enhetsnamn och princip-ID för säkerhetskopiering.

### <a name="to-start-or-delete-a-cloud-snapshot"></a>Så här startar eller tar du bort en ögonblicksbild i molnet

1. [Installera Azure PowerShell](/powershell/azure/overview).
2. Ladda ned och konfigurera [Manage-CloudSnapshots.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Manage-CloudSnapshots.ps1) PowerShell-skriptet.
3. På servern som kör skriptet kör du PowerShell som administratör. Se till att du `-WhatIf $true` kör skriptet med för att se vilka ändringar skriptet kommer att göra. När valideringen är `-WhatIf $false`klar skickar du . Kör kommandot nedan:
   ```powershell
   .\Manage-CloudSnapshots.ps1 -SubscriptionId [Subscription Id] -TenantId [Tenant ID] -ResourceGroupName [Resource Group Name] -ManagerName [StorSimple Device Manager Name] -DeviceName [device name] -BackupPolicyName [backup policyname] -RetentionInDays [Retention days] -WhatIf [$true or $false]
   ```
4. Om du vill lägga till skriptet i säkerhetskopieringsjobbet redigerar du avancerade veeam-jobbalternativ.

    ![Fliken Avancerade inställningar för Veeam-säkerhetskopiering](./media/storsimple-configure-backup-target-using-veeam/veeamimage22.png)

Vi rekommenderar att du kör din StorSimple cloud snapshot backup princip som ett efterbearbetning skript i slutet av din dagliga säkerhetskopiering jobb. Mer information om hur du säkerhetskopierar och återställer din programmiljö för säkerhetskopiering så att du kan uppfylla din RPO och RTO finns i din säkerhetskopieringsarkitekt.

## <a name="storsimple-as-a-restore-source"></a>StorSimple som återställningskälla

Återställningar från en StorSimple-enhet fungerar som återställningar från alla blocklagringsenheter. Återställningar av data som är nivåindelade till molnet sker vid molnhastigheter. För lokala data sker återställningar med enhetens lokala diskhastighet.

Med Veeam får du snabb, detaljerad återställning på filnivå via StorSimple via de inbyggda utforskarvyerna i Veeam-konsolen. Använd Veeam Explorers för att återställa enskilda objekt, till exempel e-postmeddelanden, Active Directory-objekt och SharePoint-objekt från säkerhetskopior. Återställningen kan göras utan lokala VM-störningar. Du kan också göra point-in-time-återställning för Azure SQL Database- och Oracle-databaser. Veeam och StorSimple gör processen för återställning på objektnivå från Azure snabbt och enkelt. Information om hur du utför en återställning finns i Veeam-dokumentationen:

- För [Exchange Server](https://www.veeam.com/microsoft-exchange-recovery.html)
- För [Active Directory](https://www.veeam.com/microsoft-active-directory-explorer.html)
- För [SQL Server](https://www.veeam.com/microsoft-sql-server-explorer.html)
- För [SharePoint](https://www.veeam.com/microsoft-sharepoint-recovery-explorer.html)
- För [Oracle](https://www.veeam.com/oracle-backup-recovery-explorer.html)


## <a name="storsimple-failover-and-disaster-recovery"></a>StorSimple redundans och haveriberedskap

> [!NOTE]
> För scenarier för säkerhetskopieringsmål stöds Inte StorSimple Cloud Appliance som ett återställningsmål.

En katastrof kan orsakas av en mängd olika faktorer. I följande tabell visas vanliga scenarier för haveriberedskap.

| Scenario | Påverkan | Så här återställer du | Anteckningar |
|---|---|---|---|
| StorSimple-enhetsfel | Säkerhetskopierings- och återställningsåtgärder avbryts. | Byt ut den misslyckade enheten och utför [StorSimple-redundans- och haveriberedskap](storsimple-device-failover-disaster-recovery.md). | Om du behöver utföra en återställning efter återställning av enheten hämtas fullständiga dataarbetsuppsättningar från molnet till den nya enheten. Alla operationer sker i molnhastigheter. Omsökningen av index och katalog kan göra att alla säkerhetskopieringsuppsättningar genomsöks och hämtas från molnnivån till den lokala enhetsnivån, vilket kan vara en tidskrävande process. |
| Veeam-serverfel | Säkerhetskopierings- och återställningsåtgärder avbryts. | Återskapa säkerhetskopieringsservern och utför databasåterställning enligt [Veeam Help Center (teknisk dokumentation).](https://www.veeam.com/documentation-guides-datasheets.html)  | Du måste återskapa eller återställa Veeam-servern på katastrofåterställningsplatsen. Återställ databasen till den senaste punkten. Om den återställda Veeam-databasen inte är synkroniserad med dina senaste säkerhetskopieringsjobb krävs indexering och katalogisering. Den här omsökningsprocessen för index och katalog kan göra att alla säkerhetskopieringsuppsättningar genomsöks och hämtas från molnnivån till den lokala enhetsnivån. Detta gör det ytterligare tidskrävande. |
| Platsfel som leder till förlust av både säkerhetskopieringsservern och StorSimple | Säkerhetskopierings- och återställningsåtgärder avbryts. | Återställ StorSimple först och återställ sedan Veeam. | Återställ StorSimple först och återställ sedan Veeam. Om du behöver utföra en återställning efter återställning av enheten hämtas de fullständiga dataarbetsuppsättningarna från molnet till den nya enheten. Alla operationer sker i molnhastigheter. |


## <a name="references"></a>Referenser

Följande dokument refererades för den här artikeln:

- [StorSimple multipath I/O-installation](storsimple-configure-mpio-windows-server.md)
- [Lagringsscenarier: Tunn etablering](https://msdn.microsoft.com/library/windows/hardware/dn265487.aspx)
- [Använda GPT-enheter](https://msdn.microsoft.com/windows/hardware/gg463524.aspx#EHD)
- [Konfigurera skuggkopior för delade mappar](https://technet.microsoft.com/library/cc771893.aspx)

## <a name="next-steps"></a>Nästa steg

- Läs mer om hur du [återställer från en säkerhetskopia](storsimple-restore-from-backup-set-u2.md).
- Läs mer om hur du utför [enhetsväxling och haveriberedskap](storsimple-device-failover-disaster-recovery.md).
