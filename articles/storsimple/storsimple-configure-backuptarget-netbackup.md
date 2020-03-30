---
title: StorSimple 8000-serien som backup mål med NetBackup | Microsoft-dokument
description: Beskriver målkonfigurationen för Säkerhetskopior för Säkerhetskopiering med Veritas NetBackup.
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
ms.date: 06/15/2017
ms.author: matd
ms.openlocfilehash: 957fff73f2406e0e057a7c978dd76a6bd9c156b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67876209"
---
# <a name="storsimple-as-a-backup-target-with-netbackup"></a>StorSimple som ett säkerhetskopieringsmål med NetBackup

## <a name="overview"></a>Översikt

Azure StorSimple är en hybridmolnlagringslösning från Microsoft. StorSimple tar itu med komplexiteten i exponentiell datatillväxt genom att använda ett Azure-lagringskonto som ett tillägg till den lokala lösningen och automatiskt nivåindelningsdata över lokal lagring och molnlagring.

I den här artikeln diskuterar vi StorSimple-integrering med Veritas NetBackup och metodtips för att integrera båda lösningarna. Vi ger också rekommendationer om hur du ställer in Veritas NetBackup för att bäst integrera med StorSimple. Vi skjuter upp till Veritas bästa praxis, säkerhetskopiering arkitekter och administratörer för det bästa sättet att ställa in Veritas NetBackup för att uppfylla enskilda backup krav och service-nivå avtal (SLA).

Även om vi illustrerar konfigurationssteg och nyckelbegrepp är den här artikeln inte på något sätt en steg-för-steg-konfiguration eller installationsguide. Vi utgår från att de grundläggande komponenterna och infrastrukturen är i funktionsdugligt skick och redo att stödja de begrepp som vi beskriver.

### <a name="who-should-read-this"></a>Vem ska läsa det här?

Informationen i den här artikeln är till stor hjälp för administratörer, lagringsadministratörer och lagringsarkitekter som har kunskap om lagring, Windows Server 2012 R2, Ethernet, molntjänster och Veritas NetBackup.

### <a name="supported-versions"></a>Versioner som stöds

-   NetBackup 7.7.x och senare versioner
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
](./media/storsimple-configure-backup-target-using-netbackup/image1.jpg)

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

| Lagringskapacitet       | 8100          | 8600            |
|------------------------|---------------|-----------------|
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

![StorSimple som ett primärt logiskt säkerhetskopieringsmåldiagram](./media/storsimple-configure-backup-target-using-netbackup/primarybackuptargetlogicaldiagram.png)

### <a name="primary-target-backup-logical-steps"></a>Logiska steg för primär målsäkerhetskopiering

1.  Säkerhetskopieringsservern kontaktar målsäkerhetsagenten och säkerhetskopieringsagenten överför data till säkerhetskopieringsservern.
2.  Säkerhetskopieringsservern skriver data till StorSimple-nivåindelade volymer.
3.  Säkerhetskopieringsservern uppdaterar katalogdatabasen och slutför sedan säkerhetskopieringsjobbet.
4.  Ett ögonblicksbildskript utlöser StorSimple snapshot manager (start eller delete).
5.  Säkerhetskopieringsservern tar bort utgångna säkerhetskopior baserat på en bevarandeprincip.

### <a name="primary-target-restore-logical-steps"></a>Logiska steg för primär målåterställning

1.  Säkerhetskopieringsservern börjar återställa lämpliga data från lagringsdatabasen.
2.  Säkerhetskopieringsagenten tar emot data från säkerhetskopieringsservern.
3.  Säkerhetskopieringsservern slutför återställningsjobbet.

## <a name="storsimple-as-a-secondary-backup-target"></a>StorSimple som ett sekundärt säkerhetskopieringsmål

I det här fallet används StorSimple-volymer främst för långsiktig kvarhållning eller arkivering.

Följande bild visar en arkitektur där inledande säkerhetskopieringar och återställningar inriktas på en högpresterande volym. Dessa säkerhetskopior kopieras och arkiveras till en StorSimple-nivåindelad volym enligt ett ansett schema.

Det är viktigt att du ändrar storlek på din högpresterande volym så att den kan hantera din bevarandeprincipkapacitet och prestandakrav.

![StorSimple som ett sekundärt logiskt säkerhetskopieringsmåldiagram](./media/storsimple-configure-backup-target-using-netbackup/secondarybackuptargetlogicaldiagram.png)

