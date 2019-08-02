---
title: Azure Backup Server skyddar system tillstånd och återställer till Bare Metal
description: Använd Azure Backup Server för att säkerhetskopiera system tillstånd och ge skydd mot Bare Metal Recovery (BMR).
author: dcurwin
manager: carmonm
keywords: ''
ms.service: backup
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: dacurwin
ms.openlocfilehash: 12412122ba116eedc592fadc57949f707e52c355
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639667"
---
# <a name="back-up-system-state-and-restore-to-bare-metal-with-azure-backup-server"></a>Säkerhetskopiera system tillstånd och Återställ till Bare Metal med Azure Backup Server

Azure Backup Server säkerhetskopierar system tillstånd och ger BMR-skydd (Bare Metal Recovery).

*   **Säkerhets kopiering av system tillstånd**: Säkerhetskopierar operativsystemfiler, så att du kan återställa när en dator startar, men systemfiler och registret går förlorade. En säkerhets kopia av system tillstånd inkluderar:
    * Domän medlem: Startfiler, databas för COM+-klass registrering, register
    * Domänkontrollant: Windows Server Active Directory (NTDS), startfiler, COM+-klass registrerings databas, register, system volym (SYSVOL)
    * Dator som kör kluster tjänster: Metadata för kluster Server
    * Dator som kör Certificate Services: Certifikatdata
* **Bare Metal-säkerhetskopiering**: Säkerhetskopierar operativsystemfiler och alla data på kritiska volymer (förutom användar data). Efter definition innehåller en BMR säkerhets kopia en säkerhets kopia av system tillstånd. Den ger skydd när en dator inte startar och du måste återställa allt.

I följande tabell sammanfattas vad du kan säkerhetskopiera och återställa. Detaljerad information om app-versioner som kan skyddas med system tillstånd och BMR finns i [vad Azure Backup Server säkerhetskopiera?](backup-mabs-protection-matrix.md).

|Säkerhetskopiera|Problem|Återställa från Azure Backup Server säkerhets kopia|Återställa från säkerhets kopia av system tillstånd|BMR|
|----------|---------|---------------------------|------------------------------------|-------|
|**Fildata**<br /><br />Vanlig säkerhets kopiering av data<br /><br />BMR/säkerhets kopiering av system tillstånd|Förlorade fildata|J|N|N|
|**Fildata**<br /><br />Azure Backup Server säkerhets kopiering av fildata<br /><br />BMR/säkerhets kopiering av system tillstånd|Förlorat eller skadat operativ system|N|J|J|
|**Fildata**<br /><br />Azure Backup Server säkerhets kopiering av fildata<br /><br />BMR/säkerhets kopiering av system tillstånd|Förlorad Server (data volymer är intakta)|N|N|J|
|**Fildata**<br /><br />Azure Backup Server säkerhets kopiering av fildata<br /><br />BMR/säkerhets kopiering av system tillstånd|Förlorad Server (data volymer förlorade)|J|Nej|Ja (BMR, följt av regelbunden återställning av säkerhetskopierade fildata)|
|**SharePoint-data**:<br /><br />Azure Backup Server säkerhets kopiering av Server grupps data<br /><br />BMR/säkerhets kopiering av system tillstånd|Borttappad webbplats, listor, list objekt, dokument|J|N|N|
|**SharePoint-data**:<br /><br />Azure Backup Server säkerhets kopiering av Server grupps data<br /><br />BMR/säkerhets kopiering av system tillstånd|Förlorat eller skadat operativ system|N|J|J|
|**SharePoint-data**:<br /><br />Azure Backup Server säkerhets kopiering av Server grupps data<br /><br />BMR/säkerhets kopiering av system tillstånd|Haveriberedskap|N|N|N|
|Windows Server 2012 R2 Hyper-V<br /><br />Azure Backup Server säkerhets kopiering av Hyper-V-värd eller gäst<br /><br />BMR/system tillstånd säkerhets kopiering av värd|Förlorad virtuell dator|J|N|N|
|Hyper-V<br /><br />Azure Backup Server säkerhets kopiering av Hyper-V-värd eller gäst<br /><br />BMR/system tillstånd säkerhets kopiering av värd|Förlorat eller skadat operativ system|N|J|J|
|Hyper-V<br /><br />Azure Backup Server säkerhets kopiering av Hyper-V-värd eller gäst<br /><br />BMR/system tillstånd säkerhets kopiering av värd|Förlorad Hyper-V-värd (VM-intakt)|N|N|J|
|Hyper-V<br /><br />Azure Backup Server säkerhets kopiering av Hyper-V-värd eller gäst<br /><br />BMR/system tillstånd säkerhets kopiering av värd|Förlorad Hyper-V-värd (virtuella datorer förlorade)|N|N|J<br /><br />BMR, följt av regelbunden Azure Backup Server återställning|
|SQL Server/Exchange<br /><br />Azure Backup Server säkerhets kopiering av app<br /><br />BMR/säkerhets kopiering av system tillstånd|Förlorade AppData|J|N|N|
|SQL Server/Exchange<br /><br />Azure Backup Server säkerhets kopiering av app<br /><br />BMR/säkerhets kopiering av system tillstånd|Förlorat eller skadat operativ system|N|Y|J|
|SQL Server/Exchange<br /><br />Azure Backup Server säkerhets kopiering av app<br /><br />BMR/säkerhets kopiering av system tillstånd|Förlorad Server (databas/transaktions loggar intakt)|N|N|J|
|SQL Server/Exchange<br /><br />Azure Backup Server säkerhets kopiering av app<br /><br />BMR/säkerhets kopiering av system tillstånd|Förlorad Server (databas/transaktions loggar förlorade)|N|N|J<br /><br />BMR-återställning, följt av regelbunden Azure Backup Server återställning|

