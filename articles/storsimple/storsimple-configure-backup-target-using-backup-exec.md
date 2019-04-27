---
title: StorSimple 8000-serien som mål för säkerhetskopia med Backup Exec | Microsoft Docs
description: Beskriver konfigurationen för målet för säkerhetskopian av StorSimple med Veritas Backup Exec.
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
ms.date: 12/05/2016
ms.author: hkanna
ms.openlocfilehash: e11d541f0450c0de4ba6d60f889fc7471b1fa1aa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60724520"
---
# <a name="storsimple-as-a-backup-target-with-backup-exec"></a>StorSimple som ett säkerhetskopieringsmål med Backup Exec

## <a name="overview"></a>Översikt

Azure StorSimple är en hybridlösning för molnlagring från Microsoft. StorSimple hanterar komplexiteten i exponentiell datatillväxt genom att använda ett Azure storage-konto som ett tillägg till lokal lösning och automatiskt nivåindelas data i den lokala lagringen och molnlagring.

I den här artikeln diskuterar vi hur integrering av StorSimple med Veritas Backup Exec och bästa praxis för att integrera båda lösningarna. Vi kan också göra rekommendationer om hur du ställer in Backup Exec bäst integrera med StorSimple. Vi ska skjutas upp till Veritas metodtips, säkerhetskopiering arkitekter och administratörer för det bästa sättet att konfigurera Backup Exec att uppfylla individuella behov för säkerhetskopiering och servicenivåavtal (SLA).

Även om vi illustrera konfigurationssteg och viktiga begrepp som den här artikeln är inte menat en stegvis guide för konfiguration eller installation. Vi förutsätter att den grundläggande komponenter och infrastruktur fungerar och är redo för att stödja de begrepp som beskrivs.

### <a name="who-should-read-this"></a>Vem ska läsa det här?

Informationen i den här artikeln är mest användbar för att säkerhetskopieringsadministratörer, lagringsadministratörer och storage-arkitekter som har kunskaper om lagring, Windows Server 2012 R2, Ethernet, molntjänster och Backup Exec.

## <a name="supported-versions"></a>Versioner som stöds

