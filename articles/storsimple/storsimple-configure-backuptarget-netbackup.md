---
title: StorSimple 8000-serien som mål för säkerhetskopia med NetBackup | Microsoft Docs
description: Beskriver konfigurationen för målet för säkerhetskopian av StorSimple med Veritas NetBackup.
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
ms.author: hkanna
ms.openlocfilehash: 17428405a0be45854a2eaaef831864f529ed145a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60725370"
---
# <a name="storsimple-as-a-backup-target-with-netbackup"></a>StorSimple som ett säkerhetskopieringsmål med NetBackup

## <a name="overview"></a>Översikt

Azure StorSimple är en hybridlösning för molnlagring från Microsoft. StorSimple hanterar komplexiteten i exponentiell datatillväxt genom att använda ett Azure storage-konto som ett tillägg till lokal lösning och automatiskt nivåindelas data i den lokala lagringen och molnlagring.

I den här artikeln diskuterar vi hur integrering av StorSimple med Veritas NetBackup och bästa praxis för att integrera båda lösningarna. Vi kan också göra rekommendationer om hur du ställer in Veritas NetBackup bäst integrera med StorSimple. Vi ska skjutas upp till Veritas metodtips, säkerhetskopiering arkitekter och administratörer för det bästa sättet att konfigurera Veritas NetBackup att uppfylla individuella behov för säkerhetskopiering och servicenivåavtal (SLA).

Även om vi illustrera konfigurationssteg och viktiga begrepp som den här artikeln är inte menat en stegvis guide för konfiguration eller installation. Vi förutsätter att den grundläggande komponenter och infrastruktur fungerar och är redo för att stödja de begrepp som beskrivs.

### <a name="who-should-read-this"></a>Vem ska läsa det här?

Informationen i den här artikeln är mest användbar för att säkerhetskopieringsadministratörer, lagringsadministratörer och storage-arkitekter som har kunskaper om lagring, Windows Server 2012 R2, Ethernet, molntjänster och Veritas NetBackup.

### <a name="supported-versions"></a>Versioner som stöds