### <a name="secondary-target-backup-logical-steps"></a>Logiska steg för sekundär säkerhetskopiering av mål

1.  Säkerhetskopieringsservern kontaktar målsäkerhetsagenten och säkerhetskopieringsagenten överför data till säkerhetskopieringsservern.
2.  Säkerhetskopieringsservern skriver data till högpresterande lagring.
3.  Säkerhetskopieringsservern uppdaterar katalogdatabasen och slutför sedan säkerhetskopieringsjobbet.
4.  Säkerhetskopieringsservern kopierar säkerhetskopior till StorSimple baserat på en bevarandeprincip.
5.  Ett ögonblicksbildskript utlöser StorSimple snapshot manager (start eller delete).
6.  Säkerhetskopieringsservern tar bort de utgångna säkerhetskopiorna baserat på en bevarandeprincip.

### <a name="secondary-target-restore-logical-steps"></a>Sekundära logiska steg för återställning av mål

1.  Säkerhetskopieringsservern börjar återställa lämpliga data från lagringsdatabasen.
2.  Säkerhetskopieringsagenten tar emot data från säkerhetskopieringsservern.
3.  Säkerhetskopieringsservern slutför återställningsjobbet.

## <a name="deploy-the-solution"></a>Distribuera lösningen

Distribuera den här lösningen kräver tre steg:
1. Förbered nätverksinfrastrukturen.
2. Distribuera din StorSimple-enhet som ett säkerhetskopieringsmål.
3. Distribuera Veritas NetBackup.

Varje steg diskuteras i detalj i följande avsnitt.

### <a name="set-up-the-network"></a>Konfigurera nätverket

Eftersom StorSimple är en lösning som är integrerad med Azure-molnet kräver StorSimple en aktiv och fungerande anslutning till Azure-molnet. Den här anslutningen används för åtgärder som ögonblicksbilder av molnet, datahantering och metadataöverföring och för att nivå äldre, mindre åtkomstda data till Azure-molnlagring.

För att lösningen ska fungera optimalt rekommenderar vi att du följer de här metodtipsen för nätverk:

-   Länken som ansluter StorSimple-nivåindelningen till Azure måste uppfylla dina bandbreddskrav. För att uppnå detta, tillämpa rätt kvalitet på tjänsten (QoS) nivå på din infrastruktur växlar för att matcha din RPO och återställningstid mål (RTO) SLA.

-   Maximal Azure Blob storage access-svarstider bör vara cirka 80 ms.

### <a name="deploy-storsimple"></a>Distribuera StorSimple

Stegvis storsimple-distributionsvägledning finns i [Distribuera din lokala StorSimple-enhet](storsimple-deployment-walkthrough-u2.md).

### <a name="deploy-netbackup"></a>Distribuera NetBackup