## <a name="how-system-state-backup-works"></a>Så här fungerar säkerhets kopiering av system tillstånd

När en säkerhets kopiering av system tillstånd körs kommunicerar backup-servern med Windows Server Backup för att begära en säkerhets kopia av serverns system tillstånd. Som standard använder säkerhets kopierings servern och Windows Server Backup den enhet som har mest ledigt utrymme. Information om den här enheten sparas i filen PSDataSourceConfig. xml. Detta är den enhet som Windows Server Backup använder för säkerhets kopiering.

Du kan anpassa den enhet som säkerhets kopierings servern använder för säkerhets kopiering av system tillstånd. På den skyddade servern går du till C:\Program Files\Microsoft Data Protection Manager\MABS\Datasources. Öppna filen PSDataSourceConfig. xml för redigering. \<Ändra FilesToProtect\> -värdet för enhets beteckningen. Spara och stäng filen. Om det finns en skydds grupp som skyddar datorns system tillstånd kör du en konsekvens kontroll. Om en avisering genereras väljer du **ändra skydds grupp** i aviseringen och slutför sedan guiden. Kör sedan en annan konsekvens kontroll.

Observera att om skydds servern finns i ett kluster är det möjligt att en kluster enhet väljs som enhet med mest ledigt utrymme. Om enhetens ägarskap har växlats till en annan nod och en säkerhets kopiering av system tillstånd körs, är enheten inte tillgänglig och säkerhets kopieringen Miss lyckas. I det här scenariot ändrar du PSDataSourceConfig. XML så att den pekar på en lokal enhet.

Därefter skapar Windows Server Backup en mapp med namnet WindowsImageBackup i roten i mappen Restore. När Windows Server Backup skapar säkerhets kopian placeras alla data i den här mappen. När säkerhets kopieringen är slutförd överförs filen till säkerhets kopierings servern. Notera följande information:

* Den här mappen och dess innehåll rensas inte när säkerhets kopieringen eller överföringen är klar. Det bästa sättet att tänka på detta är att utrymmet reserveras för nästa gång en säkerhets kopiering är färdig.
* Mappen skapas varje gång en säkerhets kopia görs. Tids-och datumstämpeln visar tiden för den senaste säkerhets kopieringen av system tillstånd.

## <a name="bmr-backup"></a>BMR-säkerhetskopiering

För BMR (inklusive en säkerhets kopia av system tillstånd) sparas säkerhets kopierings jobbet direkt till en resurs på datorn för säkerhets kopierings servern. Den sparas inte i en mapp på den skyddade servern.