-   NetBackup 7.7.x och senare versioner
-   [StorSimple uppdatering 3 och senare versioner](storsimple-overview.md#storsimple-workload-summary)


## <a name="why-storsimple-as-a-backup-target"></a>Varför StorSimple som ett säkerhetskopieringsmål?

StorSimple är ett bra alternativ för ett säkerhetskopieringsmål eftersom:

-   Det ger standard och lokal lagring för program för säkerhetskopiering ska användas som ett mål för snabb säkerhetskopiering, utan ändringar. Du kan också använda StorSimple för en snabb återställning av säkerhetskopior som nyligen skapats.
-   Sitt moln lagringsnivåer integreras sömlöst med ett Azure cloud storage-konto du använder Azure Storage med kostnadseffektiv lagring.
-   Den tillhandahåller automatiskt lagring på annan plats för haveriberedskap.

## <a name="key-concepts"></a>Viktiga begrepp

Precis som med alla lagringslösning, en noggrann utvärdering av lösningens lagringsprestanda, serviceavtal, är för ändrings- och tillväxt kapacitetsbehoven nyckeln till framgång. Den huvudsakliga tanken är att genom att introducera en cloud-nivå, dina åtkomsttider och genomströmning till molnet play en fundamental roll i StorSimple förmåga att utföra sitt arbete.

StorSimple har utformats för att ge lagring för program som körs på en väldefinierad arbetsminnet för data (frekventa data). I den här modellen arbetsminnet för data lagras på de lokala nivåerna och de återstående ledig/kall/arkiveras datamängder nivåer till molnet. Den här modellen visas i följande bild. Den fasta nästan gröna linjen representerar de data som lagras på de lokala nivåerna av StorSimple-enhet. Den röda linjen representerar den totala mängden data som lagras på StorSimple-lösningen oavsett nivå. Utrymme mellan den fasta gröna linjen och exponentiell red kurvan representerar den totala mängden data som lagras i molnet.

**StorSimple lagringsnivåer**
![StorSimple lagringsnivåer diagram](./media/storsimple-configure-backup-target-using-netbackup/image1.jpg)

Du kommer märka att StorSimple är idealisk för att fungera som ett säkerhetskopieringsmål med den här arkitekturen i åtanke. Du kan använda StorSimple till:
-   Utföra dina vanligaste återställningar från lokala arbetsminnet för data.
-   Använd molnet för katastrofåterställning för på en annan plats och äldre data där återställningar är mer sällan.

## <a name="storsimple-benefits"></a>StorSimple-fördelar

StorSimple är en lokal lösning som är sömlöst integrerad med Microsoft Azure, genom att utnyttja sömlös åtkomst till lokala program och lagring i molnet.

StorSimple använder automatisk lagringsnivåer mellan den lokala enheten, vilket har SSD-enhet (SSD) och seriellt ansluten SCSI (SAS) storage och Azure Storage. Automatisk lagringsnivåer behåller data som används ofta local, på SSD- och SAS-nivåerna. Data som sällan används flyttas till Azure Storage.

StorSimple erbjuder följande fördelar:

-   Unika avduplicering och komprimering algoritmer som använder molnet för att uppnå en oöverträffad deduplicering
-   Hög tillgänglighet
-   GEO-replikering med hjälp av Azure geo-replikering
-   Integrering med Azure
-   Kryptering av data i molnet
-   Förbättrad haveriberedskap och efterlevnad

StorSimple presenterar två huvudsakliga distributionsscenarier (primära mål för säkerhetskopian och sekundära säkerhetskopieringsmål) grunden, är det en vanlig, blocklagringsenhet. StorSimple har alla komprimering och deduplicering. Sömlöst skickar och hämtar data mellan molnet och program och filsystemet.

Läs mer om StorSimple, [StorSimple 8000-serien: Hybridmolnlagringslösning](storsimple-overview.md). Du kan också granska den [tekniska specifikationer för StorSimple 8000-serien](storsimple-technical-specifications-and-compliance.md).

> [!IMPORTANT]
> Med hjälp av en StorSimple-enheten som ett säkerhetskopieringsmål stöds endast för StorSimple 8000 uppdatering 3 och senare versioner.

## <a name="architecture-overview"></a>Översikt över arkitekturen

Följande tabeller visar enheten modellen till arkitektur inledande vägledning.

**StorSimple-kapaciteter för lokal och molnlagring**

| Lagringskapacitet       | 8100          | 8600            |
|------------------------|---------------|-----------------|
| Lokal lagringskapacitet | &lt; 10 TiB\*  | &lt; 20 TiB\*  |
| Kapacitet för molnlagring | &gt; 200 TiB\* | &gt; 500 TiB\* |

\* Lagringsstorlek förutsätter att inga deduplicering eller komprimering.

**StorSimple-kapaciteter för primära och sekundära säkerhetskopieringar**

| Säkerhetskopiering scenario  | Lokal lagringskapacitet  | Kapacitet för molnlagring  |
|---|---|---|
| Primära säkerhetskopia  | Senaste säkerhetskopior som lagras på en lokal lagringsenhet för snabb återställning att uppfylla mål för återställningspunkt (RPO) | Historik för säkerhetskopiering (RPO) passar i Molnets kapacitet |
| Sekundär säkerhetskopiering | Sekundär kopia av säkerhetskopierade data kan lagras i Molnets kapacitet  | Gäller inte  |

## <a name="storsimple-as-a-primary-backup-target"></a>StorSimple som ett primära säkerhetskopieringsmål

I det här scenariot visas StorSimple-volymer till säkerhetskopieringsprogrammet som en enda lagringsplats för säkerhetskopior. Följande bild visar en arkitektur där alla säkerhetskopieringar används StorSimple nivåindelade volymer för säkerhetskopior och återställningar.

![StorSimple som ett logiskt diagram för primära mål för säkerhetskopia](./media/storsimple-configure-backup-target-using-netbackup/primarybackuptargetlogicaldiagram.png)

### <a name="primary-target-backup-logical-steps"></a>Primärt mål säkerhetskopiering logiska steg

1.  Backup-servern kontaktar säkerhetskopieringsagenten mål och backup-agenten skickar data till säkerhetskopieringsservern.
2.  Backup-servern skriver data till StorSimple nivåindelade volymer.
3.  Säkerhetskopieringsservern uppdaterar katalogdatabasen och sedan är klar säkerhetskopieringsjobbet.
4.  Ett skript för ögonblicksbild utlöser StorSimple snapshot manager (starta eller ta bort).
5.  Backup-servern tar du bort utgångna säkerhetskopieringar utifrån en bevarandeprincip.

### <a name="primary-target-restore-logical-steps"></a>Primärt mål återställning logiska steg

1.  Backup-servern börjar återställa lämplig data från lagringsplatsen.
2.  Backup-agenten tar emot data från backup-servern.
3.  Backup-servern är klar återställningsjobbet.

## <a name="storsimple-as-a-secondary-backup-target"></a>StorSimple som ett sekundära säkerhetskopieringsmål

I det här scenariot används främst StorSimple-volymer för långsiktig kvarhållning eller arkivering.

Följande bild visar en arkitektur i vilken första säkerhetskopieringarna och återställer målvolymen höga prestanda. Dessa säkerhetskopior kopieras och arkiveras på ett StorSimple nivåindelad volym enligt ett schema.

Det är viktigt att storleksanpassa högpresterande volymen så att den kan hantera din kvarhållning principkrav kapacitet och prestanda.

![StorSimple som ett logiskt diagram för sekundära säkerhetskopieringsmål](./media/storsimple-configure-backup-target-using-netbackup/secondarybackuptargetlogicaldiagram.png)

### <a name="secondary-target-backup-logical-steps"></a>Sekundär target säkerhetskopiering logiska steg

1.  Backup-servern kontaktar säkerhetskopieringsagenten mål och backup-agenten skickar data till säkerhetskopieringsservern.
2.  Backup-servern skriver data till lagring med höga prestanda.
3.  Säkerhetskopieringsservern uppdaterar katalogdatabasen och sedan är klar säkerhetskopieringsjobbet.
4.  Backup-servern kopierar säkerhetskopior till StorSimple baserat på en bevarandeprincip.
5.  Ett skript för ögonblicksbild utlöser StorSimple snapshot manager (starta eller ta bort).
6.  Backup-servern tar du bort utgångna säkerhetskopior baserat på en bevarandeprincip.

### <a name="secondary-target-restore-logical-steps"></a>Sekundär target återställning logiska steg

1.  Backup-servern börjar återställa lämplig data från lagringsplatsen.
2.  Backup-agenten tar emot data från backup-servern.
3.  Backup-servern är klar återställningsjobbet.

## <a name="deploy-the-solution"></a>Distribuera lösningen

Distribuera den här lösningen kräver tre steg:
1. Förbered nätverksinfrastrukturen.
2. Distribuera din StorSimple-enhet som ett säkerhetskopieringsmål.
3. Distribuera Veritas NetBackup.

Varje steg beskrivs i detalj i följande avsnitt.

### <a name="set-up-the-network"></a>Konfigurera nätverket

Eftersom StorSimple är en lösning som är integrerad med Azure-molnet, kräver StorSimple en aktiv och fungerande anslutning till Azure-molnet. Den här anslutningen används för åtgärder som ögonblicksbilder av molnet, datahantering och metadata överföring och till nivå äldre, använda mindre data till Azure-molnlagring.

För lösningen ska fungera optimalt, rekommenderar vi att du följer dessa nätverk metodtips:

-   Den länk som ansluter StorSimple lagringsnivåer till Azure måste uppfylla dina krav på bandbredd. För att uppnå detta gäller rätt nivå för tjänstkvalitet (QoS) för infrastrukturen växlar som motsvarar dina RPO och återställning tid mål för Återställningstid serviceavtal.

-   Maximal Azure Blob storage-åtkomstfördröjning ska vara cirka 80 ms.

### <a name="deploy-storsimple"></a>Deploy StorSimple

Stegvisa anvisningar för StorSimple-distribution, se [distribuera din lokala StorSimple-enhet](storsimple-deployment-walkthrough-u2.md).

### <a name="deploy-netbackup"></a>Distribuera NetBackup

Stegvisa NetBackup 7.7.x distributionsvägledning finns i den [NetBackup 7.7.x dokumentation](http://www.veritas.com/docs/000094423).

## <a name="set-up-the-solution"></a>Konfigurera lösningen

I det här avsnittet visar vi några Konfigurationsexempel. Följande exempel och rekommendationer visar den mest grundläggande och grundläggande implementeringen. Den här implementeringen kan inte tillämpas direkt på dina specifika behov för säkerhetskopiering.

### <a name="set-up-storsimple"></a>Konfigurera StorSimple

| StorSimple distributionsuppgifter  | Ytterligare kommentarer |
|---|---|
| Distribuera din lokala StorSimple-enhet. | Versioner som stöds: Uppdatering 3 och senare versioner. |
| Aktivera målet för säkerhetskopian. | Använd dessa kommandon för att aktivera eller inaktivera säkerhetskopieringsmål läge och för att hämta status. Mer information finns i [Anslut via en fjärranslutning till en StorSimple-enhet](storsimple-remote-connect.md).</br> Aktivera säkerhetskopieringsläge: `Set-HCSBackupApplianceMode -enable`. </br> Inaktivera säkerhetskopieringsläge: `Set-HCSBackupApplianceMode -disable`. </br> Att hämta det aktuella tillståndet för inställningar för säkerhetskopiering: `Get-HCSBackupApplianceMode`. |
| Skapa en gemensam volymbehållare för volymen som lagrar säkerhetskopierade data. Alla data i en volymbehållare är deduplicerad. | StorSimple volymbehållare definierar deduplicering domäner.  |
| Skapa StorSimple-volymer. | Skapa volymer med storlekar som nära den förväntade användningen som möjligt, eftersom volymstorleken påverkar varaktighetstiden av ögonblicksbild av molndata. Information om hur du kan ändra storlek på en volym, Läs om [bevarandeprinciper](#retention-policies).</br> </br> Använd StorSimple nivåindelade volymer och välj den **Använd volymen för arkivdata mindre ofta** markerar du kryssrutan. </br> Med hjälp av endast lokalt fixerade volymer stöds inte. |
| Skapa en unik StorSimple-säkerhetskopieringsprincip för alla volymer som mål för säkerhetskopian. | En StorSimple-säkerhetskopieringsprincip definierar konsekvensgrupp volym. |
| Inaktivera schemat som ögonblicksbilderna upphör att gälla. | Ögonblicksbilder initieras som en efterbearbetning åtgärd. |

### <a name="set-up-the-host-backup-server-storage"></a>Konfigurera värd säkerhetskopieringsserver lagring

Ställ in säkerhetskopieringsserver värdlagring enligt dessa riktlinjer:  

- Använd inte disklänkade volymer (som skapats av Windows Diskhantering); disklänkade volymer stöds inte.
- Formatera volymerna med NTFS med 64 KB allokeringsstorlek.
- Mappa StorSimple-volymer direkt till NetBackup-servern.
    - Använd iSCSI för fysiska servrar.
    - Använd pass-through diskar för virtuella servrar.


## <a name="best-practices-for-storsimple-and-netbackup"></a>Metodtips för StorSimple och NetBackup

Konfigurera din lösning enligt riktlinjerna i följande avsnitt.

### <a name="operating-system-best-practices"></a>Metodtips för operativsystem

- Inaktivera Windows Server-kryptering och deduplicering för NTFS-filsystemet.
- Inaktivera Windows Server defragmentering på StorSimple-volymer.
- Inaktivera Windows Server-indexering på StorSimple-volymer.
- Kör en virusgenomsökning källvärden (inte mot StorSimple-volymer).
- Inaktivera [Windows serverunderhåll](https://msdn.microsoft.com/library/windows/desktop/hh848037.aspx) i Aktivitetshanteraren. Gör detta på något av följande sätt:
  - Inaktivera Underhåll configurator i Schemaläggaren i Windows.
  - Ladda ned [PsExec](https://technet.microsoft.com/sysinternals/bb897553.aspx) från Windows Sysinternals. När du har hämtat PsExec kör du Windows PowerShell som administratör och skriv:
    ```powershell
    psexec \\%computername% -s schtasks /change /tn “MicrosoftWindowsTaskSchedulerMaintenance Configurator" /disable
    ```

### <a name="storsimple-best-practices"></a>Metodtips för StorSimple

-   Se till att StorSimple-enheten har uppdaterats till [Update 3 eller senare](storsimple-install-update-3.md).
-   Isolera iSCSI och molntrafik. Använd iSCSI-dedikerade anslutningar för trafik mellan StorSimple och backup-servern.
-   Tänk på att din StorSimple-enhet är en dedikerad säkerhetskopieringsmål. Blandade arbetsbelastningar stöds inte eftersom de påverkar din RTO och RPO.

### <a name="netbackup-best-practices"></a>Metodtips för NetBackup

-   NetBackup databasen bör vara lokal på servern och inte finns på en StorSimple-volym.
-   För haveriberedskap, säkerhetskopiera du NetBackup databasen på en StorSimple-volym.
-   Vi stöder NetBackup fullständiga och inkrementella säkerhetskopior (kallas även differentiella säkerhetskopior i NetBackup) för den här lösningen. Vi rekommenderar att du inte använder syntetiska och kumulativa inkrementella säkerhetskopieringar.
-   Säkerhetskopior bör innehålla endast data för ett specifikt jobb. Till exempel lägger till något medium över olika jobb är tillåtna.

Senaste NetBackup inställningarna och bästa praxis för att implementera dessa krav finns i dokumentationen för NetBackup på [www.veritas.com](https://www.veritas.com).


## <a name="retention-policies"></a>Principer för kvarhållning

En av de vanligaste principtyper för kvarhållning av säkerhetskopior är en princip för farfar-far och Son offentlig sektor (GFS). I en GFS i princip en inkrementell säkerhetskopiering utförs dagligen och fullständiga säkerhetskopieringar är klar varje vecka och per månad. Den här principen resulterar i sex StorSimple nivåindelade volymer: en volym som innehåller de veckovisa, månatliga och årliga fullständiga säkerhetskopiorna. de fem volymerna lagring av dagliga inkrementella säkerhetskopieringar.

I följande exempel använder vi en GFS rotation. I exemplet förutsätter vi följande:

-   Icke-deduplicerade eller komprimerade data används.
-   Fullständiga säkerhetskopieringar är 1 TiB.
-   Dagliga säkerhetskopior är 500 GiB.
-   Fyra veckovisa säkerhetskopior behålls i en månad.
-   Tolv månatliga säkerhetskopior behålls under ett år.
-   En årliga säkerhetskopian sparas för 10 år.

Baserat på föregående antaganden kan du skapa en 26-TiB StorSimple nivåindelade volymen för de månatliga och årliga fullständiga säkerhetskopieringarna. Skapa en 5-TiB StorSimple nivåindelad volym för var och en för de dagliga säkerhetskopiorna.

| Typ av säkerhetskopiering kvarhållning | Storlek (TiB) | GFS multiplikatorn\* | Total kapacitet (TiB)  |
|---|---|---|---|
| Varje vecka fullständig | 1 | 4  | 4 |
| Dagliga inkrementella | 0,5 | 20 (cykler lika med antalet veckor per månad) | 12.2 för större kvot |
| Månatliga fullständig | 1 | 12 | 12 |
| Årlig fullständig | 1  | 10 | 10 |
| GFS krav |   | 38 |   |
| Större kvot  | 4  |   | 42 GFS totalkravet  |

\* Multiplikatorn som GFS är antalet kopior måste du skydda och bevara så att de uppfyller dina krav för princip för säkerhetskopiering.

## <a name="set-up-netbackup-storage"></a>Konfigurera NetBackup lagring

### <a name="to-set-up-netbackup-storage"></a>Du ställer in NetBackup lagring

1.  I administrationskonsolen NetBackup väljer **Media och enhetshantering** > **enheter** > **Diskpooler**. I konfigurationsguiden för Disk-Pool väljer du server lagringstyp **AdvancedDisk**, och välj sedan **nästa**.

    ![Konfigurationsguiden för Disk-Pool-administrationskonsolen NetBackup](./media/storsimple-configure-backup-target-using-netbackup/nbimage1.png)

2.  Välj din server och välj sedan **nästa**.

    ![NetBackup-administrationskonsolen väljer du servern](./media/storsimple-configure-backup-target-using-netbackup/nbimage2.png)

3.  Välj din StorSimple-volym.

    ![NetBackup-administrationskonsolen väljer du disken för StorSimple-volym](./media/storsimple-configure-backup-target-using-netbackup/nbimage3.png)

4.  Ange ett namn för målet för säkerhetskopian och välj sedan **nästa** > **nästa** avsluta guiden.

5.  Granska inställningarna och välj sedan **Slutför**.

6.  I slutet av varje volym tilldelning ändra lagringsinställningarna för enheten för att matcha de rekommenderade i [bästa praxis för StorSimple och NetBackup](#best-practices-for-storsimple-and-netbackup).

7. Upprepa steg 1 – 6 tills du är klar med att tilldela dina StorSimple-volymer.

    ![Diskkonfigurationen i administrationskonsolen NetBackup](./media/storsimple-configure-backup-target-using-netbackup/nbimage5.png)

## <a name="set-up-storsimple-as-a-primary-backup-target"></a>Konfigurera StorSimple som ett primära säkerhetskopieringsmål

> [!NOTE]
> Återställer data från en säkerhetskopia som har varit nivåindelade till molnet sker hastigheter för molnet.

Följande bild visar mappningen för en typisk volym till ett säkerhetskopieringsjobb. I det här fallet alla veckovisa säkerhetskopior mappa till lördag fullständig disken, och de inkrementella säkerhetskopiorna mappa till måndag – fredag inkrementella diskar. Alla säkerhetskopior och återställningar är från en StorSimple nivåindelad volym.

![Logiskt diagram för primära säkerhetskopieringsmål konfiguration](./media/storsimple-configure-backup-target-using-netbackup/primarybackuptargetdiagram.png)

### <a name="storsimple-as-a-primary-backup-target-gfs-schedule-example"></a>StorSimple som ett primära säkerhetskopieringsmål GFS schemalägga exempel

Här är ett exempel på ett schema för rotation av GFS för fyra veckor, månatliga och årliga:

| Typ av frekvens/säkerhetskopiering | Fullständig | Inkrementell (dagar 1-5)  |   
|---|---|---|
| Varje vecka (1 – 4 veckor) | Lördag | Måndag-fredag |
| Månadsvis  | Lördag  |   |
| Varje år | Lördag  |   |

## <a name="assigning-storsimple-volumes-to-a-netbackup-backup-job"></a>Tilldela ett säkerhetskopieringsjobb för NetBackup StorSimple-volymer

Följande sekvens förutsätter att NetBackup och målvärden har konfigurerats enligt riktlinjerna som NetBackup agent.

### <a name="to-assign-storsimple-volumes-to-a-netbackup-backup-job"></a>Tilldela StorSimple-volymer till ett säkerhetskopieringsjobb för NetBackup

1. I administrationskonsolen NetBackup väljer **NetBackup Management**, högerklicka på **principer**, och välj sedan **ny princip**.

   ![NetBackup Administrationskonsolen, skapa en ny princip](./media/storsimple-configure-backup-target-using-netbackup/nbimage6.png)

2. I den **Lägg till en ny princip** dialogrutan, ange ett namn för principen och välj sedan den **Använd principen konfigurationsguiden** markerar du kryssrutan. Välj **OK**.

   ![NetBackup Administrationskonsolen, lägga till en ny princip dialogruta](./media/storsimple-configure-backup-target-using-netbackup/nbimage7.png)

3. I princip guiden Konfigurera säkerhetskopiering, väljer du den typ av säkerhetskopiering och väljer sedan **nästa**.

   ![Välj typ av säkerhetskopiering-administrationskonsolen NetBackup](./media/storsimple-configure-backup-target-using-netbackup/nbimage8.png)

4. Om du vill ange Principtyp, Välj **Standard**, och välj sedan **nästa**.

   ![Välj Principtyp-administrationskonsolen NetBackup](./media/storsimple-configure-backup-target-using-netbackup/nbimage9.png)

5. Välj värden, Välj den **identifierar klientens operativsystem** kryssrutan och välj sedan **Lägg till**. Välj **Nästa**.

   ![Lista över klienter i en ny princip-administrationskonsolen NetBackup](./media/storsimple-configure-backup-target-using-netbackup/nbimage10.png)

6. Välj de enheter du vill säkerhetskopiera.

   ![Säkerhetskopiering val för en ny princip-administrationskonsolen NetBackup](./media/storsimple-configure-backup-target-using-netbackup/nbimage11.png)

7. Välj de värden för frekvens och kvarhållning som uppfyller dina krav på säkerhetskopieringsrotation.

   ![NetBackup Administrationskonsolen, säkerhetskopieringsfrekvens och rotation för en ny princip](./media/storsimple-configure-backup-target-using-netbackup/nbimage12.png)

8. Välj **nästa** > **nästa** > **Slutför**.  Du kan ändra schemat när principen har skapats.

9. Välj för att expandera den princip som du precis skapade och välj **scheman**.

   ![Scheman för en ny princip-administrationskonsolen NetBackup](./media/storsimple-configure-backup-target-using-netbackup/nbimage13.png)

10. Högerklicka på **differentiell Inc**väljer **kopiera till nya**, och välj sedan **OK**.

    ![Schemat för en ny princip-administrationskonsolen NetBackup](./media/storsimple-configure-backup-target-using-netbackup/nbimage14.png)

11. Högerklicka på det nyligen skapade schemat och välj sedan **ändra**.

12. På den **attribut** fliken den **åsidosätta principen val av mållagring** kryssrutan och sedan väljer du den volym där måndag inkrementella säkerhetskopieringar går.

    ![Ändra schema-administrationskonsolen NetBackup](./media/storsimple-configure-backup-target-using-netbackup/nbimage15.png)

13. På den **starta fönstret** väljer du tidsperioden för säkerhetskopiorna.

    ![Starta ändringsfönster-administrationskonsolen NetBackup](./media/storsimple-configure-backup-target-using-netbackup/nbimage16.png)

14. Välj **OK**.

15. Upprepa steg 10 – 14 för varje inkrementell säkerhetskopiering. Välj lämplig volym och schemat för varje säkerhetskopiering som du skapar.

16. Högerklicka på den **differentiell Inc** schemalägga och tar bort den.

17. Ändra ditt fullständiga schema så att den uppfyller dina säkerhetskopieringsbehov.

    ![Ändra schema för fullständig-administrationskonsolen NetBackup](./media/storsimple-configure-backup-target-using-netbackup/nbimage17.png)

18. Ändra start-fönstret.

    ![NetBackup Administrationskonsolen, ändra fönstret start](./media/storsimple-configure-backup-target-using-netbackup/nbimage18.png)

19. Sista schemat ser ut så här:

    ![Sista schema-administrationskonsolen NetBackup](./media/storsimple-configure-backup-target-using-netbackup/nbimage19.png)

## <a name="set-up-storsimple-as-a-secondary-backup-target"></a>Konfigurera StorSimple som ett sekundära säkerhetskopieringsmål

> [!NOTE]
>Återställer data från en säkerhetskopia som har varit nivåindelade till molnet sker hastigheter för molnet.

I den här modellen måste du ha ett lagringsmedium (andra än StorSimple) som fungerar som ett tillfälligt cacheminne. Du kan till exempel använda ett redundant matris av oberoende diskar (RAID) volymen för att passa utrymme, indata/utdata (I/O) och bandbredd. Vi rekommenderar att du använder RAID 5, 50 och 10.

Följande bild visar vanliga kortsiktig kvarhållning lokala (för servern) volymer och långsiktig kvarhållning arkiverar volymer. I detta scenario kör alla säkerhetskopieringar på den lokala (för servern) RAID-volymen. Dessa säkerhetskopior är regelbundet en dubblett och arkiveras på ett Arkiv-volym. Det är viktigt att storleken på din lokala (för servern) RAID-volym så att den kan hantera dina kortsiktig kvarhållning kapacitet och prestanda krav.

### <a name="storsimple-as-a-secondary-backup-target-gfs-example"></a>StorSimple som en sekundär säkerhetskopieringsmål GFS-exempel

![StorSimple som ett logiskt diagram för sekundära säkerhetskopieringsmål](./media/storsimple-configure-backup-target-using-netbackup/secondarybackuptargetdiagram.png)

I följande tabell visar hur du konfigurerar säkerhetskopieringar att köras på lokala och StorSimple-diskar. Den innehåller kapacitetskrav för enskilda och totala.

### <a name="backup-configuration-and-capacity-requirements"></a>Konfiguration av säkerhetskopiering och kapacitetskrav

| Typ av säkerhetskopiering och kvarhållning | Konfigurerad lagring | Storlek (TiB) | GFS multiplikatorn | Total kapacitet\* (TiB) |
|---|---|---|---|---|
| Vecka 1 (fullständiga och inkrementella) |Lokal disk (kortsiktig)| 1 | 1 | 1 |
| StorSimple veckor 2 – 4 |StorSimple-disk (långsiktig) | 1 | 4 | 4 |
| Månatliga fullständig |StorSimple-disk (långsiktig) | 1 | 12 | 12 |
| Årlig fullständig |StorSimple-disk (långsiktig) | 1 | 1 | 1 |
|GFS volymer kravet |  |  |  | 18*|

\* Total kapacitet innehåller 17 TiB av StorSimple-diskar och 1 TiB lokal RAID-volym.


### <a name="gfs-example-schedule-gfs-rotation-weekly-monthly-and-yearly-schedule"></a>GFS exempel schema: GFS rotation veckovisa, månatliga och årliga schema

| Vecka | Fullständig | Inkrementell dag 1 | Inkrementell dag 2 | Inkrementell dag 3 | Inkrementell dag 4 | Inkrementell dag 5 |
|---|---|---|---|---|---|---|
| Vecka 1 | Lokal RAID-volym  | Lokal RAID-volym | Lokal RAID-volym | Lokal RAID-volym | Lokal RAID-volym | Lokal RAID-volym |
| Veckan 2 | StorSimple veckor 2 – 4 |   |   |   |   |   |
| Vecka 3 | StorSimple veckor 2 – 4 |   |   |   |   |   |
| Vecka 4 | StorSimple veckor 2 – 4 |   |   |   |   |   |
| Månadsvis | StorSimple per månad |   |   |   |   |   |
| Varje år | StorSimple varje år  |   |   |   |   |   |


## <a name="assign-storsimple-volumes-to-a-netbackup-archive-and-duplication-job"></a>Tilldela ett NetBackup arkivering och duplicering jobb StorSimple-volymer

Eftersom NetBackup erbjuder en mängd olika alternativ för hantering av lagring och media, rekommenderar vi att du kontaktar Veritas eller din NetBackup architect korrekt utvärdera principen (SLP) för lagring livscykelkrav.

När du har definierat de inledande diskpooler, måste du definiera tre ytterligare livscykel principer för lagring, sammanlagt fyra principer:
* LocalRAIDVolume
* StorSimpleWeek2-4
* StorSimpleMonthlyFulls
* StorSimpleYearlyFulls

### <a name="to-assign-storsimple-volumes-to-a-netbackup-archive-and-duplication-job"></a>Tilldela StorSimple-volymer till ett NetBackup arkivering och duplicering jobb

1. I administrationskonsolen NetBackup väljer **Storage** > **livscykel-principer för lagring** > **nya Storage livscykelpolicy**.

   ![Livscykelprincipen för nya storage-administrationskonsolen NetBackup](./media/storsimple-configure-backup-target-using-netbackup/nbimage20.png)

2. Ange ett namn för ögonblicksbilden och välj sedan **Lägg till**.

3. I den **nya åtgärden** dialogrutan den **egenskaper** fliken för **åtgärden**väljer **Backup**. Välj de värden som du vill använda för **mållagret**, **kvarhållning typ**, och **kvarhållningsperioden**. Välj **OK**.

   ![Nya åtgärden i dialogrutan som visas i administrationskonsolen NetBackup](./media/storsimple-configure-backup-target-using-netbackup/nbimage22.png)

   Detta definierar den första säkerhetskopieringen och databasen.

4. Välj för att markera den föregående åtgärden och välj sedan **Lägg till**. I den **ändra lagringsåtgärd** dialogrutan väljer du de värden som du vill använda för **mållagret**, **kvarhållning typ**, och **kvarhållningsperioden**.

   ![Åtgärd för lösenordsbyte lagring i dialogrutan som visas i administrationskonsolen NetBackup](./media/storsimple-configure-backup-target-using-netbackup/nbimage23.png)

5. Välj för att markera den föregående åtgärden och välj sedan **Lägg till**. I den **nya Storage livscykelpolicy** dialogrutan lägger du till månatliga säkerhetskopior för ett år.

   ![Dialogrutan Ny Storage livscykelpolicy-administrationskonsolen NetBackup](./media/storsimple-configure-backup-target-using-netbackup/nbimage24.png)

6. Upprepa steg 4 – 5 tills du har skapat omfattande SLP bevarandeprincipen som du behöver.

   ![Lägg till principer i dialogrutan Ny Storage livscykelpolicy-administrationskonsolen NetBackup](./media/storsimple-configure-backup-target-using-netbackup/nbimage25.png)

7. När du är klar med att definiera bevarandeprincipen SLP, under **princip**, definiera en princip för säkerhetskopiering genom att följa stegen som beskrivs i [tilldela StorSimple-volymer till ett säkerhetskopieringsjobb för NetBackup](#assigning-storsimple-volumes-to-a-netbackup-backup-job).

8. Under **scheman**i den **Ändra schema** dialogrutan högerklickar du på **fullständig**, och välj sedan **ändra**.

   ![Ändra schema i dialogrutan som visas i administrationskonsolen NetBackup](./media/storsimple-configure-backup-target-using-netbackup/nbimage26.png)

9. Välj den **åsidosätta principen val av mållagring** kryssrutan och välj sedan SLP bevarandeprincipen som du skapade i steg 1 – 6.

   ![Val av mållagring för åsidosättning princip-administrationskonsolen NetBackup](./media/storsimple-configure-backup-target-using-netbackup/nbimage27.png)

10. Välj **OK**, och upprepa sedan för schemat för inkrementell säkerhetskopiering.

    ![Ändra schema dialogrutan för inkrementell säkerhetskopiering-administrationskonsolen NetBackup](./media/storsimple-configure-backup-target-using-netbackup/nbimage28.png)


| Typ av säkerhetskopiering kvarhållning | Storlek (TiB) | GFS multiplikatorn\* | Total kapacitet (TiB)  |
|---|---|---|---|
| Varje vecka fullständig |  1  |  4 | 4  |
| Dagliga inkrementella  | 0,5  | 20 (cykler är lika med antalet veckor per månad) | 12.2 för större kvot |
| Månatliga fullständig  | 1 | 12 | 12 |
| Årlig fullständig | 1  | 10 | 10 |
| GFS krav  |     |     | 38 |
| Större kvot  | 4  |    | 42 GFS totalkravet |

\* Multiplikatorn som GFS är antalet kopior måste du skydda och bevara så att de uppfyller dina krav för princip för säkerhetskopiering.

## <a name="storsimple-cloud-snapshots"></a>Ögonblicksbilder av molnet för StorSimple

StorSimple molnögonblicksbilder skydda data som finns i din StorSimple-enhet. Skapa en molnögonblicksbild motsvarar leverans lokala säkerhetskopiebanden till en resurs på en annan plats. Om du använder Azure geo-redundant lagring, motsvarar skapa en molnögonblicksbild leverans säkerhetskopiebanden till flera platser. Om du vill återställa en enhet efter en katastrof kan du ta med en annan StorSimple enhet online och gör en redundansväxling. Efter redundansen, skulle du kunna komma åt data (hastigheter molnet) från den senaste ögonblicksbilden.

I följande avsnitt beskrivs hur du skapar ett kort skript för att starta och ta bort StorSimple molnögonblicksbilder under efterbearbeta för säkerhetskopiering.

> [!NOTE]
> Ögonblicksbilder som manuellt eller programmässigt skapas följer inte en StorSimple snapshot princip. De här ögonblicksbilderna raderas manuellt eller programmässigt.

### <a name="start-and-delete-cloud-snapshots-by-using-a-script"></a>Starta och ta bort ögonblicksbilder av molnet med hjälp av ett skript

> [!NOTE]
> Utvärdera noggrant efterlevnad och konsekvenser för kvarhållning av data innan du tar bort en StorSimple-ögonblicksbild. Mer information om hur du kör en skriptkörning efter säkerhetskopiering finns i den [NetBackup dokumentation](http://www.veritas.com/docs/000094423).

### <a name="backup-lifecycle"></a>Livscykel för säkerhetskopiering

![Säkerhetskopiera livscykel diagram](./media/storsimple-configure-backup-target-using-netbackup/backuplifecycle.png)

### <a name="requirements"></a>Krav

-   Den server som kör skriptet måste ha åtkomst till molnresurser i Azure.
-   Användarkontot måste ha tillräcklig behörighet.
-   En StorSimple-säkerhetskopieringsprincip med associerade StorSimple-volymer måste ställa in men inte aktiverad.
-   Du behöver StorSimple resursens namn, nyckel för tjänstregistrering, enhetens namn och säkerhetskopiering princip-ID.

### <a name="to-start-or-delete-a-cloud-snapshot"></a>Starta eller ta bort en ögonblicksbild i molnet

1. [Installera Azure PowerShell](/powershell/azure/overview).
2. Ladda ned och konfigurera [hantera CloudSnapshots.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Manage-CloudSnapshots.ps1) PowerShell-skript.
3. Kör PowerShell som administratör på den server som kör skriptet. Kontrollera att du kört skriptet med `-WhatIf $true` att se vad ändrar skriptet gör. När verifieringen är klar kan du skicka `-WhatIf $false`. Kör i kommandot nedan:
   ```powershell
   .\Manage-CloudSnapshots.ps1 -SubscriptionId [Subscription Id] -TenantId [Tenant ID] -ResourceGroupName [Resource Group Name] -ManagerName [StorSimple Device Manager Name] -DeviceName [device name] -BackupPolicyName [backup policyname] -RetentionInDays [Retention days] -WhatIf [$true or $false]
   ```
4. Lägg till skript till säkerhetskopieringsjobbet i NetBackup. Gör detta genom att redigera din NetBackup jobbalternativ-före och efter bearbetning kommandon.

> [!NOTE]
> Vi rekommenderar att du kör din StorSimple cloud ögonblicksbild säkerhetskopieringsprincip som en efterbearbetning skriptet i slutet av det dagliga säkerhetskopieringsjobbet. Mer information om hur du säkerhetskopierar och återställer miljön säkerhetskopieringsprogrammet för att hjälpa dig att uppnå dina RPO och RTO i med din säkerhetskopiering architect.

## <a name="storsimple-as-a-restore-source"></a>StorSimple som en källa för återställning

Återställer från en StorSimple-enheten fungerar som återställningar från alla blocklagringsenhet. Återställningar av data som är nivåindelade till molnet sker hastigheter för molnet. För lokala data inträffar återställningar på lokal diskhastigheten på enheten. Information om hur du utför en återställning finns i den [NetBackup dokumentation](http://www.veritas.com/docs/000094423). Vi rekommenderar att du följer bästa praxis för NetBackup återställning.

## <a name="storsimple-failover-and-disaster-recovery"></a>StorSimple redundans och disaster recovery

> [!NOTE]
> Säkerhetskopieringsmål scenarier stöds StorSimple Cloud Appliance inte som ett mål för återställning.

En katastrof kan ha orsakats av olika faktorer. I följande tabell visas vanliga scenarier för haveriberedskap.

| Scenario | Påverkan | Så här återställer du | Anteckningar |
|---|---|---|---|
| StorSimple enhetsfel | Åtgärder för säkerhetskopiering och återställning avbryts. | Ersätt misslyckade enheten och utföra [StorSimple redundans och disaster recovery](storsimple-device-failover-disaster-recovery.md). | Om du vill utföra en återställning när enheten har återställts hämtas arbetsminnet för fullständig data från molnet till den nya enheten. Alla åtgärder är molnet hastigheter. Index och catalog körs processen kan leda till alla säkerhetskopior som ska genomsökas och hämtas från molnnivån till den lokala enheten-nivå, som kan vara en tidskrävande process. |
| NetBackup-serverfel | Åtgärder för säkerhetskopiering och återställning avbryts. | Återskapa säkerhetskopieringsservern och utföra återställning av databasen. | Du måste återskapa eller återställa NetBackup-server på katastrofåterställningsplatsen. Återställa databasen till den senaste tidpunkten. Om den återställda NetBackup-databasen inte är synkroniserat med senaste säkerhetskopieringsjobb, krävs indexering och katalogisera. Det här indexet och katalogisera körs processen kan leda till alla säkerhetskopior som ska genomsökas och hämtas från molnnivån till den lokala enhet-nivån. Detta gör det mer tidskrävande. |
| Platsfel som leder till förlust av både backup server och StorSimple | Åtgärder för säkerhetskopiering och återställning avbryts. | Återställ StorSimple först och sedan återställa NetBackup. | Återställ StorSimple först och sedan återställa NetBackup. Om du vill utföra en återställning när enheten har återställts hämtas arbetsminnet fullständiga data från molnet till den nya enheten. Alla åtgärder är molnet hastigheter. |

## <a name="references"></a>Referenser

Följande dokument har referenser till den här artikeln:

- [StorSimple multipath i/o-installationen](storsimple-configure-mpio-windows-server.md)
- [Storage-scenarier: Tunn allokering](https://msdn.microsoft.com/library/windows/hardware/dn265487.aspx)
- [Använda GPT-enheter](https://msdn.microsoft.com/windows/hardware/gg463524.aspx#EHD)
- [Konfigurera skuggkopior för delade mappar](https://technet.microsoft.com/library/cc771893.aspx)

## <a name="next-steps"></a>Nästa steg

- Mer information om hur du [återställning från en säkerhetskopia](storsimple-restore-from-backup-set-u2.md).
- Mer information om hur du utför [enheten redundans och disaster recovery](storsimple-device-failover-disaster-recovery.md).
