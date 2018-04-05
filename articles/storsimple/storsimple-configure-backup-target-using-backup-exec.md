---
title: StorSimple 8000-serien som mål för säkerhetskopian med Backup Exec | Microsoft Docs
description: Beskriver hur StorSimple mål för säkerhetskopian med Veritas Backup Exec.
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
ms.openlocfilehash: a28b46e10bbdd5331cc665fad3f80523b3aa8a58
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="storsimple-as-a-backup-target-with-backup-exec"></a>StorSimple som ett mål med Backup Exec

## <a name="overview"></a>Översikt

Azure StorSimple är en cloud hybridlagringslösning från Microsoft. StorSimple adresser svårigheter av exponentiell datatillväxt med hjälp av Azure storage-konto som ett tillägg för lösningen lokalt och automatiskt skiktning data över lokala lagring och lagringsutrymmet i molnet.

I den här artikeln tar vi upp StorSimple integrering med Veritas Backup Exec och bästa praxis för integrering av båda lösningarna. Vi kan också göra rekommendationer om hur du ställer in Backup Exec bäst integreras med StorSimple. Vi skjuter upp att Veritas metodtips säkerhetskopiering arkitekter och administratörer för det bästa sättet att konfigurera Backup Exec att uppfylla krav för enskilda säkerhetskopiering och servicenivåavtal (SLA).

Även om vi illustrerar konfigurationssteg och viktiga begrepp är i den här artikeln inte menat att en stegvis guide för konfiguration och installation. Vi förutsätter att den grundläggande komponenter och infrastruktur fungerar och är redo för att stödja de begrepp som beskrivs.

### <a name="who-should-read-this"></a>Vem ska läsa det här?

Informationen i den här artikeln är mest användbar för administratörer för säkerhetskopiering, lagringsadministratörer och lagring-arkitekter som känner till lagring, Windows Server 2012 R2, Ethernet, molntjänster och Backup Exec.

## <a name="supported-versions"></a>Versioner som stöds