Backup Server anropar Windows Server Backup och delar ut replik volymen för den BMR säkerhets kopian. I det här fallet meddelar den inte Windows Server Backup att använda enheten med mest ledigt utrymme. I stället används den resurs som skapades för jobbet.

När säkerhets kopieringen är slutförd överförs filen till säkerhets kopierings servern. Loggarna lagras i C:\Windows\Logs\WindowsServerBackup.

## <a name="prerequisites-and-limitations"></a>Krav och begränsningar

-   BMR stöds inte för datorer som kör Windows Server 2003 eller för datorer som kör ett klient operativ system.

-   Du kan inte skydda BMR och system tillstånd för samma dator i olika skydds grupper.

-   En säkerhets kopierings Server dator kan inte skydda sig själv för BMR.

-   Kortsiktigt skydd till band (disk-till-band eller D2T) stöds inte för BMR. Långsiktig lagring till band (disk till disk till band eller D2D2T) stöds.

-   För BMR-skydd måste Windows Server Backup installeras på den skyddade datorn.

-   För BMR-skydd, till skillnad från system tillstånds skydd, finns det inga utrymmes krav för säkerhets kopierings servern på den skyddade datorn. Windows Server Backup överför säkerhets kopiorna direkt till säkerhets kopierings servern. Överförings jobbet för säkerhets kopiering visas inte i vyn säkerhets kopierings Server **jobb** .

-   Backup server reserverar 30 GB utrymme på replik volymen för BMR. Du kan ändra detta på sidan **diskallokering** i guiden Ändra skydds grupp eller genom att använda PowerShell-cmdletarna get-DatasourceDiskAllocation och set-DatasourceDiskAllocation. På återställnings punkt volymen kräver BMR-skydd cirka 6 GB för kvarhållning av fem dagar.
    * Observera att du inte kan minska storleken på replik volymen till mindre än 15 GB.
    * Säkerhets kopierings servern beräknar inte storleken på data källan BMR. Den förutsätter 30 GB för alla servrar. Ändra värdet baserat på storleken på BMR säkerhets kopieringar som du förväntar dig i din miljö. Storleken på en BMR-säkerhetskopiering kan grovt beräknas som summan av använt utrymme på alla kritiska volymer. Kritiska volymer = start volym + system volym + volym värd system tillstånds data, till exempel Active Directory.

-   Om du ändrar från system tillstånds skydd till BMR-skydd kräver BMR-skydd mindre utrymme på återställnings *punkt volymen*. Det extra utrymmet på volymen frigörs dock inte. Du kan minska volym storleken manuellt på sidan **Ändra diskallokering** i guiden Ändra skydds grupp eller genom att använda PowerShell-cmdletarna get-DatasourceDiskAllocation och set-DatasourceDiskAllocation.

    Om du ändrar från system tillstånds skydd till BMR-skydd kräver BMR-skydd mer utrymme på *replik volymen*. Volymen utökas automatiskt. Om du vill ändra standard utrymmes tilldelningarna använder du PowerShell-cmdleten Modify-DiskAllocation.

-   Om du ändrar från BMR-skydd till skydd av system tillstånd behöver du mer utrymme på återställnings punkt volymen. Säkerhets kopierings servern kan försöka öka volymen automatiskt. Om det inte finns tillräckligt med utrymme i lagringspoolen uppstår ett fel.

    Om du ändrar från BMR-skydd till skydd av system tillstånd behöver du utrymme på den skyddade datorn. Detta beror på att skydd av system tillstånd först skriver repliken till den lokala datorn och sedan överför den till datorn för säkerhets kopierings servern.

## <a name="before-you-begin"></a>Innan du börjar