Stegvis distributionsvägledning för NetBackup 7.7.x finns i [NetBackup 7.7.x-dokumentationen](http://www.veritas.com/docs/000094423).

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

- Använd inte intervallerade volymer (skapad av Windows DiskHantering). volymer som sträcker sig över stöds inte.
- Formatera dina volymer med NTFS med 64 KB allokeringsstorlek.
- Mappa StorSimple-volymerna direkt till NetBackup-servern.
    - Använd iSCSI för fysiska servrar.
    - Använd direktdiskar för virtuella servrar.


## <a name="best-practices-for-storsimple-and-netbackup"></a>Metodtips för StorSimple och NetBackup

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

### <a name="netbackup-best-practices"></a>Bästa metoder för NetBackup

-   NetBackup-databasen ska vara lokal för servern och inte finnas på en StorSimple-volym.
-   För haveriberedskap säkerhetskopierar du NetBackup-databasen på en StorSimple-volym.
-   Vi stöder NetBackup fullständiga och inkrementella säkerhetskopior (även kallad differentiell inkrementella säkerhetskopior i NetBackup) för den här lösningen. Vi rekommenderar att du inte använder syntetiska och kumulativa inkrementella säkerhetskopior.
-   Säkerhetskopierade datafiler bör bara innehålla data för ett visst jobb. Till exempel tillåts inga medietillägg mellan olika jobb.

De senaste NetBackup-inställningarna och metodtipsen för att implementera dessa krav finns i NetBackup-dokumentationen på [www.veritas.com](https://www.veritas.com).


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

## <a name="set-up-netbackup-storage"></a>Konfigurera NetBackup-lagring

### <a name="to-set-up-netbackup-storage"></a>Så här konfigurerar du NetBackup-lagring

1.  I NetBackup Administration Console väljer du Diskpooler för diskpooler för **medie- och enhetshanteringsenheter** > **Devices** > **Disk Pools**. I konfigurationsguiden för diskpool väljer du lagringsservertypen **AdvancedDisk**och väljer sedan **Nästa**.

    ![NetBackup Administration Console, konfigurationsguide för diskpool](./media/storsimple-configure-backup-target-using-netbackup/nbimage1.png)

2.  Välj servern och välj sedan **Nästa**.

    ![NetBackup Administration Console, välj servern](./media/storsimple-configure-backup-target-using-netbackup/nbimage2.png)

3.  Välj din StorSimple-volym.

    ![NetBackup Administration Console, välj StorSimple-volymdisken](./media/storsimple-configure-backup-target-using-netbackup/nbimage3.png)

4.  Ange ett namn för säkerhetskopieringsmålet och välj sedan **Nästa** > **nästa för** att slutföra guiden.

5.  Granska inställningarna och välj sedan **Slutför**.

6.  I slutet av varje volymtilldelning ändrar du inställningarna för lagringsenhet så att de matchar de som rekommenderas i [Metodtips för StorSimple och NetBackup](#best-practices-for-storsimple-and-netbackup).

7. Upprepa steg 1-6 tills du har tilldelat dina StorSimple-volymer.

    ![NetBackup Administration Console, diskkonfiguration](./media/storsimple-configure-backup-target-using-netbackup/nbimage5.png)

## <a name="set-up-storsimple-as-a-primary-backup-target"></a>Konfigurera StorSimple som ett primärt säkerhetskopieringsmål

> [!NOTE]
> Data återställs från en säkerhetskopia som har nivåats till molnet sker vid molnhastigheter.

Följande bild visar mappningen av en typisk volym till ett säkerhetskopieringsjobb. I det här fallet mappar alla veckovisa säkerhetskopior till den fullständiga disken på lördagen och de inkrementella säkerhetskopiorna till inkrementella diskar måndag-fredag. Alla säkerhetskopior och återställningar kommer från en StorSimple-nivåindelad volym.

![Logiskt diagram för primär konfiguration av säkerhetskopieringsmål](./media/storsimple-configure-backup-target-using-netbackup/primarybackuptargetdiagram.png)

### <a name="storsimple-as-a-primary-backup-target-gfs-schedule-example"></a>StorSimple som ett primärt gfs-schemaexempel för säkerhetskopieringsmål

Här är ett exempel på en GFS rotation schema för fyra veckor, månadsvis och årligen:

| Typ av frekvens/säkerhetskopiering | Fullständig | Inkrementell (dag 1-5)  |   
|---|---|---|
| Varje vecka (vecka 1-4) | Lördag | Måndag-fredag |
| Månadsvis  | Lördag  |   |
| Varje år | Lördag  |   |

## <a name="assigning-storsimple-volumes-to-a-netbackup-backup-job"></a>Tilldela StorSimple-volymer till ett NetBackup-säkerhetskopieringsjobb

Följande sekvens förutsätter att NetBackup och målvärden är konfigurerade i enlighet med NetBackup-agentens riktlinjer.

### <a name="to-assign-storsimple-volumes-to-a-netbackup-backup-job"></a>Så här tilldelar du StorSimple-volymer till ett NetBackup-säkerhetskopieringsjobb

1. I NetBackup Administration Console väljer du **NetBackup Management**, högerklickar på **Principer**och väljer sedan **Ny princip**.

   ![NetBackup Administration Console, skapa en ny princip](./media/storsimple-configure-backup-target-using-netbackup/nbimage6.png)

2. Ange ett namn på principen i dialogrutan **Lägg till en ny princip** och markera sedan kryssrutan Använd **principkonfigurationsguiden.** Välj **OK**.

   ![NetBackup Administration Console, Lägg till en ny principdialogruta](./media/storsimple-configure-backup-target-using-netbackup/nbimage7.png)

3. Välj den konfigurationstyp för säkerhetskopieringsprincip du vill använda i konfigurationsguiden för säkerhetskopieringsprincipen och välj sedan **Nästa**.

   ![NetBackup Administration Console, välj backup typ](./media/storsimple-configure-backup-target-using-netbackup/nbimage8.png)

4. Om du vill ange principtypen väljer du **Standard**och väljer sedan **Nästa**.

   ![NetBackup Administration Console, välj principtyp](./media/storsimple-configure-backup-target-using-netbackup/nbimage9.png)

5. Markera värden, markera kryssrutan **Identifiera klientoperativsystem** och välj sedan **Lägg till**. Välj **Nästa**.

   ![NetBackup Administration Console, lista klienter i en ny princip](./media/storsimple-configure-backup-target-using-netbackup/nbimage10.png)

6. Välj de enheter som du vill säkerhetskopiera.

   ![NetBackup Administration Console, val av säkerhetskopiering för en ny princip](./media/storsimple-configure-backup-target-using-netbackup/nbimage11.png)

7. Välj de frekvens- och kvarhållningsvärden som uppfyller kraven för säkerhetskopieringsrotation.

   ![NetBackup Administration Console, säkerhetskopieringsfrekvens och rotation för en ny princip](./media/storsimple-configure-backup-target-using-netbackup/nbimage12.png)

8. Välj **Nästa** > **slut** > **Finish**.  Du kan ändra schemat när principen har skapats.

9. Markera det här om du vill expandera den princip som du just skapade och välj sedan **Scheman**.

   ![NetBackup Administration Console, scheman för en ny princip](./media/storsimple-configure-backup-target-using-netbackup/nbimage13.png)

10. Högerklicka på **Differential-Inc**, välj **Kopiera till ny**och välj sedan **OK**.

    ![NetBackup Administration Console, kopiera schema till en ny princip](./media/storsimple-configure-backup-target-using-netbackup/nbimage14.png)

11. Högerklicka på det nyligen skapade schemat och välj sedan **Ändra**.

12. Markera kryssrutan **Åsidosätt principlagringsval** på fliken **Attribut** och markera sedan volymen där inkrementella säkerhetskopieringar på måndagar går.

    ![NetBackup Administration Console, ändra schema](./media/storsimple-configure-backup-target-using-netbackup/nbimage15.png)

13. På fliken **Startfönster** väljer du tidsfönstret för dina säkerhetskopior.

    ![NetBackup Administration Console, ändra startfönstret](./media/storsimple-configure-backup-target-using-netbackup/nbimage16.png)

14. Välj **OK**.

15. Upprepa steg 10-14 för varje inkrementell säkerhetskopiering. Välj lämplig volym och schema för varje säkerhetskopia du skapar.

16. Högerklicka på **schemat Differential-Inc** och ta sedan bort det.

17. Ändra ditt fullständiga schema för att uppfylla dina säkerhetskopieringsbehov.

    ![NetBackup Administration Console, ändra fullt schema](./media/storsimple-configure-backup-target-using-netbackup/nbimage17.png)

18. Ändra startfönstret.

    ![NetBackup Administration Console, ändra startfönstret](./media/storsimple-configure-backup-target-using-netbackup/nbimage18.png)

19. Det slutliga schemat ser ut så här:

    ![NetBackup Administration Console, slutligt schema](./media/storsimple-configure-backup-target-using-netbackup/nbimage19.png)

## <a name="set-up-storsimple-as-a-secondary-backup-target"></a>Konfigurera StorSimple som ett sekundärt säkerhetskopieringsmål

> [!NOTE]
>Data återställs från en säkerhetskopia som har nivåats till molnet sker vid molnhastigheter.

I den här modellen måste du ha ett lagringsmedia (förutom StorSimple) för att fungera som en tillfällig cache. Du kan till exempel använda en redundant matris med oberoende diskar (RAID) för att rymma utrymme, indata/utdata (I/O) och bandbredd. Vi rekommenderar att du använder RAID 5, 50 och 10.

Följande bild visar typiska lokala volymer (på kort sikt( till servern) och långsiktiga lagringsarkivvolymer. I det här fallet körs alla säkerhetskopior på den lokala (till servern) RAID-volymen. Dessa säkerhetskopior dupliceras regelbundet och arkiveras i en arkivvolym. Det är viktigt att du ändrar storlek på din lokala (till servern) RAID-volymen så att den kan hantera dina kortsiktiga lagringskapacitet och prestandakrav.

### <a name="storsimple-as-a-secondary-backup-target-gfs-example"></a>StorSimple som ett sekundärt säkerhetskopieringsmål GFS exempel

![StorSimple som ett sekundärt logiskt säkerhetskopieringsmåldiagram](./media/storsimple-configure-backup-target-using-netbackup/secondarybackuptargetdiagram.png)

Följande tabell visar hur du ställer in säkerhetskopior för att köras på de lokala diskarna och StorSimple-diskarna. Den omfattar individuella och totala kapacitetskrav.

### <a name="backup-configuration-and-capacity-requirements"></a>Konfigurations- och kapacitetskrav för säkerhetskopiering och kapacitet

| Typ och kvarhållning av säkerhetskopiering | Konfigurerad lagring | Storlek (TiB) | GFS-multiplikator | Total\* kapacitet (TiB) |
|---|---|---|---|---|
| Vecka 1 (hel och inkrementell) |Lokal disk (kortsiktig)| 1 | 1 | 1 |
| StorSimple vecka 2-4 |StorSimple disk (på lång sikt) | 1 | 4 | 4 |
| Månadsvis full |StorSimple disk (på lång sikt) | 1 | 12 | 12 |
| Årlig full |StorSimple disk (på lång sikt) | 1 | 1 | 1 |
|GFS volymer storlekskrav |  |  |  | 18*|

\*Total kapacitet inkluderar 17 TiB StorSimple-diskar och 1 TiB lokal RAID-volym.


### <a name="gfs-example-schedule-gfs-rotation-weekly-monthly-and-yearly-schedule"></a>GFS exempelschema: GFS rotation varje vecka, månadsvis och årligt schema

| Vecka | Fullständig | Inkrementell dag 1 | Inkrementell dag 2 | Inkrementell dag 3 | Inkrementell dag 4 | Inkrementell dag 5 |
|---|---|---|---|---|---|---|
| Vecka 1 | Lokal RAID-volym  | Lokal RAID-volym | Lokal RAID-volym | Lokal RAID-volym | Lokal RAID-volym | Lokal RAID-volym |
| Vecka 2 | StorSimple vecka 2-4 |   |   |   |   |   |
| Vecka 3 | StorSimple vecka 2-4 |   |   |   |   |   |
| Vecka 4 | StorSimple vecka 2-4 |   |   |   |   |   |
| Månadsvis | StorSimple månadsvis |   |   |   |   |   |
| Varje år | StorSimple årligen  |   |   |   |   |   |


## <a name="assign-storsimple-volumes-to-a-netbackup-archive-and-duplication-job"></a>Tilldela StorSimple-volymer till ett NetBackup-arkiv och dupliceringsjobb

Eftersom NetBackup erbjuder ett brett utbud av alternativ för lagring och mediehantering rekommenderar vi att du rådgör med Veritas eller din NetBackup-arkitekt för att korrekt bedöma dina SLP-krav (Storage Lifecycle Policy).

När du har definierat de första diskpoolerna måste du definiera ytterligare tre livscykelprinciper för lagring för totalt fyra principer:
* LocalRAIDVolume
* StorSimpleWeek2-4
* StorSimpleMonthlyFulls
* StorSimpleYearlyFulls

### <a name="to-assign-storsimple-volumes-to-a-netbackup-archive-and-duplication-job"></a>Så här tilldelar du StorSimple-volymer till ett NetBackup-arkiv- och dupliceringsjobb

1. I NetBackup Administration Console väljer du > **Livscykelprinciper för** > **lagringslagring ny lagringslivscykel .** **Storage**

   ![NetBackup Administration Console, ny livscykelpolicy för lagring](./media/storsimple-configure-backup-target-using-netbackup/nbimage20.png)

2. Ange ett namn för ögonblicksbilden och välj sedan **Lägg till**.

3. Välj **Säkerhetskopiering**på fliken **Egenskaper** i **Operation**dialogrutan **Ny åtgärd.** Välj de värden du vill använda för **mållagring,** **kvarhållningstyp**och **kvarhållningsperiod**. Välj **OK**.

   ![NetBackup Administration Console, dialogrutan Ny åtgärd](./media/storsimple-configure-backup-target-using-netbackup/nbimage22.png)

   Detta definierar den första säkerhetskopieringen och databasen.

4. Markera det här om du vill markera föregående åtgärd och välj sedan **Lägg till**. I dialogrutan **Ändra lagringsåtgärd** väljer du de värden du vill använda för **Mållagring,** **Kvarhållningstyp**och **Kvarhållningsperiod**.

   ![NetBackup Administration Console,Dialogrutan Ändra lagringsåtgärd](./media/storsimple-configure-backup-target-using-netbackup/nbimage23.png)

5. Markera det här om du vill markera föregående åtgärd och välj sedan **Lägg till**. Lägg till månatliga säkerhetskopior under ett år i dialogrutan **Ny livscykelpolicy** för lagring.

   ![NetBackup Administration Console, dialogrutan Ny livscykelpolicy för lagring](./media/storsimple-configure-backup-target-using-netbackup/nbimage24.png)

6. Upprepa steg 4-5 tills du har skapat den omfattande SLP-bevarandeprincip som du behöver.

   ![NetBackup Administration Console, Lägg till principer i dialogrutan Ny livscykelpolicy för lagring](./media/storsimple-configure-backup-target-using-netbackup/nbimage25.png)

7. När du är klar med att definiera bevarandeprincipen för SLP definierar du en principer för säkerhetskopiering under **Princip**genom att följa stegen i [Tilldela StorSimple-volymer till ett NetBackup-säkerhetskopieringsjobb](#assigning-storsimple-volumes-to-a-netbackup-backup-job).

8. Högerklicka på **Fullständig**i dialogrutan **Ändra schema** under **Schemalägg**och välj sedan **Ändra**.

   ![NetBackup Administration Console, Dialogrutan Ändra schema](./media/storsimple-configure-backup-target-using-netbackup/nbimage26.png)

9. Markera kryssrutan **Åsidosätt principlagringsval** och markera sedan den SLP-bevarandeprincip som du skapade i steg 1-6.

   ![NetBackup Administration Console, åsidosätt princip lagringsval](./media/storsimple-configure-backup-target-using-netbackup/nbimage27.png)

10. Välj **OK**och upprepa sedan för det inkrementella säkerhetskopieringsschemat.

    ![NetBackup Administration Console, Ändra schema för inkrementella säkerhetskopior](./media/storsimple-configure-backup-target-using-netbackup/nbimage28.png)


| Kvarhållning av säkerhetskopieringstyp | Storlek (TiB) | GFS-multiplikator\* | Total kapacitet (TiB)  |
|---|---|---|---|
| Veckans fulla |  1  |  4 | 4  |
| Daglig inkrementell  | 0,5  | 20 (cyklerna är lika med antalet veckor per månad) | 12 (2 för tilläggskvot) |
| Månadsvis full  | 1 | 12 | 12 |
| Årlig full | 1  | 10 | 10 |
| GFS-krav  |     |     | 38 |
| Ytterligare kvot  | 4  |    | 42 totalt GFS-krav |

\*GFS-multiplikatorn är antalet kopior du behöver skydda och behålla för att uppfylla dina krav för säkerhetskopieringsprincip.

## <a name="storsimple-cloud-snapshots"></a>StorSimple moln ögonblicksbilder

StorSimple ögonblicksbilder av molnet skydda data som finns i din StorSimple-enhet. Att skapa en ögonblicksbild i molnet motsvarar att skicka lokala säkerhetskopieringsband till en anläggning på annan plats. Om du använder Azure geo-redundant lagring, skapa en ögonblicksbild i molnet motsvarar leverans backup band till flera platser. Om du behöver återställa en enhet efter en katastrof kan du ansluta en annan StorSimple-enhet och göra en redundansväxling. Efter redundansen kan du komma åt data (med molnhastigheter) från den senaste ögonblicksbilden av molnet.

I följande avsnitt beskrivs hur du skapar ett kort skript för att starta och ta bort StorSimple-ögonblicksbilder av moln under efterbearbetning av säkerhetskopiering.

> [!NOTE]
> Ögonblicksbilder som skapas manuellt eller programmässigt följer inte principen för förfallodatum för ögonblicksbilder i StorSimple. Dessa ögonblicksbilder måste tas bort manuellt eller programmässigt.

### <a name="start-and-delete-cloud-snapshots-by-using-a-script"></a>Starta och ta bort ögonblicksbilder i molnet med hjälp av ett skript

> [!NOTE]
> Gör en noggrann bedömning av återverkningarna av efterlevnaden och datalagringen innan du tar bort en StorSimple-ögonblicksbild. Mer information om hur du kör ett skript efter säkerhetskopiering finns i [NetBackup-dokumentationen](http://www.veritas.com/docs/000094423).

### <a name="backup-lifecycle"></a>Livscykel för säkerhetskopiering

![Livscykeldiagram för säkerhetskopiering](./media/storsimple-configure-backup-target-using-netbackup/backuplifecycle.png)

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
4. Lägg till skriptet i säkerhetskopieringsjobbet i NetBackup. Det gör du genom att redigera netbackup-jobbalternativens förbearbetnings- och efterbearbetningskommandon.

> [!NOTE]
> Vi rekommenderar att du kör din StorSimple cloud snapshot backup princip som ett efterbearbetning skript i slutet av din dagliga säkerhetskopiering jobb. Mer information om hur du säkerhetskopierar och återställer din programmiljö för säkerhetskopiering så att du kan uppfylla din RPO och RTO finns i din säkerhetskopieringsarkitekt.

## <a name="storsimple-as-a-restore-source"></a>StorSimple som återställningskälla

Återställningar från en StorSimple-enhet fungerar som återställningar från alla blocklagringsenheter. Återställningar av data som är nivåindelade till molnet sker vid molnhastigheter. För lokala data sker återställningar med enhetens lokala diskhastighet. Information om hur du utför en återställning finns i [NetBackup-dokumentationen](http://www.veritas.com/docs/000094423). Vi rekommenderar att du följer de bästa metoderna för återställning av NetBackup.

## <a name="storsimple-failover-and-disaster-recovery"></a>StorSimple redundans och haveriberedskap

> [!NOTE]
> För scenarier för säkerhetskopieringsmål stöds Inte StorSimple Cloud Appliance som ett återställningsmål.

En katastrof kan orsakas av en mängd olika faktorer. I följande tabell visas vanliga scenarier för haveriberedskap.

| Scenario | Påverkan | Så här återställer du | Anteckningar |
|---|---|---|---|
| StorSimple-enhetsfel | Säkerhetskopierings- och återställningsåtgärder avbryts. | Byt ut den misslyckade enheten och utför [StorSimple-redundans- och haveriberedskap](storsimple-device-failover-disaster-recovery.md). | Om du behöver utföra en återställning efter återställning av enheten hämtas fullständiga dataarbetsuppsättningar från molnet till den nya enheten. Alla operationer sker i molnhastigheter. Omsökningen av index och katalog kan göra att alla säkerhetskopieringsuppsättningar genomsöks och hämtas från molnnivån till den lokala enhetsnivån, vilket kan vara en tidskrävande process. |
| NetBackup-serverfel | Säkerhetskopierings- och återställningsåtgärder avbryts. | Återskapa säkerhetskopieringsservern och utför databasåterställning. | Du måste återskapa eller återställa NetBackup-servern på katastrofåterställningsplatsen. Återställ databasen till den senaste punkten. Om den återställda NetBackup-databasen inte är synkroniserad med dina senaste säkerhetskopieringsjobb krävs indexering och katalogisering. Den här omsökningsprocessen för index och katalog kan göra att alla säkerhetskopieringsuppsättningar genomsöks och hämtas från molnnivån till den lokala enhetsnivån. Detta gör det ytterligare tidskrävande. |
| Platsfel som leder till förlust av både säkerhetskopieringsservern och StorSimple | Säkerhetskopierings- och återställningsåtgärder avbryts. | Återställ StorSimple först och återställ sedan NetBackup. | Återställ StorSimple först och återställ sedan NetBackup. Om du behöver utföra en återställning efter återställning av enheten hämtas de fullständiga dataarbetsuppsättningarna från molnet till den nya enheten. Alla operationer sker i molnhastigheter. |

## <a name="references"></a>Referenser

Följande dokument refererades för den här artikeln:

- [StorSimple multipath I/O-installation](storsimple-configure-mpio-windows-server.md)
- [Lagringsscenarier: Tunn etablering](https://msdn.microsoft.com/library/windows/hardware/dn265487.aspx)
- [Använda GPT-enheter](https://msdn.microsoft.com/windows/hardware/gg463524.aspx#EHD)
- [Konfigurera skuggkopior för delade mappar](https://technet.microsoft.com/library/cc771893.aspx)

## <a name="next-steps"></a>Nästa steg

- Läs mer om hur du [återställer från en säkerhetskopia](storsimple-restore-from-backup-set-u2.md).
- Läs mer om hur du utför [enhetsväxling och haveriberedskap](storsimple-device-failover-disaster-recovery.md).