-   [Säkerhetskopiera Exec 16 och senare versioner](http://backupexec.com/compatibility)
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
![StorSimple lagringsnivåer diagram](./media/storsimple-configure-backup-target-using-backup-exec/image1.jpg)

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

| Lagringskapacitet       | 8100          | 8600            |
|------------------------|---------------|-----------------|
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

![StorSimple som ett logiskt diagram för primära mål för säkerhetskopian](./media/storsimple-configure-backup-target-using-backup-exec/primarybackuptargetlogicaldiagram.png)

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

![StorSimple som ett logiskt diagram sekundära mål för säkerhetskopian](./media/storsimple-configure-backup-target-using-backup-exec/secondarybackuptargetlogicaldiagram.png)

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
3. Distribuera Backup Exec.

Varje steg beskrivs i detalj i följande avsnitt.

### <a name="set-up-the-network"></a>Konfigurera nätverket

Eftersom StorSimple är en lösning som är integrerad med Azure-molnet, kräver StorSimple en aktiv och en fungerande anslutning till Azure-molnet. Den här anslutningen används för åtgärder som molnögonblicksbilder, hantering och överföring av metadata och äldre-nivån, mindre data används till Azure-molnlagring.

För lösningen ska fungera optimalt, rekommenderar vi att du följer dessa nätverk metodtips:

-   Den länk som ansluter din StorSimple skiktning till Azure måste uppfylla krav på bandbredd. För att uppnå detta gäller den nödvändiga nivån för tjänstkvalitet (QoS) för infrastrukturen växlar för att matcha dina Återställningspunktmål och återställning tid mål för Återställningstid serviceavtal.
-   Maximal Azure Blob storage-åtkomstfördröjning ska vara runt 80 ms.

### <a name="deploy-storsimple"></a>Distribuera StorSimple

En stegvisa distributionsanvisningar StorSimple finns [distribuera din lokala StorSimple-enhet](storsimple-deployment-walkthrough-u2.md).

### <a name="deploy-backup-exec"></a>Distribuera Backup Exec

Backup Exec Metodtips för installation, se [Metodtips för installation av Backup Exec](https://www.veritas.com/support/en_US/article.000068207).

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

- Använd inte volymer (skapas av Windows Diskhantering). Disklänkande diskar stöds inte.
- Formatera volymerna med NTFS med 64 KB allokeringsstorlek.
- Mappa StorSimple-volymer direkt till Backup Exec-servern.
    - Använd iSCSI för fysiska servrar.
    - Använd genomströmning av diskar för virtuella servrar.

## <a name="best-practices-for-storsimple-and-backup-exec"></a>Metodtips för StorSimple och Backup Exec

Ställ in din lösning enligt riktlinjerna i följande avsnitt.

### <a name="operating-system-best-practices"></a>Metodtips för operativsystem

-   Inaktivera Windows Server-kryptering och deduplicering för NTFS-filsystemet.
-   Inaktivera Windows Server defragmentering på StorSimple-volymer.
-   Inaktivera Windows Server-indexering på StorSimple-volymer.
-   Köra antivirusprogram på källvärden (inte mot StorSimple-volymer).
-   Inaktivera standard [Windows serverunderhåll](https://msdn.microsoft.com/library/windows/desktop/hh848037.aspx) i Aktivitetshanteraren. Gör på något av följande sätt:
   - Inaktivera Underhåll konfiguratorn i Schemaläggaren i Windows.
   - Hämta [PsExec](https://technet.microsoft.com/sysinternals/bb897553.aspx) från Windows Sysinternals. När du har hämtat PsExec, kör du Azure PowerShell som administratör och skriv:
      ```powershell
      psexec \\%computername% -s schtasks /change /tn “MicrosoftWindowsTaskSchedulerMaintenance Configurator" /disable
      ```

### <a name="storsimple-best-practices"></a>Metodtips för StorSimple

  -   Se till att StorSimple-enheten har uppdaterats till [Update 3 eller senare](storsimple-install-update-3.md).
  -   Isolera iSCSI och molntrafik. Använd dedikerade iSCSI-anslutningar för trafik mellan StorSimple och backup-servern.
  -   Se till att din StorSimple-enhet är en dedikerad mål för säkerhetskopian. Blandade arbetsbelastningar stöds inte eftersom de påverkar din RTO och Återställningspunktmål.

### <a name="backup-exec-best-practices"></a>Säkerhetskopiera Exec bästa praxis

-   Backup Exec måste installeras på en lokal enhet på servern och inte på en StorSimple-volym.
-   Ange att lagringsutrymmet Backup Exec **samtidiga skrivåtgärder** till det högsta tillåtna.
    -   Ange att lagringsutrymmet Backup Exec **block och bufferten storlek** till 512 KB.
    -   Aktivera Backup Exec lagring **buffras läsning och skrivning**.
-   StorSimple stöder Backup Exec fullständiga och inkrementella säkerhetskopior. Vi rekommenderar att du inte använder syntetiska och differentiell säkerhetskopiering.
-   Säkerhetskopierade datafiler bör innehålla endast data för ett specifikt jobb. Till exempel lägger till något medium över olika jobb är tillåtna.
-   Inaktivera verifiering av jobbet. Om det behövs ska verifiering schemaläggas efter det senaste jobbet. Det är viktigt att förstå att det här jobbet påverkar säkerhetskopieringsfönstret.
-   Välj **lagring** > **disken** > **information** > **egenskaper**. Inaktivera **före allokera diskutrymme**.

Senaste Backup Exec-inställningarna och bästa praxis för att implementera kraven finns i [webbplatsen Veritas](https://www.veritas.com).

## <a name="retention-policies"></a>Principer för kvarhållning

En av de vanligaste principer för lagring av säkerhetskopior är en princip för farfar, pappa och Son offentlig sektor (GFS). I en GFS princip en inkrementell säkerhetskopiering utförs dagligen och fullständiga säkerhetskopieringar är klar vecka och månad. Den här principen resulterar i sex StorSimple nivåindelade volymer. En volym som innehåller en fullständig säkerhetskopiering varje vecka, månad och år. De fem volymerna lagrar dagliga inkrementella säkerhetskopieringar.

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

## <a name="set-up-backup-exec-storage"></a>Konfigurera Backup Exec lagring

### <a name="to-set-up-backup-exec-storage"></a>Att ställa in Backup Exec lagring

1.  I hanteringskonsolen Backup Exec Välj **lagring** > **konfigurera lagring** > **diskbaserad lagring**  >   **Nästa**.

    ![Säkerhetskopiera Exec-hanteringskonsolen, konfigurera lagring](./media/storsimple-configure-backup-target-using-backup-exec/image4.png)

2.  Välj **disklagring**, och välj sedan **nästa**.

    ![Säkerhetskopiera Exec-hanteringskonsolen, Välj lagring](./media/storsimple-configure-backup-target-using-backup-exec/image5.png)

3.  Ange ett representativt namn, till exempel **lördag fullständig**, och en beskrivning. Välj **Nästa**.

    ![Säkerhetskopiera Exec-konsolen, namn och beskrivning av sidan för hantering](./media/storsimple-configure-backup-target-using-backup-exec/image7.png)

4.  Välj den disk där du vill skapa lagringsenheten disken och välj sedan **nästa**.

    ![Säkerhetskopiera Exec konsolen för hantering av lagringssidan för diskval](./media/storsimple-configure-backup-target-using-backup-exec/image9.png)

5.  Öka antalet skrivåtgärder till **16**, och välj sedan **nästa**.

    ![Backup Exec konsolen för hantering av samtidiga skriva operations inställningssidan](./media/storsimple-configure-backup-target-using-backup-exec/image10.png)

6.  Granska inställningarna och välj sedan **Slutför**.

    ![Säkerhetskopiera Exec konsolen för hantering av lagring configuration sammanfattningssida](./media/storsimple-configure-backup-target-using-backup-exec/image11.png)

7.  Ändra lagringsinställningarna för enheten för att matcha de rekommenderade i slutet av varje volym tilldelning [bästa praxis för StorSimple och Backup Exec](#best-practices-for-storsimple-and-backup-exec).

    ![Säkerhetskopiera Exec konsolen för hantering av lagring inställningssidan för enhet](./media/storsimple-configure-backup-target-using-backup-exec/image12.png)

8.  Upprepa steg 1-7 tills du är klar tilldelar Backup Exec StorSimple-volymer.

## <a name="set-up-storsimple-as-a-primary-backup-target"></a>Ställa in StorSimple som en primär mål för säkerhetskopian

> [!NOTE]
> Återställning av data från en säkerhetskopia som har varit nivåindelade till molnet inträffar hastigheter molnet.

Följande bild visar mappningen av en typisk volym till en säkerhetskopiering. I det här fallet alla veckovisa säkerhetskopior mappas till lördag fullständig disken och inkrementella säkerhetskopieringar mappa till måndag-fredag inkrementell diskar. Alla säkerhetskopior och återställer från en StorSimple nivåer volym.

![Primära mål för säkerhetskopian configuration logiskt diagram](./media/storsimple-configure-backup-target-using-backup-exec/primarybackuptargetdiagram.png)

### <a name="storsimple-as-a-primary-backup-target-gfs-schedule-example"></a>StorSimple som mål för primär säkerhetskopiering GFS schemalägga exempel

Här är ett exempel på ett GFS rotationsschema för fyra veckor, månatliga och årliga:

| Typ av frekvens/säkerhetskopiering | Fullständig | Stegvis (1-5 dagar)  |   
|---|---|---|
| Varje vecka (1 – 4 veckor) | Lördag | Monday-Friday |
| Månadsvis  | Lördag  |   |
| Varje år | Lördag  |   |   |


### <a name="assign-storsimple-volumes-to-a-backup-exec-backup-job"></a>Tilldela ett säkerhetskopieringsjobb Backup Exec StorSimple-volymer

Följande sekvens förutsätter att Backup Exec- och målvärden har konfigurerats enligt riktlinjerna som Backup Exec agent.

#### <a name="to-assign-storsimple-volumes-to-a-backup-exec-backup-job"></a>Tilldela StorSimple-volymer till en Backup Exec-säkerhetskopiering

1.  I hanteringskonsolen Backup Exec Välj **värden** > **säkerhetskopiering** > **säkerhetskopiering till Disk**.

    ![Backup Exec-hanteringskonsolen, valda värden, säkerhetskopiering och säkerhetskopiering till disk](./media/storsimple-configure-backup-target-using-backup-exec/image14.png)

2.  I den **egenskaper för Definition av säkerhetskopiering** dialogrutan under **säkerhetskopiering**väljer **redigera**.

    ![Säkerhetskopiera Exec konsolen för hantering av dialogrutan Egenskaper för Definition av säkerhetskopiering](./media/storsimple-configure-backup-target-using-backup-exec/image15.png)

3.  Konfigurera din fullständiga och inkrementella säkerhetskopieringar så att de uppfyller dina krav Återställningspunktmål och Återställningstidsmål och motsvara Veritas bästa praxis.

4.  I den **alternativ** dialogrutan **lagring**.

    ![Säkerhetskopiera Exec konsolen för hantering av säkerhetskopiering alternativ lagring dialogruta](./media/storsimple-configure-backup-target-using-backup-exec/image16.png)

5.  Tilldela motsvarande StorSimple-volymer till schemat för säkerhetskopiering.

    > [!NOTE]
    > **Komprimering** och **krypteringstyp** är inställda på att **ingen**.

6.  Under **Kontrollera**, Välj den **inte verifiera data för det här jobbet** kryssrutan. Med det här alternativet kan påverka StorSimple skiktning.

    > [!NOTE]
    > Defragmentering indexering och bakgrunden verifiering påverkas negativt om StorSimple skiktning.

    ![Kontrollera inställningarna för säkerhetskopiering Exec hanteringskonsolen alternativ](./media/storsimple-configure-backup-target-using-backup-exec/image17.png)

7.  När du har ställt in resten av alternativ för säkerhetskopieringen som uppfyller dina krav, Välj **OK** ska slutföras.

## <a name="set-up-storsimple-as-a-secondary-backup-target"></a>Ställa in StorSimple som en sekundär mål för säkerhetskopian

> [!NOTE]
>Data återställs från en säkerhetskopia som har varit nivåindelade till molnet uppstå hastigheter molnet.

I den här modellen måste du ha ett lagringsmedium (andra än StorSimple) som fungerar som en tillfällig cache. Du kan till exempel använda ett redundant matris av oberoende diskar (RAID) volymen för att passa utrymme, indata/utdata (I/O) och bandbredd. Vi rekommenderar att du använder RAID 5, 50 och 10.

Följande bild visar vanliga kortsiktig kvarhållning lokala (på servern) volymer och långsiktig kvarhållning arkiveras volymer. I det här scenariot kör alla säkerhetskopior på den lokala (på servern) RAID-volymen. Dessa säkerhetskopior dubbletter och arkiveras till en volym som Arkiv med jämna mellanrum. Det är viktigt att anpassa din lokala (på servern) RAID-volym så att den kan hantera ditt kortsiktiga krav på datalagring kapacitet och prestanda.

### <a name="storsimple-as-a-secondary-backup-target-gfs-example"></a>StorSimple som en sekundär mål för säkerhetskopian GFS-exempel

![StorSimple som sekundär mål för säkerhetskopian logiskt diagram](./media/storsimple-configure-backup-target-using-backup-exec/secondarybackuptargetdiagram.png)

Följande tabell visar hur du konfigurerar säkerhetskopieringar att köras på den lokala och StorSimple-diskar. Den innehåller enskilda och totala kapacitetsbehov.

### <a name="backup-configuration-and-capacity-requirements"></a>Konfigurationen för säkerhetskopiering och kapacitetskrav

| Typ av säkerhetskopiering och lagring | Konfigurerad lagring | Storlek (TiB) | Multiplikatorn GFS | Total kapacitet\* (TiB) |
|---|---|---|---|---|
| Vecka 1 (fullständiga och inkrementella) |Lokal disk (kortsiktig)| 1 | 1 | 1 |
| StorSimple veckor 2-4 |StorSimple disk (långsiktiga) | 1 | 4 | 4 |
| Månatliga fullständig |StorSimple disk (långsiktiga) | 1 | 12 | 12 |
| Årlig fullständig |StorSimple disk (långsiktiga) | 1 | 1 | 1 |
|Kravet GFS volymer |  |  |  | 18*|
\* Total kapacitet innehåller 17 TiB StorSimple-diskar och 1 TiB lokal RAID-volym.


### <a name="gfs-example-schedule-gfs-rotation-weekly-monthly-and-yearly-schedule"></a>GFS exempel schema: GFS rotation schema för varje vecka, månad och år

| Vecka | Fullständig | Inkrementell dag 1 | Inkrementell dag 2 | Inkrementell dag 3 | Inkrementell dag 4 | Inkrementell dag 5 |
|---|---|---|---|---|---|---|
| Vecka 1 | Lokal RAID-volym  | Lokal RAID-volym | Lokal RAID-volym | Lokal RAID-volym | Lokal RAID-volym | Lokal RAID-volym |
| Vecka 2 | StorSimple veckor 2-4 |   |   |   |   |   |
| Vecka 3 | StorSimple veckor 2-4 |   |   |   |   |   |
| Vecka 4 | StorSimple veckor 2-4 |   |   |   |   |   |
| Månadsvis | StorSimple varje månad |   |   |   |   |   |
| Varje år | StorSimple varje år  |   |   |   |   |   |   |


### <a name="assign-storsimple-volumes-to-a-backup-exec-archive-and-deduplication-job"></a>Tilldela ett Backup Exec-Arkiv StorSimple-volymer och dedupliceringen jobb

#### <a name="to-assign-storsimple-volumes-to-a-backup-exec-archive-and-duplication-job"></a>Att tilldela ett Backup Exec arkivet och duplicering jobb StorSimple-volymer

1.  Högerklicka på det jobb som du vill arkivera till en StorSimple-volym och välj sedan i hanteringskonsolen Backup Exec **egenskaper för Definition av säkerhetskopiering** > **redigera**.

    ![Säkerhetskopiera Exec konsolen för hantering av fliken Egenskaper för Definition av säkerhetskopiering](./media/storsimple-configure-backup-target-using-backup-exec/image19.png)

2.  Välj **lägga till steget** > **duplicera till Disk** > **redigera**.

    ![Säkerhetskopiera Exec-hanteringskonsolen, Lägg till fas](./media/storsimple-configure-backup-target-using-backup-exec/image20.png)

3.  I den **duplicera alternativ** dialogrutan väljer du de värden som du vill använda för **källa** och **schema**.

    ![Säkerhetskopiera Exec-hanteringskonsolen, säkerhetskopiering definitioner egenskaper och alternativ för dubbletter](./media/storsimple-configure-backup-target-using-backup-exec/image21.png)

4.  I den **lagring** listrutan väljer du StorSimple-volym där du vill arkivera jobb att lagra data.

    ![Säkerhetskopiera Exec-hanteringskonsolen, säkerhetskopiering definitioner egenskaper och alternativ för dubbletter](./media/storsimple-configure-backup-target-using-backup-exec/image22.png)

5.  Välj **Kontrollera**, och välj sedan den **inte verifiera data för det här jobbet** kryssrutan.

    ![Säkerhetskopiera Exec-hanteringskonsolen, säkerhetskopiering definitioner egenskaper och alternativ för dubbletter](./media/storsimple-configure-backup-target-using-backup-exec/image23.png)

6.  Välj **OK**.

    ![Säkerhetskopiera Exec-hanteringskonsolen, säkerhetskopiering definitioner egenskaper och alternativ för dubbletter](./media/storsimple-configure-backup-target-using-backup-exec/image24.png)

7.  I den **säkerhetskopiering** kolumn, Lägg till en ny fas. Källan, Använd **inkrementella**. För målet, väljer du StorSimple-volym där säkerhetskopieringen arkiveras. Upprepa steg 1 – 6.

## <a name="storsimple-cloud-snapshots"></a>StorSimple molnögonblicksbilder

StorSimple molnögonblicksbilder skydda data som finns i din StorSimple-enhet. Skapa en ögonblicksbild i molnet motsvarar leverans lokala band och en annan plats. Om du använder Azure geo-redundant lagring, motsvarar skapa en ögonblicksbild i molnet leverans band till flera platser. Om du behöver återställa en enhet efter en katastrof kan du använda en annan StorSimple enhet online och gör en redundansväxling. Efter växling vid fel, skulle du kunna komma åt data (hastigheter moln) från den senaste ögonblicksbilden i molnet.

I följande avsnitt beskrivs hur du skapar ett kort skript för att starta och ta bort ögonblicksbilder för StorSimple-moln under säkerhetskopiering efter bearbetning.

> [!NOTE]
> Ögonblicksbilder som manuellt eller programmässigt skapas följer inte förfalloprincipen för StorSimple snapshot. Dessa ögonblicksbilder raderas manuellt eller programmässigt.

### <a name="start-and-delete-cloud-snapshots-by-using-a-script"></a>Starta och ta bort molnögonblicksbilder med hjälp av ett skript

> [!NOTE]
> Utvärdera noggrant för efterlevnad och konsekvenser för kvarhållning av data innan du tar bort en StorSimple-ögonblicksbild. Mer information om hur du kör ett skript för efter säkerhetskopieringen finns på [Backup Exec dokumentationen](https://www.veritas.com/support/en_US/15047.html).

### <a name="backup-lifecycle"></a>Livscykeln för säkerhetskopiering

![Säkerhetskopiera livscykel diagram](./media/storsimple-configure-backup-target-using-backup-exec/backuplifecycle.png)

### <a name="requirements"></a>Krav

-   Den server som kör skriptet måste ha åtkomst till resurser i Azure-molnet.
-   Användarkontot måste ha behörighet.
-   En princip för StorSimple-säkerhetskopiering med associerade StorSimple-volymer måste ställa in men inte aktiverad.
-   Du behöver StorSimple resursnamnet, registreringsnyckel, namn och en säkerhetskopiering princip-enhets-ID.

### <a name="to-start-or-delete-a-cloud-snapshot"></a>Starta eller ta bort en ögonblicksbild i molnet

1.  [Installera Azure PowerShell](/powershell/azure/overview).
2. Hämta och installera [hantera CloudSnapshots.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Manage-CloudSnapshots.ps1) PowerShell-skript.
3. Kör PowerShell som administratör på den server som kör skriptet. Se till att du kör skriptet med `-WhatIf $true` att se vilka ändringar skriptet gör. När verifieringen är klar kan du skicka `-WhatIf $false`. Kör den nedan kommando:
```powershell
.\Manage-CloudSnapshots.ps1 -SubscriptionId [Subscription Id] -TenantId [Tenant ID] -ResourceGroupName [Resource Group Name] -ManagerName [StorSimple Device Manager Name] -DeviceName [device name] -BackupPolicyName [backup policyname] -RetentionInDays [Retention days] -WhatIf [$true or $false]
```
4.  Lägg till skriptet säkerhetskopieringsjobbet i Backup Exec genom att redigera din Backup Exec alternativ föregående bearbetning och kommandon för efterbearbetning.

    ![Säkerhetskopiera Exec-konsolen, alternativ för säkerhetskopiering, fliken före och efter bearbetning kommandon](./media/storsimple-configure-backup-target-using-backup-exec/image25.png)

> [!NOTE]
> Vi rekommenderar att du kör din StorSimple moln ögonblicksbild säkerhetskopieringsprincip som ett skript för efterbearbetning i slutet av det dagliga säkerhetskopieringsjobbet. Mer information om hur du säkerhetskopierar och återställer säkerhetskopieringsprogram miljön som hjälper dig att uppfylla dina Återställningspunktmål och Återställningstidsmål finns med dina säkerhetskopierade systemarkitekter.

## <a name="storsimple-as-a-restore-source"></a>StorSimple som en källa för återställning

Återställer från en StorSimple-enhet fungerar som återställer från valfri enhet för lagring av block. Återställning av data som är nivåer till molnet inträffar hastigheter molnet. För lokala data finnas återställer på lokal diskhastigheten på enheten. Information om hur du utför en återställning finns i Backup Exec-dokumentationen. Vi rekommenderar att du uppfyller Backup Exec återställning bästa praxis.

## <a name="storsimple-failover-and-disaster-recovery"></a>StorSimple redundans och disaster recovery

> [!NOTE]
> Mål för säkerhetskopian scenarier stöds inte StorSimple moln installation som ett mål för återställning.

En katastrof kan orsakas av olika faktorer. I följande tabell visas vanliga disaster recovery-scenarier.

| Scenario | Påverkan | Så här återställer du | Anteckningar |
|---|---|---|---|
| Fel för StorSimple-enhet | Åtgärder för säkerhetskopiering och återställning avbryts. | Ersätt misslyckade enheten och utföra [StorSimple redundans och disaster recovery](storsimple-device-failover-disaster-recovery.md). | Om du behöver utföra en återställning efter återställning av enheten hämtas arbetsminnet fullständiga data från molnet till den nya enheten. Alla åtgärder är hastigheter molnet. Genomsöka processen indexering och filförteckningsprocessen kan leda till att alla säkerhetskopior som ska genomsökas och hämtas från molnnivån på nivån för lokala enheter som kan vara en tidskrävande process. |
| Backup Exec-serverfel | Åtgärder för säkerhetskopiering och återställning avbryts. | Återskapa backup-servern och utföra återställning av databasen enligt anvisningarna i [hur du gör en manuell säkerhetskopiering och återställning av säkerhetskopierade Exec (BEDB) databas](http://www.veritas.com/docs/000041083). | Du måste återskapa eller återställa Backup Exec-server på disaster recovery-plats. Återställa databasen till den senaste tidpunkten. Om den återställda Backup Exec-databasen inte är synkroniserad med din senaste säkerhetskopieringsjobb krävs indexering och katalogiserar. Den här index och katalogen som processen körs kan orsaka alla säkerhetskopior som ska genomsökas och hämtas från molnnivån på nivån för lokala enheter. Detta gör det mer tidskrävande. |
| Platsfel som leder till förlust av både reservserver och StorSimple | Åtgärder för säkerhetskopiering och återställning avbryts. | Återställ StorSimple först och sedan återställa Backup Exec. | Återställ StorSimple först och sedan återställa Backup Exec. Om du behöver utföra en återställning efter återställning av enheten hämtas arbetsminnet fullständiga data från molnet till den nya enheten. Alla åtgärder är hastigheter molnet. |

## <a name="references"></a>Referenser

Följande dokument har referenser till den här artikeln:

- [StorSimple multipath i/o-installationen](storsimple-configure-mpio-windows-server.md)
- [Scenarier för lagring: tunn allokering](http://msdn.microsoft.com/library/windows/hardware/dn265487.aspx)
- [Använda GPT-enheter](http://msdn.microsoft.com/windows/hardware/gg463524.aspx#EHD)
- [Konfigurera skuggkopior för delade mappar](http://technet.microsoft.com/library/cc771893.aspx)

## <a name="next-steps"></a>Nästa steg

- Mer information om hur du [återställning från en säkerhetskopia](storsimple-restore-from-backup-set-u2.md).
- Mer information om hur du utför [enheten redundans och disaster recovery](storsimple-device-failover-disaster-recovery.md).