-   [Säkerhetskopiera Exec 16 och senare versioner](http://backupexec.com/compatibility)
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
![StorSimple lagringsnivåer diagram](./media/storsimple-configure-backup-target-using-backup-exec/image1.jpg)

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

![StorSimple som ett logiskt diagram för primära mål för säkerhetskopia](./media/storsimple-configure-backup-target-using-backup-exec/primarybackuptargetlogicaldiagram.png)

### <a name="primary-target-backup-logical-steps"></a>Primärt mål säkerhetskopiering logiska steg

1.  Backup-servern kontaktar säkerhetskopieringsagenten mål och backup-agenten skickar data till säkerhetskopieringsservern.
2.  Backup-servern skriver data till StorSimple nivåindelade volymer.
3.  Säkerhetskopieringsservern uppdaterar katalogdatabasen och sedan är klar säkerhetskopieringsjobbet.
4.  Ett skript för ögonblicksbild utlöser StorSimple cloud snapshot manager (starta eller ta bort).
5.  Backup-servern tar du bort utgångna säkerhetskopieringar utifrån en bevarandeprincip.


### <a name="primary-target-restore-logical-steps"></a>Primärt mål återställning logiska steg

1.  Backup-servern börjar återställa lämplig data från lagringsplatsen.
2.  Backup-agenten tar emot data från backup-servern.
3.  Backup-servern är klar återställningsjobbet.

## <a name="storsimple-as-a-secondary-backup-target"></a>StorSimple som ett sekundära säkerhetskopieringsmål

I det här scenariot används främst StorSimple-volymer för långsiktig kvarhållning eller arkivering.

Följande bild visar en arkitektur i vilken första säkerhetskopieringarna och återställer målvolymen höga prestanda. Dessa säkerhetskopior kopieras och arkiveras på ett StorSimple nivåindelad volym enligt ett schema.

Det är viktigt att storleksanpassa högpresterande volymen så att den kan hantera din kvarhållning principkrav kapacitet och prestanda.

![StorSimple som ett logiskt diagram för sekundära säkerhetskopieringsmål](./media/storsimple-configure-backup-target-using-backup-exec/secondarybackuptargetlogicaldiagram.png)

### <a name="secondary-target-backup-logical-steps"></a>Sekundär target säkerhetskopiering logiska steg

1.  Backup-servern kontaktar säkerhetskopieringsagenten mål och backup-agenten skickar data till säkerhetskopieringsservern.
2.  Backup-servern skriver data till lagring med höga prestanda.
3.  Säkerhetskopieringsservern uppdaterar katalogdatabasen och sedan är klar säkerhetskopieringsjobbet.
4.  Backup-servern kopierar säkerhetskopior till StorSimple baserat på en bevarandeprincip.
5.  Ett skript för ögonblicksbild utlöser StorSimple cloud snapshot manager (starta eller ta bort).
6.  Backup-servern tar du bort utgångna säkerhetskopieringar utifrån en bevarandeprincip.

### <a name="secondary-target-restore-logical-steps"></a>Sekundär target återställning logiska steg

1.  Backup-servern börjar återställa lämplig data från lagringsplatsen.
2.  Backup-agenten tar emot data från backup-servern.
3.  Backup-servern är klar återställningsjobbet.

## <a name="deploy-the-solution"></a>Distribuera lösningen

Distribuera lösningen kräver tre steg:
1. Förbered nätverksinfrastrukturen.
2. Distribuera din StorSimple-enhet som ett säkerhetskopieringsmål.
3. Distribuera Backup Exec.

Varje steg beskrivs i detalj i följande avsnitt.

### <a name="set-up-the-network"></a>Konfigurera nätverket

Eftersom StorSimple är en lösning som är integrerad med Azure-molnet, kräver StorSimple en aktiv och fungerande anslutning till Azure-molnet. Den här anslutningen används för åtgärder som ögonblicksbilder av molnet, hantering och överföring av metadata och till nivå äldre, använda mindre data till Azure-molnlagring.

För lösningen ska fungera optimalt, rekommenderar vi att du följer dessa nätverk metodtips:

-   Den länk som ansluter StorSimple lagringsnivåer till Azure måste uppfylla dina krav på bandbredd. För att uppnå detta gäller den nödvändiga nivån för tjänstkvalitet (QoS) för infrastrukturen växlar som motsvarar dina RPO och återställning tid mål för Återställningstid serviceavtal.
-   Maximal Azure Blob storage-åtkomstfördröjning ska vara cirka 80 ms.

### <a name="deploy-storsimple"></a>Deploy StorSimple

Läs en stegvisa distributionsvägledning StorSimple [distribuera din lokala StorSimple-enhet](storsimple-deployment-walkthrough-u2.md).

### <a name="deploy-backup-exec"></a>Distribuera Backup Exec

Backup Exec Metodtips för installation, se [Metodtips för installation av Backup Exec](https://www.veritas.com/content/support/en_US/doc/72686287-131623464-0/v70444238-131623464).

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

- Använd inte disklänkade volymer (som skapats av Windows Diskhantering). Disklänkande diskar stöds inte.
- Formatera volymerna med NTFS med 64 KB allokeringsstorlek.
- Mappa StorSimple-volymer direkt till Backup Exec-servern.
    - Använd iSCSI för fysiska servrar.
    - Använd pass-through diskar för virtuella servrar.

## <a name="best-practices-for-storsimple-and-backup-exec"></a>Metodtips för StorSimple och Backup Exec

Konfigurera din lösning enligt instruktionerna i följande avsnitt.

### <a name="operating-system-best-practices"></a>Metodtips för operativsystem

- Inaktivera Windows Server-kryptering och deduplicering för NTFS-filsystemet.
- Inaktivera Windows Server defragmentering på StorSimple-volymer.
- Inaktivera Windows Server-indexering på StorSimple-volymer.
- Kör en virusgenomsökning källvärden (inte mot StorSimple-volymer).
- Inaktivera [Windows serverunderhåll](https://msdn.microsoft.com/library/windows/desktop/hh848037.aspx) i Aktivitetshanteraren. Gör detta på något av följande sätt:
  - Inaktivera Underhåll configurator i Schemaläggaren i Windows.
  - Ladda ned [PsExec](https://technet.microsoft.com/sysinternals/bb897553.aspx) från Windows Sysinternals. När du har hämtat PsExec kör du Azure PowerShell som administratör och skriv:
    ```powershell
    psexec \\%computername% -s schtasks /change /tn “MicrosoftWindowsTaskSchedulerMaintenance Configurator" /disable
    ```

### <a name="storsimple-best-practices"></a>Metodtips för StorSimple

  -   Se till att StorSimple-enheten har uppdaterats till [Update 3 eller senare](storsimple-install-update-3.md).
  -   Isolera iSCSI och molntrafik. Använd iSCSI-dedikerade anslutningar för trafik mellan StorSimple och backup-servern.
  -   Tänk på att din StorSimple-enhet är en dedikerad säkerhetskopieringsmål. Blandade arbetsbelastningar stöds inte eftersom de påverkar din RTO och RPO.

### <a name="backup-exec-best-practices"></a>Metodtips för Backup Exec

-   Backup Exec måste installeras på en lokal enhet på servern och inte på en StorSimple-volym.
-   Ange Backup Exec storage **samtidiga skrivåtgärder** det maximalt tillåtna antalet.
    -   Ange Backup Exec storage **block- och bufferten storlek** till 512 KB.
    -   Aktivera Backup Exec storage **buffras läsning och skrivning**.
-   StorSimple har stöd för Backup Exec fullständiga och inkrementella säkerhetskopior. Vi rekommenderar att du inte använder syntetiska och differentiella säkerhetskopieringar.
-   Säkerhetskopior bör innehålla endast data för ett specifikt jobb. Till exempel lägger till något medium över olika jobb är tillåtna.
-   Inaktivera verifiering av jobbet. Om det behövs ska verifiering schemaläggas efter senaste säkerhetskopieringen. Det är viktigt att förstå att det här jobbet påverkar din säkerhetskopieringsintervallet.
-   Välj **Storage** > **disken** > **information** > **egenskaper**. Inaktivera **före allokera diskutrymme**.

Senaste Backup Exec-inställningarna och bästa praxis för att implementera dessa krav finns i [webbplatsen Veritas](https://www.veritas.com).

## <a name="retention-policies"></a>Principer för kvarhållning

En av de vanligaste principtyper för kvarhållning av säkerhetskopior är en princip för farfar-far och Son offentlig sektor (GFS). I en GFS i princip en inkrementell säkerhetskopiering utförs dagligen och fullständiga säkerhetskopieringar är klar varje vecka och per månad. Den här principen resulterar i sex StorSimple nivåindelade volymer. En volym som innehåller de veckovisa, månatliga och årliga fullständiga säkerhetskopieringarna. De fem volymerna lagring av dagliga inkrementella säkerhetskopieringar.

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

## <a name="set-up-backup-exec-storage"></a>Konfigurera Backup Exec-lagring

### <a name="to-set-up-backup-exec-storage"></a>Du ställer in Backup Exec-lagring

1.  I hanteringskonsolen för Backup Exec Välj **Storage** > **konfigurerar du lagring** > **diskbaserad lagring**  >   **Nästa**.

    ![Säkerhetskopiera Exec-hanteringskonsolen, konfigurera lagringssidan](./media/storsimple-configure-backup-target-using-backup-exec/image4.png)

2.  Välj **disklagring**, och välj sedan **nästa**.

    ![Backup Exec-hanteringskonsolen, väljer lagringssidan](./media/storsimple-configure-backup-target-using-backup-exec/image5.png)

3.  Ange ett representativt namn, till exempel **lördag fullständig**, och en beskrivning. Välj **Nästa**.

    ![Backup Exec-konsolen, namn och beskrivning hanteringssidan](./media/storsimple-configure-backup-target-using-backup-exec/image7.png)

4.  Välj den disk där du vill skapa lagringsenheten disken och välj sedan **nästa**.

    ![Backup Exec-hanteringskonsolen urvalssidan för storage disk](./media/storsimple-configure-backup-target-using-backup-exec/image9.png)

5.  Öka antalet skrivåtgärder till **16**, och välj sedan **nästa**.

    ![Backup Exec konsolen för hantering av samtidiga skriva inställningssidan för åtgärder](./media/storsimple-configure-backup-target-using-backup-exec/image10.png)

6.  Granska inställningarna och välj sedan **Slutför**.

    ![Backup Exec-hanteringskonsolen, sammanfattningssidan för storage-konfiguration](./media/storsimple-configure-backup-target-using-backup-exec/image11.png)

7.  Ändra lagringsinställningarna för enheten för att matcha de rekommenderade på i slutet av varje volym tilldelning [bästa praxis för StorSimple och Backup Exec](#best-practices-for-storsimple-and-backup-exec).

    ![Backup Exec-hanteringskonsolen inställningssidan för storage-enhet](./media/storsimple-configure-backup-target-using-backup-exec/image12.png)

8.  Upprepa steg 1-7 tills du är klar tilldelas Backup Exec din StorSimple-volymer.

## <a name="set-up-storsimple-as-a-primary-backup-target"></a>Konfigurera StorSimple som ett primära säkerhetskopieringsmål

> [!NOTE]
> Återställning av data från en säkerhetskopia som har varit nivåindelade till molnet sker hastigheter för molnet.

Följande bild visar mappningen för en typisk volym till ett säkerhetskopieringsjobb. I det här fallet alla veckovisa säkerhetskopior mappa till lördag fullständig disken, och de inkrementella säkerhetskopiorna mappa till måndag – fredag inkrementella diskar. Alla säkerhetskopior och återställningar är från en StorSimple nivåindelad volym.

![Logiskt diagram för primära säkerhetskopieringsmål konfiguration](./media/storsimple-configure-backup-target-using-backup-exec/primarybackuptargetdiagram.png)

### <a name="storsimple-as-a-primary-backup-target-gfs-schedule-example"></a>StorSimple som ett primära säkerhetskopieringsmål GFS schemalägga exempel

Här är ett exempel på ett schema för rotation av GFS för fyra veckor, månatliga och årliga:

| Typ av frekvens/säkerhetskopiering | Fullständig | Inkrementell (dagar 1-5)  |   
|---|---|---|
| Varje vecka (1 – 4 veckor) | Lördag | Måndag-fredag |
| Månadsvis  | Lördag  |   |
| Varje år | Lördag  |   |


### <a name="assign-storsimple-volumes-to-a-backup-exec-backup-job"></a>Tilldela ett säkerhetskopieringsjobb för Backup Exec StorSimple-volymer

Följande sekvens förutsätter att Backup Exec- och målvärden är konfigurerat i enlighet med riktlinjerna för Backup Exec-agenten.

#### <a name="to-assign-storsimple-volumes-to-a-backup-exec-backup-job"></a>Tilldela StorSimple-volymer till ett säkerhetskopieringsjobb för Backup Exec

1.  I hanteringskonsolen för Backup Exec Välj **värden** > **Backup** > **säkerhetskopiering till Disk**.

    ![Backup Exec-hanteringskonsolen, valda värden, säkerhetskopiering och säkerhetskopiering till disk](./media/storsimple-configure-backup-target-using-backup-exec/image14.png)

2.  I den **egenskaper för Definition av säkerhetskopiering** dialogrutan **Backup**väljer **redigera**.

    ![Backup Exec konsolen för hantering av dialogrutan Egenskaper för Definition av säkerhetskopiering](./media/storsimple-configure-backup-target-using-backup-exec/image15.png)

3.  Konfigurera din fullständiga och inkrementella säkerhetskopieringar så att de uppfyller dina krav RPO och RTO och överensstämmer med Veritas bästa praxis.

4.  I den **alternativ** dialogrutan **Storage**.

    ![Backup Exec-hanteringskonsolen Backup alternativ Storage dialogrutan](./media/storsimple-configure-backup-target-using-backup-exec/image16.png)

5.  Tilldela motsvarande StorSimple-volymer till schemat för säkerhetskopiering.

    > [!NOTE]
    > **Komprimering** och **krypteringstyp** är inställda på **ingen**.

6.  Under **Kontrollera**väljer den **verifiera inte data för det här jobbet** markerar du kryssrutan. Med det här alternativet kan påverka StorSimple lagringsnivåer.

    > [!NOTE]
    > Defragmentering, indexering och bakgrunden verifiering påverka negativt StorSimple lagringsnivåer.

    ![Kontrollera inställningarna för Backup Exec-hanteringskonsolen, alternativ för säkerhetskopiering](./media/storsimple-configure-backup-target-using-backup-exec/image17.png)

7.  När du har konfigurerat resten av dina alternativ för säkerhetskopiering som uppfyller dina krav, välja **OK** ska slutföras.

## <a name="set-up-storsimple-as-a-secondary-backup-target"></a>Konfigurera StorSimple som ett sekundära säkerhetskopieringsmål

> [!NOTE]
>Återställer data från en säkerhetskopia som har varit nivåindelade till molnet sker hastigheter för molnet.

I den här modellen måste du ha ett lagringsmedium (andra än StorSimple) som fungerar som ett tillfälligt cacheminne. Du kan till exempel använda ett redundant matris av oberoende diskar (RAID) volymen för att passa utrymme, indata/utdata (I/O) och bandbredd. Vi rekommenderar att du använder RAID 5, 50 och 10.

Följande bild visar vanliga kortsiktig kvarhållning lokala (för servern) volymer och långsiktig kvarhållning arkiverar volymer. I detta scenario kör alla säkerhetskopieringar på den lokala (för servern) RAID-volymen. Dessa säkerhetskopior är regelbundet en dubblett och arkiveras på ett Arkiv-volym. Det är viktigt att storleken på din lokala (för servern) RAID-volym så att den kan hantera dina kortsiktig kvarhållning kapacitet och prestanda krav.

### <a name="storsimple-as-a-secondary-backup-target-gfs-example"></a>StorSimple som en sekundär säkerhetskopieringsmål GFS-exempel

![StorSimple som sekundär säkerhetskopieringsmål logiskt diagram](./media/storsimple-configure-backup-target-using-backup-exec/secondarybackuptargetdiagram.png)

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


### <a name="assign-storsimple-volumes-to-a-backup-exec-archive-and-deduplication-job"></a>Tilldela StorSimple-volymer till ett Backup Exec-Arkiv och dedupliceringen jobb

#### <a name="to-assign-storsimple-volumes-to-a-backup-exec-archive-and-duplication-job"></a>Tilldela StorSimple-volymer i ett Backup Exec arkivering och duplicering jobb

1.  Högerklicka på det jobb som du vill arkivera till en StorSimple-volym och välj sedan i hanteringskonsolen för Backup Exec **egenskaper för Definition av säkerhetskopiering** > **redigera**.

    ![Backup Exec-hanteringskonsolen, fliken Egenskaper för Definition av säkerhetskopiering](./media/storsimple-configure-backup-target-using-backup-exec/image19.png)

2.  Välj **lägga till steget** > **duplicera till Disk** > **redigera**.

    ![Säkerhetskopiera Exec-hanteringskonsolen, lägga till steg](./media/storsimple-configure-backup-target-using-backup-exec/image20.png)

3.  I den **duplicera alternativ** dialogrutan väljer du de värden som du vill använda för **källa** och **schema**.

    ![Säkerhetskopiera Exec-hanteringskonsolen, säkerhetskopiering definitioner egenskaper och Dubblettalternativ](./media/storsimple-configure-backup-target-using-backup-exec/image21.png)

4.  I den **Storage** listrutan väljer du StorSimple-volym där du vill arkivera jobb att lagra data.

    ![Säkerhetskopiera Exec-hanteringskonsolen, säkerhetskopiering definitioner egenskaper och Dubblettalternativ](./media/storsimple-configure-backup-target-using-backup-exec/image22.png)

5.  Välj **Kontrollera**, och välj sedan den **verifiera inte data för det här jobbet** markerar du kryssrutan.

    ![Säkerhetskopiera Exec-hanteringskonsolen, säkerhetskopiering definitioner egenskaper och Dubblettalternativ](./media/storsimple-configure-backup-target-using-backup-exec/image23.png)

6.  Välj **OK**.

    ![Säkerhetskopiera Exec-hanteringskonsolen, säkerhetskopiering definitioner egenskaper och Dubblettalternativ](./media/storsimple-configure-backup-target-using-backup-exec/image24.png)

7.  I den **Backup** kolumn, Lägg till en ny fas. Källan, använda **inkrementella**. För målet, väljer du StorSimple-volym där säkerhetskopieringen arkiveras. Upprepa steg 1 – 6.

## <a name="storsimple-cloud-snapshots"></a>Ögonblicksbilder av molnet för StorSimple

StorSimple molnögonblicksbilder skydda data som finns i din StorSimple-enhet. Skapa en molnögonblicksbild motsvarar leverans lokala säkerhetskopiebanden till en resurs på en annan plats. Om du använder Azure geo-redundant lagring, motsvarar skapa en molnögonblicksbild leverans säkerhetskopiebanden till flera platser. Om du vill återställa en enhet efter en katastrof kan du ta med en annan StorSimple enhet online och gör en redundansväxling. Efter redundansen, skulle du kunna komma åt data (hastigheter molnet) från den senaste ögonblicksbilden.

I följande avsnitt beskrivs hur du skapar ett kort skript för att starta och ta bort StorSimple molnögonblicksbilder under efterbearbeta för säkerhetskopiering.

> [!NOTE]
> Ögonblicksbilder som manuellt eller programmässigt skapas följer inte en StorSimple snapshot princip. De här ögonblicksbilderna raderas manuellt eller programmässigt.

### <a name="start-and-delete-cloud-snapshots-by-using-a-script"></a>Starta och ta bort ögonblicksbilder av molnet med hjälp av ett skript

> [!NOTE]
> Utvärdera noggrant efterlevnad och konsekvenser för kvarhållning av data innan du tar bort en StorSimple-ögonblicksbild. Mer information om hur du kör en skriptkörning efter säkerhetskopiering finns i den [dokumentation om Backup Exec](https://www.veritas.com/support/en_US/article.100032497.html).

### <a name="backup-lifecycle"></a>Livscykel för säkerhetskopiering

![Säkerhetskopiera livscykel diagram](./media/storsimple-configure-backup-target-using-backup-exec/backuplifecycle.png)

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
4. Lägg till skript säkerhetskopieringsjobbet i Backup Exec genom att redigera din Backup Exec jobbalternativ-förbearbetning och efterbearbetning kommandon.

   ![Säkerhetskopiera Exec-konsolen, alternativ för säkerhetskopiering, fliken före och efter bearbetnings-kommandon](./media/storsimple-configure-backup-target-using-backup-exec/image25.png)

> [!NOTE]
> Vi rekommenderar att du kör din StorSimple cloud ögonblicksbild säkerhetskopieringsprincip som en efterbearbetning skriptet i slutet av det dagliga säkerhetskopieringsjobbet. Mer information om hur du säkerhetskopierar och återställer miljön säkerhetskopieringsprogrammet för att hjälpa dig att uppnå dina RPO och RTO i med din säkerhetskopiering architect.

## <a name="storsimple-as-a-restore-source"></a>StorSimple som en källa för återställning

Återställer från en StorSimple-enheten fungerar som återställningar från alla blocklagringsenhet. Återställningar av data som är nivåindelade till molnet sker hastigheter för molnet. För lokala data inträffar återställningar på lokal diskhastigheten på enheten. Information om hur du utför en återställning finns i dokumentationen om Backup Exec. Vi rekommenderar att du följer bästa praxis för Backup Exec återställning.

## <a name="storsimple-failover-and-disaster-recovery"></a>StorSimple redundans och disaster recovery

> [!NOTE]
> Säkerhetskopieringsmål scenarier stöds StorSimple Cloud Appliance inte som ett mål för återställning.

En katastrof kan ha orsakats av olika faktorer. I följande tabell visas vanliga scenarier för haveriberedskap.

| Scenario | Påverkan | Så här återställer du | Anteckningar |
|---|---|---|---|
| StorSimple enhetsfel | Åtgärder för säkerhetskopiering och återställning avbryts. | Ersätt misslyckade enheten och utföra [StorSimple redundans och disaster recovery](storsimple-device-failover-disaster-recovery.md). | Om du vill utföra en återställning när enheten har återställts hämtas arbetsminnet för fullständig data från molnet till den nya enheten. Alla åtgärder är molnet hastigheter. Genomsöka processen onlineindex och filförteckningsprocessen kan leda till alla säkerhetskopior som ska genomsökas och hämtas från molnnivån till den lokala enheten-nivå, som kan vara en tidskrävande process. |
| Backup Exec-serverfel | Åtgärder för säkerhetskopiering och återställning avbryts. | Återskapa säkerhetskopieringsservern och utföra återställning av databasen som beskrivs i [hur du gör en manuell säkerhetskopiering och återställning av Backup Exec (BEDB) databas](http://www.veritas.com/docs/000041083). | Du måste återskapa eller återställa Backup Exec-server på katastrofåterställningsplatsen. Återställa databasen till den senaste tidpunkten. Om den återställda Backup Exec-databasen inte är synkroniserat med senaste säkerhetskopieringsjobb, krävs indexering och katalogisera. Det här indexet och katalogisera körs processen kan leda till alla säkerhetskopior som ska genomsökas och hämtas från molnnivån till den lokala enhet-nivån. Detta gör det mer tidskrävande. |
| Platsfel som leder till förlust av både backup server och StorSimple | Åtgärder för säkerhetskopiering och återställning avbryts. | Återställ StorSimple först och sedan återställa Backup Exec. | Återställ StorSimple först och sedan återställa Backup Exec. Om du vill utföra en återställning när enheten har återställts hämtas arbetsminnet fullständiga data från molnet till den nya enheten. Alla åtgärder är molnet hastigheter. |

## <a name="references"></a>Referenser

Följande dokument har referenser till den här artikeln:

- [StorSimple multipath i/o-installationen](storsimple-configure-mpio-windows-server.md)
- [Storage-scenarier: Tunn allokering](https://msdn.microsoft.com/library/windows/hardware/dn265487.aspx)
- [Använda GPT-enheter](https://msdn.microsoft.com/windows/hardware/gg463524.aspx#EHD)
- [Konfigurera skuggkopior för delade mappar](https://technet.microsoft.com/library/cc771893.aspx)

## <a name="next-steps"></a>Nästa steg

- Mer information om hur du [återställning från en säkerhetskopia](storsimple-restore-from-backup-set-u2.md).
- Mer information om hur du utför [enheten redundans och disaster recovery](storsimple-device-failover-disaster-recovery.md).