1.  **Distribuera Azure Backup Server**. Kontrol lera att säkerhets kopierings servern är korrekt distribuerad. Mer information finns i:
    * [System krav för Azure Backup Server](https://docs.microsoft.com/system-center/dpm/install-dpm#setup-prerequisites)
    * [Skydds mat ris för säkerhets kopierings Server](backup-mabs-protection-matrix.md)

2.  **Konfigurera lagring**. Du kan lagra säkerhets kopierings data på disk, på band och i molnet med Azure. Mer information finns i [förbereda data lagring](https://docs.microsoft.com/system-center/dpm/plan-long-and-short-term-data-storage).

3.  **Konfigurera skydds agenten**. Installera skydds agenten på den dator som du vill säkerhetskopiera. Mer information finns i [Distribuera DPM-skyddsagenten](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent).

## <a name="back-up-system-state-and-bare-metal"></a>Säkerhetskopiera system tillstånd och Bare Metal
Konfigurera en skydds grupp enligt beskrivningen i [distribuera skydds grupper](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups). Observera att du inte kan skydda BMR och system tillstånd för samma dator i olika grupper. När du väljer BMR aktive ras också system tillstånd automatiskt.


1.  Öppna guiden Skapa ny skydds grupp på säkerhets kopierings servern administratörskonsol genom att > välja skydds**åtgärder** > **skapa skydds grupp**.

2.  Välj **servrar**på sidan **Välj typ av skydds grupp** och välj sedan **Nästa**.

3.  På sidan **Välj grupp medlemmar** expanderar du datorn och väljer sedan antingen **BMR** eller **system tillstånd**.

    Kom ihåg att du inte kan skydda både BMR och system tillstånd för samma dator i olika grupper. När du väljer BMR aktive ras också system tillstånd automatiskt. Mer information finns i [distribuera skydds grupper](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups).

4.  På sidan **Välj data skydds metod** väljer du hur du vill hantera kortsiktig och långsiktig säkerhets kopiering. Kortsiktig säkerhets kopiering måste alltid vara en första disk, med alternativet att säkerhetskopiera från disken till Azure-molnet med hjälp Azure Backup (kortsiktig eller långsiktig). Ett alternativ till långsiktig säkerhets kopiering till molnet är att konfigurera långsiktig säkerhets kopiering till en fristående band enhet eller ett band bibliotek som är anslutet till säkerhets kopierings servern.

5.  På sidan **Välj kortvariga mål** väljer du hur du vill säkerhetskopiera till kortsiktig lagring på disk:
    1. För **kvarhållningsintervall**väljer du hur länge du vill behålla data på disken. 
    2. I **Synkroniseringsfrekvens**väljer du hur ofta du vill köra en stegvis säkerhets kopiering på disk. Om du inte vill ange ett intervall för säkerhets kopiering kan du markera alternativet **precis före en återställnings punkt** . Backup server kör en Express, fullständig säkerhets kopiering precis innan varje återställnings punkt schemaläggs.

6.  Om du vill lagra data på band för långsiktig lagring väljer du hur länge du vill behålla band data (1-99 år) på sidan **Ange långsiktiga mål** . 
    1. För **säkerhets kopierings frekvens**väljer du hur ofta säkerhets kopiering ska köras. Frekvensen baseras på det kvarhållningsintervall som du har valt:
        * Om kvarhållningsintervallet är 1-99 år kan du välja att säkerhets kopieringen ska ske varje dag, varje vecka, varannan vecka, månads vis, kvartals vis, halvårs vis eller varje år.
        * När kvarhållningsintervallet är 1-11 månader kan du välja att säkerhets kopieringen ska ske varje dag, varje vecka, varannan vecka eller varje månad.
        * När kvarhållningsintervallet är 1-4 veckor kan du välja att säkerhets kopieringen ska ske varje dag eller varje vecka.

    2. På sidan **Välj band-och biblioteks information** väljer du det band och bibliotek som ska användas och om data ska komprimeras och krypteras.

7.  På sidan **Granska diskallokering** granskar du disk utrymmet för lagringspoolen som har allokerats för skydds gruppen.

    1. **Total data storlek** är storleken på de data som du vill säkerhetskopiera.
    2. **Disk utrymme** som ska tillhandahållas på Azure Backup Server är det utrymme som säkerhets kopierings servern rekommenderar för skydds gruppen. Backup server väljer den ideala säkerhets kopierings volymen baserat på inställningarna. Du kan dock redigera säkerhets kopierings volym valen i **disk tilldelnings information**. 
    3. För arbets belastningar väljer du önskad lagring i den nedrullningsbara menyn. Dina ändringar ändrar värdena för **Total lagring** och ledigt **lagrings utrymme** i fönstret **tillgängliga disklagring** . Underetablerat utrymme är den mängd lagrings utrymme som säkerhets kopierings servern föreslår att du lägger till volymen för att säkerställa smidig säkerhets kopiering.

8.  På sidan **Välj metod för skapande av replik** väljer du hur du vill hantera den första fullständiga datareplikeringen. Om du väljer att replikera över nätverket rekommenderar vi att du väljer en tid med låg belastning. För stora mängder data eller för nätverks villkor som är mindre än optimala bör du överväga att replikera data offline med hjälp av flyttbara medier.

9. På sidan **Välj alternativ för konsekvens kontroll** väljer du hur du vill automatisera konsekvens kontroller. Du kan bara välja att köra en kontroll när replik data blir inkonsekventa eller enligt ett schema. Om du inte vill konfigurera automatisk konsekvens kontroll kan du när som helst köra en manuell kontroll. Om du vill köra en manuell kontroll högerklickar du på skydds gruppen i **skydds** delen på säkerhets kopierings servern administratörskonsol och väljer sedan **utför konsekvens kontroll**.

10. Om du har valt att säkerhetskopiera till molnet med hjälp av Azure Backup, på sidan **Ange online skydds data** , kontrollerar du att du väljer de arbets belastningar som du vill säkerhetskopiera till Azure.

11. På sidan **Ange schema för onlinesäkerhetskopiering** väljer du hur ofta stegvisa säkerhets kopieringar ska ske i Azure. Du kan schemalägga säkerhets kopieringar så att de körs varje dag, vecka, månad och år och välja den tid och det datum då de ska köras. Säkerhets kopieringar kan göras upp till två gånger per dag. Varje gång en säkerhets kopiering körs skapas en data återställnings punkt i Azure från kopian av säkerhets kopierings data som lagras på säkerhets kopierings Server disken.

12. På sidan **Ange bevarande princip för online** väljer du hur återställnings punkterna som skapas från de dagliga, vecko Visa, månatliga och årliga säkerhets kopiorna ska behållas i Azure.

13. På sidan **Välj replikering online** väljer du hur den första fullständiga replikeringen av data ska ske. Du kan replikera över nätverket eller göra en offline-säkerhetskopiering (offline-dirigering). Offline-säkerhetskopiering använder Azure import-funktionen. Mer information finns i [arbets flöde för säkerhets kopiering offline i Azure Backup](backup-azure-backup-import-export.md).

14. På sidan **Sammanfattning** granskar du inställningarna. När du har valt **Skapa grupp**sker inledande replikering av data. När datareplikeringen har slutförts på sidan **status** är skydds gruppens status **OK**. Säkerhets kopieringen sker sedan per skydds grupps inställningar.

## <a name="recover-system-state-or-bmr"></a>Återställa system tillstånd eller BMR
Du kan återställa BMR eller system tillstånd till en nätverks plats. Om du har säkerhetskopierat BMR använder du Windows återställnings miljö (WinRE) för att starta systemet och ansluta det till nätverket. Använd sedan Windows Server Backup för att återställa från nätverks platsen. Om du har säkerhetskopierat system tillstånd använder du bara Windows Server Backup för att återställa från nätverks platsen.

### <a name="restore-bmr"></a>Återställ BMR
Kör återställning på datorn för säkerhets kopierings servern:

1.  I rutan **återställning** letar du reda på den dator som du vill återställa och väljer sedan **Bare Metal Recovery**.

2.  Tillgängliga återställnings punkter visas i fetstil i kalendern. Välj datum och tid för den återställnings punkt som du vill använda.

3.  På sidan **Välj återställnings typ** väljer **du kopiera till en nätverksmapp.**

4.  På sidan **Ange mål** väljer du var du vill kopiera data. Kom ihåg att det valda målet måste ha tillräckligt med utrymme. Vi rekommenderar att du skapar en ny mapp.

5.  På sidan **Ange återställnings alternativ** väljer du de säkerhets inställningar som ska användas. Välj sedan om du vill använda storage area network (SAN)-baserade ögonblicks bilder av maskin vara för snabbare återställning. (Detta är bara ett alternativ om du har ett SAN med den här funktionen tillgänglig och möjligheten att skapa och dela en klon för att göra den skrivbar. Dessutom måste datorn med den skyddade datorn och säkerhets kopierings servern vara ansluten till samma nätverk.)

6.  Konfigurera meddelande alternativ. På sidan **bekräftelse** väljer du **Återställ**.

Konfigurera resurs platsen:

1.  På återställnings platsen går du till den mapp där säkerhets kopian finns.

2.  Dela mappen som är en nivå över WindowsImageBackup så att roten i den delade mappen är mappen WindowsImageBackup. Om du inte gör det kommer inte Restore att hitta säkerhets kopian. För att ansluta med hjälp av Windows återställnings miljö (WinRE) behöver du en resurs som du kan komma åt i WinRE med rätt IP-adress och autentiseringsuppgifter.

Återställa systemet:

1.  Starta datorn där du vill återställa avbildningen med hjälp av Windows-DVD: n för systemet som du återställer.

2.  På den första sidan kontrollerar du språk och nationella inställningar. På sidan **Installera** väljer du **Reparera datorn**.

3.  På sidan **alternativ för system återställning** väljer **du Återställ datorn med hjälp av en system avbildning som du skapade tidigare**.

4.  På sidan **Välj en säkerhets kopia av system avbildning** väljer du **Välj en** > system avbildning**Avancerad** > **sökning för en system avbildning i nätverket**. Välj **Ja**om en varning visas. Gå till resurs Sök vägen, ange autentiseringsuppgifterna och välj sedan återställnings punkten. Detta söker efter säkerhets kopior som är tillgängliga i återställnings punkten. Välj den återställnings punkt som du vill använda.

5.  På sidan **Välj hur du vill återställa säkerhets kopian** väljer du **Formatera och partitionera om diskar**. På nästa sida kontrollerar du inställningarna. 

6.  Starta återställningen genom att välja **Slutför**. En omstart krävs.

### <a name="restore-system-state"></a>Återställa system tillstånd

Kör återställning på säkerhets kopierings Server:

1.  I rutan **återställning** letar du reda på den dator som du vill återställa och väljer sedan **Bare Metal Recovery**.

2.  Tillgängliga återställnings punkter visas i fetstil i kalendern. Välj datum och tid för den återställnings punkt som du vill använda.

3.  På sidan **Välj återställnings typ** väljer **du kopiera till en nätverksmapp**.

4.  På sidan **Ange mål** väljer du var du vill kopiera data. Kom ihåg att det valda målet behöver tillräckligt med plats. Vi rekommenderar att du skapar en ny mapp.

5.  På sidan **Ange återställnings alternativ** väljer du de säkerhets inställningar som ska användas. Välj sedan om du vill använda SAN-baserade ögonblicks bilder av maskin vara för snabbare återställning. (Detta är bara ett alternativ om du har ett SAN-nätverk med den här funktionen och möjligheten att skapa och dela en klon så att den blir skrivbar. Dessutom måste den skyddade datorn och servern för säkerhets kopiering vara anslutna till samma nätverk.)

6.  Konfigurera meddelande alternativ. På sidan **bekräftelse** väljer du **Återställ**.

Kör Windows Server Backup:

1.  Välj **åtgärder** > **Återställ** **den här**servernNästa > . > 

2.  Välj **en annan server**, Välj sidan **Ange plats typ** och välj sedan **delad fjärrmapp**. Ange sökvägen till den mapp som innehåller återställnings punkten.

3.  På sidan **Välj återställnings typ** väljer du **system tillstånd**. 

4. På sidan **Välj plats för återställning av system tillstånd** väljer du **ursprunglig plats**.

5.  På sidan **bekräftelse** väljer du **Återställ**. Starta om servern efter återställningen.

6.  Du kan också köra återställning av system tillstånd i en kommando tolk. Det gör du genom att starta Windows Server Backup på den dator som du vill återställa. Hämta versions identifierare genom att ange följande i en kommando tolk:```wbadmin get versions -backuptarget \<servername\sharename\>```

    Använd versions-ID: n för att starta återställning av system tillstånd. Skriv följande i kommando tolken:```wbadmin start systemstaterecovery -version:<versionidentified> -backuptarget:<servername\sharename>```

    Bekräfta att du vill starta återställningen. Du kan se processen i kommando tolkens fönster. En återställnings logg skapas. Starta om servern efter återställningen.

